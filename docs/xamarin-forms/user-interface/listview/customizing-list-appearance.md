---
title: 自定义 ListView 外观
description: 本文介绍如何自定义 Listview Xamarin.Forms 应用程序中使用标头、 页脚、 组和高度不同的单元格。
ms.prod: xamarin
ms.assetid: DC8009B0-4371-4D60-885A-5362FC7EE3E5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/13/2018
ms.openlocfilehash: 270191890e8747ecad7d250fb0fe18ce253d3823
ms.sourcegitcommit: 97dca3face7c4ad5555dfaca88f5b45a70ca556d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2019
ms.locfileid: "57972645"
---
# <a name="customizing-listview-appearance"></a>自定义 ListView 外观

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/Grouping)

[`ListView`](xref:Xamarin.Forms.ListView) 能够控制表示法的列表中，除了[ `ViewCell` ](xref:Xamarin.Forms.ViewCell)实例列表中的每一行。

<a name="Grouping" />

## <a name="grouping"></a>分组
通常，大型数据集可以变得难以处理时不断滚动列表中显示。 启用分组可以更好地组织内容和激活轻松导航数据的特定于平台的控件通过提高在这些情况下的用户体验。

有关激活分组时`ListView`，为每个组添加一个标题行。

若要启用分组：

- 创建列表的列表 （组的列表，每个组正在元素的列表）。
- 设置`ListView`的`ItemsSource`到该列表。
- 设置`IsGroupingEnabled`为 true。
- 设置[ `GroupDisplayBinding` ](xref:Xamarin.Forms.ListView.GroupDisplayBinding)要绑定到正用作组的标题的组的属性。
- [可选]设置[ `GroupShortNameBinding` ](xref:Xamarin.Forms.ListView.GroupShortNameBinding)要绑定到要用作组的短名称的组的属性。 短名称用于跳转列表 （在 iOS 上的右侧列）。

首先创建组的类：

```csharp
public class PageTypeGroup : List<PageModel>
    {
        public string Title { get; set; }
        public string ShortName { get; set; } //will be used for jump lists
        public string Subtitle { get; set; }
        private PageTypeGroup(string title, string shortName)
        {
            Title = title;
            ShortName = shortName;
        }

        public static IList<PageTypeGroup> All { private set; get; }
    }
```

在上面的代码，`All`是要提供给我们 ListView 用作绑定源的列表。 `Title` 和`ShortName`是将用于组标题的属性。

在此阶段，`All`为空列表。 添加静态构造函数，使程序启动时将填充列表：

```csharp
static PageTypeGroup()
{
    List<PageTypeGroup> Groups = new List<PageTypeGroup> {
            new PageTypeGroup ("Alfa", "A"){
                new PageModel("Amelia", "Cedar", new switchCellPage(),""),
                new PageModel("Alfie", "Spruce", new switchCellPage(), "grapefruit.jpg"),
                new PageModel("Ava", "Pine", new switchCellPage(), "grapefruit.jpg"),
                new PageModel("Archie", "Maple", new switchCellPage(), "grapefruit.jpg")
            },
            new PageTypeGroup ("Bravo", "B"){
                new PageModel("Brooke", "Lumia", new switchCellPage(),""),
                new PageModel("Bobby", "Xperia", new switchCellPage(), "grapefruit.jpg"),
                new PageModel("Bella", "Desire", new switchCellPage(), "grapefruit.jpg"),
                new PageModel("Ben", "Chocolate", new switchCellPage(), "grapefruit.jpg")
            }
        }
        All = Groups; //set the publicly accessible list
}
```

在上述代码中，我们可以调用`Add`上的元素`groups`，这是类型的实例`PageTypeGroup`。 这可能是因为`PageTypeGroup`继承`List<PageModel>`。 这是列表的上面记下列表模式的示例。

下面是用于显示分组的列表 XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="DemoListView.GroupingViewPage"
    <ContentPage.Content>
        <ListView  x:Name="GroupedView"
        GroupDisplayBinding="{Binding Title}"
        GroupShortNameBinding="{Binding ShortName}"
        IsGroupingEnabled="true">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Title}"
                     Detail="{Binding Subtitle}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </ContentPage.Content>
</ContentPage>
```

这将产生以下结果：

![](customizing-list-appearance-images/grouping-depth.png "ListView 分组示例")

请注意，我们具有：

- 设置`GroupShortNameBinding`到`ShortName`我们组的类中定义的属性
- 设置`GroupDisplayBinding`到`Title`我们组的类中定义的属性
- 设置`IsGroupingEnabled`为 true
- 更改`ListView`的`ItemsSource`到分组列表

### <a name="customizing-grouping"></a>自定义分组

如果在列表中已启用分组，也可以定制的组标头。

类似于如何`ListView`已`ItemTemplate`用于定义如何显示行`ListView`具有`GroupHeaderTemplate`。

自定义 XAML 中的组标头的示例如下所示：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="DemoListView.GroupingViewPage">
    <ContentPage.Content>
        <ListView x:Name="GroupedView"
         GroupDisplayBinding="{Binding Title}"
         GroupShortNameBinding="{Binding ShortName}"
         IsGroupingEnabled="true">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Title}"
                    Detail="{Binding Subtitle}"
                    TextColor="#f35e20"
                    DetailColor="#503026" />
                </DataTemplate>
            </ListView.ItemTemplate>
            <!-- Group Header Customization-->
            <ListView.GroupHeaderTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Title}"
                    Detail="{Binding ShortName}"
                    TextColor="#f35e20"
                    DetailColor="#503026" />
                </DataTemplate>
            </ListView.GroupHeaderTemplate>
            <!-- End Group Header Customization -->
        </ListView>
    </ContentPage.Content>
</ContentPage>
```

<a name="Headers_and_Footers" />

## <a name="headers-and-footers"></a>页眉和页脚
很可能 ListView 显示页眉和页脚的向下滚动列表中的元素。 页眉和页脚可以是文本字符串或更复杂的布局。 请注意，这是独立于[部分组](#Grouping)。

可以设置`Header`和/或`Footer`到一个简单的字符串值，也可以将其设置为更复杂的布局。
此外，还有`HeaderTemplate`和`FooterTemplate`属性，可创建更复杂的页眉和页脚的布局支持数据绑定的。

若要创建简单的页眉/页脚，只需为你想要显示的文本设置页眉或页脚属性。 在代码中：

```csharp
ListView HeaderList = new ListView() {
    Header = "Header",
    Footer = "Footer"
    };
```

在 XAML:

```xaml
<ListView  x:Name="HeaderList"  Header="Header" Footer="Footer"></ListView>
```

![](customizing-list-appearance-images/header-default.png "使用页眉和页脚的 ListView")

若要创建自定义的页眉和页脚，请定义页眉和页脚视图：

```xaml
<ListView.Header>
    <StackLayout Orientation="Horizontal">
        <Label Text="Header"
        TextColor="Olive"
        BackgroundColor="Red" />
    </StackLayout>
</ListView.Header>
<ListView.Footer>
    <StackLayout Orientation="Horizontal">
        <Label Text="Footer"
        TextColor="Gray"
        BackgroundColor="Blue" />
    </StackLayout>
</ListView.Footer>
```

![](customizing-list-appearance-images/header-custom.png "使用自定义标头和表尾的 ListView")

## <a name="scrollbar-visibility"></a>滚动条可见性

[`ListView`](xref:Xamarin.Forms.ListView) 具有`HorizontalScrollBarVisibility`并`VerticalScrollBarVisibility`属性，获取或设置[ `ScrollBarVisibility` ](xref:Xamarin.Forms.ScrollBarVisibility)值，该值表示当水平或垂直滚动条是否可见。 这两个属性可以设置为以下值：

- [`Default`](xref:Xamarin.Forms.ScrollBarVisibility) 表示在平台的默认滚动栏行为，它是默认值`HorizontalScrollBarVisibility`和`VerticalScrollBarVisibility`属性。
- [`Always`](xref:Xamarin.Forms.ScrollBarVisibility) 指示滚动条将显示，即使内容适合在视图中。
- [`Never`](xref:Xamarin.Forms.ScrollBarVisibility) 指示滚动条将不可见，即使内容无法放入视图中。

<a name="Row_Separators" />

## <a name="row-separators"></a>行分隔符
分隔符线之间显示`ListView`默认情况下在 iOS 和 Android 上的元素。 如果你想隐藏在 iOS 和 Android 上的分隔符线，设置`SeparatorVisibility`在 ListView 中的属性。 选项为`SeparatorVisibility`是：

* **默认**-iOS 和 Android 上显示一条分隔线。
* **无**-隐藏所有平台上的分隔符。

默认可见性：

C#：

```csharp
SepratorDemoListView.SeparatorVisibility = SeparatorVisibility.Default;
```

XAML:

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorVisibility="Default" />
```

![](customizing-list-appearance-images/separator-default.png "与默认行分隔符的 ListView")

None:

C#：

```csharp
SepratorDemoListView.SeparatorVisibility = SeparatorVisibility.None;
```

XAML:

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorVisibility="None" />
```

![](customizing-list-appearance-images/separator-none.png "不带行分隔符的 ListView")

此外可以设置通过分隔线的颜色`SeparatorColor`属性：

C#：

```csharp
SepratorDemoListView.SeparatorColor = Color.Green;
```

XAML:

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorColor="Green" />
```

![](customizing-list-appearance-images/separator-custom.png "带有绿色行分隔符的 ListView")

> [!NOTE]
> 设置这些属性在 Android 上加载后`ListView`会产生对较大性能产生负面影响。

<a name="Row_Heights" />

## <a name="row-heights"></a>行高
默认情况下，ListView 中的所有行都具有相同的高度。 ListView 有可用于更改该行为的两个属性：

- `HasUnevenRows` &ndash; `true`/`false` 值，如果行具有不同高度设置为`true`。 默认为 `false`。
- `RowHeight` &ndash; 集的每个高度行何时`HasUnevenRows`是`false`。

通过设置可设置的所有行的高度`RowHeight`属性上的`ListView`。

### <a name="custom-fixed-row-height"></a>自定义固定的行高

C#：

```csharp
RowHeightDemoListView.RowHeight = 100;
```

XAML:

```xaml
<ListView x:Name="RowHeightDemoListView" RowHeight="100" />
```

![](customizing-list-appearance-images/height-custom.png "使用固定的行高度的 ListView")


### <a name="uneven-rows"></a>以不相等的行

如果你想要具有不同高度的单个行，则可以设置`HasUnevenRows`属性设置为`true`。
请注意行高不需要手动设置一次`HasUnevenRows`已设置为`true`，因为 xamarin.forms 自动计算高度。


C#：

```csharp
RowHeightDemoListView.HasUnevenRows = true;
```

XAML:

```xaml
<ListView x:Name="RowHeightDemoListView" HasUnevenRows="true" />
```

![](customizing-list-appearance-images/height-uneven.png "以不相等行的 ListView")

### <a name="runtime-resizing-of-rows"></a>运行时调整大小的行

各个`ListView`行在运行时，提供的可以以编程方式调整大小`HasUnevenRows`属性设置为`true`。 [ `Cell.ForceUpdateSize` ](xref:Xamarin.Forms.Cell.ForceUpdateSize)方法更新单元格的大小，即使它不是当前可见，如下面的代码示例中所示：

```csharp
void OnImageTapped (object sender, EventArgs args)
{
    var image = sender as Image;
    var viewCell = image.Parent.Parent as ViewCell;

    if (image.HeightRequest < 250) {
        image.HeightRequest = image.Height + 100;
        viewCell.ForceUpdateSize ();
    }
}
```

`OnImageTapped`事件处理程序执行以响应[ `Image` ](xref:Xamarin.Forms.Image)在单元格中被点击，并会增加的大小`Image`，以便轻松地查看该单元中显示。

![](customizing-list-appearance-images/dynamic-row-resizing.png "使用运行时行重设大小的 ListView")

请注意是否过度使用此功能没有强可能导致性能下降。



## <a name="related-links"></a>相关链接

- [分组 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/Grouping)
- [自定义呈现器视图 （示例）](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithListviewNative/)
- [动态调整大小的行 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/DynamicUnevenListCells/)
- [1.4 的发行说明](http://forums.xamarin.com/discussion/35451/xamarin-forms-1-4-0-released/)
- [1.3 的发行说明](http://forums.xamarin.com/discussion/29934/xamarin-forms-1-3-0-released/)
