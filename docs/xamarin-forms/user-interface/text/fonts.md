---
title: 在 Xamarin.Forms 中的字体
description: 本文介绍如何在 Xamarin.Forms 应用程序中显示文本的控件上指定字体信息。
ms.prod: xamarin
ms.assetid: 49DD2249-C575-41AE-AE06-08F890FD6031
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/28/2019
ms.openlocfilehash: de77be818abbe1250946ee2ce1599235b79d8c01
ms.sourcegitcommit: 0fd04ea3af7d6a6d6086525306523a5296eec0df
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2019
ms.locfileid: "67512974"
---
# <a name="fonts-in-xamarinforms"></a>在 Xamarin.Forms 中的字体

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFonts/)

本文介绍了 Xamarin.Forms 如何允许您指定的字体特性 （包括权重和大小） 上显示文本的控件。 字体信息可以是[在代码中指定](#Setting_Font_in_Code)或[在 XAML 中指定](#Setting_Font_in_Xaml)。 它具有还可以使用[自定义字体](#Using_a_Custom_Font)，并[显示字体图标](#display-font-icons)。

<a name="Setting_Font_in_Code" />

## <a name="set-the-font-in-code"></a>在代码中设置该字体

使用任何控件的显示文本的三个与字体相关的属性：

- **FontFamily** &ndash; `string`字体名称。
- **FontSize** &ndash;形式的字体大小`double`。
- **FontAttributes** &ndash;一个字符串，指定样式信息，如*斜体*并**加粗**(使用`FontAttributes`C# 中的枚举)。

此代码演示如何创建一个标签，并指定的字体大小和权重来显示：

```csharp
var about = new Label {
    FontSize = Device.GetNamedSize (NamedSize.Medium, typeof(Label)),
    FontAttributes = FontAttributes.Bold,
    Text = "Medium Bold Font"
};
```

<a name="FontSize" />

### <a name="font-size"></a>字体大小

`FontSize`属性可以设置为双精度值，例如：

```csharp
label.FontSize = 24;
```

Xamarin.Forms 还定义中的字段[ `NamedSize` ](xref:Xamarin.Forms.NamedSize)枚举，用于表示特定字体大小。 有关命名的字体大小的详细信息，请参阅[名为字体大小](#named-font-sizes)。

<a name="FontAttributes" />

### <a name="font-attributes"></a>字体特性

字体样式等**粗体**并*斜体*可以设置`FontAttributes`属性。 目前支持以下值：

-  **无**
-  **加粗**
-  **斜体**

`FontAttribute`可以按如下所示使用枚举 (您可以指定单个属性或`OR`它们组合在一起):

```csharp
label.FontAttributes = FontAttributes.Bold | FontAttributes.Italic;
```

### <a name="set-font-info-per-platform"></a>每个平台的设置字体信息

或者，`Device.RuntimePlatform`属性可以用于设置不同的字体名称在每个平台上，此代码中所示：

```csharp
label.FontFamily = Device.RuntimePlatform == Device.iOS ? "Lobster-Regular" :
   Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : "Assets/Fonts/Lobster-Regular.ttf#Lobster",
label.FontSize = Device.RuntimePlatform == Device.iOS ? 24 :
   Device.RuntimePlatform == Device.Android ? Device.GetNamedSize(NamedSize.Medium, label) : Device.GetNamedSize(NamedSize.Large, label);
```

是一个很好的适用于 iOS 的字体信息的来源[iosfonts.com](http://iosfonts.com)。

<a name="Setting_Font_in_Xaml" />

## <a name="set-the-font-in-xaml"></a>在 XAML 中设置该字体

Xamarin.Forms 控制所有具有该显示文本`FontSize`可以在 XAML 中设置的属性。 在 XAML 中设置该字体的最简单方法是使用命名的大小的枚举值，在此示例中所示：

```xaml
<Label Text="Login" FontSize="Large"/>
<Label Text="Instructions" FontSize="Small"/>
```

没有内置的转换器，用于`FontSize`允许所有字体设置表示为 XAML 中的字符串值的属性。 此外，`FontAttributes`属性可用于指定的字体特性：

```xaml
<Label Text="Italics are supported" FontAttributes="Italic" />
<Label Text="Biggest NamedSize" FontSize="Large" />
<Label Text="Use size 72" FontSize="72" />
```

[`Device.RuntimePlatform`](~/xamarin-forms/platform/device.md#providing-platform-specific-values) 此外可在 XAML 中呈现每个平台上不同的字体。 下面的示例在 iOS 上使用自定义字体 (<span style="font-family:MarkerFelt-Thin">MarkerFelt 精简</span>)，并在其他平台上指定仅大小/属性：

```xaml
<Label Text="Hello Forms with XAML">
    <Label.FontFamily>
        <OnPlatform x:TypeArguments="x:String">
                <On Platform="iOS" Value="MarkerFelt-Thin" />
                <On Platform="Android" Value="Lobster-Regular.ttf#Lobster-Regular" />
                <On Platform="UWP" Value="Assets/Fonts/Lobster-Regular.ttf#Lobster" />
        </OnPlatform>
    </Label.FontFamily>
</Label>
```

指定自定义字体，它是始终使用一个好办法`OnPlatform`，因为很难找到可在所有平台的字体。

## <a name="named-font-sizes"></a>命名的字体大小

Xamarin.Forms 定义中的字段[ `NamedSize` ](xref:Xamarin.Forms.NamedSize)枚举，用于表示特定字体大小。 下表显示`NamedSize`成员，并在 iOS、 Android 和通用 Windows 平台 (UWP) 上的其默认大小：

| 成员 | iOS | Android | UWP |
| --- | --- | --- | --- |
| `Default` | 16 | 14 | 14 |
| `Micro` | 11 | 10 | 15.667 |
| `Small` | 13 | 14 | 18.667 |
| `Medium` | 16 | 17 | 22.667 |
| `Large` | 20 | 22 | 32 |
| `Body` | 17 | 16 | 14 |
| `Header` | 17 | 96 | 46 |
| `Title` | 28 | 24 | 24 |
| `Subtitle` | 22 | 16 | 20 |
| `Caption` | 12 | 12 | 12 |

可通过 XAML 和代码设置命名的字体大小。 此外，`Device.GetNamedSize`可以调用方法以返回`double`表示命名的字体大小：

```csharp
label.FontSize = Device.GetNamedSize(NamedSize.Small, typeof(Label));
```

> [!NOTE]
> 在 iOS 和 Android 上，命名的字体大小将根据操作系统可访问性选项进行自动缩放。 可以使用平台特定的 iOS 上禁用此行为。 有关详细信息，请参阅[辅助功能针对在 iOS 上名为字体大小进行扩展](~/xamarin-forms/platform/ios/named-font-size-scaling.md)。

<a name="Using_a_Custom_Font" />

## <a name="use-a-custom-font"></a>使用自定义字体

使用非内置字样的字体，则需要一些特定于平台的编码。 此屏幕截图显示自定义字体**Lobster**从[Google 的开放源代码字体](https://www.google.com/fonts)呈现使用 Xamarin.Forms。

 [![IOS 和 Android 上的自定义字体](fonts-images/custom-sml.png "自定义字体示例")](fonts-images/custom.png#lightbox "自定义字体示例")

为每个平台所需的步骤如下所述。 包含与应用程序的自定义字体文件时，请务必验证字体的许可证进行分发。

### <a name="ios"></a>iOS

可以通过首先确保将加载它，然后使用 Xamarin.Forms 按名称引用它来显示自定义字体`Font`方法。
按照中的说明[这篇博客文章](https://blog.xamarin.com/custom-fonts-in-ios/):

1. 添加字体文件**生成操作：BundleResource**，和
2. 更新**Info.plist**文件 (**提供的应用程序字体**，或`UIAppFonts`、 密钥)，然后
3. 它按名称引用任何在 Xamarin.Forms 中定义一种字体位置 ！

```csharp
new Label
{
    Text = "Hello, Forms!",
    FontFamily = Device.RuntimePlatform == Device.iOS ? "Lobster-Regular" : null // set only for iOS
}
```

### <a name="android"></a>Android

适用于 Android 的 Xamarin.Forms 可以引用按照特定的命名标准添加到项目的自定义字体。 首先添加字体文件的**资产**文件夹中的应用程序项目并设置*生成操作：AndroidAsset*。 然后，使用的完整路径和*字体名称*，作为在 Xamarin.Forms 中，字体名称的哈希 （#） 分隔，如以下代码段演示了：

```csharp
new Label
{
  Text = "Hello, Forms!",
  FontFamily = Device.RuntimePlatform == Device.Android ? "Lobster-Regular.ttf#Lobster-Regular" : null // set only for Android
}
```

### <a name="windows"></a>Windows

适用于 Windows 平台的 Xamarin.Forms 可以引用按照特定的命名标准添加到项目的自定义字体。 首先添加字体文件的 **/Assets 字体/** 文件夹中的应用程序项目并设置<span class="UIItem">生成操作： 内容</span>。 然后，使用的完整路径和字体文件名后, 跟哈希 （#） 和<span class="UIItem">字体名称</span>，如以下代码段所示：

```csharp
new Label
{
    Text = "Hello, Forms!",
    FontFamily = Device.RuntimePlatform == Device.UWP ? "Assets/Fonts/Lobster-Regular.ttf#Lobster" : null // set only for UWP apps
}
```

> [!NOTE]
> 请注意，但使用的字体文件名称和字体名称可能不同。 若要发现 Windows 上的字体名称，右键单击.ttf 文件，然后选择**预览版**。 然后可以从预览窗口中确定的字体名称。

此时完成了应用程序的常用代码。 此时，可将特定于平台的电话拨号程序实现为 [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md)。

### <a name="xaml"></a>XAML

此外可以使用[ `Device.RuntimePlatform` ](~/xamarin-forms/platform/device.md#interact-with-the-ui-from-background-threads)中 XAML 呈现自定义字体：

```xaml
<Label Text="Hello Forms with XAML">
    <Label.FontFamily>
        <OnPlatform x:TypeArguments="x:String">
                <On Platform="iOS" Value="Lobster-Regular" />
                <On Platform="Android" Value="Lobster-Regular.ttf#Lobster-Regular" />
                <On Platform="UWP" Value="Assets/Fonts/Lobster-Regular.ttf#Lobster" />
        </OnPlatform>
    </Label.FontFamily>
</Label>
```

## <a name="display-font-icons"></a>显示字体图标

字体图标可显示的 Xamarin.Forms 应用程序通过指定中的字体图标数据`FontImageSource`对象。 此类，该类派生自[ `ImageSource` ](xref:Xamarin.Forms.ImageSource)类中，具有以下属性：

- `Glyph` -字体图标，指定为 unicode 字符值`string`。
- `Size` –`double`值，该值指示的大小，以设备无关的单位，在呈现字体的字号图标。 默认值为 30。
- `FontFamily` –`string`表示字体图标所属的字体系列。
- `Color` – 一个可选[ `Color` ](xref:Xamarin.Forms.Color)显示字体图标时要使用的值。

此数据用于创建可以显示任何视图可以显示 PNG `ImageSource`。 此方法使字体图标，如表情符号，要显示的多个视图，而不是限制到单个文本显示视图，如字体图标显示[ `Label` ](xref:Xamarin.Forms.Label)。

> [!IMPORTANT]
> 当前仅可通过其 unicode 字符表示形式指定字体的图标。

下面的 XAML 示例已显示的单个字体图标[ `Image` ](xref:Xamarin.Forms.Image)视图：

```xaml
<Image BackgroundColor="#D1D1D1">
    <Image.Source>
        <FontImageSource Glyph="&#xf30c;"
                         FontFamily="{OnPlatform iOS=Ionicons, Android=ionicons.ttf#}"
                         Size="44" />
    </Image.Source>
</Image>
```

此代码中显示一个 XBox 图标，Ionicons 字体系列，从[ `Image` ](xref:Xamarin.Forms.Image)视图。 请注意，尽管 unicode 字符是此图标`\uf30c`，它必须在 XAML 中进行转义，因此将变为`&#xf30c;`。 等效 C# 代码如下：

```csharp
Image image = new Image { BackgroundColor = Color.FromHex("#D1D1D1") };
image.Source = new FontImageSource
{
    Glyph = "\uf30c",
    FontFamily = Device.RuntimePlatform == Device.iOS ? "Ionicons" : "ionicons.ttf#",
    Size = 44
};
```

下面的屏幕截图，从[可绑定布局](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BindableLayouts/)示例，显示多个字体图标显示的可绑定的布局：

![字体图标显示的、 iOS 和 Android 上的屏幕截图](fonts-images/font-image-source.png "图像视图中显示的字体图标")

## <a name="related-links"></a>相关链接

- [FontsSample](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithFonts/)
- [文本 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text/)
- [可绑定的布局 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BindableLayouts/)
- [可绑定的布局](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)
