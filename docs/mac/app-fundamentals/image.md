---
title: 在 Xamarin.Mac 中的图像
description: 本文介绍如何使用图像和 Xamarin.Mac 应用程序中的图标。 它介绍了创建和维护的映像创建应用程序图标所需和使用 C# 代码和 Xcode 的 Interface Builder 中的映像。
ms.prod: xamarin
ms.assetid: C6B539C2-FC6A-4C38-B839-32BFFB9B16A7
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/15/2017
ms.openlocfilehash: 719efc87b8843d0d2fcd2643aab23aa6849d940a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61078635"
---
# <a name="images-in-xamarinmac"></a>在 Xamarin.Mac 中的图像

_本文介绍如何使用图像和 Xamarin.Mac 应用程序中的图标。它介绍了创建和维护的映像创建应用程序图标所需和使用 C# 代码和 Xcode 的 Interface Builder 中的映像。_

## <a name="overview"></a>概述

如果 Xamarin.Mac 应用程序中使用 C# 和.NET，则可以访问到相同的图像和图标工具使用的开发人员*Objective C*并*Xcode* does。

有几种方法资产在 macOS （以前称为 Mac OS X） 应用程序内使用该映像。 只需显示一个图像作为应用程序的 UI 的一部分提供图标，将其分配给 UI 控件的工具栏或源列表项，如 Xamarin.Mac 轻松地按以下方式向 macOS 应用程序添加强大图稿: 

- **UI 元素**-可以显示图像，作为背景或图像视图中的应用程序的一部分 (`NSImageView`)。
- **按钮**-可以在按钮中显示图像 (`NSButton`)。
- **图像单元格**-基于在表控件的一部分 (`NSTableView`或`NSOutlineView`)，可以在图像单元格中使用映像 (`NSImageCell`)。
- **工具栏项**-可以将图像添加到工具栏 (`NSToolbar`) 作为图像工具栏项 (`NSToolbarItem`)。
- **源列表图标**-源列表的一部分 (特殊格式`NSOutlineView`)。
- **应用图标**-一系列的图像可以组合到一起`.icns`设置并使用为应用程序的图标。 请参阅我们[应用程序图标](~/mac/deploy-test/app-icon.md)文档了解详细信息。

此外，macOS 提供了一系列可在整个应用程序使用的预定义映像。

[![示例运行的应用程序](image-images/intro01.png "示例运行的应用程序")](image-images/intro01-large.png#lightbox)

在本文中，我们将介绍 Xamarin.Mac 应用程序中使用图像和图标的基本知识。 强烈建议您明确[Hello，Mac](~/mac/get-started/hello-mac.md)文章第一次，具体而言[Xcode 和 Interface Builder 简介](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)并[输出口和操作](~/mac/get-started/hello-mac.md#outlets-and-actions)部分中的，因为它介绍了关键概念和技术，我们将在本文中使用。


## <a name="adding-images-to-a-xamarinmac-project"></a>将映像添加到 Xamarin.Mac 项目

添加时使用的图像在 Xamarin.Mac 应用程序中，有几个位置和方式，开发人员可以包括到项目的源的图像文件：

- **[弃用] 的主项目树**-映像可以直接添加到项目树。 在调用存储在代码中的主项目树中的映像时，指定没有文件夹位置。 例如：`NSImage image = NSImage.ImageNamed("tags.png");`。 
- **[弃用] 资源文件夹**-这两个特殊**资源**文件夹是用于将成为应用程序的一部分的任何文件的捆绑如图标、 启动屏幕或常规映像 （或任何其他图像或文件，开发人员想要添加）。 调用存储中的图像时**资源**文件夹从代码中的，就像映像存储在主项目树中，不指定任何文件夹位置。 例如：`NSImage.ImageNamed("tags.png")`。
- **自定义文件夹或子文件夹 [弃用]** -开发人员可以将自定义文件夹添加到项目源树和存储存在的映像。 该文件将添加的位置可以嵌套以进一步帮助组织项目的子文件夹中。 例如，如果开发人员添加`Card`文件夹到项目，并的子文件夹`Hearts`到该文件夹，然后存储映像**Jack.png**中`Hearts`文件夹，`NSImage.ImageNamed("Card/Hearts/Jack.png")`将加载的图像运行时。
- **资产目录图像集 [首选]** -在 OS X El Capitan 中，添加**资产目录图像集**包含所有版本或所需支持各种设备和缩放比例的图像的表示形式在应用程序。 而不是依靠图像资产文件名 (**@1x**， **@2x**)。

<a name="asset-catalogs" />

### <a name="adding-images-to-an-asset-catalog-image-set"></a>将映像添加到资产目录映像设置

如上面所述**资产目录图像集**包含所有版本或所需支持各种设备和缩放比例为应用程序的图像的表示形式。 而不是依靠图像资产文件名 （请参阅分辨率独立图像和映像命名法更高版本）**图像集**使用资产编辑器来指定哪个映像属于哪些设备和/或解决方法。

1. 在中**Solution Pad**，双击**Assets.xcassets**文件将其打开进行编辑： 

    ![选择 Assets.xcassets](image-images/imageset01.png "选择 Assets.xcassets")
2. 右键单击**Assets 列表**，然后选择**新映像设置**: 

    [![添加新的图像集](image-images/imageset02.png "添加新的图像集")](image-images/imageset02-large.png#lightbox)
3. 选择新的图像集并将显示在编辑器： 

    [![选择新的图像集](image-images/imageset03.png "选择新的图像集")](image-images/imageset03-large.png#lightbox)
4. 在这里我们可以拖动图像中的每个不同的设备和所需的解决方法。 
5. 双击新的图像集**名称**中**Assets 列表**对其进行编辑： 

    [![编辑图像集名称](image-images/imageset04.png "编辑图像集名称")](image-images/imageset04-large.png#lightbox)
    
一种特殊**向量**类，如已添加到**图像集**使我们能够包括_PDF_格式中改为包括在单个位图文件 casset 矢量图像不同的分辨率。 使用此方法，你提供的单个向量文件 **@1x** 分辨率 （格式为向量 PDF 文件） 和 **@2x** 并 **@3x** 将在编译时生成并包含在应用程序的捆绑中文件的版本。

[![映像设置编辑器接口](image-images/imageset05.png "映像设置编辑器接口")](image-images/imageset05-large.png#lightbox)

例如，如果您包括`MonkeyIcon.pdf`文件作为资产目录的分辨率高达 150px x 150px，以下资产在编译时将在最终应用程序捆绑包中包含的位图的矢量：

1. **MonkeyIcon@1x.png** -150px x 150px 解析。
2. **MonkeyIcon@2x.png** -为 300 像素 x 300px 解析。
3. **MonkeyIcon@3x.png** -450px x 450px 解析。

以下应考虑使用资产目录中的 PDF 矢量图像时：

- 这是不完整的向量支持，因为 pdf 文件将在编译时和在最终应用程序中提供的位图是位图的光栅化。
- 设置资产目录中后，不能调整图像的大小。 如果你尝试调整图像 （不管是在代码中或通过使用自动布局和大小类） 的大小的图像将会扭曲就像任何其他的位图。

使用时**集中映像**Xcode 的 Interface Builder 中可以只需选择集的名称从下拉列表中**属性检查器**:

![在 Xcode 的 Interface Builder 中选择一个映像设置](image-images/imageset06.png "在 Xcode 的 Interface Builder 中选择一个映像设置")

<a name="Adding-new-Assets-Collections"/>

### <a name="adding-new-assets-collections"></a>添加新资产集合

使用资产目录中的映像时可能有你想要创建新的集合，而不是添加到你的映像的所有**Assets.xcassets**集合。 例如，在设计上按需资源。

若要将新的资产目录添加到你的项目：

1. 右键单击该项目中**Solution Pad** ，然后选择**添加** > **新文件...**
2. 选择**Mac** > **资产目录**，输入**名称**作为集合，然后单击**新建**按钮： 

    ![添加一个新的资产目录](image-images/asset01.png "添加一个新的资产目录")

从此处你可以使用集合作为默认值相同的方式**Assets.xcassets**自动包含在项目中的集合。


### <a name="adding-images-to-resources"></a>将映像添加到资源

> [!IMPORTANT]
> 使用 macOS 应用中的映像的此方法由 Apple 已弃用。 应使用[资产目录图像集](#asset-catalogs)manager 到您的应用程序改为映像。

可以在 Xamarin.Mac 应用程序 （不管是在 C# 代码中或从接口生成器） 中使用的图像文件之前需要包含在项目的**资源**的文件夹**捆绑资源**。 若要将文件添加到项目中，执行以下操作：

1. 右键单击**资源**文件夹中的项目中**Solution Pad** ，然后选择**添加** > **添加文件...**: 

    ![将文件添加](image-images/add01.png "添加文件")
2. 从**添加文件**对话框中，选择图像文件添加到项目中，选择`BundleResource`有关**替代生成操作**然后单击**打开**按钮：

    [![选择要添加的文件](image-images/add02.png "选择要添加的文件")](image-images/add02-large.png#lightbox)
3. 如果文件尚不在**资源**文件夹中，系统会询问是否要**副本**，**移动**或者**链接**文件。 选择的每个花色您的需要，通常将要**复制**:

    ![选择添加操作](image-images/add04.png "选择添加操作")
4. 新文件将包含在项目中并用于读取： 

    ![新图像文件添加到 Solution Pad](image-images/add03.png "新图像文件添加到 Solution Pad")
5. 为任何所需的图像文件重复该过程。

在 Xamarin.Mac 应用程序中作为源映像，可以使用任何 png、 jpg 或 pdf 文件。 在下一步部分中，我们将介绍添加高分辨率版本的我们的图像和图标，以支持 Retina 基于 Mac。

> [!IMPORTANT]
> 如果要添加到映像**资源**文件夹中，您可以保留**替代生成操作**设置为**默认**。 默认值生成的此文件夹的操作是`BundleResource`。

## <a name="provide-high-resolution-versions-of-all-app-graphics-resources"></a>提供所有应用图形资源的高分辨率的版本

您将添加到 Xamarin.Mac 应用程序 （图标、 自定义控件、 自定义光标、 自定义图片等） 的任何图形资产需要具有高分辨率版本除了其标准分辨率版本。 这是必需的以便你的应用程序时的外观了最大努力 Retina 显示屏上的运行配备 Mac 计算机。


### <a name="adopt-the-2x-naming-convention"></a>采用@2x命名约定

> [!IMPORTANT]
> 使用 macOS 应用中的映像的此方法由 Apple 已弃用。 应使用[资产目录图像集](#asset-catalogs)manager 到您的应用程序改为映像。

创建映像的标准和高分辨率版本时，它们包含在 Xamarin.Mac 项目中时请遵循此映像对此命名约定：

- **标准分辨率**  - **ImageName.filename 扩展**(示例： **tags.png**)
- **高分辨率**  -  **ImageName@2x.filename-extension** (示例： **tags@2x.png**)

添加到项目时，它们将出现，如下所示：

![Solution Pad 中的图像文件](image-images/add03.png "Solution Pad 中的图像文件")

图像分配给 Interface Builder 中的 UI 元素时将只需选择中的文件_ImageName_**。**_文件扩展名_格式 (示例： **tags.png**)。 相同的 C# 代码中使用的映像，将选择的文件中_ImageName_**。**_文件扩展名_格式。

在 Xamarin.Mac 应用程序在 Mac 上运行时_ImageName_**。**_文件扩展名_上标准分辨率显示，将使用格式图像**ImageName@2x.filename-extension**映像将自动提取 Retina 显示屏基 Mac。


## <a name="using-images-in-interface-builder"></a>使用 Interface Builder 中的图像

你已添加到任何图像资源**资源**文件夹中你 Xamarin.Mac 项目，然后将生成操作设置为**BundleResource**自动将显示在 Interface Builder 中，并且可以是（如果它处理图像），则选择为 UI 元素的一部分。

若要在接口生成器中使用的映像，执行以下操作：

1. 添加到图像**资源**具有文件夹**生成操作**的`BundleResource`: 

     ![在 Solution Pad 中的图像资源](image-images/ib00.png "Solution Pad 中的图像资源")
2. 双击**Main.storyboard**文件以打开 Interface Builder 中为进行编辑： 

     [![编辑主要情节提要](image-images/ib01.png "编辑主要情节提要")](image-images/ib01-large.png#lightbox)
3. 将采用图像到设计图面上的用户界面元素 (例如，**图像工具栏项**): 

     ![编辑工具栏项](image-images/ib02.png "编辑工具栏项")
4. 选择添加到的映像**资源**中的文件夹**映像名称**下拉列表中： 

     [![选择工具栏项的图像](image-images/ib03.png "选择图像的工具栏项")](image-images/ib03-large.png#lightbox)
5. 在设计图面上，将显示所选的映像： 

     ![工具栏编辑器中正在显示的图像](image-images/ib04.png "工具栏编辑器中正在显示的图像")
6. 保存所做的更改并返回到 Visual Studio for Mac 与 Xcode 同步。

上面的步骤适用于任何 UI 元素，允许其图像属性设置**属性检查器**。 同样，如果你已包含 **@2x** 图像文件的版本，它将自动使用在显示屏基于 Mac。

> [!IMPORTANT]
> 如果映像中不可用**映像名称**下拉列表中，关闭在 Xcode 中的.storyboard 项目并重新打开它从 Visual Studio for mac。 如果映像仍不可用，请确保其**生成操作**是`BundleResource`并将映像添加到**资源**文件夹。

## <a name="using-images-in-c-code"></a>在 C# 代码中使用图像

当加载到内存中使用 C# 代码在 Xamarin.Mac 应用程序中的图像，图像将存储在`NSImage`对象。 如果在 Xamarin.Mac 应用程序捆绑包 （包含在资源） 中包含的图像文件，使用以下代码以将图像加载：

```csharp
NSImage image = NSImage.ImageNamed("tags.png");
```

上面的代码中使用静态`ImageNamed("...")`方法`NSImage`类以给定的图像加载到内存中从**资源**文件夹中，如果找不到图像，`null`将返回。 如果有包含喜欢 Interface Builder 中分配的映像 **@2x** 图像文件的版本，它将自动使用在显示屏基于 Mac。

若要加载外部 （来自 Mac 文件系统） 的应用程序的捆绑包的图像，请使用以下代码：

```csharp
NSImage image = new NSImage("/Users/KMullins/Documents/photo.jpg")
```

<a name="Working-with-Template-Images"/>

## <a name="working-with-template-images"></a>使用模板映像

根据你的 macOS 应用的设计，可能有您需要自定义图标或内部用户界面来匹配更改配色方案 （例如，根据用户首选项） 中的映像。

若要实现此效果，切换_呈现模式_给图像资产的**模板映像**:

[![设置模板映像](image-images/templateimage01.png "设置模板映像")](image-images/templateimage01-large.png#lightbox)

从 Xcode 的 Interface Builder 中，将分配到 UI 控件的图像资产：

![在 Xcode 的 Interface Builder 中选择映像](image-images/templateimage02.png "Xcode 的 Interface Builder 中选择图像")

或者，可以选择在代码中设置图像源：

```csharp
MyIcon.Image = NSImage.ImageNamed ("MessageIcon");
```

将以下公共函数添加到视图控制器：

```csharp
public NSImage ImageTintedWithColor(NSImage sourceImage, NSColor tintColor)
    => NSImage.ImageWithSize(sourceImage.Size, false, rect => {
        // Draw the original source image
        sourceImage.DrawInRect(rect, CGRect.Empty, NSCompositingOperation.SourceOver, 1f);

        // Apply tint
        tintColor.Set();
        NSGraphics.RectFill(rect, NSCompositingOperation.SourceAtop);

        return true;
    });
```

> [!IMPORTANT]
> 尤其是对于 macos Mojave 深色模式的问世，务必要避免`LockFocus`API 自定义呈现时`NSImage`对象。 此类映像成为静态值，将不会自动更新的外观或显示密度变化。
>
> 通过采用基于处理程序的机制更高版本，重新呈现的动态条件会自动发生时`NSImage`托管，例如，在`NSImageView`。

最后，若要染色模板映像，调用此函数对着色，让该映像：

```csharp
MyIcon.Image = ImageTintedWithColor (MyIcon.Image, NSColor.Red);
```

<a name="Using_Images_with_Table_Views" />

## <a name="using-images-with-table-views"></a>表视图中使用图像

用于包含映像中的单元格的一部分`NSTableView`，将需要更改表视图返回的数据是如何`NSTableViewDelegate's``GetViewForItem`要使用的方法`NSTableCellView`而不是典型`NSTextField`。 例如：

```csharp
public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
{

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTableCellView view = (NSTableCellView)tableView.MakeView (tableColumn.Title, this);
    if (view == null) {
        view = new NSTableCellView ();
        if (tableColumn.Title == "Product") {
            view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
            view.AddSubview (view.ImageView);
            view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
        } else {
            view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
        }
        view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
        view.AddSubview (view.TextField);
        view.Identifier = tableColumn.Title;
        view.TextField.BackgroundColor = NSColor.Clear;
        view.TextField.Bordered = false;
        view.TextField.Selectable = false;
        view.TextField.Editable = true;

        view.TextField.EditingEnded += (sender, e) => {

            // Take action based on type
            switch(view.Identifier) {
            case "Product":
                DataSource.Products [(int)view.TextField.Tag].Title = view.TextField.StringValue;
                break;
            case "Details":
                DataSource.Products [(int)view.TextField.Tag].Description = view.TextField.StringValue;
                break; 
            }
        };
    }

    // Tag view
    view.TextField.Tag = row;

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.ImageView.Image = NSImage.ImageNamed ("tags.png");
        view.TextField.StringValue = DataSource.Products [(int)row].Title;
        break;
    case "Details":
        view.TextField.StringValue = DataSource.Products [(int)row].Description;
        break;
    }

    return view;
}
```

有少量所关注的行。 首先，我们想要的图像的列，我们创建一个新`NSImageView`的所需的大小和位置，我们还创建一个新`NSTextField`并将根据是否我们使用的映像其默认位置：

```csharp
if (tableColumn.Title == "Product") {
    view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
    view.AddSubview (view.ImageView);
    view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
} else {
    view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
}
```

其次，我们需要将新的图像视图和文本字段包括在父`NSTableCellView`:

```csharp
view.AddSubview (view.ImageView);
...

view.AddSubview (view.TextField);
...

```

最后，我们需要告诉文本字段，它可以收缩和增长与表视图单元格：

```csharp
view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
```

示例输出：

[![在应用中显示图像的示例](image-images/tables01.png "举例说明在应用中显示图像")](image-images/tables01-large.png#lightbox)

有关使用表视图的详细信息，请参阅我们[表视图](~/mac/user-interface/table-view.md)文档。

<a name="Using_Images_with_Outline_Views" />

## <a name="using-images-with-outline-views"></a>使用大纲视图中使用映像

用于包含映像中的单元格的一部分`NSOutlineView`，将需要更改大纲视图返回的数据是如何`NSTableViewDelegate's``GetView`要使用的方法`NSTableCellView`而不是典型`NSTextField`。 例如：

```csharp
public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item) {
    // Cast item
    var product = item as Product;

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTableCellView view = (NSTableCellView)outlineView.MakeView (tableColumn.Title, this);
    if (view == null) {
        view = new NSTableCellView ();
        if (tableColumn.Title == "Product") {
            view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
            view.AddSubview (view.ImageView);
            view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
        } else {
            view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
        }
        view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
        view.AddSubview (view.TextField);
        view.Identifier = tableColumn.Title;
        view.TextField.BackgroundColor = NSColor.Clear;
        view.TextField.Bordered = false;
        view.TextField.Selectable = false;
        view.TextField.Editable = !product.IsProductGroup;
    }

    // Tag view
    view.TextField.Tag = outlineView.RowForItem (item);

    // Allow for edit
    view.TextField.EditingEnded += (sender, e) => {

        // Grab product
        var prod = outlineView.ItemAtRow(view.Tag) as Product;

        // Take action based on type
        switch(view.Identifier) {
        case "Product":
            prod.Title = view.TextField.StringValue;
            break;
        case "Details":
            prod.Description = view.TextField.StringValue;
            break; 
        }
    };

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.ImageView.Image = NSImage.ImageNamed (product.IsProductGroup ? "tags.png" : "tag.png");
        view.TextField.StringValue = product.Title;
        break;
    case "Details":
        view.TextField.StringValue = product.Description;
        break;
    }

    return view;
}
```

有少量所关注的行。 首先，我们想要的图像的列，我们创建一个新`NSImageView`的所需的大小和位置，我们还创建一个新`NSTextField`并将根据是否我们使用的映像其默认位置：

```csharp
if (tableColumn.Title == "Product") {
    view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
    view.AddSubview (view.ImageView);
    view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
} else {
    view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
}
```

其次，我们需要将新的图像视图和文本字段包括在父`NSTableCellView`:

```csharp
view.AddSubview (view.ImageView);
...

view.AddSubview (view.TextField);
...

```

最后，我们需要告诉文本字段，它可以收缩和增长与表视图单元格：

```csharp
view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
```

示例输出：

[![在大纲视图中显示的图像的示例](image-images/outline01.png "在大纲视图中显示的图像的示例")](image-images/outline01-large.png#lightbox)

有关使用大纲视图的详细信息，请参阅我们[大纲视图](~/mac/user-interface/outline-view.md)文档。


## <a name="summary"></a>总结

本文已采取了 Xamarin.Mac 应用程序中使用图像和图标的详细的信息。 我们看到了不同类型，并使用的图像、 如何在 Xcode 的 Interface Builder 中使用图像和图标和如何在 C# 代码中处理图像和图标。



## <a name="related-links"></a>相关链接

- [MacImages（示例）](https://developer.xamarin.com/samples/mac/MacImages/)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [表视图](~/mac/user-interface/table-view.md)
- [大纲视图](~/mac/user-interface/outline-view.md)
- [macOS X 人机接口指南 》](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
- [有关适用于 OS X 的高分辨率](https://developer.apple.com/library/content/documentation/GraphicsAnimation/Conceptual/HighResolutionOSX/Introduction/Introduction.html)
