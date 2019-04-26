---
title: Xamarin.Forms 字数
description: 本文介绍如何使用一个有色的矩形进行修饰、 图形和 Xamarin.Forms 应用程序中的交互。
ms.prod: xamarin
ms.assetid: 4CBF703D-84A0-4CDF-A433-5926B587782A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/26/2018
ms.openlocfilehash: 85edbf657382b7c85ab2c5af543431fb51fb0d4e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61019513"
---
# <a name="xamarinforms-boxview"></a>Xamarin.Forms 字数

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BasicBoxView)

[`BoxView`](xref:Xamarin.Forms.BoxView) 呈现指定的宽度、 高度和颜色的一个简单的矩形。 可以使用`BoxView`修饰，基本图形，以及与用户通过触摸交互。

因为 Xamarin.Forms 不具有内置的向量图形系统`BoxView`可帮助进行补偿。 此文章使用中所述的示例程序的一些`BoxView`呈现图形。 `BoxView`可以调整大小以类似于特定的宽度和粗细的行，然后通过任何角度旋转`Rotation`属性。

尽管`BoxView`可以模仿简单的图形，你可能想要调查[Xamarin.Forms 中使用 SkiaSharp](~/xamarin-forms/user-interface/graphics/skiasharp/index.md)更复杂的图形要求。

本文讨论以下主题：

- **[设置字数颜色和大小](#colorandsize)** &ndash;设置`BoxView`属性。
- **[呈现的文本修饰](#textdecorations)** &ndash;使用`BoxView`呈现线条。
- **[列出的颜色的字数](#listingcolors)** &ndash;显示所有系统中的颜色`ListView`。
- **[播放游戏的生命周期由子类化字数](#subclassing)** &ndash;实现著名的移动电话自动机。
- **[创建数字时钟](#digitalclock)** &ndash;模拟点矩阵显示。
- **[创建模拟时钟](#analogclock)** &ndash;转换并进行动画处理`BoxView`元素。

<a name="colorandsize" />

## <a name="setting-boxview-color-and-size"></a>设置字数颜色和大小

通常将设置以下属性的`BoxView`:

- [`Color`](xref:Xamarin.Forms.BoxView.Color) 若要设置其颜色。
- [`CornerRadius`](xref:Xamarin.Forms.BoxView.CornerRadius) 若要设置其圆角半径。
- [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 若要设置的宽度`BoxView`以与设备无关单位。
- [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 若要设置的高度`BoxView`。

`Color`属性属于类型`Color`; 的属性可以设置为任何`Color`值，其中包括 141 静态只读字段的已命名的颜色范围为按字母顺序`AliceBlue`到`YellowGreen`。

`CornerRadius`属性属于类型[ `CornerRadius` ](xref:Xamarin.Forms.CornerRadius); 的属性可以设置为单个`double`统一角半径值，或`CornerRadius`结构定义的四个`double`应用到的值左上、 右上、 左下角和右下角`BoxView`。

`WidthRequest`并`HeightRequest`属性只能播放一个角色，如果`BoxView`是*不受约束*布局中。 这种情况时的布局容器需要知道子的大小，例如，当`BoxView`中的自动调整大小单元格的子`Grid`布局。 一个`BoxView`也是不受约束时其`HorizontalOptions`并`VerticalOptions`属性设置为值而不`LayoutOptions.Fill`。 如果`BoxView`是不受约束，但`WidthRequest`和`HeightRequest`属性未设置，然后宽度或高度设置为 40 单位，或大约 1/4 英寸的移动设备上的默认值。

`WidthRequest`并`HeightRequest`属性将被忽略，如果`BoxView`是*约束*在布局中，用例的布局容器对其自身的大小施加`BoxView`。

一个`BoxView`可以被限制在一个维度和其他不受约束。 例如，如果`BoxView`是垂直的子级`StackLayout`，垂直维度的`BoxView`是不受约束，且其水平维度通常被约束。 但有该水平维度的例外情况：如果`BoxView`具有其`HorizontalOptions`属性设置为内容而不`LayoutOptions.Fill`，也不受约束的水平维度则。 也可能是`StackLayout`本身可以具有不受约束的水平维度，在这种情况下`BoxView`也将水平方向不受约束。

[ **BasicBoxView** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BasicBoxView)示例将显示一英寸的正方形不受约束`BoxView`在其页面的中心：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:BasicBoxView"
             x:Class="BasicBoxView.MainPage">

    <BoxView Color="CornflowerBlue"
             CornerRadius="10"
             WidthRequest="160"
             HeightRequest="160"
             VerticalOptions="Center"
             HorizontalOptions="Center" />

</ContentPage>
```

下面是结果：

[![基本字数](boxview-images/basicboxview-small.png "基本字数")](boxview-images/basicboxview-large.png#lightbox "BasicBoxView")

如果`VerticalOptions`并`HorizontalOptions`属性都将从中`BoxView`标记，或者被设置为`Fill`，则`BoxView`变得受页的大小和扩展以填充页。

一个`BoxView`也可以是子的`AbsoluteLayout`。 在此情况下，位置和大小`BoxView`使用设置`LayoutBounds`附加可绑定属性。 `AbsoluteLayout`一文中讨论[ **AbsoluteLayout**](~/xamarin-forms/user-interface/layouts/absolute-layout.md)。

您将看到的示例程序，请按照中的所有这些情况下的示例。

<a name="textdecorations" />

## <a name="rendering-text-decorations"></a>呈现的文本修饰

可以使用`BoxView`形式的水平线和垂直线页面上添加一些简单装饰物。 [ **TextDecoration** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/TextDecoration)示例演示了这。 所有程序的视觉对象中定义**MainPage.xaml**文件，其中包含多个`Label`并`BoxView`中的元素`StackLayout`如下所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:TextDecoration"
             x:Class="TextDecoration.MainPage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>

    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="BoxView">
                <Setter Property="Color" Value="Black" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <ScrollView Margin="15">
        <StackLayout>

            ···

        </StackLayout>
    </ScrollView>
</ContentPage>
```

所有遵循的标记都是子级`StackLayout`。 此标记由几种类型的装饰性`BoxView`元素用于`Label`元素：

[![文本修饰](boxview-images/textdecoration-small.png "文本修饰")](boxview-images/textdecoration-large.png#lightbox "文本修饰")

在页面顶部的时尚标头，可以使用`AbsoluteLayout`其子级是四个`BoxView`元素和一个`Label`，则所有这些选项均进行分配的特定位置和大小：

```xaml
<AbsoluteLayout>
    <BoxView AbsoluteLayout.LayoutBounds="0, 10, 200, 5" />
    <BoxView AbsoluteLayout.LayoutBounds="0, 20, 200, 5" />
    <BoxView AbsoluteLayout.LayoutBounds="10, 0, 5, 65" />
    <BoxView AbsoluteLayout.LayoutBounds="20, 0, 5, 65" />
    <Label Text="Stylish Header"
           FontSize="24"
           AbsoluteLayout.LayoutBounds="30, 25, AutoSize, AutoSize"/>
</AbsoluteLayout>
```

在 XAML 文件中，`AbsoluteLayout`后跟`Label`使用的格式化文本描述`AbsoluteLayout`。

可以通过封闭同时添加下划线的文本字符串`Label`并`BoxView`中`StackLayout`具有其`HorizontalOptions`值设置为内容而不`Fill`。 宽度`StackLayout`然后受到的宽度`Label`，后者随后实施该宽度`BoxView`。 `BoxView`分配仅显式高度：

```xaml
<StackLayout HorizontalOptions="Center">
    <Label Text="Underlined Text"
           FontSize="24" />
    <BoxView HeightRequest="2" />
</StackLayout>
```

不能使用此方法以用下划线标出较长文本字符串或一个段落中的各个单词。

它也是可以使用`BoxView`类似于 HTML `hr` （水平标尺） 元素。 只需让的宽度`BoxView`由其父容器，在这种情况下是`StackLayout`:

```xaml
<BoxView HeightRequest="3" />
```

最后，您可以绘制一条竖线一侧的文本的段落括两者`BoxView`并`Label`水平`StackLayout`。 在本例中为的高度`BoxView`的高度相同`StackLayout`，其中所依据的高度`Label`:

```xaml
<StackLayout Orientation="Horizontal">
    <BoxView WidthRequest="4"
             Margin="0, 0, 10, 0" />
    <Label>

        ···

    </Label>
</StackLayout>
```
<a name="listingcolors" />

## <a name="listing-colors-with-boxview"></a>列出的颜色的字数

`BoxView`便于在显示颜色。 此程序使用`ListView`若要列出所有的公共静态只读字段的 Xamarin.Forms`Color`结构：

[![ListView 颜色](boxview-images/listviewcolors-small.png "ListView 颜色")](boxview-images/listviewcolors-large.png#lightbox "ListView 颜色")

[ **ListViewColors** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/ListViewColors/)程序包括一个名为类`NamedColor`。 静态构造函数使用反射访问的所有字段`Color`结构，并创建`NamedColor`为每个对象。 它们存储在静态`All`属性：

```csharp
public class NamedColor
{
    // Instance members.
    private NamedColor()
    {
    }

    public string Name { private set; get; }

    public string FriendlyName { private set; get; }

    public Color Color { private set; get; }

    public string RgbDisplay { private set; get; }

    // Static members.
    static NamedColor()
    {
        List<NamedColor> all = new List<NamedColor>();
        StringBuilder stringBuilder = new StringBuilder();

        // Loop through the public static fields of the Color structure.
        foreach (FieldInfo fieldInfo in typeof(Color).GetRuntimeFields ())
        {
            if (fieldInfo.IsPublic &&
                fieldInfo.IsStatic &&
                fieldInfo.FieldType == typeof (Color))
            {
                // Convert the name to a friendly name.
                string name = fieldInfo.Name;
                stringBuilder.Clear();
                int index = 0;

                foreach (char ch in name)
                {
                    if (index != 0 && Char.IsUpper(ch))
                    {
                        stringBuilder.Append(' ');
                    }
                    stringBuilder.Append(ch);
                    index++;
                }

                // Instantiate a NamedColor object.
                Color color = (Color)fieldInfo.GetValue(null);

                NamedColor namedColor = new NamedColor
                {
                    Name = name,
                    FriendlyName = stringBuilder.ToString(),
                    Color = color,
                    RgbDisplay = String.Format("{0:X2}-{1:X2}-{2:X2}",
                                               (int)(255 * color.R),
                                               (int)(255 * color.G),
                                               (int)(255 * color.B))
                };

                // Add it to the collection.
                all.Add(namedColor);
            }
        }
        all.TrimExcess();
        All = all;
    }

    public static IList<NamedColor> All { private set; get; }
}
```

XAML 文件中描述了程序视觉对象。 `ItemsSource`的属性`ListView`设置为静态`NamedColor.All`属性，这意味着`ListView`显示所有单个`NamedColor`对象：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ListViewColors"
             x:Class="ListViewColors.MainPage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="10, 20, 10, 0" />
            <On Platform="Android, UWP" Value="10, 0" />
        </OnPlatform>
    </ContentPage.Padding>

    <ListView SeparatorVisibility="None"
              ItemsSource="{x:Static local:NamedColor.All}">
        <ListView.RowHeight>
            <OnPlatform x:TypeArguments="x:Int32">
                <On Platform="iOS, Android" Value="80" />
                <On Platform="UWP" Value="90" />
            </OnPlatform>
        </ListView.RowHeight>

        <ListView.ItemTemplate>
            <DataTemplate>
                <ViewCell>
                    <ContentView Padding="5">
                        <Frame OutlineColor="Accent"
                               Padding="10">
                            <StackLayout Orientation="Horizontal">
                                <BoxView Color="{Binding Color}"
                                         WidthRequest="50"
                                         HeightRequest="50" />
                                <StackLayout>
                                    <Label Text="{Binding FriendlyName}"
                                           FontSize="22"
                                           VerticalOptions="StartAndExpand" />
                                    <Label Text="{Binding RgbDisplay, StringFormat='RGB = {0}'}"
                                           FontSize="16"
                                           VerticalOptions="CenterAndExpand" />
                                </StackLayout>
                            </StackLayout>
                        </Frame>
                    </ContentView>
                </ViewCell>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</ContentPage>
```

`NamedColor`对象的格式由`ViewCell`对象，它将设置为的数据模板`ListView`。 此模板包括`BoxView`其`Color`属性绑定到`Color`属性的`NamedColor`对象。

<a name="subclassing" />

## <a name="playing-the-game-of-life-by-subclassing-boxview"></a>玩游戏通过子类化字数生命

游戏的生命周期是移动电话自动机由数学知识的人 John Conway 发明和的页中在那时推广*科学记数法美国*在二十世纪七十年代。 维基百科文章提供详细的介绍[Conway 游戏的生活](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life)。

Xamarin.Forms [ **GameOfLife** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/GameOfLife/)程序定义一个名为`LifeCell`派生`BoxView`。 此类封装的逻辑的游戏的生命周期中的单个单元格：

```csharp
class LifeCell : BoxView
{
    bool isAlive;

    public event EventHandler Tapped;

    public LifeCell()
    {
        BackgroundColor = Color.White;

        TapGestureRecognizer tapGesture = new TapGestureRecognizer();
        tapGesture.Tapped += (sender, args) =>
        {
            Tapped?.Invoke(this, EventArgs.Empty);
        };
        GestureRecognizers.Add(tapGesture);
    }

    public int Col { set; get; }

    public int Row { set; get; }

    public bool IsAlive
    {
        set
        {
            if (isAlive != value)
            {
                isAlive = value;
                BackgroundColor = isAlive ? Color.Black : Color.White;
            }
        }
        get
        {
            return isAlive;
        }
    }
}
```

`LifeCell` 将添加到其他三个属性`BoxView`:`Col`并`Row`属性存储在网格内单元格的位置和`IsAlive`属性指示其状态。 `IsAlive`属性还会设置`Color`属性的`BoxView`为黑色单元格是否处于活动状态，和白色如果该单元格不处于活动状态。

`LifeCell` 此外会安装`TapGestureRecognizer`以允许用户在点击它们切换单元格的状态。 类将`Tapped`事件到其自己的手势识别器从`Tapped`事件。

**GameOfLife**程序还包括`LifeGrid`封装的逻辑的游戏时，大部分的类和一个`MainPage`处理程序的视觉对象的类。 其中包括介绍游戏的规则的覆盖。 下面是该程序中显示几个量为几百`LifeCell`页上的对象：

[![游戏生命](boxview-images/gameoflife-small.png "游戏生命")](boxview-images/gameoflife-large.png#lightbox "生命的游戏")

<a name="digitalclock" />

## <a name="creating-a-digital-clock"></a>创建数字时钟

[ **DotMatrixClock** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/DotMatrixClock/)程序创建 210`BoxView`元素，以模拟老式的 5 到 7 点阵显示的点。 可以读取在纵向或横向模式下的时间，但它是在环境中更大：

[![点式时钟](boxview-images/dotmatrixclock-small.png "点阵时钟")](boxview-images/dotmatrixclock-large.png#lightbox "点阵时钟")

XAML 文件 does 稍有多个实例化`AbsoluteLayout`用于时钟：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DotMatrixClock"
             x:Class="DotMatrixClock.MainPage"
             Padding="10"
             SizeChanged="OnPageSizeChanged">

    <AbsoluteLayout x:Name="absoluteLayout"
                    VerticalOptions="Center" />
</ContentPage>
```

所有其他代码隐藏文件中出现。 圆点矩阵显示逻辑大大简化了描述对应于每个 10 位数字和一个冒号的点的多个数组的定义：

```csharp
public partial class MainPage : ContentPage
{
    // Total dots horizontally and vertically.
    const int horzDots = 41;
    const int vertDots = 7;

    // 5 x 7 dot matrix patterns for 0 through 9.
    static readonly int[, ,] numberPatterns = new int[10, 7, 5]
    {
        {
            { 0, 1, 1, 1, 0}, { 1, 0, 0, 0, 1}, { 1, 0, 0, 1, 1}, { 1, 0, 1, 0, 1},
            { 1, 1, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 0, 1, 0, 0}, { 0, 1, 1, 0, 0}, { 0, 0, 1, 0, 0}, { 0, 0, 1, 0, 0},
            { 0, 0, 1, 0, 0}, { 0, 0, 1, 0, 0}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 1, 1, 1, 0}, { 1, 0, 0, 0, 1}, { 0, 0, 0, 0, 1}, { 0, 0, 0, 1, 0},
            { 0, 0, 1, 0, 0}, { 0, 1, 0, 0, 0}, { 1, 1, 1, 1, 1}
        },
        {
            { 1, 1, 1, 1, 1}, { 0, 0, 0, 1, 0}, { 0, 0, 1, 0, 0}, { 0, 0, 0, 1, 0},
            { 0, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 0, 0, 1, 0}, { 0, 0, 1, 1, 0}, { 0, 1, 0, 1, 0}, { 1, 0, 0, 1, 0},
            { 1, 1, 1, 1, 1}, { 0, 0, 0, 1, 0}, { 0, 0, 0, 1, 0}
        },
        {
            { 1, 1, 1, 1, 1}, { 1, 0, 0, 0, 0}, { 1, 1, 1, 1, 0}, { 0, 0, 0, 0, 1},
            { 0, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 0, 1, 1, 0}, { 0, 1, 0, 0, 0}, { 1, 0, 0, 0, 0}, { 1, 1, 1, 1, 0},
            { 1, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 1, 1, 1, 1, 1}, { 0, 0, 0, 0, 1}, { 0, 0, 0, 1, 0}, { 0, 0, 1, 0, 0},
            { 0, 1, 0, 0, 0}, { 0, 1, 0, 0, 0}, { 0, 1, 0, 0, 0}
        },
        {
            { 0, 1, 1, 1, 0}, { 1, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0},
            { 1, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 0}
        },
        {
            { 0, 1, 1, 1, 0}, { 1, 0, 0, 0, 1}, { 1, 0, 0, 0, 1}, { 0, 1, 1, 1, 1},
            { 0, 0, 0, 0, 1}, { 0, 0, 0, 1, 0}, { 0, 1, 1, 0, 0}
        },
    };

    // Dot matrix pattern for a colon.
    static readonly int[,] colonPattern = new int[7, 2]
    {
        { 0, 0 }, { 1, 1 }, { 1, 1 }, { 0, 0 }, { 1, 1 }, { 1, 1 }, { 0, 0 }
    };

    // BoxView colors for on and off.
    static readonly Color colorOn = Color.Red;
    static readonly Color colorOff = new Color(0.5, 0.5, 0.5, 0.25);

    // Box views for 6 digits, 7 rows, 5 columns.
    BoxView[, ,] digitBoxViews = new BoxView[6, 7, 5];

    ···

}
```

这些字段演示的三维数组的最后`BoxView`元素用于存储六位数字的圆点模式。

该构造函数创建所有`BoxView`数字和冒号，以及初始化元素`Color`属性的`BoxView`冒号的元素：

```csharp
public partial class MainPage : ContentPage
{

    ···

    public MainPage()
    {
        InitializeComponent();

        // BoxView dot dimensions.
        double height = 0.85 / vertDots;
        double width = 0.85 / horzDots;

        // Create and assemble the BoxViews.
        double xIncrement = 1.0 / (horzDots - 1);
        double yIncrement = 1.0 / (vertDots - 1);
        double x = 0;

        for (int digit = 0; digit < 6; digit++)
        {
            for (int col = 0; col < 5; col++)
            {
                double y = 0;

                for (int row = 0; row < 7; row++)
                {
                    // Create the digit BoxView and add to layout.
                    BoxView boxView = new BoxView();
                    digitBoxViews[digit, row, col] = boxView;
                    absoluteLayout.Children.Add(boxView,
                                                new Rectangle(x, y, width, height),
                                                AbsoluteLayoutFlags.All);
                    y += yIncrement;
                }
                x += xIncrement;
            }
            x += xIncrement;

            // Colons between the hours, minutes, and seconds.
            if (digit == 1 || digit == 3)
            {
                int colon = digit / 2;

                for (int col = 0; col < 2; col++)
                {
                    double y = 0;

                    for (int row = 0; row < 7; row++)
                    {
                        // Create the BoxView and set the color.
                        BoxView boxView = new BoxView
                            {
                                Color = colonPattern[row, col] == 1 ?
                                            colorOn : colorOff
                            };
                        absoluteLayout.Children.Add(boxView,
                                                    new Rectangle(x, y, width, height),
                                                    AbsoluteLayoutFlags.All);
                        y += yIncrement;
                    }
                    x += xIncrement;
                }
                x += xIncrement;
            }
        }

        // Set the timer and initialize with a manual call.
        Device.StartTimer(TimeSpan.FromSeconds(1), OnTimer);
        OnTimer();
    }

    ···

}
```

此程序使用的相对定位和大小调整功能`AbsoluteLayout`。 宽度和高度的每个`BoxView`设置为小数值，专门 85%的 1 除以水平和垂直点数目。 位置也都设置为小数部分值。

因为所有位置和大小都都相对于总大小`AbsoluteLayout`，则`SizeChanged`页的处理程序只需要设置`HeightRequest`的`AbsoluteLayout`:

```csharp
public partial class MainPage : ContentPage
{

    ···

    void OnPageSizeChanged(object sender, EventArgs args)
    {
        // No chance a display will have an aspect ratio > 41:7
        absoluteLayout.HeightRequest = vertDots * Width / horzDots;
    }

    ···

}
```

宽度`AbsoluteLayout`自动设置，因为它拉伸到整个页面的宽度。

中的最终代码`MainPage`类处理计时器回调和颜色的每个数字的点。 在代码隐藏文件开头多维数组的定义可帮助使此逻辑的程序的最简单的一部分：

```csharp
public partial class MainPage : ContentPage
{

    ···

    bool OnTimer()
    {
        DateTime dateTime = DateTime.Now;

        // Convert 24-hour clock to 12-hour clock.
        int hour = (dateTime.Hour + 11) % 12 + 1;

        // Set the dot colors for each digit separately.
        SetDotMatrix(0, hour / 10);
        SetDotMatrix(1, hour % 10);
        SetDotMatrix(2, dateTime.Minute / 10);
        SetDotMatrix(3, dateTime.Minute % 10);
        SetDotMatrix(4, dateTime.Second / 10);
        SetDotMatrix(5, dateTime.Second % 10);
        return true;
    }

    void SetDotMatrix(int index, int digit)
    {
        for (int row = 0; row < 7; row++)
            for (int col = 0; col < 5; col++)
            {
                bool isOn = numberPatterns[digit, row, col] == 1;
                Color color = isOn ? colorOn : colorOff;
                digitBoxViews[index, row, col].Color = color;
            }
    }
}
```
<a name="analogclock" />

## <a name="creating-an-analog-clock"></a>创建模拟时钟

点式时钟可能看起来是显而易见的应用程序`BoxView`，但`BoxView`元素也是能够意识到了模拟时钟：

[![字数时钟](boxview-images/boxviewclock-small.png "字数时钟")](boxview-images/boxviewclock-large.png#lightbox "字数时钟")

中的所有视觉对象[ **BoxViewClock** ](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BoxViewClock/)程序是的子级`AbsoluteLayout`。 这些元素的大小将调整使用`LayoutBounds`附加属性，并使用旋转`Rotation`属性。

这三个`BoxView`时钟指针的元素在 XAML 文件中，实例化但未定位或调整大小：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:BoxViewClock"
             x:Class="BoxViewClock.MainPage">
    <ContentPage.Padding>
        <OnPlatform x:TypeArguments="Thickness">
            <On Platform="iOS" Value="0, 20, 0, 0" />
        </OnPlatform>
    </ContentPage.Padding>

    <AbsoluteLayout x:Name="absoluteLayout"
                    SizeChanged="OnAbsoluteLayoutSizeChanged">

        <BoxView x:Name="hourHand"
                 Color="Black" />

        <BoxView x:Name="minuteHand"
                 Color="Black" />

        <BoxView x:Name="secondHand"
                 Color="Black" />
    </AbsoluteLayout>
</ContentPage>
```

代码隐藏文件的构造函数实例化 60`BoxView`刻度线在时钟圆周的周围的元素：

```csharp
public partial class MainPage : ContentPage
{

    ···

    BoxView[] tickMarks = new BoxView[60];

    public MainPage()
    {
        InitializeComponent();

        // Create the tick marks (to be sized and positioned later).
        for (int i = 0; i < tickMarks.Length; i++)
        {
            tickMarks[i] = new BoxView { Color = Color.Black };
            absoluteLayout.Children.Add(tickMarks[i]);
        }

        Device.StartTimer(TimeSpan.FromSeconds(1.0 / 60), OnTimerTick);
    }

    ···

}
```

所有的位置和大小`BoxView`元素中出现`SizeChanged`处理程序`AbsoluteLayout`。 内部的类的一个小结构称为`HandParams`介绍每三个手中相对于时钟的总大小的大小：

```csharp
public partial class MainPage : ContentPage
{
    // Structure for storing information about the three hands.
    struct HandParams
    {
        public HandParams(double width, double height, double offset) : this()
        {
            Width = width;
            Height = height;
            Offset = offset;
        }

        public double Width { private set; get; }   // fraction of radius
        public double Height { private set; get; }  // ditto
        public double Offset { private set; get; }  // relative to center pivot
    }

    static readonly HandParams secondParams = new HandParams(0.02, 1.1, 0.85);
    static readonly HandParams minuteParams = new HandParams(0.05, 0.8, 0.9);
    static readonly HandParams hourParams = new HandParams(0.125, 0.65, 0.9);

    ···

 }
```

`SizeChanged`处理程序确定的中心和半径`AbsoluteLayout`，然后调整大小和定位 60`BoxView`用作刻度的元素。 `for`循环的结尾部分通过设置`Rotation`属性的每种`BoxView`元素。 在末尾`SizeChanged`处理程序，`LayoutHand`调用方法来调整大小和位置时钟三个指针：

```csharp
public partial class MainPage : ContentPage
{

    ···

    void OnAbsoluteLayoutSizeChanged(object sender, EventArgs args)
    {
        // Get the center and radius of the AbsoluteLayout.
        Point center = new Point(absoluteLayout.Width / 2, absoluteLayout.Height / 2);
        double radius = 0.45 * Math.Min(absoluteLayout.Width, absoluteLayout.Height);

        // Position, size, and rotate the 60 tick marks.
        for (int index = 0; index < tickMarks.Length; index++)
        {
            double size = radius / (index % 5 == 0 ? 15 : 30);
            double radians = index * 2 * Math.PI / tickMarks.Length;
            double x = center.X + radius * Math.Sin(radians) - size / 2;
            double y = center.Y - radius * Math.Cos(radians) - size / 2;
            AbsoluteLayout.SetLayoutBounds(tickMarks[index], new Rectangle(x, y, size, size));
            tickMarks[index].Rotation = 180 * radians / Math.PI;
        }

        // Position and size the three hands.
        LayoutHand(secondHand, secondParams, center, radius);
        LayoutHand(minuteHand, minuteParams, center, radius);
        LayoutHand(hourHand, hourParams, center, radius);
    }

    void LayoutHand(BoxView boxView, HandParams handParams, Point center, double radius)
    {
        double width = handParams.Width * radius;
        double height = handParams.Height * radius;
        double offset = handParams.Offset;

        AbsoluteLayout.SetLayoutBounds(boxView,
            new Rectangle(center.X - 0.5 * width,
                          center.Y - offset * height,
                          width, height));

        // Set the AnchorY property for rotations.
        boxView.AnchorY = handParams.Offset;
    }

    ···

}
```

`LayoutHand`方法大小和位置以垂直向上 12:00 位置点每个指针。 该方法末尾`AnchorY`属性设置为对应于时钟的中心的位置。 这表示旋转中心。

手轮换计时器回调函数中：

```csharp
public partial class MainPage : ContentPage
{

    ···

    bool OnTimerTick()
    {
        // Set rotation angles for hour and minute hands.
        DateTime dateTime = DateTime.Now;
        hourHand.Rotation = 30 * (dateTime.Hour % 12) + 0.5 * dateTime.Minute;
        minuteHand.Rotation = 6 * dateTime.Minute + 0.1 * dateTime.Second;

        // Do an animation for the second hand.
        double t = dateTime.Millisecond / 1000.0;

        if (t < 0.5)
        {
            t = 0.5 * Easing.SpringIn.Ease(t / 0.5);
        }
        else
        {
            t = 0.5 * (1 + Easing.SpringOut.Ease((t - 0.5) / 0.5));
        }

        secondHand.Rotation = 6 * (dateTime.Second + t);
        return true;
    }
}
```

第二个指针是略有不同处理：缓动函数的动画应用以使移动看上去机械，而不是平滑。 在每个时钟周期，第二个指针有点拉取，然后超过其目标。 此小段代码很向移动的真实性。

## <a name="conclusion"></a>结束语

`BoxView`可能看起来简单在第一次，而是作为你所见，它可以相当灵活多变，并且可以几乎重现的视觉效果通常可以仅使用矢量图形。 有关更复杂的图形，请查阅[Xamarin.Forms 中使用 SkiaSharp](~/xamarin-forms/user-interface/graphics/skiasharp/index.md)。


## <a name="related-links"></a>相关链接

- [基本字数 （示例）](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BasicBoxView)
- [文本修饰 （示例）](https://developer.xamarin.com/samples/xamarin-forms/BoxView/TextDecoration)
- [列表框颜色 （示例）](https://developer.xamarin.com/samples/xamarin-forms/BoxView/ColorListBox)
- [游戏的生命周期 （示例）](https://developer.xamarin.com/samples/xamarin-forms/BoxView/GameOfLife)
- [点式时钟 （示例）](https://developer.xamarin.com/samples/xamarin-forms/BoxView/DotMatrixClock)
- [字数时钟 （示例）](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BoxViewClock)
- [BoxView](xref:Xamarin.Forms.BoxView)
