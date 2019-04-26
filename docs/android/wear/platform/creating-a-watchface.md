---
title: 创建适用于 Android 穿戴设备 1.0 手表表盘
description: 本指南介绍如何实现自定义的观察人脸服务针对 Android Wear 1.0。 用于构建压下数字监视人脸服务后, 跟更多代码以创建模拟样式表盘提供分步说明。
ms.prod: xamarin
ms.assetid: 4D3F9A40-A820-458D-A12A-D784BB11F643
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/23/2018
ms.openlocfilehash: 067a39838fbfe3f1b33ac0d30b5069366b11e407
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61287017"
---
# <a name="creating-a-watch-face"></a>创建表盘

_本指南介绍如何实现自定义的观察人脸服务针对 Android Wear 1.0。用于构建压下数字监视人脸服务后, 跟更多代码以创建模拟样式表盘提供分步说明。_

## <a name="overview"></a>概述

在此演练中，创建一个基本的监视人脸服务来演示创建自定义的 Android Wear 1.0 手表表盘的基础知识。
初始监视的人脸服务显示以小时和分钟显示的当前时间的简单数字监视：

[![数字手表表盘](creating-a-watchface-images/01-initial-face.png "的初始数字手表表盘的示例屏幕截图")](creating-a-watchface-images/01-initial-face.png#lightbox)

此数字手表表盘的开发和测试后，将其升级到更复杂的三个手与模拟手表表盘添加更多的代码：

[![模拟手表表盘](creating-a-watchface-images/02-example-watchface.png "最终模拟手表表盘的屏幕截图示例")](creating-a-watchface-images/02-example-watchface.png#lightbox)

观看人脸服务是捆绑在一起且作为 Wear 1.0 应用程序的一部分安装。 在以下示例中，`MainActivity`包含没有什么比 Wear 1.0 应用程序模板中的代码，以便监视人脸服务可以打包并部署到智能手表应用的一部分。 实际上，此应用将充当纯粹获取监视人脸服务加载到 Wear 1.0 设备 （或仿真器） 的工具进行调试和测试。

## <a name="requirements"></a>要求

若要实现监视人脸服务，需要以下各项：

-   Android 5.0 （API 级别 21） 或更高版本在穿戴设备或仿真程序上。

-   [Xamarin Android Wear 支持库](https://www.nuget.org/packages/Xamarin.Android.Wear)必须添加到 Xamarin.Android 项目。

虽然 Android 5.0 是最低 API 级别用于实现监视的人脸服务，Android 5.1 或更高版本建议。 Android Wear 运行 Android 5.1 (API 22) 的设备或更高版本允许穿戴设备应用来控制显示的内容在屏幕上时在设备处于低功耗*环境*模式。 当设备离开低功耗*环境*模式时，它是在*交互式*模式。 有关这些模式的详细信息，请参阅[使您的应用程序可见](https://developer.android.com/training/wearables/apps/always-on.html)。


## <a name="start-an-app-project"></a>启动应用程序项目

创建一个名为的新的 Android Wear 1.0 项目**WatchFace** (有关创建新的 Xamarin.Android 项目的详细信息，请参阅[Hello，Android](~/android/get-started/hello-android/hello-android-quickstart.md)):

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![新建项目对话框](creating-a-watchface-images/03-wear-project-vs-sml.png "新项目对话框中选择 Wear 应用")](creating-a-watchface-images/03-wear-project-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![新建项目对话框](creating-a-watchface-images/03-wear-project-xs-sml.png "新项目对话框中选择 Wear 应用")](creating-a-watchface-images/03-wear-project-xs.png#lightbox)

-----


将包名称设置为`com.xamarin.watchface`:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![包名称设置](creating-a-watchface-images/04-package-name-vs.png "包名称设置为 com.xamarin.watchface")](creating-a-watchface-images/04-package-name-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![包名称设置](creating-a-watchface-images/04-package-name-xs.png "包名称设置为 com.xamarin.watchface")](creating-a-watchface-images/04-package-name-xs.png#lightbox)

-----

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

此外，向下滚动并启用**INTERNET**并**WAKE_LOCK**权限：

[![所需的权限](creating-a-watchface-images/05-required-permissions-vs.png "启用 INTERNET 和 WAKE_LOCK 权限")](creating-a-watchface-images/05-required-permissions-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

最低 Android 版本设置为**Android 5.1 （API 级别 22）**。
此外，启用**Internet**并**WakeLock**权限：

[![所需的权限](creating-a-watchface-images/05-required-permissions-xs.png "启用 Internet 和 WakeLock 权限")](creating-a-watchface-images/05-required-permissions-xs.png#lightbox)

-----

接下来，下载[preview.png](creating-a-watchface-images/preview.png) &ndash;这将添加到**绘图**稍后在本演练的文件夹。


## <a name="add-the-xamarinandroid-wear-package"></a>添加 Xamarin.Android 穿戴设备包

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

启动 NuGet 包管理器 (在 Visual Studio 中，右键单击**引用**中**解决方案资源管理器**，然后选择**管理 NuGet 包...**).更新到最新稳定版本的项目**Xamarin.Android.Wear**:

[![添加 NuGet 包管理器](creating-a-watchface-images/06-add-wear-pkg-vs-sml.png "添加 Xamarin.Android.Wear 包")](creating-a-watchface-images/06-add-wear-pkg-vs.png#lightbox)

接下来，如果**Xamarin.Android.Support.v13**是安装，将其卸载：

[![NuGet 包管理器删除](creating-a-watchface-images/07-uninstall-v13-sml.png "删除 Xamarin.Support.v13")](creating-a-watchface-images/07-uninstall-v13.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

启动 NuGet 包管理器 (在 Visual Studio for Mac 中，右键单击**包**中**解决方案窗格**，然后选择**添加包...**).更新到最新稳定版本的项目**Xamarin.Android.Wear**:

[![添加 NuGet 包管理器](creating-a-watchface-images/06-add-wear-pkg-xs-sml.png "添加 Xamarin.Android.Wear 包")](creating-a-watchface-images/06-add-wear-pkg-xs.png#lightbox)

-----


生成并在穿戴设备或仿真器上运行应用 (有关如何执行此操作的详细信息，请参阅[Getting Started](~/android/wear/get-started/index.md)指南)。 在穿戴设备上看到以下应用屏幕：

[![应用程序的屏幕截图](creating-a-watchface-images/08-app-screen.png "穿戴设备上的应用屏幕")](creating-a-watchface-images/08-app-screen.png#lightbox)

此时，基本 Wear 应用不具有监视人脸功能，因为尚未提供监视人脸服务实现。 接下来将添加此服务。


## <a name="canvaswatchfaceservice"></a>CanvasWatchFaceService

Android 穿戴设备实现观看通过人脸`CanvasWatchFaceService`类。 `CanvasWatchFaceService` 派生自`WatchFaceService`，该类本身派生自`WallpaperService`以下关系图中所示：

[![继承关系图](creating-a-watchface-images/09-inheritance-diagram-sml.png "CanvasWatchFaceService 继承关系图")](creating-a-watchface-images/09-inheritance-diagram.png#lightbox)

`CanvasWatchFaceService` 包括嵌套`CanvasWatchFaceService.Engine`; 它实例化`CanvasWatchFaceService.Engine`执行绘制手表表盘的实际工作的对象。 `CanvasWatchFaceService.Engine` 派生自`WallpaperService.Engine`上图中所示。

不在此图中所示是`Canvas`该`CanvasWatchFaceService`用于绘制手表表盘&ndash;这`Canvas`通过传入`OnDraw`方法如下所述。

在以下部分中，将通过执行以下步骤创建自定义的观察人脸服务：

1.  定义一个名为类`MyWatchFaceService`派生自`CanvasWatchFaceService`。

2.  内`MyWatchFaceService`，创建一个名为的嵌套的类`MyWatchFaceEngine`派生自`CanvasWatchFaceService.Engine`。

3.  在中`MyWatchFaceService`，实现`CreateEngine`方法实例化`MyWatchFaceEngine`并将其返回。

4.  在中`MyWatchFaceEngine`，实现`OnCreate`方法创建的监视人脸样式并执行任何其他初始化任务。

5.  实现`OnDraw`方法的`MyWatchFaceEngine`。 每当手表表盘需要重绘时调用此方法 (即*失效*)。 `OnDraw` 为绘制 （和重绘） 监视人脸元素，如小时、 分钟和第二个指针的方法。

6.  实现`OnTimeTick`方法的`MyWatchFaceEngine`。
    `OnTimeTick` 每分钟 （在环境和交互模式） 或日期/时间已更改时至少一次调用。

有关详细信息`CanvasWatchFaceService`，请参阅 Android [CanvasWatchFaceService](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.html) API 文档。
同样， [CanvasWatchFaceService.Engine](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.Engine.html)介绍手表表盘的实际实现。


### <a name="add-the-canvaswatchfaceservice"></a>添加 CanvasWatchFaceService

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

添加名为的新文件**MyWatchFaceService.cs** (在 Visual Studio 中，右键单击**WatchFace**中**解决方案资源管理器**，单击**添加 > 新建项...**，然后选择**类**)。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

添加名为的新文件**MyWatchFaceService.cs** (在 Visual Studio for Mac 中，右键单击**WatchFace**项目，请单击**添加 > 新建文件...**，然后选择**的空类**)。

-----

此文件的内容替换为以下代码：

```csharp
using System;
using Android.Views;
using Android.Support.Wearable.Watchface;
using Android.Service.Wallpaper;
using Android.Graphics;

namespace WatchFace
{
    class MyWatchFaceService : CanvasWatchFaceService
    {
        public override WallpaperService.Engine OnCreateEngine()
        {
            return new MyWatchFaceEngine(this);
        }

        public class MyWatchFaceEngine : CanvasWatchFaceService.Engine
        {
            CanvasWatchFaceService owner;
            public MyWatchFaceEngine (CanvasWatchFaceService owner) : base(owner)
            {
                this.owner = owner;
            }
        }
    }
}
```

`MyWatchFaceService` (派生自`CanvasWatchFaceService`) 是手表表盘"主计划"。 `MyWatchFaceService` 实现只有一个方法， `OnCreateEngine`，其实例化并返回`MyWatchFaceEngine`对象 (`MyWatchFaceEngine`派生自`CanvasWatchFaceService.Engine`)。 实例化`MyWatchFaceEngine`对象必须作为返回`WallpaperService.Engine`。 封装`MyWatchFaceService`对象传递到构造函数。

`MyWatchFaceEngine` 是实际观察人脸实现&ndash;它包含绘制手表表盘的代码。 它还处理系统事件，例如屏幕更改 （环境/交互模式中，屏幕关闭，等等。）。


### <a name="implement-the-engine-oncreate-method"></a>实现引擎 OnCreate 方法

`OnCreate`方法初始化手表表盘。 以下字段添加到`MyWatchFaceEngine`:

```csharp
Paint hoursPaint;
```

这`Paint`对象将用于绘制 watch 表盘上的当前时间。 接下来，添加以下方法`MyWatchFaceEngine`:

```csharp
public override void OnCreate(ISurfaceHolder holder)
{
    base.OnCreate (holder);

    SetWatchFaceStyle (new WatchFaceStyle.Builder(owner)
        .SetCardPeekMode (WatchFaceStyle.PeekModeShort)
        .SetBackgroundVisibility (WatchFaceStyle.BackgroundVisibilityInterruptive)
        .SetShowSystemUiTime (false)
        .Build ());

    hoursPaint = new Paint();
    hoursPaint.Color = Color.White;
    hoursPaint.TextSize = 48f;
}
```

`OnCreate` 不久后调用`MyWatchFaceEngine`已启动。 设置了`WatchFaceStyle`（它可以控制在穿戴设备与用户交互的方式），并实例化`Paint`将用于显示时间的对象。

对调用`SetWatchFaceStyle`执行以下操作：

1.  集*peek 模式*到`PeekModeShort`，这将导致通知，以显示为小的"速览"卡上显示。

2.  将背景可见性设置为`Interruptive`，这会导致背景的查看卡以显示只是暂时是否它表示中断通知。

3.  禁用默认系统 UI 时间从手表表盘上进行绘制，以使自定义手表表盘可以改为显示时间。

有关这些和其他监视人脸样式选项的详细信息，请参阅 Android [WatchFaceStyle.Builder](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceStyle.Builder.html) API 文档。

之后`SetWatchFaceStyle`完成后，`OnCreate`实例化`Paint`对象 (`hoursPaint`)，并将其颜色设置为白色，其文本大小以适合 48 像素 ([TextSize](https://developer.android.com/reference/android/graphics/Paint.html#setTextSize%28float%29)必须指定以像素为单位)。


### <a name="implement-the-engine-ondraw-method"></a>实现引擎 OnDraw 方法

`OnDraw`方法可能是最重要`CanvasWatchFaceService.Engine`方法&ndash;是，实际绘制观看人脸元素，如数字和时钟人脸指针的方法。
在以下示例中，watch 表盘上绘制的时间字符串。
添加以下方法`MyWatchFaceEngine`:

```csharp
public override void OnDraw (Canvas canvas, Rect frame)
{
    var str = DateTime.Now.ToString ("h:mm tt");
    canvas.DrawText (str,
        (float)(frame.Left + 70),
        (float)(frame.Top  + 80), hoursPaint);
}
```

当调用 Android `OnDraw`，它将传入`Canvas`实例，并可以在其中绘制将人脸的边界。 在上面的代码示例中，`DateTime`用于计算的当前时间以小时和分钟数 （采用 12 小时格式）。 生成的时间字符串在画布上绘制使用`Canvas.DrawText`方法。 字符串将显示 70 像素通过从左边的缘和 80 像素下的上边缘。

有关详细信息`OnDraw`方法，请参阅 Android [onDraw](https://developer.android.com/reference/android/support/wearable/watchface/CanvasWatchFaceService.Engine#ondraw) API 文档。


### <a name="implement-the-engine-ontimetick-method"></a>实现引擎 OnTimeTick 方法

Android 定期调用`OnTimeTick`方法来更新通过手表表盘所显示的时间。 它在至少一次，每分钟 （在环境和交互模式下），或已更改的日期/时间或时区时调用。 添加以下方法`MyWatchFaceEngine`:

```csharp
public override void OnTimeTick()
{
    Invalidate();
}
```

此实现`OnTimeTick`只需调用`Invalidate`。 `Invalidate`方法计划`OnDraw`重绘手表表盘。

有关详细信息`OnTimeTick`方法，请参阅 Android [onTimeTick](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onTimeTick()) API 文档。


## <a name="register-the-canvaswatchfaceservice"></a>注册 CanvasWatchFaceService

`MyWatchFaceService` 必须在中注册**AndroidManifest.xml**关联穿戴设备应用程序。 若要执行此操作，添加以下 XML 到`<application>`部分：

```xml
<service
    android:name="watchface.MyWatchFaceService"
    android:label="Xamarin Sample"
    android:allowEmbedded="true"
    android:taskAffinity=""
    android:permission="android.permission.BIND_WALLPAPER">
    <meta-data
        android:name="android.service.wallpaper"
        android:resource="@xml/watch_face" />
    <meta-data
        android:name="com.google.android.wearable.watchface.preview"
        android:resource="@drawable/preview" />
    <intent-filter>
        <action android:name="android.service.wallpaper.WallpaperService" />
        <category android:name="com.google.android.wearable.watchface.category.WATCH_FACE" />
    </intent-filter>
</service>
```

此 XML 将执行以下操作：

1.  集`android.permission.BIND_WALLPAPER`权限。 此权限使更改在设备上的系统墙纸的监视人脸服务权限。 请注意，此权限必须设置在`<service>`部分，而不是在外部`<application>`部分。

2.  定义`watch_face`资源。 此资源是声明一个简短的 XML 文件`wallpaper`资源 （在下一节中将创建此文件）。

3.  声明名为 drawable 映像`preview`，将显示的监视选取器选择屏幕。

4.  包括`intent-filter`让 Android 知道`MyWatchFaceService`将显示手表表盘。

完成为基本代码`WatchFace`示例。 下一步是添加所需的资源。


## <a name="add-resource-files"></a>添加资源文件

您可以运行监视服务之前，必须添加**watch_face**资源和预览图像。 首先，创建新的 XML 文件在**Resources/xml/watch_face.xml**并将其内容替换为以下 XML:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<wallpaper xmlns:android="http://schemas.android.com/apk/res/android" />
```

将此文件的生成操作设置为**AndroidResource**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![生成操作](creating-a-watchface-images/10-android-resource-vs.png "组生成操作为 AndroidResource")](creating-a-watchface-images/10-android-resource-vs.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![生成操作](creating-a-watchface-images/10-android-resource-xs.png "组生成操作为 AndroidResource")](creating-a-watchface-images/10-android-resource-xs.png#lightbox)

-----

此资源文件定义了一个简单`wallpaper`用于手表表盘的元素。

如果尚未这样做，下载[preview.png](creating-a-watchface-images/preview.png)。
安装在**Resources/drawable/preview.png**。 请确保添加到此文件`WatchFace`项目。 在穿戴设备上观看人脸选取器中向用户显示此预览图像。 若要创建你自己的手表表盘的预览图像，可以在运行时执行手表表盘的屏幕截图。 (有关获取从穿戴设备的设备的屏幕截图的详细信息，请参阅[拍摄屏幕快照](~/android/wear/deploy-test/debug-on-device.md#screenshots))。


## <a name="try-it"></a>试试看 ！

生成并部署到在穿戴设备的应用程序。 您应该可以看到 Wear 应用屏幕像以前一样显示。 执行以下操作来启用新的手表表盘：

1.  轻扫到右侧，直到您看到监视屏幕的背景。

2.  触摸并在屏幕的背景上任意位置保存两秒钟。

3.  轻扫，从左到右，可以浏览各种表盘。

4.  选择**Xamarin 示例**观看 （显示在右侧） 的人脸：

    [![选取器 Watchface](creating-a-watchface-images/11-watchface-picker.png "轻扫来查找 Xamarin 示例手表表盘")](creating-a-watchface-images/11-watchface-picker.png#lightbox)

5.  点击**Xamarin 示例**表盘以将其选中。

这会更改要使用自定义的观察人脸服务实现到目前为止在穿戴设备表盘：

[![数字手表表盘](creating-a-watchface-images/12-digital-watchface.png "穿戴设备上运行的自定义数字监视")](creating-a-watchface-images/12-digital-watchface.png#lightbox)

这是因为应用程序实现是因此最小种相对简陋手表表盘 (例如，它不包括监视人脸背景，它不会调用`Paint`抗锯齿方法以改善外观)。
但是，它实现所需创建自定义手表表盘的基本功能。

在下一步部分中，此手表表盘将升级到更复杂的实现。


## <a name="upgrading-the-watch-face"></a>升级手表表盘

在本演练的其余部分`MyWatchFaceService`升级以显示模拟样式表盘，它可进行扩展以支持更多的功能。 将添加以下功能，若要创建已升级的手表表盘：

1.  指示以模拟小时、 分钟和第二个手的时间。

2.  在可见性的更改作出反应。

3.  对环境的模式和交互模式之间的更改做出响应。

4.  读取基础穿戴设备的属性。

5.  自动更新所在的时区更改发生的时间。

实现下面的代码更改之前, 下载[drawable.zip](https://github.com/xamarin/monodroid-samples/blob/master/wear/WatchFace/Resources/drawable.zip?raw=true)，将其解压缩，并移动到已解压缩的.png 文件**资源/drawable** (覆盖前一**preview.png**). 添加到新的.png 文件`WatchFace`项目。


### <a name="update-engine-features"></a>更新引擎功能

下一步是升级**MyWatchFaceService.cs**到绘制模拟表盘和支持新功能的实现。 内容替换为**MyWatchFaceService.cs**中的监视人脸代码的模拟版本[MyWatchFaceService.cs](https://github.com/xamarin/monodroid-samples/blob/master/wear/WatchFace/WatchFace/MyWatchFaceService.cs) (可以剪切并粘贴此源的现有**MyWatchFaceService.cs**)。

此版本的**MyWatchFaceService.cs**将更多的代码添加到现有方法并包括其他重写的方法来添加更多的功能。 以下部分提供的源代码的指导的教程。

#### <a name="oncreate"></a>OnCreate

已更新**OnCreate**方法配置监视人脸样式与之前一样，但它还包括一些附加步骤：

1.  将背景图像设置为**xamarin_background**驻留在资源**Resources/drawable-hdpi/xamarin_background.png**。

2.  初始化`Paint`绘制小时手、 分针和第二个指针的对象。

3.  初始化`Paint`对象，用于绘制手表表盘的边缘周围小时计时周期数。

4.  该调用将创建一个计时器`Invalidate`（重绘） 方法，以便第二个指针都将重绘每隔一秒。 请注意，此计时器是必要的因为`OnTimeTick`调用`Invalidate`仅一次每隔一分钟。

此示例包含一个**xamarin_background.png**映像; 但是，你可能想要创建你的自定义手表表盘将支持每个屏幕密度的不同的背景图像。

#### <a name="ondraw"></a>OnDraw

已更新**OnDraw**方法绘制模拟样式表盘使用以下步骤：

1.  获取当前时间，它现在保存在`time`对象。

2.  确定的绘图图面和其中心的边界。

3.  绘制背景，缩放以适应设备绘制背景。

4.  绘制 12*计时周期*围绕时钟 （对应于时钟表面上的小时数） 的人脸。

5.  计算角度、 旋转和每个监视指针的长度。

6.  监视的表面上绘制每个指针。 请注意是否所监视的环境模式不绘制第二个指针。


#### <a name="onpropertieschanged"></a>OnPropertiesChanged

调用此方法以通知`MyWatchFaceEngine`穿戴设备 （如低位环境模式和刻录中保护） 的属性。 在`MyWatchFaceEngine`，此方法只检查的低位环境模式 （在低位环境模式下，屏幕支持较少的位用于每种颜色）。

有关此方法的详细信息，请参阅 Android [onPropertiesChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onPropertiesChanged%28android.os.Bundle%29) API 文档。


#### <a name="onambientmodechanged"></a>OnAmbientModeChanged

在穿戴设备进入或退出环境模式时，调用此方法。 在`MyWatchFaceEngine`实现中，在环境的模式下时手表表盘禁用抗锯齿。

有关此方法的详细信息，请参阅 Android [onAmbientModeChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onAmbientModeChanged%28boolean%29) API 文档。


#### <a name="onvisibilitychanged"></a>OnVisibilityChanged

调用此方法是每当监视变得可见还是隐藏。 在`MyWatchFaceEngine`，此方法注册/注销的时区接收方 （如下所述） 根据的可见性状态。

有关此方法的详细信息，请参阅 Android [onVisibilityChanged](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html#onVisibilityChanged%28boolean%29) API 文档。


### <a name="time-zone-feature"></a>时区功能

新**MyWatchFaceService.cs**还包括功能更新时区更改 （例如在旅行跨时区） 的当前时间。 结尾附近**MyWatchFaceService.cs**，区域更改的时间`BroadcastReceiver`定义用于处理时区更改意向对象：

```csharp
public class TimeZoneReceiver: BroadcastReceiver
{
    public Action<Intent> Receive { get; set; }
    public override void OnReceive (Context context, Intent intent)
    {
        if (Receive != null)
            Receive (intent);
    }
}
```

`RegisterTimezoneReceiver`并`UnregisterTimezoneReceiver`会调用方法`OnVisibilityChanged`方法。
`UnregisterTimezoneReceiver` 调用时手表表盘的可见性状态更改为隐藏。 当再次可见时手表表盘`RegisterTimezoneReceiver`调用 (请参阅`OnVisibilityChanged`方法)。

引擎`RegisterTimezoneReceiver`方法将一个处理程序声明此时区接收器`Receive`事件; 此处理程序更新`time`对象所跨时区的新时间：

```csharp
timeZoneReceiver = new TimeZoneReceiver ();
timeZoneReceiver.Receive = (intent) => {
    time.Clear (intent.GetStringExtra ("time-zone"));
    time.SetToNow ();
};
```

创建并注册为时区接收方意向筛选器：

```csharp
IntentFilter filter = new IntentFilter(Intent.ActionTimezoneChanged);
Application.Context.RegisterReceiver (timeZoneReceiver, filter);
```

`UnregisterTimezoneReceiver`方法注销时区接收方：

```csharp
Application.Context.UnregisterReceiver (timeZoneReceiver);
```

### <a name="run-the-improved-watch-face"></a>运行改进的手表表盘

生成并再次将应用部署到在穿戴设备。 从监视人脸选取器作为之前选择手表表盘。 在监视选取器中的预览将显示在左侧，并在右侧显示新的手表表盘：

[![模拟手表表盘](creating-a-watchface-images/13-analog-watchface.png "改进了在选取器和设备上的模拟人脸")](creating-a-watchface-images/13-analog-watchface.png#lightbox)

在此屏幕截图，第二个指针每秒一次移动。 在穿戴设备上运行此代码时，第二个指针将消失时监视输入环境的模式。


## <a name="summary"></a>总结

在此演练中，自定义 Android Wear 1.0 watchface 是实现和测试。 `CanvasWatchFaceService`和`CanvasWatchFaceService.Engine`引入了类，并实现引擎类的基本方法来创建简单的数字表盘。 此实现了更新，采用更多的功能，以创建模拟的表盘，和其他方法在实现以处理更改可见性、 环境模式和设备属性之间的差异。 最后，时区广播的接收器已实现，以便监视会自动更新何时跨越一个时区的时间。


## <a name="related-links"></a>相关链接

- [创建表盘](https://developer.android.com/training/wearables/watch-faces/index.html)
- [WatchFace 示例](https://developer.xamarin.com/samples/monodroid/wear/WatchFace)
- [WatchFaceService.Engine](https://developer.android.com/reference/android/support/wearable/watchface/WatchFaceService.Engine.html)
