---
title: 在 Xamarin.iOS CallKit
description: 本文介绍新 CallKit API，Apple 在 iOS 10 及如何在 Xamarin.iOS VOIP 应用实现中发布。
ms.prod: xamarin
ms.assetid: 738A142D-FFD2-4738-B3ED-57C273179848
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/15/2017
ms.openlocfilehash: 6db9ff0085c17f07d07a7591f5d735793bfbc5f9
ms.sourcegitcommit: 946ce514fd6575aa6b93ff24181e02a60b24b106
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2019
ms.locfileid: "58678036"
---
# <a name="callkit-in-xamarinios"></a>在 Xamarin.iOS CallKit

在 iOS 10 中新的 CallKit API 提供用于 VOIP 的应用程序将与 iPhone UI 集成并提供熟悉的界面，然后向最终用户体验的方法。 使用此 API 用户可以查看和与 VOIP 呼叫从 iOS 设备锁定屏幕进行交互和管理使用的手机应用的联系人**收藏夹**并**最近**视图。

## <a name="about-callkit"></a>有关 CallKit

根据 Apple，CallKit 是一个新的框架，将提升第三方通过 IP 语音 (VOIP) 应用采用第一方在 iOS 10 上的体验。 CallKit API 允许 VOIP 应用与 iPhone UI 集成和提供熟悉的界面，并向最终用户体验。 就像内置 Phone 应用程序，用户可以查看和与 VOIP 呼叫从 iOS 设备锁定屏幕进行交互和管理使用的手机应用的联系人**收藏夹**并**最近**视图。

此外，CallKit API 提供的功能来创建应用扩展，可以将电话号码与名称 (调用方 ID) 相关联或者告知系统应将数字时被阻止 （阻止调用）。

### <a name="the-existing-voip-app-experience"></a>现有的 VOIP 应用体验

在讨论之前新 CallKit API 和其功能，请查看当前的用户体验，使用第三方 VOIP 应用在 iOS 9 （和较小者） 使用名为 MonkeyCall 虚构 VOIP 应用程序。 MonkeyCall 是一种简单的应用，使用户可以发送和接收的 VOIP 呼叫，使用现有的 iOS Api。

目前，如果用户正在接收 MonkeyCall 上的传入呼叫，并且其 iPhone 已锁定，锁定屏幕上所接收的通知是无法区分从任何其他类型的通知 (如从消息或邮件应用程序为例)。

如果用户想要接听电话，他们将需要滑动 MonkeyCall 通知以打开应用并输入其密码 （或用户 Touch ID） 来解锁手机之前他们无法接受在调用并启动会话。

会在手机处于解锁状态同样很麻烦。 同样，传入 MonkeyCall 调用显示为一个标准通知横幅，从屏幕顶部幻灯片中。 通知是暂时性的因为系统可以轻松地将它丢失了由用户强制他们打开通知中心和查找特定的通知，回答然后调用或查找和手动启动 MonkeyCall 应用。

### <a name="the-callkit-voip-app-experience"></a>CallKit VOIP 应用体验

通过 MonkeyCall 应用中实现新 CallKit Api，在 iOS 10 中将极大地改进与传入的 VOIP 呼叫的用户的体验。 以上面提供的锁定其电话号码时收到 VOIP 呼叫的用户为例。 通过实现 CallKit，调用将显示在 iPhone 的锁定屏幕上，就像如果从内置 Phone 应用程序，使用全屏幕、 本机 UI 和标准轻扫答案功能接收呼叫的一样。

同样，如果收到 MonkeyCall VOIP 呼叫时，在 iPhone 已解锁，相同的全屏幕、 本机 UI 和内置的应用程序提供的电话和 MonkeyCall 标准提示问题答案轻扫并点击拒绝功能可以选择播放铃声自定义的步骤.

CallKit 提供附加功能 MonkeyCall，允许其 VOIP 调用与其他类型的调用，以显示在生成中最近使用的项目进行交互和收藏夹列表、 要使用内置的请勿打扰和阻止功能，使用 Siri 从启动 MonkeyCall 调用和提供为其分配给联系人应用中的人员 MonkeyCall 调用用户的功能。

以下各节将介绍 CallKit 体系结构、 传入和传出的流和 CallKit API 中调用详细信息。

## <a name="the-callkit-architecture"></a>CallKit 体系结构

在 iOS 10 中，Apple 已采用了 CallKit 中的所有系统服务，以便于系统用户界面通过 CallKit 已知对 CarPlay，例如，进行的调用。 在由于 MonkeyCall 采用 CallKit，下面给出的示例，它了解系统这些内置的系统服务的方式相同，将获取所有相同功能：

[![](callkit-images/callkit01.png "CallKit 服务堆栈")](callkit-images/callkit01.png#lightbox)

仔细看一下上面的关系图中的 MonkeyCall 应用程序。 该应用包含所有它的代码以使用其自身的网络通信，并包含其自己的用户界面。 它链接中 CallKit 与系统进行通信：

[![](callkit-images/callkit02.png "MonkeyCall 应用程序体系结构")](callkit-images/callkit02.png#lightbox)

该应用使用的 CallKit 中有两个主要接口：

- `CXProvider` -这可让 MonkeyCall 应用通知可能会出现任何带外通知系统。
- `CXCallController` -允许 MonkeyCall 应用情况通知给本地用户操作的系统。

### <a name="the-cxprovider"></a>CXProvider

如上所述，`CXProvider`允许应用通知可能会出现任何带外通知系统。 这些是由于本地用户操作，而不会发生，但由于外部事件，例如传入呼叫而发生的通知。

应用程序应使用`CXProvider`以下：

- 报告对系统的传入呼叫。
- 报告的传出调用已连接到系统。
- 报告远程用户结束对系统的调用。

当应用想要对系统进行通信时，它使用`CXCallUpdate`类，并当系统需要与应用程序进行通信时，它使用`CXAction`类：

[![](callkit-images/callkit03.png "与通过 CXProvider 系统进行通信")](callkit-images/callkit03.png#lightbox)

### <a name="the-cxcallcontroller"></a>CXCallController

`CXCallController`允许应用通知系统的本地用户操作，例如开始 VOIP 呼叫的用户。 通过实现`CXCallController`应用获取要与其他类型的调用相互作用系统中。 例如，如果已有正在运行，active 电话服务调用`CXCallController`可以允许该调用将置于保持状态并启动或 VOIP 呼叫应答的 VOIP 应用。

应用程序应使用`CXCallController`以下：

- 当用户已开始对系统的传出调用时的报表。
- 当用户接听对系统的传入呼叫的报表。
- 当用户结束对系统的调用时的报表。

当应用程序需要进行通信到系统的本地用户操作时，它使用`CXTransaction`类：

[![](callkit-images/callkit04.png "向使用 CXCallController 系统报告")](callkit-images/callkit04.png#lightbox)

## <a name="implementing-callkit"></a>实现 CallKit

以下部分将说明如何在 Xamarin.iOS VOIP 应用中实现 CallKit。 作为示例，本文档将使用来自虚构 MonkeyCall VOIP 应用代码。 此处介绍的代码表示多个支持类，CallKit 特定部分将详细地介绍了以下各节。

### <a name="the-activecall-class"></a>ActiveCall 类

`ActiveCall` MonkeyCall 应用使用类来保存所有当前处于活动状态，如下所示的 VOIP 调用有关的信息：

```csharp
using System;
using CoreFoundation;
using Foundation;

namespace MonkeyCall
{
    public class ActiveCall
    {
        #region Private Variables
        private bool isConnecting;
        private bool isConnected;
        private bool isOnhold;
        #endregion

        #region Computed Properties
        public NSUuid UUID { get; set; }
        public bool isOutgoing { get; set; }
        public string Handle { get; set; }
        public DateTime StartedConnectingOn { get; set;}
        public DateTime ConnectedOn { get; set;}
        public DateTime EndedOn { get; set; }

        public bool IsConnecting {
            get { return isConnecting; }
            set {
                isConnecting = value;
                if (isConnecting) StartedConnectingOn = DateTime.Now;
                RaiseStartingConnectionChanged ();
            }
        }

        public bool IsConnected {
            get { return isConnected; }
            set {
                isConnected = value;
                if (isConnected) {
                    ConnectedOn = DateTime.Now;
                } else {
                    EndedOn = DateTime.Now;
                }
                RaiseConnectedChanged ();
            }
        }

        public bool IsOnHold {
            get { return isOnhold; }
            set {
                isOnhold = value;
            }
        }
        #endregion

        #region Constructors
        public ActiveCall ()
        {
        }

        public ActiveCall (NSUuid uuid, string handle, bool outgoing)
        {
            // Initialize
            this.UUID = uuid;
            this.Handle = handle;
            this.isOutgoing = outgoing;
        }
        #endregion

        #region Public Methods
        public void StartCall (ActiveCallbackDelegate completionHandler)
        {
            // Simulate the call starting successfully
            completionHandler (true);

            // Simulate making a starting and completing a connection
            DispatchQueue.MainQueue.DispatchAfter (new DispatchTime(DispatchTime.Now, 3000), () => {
                // Note that the call is starting
                IsConnecting = true;

                // Simulate pause before connecting
                DispatchQueue.MainQueue.DispatchAfter (new DispatchTime (DispatchTime.Now, 1500), () => {
                    // Note that the call has connected
                    IsConnecting = false;
                    IsConnected = true;
                });
            });
        }

        public void AnswerCall (ActiveCallbackDelegate completionHandler)
        {
            // Simulate the call being answered
            IsConnected = true;
            completionHandler (true);
        }

        public void EndCall (ActiveCallbackDelegate completionHandler)
        {
            // Simulate the call ending
            IsConnected = false;
            completionHandler (true);
        }
        #endregion

        #region Events
        public delegate void ActiveCallbackDelegate (bool successful);
        public delegate void ActiveCallStateChangedDelegate (ActiveCall call);

        public event ActiveCallStateChangedDelegate StartingConnectionChanged;
        internal void RaiseStartingConnectionChanged ()
        {
            if (this.StartingConnectionChanged != null) this.StartingConnectionChanged (this);
        }

        public event ActiveCallStateChangedDelegate ConnectedChanged;
        internal void RaiseConnectedChanged ()
        {
            if (this.ConnectedChanged != null) this.ConnectedChanged (this);
        }
        #endregion
    }
}
```

`ActiveCall` 包含多个属性来定义调用和可以调用状态更改时引发的两个事件的状态。 由于这是仅为示例，有三种用于模拟开始、 回答和结束调用的方法。

### <a name="the-startcallrequest-class"></a>StartCallRequest 类

`StartCallRequest`静态类，提供了一些使用传出调用时，将使用的帮助器方法：

```csharp
using System;
using Foundation;
using Intents;

namespace MonkeyCall
{
    public static class StartCallRequest
    {
        public static string URLScheme {
            get { return "monkeycall"; }
        }

        public static string ActivityType {
            get { return INIntentIdentifier.StartAudioCall.GetConstant ().ToString (); }
        }

        public static string CallHandleFromURL (NSUrl url)
        {
            // Is this a MonkeyCall handle?
            if (url.Scheme == URLScheme) {
                // Yes, return host
                return url.Host;
            } else {
                // Not handled
                return null;
            }
        }

        public static string CallHandleFromActivity (NSUserActivity activity)
        {
            // Is this a start call activity?
            if (activity.ActivityType == ActivityType) {
                // Yes, trap any errors
                try {
                    // Get first contact
                    var interaction = activity.GetInteraction ();
                    var startAudioCallIntent = interaction.Intent as INStartAudioCallIntent;
                    var contact = startAudioCallIntent.Contacts [0];

                    // Get the person handle
                    return contact.PersonHandle.Value;
                } catch {
                    // Error, report null
                    return null;
                }
            } else {
                // Not handled
                return null;
            }
        }
    }
}
```

`CallHandleFromURL`和`CallHandleFromActivity`类用于在 AppDelegate 中获取被调用的传出调用中的人员的联系句柄。 有关详细信息，请参阅[处理传出调用](#handling-outgoing-calls)下面一节。

### <a name="the-activecallmanager-class"></a>ActiveCallManager 类

`ActiveCallManager`类处理 MonkeyCall 应用中所有打开的调用。

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using CallKit;

namespace MonkeyCall
{
    public class ActiveCallManager
    {
        #region Private Variables
        private CXCallController CallController = new CXCallController ();
        #endregion

        #region Computed Properties
        public List<ActiveCall> Calls { get; set; }
        #endregion

        #region Constructors
        public ActiveCallManager ()
        {
            // Initialize
            this.Calls = new List<ActiveCall> ();
        }
        #endregion

        #region Private Methods
        private void SendTransactionRequest (CXTransaction transaction)
        {
            // Send request to call controller
            CallController.RequestTransaction (transaction, (error) => {
                // Was there an error?
                if (error == null) {
                    // No, report success
                    Console.WriteLine ("Transaction request sent successfully.");
                } else {
                    // Yes, report error
                    Console.WriteLine ("Error requesting transaction: {0}", error);
                }
            });
        }
        #endregion

        #region Public Methods
        public ActiveCall FindCall (NSUuid uuid)
        {
            // Scan for requested call
            foreach (ActiveCall call in Calls) {
                if (call.UUID.Equals(uuid)) return call;
            }

            // Not found
            return null;
        }

        public void StartCall (string contact)
        {
            // Build call action
            var handle = new CXHandle (CXHandleType.Generic, contact);
            var startCallAction = new CXStartCallAction (new NSUuid (), handle);

            // Create transaction
            var transaction = new CXTransaction (startCallAction);

            // Inform system of call request
            SendTransactionRequest (transaction);
        }

        public void EndCall (ActiveCall call)
        {
            // Build action
            var endCallAction = new CXEndCallAction (call.UUID);

            // Create transaction
            var transaction = new CXTransaction (endCallAction);

            // Inform system of call request
            SendTransactionRequest (transaction);
        }

        public void PlaceCallOnHold (ActiveCall call)
        {
            // Build action
            var holdCallAction = new CXSetHeldCallAction (call.UUID, true);

            // Create transaction
            var transaction = new CXTransaction (holdCallAction);

            // Inform system of call request
            SendTransactionRequest (transaction);
        }

        public void RemoveCallFromOnHold (ActiveCall call)
        {
            // Build action
            var holdCallAction = new CXSetHeldCallAction (call.UUID, false);

            // Create transaction
            var transaction = new CXTransaction (holdCallAction);

            // Inform system of call request
            SendTransactionRequest (transaction);
        }
        #endregion
    }
}
```

再次，因为这是一种模拟仅`ActiveCallManager`只维护一系列`ActiveCall`对象，并具有用于查找由给定的调用的例程其`UUID`属性。 它还包括开始、 结束和更改的传出呼叫暂挂状态的方法。 有关详细信息，请参阅[处理传出调用](#handling-outgoing-calls)下面一节。

### <a name="the-providerdelegate-class"></a>ProviderDelegate 类

如上所述，`CXProvider`提供带外通知的应用程序和系统之间的双向通信。 开发人员需要提供自定义`CXProviderDelegate`并将其附加到`CXProvider`的应用程序能够处理带外 CallKit 事件。 MonkeyCall 使用以下`CXProviderDelegate`:

```csharp
using System;
using Foundation;
using CallKit;
using UIKit;

namespace MonkeyCall
{
    public class ProviderDelegate : CXProviderDelegate
    {
        #region Computed Properties
        public ActiveCallManager CallManager { get; set;}
        public CXProviderConfiguration Configuration { get; set; }
        public CXProvider Provider { get; set; }
        #endregion

        #region Constructors
        public ProviderDelegate (ActiveCallManager callManager)
        {
            // Save connection to call manager
            CallManager = callManager;

            // Define handle types
            var handleTypes = new [] { (NSNumber)(int)CXHandleType.PhoneNumber };

            // Get Image Template
            var templateImage = UIImage.FromFile ("telephone_receiver.png");

            // Setup the initial configurations
            Configuration = new CXProviderConfiguration ("MonkeyCall") {
                MaximumCallsPerCallGroup = 1,
                SupportedHandleTypes = new NSSet<NSNumber> (handleTypes),
                IconTemplateImageData = templateImage.AsPNG(),
                RingtoneSound = "musicloop01.wav"
            };

            // Create a new provider
            Provider = new CXProvider (Configuration);

            // Attach this delegate
            Provider.SetDelegate (this, null);

        }
        #endregion

        #region Override Methods
        public override void DidReset (CXProvider provider)
        {
            // Remove all calls
            CallManager.Calls.Clear ();
        }

        public override void PerformStartCallAction (CXProvider provider, CXStartCallAction action)
        {
            // Create new call record
            var activeCall = new ActiveCall (action.CallUuid, action.CallHandle.Value, true);

            // Monitor state changes
            activeCall.StartingConnectionChanged += (call) => {
                if (call.isConnecting) {
                    // Inform system that the call is starting
                    Provider.ReportConnectingOutgoingCall (call.UUID, call.StartedConnectingOn.ToNSDate());
                }
            };

            activeCall.ConnectedChanged += (call) => {
                if (call.isConnected) {
                    // Inform system that the call has connected
                    provider.ReportConnectedOutgoingCall (call.UUID, call.ConnectedOn.ToNSDate ());
                }
            };

            // Start call
            activeCall.StartCall ((successful) => {
                // Was the call able to be started?
                if (successful) {
                    // Yes, inform the system
                    action.Fulfill ();

                    // Add call to manager
                    CallManager.Calls.Add (activeCall);
                } else {
                    // No, inform system
                    action.Fail ();
                }
            });
        }

        public override void PerformAnswerCallAction (CXProvider provider, CXAnswerCallAction action)
        {
            // Find requested call
            var call = CallManager.FindCall (action.CallUuid);

            // Found?
            if (call == null) {
                // No, inform system and exit
                action.Fail ();
                return;
            }

            // Attempt to answer call
            call.AnswerCall ((successful) => {
                // Was the call successfully answered?
                if (successful) {
                    // Yes, inform system
                    action.Fulfill ();
                } else {
                    // No, inform system
                    action.Fail ();
                }
            });
        }

        public override void PerformEndCallAction (CXProvider provider, CXEndCallAction action)
        {
            // Find requested call
            var call = CallManager.FindCall (action.CallUuid);

            // Found?
            if (call == null) {
                // No, inform system and exit
                action.Fail ();
                return;
            }

            // Attempt to answer call
            call.EndCall ((successful) => {
                // Was the call successfully answered?
                if (successful) {
                    // Remove call from manager's queue
                    CallManager.Calls.Remove (call);

                    // Yes, inform system
                    action.Fulfill ();
                } else {
                    // No, inform system
                    action.Fail ();
                }
            });
        }

        public override void PerformSetHeldCallAction (CXProvider provider, CXSetHeldCallAction action)
        {
            // Find requested call
            var call = CallManager.FindCall (action.CallUuid);

            // Found?
            if (call == null) {
                // No, inform system and exit
                action.Fail ();
                return;
            }

            // Update hold status
            call.isOnHold = action.OnHold;

            // Inform system of success
            action.Fulfill ();
        }

        public override void TimedOutPerformingAction (CXProvider provider, CXAction action)
        {
            // Inform user that the action has timed out
        }

        public override void DidActivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
        {
            // Start the calls audio session here
        }

        public override void DidDeactivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
        {
            // End the calls audio session and restart any non-call
            // related audio
        }
        #endregion

        #region Public Methods
        public void ReportIncomingCall (NSUuid uuid, string handle)
        {
            // Create update to describe the incoming call and caller
            var update = new CXCallUpdate ();
            update.RemoteHandle = new CXHandle (CXHandleType.Generic, handle);

            // Report incoming call to system
            Provider.ReportNewIncomingCall (uuid, update, (error) => {
                // Was the call accepted
                if (error == null) {
                    // Yes, report to call manager
                    CallManager.Calls.Add (new ActiveCall (uuid, handle, false));
                } else {
                    // Report error to user here
                    Console.WriteLine ("Error: {0}", error);
                }
            });
        }
        #endregion
    }
}
```

创建此委托的实例后，它传递`ActiveCallManager`，它将用于处理调用的任何活动。 接下来，它定义句柄类型 (`CXHandleType`) 的`CXProvider`将响应：

```csharp
// Define handle types
var handleTypes = new [] { (NSNumber)(int)CXHandleType.PhoneNumber };
```

并获取将应用到应用的图标中，当调用正在进行的模板映像：

```csharp
// Get Image Template
var templateImage = UIImage.FromFile ("telephone_receiver.png");
```

这些值获取绑定到`CXProviderConfiguration`将用于配置`CXProvider`:

```csharp
// Setup the initial configurations
Configuration = new CXProviderConfiguration ("MonkeyCall") {
    MaximumCallsPerCallGroup = 1,
    SupportedHandleTypes = new NSSet<NSNumber> (handleTypes),
    IconTemplateImageData = templateImage.AsPNG(),
    RingtoneSound = "musicloop01.wav"
};
```

然后，该委托创建一个新`CXProvider`与这些配置并将其自身附加到它：

```csharp
// Create a new provider
Provider = new CXProvider (Configuration);

// Attach this delegate
Provider.SetDelegate (this, null);
```

当使用 CallKit，应用程序将无法再创建和处理其自己的音频会话，而是将需要配置和使用系统将创建并为其处理的音频会话。 

如果这是真正的应用程序，`DidActivateAudioSession`方法将用于与预配置启动调用`AVAudioSession`系统提供的：

```csharp
public override void DidActivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
{
    // Start the call's audio session here...
}
```

它还使用`DidDeactivateAudioSession`方法以完成并释放其连接到系统提供的音频会话：

```csharp
public override void DidDeactivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
{
    // End the calls audio session and restart any non-call
    // releated audio
}
```

代码的其余部分将在以下各节中详细介绍。

### <a name="the-appdelegate-class"></a>AppDelegate 类

MonkeyCall 使用 AppDelegate 来保存的实例`ActiveCallManager`和`CXProviderDelegate`，将使用在整个应用：

```csharp
using Foundation;
using UIKit;
using Intents;
using System;

namespace MonkeyCall
{
    [Register ("AppDelegate")]
    public class AppDelegate : UIApplicationDelegate
    {
        #region Constructors
        public override UIWindow Window { get; set; }
        public ActiveCallManager CallManager { get; set; }
        public ProviderDelegate CallProviderDelegate { get; set; }
        #endregion

        #region Override Methods
        public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
        {
            // Initialize the call handlers
            CallManager = new ActiveCallManager ();
            CallProviderDelegate = new ProviderDelegate (CallManager);

            return true;
        }

        public override bool OpenUrl (UIApplication app, NSUrl url, NSDictionary options)
        {
            // Get handle from url
            var handle = StartCallRequest.CallHandleFromURL (url);

            // Found?
            if (handle == null) {
                // No, report to system
                Console.WriteLine ("Unable to get call handle from URL: {0}", url); 
                return false;
            } else {
                // Yes, start call and inform system
                CallManager.StartCall (handle);
                return true;
            }
        }

        public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
        {
            var handle = StartCallRequest.CallHandleFromActivity (userActivity);

            // Found?
            if (handle == null) {
                // No, report to system
                Console.WriteLine ("Unable to get call handle from User Activity: {0}", userActivity);
                return false;
            } else {
                // Yes, start call and inform system
                CallManager.StartCall (handle);
                return true;
            }
        }

        ...
        #endregion
    }
}
```

`OpenUrl`和`ContinueUserActivity`重写应用程序处理传出呼叫时所使用的方法。 有关详细信息，请参阅[处理传出调用](#handling-outgoing-calls)下面一节。

## <a name="handling-incoming-calls"></a>处理传入的调用

有多个状态和流程的传入的 VOIP 呼叫可经历典型的传入调用工作流期间如：

- 通知用户 （和系统） 的传入呼叫存在。
- 当用户想要接听电话时收到通知并初始化与其他用户的呼叫。
- 如果用户想要结束当前调用，通知系统和通信网络。

以下各节将需要应用如何使用 CallKit 来处理传入调用工作流，例如再次使用 MonkeyCall VOIP 应用的详细的信息。

### <a name="informing-user-of-incoming-call"></a>通知的传入呼叫的用户

当远程用户已开始了 VOIP 与会话的本地用户，将发生以下情况：

[![](callkit-images/callkit05.png "远程用户已启动 VOIP 会话")](callkit-images/callkit05.png#lightbox)

1. 应用程序会从其通信网络获得通知，有传入的 VOIP 呼叫。
2. 此应用使用`CXProvider`发送`CXCallUpdate`到告知它调用的系统。
3. 系统会将发布到系统 UI、 系统服务和使用 CallKit 任何其他 VOIP 的应用程序的调用。

例如，在`CXProviderDelegate`:

```csharp
public void ReportIncomingCall (NSUuid uuid, string handle)
{
    // Create update to describe the incoming call and caller
    var update = new CXCallUpdate ();
    update.RemoteHandle = new CXHandle (CXHandleType.Generic, handle);

    // Report incoming call to system
    Provider.ReportNewIncomingCall (uuid, update, (error) => {
        // Was the call accepted
        if (error == null) {
            // Yes, report to call manager
            CallManager.Calls.Add (new ActiveCall (uuid, handle, false));
        } else {
            // Report error to user here
            Console.WriteLine ("Error: {0}", error);
        }
    });
}
```

此代码将创建一个新`CXCallUpdate`实例，并将句柄附加到它将确定调用方。 接下来，它使用`ReportNewIncomingCall`方法的`CXProvider`类，以通知调用的系统。 如果成功，在调用添加到应用程序的集合中的活动调用，如果不可用，错误需要报告给用户。

### <a name="user-answering-incoming-call"></a>用户应答传入呼叫

如果用户想要传入的 VOIP 呼叫应答，发生以下情况：

[![](callkit-images/callkit06.png "用户应答传入的 VOIP 呼叫")](callkit-images/callkit06.png#lightbox)

1. 系统 UI 将通知系统用户想要 VOIP 呼叫应答。
2. 系统发送`CXAnswerCallAction`到应用程序的`CXProvider`通知其答案意向。
3. 应用程序通知其通信网络用户正在应答电话和 VOIP 呼叫将照常运行。

例如，在`CXProviderDelegate`:

```csharp
public override void PerformAnswerCallAction (CXProvider provider, CXAnswerCallAction action)
{
    // Find requested call
    var call = CallManager.FindCall (action.CallUuid);

    // Found?
    if (call == null) {
        // No, inform system and exit
        action.Fail ();
        return;
    }

    // Attempt to answer call
    call.AnswerCall ((successful) => {
        // Was the call successfully answered?
        if (successful) {
            // Yes, inform system
            action.Fulfill ();
        } else {
            // No, inform system
            action.Fail ();
        }
    });
}
```

此代码首先搜索活动调用其列表中的给定调用的。 如果找不到该调用，通知系统，该方法退出。 如果找到了它，则`AnswerCall`方法的`ActiveCall`类调用以开始调用和系统是信息，其操作是成功还是失败。

### <a name="user-ending-incoming-call"></a>结束传入呼叫的用户

如果用户想要终止从调用应用程序的 UI 中，将发生以下情况：

[![](callkit-images/callkit07.png "在用户终止从调用包括在应用程序的 UI")](callkit-images/callkit07.png#lightbox)

1. 应用创建`CXEndCallAction`，获取捆绑到`CXTransaction`的发送到系统以通知它结束调用。
2. 此系统验证结束调用意向，并且发送`CXEndCallAction`返回到通过应用`CXProvider`。
3. 然后，应用程序会通知其通信网络调用正在结束。

例如，在`CXProviderDelegate`:

```csharp
public override void PerformEndCallAction (CXProvider provider, CXEndCallAction action)
{
    // Find requested call
    var call = CallManager.FindCall (action.CallUuid);

    // Found?
    if (call == null) {
        // No, inform system and exit
        action.Fail ();
        return;
    }

    // Attempt to answer call
    call.EndCall ((successful) => {
        // Was the call successfully answered?
        if (successful) {
            // Remove call from manager's queue
            CallManager.Calls.Remove (call);

            // Yes, inform system
            action.Fulfill ();
        } else {
            // No, inform system
            action.Fail ();
        }
    });
}
```

此代码首先搜索活动调用其列表中的给定调用的。 如果找不到该调用，通知系统，该方法退出。 如果找到了它，则`EndCall`方法的`ActiveCall`类称为结束呼叫，它成功或失败时，系统是信息。 如果成功，在调用已从活动调用的集合。

## <a name="managing-multiple-calls"></a>管理多个调用

大多数的 VOIP 应用可以同时处理多个调用。 例如，如果当前没有活动的 VOIP 呼叫并应用获取通知存在是新的传入调用，用户可以暂停或挂机回答第二个在首次调用。

在上述情况下为提供，系统将发送`CXTransaction`到的应用程序将包括多个操作的列表 (如`CXEndCallAction`和`CXAnswerCallAction`)。 所有这些操作将需要单独实现，以便系统可以相应地更新 UI。

## <a name="handling-outgoing-calls"></a>处理传出调用

如果用户点击最近使用的项目列表 （按 Phone 应用程序） 中的条目，例如，这通过调用属于应用程序中，它将发送_启动调用意向_系统：

[![](callkit-images/callkit08.png "接收开始调用意向")](callkit-images/callkit08.png#lightbox)

1. 该应用会创建_启动调用的操作_基于启动调用着眼于从系统收到的。 
2. 应用将使用`CXCallController`从系统请求启动调用操作。
3. 如果系统接受该操作，它将返回到通过应用`XCProvider`委托。
4. 应用程序启动与它通信网络传出呼叫。

意向的详细信息，请参阅我们[Intents 和 Intents UI 扩展](~/ios/platform/sirikit/understanding-sirikit.md)文档。 

### <a name="the-outgoing-call-lifecycle"></a>传出调用生命周期

时使用的 CallKit 和传出呼叫，应用将需要通知的以下生命周期事件的系统：

1. **启动**-通知的系统的传出呼叫即将开始。
2. **启动**-情况通知给传出调用已启动系统。
3. **连接**-通知的系统的连接传出呼叫。
4. **连接**-通知的传出调用已连接，但这两个参与方可以现在通信。

例如，下面的代码将启动的传出调用：

```csharp
private CXCallController CallController = new CXCallController ();
...

private void SendTransactionRequest (CXTransaction transaction)
{
    // Send request to call controller
    CallController.RequestTransaction (transaction, (error) => {
        // Was there an error?
        if (error == null) {
            // No, report success
            Console.WriteLine ("Transaction request sent successfully.");
        } else {
            // Yes, report error
            Console.WriteLine ("Error requesting transaction: {0}", error);
        }
    });
}

public void StartCall (string contact)
{
    // Build call action
    var handle = new CXHandle (CXHandleType.Generic, contact);
    var startCallAction = new CXStartCallAction (new NSUuid (), handle);

    // Create transaction
    var transaction = new CXTransaction (startCallAction);

    // Inform system of call request
    SendTransactionRequest (transaction);
}
```

它会创建`CXHandle`并使用它来配置`CXStartCallAction`捆绑到`CXTransaction`，它是发送给系统使用`RequestTransaction`方法`CXCallController`类。 通过调用`RequestTransaction`方法，系统可以将任何现有调用暂挂，无论源 (Phone 应用程序、 FaceTime、 VOIP，等等) 之前的新调用会启动。

若要开始传出的 VOIP 呼叫请求可以来自几个不同的源，（例如 Siri) 联系人卡 （在使用联系人应用程序） 中的项或从最近使用的项目列表 （按 Phone 应用程序）。 在这些情况下，应用将发送启动调用意向内`NSUserActivity`AppDelegate 将需要对其进行处理：

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    var handle = StartCallRequest.CallHandleFromActivity (userActivity);

    // Found?
    if (handle == null) {
        // No, report to system
        Console.WriteLine ("Unable to get call handle from User Activity: {0}", userActivity);
        return false;
    } else {
        // Yes, start call and inform system
        CallManager.StartCall (handle);
        return true;
    }
}
```

此处`CallHandleFromActivity`帮助器类方法`StartCallRequest`用于正在调用的人员获取的句柄 (请参阅[StartCallRequest 类](#the-startcallrequest-class)上面)。

`PerformStartCallAction`方法[ProviderDelegate 类](#the-providerdelegate-class)用于最后启动实际传出呼叫和通知生命周期中的系统：

```csharp
public override void PerformStartCallAction (CXProvider provider, CXStartCallAction action)
{
    // Create new call record
    var activeCall = new ActiveCall (action.CallUuid, action.CallHandle.Value, true);

    // Monitor state changes
    activeCall.StartingConnectionChanged += (call) => {
        if (call.IsConnecting) {
            // Inform system that the call is starting
            Provider.ReportConnectingOutgoingCall (call.UUID, call.StartedConnectingOn.ToNSDate());
        }
    };

    activeCall.ConnectedChanged += (call) => {
        if (call.IsConnected) {
            // Inform system that the call has connected
            Provider.ReportConnectedOutgoingCall (call.UUID, call.ConnectedOn.ToNSDate ());
        }
    };

    // Start call
    activeCall.StartCall ((successful) => {
        // Was the call able to be started?
        if (successful) {
            // Yes, inform the system
            action.Fulfill ();

            // Add call to manager
            CallManager.Calls.Add (activeCall);
        } else {
            // No, inform system
            action.Fail ();
        }
    });
}
```

它创建的实例`ActiveCall`类 （用于正在进行中保留有关调用的信息），并填充与所调用的人员。 `StartingConnectionChanged`和`ConnectedChanged`事件用于监视和报告的传出调用生命周期。 调用已启动并且，系统通知操作已完成。

### <a name="ending-an-outgoing-call"></a>结束传出呼叫

用户使用的传出调用和希望结束它完成后，可以使用以下代码：

```csharp
private CXCallController CallController = new CXCallController ();
...

private void SendTransactionRequest (CXTransaction transaction)
{
    // Send request to call controller
    CallController.RequestTransaction (transaction, (error) => {
        // Was there an error?
        if (error == null) {
            // No, report success
            Console.WriteLine ("Transaction request sent successfully.");
        } else {
            // Yes, report error
            Console.WriteLine ("Error requesting transaction: {0}", error);
        }
    });
}

public void EndCall (ActiveCall call)
{
    // Build action
    var endCallAction = new CXEndCallAction (call.UUID);

    // Create transaction
    var transaction = new CXTransaction (endCallAction);

    // Inform system of call request
    SendTransactionRequest (transaction);
}
```

如果创建`CXEndCallAction`与调用结束的 UUID，捆绑中`CXTransaction`，它是发送给系统使用`RequestTransaction`方法的`CXCallController`类。 

## <a name="additional-callkit-details"></a>CallKit 的其他详细信息

本部分介绍开发人员需要如使用 CallKit 时需要考虑一些其他详细信息：

- 提供程序配置
- 操作错误
- 系统限制
- VOIP 音频

### <a name="provider-configuration"></a>提供程序配置

提供程序配置允许 iOS 10 VOIP 应用自定义用户体验 （在本机调用在 UI 中) 时使用 CallKit。

应用可以进行以下类型的自定义项：

- 显示本地化的名称。
- 启用视频通话的支持。
- 自定义在调用 UI 上的按钮，通过提供其自己的模板图像图标。 用户与自定义按钮的交互是直接发送到应用程序进行处理。 

### <a name="action-errors"></a>操作错误

iOS 10 VOIP 应用使用 CallKit 需要处理正常失败的操作，并随时在任何时候通知操作状态的用户。 

请考虑下面的示例：

1. 应用程序已收到启动调用的操作，并开始初始化新的 VOIP 调用与它通信网络的过程。
2. 由于受限或没有任何网络通信功能，而此连接将失败。
3. 应用程序*必须*发送**失败**消息返回到调用启动操作 (`Action.Fail()`) 通知失败的系统。
4. 这允许系统以通知用户调用的状态。 例如，若要显示的调用失败 UI。

此外，iOS 10 VOIP 应用程序将需要响应_超时错误_时应采取的操作不能在给定的时间内处理可能发生。 由 CallKit 提供每个操作类型都有与之关联的最大超时值。 这些超时值确保，用户请求任何 CallKit 操作进行处理以响应式的方式，从而阻止将 OS 流畅且响应迅速也。

提供程序委托上有几种方法 (`CXProviderDelegate`)，应重写以适当地处理此超时情况。

### <a name="system-restrictions"></a>系统限制

基于运行 iOS 10 VOIP 应用的 iOS 设备的当前状态，特定系统的限制可能会强制实施。

例如，如果由系统受限制的传入的 VOIP 呼叫：

1. 调用的人是用户的阻止调用方列表上。
2. 在 Do Not 打扰模式下是用户的 iOS 设备。

如果 VOIP 调用受到限制，任何情况下，使用以下代码对其进行处理：

```csharp
public class ProviderDelegate : CXProviderDelegate
{
...

    public void ReportIncomingCall (NSUuid uuid, string handle)
    {
        // Create update to describe the incoming call and caller
        var update = new CXCallUpdate ();
        update.RemoteHandle = new CXHandle (CXHandleType.Generic, handle);
    
        // Report incoming call to system
        Provider.ReportNewIncomingCall (uuid, update, (error) => {
            // Was the call accepted
            if (error == null) {
                // Yes, report to call manager
                CallManager.Calls.Add (new ActiveCall (uuid, handle, false));
            } else {
                // Report error to user here
                if (error.Code == (int)CXErrorCodeIncomingCallError.CallUuidAlreadyExists) {
                    // Handle duplicate call ID
                } else if (error.Code == (int)CXErrorCodeIncomingCallError.FilteredByBlockList) {
                    // Handle call from blocked user
                } else if (error.Code == (int)CXErrorCodeIncomingCallError.FilteredByDoNotDisturb) {
                    // Handle call while in do-not-disturb mode
                } else {
                    // Handle unknown error
                }
            }
        });
    }

}
```

### <a name="voip-audio"></a>VOIP 音频

CallKit 提供用于处理实时的 VOIP 呼叫期间将需要 iOS 10 VOIP 应用的音频资源的以下几个好处。 最大优势之一是应用的音频会话将具有提升的优先级在 iOS 10 中运行时。 这是与内置电话的相同的优先级级别，FaceTime 应用和该增强的优先级级别将阻止其他正在运行的应用程序中断 VOIP 应用的音频会话。

此外，CallKit 有权访问其他音频的路由提示可增强性能和智能地路由 VOIP 音频到特定的输出设备在实时调用基于用户首选项和设备状态。 例如，基于连接的设备 （例如蓝牙耳机），实时 CarPlay 连接或辅助功能设置。

在典型的 VOIP 的生命周期内使用 CallKit 调用，该应用将需要配置 CallKit 将提供音频 Stream。 看看下面的示例：

[![](callkit-images/callkit09.png "开始调用操作序列")](callkit-images/callkit09.png#lightbox)

1. 应用程序以应答传入呼叫接收启动调用的操作。
2. 由应用程序实现此操作之前，它提供的配置将需要为其`AVAudioSession`。
3. 应用程序通知系统的操作已完成。
4. CallKit 调用连接之前，提供高优先级`AVAudioSession`匹配应用请求的配置。 应用程序将通过通知`DidActivateAudioSession`的方法及其`CXProviderDelegate`。

## <a name="working-with-call-directory-extensions"></a>使用调用目录扩展

使用 CallKit 时,_调用目录扩展_提供一种方法来添加阻止的呼叫电话号码和识别特定于给定的 VOIP 应用到 iOS 设备上的联系人应用程序中的联系人的数字。

### <a name="implementing-a-call-directory-extension"></a>实现的呼叫目录扩展

Xamarin.iOS 应用程序中实现调用目录扩展插件，请执行以下操作：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 打开应用的解决方案在 Visual Studio for mac。
2. 右键单击解决方案名称在**解决方案资源管理器**，然后选择**添加** > **添加新项目**。
3. 选择**iOS** > **扩展** > **调用目录扩展**然后单击**下一步**按钮： 

    [![](callkit-images/calldir01.png "创建新的呼叫目录扩展")](callkit-images/calldir01.png#lightbox)
4. 输入**名称**作为扩展，然后单击**下一步**按钮： 

    [![](callkit-images/calldir02.png "输入扩展的名称")](callkit-images/calldir02.png#lightbox)
5. 调整**项目名称**和/或**解决方案名称**如果需要，再单击**创建**按钮： 

    [![](callkit-images/calldir03.png "创建项目")](callkit-images/calldir03.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 在 Visual Studio 中打开应用的解决方案。
2. 右键单击解决方案名称在**解决方案资源管理器**，然后选择**添加** > **添加新项目**。
3. 选择**iOS** > **扩展** > **调用目录扩展**然后单击**下一步**按钮： 

    [![](callkit-images/calldir01w.png "创建新的呼叫目录扩展")](callkit-images/calldir01.png#lightbox)
4. 输入**名称**作为扩展，然后单击**确定**按钮

-----

这将添加`CallDirectoryHandler.cs`类到类似于以下项目：

```csharp
using System;

using Foundation;
using CallKit;

namespace MonkeyCallDirExtension
{
    [Register ("CallDirectoryHandler")]
    public class CallDirectoryHandler : CXCallDirectoryProvider, ICXCallDirectoryExtensionContextDelegate
    {
        #region Constructors
        protected CallDirectoryHandler (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void BeginRequest (CXCallDirectoryExtensionContext context)
        {
            context.Delegate = this;

            if (!AddBlockingPhoneNumbers (context)) {
                Console.WriteLine ("Unable to add blocking phone numbers");
                var error = new NSError (new NSString ("CallDirectoryHandler"), 1, null);
                context.CancelRequest (error);
                return;
            }

            if (!AddIdentificationPhoneNumbers (context)) {
                Console.WriteLine ("Unable to add identification phone numbers");
                var error = new NSError (new NSString ("CallDirectoryHandler"), 2, null);
                context.CancelRequest (error);
                return;
            }

            context.CompleteRequest (null);
        }
        #endregion

        #region Private Methods
        private bool AddBlockingPhoneNumbers (CXCallDirectoryExtensionContext context)
        {
            // Retrieve phone numbers to block from data store. For optimal performance and memory usage when there are many phone numbers,
            // consider only loading a subset of numbers at a given time and using autorelease pool(s) to release objects allocated during each batch of numbers which are loaded.
            //
            // Numbers must be provided in numerically ascending order.

            long [] phoneNumbers = { 14085555555, 18005555555 };

            foreach (var phoneNumber in phoneNumbers)
                context.AddBlockingEntry (phoneNumber);

            return true;
        }

        private bool AddIdentificationPhoneNumbers (CXCallDirectoryExtensionContext context)
        {
            // Retrieve phone numbers to identify and their identification labels from data store. For optimal performance and memory usage when there are many phone numbers,
            // consider only loading a subset of numbers at a given time and using autorelease pool(s) to release objects allocated during each batch of numbers which are loaded.
            //
            // Numbers must be provided in numerically ascending order.

            long [] phoneNumbers = { 18775555555, 18885555555 };
            string [] labels = { "Telemarketer", "Local business" };

            for (var i = 0; i < phoneNumbers.Length; i++) {
                long phoneNumber = phoneNumbers [i];
                string label = labels [i];
                context.AddIdentificationEntry (phoneNumber, label);
            }

            return true;
        }
        #endregion

        #region Public Methods
        public void RequestFailed (CXCallDirectoryExtensionContext extensionContext, NSError error)
        {
            // An error occurred while adding blocking or identification entries, check the NSError for details.
            // For Call Directory error codes, see the CXErrorCodeCallDirectoryManagerError enum.
            //
            // This may be used to store the error details in a location accessible by the extension's containing app, so that the
            // app may be notified about errors which occurred while loading data even if the request to load data was initiated by
            // the user in Settings instead of via the app itself.
        }
        #endregion
    }
}
```

`BeginRequest`方法中调用目录处理程序将需要进行修改以提供所需的功能。 在上述示例中，它尝试 VOIP 应用的联系人数据库中设置的阻止和可用数量的列表。 如果出于任何原因，或者请求失败，创建`NSError`描述错误并将其传递`CancelRequest`方法的`CXCallDirectoryExtensionContext`类。

若要设置阻止的数字使用`AddBlockingEntry`方法的`CXCallDirectoryExtensionContext`类。 为方法提供的数字_必须_将按数值大小升序排序。 为优化性能和内存使用情况时有许多电话号码，请考虑仅在给定时间加载的数字子集并使用 autorelease 池释放在数字加载的每个批处理期间分配的对象。

若要通知到的已知的 VOIP 应用联系人电话号码的联系人应用，请使用`AddIdentificationEntry`方法的`CXCallDirectoryExtensionContext`类，并提供数和标识标签。 同样，为方法提供的数字_必须_将按数值大小升序排序。 为优化性能和内存使用情况时有许多电话号码，请考虑仅在给定时间加载的数字子集并使用 autorelease 池释放在数字加载的每个批处理期间分配的对象。

## <a name="summary"></a>总结

本文只讨论了新 CallKit API，Apple 在 iOS 10 及如何在 Xamarin.iOS VOIP 应用实现中发布。 它介绍了如何 CallKit 允许应用在 ios 系统集成、 如何提供内置应用 （如电话） 功能奇偶一致性和它会在 iOS 中位置的锁和主页屏幕上，例如通过使用 Siri 交互和整个应用的可见性的增加联系人应用程序。

## <a name="related-links"></a>相关链接

- [iOS 10 示例](https://developer.xamarin.com/samples/ios/iOS10/)
