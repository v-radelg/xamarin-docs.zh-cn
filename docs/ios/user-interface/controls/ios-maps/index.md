---
title: Xamarin.iOS 中的映射
description: 本文档介绍 iOS MapKit 框架以及如何使用通过 Xamarin.iOS。 它讨论了如何添加映射，样式，平移和缩放，使用用户位置、 添加注释、 处理标注和覆盖层，并执行本地搜索。
ms.prod: xamarin
ms.assetid: 5DD8E56D-51C1-4AFA-B387-79B5734698ED
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: c61b5a8bd99afda5e8fbeea44e3362574fa7feea
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61226599"
---
# <a name="maps-in-xamarinios"></a>Xamarin.iOS 中的映射

映射是在所有新式移动操作系统中的常见功能。 iOS 提供映射支持本机通过 Map kit 包框架。 使用 Map 工具包应用程序可以轻松地添加丰富的交互式地图。 中有许多种情况下，例如添加注释标记在地图上的位置以及覆盖的任意形状的图形，可以自定义这些映射。 Map kit 包甚至具有对显示设备的当前的位置的内置支持。

## <a name="adding-a-map"></a>添加映射

将地图添加到应用程序通过添加`MKMapView`实例视图层次结构，如下所示：

```csharp
// map is an MKMapView declared as a class variable
map = new MKMapView (UIScreen.MainScreen.Bounds);
View = map;
```

 `MKMapView` 是`UIView`显示了一个映射的子类。 只需添加映射使用上面的代码生成互动地图：

 ![](images/00-map.png "示例映射")

## <a name="map-style"></a>地图样式

 `MKMapView` 支持 3 个不同的映射方式。 若要将地图样式的应用，只需设置`MapType`属性的值从`MKMapType`枚举：
 ```
map.MapType = MKMapType.Standard; //road map
map.MapType = MKMapType.Satellite;
map.MapType = MKMapType.Hybrid;
 ```
  下面的屏幕截图显示可用的不同的映射样式：

 ![](images/01-mapstyles.png "此屏幕截图显示可用的不同的映射样式")

## <a name="panning-and-zooming"></a>平移和缩放

 `MKMapView` 包括对地图交互功能的支持，如所示：

-  通过收缩手势进行缩放
-  通过平移手势平移


这些功能可以启用或禁用通过只需设置`ZoomEnabled`并`ScrollEnabled`的属性`MKMapView`实例，默认值是两个，则返回 true。 例如，若要显示的静态映射，只需设置相应的属性为 false:

```csharp
map.ZoomEnabled = false;
map.ScrollEnabled = false;
```

## <a name="user-location"></a>用户位置

除了用户交互`MKMapView`还具有用于显示设备的位置的内置支持。 方法是使用*核心位置*框架。 在可以访问用户的位置之前，必须提示用户。 若要执行此操作，创建的实例`CLLocationManager`，并调用`RequestWhenInUseAuthorization`。

```csharp
CLLocationManager locationManager = new CLLocationManager();
locationManager.RequestWhenInUseAuthorization();
//locationManager.RequestAlwaysAuthorization(); //requests permission for access to location data while running in the background
```

请注意，在 iOS 8.0，尝试调用之前的版本`RequestWhenInUseAuthorization`将导致错误。 请务必进行该调用，如果你想要支持 8 之前的版本之前，检查的 iOS 版本。

访问用户的位置，还需要对修改**Info.plist**。 应设置如下与位置数据相关的键：

- **NSLocationWhenInUseUsageDescription** - 用于用户在和你的应用互动时访问用户的位置。
- **NSLocationAlwaysUsageDescription** - 用于应用在后台访问用户的位置时。

可以通过打开添加这些密钥**Info.plist** ，然后选择*源*在编辑器的底部。

已更新后**Info.plist**并提示用户输入其位置的访问权，可以在地图上显示用户的位置，通过设置`ShowsUserLocation`属性设为 true:

```csharp
map.ShowsUserLocation = true;
```

 ![](images/02-location-alert.png "允许的位置访问警报")
 
## <a name="annotations"></a>批注

 `MKMapView` 此外支持名为批注，在地图上显示图像。 这些可以是自定义映像或不同颜色的系统定义的 pin。 例如，下面的屏幕截图显示了一个使用这两个 pin 和自定义映像：

 ![](images/03-annotations.png "此屏幕截图显示了一个使用这两个 pin 和自定义映像")

### <a name="adding-an-annotation"></a>添加批注

批注本身由两部分组成：

-  `MKAnnotation`对象，其中包括有关该批注，如标题和位置的批注的模型数据。
-  `MKAnnotationView` ，其中包含显示和 （可选） 显示了当用户点击批注标注的映像。


Map 工具包使用 iOS 委派模式将批注添加到映射中，其中`Delegate`的属性`MKMapView`设置为实例`MKMapViewDelegate`。 它是此委托的实现，它负责返回`MKAnnotationView`批注。

若要添加批注，首先添加批注，是通过调用`AddAnnotations`上`MKMapView`实例：

```csharp
// add an annotation
map.AddAnnotations (new MKPointAnnotation (){
    Title="MyAnnotation",
    Coordinate = new CLLocationCoordinate2D (42.364260, -71.120824)
});
```

在地图中可见的批注位置时`MKMapView`将调用其委托`GetViewForAnnotation`方法以获取`MKAnnotationView`显示。

例如，以下代码将返回系统提供`MKPinAnnotationView`:

```csharp
string pId = "PinAnnotation";

public override MKAnnotationView GetViewForAnnotation (MKMapView mapView, NSObject annotation)
{
    if (annotation is MKUserLocation)
        return null;

    // create pin annotation view
    MKAnnotationView pinView = (MKPinAnnotationView)mapView.DequeueReusableAnnotation (pId);

    if (pinView == null)
        pinView = new MKPinAnnotationView (annotation, pId);

    ((MKPinAnnotationView)pinView).PinColor = MKPinAnnotationColor.Red;
    pinView.CanShowCallout = true;

    return pinView;
}
```

### <a name="reusing-annotations"></a>重复使用批注

若要节省内存，`MKMapView`允许批注查看的可存入池中以便重复使用类似于表格单元格会重复使用的方式。 从池中获取的批注视图通过调用`DequeueReusableAnnotation`:

```csharp
MKAnnotationView pinView = (MKPinAnnotationView)mapView.DequeueReusableAnnotation (pId);
```

#### <a name="showing-callouts"></a>显示标注

前面曾提到，批注可以根据需要显示一个标注。 若要只显示一个标注设置`CanShowCallout`为 true 的`MKAnnotationView`。 这会导致批注的标题时，不显示在点击批注所示：

 ![](images/04-callout.png "正在显示的批注标题")

### <a name="customizing-the-callout"></a>自定义标注

标注也可以是自定义以显示向左和右附件视图，如下所示：

```csharp
pinView.RightCalloutAccessoryView = UIButton.FromType (UIButtonType.DetailDisclosure);
pinView.LeftCalloutAccessoryView = new UIImageView(UIImage.FromFile ("monkey.png"));
```

此代码会导致以下标注：

 ![](images/05-callout-accessories.png "示例标注")

若要处理用户点击右附件，只需实现`CalloutAccessoryControlTapped`中的方法`MKMapViewDelegate`:

```csharp
public override void CalloutAccessoryControlTapped (MKMapView mapView, MKAnnotationView view, UIControl control)
{
    ...
}
```

### <a name="overlays"></a>覆盖层

在地图上的层图形到另一种方法使用覆盖。 叠加层支持绘制在地图缩放时随之缩放的图形内容。 iOS 提供支持几种类型的覆盖层，包括：

-  多边形-通常用于突出显示地图上的某些区域。
-  折线-通常出现时显示一个路由。
-  圆形-用来突出显示地图的圆形区域。


此外，可以创建自定义覆盖层以显示任意几何图形与精细的自定义绘制代码。 例如，天气雷达图将自定义覆盖的良好候选项。

#### <a name="adding-an-overlay"></a>添加一个覆盖区

与批注类似，添加一个覆盖区涉及两个部分：

-  创建用于在覆盖区上的模型对象并将其添加到`MKMapView`。
-  为覆盖层中创建一个视图`MKMapViewDelegate`。


在覆盖区上的模型可以是任何`MKShape`子类。 包括 Xamarin.iOS`MKShape`子类的多边形、 折线和圆圈，通过`MKPolygon`，`MKPolyline`和`MKCircle`分别是类。

例如，下面的代码用于添加`MKCircle`:

```csharp
var circleOverlay = MKCircle.Circle (mapCenter, 1000);
map.AddOverlay (circleOverlay);
```

覆盖层的视图是`MKOverlayView`返回的实例`GetViewForOverlay`中`MKMapViewDelegate`。 每个`MKShape`都有一个相应`MKOverlayView`知道如何显示给定的形状。 有关`MKPolygon`没有`MKPolygonView`。 同样，`MKPolyline`对应于`MKPolylineView`，并为`MKCircle`没有`MKCircleView`。

例如，下面的代码返回`MKCircleView`为`MKCircle`:

```csharp
public override MKOverlayView GetViewForOverlay (MKMapView mapView, NSObject overlay)
{
    var circleOverlay = overlay as MKCircle;
    var circleView = new MKCircleView (circleOverlay);
    circleView.FillColor = UIColor.Blue;
    return circleView;
}
```

这将显示一个圆圈在地图上所示：

 ![](images/06-circle-overlay.png "地图上显示一个圆圈")

## <a name="local-search"></a>本地搜索

iOS 包括本地搜索 API 使用 Map 工具包，可用于指定地理区域中的关注点的异步搜索。

若要执行本地搜索，应用程序必须执行以下步骤：

1.  创建`MKLocalSearchRequest`对象。
1.  创建`MKLocalSearch`对象从`MKLocalSearchRequest`。
1.  调用`Start`方法`MKLocalSearch`对象。
1.  检索`MKLocalSearchResponse`在回调中的对象。


本地搜索 API 本身不提供用户界面。 它甚至不需要一个映射，以便使用。 但是，若要使本地搜索的实际用途，应用程序需要提供某种方式来指定搜索查询并显示结果。 此外，由于结果将包含位置数据，它将通常意义在地图上显示它们。

<a name="Adding_a_Local_Search_UI"/>

### <a name="adding-a-local-search-ui"></a>添加本地搜索 UI

若要接受搜索输入的一种方法是使用`UISearchBar`，其中提供的`UISearchController`并将结果显示在表中。

下面的代码添加`UISearchController`（具有的搜索栏属性） 中`ViewDidLoad`方法的`MapViewController`:

```csharp
//Creates an instance of a custom View Controller that holds the results
var searchResultsController = new SearchResultsViewController (map);

//Creates a search controller updater
var searchUpdater = new SearchResultsUpdator ();
searchUpdater.UpdateSearchResults += searchResultsController.Search;
            
//add the search controller
searchController = new UISearchController (searchResultsController) {
                SearchResultsUpdater = searchUpdater
            };

//format the search bar
searchController.SearchBar.SizeToFit ();
searchController.SearchBar.SearchBarStyle = UISearchBarStyle.Minimal;
searchController.SearchBar.Placeholder = "Enter a search query";

//the search bar is contained in the navigation bar, so it should be visible
searchController.HidesNavigationBarDuringPresentation = false;

//Ensure the searchResultsController is presented in the current View Controller 
DefinesPresentationContext = true;

//Set the search bar in the navigation bar
NavigationItem.TitleView = searchController.SearchBar;
```

请注意，您负责将搜索栏对象合并到用户界面。 在此示例中，我们将其分配给 TitleView 的导航栏中，但如果不在应用程序中使用导航控制器需要查找其显示的另一个位置。

在此代码片段中，我们创建另一个自定义视图控制器 – `searchResultsController` – 显示搜索结果，然后我们使用此对象将创建我们搜索的控制器对象。 我们还创建了新的搜索更新程序时用户与搜索栏将变为活动状态。 它接收通知有关与每个击键的搜索，并负责更新用户界面。
我们将看看如何实现两者`searchResultsController`和`searchResultsUpdater`本指南中更高版本。

这会导致搜索栏显示此工具针对映射，如下所示：

 ![](images/07-searchbar.png "通过代码图显示一个搜索栏")
 


### <a name="displaying-the-search-results"></a>显示搜索结果

若要显示搜索结果，我们需要创建自定义视图控制器;通常情况下`UITableViewController`。 如上所示`searchResultsController`传递给构造函数的`searchController`创建时。
下面的代码是如何创建此自定义视图控制器的示例：

```csharp
public class SearchResultsViewController : UITableViewController
{
    static readonly string mapItemCellId = "mapItemCellId";
    MKMapView map;

    public List<MKMapItem> MapItems { get; set; }

    public SearchResultsViewController (MKMapView map)
    {
        this.map = map;

        MapItems = new List<MKMapItem> ();
    }

    public override nint RowsInSection (UITableView tableView, nint section)
    {
        return MapItems.Count;
    }

    public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
    {
        var cell = tableView.DequeueReusableCell (mapItemCellId);

        if (cell == null)
            cell = new UITableViewCell ();

        cell.TextLabel.Text = MapItems [indexPath.Row].Name;
        return cell;
    }

    public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
    {
        // add item to map
        CLLocationCoordinate2D coord = MapItems [indexPath.Row].Placemark.Location.Coordinate;
        map.AddAnnotations (new MKPointAnnotation () {
            Title = MapItems [indexPath.Row].Name,
            Coordinate = coord
        });

        map.SetCenterCoordinate (coord, true);

        DismissViewController (false, null);
    }

    public void Search (string forSearchString)
    {
        // create search request
        var searchRequest = new MKLocalSearchRequest ();
        searchRequest.NaturalLanguageQuery = forSearchString;
        searchRequest.Region = new MKCoordinateRegion (map.UserLocation.Coordinate, new MKCoordinateSpan (0.25, 0.25));

        // perform search
        var localSearch = new MKLocalSearch (searchRequest);

        localSearch.Start (delegate (MKLocalSearchResponse response, NSError error) {
            if (response != null && error == null) {
                this.MapItems = response.MapItems.ToList ();
                this.TableView.ReloadData ();
            } else {
                Console.WriteLine ("local search error: {0}", error);
            }
        });


    }
}
```

### <a name="updating-the-search-results"></a>正在更新搜索结果

`SearchResultsUpdater`充当之间的调节器`searchController`的搜索栏和搜索结果。 

在此示例中我们必须首先创建中的搜索方法`SearchResultsViewController`。 若要这样做，我们必须创建`MKLocalSearch`对象，并使用它来发出搜索`MKLocalSearchRequest`，在传递给回调中检索的结果`Start`方法的`MKLocalSearch`对象。 然后在返回结果`MKLocalSearchResponse`对象的数组`MKMapItem`对象：

```csharp
public void Search (string forSearchString)
{
    // create search request
    var searchRequest = new MKLocalSearchRequest ();
    searchRequest.NaturalLanguageQuery = forSearchString;
    searchRequest.Region = new MKCoordinateRegion (map.UserLocation.Coordinate, new MKCoordinateSpan (0.25, 0.25));

    // perform search
    var localSearch = new MKLocalSearch (searchRequest);

    localSearch.Start (delegate (MKLocalSearchResponse response, NSError error) {
        if (response != null && error == null) {
            this.MapItems = response.MapItems.ToList ();
            this.TableView.ReloadData ();
        } else {
            Console.WriteLine ("local search error: {0}", error);
        }
    });


}
```

然后，在我们`MapViewController`我们将创建的自定义实现`UISearchResultsUpdating`，该值将赋给`SearchResultsUpdater`属性的我们`searchController`中[添加本地搜索 UI](#Adding_a_Local_Search_UI)部分：

```csharp
public class SearchResultsUpdator : UISearchResultsUpdating
{
    public event Action<string> UpdateSearchResults = delegate {};

    public override void UpdateSearchResultsForSearchController (UISearchController searchController)
    {
        this.UpdateSearchResults (searchController.SearchBar.Text);
    }
}
```

上述实现将批注添加到代码图时从结果中，选择项，如下所示：

 ![](images/08-search-results.png "在结果中选择项时，将向地图添加批注")
 
> [!IMPORTANT]
> `UISearchController` 在 iOS 8 中实现。 如果你想要支持设备早于此操作，则将需要使用`UISearchDisplayController`。



## <a name="summary"></a>总结

检查此文 *地图* *工具包* 适用于 iOS 的框架。 首先，探讨了`MKMapView`类允许交互式地图要包含在应用程序中。 然后，它演示了如何进一步自定义使用批注和覆盖层的映射。 最后，它会检查本地搜索功能已添加到 Map kit 包与 iOS 6.1，演示如何使用执行位置基于查询兴趣点并将其添加到地图。

## <a name="related-links"></a>相关链接

- [SearchController](https://github.com/xamarin/recipes/tree/master/Recipes/ios/content_controls/search-controller)
- [MapDemo （示例）](https://developer.xamarin.com/samples/monotouch/MapDemo)
