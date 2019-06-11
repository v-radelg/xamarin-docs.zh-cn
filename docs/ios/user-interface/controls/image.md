---
title: 显示使用 Xamarin.iOS 的图像
description: 本文档介绍如何在 Xamarin.iOS 中显示图像。 以编程方式或通过 iOS 设计器，它介绍了添加到应用程序的映像。
ms.prod: xamarin
ms.assetid: 67CA8DB6-769D-42BB-A137-3AF933789FE1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/13/2018
ms.openlocfilehash: 25c5595fac2ea2aa7a87d6640cc6b5c399ab5e5e
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827816"
---
# <a name="displaying-images-with-xamarinios"></a>显示使用 Xamarin.iOS 的图像

将映像添加到您的应用程序需要两个步骤： 首先，将图像添加到你的项目;然后，添加控件和代码以在屏幕上显示它们。 请参阅[处理图像](~/ios/app-fundamentals/images-icons/index.md)一文，其中更详细的图像在 Xamarin.iOS 中处理。

## <a name="adding-images-to-your-app"></a>将映像添加到您的应用程序

可以将图像添加到您 Visual Studio for Mac 解决方案中的任意文件夹; 如果**生成操作**设置为**内容**然后文件将包含在您的应用程序，可以显示。

Visual Studio for Mac 还支持名为的特殊目录**资源**，还可以包含图像文件。 资源文件夹中的文件应具有**生成操作**设置为**BundleResource**。

此屏幕截图显示**生成操作**右击所显示的文件时选项之后：

 [![](image-images/image30a.png "生成操作菜单")](image-images/image30a.png#lightbox)

Visual Studio for Mac 将通常选择正确**生成操作**自动，但应注意这些设置，尤其是如果你的项目中移动文件。

### <a name="adding-an-image-file"></a>添加图像文件

若要将图像文件添加到你的项目，首先右键单击该项目并选择**添加文件...**

 [![](image-images/image31a.png "将文件添加...菜单")](image-images/image31a.png#lightbox)

选择映像 （或映像） 想要包括在标准文件对话框中。 默认值生成操作将为图像**BundleResource** – 不重写此值，除非有特定原因。

 [![](image-images/image32a.png "添加文件对话框")](image-images/image32a.png#lightbox)

该图像将添加到你的项目，并可用于加载并显示在代码中。 此屏幕截图中显示的图像添加到 iOS 应用程序项目：

 [![](image-images/image33a.png "在项目中的图像")](image-images/image33a.png#lightbox)

### <a name="what-is-the-resources-directory"></a>什么是资源目录？

文件置于**资源**从常规文件 – 的内容以不同方式处理目录**资源**文件夹复制到应用程序的根目录，并且可以从此处在引用你的代码。 这很有用的原因有很多：

-  存储配置中的应用程序属性，如默认启动映像和应用程序图标的图像。
-  存储其他图像和独立于代码的文件，因此它们更易于管理 （子目录资源目录内容复制时会保留）。


**资源**目录是在库项目中，尤其有用，因为代码可以假设这些映像将被复制到使用方应用程序，使其能够更轻松地写入共享代码库，需要根图像、 声音、 视频中，XML 或其他文件。

**资源**必须因此命名目录，并且所有文件都应都具有生成操作设置为**BundleResource**。

## <a name="displaying-the-image"></a>显示图像

在 iOS 设计器中，使用**图像视图**以显示图像或动画的系列的图像。 **图像视图**从工具箱中的图标如下所示：

 [![](image-images/image35a.png "在工具箱中的 ImageView")](image-images/image35.png#lightbox)

拖动**图像视图**从**工具箱**到视图控制器上。 然后，在**图像视图 > 映像**下拉列表将提供你的项目中的所有可用的图像文件的列表。 选择任何一种即可将其添加到图像视图。

 [![](image-images/image36a.png "在工具箱中的 ImageView")](image-images/image36.png#lightbox)

### <a name="displaying-the-image-programmatically"></a>以编程方式显示的图像

因为**SF Monkey.jpg**位于根目录中的**资源**将出现在应用程序捆绑包的根目录中的运行时的目录。 若要在图像视图控件中显示此映像，请使用以下代码：

```csharp
imageview1.Image = UIImage.FromBundle("SF Monkey.png");
```

如果我们有放置中的图像**资源/Pics/SF Monkey.jpg**，然后代码将包括**Pics**文件夹路径中：

```csharp
imageview1.Image = UIImage.FromBundle("Pics/SF Monkey.png");
```

资源文件永远不会引用需要加入**资源**文件夹。

## <a name="related-links"></a>相关链接

- [控件 （示例）](https://developer.xamarin.com/samples/monotouch/Controls/)
