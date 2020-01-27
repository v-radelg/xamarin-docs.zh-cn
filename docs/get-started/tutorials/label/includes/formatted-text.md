---
ms.openlocfilehash: 841dac9486097e27923ccfe582803b4ec50371cf
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "60896651"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在 MainPage.xaml 中，修改 [`Label`](xref:Xamarin.Forms.Label) 声明，显示在一个 `Label` 中使用多种格式的文本。

    ```xaml
    <Label TextColor="Gray"
           FontSize="Medium">
        <Label.FormattedText>
            <FormattedString>
                <Span Text="This sentence contains " />
                <Span Text="words that are emphasized, "
                      FontAttributes="Italic" />
                <Span Text="and underlined."
                      TextDecorations="Underline" />
            </FormattedString>
        </Label.FormattedText>
    </Label>
    ```

    此代码显示在一个 [`Label`](xref:Xamarin.Forms.Label) 中使用多种格式的文本。 第一个 [`Span`](xref:Xamarin.Forms.Span) 中的文本使用 `Label` 上设置的格式显示，而第二个和第三个 `Span` 实例中的文本使用 `Label` 上设置的格式和每个 `Span` 上额外设置的格式显示。

    > [!NOTE]
    > [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) 属性是 [`FormattedString`](xref:Xamarin.Forms.FormattedString) 类型，包含一个或多个 [`Span`](xref:Xamarin.Forms.Span) 实例。

1. 在 Visual Studio 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选远程 iOS 模拟器或 Android Emulator 内的应用程序。 请注意，[`Label`](xref:Xamarin.Forms.Label) 的外观已更改：

    [![iOS 和 Android 上显示格式化文本的标签屏幕截图](../images/label-formatted-text.png "带格式化文本的标签")](../images/label-formatted-text-large.png#lightbox "带格式化文本的标签")

    有关设置 [`Span`](xref:Xamarin.Forms.Span) 外观的详细信息，请参阅 [Xamarin.Forms 标签](~/xamarin-forms/user-interface/text/label.md)指南中的[格式化文本](~/xamarin-forms/user-interface/text/label.md#formatted-text)。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在 MainPage.xaml 中，修改 [`Label`](xref:Xamarin.Forms.Label) 声明，显示在一个 `Label` 中使用多种格式的文本。

    ```xaml
    <Label TextColor="Gray"
           FontSize="Medium">
        <Label.FormattedText>
            <FormattedString>
                <Span Text="This sentence contains " />
                <Span Text="words that are emphasized, "
                      FontAttributes="Italic" />
                <Span Text="and underlined."
                      TextDecorations="Underline" />
            </FormattedString>
        </Label.FormattedText>
    </Label>
    ```

    此代码显示在一个 [`Label`](xref:Xamarin.Forms.Label) 中使用多种格式的文本。 第一个 [`Span`](xref:Xamarin.Forms.Span) 中的文本使用 `Label` 上设置的格式显示，而第二个和第三个 `Span` 实例中的文本使用 `Label` 上设置的格式和每个 `Span` 上额外设置的格式显示。

    > [!NOTE]
    > [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) 属性是 [`FormattedString`](xref:Xamarin.Forms.FormattedString) 类型，包含一个或多个 [`Span`](xref:Xamarin.Forms.Span) 实例。

1. 在 Visual Studio for Mac 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选 iOS 模拟器或 Android Emulator 内的应用程序。 请注意，[`Label`](xref:Xamarin.Forms.Label) 的外观已更改：

    [![iOS 和 Android 上显示格式化文本的标签屏幕截图](../images/label-formatted-text.png "带格式化文本的标签")](../images/label-formatted-text-large.png#lightbox "带格式化文本的标签")

    有关设置 [`Span`](xref:Xamarin.Forms.Span) 外观的详细信息，请参阅 [Xamarin.Forms 标签](~/xamarin-forms/user-interface/text/label.md)指南中的[格式化文本](~/xamarin-forms/user-interface/text/label.md#formatted-text)。
