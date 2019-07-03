---
title: Xamarin.Forms 映射
description: 本文介绍如何使用 Xamarin.Forms Map 类如何使用每个平台上本机地图 Api 提供熟悉的映射的用户体验。
ms.prod: xamarin
ms.assetid: 59CD1344-8248-406C-9144-0C8A67141E5B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/13/2019
ms.openlocfilehash: 60d78797406f2e69c435fb597e36775d906852f9
ms.sourcegitcommit: 0fd04ea3af7d6a6d6086525306523a5296eec0df
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2019
ms.locfileid: "67513105"
---
# <a name="xamarinforms-map"></a>Xamarin.Forms 映射

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithMaps/)

_Xamarin.Forms 每个平台上使用本机映射 Api。_

Xamarin.Forms.Maps 每个平台上使用本机映射 Api。 这对于用户，提供快速、 熟悉地图体验，但意味着一些配置步骤所需遵守每个平台 API 要求。
配置完成后，`Map`控制的工作方式就像在常见的代码中的任何其他 Xamarin.Forms 元素。

已在使用地图控件[MapsSample](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithMaps/)示例，如下所示。

 [![MobileCRM 示例中的地图](map-images/maps-zoom-sml.png "地图控件示例")](map-images/maps-zoom.png#lightbox "地图控件示例")

映射功能可以通过创建进一步增强[映射自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)。

<a name="Maps_Initialization" />

## <a name="map-initialization"></a>站点地图初始化

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

已添加 NuGet 包并在每个应用程序内调用初始化方法后`Xamarin.Forms.Maps`Api 可以使用共享项目代码的公共.NET Standard 库项目中。

<a name="Platform_Configuration" />

## <a name="platform-configuration"></a>平台配置

之前该映射将显示在某些平台上需要其他配置步骤。

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

如果没有有效的 API 密钥地图控件将显示为灰色框，在 Android 上。

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

此外，Android 9 从 bootclasspath，消除了 Apache HTTP 客户端库，因此它不是可用于应用程序面向 API 28 或更高版本。 必须将以下行添加到`application`节点的你**AndroidManifest.xml**文件以继续在面向 API 28 或更高版本的应用程序中使用 Apache HTTP 客户端：

```xml
<application ...>
    ...
    <uses-library android:name="org.apache.http.legacy" android:required="false" />    
</application>
```

### <a name="universal-windows-platform"></a>通用 Windows 平台

若要在通用 Windows 平台上使用映射必须生成授权令牌。 有关详细信息，请参阅[请求一个地图身份验证密钥](https://msdn.microsoft.com/library/windows/apps/mt219694.aspx)MSDN 上。

然后应在中指定的身份验证令牌`FormsMaps.Init("AUTHORIZATION_TOKEN")`方法调用中，与必应地图应用进行身份验证。

<a name="Using_Maps" />

## <a name="map-configuration"></a>配置映射

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

### <a name="map-type"></a>将类型映射

此外可以通过设置更改地图内容`MapType`属性，以显示正则街道地图 （默认值）、 卫星照片或这两者的组合。

```csharp
map.MapType == MapType.Street;
```

有效`MapType`的值为：

-  混合
-  附属
-  街道 （默认值）

### <a name="map-region-and-mapspan"></a>地图区域和 MapSpan

上面的代码段中所示，提供`MapSpan`map 构造函数的实例设置的初始视图 （中心点和缩放级别） 的映射在加载时。 `MoveToRegion`然后使用 map 类上的方法来更改映射的位置或缩放级别。 有两种方法来创建一个新`MapSpan`实例：

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

### <a name="map-pins"></a>映射的 pin

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

`PinType` 可以设置为以下值，可能会影响固定呈现 （具体取决于平台） 的方法之一：

-  泛型
-  位置
-  SavedPin
-  SearchResult

### <a name="map-clicks"></a>单击地图

`Map` 定义`MapClicked`点击映射时激发的事件。 `MapClickedEventArgs`对象，它附带`MapClicked`事件都有一个名为的单个属性`Position`，类型的`Position`。 激发事件时，值`Position`属性设置为点击的映射位置。

下面的代码示例显示了事件处理程序`MapClicked`事件：

```csharp
map.MapClicked += OnMapClicked;

void OnMapClicked(object sender, MapClickedEventArgs e)
{
    System.Diagnostics.Debug.WriteLine($"MapClick: {e.Position.Latitude}, {e.Position.Longitude}");
}
```

在此示例中，`OnMapClicked`事件处理程序输出的纬度和经度表示分流的映射位置。

<a name="Using_Xaml" />

### <a name="create-a-map-in-xaml"></a>在 XAML 中创建映射

在此示例中所示，还可以在 XAML 中，创建映射：

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
> 附加`xmlns`命名空间定义用于 Xamarin.Forms.Maps 控件的引用。

`MapRegion`并`Pins`可以在代码中使用的命名的引用设置`Map`:

```csharp
MyMap.MoveToRegion(
    MapSpan.FromCenterAndRadius(
        new Position(37,-122), Distance.FromMiles(1)));
```

## <a name="populate-a-map-with-data-using-data-binding"></a>地图填充数据使用数据绑定

[ `Map` ](xref:Xamarin.Forms.Maps.Map)类还公开以下属性：

- `ItemsSource` – 指定的集合`IEnumerable`要显示的项。
- `ItemTemplate` – 指定[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)要应用于每个项目中显示的项的集合。
- `ItemTemplateSelector` – 指定[ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector)将用于选择[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)在运行时的项。

> [!NOTE]
> `ItemTemplate`属性将优先于时同时`ItemTemplate`和`ItemTemplateSelector`设置属性。

一个[ `Map` ](xref:Xamarin.Forms.Maps.Map)通过使用数据绑定来绑定可以使用数据填充其`ItemsSource`属性设置为`IEnumerable`集合：

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

`ItemsSource`将属性数据绑定到`Locations`连接的视图模型，它将返回属性`ObservableCollection`的`Location`是自定义类型的对象。 每个`Location`对象定义`Address`并`Description`类型的属性`string`，和一个`Position`类型的属性[ `Position` ](xref:Xamarin.Forms.Maps.Position)。

在每个项的外观`IEnumerable`通过设置定义集合`ItemTemplate`属性设置为[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) ，其中包含[ `Pin` ](xref:Xamarin.Forms.Maps.Pin)对象将数据绑定到相应的属性。

下面的屏幕截图演示[ `Map` ](xref:Xamarin.Forms.Maps.Map)显示[ `Pin` ](xref:Xamarin.Forms.Maps.Pin)使用数据绑定的集合：

[![映射，其中数据的屏幕截图绑定 iOS 和 Android 上的 pin](map-images/pins-itemssource.png "映射具有数据绑定的插针")](map-images/pins-itemssource-large.png#lightbox "映射具有数据绑定的 pin")

### <a name="choose-item-appearance-at-runtime"></a>选择项在运行时的外观

在每个项的外观`IEnumerable`可以在运行时，通过设置基于项值中，选择集合`ItemTemplateSelector`属性设置为[ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector):

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

下面的示例演示`MapItemTemplateSelector`类：

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

`MapItemTemplateSelector`类定义`DefaultTemplate`并`XamarinTemplate` [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)设置为不同的数据模板的属性。 `OnSelectTemplate`方法将返回`XamarinTemplate`，它作为标签中时显示"Xamarin"`Pin`点击，项具有包含"旧金山"的地址时。 当项没有包含"旧金山"的地址时`OnSelectTemplate`方法将返回`DefaultTemplate`。

有关数据模板选择器的详细信息，请参阅[创建 Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)。

## <a name="related-links"></a>相关链接

- [MapsSample](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithMaps/)
- [映射自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/map/index.md)
- [Xamarin.Forms 示例](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [创建 Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
