---
title: 带视图的 ViewPager
description: ViewPager 是一个布局管理器，可用于实现 gestural 导航。 Gestural 导航允许用户向左和向右轻扫以逐步浏览数据页。 本指南说明如何使用 ViewPager 和 PagerTabStrip 实现 swipeable UI，并使用视图作为数据页（后续指南介绍了如何使用页面片段）。
ms.prod: xamarin
ms.assetid: 42E5379F-B0F4-4B87-A314-BF3DE405B0C8
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: c913f18e34f93e9ab7adc09109ea5c9e9e5067a2
ms.sourcegitcommit: 4691b48f14b166afcec69d1350b769ff5bf8c9f6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2020
ms.locfileid: "75728143"
---
# <a name="viewpager-with-views"></a>带视图的 ViewPager

_ViewPager 是一个布局管理器，可用于实现 gestural 导航。Gestural 导航允许用户向左和向右轻扫以逐步浏览数据页。本指南说明如何使用 ViewPager 和 PagerTabStrip 实现 swipeable UI，并使用视图作为数据页（后续指南介绍了如何使用页面片段）。_

## <a name="overview"></a>概述

本指南是一个演练，其中提供了如何使用 `ViewPager` 实现落叶和长时间树的图像库的分步演示。 在此应用中，用户通过 "树目录" 向左和向右 swipes 查看树图像。 在目录的每一页的顶部，树的名称将在`PagerTabStrip`中列出，并且树的图像将显示在 `ImageView`中。 适配器用于将 `ViewPager` 接口到基础数据模型。 此应用实现从 `PagerAdapter`派生的适配器。 

尽管基于 `ViewPager`的应用通常是使用 `Fragment`实现的，但也有一些相对简单的用例，`Fragment`不需要额外的复杂性。 例如，本演练中所示的基本映像库应用不需要使用 `Fragment`。 因为内容是静态的，并且用户仅在不同的图像之间来回 swipes，所以可以通过使用标准 Android 视图和布局来使实现更加简单。 

## <a name="start-an-app-project"></a>启动应用项目

创建名为**TreePager**的新 Android 项目（有关创建新 android 项目的详细信息，请参阅[Hello、Android](~/android/get-started/hello-android/hello-android-quickstart.md) ）。 接下来，启动 NuGet 包管理器。 （有关安装 NuGet 包的详细信息，请参阅[演练：在项目中包括 NuGet](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)）。 查找并安装**Android 支持库 v4**： 

[在 NuGet 包管理器中选择的支持 v4 NuGet ![屏幕截图](viewpager-and-views-images/01-install-support-lib-sml.png)](viewpager-and-views-images/01-install-support-lib.png#lightbox)

这也会安装**Android 支持库 v4**reaquired 的任何其他包。

## <a name="add-an-example-data-source"></a>添加示例数据源

在此示例中，目录数据源（由 `TreeCatalog` 类表示）提供了具有项内容的 `ViewPager`。 
`TreeCatalog` 包含一组现成的树映像和树标题，适配器将使用该集合创建 `View`。 `TreeCatalog` 构造函数不需要任何参数：

```csharp
TreeCatalog treeCatalog = new TreeCatalog();
```

`TreeCatalog` 中的图像集合进行了组织，以便索引器可以访问每个图像。 例如，以下代码行检索集合中第三个图像的图像资源 ID： 

```csharp
int imageId = treeCatalog[2].imageId;
```

由于 `TreeCatalog` 的实现细节与理解 `ViewPager`无关，`TreeCatalog` 代码未在此处列出。 [TreeCatalog.cs](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/TreePager/TreePager/TreeCatalog.cs)上提供了要 `TreeCatalog` 的源代码。 下载此源文件（或将代码复制并粘贴到新的**TreeCatalog.cs**文件中），并将其添加到项目。 同时，将[图像文件](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/TreePager/Resources/tree-images.zip?raw=true)下载并解压缩到**资源/可绘制**文件夹中，并将其包含在项目中。 

## <a name="create-a-viewpager-layout"></a>创建 ViewPager 布局

打开**Resources/layout/main.axml** ，并将其内容替换为以下 XML：

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.view.ViewPager
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/viewpager"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

</android.support.v4.view.ViewPager>

```csharp
This XML defines a `ViewPager` that occupies the entire screen. Note that
you must use the fully-qualified name **android.support.v4.view.ViewPager**
because `ViewPager` is packaged in a support library. `ViewPager` is
available only from 
[Android Support Library v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/);
it is not available in the Android SDK. 

## Set up ViewPager

Edit **MainActivity.cs** and add the following `using` statement:

```csharp
using Android.Support.V4.View;
```

将 `OnCreate` 方法替换为以下代码：

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);
    ViewPager viewPager = FindViewById<ViewPager>(Resource.Id.viewpager);
    TreeCatalog treeCatalog = new TreeCatalog();
}
```

此代码将执行以下操作：

1. 设置**main.axml**布局资源中的视图。

2. 从布局中检索对 `ViewPager` 的引用。

3. 实例化一个新的 `TreeCatalog` 作为数据源。

生成并运行此代码时，应会看到类似于以下屏幕截图的显示： 

[显示空 ViewPager 应用程序的 ![屏幕快照](viewpager-and-views-images/02-initial-screen-sml.png)](viewpager-and-views-images/02-initial-screen.png#lightbox)

此时，`ViewPager` 为空，因为它缺少用于访问**TreeCatalog**中的内容的适配器。 在下一部分中，将创建一个**PagerAdapter**以将 `ViewPager` 连接到**TreeCatalog**。 

## <a name="create-the-adapter"></a>创建适配器

`ViewPager` 使用位于 `ViewPager` 与数据源之间的适配器控制器对象（请参阅[适配器](~/android/user-interface/controls/view-pager/index.md#adapter)中的图示）。 为了访问此数据，`ViewPager` 要求你提供从 `PagerAdapter`派生的自定义适配器。 此适配器用数据源中的内容填充每个 `ViewPager` 页面。 由于此数据源是特定于应用的，因此自定义适配器是了解如何访问数据的代码。 当用户 swipes 页面上的 `ViewPager`时，适配器将从数据源中提取信息，并将其加载到页面中，以供 `ViewPager` 显示。 

实现 `PagerAdapter`时，必须重写以下内容：

- **InstantiateItem** &ndash; 为指定位置创建页面（`View`），并将其添加到 `ViewPager`的视图集合。 

- **DestroyItem** &ndash; 从给定位置删除页面。

- **计数**&ndash; 只读属性，该属性返回可用的视图（页）数。 

- **IsViewFromObject** &ndash; 确定页面是否与特定的密钥对象相关联。 （此对象由 `InstantiateItem` 方法创建。）在此示例中，密钥对象是 `TreeCatalog` 的数据对象。

添加一个名为**TreePagerAdapter.cs**的新文件，并将其内容替换为以下代码： 

```csharp
using System;
using Android.App;
using Android.Runtime;
using Android.Content;
using Android.Views;
using Android.Widget;
using Android.Support.V4.View;
using Java.Lang;

namespace TreePager
{
    class TreePagerAdapter : PagerAdapter
    {
        public override int Count
        {
            get { throw new NotImplementedException(); }
        }

        public override bool IsViewFromObject(View view, Java.Lang.Object obj)
        {
            throw new NotImplementedException();
        }

        public override Java.Lang.Object InstantiateItem (View container, int position)
        {
            throw new NotImplementedException();
        }

        public override void DestroyItem(View container, int position, Java.Lang.Object view)
        {
            throw new NotImplementedException();
        }
    }
}
```

此代码会将必要的基本 `PagerAdapter` 实现。 在以下部分中，这些方法中的每一个都被替换为工作代码。 

### <a name="implement-the-constructor"></a>实现构造函数

当应用程序实例化 `TreePagerAdapter`时，它将提供上下文（`MainActivity`）和实例化的 `TreeCatalog`。 在**TreePagerAdapter.cs**中，将以下成员变量和构造函数添加到 `TreePagerAdapter` 类的顶部： 

```csharp
Context context;
TreeCatalog treeCatalog;

public TreePagerAdapter (Context context, TreeCatalog treeCatalog)
{
    this.context = context;
    this.treeCatalog = treeCatalog;
}
```

此构造函数的目的是将上下文和 `TreePagerAdapter` 使用的 `TreeCatalog` 实例存储在一起。 

### <a name="implement-count"></a>实现计数

`Count` 实现相对简单：返回树目录中的树数。 将 `Count` 替换为以下代码：

```csharp
public override int Count
{
    get { return treeCatalog.NumTrees; }
}
```

`TreeCatalog` 的 `NumTrees` 属性返回数据集中的树（页数）数。

### <a name="implement-instantiateitem"></a>实现 InstantiateItem

`InstantiateItem` 方法创建给定位置的页面。 它还必须在 `ViewPager`的视图集合中添加新创建的视图。 为了使这种情况成为可能，`ViewPager` 将自身作为容器参数进行传递。 

将 `InstantiateItem` 方法替换为以下代码：

```csharp
public override Java.Lang.Object InstantiateItem (View container, int position)
{
    var imageView = new ImageView (context);
    imageView.SetImageResource (treeCatalog[position].imageId);
    var viewPager = container.JavaCast<ViewPager>();
    viewPager.AddView (imageView);
    return imageView;
}
```

此代码将执行以下操作：

1. 实例化一个新的 `ImageView`，以显示指定位置的树图像。 应用的 `MainActivity` 是将传递到 `ImageView` 构造函数的上下文。

2. 将 `ImageView` 资源设置为指定位置的 `TreeCatalog` 图像资源 ID。

3. 将传递的容器 `View` 转换为 `ViewPager` 引用。
    请注意，必须使用 `JavaCast<ViewPager>()` 才能正确执行此强制转换（需要此强制转换才能执行运行时检查的类型转换）。

4. 将实例化的 `ImageView` 添加到 `ViewPager` 并将 `ImageView` 返回给调用方。

`ViewPager` 在 `position`显示图像时，它会显示此 `ImageView`。 最初，`InstantiateItem` 称为两次，用视图填充前两页。 当用户滚动时，将再次调用它来维护当前显示项的紧靠后的视图。 

### <a name="implement-destroyitem"></a>实现 DestroyItem

`DestroyItem` 方法从给定位置中删除页。 在任何给定位置的视图都可以更改的应用程序中，`ViewPager` 必须在将旧视图替换为新视图之前，将其移到该位置。 在 `TreeCatalog` 示例中，每个位置的视图不会更改，因此，在为该位置调用 `InstantiateItem` 时，将只重新添加 `DestroyItem` 删除的视图。 （为了获得更高的效率，可以实现一个池，以便回收将在同一位置重新显示 `View`。） 

将 `DestroyItem` 方法替换为以下代码： 

```csharp
public override void DestroyItem(View container, int position, Java.Lang.Object view)
{
    var viewPager = container.JavaCast<ViewPager>();
    viewPager.RemoveView(view as View);
}
```

此代码将执行以下操作：

1. 将传递的容器 `View` 转换为 `ViewPager` 引用。

2. 将传递的 Java 对象（`view`）强制转换C#为 `View` （`view as View`）;

3. 从 `ViewPager`中删除视图。 

### <a name="implement-isviewfromobject"></a>实现 IsViewFromObject

当用户在内容页中向左和向右滑动时，`ViewPager` 调用 `IsViewFromObject` 来验证给定位置处的子 `View` 是否与同一位置的适配器对象相关联（因此，适配器的对象称为*对象键*）。 对于相对简单的应用程序，关联是适配器的对象密钥的标识 &ndash; 之一，该实例是以前通过 `InstantiateItem`返回到 `ViewPager` 的视图。 但对于其他应用程序，对象键可能是与 `ViewPager` 在该位置显示的子视图关联的其他特定于适配器的类实例。 只有适配器知道传递的视图和对象键是否关联。 

若要正常运行 `PagerAdapter`，必须实现 `IsViewFromObject`。 如果 `IsViewFromObject` 返回给定位置 `false`，`ViewPager` 将不会在该位置显示视图。 在 `TreePager` 应用中，`InstantiateItem` 返回的对象键*是*树的页面 `View`，因此该代码只需要检查标识（即，对象键和视图为一个和相同的）。 将 `IsViewFromObject` 替换为以下代码： 

```csharp
public override bool IsViewFromObject(View view, Java.Lang.Object obj)
{
    return view == obj;
}
```

## <a name="add-the-adapter-to-the-viewpager"></a>将适配器添加到 ViewPager

现在，`TreePagerAdapter` 已实现，接下来可以将其添加到 `ViewPager`中。 在**MainActivity.cs**中，将以下代码行添加到 `OnCreate` 方法的末尾：

```csharp
viewPager.Adapter = new TreePagerAdapter(this, treeCatalog);
```

此代码将实例化 `TreePagerAdapter`，并传入 `MainActivity` 作为上下文（`this`）。 实例化 `TreeCatalog` 将传递到构造函数的第二个参数中。 `ViewPager`的 `Adapter` 属性设置为实例化的 `TreePagerAdapter` 对象;这会将 `TreePagerAdapter` 插入到 `ViewPager`中。 

核心实现现已完成 &ndash; 生成并运行应用。 屏幕上会显示树目录的第一个图像，如接下来的屏幕截图中所示。 向左轻扫可查看更多树视图，并向右轻扫可在树目录中向后移动： 

[TreePager 应用的 ![屏幕截图通过树图像进行轻量](viewpager-and-views-images/03-example-views-sml.png)](viewpager-and-views-images/03-example-views.png#lightbox)

## <a name="add-a-pager-indicator"></a>添加寻呼指示器

这一最小 `ViewPager` 实现显示树目录的映像，但不提供用户在目录中的位置的指示。 下一步是添加 `PagerTabStrip`。 `PagerTabStrip` 向用户通知显示了哪个页面，并通过显示上一页和下一页的提示来提供导航上下文。 `PagerTabStrip` 旨在用作 `ViewPager`的当前页的指示器;随着用户 swipes 每个页面的进行滚动和更新。 

打开**Resources/layout/main.axml** ，并将 `PagerTabStrip` 添加到布局：

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.view.ViewPager
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/viewpager"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

    <android.support.v4.view.PagerTabStrip
          android:layout_width="match_parent"
          android:layout_height="wrap_content"
          android:layout_gravity="top"
          android:paddingBottom="10dp"
          android:paddingTop="10dp"
          android:textColor="#fff" />

</android.support.v4.view.ViewPager>
```

`ViewPager` 和 `PagerTabStrip` 设计为协同工作。 在 `ViewPager` 布局中声明 `PagerTabStrip` 时，`ViewPager` 将自动查找 `PagerTabStrip` 并将其连接到适配器。 当你生成并运行应用时，你应该会看到在每个屏幕顶部显示的空 `PagerTabStrip`： 

[![空 PagerTabStrip 的特写屏幕快照](viewpager-and-views-images/04-empty-pagetabstrip-cap-sml.png)](viewpager-and-views-images/04-empty-pagetabstrip-cap.png#lightbox)

### <a name="display-a-title"></a>显示标题

若要将标题添加到每个页选项卡，请在 `PagerAdapter`派生类中实现 `GetPageTitleFormatted` 方法。 `ViewPager` 调用 `GetPageTitleFormatted` （如果已实现）来获取描述指定位置上页面的标题字符串。 将以下方法添加到**TreePagerAdapter.cs**中的 `TreePagerAdapter` 类： 

```csharp
public override Java.Lang.ICharSequence GetPageTitleFormatted(int position)
{
    return new Java.Lang.String(treeCatalog[position].caption);
}
```

此代码检索树目录中指定页（位置）的树标题字符串，将其转换为 Java `String`，并将其返回到 `ViewPager`中。 当你通过此新方法运行应用时，每个页面都会在 `PagerTabStrip`中显示树标题。 你应在屏幕顶部看到树名称时不带下划线： 

[![带有文本填充的选项卡的页面屏幕快照](viewpager-and-views-images/05-final-pagetabstrip-sml.png)](viewpager-and-views-images/05-final-pagetabstrip.png#lightbox)

您可以来回切换，以查看目录中的每个带标题的树图像。 

### <a name="pagertitlestrip-variation"></a>PagerTitleStrip 变体

`PagerTitleStrip` 与 `PagerTabStrip` 非常类似，只是 `PagerTabStrip` 为当前选定的选项卡添加下划线。您可以将 `PagerTabStrip` 替换为上述布局中的 `PagerTitleStrip`，并再次运行应用程序以查看其外观 `PagerTitleStrip`： 

[从文本中删除下划线的 ![PagerTitleStrip](viewpager-and-views-images/06-pagetitlestrip-example-sml.png)](viewpager-and-views-images/06-pagetitlestrip-example.png#lightbox)

请注意，当您转换为 `PagerTitleStrip`时，下划线会被删除。 

## <a name="summary"></a>摘要

本演练提供了如何在不使用 `Fragment`的情况下生成基于基本 `ViewPager`的应用的分步示例。 它提供了一个示例数据源，其中包含图像和标题字符串、用于显示图像的 `ViewPager` 布局以及将 `ViewPager` 连接到数据源的 `PagerAdapter` 子类。 为了帮助用户浏览数据集，包含了说明如何添加 `PagerTabStrip` 或 `PagerTitleStrip` 以显示每个页面顶部的图像标题的说明。 

## <a name="related-links"></a>相关链接

- [TreePager （示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-treepager)
