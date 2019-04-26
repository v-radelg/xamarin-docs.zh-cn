---
title: 使用 Visual Basic.NET 的 Xamarin.Forms
description: 可以修改 Xamarin.Forms PCL 项目模板以使用 Visual Basic for 主程序集，实际上就允许你构建跨平台移动应用程序使用 VB.NET。
ms.prod: xamarin
ms.assetid: da4b4ba9-9205-47dc-8bae-23272ede2c50
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: f397cf595a9ae151c5f105341733b2c57023fe99
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61282232"
---
# <a name="xamarinforms-using-visual-basicnet"></a>使用 Visual Basic.NET 的 Xamarin.Forms

Xamarin 不直接支持 Visual Basic-按照此页后，可以创建一个 C# Xamarin.Forms PCL 解决方案并将替换 Visual Basic 为常见代码 PCL 项目上的说明。

[![](xamarin-forms-images/hero-sml.png "创建 Xamarin.Forms PCL 解决方案并将替换 Visual Basic 为常见代码 PCL 项目")](xamarin-forms-images/hero.png#lightbox)

> [!NOTE]
> 您必须使用 Visual Studio 在 Windows 上使用 Visual Basic 程序。

## <a name="xamarinforms-with-visual-basic-walkthrough"></a>使用 Visual Basic 演练的 Xamarin.Forms

请执行以下步骤创建一个简单的 Xamarin.Forms 项目，则使用 Visual Basic:

1. 创建一个新*Xamarin.Forms C#* 使用可移植类库 (PCL) 的解决方案。
转到**文件 > 新建项目**并在**新项目**窗口导航到**已安装 > 模板 > Visual C# > 跨平台**然后选择**跨平台应用 （Xamarin.Forms 或本机） > Xamarin.Forms**。

2. 右键单击解决方案并**添加 > 新建项目**。

3. 选择**Visual Basic > 类库 （可移植）** 项目类型：

   [![](xamarin-forms-images/add-vb-2-sml.png "添加新可移植类库项目")](xamarin-forms-images/add-vb-2.png#lightbox)

4. 若要配置正确的 PCL 配置文件，如上所示选择的平台 （请务必包括 Xamarin.iOS 和 Xamarin.Android）：

   ![](xamarin-forms-images/add-vb-3-sml.png "选择要支持的平台")

5. Visual Basic 项目中右键单击并选择**属性**，然后将更改**默认命名空间**以匹配现有 C# 项目：

   ![](xamarin-forms-images/add-vb-4s-sml.png "请确保 Visual Basic 根命名空间与匹配 Xamarin.Forms 应用")

6. 右键单击新的 Visual Basic 项目，然后选择**管理 Nuget 包**，然后安装**Xamarin.Forms**和关闭程序包管理器窗口。

   [![](xamarin-forms-images/add-vb-4-sml.png "窗体和关闭程序包管理器窗口")](xamarin-forms-images/add-vb-4.png#lightbox)

7. 重命名默认**Class1**文件*并*类到`App`:

   [![](xamarin-forms-images/add-vb-5-sml.png "向应用程序重命名默认 Class1 文件和类")](xamarin-forms-images/add-vb-5.png#lightbox)

8. 粘贴下面的代码插入**App.vb**文件，它将成为 Xamarin.Forms 应用的起始点。 请记住包括`Imports Xamarin.Forms`并添加`Inherits Application`到类：

    ```vb 
    Imports Xamarin.Forms

    Public Class App
        Inherits Application

        Public Sub New()
            Dim label = New Label With {.HoriztonalTextAlignment = TextAlignment.Center,
                                        .FontSize = Device.GetNamedSize(NamedSize.Medium, GetType(Label)),
                                        .Text = "Welcome to Xamarin.Forms with Visual Basic.NET"}

            Dim stack = New StackLayout With {
                .VerticalOptions = LayoutOptions.Center
            }
            stack.Children.Add(label)

            Dim page = New ContentPage
            page.Content = stack
            MainPage = page

        End Sub

    End Class
    ```

9. 现在，我们需要指向新的 Visual Basic 项目的 iOS 和 Android 项目。
右键单击**引用**节点中的 iOS 和 Android 项目以打开**引用管理器**。 取消勾选标记C#可移植库和刻度线 VB 可移植库 （不要忘了，此方法适用于 iOS 和 Android 项目）。

   [![](xamarin-forms-images/add-vb-8-sml.png "删除旧的项目引用，请添加 Visual Basic 参考")](xamarin-forms-images/add-vb-8.png#lightbox)

10. C# 可移植项目中删除。 添加新 **.vb**文件来构建出 Xamarin.Forms 应用程序。 新模板`ContentPage`在 Visual Basic 中的 s 如下所示：

    ```vb
    Imports Xamarin.Forms

    Public Class Page2
    Inherits ContentPage

        Public Sub New()
            Dim label = New Label With {.HoriztonalTextAlignment = TextAlignment.Center,
                                        .FontSize = Device.GetNamedSize(NamedSize.Medium, GetType(Label)),
                                        .Text = "Visual Basic ContentPage"}

            Dim stack = New StackLayout With {
                .VerticalOptions = LayoutOptions.Center
            }
            stack.Children.Add(label)

            Content = stack
        End Sub
    End Class
    ```

## <a name="limitations-of-visual-basic-in-xamarinforms"></a>Xamarin.Forms 中的 Visual Basic 的限制

如上所述[可移植 Visual Basic.NET 页](~/cross-platform/platform/visual-basic/index.md)，Xamarin 不支持 Visual Basic 语言。 这意味着有一些限制，可以使用 Visual Basic:

 - 不能在 Visual Basic 中编写自定义呈现器，它们必须是用 C# 编写的本机平台项目中。

 - 不能在 Visual Basic 中编写依赖关系服务实现，它们必须编写 C# 中的本机平台项目中。

 - 不能在 Visual Basic 项目中包括 XAML 页-隐藏代码生成器只能生成 C#。 可能要包含在一个单独的、 引用，C# 可移植类库中的 XAML 数据绑定用于填充通过 Visual Basic 模型的 XAML 文件 (此示例包含在[示例](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/XamarinFormsVB/XamlPages))。

 - Xamarin 不支持 Visual Basic.NET 的语言。

## <a name="related-links"></a>相关链接

- [XamarinFormsVB (sample)](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/XamarinFormsVB)
- [使用.NET Framework 的跨平台开发](https://docs.microsoft.com/dotnet/standard/cross-platform/)
