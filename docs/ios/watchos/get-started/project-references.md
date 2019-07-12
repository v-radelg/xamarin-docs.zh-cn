---
title: watchOS 中 Xamarin 的项目引用
description: 本文档介绍 iOS 应用程序中，watch 应用和监视应用程序扩展之间的关系。 还讨论了项目的引用和捆绑包标识符。
ms.prod: xamarin
ms.assetid: C366E062-C33D-406A-B3FF-CBE82E5D1E7E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/13/2016
ms.openlocfilehash: 60eaad98e2d5469e9c43e6b4ad889080e1aa63ba
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832063"
---
# <a name="watchos-project-references-in-xamarin"></a>watchOS 中 Xamarin 的项目引用

_IOS 应用程序中，watch 应用和监视扩展之间的关系的说明。_

WatchOS 解决方案中的三个项目是*自动配置*来互相引用 watchOS 3 应用程序生成和正确捆绑在一起以特定方式。 这些项目的引用和捆绑包标识符设置如下所述的引用。

## <a name="project-references"></a>项目引用

通过双击每个项目的引用节点来查看引用：

- **iPhone 应用**引用**Watch 应用**

  ![](project-references-images/catalog-reference1.png "iPhone 应用引用 Watch 应用")

- **监视应用**引用**监视应用程序扩展**

  ![](project-references-images/catalog-reference2.png "iPhone 应用引用 Watch 应用")


- **监视应用程序扩展**不引用其他项目之一

  ![](project-references-images/catalog-reference3.png "监视应用程序扩展不能引用其他项目")



## <a name="bundle-identifiers"></a>捆绑包标识符

此外需要确保你**捆绑包标识符**正确无误。
所有三个项目应有*同一*标识符前缀，使用预先定义的扩展的两个监视项目`watchkitextension`和`watchkitapp`，按如下所示 (有关**WatchKitCatalog**示例）：

- Xamarin.iOS 统一项目- `com.xamarin.WatchKitCatalog`

- WatchKit 扩展项目- `com.xamarin.WatchKitCatalog.watchkitextension`

- 监视应用程序项目- `com.xamarin.WatchKitCatalog.watchkitapp`

此外，请确保这些**Info.plist**设置正确无误：

- 监视应用程序项目的`WKCompanionAppBundleIdentifier`与父/容器应用的捆绑 ID 匹配 (ie。 在 iPhone 上运行);

- 监视工具包扩展项目**WKApp 捆绑 ID**与监视应用程序项目的捆绑 ID 匹配。

可以通过双击编辑标识符**Info.plist**每个项目中的文件。

这是屏幕截图**监视扩展**Info.plist 文件中，显示**Watch 应用**也标识符：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)
    
![](project-references-images/infoplist-extension.png "此屏幕截图是监视扩展的 Info.plist 文件")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)
    
![](project-references-images/infoplist-extension-vs.png "此屏幕截图是监视扩展的 Info.plist 文件")

-----

这是屏幕截图**Watch 应用**Info.plist 文件。
当前**Watch OS**版本是 8.2，因此**部署目标**Watch 应用应**8.2**。 请注意，如果必须安装 Xcode 6.3，此值可能设置为 8.3-应将其更改 8.2。

![](project-references-images/infoplist-watchapp.png "观看 Info.plist 文件")

Watch 应用的部署目标可以是不同的监视扩展和 iOS 应用。

