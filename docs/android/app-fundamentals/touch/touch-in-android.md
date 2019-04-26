---
title: Android 中的触控
ms.prod: xamarin
ms.assetid: 405A1FA0-4EFA-4AEB-B672-F36307B9CF16
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 84767975eece4f8f0efae1fe53463cbc053bd836
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61012831"
---
# <a name="touch-in-android"></a>Android 中的触控

得如 iOS，Android 创建一个对象，包含有关与屏幕的用户的物理交互数据&ndash;`Android.View.MotionEvent`对象。 此对象保存数据，例如执行哪些操作，在触摸屏输入花费了将置于，多少压力已应用，等等。一个`MotionEvent`对象分解为以下值移入：

-  描述类型的动作，如在最初触摸，触摸屏输入跨屏幕上或触摸结束移动操作代码。

-  一组描述的位置的轴值`MotionEvent`和其他移动属性，如触摸屏输入正在进行、 触摸屏输入时已发生，和使用多大的压力。
   轴的值可能不同，具体取决于该设备，因此前面的列表不介绍所有轴值。


`MotionEvent`对象将传递给应用程序中适当的方法。 有三种方法为 Xamarin.Android 应用程序以响应触摸事件：

-  *将分配到一个事件处理程序`View.Touch`*  -`Android.Views.View`类具有`EventHandler<View.TouchEventArgs>`哪些应用程序可以将分配到一个处理程序。 这是典型.NET 行为。

-  *实现`View.IOnTouchListener`*  -此接口的实例可能会分配给使用视图的视图对象。 `SetOnListener` 方法。这是功能上等效于分配到一个事件处理程序`View.Touch`事件。 如果有许多不同的视图可能需要在接触时一些常见或共享逻辑，它将创建一个类并实现此方法比若要分配每个视图自己的事件处理程序更有效。

-  *重写`View.OnTouchEvent`*  -Android 子类中的所有视图`Android.Views.View`。 当访问视图时，将调用 Android`OnTouchEvent`并将其传递`MotionEvent`对象作为参数。


> [!NOTE]
> 并非所有的 Android 设备支持触摸屏。 

将以下标记添加到清单文件导致 Google Play 为仅显示你的应用在启用了触摸这些设备：

```xml
<uses-configuration android:reqTouchScreen="finger" />
```

## <a name="gestures"></a>笔势

笔势，是手绘形状上的触摸屏。 笔势可以有一个或多个笔画，每个笔画包含的点创建联系人与屏幕的不同点的序列。 Android 可以支持的手势，从涉及多点触控的复杂手势简单 fling 在屏幕上的许多不同的类型。

Android 提供`Android.Gestures`专门针对管理和响应手势的命名空间。 在所有手势的核心是一个名为的特殊类`Android.Gestures.GestureDetector`。 顾名思义，此类将侦听的手势和基于事件`MotionEvents`由操作系统提供。

若要实现手势检测程序，活动必须实例化`GestureDetector`类，并提供的一个实例`IOnGestureListener`，如下面的代码段所示：

```csharp
GestureOverlayView.IOnGestureListener myListener = new MyGestureListener();
_gestureDetector = new GestureDetector(this, myListener);
```

活动还必须实现 OnTouchEvent 并且将 MotionEvent 传递给手势检测程序。 下面的代码段显示了此示例：

```csharp
public override bool OnTouchEvent(MotionEvent e)
{
    // This method is in an Activity
    return _gestureDetector.OnTouchEvent(e);
}
```

实例时`GestureDetector`标识一个手势的感兴趣，它会通知的活动或应用程序通过引发事件或通过提供的回调`GestureDetector.IOnGestureListener`。
此接口提供了各种手势的六个方法：

-  *OnDown* -当点击发生，但不是会被释放时调用。

-  *OnFling* -fling 发生并触发事件的开始和结束触摸设备上提供的数据时调用。

-  *OnLongPress* -长按发生时调用。

-  *OnScroll*的滚动事件发生时调用。

-  *OnShowPress* -OnDown 发生之后并移动一个调用或事件未执行。

-  *OnSingleTapUp* -点击一下发生时调用。


在许多情况下应用程序仅可能感兴趣的手势子集。 在这种情况下，应用程序应扩展类 GestureDetector.SimpleOnGestureListener 和重写到他们感兴趣的事件对应的方法。

## <a name="custom-gestures"></a>自定义笔势

手势是用户与应用程序交互的极佳方法。 我们迄今为止看到的 Api 的简单手势，即可满足要求，但有可能证实有点麻烦更复杂的笔势。 为了帮助实现更复杂的手势，Android 提供了 API 的另一组简化的一些负担与自定义笔势关联的 Android.Gestures 命名空间中。

### <a name="creating-custom-gestures"></a>创建自定义笔势

自 Android 1.6，Android SDK 附带了名为手势生成器在仿真器上预安装的应用程序。 此应用程序允许开发人员创建可以嵌入到应用程序的预定义的笔势。 下面的屏幕截图显示了手势生成器的示例：

[![屏幕截图的手势生成器通过示例手势](touch-in-android-images/image11.png)](touch-in-android-images/image11.png#lightbox)

Google Play，可以找到名为手势工具此应用程序的改进的版本。 手势工具非常类似于笔势生成器，只是它允许您测试笔势后创建它们。 此下一步的屏幕截图显示了手势生成器：

[![屏幕快照的手势工具使用示例手势](touch-in-android-images/image12.png)](touch-in-android-images/image12.png#lightbox)

手势工具是用于创建自定义的手势，因为通过它，因为它们正在创建要测试的笔势更加有用，轻松地可通过 Google Play。

手势工具允许您通过在屏幕上绘制并指定一个名称创建一个手势。 创建笔势后它们保存在你的设备的 SD 卡上的二进制文件。 此文件需要从设备中检索，然后与文件夹 /Resources/raw 中的应用程序一起打包。 此文件可以检索从仿真程序使用 Android 调试桥。 下面的示例演示从 Galaxy Nexus 的文件复制到应用程序的资源目录：

```shell
$ adb pull /storage/sdcard0/gestures <projectdirectory>/Resources/raw
```

一旦已检索到它必须与你在目录 /Resources 内应用程序/原始一起打包的文件。 若要使用此手势文件的最简单方法是将文件加载到 GestureLibrary，如以下代码片段中所示：

```csharp
GestureLibrary myGestures = GestureLibraries.FromRawResources(this, Resource.Raw.gestures);
if (!myGestures.Load())
{
    // The library didn't load, so close the activity.
    Finish();
}
```

### <a name="using-custom-gestures"></a>使用自定义笔势

若要识别在活动中的自定义操作，它必须具有 Android.Gesture.GestureOverlay 对象添加到其布局。 下面的代码段演示如何以编程方式向活动中添加 GestureOverlayView:

```csharp
GestureOverlayView gestureOverlayView = new GestureOverlayView(this);
gestureOverlayView.AddOnGesturePerformedListener(this);
SetContentView(gestureOverlayView);
```

以下 XML 代码段演示如何以声明方式添加 GestureOverlayView:

```xml
<android.gesture.GestureOverlayView
    android:id="@+id/gestures"
    android:layout_width="match_parent "
    android:layout_height="match_parent" />
```

`GestureOverlayView`具有多个将在绘制笔势的过程中引发的事件。 最值得关注的事件是`GesturePerformed`。 当用户完成绘制其手势时，引发此事件。

当引发此事件时，该活动要求`GestureLibrary`尝试进行匹配手势工具具有一个手势的用户创建的笔势。 `GestureLibrary` 将返回的预测对象的列表。

每个预测对象保留的分数和一个在手势的名称`GestureLibrary`。 更高级分数、 更有可能在预测中名为的笔势匹配用户绘制的笔势。
通常情况下，分数低于 1.0 被视为不佳的匹配项。

下面的代码演示匹配笔势的示例：

```csharp
private void GestureOverlayViewOnGesturePerformed(object sender, GestureOverlayView.GesturePerformedEventArgs gesturePerformedEventArgs)
{
    // In this example _gestureLibrary was instantiated in OnCreate
    IEnumerable<Prediction> predictions = from p in _gestureLibrary.Recognize(gesturePerformedEventArgs.Gesture)
    orderby p.Score descending
    where p.Score > 1.0
    select p;
    Prediction prediction = predictions.FirstOrDefault();

    if (prediction == null)
    {
        Log.Debug(GetType().FullName, "Nothing matched the user's gesture.");
        return;
    }

    Toast.MakeText(this, prediction.Name, ToastLength.Short).Show();
}
```

此操作完成后，应了解如何在 Xamarin.Android 应用程序中使用触控和手势。 现在让我们转到演练并查看所有工作示例应用程序中的概念。



## <a name="related-links"></a>相关链接

- [Android 接触启动 （示例）](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_start)
- [Android 接触最终 （示例）](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_final)
