---
title: 启动 Maps 应用程序
description: 如何从你的 Xamarin Android 应用程序中启动内置的地图应用程序。
ms.prod: xamarin
ms.assetid: 929EACB8-8950-50E1-093C-43FB5F1F1CD5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/25/2018
ms.openlocfilehash: b436ea4f5d8c70f0ca641bea72d1230fc07c5086
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69523778"
---
# <a name="launching-the-maps-application"></a>启动 Maps 应用程序

在 Xamarin 中使用地图的最简单方法是利用如下所示的内置地图应用程序:

[![内置 Google Maps 应用的示例屏幕截图](maps-application-images/01-mapsapplication.png)](maps-application-images/01-mapsapplication.png#lightbox)

使用 maps 应用程序时, 地图不会成为应用程序的一部分。 相反, 你的应用程序将启动 maps 应用程序并将映射加载到外部。 下一节将介绍如何使用 Xamarin 启动类似于上面的映射。


## <a name="creating-the-intent"></a>创建意向

使用 map 应用程序非常简单, 只需使用相应的 URI 创建意向, 将操作设置为 ActionView, 然后调用 StartActivity 方法。 例如, 以下代码将启动以给定纬度和经度为中心的映射应用程序:

```csharp
var geoUri = Android.Net.Uri.Parse ("geo:42.374260,-71.120824");
var mapIntent = new Intent (Intent.ActionView, geoUri);
StartActivity (mapIntent);
```

此代码是启动上面的屏幕截图中所示的地图所需的全部代码。 除了指定纬度和经度外, 映射的 URI 方案还支持多个其他选项。


## <a name="geo-uri-scheme"></a>地域 URI 方案

上面的代码使用了 geo 方案来创建 URI。 此 URI 方案支持多种格式, 如下所示:

- `geo:latitude,longitude`&ndash;打开以 lat/lon 为中心的地图应用程序。 

- `geo:latitude,longitude?z=zoom`&ndash;打开以 lat/lon 为中心的地图应用程序, 并将其缩放到指定的级别。 缩放级别的范围可以为1到 23:1显示整个地球, 23 是最接近的缩放级别。

- `geo:0,0?q=my+street+address`&ndash;打开地图应用程序到街道地址的位置。 

- `geo:0,0?q=business+near+city`&ndash;打开 maps 应用程序并显示带批注的搜索结果。 


采用查询 (即街道地址或搜索词) 的 URI 版本使用 Google 的 geocoder 服务检索随后显示在地图上的位置。 例如, URI `geo:0,0?q=coop+Cambridge`会生成如下所示的映射:

[![使用搜索词显示 Google Maps 的示例屏幕截图](maps-application-images/02-mapsearch.png)](maps-application-images/02-mapsearch.png#lightbox)



有关地理 URI 方案的详细信息, 请参阅[在地图上显示位置](https://developer.android.com/guide/components/intents-common.html#Maps)。


## <a name="street-view"></a>街道视图

除了地域方案, Android 还支持从意向加载街道视图。 从 Xamarin 启动的街道视图应用程序的示例如下所示:

[![街道视图的示例屏幕截图](maps-application-images/03-streetview.png)](maps-application-images/03-streetview.png#lightbox)

若要启动街道视图, 只需使用`google.streetview` URI 方案, 如以下代码所示:

```csharp
var streetViewUri = Android.Net.Uri.Parse (
       "google.streetview:cbll=42.374260,-71.120824&cbp=1,90,,0,1.0&mz=20");  
var streetViewIntent = new Intent (Intent.ActionView, streetViewUri);  
StartActivity (streetViewIntent);
```

以上使用的 streetview URI 方案采用以下形式:

```csharp
google.streetview:cbll=lat,lng&cbp=1,yaw,,pitch,zoom&mz=mapZoom
```

如您所见, 有几个支持的参数, 如下所示:

- `lat`&ndash;要在街道视图中显示的位置的纬度。

- `lng`&ndash;要在街道视图中显示的位置的经度。

- `pitch`&ndash;街道视图全景的角度, 以度为单位从中心测量, 以度为单位, 90 度为垂直朝下,-90 度为直线。

- `yaw`&ndash;街道视图全景的中心视图, 以顺时针度为单位进行测量。

- `zoom`&ndash;街道视图全景图的缩放乘数, 其中, 1.0 = 正常缩放, 2.0 = 放大 2, 3.0 = 放大 4x, 等等。

- `mz`&ndash;从街道视图转到地图应用程序时将使用的地图缩放级别。


使用内置地图应用程序或街道视图是快速添加映射支持的简单方法。 但是, Android 地图 API 提供更好的控制映射体验。
