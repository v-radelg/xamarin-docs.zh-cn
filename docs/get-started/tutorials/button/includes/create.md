---
ms.openlocfilehash: a7a5d51b8931aa9dffd2c16b4ead51371390f033
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61372904"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 启动 Visual Studio，新建名为 ButtonTutorial 的 Xamarin.Forms 空白应用。 确保该应用使用 .NET Standard 作为共享代码机制。

    > [!IMPORTANT]
    > 本教程中的 C# 和 XAML 片段要求将解决方案命名为 ButtonTutorial。 使用不同的名称会导致：将本教程中的代码复制到解决方案中时出现生成错误。

    有关创建的 .NET Standard 库的详细信息，请参阅 [Xamarin.Forms 快速入门的深入探讨](~/get-started/first-app/index.md)中的 [Xamarin.Forms 应用程序剖析](~/get-started/first-app/index.md)。

1. 在“解决方案资源管理器”的 ButtonTutorial 项目中，双击 MainPage.xaml 将其打开。 然后在 MainPage.xaml 中，删除所有模板代码，替换为以下代码：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>    
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="ButtonTutorial.MainPage">
       <StackLayout Margin="20,35,20,20">
           <Button Text="Click me" />
       </StackLayout>
    </ContentPage>
    ```

    此代码以声明方式定义页面的用户界面，该界面由 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 中的 [`Button`](xref:Xamarin.Forms.Button) 组成。 [`Button.Text`](xref:Xamarin.Forms.Button.Text) 属性指定在 `Button` 中显示的文本。

1. 在 Visual Studio 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选远程 iOS 模拟器或 Android Emulator 内的应用程序：

    [![iOS 和 Android 上按钮的屏幕截图](../images/create-button.png "包含文本的按钮")](../images/create-button-large.png#lightbox "Button containing text")

    注意，[`Button`](xref:Xamarin.Forms.Button) 默认趋于占据容许使用的全部空间，在本例中为其父级 ([`StackLayout`](xref:Xamarin.Forms.StackLayout)) 的全部宽度。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 启动 Visual Studio for Mac，新建名为 ButtonTutorial 的 Xamarin.Forms 空白应用。 确保该应用使用 .NET Standard 作为共享代码机制。

    > [!IMPORTANT]
    > 本教程中的 C# 和 XAML 片段要求将解决方案命名为 ButtonTutorial。 使用不同的名称会导致：将本教程中的代码复制到解决方案中时出现生成错误。

    有关创建的 .NET Standard 库的详细信息，请参阅 [Xamarin.Forms 快速入门的深入探讨](~/get-started/first-app/index.md)中的 [Xamarin.Forms 应用程序剖析](~/get-started/first-app/index.md)。

1. 在 Solution Pad 的 ButtonTutorial 项目中，双击 MainPage.xaml 将其打开。 然后在 MainPage.xaml 中，删除所有模板代码，替换为以下代码：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="ButtonTutorial.MainPage">
       <StackLayout Margin="20,35,20,20">
           <Button Text="Click me" />
       </StackLayout>
    </ContentPage>
    ```

    此代码以声明方式定义页面的用户界面，该界面由 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 中的 [`Button`](xref:Xamarin.Forms.Button) 组成。 [`Button.Text`](xref:Xamarin.Forms.Button.Text) 属性指定在 `Button` 中显示的文本。

1. 在 Visual Studio for Mac 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选 iOS 模拟器或 Android 模拟器内的应用程序：

    [![iOS 和 Android 上按钮的屏幕截图](../images/create-button.png "包含文本的按钮")](../images/create-button-large.png#lightbox "Button containing text")

    注意，[`Button`](xref:Xamarin.Forms.Button) 默认趋于占据容许使用的全部空间，在本例中为其父级 ([`StackLayout`](xref:Xamarin.Forms.StackLayout)) 的全部宽度。
