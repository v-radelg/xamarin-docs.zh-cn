---
title: KitKat 功能
description: Android 4.4 (KitKat) 是加载了太多的用户和开发人员的功能。 本指南重点介绍了这些功能的几个，并提供代码示例和实现详细信息，以帮助您充分利用 KitKat。
ms.prod: xamarin
ms.assetid: D3FDEA1C-F076-406F-BCC3-2A55D2C6ADEE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 9572a08a2403bf13d74fc5fda7c62ec1fb2d1537
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67674694"
---
# <a name="kitkat-features"></a>KitKat 功能

_Android 4.4 (KitKat) 是加载了太多的用户和开发人员的功能。本指南重点介绍了这些功能的几个，并提供代码示例和实现详细信息，以帮助您充分利用 KitKat。_

## <a name="overview"></a>概述

Android 4.4 (API 级别 19)，也称为"KitKat"，已在后期 2013年发布。 KitKat 提供了多种新功能和改进，包括：

-  [用户体验](#user_experience)&ndash;与转换 framework、 半透明状态和导航栏和的全屏沉浸式模式下的简单动画帮助用户更好的体验。

-  [用户的内容](#user_content)&ndash;用户文件管理简化了存储访问框架; 打印图片、 网站和其他内容是改进了打印 Api，更容易。

-  [硬件](#hardware)&ndash;到使用 NFC 基于主机的卡仿真的 NFC 卡将所有应用; 运行与的低功率传感器`SensorManager`。

-  [开发人员工具](#developer_tools)&ndash;截屏视频中使用 Android Debug Bridge 客户端，可用的操作的应用程序作为 Android SDK 的一部分。


本指南提供有关现有 Xamarin.Android 应用程序迁移到 KitKat，以及 KitKat 的高级概述为 Xamarin.Android 开发人员指导。

## <a name="requirements"></a>要求

若要开发 Xamarin.Android 应用程序使用 KitKat，需要*Xamarin.Android 4.11.0*或更高版本和 Android 4.4 (API 级别 19) 安装通过 Android SDK 管理器中，如以下屏幕截图所示：

[![在 Android SDK 管理器中选择 Android 4.4](kitkat-images/api19.png)](kitkat-images/api19.png#lightbox)

<a name="Migrating_Your_App_to_KitKat" />

## <a name="migrating-your-app-to-kitkat"></a>将您的应用程序迁移到 KitKat

本部分提供了一些第一个响应项以帮助转换到 Android 4.4 的现有应用程序。

### <a name="check-system-version"></a>检查系统版本

如果应用程序需要能够与较旧版本的 Android 兼容，请务必将任何 KitKat 特定的代码包装在系统版本检查，如下面的代码示例所示：

```csharp
if (Build.VERSION.SdkInt >= BuildVersionCodes.Kitkat) {
    //KitKat only code here
}
```

### <a name="alarm-batching"></a>警报批处理

Android 使用警报服务在指定时间唤醒在后台中的应用。 KitKat 这一步是通过采用批处理警报以保留电源。 这意味着，而非唤醒的确切时间每个应用，KitKat 首选组注册的相同的时间间隔内唤醒并唤醒它们在同一时间的多个应用程序。
若要告知 Android 在指定的时间间隔内唤醒应用，调用`SetWindow`上[ `AlarmManager` ](https://developer.xamarin.com/api/type/Android.App.AlarmManager/)、 传入最小和最大时间 （毫秒） 之前唤醒应用程序, 可以等待，以及要执行的操作在唤醒。
下面的代码提供了需要唤醒半小时和一小时从窗口中设置的时间之间的应用程序的示例：

```csharp
AlarmManager alarmManager = (AlarmManager)GetSystemService(AlarmService);
alarmManager.SetWindow (AlarmType.Rtc, AlarmManager.IntervalHalfHour, AlarmManager.IntervalHour, pendingIntent);
```

若要继续在准确的时间唤醒应用，请使用`SetExact`、 传入应用程序应唤醒的确切时间和要执行的操作：

```csharp
alarmManager.SetExact (AlarmType.Rtc, AlarmManager.IntervalDay, pendingIntent);
```

KitKat 不再允许您设置确切的重复警报。 使用的应用程序 [`SetRepeating`](https://developer.xamarin.com/api/member/Android.App.AlarmManager.SetRepeating/p/Android.App.AlarmType/System.Int64/System.Int64/Android.App.PendingIntent/)
并且需要确切的警报，以便将现在需要手动触发每个警报。

### <a name="external-storage"></a>外部存储

外部存储现在分为两种类型的唯一的应用程序，并由多个应用程序共享的数据存储。 读取和写入外部存储的应用程序的特定位置需要任何特殊权限。 现在与共享存储上的数据进行交互需要`READ_EXTERNAL_STORAGE`或`WRITE_EXTERNAL_STORAGE`权限。 这种情况下可以造成两个类型进行分类：

-  如果在调用方法来获取文件或目录路径`Context`-例如， [`GetExternalFilesDir`](https://developer.xamarin.com/api/member/Android.Content.Context.GetExternalFilesDir/p/System.String/)
   或 [`GetExternalCacheDirs`](https://developer.xamarin.com/api/member/Android.Content.Context.GetExternalCacheDirs%28%29/)
   - 您的应用程序需要任何额外权限。

-  如果可将文件或目录路径的访问属性或调用方法上`Environment`，如 [`GetExternalStorageDirectory`](https://developer.xamarin.com/api/property/Android.OS.Environment.ExternalStorageDirectory/)
   或 [`GetExternalStoragePublicDirectory`](https://developer.xamarin.com/api/member/Android.OS.Environment.GetExternalStoragePublicDirectory/p/System.String/)
   你的应用需要`READ_EXTERNAL_STORAGE`或`WRITE_EXTERNAL_STORAGE`权限。

> [!NOTE]
> `WRITE_EXTERNAL_STORAGE` 意味着`READ_EXTERNAL_STORAGE`权限，因此只需将一个权限设置。

### <a name="sms-consolidation"></a>SMS 合并

KitKat 简化了通过聚合由用户选择一个默认应用程序中的所有 SMS 内容的用户消息传送。 开发人员负责使应用程序的默认消息传送应用程序，可选择并运行正常的代码中和生活中如果不选择该应用程序。 有关过渡到 KitKat SMS 应用程序的详细信息，请参阅[前您 SMS 应用程序的准备工作 KitKat](http://android-developers.blogspot.com/2013/10/getting-your-sms-apps-ready-for-kitkat.html)来自 Google 的指南。

### <a name="webview-apps"></a>WebView 应用程序

[WebView](https://developer.xamarin.com/api/type/Android.Webkit.WebView/) KitKat 而陷入功能改进。 最大的变化提高加载到内容的安全性`WebView`。 虽然大多数面向较旧的 API 版本的应用程序应按预期方式工作的测试应用程序使用的`WebView`强烈建议类。 有关受影响的 WebView Api 的详细信息，请参阅 android[迁移到 WebView 中 Android 4.4](https://developer.android.com/guide/webapps/migrating.html)文档。

<a name="user_experience" />

## <a name="user-experience"></a>用户体验

KitKat 附带了几个新的 Api 来增强用户体验，包括新的过渡框架，用于处理属性动画和主题的半透明 UI 选项。 下面介绍了这些更改。

### <a name="transition-framework"></a>转换 Framework

转换 framework 可以轻松地实现动画。 KitKat 允许您执行只需一行代码，使用一个简单的属性的动画或自定义使用的转换*场景*。

#### <a name="simple-property-animation"></a>简单的属性动画

新的 Android 转换库简化了代码隐藏属性动画。 该框架使您能够使用最少的代码的简单动画。 例如，下面的代码示例使用 [`TransitionManager.BeginDelayedTransition`](https://developer.xamarin.com/api/member/Android.Transitions.TransitionManager.BeginDelayedTransition/p/Android.Views.ViewGroup/Android.Transitions.Transition/)
若要进行动画处理显示和隐藏`TextView`:

```csharp
using Android.Transitions;

public class MainActivity : Activity
{
    LinearLayout linear;
    Button button;
    TextView text;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        SetContentView (Resource.Layout.Main);

        linear = FindViewById<LinearLayout> (Resource.Id.linearLayout);
        button = FindViewById<Button> (Resource.Id.button);
        text = FindViewById<TextView> (Resource.Id.textView);

        button.Click += (o, e) => {

            TransitionManager.BeginDelayedTransition (linear);

            if(text.Visibility != ViewStates.Visible)
            {
                text.Visibility = ViewStates.Visible;
            }
            else
            {
                text.Visibility = ViewStates.Invisible;
            }
        };
    }
}
```

上面的示例中使用转换框架来创建自动、 不断变化的属性值之间的默认转换。 因为动画处理通过一行代码，就可以轻松地这与较旧版本的 Android 兼容通过包装`BeginDelayedTransition`调用中系统版本检查。 请参阅[迁移你的应用程序到 KitKat](#Migrating_Your_App_to_KitKat)部分，了解详细信息。

下面的屏幕截图显示了动画之前应用：

[![应用在动画开始前的屏幕截图](kitkat-images/trans-before.png)](kitkat-images/trans-before.png#lightbox)

下面的屏幕截图显示了应用的动画结束后：

[![动画完成后，应用程序屏幕截图](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

您可以获得更好地控制转换时将在下一节中介绍的场景。

#### <a name="android-scenes"></a>Android 场景

[场景](https://developer.xamarin.com/api/type/Android.Transitions.Scene/)作为转换 framework 为开发人员提供更好地控制动画的一部分引入。 场景在 UI 中创建动态区域： 指定容器和多个版本中或为 XML 内容的容器中，"场景"和 Android 余下的场景之间的转换进行动画处理的工作。 Android 场景允许您开发端上生成具有最少的工作的复杂动画。

其中包含动态内容的静态用户界面元素称为*容器*或*场景基*。 下面的示例使用 Android 设计器来创建`RelativeLayout`调用`container`:

[![使用 Android 设计器创建 RelativeLayout 容器](kitkat-images/container.png)](kitkat-images/container.png#lightbox)

示例布局还定义了名为的按钮`sceneButton`如下`container`。 此按钮将触发转换。

在容器内的动态内容要求两个新的 Android 布局。 这些布局指定只将代码*内*容器。
下面的代码示例定义了一种称为布局*Scene1*分别包含两个文本字段读取"工具包"和"Kat"和第二个布局称为*Scene2*包含反转相同的文本字段。 XML 是按如下所示：

 **Scene1.axml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<merge xmlns:android="http://schemas.android.com/apk/res/android">
    <TextView
        android:id="@+id/textA"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Kit"
        android:textSize="35sp" />
    <TextView
        android:id="@+id/textB"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_toRightOf="@id/textA"
        android:text="Kat"
        android:textSize="35sp" />
</merge>
```

 **Scene2.axml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<merge xmlns:android="http://schemas.android.com/apk/res/android">
    <TextView
        android:id="@+id/textB"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Kat"
        android:textSize="35sp" />
    <TextView
        android:id="@+id/textA"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_toRightOf="@id/textB"
        android:text="Kit"
        android:textSize="35sp" />
</merge>
```

上面的示例使用`merge`以使视图代码更短和简化的视图层次结构。 可以阅读更多有关`merge`布局[此处](http://android-developers.blogspot.com/2009/03/android-layout-tricks-3-optimize-by.html)。

通过调用创建一个场景[ `Scene.GetSceneForLayout` ](https://developer.xamarin.com/api/member/Android.Transitions.Scene.GetSceneForLayout/p/Android.Views.ViewGroup/System.Int32/Android.Content.Context/)，并在容器对象中，资源 ID 场景的布局文件，以及当前传递`Context`，如下面的代码示例所示：

```csharp
RelativeLayout container = FindViewById<RelativeLayout> (Resource.Id.container);

Scene scene1 = Scene.GetSceneForLayout(container, Resource.Layout.Scene1, this);
Scene scene2 = Scene.GetSceneForLayout(container, Resource.Layout.Scene2, this);

scene1.Enter();
```

单击的按钮翻转 Android 进行动画处理的默认转换值在两个场景之间：

```csharp
sceneButton.Click += (o, e) => {
    Scene temp = scene2;
    scene2 = scene1;
    scene1 = temp;

    TransitionManager.Go (scene1);
};
```

下面的屏幕截图演示了之前动画场景：

[![应用在动画开始前的屏幕截图](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

下面的屏幕截图演示了动画结束后的场景：

[![在动画完成后应用的屏幕截图](kitkat-images/scene.png)](kitkat-images/scene.png#lightbox)


> [!NOTE]
> 没有[已知的 bug](https://code.google.com/p/android/issues/detail?id=62450)在 Android 转换会导致场景的库创建使用`GetSceneForLayout`用户完成某个动作的第二个时间导航时中断。 Java 解决方法描述[此处](http://www.doubleencore.com/2013/11/new-transitions-framework/)。


##### <a name="custom-transitions-in-scenes"></a>场景中的自定义转换

可以在 xml 资源文件中定义的自定义转换`transition`目录下`Resources`，如下面的屏幕截图所示：

[![Transition.xml 资源/转换目录下的文件的位置](kitkat-images/resources.png)](kitkat-images/resources.png#lightbox)

下面的代码示例定义一个转换的 5 秒之间进行动画处理，并使用[超过了想要内插器](https://developer.android.com/reference/android/views/animation/OvershootInterpolator.html):

```xml
<changeBounds
  xmlns:android="http://schemas.android.com/apk/res/android"
  android:duration="5000"
  android:interpolator="@android:anim/overshoot_interpolator" />
```

在活动中创建转换使用[TransitionInflater](https://developer.xamarin.com/api/type/Android.Transitions.TransitionInflater/)，如下面的代码所示：

```csharp
Transition transition = TransitionInflater.From(this).InflateTransition(Resource.Transition.transition);
```

新的转换，然后添加到`Go`开始动画的调用：

```csharp
TransitionManager.Go (scene1, transition);
```

### <a name="translucent-ui"></a>半透明 UI

KitKat 提供更好地控制主题通过使用可选的半透明状态和导航栏在应用程序。 您可以更改用于定义 Android 主题的同一个 XML 文件中的系统 UI 元素的透明度。 KitKat 引入了以下属性：

-  `windowTranslucentStatus` -设置为 true，使顶部状态栏半透明。

-  `windowTranslucentNavigation` -设置为 true，可在底部导航栏半透明。

-  `fitsSystemWindows` -设置为 transcluent 顶部或底部栏默认情况下移动下的透明的 UI 元素的内容。 此属性设置为`true`是使内容在重叠的半透明系统 UI 元素的简单方法。


下面的代码定义了与半透明状态和导航条主题：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <style name="KitKatTheme" parent="android:Theme.Holo.Light">
        <item name="android:windowBackground">@color/xamgray</item>
        <item name="android:windowTranslucentStatus">true</item>
        <item name="android:windowTranslucentNavigation">true</item>
        <item name="android:fitsSystemWindows">true</item>
        <item name="android:actionBarStyle">@style/ActionBar.Solid.KitKat</item>
    </style>

    <style name="ActionBar.Solid.KitKat" parent="@android:style/Widget.Holo.Light.ActionBar.Solid">
        <item name="android:background">@color/xampurple</item>
    </style>
</resources>
```

下面的屏幕截图显示了与半透明状态和导航栏上方主题：

[![使用半透明状态和导航条应用程序的示例屏幕截图](kitkat-images/theme.png)](kitkat-images/theme.png#lightbox)

<a name="user_content" />

## <a name="user-content"></a>用户的内容

### <a name="storage-access-framework"></a>存储访问框架

存储访问框架 (SAF) 是用户与存储的内容，例如图像、 视频和文档交互的新方法。 而不是向用户显示一个对话框，以选择一个应用程序来处理内容，KitKat 打开新的用户界面，使用户能够访问其数据存储在一个聚合的位置。 选择内容后用户将返回到请求内容的应用程序，并将继续正常进行的应用体验。

此更改需要开发人员端上的两个操作： 首先，需要从提供程序的内容的应用程序需要更新为新请求内容的方式。 第二个，将数据写入到的应用程序`ContentProvider`需要进行修改以使用新的框架。 这两种方案都依赖于新 [`DocumentsProvider`](https://developer.xamarin.com/api/type/Android.Provider.DocumentsProvider/)
API。

#### <a name="documentsprovider"></a>DocumentsProvider

在 KitKat，与交互`ContentProviders`抽象化与`DocumentsProvider`类。 这意味着，SAF 并不关心数据的实际，只要它是可通过访问`DocumentsProvider`API。 本地提供程序，云服务和外部存储设备所有使用该接口，并将被视为相同的方式，用户和开发人员提供一个位置与用户的内容进行交互。

本部分介绍如何加载和保存与存储访问框架的内容。

#### <a name="request-content-from-a-provider"></a>从提供程序的请求内容

我们可以告诉 KitKat 我们想要选择使用 SAF UI 内容`ActionOpenDocument`意向，表示我们想要连接到与该设备所有内容提供商。 您可以添加一些筛选到此目的通过指定`CategoryOpenable`，将返回这意味着可以打开 （即可访问性，可使用内容） 的唯一内容。 KitKat 还允许筛选的内容与`MimeType`。 例如，以下由指定的图像的图像结果的筛选器代码`MimeType`:

```csharp
Intent intent = new Intent (Intent.ActionOpenDocument);
intent.AddCategory (Intent.CategoryOpenable);
intent.SetType ("image/*");
StartActivityForResult (intent, save_request_code);
```

调用`StartActivityForResult`启动 SAF UI，用户可以浏览以选择一个映像：

[![浏览到映像中使用存储访问框架的应用的屏幕截图示例](kitkat-images/saf-ui.png)](kitkat-images/saf-ui.png#lightbox)

用户已选择一个映像之后,`OnActivityResult`返回`Android.Net.Uri`的所选的文件。 下面的代码示例显示用户的图像选择：

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    if (resultCode == Result.Ok && data != null && requestCode == save_request_code) {
        imageView = FindViewById<ImageView> (Resource.Id.imageView);
        imageView.SetImageURI (data.Data);
    }
}
```

#### <a name="write-content-to-a-provider"></a>将内容写到提供程序

除了从 SAF UI 加载内容，KitKat 还允许您将内容保存到任何`ContentProvider`实现`DocumentProvider`API。 正在保存内容使用`Intent`与`ActionCreateDocument`:

```csharp
Intent intentCreate = new Intent (Intent.ActionCreateDocument);
intentCreate.AddCategory (Intent.CategoryOpenable);
intentCreate.SetType ("text/plain");
intentCreate.PutExtra (Intent.ExtraTitle, "NewDoc");
StartActivityForResult (intentCreate, write_request_code);
```

上面的代码示例加载 SAF UI，让用户更改文件名称，然后选择一个目录来容纳新文件：

[![更改的文件的名称为 NewDoc 下载目录中的用户的屏幕截图](kitkat-images/saf-save.png)](kitkat-images/saf-save.png#lightbox)

当用户按**保存**，`OnActivityResult`传递`Android.Net.Uri`的新创建的文件，可以通过访问`data.Data`。 Uri 可以用于数据流式传输到新的文件：

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    if (resultCode == Result.Ok && data != null && requestCode == write_request_code) {
        using (Stream stream = ContentResolver.OpenOutputStream(data.Data)) {
            Encoding u8 = Encoding.UTF8;
            string content = "Hello, world!";
            stream.Write (u8.GetBytes(content), 0, content.Length);
        }
    }
}
```

请注意， [`ContentResolver.OpenOutputStream(Android.Net.Uri)`](https://developer.xamarin.com/api/member/Android.Content.ContentResolver.OpenOutputStream/(Android.Net.Uri))
返回`System.IO.Stream`，因此可以用.NET 编写整个流式处理过程。

有关加载的详细信息，创建和编辑内容与存储访问框架，请参阅[Android 文档存储访问框架](https://developer.android.com/guide/topics/providers/document-provider.html)。

### <a name="printing"></a>打印

打印内容通过引入简化 KitKat[打印服务](https://developer.xamarin.com/api/namespace/Android.PrintServices/)和`PrintManager`。 KitKat 也是能够充分利用的第一个 API 版本[Google 云打印服务 Api](https://developers.google.com/cloud-print/)使用[Google 云打印应用程序](https://play.google.com/store/apps/details?id=com.google.android.apps.cloudprint)。
大多数设备会自动随 KitKat 下载 Google 云打印应用程序和[HP 打印服务插件](https://play.google.com/store/apps/details?id=com.hp.android.printservice)当他们首次连接到 WiFi。 用户可以通过导航到检查其自己的设备的打印设置**设置 > 系统 > 打印**:

[![打印设置屏幕的屏幕截图示例](kitkat-images/printing.png)](kitkat-images/printing.png#lightbox)


> [!NOTE]
> 虽然打印 Api 设置为使用 Google 云打印默认情况下，Android 仍允许开发人员准备打印内容使用新的 Api，并将其发送到其他应用程序可以处理打印。



#### <a name="printing-html-content"></a>打印 HTML 内容

会自动创建 KitKat [ `PrintDocumentAdapter` ](https://developer.xamarin.com/api/type/Android.Print.PrintDocumentAdapter/)的 web 视图与`WebView.CreatePrintDocumentAdapter`。 打印 web 内容是之间协调的工作量[ `WebViewClient` ](https://developer.xamarin.com/api/type/Android.Webkit.WebViewClient/) ，等待要加载的 HTML 内容，可让活动知道选项菜单中提供打印选项和等待到用户的活动选择打印选项并调用`Print`上`PrintManager`。 本部分介绍了屏幕上打印所需的基本设置 HTML 内容。

请注意，加载和打印的 web 内容需要 Internet 权限：

[![在应用程序选项中设置 Internet 权限](kitkat-images/internet.png)](kitkat-images/internet.png#lightbox)

##### <a name="print-menu-item"></a>打印菜单项

打印选项通常会在中显示的活动[选项菜单](https://developer.android.com/guide/topics/ui/menus.html#options-menu)。
选项菜单可让用户在活动上执行操作。 它是在屏幕的右上角，如下所示：

[![打印菜单项显示在屏幕的右上角的屏幕截图示例](kitkat-images/menu.png)](kitkat-images/menu.png#lightbox)


可以在中定义的附加菜单项*菜单*目录下*资源*。 下面的代码定义了一个示例菜单项调用[打印](https://developer.xamarin.com/api/type/Android.Print.PrintManager/):

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@+id/menu_print"
        android:title="Print"
        android:showAsAction="never" />
</menu>
```

与在活动中的选项菜单的交互是通过`OnCreateOptionsMenu`和`OnOptionsItemSelected`方法。
`OnCreateOptionsMenu` 可以添加新菜单项，如打印选项，从*菜单*resources 目录中。
`OnOptionsItemSelected` 侦听用户从菜单中，选择打印选项，并开始打印：

```csharp
bool dataLoaded;

public override bool OnCreateOptionsMenu (IMenu menu)
{
    base.OnCreateOptionsMenu (menu);
    if (dataLoaded) {
        MenuInflater.Inflate (Resource.Menu.print, menu);
    }
    return true;
}

public override bool OnOptionsItemSelected (IMenuItem item)
{
    if (item.ItemId == Resource.Id.menu_print) {
        PrintPage ();
        return true;
    }
    return base.OnOptionsItemSelected (item);
}
```

上面的代码还定义一个变量，名为`dataLoaded`来跟踪的 HTML 内容的状态。 `WebViewClient`将设置此变量为 true 时的所有内容已都加载，让活动知道要将打印菜单项添加到选项菜单。

##### <a name="webviewclient"></a>WebViewClient

作业`WebViewClient`是为了确保中的数据`WebView`是完全加载之前的打印选项出现在菜单中，这一点与`OnPageFinished`方法。 `OnPageFinished` 侦听的 web 内容，以完成加载，并告知要重新创建具有其选项菜单的活动`InvalidateOptionsMenu`:

```csharp
class MyWebViewClient : WebViewClient
{
    PrintHtmlActivity caller;

    public MyWebViewClient (PrintHtmlActivity caller)
    {
        this.caller = caller;
    }

    public override void OnPageFinished (WebView view, string url)
    {
        caller.dataLoaded = true;
        caller.InvalidateOptionsMenu ();
    }
}
```

`OnPageFinished` 此外设置`dataLoaded`值设为`true`，因此`OnCreateOptionsMenu`可以重新创建使用位置中的打印选项的菜单。

##### <a name="printmanager"></a>PrintManager

下面的代码示例打印的内容`WebView`:

```csharp
void PrintPage ()
{
    PrintManager printManager = (PrintManager)GetSystemService (Context.PrintService);
    PrintDocumentAdapter printDocumentAdapter = myWebView.CreatePrintDocumentAdapter ();
    printManager.Print ("MyWebPage", printDocumentAdapter, null);
}
```

`Print` 将作为参数: ("MyWebPage"在此示例中)，打印作业的名称 [`PrintDocumentAdapter`](https://developer.xamarin.com/api/type/Android.Print.PrintDocumentAdapter/)
它生成打印文档的内容和 [`PrintAttributes`](https://developer.xamarin.com/api/type/Android.Print.PrintAttributes/)
(`null`在上面的示例)。 您可以指定`PrintAttributes`以帮助打印页面上的内容的布局，但默认属性应处理大多数情况。

调用`Print`加载打印 UI，其中列出了打印作业的选项。 用户界面，用户可以选择打印或 HTML 内容保存为 pdf 格式，如下面的屏幕截图所示：

[![显示打印菜单 PrintHtmlActivity 的屏幕截图](kitkat-images/print1.png)](kitkat-images/print1.png#lightbox)

[![显示保存为 PDF 菜单 PrintHtmlActivity 的屏幕截图](kitkat-images/print2.png)](kitkat-images/print2.png#lightbox)

<a name="hardware" />

## <a name="hardware"></a>硬件

KitKat 将添加几个 Api，以适应新的设备功能。 最值得注意的是基于主机的卡仿真和新`SensorManager`。

### <a name="host-based-card-emulation-in-nfc"></a>基于主机的卡 NFC 模拟机制

基于主机的卡仿真 (HCE) 允许应用程序而不依赖于运营商的专有安全元素的行为类似于 NFC 卡或 NFC 卡读卡器。 设置 HCE 之前, 确保 HCE 可在设备上使用`PackageManager.HasSystemFeature`:

```csharp
bool hceSupport = PackageManager.HasSystemFeature(PackageManager.FeatureNfcHostCardEmulation);
```

HCE 需要这两个 HCE 功能和`Nfc`应用程序的注册权限`AndroidManifest.xml`:

```xml
<uses-feature android:name="android.hardware.nfc.hce" />
```

[![在应用程序选项中设置 NFC 权限](kitkat-images/nfc.png)](kitkat-images/nfc.png#lightbox)

若要处理，HCE 具有能够在后台运行并具有启动时用户所做的 NFC 事务，即使使用 HCE 的应用程序未运行。 我们可以完成此操作通过编写 HCE 代码作为`Service`。 HCE 服务实现`HostApduService`接口，实现以下方法：

-  *ProcessCommandApdu* -应用程序协议数据单元 (APDU) 是什么获取 NFC 读取器和 HCE 服务之间发送。 此方法使用 ADPU 从读取器，并在响应中返回数据单元。

-  *OnDeactivated* - `HostAdpuService` HCE 服务无法再使用 NFC 读取器通信时停用。


HCE 服务还需要注册应用程序清单，并使用适当的权限，意向筛选器和元数据修饰。 下面的代码是一种`HostApduService`注册的 Android 清单使用`Service`属性 (有关属性的详细信息，请参阅 Xamarin[使用 Android 清单](~/android/platform/android-manifest.md)指南):

```csharp
[Service(Exported=true, Permission="android.permissions.BIND_NFC_SERVICE"),
    IntentFilter(new[] {"android.nfc.cardemulation.HOST_APDU_SERVICE"}),
    MetaData("android.nfc.cardemulation.host.apdu_service",
    Resource="@xml/hceservice")]

class HceService : HostApduService
{
    public override byte[] ProcessCommandApdu(byte[] apdu, Bundle extras)
    {
        ...
    }

    public override void OnDeactivated (DeactivationReason reason)
    {
        ...
    }
}
```

上述服务，可以为要与该应用程序进行交互的 NFC 读取器但 NFC 读取器仍有没有办法知道如果此服务模拟它需要扫描的 NFC 卡。 为了帮助标识该服务的 NFC 读取器，我们可以将分配该服务的唯一*应用程序 ID (AID)* 。 我们指定的辅助手段，以及有关 HCE 服务的其他元数据的 xml 资源文件中注册`MetaData`属性 （请参阅上面的代码示例）。 此资源文件指定一个或多个辅助设备筛选器-以十六进制格式的唯一标识符字符串对应于一个或多个 NFC 读取器设备的辅助工具：

```xml
<host-apdu-service xmlns:android="http://schemas.android.com/apk/res/android"
    android:description="@string/hce_service_description"
    android:requireDeviceUnlock="false"
    android:apduServiceBanner="@drawable/service_banner">
    <aid-group android:description="@string/aid_group_description"
                android:category="payment">
        <aid-filter android:name="1111111111111111"/>
        <aid-filter android:name="0123456789012345"/>
    </aid-group>
</host-apdu-service>
```

除了帮助筛选器，xml 资源文件还提供 HCE 服务的面向用户的说明指定的辅助设备组 （而不是"其他"付款应用程序） 和对于付款应用程序，以向用户显示一个 260 x 96 dp 横幅。

上面所述安装程序提供了模拟的 NFC 卡的应用程序的基本构建基块。 NFC 本身需要几个详细步骤和进一步测试配置。 有关基于主机的卡仿真的详细信息，请参阅[Android 文档门户](https://developer.android.com/guide/topics/connectivity/nfc/hce.html)。
有关结合使用 NFC 和 Xamarin 的详细信息，请参阅[Xamarin NFC 示例](https://github.com/xamarin/monodroid-samples/tree/master/NfcSample)。

### <a name="sensors"></a>传感器

KitKat 人员能够通过设备的传感器[ `SensorManager` ](https://developer.xamarin.com/api/type/Android.Hardware.SensorManager/)。
`SensorManager` ，操作系统就可以计划传递传感器信息再到批处理中的应用程序保留的电池使用寿命。

KitKat 还附带了两个新的传感器类型用于跟踪用户的步骤。 这些基于加速感应器，包括：

-  *StepDetector* -应用程序通知/唤醒时用户采取的步骤，并检测程序步骤发生时提供一个时间值。

-  *StepCounter* -跟踪的传感器已注册用户后执行的步骤数*直到下一步的设备重启*。

下面的屏幕截图描绘了操作中的步计数器：

[![显示步骤计数器 SensorsActivity 应用的屏幕截图](kitkat-images/stepcounter.png)](kitkat-images/stepcounter.png#lightbox)

您可以创建`SensorManager`通过调用`GetSystemService(SensorService)`并将结果作为转换`SensorManager`。 若要使用的步骤计数器，请调用`GetDefaultSensor`上`SensorManager`。 你可以注册传感器并侦听步骤计数的帮助中的更改 [`ISensorEventListener`](https://developer.xamarin.com/api/type/Android.Hardware.ISensorEventListener/)
接口，如下面的代码示例所示：

```csharp
public class MainActivity : Activity, ISensorEventListener
{
    float count = 0;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        SetContentView (Resource.Layout.Main);

        SensorManager senMgr = (SensorManager) GetSystemService (SensorService);
        Sensor counter = senMgr.GetDefaultSensor (SensorType.StepCounter);
        if (counter != null) {
            senMgr.RegisterListener(this, counter, SensorDelay.Normal);
        }
    }

    public void OnAccuracyChanged (Sensor sensor, SensorStatus accuracy)
    {
        Log.Info ("SensorManager", "Sensor accuracy changed");
    }

    public void OnSensorChanged (SensorEvent e)
    {
        count = e.Values [0];
    }
}
```

`OnSensorChanged` 如果步骤计数更新该应用程序在前台时进行调用。 如果应用程序进入后台，或在设备处于睡眠状态时，`OnSensorChanged`不会调用; 但是，步骤将继续进行计数直到`UnregisterListener`调用。

请记住*在注册传感器的所有应用程序中，步骤计数值是累计的*。 这意味着，即使在卸载和重新安装应用程序，并初始化`count`变量 0 处开始应用程序启动时，传感器报告的值将保持的步骤时注册传感器，使总数是否由你应用程序或另一个。 可以阻止应用程序将添加到步骤计数器通过调用`UnregisterListener`上`SensorManager`，如下面的代码所示：

```csharp
protected override void OnPause()
{
    base.OnPause ();
    senMgr.UnregisterListener(this);
}
```

重启设备步骤计数重置为 0。 您的应用程序将需要额外的代码，以确保它报告的应用程序，而不考虑其他应用程序使用传感器或设备的状态的准确计数。


> [!NOTE]
> 步骤检测和计数与 KitKat 一起提供的 API，而不是所有手机都配备传感器。 您可以检查传感器是否可通过运行`PackageManager.HasSystemFeature(PackageManager.FeatureSensorStepCounter);`，或检查，以确保返回的值`GetDefaultSensor`不是`null`。


<a name="developer_tools" />

## <a name="developer-tools"></a>开发人员工具

### <a name="screen-recording"></a>屏幕录制

KitKat 包括新屏幕，以便开发人员可记录应用程序中操作录制功能。 屏幕录制已可通过[Android Debug Bridge (ADB)](https://developer.android.com/tools/help/adb.html)客户端，可以下载 Android SDK 的一部分。

若要录制你的屏幕，将设备连接;然后，找到你的 Android SDK 安装中，导航到**平台工具**目录，然后运行**adb**客户端：

```shell
adb shell screenrecord /sdcard/screencast.mp4
```

上述命令将记录 4Mbps 默认分辨率的默认值 3 分钟视频。 若要编辑的长度，请添加 *-时间限制*标志。
若要更改分辨率，添加 *-比特率*标志。 以下命令将记录在 8Mbps 长时间的分钟视频：

```shell
adb shell screenrecord --bit-rate 8000000 --time-limit 60 /sdcard/screencast.mp4
```

可以在设备上查找你的视频-它会在您的库中录制完成后。


## <a name="other-kitkat-additions"></a>其他 KitKat 新增功能：

除了上面所述的更改，KitKat，您可以：

-  *使用全屏*-KitKat 引入了一个新[沉浸式模式下](https://developer.android.com/reference/android/view/View.html#setSystemUiVisibility(int))浏览内容、 玩游戏，并运行其他应用程序可受益于全屏体验。

-  *自定义通知*-获取有关使用系统通知的其他详细信息 [`NotificationListenerService`](https://developer.xamarin.com/api/type/Android.Service.Notification.NotificationListenerService/)
   . 这允许您在您的应用程序内的不同方式显示信息。

-  *镜像可绘制资源*-可绘制资源有一个新 [`autoMirrored`](https://developer.android.com/reference/android/R.attr.html#autoMirrored)
   通知系统的属性创建需要从左到右布局翻转的图像的镜像的版本。

-  *暂停动画*-暂停和恢复使用创建的动画 [`Animator`](https://developer.xamarin.com/api/type/Android.Animation.Animator/)
   类的新实例。

-  *读取动态更改的文本*-表示动态使用新的文本更新为与新的"实时区域"的用户界面部分 [`accessibilityLiveRegion`](https://developer.android.com/reference/android/R.attr.html#accessibilityLiveRegion)
   因此在可访问性模式下会自动读取新的文本属性。

-  *增强音频体验*-请噪音跟踪与 [`LoudnessEnhancer`](https://developer.xamarin.com/api/type/Android.Media.Audiofx.LoudnessEnhancer/)
   找到的峰值数目和 RMS 的音频流 [`Visualizer`](https://developer.xamarin.com/api/field/Android.Media.Audiofx.Visualizer.MeasurementModePeakRms/)
   类，并获取信息从[音频时间戳](https://developer.xamarin.com/api/type/Android.Media.AudioTimestamp/)以帮助进行音频视频同步。

-  *在自定义间隔同步 ContentResolver* -KitKat 增加一些可变性执行同步请求的时间。 同步`ContentResolver`在自定义时或通过调用间隔`ContentResolver.RequestSync`并传入`SyncRequest`。

-  *控制器之间区分*-在 KitKat 控制器分配可通过设备的访问的唯一整数标识符`ControllerNumber`属性。 这使得更轻松地告诉相隔玩家在游戏中。

-  *远程控制*-使用的硬件和软件端上的一些更改，KitKat 允许您打开到远程控制使用配备红外线 （ir） 发射器设备`ConsumerIrService`，并与新的外围设备与之交互 [`RemoteController`](https://developer.xamarin.com/api/type/Android.Media.RemoteController/)
   Api。

有关上述 API 更改的详细信息，请参阅 Google [Android 4.4 Api](https://developer.android.com/about/versions/android-4.4.html)概述。


## <a name="summary"></a>总结

本文介绍了一些 Android 4.4 (API 级别 19) 中提供的新 Api，并转换到 KitKat 的应用程序时涉及的最佳做法。 It 体验空心的更改会影响用户的 Api，包括*过渡框架*用于和新选项*主题*。 接下来，它引入*存储访问框架*并`DocumentsProvider`类，以及新*打印 Api*。 它探讨*NFC 基于主机的卡仿真*，以及如何使用*低功率传感器*，包括两个新的传感器，用于跟踪用户的步骤。 最后，它演示了捕获的应用程序与实时演示*屏幕录制*，并提供 KitKat API 更改和新增功能的详细的列表。


## <a name="related-links"></a>相关链接

- [KitKat 示例](https://developer.xamarin.com/samples/monodroid/KitKat/)
- [Android 4.4 Api](https://developer.android.com/about/versions/android-4.4.html)
- [Android KitKat](https://developer.android.com/about/versions/kitkat.html)
