---
ms.openlocfilehash: d8f9445a0fc45c2700e8d9a901cfce9bd6307d2b
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75490670"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 在 MainPage.xaml 中，修改 [`Image`](xref:Xamarin.Forms.Image) 声明以自定义其外观  ：

    ```xaml
    <Image Source="http://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
           Aspect="Fill"
           HeightRequest="{OnPlatform iOS=300, Android=250}"
           WidthRequest="{OnPlatform iOS=300, Android=250}"
           HorizontalOptions="Center" />
    ```

    此代码将 [`Aspect`](xref:Xamarin.Forms.Image.Aspect) 属性设置为 [`Fill`](xref:Xamarin.Forms.Aspect.Fill)，该属性定义了图像的缩放模式。 `Fill` 成员在 [`Aspect`](xref:Xamarin.Forms.Aspect) 枚举中定义，并拉伸图像以完全填充视图，而不管图像是否扭曲。 有关图像缩放的详细信息，请参阅 [Xamarin.Forms 中的图像](~/xamarin-forms/user-interface/images.md)指南中的[显示图像](~/xamarin-forms/user-interface/images.md#display-images)。

    通过 `OnPlatform` 标记扩展可基于每个平台自定义 UI 外观。 在此示例中，标记扩展程序用于将 [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 和 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 属性设置为 iOS 上的 300 个与设备无关的单元，以及 Android 上的 250 个与设备无关的单元。 有关 `OnPlatform` 标记扩展的详细信息，请参阅[使用 XAML 标记扩展](~/xamarin-forms/xaml/markup-extensions/consuming.md)指南中的 [OnPlatform 标记扩展](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform)。

    此外，[`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 属性指定图像将水平居中。

1. 在 Visual Studio 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选远程 iOS 模拟器或 Android Emulator 内的应用程序  ：

    [![iOS 和 Android 上不同尺寸的图像的屏幕截图](../images/customize-appearance.png "基于每个平台的图像尺寸")](../images/customize-appearance-large.png#lightbox "基于每个平台的图像尺寸")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 在 MainPage.xaml 中，修改 [`Image`](xref:Xamarin.Forms.Image) 声明以自定义其外观  ：

    ```xaml
    <Image Source="http://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
           Aspect="Fill"
           HeightRequest="{OnPlatform iOS=300, Android=250}"
           WidthRequest="{OnPlatform iOS=300, Android=250}"
           HorizontalOptions="Center" />
    ```

    此代码将 [`Aspect`](xref:Xamarin.Forms.Image.Aspect) 属性设置为 [`Fill`](xref:Xamarin.Forms.Aspect.Fill)，该属性定义了图像的缩放模式。 `Fill` 成员在 [`Aspect`](xref:Xamarin.Forms.Aspect) 枚举中定义，并拉伸图像以完全填充视图，而不管图像是否扭曲。 有关图像缩放的详细信息，请参阅 [Xamarin.Forms 中的图像](~/xamarin-forms/user-interface/images.md)指南中的[显示图像](~/xamarin-forms/user-interface/images.md#display-images)。

    通过 `OnPlatform` 标记扩展可基于每个平台自定义 UI 外观。 在此示例中，标记扩展程序用于在 iOS 上将 [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 和 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 属性设置为 300，在 Android 上设置为 250。 有关 `OnPlatform` 标记扩展的详细信息，请参阅[使用 XAML 标记扩展](~/xamarin-forms/xaml/markup-extensions/consuming.md)指南中的 [OnPlatform 标记扩展](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform)。

    此外，[`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 属性指定图像将水平居中。

1. 在 Visual Studio for Mac 工具栏中，按“开始”按钮（类似“播放”按钮的三角形按钮），启动所选 iOS 模拟器或 Android 模拟器内的应用程序  ：

    [![iOS 和 Android 上不同尺寸的图像的屏幕截图](../images/customize-appearance.png "基于每个平台的图像尺寸")](../images/customize-appearance-large.png#lightbox "基于每个平台的图像尺寸")
