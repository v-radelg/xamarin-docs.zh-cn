---
ms.openlocfilehash: 28e10d1a2404d70d5329936e9bfeeea032a6fe99
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61375340"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 启动 Visual Studio，新建空白的名为 GridTutorial 的 Xamarin.Forms 应用。 确保该应用使用 .NET Standard 作为共享代码机制。

    > [!IMPORTANT]
    > 本教程中的 C# 和 XAML 片段要求将解决方案命名为 GridTutorial。 使用不同的名称会导致：将本教程中的代码复制到解决方案中时出现生成错误。

    有关创建的 .NET Standard 库的详细信息，请参阅 [Xamarin.Forms 快速入门的深入探讨](~/get-started/first-app/index.md)中的 [Xamarin.Forms 应用程序剖析](~/get-started/first-app/index.md)。

1. 在“解决方案资源管理器”的 GridTutorial 项目中，双击 MainPage.xaml 将其打开。 然后在 MainPage.xaml 中，删除所有模板代码，替换为以下代码：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="GridTutorial.MainPage">
        <Grid Margin="20,35,20,20">
            <Label Text="The Grid has its Margin property set, to control the rendering position of the Grid." />
        </Grid>
    </ContentPage>
    ```

    此代码以声明方式定义页面的用户界面，该界面由 [`Grid`](xref:Xamarin.Forms.Grid) 中的 [`Label`](xref:Xamarin.Forms.Label) 组成。 默认情况下，`Grid` 将其子视图放在一个单一位置。 因此，包含多个子项的 `Grid` 应指定列和行，这将在下一个练习中进行介绍。 此外，[`Margin`](xref:Xamarin.Forms.View.Margin) 属性表示 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 中 `Grid` 的呈现位置。

    > [!NOTE]
    > 除 [`Margin`](xref:Xamarin.Forms.View.Margin) 属性外，还可在 [`Grid`](xref:Xamarin.Forms.Grid) 上设置 [`Padding`](xref:Xamarin.Forms.Layout.Padding) 属性。 [`Padding`](xref:Xamarin.Forms.Layout.Padding) 属性值指定 `Grid` 中视图之间的距离。 有关详细信息，请参阅[边距和填充](~/xamarin-forms/user-interface/layouts/margin-and-padding.md)。

1. 在 Visual Studio 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选 iOS 远程模拟器或 Android Emulator 内的应用程序：

    [![iOS 和 Android 上“网格”中的“标签”的屏幕截图](../images/create-grid.png "包含标签的网格")](../images/create-grid-large.png#lightbox "Grid containing a Labels")

    有关 [`Grid`](xref:Xamarin.Forms.Grid) 的详细信息，请参阅 [Xamarin.Forms 网格](~/xamarin-forms/user-interface/layouts/grid.md)。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 启动 Visual Studio for Mac，新建空白的名为 GridTutorial 的 Xamarin.Forms 应用。 确保该应用使用 .NET Standard 作为共享代码机制。

    > [!IMPORTANT]
    > 本教程中的 C# 和 XAML 片段要求将解决方案命名为 GridTutorial。 使用不同的名称会导致：将本教程中的代码复制到解决方案中时出现生成错误。

    有关创建的 .NET Standard 库的详细信息，请参阅 [Xamarin.Forms 快速入门的深入探讨](~/get-started/first-app/index.md)中的 [Xamarin.Forms 应用程序剖析](~/get-started/first-app/index.md)。

1. 在 Solution Pad 的 GridTutorial 项目中，双击 MainPage.xaml 将其打开。 然后在 MainPage.xaml 中，删除所有模板代码，替换为以下代码：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="GridTutorial.MainPage">
        <Grid Margin="20,35,20,20">
            <Label Text="The Grid has its Margin property set, to control the rendering position of the Grid." />
        </Grid>
    </ContentPage>
    ```

    此代码以声明方式定义页面的用户界面，该界面由 [`Grid`](xref:Xamarin.Forms.Grid) 中的 [`Label`](xref:Xamarin.Forms.Label) 组成。 默认情况下，`Grid` 将其子视图放在一个单一位置。 因此，包含多个子项的 `Grid` 应指定列和行，这将在下一个练习中进行介绍。 此外，[`Margin`](xref:Xamarin.Forms.View.Margin) 属性表示 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 中 `Grid` 的呈现位置。

    > [!NOTE]
    > 除 [`Margin`](xref:Xamarin.Forms.View.Margin) 属性外，还可在 [`Grid`](xref:Xamarin.Forms.Grid) 上设置 [`Padding`](xref:Xamarin.Forms.Layout.Padding) 属性。 [`Padding`](xref:Xamarin.Forms.Layout.Padding) 属性值指定 `Grid` 中视图之间的距离。 有关详细信息，请参阅[边距和填充](~/xamarin-forms/user-interface/layouts/margin-and-padding.md)。

1. 在 Visual Studio for Mac 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选 iOS 模拟器或 Android 模拟器内的应用程序：

    [![iOS 和 Android 上“网格”中的“标签”的屏幕截图](../images/create-grid.png "包含标签的网格")](../images/create-grid-large.png#lightbox "Grid containing a Labels")

    有关 [`Grid`](xref:Xamarin.Forms.Grid) 的详细信息，请参阅 [Xamarin.Forms 网格](~/xamarin-forms/user-interface/layouts/grid.md)。
