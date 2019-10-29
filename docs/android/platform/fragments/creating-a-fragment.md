---
title: 创建片段
ms.prod: xamarin
ms.assetid: F2997242-BC29-1440-7F1A-CFC447CD73FA
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/07/2018
ms.openlocfilehash: 0e8d3748c7ddd337cf2f27f5b272b208e79d503a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027507"
---
# <a name="creating-a-fragment"></a>创建片段

若要创建片段，类必须从 `Android.App.Fragment` 继承，然后重写 `OnCreateView` 方法。 当需要将片段置于屏幕上并返回 `View`时，宿主活动将调用 `OnCreateView`。 典型 `OnCreateView` 将通过因为这样做布局文件并将其附加到父容器来创建此 `View`。 容器的特征非常重要，因为 Android 会将父对象的布局参数应用于片段的 UI。 下面的示例阐释了这一点：

```csharp
public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
{
    return inflater.Inflate(Resource.Layout.Example_Fragment, container, false);
}
```

上面的代码将 `Resource.Layout.Example_Fragment`视图中，并将其作为子视图添加到 `ViewGroup` 容器中。

> [!NOTE]
> 片段子类必须有一个公共的默认值构造函数。

## <a name="adding-a-fragment-to-an-activity"></a>向活动添加片段

可以通过两种方法在活动中承载片段：

- **以声明的方式**&ndash; 可以使用 `<Fragment>` 标记在 `.axml` 布局文件中以声明方式使用片段。

- 还可以通过使用 `FragmentManager` 类的 API，**以编程方式**动态地 &ndash; 片段。

本指南稍后将介绍如何通过 `FragmentManager` 类进行编程使用。

### <a name="using-a-fragment-declaratively"></a>以声明方式使用片段

在布局中添加片段需要使用 `<fragment>` 标记，然后通过提供 `class` 特性或 `android:name` 特性来标识片段。 以下代码片段演示如何使用 `class` 特性来声明 `fragment`：

```xml
<?xml version="1.0" encoding="utf-8"?>
<fragment class="com.xamarin.sample.fragments.TitlesFragment"
            android:id="@+id/titles_fragment"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent" />
```

下面的代码片段演示如何使用 `android:name` 属性标识片段类来声明 `fragment`：

```xml
<?xml version="1.0" encoding="utf-8"?>
<fragment android:name="com.xamarin.sample.fragments.TitlesFragment"
            android:id="@+id/titles_fragment"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent" />
```

创建活动时，Android 将实例化布局文件中指定的每个片段，并插入从 `OnCreateView` 创建的、用于替代 `Fragment` 元素的视图。
以声明方式添加到活动中的片段是静态的，将保留在活动上，直到它被销毁;无法在其附加到的活动的生存期内动态替换或删除此类片段。

必须为每个片段分配一个唯一的标识符：

- **android： id** &ndash; 与布局文件中的其他 UI 元素相同，这是唯一的 id。

- **android：标记**&ndash; 此属性是一个唯一字符串。

如果前面两种方法都不使用，则该片段将采用容器视图的 ID。 在以下示例中，`android:id` 和 `android:tag` 均未提供，Android 会将 ID `fragment_container` 分配给片段：

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
                android:id="+@id/fragment_container"
                android:orientation="horizontal"
                android:layout_width="match_parent"
                android:layout_height="match_parent">

        <fragment class="com.example.android.apis.app.TitlesFragment"
                android:layout_width="match_parent"
                android:layout_height="match_parent" />
</LinearLayout>
```

### <a name="package-name-case"></a>包名称大小写

Android 不允许包名称中有大写字符;如果包名称包含大写字符，则在尝试显示视图时会引发异常。 但是，Xamarin 更包容性，并将允许命名空间中的大写字符。

例如，以下两个代码段都适用于 Xamarin。 但是，第二个代码片段将导致 `android.view.InflateException` 由基于 Java 的纯 Android 应用程序引发。

```xml
<fragment class="com.example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```

要么

```xml
<fragment class="Com.Example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```

## <a name="fragment-lifecycle"></a>片段生命周期

片段具有其自己的生命周期，该生命周期在某种程度上独立，但仍受[承载活动的生命周期](~/android/app-fundamentals/activity-lifecycle/index.md)影响。
例如，当活动暂停时，它的所有关联片段都将暂停。 下图概述了片段的生命周期。

[说明片段生命周期的![流程图](creating-a-fragment-images/fragment-lifecycle.png)](creating-a-fragment-images/fragment-lifecycle.png#lightbox)

### <a name="fragment-creation-lifecycle-methods"></a>片段创建生命周期方法

下面的列表显示了在创建片段的生命周期中的各种回调的流：

- 在将片段创建为视图布局的一部分时调用 **`OnInflate()`** &ndash;。 从 XML 布局文件以声明方式创建片段后，可以立即调用此代码。 该片段尚未与其活动相关联，但该视图层次结构中的**活动**、**绑定**和**AttributeSet**作为参数传入。 此方法最适合用于分析**AttributeSet**并保存片段稍后可能使用的属性。

- 在片段与活动关联后调用 **`OnAttach()`** &ndash;。 这是段准备就绪时要运行的第一个方法。 通常情况下，片段不应实现构造函数或重写默认构造函数。 应在此方法中初始化片段所需的任何组件。

- 活动 **`OnCreate()`** 调用 &ndash; 创建片段。 如果调用此方法，则宿主活动的视图层次结构可能不会完全实例化，因此，片段不应依赖于活动的视图层次结构的任何部分，直到稍后在片段的生命周期中。 例如，不要使用此方法对应用程序的 UI 执行任何调整或调整操作。 这是片段开始收集所需数据的最早时间。 此时，片段会在 UI 线程中运行，因此应避免任何冗长的处理，或在后台线程上执行该处理。 如果调用**SetRetainInstance （true）** ，则可以跳过此方法。
    下面将更详细地介绍此替代方法。

- **`OnCreateView()`** &ndash; 创建片段的视图。
    在活动的**OnCreate （）** 方法完成后，将调用此方法。 此时，可以安全地与活动的视图层次结构进行交互。 此方法应返回将由片段使用的视图。

- **OnCreate**已由宿主活动完成后调用 **`OnActivityCreated()`** &ndash;。
    此时应对用户界面执行最后调整。

- 在包含活动恢复后调用 **`OnStart()`** &ndash;。 这使片段对用户可见。 在许多情况下，片段将包含活动的**OnStart （）** 方法中的代码。

- **`OnResume()`** &ndash; 这是用户可以与片段交互之前调用的最后一个方法。 在此方法中应执行的代码类型的一个示例是，将启用用户可与之交互的设备功能，如定位服务的照相机。 但这类服务可能会导致电池消耗过多，并且应用程序应最大限度地降低电池电量。

### <a name="fragment-destruction-lifecycle-methods"></a>片段析构生命周期方法

接下来的列表说明作为片段销毁的生命周期方法：

- **`OnPause()`** &ndash; 用户无法再与片段交互。 出现这种情况的原因是其他一些片段操作正在修改此片段，或暂停宿主活动。 承载此片段的活动可能仍是可见的，也就是说，焦点上的活动是部分透明的，或者不会占据整个屏幕。 此方法变为活动状态时，这是用户离开该片段的第一个指示。 片段应保存所有更改。

- 片段 &ndash; **`OnStop()`** 不再可见。 主机活动可能已停止，或者片段操作正在活动中修改它。 此回调的作用与 Activity 的**行为**相同。

- **`OnDestroyView()`** &ndash; 调用此方法以清理与视图关联的资源。 当已销毁与片段关联的视图时，将调用此代码。

- **`OnDestroy()`** &ndash; 在不再使用片段时调用此方法。 它仍与活动相关联，但片段不再正常工作。 此方法应释放由片段使用的任何资源，例如可用于照相机的[**SurfaceView**](xref:Android.Views.SurfaceView) 。 如果调用**SetRetainInstance （true）** ，则可以跳过此方法。 下面将更详细地介绍此替代方法。

- **`OnDetach()`** &ndash; 此方法只在片段不再与活动关联之前被调用。 片段的视图层次结构不再存在，此时应释放该片段使用的所有资源。

### <a name="using-setretaininstance"></a>使用 SetRetainInstance

片段可以指定在重新创建活动时，不应完全销毁它。 `Fragment` 类为此目的提供了方法 `SetRetainInstance`。 如果将 `true` 传递给此方法，则在重新启动该活动时，将使用该片段的同一个实例。 如果发生这种情况，则将调用所有回调方法，但 `OnCreate` 和 `OnDestroy` 生命周期回调除外。 上面显示的生命周期关系图中显示了此过程（由绿色虚线表示）。

## <a name="fragment-state-management"></a>片段状态管理

片段可以通过使用 `Bundle`实例，在片段生命周期中保存和还原其状态。 绑定允许片段将数据另存为键/值对，对不需要大量内存的简单数据非常有用。 片段可以通过调用 `OnSaveInstanceState`来保存其状态：

```csharp
public override void OnSaveInstanceState(Bundle outState)
{
    base.OnSaveInstanceState(outState);
    outState.PutInt("current_choice", _currentCheckPosition);
}
```

创建片段的新实例时，保存在 `Bundle` 中的状态将通过新实例的 `OnCreate`、`OnCreateView`和 `OnActivityCreated` 方法提供给新的实例。
下面的示例演示如何从 `Bundle`中检索 `current_choice` 值：

```csharp
public override void OnActivityCreated(Bundle savedInstanceState)
{
    base.OnActivityCreated(savedInstanceState);
    if (savedInstanceState != null)
    {
        _currentCheckPosition = savedInstanceState.GetInt("current_choice", 0);
    }
}
```

重写 `OnSaveInstanceState` 是一种适当的机制，用于在不同方向的更改中保存临时数据，如以上示例中的 `current_choice` 值。 不过，`OnSaveInstanceState` 的默认实现将负责为分配有 ID 的每个视图在 UI 中保存暂时性数据。 例如，查看在 XML 中定义了 `EditText` 元素的应用程序，如下所示：

```xml
<EditText android:id="@+id/myText"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"/>
```

由于 `EditText` 控件分配了 `id`，因此在调用 `OnSaveInstanceState` 时，片段会自动将数据保存在小组件中。

### <a name="bundle-limitations"></a>绑定限制

尽管使用 `OnSaveInstanceState` 可以轻松地保存暂时性数据，但使用此方法存在一些限制：

- 如果未将该片段添加到 back 堆栈，则当用户按 "**后退**" 按钮时，不会还原其状态。

- 当使用绑定保存数据时，将序列化数据。 这可能导致处理延迟。

## <a name="contributing-to-the-menu"></a>参与菜单

片段可以向宿主活动的菜单提供项。
活动处理菜单项。 如果活动没有处理程序，则会将事件传递给片段，然后将处理该事件。

若要向活动的菜单添加项，片段必须执行两项操作。
首先，片段必须实现 `OnCreateOptionsMenu` 方法，并将其项置于菜单中，如下面的代码所示：

```csharp
public override void OnCreateOptionsMenu(IMenu menu, MenuInflater menuInflater)
{
    menuInflater.Inflate(Resource.Menu.menu_fragment_vehicle_list, menu);
    base.OnCreateOptionsMenu(menu, menuInflater);
}
```

前面代码段中的菜单从以下 XML 中夸大，位于文件 `menu_fragment_vehicle_list.xml`：

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:id="@+id/add_vehicle"
        android:icon="@drawable/ic_menu_add_data"
        android:title="@string/add_vehicle" />
</menu>
```

接下来，片段必须调用 `SetHasOptionsMenu(true)`。 对此方法的调用向 Android 公布了一个片段，该片段包含用于选项菜单的菜单项。 除非对此方法进行了调用，否则不会将该片段的菜单项添加到活动的选项菜单中。 这通常在 `OnCreate()`的生命周期方法中完成，如下面的代码片段所示：

```csharp
public override void OnCreate(Bundle savedState)
{
    base.OnCreate(savedState);
    SetHasOptionsMenu(true);
}
```

以下屏幕显示了此菜单的外观：

[显示菜单项的 "我的应用" 应用的示例屏幕截图![](creating-a-fragment-images/fragment-menu-example.png)](creating-a-fragment-images/fragment-menu-example.png#lightbox)
