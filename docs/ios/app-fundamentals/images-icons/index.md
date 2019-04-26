---
title: 图像和图标在 Xamarin.iOS
description: 本部分包括各种的文章将介绍如何在 Xamarin.iOS 应用中，例如将它们用作图标的图像处理、 启动屏幕或包括这些控件和自定义文档类型提供的图标。
ms.prod: xamarin
ms.assetid: 0AB8CC07-11E4-0D75-4119-AED1A1252424
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: f359da6b8744e03cfcbd77d48f5f77f216e828f8
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61250643"
---
# <a name="images-and-icons-in-xamarinios"></a>图像和图标在 Xamarin.iOS

_本部分包括各种的文章将介绍如何在 Xamarin.iOS 应用中，例如将它们用作图标的图像处理、 启动屏幕或包括这些控件和自定义文档类型提供的图标。_

有几种方法资产在 iOS 应用程序内使用该映像。 只需显示一个图像作为一部分应用程序的 UI，如将其分配给 UI 控件`UIButton`或`UIImageView`，到提供的图标和启动屏幕，Xamarin.iOS，使其轻松添加到 iOS 应用程序很好的图片，按以下方式： 

- **分辨率独立图像**– 用于 iOS 的内置支持跨不同的设备的解决方法和类型 （iPhone、 iPad 等） 处理图像。
- **资产目录图像集**-使用**资产目录图像集**进行管理和分组给定的图像资产的应用程序所需的所有版本。
- **IOS 设计器中的映像**-使用 iOS 设计器来设置控件的图像。
- **在代码中的映像**– 使用`UIImage`类的方法，以加载和处理图像资产并将其分配到 UI 控件中C#代码。
- **应用程序图标**-定义每个 iOS 应用所需的应用图标。 用户只需点击此图标以启动应用的 iOS 主屏幕。 此外，此图标使用 Game Center 有可能的话。
- **聚焦图标**-定义应用的特别推荐图标。 每当用户在 Spotlight 搜索中输入的应用的名称，将显示此图标。
- **设置图标**-定义应用程序的**设置**图标。 如果用户输入**设置**其 iOS 设备，此图标上的应用将显示在应用程序的设置列表末尾。 
- **启动屏幕**-定义应用程序的启动屏幕。 在用户点击应用图标之后，将显示的第一个视图之前，将显示空白屏幕。 幸运的是，iOS 包括支持使用情节提要显示图像替换空白屏幕。 
- **iTunes 图标**-提供 iTune 图标。 如果使用提供的应用 （不管是为企业用户或 beta 测试实际设备上） 的临时方法，开发人员还需要包括 512x512 和 1024x1024 图像将用于代表在 iTunes 中的应用。
- **文档图标**-映像用作的 Xamarin.iOS 应用程序支持，或创建任何特定文档类型的图标。

有几个创建 iOS 应用程序，以及多个位置中将使用这些资产的图像资产时应考虑的注意事项。 其中每项功能产生影响不仅多少图像资产是必需的但如何创建这些资产。 下列主题介绍了需要的图像资产、 如何在应用程序的捆绑包中包括这些资产和如何使用图像资产以提供所需的功能的类型：


## <a name="displaying-an-imageiosapp-fundamentalsimages-iconsdisplaying-an-imagemd"></a>[显示图像](~/ios/app-fundamentals/images-icons/displaying-an-image.md)

本文介绍如何在 Xamarin.iOS 应用程序，并使用 C# 代码或将其分配给 iOS 设计器中的控件显示该图像的图像资产包括。

## <a name="application-iconsiosapp-fundamentalsimages-iconsapp-iconsmd"></a>[应用程序图标](~/ios/app-fundamentals/images-icons/app-icons.md)

此文章涉及包括并管理一个 Xamarin.iOS 应用程序中的图像资产，以用作应用图标。

## <a name="alternate-app-iconsiosapp-fundamentalsimages-iconsalternate-app-iconsmd"></a>[备用的应用图标](~/ios/app-fundamentals/images-icons/alternate-app-icons.md)

Apple 向 iOS 10.3 的允许的应用程序来管理其图标添加了多项增强功能：

 - `ApplicationIconBadgeNumber` -获取或设置应用图标徽章中 Springboard。
 - `SupportsAlternateIcons` -如果`true`应用具有一组替代的图标。
 - `AlternateIconName` -返回当前选定的备用图标的名称或`null`如果使用的主图标。
 - `SetAlternameIconName` -使用此方法以切换到给定替换图标的应用程序的图标。


## <a name="launch-screensiosapp-fundamentalsimages-iconslaunch-screensmd"></a>[启动屏幕](~/ios/app-fundamentals/images-icons/launch-screens.md)

本文介绍如何使用一种特殊的情节提要对每个 iOS 设备大小和分辨率提供通用的启动屏幕。

## <a name="custom-document-typesiosapp-fundamentalsimages-iconscustom-document-typesmd"></a>[自定义文档类型](~/ios/app-fundamentals/images-icons/custom-document-types.md)

此文章涉及包括并管理一个 Xamarin.iOS 应用程序中的图像资产，以用作自定义文档类型的图标。



## <a name="related-links"></a>相关链接

- [使用图像 （示例）](https://developer.xamarin.com/samples/WorkingWithImages/)
- [Hello，iPhone](~/ios/get-started/hello-ios/index.md)
- [自定义图标和图像创建指南](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)
