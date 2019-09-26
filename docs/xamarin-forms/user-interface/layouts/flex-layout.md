---
title: Xamarin.Forms FlexLayout
description: 使用 FlexLayout 堆叠或包装子视图的集合。
ms.prod: xamarin
ms.assetid: 6A91EA70-268C-462C-AAAF-F8DA011403F8
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/07/2018
ms.openlocfilehash: 187befd88c115133a92aa90a711438e7754518d5
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "68648805"
---
# <a name="the-xamarinforms-flexlayout"></a>Xamarin.Forms FlexLayout

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)

_使用 FlexLayout 堆叠或包装子视图的集合。_

Xamarin.Forms [ `FlexLayout` ](xref:Xamarin.Forms.FlexLayout) Xamarin.Forms 版本 3.0 中新增功能。 基于 CSS[灵活框布局模块](http://www.w3.org/TR/css-flexbox-1/)，通常称为_flex 布局_或_弹性框_，因此称为，因为它包括许多灵活的选项来排列子级在布局。

`FlexLayout` 类似于 Xamarin.Forms [ `StackLayout` ](~/xamarin-forms/user-interface/layouts/stack-layout.md) ，因为它可以排列其子级水平和垂直堆栈。 但是，`FlexLayout`还能够换行及其子级，如果有太多而无法放入单个行或列中，并且还具有许多用于方向、 对齐和适应各种屏幕尺寸的选项。

`FlexLayout` 派生自[ `Layout<View>` ](xref:Xamarin.Forms.Layout`1) ，并继承[ `Children` ](xref:Xamarin.Forms.Layout`1.Children)类型的属性`IList<View>`。

`FlexLayout` 定义了六个公共可绑定属性和影响大小、 方向和子元素的对齐方式的五个附加的可绑定属性。 (如果您不熟悉可绑定的附加属性，请参阅文章 **[附加属性](~/xamarin-forms/xaml/attached-properties.md)** 。)在下面各节详细地介绍这些属性 **[详细信息中的可绑定属性](#bindable-properties)** 并 **[详细的附加可绑定属性](#attached-properties)** . 但是，将这篇文章开始一节介绍了一些 **[常见使用方案](#common-scenarios)** 的 `FlexLayout`更通俗地说描述其中的许多属性。 本文的末尾，将了解如何结合`FlexLayout`与[CSS 样式表](~/xamarin-forms/user-interface/styles/css/index.md)。

<a name="common-scenarios" />

## <a name="common-usage-scenarios"></a>常见使用方案

**[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** 示例程序包含多个演示的一些常见用途的页面`FlexLayout`，当你尝试使用其属性。

### <a name="using-flexlayout-for-a-simple-stack"></a>使用适用于简单堆栈 FlexLayout

**简单堆栈**页显示了如何`FlexLayout`可以将替换为`StackLayout`但更简单的标记。 此示例中的所有内容是 XAML 页中定义的。 `FlexLayout`包含四个子级：

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

下面是 iOS、 Android 和通用 Windows 平台上运行该页面：

[![简单堆栈页](flex-layout-images/SimpleStack.png "简单堆栈页")](flex-layout-images/SimpleStack-Large.png#lightbox)

三个属性`FlexLayout`中所示**SimpleStackPage.xaml**文件：

- [ `Direction` ](xref:Xamarin.Forms.FlexLayout.Direction)属性设置为值为[ `FlexDirection` ](xref:Xamarin.Forms.FlexDirection)枚举。 默认值为 `Row`。 将属性设置为`Column`导致的子级`FlexLayout`排列在项的单个列中。

    当中的项`FlexLayout`排列在列中，`FlexLayout`称其具有垂直_主轴_和水平_交叉轴_。

- [ `AlignItems` ](xref:Xamarin.Forms.FlexLayout.AlignItems)属性属于类型[ `FlexAlignItems` ](xref:Xamarin.Forms.FlexAlignItems)和指定的项在横轴上的对齐方式。 `Center`选项将使每一项要在水平居中。

    如果已使用`StackLayout`而非`FlexLayout`对于此任务，您将通过分配中居中的所有项`HorizontalOptions`到每个项的属性`Center`。 `HorizontalOptions`属性并不适用于的子级`FlexLayout`，但单个`AlignItems`属性可以达到相同目的。 如果需要可以使用`AlignSelf`附加可绑定属性重写`AlignItems`各项的属性：

    ```xaml
    <Label Text="FlexLayout in Action"
           FontSize="Large"
           FlexLayout.AlignSelf="Start" />
    ```

    与此更改后，这个`Label`定位在的左边缘`FlexLayout`时的阅读顺序是从左到右。

- [ `JustifyContent` ](xref:Xamarin.Forms.FlexLayout.JustifyContent)属性属于类型[ `FlexJustify` ](xref:Xamarin.Forms.FlexJustify)，并指定项主要的轴上的排列方式。 `SpaceEvenly`选项分配所有项之间进行均分，和上面的第一项和最后一项下的所有剩余垂直空间。

    如果已使用`StackLayout`，需要将分配`VerticalOptions`到每个项的属性`CenterAndExpand`要实现类似效果。 但`CenterAndExpand`选项会分配比每个项的第一项之前和之后的最后一项之间的两倍空间。 您可以模仿`CenterAndExpand`的选项`VerticalOptions`通过设置`JustifyContent`的属性`FlexLayout`到`SpaceAround`。

这些`FlexLayout`部分中的更详细地讨论属性 **[详细信息中的可绑定属性](#bindable-properties)** 下面。

### <a name="using-flexlayout-for-wrapping-items"></a>使用 FlexLayout 进行包装的项

**照片包装** 页 **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** 示例演示如何`FlexLayout`可以包装到其他行或列及其子级。 XAML 文件实例化`FlexLayout`，并将分配两个属性：

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

`Direction`属性的这`FlexLayout`未设置，因此它具有的默认设置`Row`，这意味着在行中排列子级，并且主要轴是水平的。

[ `Wrap` ](xref:Xamarin.Forms.FlexLayout.Wrap)属性是枚举类型的[ `FlexWrap` ](xref:Xamarin.Forms.FlexWrap)。 如果有太多的项，以适合的行，此属性设置会导致要换行到下一行中的项。

请注意，`FlexLayout`的子`ScrollView`。 如果有太多的行以容纳在页上，则`ScrollView`具有一个默认`Orientation`属性的`Vertical`，并允许垂直滚动。

`JustifyContent`属性分配主轴 （水平轴） 上的剩余空间，以便每个项括在相同的空白空间量。

代码隐藏文件访问的示例照片集合，并将它们添加到`Children`的集合`FlexLayout`:

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

下面是该程序正在运行，以渐进方式滚动从上到下：

[![照片包装页](flex-layout-images/PhotoWrapping.png "照片包装页")](flex-layout-images/PhotoWrapping-Large.png#lightbox)

### <a name="page-layout-with-flexlayout"></a>使用 FlexLayout 页面布局

在调用的 web 设计中没有的标准版式[ _holy grail_ ](https://en.wikipedia.org/wiki/Holy_grail_(web_design))因为它是非常有利，但通常难以实现与完善的布局格式。 布局包含在页面顶部的标头和页脚在底部，这两个扩展到整个页面的宽度。 占用的页的中心是主要的内容，但通常与左侧的内容和补充信息的纵栏式菜单 (有时称为_放在一边_区域) 右侧。 [CSS 灵活框布局规范的第 5.4.1 节](http://www.w3.org/TR/css-flexbox-1/#order-accessibility)介绍了如何在弹性框实现 holy grail 布局。

**Holy Grail 布局** 页 **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** 示例显示了使用其中一个此布局的简单实现`FlexLayout`嵌套在另一个。 因为此页专为手机在纵向模式下，左侧和右侧的内容区域的区域只是 50 像素宽：

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

运行此处：

[![Holy Grail 布局页面](flex-layout-images/HolyGrailLayout.png "Holy Grail 布局页")](flex-layout-images/HolyGrailLayout-Large.png#lightbox)

与呈现的导航和 aside 区域`BoxView`左侧和右侧。

第一个`FlexLayout`在 XAML 文件有主纵轴，并包含三个孩子排列在列中。 这些是标头、 页和表尾的正文。 嵌套`FlexLayout`排列在行中的三个孩子都具有主要横轴。

在此程序演示了三个附加的可绑定属性：

- `Order`设置附加的可绑定属性的第一个`BoxView`。 此属性是一个整数，其默认值为 0。 此属性可用于更改在布局顺序。 通常，开发人员更喜欢页后，可以在之前的导航项的标记中显示的内容并留出的项。 设置`Order`在第一个属性`BoxView`为值早于其他同级后，即可显示为行中的第一项。 同样，可以确保项通过设置显示最后一个`Order`属性大于其同级的值。

- `Basis`对两个设置附加的可绑定属性`BoxView`要为其提供的 50 像素宽度的项。 此属性属于类型`FlexBasis`，定义类型的静态属性的结构`FlexBasis`名为`Auto`，这是默认设置。 可以使用`Basis`指定像素大小或一个百分比，指示空间项占用主轴上。 它称为_基础_因为它指定是所有后续布局的基础的项大小。

- `Grow`属性设置在嵌套`Layout`并在`Label`子表示内容。 此属性的类型是`float`和默认值为 0。 设置为正值时，该主轴的所有剩余空间分配到该项目和具有正值的同级`Grow`。 值，类似于中的星型规范按比例分配空间`Grid`。

    第一个`Grow`设置附加的属性上的嵌套`FlexLayout`，则表示此`FlexLayout`是占用内外部的所有未使用垂直空间`FlexLayout`。 第二个`Grow`上设置附加的属性`Label`表示的内容，指示此内容以占据中内部的所有未使用水平空间`FlexLayout`。

    此外，还有一个类似`Shrink`附加时子项的大小超过的大小，可以使用的可绑定属性`FlexLayout`但不是需要换行。

### <a name="catalog-items-with-flexlayout"></a>使用 FlexLayout 目录项

**目录项** 页面 **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** 示例是类似于[示例 1 中的 CSS Flex 布局框规范部分1.1](http://www.w3.org/TR/css-flexbox-1/#overview)，只不过它将显示一系列可水平滚动的图片和说明的三个放弃：

[![目录项页](flex-layout-images/CatalogItems.png "目录项页")](flex-layout-images/CatalogItems-Large.png#lightbox)

每三个放弃`FlexLayout`中包含`Frame`给定了显式高度和宽度，并且也是更大的子`FlexLayout`。 在此 XAML 文件中，大部分的属性`FlexLayout`在样式中，一个是隐式样式的指定子级：

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

隐式样式`Image`包括两个附加的可绑定属性的设置`Flexlayout`:

```xaml
<Style TargetType="Image">
    <Setter Property="FlexLayout.Order" Value="-1" />
    <Setter Property="FlexLayout.AlignSelf" Value="Center" />
</Style>
```

`Order`设置为&ndash;1 会导致`Image`首先显示在每个嵌套元素`FlexLayout`而不考虑其位置中的 children 集合视图。 `AlignSelf`的属性`Center`会导致`Image`要内居中`FlexLayout`。 这会重写的设置`AlignItems`属性，它具有默认值的`Stretch`，这意味着，`Label`并`Button`子级将拉伸到整个宽度`FlexLayout`。

中的三个`FlexLayout`视图，空白`Label`之前`Button`，但它具有`Grow`设置为 1。 这意味着，将所有额外的垂直空间分配到此值为空`Label`，这有效地将推送`Button`到底部。

<a name="bindable-properties" />

## <a name="the-bindable-properties-in-detail"></a>详细信息中的可绑定属性

现在，已了解的一些常见的应用程序`FlexLayout`的属性`FlexLayout`可以更详细地探讨了。
`FlexLayout` 定义设置的六个可绑定属性`FlexLayout`本身，在代码或 XAML 控件方向和对齐方式。 (这些属性之一[ `Position` ](xref:Xamarin.Forms.FlexLayout.Position)，本文不介绍。)

您可以尝试使用五个剩余可绑定属性使用 **试验** 页 **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** 示例。 此页面允许您添加或删除从子级`FlexLayout`并设置五个可绑定属性的组合。 所有子级`FlexLayout`都`Label`视图的各种颜色和大小，使用`Text`属性设置为对应的数字为在其位置`Children`集合。

程序启动时，五`Picker`视图中显示五个默认值`FlexLayout`属性。 `FlexLayout`屏幕的底部包含三个子级：

[![试验页：默认](flex-layout-images/ExperimentDefault.png "的试验页-默认")值](flex-layout-images/ExperimentDefault-Large.png#lightbox)

每个`Label`视图具有灰色背景显示的分配的空间`Label`内`FlexLayout`。 背景`FlexLayout`本身是艾莉斯蓝。 它在左侧和右侧的小边距除外占据了整个底部区域中的页。

<a name="direction" />

### <a name="the-direction-property"></a>Direction 属性

[ `Direction` ](xref:Xamarin.Forms.FlexLayout.Direction)属性属于类型[ `FlexDirection` ](xref:Xamarin.Forms.FlexDirection)，具有四个成员的枚举：

- `Column`
- `ColumnReverse` （或者"列的反向"在 XAML 中）
- `Row`默认值
- `RowReverse` （或者"行的反向"在 XAML 中）

在 XAML 中，可以指定使用的枚举成员名称以小写字母大写，此属性的值或混合大小写，也可以使用 CSS 指示器相同的括号中所示的两个其他字符串。 (在中定义的"列反向"和"行反向"字符串[ `FlexDirectionTypeConverter` ](xref:Xamarin.Forms.FlexDirectionTypeConverter) XAML 分析器使用的类。)

下面是**实验**（从左到右），显示页面`Row`方向`Column`方向，和`ColumnReverse`方向：

[![试验页：(flex-layout-images/ExperimentDirection.png "\"试验\" 页方向")]](flex-layout-images/ExperimentDirection-Large.png#lightbox)

请注意，对于`Reverse`项启动选项，在右侧或底部。

<a name="wrap" />

### <a name="the-wrap-property"></a>自动换行属性

[ `Wrap` ](xref:Xamarin.Forms.FlexLayout.Wrap)属性属于类型[ `FlexWrap` ](xref:Xamarin.Forms.FlexWrap)，具有三个成员的枚举：

- `NoWrap`默认值
- `Wrap`
- `Reverse` （或者"自动换行的反向"在 XAML 中）

从左到右，这些屏幕显示`NoWrap`，`Wrap`和`Reverse`12 子级的选项：

[![试验页：包装](flex-layout-images/ExperimentWrap.png "试验页包装")](flex-layout-images/ExperimentWrap-Large.png#lightbox)

当`Wrap`属性设置为`NoWrap`和主轴受到约束 （如在此程序），以及主轴不高或足够宽，以适应所有子级`FlexLayout`尝试使项目变小，如 iOS 屏幕截图演示。 您可以控制的项 shrinkness [ `Shrink` ](#shrink)附加可绑定属性。

<a name="justify-content" />

### <a name="the-justifycontent-property"></a>JustifyContent 属性

[ `JustifyContent` ](xref:Xamarin.Forms.FlexLayout.JustifyContent)属性属于类型[ `FlexJustify` ](xref:Xamarin.Forms.FlexJustify)，一个具有六个成员的枚举：

- `Start` （或"flex 启动"在 XAML 中），默认值
- `Center`
- `End` （或者"flex 端"在 XAML 中）
- `SpaceBetween` （或者"空间-之间"在 XAML 中）
- `SpaceAround` （或者"空间的大约"在 XAML 中）
- `SpaceEvenly`

此属性指定项在此示例中为水平轴的主要轴上的分布方式：

[![试验页：调整内容](flex-layout-images/ExperimentJustifyContent.png "\"试验\" 页-调整内容")](flex-layout-images/ExperimentJustifyContent-Large.png#lightbox)

在所有三个屏幕截图`Wrap`属性设置为`Wrap`。 `Start`默认上面的 Android 屏幕截图中所示。 此处的 iOS 屏幕快照显示`Center`选项： 所有项都移动到中心。 其他三个选项从单词`Space`分配项未占用的额外空间。 `SpaceBetween` 分配的项; 同样之间的空间`SpaceAround` put 等于每个项周围的空间时`SpaceEvenly`put 等于每个项之间和第一项之前和之后的行的最后一项的空间。

<a name="align-items" />

### <a name="the-alignitems-property"></a>AlignItems 属性

[ `AlignItems` ](xref:Xamarin.Forms.FlexLayout.AlignItems)属性属于类型[ `FlexAlignItems` ](xref:Xamarin.Forms.FlexAlignItems)，具有四个成员的枚举：

- `Stretch`默认值
- `Center`
- `Start` （或者"flex 启动"在 XAML 中）
- `End` （或者"flex 端"在 XAML 中）

这是两个属性之一 (另一个执行[ `AlignContent` ](#align-content))，该值指示子交叉轴上的对齐方式。 在每个行中，子级是拉伸 （如上面的屏幕截图中所示），或对齐上开始、 居中或结束的每个项，如下面的三个屏幕截图中所示：

[![试验页：对齐 "(flex-layout-images/ExperimentAlignItems.png "试验\" 页面对齐项")]](flex-layout-images/ExperimentAlignItems-Large.png#lightbox)

在 iOS 屏幕截图中，所有子级的顶部对齐。 中的 Android 屏幕截图，项是垂直方向上居中基于在最高的子活动。 在 UWP 屏幕截图中，所有项的底部对齐。

对于任何单个项，`AlignItems`设置可以使用重写[ `AlignSelf` ](#align-self)附加可绑定属性。

<a name="align-content" />

### <a name="the-aligncontent-property"></a>AlignContent 属性

[ `AlignContent` ](xref:Xamarin.Forms.FlexLayout.AlignContent)属性属于类型[ `FlexAlignContent` ](xref:Xamarin.Forms.FlexAlignContent)，具有七个成员的枚举：

- `Stretch`默认值
- `Center`
- `Start` （或者"flex 启动"在 XAML 中）
- `End` （或者"flex 端"在 XAML 中）
- `SpaceBetween` （或者"空间-之间"在 XAML 中）
- `SpaceAround` （或者"空间的大约"在 XAML 中）
- `SpaceEvenly`

像`AlignItems`，则`AlignContent`属性还将对齐交叉轴上的子级，但会影响整个行或列：

[![试验页：对齐内容](flex-layout-images/ExperimentAlignContent.png "试验页-对齐内容")](flex-layout-images/ExperimentAlignContent-Large.png#lightbox)

在 iOS 屏幕截图中，这两个行位于顶部;在 Android 屏幕截图，它们在中心;并且 UWP 的屏幕截图中它们是在底部。 此外可以以各种方式分布行：

[![试验页：对齐内容 2](flex-layout-images/ExperimentAlignContent2.png "\"试验\" 页-对齐内容 2")](flex-layout-images/ExperimentAlignContent2-Large.png#lightbox)

`AlignContent`不起作用时只有一个行或列。

<a name="attached-properties" />

## <a name="the-attached-bindable-properties-in-detail"></a>详细信息中的附加可绑定属性

`FlexLayout` 定义五个附加的可绑定属性。 子级上设置这些属性`FlexLayout`且仅属于该特定子。

<a name="align-self" />

### <a name="the-alignself-property"></a>AlignSelf 属性

[ `AlignSelf` ](xref:Xamarin.Forms.FlexLayout.AlignSelfProperty)附加可绑定属性属于类型[ `FlexAlignSelf` ](xref:Xamarin.Forms.FlexAlignContent)，具有五个成员的枚举：

- `Auto`默认值
- `Stretch`
- `Center`
- `Start` （或者"flex 启动"在 XAML 中）
- `End` （或者"flex 端"在 XAML 中）

任何单个子`FlexLayout`，此属性设置会替代[ `AlignItems` ](#align-items)属性设置的`FlexLayout`本身。 默认设置`Auto`意味着使用`AlignItems`设置。

有关`Label`名为元素`label`（或示例），可以设置`AlignSelf`类似下面的代码中的属性：

```csharp
FlexLayout.SetAlignSelf(label, FlexAlignSelf.Center);
```

请注意，没有不引用`FlexLayout`的父级`Label`。 在 XAML，您设置此类属性：

```xaml
<Label ... FlexLayout.AlignSelf="Center" ... />
```

### <a name="the-order-property"></a>Order 属性

[ `Order` ](xref:Xamarin.Forms.FlexLayout.OrderProperty)属性属于类型`int`。 默认值为 0。

`Order`属性可以更改的顺序的子级的`FlexLayout`排列。 通常情况下的子级`FlexLayout`排列中出现的顺序相同`Children`集合。 可通过设置覆盖此顺序`Order`附加可绑定属性设置为上一个或多个子级的非零的整数值。 `FlexLayout`然后排列基于的设置及其子级`Order`属性上每个子级，但具有相同的子级`Order`设置中出现的顺序排列`Children`集合。

### <a name="the-basis-property"></a>基础属性

[ `Basis` ](xref:Xamarin.Forms.FlexLayout.BasisProperty)附加可绑定属性指示的一个子级的已分配的空间量`FlexLayout`主要轴上。 指定的大小`Basis`属性为父该主轴大小`FlexLayout`。 因此，`Basis`子级排列的行或高度时在列中排列子级时指示的子级的宽度。

`Basis`属性属于类型[ `FlexBasis` ](xref:Xamarin.Forms.FlexBasis)，一种结构。 可以指定的大小，在任一设备无关的单位或大小的百分比`FlexLayout`。 默认值`Basis`属性是静态属性`FlexBasis.Auto`，这意味着子的请求使用宽度或高度。

在代码中，可以设置`Basis`属性`Label`名为`label`到 40 个与设备无关单位如下：

```csharp
FlexLayout.SetBasis(label, new FlexBasis(40, false));
```

第二个参数`FlexBasis`名为构造函数`isRelative`，指示是否为相对大小 (`true`) 或绝对 (`false`)。 自变量具有默认值为`false`，因此还可以使用以下代码：

```csharp
FlexLayout.SetBasis(label, new FlexBasis(40));
```

隐式转换`float`到`FlexBasis`定义，因此您可以进一步简化：

```csharp
FlexLayout.SetBasis(label, 40);
```

可以将大小设置为 25%的`FlexLayout`父如下：

```csharp
FlexLayout.SetBasis(label, new FlexBasis(0.25f, true));
```

此小数部分的值必须是 0 到 1 范围内。

在 XAML，您可以使用设备无关的单位中的大小数字：

```xaml
<Label ... FlexLayout.Basis="40" ... />
```

也可以在范围 0%到 100%中指定百分比：

```xaml
<Label ... FlexLayout.Basis="25%" ... />
```

**基础试验** 页 **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** 示例允许用户体验与`Basis`属性。 该页面显示的列已包装的五个`Label`交替背景和前景颜色的元素。 两个`Slider`元素允许您指定`Basis`第二个和第四个值`Label`:

[![基础试验页](flex-layout-images/BasisExperiment.png "基础试验页")](flex-layout-images/BasisExperiment-Large.png#lightbox)

在左侧的 iOS 屏幕截图显示了这两个`Label`元素获得高度以与设备无关单位。 Android 屏幕显示它们被授予的总高度的一小部分的高度`FlexLayout`。 如果设置`FlexLayout`为 100%，则子级为的高度，将换到下一列并占用该列的整个高度，如 UWP 屏幕截图所示： `Basis`它看起来就像这五个子级排列在一行中，但实际上是按5列排列的。

### <a name="the-grow-property"></a>扩展属性

[ `Grow` ](xref:Xamarin.Forms.FlexLayout.GrowProperty)附加可绑定属性属于类型`int`。 默认值为 0，并且值必须大于或等于 0。

`Grow`属性的作用时`Wrap`属性设置为`NoWrap`的子行具有的宽度小于的总宽度`FlexLayout`，或子级的列具有比短高度`FlexLayout`。 `Grow`属性指示如何将子级之间剩余的空间分配。

在中**增长实验**页上，五个`Label`交替颜色的元素排列在列和两个`Slider`元素，可以调整`Grow`属性的第二个和第四个`Label`。 在最左侧的 iOS 屏幕快照显示的默认`Grow`0 的属性：

[![Grow 实验页](flex-layout-images/GrowExperiment.png "Grow 实验页")](flex-layout-images/GrowExperiment-Large.png#lightbox)

如果未指定任何一个子正`Grow`值，则该子级占用的所有剩余的空间，如 Android 屏幕截图中所示。 此外可以在两个或多个子级之间分配此空间。 在 UWP 屏幕截图中，`Grow`属性的第二个`Label`设置为 0.5，而`Grow`的第四个属性`Label`1.5，这将使第四个`Label`三倍的第二个将剩余的空间`Label`.

子视图如何使用该空间取决于特定的子类型。 有关`Label`，可将文本放置的总空间内`Label`使用的属性`HorizontalTextAlignment`和`VerticalTextAlignment`。

<a name="shrink" />

### <a name="the-shrink-property"></a>收缩属性

[ `Shrink` ](xref:Xamarin.Forms.FlexLayout.ShrinkProperty)附加可绑定属性属于类型`int`。 默认值为 1，并且值必须大于或等于 0。

`Shrink`属性的作用时`Wrap`属性设置为`NoWrap`和聚合的子行的宽度大于宽度的`FlexLayout`，或子级的单个列的聚合高度大于高度`FlexLayout`。 通常情况下`FlexLayout`时将显示这些子级又会限制运行它们的大小。 `Shrink`属性可以指示哪些子活动优先级中显示在其完整大小。

**收缩实验**页上创建`FlexLayout`具有五个单行`Label`需要更多空间比多个子`FlexLayout`宽度。 在左侧的 iOS 屏幕截图显示了所有`Label`具有默认值为 1 的元素：

[![收缩试验页](flex-layout-images/ShrinkExperiment.png "收缩试验页")](flex-layout-images/ShrinkExperiment-Large.png#lightbox)

在 Android 屏幕截图中，`Shrink`第二个值`Label`设为 0，并且`Label`显示在其整个宽度。 此外，第四个`Label`给定`Shrink`值大于 1，并已收缩。 UWP 屏幕截图显示了这两`Label`元素被授予`Shrink`是可能的值为 0 以允许它们显示在其完整大小，如果该。

您可以同时设置`Grow`并`Shrink`值以容纳其中的聚合子大小可能有时会更少或有时大于的大小的情况下`FlexLayout`。

## <a name="css-styling-with-flexlayout"></a>CSS 样式设定 FlexLayout

可以使用[CSS 样式](~/xamarin-forms/user-interface/styles/css/index.md)门户中的 Xamarin.Forms 3.0 中引入了功能`FlexLayout`。 **CSS 目录项** 页 **[FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)** 示例重复项的布局 **目录项** 页上，但使用 CSS对于许多样式的样式表：

[![CSS 目录项页](flex-layout-images/CssCatalogItems.png "CSS 目录项页")](flex-layout-images/CssCatalogItems-Large.png#lightbox)

原始**CatalogItemsPage.xaml**文件具有五个`Style`中的定义及其`Resources`15 部分`Setter`对象。 在中**CssCatalogItemsPage.xaml**文件，已缩减成两个`Style`定义具有只需四个`Setter`对象。 这些样式补充 Xamarin.Forms CSS 样式功能当前不支持的属性的 CSS 样式表：

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

在第一行中引用的 CSS 样式表`Resources`部分：

```xaml
<StyleSheet Source="CatalogItemsStyles.css" />
```

请注意，还在每个的三个项目中的两个元素，包括`StyleClass`设置：

```xaml
<Label Text="Seated Monkey" StyleClass="header" />
···
<Label StyleClass="empty" />
```

这些是指中的选择器**CatalogItemsStyles.css**样式表：

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

多个`FlexLayout`此处引用附加可绑定属性。 在`label.empty`选择器中，你将看到`flex-grow`属性设置为空的样式`Label`若要提供上述一些空白空间`Button`。 `image`选择器将包含`order`属性和一个`align-self`属性，这两个对应于`FlexLayout`附加可绑定属性。

您已了解你可以直接在上设置属性`FlexLayout`您可以设置附加可绑定属性的子级和`FlexLayout`。 或者，可以设置这些属性间接使用传统的基于 XAML 的样式或 CSS 样式。 重要的是了解以及了解这些属性。 这些属性是使`FlexLayout`真正灵活。

## <a name="flexlayout-with-xamarinuniversity"></a>使用 Xamarin.University FlexLayout

> [!VIDEO https://youtube.com/embed/Ng3sel_5D_0]

**Xamarin 3.0 Flex 布局视频**

## <a name="related-links"></a>相关链接

- [FlexLayoutDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-flexlayoutdemos)
