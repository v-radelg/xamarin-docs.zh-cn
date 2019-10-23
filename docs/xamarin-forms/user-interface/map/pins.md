---
title: Xamarin Map 引脚
description: 本文介绍如何在 Xamarin. Forms 地图上创建 pin。
ms.prod: xamarin
ms.assetid: F8FC081B-A811-4FBB-B8F8-30D6FD36BD40
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 09/23/2019
ms.openlocfilehash: 76535f9c31a9dc138e132a3e582b986daf89bdb0
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697667"
---
# <a name="xamarinforms-map-pins"></a>Xamarin Map 引脚

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

Xamarin `Maps` 控件允许将位置标记 `Pin` 对象。 @No__t_0 是在单击或点击时打开信息窗口的地图标记。

@No__t_0 类具有以下属性：

- `Type` 是 `PinType` 枚举值： Generic、Place、SavedPin 或 SearchResult。
- `Position` 是一个 `Position` 实例，其中包含 pin 的纬度和经度。
- `Label` 是一种通常作为 pin 标题显示的 `string`。
- `Address` 是将在 "信息" 窗口中显示的 `string`。 它可以是任何 `string` 内容，而不仅仅是地址。

这些属性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持的，这意味着 `Pin` 可以是数据绑定的目标。 有关详细信息，请参阅[通过数据绑定创建 pin](#create-pins-with-data-binding)。

## <a name="create-map-pins"></a>创建地图引脚

可以在代码中创建 `Pin` 实例，并将其添加到地图中：

```csharp
Pin pin1 = new Pin
{
    Type = PinType.Place,
    Position = new Position(47.6368678, -122.137305),
    Label = "Example Pin 1",
    Address = "Example custom details..."
};
map.Pins.Add(pin1);
```

> [!NOTE]
> @No__t_0 值将影响如何根据平台呈现 pin。 若要自定义 pin 的外观，必须创建自定义呈现器。 有关详细信息，请参阅[自定义地图 pin](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)。

## <a name="create-pins-with-data-binding"></a>通过数据绑定创建 pin

[@No__t_1](xref:Xamarin.Forms.Maps.Map)类公开以下属性：

- `ItemsSource` –指定要显示的 `IEnumerable` 项的集合。
- `ItemTemplate` –指定要应用于显示的项集合中的每个项的[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 。
- `ItemTemplateSelector` –指定将用于在运行时为项选择[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)的[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) 。

> [!NOTE]
> 如果同时设置了 `ItemTemplate` 和 `ItemTemplateSelector` 属性，则 `ItemTemplate` 属性将优先。

通过使用数据绑定将其 `ItemsSource` 属性绑定到 `IEnumerable` 集合，可以使用数据填充[`Map`](xref:Xamarin.Forms.Maps.Map) ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps"
             x:Class="WorkingWithMaps.PinItemsSourcePage">
    <Grid>
        ...
        <maps:Map x:Name="map"
                  MoveToLastRegionOnLayoutChange="false"
                  ItemsSource="{Binding Locations}">
            <maps:Map.ItemTemplate>
                <DataTemplate>
                    <maps:Pin Position="{Binding Position}"
                              Address="{Binding Address}"
                              Label="{Binding Description}" />
                </DataTemplate>
            </maps:Map.ItemTemplate>
        </maps:Map>
        ...
    </Grid>
</ContentPage>
```

@No__t_0 属性数据绑定到连接视图模型的 `Locations` 属性，该属性返回 `Location` 对象的 `ObservableCollection`，这是一个自定义类型。 每个 `Location` 对象均定义类型 `string` `Address` 和 `Description` 属性，并[定义 `Position` 类型](xref:Xamarin.Forms.Maps.Position)的 `Position` 属性。

通过将 `ItemTemplate` 属性设置为包含数据绑定到相应属性的[`Pin`](xref:Xamarin.Forms.Maps.Pin)对象的[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ，来定义 `IEnumerable` 集合中每个项的外观。

以下屏幕截图显示了使用数据绑定显示[`Pin`](xref:Xamarin.Forms.Maps.Pin)集合的[`Map`](xref:Xamarin.Forms.Maps.Map) ：

[![在 iOS 和 Android 上带有数据绑定插针的地图屏幕截图](map-images/pins-itemssource.png "具有数据绑定插针的映射")](map-images/pins-itemssource-large.png#lightbox "具有数据绑定插针的映射")

### <a name="choose-item-appearance-at-runtime"></a>在运行时选择项外观

@No__t_0 集合中每个项的外观都可以根据项值在运行时选择，方法是将 `ItemTemplateSelector` 属性设置为[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)：

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:WorkingWithMaps"
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
    <ContentPage.Resources>
        <local:MapItemTemplateSelector x:Key="MapItemTemplateSelector">
            <local:MapItemTemplateSelector.DefaultTemplate>
                <DataTemplate>
                    <maps:Pin Position="{Binding Position}"
                              Address="{Binding Address}"
                              Label="{Binding Description}" />
                </DataTemplate>
            </local:MapItemTemplateSelector.DefaultTemplate>
            <local:MapItemTemplateSelector.XamarinTemplate>
                <DataTemplate>
                    <maps:Pin Position="{Binding Position}"
                              Address="{Binding Address}"
                              Label="Xamarin!" />
                </DataTemplate>
            </local:MapItemTemplateSelector.XamarinTemplate>    
        </local:MapItemTemplateSelector>
    </ContentPage.Resources>

    <Grid>
        ...
        <maps:Map x:Name="map"
                  ItemsSource="{Binding Locations}"
                  ItemTemplateSelector="{StaticResource MapItemTemplateSelector}" />
        ...
    </Grid>
</ContentPage>
```

下面的示例演示了 `MapItemTemplateSelector` 类：

```csharp
public class MapItemTemplateSelector : DataTemplateSelector
{
    public DataTemplate DefaultTemplate { get; set; }
    public DataTemplate XamarinTemplate { get; set; }

    protected override DataTemplate OnSelectTemplate(object item, BindableObject container)
    {
        return ((Location)item).Address.Contains("San Francisco") ? XamarinTemplate : DefaultTemplate;
    }
}
```

@No__t_0 类定义设置为不同数据模板 `DefaultTemplate` 和 `XamarinTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)属性。 @No__t_0 方法返回 `XamarinTemplate`，在点击 `Pin` 时将 "Xamarin" 显示为标签，而当项具有包含 "旧金山" 的地址时。 当项没有包含 "旧金山" 的地址时，`OnSelectTemplate` 方法返回 `DefaultTemplate`。

有关数据模板选择器的详细信息，请参阅[创建 Xamarin。窗体并重](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)。

## <a name="pin-events"></a>固定事件

@No__t_0 类提供两个事件：

- 当单击或点击该 pin 时，将激发 `MarkerClicked`。
- 当单击或点击 "信息" 窗口时，将激发 `InfoWindowClicked`。

可以在代码中将事件处理程序附加到 pin：

```csharp
public class PinEvents: ContentPage
{
    public PinEvents()
    {
        // ...

        pin1.MarkerClicked += OnMarkerClickedAsync;
        pin1.InfoWindowClicked += OnInfoWindowClickedAsync;
    }

    async void OnMarkerClickedAsync(object sender, PinClickedEventArgs e)
    {
        string pinName = ((Pin)sender).Label;
        await DisplayAlert("Pin Clicked", $"{pinName} was clicked.", "Ok");
    }

    async void OnInfoWindowClickedAsync(object sender, PinClickedEventArgs e)
    {
        string pinName = ((Pin)sender).Label;
        await DisplayAlert("Info Window Clicked", $"The info window was clicked for {pinName}.", "Ok");
    }
}
```

## <a name="related-links"></a>相关链接

- [地图示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [映射自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)
- [创建 Xamarin. Forms 并重](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
