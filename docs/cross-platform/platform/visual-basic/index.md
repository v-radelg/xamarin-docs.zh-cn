---
title: 可移植 Visual Basic.NET
description: 本指南将说明如何使用 Visual Basic 编写可移植类库 (PCL) 项目，可针对 Xamarin.iOS 和 Xamarin.Android 的解决方案中。
ms.prod: xamarin
ms.assetid: f264c632-8feb-4015-a5e5-cb9c681c787d
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
---

# <a name="portable-visual-basicnet"></a>可移植 Visual Basic.NET

Xamarin iOS 和 Android 项目本身并不支持 Visual Basic;但是开发人员可以使用可移植类库，将现有 Visual Basic 代码迁移到 iOS 和 Android，或在 Visual Basic 中编写其应用程序逻辑的重要部分。 可以完全在 Visual Basic （不包括自定义呈现器、 依赖关系服务和 XAML 代码隐藏文件） 中创建 Xamarin.Forms 应用程序。

## <a name="requirements"></a>要求

在 Xamarin.Android 4.10.1，Xamarin.iOS 7.0.4 和 Xamarin Studio 4.2，这意味着任何使用这些工具创建的 Xamarin 项目可以合并 Visual Basic PCL 的程序集已添加可移植类库支持。

若要创建并编译 Visual Basic 可移植类库必须在 Windows (Visual Studio 2012 或更高版本) 上使用 Visual Studio。

> [!NOTE]
> Visual Basic 库只能创建和编译使用 Visual Studio。 Xamarin.iOS 和 Xamarin.Android 不支持 Visual Basic 语言。
>
> 如果您在 Visual Studio 中单独工作可以从 Xamarin.iOS 和 Xamarin.Android 项目来引用 Visual Basic 项目中。
>
> 如果你的 iOS 和 Android 项目必须同时加载在 Visual Studio for Mac 应从 Visual Basic PCL 来引用输出程序集。


## <a name="creating-a-visual-basicnet-pcl"></a>创建 Visual Basic.NET PCL

本部分将指导完成如何创建 Visual Basic 可移植类库使用 Visual Studio。
然后可以在其他项目，包括 Xamarin.iOS、 Xamarin.Android 和 Xamarin.Forms 应用中引用库。

### <a name="creating-a-pcl"></a>创建 PCL

在 Visual Studio 中添加 Visual Basic PCL 时必须选择描述哪些平台库应与兼容的配置文件。 在 PCL 文档简介中介绍了配置文件。

若要创建 PCL 并选择其配置文件的步骤如下：

1.  在中**新的项目**屏幕上，选择**Visual Basic > 类库 （可移植）** 选项：

    [![](images/image1-sml.png "创建新的 Visual Basic 可移植库")](images/image1.png#lightbox)

1.  Visual Studio 会立即提示使用以下**添加可移植类库**对话框，以便可以配置该配置文件。 刻度线的平台需要支持，并按**确定**。

    [![](images/image2-sml.png "通过选择平台选择 PCL 配置文件")](images/image2.png#lightbox)

1.  Visual Basic PCL 项目将显示如中所示**解决方案资源管理器**如下所示：

    [![](images/image3-sml.png "空的 Visual Studio PCL 项目")](images/image3.png#lightbox)


在 PCL 中现已准备好要添加的 Visual Basic 代码。 PCL 项目可以引用其他项目 （应用程序项目，库项目和甚至是其他 PCL 项目）。

### <a name="editing-the-pcl-profile"></a>编辑 PCL 配置文件

PCL 配置文件 （用于控制在 PCL 中适用于哪些平台） 都可以查看和更改通过右键单击项目，然后选择**属性 > 库 > 更改...**.生成的对话框屏幕截图所示：

 [![](images/image4-sml.png "在项目属性中编辑 PCL 配置文件")](images/image4.png#lightbox)

如果已向 PCL 添加代码后更改配置文件，，就可以在库将无法再编译，如果代码引用不是新选择的配置文件的一部分的功能。


## <a name="summary"></a>总结

本文演示了如何使用 Visual Basic 代码中使用 Visual Studio 和可移植类库的 Xamarin 应用程序。 尽管 Xamarin 不直接支持 Visual Basic，编译到一个 PCL 的 Visual Basic 允许使用要包含在 iOS 和 Android 应用中的 Visual Basic 编写的代码。

以下页面介绍如何在本机模式或 Xamarin.Forms 应用中使用 Visual Basic.NET Pcl:

- [生成本机使用 VB 的 Xamarin.iOS 和 Xamarin.Android 应用](native-apps.md)
- [构建使用 VB 的 Xamarin.Forms 应用程序](xamarin-forms.md)


## <a name="related-links"></a>相关链接

- [TaskyPortableVB （示例）](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyPortableVB)
- [XamarinFormsVB (sample)](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/XamarinFormsVB)
- [使用.NET Framework (Microsoft) 的跨平台开发](https://msdn.microsoft.com/library/gg597391(v=vs.110).aspx)
