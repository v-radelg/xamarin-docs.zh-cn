---
ms.openlocfilehash: a7c2aa15521b89e4930746dc5421ce67fa26b2b9
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61382488"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在 MainPage.xaml 中，修改 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 声明，以便将子元素水平对齐，而不是垂直对齐：

    ```xaml
    <StackLayout Margin="20,35,20,25"
                 Orientation="Horizontal">
        <Label Text="The StackLayout has its Margin property set, to control the rendering position of the StackLayout." />
        <Label Text="The Padding property can be set to specify the distance between the StackLayout and its children." />
        <Label Text="The Spacing property can be set to specify the distance between views in the StackLayout." />
    </StackLayout>
    ```

    此代码将 [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) 属性设置为 [`Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal)。

1. 在 Visual Studio 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选远程 iOS 模拟器或 Android Emulator 内的应用程序：

    [![iOS 和 Android 上 StackLayout 中水平方向的子元素视图的屏幕截图](../images/orientation.png "包含水平方向标签实例的 StackLayout")](../images/orientation-large.png#lightbox "StackLayout containing horizontally oriented Label instances")

    注意，[`StackLayout`](xref:Xamarin.Forms.StackLayout) 中的 [`Label`](xref:Xamarin.Forms.Label) 实例现在是水平对齐，而不是垂直对齐。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在 MainPage.xaml 中，修改 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 声明，以便将子元素水平对齐，而不是垂直对齐：

    ```xaml
    <StackLayout Margin="20,35,20,25"
                 Orientation="Horizontal">
        <Label Text="The StackLayout has its Margin property set, to control the rendering position of the StackLayout." />
        <Label Text="The Padding property can be set to specify the distance between the StackLayout and its children." />
        <Label Text="The Spacing property can be set to specify the distance between views in the StackLayout." />
    </StackLayout>
    ```

    此代码将 [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) 属性设置为 [`Horizontal`](xref:Xamarin.Forms.StackOrientation.Horizontal)。

1. 在 Visual Studio for Mac 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选 iOS 模拟器或 Android 模拟器内的应用程序：

    [![iOS 和 Android 上 StackLayout 中水平方向的子元素视图的屏幕截图](../images/orientation.png "包含水平方向标签实例的 StackLayout")](../images/orientation-large.png#lightbox "StackLayout containing horizontally oriented Label instances")

    注意，[`StackLayout`](xref:Xamarin.Forms.StackLayout) 中的 [`Label`](xref:Xamarin.Forms.Label) 实例现在是水平对齐，而不是垂直对齐。
