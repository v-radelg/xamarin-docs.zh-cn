---
title: 在 Xamarin.iOS 中显示图像
description: 本文介绍如何在 Xamarin.iOS 应用程序，并使用 C# 代码或将其分配给 iOS 设计器中的控件显示该图像的图像资产包括。
ms.prod: xamarin
ms.assetid: 60288B12-49E3-4E87-8690-D04A5EC7A664
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/24/2018
ms.openlocfilehash: 69952b0bac884fe8b14edcbb87aa8fad47594880
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57670697"
---
# <a name="displaying-an-image-in-xamarinios"></a>在 Xamarin.iOS 中显示图像

_本文介绍如何在 Xamarin.iOS 应用程序，并使用 C# 代码或将其分配给 iOS 设计器中的控件显示该图像的图像资产包括。_

## <a name="adding-and-organizing-images-in-a-xamarinios-app"></a>添加和组织中的 Xamarin.iOS 应用的映像

添加时使用的图像在 Xamarin.iOS 应用程序中，开发人员将使用_资产目录_以支持每个 iOS 设备和应用所需的分辨率。

在 iOS 7 中，添加**资产目录图像集**包含所有版本或所需支持各种设备和缩放比例的应用的图像的表示形式。 而不是依靠图像资产文件名**图像集**使用 Json 文件来指定哪个映像属于哪些设备和/或解决方法。 这是首选的方法来管理和支持 iOS （从 iOS 9 或更高版本) 中的映像。

## <a name="adding-images-to-an-asset-catalog-image-set"></a>将映像添加到资产目录映像设置

如上面所述**资产目录图像集**包含所有版本或所需支持各种设备和缩放比例的应用的图像的表示形式。 而不是依靠图像资产文件名**图像集**使用 Json 文件来指定哪个映像属于哪些设备和/或解决方法。

若要创建新的映像集，并将图像添加到它，请执行以下操作：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 在中**解决方案资源管理器**，双击`Assets.xcassets`文件将其打开进行编辑：

    ![](displaying-an-image-images/imageset01.png "在解决方案资源管理器 Assets.xcassets")
2. 右键单击**Assets 列表**，然后选择**新映像设置**:

    ![](displaying-an-image-images/imageset02.png "添加新的图像集")
3. 选择新的图像集并将显示在编辑器：

    ![](displaying-an-image-images/imageset03.png "设置图像编辑器")
4. 从此处，将在映像中的每个不同的设备和所需的解决方法。 
5. 双击新的图像集**名称**中**Assets 列表**对其进行编辑：![](displaying-an-image-images/imageset04.png "编辑新图像集的名称")

使用时**映像设置**在 iOS 设计器中，只需选择集的名称从下拉列表在属性编辑器中：

![](displaying-an-image-images/imageset06.png "从下拉列表中选择图像集的名称")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 打开从资产目录**解决方案资源管理器**，并在左上角中单击**加上**按钮：

    ![](displaying-an-image-images/asset5.png "单击加号按钮")

2. 选择**添加图像集**和映像设置编辑器将显示为新的图像集。 从此处，将在映像中的每个不同的设备和所需的解决方法。 

    ![](displaying-an-image-images/asset7.png "图像集编辑器")

### <a name="renaming-an-image-set"></a>重命名图像集

若要重命名的图像集，请执行以下操作：

1. 在中**解决方案资源管理器**，双击**资产目录**文件将其打开进行编辑：

    ![](displaying-an-image-images/rename01.png "在解决方案资源管理器中的资产目录")
2. 选择**映像设置**重命名：

    ![](displaying-an-image-images/rename02.png "选择要重命名的图像集")
3. 在中**属性资源管理器**，滚动到底部并选择**名称**(在**杂项**部分):

    ![](displaying-an-image-images/rename03.png "在杂项部分下选择名称")
4. 输入一个新**名称**有关**映像设置**并保存所做的更改。

-----

使用时**集中映像**在代码中，它按名称引用通过调用`FromBundle`方法的`UIImage`类。 例如：

```csharp
MonkeyImage.Image = UIImage.FromBundle ("PurpleMonkey");
```

> [!IMPORTANT]
> 如果分配给映像设置的映像未正确显示，请确保正确的文件名要用于`FromBundle`方法 (**映像设置**而不是父级**资产目录**名称)。 为 PNG 图像`.png`扩展，则可以省略。 对于其他图像格式，该扩展是必需的 （例如。 `PurpleMonkey.jpg`）格式模式中出现的位置生成。

### <a name="using-vector-images-in-asset-catalogs"></a>使用资产目录中的矢量图像

版本最低为 iOS 8 中，特殊**向量**类，如已添加到**图像集**的开发人员可包括**PDF**格式中改为包括盒式的矢量图像不同分辨率的各个位图文件。 使用此方法提供的单个向量文件`@1x`分辨率 （格式为向量 PDF 文件） 和`@2x`和`@3x`将在编译时生成并包含在应用程序的捆绑中文件的版本。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![](displaying-an-image-images/imageset05.png "资产目录编辑器中的矢量图像")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](displaying-an-image-images/asset8.png "资产目录编辑器中的矢量图像")

-----

例如，如果开发人员包括`MonkeyIcon.pdf`文件作为资产目录的分辨率高达 150px x 150px，以下资产在编译时将在最终应用程序捆绑包中包含的位图的矢量：

- `MonkeyIcon@1x.png` -150px x 150px 解析。
- `MonkeyIcon@2x.png` -为 300 像素 x 300px 解析。
- `MonkeyIcon@3x.png` -450px x 450px 解析。

以下应考虑使用资产目录中的 PDF 矢量图像时：

- 这是不完整的向量支持，因为 pdf 文件将在编译时和在最终应用程序中提供的位图是位图的光栅化。
- 设置资产目录中后，不能调整图像的大小。 如果开发人员尝试调整图像 （不管是在代码中或通过使用自动布局和大小类） 的大小的图像将会扭曲就像任何其他的位图。
- 资产目录才符合 iOS 7 及更高版本，如果应用需要可支持 iOS 6 或更低时，它不能使用资产目录。

## <a name="working-with-template-images"></a>使用模板映像

根据 iOS 应用的设计，可能有开发人员需要自定义图标或内部用户界面来匹配更改配色方案 （例如，根据用户首选项） 中的映像的时间。

若要轻松地实现此效果，切换_呈现模式_的图像资产以进行**模板映像**:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](displaying-an-image-images/templateimage01.png "呈现模式设置为模板映像")](displaying-an-image-images/templateimage01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](displaying-an-image-images/templateimage01vs.png "呈现模式设置为模板")](displaying-an-image-images/templateimage01vs.png#lightbox)

-----

从 iOS 设计器中，将图像资产分配给 UI 控件，则设置**色彩**着色，让图像：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](displaying-an-image-images/templateimage03.png "设置着色，让图像的色彩")](displaying-an-image-images/templateimage03.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](displaying-an-image-images/templateimage03vs.png "设置着色，让图像的色彩")](displaying-an-image-images/templateimage03vs.png#lightbox)

-----

（可选） 可以直接在代码中设置的图像资产和色彩：

```csharp
MyIcon.Image = UIImage.FromBundle ("MessageIcon");
MyIcon.TintColor = UIColor.Red;
```

若要使用模板映像完全通过代码，执行以下操作：

```csharp
if (MyIcon.Image != null) {
    var mutableImage = MyIcon.Image.ImageWithRenderingMode (UIImageRenderingMode.AlwaysTemplate);
    MyIcon.Image = mutableImage;
    MyIcon.TintColor = UIColor.Red;
}
```

因为`RenderMode`的属性`UIImage`是只读的请使用`ImageWithRenderingMode`方法来创建具有所需的呈现模式下设置的映像的新实例。

有三个可能的设置`UIImage.RenderMode`通过`UIImageRenderingMode`枚举：

* `AlwaysOriginal` -强制为原始源图像文件无需更改任何呈现的图像。
* `AlwaysTemplate` -强制要呈现为一个模板映像的着色的像素与指定的图像`Tint`颜色。
* `Automatic` -呈现图像，因为模板或原始基于环境中使用。 例如，如果在使用的映像`UIToolBar`， `UINavigationBar`，`UITabBar`或`UISegmentControl`将被视为一个模板。

## <a name="adding-new-assets-collections"></a>添加新资产集合

使用资产目录中的映像时可能有一个新集合会必需的而不是添加到应用的映像的所有`Assets.xcassets`集合。 例如，在设计上按需资源。

若要向项目添加新的资产目录：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 右键单击**项目名称**中**解决方案资源管理器**，然后选择**添加** > **新文件...**
2. 选择**iOS** > **资产目录**，输入**名称**作为集合，然后单击**新建**按钮：

    ![](displaying-an-image-images/asset01.png "创建一个新的资产目录")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 在解决方案资源管理器，右键单击**资产目录**文件夹，然后选择**添加 > 新的资产目录**。
2. 为其提供一个名称，然后单击**添加**:

    ![](displaying-an-image-images/asset1.png "创建一个新的资产目录")

-----

在这里，集合可以得到与默认值的方式相同`Assets.xcassets`自动包含在项目中的集合。

## <a name="using-images-with-controls"></a>与控件使用图像

使用映像来支持应用程序，除了 iOS 还将图像与应用程序控件类型，例如选项卡栏、 工具栏、 导航栏、 表和按钮。 若要使图像在控件上显示的简单方法是将分配`UIImage`到控件的实例`Image`属性。

### <a name="frombundle"></a>FromBundle

`FromBundle`方法调用是同步的 （阻止） 调用具有大量的内置的例如缓存支持和自动处理各种分辨率的图像文件的映像加载和管理功能。  

下面的示例演示如何设置的图像`UITabBarItem`上`UITabBar`:

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage");
```

假设`MyImage`是添加到资产目录上面的图像资产的名称。 资产目录映像时，只需指定映像设置的名称`FromBundle`方法**PNG**格式的映像：

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage");
```

对于任何其他图像格式，包括名称的扩展。 例如：

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage.jpg");
```

有关图标和图像的详细信息，请参阅 Apple 文档上[自定义图标和图像创建指南](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)。

## <a name="displaying-an-image-in-a-storyboards"></a>在情节提要中显示图像

图像添加到 Xamarin.iOS 项目使用资产目录后，它可以轻松地显示在情节提要使用`UIImageView`iOS 设计器中。 例如，如果已添加以下图像资产：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![](displaying-an-image-images/display01.png "已添加的示例图像资产")

执行以下操作以将其显示在情节提要：

1. 双击`Main.storyboard`文件中**解决方案资源管理器**打开 iOS 设计器中为进行编辑。
2. 选择**映像视图**从**工具箱**:

     ![](displaying-an-image-images/display02.png "从工具箱中选择图像视图")
3. 将图像视图拖动到设计图面和位置上并调整其大小根据需要：

    ![](displaying-an-image-images/display03.png "在设计图面上新的图像视图")
4. 在**小组件**一部分**属性资源管理器**选择所需**图像**资产，以便显示：

    ![](displaying-an-image-images/display04.png "选择所需的图像资产，以显示")
5. 在中**视图**部分中，使用**模式**来控制如何将图像调整**图像视图**调整大小。
6. 与**图像视图**处于选中状态，单击它再次添加**约束**:

    ![](displaying-an-image-images/display05.png "将约束添加")
7. 拖动"T"调整的句柄的每个边缘**图像视图**旁边的"固定"到边的图像的屏幕对应。 这样一来，**图像视图**将缩小和增大屏幕调整大小时。
8. 将所做的更改保存到情节提要。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](displaying-an-image-images/display01vs.png "已添加的示例图像资产")

执行以下操作以将其显示在情节提要：

1. 双击`Main.storyboard`文件中**解决方案资源管理器**打开 iOS 设计器中为进行编辑。
2. 选择**映像视图**从**工具箱**:

     ![](displaying-an-image-images/display02vs.png "从工具箱中选择图像视图")
3. 将图像视图拖动到设计图面和位置上并调整其大小根据需要：

    ![](displaying-an-image-images/display03vs.png "在设计图面上新的图像视图")
4. 在**小组件**一部分**属性资源管理器**选择所需**图像**资产，以便显示：

    ![](displaying-an-image-images/display04vs.png "选择所需的图像资产，以显示")
5. 在中**视图**部分中，使用**模式**来控制如何将图像调整**图像视图**调整大小。
6. 与**图像视图**处于选中状态，单击它再次添加**约束**:

    ![](displaying-an-image-images/display05vs.png "将约束添加")
7. 拖动"T"调整的句柄的每个边缘**图像视图**旁边的"固定"到边的图像的屏幕对应。 这样一来，**图像视图**将缩小和增大屏幕调整大小时。
8. 将所做的更改保存到情节提要。

-----

## <a name="displaying-an-image-in-code"></a>在代码中显示图像

就像在情节提要中显示图像，图像添加到 Xamarin.iOS 项目使用资产目录后可以轻松地显示使用 C# 代码。

请参见以下示例：

```csharp
// Create an image view that will fill the
// parent image view and set the image from
// an Image Asset

var imageView = new UIImageView (View.Frame);
imageView.Image = UIImage.FromBundle ("Kemah");

// Add the Image View to the parent view
View.AddSubview (imageView);
```

此代码将创建一个新`UIImageView`并为其初始大小和位置。 然后，它从图像资产添加到项目中加载图像并将添加`UIImageView`到父`UIView`显示它。

## <a name="related-links"></a>相关链接

- [使用图像 （示例）](https://developer.xamarin.com/samples/WorkingWithImages/)
- [Hello，iPhone](~/ios/get-started/hello-ios/index.md)
- [图像大小和分辨率 (Apple)](https://developer.apple.com/design/human-interface-guidelines/ios/icons-and-images/image-size-and-resolution/)
