---
title: 第 28 章的摘要。 位置和地图
description: 用 Xamarin 创建移动应用程序:第 28 章的摘要。 位置和地图
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F6E20077-687C-45C4-A375-31D4F49BBFA4
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 846b7fa3c905b208771a110a013283bd77214b72
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511702"
---
# <a name="summary-of-chapter-28-location-and-maps"></a>第 28 章的摘要。 位置和地图

[![下载示例](~/media/shared/download.png)下载示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)

> [!NOTE]
> 此页上的说明表明其中 Xamarin.Forms 已脱离一书中介绍的内容的区域。

Xamarin.Forms 支持[ `Map` ](xref:Xamarin.Forms.Maps.Map)派生的元素`View`。 由于使用映射所涉及的特殊平台要求，它们实现在单独的程序集， **Xamarin.Forms.Maps**，并涉及不同的命名空间： `Xamarin.Forms.Maps`。

## <a name="the-geographic-coordinate-system"></a>地理坐标系统

地理坐标系统标识球面 （或几乎球面） 的对象，如地球上的位置。 坐标组成*纬度*并*经度*用角度表示。

大圆调用`equator`之间两极地球轴从概念上讲通过该扩展时。

### <a name="parallels-and-latitude"></a>Parallels 和纬度

度量的角南北地球标记的中心从赤道行称为相等纬度*parallels*。 从在赤道 0 度到 90 度北极和南极在这些范围。 按照约定，表示赤道以北的纬度为正值，并且这些南纬赤道是负值。

### <a name="longitude-and-meridians"></a>经度和经线

从北极到南极的大圆形的部分相等经度线也被称为*经线*。 这些是相对于在英国格林威治以西起用子午线。 按照约定，经度偏东子午线正值从 0 度到 180 度，经度延伸子午线负值从 0 度到&ndash;180 度。

### <a name="the-equirectangular-projection"></a>Equirectangular 投影

地球的任何平面地图引入了时会发生失真。 如果的纬度和经度的所有行都都直接提供，并且如果等于差异纬度和经度角度对应于在地图上相等的距离，则结果是*equirectangular 投影*。 此映射歪曲更接近到极地的区域，因为它们水平拉伸。

### <a name="the-mercator-projection"></a>Mercator 投影

热门*Mercator 投影*尝试补偿也垂直拉伸这些区域的水平拉伸。 这会导致的映射区域附近极地出现实际上是，但任何本地区域非常接近符合的实际区域大得多。

### <a name="map-services-and-tiles"></a>映射服务和磁贴

映射服务使用名为 Mercator 投影的变体`Web Mercator`。 映射服务交付到客户端基于位置和缩放级别位图磁贴。

## <a name="getting-the-users-location"></a>获取用户的位置

Xamarin.Forms`Map`类不包括一个工具用于获取用户的地理位置，但这通常是需要时使用的映射，因此一个依赖关系服务必须处理它。

> [!NOTE]
> Xamarin.Forms 应用程序可以改用[ `Geolocation` ](~/essentials/geolocation.md) Xamarin.Essentials 中包含的类。

### <a name="the-location-tracker-api"></a>位置跟踪器 API

[ **Xamarin.FormsBook.Platform** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform)解决方案包含一个位置跟踪器 API 的代码。 [ `GeographicLocation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/GeographicLocation.cs)结构封装纬度和经度。 [ `ILocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/ILocationTracker.cs)接口定义了两种方法来启动和暂停位置跟踪程序，并可用的新位置时的事件。

#### <a name="the-ios-location-manager"></a>IOS 位置管理器

IOS 实现`ILocationTracker`是[ `LocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/LocationTracker.cs)类，从而利用 iOS [ `CLLocationManager` ](xref:CoreLocation.CLLocationManager)。

#### <a name="the-android-location-manager"></a>Android 位置管理器

Android 的实现`ILocationTracker`是[ `LocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/LocationTracker.cs)利用了 Android 类[ `LocationManager` ](xref:Android.Locations.LocationManager)类。

#### <a name="the-uwp-geo-locator"></a>UWP 地理定位符

通用 Windows 平台实现`ILocationTracker`是[ `LocationTracker` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/LocationTracker.cs)类，利用了 UWP [ `Geolocator` ](/uwp/api/Windows.Devices.Geolocation.Geolocator)。

### <a name="display-the-phones-location"></a>显示手机所在的位置

[ **WhereAmI** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/WhereAmI)示例使用位置跟踪程序手机所在的位置，同时显示在文本和 equirectangular 地图上。

### <a name="the-required-overhead"></a>所需的开销

一些开销是必要条件**WhereAmI**使用位置跟踪程序。 首先，在项目的所有**WhereAmI**解决方案必须具有对中的相应项目的引用**Xamarin.FormsBook.Platform**，和每个**WhereAmI**项目必须调用`Toolkit.Init`方法。

形式的位置的权限，一些额外的特定于平台的开销是必需的。

#### <a name="location-permission-for-ios"></a>适用于 iOS 的位置权限

对于 iOS， **info.plist**文件必须包括各项包含文本的一个问题，询问用户允许获取该用户的位置。

#### <a name="location-permissions-for-android"></a>适用于 Android 的位置权限

获取用户的位置的 android 应用程序必须 ACCESS_FILE_LOCATION 权限中包括的 AndroidManifest.xml 文件。

#### <a name="location-permissions-for-the-uwp"></a>适用于 UWP 的位置权限

通用 Windows 平台应用程序必须具有`location`在 Package.appxmanifest 文件中标记的设备功能。

## <a name="working-with-xamarinformsmaps"></a>使用 Xamarin.Forms.Maps

以下几个要求中使用涉及`Map`类。

### <a name="the-nuget-package"></a>NuGet 包

**Xamarin.Forms.Maps** NuGet 库必须添加到应用程序解决方案。 版本号应该是相同**Xamarin.Forms**当前安装的包。

### <a name="initializing-the-maps-package"></a>正在初始化映射包

应用程序项目必须调用`Xamarin.FormsMaps.Init`方法之后调用`Xamarin.Forms.Forms.Init`。

### <a name="enabling-map-services"></a>启用地图服务

因为`Map`可以获取用户的位置，则该应用程序必须获取在本章前面所述的方式中的用户的权限：

#### <a name="enabling-ios-maps"></a>启用 iOS 映射

IOS 应用程序中使用`Map`需要在 info.plist 文件中的两个行。

#### <a name="enabling-android-maps"></a>启用 Android 映射

需要使用 Google 地图服务提供的授权密钥。 在插入此密钥**AndroidManifest.xml**文件。 此外， **AndroidManifest.xml**文件所需的`manifest`中获取用户的位置所涉及的标记。

#### <a name="enabling-uwp-maps"></a>启用 UWP 映射

通用 Windows 平台应用程序需要使用必应地图提供的授权密钥。 作为参数传递此密钥`Xamarin.FormsMaps.Init`方法。 应用程序还必须启用位置服务。

### <a name="the-unadorned-map"></a>无修饰的映射

[ **MapDemos** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/MapDemos)示例组成[MapsDemoHomePage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml)文件和[MapsDemoHomePage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml.cs)代码隐藏文件允许导航到各种演示程序。

[BasicMapPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/BasicMapPage.xaml)文件演示了如何显示[ `Map` ](xref:Xamarin.Forms.Maps.Map)视图。 默认情况下它将显示罗马市/县，但可以由用户操作映射。

若要禁用水平和垂直滚动，设置[ `HasScrollEnabled` ](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled)属性设置为`false`。 若要禁用缩放，请设置[ `HasZoomEnabled` ](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled)到`false`。 这些属性可能不适用于所有平台。

### <a name="streets-and-terrain"></a>街道和地形

可以通过设置显示不同类型的地图`Map`属性[ `MapType` ](xref:Xamarin.Forms.Maps.Map.MapType)类型的[ `MapType` ](xref:Xamarin.Forms.Maps.MapType)，具有三个成员的枚举：

- [`Street`](xref:Xamarin.Forms.Maps.MapType.Street)默认值
- [`Satellite`](xref:Xamarin.Forms.Maps.MapType.Satellite)
- [`Hybrid`](xref:Xamarin.Forms.Maps.MapType.Hybrid)

[MapTypesPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypesPage.xaml)文件演示如何使用单选按钮来选择地图类型。 它利用了[ `RadioButtonManager` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioButtonManager.cs)类[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库和类基于[MapTypeRadioButton.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypeRadioButton.xaml)文件。

### <a name="map-coordinates"></a>地图坐标

程序可以获取当前区域的`Map`通过显示[ `VisibleRegion` ](xref:Xamarin.Forms.Maps.Map.VisibleRegion)属性。 此属性是*不*受可绑定的属性，并没有通知机制，以指示何时已更改，因此想要监视属性的程序可能应实现此目的使用一个计时器。

`VisibleRegion` 类型[ `MapSpan` ](xref:Xamarin.Forms.Maps.MapSpan)，具有四个只读属性的类：

- [`Center`](xref:Xamarin.Forms.Maps.MapSpan.Center) 类型 [`Position`](xref:Xamarin.Forms.Maps.Position)
- [`LatitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LatitudeDegrees) 类型的`double`，指示该映射显示区域的高度
- [`LongitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LongitudeDegrees) 类型的`double`，指示地图的显示区域宽度
- [`Radius`](xref:Xamarin.Forms.Maps.MapSpan.Radius) 类型的[ `Distance` ](xref:Xamarin.Forms.Maps.Distance)，指示在地图上可见的最大圆形区域的大小

`Position` 和`Distance`是这两个结构。 `Position` 定义两个只读属性，它们通过设置[`Position`构造函数](xref:Xamarin.Forms.Maps.Position.%23ctor(System.Double,System.Double)):

- [`Latitude`](xref:Xamarin.Forms.Maps.Position.Latitude)
- [`Longitude`](xref:Xamarin.Forms.Maps.Position.Longitude)

`Distance` 旨在提供独立于单元的距离的指标和英制单位之间进行转换。 一个`Distance`值可以创建几种方式：

- [`Distance` 构造函数](xref:Xamarin.Forms.Maps.Distance.%23ctor(System.Double))与以米为单位的距离
- [`Distance.FromMeters`](xref:Xamarin.Forms.Maps.Distance.FromMeters(System.Double)) 静态方法
- [`Distance.FromKilometers`](xref:Xamarin.Forms.Maps.Distance.FromKilometers(System.Double)) 静态方法
- [`Distance.FromMiles`](xref:Xamarin.Forms.Maps.Distance.FromMiles(System.Double)) 静态方法

可从三个属性的值为：

- [`Meters`](xref:Xamarin.Forms.Maps.Distance.Meters) 类型 `double`
- [`Kilometers`](xref:Xamarin.Forms.Maps.Distance.Kilometers) 类型 `double`
- [`Miles`](xref:Xamarin.Forms.Maps.Distance.Miles) 类型 `double`

[MapCoordinatesPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml)文件包含多个`Label`元素用于显示`MapSpan`信息。 [MapCoordinatesPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml.cs)代码隐藏文件中使用计时器来保持更新，因为用户操作映射的信息。

### <a name="position-extensions"></a>位置扩展

新的库名为这本书[ **Xamarin.FormsBook.Toolkit.Maps** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit.Maps)包含特定于地图的但独立于平台的类型。 [ `PositionExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs)类有`ToString`方法`Position`，并计算两个之间的距离的方法`Position`值。

### <a name="setting-an-initial-location"></a>设置初始位置

您可以调用[ `MoveToRegion` ](xref:Xamarin.Forms.Maps.Map.MoveToRegion(Xamarin.Forms.Maps.MapSpan))方法的`Map`以编程方式在代码图上设置位置和缩放级别。 参数的类型是`MapSpan`。 您可以创建`MapSpan`对象使用下列操作之一：

- [`MapSpan` 构造函数](xref:Xamarin.Forms.Maps.MapSpan.%23ctor(Xamarin.Forms.Maps.Position,System.Double,System.Double))与`Position`，和纬度和经度的范围
- [`MapSpan.FromCenterAndRadius`](xref:Xamarin.Forms.Maps.MapSpan.FromCenterAndRadius(Xamarin.Forms.Maps.Position,Xamarin.Forms.Maps.Distance)) 使用`Position`和 radius

还有可能创建一个新`MapSpan`使用的方法从现有[ `ClampLatitude` ](xref:Xamarin.Forms.Maps.MapSpan.ClampLatitude(System.Double,System.Double))或[ `WithZoom` ](xref:Xamarin.Forms.Maps.MapSpan.WithZoom(System.Double))。

[WyomingPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml)文件并[WyomingPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml.cs)代码隐藏文件演示如何使用`MoveToRegion`方法来显示状态怀俄明州。

也可以使用[`Map`构造函数](xref:Xamarin.Forms.Maps.Map.%23ctor(Xamarin.Forms.Maps.MapSpan))与`MapSpan`对象来初始化该映射的位置。 [XamarinHQPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/XamarinHQPage.xaml)文件演示如何执行此操作完全在 XAML 以显示在旧金山 Xamarin 的总部。

### <a name="dynamic-zooming"></a>动态缩放

可以使用`Slider`进行动态缩放地图。 [RadiusZoomPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml)文件并[RadiusZoomPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml.cs)代码隐藏文件演示如何更改基于地图的半径`Slider`值。

[LongitudeZoomPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml)文件并[LongitudeZoomPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml.cs)代码隐藏文件显示在 Android，效果更好的替代方法，但这两种做法也在 Windows 上的工作平台。

### <a name="the-phones-location"></a>手机所在的位置

[ `IsShowingUser` ](xref:Xamarin.Forms.Maps.Map.IsShowingUser)的属性`Map`运作方式有以不同的方式为每个平台上[ShowLocationPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ShowLocationPage.xaml)文件演示了：

- 在 iOS 上，则一个蓝点指示手机所在的位置，但您必须手动定位那里
- 在 Android 上，将显示一个图标，当推送移动到手机所在的位置地图
- UWP 与 iOS 类似，但有时自动导航到的位置

**MapDemos**项目尝试通过第一个定义根据基于图标的按钮来模拟 Android 方法[MyLocationButton.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml)文件和[MyLocationButton.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml.cs)代码隐藏文件。

[GoToLocationPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml)文件并[GoToLocationPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml.cs)代码隐藏文件中使用此按钮可导航到手机所在的位置。

### <a name="pins-and-science-museums"></a>Pin 和科学博物馆

最后，`Map`类定义[ `Pins` ](xref:Xamarin.Forms.Maps.Map.Pins)类型的属性`IList<Pin>`。 [ `Pin` ](xref:Xamarin.Forms.Maps.Pin)类定义了四个属性：

- [`Label`](xref:Xamarin.Forms.Maps.Pin.Label) 类型的`string`、 一个必需的属性
- [`Address`](xref:Xamarin.Forms.Maps.Pin.Address) 类型的`string`，用户可读的可选地址
- [`Position`](xref:Xamarin.Forms.Maps.Pin.Position) 类型的`Position`，指示固定在地图上的显示位置
- [`Type`](xref:Xamarin.Forms.Maps.Pin.Type) 类型的[ `PinType` ](xref:Xamarin.Forms.Maps.PinType)，枚举中，它不使用

**MapDemos**项目包含的文件[ScienceMuseums.xml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Data/ScienceMuseums.xml)，其中列出了在美国科学博物馆和[ `Locations` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Locations.cs)和[`Site` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Site.cs)用于反序列化此数据的类。

[ScienceMuseumsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml)文件并[ScienceMuseumsPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml.cs)科学博物馆映射中的代码隐藏文件显示 pin。 当用户点击 pin 时，它显示的地址和博物馆的网站。

### <a name="the-distance-between-two-points"></a>两个点之间的距离

[ `PositionExtensions` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs)类包含[ `DistanceTo` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs#L88)具有两个地理位置之间的距离的简化计算方法。

使用此[LocalMuseumsPage.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml)文件并[LocalMuseumsPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml.cs)代码隐藏文件中还显示距离去艺术馆从用户的位置：

[![本地博物馆页面的三个屏幕截图](images/ch28fg28-small.png "到某个位置的距离")](images/ch28fg28-large.png#lightbox "到某个位置的距离")

该程序还演示如何动态限制的基于地图的位置的 pin 的数量。

## <a name="geocoding-and-back-again"></a>地理编码和后再次

[ **Xamarin.Forms.Maps** ](xref:Xamarin.Forms.Maps)程序集还包含[ `Geocoder` ](xref:Xamarin.Forms.Maps.Geocoder)类[ `GetPositionsForAddressAsync` ](xref:Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync(System.String))转换方法零或更多可能的地理位置和另一种方法将文本地址[ `GetAddressesForPositionAsync` ](xref:Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync(Xamarin.Forms.Maps.Position)) ，它将在另一个方向。

[GeocoderRoundTrip.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml)文件并[GeocoderRoundTrip.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml.cs)代码隐藏文件演示此功能。



## <a name="related-links"></a>相关链接

- [第 28 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch28-Aug2016.pdf)
- [第 28 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)
- [Xamarin.Forms 映射](~/xamarin-forms/user-interface/map.md)
