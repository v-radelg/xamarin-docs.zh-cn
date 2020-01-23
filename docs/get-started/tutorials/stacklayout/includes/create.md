---
ms.openlocfilehash: 80688c0796a112bcb444a15cd96a6b176b8c16e0
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "72715228"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

若要完成本教程，应使用 Visual Studio 2019（最新版本），且安装了“使用 .NET 的移动开发”工作负载  。 此外，还需要一个匹配的 Mac，用于在 iOS 上生成教程应用程序。 有关安装 Xamarin 平台的信息，请参阅[安装 Xamarin](~/get-started/installation/index.md)。 有关将 Visual Studio 2019 连接到 Mac 生成主机的信息，请参阅[通过“与 Mac 配对”进行 Xamarin.iOS 开发](~/ios/get-started/installation/windows/connecting-to-mac/index.md)。

1. 启动 Visual Studio，新建名为 StackLayoutTutorial 的 Xamarin.Forms 空白应用  。 确保该应用使用 .NET Standard 作为共享代码机制。

    > [!IMPORTANT]
    > 本教程中的 C# 和 XAML 片段要求将解决方案命名为 StackLayoutTutorial  。 使用不同的名称会导致：将本教程中的代码复制到解决方案中时出现生成错误。

    有关创建的 .NET Standard 库的详细信息，请参阅 [Xamarin.Forms 快速入门的深入探讨](~/get-started/quickstarts/deepdive.md)中的 [Xamarin.Forms 应用程序剖析](~/get-started/quickstarts/deepdive.md#anatomy-of-a-xamarinforms-application)。

1. 在“解决方案资源管理器”的 StackLayoutTutorial 项目中，双击 MainPage.xaml 将其打开    。 然后在 MainPage.xaml 中，删除所有模板代码，替换为以下代码  ：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="StackLayoutTutorial.MainPage">
        <StackLayout Margin="20,35,20,25">
            <Label Text="The StackLayout has its Margin property set, to control the rendering position of the StackLayout." />
            <Label Text="The Padding property can be set to specify the distance between the StackLayout and its children." />
            <Label Text="The Spacing property can be set to specify the distance between views in the StackLayout." />
        </StackLayout>
    </ContentPage>
    ```

    此代码以声明方式定义页面的用户界面，该界面由 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 中的三个 [`Label`](xref:Xamarin.Forms.Label) 实例组成。 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 将它的子元素视图（`Label` 实例）置于默认为垂直方向的单行中。 此外，[`Margin`](xref:Xamarin.Forms.View.Margin) 属性表示 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 中 `StackLayout` 的呈现位置。

    > [!NOTE]
    > 除 [`Margin`](xref:Xamarin.Forms.View.Margin) 属性之外，还可以在 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 上设置 [`Padding`](xref:Xamarin.Forms.Layout.Padding) 和 [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) 属性。 [`Padding`](xref:Xamarin.Forms.Layout.Padding) 属性值指定 `StackLayout` 中视图之间的距离，[`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) 属性值指定 `StackLayout` 中每个子元素之间的空间大小。 有关详细信息，请参阅[边距和填充](~/xamarin-forms/user-interface/layouts/margin-and-padding.md)。

1. 在 Visual Studio 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选远程 iOS 模拟器或 Android Emulator 内的应用程序  ：

    [![iOS 和 Android 上 StackLayout 中子视图的屏幕截图](../images/create-stacklayout.png "包含标签实例的 StackLayout")](../images/create-stacklayout-large.png#lightbox "包含标签实例的 StackLayout")

    有关 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 的详细信息，请参阅 [Xamarin.Forms StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md)。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

若要完成本教程，应使用 Visual Studio for Mac（最新版），且安装了 iOS 和 Android 平台支持。 此外，还需要 Xcode（最新版）。 有关安装 Xamarin 平台的详细信息，请参阅[安装 Xamarin](~/get-started/installation/index.md)。

1. 启动 Visual Studio for Mac，新建名为 StackLayoutTutorial 的 Xamarin.Forms 空白应用  。 确保该应用使用 .NET Standard 作为共享代码机制。

    > [!IMPORTANT]
    > 本教程中的 C# 和 XAML 片段要求将解决方案命名为 StackLayoutTutorial  。 使用不同的名称会导致：将本教程中的代码复制到解决方案中时出现生成错误。

    有关创建的 .NET Standard 库的详细信息，请参阅 [Xamarin.Forms 快速入门的深入探讨](~/get-started/first-app/index.md)中的 [Xamarin.Forms 应用程序剖析](~/get-started/first-app/index.md)。

1. 在 Solution Pad 的 StackLayoutTutorial 项目中，双击 MainPage.xaml 将其打开    。 然后在 MainPage.xaml 中，删除所有模板代码，替换为以下代码  ：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="StackLayoutTutorial.MainPage">
        <StackLayout Margin="20,35,20,25">
            <Label Text="The StackLayout has its Margin property set, to control the rendering position of the StackLayout." />
            <Label Text="The Padding property can be set to specify the distance between the StackLayout and its children." />
            <Label Text="The Spacing property can be set to specify the distance between views in the StackLayout." />
        </StackLayout>
    </ContentPage>
    ```

    此代码以声明方式定义页面的用户界面，该界面由 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 中的三个 [`Label`](xref:Xamarin.Forms.Label) 实例组成。 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 将它的子元素视图（`Label` 实例）置于默认为垂直方向的单行中。 此外，[`Margin`](xref:Xamarin.Forms.View.Margin) 属性表示 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 中 `StackLayout` 的呈现位置。

    > [!NOTE]
    > 除 [`Margin`](xref:Xamarin.Forms.View.Margin) 属性之外，还可以在 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 上设置 [`Padding`](xref:Xamarin.Forms.Layout.Padding) 和 [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) 属性。 [`Padding`](xref:Xamarin.Forms.Layout.Padding) 属性值指定 `StackLayout` 中视图之间的距离，[`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) 属性值指定 `StackLayout` 中每个子元素之间的空间大小。 有关详细信息，请参阅[边距和填充](~/xamarin-forms/user-interface/layouts/margin-and-padding.md)。

1. 在 Visual Studio for Mac 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选 iOS 模拟器或 Android 模拟器内的应用程序  ：

    [![iOS 和 Android 上 StackLayout 中子视图的屏幕截图](../images/create-stacklayout.png "包含标签实例的 StackLayout")](../images/create-stacklayout-large.png#lightbox "包含标签实例的 StackLayout")

    有关 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 的详细信息，请参阅 [Xamarin.Forms StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md)。

-----

> [!div class="nextstepaction"]
> [我遇到了问题](https://github.com/MicrosoftDocs/xamarin-docs/issues/new?title=StackLayout+Tutorial+Step+1+Feedback&template=tutorial_template.md)
