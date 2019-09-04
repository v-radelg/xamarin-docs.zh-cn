---
title: KitKat 功能
description: Android 4.4 (KitKat) 为用户和开发人员提供了 cornucopia 功能。 本指南重点介绍其中一些功能, 并提供代码示例和实现详细信息, 以帮助充分利用 KitKat。
ms.prod: xamarin
ms.assetid: D3FDEA1C-F076-406F-BCC3-2A55D2C6ADEE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 1aa4e6dcf5137d12647fb2a5531218839b6db9a1
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2019
ms.locfileid: "70225798"
---
# <a name="kitkat-features"></a>KitKat 功能

_Android 4.4 (KitKat) 为用户和开发人员提供了 cornucopia 功能。本指南重点介绍其中一些功能, 并提供代码示例和实现详细信息, 以帮助充分利用 KitKat。_

## <a name="overview"></a>概述

Android 4.4 (API 级别 19) (也称为 "KitKat") 在后期2013发布。 KitKat 提供了各种新的功能和改进, 包括:

- [用户体验](#user_experience)&ndash;使用转换框架、半透明状态和导航栏以及全屏沉浸模式的简单动画有助于为用户带来更好的体验。

- [用户内容](#user_content)&ndash;使用存储访问框架简化了用户文件管理; 打印图片、网站和其他内容更易于打印 api。

- [硬件](#hardware)使用基于 nfc 主机的卡仿真将任何应用转换为 nfc 卡; `SensorManager`使用运行低功耗传感器。 &ndash;

- [开发人员工具](#developer_tools)&ndash; Android Debug Bridge 客户端使用的 Screencast 应用程序, 可作为 Android SDK 的一部分。


本指南提供有关将现有 Xamarin Android 应用程序迁移到 KitKat 的指导, 以及针对 Xamarin 开发人员的 KitKat 的高级概述。

## <a name="requirements"></a>要求

若要使用 KitKat 开发 Xamarin Android 应用程序, 需要通过 Android SDK Manager 安装*xamarin 4.11.0*或更高版本的 android 4.4 (API 级别 19), 如以下屏幕截图所示:

[![在 Android SDK 管理器中选择 Android 4。4](kitkat-images/api19.png)](kitkat-images/api19.png#lightbox)

<a name="Migrating_Your_App_to_KitKat" />

## <a name="migrating-your-app-to-kitkat"></a>将应用迁移到 KitKat

本部分提供了一些第一项响应项, 可帮助将现有应用程序转换为 Android 4.4。

### <a name="check-system-version"></a>检查系统版本

如果应用程序需要与较旧版本的 Android 兼容, 请确保在系统版本检查中包装任何 KitKat 特定的代码, 如下面的代码示例所示:

```csharp
if (Build.VERSION.SdkInt >= BuildVersionCodes.Kitkat) {
    //KitKat only code here
}
```

### <a name="alarm-batching"></a>警报批处理

Android 使用警报服务在指定时间在后台唤醒应用。 KitKat 通过批处理警报来更进一步地执行此操作, 以保持电源。 这意味着, KitKat 倾向于在同一时间间隔内对注册为唤醒的多个应用程序进行分组, 而不是在精确的时间内对每个应用进行分组, 并同时唤醒它们。
若要告知 Android 在指定时间间隔内唤醒应用, 请在`SetWindow` [`AlarmManager`](xref:Android.App.AlarmManager)上调用, 并传入应用程序唤醒之前可以经过的最小和最大时间 (以毫秒为单位) 以及要在唤醒时执行的操作。
下面的代码提供了一个应用程序示例, 该应用程序需要在每半小时到一小时的时间从设置窗口的时间唤醒:

```csharp
AlarmManager alarmManager = (AlarmManager)GetSystemService(AlarmService);
alarmManager.SetWindow (AlarmType.Rtc, AlarmManager.IntervalHalfHour, AlarmManager.IntervalHour, pendingIntent);
```

若要在精确的时间继续唤醒应用, 请`SetExact`使用, 并在应用应唤醒的确切时间内进行传递, 并执行以下操作:

```csharp
alarmManager.SetExact (AlarmType.Rtc, AlarmManager.IntervalDay, pendingIntent);
```

KitKat 不再允许您设置精确的重复警报。 使用的应用程序[`SetRepeating`](xref:Android.App.AlarmManager.SetRepeating*)
并且需要精确的警报, 现在需要手动触发每个警报。

### <a name="external-storage"></a>外部存储

外部存储现在划分为两种类型-应用程序的唯一存储空间, 以及由多个应用程序共享的数据。 在外部存储上读取和写入应用的特定位置不需要特殊权限。 与共享存储上的数据交互现在需要`READ_EXTERNAL_STORAGE`或`WRITE_EXTERNAL_STORAGE`权限。 这两种类型可以分类为:

- 如果是通过调用方法`Context`获取文件或目录路径, 例如:[`GetExternalFilesDir`](xref:Android.Content.Context.GetExternalFilesDir*)
  或[`GetExternalCacheDirs`](xref:Android.Content.Context.GetExternalCacheDirs)
  - 您的应用程序不需要额外的权限。

- 如果是通过访问属性或对`Environment`调用方法来获取文件或目录路径, 例如[`GetExternalStorageDirectory`](xref:Android.OS.Environment.ExternalStorageDirectory)
  或[`GetExternalStoragePublicDirectory`](xref:Android.OS.Environment.GetExternalStoragePublicDirectory*)
  , 应用需要`READ_EXTERNAL_STORAGE`或`WRITE_EXTERNAL_STORAGE`权限。

> [!NOTE]
> `WRITE_EXTERNAL_STORAGE``READ_EXTERNAL_STORAGE`表示权限, 因此只需设置一个权限。

### <a name="sms-consolidation"></a>SMS 合并

KitKat 通过聚合用户选择的一个默认应用程序中的所有 SMS 内容来简化用户的消息传递。 开发人员负责将应用程序设置为默认的消息传递应用程序, 并在代码中以及在应用程序未选中的情况下正常工作。 有关将 SMS 应用转换为 KitKat 的详细信息, 请参阅从 Google[获取 Sms 应用准备好 KitKat](http://android-developers.blogspot.com/2013/10/getting-your-sms-apps-ready-for-kitkat.html)指南。

### <a name="webview-apps"></a>Web 视图应用

[Web 视图](xref:Android.Webkit.WebView)在 KitKat 中获取了功能改进。 最大的变化增加了将内容加载到中`WebView`的安全性。 尽管面向较早的`WebView` API 版本的大多数应用程序应该按预期方式工作, 但强烈建议测试使用类的应用程序。 有关受影响的 Web 视图 Api 的详细信息, 请参阅[android 4.4 文档中的 Android 迁移到 Web 视图](https://developer.android.com/guide/webapps/migrating.html)。

<a name="user_experience" />

## <a name="user-experience"></a>用户体验

KitKat 附带了几个新的 Api 来增强用户体验, 其中包括用于处理属性动画的新转换框架和主题的半透明 UI 选项。 下面介绍了这些更改。

### <a name="transition-framework"></a>转换框架

转换框架使动画更易于实现。 使用 KitKat 可以只使用一行代码执行简单的属性动画, 或使用*场景*自定义转换。

#### <a name="simple-property-animation"></a>简单属性动画

新的 Android 转换库简化了属性动画的隐藏代码。 利用框架, 可以通过最少的代码执行简单动画。 例如, 下面的代码示例使用[`TransitionManager.BeginDelayedTransition`](xref:Android.Transitions.TransitionManager.BeginDelayedTransition*)
若要动态显示和隐藏`TextView`:

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

上面的示例使用转换框架在更改属性值之间创建自动的默认过渡。 由于动画是通过一行代码来处理的, 因此可以通过将`BeginDelayedTransition`调用包装在系统版本检查中, 轻松使此与早期版本的 Android 兼容。 有关详细信息, 请参阅将[应用迁移到 KitKat](#Migrating_Your_App_to_KitKat)部分。

下面的屏幕截图显示了在动画之前的应用程序:

[![动画开始之前的应用屏幕截图](kitkat-images/trans-before.png)](kitkat-images/trans-before.png#lightbox)

下面的屏幕截图显示了动画后面的应用程序:

[![动画完成后的应用屏幕截图](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

在下一节中介绍了如何更好地控制后台转换。

#### <a name="android-scenes"></a>Android 场景

[场景](xref:Android.Transitions.Scene)作为转换框架的一部分引入, 使开发人员可以更好地控制动画。 场景在 UI 中创建动态区域: 为容器中的 XML 内容指定容器和多个版本或 "场景", Android 会执行其余工作以对场景之间的转换进行动画处理。 Android 场景允许您使用最小的开发端来构建复杂的动画。

承载动态内容的静态 UI 元素称为*容器*或*场景基*。 下面的示例使用 Android Designer 来创建一个名`RelativeLayout` `container`为的:

[![使用 Android Designer 创建 RelativeLayout 容器](kitkat-images/container.png)](kitkat-images/container.png#lightbox)

示例布局还定义了下面的一个`sceneButton`名为`container`的按钮。 此按钮将触发转换。

容器内的动态内容需要两个新的 Android 布局。 这些布局仅指定容器*中*的代码。
下面的示例代码定义了一个名为*Scene1*的布局, 其中分别包含两个文本字段 "工具包" 和 "katt", 另一个名为*Scene2*的布局包含反转的相同文本字段。 XML 如下所示:

 **Scene1. main.axml**:

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

 **Scene2. main.axml**:

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

上面的示例使用`merge`来缩短视图代码, 并简化视图层次结构。 可在[此处](http://android-developers.blogspot.com/2009/03/android-layout-tricks-3-optimize-by.html)阅读有关`merge`布局的详细信息。

场景是通过调用[`Scene.GetSceneForLayout`](xref:Android.Transitions.Scene.GetSceneForLayout*)、传入容器对象、场景布局文件的资源 ID 和当前`Context`的来创建的, 如下面的代码示例所示:

```csharp
RelativeLayout container = FindViewById<RelativeLayout> (Resource.Id.container);

Scene scene1 = Scene.GetSceneForLayout(container, Resource.Layout.Scene1, this);
Scene scene2 = Scene.GetSceneForLayout(container, Resource.Layout.Scene2, this);

scene1.Enter();
```

单击该按钮将在两个场景之间切换, Android 会以默认过渡值进行动画处理:

```csharp
sceneButton.Click += (o, e) => {
    Scene temp = scene2;
    scene2 = scene1;
    scene1 = temp;

    TransitionManager.Go (scene1);
};
```

下面的屏幕截图说明了动画前面的场景:

[![动画启动之前的应用程序屏幕截图](kitkat-images/trans-after.png)](kitkat-images/trans-after.png#lightbox)

下面的屏幕截图演示动画后面的场景:

[![动画完成后应用的屏幕截图](kitkat-images/scene.png)](kitkat-images/scene.png#lightbox)


> [!NOTE]
> Android 转换库中有一个[已知 bug](https://code.google.com/p/android/issues/detail?id=62450) , 该 bug 导致使用`GetSceneForLayout`创建的场景在用户第二次导航活动时中断。 [此处](http://www.doubleencore.com/2013/11/new-transitions-framework/)介绍了 java 解决方法。


##### <a name="custom-transitions-in-scenes"></a>场景中的自定义转换

自定义转换可以在下`transition` `Resources`目录的 xml 资源文件中定义, 如以下屏幕截图所示:

[![资源/转换目录下转换 .xml 文件的位置](kitkat-images/resources.png)](kitkat-images/resources.png#lightbox)

下面的代码示例定义一个转换, 该转换将在5秒内进行动画处理, 并使用[超过内插](https://developer.android.com/reference/android/views/animation/OvershootInterpolator.html)程序:

```xml
<changeBounds
  xmlns:android="http://schemas.android.com/apk/res/android"
  android:duration="5000"
  android:interpolator="@android:anim/overshoot_interpolator" />
```

在活动中使用[TransitionInflater](xref:Android.Transitions.TransitionInflater)创建转换, 如以下代码所示:

```csharp
Transition transition = TransitionInflater.From(this).InflateTransition(Resource.Transition.transition);
```

然后, 将新的转换添加到`Go`开始动画的调用:

```csharp
TransitionManager.Go (scene1, transition);
```

### <a name="translucent-ui"></a>半透明 UI

KitKat 使你可以更好地控制具有可选半透明状态和导航栏的应用的主题。 你可以在用于定义 Android 主题的同一 XML 文件中更改系统 UI 元素的半透明度。 KitKat 引入了以下属性:

- `windowTranslucentStatus`-如果设置为 true, 则使顶部状态栏成为半透明的。

- `windowTranslucentNavigation`-设置为 true 时, 使底部导航栏半透明。

- `fitsSystemWindows`-默认情况下, 将顶部或底部栏设置为 "transcluent" 可在透明 UI 元素下移动内容。 如果将此属性`true`设置为, 则可以使用一种简单的方法来防止内容与半透明系统 UI 元素重叠。


下面的代码定义了一个带有半透明状态和导航栏的主题:

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

下面的屏幕截图显示了与半透明状态和导航栏有关的主题:

[![带有半透明状态和导航栏的应用的示例屏幕截图](kitkat-images/theme.png)](kitkat-images/theme.png#lightbox)

<a name="user_content" />

## <a name="user-content"></a>用户内容

### <a name="storage-access-framework"></a>存储访问框架

存储访问框架 (SAF) 是一种新的方法, 使用户能够与存储的内容 (例如图像、视频和文档) 进行交互。 KitKat 将打开一个新的 UI, 使用户能够在一个聚合位置访问其数据, 而不是向用户显示用于选择应用程序来处理内容的对话框。 选择内容后, 用户将返回到请求内容的应用程序, 并且应用程序体验会照常继续。

此更改需要对开发人员执行两个操作: 首先, 需要将提供商提供的内容的应用更新为请求内容的新方法。 其次, 将数据写入到`ContentProvider`需要修改以使用新框架的应用程序。 这两种方案都依赖于新的[`DocumentsProvider`](xref:Android.Provider.DocumentsProvider)
API。

#### <a name="documentsprovider"></a>DocumentsProvider

在 KitKat 中, 与`ContentProviders` `DocumentsProvider`类进行了抽象。 这意味着, 只要可通过`DocumentsProvider` API 访问数据, SAF 就不会关心数据的物理位置。 本地提供程序、云服务和外部存储设备均使用相同的接口, 并以相同的方式进行处理, 为用户和开发人员提供一个位置以便与用户的内容交互。

本部分介绍如何使用存储访问框架加载和保存内容。

#### <a name="request-content-from-a-provider"></a>从提供程序请求内容

我们可以告诉 KitKat, 我们想要使用 SAF UI 通过`ActionOpenDocument`意向选取内容, 这表示我们想要连接到设备可用的所有内容提供商。 您可以通过指定`CategoryOpenable`向此目的添加一些筛选, 这意味着只会返回可以打开的内容 (即可访问的内容)。 KitKat 还允许通过`MimeType`筛选内容。 例如, 以下代码通过指定图像`MimeType`来筛选图像结果:

```csharp
Intent intent = new Intent (Intent.ActionOpenDocument);
intent.AddCategory (Intent.CategoryOpenable);
intent.SetType ("image/*");
StartActivityForResult (intent, save_request_code);
```

调用`StartActivityForResult`会启动 SAF UI, 用户可以通过浏览该 UI 来选择图像:

[![使用存储访问框架浏览映像的应用的示例屏幕截图](kitkat-images/saf-ui.png)](kitkat-images/saf-ui.png#lightbox)

用户选择了一个图像后, `OnActivityResult`将`Android.Net.Uri`返回所选文件的。 下面的代码示例显示用户的图像选择:

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

#### <a name="write-content-to-a-provider"></a>将内容写入提供程序

除了从 SAF UI 加载内容外, KitKat 还允许你将内容保存到任何`ContentProvider` `DocumentProvider`实现 API 的。 保存内容将`Intent`与一起`ActionCreateDocument`使用:

```csharp
Intent intentCreate = new Intent (Intent.ActionCreateDocument);
intentCreate.AddCategory (Intent.CategoryOpenable);
intentCreate.SetType ("text/plain");
intentCreate.PutExtra (Intent.ExtraTitle, "NewDoc");
StartActivityForResult (intentCreate, write_request_code);
```

上面的代码示例加载 SAF UI, 让用户更改文件名, 并选择一个目录来容纳新文件:

[![用户将文件名更改为下载目录中的 NewDoc 的屏幕截图](kitkat-images/saf-save.png)](kitkat-images/saf-save.png#lightbox)

当用户按 "**保存**" `OnActivityResult`时, 将`Android.Net.Uri`传递新创建的文件的, 该文件可通过`data.Data`访问。 Uri 可用于将数据流式传输到新文件中:

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

请注意,[`ContentResolver.OpenOutputStream(Android.Net.Uri)`](xref:Android.Content.ContentResolver.OpenOutputStream*)
`System.IO.Stream`返回, 因此可使用 .net 编写整个流式处理过程。

有关使用存储访问框架加载、创建和编辑内容的详细信息, 请参阅[存储访问框架的 Android 文档](https://developer.android.com/guide/topics/providers/document-provider.html)。

### <a name="printing"></a>打印

随着[打印服务](xref:Android.PrintServices)和的引入, `PrintManager`KitKat 中的打印内容已简化。 KitKat 也是第一个 API 版本, 使用[Google Cloud 打印应用程序](https://play.google.com/store/apps/details?id=com.google.android.apps.cloudprint)完全利用[Google 的云打印服务 api](https://developers.google.com/cloud-print/) 。
当 KitKat 附带的大多数设备首次连接到 WiFi 时, 会自动下载 Google Cloud 打印应用和[HP 打印服务插件](https://play.google.com/store/apps/details?id=com.hp.android.printservice)。 用户可以通过导航到 "**设置" > 系统 > 打印**来检查其设备的打印设置:

[![打印设置屏幕的示例屏幕截图](kitkat-images/printing.png)](kitkat-images/printing.png#lightbox)


> [!NOTE]
> 尽管打印 Api 设置为在默认情况下使用 Google Cloud 打印, 但 Android 仍允许开发人员使用新 Api 准备打印内容, 并将其发送给其他应用程序以处理打印。



#### <a name="printing-html-content"></a>打印 HTML 内容

KitKat 自动为 web [`PrintDocumentAdapter`](xref:Android.Print.PrintDocumentAdapter) `WebView.CreatePrintDocumentAdapter`视图创建一个。 打印 web 内容是在等待 HTML 内容加载[`WebViewClient`](xref:Android.Webkit.WebViewClient)并使活动知道在 "选项" 菜单中提供 "打印" 选项的情况下协调的一项工作, 并使活动知道在 "选项" 菜单中提供 "打印" 选项, 并使活动等待用户选择 "打印" 选项并调用`Print`在`PrintManager`上。 本部分介绍了打印屏幕 HTML 内容所需的基本设置。

请注意, 加载和打印 web 内容需要 Internet 权限:

[![在应用选项中设置 Internet 权限](kitkat-images/internet.png)](kitkat-images/internet.png#lightbox)

##### <a name="print-menu-item"></a>打印菜单项

"打印" 选项通常会出现在活动的 "[选项" 菜单](https://developer.android.com/guide/topics/ui/menus.html#options-menu)中。
"选项" 菜单允许用户对活动执行操作。 它位于屏幕的右上角, 如下所示:

[![屏幕右上角显示的 "打印" 菜单项的示例屏幕截图](kitkat-images/menu.png)](kitkat-images/menu.png#lightbox)


可以在 "*资源*" 下的*菜单*目录中定义其他菜单项。 下面的代码定义了一个名为[Print](xref:Android.Print.PrintManager)的示例菜单项:

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@+id/menu_print"
        android:title="Print"
        android:showAsAction="never" />
</menu>
```

与活动中的 "选项" 菜单的交互会`OnCreateOptionsMenu`通过`OnOptionsItemSelected`和方法进行。
`OnCreateOptionsMenu`是在*菜单*资源目录中添加新菜单项 (例如 "打印" 选项) 的位置。
`OnOptionsItemSelected`侦听用户从菜单中选择 "打印" 选项, 然后开始打印:

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

上面的代码还定义了一个名`dataLoaded`为的变量, 用于跟踪 HTML 内容的状态。 加载所有内容时,会将此变量设置为true,以便活动知道将"打印"菜单项添加到"选项"菜单。`WebViewClient`

##### <a name="webviewclient"></a>WebViewClient

的工作`WebViewClient`是确保在打印选项显示在菜单`WebView`中之前完全加载中的数据, `OnPageFinished`这与方法相同。 `OnPageFinished`侦听 web 内容以完成加载, 并通知活动通过以下方式`InvalidateOptionsMenu`重新创建其选项菜单:

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

`OnPageFinished`还会将`dataLoaded`值设置`true`为, `OnCreateOptionsMenu`因此可以通过 "打印" 选项就地重新创建菜单。

##### <a name="printmanager"></a>PrintManager

下面的代码示例将打印的内容`WebView`:

```csharp
void PrintPage ()
{
    PrintManager printManager = (PrintManager)GetSystemService (Context.PrintService);
    PrintDocumentAdapter printDocumentAdapter = myWebView.CreatePrintDocumentAdapter ();
    printManager.Print ("MyWebPage", printDocumentAdapter, null);
}
```

`Print`采用作为参数: 打印作业的名称 (此示例中为 "MyWebPage")、[`PrintDocumentAdapter`](xref:Android.Print.PrintDocumentAdapter)
它从内容生成打印文档, 并[`PrintAttributes`](xref:Android.Print.PrintAttributes)
(`null`在上面的示例中)。 您可以指定`PrintAttributes`以帮助对打印页上的内容进行布局, 不过, 默认属性应处理大多数方案。

调用`Print`会加载打印 UI, 其中列出了打印作业的选项。 UI 使用户能够选择将 HTML 内容打印或保存到 PDF, 如以下屏幕截图所示:

[![显示 "打印" 菜单的 PrintHtmlActivity 屏幕截图](kitkat-images/print1.png)](kitkat-images/print1.png#lightbox)

[![显示 "另存为 PDF" 菜单的 PrintHtmlActivity 屏幕截图](kitkat-images/print2.png)](kitkat-images/print2.png#lightbox)

<a name="hardware" />

## <a name="hardware"></a>硬件

KitKat 添加了几个 Api 来容纳新的设备功能。 最值得注意的是, 这是基于主机的卡仿真和`SensorManager`新的。

### <a name="host-based-card-emulation-in-nfc"></a>NFC 中基于主机的卡仿真

基于主机的卡仿真 (HCE) 允许应用程序的行为类似于 NFC 卡或 NFC 卡读卡器, 而无需依赖于电信公司的专有安全元素。 设置 HCE 之前, 请确保设备`PackageManager.HasSystemFeature`上的 HCE 可用:

```csharp
bool hceSupport = PackageManager.HasSystemFeature(PackageManager.FeatureNfcHostCardEmulation);
```

HCE 要求将 HCE 功能和`Nfc`权限都注册到应用程序的: `AndroidManifest.xml`

```xml
<uses-feature android:name="android.hardware.nfc.hce" />
```

[![在应用选项中设置 NFC 权限](kitkat-images/nfc.png)](kitkat-images/nfc.png#lightbox)

若要工作, HCE 必须能够在后台运行, 并且在用户进行 NFC 事务时必须启动, 即使使用 HCE 的应用程序未运行也是如此。 可以通过将`Service`HCE 代码编写为来实现此目的。 HCE 服务实现了`HostApduService`接口, 该接口实现以下方法:

- *ProcessCommandApdu* -应用程序协议数据单元 (APDU) 是 NFC 读取器和 HCE 服务之间发送的内容。 此方法从读取器使用 ADPU, 并返回响应中的数据单位。

- *OnDeactivated* - `HostAdpuService`当 HCE 服务不再与 NFC 读取器通信时, 将停用。


还需要在应用程序清单中注册 HCE 服务, 并使用适当的权限、意向筛选器和元数据对其进行修饰。 下面的代码是`HostApduService` `Service`使用属性注册到 Android 清单的示例 (有关属性的详细信息, 请参阅使用[android 清单](~/android/platform/android-manifest.md)的 Xamarin 指南):

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

上述服务为 NFC 读取器提供了一种与应用程序交互的方式, 但 NFC 读取器仍无法知道此服务是否正在模拟其需要扫描的 NFC 卡。 为帮助 NFC 读取器识别服务, 可以为服务分配一个唯一的*应用程序 ID (辅助)* 。 我们在使用`MetaData`属性注册的 xml 资源文件中指定辅助工具以及 HCE 服务的其他元数据 (请参阅上面的代码示例)。 此资源文件指定一个或多个辅助筛选器-以十六进制格式表示的、与一个或多个 NFC 读取器设备的帮助相对应的唯一标识符字符串。

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

除辅助筛选器外, xml 资源文件还提供 HCE 服务的面向用户的说明, 指定帮助组 (支付应用程序与 "其他"), 并在支付应用程序时向用户显示 260x96 dp 横幅。

上述设置提供模拟 NFC 卡的应用程序的基本构建基块。 NFC 本身需要执行更多步骤并进行进一步的测试。 有关基于主机的卡仿真的详细信息, 请参阅[Android 文档门户](https://developer.android.com/guide/topics/connectivity/nfc/hce.html)。
有关将 NFC 与 Xamarin 配合使用的详细信息, 请参阅[XAMARIN NFC 示例](https://github.com/xamarin/monodroid-samples/tree/master/NfcSample)。

### <a name="sensors"></a>传感器

通过 KitKat, 可以通过[`SensorManager`](xref:Android.Hardware.SensorManager)访问设备传感器。
`SensorManager`允许操作系统将传感器信息按批次传递给应用程序, 从而保留电池寿命。

KitKat 还附带了两种新的传感器类型, 用于跟踪用户的步骤。 这些是基于加速感应, 并包括:

- *StepDetector* -当用户执行某个步骤时, 会通知/唤醒, 并且当发生该步骤时, 检测程序会提供一个时间值。

- *StepCounter* -跟踪用户自传感器注册到*下一次设备重新启动*以来所执行的步骤数。

下面的屏幕截图描述了操作中的步骤计数器:

[![显示步骤计数器的 SensorsActivity 应用程序的屏幕截图](kitkat-images/stepcounter.png)](kitkat-images/stepcounter.png#lightbox)

可以`SensorManager`通过调用`GetSystemService(SensorService)`并将结果`SensorManager`强制转换为来创建。 若要使用步骤计数器, 请`GetDefaultSensor` `SensorManager`对调用。 你可以注册传感器, 并侦听步骤计数中的更改, 并提供[`ISensorEventListener`](xref:Android.Hardware.ISensorEventListener)
接口, 如下面的代码示例所示:

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

`OnSensorChanged`如果在应用程序处于前台时进行升级, 则会调用。 如果应用程序进入后台, 或者设备处于睡眠状态, `OnSensorChanged`则将不会调用; 但是, 在调用之前`UnregisterListener` , 将继续对这些步骤进行计数。

请记住, 每个*注册传感器的应用程序的步骤计数值都是累积*的。 这意味着, 即使你卸载并重新安装应用程序, 并在应用`count`程序启动时将变量初始化为 0, 由传感器报告的值仍将保留在注册传感器时执行的总步骤数, 无论你的应用程序或另一个。 您可以通过`UnregisterListener` `SensorManager`在上调用来阻止您的应用程序添加到步骤计数器, 如以下代码所示:

```csharp
protected override void OnPause()
{
    base.OnPause ();
    senMgr.UnregisterListener(this);
}
```

重新启动设备会将步骤计数重置为0。 您的应用程序将需要额外的代码, 以确保它报告的应用程序的准确计数, 而不考虑使用传感器的其他应用程序或设备的状态。


> [!NOTE]
> 尽管步骤检测和计数的 API 随附了 KitKat, 但并不是所有手机都与传感器 outfitted。 可以通过运行`PackageManager.HasSystemFeature(PackageManager.FeatureSensorStepCounter);`查看传感器是否可用, 或检查以确保返回的`GetDefaultSensor`值不`null`是。


<a name="developer_tools" />

## <a name="developer-tools"></a>开发人员工具

### <a name="screen-recording"></a>屏幕录制

KitKat 包括新的屏幕录制功能, 使开发人员能够记录应用程序的操作。 屏幕录制通过[Android Debug Bridge (ADB)](https://developer.android.com/tools/help/adb.html)客户端提供, 可作为 Android SDK 的一部分下载。

若要录制屏幕, 请连接设备;然后, 找到 Android SDK 安装, 导航到 "**平台工具**" 目录并运行**adb**客户端:

```shell
adb shell screenrecord /sdcard/screencast.mp4
```

上述命令将以默认的4Mbps 分辨率记录默认的3分钟视频。 若要编辑长度, 请添加 *--时间限制*标志。
若要更改分辨率, 请添加 *--比特率*标志。 以下命令将在8Mbps 上记录一分钟的视频:

```shell
adb shell screenrecord --bit-rate 8000000 --time-limit 60 /sdcard/screencast.mp4
```

你可以在设备上找到你的视频-录制完成后, 它将显示在你的库中。


## <a name="other-kitkat-additions"></a>其他 KitKat 添加

除了上述更改之外, KitKat 允许你:

- *使用全屏*-KitKat 引入了一种新的[沉浸式模式](https://developer.android.com/reference/android/view/View.html#setSystemUiVisibility(int)), 用于浏览内容、玩游戏, 以及运行可从全屏体验中获益的其他应用程序。

- *自定义通知*-获取有关系统通知的其他详细信息[`NotificationListenerService`](xref:Android.Service.Notification.NotificationListenerService)
  . 这使你能够以不同的方式在应用程序中显示信息。

- *镜像可绘制资源*-可绘制资源具有新的[`autoMirrored`](https://developer.android.com/reference/android/R.attr.html#autoMirrored)
  此属性告知系统为需要翻转以实现从左到右布局的图像创建镜像版本。

- *暂停动画*-暂停和恢复用[`Animator`](xref:Android.Animation.Animator)
  类的新实例。

- *阅读动态更改文本*-表示 UI 的部分, 这些部分以新[`accessibilityLiveRegion`](https://developer.android.com/reference/android/R.attr.html#accessibilityLiveRegion)
  特性, 以便在辅助功能模式下自动读取新文本。

- *增强音频体验*-使跟踪与[`LoudnessEnhancer`](xref:Android.Media.Audiofx.LoudnessEnhancer)
  , 查找音频流的峰值和 RMS, 其中包含[`Visualizer`](xref:Android.Media.Audiofx.Visualizer.MeasurementModePeakRms)
  类, 并从[音频时间戳](xref:Android.Media.AudioTimestamp)获取信息以帮助进行音频视频同步。

- *同步 ContentResolver 自定义间隔*-KitKat 将一些可变性添加到同步请求的执行时间。 通过调用`ContentResolver.RequestSync`并传入在`SyncRequest`自定义时间或间隔同步。 `ContentResolver`

- *区分控制器*-在 KitKat 中, 为控制器分配唯一的整数标识符, 可通过设备的`ControllerNumber`属性进行访问。 这样就可以更轻松地在游戏中区分玩家。

- *远程控制*-对硬件和软件端进行一些更改, KitKat 允许你使用 IR 发送器将设备 outfitted 启用到远程控制`ConsumerIrService`, 并使用新的[`RemoteController`](xref:Android.Media.RemoteController)
  Api.

有关上述 API 更改的详细信息, 请参阅 Google [Android 4.4 api](https://developer.android.com/about/versions/android-4.4.html)概述。


## <a name="summary"></a>总结

本文介绍了 Android 4.4 中提供的一些新 Api (API 级别 19), 并介绍了将应用程序转换为 KitKat 时的最佳做法。 它概述了影响用户体验的 Api 的更改, 其中包括*转换框架*和*主题*的新选项。 接下来, 它引入了*存储访问框架*和`DocumentsProvider`类以及新的*打印 api*。 它探讨了*基于 NFC 主机的智能卡仿真*以及如何使用*低能耗传感器*, 其中包括两个新传感器, 用于跟踪用户的步骤。 最后, 它演示了如何使用*屏幕录制*来捕获应用程序的实时演示, 并提供了 KitKat API 更改和添加的详细列表。


## <a name="related-links"></a>相关链接

- [KitKat 示例](https://docs.microsoft.com/samples/xamarin/monodroid-samples/kitkat)
- [Android 4.4 Api](https://developer.android.com/about/versions/android-4.4.html)
- [Android KitKat](https://developer.android.com/about/versions/kitkat.html)
