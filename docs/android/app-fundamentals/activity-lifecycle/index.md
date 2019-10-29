---
title: 活动生命周期
description: 活动是 Android 应用程序的基本构建基块，可在许多不同的状态中存在。 活动生命周期从实例化开始，以析构结束，并在之间包含许多状态。 活动更改状态时，将调用相应的生命周期事件方法，并通知活动即将发生的状态更改，并允许它执行代码以适应该更改。 本文介绍活动的生命周期，并说明在每种状态中，活动在每种状态中所发生的责任改变为操作良好且可靠的应用程序的一部分。
ms.prod: xamarin
ms.assetid: 05B34788-F2D2-4347-B66B-40AFD7B1D167
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/28/2018
ms.openlocfilehash: 6e69d21bb734f13d220c042535842538306d16c8
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016996"
---
# <a name="activity-lifecycle"></a>活动生命周期

_活动是 Android 应用程序的基本构建基块，可在许多不同的状态中存在。活动生命周期从实例化开始，以析构结束，并在之间包含许多状态。活动更改状态时，将调用相应的生命周期事件方法，并通知活动即将发生的状态更改，并允许它执行代码以适应该更改。本文介绍活动的生命周期，并说明在每种状态中，活动在每种状态中所发生的责任改变为操作良好且可靠的应用程序的一部分。_

## <a name="activity-lifecycle-overview"></a>活动生命周期概述

活动是特定于 Android 的异常编程概念。 在传统的应用程序开发中，通常会执行一个静态 main 方法，以启动应用程序。 但对于 Android，情况有所不同;可以通过应用程序内的任何已注册活动启动 Android 应用程序。 实际上，大多数应用程序将只有指定为应用程序入口点的特定活动。 但是，如果应用程序崩溃或由操作系统终止，则 OS 可以尝试在上一次打开的活动中或之前的活动堆栈中的任何其他位置重新启动应用程序。
此外，当操作系统不处于活动状态时，它们可能会暂停活动，如果内存不足，则回收它们。 若要使应用程序能够在活动重新启动时正确地还原其状态，则必须谨慎考虑，尤其是在该活动依赖于先前活动中的数据时。

活动生命周期实现为操作系统在活动的整个生命周期中调用的方法的集合。 这些方法允许开发人员实现满足其应用程序的状态和资源管理要求所必需的功能。

对于应用程序开发人员而言，分析每个活动的要求以确定需要实现活动生命周期所公开的方法极其重要。 如果不这样做，可能会导致应用程序不稳定、崩溃、资源膨胀，甚至可能导致基础操作系统不稳定。

本章详细介绍活动生命周期，包括：

- 活动状态
- 生命周期方法
- 保留应用程序的状态

本部分还包括一个[演练](~/android/app-fundamentals/activity-lifecycle/saving-state.md)，其中提供了有关如何在活动生命周期内有效保存状态的实际示例。 本章结束时，你应了解活动生命周期，以及如何在 Android 应用程序中对其进行支持。

## <a name="activity-lifecycle"></a>活动生命周期

Android 活动生命周期包含在向开发人员提供资源管理框架的活动类中公开的方法的集合。 此框架使开发人员能够满足应用程序内每个活动的唯一状态管理要求，并正确处理资源管理。

### <a name="activity-states"></a>活动状态

Android OS 仲裁活动基于其状态。 这有助于 Android 识别不再使用的活动，从而允许操作系统回收内存和资源。 下图说明了活动在其生存期内可以经历的状态：

[![活动状态关系图](images/image1-sml.png)](images/image1.png#lightbox)

这些状态可分为4个主要组，如下所示：

1. 如果活动*或正在运行*的 &ndash; 活动处于前台（也称为活动堆栈的顶部），则它们将被视为处于活动状态或正在运行。 这会被视为 Android 中优先级最高的活动，因此，只有在极端情况下，操作系统才会终止此活动，如活动尝试使用比设备上提供的内存更多的内存，这可能会导致 UI 无响应。

1. *暂停*&ndash; 设备进入睡眠状态，或者活动仍可见，但活动仍被新的、未完全大小的或透明的活动部分隐藏时，该活动被视为已暂停。 暂停的活动仍处于活动状态，即，它们维护所有状态和成员信息，并保持附加到窗口管理器。 这被视为 Android 中的第二个优先级最高的活动，因此，只有在终止此活动时，操作系统才会终止此活动，这是因为终止此活动将满足保持活动/活动活动稳定和响应所需的资源要求。

1. *已停止/Backgrounded* &ndash; 已被其他活动完全遮盖的活动被视为已停止或处于后台。
    已停止的活动仍会尝试尽可能地保留其状态和成员信息，但停止的活动被认为是三个状态的最低优先级，因此，OS 将首先终止此状态下的活动以满足资源优先级较高的活动的要求。

1. *重新启动*&ndash; 可能会在生命周期内从暂停的任何位置停止的活动从内存中删除。 如果用户向后导航到活动，则必须重新启动它，还原到其以前保存的状态，然后向用户显示。

### <a name="activity-re-creation-in-response-to-configuration-changes"></a>响应配置更改时的活动重新创建

为了使问题更复杂，Android 在混合环境中多次引发了一次扳手，称为配置更改。 配置更改是指在活动的配置发生更改时（例如，当设备[旋转](~/android/app-fundamentals/handling-rotation.md)时，以及在横向或纵向模式下重新生成活动需要重新生成活动）时的快速活动销毁/重新创建循环。将显示键盘（并且向活动提供一种调整其自身大小的机会），或将设备放置在停靠中以及其他设备。

配置更改仍会导致在停止和重启活动期间发生相同的活动状态更改。 但是，为了确保应用程序在配置更改期间感觉到响应并正常运行，必须尽快处理它们。 因此，Android 具有特定的 API，可用于在配置更改期间保持状态。
稍后我们将在[整个生命周期的 "管理状态](~/android/app-fundamentals/activity-lifecycle/index.md#Managing_State_Throughout_the_Lifecycle)" 部分介绍这一点。

### <a name="activity-lifecycle-methods"></a>活动生命周期方法

Android SDK 和扩展，Xamarin framework 提供了一个功能强大的模型，可用于管理应用程序内的活动状态。 当活动的状态发生变化时，操作系统会通知活动，这会对该活动调用特定的方法。 下图演示了与活动生命周期相关的这些方法：

[![活动生命周期流程图](images/image2-sml.png)](images/image2.png#lightbox)

作为开发人员，您可以通过在活动中重写这些方法来处理状态更改。 但请务必注意，所有生命周期方法都在 UI 线程上调用，并将阻止操作系统执行下一步的 UI 工作，如隐藏当前活动、显示新活动等。因此，这些方法中的代码应尽可能简短，以使应用程序感觉良好。 应在后台线程上执行任何长时间运行的任务。

让我们来看一下这些生命周期方法及其用法：

#### <a name="oncreate"></a>OnCreate

[OnCreate](xref:Android.App.Activity.OnCreate*)是创建活动时要调用的第一个方法。
始终会重写 `OnCreate`，以执行活动可能需要的任何启动初始化，如下所示：

- 创建视图
- 初始化变量
- 将静态数据绑定到列表

`OnCreate` 采用了一个[绑定](xref:Android.OS.Bundle)参数，该参数是一个用于存储和传递活动之间的状态信息和对象的字典（如果捆绑包不为 null），这表示活动正在重新启动，应从上一实例. 下面的代码演示如何从捆绑包中检索值：

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

`OnCreate` 完成后，Android 将调用 `OnStart`。

#### <a name="onstart"></a>OnStart

`OnCreate` 完成后，系统始终会调用[OnStart](xref:Android.App.Activity.OnStart) 。 如果活动在活动可见之前需要执行任何特定的任务，例如刷新活动中视图的当前值，则活动可能会重写此方法。 Android 将立即调用此方法后 `OnResume`。

#### <a name="onresume"></a>OnResume

当活动准备好开始与用户交互时，系统将调用[OnResume](xref:Android.App.Activity.OnResume) 。
活动应重写此方法以执行如下任务：

- 斜向上帧速率（游戏开发中的常见任务）
- 开始动画
- 侦听 GPS 更新
- 显示任何相关的警报或对话框
- 连接外部事件处理程序

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

`OnResume` 很重要，因为在 `OnPause` 中完成的任何操作都应在 `OnResume`中完成，因为这是在将活动恢复到生活中后，保证在 `OnPause` 后执行的唯一生命周期方法。

#### <a name="onpause"></a>OnPause

当系统要将活动放入背景中或活动被部分遮盖时，将调用[OnPause](xref:Android.App.Activity.OnPause) 。 如果活动需要，则应重写此方法：

- 提交永久性数据的未保存更改

- 销毁或清理使用资源的其他对象

- 增加帧速率和暂停动画

- 注销外部事件处理程序或通知处理程序（即绑定到服务的程序）。 必须执行此操作以防止活动内存泄露。

- 同样，如果活动显示了任何对话框或警报，则必须用 `.Dismiss()` 方法对其进行清理。

例如，下面的代码段将释放相机，因为活动不能在暂停时使用它：

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

`OnPause`后会调用两种可能的生命周期方法：

1. 如果活动将返回到前台，将调用 `OnResume`。
1. 如果活动位于背景中，将调用 `OnStop`。

#### <a name="onstop"></a>OnStop

当用户不再显示该活动时，将调用[OnStop](xref:Android.App.Activity.OnStop) 。 发生以下情况之一时，会发生这种情况：

- 正在启动新活动，并覆盖此活动。
- 现有活动将进入前台。
- 正在销毁活动。

在内存不足的情况下可能不会始终调用 `OnStop`，例如 Android 会耗尽资源，无法正确地为活动提供背景。 出于此原因，最好不要依赖于在准备用于销毁的活动时调用 `OnStop`。 如果活动即将离开，则可以在 `OnDestroy` 此操作之后调用的下一个生命周期方法，如果活动正在返回与用户交互，则为 `OnRestart`。

#### <a name="ondestroy"></a>OnDestroy

[OnDestroy](xref:Android.App.Activity.OnDestroy)是在将其销毁并从内存中完全删除之前，在活动实例上调用的最终方法。 在极端情况下，Android 可能会终止宿主活动的应用程序进程，这将导致 `OnDestroy` 不会被调用。 大多数活动将不会实现此方法，因为在 `OnPause` 和 `OnStop` 方法中，大多数清理和关闭操作都已完成。 通常会重写 `OnDestroy` 方法，以清理可能会泄漏资源的长时间运行的资源。 这种情况的一个示例可能是 `OnCreate`中启动的后台线程。

在活动被销毁后，不会调用任何生命周期方法。

#### <a name="onrestart"></a>OnRestart

当活动停止后，在重新启动活动之前，将调用[OnRestart](xref:Android.App.Activity.OnRestart) 。 例如，当用户在应用程序中的活动上按下 "主页" 按钮时，就会出现这种情况。 发生此情况时 `OnPause`，然后调用 `OnStop` 方法，并将活动移动到后台但不销毁。 如果用户随后使用任务管理器或类似的应用程序来还原应用程序，则 Android 将调用活动的 `OnRestart` 方法。

对于应在 `OnRestart`中实现的逻辑类型，没有一般的指导原则。 这是因为无论活动正在创建还是正在重新启动，都将始终调用 `OnStart`，因此应在 `OnStart`中（而不是 `OnRestart`）初始化活动所需的任何资源。

将 `OnStart``OnRestart` 之后调用的下一个生命周期方法。

### <a name="back-vs-home"></a>后退与 Home

许多 Android 设备具有两个不同的按钮： "后退" 按钮和 "主页" 按钮。 Android 4.0.3 的以下屏幕截图中显示了这种情况的示例：

[!["后退" 和 "主页" 按钮](images/image4-sml.png)](images/image4.png#lightbox)

这两个按钮之间存在细微的差异，尽管它们看起来与将应用程序放置在后台的效果相同。 当用户单击 "后退" 按钮时，他们会告诉 Android 它们已在活动中完成。 Android 会销毁活动。 相反，当用户单击 "主页" 按钮时，该活动仅置于后台 &ndash; Android 不会终止活动。

<a name="Managing_State_Throughout_the_Lifecycle" />

## <a name="managing-state-throughout-the-lifecycle"></a>管理整个生命周期中的状态

当活动停止或销毁时，系统将提供保存活动状态的机会，以便以后解除冻结。
此保存状态称为实例状态。 Android 提供三个用于在活动生命周期内存储实例状态的选项：

1. 将基元值存储在名为 Android 将用于保存状态的[捆绑](xref:Android.OS.Bundle)`Dictionary` 中。

1. 创建将包含复杂值（如位图）的自定义类。 Android 将使用此自定义类来保存状态。

1. 规避配置更改生命周期，并假设完全负责维护活动中的状态。

本指南介绍前两个选项。

### <a name="bundle-state"></a>绑定状态

保存实例状态的主要选项是使用称为[捆绑](xref:Android.OS.Bundle)的键/值字典对象。
请注意，在创建了将 `OnCreate` 方法传递到作为参数的捆绑的活动时，可以使用此绑定来还原实例状态。 对于更复杂的数据，不建议使用捆绑包，这些数据不会快速或轻松地序列化为键/值对（如位图）;相反，应将其用于简单的值（如字符串）。

活动提供一些方法来帮助在捆绑包中保存和检索实例状态：

- [OnSaveInstanceState](xref:Android.App.Activity.OnSaveInstanceState*) &ndash; 在销毁活动时，Android 会调用此方法。 如果活动需要保留任何键/值状态项，则这些活动可以实现此方法。

- [OnRestoreInstanceState](xref:Android.App.Activity.OnRestoreInstanceState*) &ndash; 在 `OnCreate` 方法完成后调用此方法，并且为活动提供了在初始化完成后恢复其状态的另一种机会。

下图演示了如何使用这些方法：

[![捆绑状态流程图](images/image3-sml.png)](images/image3.png#lightbox)

#### <a name="onsaveinstancestate"></a>OnSaveInstanceState

当活动正在停止时，将调用[OnSaveInstanceState](xref:Android.App.Activity.OnSaveInstanceState*) 。 它将接收一个捆绑参数，活动可在其中存储其状态。 当设备遇到配置更改时，活动可以使用传入的 `Bundle` 对象，通过重写 `OnSaveInstanceState`来保留活动状态。 例如，考虑以下代码：

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

当单击名为 `incrementCounter` 的按钮时，上面的代码会递增名为 `c` 的整数，并将结果显示在名为 `output`的 `TextView` 中。 发生配置更改时（例如，当设备旋转时），上面的代码将失去 `c` 的值，因为 `bundle` 将 `null`，如下图所示：

[![显示不显示以前的值](images/07-sml.png)](images/07.png#lightbox)

若要在此示例中保留 `c` 的值，活动可重写 `OnSaveInstanceState`，并保存包中的值，如下所示：

```csharp
protected override void OnSaveInstanceState (Bundle outState)
{
  outState.PutInt ("counter", c);
  base.OnSaveInstanceState (outState);
}
```

现在，当设备旋转到新方向时，该整数将保存在绑定中，并使用以下行进行检索：

```csharp
c = bundle.GetInt ("counter", -1);
```

> [!NOTE]
> 务必始终调用 `OnSaveInstanceState` 的基实现，以便也可以保存视图层次结构的状态。

##### <a name="view-state"></a>视图状态

覆盖 `OnSaveInstanceState` 是一种适当的机制，用于在不同方向的更改（例如上述示例中的计数器）中保存活动的暂时性数据。 但是，只要每个视图都分配了 ID，`OnSaveInstanceState` 的默认实现将负责在 UI 中为每个视图保存暂时性数据。 例如，假设某个应用程序在 XML 中定义了一个 `EditText` 元素，如下所示：

```xml
<EditText android:id="@+id/myText"
  android:layout_width="fill_parent"
  android:layout_height="wrap_content"/>
```

由于 `EditText` 控件分配了 `id`，因此当用户输入某些数据并旋转设备时，仍会显示数据，如下所示：

[在横向模式下保留![数据](images/08-sml.png)](images/08.png#lightbox)

#### <a name="onrestoreinstancestate"></a>OnRestoreInstanceState

`OnStart`后将调用[OnRestoreInstanceState](xref:Android.App.Activity.OnRestoreInstanceState*) 。 它为活动提供了在以前的 `OnSaveInstanceState`期间还原以前保存到捆绑的任何状态的机会。 不过，这是为 `OnCreate`提供的相同捆绑包。

下面的代码演示如何在 `OnRestoreInstanceState`中还原状态：

```csharp
protected override void OnRestoreInstanceState(Bundle savedState)
{
    base.OnRestoreSaveInstanceState(savedState);
    var myString = savedState.GetString("myString");
    var myBool = savedState.GetBoolean("myBool");
}
```

此方法用于在应还原状态时提供一些灵活性。 有时，更适合等待所有初始化完成后再还原实例状态。 此外，现有活动的子类可能只需要从实例状态还原某些值。 在许多情况下，不需要重写 `OnRestoreInstanceState`，因为大多数活动都可以使用提供给 `OnCreate`的捆绑来还原状态。

有关使用 `Bundle`保存状态的示例，请参阅[演练-保存活动状态](saving-state.md)。

#### <a name="bundle-limitations"></a>绑定限制

尽管 `OnSaveInstanceState` 可以轻松地保存暂时性数据，但有一些限制：

- 在所有情况下均不会调用它。 例如，按**Home**或**Back**退出活动不会导致调用 `OnSaveInstanceState`。

- 传递给 `OnSaveInstanceState` 的绑定不是针对大型对象（如图像）设计的。 对于大型对象，如下面所述，保存[OnRetainNonConfigurationInstance](xref:Android.App.Activity.OnRetainNonConfigurationInstance)中的对象更可取。

- 使用捆绑包保存的数据将被序列化，这可能会导致延迟。

绑定状态对于不使用大量内存的简单数据很有用，而*非配置实例数据*对于更复杂的数据或检索开销很大的数据（如从 web 服务调用或复杂的数据库查询）很有用。 非配置实例数据将根据需要保存在对象中。 下一节介绍 `OnRetainNonConfigurationInstance` 作为一种通过配置更改来保留更复杂的数据类型的方式。

### <a name="persisting-complex-data"></a>保留复杂数据

除了保留捆绑中的数据以外，Android 还支持通过重写[OnRetainNonConfigurationInstance](xref:Android.App.Activity.OnRetainNonConfigurationInstance)并返回包含要保存的数据的 `Java.Lang.Object` 的实例来保存数据。 使用 `OnRetainNonConfigurationInstance` 保存状态有两个主要优点：

- 从 `OnRetainNonConfigurationInstance` 返回的对象非常适合更大、更复杂的数据类型，因为内存会保留此对象。

- `OnRetainNonConfigurationInstance` 方法将按需调用，并且仅在需要时调用。 这比使用手动缓存更经济。

使用 `OnRetainNonConfigurationInstance` 适用于多次检索数据（如在 web 服务调用中）的情况。 例如，请考虑以下搜索 Twitter 的代码：

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

此代码从 web 中检索格式为 JSON 的结果，对其进行分析，然后将结果显示在列表中，如以下屏幕截图所示：

[屏幕上显示的![结果](images/06-sml.png)](images/06.png#lightbox)

发生配置更改时（例如，当设备旋转时），代码将重复该过程。 若要重用最初检索到的结果，而不会导致不必要的冗余网络调用，可以使用 `OnRetainNonconfigurationInstance` 来保存结果，如下所示：

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

现在，当设备旋转时，将从 `LastNonConfiguartionInstance` 属性检索原始结果。 在此示例中，结果包含一个包含推文的 `string[]`。 由于 `OnRetainNonConfigurationInstance` 要求返回 `Java.Lang.Object`，因此 `string[]` 包装在子类 `Java.Lang.Object`的类中，如下所示：

```csharp
class TweetListWrapper : Java.Lang.Object
{
  public string[] Tweets { get; set; }
}
```

例如，尝试使用 `TextView` 作为从 `OnRetainNonConfigurationInstance` 返回的对象将会泄漏活动，如下面的代码所示：

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

在本部分中，我们学习了如何在 `Bundle`中保留简单状态数据，并将更复杂的数据类型与 `OnRetainNonConfigurationInstance`保存在一起。

## <a name="summary"></a>总结

Android 活动生命周期提供了一个功能强大的框架，可用于对应用程序中的活动进行状态管理，但这可能对理解和实现非常棘手。 本章介绍活动在其生存期内可能经历的不同状态，以及与这些状态关联的生命周期方法。 接下来，提供有关应在每个方法中执行的逻辑类型的指南。

## <a name="related-links"></a>相关链接

- [处理旋转](~/android/app-fundamentals/handling-rotation.md)
- [Android 活动](xref:Android.App.Activity)
