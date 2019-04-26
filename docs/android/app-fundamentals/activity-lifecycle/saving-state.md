---
title: 演练 - 保存活动状态
description: 我们已介绍其理论依据活动生命周期指南; 中保存状态现在，让我们看一下示例。
ms.prod: xamarin
ms.assetid: A6090101-67C6-4BDD-9416-F2FB74805A87
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: c8f92e55648dff469227cc3bad981ad5f6e6d0ac
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61019111"
---
# <a name="walkthrough---saving-the-activity-state"></a>演练 - 保存活动状态

_我们已介绍其理论依据活动生命周期指南; 中保存状态现在，让我们看一下示例。_

## <a name="activity-state-walkthrough"></a>活动状态演练

让我们打开**ActivityLifecycle_Start**项目 (在[ActivityLifecycle](https://developer.xamarin.com/samples/monodroid/ActivityLifecycle)示例)，生成它，并运行它。 这是一个非常简单的项目具有两个活动以演示活动生命周期和各种生命周期方法的调用方式。 当启动应用程序的屏幕`MainActivity`显示： 

[![一个活动屏幕](saving-state-images/01-activity-a-sml.png)](saving-state-images/01-activity-a.png#lightbox)

### <a name="viewing-state-transitions"></a>查看状态转换

在此示例中的每个方法将写入到 IDE 应用程序输出窗口以指示活动状态。 (若要在 Visual Studio 中打开输出窗口，键入**CTRL ALT O**; 若要将在 Visual Studio for Mac 中打开输出窗口中，单击**视图 > 板 > 应用程序输出**。)

当首次启动该应用程序时，输出窗口会显示的状态更改*活动 A*: 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
```

当我们单击**启动活动 B**按钮，我们看到*活动 A*暂停和停止时*活动 B*经历其状态更改： 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.SecondActivity] Activity B - OnCreate
[ActivityLifecycle.SecondActivity] Activity B - OnStart
[ActivityLifecycle.SecondActivity] Activity B - OnResume
[ActivityLifecycle.MainActivity] Activity A - OnStop
```

因此，*活动 B*已启动并显示来代替*活动 A*: 

[![活动 B 屏幕](saving-state-images/02-activity-b-sml.png)](saving-state-images/02-activity-b.png#lightbox)

当我们单击**回**按钮，*活动 B*销毁并*活动 A*恢复： 

```shell
[ActivityLifecycle.SecondActivity] Activity B - OnPause
[ActivityLifecycle.MainActivity] Activity A - OnRestart
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
[ActivityLifecycle.SecondActivity] Activity B - OnStop
[ActivityLifecycle.SecondActivity] Activity B - OnDestroy
```
### <a name="adding-a-click-counter"></a>添加一个单击计数器

接下来，我们将更改应用程序，它使我们能够计算并显示单击次数的按钮。 首先，让我们添加`_counter`实例的变量`MainActivity`:

```csharp
int _counter = 0;
```

接下来，让我们编辑**Resource/layout/Main.axml**布局文件，并添加一个新`clickButton`显示的用户单击按钮的次数。 得到**Main.axml**应如下所示： 

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
        android:text="@string/mybutton_text" />
    <Button
        android:id="@+id/clickButton"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/counterbutton_text" />
</LinearLayout>
```

让我们将以下代码添加到末尾[OnCreate](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/)中的方法`MainActivity`&ndash;此代码处理单击事件从`clickButton`:

```csharp
var clickbutton = FindViewById<Button> (Resource.Id.clickButton);
clickbutton.Text = Resources.GetString (
    Resource.String.counterbutton_text, _counter);
clickbutton.Click += (object sender, System.EventArgs e) =>
{
    _counter++;
    clickbutton.Text = Resources.GetString (
        Resource.String.counterbutton_text, _counter);
} ;
```

当我们生成并再次运行该应用，一个新按钮显示的递增，并显示的值`_counter`上每次单击：

[![添加触摸计数](saving-state-images/03-touched-sml.png)](saving-state-images/03-touched.png#lightbox)

但当我们旋转为横向模式的设备，此计数将丢失：

[![旋转为横向将计数设置为零](saving-state-images/05-rotate-nosave-sml.png)](saving-state-images/05-rotate-nosave.png#lightbox)

检查应用程序输出，我们看到*活动 A*已暂停、 停止、 销毁、 重新创建、 重新启动，然后恢复期间从纵向到横向模式下的旋转： 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.MainActivity] Activity A - OnStop
[ActivityLifecycle.MainActivity] Activity A - On Destroy

[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
```

因为*活动 A*即被损坏和将设备旋转时，再次重新创建其实例状态将丢失。 接下来，我们将添加代码以保存并还原实例状态。

### <a name="adding-code-to-preserve-instance-state"></a>将代码添加到保留实例状态

让我们将方法添加到`MainActivity`保存实例状态。 之前*活动 A*是销毁，Android 会自动调用[OnSaveInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnSaveInstanceState/p/Android.OS.Bundle/) ，并在将传递[捆绑](https://developer.xamarin.com/api/type/Android.OS.Bundle/)，我们可以使用它来存储我们实例状态。 让我们使用它来将我们单击计数另存为一个整数值：

```csharp
protected override void OnSaveInstanceState (Bundle outState)
{
    outState.PutInt ("click_count", _counter);
    Log.Debug(GetType().FullName, "Activity A - Saving instance state");

    // always call the base implementation!
    base.OnSaveInstanceState (outState);    
}
```

当*活动 A*重新创建和恢复后，Android 将此传递`Bundle`返回到我们`OnCreate`方法。 让我们将代码添加到`OnCreate`还原`_counter`从传入的值`Bundle`。 添加以下代码行之前只是其中`clickbutton`定义： 

```csharp
if (bundle != null)
{
    _counter = bundle.GetInt ("click_count", 0);
    Log.Debug(GetType().FullName, "Activity A - Recovered instance state");
}
```

生成和应用程序再次运行，然后单击第二个按钮几次。 当我们旋转为横向模式的设备时，将被保留计数 ！

[![旋转屏幕显示四个保留的计数](saving-state-images/06-rotate-save-sml.png)](saving-state-images/06-rotate-save.png#lightbox)


让我们看看输出窗口以查看发生了什么情况：
    
```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.MainActivity] Activity A - Saving instance state
[ActivityLifecycle.MainActivity] Activity A - OnStop
[ActivityLifecycle.MainActivity] Activity A - On Destroy

[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - Recovered instance state
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
``` 

之前[OnStop](https://developer.xamarin.com/api/member/Android.App.Activity.OnStop/)调用方法，我们的新`OnSaveInstanceState`调用方法以保存`_counter`中的值`Bundle`。 Android 传递这`Bundle`告诉我们调用时我们`OnCreate`方法，并且我们能够用它来还原`_counter`到从我们离开的位置的值。


## <a name="summary"></a>总结

在本演练中，我们使用了我们的活动生命周期的知识来保留状态数据。 



## <a name="related-links"></a>相关链接

- [ActivityLifecycle （示例）](https://developer.xamarin.com/samples/monodroid/ActivityLifecycle)
- [活动生命周期](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Android 活动](https://developer.xamarin.com/api/type/Android.App.Activity/)
