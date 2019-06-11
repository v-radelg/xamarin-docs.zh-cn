---
title: 使用 Xamarin.Android 和穿戴设备的操作系统中的屏幕大小
ms.prod: xamarin
ms.assetid: 77831169-C663-4D42-B742-B8B556B1DA4B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: a9b71fb069a428d9bec03481c986f4deb4c904ea
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827731"
---
# <a name="working-with-screen-sizes"></a>使用屏幕大小

Android Wear 设备可以有矩形或圆角显示，这也可以是不同的大小。

![矩形和圆形穿戴设备的屏幕截图显示](screen-sizes-images/moyeu-wear.png)

## <a name="identifying-screen-type"></a>确定屏幕上键入

Wear 支持库提供了一些控件可帮助您检测和适应不同的屏幕的形状，如`WatchViewStub`和`BoxInsetLayout`。

请注意的其他支持库控件 (如`GridViewPager`)*自动*检测屏幕形状本身并不应如下所述控件的子级添加。

### <a name="watchviewstub"></a>WatchViewStub

请参阅[WatchViewStub](https://developer.xamarin.com/samples/monodroid/wear/WatchViewStub/)示例，了解有关如何检测屏幕上，键入并显示每个类型不同的布局。

主要的布局文件包含`android.support.wearable.view.WatchViewStub`引用不同的布局矩形的圆角屏幕，即使用`app:rectLayout`和`app:roundLayout`属性：

```xml
<android.support.wearable.view.WatchViewStub
    xmlns:app="http://schemas.android.com/apk/res-auto"
  android:layout_width="match_parent"
  android:layout_height="match_parent"
  android:id="@+id/stub"
  app:rectLayout="@layout/rect_layout"
  app:roundLayout="@layout/round_layout" />
```

该解决方案包含有关每个样式将在运行时选择不同的布局：

![资源/布局中所示的文件](screen-sizes-images/solution.png)


### <a name="boxinsetlayout"></a>BoxInsetLayout

而不是生成每个屏幕上，键入不同的布局，还可以创建适应矩形或圆角的屏幕的单一视图。

这[Google 示例](https://developer.android.com/training/wearables/ui/layouts.html#same-layout)演示如何使用`BoxInsetLayout`矩形的圆角屏幕上使用相同的布局。


## <a name="wear-ui-designer"></a>Wear UI 设计器

Xamarin Android 设计器支持矩形的圆角屏幕：

![在 Xamarin Android 设计器中选择 Android Wear 正方形屏幕](screen-sizes-images/design-screen-type.png)

矩形样式中的设计图面将如下所示：

![矩形样式中的设计图面](screen-sizes-images/design-rect.png) 

舍入样式中的设计图面将如下所示：

![舍入样式中的设计图面](screen-sizes-images/design-round.png)


## <a name="wear-simulator"></a>穿戴设备模拟器

**Google 仿真器管理器**包含设备定义，用于同时为屏幕类型。 您可以创建矩形和圆形仿真程序来测试您的应用程序。

![Wear Google 仿真器管理器中显示的设备定义](screen-sizes-images/emulator-devices.png)

为矩形屏幕如下所示在仿真程序：

![仿真程序呈现的矩形屏幕](screen-sizes-images/recipe-2.png) 

为圆角屏幕，它会呈现如下：

![仿真程序呈现的圆角屏幕](screen-sizes-images/recipe-2-round.png)

## <a name="video"></a>视频

[适用于 Android 穿戴设备的全屏应用](https://www.youtube.com/watch?v=naf_WbtFAlY)从[developers.google.com](https://www.youtube.com/channel/UC_x5XG1OV2P6uZZ5FSM9Ttw)。

