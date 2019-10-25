---
title: Xamarin Map 引脚
description: 本文介绍如何在 Xamarin. Forms 地图上创建 pin。
ms.prod: xamarin
ms.assetid: F8FC081B-A811-4FBB-B8F8-30D6FD36BD40
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/23/2019
ms.openlocfilehash: a2fb0ba2036dfe34e85c7bebab6ecb55cd868ad5
ms.sourcegitcommit: 5c22097bed2a8d51ecaf6ca197bf4d449dfe1377
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72810498"
---
# <a name="xamarinforms-map-pins"></a>Xamarin Map 引脚

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

Xamarin [`Map`](xref:Xamarin.Forms.Maps.Map)控件允许将位置标记[`Pin`](xref:Xamarin.Forms.Maps.Pin)对象。 `Pin` 是在点击时打开信息窗口的地图标记：

[![IOS 和 Android 上的地图 pin 及其信息窗口的屏幕截图](pins-images/pin-and-information-window.png "用信息窗口映射 pin")](pins-images/pin-and-information-window-large.png#lightbox "用信息窗口映射 pin")

将[`Pin`](xref:Xamarin.Forms.Maps.Pin)对象添加到[`Map.Pins`](xref:Xamarin.Forms.Maps.Pin)集合时，会在映射上呈现该固定。

[`Pin`](xref:Xamarin.Forms.Maps.Pin)类具有以下属性：

- `string`类型的[`Address`](xref:Xamarin.Forms.Maps.Pin.Address)，通常表示 pin 位置的地址。 但是，它可以是任何 `string` 内容，而不仅仅是地址。
- `string`类型的[`Label`](xref:Xamarin.Forms.Maps.Pin.Label)，通常表示 pin 标题。
- [`Position`](xref:Xamarin.Forms.Maps.Position)类型的[`Position`](xref:Xamarin.Forms.Maps.Pin.Position)，表示 pin 的纬度和经度。
- [`Type`](xref:Xamarin.Forms.Maps.Pin.Type)，类型[`PinType`](xref:Xamarin.Forms.Maps.PinType)，表示 pin 类型。

这些属性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持的，这意味着 `Pin` 可以是数据绑定的目标。 有关 `Pin` 对象的数据绑定的详细信息，请参阅[显示固定的集合](#display-a-pin-collection)。

此外， [`Pin`](xref:Xamarin.Forms.Maps.Pin)类定义 `MarkerClicked` 和 `InfoWindowClicked` 事件。 当点击某个 pin 时，将激发 `MarkerClicked` 事件，并在点击信息窗口时激发 `InfoWindowClicked` 事件。 同时提供两个事件的 `PinClickedEventArgs` 对象都有一个类型为 `bool`的 `HideInfoWindow` 属性。

## <a name="display-a-pin"></a>显示 pin

可以将[`Pin`](xref:Xamarin.Forms.Maps.Pin)添加到 XAML 中的[`Map`](xref:Xamarin.Forms.Maps.Map) ：

```xaml
<ContentPage ...
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps">
     <maps:Map x:Name="map"
               IsShowingUser="True"
               MoveToLastRegionOnLayoutChange="False"
               HeightRequest="100"                  
               WidthRequest="960"
               VerticalOptions="FillAndExpand">
         <x:Arguments>
             <maps:MapSpan>
                 <x:Arguments>
                     <maps:Position>
                         <x:Arguments>
                             <x:Double>36.9628066</x:Double>
                             <x:Double>-122.0194722</x:Double>
                         </x:Arguments>
                     </maps:Position>
                     <x:Double>0.01</x:Double>
                     <x:Double>0.01</x:Double>
                 </x:Arguments>
             </maps:MapSpan>
         </x:Arguments>
         <maps:Map.Pins>
             <maps:Pin Label="Santa Cruz"
                       Address="The city with a boardwalk"
                       Type="Place">
                 <maps:Pin.Position>
                     <maps:Position>
                         <x:Arguments>
                             <x:Double>36.9628066</x:Double>
                             <x:Double>-122.0194722</x:Double>
                         </x:Arguments>
                     </maps:Position>
                 </maps:Pin.Position>
             </maps:Pin>
         </maps:Map.Pins>
     </maps:Map>
</ContentPage>
```

此 XAML 创建一个[`Map`](xref:Xamarin.Forms.Maps.Map)对象，该对象显示由[`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan)对象指定的区域。 `MapSpan` 对象以由[`Position`](xref:Xamarin.Forms.Maps.Position)对象表示的纬度和经度为中心，该对象扩展了0.01 的纬度和经度度。 [`Pin`](xref:Xamarin.Forms.Maps.Pin)对象将添加到[`Map.Pins`](xref:Xamarin.Forms.Maps.Pin)集合，并在 `Map` 的[`Position`](xref:Xamarin.Forms.Maps.Pin.Position)属性指定的位置绘制该对象。 有关将 XAML 中的参数传递给缺少默认构造函数的对象的信息，请参阅[在 xaml 中传递参数](~/xamarin-forms/xaml/passing-arguments.md)。

> [!NOTE]
> [`Position`](xref:Xamarin.Forms.Maps.Position)结构定义只读[`Latitude`](xref:Xamarin.Forms.Maps.Position.Latitude)和[`Longitude`](xref:Xamarin.Forms.Maps.Position.Longitude)属性，两者都是 `double`类型。 当通过其构造函数创建 `Position` 对象时，纬度值将为-90.0 和90.0 之间的限制，而经度值将为限制-180.0 和180.0 之间。

等效 C# 代码如下：

```csharp
using Xamarin.Forms.Maps;
// ...
Map map = new Map
{
  // ...
};
Pin pin = new Pin
{
  Label = "Santa Cruz",
  Address = "The city with a boardwalk",
  Type = PinType.Place,
  Position = new Position(36.9628066, -122.0194722)
};
map.Pins.Add(pin);
```

> [!WARNING]
> 如果无法设置[`Pin.Label`](xref:Xamarin.Forms.Maps.Pin.Label)属性，则在将[`Pin`](xref:Xamarin.Forms.Maps.Pin)添加到[`Map`](xref:Xamarin.Forms.Maps.Map)时将引发 `ArgumentException`。

此示例代码会导致在地图上呈现一个插针：

[![IOS 和 Android 上的地图 pin 的屏幕截图](pins-images/pin-only.png "地图 pin")](pins-images/pin-only-large.png#lightbox "地图 pin")

## <a name="interact-with-a-pin"></a>与 pin 交互

默认情况下，在点击[`Pin`](xref:Xamarin.Forms.Maps.Pin)时，将显示其信息窗口：

[![IOS 和 Android 上的地图 pin 及其信息窗口的屏幕截图](pins-images/pin-and-information-window.png "用信息窗口映射 pin")](pins-images/pin-and-information-window-large.png#lightbox "用信息窗口映射 pin")

点击地图上的其他位置会关闭信息窗口。

[`Pin`](xref:Xamarin.Forms.Maps.Pin)类定义 `MarkerClicked` 事件，该事件在点击 `Pin` 时激发。 不需要处理此事件以显示信息窗口。 相反，应仅在需要通知已点击特定 pin 时才处理此事件。

[`Pin`](xref:Xamarin.Forms.Maps.Pin)类还定义了在点击信息窗口时触发的 `InfoWindowClicked` 事件。 当需要通知已点击特定信息窗口时，应处理此事件。

下面的代码显示了处理这些事件的示例：

```csharp
using Xamarin.Forms.Maps;
// ...
Pin boardwalkPin = new Pin
{
    Position = new Position(36.9641949, -122.0177232),
    Label = "Boardwalk",
    Address = "Santa Cruz",
    Type = PinType.Place
};
boardwalkPin.MarkerClicked += async (s, args) =>
{
    args.HideInfoWindow = true;
    string pinName = ((Pin)s).Label;
    await DisplayAlert("Pin Clicked", $"{pinName} was clicked.", "Ok");
};

Pin wharfPin = new Pin
{
    Position = new Position(36.9571571, -122.0173544),
    Label = "Wharf",
    Address = "Santa Cruz",
    Type = PinType.Place
};
wharfPin.InfoWindowClicked += async (s, args) =>
{
    string pinName = ((Pin)s).Label;
    await DisplayAlert("Info Window Clicked", $"The info window was clicked for {pinName}.", "Ok");
};
```

同时提供两个事件的 `PinClickedEventArgs` 对象都有一个类型为 `bool`的 `HideInfoWindow` 属性。 如果将此属性设置为在事件处理程序中 `true`，则将隐藏 "信息" 窗口。

## <a name="pin-types"></a>固定类型

[`Pin`](xref:Xamarin.Forms.Maps.Pin)对象包括类型[`PinType`](xref:Xamarin.Forms.Maps.PinType)的[`Type`](xref:Xamarin.Forms.Maps.Pin.Type)属性，该属性表示 pin 的类型。 `PinType` 枚举定义下列成员：

- `Generic`，表示泛型 pin。
- `Place`，表示位置的 pin。
- `SavedPin`，表示保存位置的 pin。
- `SearchResult`，表示搜索结果的 pin。

但是，将[`Pin.Type`](xref:Xamarin.Forms.Maps.Pin.Type)属性设置为任何[`PinType`](xref:Xamarin.Forms.Maps.PinType)成员不会更改呈现的 pin 的外观。 相反，您必须创建自定义呈现器来自定义 pin 外观。 有关详细信息，请参阅[自定义地图 pin](~/xamarin-forms/app-fundamentals/custom-renderer/map/customized-pin.md)。

## <a name="display-a-pin-collection"></a>显示 pin 集合

[`Map`](xref:Xamarin.Forms.Maps.Map)类定义以下属性：

- [`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource)类型 `IEnumerable`，它指定要显示的 `IEnumerable` 项的集合。
- [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)类型的[`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate)，它指定要应用于显示的项集合中的每个项的[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 。
- `ItemTemplateSelector`类型[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)，它指定在运行时用于为项选择[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)的[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) 。

> [!IMPORTANT]
> 如果同时设置了 `ItemTemplate` 和 `ItemTemplateSelector` 属性，则[`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate)属性将优先。

通过使用数据绑定将其[`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource)属性绑定到 `IEnumerable` 集合，可以使用 pin 填充[`Map`](xref:Xamarin.Forms.Maps.Map) ：

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

[`ItemsSource`](xref:Xamarin.Forms.Maps.Map.ItemsSource)属性数据绑定到连接的 viewmodel 的 `Locations` 属性，该属性返回 `Location` 对象的 `ObservableCollection`，这是一个自定义类型。 每个 `Location` 对象均定义类型 `string` `Address` 和 `Description` 属性，并[定义 `Position` 类型](xref:Xamarin.Forms.Maps.Position)的 `Position` 属性。

通过将[`ItemTemplate`](xref:Xamarin.Forms.Maps.Map.ItemTemplate)属性设置为包含数据绑定到相应属性的[`Pin`](xref:Xamarin.Forms.Maps.Pin)对象的[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ，来定义 `IEnumerable` 集合中每个项的外观。

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

## <a name="related-links"></a>相关链接

- [地图示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [映射自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)
- [在 XAML 中传递参数](~/xamarin-forms/xaml/passing-arguments.md)
- [创建 Xamarin. Forms 并重](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
