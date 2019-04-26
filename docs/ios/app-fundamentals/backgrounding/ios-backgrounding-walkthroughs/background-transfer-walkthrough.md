---
title: 后台传输并在 Xamarin.iOS NSURLSession
description: 本文档提供的演练，演示如何使用后台传输和 NSUrlSession 启动下载的大图像，并当应用程序放置在后台继续下载。
ms.prod: xamarin
ms.assetid: 6960E025-3D5C-457A-B893-25B734F8626D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 4e525388290d92901e68e61f1ffa81866f5aac4d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61392203"
---
# <a name="background-transfer-and-nsurlsession-in-xamarinios"></a>后台传输并在 Xamarin.iOS NSURLSession

通过配置背景启动后台传输`NSURLSession`和上传或下载任务排入队列。 如果任务完成后台运行、 挂起或终止该应用程序时，iOS 将通过在应用程序的调用完成处理程序通知应用程序*AppDelegate*。 下图中操作演示此操作：

 [![](background-transfer-walkthrough-images/transfer.png "通过配置背景 NSURLSession 启动后台传输和排入队列上传或下载任务")](background-transfer-walkthrough-images/transfer.png#lightbox)

让我们查看一下这种情况在代码中。

## <a name="configuring-a-background-session"></a>配置后台会话

若要使后台会话，请创建一个新`NSUrlSession`并将其使用配置`NSUrlSessionConfiguration`对象。

配置对象决定会话可以执行的操作，并可以运行的任务类型。
使用配置的会话`CreateBackgroundSessionConfiguration`方法将在单独的进程中运行，并执行自定义 (WiFi) 传输来保留数据和电池寿命。
下面的代码示例演示如何正确设置后台传输会话使用`CreateBackgroundSessionConfiguration`方法和唯一字符串标识符：

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

除了配置对象，会话还要求会话委托和队列。
队列确定将在其中完成的任务的顺序。 会话委托 chaperones 传输过程中，并处理身份验证、 缓存和其他与会话相关的问题。

## <a name="working-with-tasks-and-delegates"></a>处理任务和委托

现在，我们已配置后台会话，让我们启动任务来处理传输。 我们可以跟踪的这些任务使用`NSUrlSessionDelegate`实例称为会话委托。 会话委托负责唤醒的终止或挂起应用程序在后台对句柄身份验证、 错误或传输完成。

`NSUrlSessionDelegate`提供以下基本的方法来检查传输状态：

-  *DidFinishEventsForBackgroundSession* -所有任务都完成，并已完成传输时调用此方法。
-  *DidReceiveChallenge* -请求凭据需要授权时调用。
-  *DidBecomeInvalidWithError* -时调用`NSURLSession`变得无效。


后台会话需要更专用的委托，具体取决于正在运行的任务的类型。 后台会话仅限于两种类型的任务：

-  *上传任务*的类型的任务`NSUrlSessionUploadTask`使用`NSUrlSessionTaskDelegate`，后者又继承`NSUrlSessionDelegate`。 此委托提供了其他方法来跟踪上载进度、 句柄的 HTTP 重定向，和的详细信息。
-  *下载任务*的类型的任务`NSUrlSessionDownloadTask`使用`NSUrlSessionDownloadDelegate`，后者又继承`NSUrlSessionTaskDelegate`。 此委托提供的所有方法上都传任务，以及特定于下载的方法来跟踪下载进度和确定何时下载任务已恢复或已完成。


下面的代码定义一个可用于从 URL 下载映像的任务。 我们通过调用启动任务`CreateDownloadTask`上我们后台会话，并传入请求的 URL:

```csharp
const string DownloadURLString = "http://cdn1.xamarin.com/webimages/images/xamarin.png";
public NSUrlSessionDownloadTask downloadTask;

NSUrl downloadURL = NSUrl.FromString (DownloadURLString);
NSUrlRequest request = NSUrlRequest.FromUrl (downloadURL);
downloadTask = session.CreateDownloadTask (request);
```

接下来，我们将创建一个新的会话下载委托，以在此会话中跟踪的所有下载任务：

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

如果我们想要找出某个下载任务的进度，可以重写`DidWriteData`方法来跟踪进度，甚至是更新 UI。 如果应用程序位于前台，或将正在等待用户在打开应用程序的下一步时，UI 更新将会立即显示。

会话委托 API 用来与任务交互提供广泛的工具包。 有关会话的完整列表委托方法，请参阅`NSUrlSessionDelegate`API 文档。

> [!IMPORTANT]
> 因此若要更新 UI 的任何调用必须显式运行在 UI 线程上通过调用在后台线程上开始后台会话`InvokeOnMainThread`以免 iOS 终止应用程序。 


## <a name="handling-transfer-completion"></a>处理传输完成

最后一步是让应用程序了解与会话相关联的所有任务都完成时，并处理新内容。

在中*AppDelegate*，订阅`HandleEventsForBackgroundUrl`事件。 当应用程序进入后台，传输会话正在运行时，调用此方法并且系统通过我们完成处理程序：

```csharp
public System.Action backgroundSessionCompletionHandler;

public override void HandleEventsForBackgroundUrl (UIApplication application, string sessionIdentifier, System.Action completionHandler)
{
  this.backgroundSessionCompletionHandler = completionHandler;
}
```

我们将使用完成处理程序来让 iOS 知道我们的应用程序完成时处理。

前面曾提到一个会话可以生成多个任务以处理传输。 最后一个任务完成后，挂起或终止应用程序是在后台重新启动。 然后，应用程序重新连接到`NSURLSession`使用的唯一会话标识符和调用`DidFinishEventsForBackgroundSession`会话委托上。 此方法是应用程序的机会来处理新的内容，包括更新 UI 以反映传输的结果：

```csharp
public override void DidFinishEventsForBackgroundSession (NSUrlSession session) {
  // Handle new information, update UI, etc.
}
```

一旦我们完成了处理新内容，我们将调用要告知系统安全地创建应用程序的快照，并返回休眠状态的完成处理程序：

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

在此演练中，我们介绍如何在 iOS 7 中实现后台传输服务的基本步骤。



## <a name="related-links"></a>相关链接

- [简单后台传输 （示例）](https://developer.xamarin.com/samples/monotouch/SimpleBackgroundTransfer/)
