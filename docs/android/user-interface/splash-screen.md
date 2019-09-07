---
title: 初始屏幕
description: Android 应用程序需要一些时间才能启动，特别是在设备上首次启动应用程序时。 初始屏幕可能会显示用户的启动进度或指示品牌。
ms.prod: xamarin
ms.assetid: 26480465-CE19-71CD-FC7D-69D0990D05DE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/06/2018
ms.openlocfilehash: b05ab7ee835a97f13af618332baec7a5ebf404ec
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764089"
---
# <a name="splash-screen"></a>初始屏幕

_Android 应用程序需要一些时间才能启动，特别是在设备上首次启动应用程序时。初始屏幕可能会显示用户的启动进度或指示品牌。_

## <a name="overview"></a>概述

Android 应用程序需要一些时间才能启动，特别是在第一次在设备上运行应用程序时（有时这称为_冷启动_）。 初始屏幕可能会显示用户的启动进度，也可能显示品牌信息来识别和升级应用程序。

本指南讨论了在 Android 应用程序中实现初始屏幕的一项技术。 其中包括以下步骤：

1. 创建初始屏幕的 "绘制资源"。

2. 定义将显示可绘制资源的新主题。

3. 向应用程序添加一个新活动，该活动将用作上一步骤中创建的主题定义的初始屏幕。

[![示例 Xamarin 徽标初始屏幕，后面跟有应用屏幕](splash-screen-images/splashscreen-01-sml.png)](splash-screen-images/splashscreen-01.png#lightbox)

## <a name="requirements"></a>要求

本指南假定应用程序面向 Android API 级别15（Android 4.0.3）或更高版本。 该应用程序还必须具有添加到项目中的**xamarin** . **AppCompat 和 v7.** NuGet 包。

本指南中的所有代码和 XML 都可在本指南的[SplashScreen](https://docs.microsoft.com/samples/xamarin/monodroid-samples/splashscreen)示例项目中找到。

## <a name="implementing-a-splash-screen"></a>实现初始屏幕

呈现和显示初始屏幕的最快捷方法是创建自定义主题，并将其应用于显示初始屏幕的活动。 呈现活动时，它将加载主题，并将可绘制资源（由主题引用）应用到活动的背景。 此方法避免了创建布局文件的需要。

初始屏幕实现为一个活动，该活动显示品牌可绘制、执行任何初始化并启动所有任务。 应用引导后，初始屏幕活动将启动主活动，并从应用程序后端堆栈中移除自身。

### <a name="creating-a-drawable-for-the-splash-screen"></a>创建初始屏幕的 "绘制"

初始屏幕将在初始屏幕活动的背景中显示 XML 可绘制的。 需要使用位图图像（如 PNG 或 JPG）来显示图像。

在本指南中，我们将使用一个[层列表](https://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList)在应用程序中居中显示初始屏幕图像。 以下代码片段是使用的`drawable` `layer-list`资源的示例：

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

这`layer-list`会使初始映像在`@color/splash_background`资源指定的背景上处于**png。** 将此 XML 文件放在**resources/** splash_screen 文件夹中（例如， **resources//.xml**）。

创建初始屏幕可绘制屏幕后，下一步是为初始屏幕创建主题。

### <a name="implementing-a-theme"></a>实现主题

若要为初始屏幕活动创建自定义主题，请编辑（或添加）文件**值/样式 .xml** ，并为初始屏幕`style`创建新元素。 下面显示了一个示例**值/样式 .xml**文件，其中包含`style`一个名为**MyTheme**的示例：

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

**MyTheme**是非常 spartan &ndash;的，它声明了窗口背景，从窗口中显式删除了标题栏，并声明它为全屏。 如果要创建在活动增加第一个布局之前模拟应用 UI 的初始屏幕，可以使用`windowContentOverlay` `windowBackground`而不是在样式定义中使用。 在这种情况下，还必须修改**splash_screen**可绘制，使其显示 UI 的仿真。

### <a name="create-a-splash-activity"></a>创建初始活动

现在，我们需要一个用于启动 Android 的新活动，该活动具有初始映像并执行任何启动任务。 下面的代码是完整的初始屏幕实现的示例：

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

`SplashActivity`显式使用在上一部分中创建的主题，替代应用程序的默认主题。
不需要在中`OnCreate`加载布局，因为主题将可绘制的作为背景声明。

必须设置`NoHistory=true`属性，以便从后端堆栈中删除活动。 若要阻止 "后退" 按钮取消启动进程，还可以重写`OnBackPressed` ，使其不执行任何操作：

```csharp
public override void OnBackPressed() { }
```

启动工作在中`OnResume`以异步方式执行。 这是必需的，以便启动工作不会减慢启动屏幕或延迟启动屏幕的外观。 工作完成后， `SplashActivity`将启动`MainActivity` ，并且用户可能开始与应用进行交互。

此新`SplashActivity`的设置为应用程序的启动程序活动，方法是`MainLauncher`将属性`true`设置为。 由于`SplashActivity`现在是启动器活动，因此必须编辑`MainActivity.cs`并从以下内容中`MainLauncher` `MainActivity`删除属性：

```csharp
[Activity(Label = "@string/ApplicationName")]
public class MainActivity : AppCompatActivity
{
    // Code omitted for brevity
}
```

## <a name="landscape-mode"></a>横向模式

在前面的步骤中实现的初始屏幕将在纵向模式和横向模式下正确显示。 但是，在某些情况下，需要为纵向模式和横向模式使用单独的初始屏幕（例如，如果初始映像为全屏）。

若要为横向模式添加初始屏幕，请使用以下步骤：

1. 在 "**资源/可绘制**的文件夹" 中，添加要使用的初始屏幕映像的横向版本。 在此示例中， **splash_logo_land**是上面示例中使用的徽标的横向版本（它使用的是白色的，而不是蓝色）。

2. 在**资源/可绘制**文件夹中，创建前面定义的可`layer-list`绘制的横向版本（例如， **splash_screen_land**）。 在此文件中，将位图路径设置为初始屏幕图像的横向版本。 在下面的示例中， **splash_screen_land**使用**splash_logo_land**：

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

3. 如果**资源/值**尚不存在，则创建它们。

4. 将文件**colors .xml**和**style .xml**添加到**值-土地**（可从现有的**值/颜色 .xml**和**值/样式 .xml**文件进行复制和修改）。

5. 修改**values-land/style .xml** ，使其使用的可绘制的`windowBackground`横向版本。 在此示例中，使用了**splash_screen_land** ：

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

6. 修改**values-land/colors**以配置要用于初始屏幕的横向版本的颜色。 在此示例中，对于横向模式，初始背景色更改为蓝色：

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

7. 重新生成并运行应用。 在仍显示初始屏幕时，将设备旋转到横向模式。 初始屏幕更改为横向版本：

    [![初始屏幕旋转到横向模式](splash-screen-images/landscape-splash-sml.png)](splash-screen-images/landscape-splash.png#lightbox)

请注意，使用横向模式初始屏幕并不总是提供无缝体验。 默认情况下，Android 在纵向模式下启动应用并将其转换为横向模式，即使设备已处于横向模式下也是如此。 因此，如果在设备处于横向模式时启动该应用，则设备会短暂显示纵向初始屏幕，然后在屏幕上将旋转从纵向变为横向初始屏幕。 遗憾的`ScreenOrientation = Android.Content.PM.ScreenOrientation.Landscape`是，即使在初始活动的标志中指定了，也会发生这种最初的纵向到横向转换。 解决此限制的最佳方式是创建一个在纵向和横向模式下均正确呈现的单一初始屏幕图像。

## <a name="summary"></a>总结

本指南讨论了在 Xamarin Android 应用程序中实现初始屏幕的一种方法：即，将自定义主题应用于启动活动。

## <a name="related-links"></a>相关链接

- [SplashScreen （示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/splashscreen)
- [层列表的绘制](https://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList)
- [材料设计模式-启动屏幕](https://material.io/design/communication/launch-screen.html#usage)
