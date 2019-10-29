---
title: 在 Xamarin 中使用 watchOS 父应用程序
description: 本文档介绍如何在 Xamarin 中使用 watchOS 父应用程序。 它讨论了 WatchKit 应用扩展、iOS 应用、共享存储等。
ms.prod: xamarin
ms.assetid: 9AD29833-E9CC-41A3-95D2-8A655FF0B511
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 08637fe13b28565e7c6ab1c89b291c6db3b81025
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73001480"
---
# <a name="working-with-the-watchos-parent-application-in-xamarin"></a>在 Xamarin 中使用 watchOS 父应用程序

> [!IMPORTANT]
> 使用以下示例访问父应用程序仅适用于 watchOS 1 监视应用。

可以通过不同的方式在监视应用和它所捆绑到的 iOS 应用之间进行通信：

- 监视扩展可以针对在 iPhone 上后台运行的父应用[调用方法](#code)。

- 监视扩展可以与父 iPhone 应用[共享存储位置](#storage)。

- 使用移交将数据从速览或通知传递到 Watch 应用，并将用户发送到应用中的特定界面控制器。

父应用程序有时也称为容器应用。

<a name="code" />

## <a name="run-code"></a>运行代码

[GpsWatch 示例](https://docs.microsoft.com/samples/xamarin/ios-samples/watchkit-gpswatch)演示了监视扩展和父 iPhone 应用之间的通信。
你的监视扩展可以请求父 iOS 应用使用 `OpenParentApplication` 方法代表其执行某种处理。

这对于长时间运行的任务（包括网络请求）尤其有用-只有父 iOS 应用才能利用后台处理来完成这些任务，并将检索到的数据保存到可访问监视扩展的位置。

### <a name="watch-kit-app-extension"></a>观看工具包应用扩展

在手表应用扩展中调用 `WKInterfaceController.OpenParentApplication`。 它将返回一个指示是否已成功发送方法请求的 `bool`。 检查响应 `Action` 中的 `error` 参数，确定在 iPhone 应用中运行的方法期间是否发生了任何错误。

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

来自监视应用扩展的所有调用都通过 iPhone 应用的 `HandleWatchKitExtensionRequest` 方法进行路由。
如果在手表应用中发出不同的请求，则此方法将需要查询 `userInfo` 字典，以确定如何处理请求。

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

## <a name="shared-storage"></a>共享存储

如果配置[应用组](~/ios/watchos/app-fundamentals/app-groups.md)，则 iOS 8 扩展（包括监视扩展）可以与父应用共享数据。

<a name="nsuserdefaults" />

### <a name="nsuserdefaults"></a>使用 nsuserdefaults

可以在 "监视应用扩展" 和 "父 iPhone" 应用中编写以下代码，以便它们可以引用一组常见的 `NSUserDefaults`：

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

IOS 应用和监视扩展还可以使用公共文件路径共享文件。

```csharp
var FileManager = new NSFileManager ();
var appGroupContainer =
            FileManager.GetContainerUrl ("group.com.your-company.watchstuff");
var appGroupContainerPath = appGroupContainer.Path;
Console.WriteLine ("agcpath: " + appGroupContainerPath);
// use the path to create and update files
```

注意：如果路径为 `null`，请检查[应用组配置](~/ios/watchos/app-fundamentals/app-groups.md)，以确保正确配置了预配配置文件并将其下载/安装到了开发计算机上。

有关详细信息，请参阅[应用组功能](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md)文档。

## <a name="wormholesharp"></a>WormHoleSharp

一种常用的开源机制，适用于 watchOS 1 （基于[MMWormHole](https://github.com/mutualmobile/MMWormhole)）在父应用程序和监视应用程序之间传递数据或命令。

可以在 iOS 应用和监视扩展中使用类似于下面的应用组配置旋涡式星体：

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
- [Apple 的 WKInterfaceController 参考](https://developer.apple.com/library/prerelease/ios/documentation/WatchKit/Reference/WKInterfaceController_class/index.html#//apple_ref/occ/clm/WKInterfaceController/openParentApplication:reply:)
- [Apple 与你的包含应用共享数据](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/ExtensionScenarios.html)
