---
title: 批注和覆盖层在 Xamarin.iOS
description: 本文提供了分步演练演示如何使用 Map kit 包批注和覆盖层功能。 它演示如何将地图添加到显示 Xamarin 发展 2013年会议的位置中的批注和覆盖层的应用程序。
ms.prod: xamarin
ms.assetid: 1BC4F7FC-AE3C-46D7-A4D3-18E142F55B8E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 445661513b0cf79df99d54ed0bb4b0261dd75c2a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61381473"
---
# <a name="annotations-and-overlays-in-xamarinios"></a>批注和覆盖层在 Xamarin.iOS

我们将在本演练中生成的应用程序如下所示：

 [![](ios-maps-walkthrough-images/00-map-overlay.png "MapKit 应用示例")](ios-maps-walkthrough-images/00-map-overlay.png#lightbox)
 
您可以发现中的已完成的代码[Maps 演练示例](https://developer.xamarin.com/samples/monotouch/MapsWalkthrough/)。

让我们首先创建一个新**iOS 空项目**，并为其赋予一个相应的名称。 我们将首先将代码添加到我们的视图控制器，以显示 MapView，并将为我们 MapDelegate 和自定义批注创建新的类。 为此，请按照以下步骤进行操作：

## <a name="viewcontroller"></a>ViewController


1. 添加以下命名空间到`ViewController`:

    ```csharp
    using MapKit;
    using CoreLocation;
    using UIKit
    using CoreGraphics
    ```

1. 添加`MKMapView`连同实例到类中，变量`MapDelegate`实例。 我们将创建`MapDelegate`很快：

    ```csharp
    public partial class ViewController : UIViewController
    {
        MKMapView map;
        MapDelegate mapDelegate;
        ...
    ```

1. 在控制器的`LoadView`方法中，添加`MKMapView`并将其设置为`View`控制器的属性：

    ```csharp
    public override void LoadView ()
    {
        map = new MKMapView (UIScreen.MainScreen.Bounds);
        View = map;
    }
    ```

    接下来，我们将添加代码以初始化中的映射 ViewDidLoad ' 方法。

1. 在`ViewDidLoad`添加代码以设置地图类型、 显示用户位置和允许缩放和平移：

    ```csharp
    // change map type, show user location and allow zooming and panning
    map.MapType = MKMapType.Standard;
    map.ShowsUserLocation = true;
    map.ZoomEnabled = true;
    map.ScrollEnabled = true;
    
    ```

1. 接下来，添加代码以定位地图中心，并将它的区域设置：

    ```csharp
    double lat = 30.2652233534254;
    double lon = -97.73815460962083;
    CLLocationCoordinate2D mapCenter = new CLLocationCoordinate2D (lat, lon);
    MKCoordinateRegion mapRegion = MKCoordinateRegion.FromDistance (mapCenter, 100, 100);
    map.CenterCoordinate = mapCenter;
    map.Region = mapRegion;
    
    ```

1. 创建的新实例`MapDelegate`并将其分配给`Delegate`的`MKMapView`。 同样，我们将 implcodeent`MapDelegate`很快：

    ```csharp
    mapDelegate = new MapDelegate ();
    map.Delegate = mapDelegate;     
    ```

1. 截至 iOS 8，您应为请求授权从用户要使用它们的位置，因此，让我们添加到我们的示例。 首先，定义`CLLocationManager`类级变量：

    ```csharp
    CLLocationManager locationManager = new CLLocationManager();
    ```

1. 在`ViewDidLoad`方法中，我们想要检查是否运行该应用程序的设备正在使用 iOS 8，以及它是否使用应用程序时，我们将请求授权：

    ```csharp
    if (UIDevice.CurrentDevice.CheckSystemVersion(8,0)){
                    locationManager.RequestWhenInUseAuthorization ();
                }
    ```

1. 最后，我们需要编辑**Info.plist**文件让用户请求其位置的原因。 在中**源**菜单**Info.plist**，添加以下项：
    
    `NSLocationWhenInUseUsageDescription` 
    
    和字符串： 

    `Maps Walkthrough Docs Sample`。


## <a name="conferenceannotationcs--a-class-for-custom-annotations"></a>ConferenceAnnotation.cs – 用于自定义批注的类


1. 我们将使用名为的批注的自定义类`ConferenceAnnotation`。 向项目添加以下类：

    ```csharp
    using System;
    using CoreLocation;
    using MapKit;
    
    namespace MapsWalkthrough
    {
        public class ConferenceAnnotation : MKAnnotation
        {
            string title;
            CLLocationCoordinate2D coord;
    
            public ConferenceAnnotation (string title,
            CLLocationCoordinate2D coord)
            {
                this.title = title;
                this.coord = coord;
            }
    
            public override string Title {
                get {
                    return title;
                }
            }
    
            public override CLLocationCoordinate2D Coordinate {
                get {
                    return coord;
                }
            }
        }
    }   
    ```

## <a name="viewcontroller---adding-the-annotation-and-overlay"></a>ViewController-添加批注和覆盖层

1. 使用`ConferenceAnnotation`就地我们可以将其添加到地图。 回到`ViewDidLoad`方法的`ViewController`，添加地图的中心坐标处的注释：

    ```csharp
    map.AddAnnotations (new ConferenceAnnotation ("Evolve Conference", mapCenter)); 
    ```

1. 我们还想要具有旅馆的覆盖。 添加以下代码以创建`MKPolygon`坐标用于旅馆提供，并将其添加到映射通过调用`AddOverlay`:

    ```csharp
    // add an overlay of the hotel
    MKPolygon hotelOverlay = MKPolygon.FromCoordinates(
        new CLLocationCoordinate2D[]{
        new CLLocationCoordinate2D(30.2649977168594, -97.73863627705),
        new CLLocationCoordinate2D(30.2648461170005, -97.7381627734755),
        new CLLocationCoordinate2D(30.2648355402574, -97.7381750192576),
        new CLLocationCoordinate2D(30.2647791309417, -97.7379872505988),
        new CLLocationCoordinate2D(30.2654525150319, -97.7377341711021),
        new CLLocationCoordinate2D(30.2654807195004, -97.7377994819399),
        new CLLocationCoordinate2D(30.2655089239607, -97.7377994819399),
        new CLLocationCoordinate2D(30.2656428950368, -97.738346460207),
        new CLLocationCoordinate2D(30.2650364981811, -97.7385709662122),
        new CLLocationCoordinate2D(30.2650470749025, -97.7386199493406)
    });
    
    map.AddOverlay (hotelOverlay);  
    ```
这将完成中的代码`ViewDidLoad`。 现在，我们需要实现我们`MapDelegate`类来处理创建批注，并分别覆盖视图。


## <a name="mapdelegate"></a>MapDelegate

1. 创建一个名为类`MapDelegate`，它继承自`MKMapViewDelegate`和包含`annotationId`变量以用作重复使用标识符的批注：

    ```csharp
    class MapDelegate : MKMapViewDelegate
    {
        static string annotationId = "ConferenceAnnotation";
        ...
    }
    ```
    我们仅具有一个批注此处以便重复使用代码并不是必需的但它是将其包含一个好办法。

1. 实现`GetViewForAnnotation`方法返回的视图`ConferenceAnnotation`使用**conference.png**本演练中包含的图像：

    ```csharp
    public override MKAnnotationView GetViewForAnnotation (MKMapView mapView, NSObject annotation)
    {
        MKAnnotationView annotationView = null;
    
        if (annotation is MKUserLocation)
            return null; 
    
        if (annotation is ConferenceAnnotation) {
    
            // show conference annotation
            annotationView = mapView.DequeueReusableAnnotation (annotationId);
    
            if (annotationView == null)
                annotationView = new MKAnnotationView (annotation, annotationId);
        
            annotationView.Image = UIImage.FromFile ("images/conference.png");
            annotationView.CanShowCallout = true;
        } 
    
        return annotationView;
    }
    ```

1. 当用户点击上批注时，我们想要显示的图像显示奥斯汀市/县。 添加以下变量添加到`MapDelegate`图像和视图以显示它：

    ```csharp
    UIImageView venueView;
    UIImage venueImage;
    ```

1. 接下来，若要显示的图像，点击批注时，实现`DidSelectAnnotation`方法，如下所示：

    ```csharp
    public override void DidSelectAnnotationView (MKMapView mapView, MKAnnotationView view)
    {
        // show an image view when the conference annotation view is selected
        if (view.Annotation is ConferenceAnnotation) {
    
            venueView = new UIImageView ();
            venueView.ContentMode = UIViewContentMode.ScaleAspectFit;
            venueImage = UIImage.FromFile ("image/venue.png");
            venueView.Image = venueImage;
            view.AddSubview (venueView);
    
            UIView.Animate (0.4, () => {
            venueView.Frame = new CGRect (-75, -75, 200, 200); });
        }
    }
    ```

1. 若要隐藏图像，当用户点击在地图上的其他任何位置，即取消选择批注，实现`DidSelectAnnotationView`方法，如下所示：

    ```csharp
    public override void DidDeselectAnnotationView (MKMapView mapView, MKAnnotationView view)
    {
        // remove the image view when the conference annotation is deselected
        if (view.Annotation is ConferenceAnnotation) {
    
            venueView.RemoveFromSuperview ();
            venueView.Dispose ();
            venueView = null;
        }
    }
    ```
    现在，我们在位置有批注的代码。 剩下的就是将代码添加到`MapDelegate`创建酒店覆盖的视图。

1. 添加以下实现`GetViewForOverlay`到`MapDelegate`:

    ```csharp
    public override MKOverlayView GetViewForOverlay (MKMapView mapView, NSObject overlay)
    {
        // return a view for the polygon
        MKPolygon polygon = overlay as MKPolygon;
        MKPolygonView polygonView = new MKPolygonView (polygon);
        polygonView.FillColor = UIColor.Blue;
        polygonView.StrokeColor = UIColor.Red;
        return polygonView;
    }
    ```

运行该应用程序。 现在，我们使用一个自定义批注和覆盖层互动地图 ！ 在批注上点击并显示奥斯汀的映像，如下所示：

 [![](ios-maps-walkthrough-images/01-map-image.png "在批注上点击，然后显示奥斯汀的图像")](ios-maps-walkthrough-images/01-map-image.png#lightbox)

## <a name="summary"></a>总结

在本文中介绍了如何将批注添加到映射，以及如何添加指定的多边形的覆盖。 我们还演示了如何将触控支持添加到要在地图上对图像进行动画处理的批注。


## <a name="related-links"></a>相关链接

- [地图演练示例](https://developer.xamarin.com/samples/monotouch/MapsWalkthrough/)
- [映射演示示例](https://developer.xamarin.com/samples/monotouch/MapDemo/)
- [iOS 地图](~/ios/user-interface/controls/ios-maps/index.md)
