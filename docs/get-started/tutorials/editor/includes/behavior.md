---
ms.openlocfilehash: bd3f37082443e93f10f60d9466fe22aae8571b14
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "61373346"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在 MainPage.xaml 中，修改 [`Editor`](xref:Xamarin.Forms.Editor) 声明以自定义其行为：

    ```xaml
    <Editor Placeholder="Enter multi-line text here"
            AutoSize="TextChanges"
            MaxLength="200"
            IsSpellCheckEnabled="false"
            IsTextPredictionEnabled="false" />
    ```

    此代码设置用于自定义 [`Editor`](xref:Xamarin.Forms.Editor) 行为的属性。 将 [`AutoSize`](xref:Xamarin.Forms.Editor.AutoSize) 属性设置为 [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges)，这表示当 `Editor` 填充文本时其高度会增加，而当文本被删除时，其高度会降低。 [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) 属性限制 `Editor` 允许的输入长度。 此外，[`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) 属性设置为 `false` 以禁用拼写检查，而 `IsTextPredictionEnabled` 属性设置为 `false` 以禁用文本预测和自动文本预测。

1. 在 Visual Studio 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选远程 iOS 模拟器或 Android Emulator 内的应用程序。 在 [`Editor`](xref:Xamarin.Forms.Entry) 中输入文本并观察 `Editor` 的高度在填充文本时增加，并在删除文本时减少，且可以输入的最大字符数为 200：

    [![iOS 和 Android 上自动调整大小的编辑器屏幕截图](../images/customize-behavior.png "自动调整大小的编辑器")](../images/customize-behavior-large.png#lightbox "自动调整大小的编辑器")

    有关自定义 [`Editor`](xref:Xamarin.Forms.Editor) 行为的详细信息，请参阅 [Xamarin.Forms 编辑器](~/xamarin-forms/user-interface/text/editor.md)指南。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在 MainPage.xaml 中，修改 [`Editor`](xref:Xamarin.Forms.Editor) 声明以自定义其行为：

    ```xaml
    <Editor Placeholder="Enter multi-line text here"
            AutoSize="TextChanges"
            MaxLength="200"
            IsSpellCheckEnabled="false"
            IsTextPredictionEnabled="false" />
    ```

    此代码设置用于自定义 [`Editor`](xref:Xamarin.Forms.Editor) 行为的属性。 将 [`AutoSize`](xref:Xamarin.Forms.Editor.AutoSize) 属性设置为 [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges)，这表示当 `Editor` 填充文本时其高度会增加，而当文本被删除时，其高度会降低。 [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) 属性限制 `Editor` 允许的输入长度。 此外，[`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) 属性设置为 `false` 以禁用拼写检查，而 `IsTextPredictionEnabled` 属性设置为 `false` 以禁用文本预测和自动文本预测。

1. 在 Visual Studio for Mac 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选 iOS 模拟器或 Android Emulator 内的应用程序。 在 [`Editor`](xref:Xamarin.Forms.Entry) 中输入文本并观察 `Editor` 的高度在填充文本时增加，并在删除文本时减少，且可以输入的最大字符数为 200：

    [![iOS 和 Android 上自动调整大小的编辑器屏幕截图](../images/customize-behavior.png "自动调整大小的编辑器")](../images/customize-behavior-large.png#lightbox "自动调整大小的编辑器")

    有关自定义 [`Editor`](xref:Xamarin.Forms.Editor) 行为的详细信息，请参阅 [Xamarin.Forms 编辑器](~/xamarin-forms/user-interface/text/editor.md)指南。
