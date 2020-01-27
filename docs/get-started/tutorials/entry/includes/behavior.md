---
ms.openlocfilehash: 48af50d31013f696879174a5cf108ab9fde92d0b
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "61343383"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在 MainPage.xaml 中，修改 [`Entry`](xref:Xamarin.Forms.Entry) 声明以自定义其行为：

    ```xaml
    <Entry Placeholder="Enter password"
           MaxLength="15"
           IsSpellCheckEnabled="false"
           IsTextPredictionEnabled="false"
           IsPassword="true" />
    ```

    此代码设置用于自定义 [`Entry`](xref:Xamarin.Forms.Entry) 行为的属性。 [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) 属性限制允许的 `Entry` 输入长度，将 [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) 属性设置为 `false` 以禁用拼写检查。 同样，将 [`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) 属性设置为 `false` 以禁用文本预测和自动文本预测。 此外，[`IsPassword`](xref:Xamarin.Forms.Entry.IsPassword) 属性确保使用密码字符（黑色圆圈）对输入的字符进行掩码。

    > [!NOTE]
    > 对于某些文本输入场景（例如输入密码），拼写检查和文本预测提供的体验并不太好，因此应禁用它们。

1. 在 Visual Studio 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选远程 iOS 模拟器或 Android Emulator 内的应用程序。 在 [`Entry`](xref:Xamarin.Forms.Entry) 中输入文本，观察每个字符是否被替换成密码掩码字符并且可输入的最大字符数是否为 15：

    [![iOS 和 Android 上文本被密码字符屏蔽的条目的屏幕截图](../images/customize-behavior.png "包含掩码密码字符的条目")](../images/customize-behavior-large.png#lightbox "包含掩码密码字符的条目")

    有关自定义 [`Entry`](xref:Xamarin.Forms.Entry) 行为的详细信息，请参阅 [Xamarin.Forms 输入](~/xamarin-forms/user-interface/text/entry.md)指南。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在 MainPage.xaml 中，修改 [`Entry`](xref:Xamarin.Forms.Entry) 声明以自定义其行为：

    ```xaml
    <Entry Placeholder="Enter password"
           MaxLength="15"
           IsSpellCheckEnabled="false"
           IsTextPredictionEnabled="false"
           IsPassword="true" />
    ```

    此代码设置用于自定义 [`Entry`](xref:Xamarin.Forms.Entry) 行为的属性。 [`MaxLength`](xref:Xamarin.Forms.InputView.MaxLength) 属性限制允许的 `Entry` 输入长度，将 [`IsSpellCheckEnabled`](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled) 属性设置为 `false` 以禁用拼写检查。 同样，将 [`IsTextPredictionEnabled`](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled) 属性设置为 `false` 以禁用文本预测和自动文本预测。 此外，[`IsPassword`](xref:Xamarin.Forms.Entry.IsPassword) 属性确保使用密码字符（黑色圆圈）对输入的字符进行掩码。

    > [!NOTE]
    > 对于某些文本输入场景（例如输入密码），拼写检查和文本预测提供的体验并不太好，因此应禁用它们。

1. 在 Visual Studio for Mac 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选 iOS 模拟器或 Android Emulator 内的应用程序。 在 [`Entry`](xref:Xamarin.Forms.Entry) 中输入文本，观察每个字符是否被替换成密码掩码字符并且可输入的最大字符数是否为 15：

    [![iOS 和 Android 上文本被密码字符屏蔽的文本的条目的屏幕截图](../images/customize-behavior.png "包含掩码密码字符的条目")](../images/customize-behavior-large.png#lightbox "包含掩码密码字符的条目")

    有关自定义 [`Entry`](xref:Xamarin.Forms.Entry) 行为的详细信息，请参阅 [Xamarin.Forms 输入](~/xamarin-forms/user-interface/text/entry.md)指南。
