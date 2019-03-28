---
title: 填充数据的 Xamarin.Forms 之间导航
description: 通过将其 ItemsSource 属性设置为任何实现 IEnumerable 的集合情况下，使用数据填充之间导航。
ms.prod: xamarin
ms.assetid: E1783E34-1C0F-401A-80D5-B2BE5508F5F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/15/2019
ms.openlocfilehash: 57012202d981b96dba42f3017a19f2e32e4982ec
ms.sourcegitcommit: a7170494e1975f0f1be547a45444752fd8e57819
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2019
ms.locfileid: "58507183"
---
# <a name="populate-xamarinforms-collectionview-with-data"></a>填充数据的 Xamarin.Forms 之间导航

![预览](~/media/shared/preview.png)

[![下载示例](~/media/shared/download.png)下载示例](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)

> [!IMPORTANT]
> `CollectionView`当前为预览版，并且缺少一些其计划的功能。 此外，API 可能会更改为实现已完成。

`CollectionView` 定义以下属性，定义要显示的数据，其外观：

- `ItemsSource`类型的`IEnumerable`，指定要显示项目的集合，默认值为`null`。
- `ItemTemplate`类型的[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)，指定要应用于要显示的项集合中的每个项目的模板。

这些属性受到[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)对象，这意味着，属性可以是数据绑定的目标。

## <a name="populate-a-collectionview-with-data"></a>填充数据 CollectionView

一个`CollectionView`通过设置使用数据填充其`ItemsSource`属性设置为任何实现的集合`IEnumerable`。 项可以在 XAML 中通过初始化中添加`ItemsSource`属性从一个字符串数组：

```xaml
<CollectionView>
  <CollectionView.ItemsSource>
    <x:Array Type="{x:Type x:String}">
      <x:String>Baboon</x:String>
      <x:String>Capuchin Monkey</x:String>
      <x:String>Blue Monkey</x:String>
      <x:String>Squirrel Monkey</x:String>
      <x:String>Golden Lion Tamarin</x:String>
      <x:String>Howler Monkey</x:String>
      <x:String>Japanese Macaque</x:String>
    </x:Array>
  </CollectionView.ItemsSource>
</CollectionView>
```

> [!NOTE]
> 请注意，`x:Array` 元素需要用于指示数组中项目类型的 `Type` 属性。

等效的 C# 代码是：

```csharp
CollectionView collectionView = new CollectionView();
collectionView.ItemsSource = new string[]
{
    "Baboon",
    "Capuchin Monkey",
    "Blue Monkey",
    "Squirrel Monkey",
    "Golden Lion Tamarin",
    "Howler Monkey",
    "Japanese Macaque"
};
```

> [!IMPORTANT]
> 如果`CollectionView`是所需刷新，如添加、 移除或更改基础集合中的项，应为基础集合`IEnumerable`集合，其中将发送属性更改通知，例如`ObservableCollection`。

默认情况下，`CollectionView`垂直列表中显示项，如以下屏幕截图中所示：

[![包含文本项，在 iOS 和 Android 上的 CollectionView 的屏幕截图](populate-data-images/text.png "文本项中 CollectionView")](populate-data-images/text-large.png#lightbox "之间导航中的文本项")

有关如何更改的信息`CollectionView`布局，请参阅[指定布局](layout.md)。 了解如何定义在每个项的外观`CollectionView`，请参阅[定义项外观](#define-item-appearance)。

### <a name="data-binding"></a>数据绑定

`CollectionView` 通过使用数据绑定来绑定可以使用数据填充其`ItemsSource`属性设置为`IEnumerable`集合。 在 XAML，这实现与`Binding`标记扩展：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}" />
```

等效的 C# 代码是：

```csharp
CollectionView collectionView = new CollectionView();
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

在此示例中，`ItemsSource`将属性数据绑定到`Monkeys`连接的视图模型属性。

> [!NOTE]
> 可以启用已编译的绑定，以提高 Xamarin.Forms 应用程序中的数据绑定性能。 有关详细信息，请参阅[编译绑定](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md)。

有关数据绑定的详细信息，请参阅 [Xamarin.Forms 数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)。

## <a name="define-item-appearance"></a>定义项外观

在每个项的外观`CollectionView`可以通过设置定义`CollectionView.ItemTemplate`属性设置为[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate):

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="Auto" />
                    <ColumnDefinition Width="Auto" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
    ...
</CollectionView>
```

等效的 C# 代码是：

```csharp
CollectionView collectionView = new CollectionView();
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");

collectionView.ItemTemplate = new DataTemplate(() =>
{
    Grid grid = new Grid { Padding = 10 };
    grid.RowDefinitions.Add(new RowDefinition { Height = GridLength.Auto });
    grid.RowDefinitions.Add(new RowDefinition { Height = GridLength.Auto });
    grid.ColumnDefinitions.Add(new ColumnDefinition { Width = GridLength.Auto });
    grid.ColumnDefinitions.Add(new ColumnDefinition { Width = GridLength.Auto });

    Image image = new Image { Aspect = Aspect.AspectFill, HeightRequest = 60, WidthRequest = 60 };
    image.SetBinding(Image.SourceProperty, "ImageUrl");

    Label nameLabel = new Label { FontAttributes = FontAttributes.Bold };
    nameLabel.SetBinding(Label.TextProperty, "Name");

    Label locationLabel = new Label { FontAttributes = FontAttributes.Italic, VerticalOptions = LayoutOptions.End };
    locationLabel.SetBinding(Label.TextProperty, "Location");

    Grid.SetRowSpan(image, 2);

    grid.Children.Add(image);
    grid.Children.Add(nameLabel, 1, 0);
    grid.Children.Add(locationLabel, 1, 1);

    return grid;
});
```

中指定的元素[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)在列表中定义每个项的外观。 在示例中中的布局`DataTemplate`由[ `Grid` ](xref:Xamarin.Forms.Grid)。 `Grid`包含[ `Image` ](xref:Xamarin.Forms.Image)对象和两个[ `Label` ](xref:Xamarin.Forms.Label)对象时，所有绑定到的属性`Monkey`类：

```csharp
public class Monkey
{
    public string Name { get; set; }
    public string Location { get; set; }
    public string Details { get; set; }
    public string ImageUrl { get; set; }
}
```

下面的屏幕截图显示的模板化结果列表中的每个项：

[![每个项是 iOS 和 Android 上模板化 CollectionView 的屏幕截图](populate-data-images/datatemplate.png "之间导航中的模板化项")](populate-data-images/datatemplate-large.png#lightbox "之间导航中的模板化项")

有关数据模板的详细信息，请参阅 [Xamarin.Forms 数据模板](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。

## <a name="related-links"></a>相关链接

- [CollectionView （示例）](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)
- [Xamarin.Forms 数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Xamarin.Forms 数据模板](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
