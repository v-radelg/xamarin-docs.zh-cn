---
title: 创建片段
ms.prod: xamarin
ms.assetid: F2997242-BC29-1440-7F1A-CFC447CD73FA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/07/2018
ms.openlocfilehash: 339de4930242e35c40b034af2ce6ba47fe1543af
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61023652"
---
# <a name="creating-a-fragment"></a>创建片段

若要创建一个片段，类必须继承`Android.App.Fragment`，然后重写`OnCreateView`方法。 `OnCreateView` 它是时候将片段放在屏幕上，并将返回时将调用由托管活动`View`。 典型`OnCreateView`将创建此`View`由以下布局文件，然后将其附加到父容器。 容器的特征是父的非常重要，因为 Android 将应用于该片段的 UI 布局参数。 下面的示例阐释了这一点：

```csharp
public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
{
    return inflater.Inflate(Resource.Layout.Example_Fragment, container, false);
}
```

上面的代码将放在视图大量`Resource.Layout.Example_Fragment`，并将其添加到的子视图作为`ViewGroup`容器。


> [!NOTE]
> 片段类必须具有公共默认无参数构造函数。

## <a name="adding-a-fragment-to-an-activity"></a>将片段添加到活动

有两种方法可以托管一个片段活动内部的：

-   **以声明方式**&ndash;可以以声明方式中使用片段`.axml`使用的布局文件`<Fragment>`标记。

-   **以编程方式**&ndash;片段可以还实例化动态使用`FragmentManager`类的 API。

通过以编程方式使用`FragmentManager`类将本指南后面所述。

### <a name="using-a-fragment-declaratively"></a>以声明方式使用段

添加布局内的一个片段需要使用`<fragment>`标记，然后通过提供可以确定片段`class`属性或`android:name`属性。 以下代码片段演示如何使用`class`属性来声明`fragment`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<fragment class="com.xamarin.sample.fragments.TitlesFragment"
            android:id="@+id/titles_fragment"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent" />
```

此下一个代码段演示了如何声明`fragment`通过使用`android:name`属性来标识片段类：

```xml
<?xml version="1.0" encoding="utf-8"?>
<fragment android:name="com.xamarin.sample.fragments.TitlesFragment"
            android:id="@+id/titles_fragment"
            android:layout_width="fill_parent"
            android:layout_height="fill_parent" />
```

Android 创建活动后，将实例化布局文件中指定每个片段和插入从创建该视图`OnCreateView`来代替`Fragment`元素。
以声明方式添加到活动的片段是静态的将保留在活动中，直到销毁;不能动态替换或删除附加到的活动的生存期内的片段。

每个片段必须分配有唯一标识符：

-  **android: id** &ndash;因为使用布局文件中的其他 UI 元素，这是唯一的 id。

-  **android： 标记**&ndash;此属性是唯一的字符串。

如果上述两种方法都不使用时，该片段将假定容器视图的 ID。 在下面的示例其中既不`android:id`也不`android:tag`提供 Android 将向分配 ID`fragment_container`到段：

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

Android 不允许在包名称; 中的大写字符尝试放大量视图，如果包名称包含大写字符时，它将引发异常。 但是，Xamarin.Android 内更能容忍，并且将容忍在命名空间中的大写字符。

例如，这两个以下的代码片段将使用 Xamarin.Android。 但是，第二个代码段将导致`android.view.InflateException`要引发的纯基于 Java 的 Android 应用程序。

```xml
<fragment class="com.example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```

或

```xml
<fragment class="Com.Example.DetailsFragment" android:id="@+id/fragment_content" android:layout_width="match_parent" android:layout_height="match_parent" />
```


## <a name="fragment-lifecycle"></a>片段生命周期

片段具有一定程度上独立的但仍受其自身生命周期[托管的活动的生命周期](~/android/app-fundamentals/activity-lifecycle/index.md)。
例如，当活动暂停，所有其关联的片段将暂停。 下图概述了片段的生命周期。

[![说明片段生命周期的数据流关系图](creating-a-fragment-images/fragment-lifecycle.png)](creating-a-fragment-images/fragment-lifecycle.png#lightbox)


### <a name="fragment-creation-lifecycle-methods"></a>片段创建生命周期方法

下面列出了各种回调的流中片段的生命周期，在创建时：

-   **`OnInflate()`** &ndash; 片段正在创建的视图布局的一部分时调用。 这不能从 XML 布局文件以声明方式创建该片段后立即调用。 片段不是其活动与相关联，但**活动**，**捆绑**，并**AttributeSet**从视图层次结构中作为参数传递。 此方法最适合用于分析**AttributeSet**和保存属性可能会使用更高版本的片段。

-   **`OnAttach()`** &ndash; 片段是与活动关联后调用。 这是该片段是可供使用时要运行的第一个方法。 一般情况下，片段不应实现一个构造函数或重写默认构造函数。 应在此方法中初始化所需的片段的任何组件。

-   **`OnCreate()`** &ndash; 要创建片段的活动由调用。 调用此方法时，托管活动的视图层次结构可能不是完全实例化，因此该片段不应依赖直到更高版本上的片段的生命周期中的活动的视图层次结构的任何部分。 例如，不要使用此方法以执行任何调整或应用程序用户界面调整。 这是片段可能会开始收集数据所需的最早时间。 片段现在运行在 UI 线程中，因此避免任何耗时较长的处理，或在后台线程上执行该处理。 此方法可能会跳过，如果**SetRetainInstance(true)** 调用。
    将下面更详细地介绍此替代方法。

-   **`OnCreateView()`** &ndash; 创建片段的视图。
    此方法调用一次活动的**OnCreate()** 方法已完成。 此时，它是安全地与该活动的视图层次结构进行交互。 此方法应返回将由该片段的视图。

-   **`OnActivityCreated()`** &ndash; 之后调用**Activity.OnCreate**由托管活动已完成。
    此时应执行最终用户界面的调整。

-   **`OnStart()`** &ndash; 包含活动已恢复后调用。 这使得该片段对用户可见。 在许多情况下，片段将包含代码，否则会采用**onstart （)** 活动的方法。

-   **`OnResume()`** &ndash; 这是最后一个方法调用之前用户可以进行交互的片段。 应在此方法中执行的代码的类的一个示例将启用功能的设备的用户可能会与进行交互，如照相机的位置服务。 不过，这些服务可能会导致过多的电池耗尽，并应用程序应尽可能少地使用以保持电池寿命。


### <a name="fragment-destruction-lifecycle-methods"></a>片段析构生命周期方法

下一步的列表说明了调用正在销毁片段的生命周期方法：

-   **`OnPause()`** &ndash; 用户不再能够与片段进行交互。 这种情况存在由于某个其他片段操作修改此代码段，或托管活动已暂停。 就可以托管此片段的活动可能仍是可见、 焦点中的活动，即部分透明的或未占据整个屏幕。 当此方法变为活动状态时，它是用户将离开该片段的第一个指示。 片段应保存任何更改。

-   **`OnStop()`** &ndash; 片段不再可见。 主机活动可能会停止，或片段操作在活动中修改它。 此回调提供相同的目的**Activity.OnStop**。

-   **`OnDestroyView()`** &ndash; 调用此方法来清理与视图关联的资源。 与片断关联的视图已被销毁时调用。

-   **`OnDestroy()`** &ndash; 不再使用该片段时，调用此方法。 仍然与活动相关联，但该片段不再起作用。 此方法应释放任何资源使用的片段，如[ **SurfaceView** ](https://developer.xamarin.com/api/type/Android.Views.SurfaceView/)可能用于相机。 此方法可能会跳过，如果**SetRetainInstance(true)** 调用。 将下面更详细地介绍此替代方法。

-   **`OnDetach()`** &ndash; 该片段将不再与活动关联之前，调用此方法。 片段的视图层次结构不再存在，并且应在此时释放片断所使用的所有资源。


### <a name="using-setretaininstance"></a>使用 SetRetainInstance

很可能要指定，它不应完全销毁活动正被重新创建的片段。 `Fragment`类提供了方法`SetRetainInstance`实现此目的。 如果`true`传递给此方法，则重新启动该活动时，将使用该片段的同一个实例。 如果发生这种情况，则所有回叫方法将调用除外`OnCreate`和`OnDestroy`生命周期的回调。 在上面所示 （以绿色的点线） 生命周期关系图中阐释了此流程。


## <a name="fragment-state-management"></a>片断状态管理

片段可能保存和片段生命周期内使用的实例还原其状态`Bundle`。 此捆绑包允许将数据保存为键/值对的片段和适用于的简单数据，无需太多内存。 片段可以保存其状态，通过调用`OnSaveInstanceState`:

```csharp
public override void OnSaveInstanceState(Bundle outState)
{
    base.OnSaveInstanceState(outState);
    outState.PutInt("current_choice", _currentCheckPosition);
}
```

当创建片段的新实例，在保存的状态`Bundle`将变为可用的新实例通过`OnCreate`， `OnCreateView`，和`OnActivityCreated`方法的新实例。
下面的示例演示如何检索的值`current_choice`从`Bundle`:

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

重写`OnSaveInstanceState`是用于保存暂时性数据片段中跨方向更改，如适当机制`current_choice`上述示例中的值。 但是的默认实现`OnSaveInstanceState`负责的暂时性数据保存在分配的 id 的每个视图的 UI。 例如，查看应用程序具有`EditText`元素在 XML 中定义，如下所示：

```xml
<EditText android:id="@+id/myText"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"/>
```

由于`EditText`控件具有`id`分配，片段会自动将数据保存在小组件时`OnSaveInstanceState`调用。


### <a name="bundle-limitations"></a>捆绑包限制

尽管使用`OnSaveInstanceState`使我们可以很容易保存暂时性数据，使用此方法具有一些限制：

-  如果该片段不会添加到 back 堆栈中，则当用户按下时，其状态将不会还原**回**按钮。

-  当绑定用于保存数据时，该数据序列化。 这可能会导致处理延迟。


## <a name="contributing-to-the-menu"></a>参与到菜单

片段可能会导致其托管活动的菜单项。
活动首次处理菜单项。 如果活动没有处理程序，然后该事件将传递到段，然后将处理。

若要添加到活动的菜单项，片段必须做两件事。
首先，该片段必须实现的方法`OnCreateOptionsMenu`并将其项放置到菜单中，如下面的代码中所示：

```csharp
public override void OnCreateOptionsMenu(IMenu menu, MenuInflater menuInflater)
{
    menuInflater.Inflate(Resource.Menu.menu_fragment_vehicle_list, menu);
    base.OnCreateOptionsMenu(menu, menuInflater);
}
```

在前面的代码片段菜单使从位于文件中的以下 XML `menu_fragment_vehicle_list.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:id="@+id/add_vehicle"
        android:icon="@drawable/ic_menu_add_data"
        android:title="@string/add_vehicle" />
</menu>
```

接下来，必须调用片段`SetHasOptionsMenu(true)`。 调用此方法向 Android 通知片段具有可用于参与选项菜单的菜单项。 除非对此方法的调用，该片段的菜单项将不添加到活动的选项菜单。 这通常是在生命周期方法`OnCreate()`下, 一步的代码片段中所示：

```csharp
public override void OnCreate(Bundle savedState)
{
    base.OnCreate(savedState);
    SetHasOptionsMenu(true);
}
```

以下屏幕显示了此菜单的外观：

[![我行程应用显示菜单项的示例屏幕截图](creating-a-fragment-images/fragment-menu-example.png)](creating-a-fragment-images/fragment-menu-example.png#lightbox)
