---
title: 自定义 ListView 单元格的外观
description: 本文探讨了表示 Xamarin.Forms 应用程序中的数据，同时利用 ListView 控件的方便的选项。
ms.prod: xamarin
ms.assetid: FD45CB91-1A8F-46FB-B432-6BC20492E456
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/07/2016
ms.openlocfilehash: 1e57764dacf7ae0599df46f13bd4f5505c4cc4a2
ms.sourcegitcommit: 93c9fe61eb2cdfa530960b4253eb85161894c882
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2019
ms.locfileid: "55831776"
---
# <a name="customizing-listview-cell-appearance"></a>自定义 ListView 单元格的外观

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/CustomCells)

[`ListView`](xref:Xamarin.Forms.ListView) 显示可滚动列表，可以使用自定义`ViewCell`s。 `ViewCells` 可用于显示文本和图像、 指示 true/false 状态和接收用户输入。

<a name="Built_in_Cells" />

## <a name="built-in-cells"></a>内置的单元格
Xamarin.Forms 附带内置的适用于许多简单应用程序的单元格：

- **TextCell** &ndash;用于显示文本
- **ImageCell** &ndash;用于显示文本与图像。

两个其他单元格[ `SwitchCell` ](~/xamarin-forms/user-interface/tableview.md#switchcell)并[ `EntryCell` ](~/xamarin-forms/user-interface/tableview.md#entrycell)可供使用，但它们不常用于`ListView`。 请参阅[ `TableView` ](~/xamarin-forms/user-interface/tableview.md)详细了解这些单元格。

<a name="TextCell" />

### <a name="textcell"></a>TextCell

[`TextCell`](xref:Xamarin.Forms.TextCell) 是用于显示文本，并且根据需要使用第二行详细信息的文本作为一个单元格。

TextCells 呈现为本机控件在运行时，因此性能非常好与自定义比较`ViewCell`。 TextCells 可自定义，使你能够设置：

- `Text` &ndash; 在第一行，用大字体显示文本。
- `Detail` &ndash; 较小的字体中的第一行下面所示的文本。
- `TextColor` &ndash; 文本的颜色。
- `DetailColor` &ndash; 详细信息文本的颜色

![](customizing-cell-appearance-images/text-cell-default.png "默认 TextCell 示例")

![](customizing-cell-appearance-images/text-cell-custom.png "自定义的 TextCell 示例")

<a name="ImageCell" />

### <a name="imagecell"></a>ImageCell

[`ImageCell`](xref:Xamarin.Forms.ImageCell)如`TextCell`，可以用于显示文本和辅助的详细信息，并通过使用每个平台的本机控件提供优异的性能。 `ImageCell` 不同于`TextCell`，因为它的文本的左侧显示图像。

`ImageCell` 如果需要显示数据的可视方面，例如的联系人或电影列表的列表时很有用。 ImageCells 可自定义，使你能够设置：

- `Text` &ndash; 在第一行，用大字体显示文本
- `Detail` &ndash; 较小的字体中的第一行下面所示的文本
- `TextColor` &ndash; 文本的颜色
- `DetailColor` &ndash; 详细信息文本的颜色
- `ImageSource` &ndash; 要在文本旁边显示的图像

![](customizing-cell-appearance-images/image-cell-default.png "默认 ImageCell 示例")

![](customizing-cell-appearance-images/image-cell-custom.png "自定义的 ImageCell 示例")

<a name="customcells" />

## <a name="custom-cells"></a>自定义单元格
当内置的单元格未提供必需的布局时，自定义单元格实现必需的布局。 例如，你可能想要提供两个标签具有相等的权重的单元格。 一个`TextCell`不足是因为`TextCell`具有较小的一个标签。 大多数单元格自定义添加其他只读数据 （如其他标签、 图像或其他显示信息）。

自定义的所有单元格必须派生自[ `ViewCell` ](xref:Xamarin.Forms.ViewCell)，所有内置的单元格类型使用相同的基类。

Xamarin.Forms 2 引入了一个新[缓存行为](~/xamarin-forms/user-interface/listview/performance.md#cachingstrategy)上`ListView`可以设置以提高滚动性能对于某些类型的自定义单元格的控件。

这是自定义单元格的一个示例：

![](customizing-cell-appearance-images/custom-cell.png "自定义单元格示例")

### <a name="xaml"></a>XAML
若要创建上述布局 XAML 如下所示：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="demoListView.ImageCellPage">
    <ContentPage.Content>
        <ListView  x:Name="listView">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <StackLayout BackgroundColor="#eee"
                        Orientation="Vertical">
                            <StackLayout Orientation="Horizontal">
                                <Image Source="{Binding image}" />
                                <Label Text="{Binding title}"
                                TextColor="#f35e20" />
                                <Label Text="{Binding subtitle}"
                                HorizontalOptions="EndAndExpand"
                                TextColor="#503026" />
                            </StackLayout>
                        </StackLayout>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </ContentPage.Content>
</ContentPage>
```

上述 XAML 执行大量操作。 让我们深入细致地：

- 自定义单元格嵌套在`DataTemplate`，这是内部`ListView.ItemTemplate`。 这是与使用的任何其他单元相同的进程。
- `ViewCell` 是类型的自定义单元格。 子级`DataTemplate`元素必须是或派生类型`ViewCell`。
- 请注意，该内部`ViewCell`，由管理布局`StackLayout`。 此布局可用于自定义的背景色。 请注意，任何属性`StackLayout`，它是可绑定可以绑定自定义单元格，内部，尽管此处未显示的。
- 内部`ViewCell`，布局可以由任何 Xamarin.Forms 布局。 

### <a name="cnum"></a>C&num;

在 C# 中指定自定义单元格是 XAML 等效项的更详细。 让我们来实际操作一下：

首先，定义自定义单元格类，`ViewCell`作为基类：

```csharp
public class CustomCell : ViewCell
    {
        public CustomCell()
        {
            //instantiate each of our views
            var image = new Image ();
            StackLayout cellWrapper = new StackLayout ();
            StackLayout horizontalLayout = new StackLayout ();
            Label left = new Label ();
            Label right = new Label ();

            //set bindings
            left.SetBinding (Label.TextProperty, "title");
            right.SetBinding (Label.TextProperty, "subtitle");
            image.SetBinding (Image.SourceProperty, "image");

            //Set properties for desired design
            cellWrapper.BackgroundColor = Color.FromHex ("#eee");
            horizontalLayout.Orientation = StackOrientation.Horizontal;
            right.HorizontalOptions = LayoutOptions.EndAndExpand;
            left.TextColor = Color.FromHex ("#f35e20");
            right.TextColor = Color.FromHex ("503026");

            //add views to the view hierarchy
            horizontalLayout.Children.Add (image);
            horizontalLayout.Children.Add (left);
            horizontalLayout.Children.Add (right);
            cellWrapper.Children.Add (horizontalLayout);
            View = cellWrapper;
        }
    }
```

在构造函数具有页面`ListView`，设置 ListView`ItemTemplate`属性设置为一个新`DataTemplate`:

```csharp
public partial class ImageCellPage : ContentPage
    {
        public ImageCellPage ()
        {
            InitializeComponent ();
            listView.ItemTemplate = new DataTemplate (typeof(CustomCell));
        }
    }
```

请注意，对于构造函数`DataTemplate`接受一个类型。 Typeof 运算符获取的 CLR 类型`CustomCell`。

<a name="binding-context-changes" />

### <a name="binding-context-changes"></a>绑定上下文的更改

当绑定到自定义单元格类型的[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)实例，显示的 UI 控件`BindableProperty`的值应使用[ `OnBindingContextChanged` ](xref:Xamarin.Forms.Cell.OnBindingContextChanged)替代以将设置要在中显示的数据每个单元格，而不是单元格构造函数，如下面的代码示例中所示：

```csharp
public class CustomCell : ViewCell
{
    Label nameLabel, ageLabel, locationLabel;

    public static readonly BindableProperty NameProperty =
        BindableProperty.Create ("Name", typeof(string), typeof(CustomCell), "Name");
    public static readonly BindableProperty AgeProperty =
        BindableProperty.Create ("Age", typeof(int), typeof(CustomCell), 0);
    public static readonly BindableProperty LocationProperty =
        BindableProperty.Create ("Location", typeof(string), typeof(CustomCell), "Location");

    public string Name {
        get { return(string)GetValue (NameProperty); }
        set { SetValue (NameProperty, value); }
    }

    public int Age {
        get { return(int)GetValue (AgeProperty); }
        set { SetValue (AgeProperty, value); }
    }

    public string Location {
        get { return(string)GetValue (LocationProperty); }
        set { SetValue (LocationProperty, value); }
    }
    ...

    protected override void OnBindingContextChanged ()
    {
        base.OnBindingContextChanged ();

        if (BindingContext != null) {
            nameLabel.Text = Name;
            ageLabel.Text = Age.ToString ();
            locationLabel.Text = Location;
        }
    }
}
```

[ `OnBindingContextChanged` ](xref:Xamarin.Forms.Cell.OnBindingContextChanged)重写时将会调用[ `BindingContextChanged` ](xref:Xamarin.Forms.BindableObject.BindingContextChanged)事件触发时，响应的值[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)属性更改。 因此，当`BindingContext`发生更改，显示的 UI 控件[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)值应设置其数据。 请注意，`BindingContext`应检查是否有`null`值，因为这可以通过 Xamarin.Forms 设置，进行垃圾回收，这反过来会导致`OnBindingContextChanged`重写调用。

或者，UI 控件可以绑定到[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)实例，以显示它们的值，无需重写`OnBindingContextChanged`方法。

> [!NOTE]
> 重写时`OnBindingContextChanged`，确保的基类`OnBindingContextChanged`方法调用，以便注册的委托接收`BindingContextChanged`事件。

在 XAML 中，绑定到数据的自定义单元格类型可以实现的如下面的代码示例中所示：

```xaml
<ListView x:Name="listView">
    <ListView.ItemTemplate>
        <DataTemplate>
            <local:CustomCell Name="{Binding Name}" Age="{Binding Age}" Location="{Binding Location}" />
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

这会将绑定`Name`， `Age`，并`Location`中的可绑定属性`CustomCell`实例，为`Name`， `Age`，和`Location`基础集合中每个对象的属性。

在 C# 中的等效绑定以下的代码示例所示：

```csharp
var customCell = new DataTemplate (typeof(CustomCell));
customCell.SetBinding (CustomCell.NameProperty, "Name");
customCell.SetBinding (CustomCell.AgeProperty, "Age");
customCell.SetBinding (CustomCell.LocationProperty, "Location");

var listView = new ListView {
    ItemsSource = people,
    ItemTemplate = customCell
};
```

在 iOS 和 Android，如果[ `ListView` ](xref:Xamarin.Forms.ListView)回收元素和自定义单元格使用自定义呈现器，自定义呈现器必须正确实现属性更改通知。 重用单元格时其属性值将更改时的绑定上下文将与更新为的一个可用的单元格，`PropertyChanged`引发的事件的。 有关详细信息，请参阅[自定义 ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)。 有关单元格回收的详细信息，请参阅[缓存策略](~/xamarin-forms/user-interface/listview/performance.md#cachingstrategy)。

## <a name="related-links"></a>相关链接

- [构建的单元格 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/BuiltInCells)
- [自定义单元格 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/CustomCells)
- [绑定上下文更改 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/ListView/BindingContextChanged)
