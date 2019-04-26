---
title: 带视图的 ViewPager
description: ViewPager 是使您可以实现动作导航的布局管理器。 动作导航允许用户轻扫，左侧和右侧到单步执行的数据页。 本指南介绍如何实现使用 ViewPager 和 PagerTabStrip，为数据页使用视图可轻扫 UI （后续指南介绍如何使用页片段）。
ms.prod: xamarin
ms.assetid: 42E5379F-B0F4-4B87-A314-BF3DE405B0C8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: a8b7fa53d3384821d028e4a88ba22071a17e5bd9
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61311540"
---
# <a name="viewpager-with-views"></a>带视图的 ViewPager

_ViewPager 是使您可以实现动作导航的布局管理器。动作导航允许用户轻扫，左侧和右侧到单步执行的数据页。本指南介绍如何实现使用 ViewPager 和 PagerTabStrip，为数据页使用视图可轻扫 UI （后续指南介绍如何使用页片段）。_

 
## <a name="overview"></a>概述

本指南是一个演练，提供了分步演示如何使用`ViewPager`实现落叶和长期有效树的图像库。 在用户轻左侧和右侧的"树目录"通过扫此应用中查看树映像。 目录的每个页面顶部的树的名称被列入`PagerTabStrip`，并在树的映像显示在`ImageView`。 使用适配器接口`ViewPager`到基础数据模型。 此应用程序实现派生自的适配器`PagerAdapter`。 

尽管`ViewPager`-基于应用程序通常实现与`Fragment`s，有一些相对简单的用例，额外的复杂性`Fragment`s 不是必要。 例如，在本演练中所示的基本映像库应用不需要使用`Fragment`s。 因为内容是静态的且不同的映像之间来回用户唯一扫，实现可以保持为更简单，使用标准 Android 视图和布局。 



## <a name="start-an-app-project"></a>启动应用程序项目

创建一个名为的新的 Android 项目**TreePager** (请参阅[Hello，Android](~/android/get-started/hello-android/hello-android-quickstart.md)有关创建新的 Android 项目的详细信息)。 接下来，启动 NuGet 包管理器。 (有关安装 NuGet 包的详细信息，请参阅[演练：在项目中包括 NuGet](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough))。 查找和安装**Android 支持库 v4**: 

[![在 NuGet 包管理器选择屏幕截图的支持 v4 Nuget](viewpager-and-views-images/01-install-support-lib-sml.png)](viewpager-and-views-images/01-install-support-lib.png#lightbox)

这还将安装的任何其他包 reaquired **Android 支持库 v4**。



## <a name="add-an-example-data-source"></a>添加示例数据源

在此示例中，树目录数据源 (由`TreeCatalog`类) 提供`ViewPager`项内容。 
`TreeCatalog` 包含一系列现成树映像和树标题，则适配器将使用用于创建`View`s。 `TreeCatalog`构造函数不需要任何参数：

```csharp
TreeCatalog treeCatalog = new TreeCatalog();
```

中的映像集合`TreeCatalog`组织，因此可通过索引器访问的每个图像。 例如，以下代码行检索集合中的第三个图像的图像资源 ID: 

```csharp
int imageId = treeCatalog[2].imageId;
```

因为的实现细节`TreeCatalog`了解与不相关`ViewPager`，则`TreeCatalog`此处未列出的代码。 向源代码`TreeCatalog`网址[TreeCatalog.cs](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/TreePager/TreePager/TreeCatalog.cs)。 下载此源文件 (或复制并粘贴到一个新的代码**TreeCatalog.cs**文件) 并将其添加到你的项目。 此外，下载并解压缩[映像文件](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/TreePager/Resources/tree-images.zip?raw=true)到你**资源/drawable**文件夹并将其包含在项目中。 



## <a name="create-a-viewpager-layout"></a>创建 ViewPager 布局

打开**Resources/layout/Main.axml**并将其内容替换为以下 XML:

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

1.  设置从视图**Main.axml**布局资源。

2.  检索到的引用`ViewPager`从布局。

3.  实例化新`TreeCatalog`作为数据源。

当生成并运行此代码时，您应看到类似于以下屏幕截图显示： 

[![显示空的 ViewPager 应用的屏幕截图](viewpager-and-views-images/02-initial-screen-sml.png)](viewpager-and-views-images/02-initial-screen.png#lightbox)

在此情况下，`ViewPager`为空，因为它缺少适配器用于访问中的内容**TreeCatalog**。 在下一步部分中， **PagerAdapter**创建连接`ViewPager`到**TreeCatalog**。 


## <a name="create-the-adapter"></a>创建适配器

`ViewPager` 使用适配器控制器对象位于之间`ViewPager`和数据源 (请参阅中的图示[适配器](~/android/user-interface/controls/view-pager/index.md#adapter))。 若要访问此数据，`ViewPager`要求你提供自定义适配器派生自`PagerAdapter`。 此适配器将填充每个`ViewPager`页中的数据源的内容。 由于此数据源是特定于应用程序，自定义适配器将是一个知道如何访问数据的代码。 作为通过页的用户扫`ViewPager`，该适配器从数据源中提取信息，并将其加载到页面`ViewPager`显示。 

当您实现`PagerAdapter`，必须重写以下：

-   **InstantiateItem** &ndash;创建页 (`View`) 的给定位置并将其添加到`ViewPager`的视图的集合。 

-   **DestroyItem** &ndash;从给定位置移除一个页面。

-   **计数**&ndash;只读属性，该属性返回的视图 （页面） 可用。 

-   **IsViewFromObject** &ndash;确定页是否与特定的密钥对象相关联。 (此对象创建的`InstantiateItem`方法。)在此示例中，关键对象是`TreeCatalog`数据对象。

添加名为的新文件**TreePagerAdapter.cs**和其内容替换为以下代码： 

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

此代码存根不可或缺`PagerAdapter`实现。 在以下部分中，每种方法将被替换的工作代码。 



### <a name="implement-the-constructor"></a>实现构造函数

当应用程序实例化`TreePagerAdapter`，它会提供一个上下文 ( `MainActivity`) 和实例化`TreeCatalog`。 将以下成员变量和构造函数添加到顶部`TreePagerAdapter`类中**TreePagerAdapter.cs**: 

```csharp
Context context;
TreeCatalog treeCatalog;

public TreePagerAdapter (Context context, TreeCatalog treeCatalog)
{
    this.context = context;
    this.treeCatalog = treeCatalog;
}
```

此构造函数的目的是为存储上下文和`TreeCatalog`实例的`TreePagerAdapter`将使用。 



### <a name="implement-count"></a>实现计数

`Count`实现是相对简单： 它返回树目录中的树数。 将 `Count` 替换为以下代码：

```csharp
public override int Count
{
    get { return treeCatalog.NumTrees; }
}
```

`NumTrees`属性的`TreeCatalog`数据集中返回树 （页数） 数。



### <a name="implement-instantiateitem"></a>实现 InstantiateItem

`InstantiateItem`方法创建的给定位置的页。 它还必须添加到新创建的视图`ViewPager`的查看集合。 为了实现这一点，`ViewPager`将自身作为容器参数中传递。 

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

1.  实例化新`ImageView`在指定位置显示树图像。 应用程序的`MainActivity`是将传递给上下文`ImageView`构造函数。

2.  集`ImageView`资源`TreeCatalog`映像中的指定位置的资源 ID。

3.  将强制转换传递的容器`View`到`ViewPager`引用。
    请注意，必须使用`JavaCast<ViewPager>()`若要正确执行此转换 （这需要以便 Android 执行运行时检查类型转换）。

4.  添加了实例化`ImageView`到`ViewPager`，并返回`ImageView`给调用方。

当`ViewPager`显示在图像`position`，它会显示以下`ImageView`。 最初，`InstantiateItem`调用两次以填充具有视图的前两个页面。 当用户滚动时，它再次被调用来维护视图只是后面和前面的当前显示的项。 



### <a name="implement-destroyitem"></a>实现 DestroyItem

`DestroyItem`方法从给定位置移除一个页面。 在任意给定位置处的视图可以更改其中的应用程序中`ViewPager`必须有办法其替换为新视图之前删除过时视图中的该位置。 在`TreeCatalog`示例中，每个位置处的视图不会更改，因此视图删除通过`DestroyItem`只需进行重新添加时`InstantiateItem`为该位置调用。 (一个可以为更好地提高效率，实现池回收`View`将重新显示在相同的位置上的 s。) 

将 `DestroyItem` 方法替换为以下代码： 

```csharp
public override void DestroyItem(View container, int position, Java.Lang.Object view)
{
    var viewPager = container.JavaCast<ViewPager>();
    viewPager.RemoveView(view as View);
}
```

此代码将执行以下操作：

1.  将强制转换传递的容器`View`到`ViewPager`引用。

2.  将转换所传递的 Java 对象 (`view`) 到C# `View` (`view as View`);

3.  从视图中删除`ViewPager`。 



### <a name="implement-isviewfromobject"></a>实现 IsViewFromObject

作为用户滑向左和右页的内容，通过`ViewPager`调用`IsViewFromObject`若要验证的子`View`给定的位置是与该相同位置的适配器的对象相关联 (因此，调用适配器的对象*的对象键*)。 对于相对简单的应用程序，关联是一个标识&ndash;在该实例上的适配器的对象键是之前返回到视图`ViewPager`通过`InstantiateItem`。 但是对于其他应用程序，该对象键可能与关联的某些其他特定于适配器的类实例 （但不是完全相同） 子视图，`ViewPager`在该位置显示。 只有适配器知道是否不传递的视图和对象键相关联。 

`IsViewFromObject` 必须为实现`PagerAdapter`才能正常工作。 如果`IsViewFromObject`将返回`false`的给定位置`ViewPager`将不显示视图中的相应位置。 在`TreePager`应用程序，该对象返回键`InstantiateItem`*是*页面`View`的树，因此该代码仅有以检查标识 （即，该对象键和视图是同一个）。 将 `IsViewFromObject` 替换为以下代码： 

```csharp
public override bool IsViewFromObject(View view, Java.Lang.Object obj)
{
    return view == obj;
}
```


## <a name="add-the-adapter-to-the-viewpager"></a>将适配器添加到 ViewPager

既然`TreePagerAdapter`是实现，是时候将其添加到`ViewPager`。 在中**MainActivity.cs**，将以下代码行添加到末尾`OnCreate`方法：

```csharp
viewPager.Adapter = new TreePagerAdapter(this, treeCatalog);
```

此代码实例化`TreePagerAdapter`，并传入`MainActivity`作为上下文 (`this`)。 实例化`TreeCatalog`传递到构造函数的第二个参数。 `ViewPager`的`Adapter`属性设置为实例化`TreePagerAdapter`对象; 此插入`TreePagerAdapter`到`ViewPager`。 

现已完成的核心实现&ndash;生成并运行应用。 应会看到第一个图像的树目录显示在屏幕上，在左侧的下一步的屏幕截图中所示。 轻扫保留以查看更多的树视图，然后右轻扫可向后移动树目录： 

[![轻扫浏览树图像的屏幕截图的 TreePager 应用](viewpager-and-views-images/03-example-views-sml.png)](viewpager-and-views-images/03-example-views.png#lightbox)



## <a name="add-a-pager-indicator"></a>添加页导航指示器

此最小`ViewPager`实现显示树目录的映像，但它提供了没有指明用户所在目录中。 下一步是添加`PagerTabStrip`。 `PagerTabStrip`通知哪一页显示，并通过显示上一个和下一页面的提示来提供导航上下文作为到用户。 `PagerTabStrip` 旨在用作当前页的指示符`ViewPager`; 进行滚动和更新为每个页上用户扫。 

打开**Resources/layout/Main.axml**并添加`PagerTabStrip`到布局：

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

`ViewPager` 和`PagerTabStrip`设计为协同工作。 当你声明`PagerTabStrip`内`ViewPager`布局`ViewPager`将自动查找`PagerTabStrip`并将其连接到适配器。 当生成并运行应用时，应该会看到空`PagerTabStrip`显示在每个屏幕的顶部： 

[![空 PagerTabStrip 特写屏幕截图](viewpager-and-views-images/04-empty-pagetabstrip-cap-sml.png)](viewpager-and-views-images/04-empty-pagetabstrip-cap.png#lightbox)



### <a name="display-a-title"></a>显示标题

若要添加到每个页选项卡标题，实现`GetPageTitleFormatted`中的方法`PagerAdapter`-派生的类。 `ViewPager` 调用`GetPageTitleFormatted`（如果已实现） 获取描述指定位置处的页的标题字符串。 添加以下方法`TreePagerAdapter`类中**TreePagerAdapter.cs**: 

```csharp
public override Java.Lang.ICharSequence GetPageTitleFormatted(int position)
{
    return new Java.Lang.String(treeCatalog[position].caption);
}
```

此代码从树目录中指定的页 （位置） 中检索树标题字符串，将其转换到 Java `String`，并返回到`ViewPager`。 每个页时使用此新方法运行该应用，显示的树标题`PagerTabStrip`。 应看到在不使用下划线屏幕顶部的树的名称： 

[![使用文本填充 PagerTabStrip 选项卡页的屏幕快照](viewpager-and-views-images/05-final-pagetabstrip-sml.png)](viewpager-and-views-images/05-final-pagetabstrip.png#lightbox)

轻扫可以来回若要查看目录中的每个没有标题树映像。 



### <a name="pagertitlestrip-variation"></a>PagerTitleStrip Variation

`PagerTitleStrip` 非常类似于`PagerTabStrip`只不过`PagerTabStrip`添加当前所选的选项卡的下划线。您可以替换`PagerTabStrip`与`PagerTitleStrip`在上面的布局和运行应用以查看其外观与`PagerTitleStrip`: 

[![用下划线从文本中删除 PagerTitleStrip](viewpager-and-views-images/06-pagetitlestrip-example-sml.png)](viewpager-and-views-images/06-pagetitlestrip-example.png#lightbox)

请注意，下划线删除时将转换为`PagerTitleStrip`。 


 
## <a name="summary"></a>总结

本演练提供如何生成基本的分步示例`ViewPager`-基于应用程序而无需使用`Fragment`s。 显示包含图像和标题字符串的示例数据源`ViewPager`布局来显示图像，和一个`PagerAdapter`连接的子类`ViewPager`到数据源。 为了帮助用户可以浏览数据集，说明已包含说明了如何添加`PagerTabStrip`或`PagerTitleStrip`若要在每个页面的顶部显示的图像标题。 


## <a name="related-links"></a>相关链接

- [TreePager （示例）](https://developer.xamarin.com/samples/monodroid/UserInterface/TreePager)
