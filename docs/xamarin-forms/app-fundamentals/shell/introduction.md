---
title: Xamarin.Forms Shell 简介
description: Xamarin.Forms Shell 提供了大多数应用程序所需的基本功能，包括常见的导航用户体验、基于 URI 的导航方案，以及集成的搜索处理程序。
ms.prod: xamarin
ms.assetid: 4604DCB5-83DA-458A-8B02-6508A740BE0E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/24/2019
ms.openlocfilehash: 38553b3b30388bc64fd97a7ac96a671279d20bc5
ms.sourcegitcommit: b986460787677cf8c2fc7cc8c03f4bc60c592120
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/24/2019
ms.locfileid: "66213333"
---
# <a name="xamarinforms-shell"></a>Xamarin.Forms Shell

[![下载示例](~/media/shared/download.png) 下载示例](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/Xaminals/)

Xamarin.Forms Shell 简化了移动应用程序开发，方法是提供大多数移动应用程序所需的基本功能，包括：

- 用于描述应用程序的视觉层次结构的单个位置。
- 常见的导航用户体验。
- 允许导航到应用程序中的任何页面的基于 URI 的导航方案。
- 集成的搜索处理程序。

此外，Shell 应用程序还因提高了呈现速度和减少了内存消耗而受益。

> [!IMPORTANT]
> Xamarin.Forms Shell 仅适用于 iOS 和 Android。 现有的 iOS 和 Android 应用程序可以采用 Shell，立即获益于导航、性能和扩展性方面的改进。

## <a name="shell-navigation-experience"></a>Shell 导航体验

Shell 提供了基于浮出控件和选项卡的固定导航体验。 Shell 应用程序中的顶级导航是浮出控件或底部选项卡栏，具体取决于应用程序的导航要求。 下面的示例演示了顶级导航是浮出控件的应用程序：

[![iOS 和 Android 上的 Shell 浮出控件的屏幕截图](introduction-images/flyout.png "Shell 浮出控件")](introduction-images/flyout-large.png#lightbox "Shell 浮出控件")

选择浮出控件项导致选中并显示表示项的底部选项卡：

[![iOS 和 Android 上的 Shell 底部选项卡的屏幕截图](introduction-images/monkeys.png "Shell 底部选项卡")](introduction-images/monkeys-large.png#lightbox "Shell 底部选项卡")

> [!NOTE]
> 浮出控件未打开时，系统可以将底部选项卡栏视作应用程序中的顶级导航。

每个选项卡都会显示 [`ContentPage`](xref:Xamarin.Forms.ContentPage)。 但是，如果底部选项卡包含多个页面，则页面可通过顶部选项卡栏进行导航：

[![iOS 和 Android 上的 Shell 顶部选项卡的屏幕截图](introduction-images/cats.png "Shell 顶部选项卡")](introduction-images/cats-large.png#lightbox "Shell 顶部选项卡")

在每个选项卡中，可以导航到其他 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 对象：

[![iOS 和 Android 上的 Shell 页面导航的屏幕截图](introduction-images/cat-details.png "Shell 应用导航")](introduction-images/cat-details-large.png#lightbox "Shell 应用导航")

## <a name="related-links"></a>相关链接

- [Xaminals（示例）](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/Xaminals/)
