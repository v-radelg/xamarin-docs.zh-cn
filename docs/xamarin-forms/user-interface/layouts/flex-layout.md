---
title: Xamarin FlexLayout
description: 使用 FlexLayout 来堆积或包装子视图的集合。
ms.prod: xamarin
ms.assetid: 6A91EA70-268C-462C-AAAF-F8DA011403F8
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
ms.openlocfilehash: 187befd88c115133a92aa90a711438e7754518d5
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "68648805"
---
# <a name="the-xamarinforms-flexlayout"></a>Xamarin FlexLayout

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)

_使用 FlexLayout 来堆积或包装子视图的集合。_

Xamarin. Forms [`FlexLayout`](xref:Xamarin.Forms.FlexLayout)是 xamarin 版本3.0 中的新增版本。 它基于 "CSS[挠性方框布局" 模块](http://www.w3.org/TR/css-flexbox-1/)（通常称为 "_弹性布局_" 或 "_弹性_"），因为它包含多个灵活的选项来排列布局中的子级。

`FlexLayout` 类似于 Xamarin. 窗体[`StackLayout`](~/xamarin-forms/user-interface/layouts/stack-layout.md) ，它可以在堆栈中水平和垂直排列子窗体。 但是，如果在单个行或列中容纳太多项，则 `FlexLayout` 还可以包装其子级，还具有许多用于方向、对齐和适应各种屏幕大小的选项。

`FlexLayout` 派生自[`Layout<View>`](xref:Xamarin.Forms.Layout`1)并继承类型 `IList<View>` 的[`Children`](xref:Xamarin.Forms.Layout`1.Children)属性。

`FlexLayout` 定义了六个公共可绑定属性和5个附加的可绑定属性，这些属性影响其子元素的大小、方向和对齐方式。 （如果你不熟悉附加的可绑定属性，请参阅 **[附加属性](~/xamarin-forms/xaml/attached-properties.md)** 一文。）以下各节详细介绍了这些属性，详细介绍了可 **[绑定属性](#bindable-properties)** 和 **[附加的可绑定属性](#attached-properties)** 。 不过，本文首先介绍 `FlexLayout` 的一些 **[常见使用方案](#common-scenarios)** ，这些方案更通俗地说明了其中的许多属性。 到文章末尾，你将了解如何将 `FlexLayout` 与[CSS 样式表](~/xamarin-forms/user-interface/styles/css/index.md)结合。

<a name="common-scenarios" />

## <a name="common-usage-scenarios"></a>常见使用方案

**[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** 示例程序包含几个页面，用于演示 `FlexLayout` 的一些常见用法，并使你可以使用其属性进行试验。

### <a name="using-flexlayout-for-a-simple-stack"></a>将 FlexLayout 用于简单堆栈

"**简单堆栈**" 页显示 `FlexLayout` 可以如何替换 `StackLayout` 但标记更简单。 在 XAML 页中定义了此示例中的所有内容。 @No__t_0 包含四个子元素：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:FlexLayoutDemos"
             x:Class="FlexLayoutDemos.SimpleStackPage"
             Title="Simple Stack">

    <FlexLayout Direction="Column"
                AlignItems="Center"
                JustifyContent="SpaceEvenly">

        <Label Text="FlexLayout in Action"
               FontSize="Large" />

        <Image Source="{local:ImageResource FlexLayoutDemos.Images.SeatedMonkey.jpg}" />

        <Button Text="Do-Nothing Button" />

        <Label Text="Another Label" />
    </FlexLayout>
</ContentPage>
```

在 iOS、Android 和通用 Windows 平台上运行的页面如下：

[!["简单堆栈" 页](flex-layout-images/SimpleStack.png ""简单堆栈" 页")](flex-layout-images/SimpleStack-Large.png#lightbox)

**SimpleStackPage**文件中显示了 `FlexLayout` 的三个属性：

- [@No__t_1](xref:Xamarin.Forms.FlexLayout.Direction)属性设置为[`FlexDirection`](xref:Xamarin.Forms.FlexDirection)枚举的值。 默认值为 `Row`。 将属性设置为 `Column` 会导致 `FlexLayout` 的子级排列在单个项列中。

    当 `FlexLayout` 中的项目排列在一列中时，`FlexLayout` 称为具有垂直_主轴_和水平_十字轴_。

- [@No__t_1](xref:Xamarin.Forms.FlexLayout.AlignItems)属性的类型[`FlexAlignItems`](xref:Xamarin.Forms.FlexAlignItems) ，并指定如何在十字轴上对齐项。 @No__t_0 选项会使每个项目水平居中。

    如果使用的是 `StackLayout` 而不是此任务的 `FlexLayout`，则可以通过将每个项的 `HorizontalOptions` 属性分配给 `Center` 来使所有项居中。 @No__t_0 属性对 `FlexLayout` 的子级不起作用，但单一 `AlignItems` 属性可实现相同的目标。 如果需要，可以使用 `AlignSelf` 附加的可绑定属性覆盖各个项的 `AlignItems` 属性：

    ```xaml
    <Label Text="FlexLayout in Action"
           FontSize="Large"
           FlexLayout.AlignSelf="Start" />
    ```

    发生这种更改后，当读取顺序为从左到右时，此 `Label` 会位于 `FlexLayout` 的左边缘。

- [@No__t_1](xref:Xamarin.Forms.FlexLayout.JustifyContent)属性的类型为[`FlexJustify`](xref:Xamarin.Forms.FlexJustify)，指定如何在主轴上排列项。 @No__t_0 选项在所有项之间、在第一项的上方以及最后一项的下方分配所有剩余的垂直空间。

    如果使用的是 `StackLayout`，则需要将每个项的 `VerticalOptions` 属性分配给 `CenterAndExpand`，以实现类似的效果。 但 `CenterAndExpand` 选项将在每个项之间分配两倍于第一项和最后一项之后的空间。 您可以通过将 `FlexLayout` 的 `JustifyContent` 属性设置为 `SpaceAround` 来模拟 `VerticalOptions` 的 `CenterAndExpand` 选项。

下面详细介绍了 "可 **[绑定属性](#bindable-properties)** " 一节中更详细地讨论了这些 `FlexLayout` 属性。

### <a name="using-flexlayout-for-wrapping-items"></a>使用 FlexLayout 包装项

**[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** 示例的 "**照片包装**" 页演示了 `FlexLayout` 如何将其子级包装到其他行或列。 XAML 文件实例化 `FlexLayout`，并为其分配两个属性：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="FlexLayoutDemos.PhotoWrappingPage"
             Title="Photo Wrapping">
    <Grid>
        <ScrollView>
            <FlexLayout x:Name="flexLayout"
                        Wrap="Wrap"
                        JustifyContent="SpaceAround" />
        </ScrollView>

        <ActivityIndicator x:Name="activityIndicator"
                           IsRunning="True"
                           VerticalOptions="Center" />
    </Grid>
</ContentPage>
```

未设置此 `FlexLayout` 的 `Direction` 属性，因此它具有 `Row` 的默认设置，这意味着子项按行排列，并且主轴是水平轴。

[@No__t_1](xref:Xamarin.Forms.FlexLayout.Wrap)属性的类型[`FlexWrap`](xref:Xamarin.Forms.FlexWrap)为枚举类型。 如果行上容纳了太多的项，则此属性设置会导致项换行到下一行。

请注意，`FlexLayout` 是 `ScrollView` 的子。 如果页面上容纳的行过多，则 `ScrollView` 具有 `Vertical` 的默认 `Orientation` 属性，并允许垂直滚动。

@No__t_0 属性在主轴（水平轴）上分配剩余空间，以便每个项目都环绕相同数量的空白空间。

代码隐藏文件可访问示例照片的集合，并将其添加到 `FlexLayout` 的 `Children` 集合中：

```csharp
public partial class PhotoWrappingPage : ContentPage
{
    // Class for deserializing JSON list of sample bitmaps
    [DataContract]
    class ImageList
    {
        [DataMember(Name = "photos")]
        public List<string> Photos = null;
    }

    public PhotoWrappingPage ()
    {
        InitializeComponent ();

        LoadBitmapCollection();
    }

    async void LoadBitmapCollection()
    {
        using (WebClient webClient = new WebClient())
        {
            try
            {
                // Download the list of stock photos
                Uri uri = new Uri("https://raw.githubusercontent.com/xamarin/docs-archive/master/Images/stock/small/stock.json");
                byte[] data = await webClient.DownloadDataTaskAsync(uri);

                // Convert to a Stream object
                using (Stream stream = new MemoryStream(data))
                {
                    // Deserialize the JSON into an ImageList object
                    var jsonSerializer = new DataContractJsonSerializer(typeof(ImageList));
                    ImageList imageList = (ImageList)jsonSerializer.ReadObject(stream);

                    // Create an Image object for each bitmap
                    foreach (string filepath in imageList.Photos)
                    {
                        Image image = new Image
                        {
                            Source = ImageSource.FromUri(new Uri(filepath))
                        };
                        flexLayout.Children.Add(image);
                    }
                }
            }
            catch
            {
                flexLayout.Children.Add(new Label
                {
                    Text = "Cannot access list of bitmap files"
                });
            }
        }

        activityIndicator.IsRunning = false;
        activityIndicator.IsVisible = false;
    }
}
```

下面是正在运行的程序，从上到下逐渐滚动：

[![照片包装页](flex-layout-images/PhotoWrapping.png "照片包装页")](flex-layout-images/PhotoWrapping-Large.png#lightbox)

### <a name="page-layout-with-flexlayout"></a>具有 FlexLayout 的页面布局

Web 设计中有一个称为 "[_圣圣杯_](https://en.wikipedia.org/wiki/Holy_grail_(web_design))" 的标准布局，因为它是一种非常理想的布局格式，但通常很难实现。 布局由页面顶部的页眉和底部的页脚组成，并延伸到页面的整个宽度。 占据页面中心就是主要内容，但通常会在右侧出现一个纵栏式菜单，内容和补充信息（有时称为_旁边_）。 [CSS 挠性 Box 布局规范的节 5.4.1](http://www.w3.org/TR/css-flexbox-1/#order-accessibility)介绍了如何使用弹性框来实现圣圣杯布局。

**[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** 示例的 "**美妙的圣杯" 布局**页显示了此布局的简单实现，该实现使用一个嵌套在另一个 `FlexLayout`。 由于此页是针对纵向模式下的手机设计的，因此内容区域左侧和右侧的区域只有50像素宽：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="FlexLayoutDemos.HolyGrailLayoutPage"
             Title="Holy Grail Layout">

    <FlexLayout Direction="Column">

        <!-- Header -->
        <Label Text="HEADER"
               FontSize="Large"
               BackgroundColor="Aqua"
               HorizontalTextAlignment="Center" />

        <!-- Body -->
        <FlexLayout FlexLayout.Grow="1">

            <!-- Content -->
            <Label Text="CONTENT"
                   FontSize="Large"
                   BackgroundColor="Gray"
                   HorizontalTextAlignment="Center"
                   VerticalTextAlignment="Center"
                   FlexLayout.Grow="1" />

            <!-- Navigation items-->
            <BoxView FlexLayout.Basis="50"
                     FlexLayout.Order="-1"
                     Color="Blue" />

            <!-- Aside items -->
            <BoxView FlexLayout.Basis="50"
                     Color="Green" />

        </FlexLayout>

        <!-- Footer -->
        <Label Text="FOOTER"
               FontSize="Large"
               BackgroundColor="Pink"
               HorizontalTextAlignment="Center" />
    </FlexLayout>
</ContentPage>
```

此处运行的是：

[![圣圣杯布局页](flex-layout-images/HolyGrailLayout.png "圣圣杯布局页")](flex-layout-images/HolyGrailLayout-Large.png#lightbox)

向左和向右 `BoxView` 呈现导航区域和搁置区域。

XAML 文件中的第一个 `FlexLayout` 具有垂直主轴，并包含三个子元素，它们排列在一列中。 它们是页眉、页面的正文和页脚。 嵌套 `FlexLayout` 有一个水平主轴，其中有三个子轴排列在一行中。

此程序演示了三个附加的可绑定属性：

- 在第一个 `BoxView` 上设置 `Order` 附加的可绑定属性。 此属性是一个整数，其默认值为0。 您可以使用此属性来更改布局顺序。 通常，开发人员更喜欢在导航项之前的标记中显示的页面内容。 将第一个 `BoxView` 上的 `Order` 属性设置为小于其其他同级的值将导致它显示为行中的第一项。 同样，您可以通过将 `Order` 属性设置为一个大于同级的值来确保该项出现最后。

- 在两个 `BoxView` 项上设置 `Basis` 附加的可绑定属性，使其宽度为50像素。 此属性的类型为 `FlexBasis`，这是一个结构，它定义名为 `FlexBasis` 类型的静态属性，这是默认值 `Auto`。 您可以使用 `Basis` 来指定像素大小或指示项在主轴上的空间大小的百分比。 它被称为_基数_，因为它指定的项大小是所有后续布局的基础。

- @No__t_0 属性设置在嵌套 `Layout` 上，以及表示内容的 `Label` 子元素上。 此属性的类型为 `float`，其默认值为0。 如果设置为正值，则将主轴上的所有剩余空间分配给该项目，并将其值分配给具有正值 `Grow` 的同级。 空间按比例分配给值，与 `Grid` 中的星型规范有些类似。

    第一个 `Grow` 附加属性设置在嵌套 `FlexLayout` 上，指示此 `FlexLayout` 用于占用外部 `FlexLayout` 中的所有未使用的垂直空间。 第二个 `Grow` 附加属性在表示内容的 `Label` 上设置，指示此内容将占用内部 `FlexLayout` 内所有未使用的水平空间。

    还有一个类似 `Shrink` 附加的可绑定属性，当子级的大小超出 `FlexLayout` 的大小但不需要包装时，可以使用此属性。

### <a name="catalog-items-with-flexlayout"></a>具有 FlexLayout 的目录项

**[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** 示例中的 "**目录项**" 页与["CSS 弹性布局" 对话框规范第1.1 节中的示例 1](http://www.w3.org/TR/css-flexbox-1/#overview)类似，不同之处在于它显示一系列水平滚动的图片和三个猴的说明。:

[!["目录项" 页](flex-layout-images/CatalogItems.png ""目录项" 页")](flex-layout-images/CatalogItems-Large.png#lightbox)

这三个猴中的每一项都是一个 `Frame` 中包含的 `FlexLayout`，它是一个明确的高度和宽度，也是更大 `FlexLayout` 的子级。 在此 XAML 文件中，在样式中指定 `FlexLayout` 子级的大多数属性，但其中一个属性是隐式样式：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:FlexLayoutDemos"
             x:Class="FlexLayoutDemos.CatalogItemsPage"
             Title="Catalog Items">
    <ContentPage.Resources>
        <Style TargetType="Frame">
            <Setter Property="BackgroundColor" Value="LightYellow" />
            <Setter Property="BorderColor" Value="Blue" />
            <Setter Property="Margin" Value="10" />
            <Setter Property="CornerRadius" Value="15" />
        </Style>

        <Style TargetType="Label">
            <Setter Property="Margin" Value="0, 4" />
        </Style>

        <Style x:Key="headerLabel" TargetType="Label">
            <Setter Property="Margin" Value="0, 8" />
            <Setter Property="FontSize" Value="Large" />
            <Setter Property="TextColor" Value="Blue" />
        </Style>

        <Style TargetType="Image">
            <Setter Property="FlexLayout.Order" Value="-1" />
            <Setter Property="FlexLayout.AlignSelf" Value="Center" />
        </Style>

        <Style TargetType="Button">
            <Setter Property="Text" Value="LEARN MORE" />
            <Setter Property="FontSize" Value="Large" />
            <Setter Property="TextColor" Value="White" />
            <Setter Property="BackgroundColor" Value="Green" />
            <Setter Property="BorderRadius" Value="20" />
        </Style>
    </ContentPage.Resources>

    <ScrollView Orientation="Both">
        <FlexLayout>
            <Frame WidthRequest="300"
                   HeightRequest="480">

                <FlexLayout Direction="Column">
                    <Label Text="Seated Monkey"
                           Style="{StaticResource headerLabel}" />
                    <Label Text="This monkey is laid back and relaxed, and likes to watch the world go by." />
                    <Label Text="  &#x2022; Doesn't make a lot of noise" />
                    <Label Text="  &#x2022; Often smiles mysteriously" />
                    <Label Text="  &#x2022; Sleeps sitting up" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.SeatedMonkey.jpg}"
                           WidthRequest="180"
                           HeightRequest="180" />
                    <Label FlexLayout.Grow="1" />
                    <Button />
                </FlexLayout>
            </Frame>

            <Frame WidthRequest="300"
                   HeightRequest="480">

                <FlexLayout Direction="Column">
                    <Label Text="Banana Monkey"
                           Style="{StaticResource headerLabel}" />
                    <Label Text="Watch this monkey eat a giant banana." />
                    <Label Text="  &#x2022; More fun than a barrel of monkeys" />
                    <Label Text="  &#x2022; Banana not included" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.Banana.jpg}"
                           WidthRequest="240"
                           HeightRequest="180" />
                    <Label FlexLayout.Grow="1" />
                    <Button />
                </FlexLayout>
            </Frame>

            <Frame WidthRequest="300"
                   HeightRequest="480">

                <FlexLayout Direction="Column">
                    <Label Text="Face-Palm Monkey"
                           Style="{StaticResource headerLabel}" />
                    <Label Text="This monkey reacts appropriately to ridiculous assertions and actions." />
                    <Label Text="  &#x2022; Cynical but not unfriendly" />
                    <Label Text="  &#x2022; Seven varieties of grimaces" />
                    <Label Text="  &#x2022; Doesn't laugh at your jokes" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.FacePalm.jpg}"
                           WidthRequest="180"
                           HeightRequest="180" />
                    <Label FlexLayout.Grow="1" />
                    <Button />
                </FlexLayout>
            </Frame>
        </FlexLayout>
    </ScrollView>
</ContentPage>
```

@No__t_0 的隐式样式包括 `Flexlayout` 的两个附加可绑定属性的设置：

```xaml
<Style TargetType="Image">
    <Setter Property="FlexLayout.Order" Value="-1" />
    <Setter Property="FlexLayout.AlignSelf" Value="Center" />
</Style>
```

@No__t_11 的 `Order` 设置导致 `Image` 元素在每个嵌套的 `FlexLayout` 视图中首先显示，而不考虑其在子集合中的位置。 @No__t_1 的 `AlignSelf` 属性导致 `Image` 在 `FlexLayout` 中居中。 这会重写 `AlignItems` 属性的设置，该属性的默认值为 `Stretch`，这意味着 `Label` 和 `Button` 子级拉伸到 `FlexLayout` 的整个宽度。

在三个 `FlexLayout` 视图的每个视图中，空 `Label` 在 `Button` 之前，但其 `Grow` 设置为1。 这意味着会将所有额外的垂直空间分配给此空白 `Label`，从而有效地将 `Button` 推送到底部。

<a name="bindable-properties" />

## <a name="the-bindable-properties-in-detail"></a>详细的可绑定属性

现在，你已了解 `FlexLayout` 的一些常见应用程序，可以更详细地探讨 `FlexLayout` 的属性。
`FlexLayout` 定义了六个可绑定属性，这些属性是在 `FlexLayout` 本身上设置的，可在代码或 XAML 中进行设置，以控制方向和对齐方式。 （本文未介绍其中的一个属性[`Position`](xref:Xamarin.Forms.FlexLayout.Position)。）

你可以使用 **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** 示例的**试验**页试验5个剩余的可绑定属性。 此页允许您在 `FlexLayout` 中添加或删除子级，以及设置五个可绑定属性的组合。 @No__t_0 的所有子级都是各种颜色和大小的 `Label` 视图，`Text` 属性设置为与其在 `Children` 集合中的位置相对应的数字。

当程序启动时，五个 `Picker` 视图显示这五个 `FlexLayout` 属性的默认值。 位于屏幕底部的 `FlexLayout` 包含三个子元素：

[!["试验" 页：默认值](flex-layout-images/ExperimentDefault.png ""试验" 页-默认")](flex-layout-images/ExperimentDefault-Large.png#lightbox)

每个 `Label` 视图都有一个灰色背景，其中显示了分配给 `FlexLayout` 内的 `Label` 的空间。 @No__t_0 的背景为 Alice 蓝色。 它占据页面的整个底部，但左侧和右侧有一个小边距。

<a name="direction" />

### <a name="the-direction-property"></a>Direction 属性

[@No__t_1](xref:Xamarin.Forms.FlexLayout.Direction)属性的类型为[`FlexDirection`](xref:Xamarin.Forms.FlexDirection)，这是具有四个成员的枚举：

- `Column`
- `ColumnReverse` （在 XAML 中为 "列反向"）
- `Row`，默认值为
- `RowReverse` （在 XAML 中为 "行反向"）

在 XAML 中，可以使用以小写、大写或混合大小写形式表示的枚举成员名称来指定此属性的值，也可以使用与 CSS 指示器相同的括号中显示的两个其他字符串。 （"列反转" 和 "行反向" 字符串在 XAML 分析器使用的[`FlexDirectionTypeConverter`](xref:Xamarin.Forms.FlexDirectionTypeConverter)类中定义。）

下面是 "**试验**" 页，其中显示了 "`Row` 方向、`Column` 方向和 `ColumnReverse` 方向：

[![试验页：方向](flex-layout-images/ExperimentDirection.png "试验页方向")](flex-layout-images/ExperimentDirection-Large.png#lightbox)

请注意，对于 `Reverse` 选项，项从右侧或底部开始。

<a name="wrap" />

### <a name="the-wrap-property"></a>Wrap 属性

[@No__t_1](xref:Xamarin.Forms.FlexLayout.Wrap)属性的类型为[`FlexWrap`](xref:Xamarin.Forms.FlexWrap)，这是一个包含三个成员的枚举：

- `NoWrap`，默认值为
- `Wrap`
- `Reverse` （在 XAML 中为 "反向"）

从左到右，这些屏幕显示了12个子项的 `NoWrap`、`Wrap` 和 `Reverse` 选项：

[!["试验" 页：换行](flex-layout-images/ExperimentWrap.png "试验页换行")](flex-layout-images/ExperimentWrap-Large.png#lightbox)

如果将 `Wrap` 属性设置为 "`NoWrap` 并且主轴受到约束（如在此程序中），并且主轴的宽度或高度不足以容纳所有子级，则 `FlexLayout` 会尝试使项目变小，如 iOS 屏幕截图所示。 可以通过[`Shrink`](#shrink)附加的可绑定属性控制项的 shrinkness。

<a name="justify-content" />

### <a name="the-justifycontent-property"></a>JustifyContent 属性

[@No__t_1](xref:Xamarin.Forms.FlexLayout.JustifyContent)属性的类型为[`FlexJustify`](xref:Xamarin.Forms.FlexJustify)，这是包含六个成员的枚举：

- `Start` （在 XAML 中为 "flex start"），默认值为
- `Center`
- `End` （在 XAML 中为 "flex end"）
- `SpaceBetween` （或 "XAML 中的空格"）
- `SpaceAround` （或 "在 XAML 中的空格"）
- `SpaceEvenly`

此属性指定如何在主轴上分隔项，这是本示例中的水平轴：

[![试验页：调整内容](flex-layout-images/ExperimentJustifyContent.png "试验页-调整内容")](flex-layout-images/ExperimentJustifyContent-Large.png#lightbox)

在所有三个屏幕截图中，`Wrap` 属性设置为 `Wrap`。 @No__t_0 默认值显示在前面的 Android 屏幕截图中。 此处的 iOS 屏幕截图显示 `Center` 选项：所有项都移到中心。 以单词开头的三个其他选项 `Space` 分配项未占用的额外空间。 `SpaceBetween` 在项之间平均分配空间; `SpaceAround` 在每个项周围放置相等的空间，而 `SpaceEvenly` 则在每个项之间以及第一项和最后一项之后、第一项和最后一项之后。

<a name="align-items" />

### <a name="the-alignitems-property"></a>AlignItems 属性

[@No__t_1](xref:Xamarin.Forms.FlexLayout.AlignItems)属性的类型为[`FlexAlignItems`](xref:Xamarin.Forms.FlexAlignItems)，这是具有四个成员的枚举：

- `Stretch`，默认值为
- `Center`
- `Start` （在 XAML 中为 "flex start"）
- `End` （在 XAML 中为 "flex end"）

这是两个属性中的一个（另一个是[`AlignContent`](#align-content)），指示子轴上的子项的对齐方式。 在每行中，子元素将被拉伸（如前面的屏幕截图中所示），或与每个项的开始、中心或结束对齐，如以下三个屏幕截图所示：

[!["试验" 页：对齐项](flex-layout-images/ExperimentAlignItems.png "试验页对齐项")](flex-layout-images/ExperimentAlignItems-Large.png#lightbox)

在 iOS 屏幕截图中，所有子项的顶部均为对齐。 在 Android 屏幕截图中，项基于最高子级垂直居中。 在 UWP 屏幕截图中，所有项的底部均为对齐。

对于任何单个项，`AlignItems` 设置都可以用[`AlignSelf`](#align-self)附加的可绑定属性来重写。

<a name="align-content" />

### <a name="the-aligncontent-property"></a>AlignContent 属性

[@No__t_1](xref:Xamarin.Forms.FlexLayout.AlignContent)属性的类型为[`FlexAlignContent`](xref:Xamarin.Forms.FlexAlignContent)，是包含七个成员的枚举：

- `Stretch`，默认值为
- `Center`
- `Start` （在 XAML 中为 "flex start"）
- `End` （在 XAML 中为 "flex end"）
- `SpaceBetween` （或 "XAML 中的空格"）
- `SpaceAround` （或 "在 XAML 中的空格"）
- `SpaceEvenly`

与 `AlignItems` 一样，"`AlignContent`" 属性还可对齐跨轴上的子级，但会影响整行或整列：

[!["试验" 页：对齐内容](flex-layout-images/ExperimentAlignContent.png "试验页-对齐内容")](flex-layout-images/ExperimentAlignContent-Large.png#lightbox)

在 iOS 屏幕截图中，这两行位于顶部;在 Android 屏幕截图中，它们位于中心;并在 UWP 屏幕截图中看到它们。 还可以通过多种方式分隔行：

[!["试验" 页：对齐内容2](flex-layout-images/ExperimentAlignContent2.png "试验页-对齐内容2")](flex-layout-images/ExperimentAlignContent2-Large.png#lightbox)

当只有一个行或列时，`AlignContent` 不起作用。

<a name="attached-properties" />

## <a name="the-attached-bindable-properties-in-detail"></a>附加的可绑定属性详细信息

`FlexLayout` 定义五个附加的可绑定属性。 这些属性在 `FlexLayout` 的子级上设置，并且仅适用于该特定子级。

<a name="align-self" />

### <a name="the-alignself-property"></a>AlignSelf 属性

[@No__t_1](xref:Xamarin.Forms.FlexLayout.AlignSelfProperty)附加的可绑定属性属于类型[`FlexAlignSelf`](xref:Xamarin.Forms.FlexAlignContent)，这是一个包含五个成员的枚举：

- `Auto`，默认值为
- `Stretch`
- `Center`
- `Start` （在 XAML 中为 "flex start"）
- `End` （在 XAML 中为 "flex end"）

对于 `FlexLayout` 的任何单个子级，此属性设置将覆盖 `FlexLayout` 本身上设置的[`AlignItems`](#align-items)属性。 @No__t_0 的默认设置意味着使用 `AlignItems` 设置。

对于名为 `label` 的 `Label` 元素（或示例），可以在代码中设置 `AlignSelf` 属性，如下所示：

```csharp
FlexLayout.SetAlignSelf(label, FlexAlignSelf.Center);
```

请注意，不存在对 `Label` 的 `FlexLayout` 父级的引用。 在 XAML 中，按如下所示设置属性：

```xaml
<Label ... FlexLayout.AlignSelf="Center" ... />
```

### <a name="the-order-property"></a>Order 属性

[@No__t_1](xref:Xamarin.Forms.FlexLayout.OrderProperty)属性的类型 `int`。 默认值为 0。

@No__t_0 属性允许您更改 `FlexLayout` 的子级排列顺序。 通常，`FlexLayout` 的子项按其在 `Children` 集合中的显示顺序排列。 您可以通过将 `Order` "附加的可绑定属性" 设置为一个或多个子级的非零整数值，来重写此顺序。 然后，`FlexLayout` 根据每个子元素的 `Order` 属性的设置来排列其子级，但具有相同 `Order` 设置的子级按它们在 `Children` 集合中出现的顺序排列。

### <a name="the-basis-property"></a>Basis 属性

[@No__t_1](xref:Xamarin.Forms.FlexLayout.BasisProperty)附加的可绑定属性指示分配给主轴上 `FlexLayout` 子级的空间量。 @No__t_0 属性指定的大小是父级 `FlexLayout` 的主轴的大小。 因此，当子级按行排列时，或当子级排列在列中时，`Basis` 指示子项的宽度。

@No__t_0 属性的类型为[`FlexBasis`](xref:Xamarin.Forms.FlexBasis)的结构。 大小可以指定为与设备无关的单位，或以 `FlexLayout` 大小的百分比指定。 @No__t_0 属性的默认值是 `FlexBasis.Auto` 的静态属性，这意味着将使用子级请求的宽度或高度。

在代码中，可以将名为 `label` 的 `Label` 的 `Basis` 属性设置为40与设备无关的单位，如下所示：

```csharp
FlexLayout.SetBasis(label, new FlexBasis(40, false));
```

@No__t_0 构造函数的第二个参数名为 `isRelative` 并指示大小是相对（`true`）还是绝对大小（`false`）。 自变量的默认值为 `false`，因此还可以使用以下代码：

```csharp
FlexLayout.SetBasis(label, new FlexBasis(40));
```

定义了从 `float` 到 `FlexBasis` 的隐式转换，因此您可以进一步简化它：

```csharp
FlexLayout.SetBasis(label, 40);
```

可将大小设置为 `FlexLayout` 父项的25%，如下所示：

```csharp
FlexLayout.SetBasis(label, new FlexBasis(0.25f, true));
```

此小数值必须在0到1的范围内。

在 XAML 中，可以在与设备无关的单位中为大小使用数字：

```xaml
<Label ... FlexLayout.Basis="40" ... />
```

或者，你可以指定介于0% 到100% 之间的百分比：

```xaml
<Label ... FlexLayout.Basis="25%" ... />
```

**[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** 示例的 "**基本实验**" 页允许您试验 `Basis` 属性。 页面将显示包含5个 `Label` 元素的已包装列以及背景色和前景色。 通过两个 `Slider` 元素，你可以为第二个和第四个 `Label` 指定 `Basis` 值：

[!["基本实验" 页](flex-layout-images/BasisExperiment.png ""基本实验" 页")](flex-layout-images/BasisExperiment-Large.png#lightbox)

左侧的 iOS 屏幕截图显示了两个 `Label` 元素，这些元素在与设备无关的单位中获得高度。 Android 屏幕显示它们的高度是 `FlexLayout` 总高度的一小部分。 如果 `Basis` 设置为100%，则子级为 `FlexLayout` 的高度，并将换到下一列并占用该列的整个高度，如 UWP 屏幕截图所示：显示为一行中的五个子项，但实际上它们是按5列排列的。

### <a name="the-grow-property"></a>增大属性

附加的可绑定属性是类型 `int` [`Grow`](xref:Xamarin.Forms.FlexLayout.GrowProperty) 。 默认值为0，值必须大于或等于0。

当 `Wrap` 属性设置为 `NoWrap`，并且子级的行的总宽度小于 `FlexLayout` 的宽度时，或子列的高度小于 `FlexLayout` 时，`Grow` 属性会扮演角色。 @No__t_0 属性指示如何分配子级之间的遗留空间。

在 "**增长实验**" 页中，`Label` 交替颜色的五个元素排列在一列中，两个 `Slider` 元素使您可以调整第二个和第四个 `Label` 的 `Grow` 属性。 最左侧的 iOS 屏幕截图显示默认 `Grow` 值为0的属性：

[!["增长实验" 页](flex-layout-images/GrowExperiment.png ""增长实验" 页")](flex-layout-images/GrowExperiment-Large.png#lightbox)

如果为任何一个子级指定了正 `Grow` 值，则该子级将占用所有剩余空间，如 Android 屏幕截图所示。 此空间还可以在两个或多个子级中分配。 在 UWP 屏幕截图中，第二个 `Label` 的 `Grow` 属性设置为0.5，而第四个 `Label` 的 `Grow` 属性为1.5，这将为第四个 `Label` 提供与第二个 `Label` 最多的剩余空间的三倍。

子视图使用该空间的方式取决于特定类型的子类型。 对于 `Label`，可以使用属性 `HorizontalTextAlignment` 和 `VerticalTextAlignment` 将文本置于 `Label` 的总空间内。

<a name="shrink" />

### <a name="the-shrink-property"></a>Shrink 属性

附加的可绑定属性是类型 `int` [`Shrink`](xref:Xamarin.Forms.FlexLayout.ShrinkProperty) 。 默认值为1，值必须大于或等于0。

当 `Wrap` 属性设置为 `NoWrap`，并且子级的行的聚合宽度大于 `FlexLayout` 的宽度，或子级的单个列的聚合高度大于的高度时，`Shrink` 属性会扮演一个角色。`FlexLayout`。 通常，`FlexLayout` 会通过 constricting 其大小来显示这些子级。 @No__t_0 属性可以指示哪些子级在按其完整大小显示时具有优先级。

"**收缩试验**" 页将创建一个 `FlexLayout`，其中包含5个 `Label` 子级，其中的行需要比 `FlexLayout` 宽度更多的空间。 左侧的 iOS 屏幕截图显示默认值为1的所有 `Label` 元素：

[!["收缩试验" 页](flex-layout-images/ShrinkExperiment.png ""收缩试验" 页")](flex-layout-images/ShrinkExperiment-Large.png#lightbox)

在 Android 屏幕截图中，第二个 `Label` 的 `Shrink` 值设置为0，`Label` 以其完整宽度显示。 此外，第四个 `Label` 给定 `Shrink` 大于1的值，并且它已收缩。 UWP 屏幕截图显示给定 `Shrink` 值为0的 `Label` 元素，以使其在可能的情况下显示为其完整大小。

可以同时设置 `Grow` 和 `Shrink` 值，以适应聚合子大小有时可能小于或大于 `FlexLayout` 大小的情况。

## <a name="css-styling-with-flexlayout"></a>具有 FlexLayout 的 CSS 样式

你可以使用 Xamarin. Forms 3.0 中引入的[CSS 样式](~/xamarin-forms/user-interface/styles/css/index.md)功能与 `FlexLayout`。 **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** 示例的 " **CSS 目录项**" 页复制 "**目录项**" 页的布局，但包含许多样式的 css 样式表：

[!["CSS 目录项" 页](flex-layout-images/CssCatalogItems.png ""CSS 目录项" 页")](flex-layout-images/CssCatalogItems-Large.png#lightbox)

原始**CatalogItemsPage**文件的 `Resources` 部分中有5个 `Style` 定义，其中包含15个 `Setter` 对象。 在**CssCatalogItemsPage**文件中，已减少到两个仅包含四个 `Setter` 对象的 `Style` 定义。 以下样式对 Xamarin CSS 样式功能当前不支持的属性的 CSS 样式表进行了补充：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:FlexLayoutDemos"
             x:Class="FlexLayoutDemos.CssCatalogItemsPage"
             Title="CSS Catalog Items">
    <ContentPage.Resources>
        <StyleSheet Source="CatalogItemsStyles.css" />

        <Style TargetType="Frame">
            <Setter Property="BorderColor" Value="Blue" />
            <Setter Property="CornerRadius" Value="15" />
        </Style>

        <Style TargetType="Button">
            <Setter Property="Text" Value="LEARN MORE" />
            <Setter Property="BorderRadius" Value="20" />
        </Style>
    </ContentPage.Resources>

    <ScrollView Orientation="Both">
        <FlexLayout>
            <Frame>
                <FlexLayout Direction="Column">
                    <Label Text="Seated Monkey" StyleClass="header" />
                    <Label Text="This monkey is laid back and relaxed, and likes to watch the world go by." />
                    <Label Text="  &#x2022; Doesn't make a lot of noise" />
                    <Label Text="  &#x2022; Often smiles mysteriously" />
                    <Label Text="  &#x2022; Sleeps sitting up" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.SeatedMonkey.jpg}" />
                    <Label StyleClass="empty" />
                    <Button />
                </FlexLayout>
            </Frame>

            <Frame>
                <FlexLayout Direction="Column">
                    <Label Text="Banana Monkey" StyleClass="header" />
                    <Label Text="Watch this monkey eat a giant banana." />
                    <Label Text="  &#x2022; More fun than a barrel of monkeys" />
                    <Label Text="  &#x2022; Banana not included" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.Banana.jpg}" />
                    <Label StyleClass="empty" />
                    <Button />
                </FlexLayout>
            </Frame>

            <Frame>
                <FlexLayout Direction="Column">
                    <Label Text="Face-Palm Monkey" StyleClass="header" />
                    <Label Text="This monkey reacts appropriately to ridiculous assertions and actions." />
                    <Label Text="  &#x2022; Cynical but not unfriendly" />
                    <Label Text="  &#x2022; Seven varieties of grimaces" />
                    <Label Text="  &#x2022; Doesn't laugh at your jokes" />
                    <Image Source="{local:ImageResource FlexLayoutDemos.Images.FacePalm.jpg}" />
                    <Label StyleClass="empty" />
                    <Button />
                </FlexLayout>
            </Frame>
        </FlexLayout>
    </ScrollView>
</ContentPage>
```

CSS 样式表在 `Resources` 部分的第一行中被引用：

```xaml
<StyleSheet Source="CatalogItemsStyles.css" />
```

另请注意，这三个项目中的每个元素都包含 `StyleClass` 设置：

```xaml
<Label Text="Seated Monkey" StyleClass="header" />
···
<Label StyleClass="empty" />
```

这些是指**CatalogItemsStyles**样式表中的选择器：

```css
frame {
    width: 300;
    height: 480;
    background-color: lightyellow;
    margin: 10;
}

label {
    margin: 4 0;
}

label.header {
    margin: 8 0;
    font-size: large;
    color: blue;
}

label.empty {
    flex-grow: 1;
}

image {
    height: 180;
    order: -1;
    align-self: center;
}

button {
    font-size: large;
    color: white;
    background-color: green;
}
```

此处引用了多个 `FlexLayout` 附加的可绑定属性。 在 `label.empty` 选择器中，你将看到 `flex-grow` 属性，它将空 `Label` 的样式，以在 `Button` 上方提供一些空格。 @No__t_0 选择器包含一个 `order` 属性和一个 `align-self` 属性，这两个属性分别对应于 `FlexLayout` 附加的可绑定属性。

你已看到，你可以直接在 `FlexLayout` 上设置属性，并且可以在 `FlexLayout` 的子级上设置附加的可绑定属性。 或者，可以使用传统的基于 XAML 的样式或 CSS 样式间接设置这些属性。 重要的是了解并了解这些属性。 这些属性使 `FlexLayout` 真正灵活。

## <a name="flexlayout-with-xamarinuniversity"></a>用 Xamarin 大学 FlexLayout

> [!VIDEO https://youtube.com/embed/Ng3sel_5D_0]

**Xamarin 3.0 Flex 布局视频**

## <a name="related-links"></a>相关链接

- [FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)
