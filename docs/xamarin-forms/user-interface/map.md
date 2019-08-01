---
title: Xamarin.Forms 地图
description: 本文介绍如何使用 Xamarin.Forms 地图类在每个平台上使用本机地图 API，从而为用户提供熟悉的地图体验。
ms.prod: xamarin
ms.assetid: 59CD1344-8248-406C-9144-0C8A67141E5B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/13/2019
ms.openlocfilehash: 51555382e38412b7fa2c5c821b252984758bb1ec
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656969"
---
# <a name="xamarinforms-map"></a>Xamarin.Forms 地图

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)

_Xamarin.Forms 每个平台上使用本机映射 Api。_

Xamarin.Forms.Maps 在每个平台上使用本机地图 API。 这为用户提供了快速、熟悉的地图体验，但这意味着需要执行一些配置步骤以符合每个平台的 API 要求。
配置完成后，`Map`控制的工作方式就像在常见的代码中的任何其他 Xamarin.Forms 元素。

已在使用地图控件[MapsSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)示例，如下所示。

 [![MobileCRM 示例中的地图](map-images/maps-zoom-sml.png "地图控件示例")](map-images/maps-zoom.png#lightbox "地图控件示例")

可通过创建[地图自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)来进一步增强地图功能。

<a name="Maps_Initialization" />

## <a name="map-initialization"></a>地图初始化

将地图添加到 Xamarin.Forms 应用程序时**Xamarin.Forms.Maps**是一个单独的 NuGet 包，应添加到解决方案中的每个项目。
在 Android 上，这也存在依赖关系添加 Xamarin.Forms.Maps 时，会自动下载的 GooglePlayServices (另一个 NuGet)。

安装 NuGet 包后, 一些初始化代码需要在每个应用程序项目中，*后*`Xamarin.Forms.Forms.Init`方法调用。 对于 iOS 使用以下代码：

```csharp
Xamarin.FormsMaps.Init();
```

必须将相同的参数传递在 Android 上`Forms.Init`:

```csharp
Xamarin.FormsMaps.Init(this, bundle);
```

有关通用 Windows 平台 (UWP) 中使用以下代码：

```csharp
Xamarin.FormsMaps.Init("INSERT_AUTHENTICATION_TOKEN_HERE");
```

为每个平台在以下文件中添加此调用：

-  **iOS** -AppDelegate.cs 文件中，在`FinishedLaunching`方法。
-  **Android** -MainActivity.cs 文件`OnCreate`方法。
-  **UWP** -MainPage.xaml.cs 文件中，在`MainPage`构造函数。

添加 NuGet 包并在每个应用程序内调用初始化方法后, `Xamarin.Forms.Maps`可以在通用 .NET Standard 库项目或共享项目代码中使用 api。

<a name="Platform_Configuration" />

## <a name="platform-configuration"></a>平台配置

一些平台上需执行其他配置步骤，然后该地图才可显示。

### <a name="ios"></a>iOS

若要访问在 iOS 上的位置服务，必须设置以下项**Info.plist**:

- iOS 11
    - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) – 使用位置服务，当应用程序正在使用中
    - [`NSLocationAlwaysAndWhenInUseUsageDescription`](https://developer.apple.com/documentation/corelocation/choosing_the_authorization_level_for_location_services/requesting_always_authorization?language=objc) – 在任何时候使用位置服务
- iOS 10 及更早版本
    - [`NSLocationWhenInUseUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW26) – 使用位置服务，当应用程序正在使用中
    - [`NSLocationAlwaysUsageDescription`](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW18) – 在任何时候使用位置服务    

若要支持 iOS 11 和更早版本，可以包括所有三个密钥： `NSLocationWhenInUseUsageDescription`， `NSLocationAlwaysAndWhenInUseUsageDescription`，和`NSLocationAlwaysUsageDescription`。

中的这些项的 XML 表示形式**Info.plist**如下所示。 应更新`string`值以反映你的应用程序如何使用位置信息：

```xml
<key>NSLocationAlwaysUsageDescription</key>
<string>Can we use your location at all times?</string>
<key>NSLocationWhenInUseUsageDescription</key>
<string>Can we use your location when your app is being used?</string>
<key>NSLocationAlwaysAndWhenInUseUsageDescription</key>
<string>Can we use your location at all times?</string>
```

**Info.plist**还可以在中添加条目**源**在其他视图**Info.plist**文件：

![适用于 iOS 8 Info.plist](map-images/ios8-map-permissions.png "iOS 8 需要 Info.plist 条目")

### <a name="android"></a>Android

若要使用[Google 地图 API v2](https://developers.google.com/maps/documentation/android/)必须在 Android 上生成 API 密钥并将其添加到你的 Android 项目。
Xamarin 文档中的说明进行操作并遵照[获取 Google Maps API v2 密钥](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)。
按照这些说明进行操作之后, 将粘贴中的 API 密钥**properties/Androidmanifest.xml**文件 （查看源和查找/更新的以下元素）：

```xml
<application ...>
    <meta-data android:name="com.google.android.maps.v2.API_KEY" android:value="YOUR_API_KEY" />
</application>
```

如果没有有效的 API 密钥, map 控件将在 Android 上显示为灰色框。

> [!NOTE]
> 请注意，为了使 APK 来访问 Google 地图，您必须包括 sha-1 指纹，包使用对 APK 进行签名每个密钥存储 （调试和发布） 的名称。 例如，如果一台计算机用于调试和生成发布 APK 的另一台计算机，您应包括 sha-1 证书指纹从第一台计算机的调试密钥存储和从的发布密钥存储的 sha-1 证书指纹第二台计算机。 此外，切记要编辑的密钥凭据，如果应用程序的**包名称**更改。 请参阅[获取 Google Maps API v2 密钥](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)。

您还需要通过右键单击 Android 项目，然后选择启用适当的权限**选项 > 生成 > Android 应用程序**和勾选以下：

* `AccessCoarseLocation`
* `AccessFineLocation`
* `AccessLocationExtraCommands`
* `AccessMockLocation`
* `AccessNetworkState`
* `AccessWifiState`
* `Internet`

下面的屏幕截图显示了其中一些：

![针对 Android 所需权限](map-images/android-map-permissions.png "适用于 Android 的所需的权限")

需要最后两个，因为应用程序需要网络连接才能下载地图数据。 了解 Android[权限](https://developer.android.com/reference/android/Manifest.permission.html)若要了解详细信息。

此外, Android 9 从 bootclasspath 中删除了 Apache HTTP 客户端库, 因此它不适用于面向 API 28 或更高版本的应用程序。 必须将以下行添加到**androidmanifest.xml**文件`application`的节点, 才能继续在面向 API 28 或更高版本的应用程序中使用 Apache HTTP 客户端:

```xml
<application ...>
    ...
    <uses-library android:name="org.apache.http.legacy" android:required="false" />    
</application>
```

### <a name="universal-windows-platform"></a>通用 Windows 平台

若要在通用 Windows 平台上使用地图，则必须生成授权令牌。 有关详细信息，请参阅 MSDN 上的 [请求地图身份验证密钥](https://msdn.microsoft.com/library/windows/apps/mt219694.aspx)。

然后应在中指定的身份验证令牌`FormsMaps.Init("AUTHORIZATION_TOKEN")`方法调用中，与必应地图应用进行身份验证。

<a name="Using_Maps" />

## <a name="map-configuration"></a>地图配置

请参阅[MapPage.cs](https://github.com/xamarin/xamarin-forms-samples/blob/master/MobileCRM/MobileCRM.Shared/Pages/MapPage.cs) MobileCRM 示例以举例说明如何在代码中使用地图控件中。 一个简单`MapPage`类看起来像此-请注意，新`MapSpan`创建来定位地图的视图：

```csharp
public class MapPage : ContentPage {
    public MapPage() {
        var map = new Map(
            MapSpan.FromCenterAndRadius(
                    new Position(37,-122), Distance.FromMiles(0.3))) {
                IsShowingUser = true,
                HeightRequest = 100,
                WidthRequest = 960,
                VerticalOptions = LayoutOptions.FillAndExpand
            };
        var stack = new StackLayout { Spacing = 0 };
        stack.Children.Add(map);
        Content = stack;
    }
}
```

### <a name="map-type"></a>地图类型

此外可以通过设置更改地图内容`MapType`属性，以显示正则街道地图 （默认值）、 卫星照片或这两者的组合。

```csharp
map.MapType == MapType.Street;
```

有效`MapType`的值为：

-  混合
-  附属
-  街道 （默认值）

### <a name="map-region-and-mapspan"></a>地图区域和 MapSpan

如上面的代码片段所示，若向地图构造函数提供一个 `MapSpan` 实例，则会在加载地图时设置地图的初始视图（中心点和缩放级别）。 然后，可使用地图类上的 `MoveToRegion` 方法来更改地图的位置或缩放级别。 创建新 `MapSpan` 实例的方法有两种：

-  **MapSpan.FromCenterAndRadius()** 的静态方法，用于创建从 span`Position`并指定`Distance`。
-  **新 MapSpan （)** 的构造函数使用`Position`和程度的纬度和经度来显示。


若要更改地图的缩放级别而无需更改位置，请创建一个新`MapSpan`使用从当前位置`VisibleRegion.Center`地图控件的属性。 一个`Slider`无法用于控制此类地图缩放 （但是，缩放直接在地图控件中当前不能更新滑块的值）：

```csharp
var slider = new Slider (1, 18, 1);
slider.ValueChanged += (sender, e) => {
    var zoomLevel = e.NewValue; // between 1 and 18
    var latlongdegrees = 360 / (Math.Pow(2, zoomLevel));
    map.MoveToRegion(new MapSpan (map.VisibleRegion.Center, latlongdegrees, latlongdegrees));
};
```

 [![与 zoom 的地图](map-images/maps-zoom-sml.png "地图控件缩放")](map-images/maps-zoom.png#lightbox "地图控件缩放")

### <a name="map-pins"></a>地图图钉

可以在代码图上标记位置`Pin`对象。

```csharp
var position = new Position(37,-122); // Latitude, Longitude
var pin = new Pin {
            Type = PinType.Place,
            Position = position,
            Label = "custom pin",
            Address = "custom detail info"
        };
map.Pins.Add(pin);
```

`PinType`可以设置为以下值之一, 这可能会影响 pin 的呈现方式 (具体取决于平台):

-  泛型
-  位置
-  SavedPin
-  SearchResult

### <a name="map-clicks"></a>地图单击

`Map`定义在点击地图时触发的事件。`MapClicked` 事件附带的`Position`对象具有`Position`名为的单个属性, 类型为。 `MapClickedEventArgs` `MapClicked` 触发事件时, `Position`属性的值将设置为点击的映射位置。

下面的代码示例演示`MapClicked`事件的事件处理程序:

```csharp
map.MapClicked += OnMapClicked;

void OnMapClicked(object sender, MapClickedEventArgs e)
{
    System.Diagnostics.Debug.WriteLine($"MapClick: {e.Position.Latitude}, {e.Position.Longitude}");
}
```

在此示例中, `OnMapClicked`事件处理程序输出表示螺纹地图位置的纬度和经度。

<a name="Using_Xaml" />

### <a name="create-a-map-in-xaml"></a>在 XAML 中创建地图

还可以在 XAML 创建地图，如下例所示：

```xaml
<?xml version="1.0" encoding="UTF-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps"
             x:Class="MapDemo.MapPage">
    <StackLayout VerticalOptions="StartAndExpand" Padding="30">
        <maps:Map x:Name="MyMap"
                  Clicked="OnMapClicked"
                  WidthRequest="320"
                  HeightRequest="200"                  
                  IsShowingUser="true"
                  MapType="Hybrid" />
    </StackLayout>
</ContentPage>
```

> [!NOTE]
> 引用 Xamarin `xmlns` . Maps 控件需要额外的命名空间定义。

使用的`Pins`命名引用`Map`可以在代码中设置和:`MapRegion`

```csharp
MyMap.MoveToRegion(
    MapSpan.FromCenterAndRadius(
        new Position(37,-122), Distance.FromMiles(1)));
```

## <a name="populate-a-map-with-data-using-data-binding"></a>使用数据绑定填充包含数据的映射

[`Map`](xref:Xamarin.Forms.Maps.Map)类还公开了以下属性:

- `ItemsSource`–指定要显示的`IEnumerable`项的集合。
- `ItemTemplate`–指定要[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)应用于显示的项集合中的每个项的。
- `ItemTemplateSelector`–指定[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)将用于在运行时[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)为项选择的。

> [!NOTE]
> 当`ItemTemplate`设置了`ItemTemplate` 和`ItemTemplateSelector`属性时, 属性将优先。

通过使用数据绑定将其`ItemsSource`属性绑定到`IEnumerable`集合,[可以填充数据:`Map`](xref:Xamarin.Forms.Maps.Map)

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:maps="clr-namespace:Xamarin.Forms.Maps;assembly=Xamarin.Forms.Maps"
             x:Class="WorkingWithMaps.PinItemsSourcePage">
    <Grid>
        ...
        <maps:Map x:Name="map"
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

属性数据绑定`Locations`到连接的视图`ObservableCollection`模型的属性, 该属性返回对象的`Location` , 这是一个自定义类型。 `ItemsSource` 每`Location`个对象`Address`都`Description`定义类型的`string`和属性, 并`Position`定义类型[`Position`](xref:Xamarin.Forms.Maps.Position)的属性和属性。

通过`IEnumerable` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)将属性设置为, 可以定义集合中每个项的外观, 方法是将属性设置为, 其中包含数据绑定到相应属性的[对象。`Pin`](xref:Xamarin.Forms.Maps.Pin) `ItemTemplate`

以下屏幕截图显示了[`Map`](xref:Xamarin.Forms.Maps.Map)使用数据[`Pin`](xref:Xamarin.Forms.Maps.Pin)绑定显示集合的内容:

[![在 IOS 和 Android 地图上带有数据绑定插针、](map-images/pins-itemssource.png "包含")数据绑定插针的地图屏幕截图](map-images/pins-itemssource-large.png#lightbox "具有数据绑定插针的映射")

### <a name="choose-item-appearance-at-runtime"></a>在运行时选择项外观

可以在运行时根据项值`IEnumerable`选择集合中每个项的外观, 方法是`ItemTemplateSelector`将属性设置为[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector):

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

下面的示例演示`MapItemTemplateSelector`了类:

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

类定义`DefaultTemplate`了并`XamarinTemplate`将[属性设置为不同的数据模板。`DataTemplate`](xref:Xamarin.Forms.DataTemplate) `MapItemTemplateSelector` 方法返回, 当在点击时将 "Xamarin" `Pin`作为标签显示, 当项的地址包含 "旧金山" 时。 `XamarinTemplate` `OnSelectTemplate` 如果项没有包含 "旧金山" 的地址, 则该`OnSelectTemplate`方法将`DefaultTemplate`返回。

有关数据模板选择器的详细信息, 请参阅[创建 Xamarin。窗体并重](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)。

## <a name="related-links"></a>相关链接

- [MapsSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithmaps)
- [映射自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)
- [Xamarin.Forms 示例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
- [创建 Xamarin. Forms 并重](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
- [地图 API](xref:Xamarin.Forms.Maps)
