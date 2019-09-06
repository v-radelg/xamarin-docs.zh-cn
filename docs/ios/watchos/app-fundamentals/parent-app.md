---
title: 使用 watchOS 在 Xamarin 中的父应用程序
description: 本文档介绍如何在 Xamarin 中的 watchOS 父应用程序使用。 它讨论了 WatchKit 应用扩展、 iOS 应用、 共享的存储，和的详细信息。
ms.prod: xamarin
ms.assetid: 9AD29833-E9CC-41A3-95D2-8A655FF0B511
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: 0049d69caabce545b2813dbd2b3905fe96f28fed
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292727"
---
# <a name="working-with-the-watchos-parent-application-in-xamarin"></a>使用 watchOS 在 Xamarin 中的父应用程序

> [!IMPORTANT]
> 访问父应用程序使用下面的示例仅适用于 watchOS 1 watch 应用。


有不同的方式，监视应用和与捆绑的 iOS 应用之间进行通信：

- 监视扩展可以针对在 iPhone 上后台运行的父应用[调用方法](#code)。

- 监视扩展可以[共享的存储位置](#storage)父 iPhone 应用的。

- 使用移交将数据从速览或通知传递到 Watch 应用，并将用户发送到应用中的特定界面控制器。

父应用程序有时也称为容器应用。


<a name="code" />

## <a name="run-code"></a>运行代码

监视扩展和父 iPhone 应用之间进行通信进行了演示[GpsWatch 示例](https://docs.microsoft.com/samples/xamarin/ios-samples/watchkit-gpswatch)。
监视扩展可以请求父 iOS 应用程序执行一些处理其代表使用`OpenParentApplication`方法。

对于长时间运行的任务 （包括网络请求）-仅父 iOS 应用程序可以充分利用后台处理，以完成这些任务并将检索到的数据保存在可以访问的监视扩展的位置，这是特别有用。



### <a name="watch-kit-app-extension"></a>观看工具包应用扩展

调用`WKInterfaceController.OpenParentApplication`你监视应用程序扩展中。 它将返回`bool`，该值指示是否已成功或不发送方法请求。 检查`error`在响应中的参数`Action`可确定是否有任何过程中发生在 iPhone 应用中运行的方法。

```csharp
WKInterfaceController.OpenParentApplication (new NSDictionary (), (replyInfo, error) => {
    if(error != null) {
        Console.WriteLine (error);
        return;
    }
    Console.WriteLine ("parent app responded");
    // do something with replyInfo[] dictionary
});
```


### <a name="ios-app"></a>iOS 应用

从监视应用程序扩展的所有呼叫都会都路由通过 iPhone 应用`HandleWatchKitExtensionRequest`方法。
如果您正在监视应用程序中进行不同的请求，则此方法需要查询`userInfo`字典来确定如何处理该请求。


```csharp
[Register ("AppDelegate")]
public partial class AppDelegate : UIApplicationDelegate
{
    // ... other AppDelegate methods
    public override void HandleWatchKitExtensionRequest
        (UIApplication application, NSDictionary userInfo, Action<NSDictionary> reply)
    {
        var status = 2;
        // do something in the background, and respond
        reply (new NSDictionary (
            "count", NSNumber.FromInt32 ((int)status),
            "value2", new NSString("some-info")
            ));
    }
}
```


<a name="storage" />

## <a name="shared-storage"></a>共享的存储

如果你配置[应用程序组](~/ios/watchos/app-fundamentals/app-groups.md)然后 iOS 8 扩展 （包括监视扩展） 可以与父应用共享数据。

<a name="nsuserdefaults" />

### <a name="nsuserdefaults"></a>使用 NSUserDefaults

可以监视应用程序扩展和父 iPhone 应用中编写以下代码，以便它们可以引用一组通用的`NSUserDefaults`:

```csharp
NSUserDefaults shared = new NSUserDefaults(
        "group.com.your-company.watchstuff",
        NSUserDefaultsType.SuiteName);

// set values
shared.SetInt (2, "count");
shared.Synchronize ();

// get values
shared.Synchronize ();
var count = shared.IntForKey ("count");
```

<a name="files" />

### <a name="files"></a>文件

IOS 应用和监视扩展还可以共享使用的公共文件路径的文件。

```csharp
var FileManager = new NSFileManager ();
var appGroupContainer =
            FileManager.GetContainerUrl ("group.com.your-company.watchstuff");
var appGroupContainerPath = appGroupContainer.Path;
Console.WriteLine ("agcpath: " + appGroupContainerPath);
// use the path to create and update files
```

注意： 如果该路径是`null`然后检查[应用程序组配置](~/ios/watchos/app-fundamentals/app-groups.md)以确保已正确配置和已下载/安装在开发计算机上的预配配置文件。

有关详细信息，请参阅[应用程序组功能](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md)文档。

## <a name="wormholesharp"></a>WormHoleSharp

在 watchOS 1 中的受欢迎的开源机制 (基于[MMWormHole](https://github.com/mutualmobile/MMWormhole)) 父应用和监视应用程序之间传递数据或命令。

可以配置使用 iOS 应用程序中的此类应用程序组旋涡式星体并观看扩展：

```csharp
// AppDelegate (iOS) or InterfaceController (watch extension)
Wormhole wormHole;
// ...
wormHole = new Wormhole ("group.com.your-company.watchstuff", "messageDir");
```

下载C#版本[WormHoleSharp](https://github.com/Clancey/WormHoleSharp)。



## <a name="related-links"></a>相关链接

- [GpsWatch （示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [WormHoleSharp （示例）](https://github.com/Clancey/WormHoleSharp)
- [Apple 的 WKInterfaceController 引用](https://developer.apple.com/library/prerelease/ios/documentation/WatchKit/Reference/WKInterfaceController_class/index.html#//apple_ref/occ/clm/WKInterfaceController/openParentApplication:reply:)
- [Apple 的包含应用程序与共享数据](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/ExtensionScenarios.html)
