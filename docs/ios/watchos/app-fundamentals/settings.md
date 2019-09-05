---
title: 在 Xamarin 中使用 watchOS 设置
description: 本文档介绍如何在 Xamarin 中使用 watchOS 设置。 本文讨论如何向监视应用解决方案添加设置、如何在应用中使用这些设置以及 iPhone 上的 Apple Watch 应用。
ms.prod: xamarin
ms.assetid: 4B2EB192-F0A2-4010-B141-0431520594C0
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: bcb719451529cd5a9ca829b8693c425d752cc93b
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70283226"
---
# <a name="working-with-watchos-settings-in-xamarin"></a>在 Xamarin 中使用 watchOS 设置

Apple Watch 应用可以使用与 iOS 应用相同的设置功能-"设置" 用户界面显示在**Apple Watch** iphone 应用中，但这些值可在 iphone 应用中访问，也可在监视扩展中访问。

![](settings-images/intro.png "Apple Watch 应用可以使用与 iOS 应用相同的设置功能")

这些设置将存储在一个共享文件位置，该位置可由**应用程序组**定义的 iOS 应用程序和监视应用程序扩展访问。 在添加设置之前，应使用以下说明来[配置应用组](~/ios/watchos/app-fundamentals/app-groups.md)。

## <a name="add-settings-in-a-watch-solution"></a>在手表解决方案中添加设置

在解决方案的**iPhone 应用**中（*不*是 "监视" 应用或扩展）：

1. 右键单击 "**添加 > 新文件 ...** "，然后选择 "设置" "**捆绑**" （无法在 "**新建文件**" 对话框中编辑名称）：

   [![](settings-images/settings-add-sml.png "添加新的设置捆绑包")](settings-images/settings-add.png#lightbox)

2. 将名称更改为 "**设置"-"监视**" （选择并键入**Command + R**进行重命名）：

   ![](settings-images/settings-rename.png "重命名绑定")

3. 将新项`ApplicationGroupContainerIdentifier`添加到**info.plist** ，并将值设置为配置的应用组（例如， `group.com.xamarin.WatchSettings`在示例中）：

   [![](settings-images/settings-appgroup-sml.png "将 ApplicationGroupContainerIdentifier 键添加到 info.plist")](settings-images/settings-appgroup.png#lightbox)

4. 编辑**Settings-Watch/info.plist**以包含想要使用的选项-模板文件包含一个组。
  默认设置为 "文本"、"切换" 和 "滑块" （您可以删除并替换为您自己的设置）：

  [![](settings-images/rootplist-sml.png "编辑 Settings-Watch/info.plist")](settings-images/rootplist.png#lightbox)


## <a name="use-settings-in-the-watch-app"></a>在监视应用中使用设置

若要访问用户选择的值，请使用应用`NSUserDefaults`组创建实例，并指定： `NSUserDefaultsType.SuiteName`

```csharp
NSUserDefaults shared = new NSUserDefaults(
    "group.com.xamarin.WatchSettings",
    NSUserDefaultsType.SuiteName);

var isEnabled = shared.BoolForKey ("enabled_preference");
var userName = shared.StringForKey ("name_preference");
```

## <a name="apple-watch-app"></a>Apple Watch 应用

[![](settings-images/settings-app-sml.png "IPhone 上的新 Apple Watch 应用")](settings-images/settings-app.png#lightbox)

用户将通过其 iPhone 上的新**Apple Watch**应用与设置进行交互。 此应用允许用户显示/隐藏监视上的应用，还可编辑使用 "**设置-watch**" 公开的设置。

![](settings-images/applewatch-1.png "应用设置的示例") ![](settings-images/applewatch-2.png "示例应用程序设置")



## <a name="related-links"></a>相关链接

- [Apple 的设置文档](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Settings.html#//apple_ref/doc/uid/TP40014969-CH22-SW1)
