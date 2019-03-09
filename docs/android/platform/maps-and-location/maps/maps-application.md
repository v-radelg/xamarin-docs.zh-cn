---
title: 启动映射应用程序
description: 如何启动内置地图中的应用程序从 Xamarin.Android 应用程序。
ms.prod: xamarin
ms.assetid: 929EACB8-8950-50E1-093C-43FB5F1F1CD5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/25/2018
ms.openlocfilehash: fa32783617fce99514560677184f17be904cd42d
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57670315"
---
# <a name="launching-the-maps-application"></a>启动映射应用程序

有关在 Xamarin.Android 中的映射的最简单方法是利用内置地图应用程序如下所示：

[![内置的 Google Maps 应用的示例屏幕截图](maps-application-images/01-mapsapplication.png)](maps-application-images/01-mapsapplication.png#lightbox)

使用地图应用程序时，该映射不会应用程序的一部分。 相反，你的应用程序将启动地图应用程序和加载外部地图。 下一节探讨如何使用 Xamarin.Android 以启动与上述的映射。


## <a name="creating-the-intent"></a>创建意向

使用映射应用程序是只需使用相应的 URI 创建一个意向，将操作设置为 ActionView，并调用 StartActivity 方法。 例如，下面的代码会启动以在给定的纬度和经度为中心的映射应用程序：

```csharp
var geoUri = Android.Net.Uri.Parse ("geo:42.374260,-71.120824");
var mapIntent = new Intent (Intent.ActionView, geoUri);
StartActivity (mapIntent);
```

此代码是只需启动上面的屏幕截图中所示的映射。 除了指定纬度和经度，映射的 URI 方案支持多个其他选项。


## <a name="geo-uri-scheme"></a>异地 URI 方案

上面的代码使用异地方案来创建一个 URI。 此 URI 方案支持几种格式，如下所示：

-   `geo:latitude,longitude` &ndash; 将打开地图应用程序以 lat/lon 中心。 

-   `geo:latitude,longitude?z=zoom` &ndash; 将打开的地图应用程序在 lat/lon 居中和缩放后为指定的级别。 缩放级别的范围可以从 1 到 23:1 显示整个地球和 23 是最接近的缩放级别。

-   `geo:0,0?q=my+street+address` &ndash; 将打开到街道地址的位置的地图应用程序。 

-   `geo:0,0?q=business+near+city` &ndash; 将打开地图应用程序并显示带批注的搜索结果。 


执行查询 （即街道地址或搜索词） 的 uri 的版本使用 Google 的地理编码器也服务检索然后地图显示的位置。 例如，URI`geo:0,0?q=coop+Cambridge`导致映射如下所示：

[![显示与搜索词的 Google Maps 的示例屏幕截图](maps-application-images/02-mapsearch.png)](maps-application-images/02-mapsearch.png#lightbox)



有关异地 URI 方案的详细信息，请参阅[在地图上显示位置](https://developer.android.com/guide/components/intents-common.html#Maps)。


## <a name="street-view"></a>街道视图

除了异地方案中，Android 还支持从意向加载街道的视图。 从 Xamarin.Android 启动街道视图应用程序的示例如下所示：

[![街道视图的示例屏幕截图](maps-application-images/03-streetview.png)](maps-application-images/03-streetview.png#lightbox)

若要启动的街道视图，只需使用`google.streetview`URI 方案，如下面的代码中所示：

```csharp
var streetViewUri = Android.Net.Uri.Parse (
       "google.streetview:cbll=42.374260,-71.120824&cbp=1,90,,0,1.0&mz=20");  
var streetViewIntent = new Intent (Intent.ActionView, streetViewUri);  
StartActivity (streetViewIntent);
```

使用上面的 google.streetview URI 方案采用以下形式：

```csharp
google.streetview:cbll=lat,lng&cbp=1,yaw,,pitch,zoom&mz=mapZoom
```

正如您所看到的有几个参数支持，如下所示：

-   `lat` &ndash; 要在街道视图中显示的位置的纬度。

-   `lng` &ndash; 要在街道视图中显示的位置的经度。

-   `pitch` &ndash; 垂直向上街道视图全景图，从中心中度 90 度是直接向下和-90 度为单位的角度。

-   `yaw` &ndash; 由北的度数的街道视图全景视图中心顺时针测量。

-   `zoom` &ndash; 放大乘数街道视图全景，其中 1.0 = 正常缩放，2.0 = 缩放的 2 x 3.0 = 缩放的 4 x，等等。

-   `mz` &ndash; 从街道视图转到映射应用程序时，将使用地图缩放级别。


使用内置映射应用程序或街道视图快速添加映射支持的简单方法。 但是，Android 的地图 API 提供更好地控制映射体验。
