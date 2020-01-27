---
ms.openlocfilehash: 875f00b379879aa131d37018f89e475170e5320e
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "67277127"
---
Xamarin.Forms 有一个称作警报的模态弹出项，用于警告用户或向用户询问简单的问题。 本练习使用 [`Page`](xref:Xamarin.Forms.Page) 类的 [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) 方法，向用户展示警报和询问简单的问题。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

若要完成本教程，应使用 Visual Studio 2019（最新版本），且安装了“使用 .NET 的移动开发”工作负载。 此外，还需要一个匹配的 Mac，用于在 iOS 上生成教程应用程序。 有关安装 Xamarin 平台的信息，请参阅[安装 Xamarin](~/get-started/installation/index.md)。 有关将 Visual Studio 2019 连接到 Mac 生成主机的信息，请参阅[通过“与 Mac 配对”进行 Xamarin.iOS 开发](~/ios/get-started/installation/windows/connecting-to-mac/index.md)。

1. 启动 Visual Studio，新建一个名为 PopupsTutorial 的 Xamarin.Forms 空白应用。 确保该应用使用 .NET Standard 作为共享代码机制。

    > [!IMPORTANT]
    > 本教程中的 C# 和 XAML 片段要求将解决方案命名为 PopupsTutorial。 使用不同的名称会导致：将本教程中的代码复制到解决方案中时出现生成错误。

    有关创建的 .NET Standard 库的详细信息，请参阅 [Xamarin.Forms 快速入门的深入探讨](~/get-started/first-app/index.md)中的 [Xamarin.Forms 应用程序剖析](~/get-started/first-app/index.md)。

1. 在“解决方案资源管理器”的 PopupsTutorial 项目中，双击 MainPage.xaml 将其打开。 然后在 MainPage.xaml 中，删除所有模板代码，替换为以下代码：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="PopupsTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Button Text="Display alert"
                    Clicked="OnDisplayAlertButtonClicked" />
            <Button Text="Display alert question"
                    Clicked="OnDisplayAlertQuestionButtonClicked" />
        </StackLayout>
    </ContentPage>
    ```

    此代码以声明方式定义页面的用户界面，该界面由 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 中的两个 [`Button`](xref:Xamarin.Forms.Button) 对象组成。 [`Button.Text`](xref:Xamarin.Forms.Button.Text) 属性指定每个 `Button` 中显示的文本，[`Clicked`](xref:Xamarin.Forms.Button.Clicked) 事件设置为将在下一步中创建的事件处理程序。

1. 在“解决方案资源管理器”的 PopupsTutorial 项目中，展开“MainPage.xaml”，然后双击“MainPage.xaml.cs”将其打开。 然后在 MainPage.xaml.cs 中，将 `OnDisplayAlertButtonClicked` 和 `OnDisplayAlertQuestionButtonClicked` 事件处理程序添加到类：

    ```csharp
    async void OnDisplayAlertButtonClicked(object sender, EventArgs e)
    {
        await DisplayAlert("Alert", "This is an alert.", "OK");
    }

    async void OnDisplayAlertQuestionButtonClicked(object sender, EventArgs e)
    {
        bool response = await DisplayAlert("Save?", "Would you like to save your data?", "Yes", "No");
        Console.WriteLine("Save data: " + response);
    }
    ```

    相应的事件处理程序方法在点击 [`Button`](xref:Xamarin.Forms.Button) 时得到执行。 `OnDisplayAlertButtonClicked` 方法调用 [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) 方法，以显示带有一个“取消”按钮的模态警报。 警报解除后，用户可以继续与应用程序交互。

    `OnDisplayAlertQuestionButtonClicked` 方法调用 [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) 方法重载，以显示带有“接受”按钮和“取消”按钮的模态警报。 用户选择其中一个按钮后，所选内容将作为 `boolean` 返回。

    > [!IMPORTANT]
    > [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) 方法为异步方法，应始终使用 `await` 关键字等待。

1. 在 Visual Studio 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选远程 iOS 模拟器或 Android Emulator 内的应用程序。 然后，点击第一个 [`Button`](xref:Xamarin.Forms.Button)：

    [![iOS 和 Android 上的警报屏幕截图](../images/alert.png "警报")](../images/alert-large.png#lightbox "警报")

    警报解除后，点击第二个 [`Button`](xref:Xamarin.Forms.Button)：

    [![iOS 和 Android 上提出问题的警报屏幕截图](../images/alert-question.png "提出问题的警报")](../images/alert-question-large.png#lightbox "提出问题的警报")

    注意，为问题选择一个回答之后，该回答输出到 Visual Studio 的“输出”窗口。

    若要详细了解如何显示警报，请参阅[显示弹出窗口](~/xamarin-forms/user-interface/pop-ups.md)指南中的[显示警报](~/xamarin-forms/user-interface/pop-ups.md#display-an-alert)。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

若要完成本教程，应使用 Visual Studio for Mac（最新版），且安装了 iOS 和 Android 平台支持。 此外，还需要 Xcode（最新版）。 有关安装 Xamarin 平台的详细信息，请参阅[安装 Xamarin](~/get-started/installation/index.md)。

1. 启动 Visual Studio for Mac，新建名为 PopupsTutorial 的 Xamarin.Forms 空白应用。 确保该应用使用 .NET Standard 作为共享代码机制。

    > [!IMPORTANT]
    > 本教程中的 C# 和 XAML 片段要求将解决方案命名为 PopupsTutorial。 使用不同的名称会导致：将本教程中的代码复制到解决方案中时出现生成错误。

    有关创建的 .NET Standard 库的详细信息，请参阅 [Xamarin.Forms 快速入门的深入探讨](~/get-started/first-app/index.md)中的 [Xamarin.Forms 应用程序剖析](~/get-started/first-app/index.md)。

1. 在 Solution Pad 的 PopupsTutorial 项目中，双击 MainPage.xaml 将其打开。 然后在 MainPage.xaml 中，删除所有模板代码，替换为以下代码：

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="PopupsTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <Button Text="Display alert"
                    Clicked="OnDisplayAlertButtonClicked" />
            <Button Text="Display alert question"
                    Clicked="OnDisplayAlertQuestionButtonClicked" />
        </StackLayout>
    </ContentPage>
    ```

    此代码以声明方式定义页面的用户界面，该界面由 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 中的两个 [`Button`](xref:Xamarin.Forms.Button) 对象组成。 [`Button.Text`](xref:Xamarin.Forms.Button.Text) 属性指定每个 `Button` 中显示的文本，[`Clicked`](xref:Xamarin.Forms.Button.Clicked) 事件设置为将在下一步中创建的事件处理程序。

1. 在 Solution Pad 的 PopupsTutorial 项目中，展开“MainPage.xaml”，然后双击“MainPage.xaml.cs”将其打开。 然后在 MainPage.xaml.cs 中，将 `OnDisplayAlertButtonClicked` 和 `OnDisplayAlertQuestionButtonClicked` 事件处理程序添加到类：

    ```csharp
    async void OnDisplayAlertButtonClicked(object sender, EventArgs e)
    {
        await DisplayAlert("Alert", "This is an alert.", "OK");
    }

    async void OnDisplayAlertQuestionButtonClicked(object sender, EventArgs e)
    {
        bool response = await DisplayAlert("Save?", "Would you like to save your data?", "Yes", "No");
        Console.WriteLine("Save data: " + response);
    }
    ```

    相应的事件处理程序方法在点击 [`Button`](xref:Xamarin.Forms.Button) 时得到执行。 `OnDisplayAlertButtonClicked` 方法调用 [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) 方法，以显示带有一个“取消”按钮的模态警报。 警报解除后，用户可以继续与应用程序交互。

    `OnDisplayAlertQuestionButtonClicked` 方法调用 [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) 方法重载，以显示带有“接受”按钮和“取消”按钮的模态警报。 用户选择其中一个按钮后，所选内容将作为 `boolean` 返回。

    > [!IMPORTANT]
    > [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) 方法为异步方法，应始终使用 `await` 关键字等待。

1. 在 Visual Studio for Mac 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选 iOS 模拟器或 Android Emulator 内的应用程序。 然后，点击第一个 [`Button`](xref:Xamarin.Forms.Button)：

    [![iOS 和 Android 上的警报屏幕截图](../images/alert.png "警报")](../images/alert-large.png#lightbox "警报")

    警报解除后，点击第二个 [`Button`](xref:Xamarin.Forms.Button)：

    [![iOS 和 Android 上提出问题的警报屏幕截图](../images/alert-question.png "提出问题的警报")](../images/alert-question-large.png#lightbox "提出问题的警报")

    注意，为问题选择一个回答之后，该回答输出到 Visual Studio for Mac 的“输出”窗口。

    若要详细了解如何显示警报，请参阅[显示弹出窗口](~/xamarin-forms/user-interface/pop-ups.md)指南中的[显示警报](~/xamarin-forms/user-interface/pop-ups.md#display-an-alert)。
