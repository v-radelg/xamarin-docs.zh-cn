---
title: Xamarin CollectionView 数据
description: 使用数据填充 CollectionView，方法是将其 System.windows.controls.itemscontrol.itemssource 属性设置为任何实现 IEnumerable 的集合。
ms.prod: xamarin
ms.assetid: E1783E34-1C0F-401A-80D5-B2BE5508F5F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/13/2019
ms.openlocfilehash: 6942baed6af2a2e9b2c713a8fe08cf4c8ed4416b
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "69888549"
---
# <a name="xamarinforms-collectionview-data"></a>Xamarin CollectionView 数据

![](~/media/shared/preview.png "此 API 当前为预发布版本")

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)定义以下属性，这些属性定义要显示的数据及其外观：

- [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource)类型`IEnumerable`为的指定要显示的项的集合，其默认`null`值为。
- [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate)类型[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)为的指定要应用于要显示的项集合中的每一项的模板。

这些属性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持的，这意味着属性可以是数据绑定的目标。

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView)定义一个`ItemsUpdatingScrollMode`属性，该属性表示在添加新`CollectionView`项时的滚动行为。 有关此属性的详细信息，请参阅[在添加新项时控制滚动位置](scrolling.md#control-scroll-position-when-new-items-are-added)。

[`CollectionView`](xref:Xamarin.Forms.CollectionView)还可以在用户滚动时以增量方式加载数据。 有关详细信息，请参阅[以增量方式加载数据](#load-data-incrementally)。

## <a name="populate-a-collectionview-with-data"></a>使用数据填充 CollectionView

使用数据填充数据，方法[`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) `IEnumerable`是将其属性设置为任何实现的集合。 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 可以通过从字符串数组中初始化`ItemsSource`属性，在 XAML 中添加项：

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

等效 C# 代码如下：

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
> 如果需要在基础集合中添加、删除或更改项时进行刷新，则基础集合应`IEnumerable`为发送属性`ObservableCollection`更改通知的集合，如。 [`CollectionView`](xref:Xamarin.Forms.CollectionView)

默认情况下[`CollectionView`](xref:Xamarin.Forms.CollectionView) ，显示垂直列表中的项，如以下屏幕截图所示：

[![在 iOS 和 Android 上包含文本项的 CollectionView 的屏幕截图](populate-data-images/text.png "CollectionView 中的文本项")](populate-data-images/text-large.png#lightbox "CollectionView 中的文本项")

有关如何更改[`CollectionView`](xref:Xamarin.Forms.CollectionView)布局的信息，请参阅[指定布局](layout.md)。 有关如何定义中`CollectionView`每个项的外观的信息，请参阅[定义项外观](#define-item-appearance)。

### <a name="data-binding"></a>数据绑定

[`CollectionView`](xref:Xamarin.Forms.CollectionView)使用数据绑定将其[`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource)属性绑定`IEnumerable`到集合，可以填充数据。 在 XAML 中，这是通过`Binding`标记扩展实现的：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}" />
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView();
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

在此示例中， [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource)属性数据绑定`Monkeys`到连接的 viewmodel 的属性。

> [!NOTE]
> 可以启用编译的绑定以提高 Xamarin. Forms 应用程序中的数据绑定性能。 有关详细信息，请参阅[已编译的绑定](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md)。

有关数据绑定的详细信息，请参阅 [Xamarin.Forms 数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)。

## <a name="define-item-appearance"></a>定义项外观

[`CollectionView`](xref:Xamarin.Forms.CollectionView)可以通过[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)将属性设置为来定义中每个项的外观： [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate)

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

等效 C# 代码如下：

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

在中[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)指定的元素定义列表中每一项的外观。 在此示例中，中的`DataTemplate`布局由管理。 [`Grid`](xref:Xamarin.Forms.Grid) 包含一个[`Image`](xref:Xamarin.Forms.Image)对象和两个[`Label`](xref:Xamarin.Forms.Label)对象`Monkey` ，这些对象都绑定到类的属性： `Grid`

```csharp
public class Monkey
{
    public string Name { get; set; }
    public string Location { get; set; }
    public string Details { get; set; }
    public string ImageUrl { get; set; }
}
```

以下屏幕截图显示列表中每一项的模板化结果：

[![IOS 和 Android 上每个项的模板 CollectionView 的屏幕截图](populate-data-images/datatemplate.png "CollectionView 中的模板化项")](populate-data-images/datatemplate-large.png#lightbox "CollectionView 中的模板化项")

有关数据模板的详细信息，请参阅 [Xamarin.Forms 数据模板](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。

## <a name="choose-item-appearance-at-runtime"></a>在运行时选择项外观

通过[`CollectionView`](xref:Xamarin.Forms.CollectionView) [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)将属性设置为对象，可在运行时根据项值选择每个项的外观： [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate)

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:CollectionViewDemos.Controls">
    <ContentPage.Resources>
        <DataTemplate x:Key="AmericanMonkeyTemplate">
            ...
        </DataTemplate>

        <DataTemplate x:Key="OtherMonkeyTemplate">
            ...
        </DataTemplate>

        <controls:MonkeyDataTemplateSelector x:Key="MonkeySelector"
                                             AmericanMonkey="{StaticResource AmericanMonkeyTemplate}"
                                             OtherMonkey="{StaticResource OtherMonkeyTemplate}" />
    </ContentPage.Resources>

    <CollectionView ItemsSource="{Binding Monkeys}"
                    ItemTemplate="{StaticResource MonkeySelector}" />
</ContentPage>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    ItemTemplate = new MonkeyDataTemplateSelector { ... }
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

[`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate)属性设置`MonkeyDataTemplateSelector`为对象。 下面的示例演示`MonkeyDataTemplateSelector`了类:

```csharp
public class MonkeyDataTemplateSelector : DataTemplateSelector
{
    public DataTemplate AmericanMonkey { get; set; }
    public DataTemplate OtherMonkey { get; set; }

    protected override DataTemplate OnSelectTemplate(object item, BindableObject container)
    {
        return ((Monkey)item).Location.Contains("America") ? AmericanMonkey : OtherMonkey;
    }
}
```

类定义`AmericanMonkey`了并`OtherMonkey`将[属性设置为不同的数据模板。`DataTemplate`](xref:Xamarin.Forms.DataTemplate) `MonkeyDataTemplateSelector` 重写将`AmericanMonkey`返回模板，该模板将在猴子名称包含 "北美洲" 时显示猴子名称和位置。 `OnSelectTemplate` 当猴子名称中不包含 "美洲" 时， `OnSelectTemplate`该重写`OtherMonkey`将返回模板，该模板将在银中显示猴子名称和位置：

[![IOS 和 Android 上的 CollectionView 运行时项模板选择屏幕截图](populate-data-images/datatemplateselector.png "CollectionView 中的运行时项模板选择")](populate-data-images/datatemplateselector-large.png#lightbox "CollectionView 中的运行时项模板选择")

有关数据模板选择器的详细信息，请参阅[Create a Xamarin. Forms 并重](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)。

> [!IMPORTANT]
> 使用[`CollectionView`](xref:Xamarin.Forms.CollectionView)时，不要将[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)对象的根元素设置为`ViewCell`。 这将导致引发异常，因为`CollectionView`没有单元的概念。

## <a name="load-data-incrementally"></a>以增量方式加载数据

[`CollectionView`](xref:Xamarin.Forms.CollectionView)支持在用户滚动项目时以增量方式加载数据。 这可以实现各种方案，例如，在用户滚动时，从 web 服务异步加载数据页。 此外，还可以配置加载更多数据的点，以便用户不会看到空白空间，也不会停止滚动。

[`CollectionView`](xref:Xamarin.Forms.CollectionView)定义以下属性以控制数据的增量加载：

- `RemainingItemsThreshold`，类型`int`为，在`RemainingItemsThresholdReached`事件激发时，列表中尚未显示的项的阈值。
- `RemainingItemsThresholdReachedCommand`，类型`ICommand`为，在达到`RemainingItemsThreshold`时执行。
- `RemainingItemsThresholdReachedCommandParameter`，属于 `object` 类型，是传递给 `RemainingItemsThresholdReachedCommand` 的参数。

[`CollectionView`](xref:Xamarin.Forms.CollectionView)还定义了`RemainingItemsThresholdReached`一个事件，该事件在`CollectionView`滚动到足够多的`RemainingItemsThreshold`项尚未显示时激发。 可以处理此事件以加载更多项。 此外，当引发`RemainingItemsThresholdReached` `RemainingItemsThresholdReachedCommand`事件时，将执行，以便在 viewmodel 中进行增量数据加载。

此`RemainingItemsThreshold`属性的默认值为-1，表示将永远不会`RemainingItemsThresholdReached`触发该事件。 当属性值为0时， `RemainingItemsThresholdReached`将在显示[`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource)中的最后一项时触发事件。 对于大于0的值， `RemainingItemsThresholdReached` `ItemsSource`当包含该数目的项尚未滚动到时，将触发事件。

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView)`RemainingItemsThreshold`验证属性，使其值始终大于或等于-1。

下面的 XAML 示例显示了[`CollectionView`](xref:Xamarin.Forms.CollectionView)一个以增量方式加载数据的：

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                RemainingItemsThreshold="5"
                RemainingItemsThresholdReached="OnCollectionViewRemainingItemsThresholdReached">
    ...
</CollectionView>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    RemainingItemsThreshold = 5
};
collectionView.RemainingItemsThresholdReached += OnCollectionViewRemainingItemsThresholdReached;
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Animals");
```

在此代码示例中， `RemainingItemsThresholdReached`当有5个项尚未滚动到并且在响应中`OnCollectionViewRemainingItemsThresholdReached`执行事件处理程序时，将触发事件：

```csharp
void OnCollectionViewRemainingItemsThresholdReached(object sender, EventArgs e)
{
    // Retrieve more data here and add it to the CollectionView's ItemsSource collection.
}
```

> [!NOTE]
> 还可以通过将绑定`RemainingItemsThresholdReachedCommand` `ICommand`到 viewmodel 中的实现来增量方式加载数据。

## <a name="related-links"></a>相关链接

- [CollectionView （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Xamarin. 窗体数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Xamarin. Forms 数据模板](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [创建 Xamarin. Forms 并重](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
