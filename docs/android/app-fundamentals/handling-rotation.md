---
title: 处理旋转
description: 本主题介绍如何处理在 Xamarin.Android 中的设备方向更改。 它介绍了如何使用 Android 资源系统以自动加载资源的特定设备方向以及如何以编程方式处理方向更改。
ms.prod: xamarin
ms.assetid: 6D33ADF7-ED81-0256-479D-D9E3787A76B0
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 62e64be89e26e5a8412cd34221da581e99fc5e6a
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57668213"
---
# <a name="handling-rotation"></a>处理旋转

_本主题介绍如何处理在 Xamarin.Android 中的设备方向更改。它介绍了如何使用 Android 资源系统以自动加载资源的特定设备方向以及如何以编程方式处理方向更改。_


## <a name="overview"></a>概述

由于移动设备轻松地旋转，内置旋转是移动操作系统中的标准功能。 Android 提供复杂的框架来处理旋转中的应用程序，而不管在 XML 中或以编程方式在代码中以声明方式创建的用户界面。 在自动处理旋转的设备上的声明性布局更改时，应用程序可以受益于与 Android 资源系统的紧密集成。 对于以编程方式布局，则必须手动处理更改。 这样可以更好地控制在运行时，但代价更多的工作是开发人员。 应用程序还可以选择以选择退出活动重新启动并手动控制的方向更改。

本指南将检查以下有针对性的主题：

-   **声明性布局旋转**&ndash;如何使用 Android 资源系统生成能够识别方向的应用程序，包括如何加载布局和特定方向的绘图。

-   **以编程方式布局旋转**&ndash;如何以编程方式添加控件，以及如何手动处理方向的更改。


## <a name="handling-rotation-declaratively-with-layouts"></a>处理旋转以声明方式与布局

通过遵循命名约定的文件夹中包括的文件，Android 会自动加载相应的文件时在方向更改。
这包括对支持：

-   *布局资源*&ndash;指定哪些布局文件针对每个方向进行扩充。

-   *可绘制资源*&ndash;指定哪个绘图加载针对每个方向。


### <a name="layout-resources"></a>布局资源

默认情况下，Android XML (AXML) 文件包含在**资源/布局**文件夹用于呈现视图的活动。 如果没有其他布局的资源提供专为横向，所使用的纵向或横向方向该文件夹内的资源。 默认项目模板创建的项目结构，请考虑：

[![默认项目模板结构](handling-rotation-images/00.png)](handling-rotation-images/00.png#lightbox)

此项目创建单个**Main.axml**中的文件**资源/布局**文件夹。 当活动的`OnCreate`方法调用时，它增加所空间中定义的视图**Main.axml，** 其中声明一个按钮，如下面的 XML 中所示：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
  android:orientation="vertical"
  android:layout_width="fill_parent"
  android:layout_height="fill_parent">
<Button  
  android:id="@+id/myButton"
  android:layout_width="fill_parent" 
  android:layout_height="wrap_content" 
  android:text="@string/hello"/>
</LinearLayout>
```

如果将设备旋转为横向方向，该活动`OnCreate`再次调用方法和相同**Main.axml**扩充文件，如下面的屏幕截图中所示：

[![相同但在屏幕上横向方向](handling-rotation-images/01-sml.png)](handling-rotation-images/01.png#lightbox)


#### <a name="orientation-specific-layouts"></a>特定于方向的布局

除了布局文件夹 (其默认设置是纵向，还可以显式命名*布局端口*通过包括名为的文件夹`layout-land`)，应用程序可以定义它需要在无需任何代码的环境中的视图更改。

假设**Main.axml**文件包含以下 XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
  android:layout_width="fill_parent"
  android:layout_height="fill_parent">
  <TextView
    android:text="This is portrait"
    android:layout_height="wrap_content"
    android:layout_width="fill_parent" />
</RelativeLayout>
```

如果文件夹名为包含一个附加的布局 land **Main.axml**文件添加到项目中，放大布局在环境中时将立即导致加载新添加的 Android **Main.axml。** 请考虑横向形式**Main.axml**文件，其中包含以下代码 (为简单起见，此 XML 类似于默认纵向版本的代码，但使用中的不同字符串`TextView`):

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
  android:layout_width="fill_parent"
  android:layout_height="fill_parent">
  <TextView
    android:text="This is landscape"
    android:layout_height="wrap_content"
    android:layout_width="fill_parent" />
</RelativeLayout>
```

运行此代码并将设备从纵向向横向旋转演示新的 XML 加载，如下所示：

[![纵向和横向打印纵向模式的屏幕截图](handling-rotation-images/02.png)](handling-rotation-images/02.png#lightbox)


### <a name="drawable-resources"></a>可绘制资源

在旋转，期间 Android 将视为可绘制资源同样到布局资源。 在这种情况下，系统获取从绘图**资源/drawable**并**资源/drawable land**文件夹，分别。

例如，假设项目包含名为 Monkey.png 中的映像**资源/drawable**文件夹中，可绘制引用的位置从`ImageView`中 XML 如下：

```xml
<ImageView
  android:layout_height="wrap_content"
  android:layout_width="wrap_content"
  android:src="@drawable/monkey"
  android:layout_centerVertical="true"
  android:layout_centerHorizontal="true" />
```

让我们进一步假设的不同版本的**Monkey.png**包含下面**资源/drawable land**。 就像布局文件，当设备时的旋转，针对给定方向，可绘制的更改，如下所示：

[![不同版本的 Monkey.png 纵向和横向模式中所示](handling-rotation-images/03.png)](handling-rotation-images/03.png#lightbox)


## <a name="handling-rotation-programmatically"></a>以编程方式处理旋转

有时，我们在代码中定义的布局。 这可能由于各种原因，包括技术限制，限制、 开发人员首选项等。当我们将以编程方式添加控件时，必须手动会考虑设备方向，当我们使用 XML 资源自动处理应用程序。


### <a name="adding-controls-in-code"></a>在代码中添加控件

若要以编程方式添加控件，应用程序需要执行以下步骤：

-  创建布局。
-  设置布局参数。
-  创建控件。
-  设置控件的布局参数。
-  将控件添加到布局。
-  设置为内容视图布局。

例如，考虑一个用户界面，包含单个`TextView`控件添加到`RelativeLayout`，如下面的代码中所示。

```csharp
protected override void OnCreate (Bundle bundle)
{
  base.OnCreate (bundle);
                        
  // create a layout
  var rl = new RelativeLayout (this);

  // set layout parameters
  var layoutParams = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.FillParent);
  rl.LayoutParameters = layoutParams;
        
  // create TextView control
  var tv = new TextView (this);

  // set TextView's LayoutParameters
  tv.LayoutParameters = layoutParams;
  tv.Text = "Programmatic layout";

  // add TextView to the layout
  rl.AddView (tv);
        
  // set the layout as the content view
  SetContentView (rl);
}
```

此代码创建的实例`RelativeLayout`类并设置其`LayoutParameters`属性。 `LayoutParams`类是封装如何重用的方式放置控件的 Android 的方法。 创建布局的实例后，可以创建并添加到该控件。 控件还具有`LayoutParameters`，如`TextView`在此示例中。 之后`TextView`创建后，将其添加到`RelativeLayout`并设置`RelativeLayout`中显示的应用程序的内容视图结果作为`TextView`所示：

[![纵向和横向模式中所示的递增计数器按钮](handling-rotation-images/04.png)](handling-rotation-images/04.png#lightbox)


### <a name="detecting-orientation-in-code"></a>在代码中检测方向

如果应用程序尝试加载每个方向一个不同的用户界面时`OnCreate`称为 （会出现这种每次旋转设备时），它必须检测方向，，然后加载所需的用户界面代码。 Android 有一个名为类`WindowManager`，这可以用于确定通过当前的设备旋转`WindowManager.DefaultDisplay.Rotation`属性，如下所示：

```csharp
protected override void OnCreate (Bundle bundle)
{
  base.OnCreate (bundle);
                        
  // create a layout
  var rl = new RelativeLayout (this);

  // set layout parameters
  var layoutParams = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.FillParent);
  rl.LayoutParameters = layoutParams;
                        
  // get the initial orientation
  var surfaceOrientation = WindowManager.DefaultDisplay.Rotation;
  // create layout based upon orientation
  RelativeLayout.LayoutParams tvLayoutParams;
                
  if (surfaceOrientation == SurfaceOrientation.Rotation0 || surfaceOrientation == SurfaceOrientation.Rotation180) {
    tvLayoutParams = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.WrapContent);
  } else {
    tvLayoutParams = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.WrapContent);
    tvLayoutParams.LeftMargin = 100;
    tvLayoutParams.TopMargin = 100;
  }
                        
  // create TextView control
  var tv = new TextView (this);
  tv.LayoutParameters = tvLayoutParams;
  tv.Text = "Programmatic layout";
        
  // add TextView to the layout
  rl.AddView (tv);
        
  // set the layout as the content view
  SetContentView (rl);
}
```

此代码将设置`TextView`为定位的 100 像素，角的屏幕上，会自动进行动画处理到新的布局时旋转为横向，如下所示：

[![在纵向和横向模式中保留视图状态](handling-rotation-images/05.png)](handling-rotation-images/05.png#lightbox)


### <a name="preventing-activity-restart"></a>阻止活动重新启动

除了处理中的所有内容`OnCreate`，应用程序还可以防止某个活动正在重新启动时在方向更改通过设置`ConfigurationChanges`中`ActivityAttribute`，如下所示：

```csharp
[Activity (Label = "CodeLayoutActivity", ConfigurationChanges=Android.Content.PM.ConfigChanges.Orientation | Android.Content.PM.ConfigChanges.ScreenSize)]
```
现在时将设备旋转，将不重新启动该活动。 若要手动处理方向更改这种情况下，活动可以重写`OnConfigurationChanged`方法，并确定打印方向从`Configuration`对象传入，如下面的活动的新实现中所示：

```csharp
[Activity (Label = "CodeLayoutActivity", ConfigurationChanges=Android.Content.PM.ConfigChanges.Orientation | Android.Content.PM.ConfigChanges.ScreenSize)]
public class CodeLayoutActivity : Activity
{
  TextView _tv;
  RelativeLayout.LayoutParams _layoutParamsPortrait;
  RelativeLayout.LayoutParams _layoutParamsLandscape;
                
  protected override void OnCreate (Bundle bundle)
  {
    // create a layout
    // set layout parameters
    // get the initial orientation

    // create portrait and landscape layout for the TextView
    _layoutParamsPortrait = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.WrapContent);
                
    _layoutParamsLandscape = new RelativeLayout.LayoutParams (ViewGroup.LayoutParams.FillParent, ViewGroup.LayoutParams.WrapContent);
    _layoutParamsLandscape.LeftMargin = 100;
    _layoutParamsLandscape.TopMargin = 100;
                        
    _tv = new TextView (this);
                        
    if (surfaceOrientation == SurfaceOrientation.Rotation0 || surfaceOrientation == SurfaceOrientation.Rotation180) {
      _tv.LayoutParameters = _layoutParamsPortrait;
    } else {
      _tv.LayoutParameters = _layoutParamsLandscape;
    }
                        
    _tv.Text = "Programmatic layout";
    rl.AddView (_tv);
    SetContentView (rl);
  }
                
  public override void OnConfigurationChanged (Android.Content.Res.Configuration newConfig)
  {
    base.OnConfigurationChanged (newConfig);
                        
    if (newConfig.Orientation == Android.Content.Res.Orientation.Portrait) {
      _tv.LayoutParameters = _layoutParamsPortrait;
      _tv.Text = "Changed to portrait";
    } else if (newConfig.Orientation == Android.Content.Res.Orientation.Landscape) {
      _tv.LayoutParameters = _layoutParamsLandscape;
      _tv.Text = "Changed to landscape";
    }
  }
}
```

此处`TextView's`横向和纵向初始化布局参数。 类变量保存参数，连同`TextView`本身，因为该活动不会重新创建方向更改时。 该代码仍使用`surfaceOrientartion`中`OnCreate`若要设置的初始布局`TextView`。 在此之后，`OnConfigurationChanged`处理所有后续布局更改。

当我们运行该应用程序时，Android 将加载用户界面更改，如设备旋转发生，并且不会重启活动。


## <a name="preventing-activity-restart-for-declarative-layouts"></a>声明性布局的阻止活动重启

如果我们在 XML 中定义的布局，则还可以避免导致的设备旋转活动重启。 例如，我们可以使用这种方法，如果我们想要防止活动重新启动 (出于性能原因，可能是) 和我们无需加载为不同的方向的新资源。

若要执行此操作，我们遵循以编程方式布局，我们使用的相同过程。 只需将设置`ConfigurationChanges`中`ActivityAttribute`我们做的一样`CodeLayoutActivity`前面。 确实需要运行方向更改可以重新实现中的任何代码`OnConfigurationChanged`方法。


## <a name="maintaining-state-during-orientation-changes"></a>在方向更改过程中维护状态

是否以声明方式或以编程方式处理旋转，所有的 Android 应用程序应实现的相同技术用于管理设备方向更改时的状态。 管理状态非常重要，因为旋转 Android 设备时，系统重新启动正在运行的活动。 Android 这样做是为了方便加载备用资源，例如布局和专为特定方向的绘图。 它重新启动时，该活动将失去它可能已在本地类变量中存储任何暂时性状态。 因此，如果某个活动是依赖状态，它必须保存在应用程序级别的状态。 应用程序需要处理保存和还原，其目的是跨方向更改保留的任何应用程序状态。

有关在 Android 中持久保存状态的详细信息，请参阅[活动生命周期](~/android/app-fundamentals/activity-lifecycle/index.md)指南。


## <a name="summary"></a>总结

本文介绍如何使用 Android 的内置功能来使用旋转。 首先，它介绍了如何使用 Android 资源系统可创建方向识别应用程序。 然后，它显示如何在代码中添加控件，以及如何手动处理方向的更改。



## <a name="related-links"></a>相关链接

- [旋转演示 （示例）](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/RotationDemo/)
- [活动生命周期](~/android/app-fundamentals/activity-lifecycle/index.md)
- [处理运行时更改](https://developer.android.com/guide/topics/resources/runtime-changes.html)
- [快速屏幕方向更改](http://android-developers.blogspot.com/2009/02/faster-screen-orientation-change.html)
