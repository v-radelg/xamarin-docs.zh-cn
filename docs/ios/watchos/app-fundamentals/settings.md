---
title: 使用 watchOS 中 Xamarin 的设置
description: 本文档介绍如何使用 Xamarin 中的 watchOS 设置。 它讨论添加设置到监视应用程序解决方案，在 iPhone 上使用应用程序中，并且 Apple Watch 应用这些设置。
ms.prod: xamarin
ms.assetid: 4B2EB192-F0A2-4010-B141-0431520594C0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: a8fe2c2765676db52c23fd7c475f218f14697caf
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67675233"
---
# <a name="working-with-watchos-settings-in-xamarin"></a>使用 watchOS 中 Xamarin 的设置

Apple Watch 应用可以使用相同的设置功能作为 iOS 应用-设置用户界面中显示**Apple Watch** iPhone 应用，但这些值都可以访问在 iPhone 应用和还监视扩展。

![](settings-images/intro.png "Apple Watch 应用可以使用相同的设置功能作为 iOS 应用")

设置将存储在 iOS 应用程序和监视应用程序扩展，通过定义可访问的共享的文件位置**应用程序组**。 您应该[配置应用组](~/ios/watchos/app-fundamentals/app-groups.md)之前添加设置，请使用下面的说明。

## <a name="add-settings-in-a-watch-solution"></a>监视解决方案中添加设置

在中**iPhone 应用**在你的解决方案 (*不*watch 应用或扩展):

1. 右键单击**添加 > 新建文件...** ，然后选择**Settings.bundle** (不能编辑中的名称**新文件**对话框):

   [![](settings-images/settings-add-sml.png "添加新的设置捆绑包")](settings-images/settings-add.png#lightbox)

2. 将名称更改为**设置 Watch.bundle** (选择，然后键入**Command + R**重命名):

   ![](settings-images/settings-rename.png "重命名此捆绑包")

3. 添加新的密钥`ApplicationGroupContainerIdentifier`到**Root.plist**值设置为配置后，（例如应用组。 `group.com.xamarin.WatchSettings` 在此示例中）：

   [![](settings-images/settings-appgroup-sml.png "将 ApplicationGroupContainerIdentifier 密钥添加到 Root.plist")](settings-images/settings-appgroup.png#lightbox)

4. 编辑**Settings-Watch.bundle/Root.plist**以包含你想要使用的选项的模板文件包含一组。
  文本字段、 切换开关和滑块的默认值 （它可以删除并替换为你自己的设置）：

  [![](settings-images/rootplist-sml.png "编辑 Settings-Watch.bundle/Root.plist")](settings-images/rootplist.png#lightbox)


## <a name="use-settings-in-the-watch-app"></a>在 Watch 应用中使用设置

若要访问由用户选择的值，创建`NSUserDefaults`实例使用的应用程序组，并指定`NSUserDefaultsType.SuiteName`:

```csharp
NSUserDefaults shared = new NSUserDefaults(
    "group.com.xamarin.WatchSettings",
    NSUserDefaultsType.SuiteName);

var isEnabled = shared.BoolForKey ("enabled_preference");
var userName = shared.StringForKey ("name_preference");
```

## <a name="apple-watch-app"></a>Apple Watch 应用

[![](settings-images/settings-app-sml.png "在 iPhone 上新的 Apple Watch 应用")](settings-images/settings-app.png#lightbox)

用户将与通过新的设置进行交互**Apple Watch**其 iPhone 上的应用。 此应用程序允许用户显示/隐藏上监视，以及编辑设置公开使用的应用程序**设置 Watch.bundle**。

![](settings-images/applewatch-1.png "应用设置的示例") ![](settings-images/applewatch-2.png "示例应用程序设置")



## <a name="related-links"></a>相关链接

- [Apple 的设置文档](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Settings.html#//apple_ref/doc/uid/TP40014969-CH22-SW1)
