---
ms.openlocfilehash: 1d2bed830af97ce1ff329a5396a415247a43189d
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "61372901"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在 MainPage.xaml 中，修改 [`Button`](xref:Xamarin.Forms.Button) 声明，改变其视觉外观：

    ```xaml
    <Button Text="Click me"
            Clicked="OnButtonClicked"
            TextColor="Blue"
            BackgroundColor="Aqua"
            BorderColor="Red"
            BorderWidth="5"
            CornerRadius="5"
            WidthRequest="150"
            HeightRequest="75" />
    ```

    此代码设置更改 [`Button`](xref:Xamarin.Forms.Button) 视觉外观的属性。 [`TextColor`](xref:Xamarin.Forms.Button.TextColor) 属性设置 `Button` 文本的颜色，[`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) 属性设置文本背景的颜色。 [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) 属性设置 `Button` 周围区域的颜色，[`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) 属性设置边框的宽度。 默认情况下，`Button` 是矩形，但可以通过将 [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius) 属性设置为合适的值来设定圆角。 另外，通过设置 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 和 [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 属性来改变 `Button` 的大小。

1. 在 Visual Studio 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选远程 iOS 模拟器或 Android Emulator 内的应用程序。 请注意，[`Button`](xref:Xamarin.Forms.Button) 的外观已更改：

    [![iOS 和 Android 上视觉外观已更改的按钮屏幕截图](../images/change-button-appearance.png "外观已更改的按钮")](../images/change-button-appearance-large.png#lightbox "外观已更改的按钮")

    有关设置 [`Button`](xref:Xamarin.Forms.Button) 外观的详细信息，请参阅 [Xamarin.Forms 按钮](~/xamarin-forms/user-interface/button.md)指南中的[按钮外观](~/xamarin-forms/user-interface/button.md#button-appearance)。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在 MainPage.xaml 中，修改 [`Button`](xref:Xamarin.Forms.Button) 声明，改变其视觉外观：

    ```xaml
    <Button Text="Click me"
            Clicked="OnButtonClicked"
            TextColor="Blue"
            BackgroundColor="Aqua"
            BorderColor="Red"
            BorderWidth="5"
            CornerRadius="5"
            WidthRequest="150"
            HeightRequest="75" />
    ```

    此代码设置更改 [`Button`](xref:Xamarin.Forms.Button) 视觉外观的属性。 [`TextColor`](xref:Xamarin.Forms.Button.TextColor) 属性设置 `Button` 文本的颜色，[`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) 属性设置文本背景的颜色。 [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) 属性设置 `Button` 周围区域的颜色，[`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) 属性设置边框的宽度。 默认情况下，`Button` 是矩形，但可以通过将 [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius) 属性设置为合适的值来设定圆角。 另外，通过设置 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 和 [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 属性来改变 `Button` 的大小。

1. 在 Visual Studio for Mac 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选 iOS 模拟器或 Android Emulator 内的应用程序。 请注意，[`Button`](xref:Xamarin.Forms.Button) 的外观已更改：

    [![iOS 和 Android 上视觉外观已更改的按钮屏幕截图](../images/change-button-appearance.png "外观已更改的按钮")](../images/change-button-appearance-large.png#lightbox "外观已更改的按钮")

    有关设置 [`Button`](xref:Xamarin.Forms.Button) 外观的详细信息，请参阅 [Xamarin.Forms 按钮](~/xamarin-forms/user-interface/button.md)指南中的[按钮外观](~/xamarin-forms/user-interface/button.md#button-appearance)。
