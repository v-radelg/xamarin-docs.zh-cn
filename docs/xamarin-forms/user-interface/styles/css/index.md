---
title: 使用级联样式表 (CSS) 样式设置 Xamarin.Forms 应用
description: Xamarin.Forms 支持使用级联样式表 (CSS) 样式可视元素。
ms.prod: xamarin
ms.assetid: C89D57A6-DAB9-4C42-963F-26D67627DDC2
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 12/13/2018
---
# <a name="styling-xamarinforms-apps-using-cascading-style-sheets-css"></a>使用级联样式表 (CSS) 样式设置 Xamarin.Forms 应用

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/MonkeyAppCSS/)

_Xamarin.Forms 支持使用级联样式表 (CSS) 样式可视元素。_

Xamarin.Forms 应用程序可以使用 CSS 来设置样式。 样式表包含的规则，每个规则由一个或多个选择器和声明块组成的列表。 声明块包含在括号中，与每个声明，其中包含的属性、 一个冒号和一个值的声明的列表。 当有多个块中的声明时，以分号作为分隔符插入。 下面的代码示例显示了一些 Xamarin.Forms 符合 CSS:

```css
navigationpage {
    -xf-bar-background-color: lightgray;
}

^contentpage {
    background-color: lightgray;
}

#listView {
    background-color: lightgray;
}

stacklayout {
    margin: 20;
}

.mainPageTitle {
    font-style: bold;
    font-size: medium;
}

.mainPageSubtitle {
    margin-top: 15;
}

.detailPageTitle {
    font-style: bold;
    font-size: medium;
    text-align: center;
}

.detailPageSubtitle {
    text-align: center;
    font-style: italic;
}

listview image {
    height: 60;
    width: 60;
}

stacklayout>image {
    height: 200;
    width: 200;
}
```

在 Xamarin.Forms 中，可以分析和计算在运行时，而不是编译时，CSS 样式表和样式表是在使用重新分析。

> [!NOTE]
> 目前，所有可以实现的 XAML 样式的样式不能使用 CSS 执行。 但是，可以使用 XAML 样式来补充 CSS Xamarin.Forms 当前不支持的属性。 有关 XAML 样式的详细信息，请参阅[样式设置 Xamarin.Forms 应用使用 XAML 样式](~/xamarin-forms/user-interface/styles/xaml/index.md)。

[MonkeyAppCSS](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/MonkeyAppCSS/)示例演示了如何使用 CSS 来设置简单的应用程序的样式，并在下面的屏幕截图中所示：

[![使用 CSS 样式 MonkeyApp 主页](css-images/MonkeyAppMainPage.png "MonkeyApp 主页面的 CSS 样式")](css-images/MonkeyAppMainPage-Large.png#lightbox "MonkeyApp 主页面的 CSS 样式")

[![使用 CSS 样式 MonkeyApp 详细信息页](css-images/MonkeyAppDetailPage.png "MonkeyApp CSS 样式设置的详细信息页")](css-images/MonkeyAppDetailPage-Large.png#lightbox "MonkeyApp CSS 样式设置的详细信息页")

## <a name="consuming-a-style-sheet"></a>使用样式表

向解决方案添加样式表的过程如下所示：

1. 将空 CSS 文件添加到.NET Standard 库项目。
1. 设置的 CSS 文件的生成操作**EmbeddedResource**。

### <a name="loading-a-style-sheet"></a>加载样式表

有多种方法可用于加载样式表。

### <a name="xaml"></a>XAML

样式表可以加载和分析[ `StyleSheet` ](xref:Xamarin.Forms.StyleSheets.StyleSheet)类，然后添加到[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary):

```xaml
<Application ...>
    <Application.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </Application.Resources>
</Application>
```

[ `StyleSheet.Source` ](xref:Xamarin.Forms.Xaml.StyleSheetExtension.Source)属性为相对于封闭的 XAML 文件的位置或相对于项目根的 URI 指定的样式表，如果 URI 开头`/`。

> [!WARNING]
> CSS 文件将无法加载如果未设置为其生成操作**EmbeddedResource**。

或者，可以加载和分析与样式表[ `StyleSheet` ](xref:Xamarin.Forms.StyleSheets.StyleSheet)类，然后再添加到[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)，也可由内联在`CDATA`部分：

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet>
            <![CDATA[
            ^contentpage {
                background-color: lightgray;
            }
            ]]>
        </StyleSheet>
    </ContentPage.Resources>
    ...
</ContentPage>
```

有关资源字典的详细信息，请参阅[资源字典](~/xamarin-forms/xaml/resource-dictionaries.md)。

### <a name="c"></a>C#

在C#，可以作为嵌入资源加载样式表，并将其添加到[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary):

```csharp
public partial class MyPage : ContentPage
{
    public MyPage()
    {
        InitializeComponent();

        this.Resources.Add(StyleSheet.FromAssemblyResource(
            IntrospectionExtensions.GetTypeInfo(typeof(MyPage)).Assembly,
            "MyProject.Assets.styles.css"));
    }
}
```

第一个参数`StyleSheet.FromAssemblyResource`方法是包含样式表的程序集，同时第二个参数是`string`表示的资源标识符。 可以从获取的资源标识符**属性**窗口选择 CSS 文件。

或者，从加载样式表`StringReader`并添加到[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary):

```csharp
public partial class MyPage : ContentPage
{
    public MyPage()
    {
        InitializeComponent();

        using (var reader = new StringReader("^contentpage { background-color: lightgray; }"))
        {
            this.Resources.Add(StyleSheet.FromReader(reader));
        }
    }
}
```

参数`StyleSheet.FromReader`方法是`TextReader`，它具有读取样式表。

## <a name="selecting-elements-and-applying-properties"></a>选择元素并将属性应用

CSS 使用选择器来确定要为目标的元素。 按定义顺序连续，应用具有匹配的选择器的样式。 始终最后应用在特定项目上定义的样式。 有关受支持的选择器的详细信息，请参阅[选择器引用](#selector-reference)。

CSS 使用属性来设置所选的元素的样式。 每个属性具有一系列可能的值，并且某些属性会影响任何类型的元素，而其他人将应用于组的元素。 有关支持的属性的详细信息，请参阅[属性引用](#property-reference)。

### <a name="selecting-elements-by-type"></a>选择按类型的元素

可视化树中的元素可以选择按类型对不区分大小写`element`选择器：

```css
stacklayout {
    margin: 20;
}
```

此选择器标识任何[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)的使用样式表，并将它们的边距设置为统一粗细，即 20 页上的元素。

> [!NOTE]
> `element`选择器不会确定指定类型的子类。

### <a name="selecting-elements-by-base-class"></a>由基类选择元素

可视化树中的元素可以选择通过使用不区分大小写的基类`^base`选择器：

```css
^contentpage {
    background-color: lightgray;
}
```

此选择器标识任何[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)元素使用的样式表，并设置其背景颜色`lightgray`。

> [!NOTE]
> `^base`选择器特定于 Xamarin.Forms 中，并且不是 CSS 规范的一部分。

### <a name="selecting-an-element-by-name"></a>按名称选择元素

可视化树中的单个元素可以选择使用区分大小写`#id`选择器：

```css
#listView {
    background-color: lightgray;
}
```

此选择器标识的元素的[ `StyleId` ](xref:Xamarin.Forms.Element.StyleId)属性设置为`listView`。 但是，如果`StyleId`属性未设置，则选择器将回退到使用`x:Name`的元素。 因此，在下面的 XAML 示例中，`#listView`选择器将标识[ `ListView` ](xref:Xamarin.Forms.ListView)其`x:Name`属性设置为`listView`，并将背景色设置为`lightgray`。

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    <StackLayout>
        <ListView x:Name="listView" ...>
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

### <a name="selecting-elements-with-a-specific-class-attribute"></a>选择具有特定的类属性的元素

具有特定的类属性的元素可以选择使用区分大小写`.class`选择器：

```css
.detailPageTitle {
    font-style: bold;
    font-size: medium;
    text-align: center;
}

.detailPageSubtitle {
    text-align: center;
    font-style: italic;
}
```

CSS 类可以通过设置分配给 XAML 元素[ `StyleClass` ](xref:Xamarin.Forms.VisualElement.StyleClass)的元素的 CSS 类名称的属性。 因此，在下面的 XAML 示例中，定义了样式通过`.detailPageTitle`分配给第一个类[ `Label` ](xref:Xamarin.Forms.Label)，而定义的样式`.detailPageSubtitle`类都被分配给第二个`Label`。

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    <ScrollView>
        <StackLayout>
            <Label ... StyleClass="detailPageTitle" />
            <Label ... StyleClass="detailPageSubtitle"/>
            ...
        </StackLayout>
    </ScrollView>
</ContentPage>
```

### <a name="selecting-child-elements"></a>选择子元素

可视化树中的子元素可以选择使用不区分大小写`element element`选择器：

```css
listview image {
    height: 60;
    width: 60;
}
```

此选择器标识任何[ `Image` ](xref:Xamarin.Forms.Image)元素的子级[ `ListView` ](xref:Xamarin.Forms.ListView)元素，并将其高度和宽度设置为 60。 因此，在下面的 XAML 示例中，`listview image`选择器将标识[ `Image` ](xref:Xamarin.Forms.Image)是的子级[ `ListView` ](xref:Xamarin.Forms.ListView)，并将其高度和宽度设置为 60。

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    <StackLayout>
        <ListView ...>
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <Grid>
                            ...
                            <Image ... />
                            ...
                        </Grid>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

> [!NOTE]
> `element element`选择器不需要子元素是_直接_父-子元素的子级可能有一个不同的父级。 提供的该祖先是指定的第一个元素，则发生所选内容。

### <a name="selecting-direct-child-elements"></a>选择直接子元素

直接在可视化树中的子元素可以选择使用不区分大小写`element>element`选择器：

```css
stacklayout>image {
    height: 200;
    width: 200;
}
```

此选择器标识任何[ `Image` ](xref:Xamarin.Forms.Image)元素的直接子级[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)元素，并将其高度和宽度设置为 200。 因此，在下面的 XAML 示例中，`stacklayout>image`选择器将标识[ `Image` ](xref:Xamarin.Forms.Image)是的直接子级[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)，并将其高度和宽度设置为 200。

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </ContentPage.Resources>
    <ScrollView>
        <StackLayout>
            ...
            <Image ... />
            ...
        </StackLayout>
    </ScrollView>
</ContentPage>
```

> [!NOTE]
> `element>element`选择器要求的子元素是_直接_的父级的子级。

## <a name="selector-reference"></a>选择器引用

Xamarin.Forms 支持以下 CSS 选择器：

|选择器|示例|描述|
|---|---|---|
|`.class`|`.header`|选择所有元素与`StyleClass`包含 header 属性。 请注意，此选择器区分大小写。|
|`#id`|`#email`|选择所有元素具有`StyleId`设置为`email`。 如果`StyleId`未设置，则回退到`x:Name`。 使用 XAML 时,`x:Name`最好通过`StyleId`。 请注意，此选择器区分大小写。|
|`*`|`*`|选择所有元素。|
|`element`|`label`|选择所有元素类型的`Label`，但不是子类。 请注意，此选择器不区分大小写。|
|`^base`|`^contentpage`|选择所有元素具有`ContentPage`作为基类，包括`ContentPage`本身。 请注意，此选择器是不区分大小写，不是 CSS 规范的一部分。|
|`element,element`|`label,button`|选择所有`Button`元素和所有`Label`元素。 请注意，此选择器不区分大小写。|
|`element element`|`stacklayout label`|选择所有`Label`内的元素`StackLayout`。 请注意，此选择器不区分大小写。|
|`element>element`|`stacklayout>label`|选择所有`Label`元素与`StackLayout`作为直接父级。 请注意，此选择器不区分大小写。|
|`element+element`|`label+entry`|选择所有`Entry`后的元素直接`Label`。 请注意，此选择器不区分大小写。|
|`element~element`|`label~entry`|选择所有`Entry`元素前面`Label`。 请注意，此选择器不区分大小写。|

按定义顺序连续，应用具有匹配的选择器的样式。 始终最后应用在特定项目上定义的样式。

> [!TIP]
> 选择器可以组合没有限制，如`StackLayout>ContentView>label.email`。

以下选择器是当前不受支持：

- `[attribute]`
- `@media` 和 `@supports`
- `:` 和 `::`

> [!NOTE]
> 特异性和特异性替代是不受支持。

## <a name="property-reference"></a>属性参考

通过 Xamarin.Forms 支持以下 CSS 属性 (在**值**列中，类型为_斜体_，而字符串文本是`gray`):

|属性|适用对象|值|示例|
|---|---|---|---|
|`align-content`|`FlexLayout`| `stretch` \| `center` \| `start` \| `end` \| `spacebetween` \| `spacearound` \| `spaceevenly` \| `flex-start` \| `flex-end` \| `space-between` \| `space-around` \| `initial` |`align-content: space-between;`|
|`align-items`|`FlexLayout`| `stretch` \| `center` \| `start` \| `end` \| `flex-start` \| `flex-end` \| `initial` |`align-items: flex-start;`|
|`align-self`|`VisualElement`| `auto` \| `stretch` \| `center` \| `start` \| `end` \| `flex-start` \| `flex-end` \| `initial`|`align-self: flex-end;`|
|`background-color`|`VisualElement`|_颜色_ \| `initial` |`background-color: springgreen;`|
|`background-image`|`Page`|_字符串_ \| `initial` |`background-image: bg.png;`|
|`border-color`|`Button`, `Frame`, `ImageButton`|_颜色_ \| `initial`|`border-color: #9acd32;`|
|`border-radius`|`BoxView`, `Button`, `Frame`, `ImageButton`|_双精度_ \| `initial` |`border-radius: 10;`|
|`border-width`|`Button`， `ImageButton`|_双精度_ \| `initial` |`border-width: .5;`|
|`color`|`ActivityIndicator`, `BoxView`, `Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `ProgressBar`, `SearchBar`, `Switch`, `TimePicker`|_颜色_ \| `initial` |`color: rgba(255, 0, 0, 0.3);`|
|`column-gap`|`Grid`|_双精度_ \| `initial`|`column-gap: 9;`|
|`direction`|`VisualElement`|`ltr` \| `rtl` \| `inherit` \| `initial` |`direction: rtl;`|
|`flex-direction`|`FlexLayout`| `column` \| `columnreverse` \| `row` \| `rowreverse` \| `row-reverse` \| `column-reverse` \| `initial`|`flex-direction: column-reverse;`|
|`flex-basis`|`VisualElement`|_float_ \| `auto` \| `initial`。 此外，使用指定的百分比在范围 0%到 100%`%`登录。|`flex-basis: 25%;`|
|`flex-grow`|`VisualElement`|_Float_ \| `initial`|`flex-grow: 1.5;`|
|`flex-shrink`|`VisualElement`|_Float_ \| `initial`|`flex-shrink: 1;`|
|`flex-wrap`|`VisualElement`| `nowrap` \| `wrap` \| `reverse` \| `wrap-reverse` \| `initial`|`flex-wrap: wrap-reverse;`|
|`font-family`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_字符串_ \| `initial` |`font-family: Consolas;`|
|`font-size`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|_双精度_\| _namedsize_ \| `initial` |`font-size: 12;`|
|`font-style`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `TimePicker`, `Span`|`bold` \| `italic` \| `initial` |`font-style: bold;`|
|`height`|`VisualElement`|_双精度_ \| `initial` |`min-height: 250;`|
|`justify-content`|`FlexLayout`| `start` \| `center` \| `end` \| `spacebetween` \| `spacearound` \| `spaceevenly` \| `flex-start` \| `flex-end` \| `space-between` \| `space-around` \| `initial`|`justify-content: flex-end;`|
|`line-height`|`Label`， `Span`|_双精度_ \| `initial` |`line-height: 1.8;`|
|`margin`|`View`|_粗细_ \| `initial` |`margin: 6 12;`|
|`margin-left`|`View`|_粗细_ \| `initial` |`margin-left: 3;`|
|`margin-top`|`View`|_粗细_ \| `initial` |`margin-top: 2;`|
|`margin-right`|`View`|_粗细_ \| `initial` |`margin-right: 1;`|
|`margin-bottom`|`View`|_粗细_ \| `initial` |`margin-bottom: 6;`|
|`max-lines`|`Label`|_Int_ \| `initial`|`max-lines: 2;`|
|`min-height`|`VisualElement`|_双精度_ \| `initial` |`min-height: 50;`|
|`min-width`|`VisualElement`|_双精度_ \| `initial` |`min-width: 112;`|
|`opacity`|`VisualElement`|_双精度_ \| `initial` |`opacity: .3;`|
|`order`|`VisualElement`|_Int_ \| `initial`|`order: -1;`|
|`padding`|`Button`, `ImageButton`, `Layout`, `Page`|_粗细_ \| `initial` |`padding: 6 12 12;`|
|`padding-left`|`Button`, `ImageButton`, `Layout`, `Page`|_双精度_ \| `initial`|`padding-left: 3;`|
|`padding-top`|`Button`, `ImageButton`, `Layout`, `Page`| _双精度_ \| `initial` |`padding-top: 4;`|
|`padding-right`|`Button`, `ImageButton`, `Layout`, `Page`| _双精度_ \| `initial` |`padding-right: 2;`|
|`padding-bottom`|`Button`, `ImageButton`, `Layout`, `Page`| _双精度_ \| `initial` |`padding-bottom: 6;`|
|`position`|`FlexLayout`| `relative` \| `absolute` \| `initial`|`position: absolute;`|
|`row-gap`|`Grid`| _双精度_ \| `initial`|`row-gap: 12;`|
|`text-align`| `Entry`, `EntryCell`, `Label`, `SearchBar`|`left` \| `top` \| `right` \| `bottom` \| `start` \| `center` \| `middle` \| `end` \| `initial`. `left` 和`right`应避免使用从右到左环境中。| `text-align: right;`|
|`text-decoration`|`Label`， `Span`|`none` \| `underline` \| `strikethrough` \| `line-through` \| `initial`|`text-decoration: underline, line-through;`|
|`transform`|`VisualElement`| `none`, `rotate`, `rotateX`, `rotateY`, `scale`, `scaleX`, `scaleY`, `translate`, `translateX`, `translateY`, `initial` |`transform: rotate(180), scaleX(2.5);`|
|`transform-origin`|`VisualElement`| _双精度_，_双精度_ \| `initial` |`transform-origin: 7.5, 12.5;`|
|`vertical-align`|`Label`|`left` \| `top` \| `right` \| `bottom` \| `start` \| `center` \| `middle` \| `end` \| `initial`|`vertical-align: bottom;`|
|`visibility`|`VisualElement`|`true` \| `visible` \| `false` \| `hidden` \| `collapse` \| `initial `|`visibility: hidden;`|
|`width`|`VisualElement`|_双精度_ \| `initial`|`min-width: 320;`|

此外支持以下 Xamarin.Forms 特定 CSS 属性 (在**值**列中，类型为_斜体_，而字符串文本是`gray`):

|属性|适用对象|值|示例|
|---|---|---|---|
|`-xf-placeholder`|`Entry`, `Editor`, `SearchBar`|_带引号的文本_ \| `initial` |`-xf-placeholder: Enter name;`|
|`-xf-placeholder-color`|`Entry`, `Editor`, `SearchBar`|_颜色_ \| `initial` |`-xf-placeholder-color: green;`|
|`-xf-max-length`|`Entry`， `Editor`|_Int_ \| `initial` |`-xf-max-length: 20;`|
|`-xf-bar-background-color`|`NavigationPage`， `TabbedPage`|_颜色_ \| `initial` |`-xf-bar-background-color: teal;`|
|`-xf-bar-text-color`|`NavigationPage`， `TabbedPage`|_颜色_ \| `initial` |`-xf-bar-text-color: gray`|
|`-xf-orientation`|`ScrollView`， `StackLayout`| `horizontal` \| `vertical` \| `both` \| `initial`. `both` 仅支持`ScrollView`。 |`-xf-orientation: horizontal;`|
|`-xf-horizontal-scroll-bar-visibility`|`ScrollView`| `default` \| `always` \| `never` \| `initial` |`-xf-horizontal-scroll-bar-visibility: never;`|
|`-xf-vertical-scroll-bar-visibility`|`ScrollView`| `default` \| `always` \| `never` \| `initial` |`-xf-vertical-scroll-bar-visibility: always;`|
|`-xf-min-track-color`|`Slider`|_颜色_ \| `initial` |`-xf-min-track-color: yellow;`|
|`-xf-max-track-color`|`Slider`|_颜色_ \| `initial` |`-xf-max-track-color: red;`|
|`-xf-thumb-color`|`Slider`|_颜色_ \| `initial` |`-xf-thumb-color: limegreen;`|
|`-xf-spacing`|`StackLayout`|_双精度_ \| `initial` |`-xf-spacing: 8;`|

> [!NOTE]
> `initial` 是为所有属性的有效值。 它会清除从另一个样式设置的值 （重置为默认值）。

是当前不支持以下属性：

- `all: initial`。
- 布局属性 （框中，或网格）。
- 速记属性，如`font`，和`border`。

此外，还有没有`inherit`值，因此继承不受支持。 因此不能例如，设置`font-size`布局的属性和预期所有[ `Label` ](xref:Xamarin.Forms.Label)要继承的值的布局中的实例。 是一个例外`direction`属性，它具有默认值的`inherit`。

### <a name="color"></a>颜色

以下`color`支持值：

- `X11` [颜色](https://en.wikipedia.org/wiki/X11_color_names/)，其中匹配 CSS 颜色、 UWP 预定义的颜色和 Xamarin.Forms 的颜色。 请注意这些颜色值是不区分大小写。
- 十六进制颜色： `#rgb`， `#argb`， `#rrggbb`， `#aarrggbb`
- rgb 颜色： `rgb(255,0,0)`， `rgb(100%,0%,0%)`。 值为范围内，0-255 或 0%-100%。
- rgba 颜色： `rgba(255, 0, 0, 0.8)`， `rgba(100%, 0%, 0%, 0.8)`。 不透明度值为 0.0 到 1.0 范围内。
- hsl 颜色： `hsl(120, 100%, 50%)`。 H 值为范围 0-360，尽管 s 和 l 是在范围 0%-100%。
- hsla 颜色： `hsla(120, 100%, 50%, .8)`。 不透明度值为 0.0 到 1.0 范围内。

### <a name="thickness"></a>Thickness

一个、 两个、 三或四个`thickness`支持的值，每个由空格分隔：

- 单个值指示统一粗细，即。
- 两个值表示垂直然后水平的粗细。
- 三个值表示顶部，然后水平 （左侧和右侧），然后底部粗细。
- 四个值表示顶部，然后右，然后底部，左侧的粗细。

> [!NOTE]
> CSS`thickness`值不同于 XAML [ `Thickness` ](xref:Xamarin.Forms.Thickness)值。 例如，在两个值 XAML`Thickness`指示水平然后垂直粗细，四个值时`Thickness`指示左侧、 顶部，然后右侧，然后下粗细。 此外，XAML`Thickness`逗号分隔的有效值。

### <a name="namedsize"></a>NamedSize

以下不区分大小写`namedsize`支持值：

- `default`
- `micro`
- `small`
- `medium`
- `large`

每个的确切含义`namedsize`值是依赖于平台的与视图相关。

## <a name="css-in-xamarinforms-with-xamarinuniversity"></a>在 Xamarin.Forms 中使用 Xamarin.University CSS

> [!VIDEO https://youtube.com/embed/va-Vb7vtan8]

**Xamarin.Forms 3.0 CSS，也可由[Xamarin 学院课程](https://university.xamarin.com/)**

## <a name="related-links"></a>相关链接

- [MonkeyAppCSS （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/MonkeyAppCSS/)
- [资源字典](~/xamarin-forms/xaml/resource-dictionaries.md)
- [使用 XAML 样式设置 Xamarin.Forms 应用的样式](~/xamarin-forms/user-interface/styles/xaml/index.md)
