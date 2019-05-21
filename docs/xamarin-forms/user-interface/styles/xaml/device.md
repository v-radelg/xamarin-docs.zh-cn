---
title: 在 Xamarin.Forms 中的设备样式
description: Xamarin.Forms 具有六个动态样式，称为设备样式，Device.Styles 类中。 本文介绍如何使用 Xamarin.Forms 应用程序中的设备样式。
ms.prod: xamarin
ms.assetid: 7FF19ED1-0822-4238-9435-AD970317A2F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: 05e8fb9ef26f2a2a0b812b25853e4f61e658fa46
ms.sourcegitcommit: 482aef652bdaa440561252b6a1a1c0a40583cd32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2019
ms.locfileid: "65970564"
---
# <a name="device-styles-in-xamarinforms"></a>在 Xamarin.Forms 中的设备样式

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/DynamicStyles/)

_Xamarin.Forms 具有六个动态样式，称为设备样式，Device.Styles 类中。_

*设备*样式：

- [`BodyStyle`](xref:Xamarin.Forms.Device.Styles.BodyStyle)
- [`CaptionStyle`](xref:Xamarin.Forms.Device.Styles.CaptionStyle)
- [`ListItemDetailTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemDetailTextStyle)
- [`ListItemTextStyle`](xref:Xamarin.Forms.Device.Styles.ListItemTextStyle)
- [`SubtitleStyle`](xref:Xamarin.Forms.Device.Styles.SubtitleStyle)
- [`TitleStyle`](xref:Xamarin.Forms.Device.Styles.TitleStyle)

所有六个样式仅应用于[ `Label` ](xref:Xamarin.Forms.Label)实例。 例如， `Label` ，显示一个段落的主体可能会设置其[ `Style` ](xref:Xamarin.Forms.NavigableElement.Style)属性设置为[ `BodyStyle` ](xref:Xamarin.Forms.Device.Styles.BodyStyle)。

下面的代码示例演示了如何使用*设备*XAML 页面中的样式：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.DeviceStylesPage" Title="Device" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="myBodyStyle" TargetType="Label"
              BaseResourceKey="BodyStyle">
                <Setter Property="TextColor" Value="Accent" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Label Text="Title style"
              Style="{DynamicResource TitleStyle}" />
            <Label Text="Subtitle text style"
              Style="{DynamicResource SubtitleStyle}" />
            <Label Text="Body style"
              Style="{DynamicResource BodyStyle}" />
            <Label Text="Caption style"
              Style="{DynamicResource CaptionStyle}" />
            <Label Text="List item detail text style"
              Style="{DynamicResource ListItemDetailTextStyle}" />
            <Label Text="List item text style"
              Style="{DynamicResource ListItemTextStyle}" />
            <Label Text="No style" />
            <Label Text="My body style"
              Style="{StaticResource myBodyStyle}" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

设备样式绑定到使用`DynamicResource`标记扩展。 可以通过更改在 iOS 中看到样式的动态特性**可访问性**设置文本大小。 外观*设备*样式是每个平台上不同，如以下屏幕截图中所示：

![](device-images/device-styles.png "每个平台上设备样式")

*设备*样式也从通过设置派生[ `BaseResourceKey` ](xref:Xamarin.Forms.Style.BaseResourceKey)设备样式的键名称的属性。 在上面的代码示例`myBodyStyle`继承自[ `BodyStyle` ](xref:Xamarin.Forms.Device.Styles.BodyStyle)将带重音符的文本颜色设置。 有关动态样式继承的详细信息，请参阅[动态样式继承](~/xamarin-forms/user-interface/styles/xaml/dynamic.md#dynamic-style-inheritance)。

下面的代码示例演示如何在 C# 中的等效页：

```csharp
public class DeviceStylesPageCS : ContentPage
{
    public DeviceStylesPageCS ()
    {
        var myBodyStyle = new Style (typeof(Label)) {
            BaseResourceKey = Device.Styles.BodyStyleKey,
            Setters = {
                new Setter {
                    Property = Label.TextColorProperty,
                    Value = Color.Accent
                }
            }
        };

        Title = "Device";
        IconImageSource = "csharp.png";
        Padding = new Thickness (0, 20, 0, 0);

        Content = new StackLayout {
            Children = {
                new Label { Text = "Title style", Style = Device.Styles.TitleStyle },
                new Label { Text = "Subtitle style", Style = Device.Styles.SubtitleStyle },
                new Label { Text = "Body style", Style = Device.Styles.BodyStyle },
                new Label { Text = "Caption style", Style = Device.Styles.CaptionStyle },
                new Label { Text = "List item detail text style",
                  Style = Device.Styles.ListItemDetailTextStyle },
                new Label { Text = "List item text style", Style = Device.Styles.ListItemTextStyle },
                new Label { Text = "No style" },
                new Label { Text = "My body style", Style = myBodyStyle }
            }
        };
    }
}
```

[ `Style` ](xref:Xamarin.Forms.NavigableElement.Style)每个属性[ `Label` ](xref:Xamarin.Forms.Label)设置为从适当的属性实例[ `Devices.Styles` ](xref:Xamarin.Forms.Device.Styles)类。

## <a name="accessibility"></a>可访问性

*设备*样式尊重辅助工具首选项，因此字体大小将更改为可访问性首选项更改每个平台上。 因此，若要支持辅助功能的文本，确保*设备*样式用作应用程序中的任何文本样式的基础。

下面的屏幕截图演示了如何在每个平台上，使用最小的可访问的字体大小的设备样式：

[![](device-images/minimum-size.png "每个平台上的可访问的小型设备样式")](device-images/minimum-size-large.png#lightbox "每个平台上的可访问的小型设备样式")

下面的屏幕截图演示了如何在每个平台上，使用最大的可访问的字体大小的设备样式：

![](device-images/maximum-size.png "每个平台上的可访问的大型设备样式")

## <a name="related-links"></a>相关链接

- [文本样式](~/xamarin-forms/user-interface/text/styles.md)
- [XAML 标记扩展](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [动态样式 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/DynamicStyles/)
- [使用样式 （示例）](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [Device.Styles](xref:Xamarin.Forms.Device.Styles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [样式](xref:Xamarin.Forms.Style)
- [资源库](xref:Xamarin.Forms.Setter)
