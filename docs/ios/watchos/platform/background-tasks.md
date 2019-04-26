---
title: watchOS 在 Xamarin 中的后台任务
description: 本文档介绍如何使用 Xamarin，看一看类型的后台任务，请使用资源，实现后台任务、 计划、 最佳实践和的详细信息中的 watchOS 后台任务。
ms.prod: xamarin
ms.assetid: 2049C430-7566-45F8-9E3D-1446F484981E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/13/2017
ms.openlocfilehash: 45886d787ecc40c9e11ce0c713ffa22819e29db2
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61227239"
---
# <a name="watchos-background-tasks-in-xamarin"></a>watchOS 在 Xamarin 中的后台任务

Watchos 3 中，有三种主要方式，watch 应用可以保留其信息保持最新状态： 

- 使用多个新的后台任务之一。 
- 具有一个其复杂性上手表表盘 （使它更长时间来更新）。 
- 具有向新的停靠到应用的用户 pin (其中其保存在内存中，经常更新)。 

## <a name="keeping-an-app-up-to-date"></a>使应用保持最新

在讨论之前所有开发人员可以保持 watchOS 应用的数据和用户界面当前的和已更新的方式，本部分将介绍在一组典型的使用模式和其 iPhone 和全天基于其 Apple Watch 之间移动用户可能会如何 时间和活动他们当前执行的操作 （如推动）。

请参见以下示例：

[![](background-tasks-images/update00.png "其 iPhone 和全天其 Apple Watch 之间移动用户可能会如何")](background-tasks-images/update00.png#lightbox)

1. 在早上，喝杯咖啡，行中等待时在用户浏览几分钟内其 iPhone 上的当前消息。
2. 在离开咖啡厅前, 它们快速检查其 watch 表盘上一个问题与天气。
3. 在之前的午餐，它们使用地图应用程序在 iPhone 上查找附近的餐馆和预订保留项，以满足客户端。
4. 在旅行到餐馆，以及快速浏览其 Apple Watch 上它们收到通知，他们知道其午餐约会延迟运行。
5. 在晚上，它们使用地图应用程序在 iPhone 上主页推动之前检查流量。
6. 路上家庭，它们接收 iMessage 通知要求他们其 Apple Watch 上的选取某些牛奶，它们使用快速答复功能发送响应"确定"。

由于"速览"（不超过 3 秒） 性质如何用户想要使用 Apple Watch 应用，存在，通常并不足够的时间进行应用程序以提取所需的信息和向用户显示之前更新其 UI。

使用新的 Api Apple 已包含在 watchOS 3，该应用可计划的_后台刷新_和用户请求它之前已准备好所需的信息。 执行天气复杂性上文所述的示例：

[![](background-tasks-images/update01.png "天气复杂性的一个示例")](background-tasks-images/update01.png#lightbox)

1. 要唤醒系统在特定时间的应用程序计划。 
2. 此应用程序，它将需要生成更新的信息。
3. 应用程序将重新生成其用户界面，以反映新的数据。
4. 当用户向应用程序的复杂性上看时，它而无需用户无需等待的更新具有最新信息。

如上图所示，watchOS 系统唤醒使用一个或多个任务，其中它有一个非常有限的可用池的应用：

[![](background-tasks-images/update02.png "WatchOS 系统唤醒使用一个或多个任务的应用")](background-tasks-images/update02.png#lightbox)

Apple 建议此任务的最有效地利用 （因为它是这种有限的资源应用程序） 通过保留到它上面，直到应用完更新本身的过程。

系统会传送这些任务通过调用新`HandleBackgroundTasks`方法的`WKExtensionDelegate`委托。 例如：

```csharp
using System;
using Foundation;
using WatchKit;

namespace MonkeyWatch.MonkeySeeExtension
{
    public class ExtensionDelegate : WKExtensionDelegate
    {
        #region Constructors
        public ExtensionDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void HandleBackgroundTasks (NSSet<WKRefreshBackgroundTask> backgroundTasks)
        {
            // Handle background request here
            ...
        }
        #endregion
    }
}
```

当应用程序完成给定的任务时，它将其返回给系统通过将其完成标记：

[![](background-tasks-images/update03.png "该任务返回到系统通过将其完成标记")](background-tasks-images/update03.png#lightbox)

<a name="New-Background-Tasks" />

## <a name="new-background-tasks"></a>新的后台任务

watchOS 3 引入了多个应用程序可用于更新确保它具有内容的用户需要先打开应用，如其信息的后台任务：

- **后台应用程序刷新**- [WKApplicationRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkapplicationrefreshbackgroundtask)任务，应用程序以更新其在后台中的状态。 通常，这将包括另一个任务，例如从 internet 中使用下载新内容[NSUrlSession](https://developer.apple.com/reference/foundation/nsurlsession)。
- **后台刷新快照**- [WKSnapshotRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wksnapshotrefreshbackgroundtask)任务，应用程序以更新其内容和 UI 之前系统采用将用于填充停靠的快照。
- **监视连接的背景**- [WKWatchConnectivityRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkwatchconnectivityrefreshbackgroundtask)从配对的 iPhone 接收背景数据时，应用程序启动的任务。
- **后台 URL 会话**- [WKURLSessionRefreshBackgroundTask](https://developer.apple.com/reference/watchkit/wkurlsessionrefreshbackgroundtask)后台传输需要授权或完成 （成功或错误） 时，应用程序启动的任务。

将以下各节中详细介绍这些任务。

<a name="WKApplicationRefreshBackgroundTask" />

### <a name="wkapplicationrefreshbackgroundtask"></a>WKApplicationRefreshBackgroundTask

`WKApplicationRefreshBackgroundTask`可计划在将来某个日期唤醒应用的通用任务：

[![](background-tasks-images/update04.png "在将来某个日期唤醒 WKApplicationRefreshBackgroundTask")](background-tasks-images/update04.png#lightbox)

中的任务运行时，应用可以制作任意类型如更新本地处理的复杂性时间线或提取与一些必要的数据`NSUrlSession`。


<a name="WKURLSessionRefreshBackgroundTask" />

### <a name="wkurlsessionrefreshbackgroundtask"></a>WKURLSessionRefreshBackgroundTask

系统将发送`WKURLSessionRefreshBackgroundTask`正在下载且已准备好处理由应用程序数据结束时：

[![](background-tasks-images/update05.png "下载完成后数据 WKURLSessionRefreshBackgroundTask")](background-tasks-images/update05.png#lightbox)

运行时在后台下载数据不会保留应用程序。 相反，应用计划的请求数据，则它已挂起，系统处理数据，仅 reawakening 应用时下载已完成的下载。

<a name="WKSnapshotRefreshBackgroundTask" />

### <a name="wksnapshotrefreshbackgroundtask"></a>WKSnapshotRefreshBackgroundTask

在 watchOS 3，Apple 已添加了用户可以将固定其最喜欢的应用并快速访问它们的停靠。 当用户在 Apple Watch 上按端按钮时，将显示一系列固定的应用快照。 左或向右查找所需的应用，然后点击以启动该快照替换为正在运行的应用界面的应用，用户可以往下轻扫。

[![](background-tasks-images/update06.png "快照替换为正在运行的应用程序界面")](background-tasks-images/update06.png#lightbox)

系统会定期创建快照的应用程序的 UI (通过发送`WKSnapshotRefreshBackgroundTask`)，并使用这些快照以填充停靠。 watchOS 使应用能够更新其内容和 UI 之前拍摄此快照。

快照是在 watchOS 3 中非常重要，因为他们充当应用程序的预览和启动映像。 如果用户会在停靠中应用，它将展开为全屏显示、 输入前台并开始运行，所以非常有必要快照是最新：

[![](background-tasks-images/update07.png "如果用户会在停靠中应用，它将扩展到全屏显示")](background-tasks-images/update07.png#lightbox)

同样，系统将发出`WKSnapshotRefreshBackgroundTask`，以便应用程序可以 （通过更新数据和 UI） 来准备之前拍摄快照：

[![](background-tasks-images/update08.png "应用程序可以通过更新数据和 UI 之前拍摄快照准备")](background-tasks-images/update08.png#lightbox)

当应用程序将标记`WKSnapshotRefreshBackgroundTask`完成后，系统将自动拍摄快照的应用程序的 UI。

> [!IMPORTANT]
> 务必要始终先安排` WKSnapshotRefreshBackgroundTask`后收到新数据并更新其用户界面应用程序或用户将无法看到已修改的信息。




此外，当用户从应用程序接收通知，并点击它以将应用程序置于前台，需要由于它充当启动屏幕也是最新的快照：

[![](background-tasks-images/update09.png "用户会收到通知的应用中，点击它以将应用程序置于前台")](background-tasks-images/update09.png#lightbox)

如果由于用户交互的 watchOS 应用已超过一小时，将能够返回到其默认状态。 默认状态有不同的应用到不同的含义，并根据应用的设计，它可能不会默认状态根本。

<!--TODO - Possibly link to Apple's Designing Great Apple Watch Experiences video or add our own version here...-->

<a name="WKWatchConnectivityRefreshBackgroundTask" />

### <a name="wkwatchconnectivityrefreshbackgroundtask"></a>WKWatchConnectivityRefreshBackgroundTask

在 watchOS 3，Apple 已集成监视连接到后台刷新 API，以通过新`WKWatchConnectivityRefreshBackgroundTask`。 使用这一新功能，iPhone 应用可以提供新数据到其监视应用程序对应的 watchOS 应用在后台运行时：

[![](background-tasks-images/update10.png "在 watchOS 应用在后台运行时的 iPhone 应用可以将新数据传递到其监视应用程序对应的")](background-tasks-images/update10.png#lightbox)

启动复杂性推送时，应用程序上下文中，发送文件或 iphone 版应用中更新用户信息将唤醒在后台中的 Apple Watch 应用。

当通过唤醒 watch 应用`WKWatchConnectivityRefreshBackgroundTask`它将需要使用标准 API 方法 iphone 版应用中接收的数据。

[![](background-tasks-images/update11.png "WKWatchConnectivityRefreshBackgroundTask 数据流")](background-tasks-images/update11.png#lightbox)

1. 请确保该会话已激活。
2. 监视新`HasContentPending`属性，只要值是`true`，应用程序仍包含要处理的数据。 如之前，应用应保留到该任务，直到它已完成处理的所有数据。
3. 当没有更多可处理的数据 (`HasContentPending = false`)，将标记任务已完成将其返回给系统。 无法执行此操作将用完的应用分配的后台运行时导致的故障报告。

<a name="The-Background-API-Lifecycle" />

## <a name="the-background-api-lifecycle"></a>背景 API 生命周期

将所有新的后台任务 API 部分放在一起，一组典型的交互将如下所示：

[![](background-tasks-images/update12.png "背景 API 生命周期")](background-tasks-images/update12.png#lightbox)

1. 首先，在 watchOS 应用计划背景而唤醒于某个时间点为将来的任务。
2. 唤醒系统应用，并将其发送任务。
3. 应用程序处理要完成任何工作所需的任务。
4. 作为结果的处理任务，该应用程序可能需要计划任务以完成更多的工作在将来，如下载更多的内容使用的更多背景`NSUrlSession`。
5. 应用标记完成此任务，并将其返回给系统。

<a name="Using-Resources-Responsibly" />

## <a name="using-resources-responsibly"></a>负责任地使用资源

很重要，watchOS 应用的行为与负责任地此生态系统中通过限制其消耗系统的共享资源。

看看以下方案：

[![](background-tasks-images/update13.png "WatchOS 应用限制其消耗系统的共享资源")](background-tasks-images/update13.png#lightbox)

1. 下午 1:00，用户启动的 watchOS 应用。
2. 应用计划任务唤醒并在下午 2:00 一小时内下载新内容。
3. 下午 1:50 用户重新打开应用，从而让它在这一次更新其数据和 UI。
4. 而不是让任务唤醒应用在 10 分钟后，应用应重新计划为每小时运行更高版本在下午 2:50 点的任务。

虽然每个应用程序不同，Apple 建议查找模式的使用情况，如所示的这些更高版本以帮助节省系统资源。

<a name="Implementing-Background-Tasks" />

## <a name="implementing-background-tasks"></a>执行后台任务

作为示例，本文档将向用户报告足球得分的虚设 MonkeySoccer 体育应用。 

看看下面的典型使用方案：

[![](background-tasks-images/update14.png "典型使用方案")](background-tasks-images/update14.png#lightbox)

用户最喜欢足球团队进行播放匹配从 7:00 PM 大项到晚上 9:00，因此应用程序应期望用户在进行定期检查分数和上一个 30 分钟的更新间隔决定。

1. 用户打开应用程序和计划任务的后台更新 30 分钟后。 背景 API 允许仅一种类型的后台任务以在给定时间运行。
2. 应用程序接收任务并更新其数据和 UI，然后计划另一个后台任务 30 分钟后。 它是开发人员会记住安排另一个后台任务，或应用程序将永远不会被重新唤醒以获取更多更新而言十分重要。
3. 同样，应用程序接收任务并更新其数据、 更新其 UI 和安排另一个后台任务 30 分钟后。
4. 再次重复相同的过程。
5. 任务接收到的最后一个背景和应用程序再次更新其数据和 UI。 由于这是它不会为新的后台刷新计划的最终分数。 

<a name="Scheduling-for-Background-Update" />

## <a name="scheduling-for-background-update"></a>计划后台更新

给定上述情况中，MonkeySoccer 应用可以使用下面的代码的后台更新计划：

```csharp
private void ScheduleNextBackgroundUpdate ()
{
    // Create a fire date 30 minutes into the future
    var fireDate = NSDate.FromTimeIntervalSinceNow (30 * 60);

    // Create 
    var userInfo = new NSMutableDictionary ();
    userInfo.Add (new NSString ("LastActiveDate"), NSDate.FromTimeIntervalSinceNow(0));
    userInfo.Add (new NSString ("Reason"), new NSString ("UpdateScore"));

    // Schedule for update
    WKExtension.SharedExtension.ScheduleBackgroundRefresh (fireDate, userInfo, (error) => {
        // Was the Task successfully scheduled?
        if (error == null) {
            // Yes, handle if needed
        } else {
            // No, report error
        }
    });
}
```

创建一个新`NSDate`30 分钟到将来当应用想要被唤醒，并创建`NSMutableDictionary`来保存请求的任务的详细信息。 `ScheduleBackgroundRefresh`方法的`SharedExtension`用于请求在任务安排。

系统会返回`NSError`如果无法进行计划请求的任务。

<a name="Processing-the-Update" />

## <a name="processing-the-update"></a>处理更新

接下来，更详细地介绍在 5 分钟窗口显示所需更新分数的步骤：

[![](background-tasks-images/update15.png "5 分钟窗口显示所需更新分数的步骤")](background-tasks-images/update15.png#lightbox)

1. 晚上 7:30:02 应用是由系统唤醒并给定更新后台任务。 其首要任务是从服务器获取最新分数。 请参阅[计划 NSUrlSession](#Scheduling-a-NSUrlSession)下面。
2. 7:30:05 应用完成原始任务，则系统将让应用程序进入睡眠状态，并继续下载在后台所请求的数据。
3. 当系统完成下载时，它会创建新任务要唤醒该应用程序，以便处理下载的信息。 请参阅[处理后台任务](#Handling-Background-Tasks)并[处理下载完成](#Handling-the-Download-Completing)下面。 
4. 该应用将保存更新后的信息，并将标记完成的任务。 开发人员可能希望在此期间，更新应用程序的用户界面，但计划快照任务来处理该过程，Apple 提供建议。 请参阅[计划快照更新](#Scheduling-a-Snapshot-Update)下面。
5. 应用程序接收快照任务、 更新其用户界面并将标记完成的任务。 请参阅[处理快照更新](#Handling-a-Snapshot-Update)下面。

<a name="Scheduling-a-NSUrlSession" />

## <a name="scheduling-a-nsurlsession"></a>计划 NSUrlSession

下面的代码可用于计划下载最新分数：

```csharp
private void ScheduleURLUpdateSession ()
{
    // Create new configuration
    var configuration = NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration ("com.example.urlsession");

    // Create new session
    var backgroundSession = NSUrlSession.FromConfiguration (configuration);

    // Create and start download task
    var downloadTask = backgroundSession.CreateDownloadTask (new NSUrl ("https://example.com/gamexxx/currentScores.json"));
    downloadTask.Resume ();
}
```

该配置，并创建一个新`NSUrlSession`，然后使用该会话创建新的下载任务使用`CreateDownloadTask`方法。 它将调用`Resume`下载启动会话的任务的方法。

<a name="Handling-Background-Tasks" />

## <a name="handling-background-tasks"></a>处理后台任务

通过重写`HandleBackgroundTasks`方法的`WKExtensionDelegate`，应用可以处理传入的后台任务：

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using WatchKit;

namespace MonkeySoccer.MonkeySoccerExtension
{
    public class ExtensionDelegate : WKExtensionDelegate
    {
        #region Computed Properties
        public List<WKRefreshBackgroundTask> PendingTasks { get; set; } = new List<WKRefreshBackgroundTask> ();
        #endregion

        ...
        
        #region Public Methods
        public void CompleteTask (WKRefreshBackgroundTask task)
        {
            // Mark the task completed and remove from the collection
            task.SetTaskCompleted ();
            PendingTasks.Remove (task);
        }
        #endregion 

        #region Override Methods
        public override void HandleBackgroundTasks (NSSet<WKRefreshBackgroundTask> backgroundTasks)
        {
            // Handle background request
            foreach (WKRefreshBackgroundTask task in backgroundTasks) {
                // Is this a background session task?
                var urlTask = task as WKUrlSessionRefreshBackgroundTask;
                if (urlTask != null) {
                    // Create new configuration
                    var configuration = NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration (urlTask.SessionIdentifier);

                    // Create new session
                    var backgroundSession = NSUrlSession.FromConfiguration (configuration, new BackgroundSessionDelegate (this, task), null);

                    // Keep track of all pending tasks
                    PendingTasks.Add (task);
                } else {
                    // Ensure that all tasks are completed
                    task.SetTaskCompleted ();
                }
            }
        }
        #endregion
        
        ...
    }
}
```

`HandleBackgroundTasks`方法会循环访问的所有任务系统已发送应用程序 (在`backgroundTasks`) 搜索`WKUrlSessionRefreshBackgroundTask`。 如果找到一个对象，它重新加入会话，并将附加`NSUrlSessionDownloadDelegate`来处理下载完成 (请参阅[处理下载完成](#Handling-the-Download-Completing)下面):

```csharp
// Create new session
var backgroundSession = NSUrlSession.FromConfiguration (configuration, new BackgroundSessionDelegate (this, task), null);
```

它通过将其添加到集合完成之前，新设置使任务句柄：

```csharp
public List<WKRefreshBackgroundTask> PendingTasks { get; set; } = new List<WKRefreshBackgroundTask> ();
...

// Keep track of all pending tasks
PendingTasks.Add (task);
```

所有发送到应用的任务需要完成，对于任何不当前正在处理的任务标记为完成：

```csharp
if (urlTask != null) {
    ...
} else {
    // Ensure that all tasks are completed
    task.SetTaskCompleted ();
}
```

<a name="Handling-the-Download-Completing" />

## <a name="handling-the-download-completing"></a>处理下载完成

MonkeySoccer 应用使用以下`NSUrlSessionDownloadDelegate`委托来处理下载完成并处理请求的数据：

```csharp
using System;
using Foundation;
using WatchKit;

namespace MonkeySoccer.MonkeySoccerExtension
{
    public class BackgroundSessionDelegate : NSUrlSessionDownloadDelegate
    {
        #region Computed Properties
        public ExtensionDelegate WatchExtensionDelegate { get; set; }

        public WKRefreshBackgroundTask Task { get; set; }
        #endregion

        #region Constructors
        public BackgroundSessionDelegate (ExtensionDelegate extensionDelegate, WKRefreshBackgroundTask task)
        {
            // Initialize
            this.WatchExtensionDelegate = extensionDelegate;
            this.Task = task;
        }
        #endregion

        #region Override Methods
        public override void DidFinishDownloading (NSUrlSession session, NSUrlSessionDownloadTask downloadTask, NSUrl location)
        {
            // Handle the downloaded data
            ...

            // Mark the task completed
            WatchExtensionDelegate.CompleteTask (Task);

        }
        #endregion
    }
}
```

在初始化时，它使这两句柄`ExtensionDelegate`和`WKRefreshBackgroundTask`的生成。 它将替代`DidFinishDownloading`方法以处理下载完成。 然后，使用`CompleteTask`方法的`ExtensionDelegate`通知它已完成的任务并将其删除从挂起的任务的集合。 请参阅[处理后台任务](#Handling-Background-Tasks)上面。

<a name="Scheduling-a-Snapshot-Update" />

## <a name="scheduling-a-snapshot-update"></a>计划快照更新

下面的代码可用于计划的快照任务来更新 UI 以最新分数：

```csharp
private void ScheduleSnapshotUpdate ()
{
    // Create a fire date of now
    var fireDate = NSDate.FromTimeIntervalSinceNow (0);

    // Create user info dictionary
    var userInfo = new NSMutableDictionary ();
    userInfo.Add (new NSString ("lastActiveDate"), NSDate.FromTimeIntervalSinceNow (0));
    userInfo.Add (new NSString ("reason"), new NSString ("UpdateScore"));

    // Schedule for update
    WKExtension.SharedExtension.ScheduleSnapshotRefresh (fireDate, userInfo, (error) => {
        // Was the Task successfully scheduled?
        if (error == null) {
            // Yes, handle if needed
        } else {
            // No, report error
        }
    });
}
```

就像`ScheduleURLUpdateSession`上面的方法，它创建一个新`NSDate`的应用程序时想要被唤醒并创建`NSMutableDictionary`来保存请求的任务的详细信息。 `ScheduleSnapshotRefresh`方法的`SharedExtension`用于请求在任务安排。

系统会返回`NSError`如果无法进行计划请求的任务。

<a name="Handling-a-Snapshot-Update" />

## <a name="handling-a-snapshot-update"></a>处理快照更新

若要处理快照任务`HandleBackgroundTasks`方法 (请参阅[处理后台任务](#Handling-Background-Tasks)上面) 修改为如下所示：

```csharp
public override void HandleBackgroundTasks (NSSet<WKRefreshBackgroundTask> backgroundTasks)
{
    // Handle background request
    foreach (WKRefreshBackgroundTask task in backgroundTasks) {
        // Take action based on task type
        if (task is WKUrlSessionRefreshBackgroundTask) {
            var urlTask = task as WKUrlSessionRefreshBackgroundTask;

            // Create new configuration
            var configuration = NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration (urlTask.SessionIdentifier);

            // Create new session
            var backgroundSession = NSUrlSession.FromConfiguration (configuration, new BackgroundSessionDelegate (this, task), null);

            // Keep track of all pending tasks
            PendingTasks.Add (task);
        } else if (task is WKSnapshotRefreshBackgroundTask) {
            var snapshotTask = task as WKSnapshotRefreshBackgroundTask;

            // Update UI
            ...

            // Create a expiration date 30 minutes into the future
            var expirationDate = NSDate.FromTimeIntervalSinceNow (30 * 60);

            // Create user info dictionary
            var userInfo = new NSMutableDictionary ();
            userInfo.Add (new NSString ("lastActiveDate"), NSDate.FromTimeIntervalSinceNow (0));
            userInfo.Add (new NSString ("reason"), new NSString ("UpdateScore"));

            // Mark task complete
            snapshotTask.SetTaskCompleted (false, expirationDate, userInfo);
        } else {
            // Ensure that all tasks are completed
            task.SetTaskCompleted ();
        }
    }
}
```

方法可测试的任务正在处理的类型。 如果它是`WKSnapshotRefreshBackgroundTask`它获得的访问权限的任务：

```csharp
var snapshotTask = task as WKSnapshotRefreshBackgroundTask;
```

该方法更新用户界面，然后创建`NSDate`来告诉系统时快照将过时。 它会创建`NSMutableDictionary`与用户信息来描述新的快照和标记快照任务已完成，但此信息：

```csharp
// Mark task complete
snapshotTask.SetTaskCompleted (false, expirationDate, userInfo);
```

此外，它还指示快照任务应用程序未返回到默认状态 （在第一个参数）。 没有默认状态的概念的应用程序应始终将此属性设置为`true`。

<a name="Working-Efficiently" />

## <a name="working-efficiently"></a>有效地工作

3 个后台任务更新其评分，通过有效地工作并使用新 watchOS MonkeySoccer 应用所用的 5 分钟窗口上面的示例中所示，应用程序是仅 active 总共 15 秒： 

[![](background-tasks-images/update16.png "应用程序仅处于活动状态 15 秒的总计")](background-tasks-images/update16.png#lightbox)

这会降低应用程序上可用的 Apple Watch 资源和电池使用寿命的影响，还允许应用程序能够更好地与其他 watch 上运行的应用。

<a name="How-Scheduling-Works" />

## <a name="how-scheduling-works"></a>计划的工作原理

在前台 watchOS 3 应用程序时，它是始终计划运行，并可以执行如更新的数据所需的处理任何类型或重绘其 UI。 当应用程序移动到后台时，系统通常挂起和运行时的所有操作都将都暂停。 

在后台应用程序时，它可能由系统以快速运行特定任务为目标。 因此，在 watchOS 2，系统可能会暂时唤醒的后台应用程序来执行操作，例如处理长查看通知或更新应用程序的复杂性。 在 watchOS 3，有几种新方法可在后台中运行应用。

在后台应用程序时，系统施加了几个限制：

- 它只提供几秒钟来完成任何给定的任务。 不只传递的时间量，但也多少 CPU 处理能力的应用程序使用派生此限制，系统将考虑。
- 任何超出其限制的应用程序将终止与以下的错误代码：
    - **CPU** - 0xc51bad01
    - **时间**-0xc51bad02
- 系统将根据它要求应用程序以执行后台任务类型的不同限制类限制。 例如，`WKApplicationRefreshBackgroundTask`和`WKURLSessionRefreshBackgroundTask`任务提供较长的运行时于其他类型的后台任务。

<a name="Complications-and-App-Updates" />

### <a name="complications-and-app-updates"></a>复杂情况和应用更新

除了新的后台任务，Apple 已添加到 watchOS 3，watchOS 应用的复杂性可以产生影响的应用如何以及何时接收到后台更新。

复杂性是提供一目了然的有用信息的小型可视元素。 具体取决于所选手表表盘，用户必须具有可由 watchOS 3 中的监视应用提供的一个或多个复杂手表表盘的自定义功能。

如果用户包括其 watch 表盘上的应用程序的复杂情况之一，它提供应用程序更新了下列优势：

- 它会导致系统保持在就绪到启动该应用程序状态，其中它尝试在后台中启动应用，将其保留在内存和向提供了其额外时间更新。
- 复杂性被保证至少 50 个每日推送更新。

开发人员应始终致力于使创建极具吸引力的复杂性，其应用来诱使用户将其添加到其手表表盘的上面列出的原因。

在 watchOS 2，复杂性是应用程序接收到在后台运行时的主要方法。 WatchOS 3 中的复杂应用程序将仍确保接收每小时的多个更新，但它可以使用`WKExtensions`来请求更多的运行时，若要更新其复杂性。

看看用来更新已连接的 iphone 版应用中出现的错误的以下代码：

```csharp
using System;
using WatchConnectivity;
using UIKit;
using Foundation;
using System.Collections.Generic;
using System.Linq;
...

private void UpdateComplication ()
{

    // Get session and the number of remaining transfers
    var session = WCSession.DefaultSession;
    var transfers = session.RemainingComplicationUserInfoTransfers;

    // Create user info dictionary
    var iconattrs = new Dictionary<NSString, NSObject>
        {
            {new NSString ("lastActiveDate"), NSDate.FromTimeIntervalSinceNow (0)},
            {new NSString ("reason"), new NSString ("UpdateScore")}
        };

    var userInfo = NSDictionary<NSString, NSObject>.FromObjectsAndKeys (iconattrs.Values.ToArray (), iconattrs.Keys.ToArray ());

    // Take action based on the number of transfers left
    if (transfers < 1) {
        // No transfers left, either attempt to send or inform
        // user of situation.
        ...
    } else if (transfers < 11) {
        // Running low on transfers, only send on important updates
        // else conserve for a significant change.
        ...
    } else {
        // Send data
        session.TransferCurrentComplicationUserInfo (userInfo);
    }
}
```

它使用`RemainingComplicationUserInfoTransfers`属性的`WCSession`若要查看多少高优先级传输应用程序已离开天，然后基于该数量的所需操作。 如果应用程序开始在传输上运行较低，它可以发送的次要更新拖延，并仅将信息发送的重大更改时。

<a name="Scheduling-and-Dock" />

### <a name="scheduling-and-the-dock"></a>计划和停靠

在 watchOS 3，Apple 已添加了用户可以将固定其最喜欢的应用并快速访问它们的停靠。 当用户在 Apple Watch 上按端按钮时，将显示一系列固定的应用快照。 左或向右查找所需的应用，然后点击以启动该快照替换为正在运行的应用界面的应用，用户可以往下轻扫。

[![](background-tasks-images/dock01.png "The Dock")](background-tasks-images/dock01.png#lightbox)

系统定期将快照应用程序的 UI，并使用这些快照来填充文档。watchOS 使应用能够更新其内容和 UI 之前拍摄此快照。

已固定到该插接的应用可以期待以下结果：

- 他们将收到最少一个每小时更新。 这包括应用刷新任务和快照任务。
- 所有停靠面板中的应用之间分发更新预算。 因此的较少的应用用户具有固定，每个应用程序将接收的潜在更新。
- 应用将一直保留在内存中，因此应用程序将从停靠面板中选择时快速恢复。

用户运行的最后一个应用将被视为_最近使用过_应用并将占用停靠面板中的最后一个槽。 从此处，则用户可以选择将其永久固定到停靠。 像任何其他收藏应用用户具有已固定到停靠，将被视为最近使用过。

> [!IMPORTANT]
> 该任务仅已添加到主页屏幕的应用程序将不会获得任何常规计划。 若要接收常规计划和后台更新，应用程序_必须_向停靠添加。

如本文前面所述，快照是在 watchOS 3 中非常重要，因为他们充当应用程序的预览和启动映像。 如果用户会在停靠中应用，它将展开为全屏显示、 输入前台并开始运行，所以非常有必要快照是最新。

可能有系统决定它需要的应用程序的 UI 的最新快照的时间。 在此情况下，快照请求将会影响应用程序的运行时预算。 以下将触发系统快照请求：

- 复杂性时间线更新。
- 与应用的通知的用户交互。
- 从前景切换到后台状态。
- 后一小时的后台状态，因此应用程序可以返回到默认状态。
- 当 watchOS 首次启动。

<a name="Best-Practices" />

## <a name="best-practices"></a>最佳做法 

Apple 建议在使用后台任务时遵照以下最佳实践：

- 通常应用程序需要更新计划。 每次运行应用程序时它应重新评估其未来的需求并调整此计划所需。
- 如果系统发送后台刷新任务和应用程序不需要更新，延迟工作，直到实际需要进行更新时。
- 请考虑到应用程序提供的所有运行时机会：
    - 停靠和前景色的激活。
    - 通知。
    - 复杂更新。
    - 后台刷新。
- 使用`ScheduleBackgroundRefresh`如常规用途的后台运行时：
    - 轮询系统中的信息。
    - 计划未来`NSURLSessions`请求后台数据。 
    - 未知的时间进行的转换。
    - 触发复杂更新。

<a name="Snapshot-Best-Practices" />

## <a name="snapshot-best-practices"></a>快照的最佳做法

在使用快照更新数，Apple 将使以下建议：

- 使快照仅在需要，例如，重要的内容更改时无效。
- 避免高频率快照失效。 例如，计时器应用程序不应更新快照每隔一秒，它仅当应执行计时器已结束。

<a name="App-Data-Flow" />

## <a name="app-data-flow"></a>应用程序的数据流

Apple 建议使用数据流的以下：

[![](background-tasks-images/update17.png "应用程序数据流关系图")](background-tasks-images/update17.png#lightbox)

外部事件 （例如监视连接） 唤醒，应用程序。 这会强制应用以更新它的数据模型 （表示应用程序当前状态）。 作为结果的数据模型更改应用程序将需要更新其采用十分复杂，请求新的快照，可能是启动后台`NSURLSession`来拉取更多的数据和计划进一步后台刷新。

<a name="The-App-Lifecycle" />

## <a name="the-app-lifecycle"></a>应用生命周期

由于停靠和固定到它，将比以往更多应用程序之间移动用户的 Apple 认为最喜爱的应用的功能很多情况下，然后像 watchos 2。 因此，应用程序应准备好处理此更改和前景色和背景状态之间快速移动。

Apple 具有以下建议：

- 请确保应用完成尽可能快地进入前台激活任何后台任务。
- 请确保完成所有前台工作通过调用进入后台之前`NSProcessInfo.PerformExpiringActivity`。
- 当 watchOS 模拟器中测试的应用程序，没有任何任务预算将可强制使应用可以刷新根据最大可正确测试一项功能。
- 始终以确保到 iTunes Connect 应用程序未运行之前发布其预算过去的真实 Apple Watch 硬件上进行测试。
- Apple 建议在测试和调试的同时保持充电器上的 Apple Watch。
- 请确保全面测试，冷启动和恢复应用程序。
- 验证都已完成应用的所有任务。
- 在要测试的最佳和最差的停靠中固定的应用数量例方案。

<a name="Summary" />

## <a name="summary"></a>总结

本文只讨论了 Apple 已 watchOS 以及如何使用它们来使 watch 应用保持最新的增强功能。 首先，介绍的所有新后台任务 Apple 已新增了 watchOS 3。 然后，它介绍了背景 API 生命周期和如何在 Xamarin watchOS 应用中实现后台任务。 最后，它介绍如何计划的工作原理，并提供一些最佳做法。



## <a name="related-links"></a>相关链接

- [iOS 10 示例](https://developer.xamarin.com/samples/ios/iOS10/)
