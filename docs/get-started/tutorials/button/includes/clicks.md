---
ms.openlocfilehash: d1f7d209eaaca62a55b768646f51024609057a63
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "61372902"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在“MainPage.xaml”中，修改 [`Button`](xref:Xamarin.Forms.Button) 声明，以便为 [`Clicked`](xref:Xamarin.Forms.Button.Clicked) 事件设置处理程序：

    ```xaml
    <Button Text="Click me"
            Clicked="OnButtonClicked" />
    ```

    此代码将 [`Clicked`](xref:Xamarin.Forms.Button.Clicked) 事件设置为将在下一步中创建的名为 `OnButtonClicked` 的事件处理程序。

1. 在“解决方案资源管理器”的 ButtonTutorial 项目中，展开“MainPage.xaml”，然后双击“MainPage.xaml.cs”将其打开。 然后在 MainPage.xaml.cs 中，将 `OnButtonClicked` 事件处理程序添加到类：

    ```csharp
    void OnButtonClicked(object sender, EventArgs e)
    {
        (sender as Button).Text = "Click me again!";
    }
    ```

    点击 [`Button`](xref:Xamarin.Forms.Button) 时，将执行 `OnButtonClicked` 方法。 `sender` 参数是负责触发 `Clicked` 事件的 `Button` 对象，还可用于访问 `Button` 对象。 此事件处理程序更新 `Button` 显示的文本。

    > [!NOTE]
    > 除了 `Clicked` 事件，`Button` 还定义了 [`Pressed`](xref:Xamarin.Forms.Button.Pressed) 和 [`Released`](xref:Xamarin.Forms.Button.Released) 事件。 有关详细信息，请参阅 [Xamarin.Forms 按钮指南](~/xamarin-forms/user-interface/button.md)中的[按下并释放按钮](~/xamarin-forms/user-interface/button.md#pressing-and-releasing-the-button)。

1. 在 Visual Studio 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选远程 iOS 模拟器或 Android Emulator 内的应用程序。 单击 [`Button`](xref:Xamarin.Forms.Button)，观察其显示的文本是否更改：

    [![在 iOS 和 Android 上收到单击后按钮文本更改的屏幕截图](../images/handle-button-click.png "处理按钮单击")](../images/handle-button-click-large.png#lightbox "处理按钮单击")

    有关处理按钮单击事件的详细信息，请参阅 [Xamarin.Forms 按钮](~/xamarin-forms/user-interface/button.md)指南中的[处理按钮单击事件](~/xamarin-forms/user-interface/button.md#handling-button-clicks)。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在“MainPage.xaml”中，修改 [`Button`](xref:Xamarin.Forms.Button) 声明，以便为 [`Clicked`](xref:Xamarin.Forms.Button.Clicked) 事件设置处理程序：

    ```xaml
    <Button Text="Click me"
            Clicked="OnButtonClicked" />
    ```

    此代码将 [`Clicked`](xref:Xamarin.Forms.Button.Clicked) 事件设置为将在下一步中创建的名为 `OnButtonClicked` 的事件处理程序。

1. 在 Solution Pad 的 ButtonTutorial 项目中，展开“MainPage.xaml”，然后双击“MainPage.xaml.cs”将其打开。 然后在 MainPage.xaml.cs 中，将 `OnButtonClicked` 事件处理程序添加到类：

    ```csharp
    void OnButtonClicked(object sender, EventArgs e)
    {
        (sender as Button).Text = "Click me again!";
    }
    ```

    点击 [`Button`](xref:Xamarin.Forms.Button) 时，将执行 `OnButtonClicked` 方法。 `sender` 参数是负责触发 `Clicked` 事件的 `Button` 对象，还可用于访问 `Button` 对象。 此事件处理程序更新 `Button` 显示的文本。

    > [!NOTE]
    > 除了 `Clicked` 事件，`Button` 还定义了 [`Pressed`](xref:Xamarin.Forms.Button.Pressed) 和 [`Released`](xref:Xamarin.Forms.Button.Released) 事件。 有关详细信息，请参阅 [Xamarin.Forms 按钮指南](~/xamarin-forms/user-interface/button.md)中的[按下并释放按钮](~/xamarin-forms/user-interface/button.md#pressing-and-releasing-the-button)。

1. 在 Visual Studio for Mac 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选 iOS 模拟器或 Android Emulator 内的应用程序。 单击 [`Button`](xref:Xamarin.Forms.Button)，观察其显示的文本是否更改：

    [![在 iOS 和 Android 上收到单击后按钮文本更改的屏幕截图](../images/handle-button-click.png "处理按钮单击")](../images/handle-button-click-large.png#lightbox "处理按钮单击")

    有关处理按钮单击事件的详细信息，请参阅 [Xamarin.Forms 按钮](~/xamarin-forms/user-interface/button.md)指南中的[处理按钮单击事件](~/xamarin-forms/user-interface/button.md#handling-button-clicks)。
