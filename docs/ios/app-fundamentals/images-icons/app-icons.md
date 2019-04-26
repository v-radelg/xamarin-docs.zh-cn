---
title: 在 Xamarin.iOS 应用程序图标
description: 本文档介绍如何使用 Xamarin.iOS 中的各种应用程序图标： 应用程序图标本身、 聚焦图标、 设置图标和 iTunes 图稿。
ms.prod: xamarin
ms.assetid: B7791574-4A0F-4CB6-8C18-36D40B5C91EB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/22/2017
ms.openlocfilehash: 51ccf3faa2524035a30b26af6652959699663e24
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61260885"
---
# <a name="application-icons-in-xamarinios"></a>在 Xamarin.iOS 应用程序图标

将详细介绍以下主题：

* [应用程序、 聚焦和设置图标](#icon-types)的不同类型的所需的 iOS 应用程序的图标。
* [管理使用资产目录图标](#managing)-使用资产目录的管理应用程序图标。
* [iTunes 图稿](#itunes)-提供所需的 iTunes 图稿交付你的应用程序的临时方法。

<a name="icon-types" />

## <a name="application-spotlight-and-settings-icons"></a>应用程序、 Spotlight 和设置图标

在 UI 控件和文档图标作为 Xamarin.iOS 应用程序，可以使用图像资产的相同方式，可以用于提供应用程序图标图像资产。 从 iPad 下面的屏幕截图演示了在 iOS 中的图标的三个使用：

- **应用程序图标**-每个 iOS 应用程序必须定义应用程序图标。 用户只需点击此图标以启动应用的 iOS 主屏幕。 此外，此图标使用 Game Center 有可能的话。 示例: 

    [![](app-icons-images/000.png "应用程序图标")](app-icons-images/000-full.png#lightbox)
- **聚焦图标**-每当用户在 Spotlight 搜索中，输入应用的名称将显示该图标。 示例: 

    [![](app-icons-images/000a.png "聚焦图标")](app-icons-images/000a-full.png#lightbox)
- **设置图标**-如果用户输入**设置**其 iOS 设备，此图标上的应用将显示在末尾**设置**应用的列表。 示例: 

    [![](app-icons-images/000b.png "设置图标")](app-icons-images/000b-full.png#lightbox)

将需要以下图像资产大小和分辨率以支持所有面向 iOS 5 到 9 （或更高版本） 的 iOS 的 Xamarin.iOS 应用程序所需的图标类型：

### <a name="iphone-icon-sizes"></a>iPhone 图标大小

- **iPhone: iOS 9 和 10 (iPhone 6 和 7 加上)**

    ||3 倍|
    |---|---|
    |应用程序图标|180x180|
    |聚焦|120x120|
    |设置|87x87|

- **iPhone: iOS 7 和 8**

    ||1x|2x|
    |---|---|---|
    |应用程序图标|60x60<sup>1</sup>|120x120|
    |聚焦|40x40<sup>2</sup>|80x80|
    |设置|-|-|

- **iPhone: iOS 5 和 6**

    ||1x|2x|
    |---|---|---|
    |应用程序图标|57x57|114x114|
    |聚焦|29x29|58x58|
    |设置|29 x 29<sup>3，4</sup>|58x58<sup>3，4</sup>|

### <a name="ipad-icon-sizes"></a>iPad 图标大小

- **iPad: iOS 9 和 10**

    ||2 x (iPad Pro)|
    |---|---|
    |应用程序图标|167x167<sup>6</sup>|
    |聚焦|120x120<sup>6</sup>|
    |设置|58x58<sup>5</sup>|

- **iPad: iOS 7 和 8**

    ||1x|2x|
    |---|---|---|
    |应用程序图标|76x76|152x152|
    |聚焦|40x40|80x80|
    |设置|-|-|

- **iPad: iOS 5 和 6**

    ||1x|2x|
    |---|---|---|
    |应用程序图标|72x72|144x144|
    |聚焦|50x50|100x100|
    |设置|29x29<sup>3, 5</sup>|58x58<sup>3，5</sup>|

 1. 这两个 Visual Studio for Mac 和 Xcode 不再支持设置适用于 iOS 7 的 1 x 图像。
 2. 使用资产目录时，不支持设置适用于 iOS 7 的 1 x 映像。
 3. iOS 7 和 8 作为 iOS 5 和 6 中使用相同的图像大小。
 4. 为 Spotlight 图标中使用的相同映像和大小。
 5. 使用相同的大小图标作为 iPhone。
 6. 仅支持资产目录图像集。
 
 有关图标的详细信息，请参阅 Apple[图标和图像的大小](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/IconMatrix.html#//apple_ref/doc/uid/TP40006556-CH27-SW1)文档。

<a name="managing" />

## <a name="managing-icons-with-asset-catalogs"></a>使用资产目录管理图标

图标，一种特殊`AppIcon`图像集可以添加到`Assets.xcassets`应用的项目文件中。 所有版本支持所有分辨率所需的映像中都包含_xcasset_和组合在一起。 Visual Studio for Mac 中的特殊编辑器，开发人员可包括和以图形方式设置这些映像。

若要使用资产目录，请执行以下操作：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 双击`Info.plist`文件中**解决方案资源管理器**打开进行编辑。
2. 向下滚动到**应用图标**部分。
3. 从**源**下拉列表中，确保**AppIcon**处于选中状态： 

    ![](app-icons-images/migrate01.png "确保选中 AppIcon")
4. 从**解决方案资源管理器**，双击`Assets.xcassets`文件将其打开进行编辑： 

    ![](app-icons-images/asset01.png "在解决方案资源管理器中的 Assets.xcassets 文件")
5. 选择`AppIcon`从列表中的资产，以显示`Icon Editor`:

    ![](app-icons-images/asset02.png "AppIcon 编辑器")
6. 单击给定的图标类型和选择所需的类型/大小的图像文件或从文件夹拖动图像中并将其放在所需的大小。
7. 单击**打开**按钮以在项目中包含图像，并将它设为 xcasset。
8. 重复上述步骤的所需的所有图像。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 双击**Info.plist**中的文件**解决方案资源管理器**:

    ![](app-icons-images/icon01w.png "选择 Info.plist")
2. 单击**视觉对象资产**选项卡，然后单击**使用资产目录**按钮下**应用图标**: 

    ![](app-icons-images/icon02w.png "选择视觉对象资产选项卡")
4. 从**解决方案资源管理器**，展开**资产目录**文件夹： 

    ![](app-icons-images/image009.png "展开资产目录文件夹")
5. 双击**媒体**文件以在编辑器中打开它： 

    ![](app-icons-images/image010.png "在编辑器中打开媒体文件")
6. 下**属性资源管理器**开发人员可以选择不同的类型和所需的图标大小。
7. 给定的图标类型单击并选择所需的类型/大小的图像文件。
8. 单击**打开**按钮以在项目中包含图像，并将它设为 xcasset。
9. 重复上述步骤的所需的所有图像。

-----

这是包括分配和管理将用于为应用提供应用程序、 聚焦和设置图标的图像资产的首选的方法。

### <a name="migrating-from-infoplist-to-asset-catalogs"></a>从 Info.plist 迁移到资产目录

对于现有的 Xamarin.iOS 应用程序使用`Info.plist`文件来管理它的图标中，强烈建议，开发人员将其转换为使用`AppIcons`内的图像资产`Assets.xcassets`。

请执行以下操作：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 双击`Info.plist`文件中**解决方案资源管理器**打开进行编辑。
2. 向下滚动到**应用图标**部分。
3. 从**源**下拉列表中选择**迁移到资产目录**: 

    ![](app-icons-images/migrate02.png "选择将迁移到资产目录")
4. 在中定义的任何现有图标`Info.plist`文件将迁移到`AppIcons`映像设置添加到`Assets.xcassets`: 

     ![](app-icons-images/migrate03.png "在 Assets.xcassets AppIcons 图像集")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 双击`Info.plist`文件中**解决方案资源管理器**打开进行编辑。
2. 在 iPhone 上单击图标部分： 

    ![](app-icons-images/image007.png "按照 iPhone 图标编辑器")
3. 向下滚动到**图标**部分。
4. 从**资产目录**下拉列表中选择**使用资产目录**。
5. 在中定义的任何现有图标`Info.plist`文件将迁移到`Images`集已添加到`Assets.xcassets`。
6. 保存对 `Info.plist` 文件所做的更改。

-----

<a name="itunes" />

## <a name="itunes-artwork"></a>iTunes 图稿

如果使用的应用 （不管是为企业用户或 beta 测试实际设备上） 交付的临时方法，开发人员还需要包括 512x512 和 1024x1024 图像将用于代表在 iTunes 中的应用。

若要指定 iTunes 图稿，请执行以下操作：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 双击`Info.plist`文件中**解决方案资源管理器**打开进行编辑。
2. 滚动到**iTunes 图稿**编辑器的部分： 

    ![](app-icons-images/itunes01.png "滚动到 iTunes 图稿编辑器部分")
3. 任何缺少的图像，请单击编辑器中的缩略图，从打开的文件对话框中选择所需的 iTunes 图稿的图像文件，单击**确定**按钮。
4. 重复此步骤直至所有所需映像已为指定应用程序。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 双击`Info.plist`文件中**解决方案资源管理器**打开进行编辑。

2. 单击**视觉对象资产**选项卡，展开**iTunes 图稿**: 

    ![](app-icons-images/itunes01w.png "编辑 iTunes 图稿 Visual Studio 中")
4. 任何缺少的图像，请单击编辑器中的缩略图，从打开的文件对话框中选择所需的 iTunes 图稿的图像文件，单击**打开**按钮。
5. 重复此步骤直至所有所需映像已为指定应用程序。

-----

## <a name="related-links"></a>相关链接

- [使用图像 （示例）](https://developer.xamarin.com/samples/WorkingWithImages/)
- [Hello，iPhone](~/ios/get-started/hello-ios/index.md)
- [自定义图标和图像创建指南](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html))
