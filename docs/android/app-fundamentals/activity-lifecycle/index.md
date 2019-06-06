---
title: 活动生命周期
description: 活动是 Android 应用程序的基本构造块，它们可以存在于多个不同的状态。 活动生命周期以实例化开始和结尾析构，，并且包括很多状态之间。 当活动更改状态时，会调用相应的生命周期事件方法，通知即将发生的状态更改的活动并使其能够执行的代码以适应所做的更改。 本文分析活动的生命周期，并解释了负责该活动具有这些状态更改为良好、 可靠的应用程序的一部分的每一阶段。
ms.prod: xamarin
ms.assetid: 05B34788-F2D2-4347-B66B-40AFD7B1D167
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/28/2018
ms.openlocfilehash: 48ff30397b2592dd2c4dbd445987392d78ced6f3
ms.sourcegitcommit: d3f48bfe72bfe03aca247d47bc64bfbfad1d8071
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2019
ms.locfileid: "66740768"
---
# <a name="activity-lifecycle"></a>活动生命周期

_活动是 Android 应用程序的基本构造块，它们可以存在于多个不同的状态。活动生命周期以实例化开始和结尾析构，，并且包括很多状态之间。当活动更改状态时，会调用相应的生命周期事件方法，通知即将发生的状态更改的活动并使其能够执行的代码以适应所做的更改。本文分析活动的生命周期，并解释了负责该活动具有这些状态更改为良好、 可靠的应用程序的一部分的每一阶段。_

## <a name="activity-lifecycle-overview"></a>活动生命周期概述

活动是一个不寻常的特定于 Android 编程概念。 在传统的应用程序开发通常会有一个静态 main 方法，执行以启动应用程序。 与 Android，但是，情况就不同了;Android 应用程序可以通过任何已注册的活动应用程序中启动。 在实践中，大多数应用程序只能指定为应用程序入口点的特定活动。 但是，如果应用程序崩溃，或终止由操作系统，操作系统可以尝试重新启动应用程序在打开的最后一个活动或以前的活动堆栈内的其他任何位置。
此外，操作系统可能会暂停活动，当它们不处于活动状态，并回收它们是否内存不足。 若要允许应用程序的情况活动重新启动时，尤其是当活动依赖于以前的活动中的数据的正确还原其状态，必须进行仔细考虑。

活动生命周期实现为一系列操作系统的方法调用的生命周期的活动。 这些方法允许开发人员实现，才可满足他们的应用程序的状态和资源管理要求的功能。

它是非常重要的应用程序开发人员分析每个活动的要求，以确定需要实现公开的活动生命周期的方法。 如果不这样做可能会导致应用程序不稳定、 崩溃、 资源膨胀和甚至基础操作系统不稳定。

本章介绍活动生命周期中详细信息，包括：

-  活动状态
-  生命周期方法
-  保留应用程序的状态


本部分还包括[演练](~/android/app-fundamentals/activity-lifecycle/saving-state.md)，提供了有关如何有效地将状态保存在活动生命周期中的实际示例。 本章结束时应了解活动生命周期和如何支持 Android 应用程序中。

## <a name="activity-lifecycle"></a>活动生命周期

Android 活动生命周期包括开发人员提供资源管理框架的方法中的 Activity 类公开的集合。 此框架允许开发人员以满足应用程序中的每个活动的唯一的状态管理要求并正确处理资源管理。

### <a name="activity-states"></a>活动状态

Android OS 进行仲裁基于其状态的活动。 这有助于确定不再使用，从而允许操作系统以回收内存和资源的活动的 Android。 下图说明了活动可能在其生存期内要经历的状态：

[![活动状态关系图](images/image1-sml.png)](images/image1.png#lightbox)

这些状态可分为 4 个主要的组，如下所示：

1.  *活动或正在运行*&ndash;活动都被视为处于活动状态或运行它们是否在前台，也称为活动堆栈的顶部。 这被视为在 Android 中，最高优先级的活动，这种情况下将仅在终止由操作系统在极端情况下，此类像该活动将尝试使用更多的内存比是设备上的可用因为这样可能会导致 UI 无响应。

1.  *暂停*&ndash;当设备进入睡眠状态，或活动是仍然可见，但通过新的、 非全尺寸或透明的活动部分隐藏时，活动将被视为已暂停。 暂停的活动是仍保持活动状态，也就是说，维护状态和成员的所有信息，并保持附加到窗口管理器。 这被视为第二个如果取消此活动将满足保持稳定且高度可响应的活动/正在运行活动所需的资源要求最高优先级的活动在 Android 中，，因此，将仅由操作系统被终止。

1.  *已停止/Backgrounded* &ndash;完全遮盖的另一个活动的活动被视为已停止或在后台。
    请尝试停止的活动仍保留其状态和成员的信息，以便只要有可能，但已停止活动都被认为是最低优先级的三种状态，并且在这种情况下，操作系统将终止此状态，首先以满足资源中的活动要求的更高优先级的活动。

1.  *重新启动*&ndash;可能会从任何位置的活动已暂停为停止在要从内存中删除由 Android 的生命周期中。 如果用户导航回必须重新启动的活动将还原到以前保存的状态，且然后向用户显示。


### <a name="activity-re-creation-in-response-to-configuration-changes"></a>活动重新创建以响应配置更改

更复杂得多，Android 将一个详细扳手引发调用配置更改的组合中。 配置更改会快速活动析构/重新 creation 周期发生活动的配置更改时，例如，当设备是[旋转](~/android/app-fundamentals/handling-rotation.md)（和该活动需要重新生成在横向或纵向模式下），当显示键盘 （和活动提供的机会来调整自身大小），或当设备处于停靠，等等。

配置更改仍会导致相同的活动状态更改会停止并重新启动活动过程中发生的。 但是，为了确保应用程序感觉响应，并且还在配置更改过程中执行，十分重要尽可能快地处理这些。 正因为如此，Android 有一个特定的 API，可用于在配置更改过程中保留状态。
我们将介绍这更高版本中[Lifecycle 整个管理状态](~/android/app-fundamentals/activity-lifecycle/index.md#Managing_State_Throughout_the_Lifecycle)部分。

### <a name="activity-lifecycle-methods"></a>活动生命周期方法

Android SDK 和扩展插件，Xamarin.Android framework 所提供的用于管理应用程序中的活动的状态的功能强大的模型。 当更改活动的状态时，活动将通知由操作系统，对该活动调用特定的方法。 下图说明了这些方法与活动生命周期相关：

[![活动生命周期流程图](images/image2-sml.png)](images/image2.png#lightbox)

作为开发人员，您可以通过重写这些方法对在活动中的处理状态更改。 请务必注意，但是，所有生命周期方法在 UI 线程上调用，且将阻止执行下的一个 UI 工作，例如隐藏当前活动中，操作系统显示一个新的活动，等等。在这种情况下，这些方法中的代码应该越简单越好感觉良好执行使用应用程序。 应在后台线程上执行任何长时间运行的任务。

让我们检查每个这些生命周期方法和它们的使用：

#### <a name="oncreate"></a>OnCreate

[OnCreate](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/)是第一种方法来创建活动时调用。
`OnCreate` 始终重写以执行任何启动初始化，则可能如要求的活动：

-  创建视图
-  初始化变量
-  静态数据绑定到列表


`OnCreate` 采用[捆绑](https://developer.xamarin.com/api/type/Android.OS.Bundle/)参数，它是用于存储和活动，如果绑定不是 null 之间传递状态信息和对象的字典，这表示该活动正在重新启动，并且它应还原其状态从在前一个实例。 下面的代码演示如何从捆绑检索值：

```csharp
protected override void OnCreate(Bundle bundle)
{
   base.OnCreate(bundle);

   string intentString;
   bool intentBool;

   if (bundle != null)
   {
      intentString = bundle.GetString("myString");
      intentBool = bundle.GetBoolean("myBool");
   }

   // Set our view from the "main" layout resource
   SetContentView(Resource.Layout.Main);
}
```

一次`OnCreate`已完成后，将调用 Android `OnStart`。

#### <a name="onstart"></a>OnStart

[OnStart](https://developer.xamarin.com/api/member/Android.App.Activity.OnStart/)始终在调用后系统`OnCreate`已完成。 如果它们需要活动变得可见例如刷新活动中的视图的当前值之前执行特定任务的任何权利，活动可能会重写此方法。 Android 将调用`OnResume`紧跟此方法。

#### <a name="onresume"></a>OnResume

系统调用[OnResume](https://developer.xamarin.com/api/member/Android.App.Activity.OnResume/)时该活动已准备好开始与用户进行交互。
活动应重写此方法以执行以下任务：

-  帧速率 （游戏开发中的常见任务） 的提升效应
-  启动动画
-  侦听 GPS 更新
-  显示任何相关的警报或对话框
-  绑定外部事件处理程序


例如，下面的代码段演示如何初始化照相机：

```csharp
public void OnResume()
{
    base.OnResume(); // Always call the superclass first.

    if (_camera==null)
    {
        // Do camera initializations here
    }
}
```

`OnResume` 很重要，因为这是任何操作中完成`OnPause`应在未完成`OnResume`，因为它是唯一的生命周期方法，保证操作之后执行`OnPause`时将返回到生命的活动。

#### <a name="onpause"></a>OnPause

[OnPause](https://developer.xamarin.com/api/member/Android.App.Activity.OnPause/)系统以将活动放到背景或将成为部分遮住活动时调用。 在需要时，活动应重写此方法：

-   未保存的更改提交到持久性数据

-   销毁或清理消耗资源的其他对象

-   负载增加帧速率和暂停动画

-   取消注册通知处理程序 （即绑定到服务的那些） 或外部事件处理程序。 这必须进行以防止活动内存泄漏。

-   同样，如果活动具有显示任何对话框或警报，它们必须先清除与`.Dismiss()`方法。

例如，下面的代码段即会发布照相机，活动不能进行使用它暂停时：

```csharp
public void OnPause()
{
    base.OnPause(); // Always call the superclass first

    // Release the camera as other activities might need it
    if (_camera != null)
    {
        _camera.Release();
        _camera = null;
    }
}
```

有两种可能的生命周期方法将调用之后的`OnPause`:

1.  `OnResume` 如果活动是要返回到前台，将会调用。
1.  `OnStop` 如果该活动放入后台，将会调用。


#### <a name="onstop"></a>OnStop

[OnStop](https://developer.xamarin.com/api/member/Android.App.Activity.OnStop/)不再对用户可见的活动时调用。 发生以下情况之一时，将发生这种情况：

-  新的活动启动，并介绍了此活动。
-  将现有的活动置于前台。
-  活动已被破坏。


`OnStop` 可能不会始终会调用在内存不足的情况下，例如 Android 时可用的资源以及不能正确后台活动。 出于此原因，最好是不依赖于`OnStop`获取准备销毁活动时调用。 下一步后将在可能调用的生命周期方法`OnDestroy`如果该活动将消失，或`OnRestart`如果返回传入活动与用户进行交互。

#### <a name="ondestroy"></a>OnDestroy

[OnDestroy](https://developer.xamarin.com/api/member/Android.App.Activity.OnDestroy/)是之前它已被销毁并从内存中完全删除活动实例调用的最后一个方法。 在极端情况下 Android 会严重影响应用程序过程在承载活动，这将导致`OnDestroy`不调用。 大多数活动将不会实现此方法，因为大多数清理并完成了关机`OnPause`和`OnStop`方法。 `OnDestroy`方法通常被重写清理长时间运行的资源可能会泄漏资源。 出现这种可能是后台线程中启动`OnCreate`。

将名为该活动已被销毁后没有生命周期方法。

#### <a name="onrestart"></a>OnRestart

[OnRestart](https://developer.xamarin.com/api/member/Android.App.Activity.OnRestart/)后您的活动已停止，在它重新启动之前调用。 在用户按应用程序中的上一个活动的主页按钮时，会是很好的例子。 当发生这种情况`OnPause`，然后`OnStop`调用方法，并将活动移动到后台，但不是会被销毁。 如果用户是使用任务管理器或类似的应用程序还原应用程序，Android 将调用`OnRestart`活动的方法。

有哪种逻辑应在实现通用准则`OnRestart`。 这是因为`OnStart`总是调用而不考虑是否正在创建活动或正在重新启动，因此应在初始化活动所需的任何资源`OnStart`，而非`OnRestart`。

下一个生命周期方法之后调用`OnRestart`将为`OnStart`。

### <a name="back-vs-home"></a>备份 vs。主页

许多 Android 设备都有两个不同的按钮:"上一步"按钮和"主页"按钮。 可以为 Android 4.0.3 下面的屏幕截图中看到此示例：

[![上一页和主页按钮](images/image4-sml.png)](images/image4.png#lightbox)

即使它们看上去拥有相同的效果将应用程序放在后台的没有在两个按钮之间略有不同。 当用户单击后退按钮时，它们在告诉 Android 与活动完成。 Android 将销毁该活动。 与此相反，当用户单击主页按钮该活动只是放入后台&ndash;Android 将终止该活动。

<a name="Managing_State_Throughout_the_Lifecycle" />

## <a name="managing-state-throughout-the-lifecycle"></a>管理整个生命周期状态

已停止或销毁活动时系统将提供的机会保存更高版本的解除冻结的活动的状态。
已保存状态，这称为实例状态。 Android 提供了用于在活动生命周期过程中存储实例状态的三个选项：

1. 存储中的基元值`Dictionary`称为[捆绑](https://developer.xamarin.com/api/type/Android.OS.Bundle/)Android 将用于保存状态。

1. 创建自定义类将保存位图等复杂值。 Android 将使用此自定义类来保存状态。

1. 绕过配置更改生命周期中，并假设完成负责维护活动中的状态。


本指南介绍了前两个选项。



### <a name="bundle-state"></a>捆绑包状态

用于保存实例状态的首要选项是使用键/值字典对象，称为[捆绑](https://developer.xamarin.com/api/type/Android.OS.Bundle/)。
请记住，创建一个活动时`OnCreate`方法作为参数传递一个捆绑包，则此捆绑包可用于还原实例状态。 不建议用于捆绑包进行更复杂的数据，不会快速或轻松地进行序列化键/值对 （如位图）;相反，它应该用于简单的值，例如字符串。

活动提供方法来帮助进行保存和检索捆绑中的实例状态：

-   [OnSaveInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnSaveInstanceState/p/Android.OS.Bundle/) &ndash;这由 Android 时调用的活动已被破坏。 活动可以实现此方法，如果它们需要保存任何键/值状态项。

-   [OnRestoreInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnRestoreInstanceState/p/Android.OS.Bundle/) &ndash;之后调用此`OnCreate`方法完成，并且提供了另一种可能用于初始化完成后恢复其状态的活动。

下图说明了如何使用这些方法：

[![捆绑包状态流程图](images/image3-sml.png)](images/image3.png#lightbox)

#### <a name="onsaveinstancestate"></a>OnSaveInstanceState

[OnSaveInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnSaveInstanceState/p/Android.OS.Bundle/)正在停止活动都会调用。 它将接收该活动可以存储在其状态的捆绑包参数。 活动时设备体验的配置更改，可以使用`Bundle`传入的要通过重写保留的活动状态的对象`OnSaveInstanceState`。 例如，考虑以下代码：

```csharp
int c;

protected override void OnCreate (Bundle bundle)
{
  base.OnCreate (bundle);

  this.SetContentView (Resource.Layout.SimpleStateView);

  var output = this.FindViewById<TextView> (Resource.Id.outputText);

  if (bundle != null) {
    c = bundle.GetInt ("counter", -1);
  } else {
    c = -1;
  }

  output.Text = c.ToString ();

  var incrementCounter = this.FindViewById<Button> (Resource.Id.incrementCounter);

  incrementCounter.Click += (s,e) => {
    output.Text = (++c).ToString();
  };
}
```

上面的代码增加名为一个整数`c`名为的按钮时`incrementCounter`单击时，显示在结果`TextView`名为`output`。 当配置更改发生-例如，当将设备旋转-上面的代码将丢失的值`c`因为`bundle`是`null`，如下图中所示：

[![显示不会显示以前的值](images/07-sml.png)](images/07.png#lightbox)

若要保留的值`c`活动可以在此示例中，重写`OnSaveInstanceState`，捆绑中保存值，如下所示：

```csharp
protected override void OnSaveInstanceState (Bundle outState)
{
  outState.PutInt ("counter", c);
  base.OnSaveInstanceState (outState);
}
```

现在当将设备与新方向旋转的整数部分将为绑定中保存和检索的行：

```csharp
c = bundle.GetInt ("counter", -1);
```

> [!NOTE]
> 它是重要始终调用基实现`OnSaveInstanceState`，以便也可以保存的视图层次结构的状态。



##### <a name="view-state"></a>视图状态

重写`OnSaveInstanceState`是用于在活动中的暂时性数据保存在方向更改，如上面的示例中的计数器之间适当机制。 但是的默认实现`OnSaveInstanceState`将负责的每个视图中，在 UI 中保存暂时性数据，只要每个视图都有分配的 ID。 例如，假设应用程序具有`EditText`元素在 XML 中定义，如下所示：

```xml
<EditText android:id="@+id/myText"
  android:layout_width="fill_parent"
  android:layout_height="wrap_content"/>
```

由于`EditText`控件具有`id`分配，在用户输入一些数据并旋转设备时，数据仍显示，如下所示：

[![在横向模式中保留数据](images/08-sml.png)](images/08.png#lightbox)

#### <a name="onrestoreinstancestate"></a>OnRestoreInstanceState

[OnRestoreInstanceState](https://developer.xamarin.com/api/member/Android.App.Activity.OnRestoreInstanceState/p/Android.OS.Bundle/)后将调用`OnStart`。 它使活动能够还原以前保存在上一个过程绑定到任何状态`OnSaveInstanceState`。 这是提供给在同一个包`OnCreate`，但是。

下面的代码演示如何在还原状态`OnRestoreInstanceState`:

```csharp
protected override void OnRestoreInstanceState(Bundle savedState)
{
    base.OnRestoreSaveInstanceState(savedState);
    var myString = savedState.GetString("myString");
    var myBool = savedState.GetBoolean("myBool");
}
```

此方法存在是为了提供一些灵活地时应还原状态。 有时是等待，直到还原实例状态之前完成所有初始化更为合适。 此外，将现有的活动的子类可能只想要从实例状态还原的某些值。 在许多情况下，它不需要重写`OnRestoreInstanceState`，因为大多数活动可以还原使用提供给此捆绑包的状态`OnCreate`。

有关保存状态使用的示例`Bundle`，请参阅[演练-保存活动状态](saving-state.md)。


#### <a name="bundle-limitations"></a>捆绑包限制

尽管`OnSaveInstanceState`可以轻松地保存暂时性数据，它具有一些限制：

-   它不是在所有情况下调用。 例如，按**主页**或**回**退出活动不会导致`OnSaveInstanceState`被调用。

-   捆绑包传递到`OnSaveInstanceState`不专为大型对象，如图像。 对于大型对象，保存该对象从[OnRetainNonConfigurationInstance](https://developer.xamarin.com/api/member/Android.App.Activity.OnRetainNonConfigurationInstance/)非常可取，如下所述。

-   通过使用此捆绑包来保存的数据进行序列化，这可能会导致延迟。

捆绑包状态是适用于不使用太多内存的简单数据，而*非配置实例数据*很有用的更复杂的数据或检索成本较高的数据，如 web 服务调用或很复杂数据库查询。 根据需要在对象中获取保存非配置实例数据。 下一部分将介绍`OnRetainNonConfigurationInstance`作为保留通过配置更改的更复杂数据类型的一种方法。


### <a name="persisting-complex-data"></a>保存的复杂数据

捆绑包中保存数据，除了 Android 还支持将数据保存通过重写[OnRetainNonConfigurationInstance](https://developer.xamarin.com/api/member/Android.App.Activity.OnRetainNonConfigurationInstance/) ，并返回的实例`Java.Lang.Object`，其中包含要保留的数据。 有两个主要好处使用`OnRetainNonConfigurationInstance`保存状态：

-   从返回的对象`OnRetainNonConfigurationInstance`性能也具有更大、 更复杂的数据类型，因为内存保留此对象。

-   `OnRetainNonConfigurationInstance`方法是按需、 被调用，并且只在需要时。 这是比使用手动缓存更经济。

使用`OnRetainNonConfigurationInstance`是适用于方案是相当昂贵检索多个时间的数据，如 web 服务调用。 有关示例，请搜索 Twitter 的以下代码：

```csharp
public class NonConfigInstanceActivity : ListActivity
{
  protected override void OnCreate (Bundle bundle)
  {
    base.OnCreate (bundle);
    SearchTwitter ("xamarin");
  }

  public void SearchTwitter (string text)
  {
    string searchUrl = String.Format("http://search.twitter.com/search.json?" + "q={0}&rpp=10&include_entities=false&" + "result_type=mixed", text);

    var httpReq = (HttpWebRequest)HttpWebRequest.Create (new Uri (searchUrl));
    httpReq.BeginGetResponse (new AsyncCallback (ResponseCallback), httpReq);
  }

  void ResponseCallback (IAsyncResult ar)
  {
    var httpReq = (HttpWebRequest)ar.AsyncState;

    using (var httpRes = (HttpWebResponse)httpReq.EndGetResponse (ar)) {
      ParseResults (httpRes);
    }
  }

  void ParseResults (HttpWebResponse httpRes)
  {
    var s = httpRes.GetResponseStream ();
    var j = (JsonObject)JsonObject.Load (s);

    var results = (from result in (JsonArray)j ["results"] let jResult = result as JsonObject select jResult ["text"].ToString ()).ToArray ();

    RunOnUiThread (() => {
      PopulateTweetList (results);
    });
  }

  void PopulateTweetList (string[] results)
  {
    ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.ItemView, results);
  }
}
```

此代码检索从格式化为 JSON web 结果、 分析它们，然后在列表中，显示结果，如以下屏幕截图中所示：

[![在屏幕上显示的结果](images/06-sml.png)](images/06.png#lightbox)

配置发生更改时-例如，在旋转设备-时代码重复该过程。 若要重复使用最初检索到的结果并不会导致不必要的冗余网络调用，我们可以使用`OnRetainNonconfigurationInstance`来保存这些结果，如下所示：

```csharp
public class NonConfigInstanceActivity : ListActivity
{
  TweetListWrapper _savedInstance;

  protected override void OnCreate (Bundle bundle)
  {
    base.OnCreate (bundle);

    var tweetsWrapper = LastNonConfigurationInstance as TweetListWrapper;

    if (tweetsWrapper != null) {
      PopulateTweetList (tweetsWrapper.Tweets);
    } else {
      SearchTwitter ("xamarin");
    }

    public override Java.Lang.Object OnRetainNonConfigurationInstance ()
    {
      base.OnRetainNonConfigurationInstance ();
      return _savedInstance;
    }

    ...

    void PopulateTweetList (string[] results)
    {
      ListAdapter = new ArrayAdapter<string> (this, Resource.Layout.ItemView, results);
      _savedInstance = new TweetListWrapper{Tweets=results};
    }
}
```

现在，当将设备旋转，从检索原始结果`LastNonConfiguartionInstance`属性。 在此示例中，结果组成`string[]`包含推文。 由于`OnRetainNonConfigurationInstance`要求`Java.Lang.Object`将返回`string[]`子类包装在类中`Java.Lang.Object`，如下所示：

```csharp
class TweetListWrapper : Java.Lang.Object
{
  public string[] Tweets { get; set; }
}
```

例如，尝试使用`TextView`返回的对象作为`OnRetainNonConfigurationInstance`将导致泄露活动，如下面的代码所示：

```csharp
TextView _textView;

protected override void OnCreate (Bundle bundle)
{
  base.OnCreate (bundle);

  var tv = LastNonConfigurationInstance as TextViewWrapper;

  if(tv != null) {
    _textView = tv;
    var parent = _textView.Parent as FrameLayout;
    parent.RemoveView(_textView);
  } else {
    _textView = new TextView (this);
    _textView.Text = "This will leak.";
  }

  SetContentView (_textView);
}

public override Java.Lang.Object OnRetainNonConfigurationInstance ()
{
  base.OnRetainNonConfigurationInstance ();
  return _textView;
}
```

在本部分中，我们已了解如何保留与简单的状态数据`Bundle`，并保留更复杂的数据类型与`OnRetainNonConfigurationInstance`。

## <a name="summary"></a>总结

Android 活动生命周期为状态管理的应用程序中的活动提供强大的框架，但它可能难以理解和实现。 这一章介绍了活动可能会经历其生存期内，以及与这些状态相关联的生命周期方法期间的不同状态。 接下来，指南提供有关应在每个这些方法中执行逻辑的类型。


## <a name="related-links"></a>相关链接

- [处理旋转](~/android/app-fundamentals/handling-rotation.md)
- [Android 活动](https://developer.xamarin.com/api/type/Android.App.Activity/)
