---
title: Xamarin 中的后台传输和 NSURLSession
description: 本文档提供的演练演示如何使用后台传输和 NSUrlSession 来开始下载大图像，并在应用置于后台时继续下载。
ms.prod: xamarin
ms.assetid: 6960E025-3D5C-457A-B893-25B734F8626D
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/18/2017
ms.openlocfilehash: 47a07959bcfabc0980ccb90f2ae7a489e5e71223
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70756237"
---
# <a name="background-transfer-and-nsurlsession-in-xamarinios"></a>Xamarin 中的后台传输和 NSURLSession

通过配置后台`NSURLSession`和排队上传或下载任务来启动后台传输。 如果任务在应用程序被 backgrounded、挂起或终止时完成，则 iOS 会通过调用应用程序的*AppDelegate*中的完成处理程序来通知应用程序。 下图演示了此操作：

 [![](background-transfer-walkthrough-images/transfer.png "通过配置后台 NSURLSession 和排队上传或下载任务来启动后台传输")](background-transfer-walkthrough-images/transfer.png#lightbox)

让我们看看代码中的外观。

## <a name="configuring-a-background-session"></a>配置后台会话

若要创建后台会话，请`NSUrlSession` `NSUrlSessionConfiguration`使用对象创建一个新的并对其进行配置。

配置对象确定会话可以执行的操作以及可运行的任务类型。
使用方法配置的`CreateBackgroundSessionConfiguration`会话将在单独的进程中运行，并执行随机（WiFi）传输以保留数据和电池寿命。
下面的代码示例演示了如何使用`CreateBackgroundSessionConfiguration`方法和唯一的字符串标识符正确设置后台传输会话：

```csharp
public partial class SimpleBackgroundTransferViewController : UIViewController
{
  NSUrlSession session = null;

  NSUrlSessionConfiguration configuration =
      NSUrlSessionConfiguration.CreateBackgroundSessionConfiguration ("com.SimpleBackgroundTransfer.BackgroundSession");
  session = NSUrlSession.FromConfiguration
      (configuration, (NSUrlSessionDelegate) new MySessionDelegate(), new NSOperationQueue());

}
```

除了配置对象之外，会话还需要会话委托和队列。
队列确定任务完成的顺序。 会话委托 chaperones 传输过程，并处理身份验证、缓存和其他与会话相关的问题。

## <a name="working-with-tasks-and-delegates"></a>使用任务和委托

现在我们已配置了后台会话，接下来让我们开始处理此传输的任务。 我们可以使用`NSUrlSessionDelegate`称为会话委托的实例来跟踪这些任务。 会话委托负责在后台唤醒终止或挂起的应用程序，以处理身份验证、错误或传输完成。

`NSUrlSessionDelegate`提供以下用于检查传输状态的基本方法：

- *DidFinishEventsForBackgroundSession* -当所有任务都已完成并且传输完成时，将调用此方法。
- *DidReceiveChallenge* -在需要授权时调用以请求凭据。
- *DidBecomeInvalidWithError* -如果变为无效`NSURLSession` ，则调用。

后台会话需要更多的专用委托，具体取决于正在运行的任务的类型。 后台会话仅限两种类型的任务：

- *上传任务*-类型`NSUrlSessionUploadTask`的任务使用`NSUrlSessionTaskDelegate`从`NSUrlSessionDelegate`继承的。 此委托提供其他方法来跟踪上传进度、处理 HTTP 重定向等。
- *下载任务*-类型`NSUrlSessionDownloadTask`的任务使用`NSUrlSessionDownloadDelegate`从`NSUrlSessionTaskDelegate`继承的。 此委托提供了用于上传任务的所有方法，以及用于跟踪下载进度并确定下载任务恢复或完成时间的下载特定方法。

下面的代码定义可用于从 URL 下载图像的任务。 我们通过在后台会话上调用`CreateDownloadTask`并传入 URL 请求来开始任务：

```csharp
const string DownloadURLString = "http://cdn1.xamarin.com/webimages/images/xamarin.png";
public NSUrlSessionDownloadTask downloadTask;

NSUrl downloadURL = NSUrl.FromString (DownloadURLString);
NSUrlRequest request = NSUrlRequest.FromUrl (downloadURL);
downloadTask = session.CreateDownloadTask (request);
```

接下来，我们将创建一个新的会话下载委托，以跟踪此会话中的所有下载任务：

```csharp
public class MySessionDelegate : NSUrlSessionDownloadDelegate
{
  public override void DidWriteData (NSUrlSession session, NSUrlSessionDownloadTask downloadTask, long bytesWritten, long totalBytesWritten, long totalBytesExpectedToWrite)
  {
    Console.WriteLine (string.Format ("DownloadTask: {0}  progress: {1}", downloadTask, progress));
    InvokeOnMainThread( () => {
      // update UI with progress bar, if desired
    });
  }
  ...
}
```

如果我们想要了解下载任务的进度，可以重写`DidWriteData`方法以跟踪进度，甚至更新 UI。 如果应用程序处于前台，则 UI 更新将立即显示，或在用户下次打开应用程序时等待用户。

会话委托 API 提供了用于与任务交互的广泛工具包。 有关会话委托方法的完整列表，请参阅`NSUrlSessionDelegate` API 文档。

> [!IMPORTANT]
> 后台会话是在后台线程上启动的，因此，对更新 ui 的任何调用都必须通过调用`InvokeOnMainThread`来在 ui 线程上显式运行，以避免 iOS 终止应用。 

## <a name="handling-transfer-completion"></a>处理传输完成

最后一步是让应用程序知道与会话相关的所有任务都已完成，并处理新内容。

在*AppDelegate*中，订阅`HandleEventsForBackgroundUrl`事件。 当应用程序进入后台并且传输会话正在运行时，将调用此方法，系统会向我们传递一个完成处理程序：

```csharp
public System.Action backgroundSessionCompletionHandler;

public override void HandleEventsForBackgroundUrl (UIApplication application, string sessionIdentifier, System.Action completionHandler)
{
  this.backgroundSessionCompletionHandler = completionHandler;
}
```

我们将使用完成处理程序，让 iOS 知道应用程序处理完成的时间。

请记住，会话可能会生成多个任务来处理传输。 当最后一个任务完成时，暂停或终止的应用程序将重新启动到后台。 然后，应用程序`NSURLSession`使用唯一会话标识符重新连接到，并调用`DidFinishEventsForBackgroundSession`会话委托。 此方法是应用程序处理新内容的机会，包括更新 UI，以反映传输的结果：

```csharp
public override void DidFinishEventsForBackgroundSession (NSUrlSession session) {
  // Handle new information, update UI, etc.
}
```

完成对新内容的处理后，我们将调用完成处理程序，让系统知道可以安全地获取应用程序的快照并返回到睡眠状态：

```csharp
public override void DidFinishEventsForBackgroundSession (NSUrlSession session) {
  var appDelegate = UIApplication.SharedApplication.Delegate as AppDelegate;

  // Handle new information, update UI, etc.

  // call completion handler when you're done
  if (appDelegate.backgroundSessionCompletionHandler != null) {
    NSAction handler = appDelegate.backgroundSessionCompletionHandler;
    appDelegate.backgroundSessionCompletionHandler = null;
    handler.Invoke ();
  }
}
```

在本演练中，我们介绍了在 iOS 7 中实现后台传输服务的基本步骤。

## <a name="related-links"></a>相关链接

- [简单后台传输（示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/simplebackgroundtransfer)
