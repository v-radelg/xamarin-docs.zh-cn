---
title: 自定义 ListView 单元格的外观
description: 本文探讨了表示 Xamarin.Forms 应用程序中的数据，同时利用 ListView 控件的方便的选项。
ms.prod: xamarin
ms.assetid: FD45CB91-1A8F-46FB-B432-6BC20492E456
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/12/2019
ms.openlocfilehash: ab54b54c9f2f7d6d7748137ea079439b7c3ddfca
ms.sourcegitcommit: a5ef4497db04dfa016865bc7454b3de6ff088554
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2019
ms.locfileid: "70998132"
---
# <a name="customizing-listview-cell-appearance"></a>自定义 ListView 单元格的外观

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)

Xamarin [`ListView`](xref:Xamarin.Forms.ListView)类用于显示可滚动列表，可通过`ViewCell`使用元素进行自定义。 `ViewCell`元素可以显示文本和图像，指示真/假状态和接收用户输入。

## <a name="built-in-cells"></a>内置的单元格
Xamarin 提供适用于许多应用程序的内置单元：

- [`TextCell`](#textcell)控件用于显示文本，其中包含可选的第二行详细信息文本。
- [`ImageCell`](#imagecell)控件类似于， `TextCell`但在文本左侧包含一个图像。
- `SwitchCell`控件用于显示和捕获开启/关闭或真/假状态。
- `EntryCell`控件用于显示用户可编辑的文本数据。

和控件更常见地用于的上下文[`TableView`](~/xamarin-forms/user-interface/tableview.md)中。 [`EntryCell`](~/xamarin-forms/user-interface/tableview.md#entrycell) [`SwitchCell`](~/xamarin-forms/user-interface/tableview.md#switchcell)

### <a name="textcell"></a>TextCell

[`TextCell`](xref:Xamarin.Forms.TextCell) 是用于显示文本，并且根据需要使用第二行详细信息的文本作为一个单元格。 以下屏幕截图显示`TextCell` iOS 和 Android 上的项：

![](customizing-cell-appearance-images/text-cell-default.png "默认 TextCell 示例")

TextCells 呈现为本机控件在运行时，因此性能非常好与自定义比较`ViewCell`。 TextCells 可自定义，允许你设置以下属性：

- `Text` &ndash; 在第一行，用大字体显示文本。
- `Detail` &ndash; 较小的字体中的第一行下面所示的文本。
- `TextColor` &ndash; 文本的颜色。
- `DetailColor` &ndash; 详细信息文本的颜色

以下屏幕截图显示`TextCell`具有自定义颜色属性的项目：

![](customizing-cell-appearance-images/text-cell-custom.png "自定义 TextCell 示例")

### <a name="imagecell"></a>ImageCell

[`ImageCell`](xref:Xamarin.Forms.ImageCell)如`TextCell`，可以用于显示文本和辅助的详细信息，并通过使用每个平台的本机控件提供优异的性能。 `ImageCell` 不同于`TextCell`，因为它的文本的左侧显示图像。

以下屏幕截图显示`ImageCell` iOS 和 Android 上的项：!["Default ImageCell Example"](customizing-cell-appearance-images/image-cell-default.png "默认 ImageCell 示例")

`ImageCell` 如果需要显示数据的可视方面，例如的联系人或电影列表的列表时很有用。 `ImageCell`可自定义，允许设置：

- `Text` &ndash; 在第一行，用大字体显示文本
- `Detail` &ndash; 较小的字体中的第一行下面所示的文本
- `TextColor` &ndash; 文本的颜色
- `DetailColor` &ndash; 详细信息文本的颜色
- `ImageSource` &ndash; 要在文本旁边显示的图像

以下屏幕截图显示`ImageCell`具有自定义颜色属性的项目：!["自定义的 ImageCell 示例"](customizing-cell-appearance-images/image-cell-custom.png "自定义的 ImageCell 示例")

## <a name="custom-cells"></a>自定义单元格
自定义单元使您能够创建内置单元不支持的单元布局。 例如，你可能想要提供两个标签具有相等的权重的单元格。 一个`TextCell`不足是因为`TextCell`具有较小的一个标签。 大多数单元格自定义添加其他只读数据 （如其他标签、 图像或其他显示信息）。

自定义的所有单元格必须派生自[ `ViewCell` ](xref:Xamarin.Forms.ViewCell)，所有内置的单元格类型使用相同的基类。

Xamarin 在`ListView`控件上提供[缓存行为](~/xamarin-forms/user-interface/listview/performance.md#caching-strategy)，可提高某些类型的自定义单元的滚动性能。

以下屏幕截图显示了自定义单元格的示例：

!["自定义单元示例"](customizing-cell-appearance-images/custom-cell.png "自定义单元格示例")

### <a name="xaml"></a>XAML
前面的屏幕截图中显示的自定义单元可通过以下 XAML 创建：

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

XAML 的工作方式如下：

- 自定义单元格嵌套在`DataTemplate`，这是内部`ListView.ItemTemplate`。 这与使用任何内置单元的过程相同。
- `ViewCell` 是类型的自定义单元格。 `DataTemplate`元素的子级必须是`ViewCell`类或派生自类。
- 在中`ViewCell`，布局可以由任意 Xamarin 布局进行管理。 在此示例中，布局由管理`StackLayout`，这允许自定义背景色。

> [!NOTE]
> 可绑定`StackLayout`的的任何属性都可以绑定到自定义单元中。 但 XAML 示例中未显示此功能。

### <a name="code"></a>代码

还可以在代码中创建自定义单元。 首先， `ViewCell`必须创建派生自的自定义类：

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

在页构造函数中，ListView 的`ItemTemplate`属性设置`DataTemplate`为， `CustomCell`其类型为：

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

    public string Name
    {
        get { return(string)GetValue (NameProperty); }
        set { SetValue (NameProperty, value); }
    }

    public int Age
    {
        get { return(int)GetValue (AgeProperty); }
        set { SetValue (AgeProperty, value); }
    }

    public string Location
    {
        get { return(string)GetValue (LocationProperty); }
        set { SetValue (LocationProperty, value); }
    }
    ...

    protected override void OnBindingContextChanged ()
    {
        base.OnBindingContextChanged ();

        if (BindingContext != null)
        {
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

var listView = new ListView
{
    ItemsSource = people,
    ItemTemplate = customCell
};
```

在 iOS 和 Android，如果[ `ListView` ](xref:Xamarin.Forms.ListView)回收元素和自定义单元格使用自定义呈现器，自定义呈现器必须正确实现属性更改通知。 重用单元格时其属性值将更改时的绑定上下文将与更新为的一个可用的单元格，`PropertyChanged`引发的事件的。 有关详细信息，请参阅[自定义 ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)。 有关单元格回收的详细信息，请参阅[缓存策略](~/xamarin-forms/user-interface/listview/performance.md#caching-strategy)。

## <a name="related-links"></a>相关链接

- [构建的单元格 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-builtincells)
- [自定义单元格 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)
- [绑定上下文更改 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-bindingcontextchanged)
