---
title: watchOS 健身在 Xamarin 中的应用程序
description: 本文介绍如何增强功能对 watchOS 3 以及如何在 Xamarin 中实现它们的健身应用进行了 Apple。
ms.prod: xamarin
ms.assetid: F1D19635-A738-43E5-9873-1FC1BA44EEDF
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 02db7dce6ba38b6c1e943ff189ff69efb7cc1c08
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61083898"
---
# <a name="watchos-workout-apps-in-xamarin"></a>watchOS 健身在 Xamarin 中的应用程序

_本文介绍如何增强功能对 watchOS 3 以及如何在 Xamarin 中实现它们的健身应用进行了 Apple。_


新 watchOS 3，与健身相关应用程序能够在 Apple Watch 上在后台运行并获得对 HealthKit 数据的访问。 其父 iOS 10 基于应用程序还具有以启动无需用户干预 watchOS 3 基于应用的功能。

将详细介绍以下主题：

## <a name="about-workout-apps"></a>有关利用应用程序

适用性和健身应用的用户可以是高度专用内容分配到针对其运行状况和适用性的目标一天中的几个小时。 因此，他们希望响应式易于使用的应用程序能够准确地收集和显示数据，并使用 Apple Health 无缝集成。

设计良好的适用性或利用应用程序可帮助用户实现其适用性目标及其活动的图表。 通过使用 Apple Watch，健身与健身应用可即时访问核心速率热量刻录和活动检测。

[![](workout-apps-images/workout01.png "适用性和健身应用示例")](workout-apps-images/workout01.png#lightbox)

WatchOS 3 的新手_后台运行_提供健身相关应用程序能够在 Apple Watch 上在后台运行并获取对 HealthKit 数据访问。

本文档将介绍后台运行功能，涵盖健身应用生命周期并展示如何利用应用程序可能会导致用户的_活动环_Apple Watch 上。

## <a name="about-workout-sessions"></a>有关健身会话

每个健身应用程序的核心是_健身会话_(`HKWorkoutSession`) 的用户可以启动和停止。 健身会话 API 很容易实现，并提供以下几个好处到健身应用，例如：

- 动作和热量刻录检测基于活动类型。
- 以用户的活动环自动发布内容。
- 在一个会话中时，应用将自动显示用户中唤醒设备 （不论是通过引发其手腕或 Apple Watch 与之交互）。

## <a name="about-background-running"></a>有关后台运行

如上面所述，借助 watchOS 3 健身应用可设置要在后台运行。 使用后台运行的健身应用可以处理 Apple Watch 的传感器数据，同时在后台运行。 例如，应用可以继续监视用户的心率，即使它不会再显示在屏幕上也是如此。

后台运行还提供了活动的健身会话，例如发送 haptic 警报，通知其当前正在进行的用户在任何时候向用户提供实时反馈的能力。

此外，后台运行允许快速更新其用户界面，使用户具有的最新的数据时它们快速查看其 Apple Watch 应用。

为了保持在 Apple Watch 上的高性能，使用后台运行的监视应用应限制后台工作，以节省电池电量的量。 如果应用正在使用过多的 CPU 在后台，它可以获取 watchOS 挂起。

### <a name="enabling-background-running"></a>启用后台运行

若要启用后台运行，请执行以下操作：

1. 在中**解决方案资源管理器**，双击监视扩展辅助 iPhone 应用程序`Info.plist`文件将其打开进行编辑。
2. 切换到**源**视图： 

    [![](workout-apps-images/plist01.png "源视图")](workout-apps-images/plist01.png#lightbox)
3. 添加名为的新项`WKBackgroundModes`并设置**类型**到`Array`: 

    [![](workout-apps-images/plist02.png "添加新的密钥调用 WKBackgroundModes")](workout-apps-images/plist02.png#lightbox)
4. 将新项添加到数组，该数组**类型**的`String`并将值`workout-processing`: 

    [![](workout-apps-images/plist03.png "将新项添加到具有 String 类型和健身处理的值的数组")](workout-apps-images/plist03.png#lightbox)
5. 保存对文件所做的更改。

## <a name="starting-a-workout-session"></a>启动健身会话

有三个主要步骤启动健身会话：

[![](workout-apps-images/workout02.png "开始测验会话三个主要步骤")](workout-apps-images/workout02.png#lightbox)

1. 应用必须请求授权 HealthKit 中访问数据。
2. 正在启动的健身类型创建健身配置对象。
3. 创建并启动使用新创建的健身配置的健身会话。

### <a name="requesting-authorization"></a>请求授权

应用可以访问用户的 HealthKit 数据之前，它必须请求并接收来自用户的授权。 根据健身应用程序的性质，它可能提供以下类型的请求：

- 若要将数据写入的授权：
    - 锻炼
- 授权读取数据：
    - 能源
    - 距离
    - 心率  

应用程序可以请求授权之前，必须将配置为访问 HealthKit。

请执行以下操作：

1. 在“解决方案资源管理器”中，双击 `Entitlements.plist` 文件，将其打开进行编辑。
2. 滚动到底部并检查**启用 HealthKit**: 

    [![](workout-apps-images/auth01.png "检查启用 HealthKit")](workout-apps-images/auth01.png#lightbox)
3. 保存对文件所做的更改。
4. 按照中的说明[显式应用 ID 和预配配置文件](~/ios/platform/healthkit.md)并[关联的应用程序 ID 和预配配置文件与你的 Xamarin.iOS 应用程序](~/ios/platform/healthkit.md)的部分[简介HealthKit](~/ios/platform/healthkit.md)文章正确设置该应用程序。
5. 最后，使用中的说明[编程运行状况工具包](~/ios/platform/healthkit.md)并[从用户请求权限](~/ios/platform/healthkit.md)的部分[简介 HealthKit](~/ios/platform/healthkit.md)文章请求若要访问用户的 HealthKit 数据存储的授权。

### <a name="setting-the-workout-configuration"></a>设置健身配置

创建使用健身配置对象的健身会话 (`HKWorkoutConfiguration`)，它指定健身类型 (如`HKWorkoutActivityType.Running`) 和健身位置 (如`HKWorkoutSessionLocationType.Outdoor`):

```csharp
using HealthKit;
...

// Create a workout configuration
var configuration = new HKWorkoutConfiguration () {
    ActivityType = HKWorkoutActivityType.Running,
    LocationType = HKWorkoutSessionLocationType.Outdoor
};
```

### <a name="creating-a-workout-session-delegate"></a>创建健身会话委托 

若要处理健身会话期间可以发生的事件，应用将需要创建健身会话委托实例。 向项目添加新类和基本的中断它`HKWorkoutSessionDelegate`类。 有关的户外运行示例，它看起来如下所示：

```csharp
using System;
using Foundation;
using WatchKit;
using HealthKit;

namespace MonkeyWorkout.MWWatchExtension
{
    public class OutdoorRunDelegate : HKWorkoutSessionDelegate
    {
        #region Computed Properties
        public HKHealthStore HealthStore { get; private set; }
        public HKWorkoutSession WorkoutSession { get; private set;}
        #endregion

        #region Constructors
        public OutdoorRunDelegate (HKHealthStore healthStore, HKWorkoutSession workoutSession)
        {
            // Initialize
            this.HealthStore = healthStore;
            this.WorkoutSession = workoutSession;

            // Attach this delegate to the session
            workoutSession.Delegate = this;
        }
        #endregion

        #region Override Methods
        public override void DidFail (HKWorkoutSession workoutSession, NSError error)
        {
            // Handle workout session failing
            RaiseFailed ();
        }

        public override void DidChangeToState (HKWorkoutSession workoutSession, HKWorkoutSessionState toState, HKWorkoutSessionState fromState, NSDate date)
        {
            // Take action based on the change in state
            switch (toState) {
            case HKWorkoutSessionState.NotStarted:
                break;
            case HKWorkoutSessionState.Paused:
                RaisePaused ();
                break;
            case HKWorkoutSessionState.Running:
                RaiseRunning ();
                break;
            case HKWorkoutSessionState.Ended:
                RaiseEnded ();
                break;
            }

        }

        public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
        {
            base.DidGenerateEvent (workoutSession, @event);
        }
        #endregion

        #region Events
        public delegate void OutdoorRunEventDelegate ();

        public event OutdoorRunEventDelegate Failed;
        internal void RaiseFailed ()
        {
            if (this.Failed != null) this.Failed ();
        }


        public event OutdoorRunEventDelegate Paused;
        internal void RaisePaused ()
        {
            if (this.Paused != null) this.Paused ();
        }

        public event OutdoorRunEventDelegate Running;
        internal void RaiseRunning ()
        {
            if (this.Running != null) this.Running ();
        }

        public event OutdoorRunEventDelegate Ended;
        internal void RaiseEnded ()
        {
            if (this.Ended != null) this.Ended ();
        }
        #endregion
    }
}
```

此类创建的健身会话更改状态，将生成的多个事件 (`DidChangeToState`) 和健身会话失败时 (`DidFail`)。 

### <a name="creating-a-workout-session"></a>创建健身会话

使用健身配置和健身会话委托将上面创建来创建新的健身会话并开始针对用户的默认 HealthKit 存储：

```csharp
using HealthKit;
...

#region Computed Properties
public HKHealthStore HealthStore { get; set;} = new HKHealthStore ();
public OutdoorRunDelegate RunDelegate { get; set; }
#endregion
...

private void StartOutdoorRun ()
{
    // Create a workout configuration
    var configuration = new HKWorkoutConfiguration () {
        ActivityType = HKWorkoutActivityType.Running,
        LocationType = HKWorkoutSessionLocationType.Outdoor
    };

    // Create workout session
    // Start workout session
    NSError error = null;
    var workoutSession = new HKWorkoutSession (configuration, out error);

    // Successful?
    if (error != null) {
        // Report error to user and return
        return;
    }

    // Create workout session delegate and wire-up events
    RunDelegate = new OutdoorRunDelegate (HealthStore, workoutSession);

    RunDelegate.Failed += () => {
        // Handle the session failing
    };

    RunDelegate.Paused += () => {
        // Handle the session being paused
    };

    RunDelegate.Running += () => {
        // Handle the session running
    };

    RunDelegate.Ended += () => {
        // Handle the session ending
    };

    // Start session
    HealthStore.StartWorkoutSession (workoutSession);
}
```

如果应用程序启动此健身会话，并且在用户切换回其手表表盘，小绿色"正在运行手册"图标将显示上面人脸：

[![](workout-apps-images/workout03.png "人脸上方显示的一个小绿色运行 man 图标")](workout-apps-images/workout03.png#lightbox)

如果用户点击此图标，它们将被带回应用程序。

## <a name="data-collection-and-control"></a>数据收集和控制

利用会话具有已配置并启动后，应用将需要收集有关该会话 （如用户的心率） 的数据和控制会话的状态：

[![](workout-apps-images/workout04.png "数据收集和控制关系图")](workout-apps-images/workout04.png#lightbox)

1. **观察示例**-应用程序将需要从 HealthKit 将处理并向用户显示中检索信息。
2. **观察事件**-应用程序将需要对 HealthKit 或从应用程序的 UI （如用户暂停健身） 生成的事件做出响应。
3. **进入运行状态**-会话已启动，并且当前正在运行。
4. **输入已暂停状态**-用户已暂停当前健身会话，并可以重新启动它在更高版本的日期。 用户可能会多次在单个的健身会话在正在运行和已暂停状态之间进行切换。
5. **结束健身会话**-用户可以随时结束健身会话或它可能会过期并结束时 （例如两个阶段运行） 按流量计费的健身本身。

最后一步是将健身会话的结果保存到用户的 HealthKit 数据存储。

### <a name="observing-healthkit-samples"></a>观察 HealthKit 示例

应用程序将需要打开_定位点对象查询_HealthKit 数据的每个点，它感兴趣，例如心率或活动能源刻录。 对于所观测到的每个数据点，一个更新处理程序将需要创建用于捕获新数据将发送到应用程序。

从这些数据点，该应用会累积总计 （例如总运行距离） 并更新它的用户界面所需。 此外，应用程序可以达到特定目标或成就，如正在运行的下一阶段完成时通知用户。

看看下面的示例代码：

```csharp
private void ObserveHealthKitSamples ()
{
    // Get the starting date of the required samples
    var datePredicate = HKQuery.GetPredicateForSamples (WorkoutSession.StartDate, null, HKQueryOptions.StrictStartDate);

    // Get data from the local device
    var devices = new NSSet<HKDevice> (new HKDevice [] { HKDevice.LocalDevice });
    var devicePredicate = HKQuery.GetPredicateForObjectsFromDevices (devices);

    // Assemble compound predicate
    var queryPredicate = NSCompoundPredicate.CreateAndPredicate (new NSPredicate [] { datePredicate, devicePredicate });

    // Get ActiveEnergyBurned
    var queryActiveEnergyBurned = new HKAnchoredObjectQuery (HKQuantityType.Create (HKQuantityTypeIdentifier.ActiveEnergyBurned), queryPredicate, null, HKSampleQuery.NoLimit, (query, addedObjects, deletedObjects, newAnchor, error) => {
        // Valid?
        if (error == null) {
            // Yes, process all returned samples
            foreach (HKSample sample in addedObjects) {
                var quantitySample = sample as HKQuantitySample;
                ActiveEnergyBurned += quantitySample.Quantity.GetDoubleValue (HKUnit.Joule);
            }
            
            // Update User Interface
            ...
        }
    });

    // Start Query
    HealthStore.ExecuteQuery (queryActiveEnergyBurned);
                                          
}
```

它将创建一个谓词，设置想要使用获取数据的开始日期`GetPredicateForSamples`方法。 它会创建一组设备使用 HealthKit 信息提取`GetPredicateForObjectsFromDevices`方法，请在这种情况下本地 Apple Watch (`HKDevice.LocalDevice`)。 两个谓词合并为复合谓词 (`NSCompoundPredicate`) 使用`CreateAndPredicate`方法。

一个新`HKAnchoredObjectQuery`为所需的数据点创建 (在这种情况下`HKQuantityTypeIdentifier.ActiveEnergyBurned`Active 能源刻录数据点)，返回的数据量没有限制 (`HKSampleQuery.NoLimit`) 和一个更新处理程序定义为处理数据是返回到应用程序从 HealthKit。 

将新数据传递到适用于给定的数据点的应用任何时候调用更新处理程序。 如果不返回任何错误，应用可以安全地读取数据、 进行任何所需的计算和更新其所需的 UI。

此代码循环通过所有的示例 (`HKSample`) 中返回`addedObjects`数组并将它们转换为 Quantity 示例 (`HKQuantitySample`)。 然后，它获取 joule 作为示例的双精度值 (`HKUnit.Joule`) 并将其累积到正在使用的活动的健身能源总和更新用户界面。

### <a name="achieved-goal-notification"></a>实现的目标通知

如上所述，当用户来实现的目标 （例如完成之后运行的第一个阶段），利用应用程序时它可以发送 haptic 反馈 Taptic 引擎通过向用户。 应用还应更新其 UI 在此情况下，因为用户很可能会提高其手腕若要查看的事件的系统提示您的反馈。

若要播放 haptic 反馈，请使用以下代码：

```csharp
// Play haptic feedback
WKInterfaceDevice.CurrentDevice.PlayHaptic (WKHapticType.Notification);
```

### <a name="observing-events"></a>观察事件

事件是应用可用于在用户的健身期间突出显示特定时间点的时间戳。 将应用直接创建某些事件，并将其保存到健身和由 HealthKit 将自动创建某些事件。

若要观察 HealthKit 创建的事件，应用将重写`DidGenerateEvent`方法的`HKWorkoutSessionDelegate`:

```csharp
using System.Collections.Generic;
...

public List<HKWorkoutEvent> WorkoutEvents { get; set; } = new List<HKWorkoutEvent> ();
...

public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
{
    base.DidGenerateEvent (workoutSession, @event);
    
    // Save HealthKit generated event
    WorkoutEvents.Add (@event);
    
    // Take action based on the type of event
    switch (@event.Type) {
    case HKWorkoutEventType.Lap:
        break;
    case HKWorkoutEventType.Marker:
        break;
    case HKWorkoutEventType.MotionPaused:
        break;
    case HKWorkoutEventType.MotionResumed:
        break;
    case HKWorkoutEventType.Pause:
        break;
    case HKWorkoutEventType.Resume:
        break;
    }
}
```

Apple watchOS 3 中添加了以下新的事件类型：

- `HKWorkoutEventType.Lap` -适用于健身分成等差部分的事件。 例如，对于将标记一个了解运行时跟踪。
- `HKWorkoutEventType.Marker` -适用于所需的任意点内健身。 例如，达到室外运行路由上的特定点。

可以创建的应用程序和存储中以便在创建图形和统计信息的更高版本使用利用这些新类型。

若要创建一个标记事件，请执行以下操作：

```csharp
using System.Collections.Generic;
...

public float MilesRun { get; set; }
public List<HKWorkoutEvent> WorkoutEvents { get; set; } = new List<HKWorkoutEvent> ();
...

public void ReachedNextMile ()
{
    // Create and save marker event
    var markerEvent = HKWorkoutEvent.Create (HKWorkoutEventType.Marker, NSDate.Now);
    WorkoutEvents.Add (markerEvent);

    // Notify user
    NotifyUserOfReachedMileGoal (++MilesRun);
}
```

此代码将创建一个标记事件的新实例 (`HKWorkoutEvent`) 并将其保存到一个专用的事件 （稍后将写入到健身会话） 的集合，并通知通过 haptics 事件的用户。

### <a name="pausing-and-resuming-workouts"></a>暂停和恢复锻炼

健身会话中的任何时候，用户可以临时暂停健身并继续在更高版本时。 例如，它们可能会暂停室内运行以获取重要的调用并在调用完成后继续运行。

应用程序的 UI 应提供一种方法来暂停和恢复健身 （通过调入 HealthKit），以便 Apple Watch 可以节省电源和数据的空间，当用户已挂起其活动。 此外，应用程序应忽略健身会话处于暂停状态时，可能会收到任何新数据点。

HealthKit 将响应暂停和继续生成暂停和恢复事件的调用。 健身会话暂停时，任何新事件或数据将发送到应用程序通过 HealthKit 之前恢复会话。

使用以下代码来暂停和恢复健身会话：

```csharp
public HKHealthStore HealthStore { get; set;} = new HKHealthStore ();
public HKWorkoutSession WorkoutSession { get; set;}
...

public void PauseWorkout ()
{
    // Pause the current workout
    HealthStore.PauseWorkoutSession (WorkoutSession);
}

public void ResumeWorkout ()
{
    // Pause the current workout
    HealthStore.ResumeWorkoutSession (WorkoutSession);
}
```

将从 HealthKit 生成的暂停和恢复事件可以处理通过重写`DidGenerateEvent`方法的`HKWorkoutSessionDelegate`:

```csharp
public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
{
    base.DidGenerateEvent (workoutSession, @event);

    // Take action based on the type of event
    switch (@event.Type) {
    case HKWorkoutEventType.Pause:
        break;
    case HKWorkoutEventType.Resume:
        break;
    }
}
```

### <a name="motion-events"></a>动作事件

此外新增到 watchOS 3，是 Motion 暂停 (`HKWorkoutEventType.MotionPaused`) 和动态恢复 (`HKWorkoutEventType.MotionResumed`) 事件。 这些事件会引发自动 HealthKit 过程正在运行的健身用户启动和停止移动时。

当应用接收到的动作暂停事件时，它应停止收集数据，直到用户在恢复运动并收到运动恢复事件。 应用应暂停的健身会话中对 Motion 暂停事件作出响应。

> [!IMPORTANT]
> 运动暂停和动作恢复事件仅支持 RunningWorkout 活动类型 (`HKWorkoutActivityType.Running`)。

同样，这些事件可以处理通过重写`DidGenerateEvent`方法的`HKWorkoutSessionDelegate`:

```csharp
public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
{
    base.DidGenerateEvent (workoutSession, @event);
    
    // Take action based on the type of event
    switch (@event.Type) {
    case HKWorkoutEventType.MotionPaused:
        break;
    case HKWorkoutEventType.MotionResumed:
        break;
    }
}

```

## <a name="ending-and-saving-the-workout-session"></a>结束和保存健身会话

当用户完成其健身时，应用将需要结束当前的健身会话并将其保存到 HealthKit 数据库。 保存到 HealthKit 锻炼将自动显示在健身活动列表中。

新增到 iOS 10，这包括以及用户的 iPhone 上的健身活动列表列表。 因此即使 Apple Watch 并不在附近，测验将显示在手机上。

包括能源样本的锻炼将更新用户的移动中的环，活动应用程序，使第三方应用现在可以参与到用户的每日移动目标。

需要执行以下步骤以结束并保存健身会话：

[![](workout-apps-images/workout05.png "结束并保存健身会话关系图")](workout-apps-images/workout05.png#lightbox)

1. 首先，应用将需要结束健身会话。
2. 利用会话保存到 HealthKit。
3. 将任何示例 （如能源或距离） 添加到已保存的健身会话。

### <a name="ending-the-session"></a>结束会话

若要结束健身会话，请调用`EndWorkoutSession`方法`HKHealthStore`传入`HKWorkoutSession`:

```csharp
public HKHealthStore HealthStore { get; private set; }
public HKWorkoutSession WorkoutSession { get; private set;}
...

public void EndOutdoorRun ()
{
    // End the current workout session
    HealthStore.EndWorkoutSession (WorkoutSession);
}
```

这将为其正常模式下重置设备传感器。 当 HealthKit 完成结束健身时，它将接收到的回调`DidChangeToState`方法的`HKWorkoutSessionDelegate`:

```csharp
public override void DidChangeToState (HKWorkoutSession workoutSession, HKWorkoutSessionState toState, HKWorkoutSessionState fromState, NSDate date)
{
    // Take action based on the change in state
    switch (toState) {
    ...
    case HKWorkoutSessionState.Ended:
        StopObservingHealthKitSamples ();
        RaiseEnded ();
        break;
    }

}
```

### <a name="saving-the-session"></a>正在保存会话

一旦应用程序已结束健身会话，它将需要创建健身 (`HKWorkout`) 并将其 （以及事件） 保存到 HealthKit 数据存储区 (`HKHealthStore`):

```csharp
public HKHealthStore HealthStore { get; private set; }
public HKWorkoutSession WorkoutSession { get; private set;}
public float MilesRun { get; set; }
public double ActiveEnergyBurned { get; set;}
public List<HKWorkoutEvent> WorkoutEvents { get; set; } = new List<HKWorkoutEvent> ();
...

private void SaveWorkoutSession ()
{
    // Build required workout quantities 
    var energyBurned = HKQuantity.FromQuantity (HKUnit.Joule, ActiveEnergyBurned);
    var distance = HKQuantity.FromQuantity (HKUnit.Mile, MilesRun);

    // Create any required metadata
    var metadata = new NSMutableDictionary ();
    metadata.Add (new NSString ("HKMetadataKeyIndoorWorkout"), new NSString ("NO"));

    // Create workout
    var workout = HKWorkout.Create (HKWorkoutActivityType.Running, 
                                    WorkoutSession.StartDate, 
                                    NSDate.Now, 
                                    WorkoutEvents.ToArray (), 
                                    energyBurned, 
                                    distance, 
                                    metadata);

    // Save to HealthKit
    HealthStore.SaveObject (workout, (successful, error) => {
        // Handle any errors
        if (error == null) {
            // Was the save successful
            if (successful) {

            }
        } else {
            // Report error
        }
    });

}
```

此代码将创建需要使用能源的总量和距离来作为健身`HKQuantity`对象。 创建定义健身元数据的字典和指定的健身位置：

```csharp
metadata.Add (new NSString ("HKMetadataKeyIndoorWorkout"), new NSString ("NO"));
```

一个新`HKWorkout`对象创建具有相同`HKWorkoutActivityType`作为`HKWorkoutSession`，起始和结束日期，事件列表中的 （正在累加从前面的章节） 刻录，能源总距离和元数据字典。 此对象保存到运行状况存储和处理任何错误。  

### <a name="adding-samples"></a>添加示例

对应用保存到健身一组示例，HealthKit 生成这些示例和健身本身之间的连接，以便应用可以在以后对给定健身与关联的所有样本查询 HealthKit。 使用此信息，应用可以从健身数据生成关系图，并绘制针对健身时间线。

若要参与到活动应用的移动环应用，它必须包括已保存的健身能源示例。 此外，距离和能源的总计值必须与匹配的示例应用程序将与已保存的健身相关联的总和。

若要添加到已保存的健身示例，请执行以下操作：

```csharp
using System.Collections.Generic;
using WatchKit;
using HealthKit;
...

public HKHealthStore HealthStore { get; private set; }
public List<HKSample> WorkoutSamples { get; set; } = new List<HKSample> ();
...


private void SaveWorkoutSamples (HKWorkout workout)
{
    // Add samples to saved workout
    HealthStore.AddSamples (WorkoutSamples.ToArray (), workout, (success, error) => {
        // Handle any errors
        if (error == null) {
            // Was the save successful
            if (success) {

            }
        } else {
            // Report error
        }
    });
}
```

（可选） 该应用程序可以计算以及创建示例或一个庞大示例 （跨越健身的整个范围），然后获取已保存的健身与相关联的较小子集。

## <a name="workouts-and-ios-10"></a>锻炼和 iOS 10

每个 watchOS 3 锻炼应用具有父 iOS 10 基于的锻炼应用和，新到 iOS 10，此 iOS 应用程序可以用于启动测验，将 Apple Watch 置于锻炼模式 （无需用户干预） 并在后台运行模式下运行 watchOS 应用 (请参阅[有关后台运行](#about-background-running)上面有关详细信息)。

在 watchOS 应用运行时，它可以用于消息传送和与父 iOS 应用之间的通信使用 WatchConnectivity。

看看此过程的工作原理：

[![](workout-apps-images/workout06.png "iPhone 和 Apple Watch 通信关系图")](workout-apps-images/workout06.png#lightbox)

1. IPhone 应用创建`HKWorkoutConfiguration`对象并设置的健身类型和位置。
2. `HKWorkoutConfiguration`对象发送应用的 Apple Watch 版本，并且，如果尚未运行，它由操作系统启动。
3. 使用已传递的健身配置中，watchOS 3 应用程序启动新的健身会话 (`HKWorkoutSession`)。

> [!IMPORTANT]
> 为了使父 iPhone 应用在 Apple Watch 上启动测验，watchOS 3 应用程序必须具有后台运行已启用。 请参阅[启用后台运行](#enabling-background-running)上面的更多详细信息。

此过程是直接在 watchOS 3 应用中启动健身会话的过程非常相似。 在 iPhone 上使用以下代码：

```csharp
using System;
using HealthKit;
using WatchConnectivity;
...

#region Computed Properties
public HKHealthStore HealthStore { get; set; } = new HKHealthStore ();
public WCSession ConnectivitySession { get; set; } = WCSession.DefaultSession;
#endregion
...

private void StartOutdoorRun ()
{
    // Can the app communicate with the watchOS version of the app?
    if (ConnectivitySession.ActivationState == WCSessionActivationState.Activated && ConnectivitySession.WatchAppInstalled) {
        // Create a workout configuration
        var configuration = new HKWorkoutConfiguration () {
            ActivityType = HKWorkoutActivityType.Running,
            LocationType = HKWorkoutSessionLocationType.Outdoor
        };

        // Start watch app
        HealthStore.StartWatchApp (configuration, (success, error) => {
            // Handle any errors
            if (error == null) {
                // Was the save successful
                if (success) {
                    ...
                }
            } else {
                // Report error
                ...
            }
        });
    }
}
```

此代码可确保安装 watchOS 版本的应用和 iPhone 版本可以首先连接到它：

```csharp
if (ConnectivitySession.ActivationState == WCSessionActivationState.Activated && ConnectivitySession.WatchAppInstalled) {
    ...
}
```

然后它会创建`HKWorkoutConfiguration`像往常一样，并使用`StartWatchApp`方法的`HKHealthStore`来将其发送到 Apple Watch 并启动应用程序和健身会话。

监视 OS 应用上使用下面的代码和`WKExtensionDelegate`:

```csharp
using WatchKit;
using HealthKit;
...

#region Computed Properties
public HKHealthStore HealthStore { get; set;} = new HKHealthStore ();
public OutdoorRunDelegate RunDelegate { get; set; }
#endregion
...


public override void HandleWorkoutConfiguration (HKWorkoutConfiguration workoutConfiguration)
{
    // Create workout session
    // Start workout session
    NSError error = null;
    var workoutSession = new HKWorkoutSession (workoutConfiguration, out error);

    // Successful?
    if (error != null) {
        // Report error to user and return
        return;
    }

    // Create workout session delegate and wire-up events
    RunDelegate = new OutdoorRunDelegate (HealthStore, workoutSession);

    RunDelegate.Failed += () => {
        // Handle the session failing
    };

    RunDelegate.Paused += () => {
        // Handle the session being paused
    };

    RunDelegate.Running += () => {
        // Handle the session running
    };

    RunDelegate.Ended += () => {
        // Handle the session ending
    };

    // Start session
    HealthStore.StartWorkoutSession (workoutSession);
}
```

所需`HKWorkoutConfiguration`，并创建新`HKWorkoutSession`，并将附加的自定义实例`HKWorkoutSessionDelegate`。 针对用户的 HealthKit 运行状况存储启动健身会话。

## <a name="bringing-all-the-pieces-together"></a>将所有部分集中起来

将所有本文档中提供的信息，watchOS 3 基于的测验应用和其父 iOS 10 基于的测验应用可能包括以下几个部分：

1. **iOS 10 `ViewController.cs`**  -处理监视连接会话和健身 Apple Watch 上的启动。
2. **watchOS 3 `ExtensionDelegate.cs`**  -处理健身应用的 watchOS 3 版本。
3. **watchOS 3 `OutdoorRunDelegate.cs`**  -自定义`HKWorkoutSessionDelegate`健身处理事件。

> [!IMPORTANT]
> 以下各节中所示的代码仅包括实现 watchOS 3 中利用应用提供的新的增强型功能所需的部分。 支持的所有代码以及用于显示和更新用户界面的代码则不包含，但可以轻松地创建按照我们的其他 watchOS 文档。<p/>



### <a name="viewcontrollercs"></a>ViewController.cs

`ViewController.cs`中利用应用程序的父 iOS 10 版本的文件应包括以下代码：

```csharp
using System;
using HealthKit;
using UIKit;
using WatchConnectivity;

namespace MonkeyWorkout
{
    public partial class ViewController : UIViewController
    {
        #region Computed Properties
        public HKHealthStore HealthStore { get; set; } = new HKHealthStore ();
        public WCSession ConnectivitySession { get; set; } = WCSession.DefaultSession;
        #endregion

        #region Constructors
        protected ViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Private Methods
        private void InitializeWatchConnectivity ()
        {
            // Is Watch Connectivity supported?
            if (!WCSession.IsSupported) {
                // No, abort
                return;
            }

            // Is the session already active?
            if (ConnectivitySession.ActivationState != WCSessionActivationState.Activated) {
                // No, start session
                ConnectivitySession.ActivateSession ();
            }
        }

        private void StartOutdoorRun ()
        {
            // Can the app communicate with the watchOS version of the app?
            if (ConnectivitySession.ActivationState == WCSessionActivationState.Activated && ConnectivitySession.WatchAppInstalled) {
                // Create a workout configuration
                var configuration = new HKWorkoutConfiguration () {
                    ActivityType = HKWorkoutActivityType.Running,
                    LocationType = HKWorkoutSessionLocationType.Outdoor
                };

                // Start watch app
                HealthStore.StartWatchApp (configuration, (success, error) => {
                    // Handle any errors
                    if (error == null) {
                        // Was the save successful
                        if (success) {
                            ...
                        }
                    } else {
                        // Report error
                        ...
                    }
                });
            }
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Start Watch Connectivity
            InitializeWatchConnectivity ();
        }
        #endregion
    }
}
```

### <a name="extensiondelegatecs"></a>ExtensionDelegate.cs

`ExtensionDelegate.cs`健身应用的 watchOS 3 版本中的文件应包括以下代码：

```csharp
using System;
using Foundation;
using WatchKit;
using HealthKit;

namespace MonkeyWorkout.MWWatchExtension
{
    public class ExtensionDelegate : WKExtensionDelegate
    {
        #region Computed Properties
        public HKHealthStore HealthStore { get; set;} = new HKHealthStore ();
        public OutdoorRunDelegate RunDelegate { get; set; }
        #endregion

        #region Constructors
        public ExtensionDelegate ()
        {
            
        }
        #endregion

        #region Private Methods
        private void StartWorkoutSession (HKWorkoutConfiguration workoutConfiguration)
        {
            // Create workout session
            // Start workout session
            NSError error = null;
            var workoutSession = new HKWorkoutSession (workoutConfiguration, out error);

            // Successful?
            if (error != null) {
                // Report error to user and return
                return;
            }

            // Create workout session delegate and wire-up events
            RunDelegate = new OutdoorRunDelegate (HealthStore, workoutSession);

            RunDelegate.Failed += () => {
                // Handle the session failing
                ...
            };

            RunDelegate.Paused += () => {
                // Handle the session being paused
                ...
            };

            RunDelegate.Running += () => {
                // Handle the session running
                ...
            };

            RunDelegate.Ended += () => {
                // Handle the session ending
                ...
            };
            
            RunDelegate.ReachedMileGoal += (miles) => {
                // Handle the reaching a session goal
                ...
            };

            RunDelegate.HealthKitSamplesUpdated += () => {
                // Update UI as required
                ...
            };

            // Start session
            HealthStore.StartWorkoutSession (workoutSession);
        }

        private void StartOutdoorRun ()
        {
            // Create a workout configuration
            var workoutConfiguration = new HKWorkoutConfiguration () {
                ActivityType = HKWorkoutActivityType.Running,
                LocationType = HKWorkoutSessionLocationType.Outdoor
            };

            // Start the session
            StartWorkoutSession (workoutConfiguration);
        }
        #endregion

        #region Override Methods
        public override void HandleWorkoutConfiguration (HKWorkoutConfiguration workoutConfiguration)
        {
            // Start the session
            StartWorkoutSession (workoutConfiguration);
        }
        #endregion
    }
}
```

### <a name="outdoorrundelegatecs"></a>OutdoorRunDelegate.cs

`OutdoorRunDelegate.cs`健身应用的 watchOS 3 版本中的文件应包括以下代码：

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using WatchKit;
using HealthKit;

namespace MonkeyWorkout.MWWatchExtension
{
    public class OutdoorRunDelegate : HKWorkoutSessionDelegate
    {
        #region Private Variables
        private HKAnchoredObjectQuery QueryActiveEnergyBurned;
        #endregion

        #region Computed Properties
        public HKHealthStore HealthStore { get; private set; }
        public HKWorkoutSession WorkoutSession { get; private set;}
        public float MilesRun { get; set; }
        public double ActiveEnergyBurned { get; set;}
        public List<HKWorkoutEvent> WorkoutEvents { get; set; } = new List<HKWorkoutEvent> ();
        public List<HKSample> WorkoutSamples { get; set; } = new List<HKSample> ();
        #endregion

        #region Constructors
        public OutdoorRunDelegate (HKHealthStore healthStore, HKWorkoutSession workoutSession)
        {
            // Initialize
            this.HealthStore = healthStore;
            this.WorkoutSession = workoutSession;

            // Attach this delegate to the session
            workoutSession.Delegate = this;

        }
        #endregion

        #region Private Methods
        private void ObserveHealthKitSamples ()
        {
            // Get the starting date of the required samples
            var datePredicate = HKQuery.GetPredicateForSamples (WorkoutSession.StartDate, null, HKQueryOptions.StrictStartDate);

            // Get data from the local device
            var devices = new NSSet<HKDevice> (new HKDevice [] { HKDevice.LocalDevice });
            var devicePredicate = HKQuery.GetPredicateForObjectsFromDevices (devices);

            // Assemble compound predicate
            var queryPredicate = NSCompoundPredicate.CreateAndPredicate (new NSPredicate [] { datePredicate, devicePredicate });

            // Get ActiveEnergyBurned
            QueryActiveEnergyBurned = new HKAnchoredObjectQuery (HKQuantityType.Create (HKQuantityTypeIdentifier.ActiveEnergyBurned), queryPredicate, null, HKSampleQuery.NoLimit, (query, addedObjects, deletedObjects, newAnchor, error) => {
                // Valid?
                if (error == null) {
                    // Yes, process all returned samples
                    foreach (HKSample sample in addedObjects) {
                        // Accumulate totals
                        var quantitySample = sample as HKQuantitySample;
                        ActiveEnergyBurned += quantitySample.Quantity.GetDoubleValue (HKUnit.Joule);

                        // Save samples
                        WorkoutSamples.Add (sample);
                    }

                    // Inform caller
                    RaiseHealthKitSamplesUpdated ();
                }
            });

            // Start Query
            HealthStore.ExecuteQuery (QueryActiveEnergyBurned);
                                                  
        }

        private void StopObservingHealthKitSamples ()
        {
            // Stop query
            HealthStore.StopQuery (QueryActiveEnergyBurned);
        }

        private void ResumeObservingHealthkitSamples ()
        {
            // Resume current queries 
            HealthStore.ExecuteQuery (QueryActiveEnergyBurned);
        }

        private void NotifyUserOfReachedMileGoal (float miles)
        {
            // Play haptic feedback
            WKInterfaceDevice.CurrentDevice.PlayHaptic (WKHapticType.Notification);

            // Raise event
            RaiseReachedMileGoal (miles);
        }

        private void SaveWorkoutSession ()
        {
            // Build required workout quantities
            var energyBurned = HKQuantity.FromQuantity (HKUnit.Joule, ActiveEnergyBurned);
            var distance = HKQuantity.FromQuantity (HKUnit.Mile, MilesRun);

            // Create any required metadata
            var metadata = new NSMutableDictionary ();
            metadata.Add (new NSString ("HKMetadataKeyIndoorWorkout"), new NSString ("NO"));

            // Create workout
            var workout = HKWorkout.Create (HKWorkoutActivityType.Running, 
                                            WorkoutSession.StartDate, 
                                            NSDate.Now, 
                                            WorkoutEvents.ToArray (), 
                                            energyBurned, 
                                            distance, 
                                            metadata);

            // Save to HealthKit
            HealthStore.SaveObject (workout, (successful, error) => {
                // Handle any errors
                if (error == null) {
                    // Was the save successful
                    if (successful) {
                        // Add samples to workout
                        SaveWorkoutSamples (workout);
                    }
                } else {
                    // Report error
                    ...
                }
            });

        }

        private void SaveWorkoutSamples (HKWorkout workout)
        {
            // Add samples to saved workout
            HealthStore.AddSamples (WorkoutSamples.ToArray (), workout, (success, error) => {
                // Handle any errors
                if (error == null) {
                    // Was the save successful
                    if (success) {
                        ...
                    }
                } else {
                    // Report error
                    ...
                }
            });
        }
        #endregion

        #region Public Methods
        public void PauseWorkout ()
        {
            // Pause the current workout
            HealthStore.PauseWorkoutSession (WorkoutSession);
        }

        public void ResumeWorkout ()
        {
            // Pause the current workout
            HealthStore.ResumeWorkoutSession (WorkoutSession);
        }

        public void ReachedNextMile ()
        {
            // Create and save marker event
            var markerEvent = HKWorkoutEvent.Create (HKWorkoutEventType.Marker, NSDate.Now);
            WorkoutEvents.Add (markerEvent);

            // Notify user
            NotifyUserOfReachedMileGoal (++MilesRun);
        }

        public void EndOutdoorRun ()
        {
            // End the current workout session
            HealthStore.EndWorkoutSession (WorkoutSession);
        }
        #endregion

        #region Override Methods
        public override void DidFail (HKWorkoutSession workoutSession, NSError error)
        {
            // Handle workout session failing
            RaiseFailed ();
        }

        public override void DidChangeToState (HKWorkoutSession workoutSession, HKWorkoutSessionState toState, HKWorkoutSessionState fromState, NSDate date)
        {
            // Take action based on the change in state
            switch (toState) {
            case HKWorkoutSessionState.NotStarted:
                break;
            case HKWorkoutSessionState.Paused:
                StopObservingHealthKitSamples ();
                RaisePaused ();
                break;
            case HKWorkoutSessionState.Running:
                if (fromState == HKWorkoutSessionState.Paused) {
                    ResumeObservingHealthkitSamples ();
                } else {
                    ObserveHealthKitSamples ();
                }
                RaiseRunning ();
                break;
            case HKWorkoutSessionState.Ended:
                StopObservingHealthKitSamples ();
                SaveWorkoutSession ();
                RaiseEnded ();
                break;
            }

        }

        public override void DidGenerateEvent (HKWorkoutSession workoutSession, HKWorkoutEvent @event)
        {
            base.DidGenerateEvent (workoutSession, @event);

            // Save HealthKit generated event
            WorkoutEvents.Add (@event);

            // Take action based on the type of event
            switch (@event.Type) {
            case HKWorkoutEventType.Lap:
                ...
                break;
            case HKWorkoutEventType.Marker:
                ...
                break;
            case HKWorkoutEventType.MotionPaused:
                ...
                break;
            case HKWorkoutEventType.MotionResumed:
                ...
                break;
            case HKWorkoutEventType.Pause:
                ...
                break;
            case HKWorkoutEventType.Resume:
                ...
                break;
            }
        }
        #endregion

        #region Events
        public delegate void OutdoorRunEventDelegate ();
        public delegate void OutdoorRunMileGoalDelegate (float miles);

        public event OutdoorRunEventDelegate Failed;
        internal void RaiseFailed ()
        {
            if (this.Failed != null) this.Failed ();
        }


        public event OutdoorRunEventDelegate Paused;
        internal void RaisePaused ()
        {
            if (this.Paused != null) this.Paused ();
        }

        public event OutdoorRunEventDelegate Running;
        internal void RaiseRunning ()
        {
            if (this.Running != null) this.Running ();
        }

        public event OutdoorRunEventDelegate Ended;
        internal void RaiseEnded ()
        {
            if (this.Ended != null) this.Ended ();
        }

        public event OutdoorRunMileGoalDelegate ReachedMileGoal;
        internal void RaiseReachedMileGoal (float miles)
        {
            if (this.ReachedMileGoal != null) this.ReachedMileGoal (miles);
        }

        public event OutdoorRunEventDelegate HealthKitSamplesUpdated;
        internal void RaiseHealthKitSamplesUpdated ()
        {
            if (this.HealthKitSamplesUpdated != null) this.HealthKitSamplesUpdated ();
        }
        #endregion
    }
}
```

## <a name="best-practices"></a>最佳做法

使用以下最佳实践设计和实现 watchOS 3 和 iOS 10 中的健身应用时，Apple 提供建议：

- 请确保 watchOS 3 健身应用程序仍有功能，即使它是无法连接到 iPhone 和 iOS 10 版本的应用。
- GPS 会不可用，因为它是能够生成而无需 GPS 距离示例时，请使用 HealthKit 距离。
- 允许用户从 Apple Watch 或 iPhone 开始测验。
- 允许应用从其他源 （例如，其他第三方应用） 在其历史数据视图中显示锻炼。
- 请确保应用不会不显示删除锻炼历史数据中。

## <a name="summary"></a>总结

本文只讨论了增强功能对 watchOS 3 以及如何在 Xamarin 中实现它们的健身应用进行了 Apple。



## <a name="related-links"></a>相关链接

- [iOS 10 示例](https://developer.xamarin.com/samples/ios/iOS10/)
- [HealthKit 简介](~/ios/platform/healthkit.md)
