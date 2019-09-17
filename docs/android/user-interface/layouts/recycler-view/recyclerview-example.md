---
title: 基本 RecyclerView 示例
description: 演示如何使用 RecyclerView 的示例应用。
ms.prod: xamarin
ms.assetid: A50520D2-1214-40E1-9B27-B0891FE11584
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/30/2018
ms.openlocfilehash: 0c6568f9283491f0e55c79c77fb8efb2ae75f33c
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70758611"
---
# <a name="a-basic-recyclerview-example"></a>基本 RecyclerView 示例

为了了解典型`RecyclerView`应用程序中的工作原理，本主题介绍了[RecyclerViewer](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer)示例应用，这是一个用于显示`RecyclerView`大量照片的简单代码示例： 

[![使用 CardViews 显示照片的 RecyclerView 应用程序的两个屏幕截图](recyclerview-example-images/01-recyclerviewer-sml.png)](recyclerview-example-images/01-recyclerviewer.png#lightbox)

**RecyclerViewer**使用[CardView](~/android/user-interface/controls/card-view.md)实现布局中的`RecyclerView`每个照片项。 由于性能`RecyclerView`方面的优势，此示例应用程序能够快速滚动大量照片，而不会出现明显的延迟。

### <a name="an-example-data-source"></a>示例数据源

在此示例应用程序中，"相册" 数据源（由`PhotoAlbum`类表示）提供`RecyclerView`了项内容。
`PhotoAlbum`带有字幕的照片集合;当你对其进行实例化时，将获得一个现成的32照片集合：

```csharp
PhotoAlbum mPhotoAlbum = new PhotoAlbum ();
```

中的`PhotoAlbum`每个照片实例都公开了属性，这些属性允许您读取`PhotoID`其图像资源 ID 及其标题`Caption`字符串。 照片集合经过组织，可以通过索引器访问每张照片。 例如，以下代码行访问集合中第十张照片的图像资源 ID 和标题：

```csharp
int imageId = mPhotoAlbum[9].ImageId;
string caption = mPhotoAlbum[9].Caption;
```

`PhotoAlbum`还提供了`RandomSwap`一个方法，可以调用该方法，将集合中的第一个照片替换为集合中其他位置随机选择的照片：

```csharp
mPhotoAlbum.RandomSwap ();
```

由于的实现细节`PhotoAlbum`与理解`RecyclerView`无关， `PhotoAlbum`因此此处不会显示源代码。 [RecyclerViewer](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer) 示例应用程序的[PhotoAlbum.cs](https://github.com/xamarin/monodroid-samples/blob/master/android5.0/RecyclerViewer/RecyclerViewer/PhotoAlbum.cs)中提供了`PhotoAlbum`源代码 。

### <a name="layout-and-initialization"></a>布局和初始化

布局文件**main.axml**包含中`RecyclerView` `LinearLayout`的单个：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <android.support.v7.widget.RecyclerView
        android:id="@+id/recyclerView"
        android:scrollbars="vertical"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent" />
</LinearLayout>
```

请注意，必须使用完全限定的名称**v7** ，因为`RecyclerView`会打包到支持库中。 `OnCreate` 的`MainActivity`方法初始化此布局，实例化适配器，并准备基础数据源：

```csharp
public class MainActivity : Activity
{
    RecyclerView mRecyclerView;
    RecyclerView.LayoutManager mLayoutManager;
    PhotoAlbumAdapter mAdapter;
    PhotoAlbum mPhotoAlbum;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        // Prepare the data source:
        mPhotoAlbum = new PhotoAlbum ();

        // Instantiate the adapter and pass in its data source:
        mAdapter = new PhotoAlbumAdapter (mPhotoAlbum);

        // Set our view from the "main" layout resource:
        SetContentView (Resource.Layout.Main);

        // Get our RecyclerView layout:
        mRecyclerView = FindViewById<RecyclerView> (Resource.Id.recyclerView);

        // Plug the adapter into the RecyclerView:
        mRecyclerView.SetAdapter (mAdapter);
```

此代码执行以下操作：

1. 实例化`PhotoAlbum`数据源。

2. 将唱片集数据源传递到适配器`PhotoAlbumAdapter`的构造函数（在本指南的后面部分中定义）。 
   请注意，将数据源作为参数传递给适配器的构造函数是一种最佳做法。 

3. `RecyclerView`从布局中获取。

4. 通过`RecyclerView` `RecyclerView`调用方法将适配器插入到实例中，如上面所示。 `SetAdapter`

### <a name="layout-manager"></a>布局管理器

中`RecyclerView`的每一项都由一个`CardView`包含照片图像和照片标题的组成（详细信息将在下面的 "[查看容器](#view-holder)" 部分中介绍）。 预定义`LinearLayoutManager`用于在垂直滚动排列中`CardView`布局每个：

```csharp
mLayoutManager = new LinearLayoutManager (this);
mRecyclerView.SetLayoutManager (mLayoutManager);
```

此代码位于主活动的`OnCreate`方法中。 布局管理器的构造函数需要*上下文*，因此使用`MainActivity` `this`传递的是，如上所述。

可以不使用预`LinearLayoutManager`定义的，而是插入一个自定义的布局管理器，该管理器将并排显示两个`CardView`项，实现页面翻转动画效果以遍历照片集。 稍后在本指南中，你将看到一个示例，说明如何通过在不同的布局管理器中进行交换来修改布局。

<a name="view-holder" />

### <a name="view-holder"></a>查看刀柄

调用`PhotoViewHolder`视图持有者类。 每`PhotoViewHolder`个实例都包含对`ImageView`关联`TextView`行`CardView`项的和的引用，这些行项在中按图解的方式进行布局：

[![包含 ImageView 和 TextView 的 CardView 关系图](recyclerview-example-images/02-cardview-layout-sml.png)](recyclerview-example-images/02-cardview-layout.png#lightbox)

`PhotoViewHolder`派生自`RecyclerView.ViewHolder` ，其中包含用于存储对的引用`ImageView`并`TextView`显示在上述布局中的属性。
`PhotoViewHolder`包含两个属性和一个构造函数：

```csharp
public class PhotoViewHolder : RecyclerView.ViewHolder
{
    public ImageView Image { get; private set; }
    public TextView Caption { get; private set; }

    public PhotoViewHolder (View itemView) : base (itemView)
    {
        // Locate and cache view references:
        Image = itemView.FindViewById<ImageView> (Resource.Id.imageView);
        Caption = itemView.FindViewById<TextView> (Resource.Id.textView);
    }
}
```

在此代码示例中， `PhotoViewHolder`构造函数向`PhotoViewHolder`包装的父项视图`CardView`（）传递引用。 请注意，始终将父项视图转发到基构造函数。 `TextView` `ImageView` `Caption` `Image`构造函数对`FindViewById`父项视图调用以定位其每个子视图引用，并分别将结果存储在和属性中。 `PhotoViewHolder` 以后，适配器将在用新数据更新此`CardView`子视图时从这些属性中检索视图引用。

有关的详细信息`RecyclerView.ViewHolder`，请参阅[RecyclerView. ViewHolder 类引用](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ViewHolder.html)。

### <a name="adapter"></a>适配器

适配器使用特定照片`RecyclerView`的数据加载每一行。 例如，对于位于第*p*行的给定照片，适配器会在数据源中的位置*p*找到关联的数据，并将此数据复制到`RecyclerView`集合中位于*p*位置的行项。 适配器使用视图持有者在该位置查找`ImageView`和`TextView`的引用，这样当用户滚动照片集合并重复使用视图`FindViewById`时，无需重复调用这些视图。

在**RecyclerViewer**中，适配器类从`RecyclerView.Adapter`派生，以创建： `PhotoAlbumAdapter`

```csharp
public class PhotoAlbumAdapter : RecyclerView.Adapter
{
    public PhotoAlbum mPhotoAlbum;

    public PhotoAlbumAdapter (PhotoAlbum photoAlbum)
    {
        mPhotoAlbum = photoAlbum;
    }
    ...
}
```

`mPhotoAlbum`成员包含传入构造函数的数据源（相册）; 构造函数将相册复制到此成员变量中。 实现以下必需`RecyclerView.Adapter`方法：

- **`OnCreateViewHolder`** &ndash;实例化项布局文件和视图持有者。

- **`OnBindViewHolder`** &ndash;将位于指定位置的数据加载到其引用存储在给定视图持有者中的视图中。

- **`ItemCount`** &ndash;返回数据源中的项数。

布局管理器在定位中的`RecyclerView`项时调用这些方法。 以下各节将对这些方法的实现进行了检查。

#### <a name="oncreateviewholder"></a>OnCreateViewHolder

当需要新的`OnCreateViewHolder` `RecyclerView`视图持有者来表示项时，布局管理器会调用。 `OnCreateViewHolder`从视图的布局文件中增加项视图，并在新`PhotoViewHolder`的实例中包装视图。 构造函数在布局中查找并存储对子视图的引用，如前面的 "[查看容器](#view-holder)" 中所述。 `PhotoViewHolder`

每个行项由一个`CardView` `ImageView`包含（对于照片）和一个`TextView` （用于标题）的表示。 此布局位于 PhotoCardView 文件中 **。 main.axml**：

```xml
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:card_view="http://schemas.android.com/apk/res-auto"
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content">
    <android.support.v7.widget.CardView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        card_view:cardElevation="4dp"
        card_view:cardUseCompatPadding="true"
        card_view:cardCornerRadius="5dp">
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical"
            android:padding="8dp">
            <ImageView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:id="@+id/imageView"
                android:scaleType="centerCrop" />
            <TextView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:textAppearance="?android:attr/textAppearanceMedium"
                android:textColor="#333333"
                android:text="Caption"
                android:id="@+id/textView"
                android:layout_gravity="center_horizontal"
                android:layout_marginLeft="4dp" />
        </LinearLayout>
    </android.support.v7.widget.CardView>
</FrameLayout>
```

此布局表示中`RecyclerView`的单个行项。 方法（如下所述）将数据从数据源复制到此`ImageView`布局`TextView`的和中。 `OnBindViewHolder`
`OnCreateViewHolder`为中`RecyclerView`的给定照片位置增加此布局，并实例化一个`PhotoViewHolder`新实例（该实例在关联`CardView`布局中`ImageView`查找`TextView`并缓存对和子视图的引用）：

```csharp
public override RecyclerView.ViewHolder
    OnCreateViewHolder (ViewGroup parent, int viewType)
{
    // Inflate the CardView for the photo:
    View itemView = LayoutInflater.From (parent.Context).
                Inflate (Resource.Layout.PhotoCardView, parent, false);

    // Create a ViewHolder to hold view references inside the CardView:
    PhotoViewHolder vh = new PhotoViewHolder (itemView);
    return vh;
}

```

生成的视图持有者实例`vh`返回到调用方（布局管理器）。

#### <a name="onbindviewholder"></a>OnBindViewHolder

当布局管理器准备好在`RecyclerView`的可视屏幕区域中显示特定视图时，它会调用适配器的`OnBindViewHolder`方法，以使用来自数据源的内容来填充位于指定行位置的项。 `OnBindViewHolder`获取指定行位置（照片的图像资源和照片标题的字符串）的照片信息，并将此数据复制到关联的视图中。 视图通过存储在视图容器对象中的引用来定位（通过`holder`参数传入）：

```csharp
public override void
    OnBindViewHolder (RecyclerView.ViewHolder holder, int position)
{
    PhotoViewHolder vh = holder as PhotoViewHolder;

    // Load the photo image resource from the photo album:
    vh.Image.SetImageResource (mPhotoAlbum[position].PhotoID);

    // Load the photo caption from the photo album:
    vh.Caption.Text = mPhotoAlbum[position].Caption;
}
```

在使用传入的视图持有者对象之前， `PhotoViewHolder`必须先将该对象强制转换为派生的视图持有者类型（在本例中为）。
适配器将图像资源加载到视图持有者的`Image`属性所引用的视图中，并将标题文本复制到视图持有者的`Caption`属性所引用的视图中。 这会将关联视图与其数据*绑定*在一起。

请注意`OnBindViewHolder` ，是直接处理数据结构的代码。 在这种情况`OnBindViewHolder`下，了解如何将`RecyclerView`项位置映射到数据源中其关联数据项。 在这种情况下，映射很简单，因为位置可用作相册中的数组索引;但是，更复杂的数据源可能需要额外的代码来建立此类映射。

#### <a name="itemcount"></a>ItemCount

`ItemCount`方法返回数据集合中的项数。 在示例照片查看器应用程序中，项计数是相册中照片的数目：

```csharp
public override int ItemCount
{
    get { return mPhotoAlbum.NumPhotos; }
}
```

有关的详细信息`RecyclerView.Adapter`，请参阅[RecyclerView 类引用](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.Adapter.html)。

### <a name="putting-it-all-together"></a>将其全部放在一起

示例照片`RecyclerView`应用程序的结果实现`MainActivity`包括创建数据源、布局管理器和适配器的代码。 `MainActivity``mRecyclerView`创建实例，实例化数据源和适配器，并插入布局管理器和适配器：

```csharp
public class MainActivity : Activity
{
    RecyclerView mRecyclerView;
    RecyclerView.LayoutManager mLayoutManager;
    PhotoAlbumAdapter mAdapter;
    PhotoAlbum mPhotoAlbum;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        mPhotoAlbum = new PhotoAlbum();
        SetContentView (Resource.Layout.Main);
        mRecyclerView = FindViewById<RecyclerView> (Resource.Id.recyclerView);

        // Plug in the linear layout manager:
        mLayoutManager = new LinearLayoutManager (this);
        mRecyclerView.SetLayoutManager (mLayoutManager);

        // Plug in my adapter:
        mAdapter = new PhotoAlbumAdapter (mPhotoAlbum);
        mRecyclerView.SetAdapter (mAdapter);
    }
}

```

`PhotoViewHolder`查找并缓存视图引用：

```csharp
public class PhotoViewHolder : RecyclerView.ViewHolder
{
    public ImageView Image { get; private set; }
    public TextView Caption { get; private set; }

    public PhotoViewHolder (View itemView) : base (itemView)
    {
        // Locate and cache view references:
        Image = itemView.FindViewById<ImageView> (Resource.Id.imageView);
        Caption = itemView.FindViewById<TextView> (Resource.Id.textView);
    }
}
```

`PhotoAlbumAdapter`实现三个所需的方法重写：

```csharp
public class PhotoAlbumAdapter : RecyclerView.Adapter
{
    public PhotoAlbum mPhotoAlbum;
    public PhotoAlbumAdapter (PhotoAlbum photoAlbum)
    {
        mPhotoAlbum = photoAlbum;
    }

    public override RecyclerView.ViewHolder
        OnCreateViewHolder (ViewGroup parent, int viewType)
    {
        View itemView = LayoutInflater.From (parent.Context).
                    Inflate (Resource.Layout.PhotoCardView, parent, false);
        PhotoViewHolder vh = new PhotoViewHolder (itemView);
        return vh;
    }

    public override void
        OnBindViewHolder (RecyclerView.ViewHolder holder, int position)
    {
        PhotoViewHolder vh = holder as PhotoViewHolder;
        vh.Image.SetImageResource (mPhotoAlbum[position].PhotoID);
        vh.Caption.Text = mPhotoAlbum[position].Caption;
    }

    public override int ItemCount
    {
        get { return mPhotoAlbum.NumPhotos; }
    }
}
```

编译并运行此代码时，它将创建基本照片查看应用，如以下屏幕截图所示：

[![具有垂直滚动照片卡的照片查看应用的两个屏幕截图](recyclerview-example-images/03-recyclerviewer-basic-sml.png)](recyclerview-example-images/03-recyclerviewer-basic.png#lightbox)

如果未绘制阴影（如上面的屏幕截图所示），请编辑**Properties/androidmanifest.xml** ，并将以下属性设置添加到`<application>`元素：

```xml
android:hardwareAccelerated="true"
```

此基本应用仅支持浏览相册。 它不响应项触控事件，也不处理基础数据中的更改。 此功能是在[扩展 RecyclerView 示例](~/android/user-interface/layouts/recycler-view/extending-the-example.md)中添加的。

### <a name="changing-the-layoutmanager"></a>更改 LayoutManager

`RecyclerView`由于非常灵活，因此可轻松修改应用以使用不同的布局管理器。 在下面的示例中，将对其进行修改，以显示带有水平滚动而不是垂直线性布局的网格布局的相册。 为此，布局管理器实例化将被修改为使用`GridLayoutManager` ，如下所示：

```csharp
mLayoutManager = new GridLayoutManager(this, 2, GridLayoutManager.Horizontal, false);
```

此代码更改将垂直`LinearLayoutManager`替换为一个`GridLayoutManager` ，它表示由两行组成的网格，该网格沿水平方向滚动。 再次编译并运行应用程序时，将看到照片显示在网格中，并且滚动是水平而不是垂直的：

[![网格中水平滚动照片的应用的示例屏幕截图](recyclerview-example-images/04-gridlayoutmanager-sml.png)](recyclerview-example-images/04-gridlayoutmanager.png#lightbox)

通过只更改一行代码，可以修改照片查看应用程序，以使用具有不同行为的不同布局。
请注意，适配器代码和布局 XML 都必须修改以更改布局样式。 

在下一主题中，[扩展了 RecyclerView 示例](~/android/user-interface/layouts/recycler-view/extending-the-example.md)，扩展了此基本示例应用程序以处理项单击事件， `RecyclerView`并在基础数据源发生更改时进行更新。

## <a name="related-links"></a>相关链接

- [RecyclerViewer （示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer)
- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [RecyclerView 部件和功能](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)
- [扩展 RecyclerView 示例](~/android/user-interface/layouts/recycler-view/extending-the-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
