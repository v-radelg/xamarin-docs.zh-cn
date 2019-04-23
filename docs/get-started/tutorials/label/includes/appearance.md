---
ms.openlocfilehash: 4fc6c50b5aa2ce502b4157ca2b15f0d33a68ecd1
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60896655"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在 MainPage.xaml 中，修改 [`Label`](xref:Xamarin.Forms.Label) 声明，改变其视觉外观：

    ```xaml
    <Label Text="Welcome to Xamarin.Forms!"
           TextColor="Blue"
           FontAttributes="Italic"
           FontSize="22"
           TextDecorations="Underline"
           HorizontalOptions="Center" />
    ```

    此代码设置更改 [`Label`](xref:Xamarin.Forms.Label) 视觉外观的属性。 [`TextColor`](xref:Xamarin.Forms.Label.TextColor) 属性设置 `Button` 文本的颜色。 [`FontAttributes`](xref:Xamarin.Forms.Label.FontAttributes) 属性将标签的字体设置为斜体，[`FontSize`](xref:Xamarin.Forms.Label.FontSize) 属性设置字号。 此外，通过设置 [`TextDecorations`](xref:Xamarin.Forms.Label.TextDecorations) 属性向 `Label` 应用下划线文本效果，并通过将 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 属性设置为 [`Center`](xref:Xamarin.Forms.LayoutOptions.Center) 使其水平居中。

1. 在 Visual Studio 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选远程 iOS 模拟器或 Android Emulator 内的应用程序。 请注意，[`Label`](xref:Xamarin.Forms.Label) 的外观已更改：

    [![iOS 和 Android 上视觉外观不同的标签的屏幕截图](../images/change-label-appearance.png "具有不同外观的标签")](../images/change-label-appearance-large.png#lightbox "Label with changed appearance")

    有关设置 [`Label`](xref:Xamarin.Forms.Label) 外观的详细信息，请参阅 [Xamarin.Forms 标签](~/xamarin-forms/user-interface/text/label.md)指南。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在 MainPage.xaml 中，修改 [`Label`](xref:Xamarin.Forms.Label) 声明，改变其视觉外观：

    ```xaml
    <Label Text="Welcome to Xamarin.Forms!"
           TextColor="Blue"
           FontAttributes="Italic"
           FontSize="22"
           TextDecorations="Underline"
           HorizontalOptions="Center" />
    ```

    此代码设置更改 [`Label`](xref:Xamarin.Forms.Label) 视觉外观的属性。 [`TextColor`](xref:Xamarin.Forms.Label.TextColor) 属性设置 `Button` 文本的颜色。 [`FontAttributes`](xref:Xamarin.Forms.Label.FontAttributes) 属性将标签的字体设置为斜体，[`FontSize`](xref:Xamarin.Forms.Label.FontSize) 属性设置字号。 此外，通过设置 [`TextDecorations`](xref:Xamarin.Forms.Label.TextDecorations) 属性向 `Label` 应用下划线文本效果，并通过将 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 属性设置为 [`Center`](xref:Xamarin.Forms.LayoutOptions.Center) 使其水平居中。

1. 在 Visual Studio for Mac 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选 iOS 模拟器或 Android Emulator 内的应用程序。 请注意，[`Label`](xref:Xamarin.Forms.Label) 的外观已更改：

    [![iOS 和 Android 上视觉外观不同的标签的屏幕截图](../images/change-label-appearance.png "具有不同外观的标签")](../images/change-label-appearance-large.png#lightbox "Label with changed appearance")

    有关设置 [`Label`](xref:Xamarin.Forms.Label) 外观的详细信息，请参阅 [Xamarin.Forms 标签](~/xamarin-forms/user-interface/text/label.md)指南。
