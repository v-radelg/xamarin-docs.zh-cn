---
title: 初始屏幕
description: Android 应用程序需要一些时间才能启动，尤其是在设备上首次启动应用程序。 初始屏幕可能会显示开始向上向用户或指示品牌的进度。
ms.prod: xamarin
ms.assetid: 26480465-CE19-71CD-FC7D-69D0990D05DE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/06/2018
ms.openlocfilehash: b28dba9031840b312868e2ebc45e348a390d3b12
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67675050"
---
# <a name="splash-screen"></a>初始屏幕

_Android 应用程序需要一些时间才能启动，尤其是在设备上首次启动应用程序。初始屏幕可能会显示开始向上向用户或指示品牌的进度。_


## <a name="overview"></a>概述

Android 应用程序需要一些时间才能启动，尤其是在第一个期间应用程序运行在设备上 (有时这称为_冷启动_)。 可能会显示初始屏幕启动进度给用户，或者根本不显示品牌信息以确定和推广应用程序。

本指南介绍 Android 应用程序中实现初始屏幕的一项技术。 其中包括以下步骤：

1.  创建初始屏幕的可绘制资源。

2.  定义将显示可绘制资源的新主题。

3.  将新活动添加到将用作由上一步中创建的主题定义在初始屏幕的应用程序。

[![示例 Xamarin 徽标初始屏幕跟应用屏幕](splash-screen-images/splashscreen-01-sml.png)](splash-screen-images/splashscreen-01.png#lightbox)


## <a name="requirements"></a>要求

本指南假定应用程序面向 Android API 级别 15 (Android 4.0.3) 或更高版本。 应用程序还必须具有**Xamarin.Android.Support.v4**并**Xamarin.Android.Support.v7.AppCompat** NuGet 包添加到项目中。

中可能找到的所有代码和本指南中的 XML[初始屏幕](https://developer.xamarin.com/samples/monodroid/SplashScreen)用于本指南的示例项目。


## <a name="implementing-a-splash-screen"></a>实现初始屏幕

呈现和显示初始屏幕的最快方法是创建一个自定义主题，并将其应用到表现出的初始屏幕的活动。 当呈现该活动时，它加载主题，并适用于活动的背景 （引用主题） 的可绘制资源。 这种方法可以避免创建布局文件。

显示的品牌的活动作为实现初始屏幕可绘制，执行任何初始化和启动任何任务。 一旦具有引导应用程序，初始屏幕活动启动主活动，并从应用程序的 back 堆栈中移除本身。


### <a name="creating-a-drawable-for-the-splash-screen"></a>创建可绘制的初始屏幕

初始屏幕将显示 XML 可绘制背景的初始屏幕活动。 需要使用图像的位图化图像 （如 PNG 或 JPG） 来显示它。

在本指南中，我们将使用[层列表](https://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList)中心应用程序中的初始屏幕图像。 以下代码片段示范了`drawable`资源使用`layer-list`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
  <item>
    <color android:color="@color/splash_background"/>
  </item>
  <item>
    <bitmap
        android:src="@drawable/splash"
        android:tileMode="disabled"
        android:gravity="center"/>
  </item>
</layer-list>
```

这`layer-list`初始图像将居中**splash.png**由指定的背景上`@color/splash_background`资源。 将此 XML 文件中的放置**资源/drawable**文件夹 (例如， **Resources/drawable/splash_screen.xml**)。

创建初始屏幕可绘制后下, 一步是创建初始屏幕的主题。


### <a name="implementing-a-theme"></a>实现一个主题

若要创建初始屏幕活动的自定义主题，请编辑 （或添加） 文件**values/styles.xml**并创建新的`style`初始屏幕元素。 一个示例**values/style.xml**如下所示的文件是与`style`名为**MyTheme.Splash**:

```xml
<resources>
  <style name="MyTheme.Base" parent="Theme.AppCompat.Light">
  </style>

  <style name="MyTheme" parent="MyTheme.Base">
  </style>

  <style name="MyTheme.Splash" parent ="Theme.AppCompat.Light.NoActionBar">
    <item name="android:windowBackground">@drawable/splash_screen</item>
    <item name="android:windowNoTitle">true</item>
    <item name="android:windowFullscreen">true</item>
  </style>
</resources>
```

**MyTheme.Splash**是非常 spartan&ndash;声明窗口背景，显式从窗口中，删除的标题栏并声明它是全屏幕。 如果你想要创建初始屏幕，它模拟您的应用程序的 UI 之前活动增大第一个布局,，则可以使用`windowContentOverlay`而非`windowBackground`样式定义中。 在这种情况下，还必须修改**splash_screen.xml** drawable，以便它显示你的 UI 的模拟。


### <a name="create-a-splash-activity"></a>创建初始活动

现在，我们需要适用于 Android 启动我们的初始映像并执行的任何启动任务的新活动。 以下代码是完整的初始屏幕实现的示例：

```csharp
[Activity(Theme = "@style/MyTheme.Splash", MainLauncher = true, NoHistory = true)]
public class SplashActivity : AppCompatActivity
{
    static readonly string TAG = "X:" + typeof(SplashActivity).Name;

    public override void OnCreate(Bundle savedInstanceState, PersistableBundle persistentState)
    {
        base.OnCreate(savedInstanceState, persistentState);
        Log.Debug(TAG, "SplashActivity.OnCreate");
    }

    // Launches the startup task
    protected override void OnResume()
    {
        base.OnResume();
        Task startupWork = new Task(() => { SimulateStartup(); });
        startupWork.Start();
    }

    // Simulates background work that happens behind the splash screen
    async void SimulateStartup ()
    {
        Log.Debug(TAG, "Performing some startup work that takes a bit of time.");
        await Task.Delay (8000); // Simulate a bit of startup work.
        Log.Debug(TAG, "Startup work is finished - starting MainActivity.");
        StartActivity(new Intent(Application.Context, typeof (MainActivity)));
    }
}
```

`SplashActivity` 显式使用已创建在上一节中重写应用程序的默认主题的主题。
无需加载的布局`OnCreate`正如主题所声明可绘制背景。

务必要设置`NoHistory=true`属性，以便从 back 堆栈中移除活动。 若要防止后退按钮取消启动过程，您可以覆盖`OnBackPressed`，并让它不执行任何操作：

```csharp
public override void OnBackPressed() { }
```

以异步方式在执行启动工作`OnResume`。 这是必需的因此，启动工作不会变慢或延迟的启动屏幕的外观。 完成工作后，`SplashActivity`将启动`MainActivity`和用户可能会开始与应用交互。

这一新`SplashActivity`通过设置设置为应用程序的启动器活动`MainLauncher`属性为`true`。 因为`SplashActivity`启动器活动中，您必须编辑现`MainActivity.cs`，并删除`MainLauncher`属性从`MainActivity`:

```csharp
[Activity(Label = "@string/ApplicationName")]
public class MainActivity : AppCompatActivity
{
    // Code omitted for brevity
}
```

## <a name="landscape-mode"></a>横向模式

在上一步骤中实现初始屏幕将在纵向和横向模式下正常显示。 但是，在某些情况下是需要具有单独的初始屏幕的纵向和横向模式 （例如，如果初始图像是全屏幕）。

若要添加的横向模式下的初始屏幕，请使用以下步骤：

1. 在中**可绘制资源/** 文件夹中，添加你想要使用初始屏幕图像的布局版本。 在此示例中， **splash_logo_land.png**是上面的示例 （它使用白色时将其视为而不是蓝色） 中使用的徽标的横向版本。

2. 在**资源/drawable**文件夹中，创建的布局版本`layer-list`可绘制的先前定义 (例如， **splash_screen_land.xml**)。 此文件中设置初始屏幕图像横向形式的位图路径。 在以下示例中， **splash_screen_land.xml**使用**splash_logo_land.png**:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <layer-list xmlns:android="http://schemas.android.com/apk/res/android">
      <item>
        <color android:color="@color/splash_background"/>
      </item>
      <item>
        <bitmap
            android:src="@drawable/splash_logo_land"
            android:tileMode="disabled"
            android:gravity="center"/>
      </item>
    </layer-list>
    ```

3.  创建**资源/值-land**如果它尚不存在的文件夹。

4.  将文件添加**colors.xml**并**style.xml**到**值 land** (这些都可以复制和修改从现有**values/colors.xml**并**values/style.xml**文件)。

5.  修改**值的土地/style.xml** ，以便它使用横向形式的可绘制`windowBackground`。 在此示例中， **splash_screen_land.xml**使用：

    ```xml
    <resources>
      <style name="MyTheme.Base" parent="Theme.AppCompat.Light">
      </style>
        <style name="MyTheme" parent="MyTheme.Base">
      </style>
      <style name="MyTheme.Splash" parent ="Theme.AppCompat.Light.NoActionBar">
        <item name="android:windowBackground">@drawable/splash_screen_land</item>
        <item name="android:windowNoTitle">true</item>  
        <item name="android:windowFullscreen">true</item>  
        <item name="android:windowContentOverlay">@null</item>  
        <item name="android:windowActionBar">true</item>  
      </style>
    </resources>
    ```

6.  修改**值的土地/colors.xml**配置想要在初始屏幕的布局版本使用的颜色。 在此示例中，初始背景色更改为蓝色的横向模式下：

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <resources>
      <color name="primary">#2196F3</color>
      <color name="primaryDark">#1976D2</color>
      <color name="accent">#FFC107</color>
      <color name="window_background">#F5F5F5</color>
      <color name="splash_background">#3498DB</color>
    </resources>
    ```

7.  生成并再次运行该应用。 旋转为横向模式时仍显示初始屏幕的设备。 初始屏幕更改为横向版本：

    [![旋转为横向模式的初始屏幕的](splash-screen-images/landscape-splash-sml.png)](splash-screen-images/landscape-splash.png#lightbox)


请注意，使用横向模式下初始屏幕不始终提供无缝体验。 默认情况下，Android 纵向模式中启动该应用，并转换它为横向模式下，即使设备已在横向模式下。 结果，启动应用时在设备处于横向模式时，如果该设备将简要显示纵向初始屏幕，然后对从纵向到横向初始屏幕旋转进行动画处理。 遗憾的是，发生此初始纵向到横向转换，即使`ScreenOrientation = Android.Content.PM.ScreenOrientation.Landscape`指定在初始活动的标志。 若要解决此限制的最佳方式是在纵向和横向模式中正确创建呈现单个初始屏幕图像。


## <a name="summary"></a>总结

本指南介绍在 Xamarin.Android 应用程序; 实现初始屏幕的一种方法也就是说，将自定义主题应用于启动活动。


## <a name="related-links"></a>相关链接

- [初始屏幕 （示例）](https://developer.xamarin.com/samples/monodroid/SplashScreen)
- [layer-list Drawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList)
- [材料设计模式-启动屏幕](https://material.io/design/communication/launch-screen.html#usage)
