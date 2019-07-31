---
title: Xamarin.Forms AbsoluteLayout
description: 本文介绍如何使用 Xamarin.Forms AbsoluteLayout 类来创建完全适合像素的用户界面。 此类定位并调整其自身的大小和位置或绝对值成比例的子元素的大小。
ms.prod: xamarin
ms.assetid: 01A5CCE0-AD45-4806-84FD-72C007005B38
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/25/2015
ms.openlocfilehash: 416106f9948c1757f398589e730e01ab55dd01a9
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642984"
---
# <a name="xamarinforms-absolutelayout"></a>Xamarin.Forms AbsoluteLayout

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) 定位并调整其自身的大小和位置或绝对值成比例的子元素的大小。 子视图可能定位和调整大小成比例的值或静态值，使用和成比例，可以混合使用静态值。

[![](absolute-layout-images/layouts-sml.png "Xamarin.Forms 布局")](absolute-layout-images/layouts.png#lightbox "Xamarin.Forms 布局")

本文将介绍：

- **[目的](#Purpose)** &ndash;的常见用途`AbsoluteLayout`。
- **[使用情况](#Usage)** &ndash;如何使用`AbsoluteLayout`来实现您所需的设计。
  - **[按比例的布局](#Proportional_Layouts)** &ndash;了解如何按比例值即可在`AbsoluteLayout`。
  - **[指定值](#Specifying_Values)** &ndash;了解如何指定比例和绝对值。
  - **[按比例值](#Proportional_Values)** &ndash;了解如何按比例值起作用。
    - **[绝对值](#Absolute_Values)** &ndash;了解绝对值的工作原理。

<a name="Purpose" />

## <a name="purpose"></a>目标

由于定位模型的`AbsoluteLayout`，布局可以相对简单，来定位元素，以便它们与布局的任意一侧对齐或居中。 按比例大小和位置中的元素与`AbsoluteLayout`可以自动扩展到任何视图大小。 对于仅的位置，但不是大小应缩放的项，可以混合绝对和比例值。

`AbsoluteLayout` 可以使用任何位置元素需要视图中定位和对齐到边缘的元素时尤其有用。

<a name="Usage" />

## <a name="usage"></a>用法

<a name="Proportional_Layouts" />

### <a name="proportional-layouts"></a>按比例的布局

`AbsoluteLayout` 具有唯一的定位点模型，由此元素的定位点位于相对于其元素按比例定位使用时，相对于布局放置的元素。 使用绝对定位时，定位点为 (0，0) 的视图中。 这样做有两个重要的结果：

- 元素不能定位关闭屏幕使用比例值。
- 元素可进行可靠地定位沿任意一侧的布局或中心，而不考虑设备的布局的大小。

`AbsoluteLayout`如`RelativeLayout`，可以定位元素，使它们相互重叠。

请注意，在以下屏幕截图中，定位点的框为白色点。 布局中移动，请注意定位点与 box 之间的关系：

![](absolute-layout-images/anchor-start.png "在起始定位点")
![](absolute-layout-images/anchor-center.png "定位点中心")
![](absolute-layout-images/anchor-end.png "末尾的定位点")

<a name="Specifying_Values" />

### <a name="specifying-values"></a>指定值

中的视图`AbsoluteLayout`位于使用四个值：

- **X** &ndash;视图的锚点的 x （水平） 位置
- **Y** &ndash;视图的锚点的 y （垂直） 位置
- **宽度**&ndash;视图的宽度
- **高度**&ndash;视图的高度

每个这些值可设置为[比例](#Proportional_Values)值或[绝对](#Absolute_Values)值。

值指定为边界和标志的组合。 `LayoutBounds` 是[ `Rectangle` ](xref:Xamarin.Forms.Rectangle)包含以下四个值： `x`， `y`， `width`， `height`。

### <a name="absolutelayoutflags"></a>AbsoluteLayoutFlags

[`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags) 指定将如何解释值并提供以下预定义的选项：

- **无**&ndash;将所有值都解释为绝对值。 如果不指定了任何布局标志，这是默认值。
- **所有**&ndash;将解释为比例的所有值。
- **WidthProportional** &ndash;解释`Width`值作为比例和所有其他值为绝对值。
- **HeightProportional** &ndash;仅高度会将该值解释为比例绝对所有其他值。
- **XProportional** &ndash;解释`X`值作为成比例，同时将所有其他值视为绝对值。
- **YProportional** &ndash;解释`Y`值作为成比例，同时将所有其他值视为绝对值。
- **PositionProportional** &ndash;解释`X`和`Y`值作为成比例，而大小值解释为绝对值。
- **SizeProportional** &ndash;解释`Width`和`Height`绝对位置值时根据成比例的值。

在 XAML，边界和标志设置为布局中的视图定义的一部分使用`AbsoluteLayout.LayoutBounds`属性。 边界设置为一列以逗号分隔的值， `X`， `Y`， `Width`，和`Height`按顺序。 在布局中使用的视图的声明中还指定标志`AbsoluteLayout.LayoutFlags`属性。 请注意，可以在 XAML 使用逗号分隔的列表中组合标志。 请看下面的示例：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="LayoutSamples.AbsoluteLayoutExploration"
Title="Absolute Layout Exploration">
    <ContentPage.Content>
        <AbsoluteLayout>
            <Label Text="I'm centered on iPhone 4 but no other device"
                AbsoluteLayout.LayoutBounds="115,150,100,100" LineBreakMode="WordWrap"  />
            <Label Text="I'm bottom center on every device."
                AbsoluteLayout.LayoutBounds=".5,1,.5,.1" AbsoluteLayout.LayoutFlags="All"
                LineBreakMode="WordWrap"  />
            <BoxView Color="Olive"  AbsoluteLayout.LayoutBounds="1,.5, 25, 100"
                AbsoluteLayout.LayoutFlags="PositionProportional" />
            <BoxView Color="Red" AbsoluteLayout.LayoutBounds="0,.5,25,100"
                AbsoluteLayout.LayoutFlags="PositionProportional" />
            <BoxView Color="Blue" AbsoluteLayout.LayoutBounds=".5,0,100,25"
                AbsoluteLayout.LayoutFlags="PositionProportional" />
        </AbsoluteLayout>
    </ContentPage.Content>
</ContentPage>
```

![](absolute-layout-images/exploration.png "AbsoluteLayout 示例")

请注意以下事项：

- 在中心标签将置于使用绝对大小和位置的值。 正因为如此，它会显示在 iPhone 4 秒上居中和较低，但不是在较大的设备上居中。
- 在布局的底部文本位于使用比例大小和位置的值。 它将始终显示在底部中心的布局，但其大小将增长的布局越大。
- 三个彩色`BoxView`s 定位在使用按比例的位置和绝对大小在屏幕上、 左和右边缘。

以下可实现在 C# 中相同的布局：

```csharp
public class AbsoluteLayoutExplorationCode : ContentPage
{
    public AbsoluteLayoutExplorationCode ()
    {
        Title = "Absolute Layout Exploration - Code";
        var layout = new AbsoluteLayout();

        var centerLabel = new Label {
        Text = "I'm centered on iPhone 4 but no other device.",
        LineBreakMode = LineBreakMode.WordWrap};

        AbsoluteLayout.SetLayoutBounds (centerLabel, new Rectangle (115, 159, 100, 100));
        // No need to set layout flags, absolute positioning is the default

        var bottomLabel = new Label { Text = "I'm bottom center on every device.", LineBreakMode = LineBreakMode.WordWrap };
        AbsoluteLayout.SetLayoutBounds (bottomLabel, new Rectangle (.5, 1, .5, .1));
        AbsoluteLayout.SetLayoutFlags (bottomLabel, AbsoluteLayoutFlags.All);

        var rightBox = new BoxView{ Color = Color.Olive };
        AbsoluteLayout.SetLayoutBounds (rightBox, new Rectangle (1, .5, 25, 100));
        AbsoluteLayout.SetLayoutFlags (rightBox, AbsoluteLayoutFlags.PositionProportional);

        var leftBox = new BoxView{ Color = Color.Red };
        AbsoluteLayout.SetLayoutBounds (leftBox, new Rectangle (0, .5, 25, 100));
        AbsoluteLayout.SetLayoutFlags (leftBox, AbsoluteLayoutFlags.PositionProportional);

        var topBox = new BoxView{ Color = Color.Blue };
        AbsoluteLayout.SetLayoutBounds (topBox, new Rectangle (.5, 0, 100, 25));
        AbsoluteLayout.SetLayoutFlags (topBox, AbsoluteLayoutFlags.PositionProportional);

        layout.Children.Add (bottomLabel);
        layout.Children.Add (centerLabel);
        layout.Children.Add (rightBox);
        layout.Children.Add (leftBox);
        layout.Children.Add (topBox);

        Content = layout;
    }
}
```
<a name="Proportional_Values" />

### <a name="proportional-values"></a>按比例值

按比例值定义布局和视图之间的关系。 此关系定义为父布局的相应值的一定比例子视图的位置或缩放值。 这些值表示为`double`与 0 和 1 之间的值。

位置和大小视图布局中的，将使用按比例值。 因此，当视图的宽度设置为一定比例，生成的宽度值是乘以比例`AbsoluteLayout`的宽度。 例如，对于`AbsoluteLayout`宽度的`500`并且将 250 (500 x.5 被设置为.5，该视图的呈现宽度的比例宽度的视图。

若要使用按比例的值，请设置`LayoutBounds`使用 （x，y） 比例和成比例的大小，然后设置`LayoutFlags`到`All`。

在 XAML:

```xaml
<Label Text="I'm bottom center on every device."
  AbsoluteLayout.LayoutBounds=".5,1,.5,.1" AbsoluteLayout.LayoutFlags="All"  />
```

在 C# 中：

```csharp
var label = new Label {Text = "I'm bottom center on every device."};
AbsoluteLayout.SetLayoutBounds(label, new Rectangle(.5,1,.5,.1));
AbsoluteLayout.SetLayoutFlags(label, AbsoluteLayoutFlags.All);
```

<a name="Absolute_Values" />

### <a name="absolute-values"></a>绝对值

绝对值显式定义视图布局中的放置位置。 按比例值与绝对值是能够定位和调整大小不适合布局的边界内的视图。

布局的大小未知时，用于定位使用绝对值可能很危险。 在使用绝对位置，在一个大小的屏幕的中心中的元素无法在以任何其他大小偏移量。 请务必跨不同屏幕大小的受支持设备测试您的应用程序。

若要使用绝对布局值，请设置`LayoutBounds`使用 （x，y） 坐标和显式大小，然后设置`LayoutFlags`到`None`。

在 XAML:

```xaml
<Label Text="I'm centered on iPhone 4 but no other device."
  AbsoluteLayout.LayoutBounds="115,150,100,100"  />
```

在 C# 中：

```csharp
var label = new Label {Text = "I'm centered on iPhone 4 but no other device."};
AbsoluteLayout.SetLayoutBounds(label, new Rectangle(115,150,100,100));
```

## <a name="exploring-a-complex-layout"></a>浏览复杂的布局

每个布局具有的优势和劣势为特定布局的创建。 在本系列的布局文章，整个示例应用程序已创建具有相同的页面布局使用三个不同的布局实现。

请考虑以下 XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TheBusinessTumble.AbsoluteLayoutPage"
Title="AbsoluteLayout">
    <ContentPage.ToolbarItems>
        <ToolbarItem Text="Save" />
    </ContentPage.ToolbarItems>
    <ContentPage.Content>
        <ScrollView>
            <AbsoluteLayout BackgroundColor="Maroon">
                <BoxView BackgroundColor="Gray" AbsoluteLayout.LayoutBounds="0
                    0,1,100" AbsoluteLayout.LayoutFlags="XProportional,YProportional,WidthProportional" />
                <Button BackgroundColor="Maroon"
                    AbsoluteLayout.LayoutBounds=".5,55,70,70" AbsoluteLayout.LayoutFlags="XProportional" BorderRadius="35" />
                <Button BackgroundColor="Red" AbsoluteLayout.LayoutBounds=".5
                    60,60,60" AbsoluteLayout.LayoutFlags="XProportional" BorderRadius="30" />
                <Label Text="User Name" FontAttributes="Bold" FontSize="26"
                    TextColor="Black" HorizontalTextAlignment="Center" AbsoluteLayout.LayoutBounds=".5,140,1,40" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                <Entry Text="Bio + Hashtags" TextColor="White"
                    BackgroundColor="Maroon" AbsoluteLayout.LayoutBounds=".5,180,1,40" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                <AbsoluteLayout BackgroundColor="White"
                        AbsoluteLayout.LayoutBounds="0, 220, 1, 50" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional">
                    <AbsoluteLayout AbsoluteLayout.LayoutBounds="0,0,.5,1" AbsoluteLayout.LayoutFlags="WidthProportional,HeightProportional">
                        <Button BackgroundColor="Black" BorderRadius="20"
                            AbsoluteLayout.LayoutBounds="5,.5,40,40" AbsoluteLayout.LayoutFlags="YProportional" />
                        <Label Text="Accent Color" TextColor="Black"
                            AbsoluteLayout.LayoutBounds="50,.55,1,25" AbsoluteLayout.LayoutFlags="YProportional,WidthProportional" />
                    </AbsoluteLayout>
                    <AbsoluteLayout AbsoluteLayout.LayoutBounds="1,0,.5,1" AbsoluteLayout.LayoutFlags="WidthProportional,HeightProportional,XProportional">
                        <Button BackgroundColor="Maroon" BorderRadius="20"
                            AbsoluteLayout.LayoutBounds="5,.5,40,40" AbsoluteLayout.LayoutFlags="YProportional" />
                        <Label Text="Primary Color" TextColor="Black"
                            AbsoluteLayout.LayoutBounds="50,.55,1,25" AbsoluteLayout.LayoutFlags="YProportional,WidthProportional" />
                    </AbsoluteLayout>
                </AbsoluteLayout>
                <AbsoluteLayout AbsoluteLayout.LayoutBounds="0,270,1,50" AbsoluteLayout.LayoutFlags="WidthProportional" Padding="5,0,0,0">
                    <Label Text="Age:" TextColor="White"
                        AbsoluteLayout.LayoutBounds="0,25,.25,50" AbsoluteLayout.LayoutFlags="WidthProportional" />
                    <Entry Text="35" TextColor="White" BackgroundColor="Maroon"
                        AbsoluteLayout.LayoutBounds="1,10,.75,50" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                </AbsoluteLayout>
                <AbsoluteLayout AbsoluteLayout.LayoutBounds="0,320,1,50" AbsoluteLayout.LayoutFlags="WidthProportional" Padding="5,0,0,0">
                    <Label Text="Interests:" TextColor="White"
                        AbsoluteLayout.LayoutBounds="0,25,.25,50" AbsoluteLayout.LayoutFlags="WidthProportional" />
                    <Entry Text="Xamarin.Forms" TextColor="White"
                        BackgroundColor="Maroon" AbsoluteLayout.LayoutBounds="1,10,.75,50" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                </AbsoluteLayout>
                <AbsoluteLayout AbsoluteLayout.LayoutBounds="0,370,1,50" AbsoluteLayout.LayoutFlags="WidthProportional" Padding="5,0,0,0">
                    <Label Text="Ask me about:" TextColor="White"
                        AbsoluteLayout.LayoutBounds="0,25,.25,50" AbsoluteLayout.LayoutFlags="WidthProportional" />
                    <Entry Text="Xamarin, C#, .NET, Mono" TextColor="White"
                        BackgroundColor="Maroon" AbsoluteLayout.LayoutBounds="1,10,.75,50" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                </AbsoluteLayout>
            </AbsoluteLayout>
        </ScrollView>
    </ContentPage.Content>
</ContentPage>
```

上面的代码会导致以下布局：

![](absolute-layout-images/abs.png "复杂 AbsoluteLayout")

请注意， `AbsoluteLayout`s 嵌套的因为在某些情况下嵌套布局可能会比提供相同的布局中的所有元素。

## <a name="related-links"></a>相关链接

- [借助 Xamarin.Forms，第 14 章创建移动应用](https://developer.xamarin.com/r/xamarin-forms/book/chapter14.pdf)
- [AbsoluteLayout](xref:Xamarin.Forms.AbsoluteLayout)
- [布局 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [BusinessTumble 示例 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
