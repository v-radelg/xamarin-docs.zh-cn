---
title: 使用 watchOS 在 Xamarin 中的父应用程序
description: 本文档介绍如何在 Xamarin 中的 watchOS 父应用程序使用。 它讨论了 watchOS 应用扩展、iOS 应用、共享存储等。
ms.prod: xamarin
ms.assetid: 9AD29833-E9CC-41A3-95D2-8A655FF0B511
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 3e11b163d16be9711bf09102e3ab8604d98299d7
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75487758"
---
# <a name="working-with-the-watchos-parent-application-in-xamarin"></a>使用 watchOS 在 Xamarin 中的父应用程序

有不同的方式，监视应用和与捆绑的 iOS 应用之间进行通信：

- Watch 应用可以在 iPhone 上的父应用上[运行代码](#run-code)。

- 监视扩展可以[共享的存储位置](#shared-storage)父 iPhone 应用的。

- 使用移交将数据从通知传递到监视应用，并将用户发送到应用中的特定界面控制器。

父应用程序有时也称为容器应用。

## <a name="run-code"></a>运行代码

这两个示例演示如何使用 `WCSession` 在手表应用和配对的 iPhone 之间运行代码和发送消息：

- [观看连接](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchconnectivity/)
- [SimpleWatchConnectivity](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-simplewatchconnectivity/) 

## <a name="shared-storage"></a>共享的存储

如果你配置[应用程序组](~/ios/watchos/app-fundamentals/app-groups.md)然后 iOS 8 扩展 （包括监视扩展） 可以与父应用共享数据。

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

### <a name="files"></a>Files

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

## <a name="related-links"></a>相关链接

- [Apple 的 WKInterfaceController 引用](https://developer.apple.com/library/prerelease/ios/documentation/WatchKit/Reference/WKInterfaceController_class/index.html#//apple_ref/occ/clm/WKInterfaceController/openParentApplication:reply:)
- [Apple 的包含应用程序与共享数据](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/ExtensionScenarios.html)
