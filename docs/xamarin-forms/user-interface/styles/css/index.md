---
title: 使用级联样式表 (CSS) 样式设置 Xamarin.Forms 应用
description: Xamarin.Forms 支持使用级联样式表 (CSS) 样式可视元素。
ms.prod: xamarin
ms.assetid: C89D57A6-DAB9-4C42-963F-26D67627DDC2
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 11/04/2019
ms.openlocfilehash: 726ebd55b38460ee966113e4ee487327cd42b03d
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76724191"
---
# <a name="styling-xamarinforms-apps-using-cascading-style-sheets-css"></a>使用级联样式表 (CSS) 样式设置 Xamarin.Forms 应用

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss)

_Xamarin 支持使用级联样式表（CSS）对视觉元素进行样式设置。_

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
> 目前，所有可以实现的 XAML 样式的样式不能使用 CSS 执行。 但是，可以使用 XAML 样式来补充 CSS Xamarin.Forms 当前不支持的属性。 有关 XAML 样式的详细信息，请参阅[使用 XAML 样式设置 Xamarin.Forms 应用的样式](~/xamarin-forms/user-interface/styles/xaml/index.md)。

[MonkeyAppCSS](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss)示例演示如何使用 CSS 为简单应用程序进行样式，并在以下屏幕截图中所示：

[![具有 CSS 样式的 MonkeyApp 主页](css-images/MonkeyAppMainPage.png "具有 CSS 样式的 MonkeyApp 主页")](css-images/MonkeyAppMainPage-Large.png#lightbox "具有 CSS 样式的 MonkeyApp 主页")

[![具有 CSS 样式的 MonkeyApp 详细信息页](css-images/MonkeyAppDetailPage.png "具有 CSS 样式的 MonkeyApp 详细信息页")](css-images/MonkeyAppDetailPage-Large.png#lightbox "具有 CSS 样式的 MonkeyApp 详细信息页")

## <a name="consuming-a-style-sheet"></a>使用样式表

向解决方案添加样式表的过程如下所示：

1. 将空 CSS 文件添加到.NET Standard 库项目。
1. 将 CSS 文件的生成操作设置为**EmbeddedResource**。

### <a name="loading-a-style-sheet"></a>加载样式表

有多种方法可用于加载样式表。

### <a name="xaml"></a>XAML

在将样式表添加到[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)之前，可以使用[`StyleSheet`](xref:Xamarin.Forms.StyleSheets.StyleSheet)类对其进行加载和分析：

```xaml
<Application ...>
    <Application.Resources>
        <StyleSheet Source="/Assets/styles.css" />
    </Application.Resources>
</Application>
```

[`StyleSheet.Source`](xref:Xamarin.Forms.Xaml.StyleSheetExtension.Source)属性将样式表指定为相对于封闭 XAML 文件位置的 URI，如果 uri 以 `/`开头，则将相对于项目根指定样式表。

> [!WARNING]
> 如果 CSS 文件的生成操作未设置为**EmbeddedResource**，则该文件将无法加载。

或者，可以使用[`StyleSheet`](xref:Xamarin.Forms.StyleSheets.StyleSheet)类加载和分析样式表，然后再将其添加到[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)，方法是将其内联到 `CDATA` 部分：

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

### <a name="c"></a>C\#

在C#中，可以从 `StringReader` 加载样式表，并将其添加到[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)中：

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

`StyleSheet.FromReader` 方法的参数是已读取样式表的 `TextReader`。

## <a name="selecting-elements-and-applying-properties"></a>选择元素并将属性应用

CSS 使用选择器来确定要为目标的元素。 按定义顺序连续，应用具有匹配的选择器的样式。 始终最后应用在特定项目上定义的样式。 有关支持的选择器的详细信息，请参阅[选择器引用](#selector-reference)。

CSS 使用属性来设置所选的元素的样式。 每个属性具有一系列可能的值，并且某些属性会影响任何类型的元素，而其他人将应用于组的元素。 有关支持的属性的详细信息，请参阅[属性参考](#property-reference)。

### <a name="selecting-elements-by-type"></a>选择按类型的元素

可视化树中的元素可以由类型选择，不区分大小写 `element` 选择器：

```css
stacklayout {
    margin: 20;
}
```

此选择器标识页面上使用样式表的任何[`StackLayout`](xref:Xamarin.Forms.StackLayout)元素，并将其边距设置为统一的宽度20。

> [!NOTE]
> `element` 选择器不标识指定类型的子类。

### <a name="selecting-elements-by-base-class"></a>由基类选择元素

可视化树中的元素可由基类选择，不区分大小写 `^base` 选择器：

```css
^contentpage {
    background-color: lightgray;
}
```

此选择器标识使用样式表的任何[`ContentPage`](xref:Xamarin.Forms.ContentPage)元素，并将其背景色设置为 `lightgray`。

> [!NOTE]
> `^base` 选择器特定于 Xamarin，不属于 CSS 规范。

### <a name="selecting-an-element-by-name"></a>按名称选择元素

可视化树中的单个元素可以通过区分大小写 `#id` 选择器进行选择：

```css
#listView {
    background-color: lightgray;
}
```

此选择器标识其[`StyleId`](xref:Xamarin.Forms.Element.StyleId)属性设置为 `listView`的元素。 但是，如果未设置 `StyleId` 属性，则选择器将回退到使用元素的 `x:Name`。 因此，在下面的 XAML 示例中，`#listView` 选择器将标识其 `x:Name` 特性设置为 `listView`的[`ListView`](xref:Xamarin.Forms.ListView) ，并将其背景色设置为 `lightgray`。

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

可以使用区分大小写 `.class` 选择器选择具有特定类属性的元素：

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

可以通过将元素的[`StyleClass`](xref:Xamarin.Forms.NavigableElement.StyleClass)属性设置为 css 类名称，将 CSS 类分配给 XAML 元素。 因此，在下面的 XAML 示例中，`.detailPageTitle` 类定义的样式将分配给第一个[`Label`](xref:Xamarin.Forms.Label)，而 `.detailPageSubtitle` 类定义的样式将分配给第二个 `Label`。

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

如果选择了不区分大小写，则可以选择可视化树中的子元素 `element element`：

```css
listview image {
    height: 60;
    width: 60;
}
```

此选择器标识作为[`ListView`](xref:Xamarin.Forms.ListView)元素的子元素的任何[`Image`](xref:Xamarin.Forms.Image)元素，并将其高度和宽度设置为60。 因此，在下面的 XAML 示例中，`listview image` 选择器将标识是[`ListView`](xref:Xamarin.Forms.ListView)的子级的[`Image`](xref:Xamarin.Forms.Image) ，并将其高度和宽度设置为60。

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
> `element element` 选择器不需要子元素成为父元素的_直接_子级–子元素可能具有不同的父元素。 提供的该祖先是指定的第一个元素，则发生所选内容。

### <a name="selecting-direct-child-elements"></a>选择直接子元素

可以在可视化树中选择直接子元素，不区分大小写 `element>element` 选择器：

```css
stacklayout>image {
    height: 200;
    width: 200;
}
```

此选择器标识作为[`StackLayout`](xref:Xamarin.Forms.StackLayout)元素的直接子级的任何[`Image`](xref:Xamarin.Forms.Image)元素，并将其高度和宽度设置为200。 因此，在下面的 XAML 示例中，`stacklayout>image` 选择器将标识是[`StackLayout`](xref:Xamarin.Forms.StackLayout)的直接子级的[`Image`](xref:Xamarin.Forms.Image) ，并将其高度和宽度设置为200。

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
> `element>element` 选择器要求子元素是父级的_直接_子级。

## <a name="selector-reference"></a>选择器引用

Xamarin.Forms 支持以下 CSS 选择器：

|选择器|示例|说明|
|---|---|---|
|`.class`|`.header`|选择具有包含 "标头" 的 `StyleClass` 属性的所有元素。 请注意，此选择器区分大小写。|
|`#id`|`#email`|选择 `StyleId` 设置为 `email`的所有元素。 如果未设置 `StyleId`，则回退到 `x:Name`。 使用 XAML 时，`x:Name` 优先于 `StyleId`。 请注意，此选择器区分大小写。|
|`*`|`*`|选择所有元素。|
|`element`|`label`|选择 `Label`而不是子类类型的所有元素。 请注意，此选择器不区分大小写。|
|`^base`|`^contentpage`|选择 `ContentPage` 作为基类的所有元素，包括 `ContentPage` 本身。 请注意，此选择器是不区分大小写，不是 CSS 规范的一部分。|
|`element,element`|`label,button`|选择所有 `Button` 元素和所有 `Label` 元素。 请注意，此选择器不区分大小写。|
|`element element`|`stacklayout label`|选择 `StackLayout`中的所有 `Label` 元素。 请注意，此选择器不区分大小写。|
|`element>element`|`stacklayout>label`|选择 `StackLayout` 为直接父项的所有 `Label` 元素。 请注意，此选择器不区分大小写。|
|`element+element`|`label+entry`|直接选择 `Label`之后的所有 `Entry` 元素。 请注意，此选择器不区分大小写。|
|`element~element`|`label~entry`|选择前面带有 `Label`的所有 `Entry` 元素。 请注意，此选择器不区分大小写。|

按定义顺序连续，应用具有匹配的选择器的样式。 始终最后应用在特定项目上定义的样式。

> [!TIP]
> 选择器可以组合而不受限制，如 `StackLayout>ContentView>label.email`。

以下选择器是当前不受支持：

- `[attribute]`
- `@media` 和 `@supports`
- `:` 和 `::`

> [!NOTE]
> 特异性和特异性替代是不受支持。

## <a name="property-reference"></a>属性引用

Xamarin 格式支持以下 CSS 属性。窗体（在 "**值**" 列中，类型是_斜体_，而字符串文字 `gray`）：

|属性|适用于|值|示例|
|---|---|---|---|
|`align-content`|`FlexLayout`| `stretch` \| `center` \| `start` \| `end` \| `spacebetween` \| `spacearound` \| `spaceevenly` \| `flex-start` \| `flex-end` \| `space-between` \| `space-around` \| `initial` |`align-content: space-between;`|
|`align-items`|`FlexLayout`| `stretch` \| `center` \| `start` \| `end` \| `flex-start` \| `flex-end` \| `initial` |`align-items: flex-start;`|
|`align-self`|`VisualElement`| `auto` \| `stretch` \| `center` \| `start` \| `end` \| `flex-start` \| `flex-end` \| `initial`|`align-self: flex-end;`|
|`background-color`|`VisualElement`|_颜色_\| `initial` |`background-color: springgreen;`|
|`background-image`|`Page`|_string_ \| `initial` |`background-image: bg.png;`|
|`border-color`|`Button`、 `Frame`、 `ImageButton`|_颜色_\| `initial`|`border-color: #9acd32;`|
|`border-radius`|`BoxView`, `Button`, `Frame`, `ImageButton`|_双_\| `initial` |`border-radius: 10;`|
|`border-width`|`Button`, `ImageButton`|_双_\| `initial` |`border-width: .5;`|
|`color`|`ActivityIndicator`、`BoxView`、`Button`、`CheckBox`、`DatePicker`、`Editor`、`Entry`、`Label`、`Picker`、`ProgressBar`、`SearchBar`、`Switch`、`TimePicker`|_颜色_\| `initial` |`color: rgba(255, 0, 0, 0.3);`|
|`column-gap`|`Grid`|_双_\| `initial`|`column-gap: 9;`|
|`direction`|`VisualElement`|`ltr` \| `rtl` \| `inherit` \| `initial` |`direction: rtl;`|
|`flex-direction`|`FlexLayout`| `column` \| `columnreverse` \| `row` \| `rowreverse` \| `row-reverse` \| `column-reverse` \| `initial`|`flex-direction: column-reverse;`|
|`flex-basis`|`VisualElement`|\| `initial`_float_ \| `auto`。 此外，可以使用 `%` 符号指定范围0% 到100% 之间的百分比。|`flex-basis: 25%;`|
|`flex-grow`|`VisualElement`|_float_ \| `initial`|`flex-grow: 1.5;`|
|`flex-shrink`|`VisualElement`|_float_ \| `initial`|`flex-shrink: 1;`|
|`flex-wrap`|`VisualElement`| `nowrap` \| `wrap` \| `reverse` \| `wrap-reverse` \| `initial`|`flex-wrap: wrap-reverse;`|
|`font-family`|`Button`、`DatePicker`、`Editor`、`Entry`、`Label`、`Picker`、`SearchBar`、`TimePicker`、`Span`|_string_ \| `initial` |`font-family: Consolas;`|
|`font-size`|`Button`、`DatePicker`、`Editor`、`Entry`、`Label`、`Picker`、`SearchBar`、`TimePicker`、`Span`|_double_\| _namedsize_ \| `initial` |`font-size: 12;`|
|`font-style`|`Button`、`DatePicker`、`Editor`、`Entry`、`Label`、`Picker`、`SearchBar`、`TimePicker`、`Span`|`bold` \| `italic` \| `initial` |`font-style: bold;`|
|`height`|`VisualElement`|_双_\| `initial` |`min-height: 250;`|
|`justify-content`|`FlexLayout`| `start` \| `center` \| `end` \| `spacebetween` \| `spacearound` \| `spaceevenly` \| `flex-start` \| `flex-end` \| `space-between` \| `space-around` \| `initial`|`justify-content: flex-end;`|
|`letter-spacing`|`Button`, `DatePicker`, `Editor`, `Entry`, `Label`, `Picker`, `SearchBar`, `SearchHandler`, `Span`, `TimePicker`|_双_\| `initial`|`letter-spacing: 2.5;`|
|`line-height`|`Label`, `Span`|_双_\| `initial` |`line-height: 1.8;`|
|`margin`|`View`|_厚度_\| `initial` |`margin: 6 12;`|
|`margin-left`|`View`|_厚度_\| `initial` |`margin-left: 3;`|
|`margin-top`|`View`|_厚度_\| `initial` |`margin-top: 2;`|
|`margin-right`|`View`|_厚度_\| `initial` |`margin-right: 1;`|
|`margin-bottom`|`View`|_厚度_\| `initial` |`margin-bottom: 6;`|
|`max-lines`|`Label`|_int_ \| `initial`|`max-lines: 2;`|
|`min-height`|`VisualElement`|_双_\| `initial` |`min-height: 50;`|
|`min-width`|`VisualElement`|_双_\| `initial` |`min-width: 112;`|
|`opacity`|`VisualElement`|_双_\| `initial` |`opacity: .3;`|
|`order`|`VisualElement`|_int_ \| `initial`|`order: -1;`|
|`padding`|`Button`, `ImageButton`, `Layout`, `Page`|_厚度_\| `initial` |`padding: 6 12 12;`|
|`padding-left`|`Button`, `ImageButton`, `Layout`, `Page`|_双_\| `initial`|`padding-left: 3;`|
|`padding-top`|`Button`, `ImageButton`, `Layout`, `Page`| _双_\| `initial` |`padding-top: 4;`|
|`padding-right`|`Button`, `ImageButton`, `Layout`, `Page`| _双_\| `initial` |`padding-right: 2;`|
|`padding-bottom`|`Button`, `ImageButton`, `Layout`, `Page`| _双_\| `initial` |`padding-bottom: 6;`|
|`position`|`FlexLayout`| `relative` \| `absolute` \| `initial`|`position: absolute;`|
|`row-gap`|`Grid`| _双_\| `initial`|`row-gap: 12;`|
|`text-align`| `Entry`, `EntryCell`, `Label`, `SearchBar`|`left` \| `top` \| `right` \| `bottom` \| `start` \| `center` \| `middle` \| `end` \| `initial`。 在从右向左的环境中，应避免 `left` 和 `right`。| `text-align: right;`|
|`text-decoration`|`Label`, `Span`|`none` \| `underline` \| `strikethrough` \| `line-through` \| `initial`|`text-decoration: underline, line-through;`|
|`transform`|`VisualElement`| `none`, `rotate`, `rotateX`, `rotateY`, `scale`, `scaleX`, `scaleY`, `translate`, `translateX`, `translateY`, `initial` |`transform: rotate(180), scaleX(2.5);`|
|`transform-origin`|`VisualElement`| _double_、 _double_ \| `initial` |`transform-origin: 7.5, 12.5;`|
|`vertical-align`|`Label`|`left` \| `top` \| `right` \| `bottom` \| `start` \| `center` \| `middle` \| `end` \| `initial`|`vertical-align: bottom;`|
|`visibility`|`VisualElement`|`true` \| `visible` \| `false` \| `hidden` \| `collapse` \| `initial`|`visibility: hidden;`|
|`width`|`VisualElement`|_双_\| `initial`|`min-width: 320;`|

> [!NOTE]
> `initial` 是所有属性的有效值。 它会清除从另一个样式设置的值 （重置为默认值）。

是当前不支持以下属性：

- `all: initial`配置架构以匹配数据源中的架构。
- 布局属性 （框中，或网格）。
- 速记属性，如 `font`和 `border`。

此外，没有 `inherit` 的值，因此不支持继承。 因此，您不能这样做，例如，在布局上设置 `font-size` 属性，并期望布局中的所有[`Label`](xref:Xamarin.Forms.Label)实例继承值。 一个例外情况是 `direction` 属性，该属性的默认值为 `inherit`。

面向 `Span` 元素具有一个已知问题，即元素和名称（使用 `#` 符号）阻止跨越成为 CSS 样式的目标。 `Span` 元素派生自 `GestureElement`，后者没有 `StyleClass` 属性，因此它不支持 CSS 类目标。 有关详细信息，请参阅[无法将 CSS 样式应用于跨控件](https://github.com/xamarin/Xamarin.Forms/issues/5979)。

### <a name="xamarinforms-specific-properties"></a>Xamarin 特定于窗体的属性

以下 Xamarin. 还支持特定于窗体的 CSS 属性（在 "**值**" 列中，类型为_斜体_，而字符串文字 `gray`）：

|属性|适用于|值|示例|
|---|---|---|---|
|`-xf-bar-background-color`|`NavigationPage`, `TabbedPage`|_颜色_\| `initial` |`-xf-bar-background-color: teal;`|
|`-xf-bar-text-color`|`NavigationPage`, `TabbedPage`|_颜色_\| `initial` |`-xf-bar-text-color: gray`|
|`-xf-horizontal-scroll-bar-visibility`|`ScrollView`| `default` \| `always` \| `never` \| `initial` |`-xf-horizontal-scroll-bar-visibility: never;`|
|`-xf-max-length`|`Entry`, `Editor`|_int_ \| `initial` |`-xf-max-length: 20;`|
|`-xf-max-track-color`|`Slider`|_颜色_\| `initial` |`-xf-max-track-color: red;`|
|`-xf-min-track-color`|`Slider`|_颜色_\| `initial` |`-xf-min-track-color: yellow;`|
|`-xf-orientation`|`ScrollView`, `StackLayout`| `horizontal` \| `vertical` \| `both` \| `initial`。 仅 `ScrollView`支持 `both`。 |`-xf-orientation: horizontal;`|
|`-xf-placeholder`|`Entry`、 `Editor`、 `SearchBar`|_带引号的文本_\| `initial` |`-xf-placeholder: Enter name;`|
|`-xf-placeholder-color`|`Entry`、 `Editor`、 `SearchBar`|_颜色_\| `initial` |`-xf-placeholder-color: green;`|
|`-xf-spacing`|`StackLayout`|_双_\| `initial` |`-xf-spacing: 8;`|
|`-xf-thumb-color`|`Slider`, `Switch`|_颜色_\| `initial` |`-xf-thumb-color: limegreen;`|
|`-xf-vertical-scroll-bar-visibility`|`ScrollView`| `default` \| `always` \| `never` \| `initial` |`-xf-vertical-scroll-bar-visibility: always;`|
|`-xf-vertical-text-alignment`|`Label`| `start` \| `center` \| `end` \| `initial`|`-xf-vertical-text-alignment: end;`|
|`-xf-visual`|`VisualElement`|_string_ \| `initial` |`-xf-visual: material;`|

### <a name="xamarinforms-shell-specific-properties"></a>Xamarin。 Forms Shell 特定属性

以下 Xamarin。还支持以下 Xamarin Shell 特定 CSS 属性（在 "**值**" 列中，类型为_斜体_，而字符串文字 `gray`）：

|属性|适用于|值|示例|
|---|---|---|---|
|`-xf-flyout-background`|`Shell`|_颜色_\| `initial` |`-xf-flyout-background: red;`|
|`-xf-shell-background`|`Element`|_颜色_\| `initial` |`-xf-shell-background: green;`|
|`-xf-shell-disabled`|`Element`|_颜色_\| `initial` |`-xf-shell-disabled: blue;`|
|`-xf-shell-foreground`|`Element`|_颜色_\| `initial` |`-xf-shell-foreground: yellow;`|
|`-xf-shell-tabbar-background`|`Element`|_颜色_\| `initial` |`-xf-shell-tabbar-background: white;`|
|`-xf-shell-tabbar-disabled`|`Element`|_颜色_\| `initial` |`-xf-shell-tabbar-disabled: black;`|
|`-xf-shell-tabbar-foreground`|`Element`|_颜色_\| `initial` |`-xf-shell-tabbar-foreground: gray;`|
|`-xf-shell-tabbar-title`|`Element`|_颜色_\| `initial` |`-xf-shell-tabbar-title: lightgray;`|
|`-xf-shell-tabbar-unselected`|`Element`|_颜色_\| `initial` |`-xf-shell-tabbar-unselected: cyan;`|
|`-xf-shell-title`|`Element`|_颜色_\| `initial` |`-xf-shell-title: teal;`|
|`-xf-shell-unselected`|`Element`|_颜色_\| `initial` |`-xf-shell-unselected: limegreen;`|

### <a name="color"></a>颜色

支持以下 `color` 值：

- `X11`[颜色](https://en.wikipedia.org/wiki/X11_color_names)，与 CSS 颜色、UWP 预定义颜色和 Xamarin 颜色匹配。 请注意这些颜色值是不区分大小写。
- 十六进制颜色： `#rgb`、`#argb`、`#rrggbb`、`#aarrggbb`
- rgb 颜色： `rgb(255,0,0)`，`rgb(100%,0%,0%)`。 值为范围内，0-255 或 0%-100%。
- rgba 颜色： `rgba(255, 0, 0, 0.8)`，`rgba(100%, 0%, 0%, 0.8)`。 不透明度值为 0.0 到 1.0 范围内。
- hsl 颜色： `hsl(120, 100%, 50%)`。 H 值为范围 0-360，尽管 s 和 l 是在范围 0%-100%。
- hsla colors： `hsla(120, 100%, 50%, .8)`。 不透明度值为 0.0 到 1.0 范围内。

### <a name="thickness"></a>Thickness

支持一个、两个、三个或四个 `thickness` 值，每个值由空格分隔：

- 单个值指示统一粗细，即。
- 两个值表示垂直然后水平的粗细。
- 三个值表示顶部，然后水平 （左侧和右侧），然后底部粗细。
- 四个值表示顶部，然后右，然后底部，左侧的粗细。

> [!NOTE]
> CSS `thickness` 值与 XAML [`Thickness`](xref:Xamarin.Forms.Thickness)值不同。 例如，在 XAML 中，两值 `Thickness` 指示水平和垂直粗细，而四个值的 `Thickness` 指示左、上、右、下粗细。 此外，XAML `Thickness` 值以逗号分隔。

### <a name="namedsize"></a>NamedSize

支持以下不区分大小写 `namedsize` 值：

- `default`
- `micro`
- `small`
- `medium`
- `large`

每个 `namedsize` 值的确切含义取决于平台和视图相关。

## <a name="css-in-xamarinforms-with-xamarinuniversity"></a>在 Xamarin.Forms 中使用 Xamarin.University CSS

> [!VIDEO https://youtube.com/embed/va-Vb7vtan8]

**Xamarin 3.0 CSS 视频**

## <a name="related-links"></a>相关链接

- [MonkeyAppCSS （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-monkeyappcss)
- [资源字典](~/xamarin-forms/xaml/resource-dictionaries.md)
- [使用 XAML 样式设置 Xamarin.Forms 应用的样式](~/xamarin-forms/user-interface/styles/xaml/index.md)
