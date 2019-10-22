---
title: 第28章的摘要。 位置和地图
description: 用 Xamarin 创建移动应用：第28章的摘要。 位置和地图
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F6E20077-687C-45C4-A375-31D4F49BBFA4
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 5dcd84536cc6d80deb753fc6fe57f9090f6b2dad
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697077"
---
# <a name="summary-of-chapter-28-location-and-maps"></a>第28章的摘要。 位置和地图

[![下载示例](~/media/shared/download.png) 下载示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)

> [!NOTE]
> 此页上的说明指出了 Xamarin 在书籍中提供的材料中分离的区域。

Xamarin 支持从 `View` 派生的[`Map`](xref:Xamarin.Forms.Maps.Map)元素。 由于使用 maps 涉及到特殊平台要求，因此它们在单独的程序集、 **Xamarin**中实现，并涉及到不同的命名空间： `Xamarin.Forms.Maps`。

## <a name="the-geographic-coordinate-system"></a>地理坐标系统

地理坐标系统确定球形（或将近球形）对象（如地球）上的位置。 坐标由以角度表示的*纬度*和*经度*组成。

称为 `equator` 的好圆圈是地球轴在概念上扩展的两个两极之间中间。

### <a name="parallels-and-latitude"></a>等效和纬度

从地球中心标记的*赤道（称为*等效纬度的线条）的北或南部。 在北和南两极，范围从赤道到90度。 按照约定，纬度的赤道为正值，而赤道的南部是负值。

### <a name="longitude-and-meridians"></a>经度和经线

从北极点到南部极点的极佳圆圈是一系列相等的经度（也称为*经线*）。 它们相对于英国经线的质数。 按照约定，质数经线的经度东是从0度到180度的正值，而质数经线的经度西部是从0度到 &ndash;180 度的负值。

### <a name="the-equirectangular-projection"></a>Equirectangular 投影

地球的任何平面地图都引入了扭曲。 如果纬度和经度的所有行都是直线，并且纬度和经度角度的相等差异与地图上的相等距离相对应，则结果为*equirectangular 投影*。 此地图会使区域更靠近两极，因为它们水平伸展。

### <a name="the-mercator-projection"></a>Mercator 投影

常见的*Mercator 投影*会尝试通过垂直拉伸这些区域来对水平拉伸进行补偿。 这会生成一个地图，其中靠近两极的区域的显示范围比实际区域要大得多。

### <a name="map-services-and-tiles"></a>地图服务和磁贴

映射服务使用称为 `Web Mercator` 的 Mercator 投影的变体。 地图服务根据位置和缩放级别将位图磁贴传送到客户端。

## <a name="getting-the-users-location"></a>获取用户的位置

Xamarin `Map` 类不包含用于获取用户的地理位置的工具，但在使用地图时，通常需要这样做，因此依赖项服务必须处理。

> [!NOTE]
> Xamarin。窗体应用程序可以改用 Xamarin 中包含的[`Geolocation`](~/essentials/geolocation.md)类。

### <a name="the-location-tracker-api"></a>位置跟踪器 API

[**FormsBook**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Platform)解决方案包含位置跟踪器 API 的代码。 [@No__t_1](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/GeographicLocation.cs)结构封装了纬度和经度。 [@No__t_1](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform/ILocationTracker.cs)接口定义了用于启动和暂停位置跟踪程序的两种方法，并定义了新位置可用时的事件。

#### <a name="the-ios-location-manager"></a>IOS 位置管理器

@No__t_0 的 iOS 实现是使用 iOS [`CLLocationManager`](xref:CoreLocation.CLLocationManager) [`LocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.iOS/LocationTracker.cs)类。

#### <a name="the-android-location-manager"></a>Android 位置管理器

@No__t_0 的 Android 实现是利用 Android [`LocationManager`](xref:Android.Locations.LocationManager)类的[`LocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.Android/LocationTracker.cs)类。

#### <a name="the-uwp-geo-locator"></a>UWP 地域定位器

@No__t_0 的通用 Windows 平台实现是利用 UWP [`Geolocator`](/uwp/api/Windows.Devices.Geolocation.Geolocator)的[`LocationTracker`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Platform/Xamarin.FormsBook.Platform.WinRT/LocationTracker.cs)类。

### <a name="display-the-phones-location"></a>显示手机的位置

[**WhereAmI**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/WhereAmI)示例使用位置跟踪器在文本和 equirectangular 映射中显示手机的位置。

### <a name="the-required-overhead"></a>必需的开销

**WhereAmI**使用位置跟踪器需要一些开销。 首先， **WhereAmI**解决方案中的所有项目都必须引用**FormsBook**中的相应项目，并且每个**WhereAmI**项目必须调用 `Toolkit.Init` 方法。

需要一些其他特定于平台的开销（以位置权限的形式）。

#### <a name="location-permission-for-ios"></a>IOS 的位置权限

对于 iOS， **info.plist**文件必须包含包含问题文本的项，请求用户允许获取该用户的位置。

#### <a name="location-permissions-for-android"></a>Android 的位置权限

用于获取用户位置的 Android 应用程序必须在 Androidmanifest.xml 文件中具有 ACCESS_FILE_LOCATION 权限。

#### <a name="location-permissions-for-the-uwp"></a>UWP 的位置权限

通用 Windows 平台应用程序必须在 appxmanifest.xml 文件中标记 `location` 设备功能。

## <a name="working-with-xamarinformsmaps"></a>使用 Xamarin. Maps

使用 `Map` 类涉及几个要求。

### <a name="the-nuget-package"></a>NuGet 包

必须将**Xamarin. Maps** NuGet 库添加到应用程序解决方案。 版本号应与当前安装的**Xamarin. Forms**包相同。

### <a name="initializing-the-maps-package"></a>正在初始化 Maps 包

在调用 `Xamarin.Forms.Forms.Init` 之后，应用程序项目必须调用 `Xamarin.FormsMaps.Init` 方法。

### <a name="enabling-map-services"></a>启用地图服务

由于 `Map` 可以获取用户的位置，因此应用程序必须按照本章前面所述的方式获取用户的权限：

#### <a name="enabling-ios-maps"></a>启用 iOS 映射

使用 `Map` 的 iOS 应用程序需要 info.plist 文件中的两行。

#### <a name="enabling-android-maps"></a>启用 Android 地图

使用 Google Map 服务需要授权密钥。 此密钥插入到**androidmanifest.xml**文件中。 此外， **androidmanifest.xml**文件需要 `manifest` 标记来获取用户的位置。

#### <a name="enabling-uwp-maps"></a>启用 UWP 映射

通用 Windows 平台应用程序需要使用必应地图的授权密钥。 此密钥将作为参数传递给 `Xamarin.FormsMaps.Init` 方法。 还必须为位置服务启用该应用程序。

### <a name="the-unadorned-map"></a>未修饰映射

[**MapDemos**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28/MapDemos)示例包含一个[MapsDemoHomePage](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml)文件和[MapsDemoHomePage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapDemosHomePage.xaml.cs)的代码隐藏文件，该文件允许导航到各种演示程序。

[BasicMapPage](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/BasicMapPage.xaml)文件演示如何显示[`Map`](xref:Xamarin.Forms.Maps.Map)视图。 默认情况下，它会显示 "罗马" 城市，但该地图可以由用户操作。

若要禁用水平滚动和垂直滚动，请将[`HasScrollEnabled`](xref:Xamarin.Forms.Maps.Map.HasScrollEnabled)属性设置为 `false`。 若要禁用缩放，请将[`HasZoomEnabled`](xref:Xamarin.Forms.Maps.Map.HasZoomEnabled)设置为 `false`。 这些属性可能不适用于所有平台。

### <a name="streets-and-terrain"></a>街道和地形

可以通过设置类型[`MapType`](xref:Xamarin.Forms.Maps.MapType)的 `Map` 属性[`MapType`](xref:Xamarin.Forms.Maps.Map.MapType) （包含三个成员的枚举）来显示不同类型的映射：

- [`Street`](xref:Xamarin.Forms.Maps.MapType.Street)，默认值为
- [`Satellite`](xref:Xamarin.Forms.Maps.MapType.Satellite)
- [`Hybrid`](xref:Xamarin.Forms.Maps.MapType.Hybrid)

[MapTypesPage](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypesPage.xaml)文件演示如何使用单选按钮来选择映射类型。 它使用[**FormsBook**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库中的[`RadioButtonManager`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioButtonManager.cs)类，并使用基于[MapTypeRadioButton](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapTypeRadioButton.xaml)文件的类。

### <a name="map-coordinates"></a>地图坐标

程序可以获取 `Map` 通过[`VisibleRegion`](xref:Xamarin.Forms.Maps.Map.VisibleRegion)属性显示的当前区域。 此属性*不*受可绑定属性的支持，而且没有通知机制来指示其发生了什么变化，因此希望监视属性的程序在此目的上可能使用计时器。

`VisibleRegion` 的类型为[`MapSpan`](xref:Xamarin.Forms.Maps.MapSpan)，该类具有四个只读属性：

- [`Position`](xref:Xamarin.Forms.Maps.Position) 类型的 [`Center`](xref:Xamarin.Forms.Maps.MapSpan.Center)
- `double` 类型的[`LatitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LatitudeDegrees) ，指示地图的显示区域的高度
- `double` 类型的[`LongitudeDegrees`](xref:Xamarin.Forms.Maps.MapSpan.LongitudeDegrees) ，指示地图的显示区域的宽度。
- [`Distance`](xref:Xamarin.Forms.Maps.Distance)类型的[`Radius`](xref:Xamarin.Forms.Maps.MapSpan.Radius) ，指示在地图上可见的最大圆形区域的大小

`Position` 和 `Distance` 都是结构。 `Position` 定义通过[`Position` 构造函数](xref:Xamarin.Forms.Maps.Position.%23ctor(System.Double,System.Double))设置的两个只读属性：

- [`Latitude`](xref:Xamarin.Forms.Maps.Position.Latitude)
- [`Longitude`](xref:Xamarin.Forms.Maps.Position.Longitude)

`Distance` 旨在通过在度量值和英语单位之间进行转换来提供与单元无关的距离。 可以通过多种方式创建 `Distance` 值：

- [`Distance` 构造函数](xref:Xamarin.Forms.Maps.Distance.%23ctor(System.Double))，其距离以米为单位
- [`Distance.FromMeters`](xref:Xamarin.Forms.Maps.Distance.FromMeters(System.Double))静态方法
- [`Distance.FromKilometers`](xref:Xamarin.Forms.Maps.Distance.FromKilometers(System.Double))静态方法
- [`Distance.FromMiles`](xref:Xamarin.Forms.Maps.Distance.FromMiles(System.Double))静态方法

可从以下三个属性获取该值：

- `double` 类型的 [`Meters`](xref:Xamarin.Forms.Maps.Distance.Meters)
- `double` 类型的 [`Kilometers`](xref:Xamarin.Forms.Maps.Distance.Kilometers)
- `double` 类型的 [`Miles`](xref:Xamarin.Forms.Maps.Distance.Miles)

[MapCoordinatesPage](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml)文件包含若干 `Label` 元素，用于显示 `MapSpan` 信息。 当用户操作地图时， [MapCoordinatesPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MapCoordinatesPage.xaml.cs)代码隐藏文件使用计时器来保持信息更新。

### <a name="position-extensions"></a>位置扩展

此书籍的新库名为[**FormsBook**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit.Maps) ，其中包含特定于平台的映射，但与平台无关。 [@No__t_1](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs)类具有 `Position` 的 `ToString` 方法，以及用于计算两个 `Position` 值之间的距离的方法。

### <a name="setting-an-initial-location"></a>设置初始位置

可以调用 `Map` 的[`MoveToRegion`](xref:Xamarin.Forms.Maps.Map.MoveToRegion(Xamarin.Forms.Maps.MapSpan))方法，以编程方式设置地图上的位置和缩放级别。 参数的类型为 `MapSpan`。 可以使用下列任一方法创建 `MapSpan` 对象：

- 使用 `Position` 的[`MapSpan` 构造函数](xref:Xamarin.Forms.Maps.MapSpan.%23ctor(Xamarin.Forms.Maps.Position,System.Double,System.Double))以及纬度和经度范围
- 使用 `Position` 和 radius [`MapSpan.FromCenterAndRadius`](xref:Xamarin.Forms.Maps.MapSpan.FromCenterAndRadius(Xamarin.Forms.Maps.Position,Xamarin.Forms.Maps.Distance))

还可以使用[`ClampLatitude`](xref:Xamarin.Forms.Maps.MapSpan.ClampLatitude(System.Double,System.Double))或[`WithZoom`](xref:Xamarin.Forms.Maps.MapSpan.WithZoom(System.Double))的方法从现有 `MapSpan` 创建新的。

[WyomingPage](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml)文件和[WyomingPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/WyomingPage.xaml.cs)代码隐藏文件演示了如何使用 `MoveToRegion` 方法来显示怀俄明州的状态。

您也可以将[`Map` 构造函数](xref:Xamarin.Forms.Maps.Map.%23ctor(Xamarin.Forms.Maps.MapSpan))与 `MapSpan` 对象结合使用来初始化映射的位置。 [XamarinHQPage](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/XamarinHQPage.xaml)文件演示如何完全在 xaml 中完成此操作，以在旧金山中显示 Xamarin 的总部。

### <a name="dynamic-zooming"></a>动态缩放

您可以使用 `Slider` 来动态缩放地图。 [RadiusZoomPage](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml)文件和[RadiusZoomPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/RadiusZoomPage.xaml.cs)代码隐藏文件显示了如何根据 `Slider` 值更改地图的半径。

[LongitudeZoomPage](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml)文件和[LongitudeZoomPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LongitudeZoomPage.xaml.cs)代码隐藏文件显示了在 Android 上更好地工作的一种替代方法，但这两种方法在 Windows 平台上都很有用。

### <a name="the-phones-location"></a>手机的位置

@No__t_2 的[`IsShowingUser`](xref:Xamarin.Forms.Maps.Map.IsShowingUser)属性在每个平台上的工作方式稍有不同， [ShowLocationPage](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ShowLocationPage.xaml)文件如下所示：

- 在 iOS 上，蓝点指示手机的位置，但你必须在该处手动导航
- 在 Android 上，当推送时，会显示一个图标，该图标会将地图移动到手机的位置
- UWP 类似于 iOS，但有时会自动导航到位置

**MapDemos**项目尝试通过首先定义基于[MyLocationButton](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml)文件和[MyLocationButton.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/MyLocationButton.xaml.cs)代码隐藏文件的基于图标的按钮来模拟 Android 方法。

[GoToLocationPage](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml)文件和[GoToLocationPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GoToLocationPage.xaml.cs)代码隐藏文件使用此按钮导航到手机的位置。

### <a name="pins-and-science-museums"></a>Pin 和科学博物馆

最后，`Map` 类定义 `IList<Pin>` 类型的[`Pins`](xref:Xamarin.Forms.Maps.Map.Pins)属性。 [@No__t_1](xref:Xamarin.Forms.Maps.Pin)类定义四个属性：

- 类型 `string` [`Label`](xref:Xamarin.Forms.Maps.Pin.Label) ，必需属性
- `string` 类型的[`Address`](xref:Xamarin.Forms.Maps.Pin.Address) ，这是一个可选的用户可读地址
- `Position` 类型的[`Position`](xref:Xamarin.Forms.Maps.Pin.Position) ，指示在地图上显示 pin 的位置
- [`PinType`](xref:Xamarin.Forms.Maps.PinType)类型的[`Type`](xref:Xamarin.Forms.Maps.Pin.Type) ，不使用的枚举

**MapDemos**项目包含文件[ScienceMuseums](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Data/ScienceMuseums.xml)，该文件在美国中列出了科学博物馆，并[`Locations`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Locations.cs)和[`Site`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/Site.cs)类来反序列化此数据。

[ScienceMuseumsPage](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml)文件和[ScienceMuseumsPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/ScienceMuseumsPage.xaml.cs)代码隐藏文件在地图中显示这些科学博物馆的 pin。 当用户点击 pin 时，它将显示博物馆的地址和网站。

### <a name="the-distance-between-two-points"></a>两个点之间的距离

[@No__t_1](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs)类包含一个[`DistanceTo`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit.Maps/Xamarin.FormsBook.Toolkit.Maps/PositionExtensions.cs#L88)方法，该方法具有两个地理位置之间的距离的简化计算。

它用于[LocalMuseumsPage](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml)文件和[LocalMuseumsPage.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/LocalMuseumsPage.xaml.cs)代码隐藏文件，还可显示从用户所在位置到博物馆的距离：

[![本地博物馆页面的三向屏幕截图](images/ch28fg28-small.png "到位置的距离")](images/ch28fg28-large.png#lightbox "到位置的距离")

该程序还演示了如何基于地图的位置动态限制 pin 的数目。

## <a name="geocoding-and-back-again"></a>地理编码并再次返回

[**Xamarin**](xref:Xamarin.Forms.Maps)程序集还包含一个[`Geocoder`](xref:Xamarin.Forms.Maps.Geocoder)类，该类具有[`GetPositionsForAddressAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetPositionsForAddressAsync(System.String))方法，该方法可将文本地址转换为零个或多个可能的地理位置，以及另一个[`GetAddressesForPositionAsync`](xref:Xamarin.Forms.Maps.Geocoder.GetAddressesForPositionAsync(Xamarin.Forms.Maps.Position))的转换方法方向键.

[GeocoderRoundTrip](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml)文件和[GeocoderRoundTrip.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter28/MapDemos/MapDemos/MapDemos/GeocoderRoundTripPage.xaml.cs)代码隐藏文件演示了此功能。

## <a name="related-links"></a>相关链接

- [第28章全文（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch28-Aug2016.pdf)
- [第28章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter28)
- [Xamarin. Forms 地图](~/xamarin-forms/user-interface/map/index.md)
