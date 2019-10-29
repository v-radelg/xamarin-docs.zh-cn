---
title: 基本 RecyclerView 示例
description: 演示如何使用 RecyclerView 的示例应用。
ms.prod: xamarin
ms.assetid: A50520D2-1214-40E1-9B27-B0891FE11584
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/30/2018
ms.openlocfilehash: 7266d13136dbfc858bc3c882cacb802d83c92f52
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028840"
---
# <a name="a-basic-recyclerview-example"></a>基本 RecyclerView 示例

为了了解 `RecyclerView` 在典型的应用程序中如何工作，本主题介绍了[RecyclerViewer](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer)示例应用程序，这是一个使用 `RecyclerView` 来显示大量照片的简单代码示例： 

[![使用 CardViews 显示照片的 RecyclerView 应用程序的两个屏幕截图](recyclerview-example-images/01-recyclerviewer-sml.png)](recyclerview-example-images/01-recyclerviewer.png#lightbox)

**RecyclerViewer**使用[CardView](~/android/user-interface/controls/card-view.md)实现 `RecyclerView` 布局中的每个照片项。 由于 `RecyclerView`的性能优势，此示例应用程序能够平稳地快速滚动大量照片，而不会出现明显的延迟。

### <a name="an-example-data-source"></a>示例数据源

在此示例应用程序中，"相册" 数据源（由 `PhotoAlbum` 类表示）提供与项内容 `RecyclerView`。
`PhotoAlbum` 是包含字幕的照片集合;当你对其进行实例化时，将获得一个现成的32照片集合：

```csharp
PhotoAlbum mPhotoAlbum = new PhotoAlbum ();
```

`PhotoAlbum` 中的每个照片实例都公开了属性，这些属性可用于读取其图像资源 ID、`PhotoID`及其标题字符串 `Caption`。 照片集合经过组织，可以通过索引器访问每张照片。 例如，以下代码行访问集合中第十张照片的图像资源 ID 和标题：

```csharp
int imageId = mPhotoAlbum[9].ImageId;
string caption = mPhotoAlbum[9].Caption;
```

`PhotoAlbum` 还提供了一个 `RandomSwap` 方法，你可以调用该方法，将集合中的第一张照片替换为集合中其他地方随机选择的照片：

```csharp
mPhotoAlbum.RandomSwap ();
```

由于 `PhotoAlbum` 的实现细节与理解 `RecyclerView`无关，因此此处不会提供 `PhotoAlbum` 源代码。 要 `PhotoAlbum` 的源代码可在[RecyclerViewer](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer)示例应用程序的[PhotoAlbum.cs](https://github.com/xamarin/monodroid-samples/blob/master/android5.0/RecyclerViewer/RecyclerViewer/PhotoAlbum.cs)中找到。

### <a name="layout-and-initialization"></a>布局和初始化

布局文件**main.axml**包含 `LinearLayout`中的单个 `RecyclerView`：

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

请注意，必须使用完全限定的名称**v7** ，因为 `RecyclerView` 打包在支持库中。 `MainActivity` 的 `OnCreate` 方法初始化此布局，实例化适配器，并准备基础数据源：

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

1. 实例化 `PhotoAlbum` 数据源。

2. 将唱片唱片集数据源传递到适配器的构造函数，`PhotoAlbumAdapter` （本指南稍后将对此进行定义）。 
   请注意，将数据源作为参数传递给适配器的构造函数是一种最佳做法。 

3. 从布局中获取 `RecyclerView`。

4. 通过调用 `RecyclerView` `SetAdapter` 方法将适配器插入 `RecyclerView` 实例，如上所示。

### <a name="layout-manager"></a>布局管理器

`RecyclerView` 中的每一项都由包含照片图像和照片标题的 `CardView` 组成（下面的 "[查看刀柄](#view-holder)" 部分中介绍了详细信息）。 预定义的 `LinearLayoutManager` 用于在垂直滚动排列中布局每个 `CardView`：

```csharp
mLayoutManager = new LinearLayoutManager (this);
mRecyclerView.SetLayoutManager (mLayoutManager);
```

此代码位于主活动的 `OnCreate` 方法中。 布局管理器的构造函数需要*上下文*，因此使用 `this` 传递 `MainActivity`，如上面所示。

您可以插入自定义 `CardView` 的布局管理器，而不是使用预定义的 `LinearLayoutManager`，而是通过实现页面翻转动画效果来遍历照片集合，而不是使用预定义的。 稍后在本指南中，你将看到一个示例，说明如何通过在不同的布局管理器中进行交换来修改布局。

<a name="view-holder" />

### <a name="view-holder"></a>查看刀柄

视图持有者类称为 `PhotoViewHolder`。 每个 `PhotoViewHolder` 实例都包含对关联行项的 `ImageView` 和 `TextView` 的引用，该项在如下所示的 `CardView` 中布局：

[包含 ImageView 和 TextView 的 CardView![关系图](recyclerview-example-images/02-cardview-layout-sml.png)](recyclerview-example-images/02-cardview-layout.png#lightbox)

`PhotoViewHolder` 派生自 `RecyclerView.ViewHolder`，其中包含用于存储对 `ImageView` 的引用的属性，以及显示在上述布局中的 `TextView`。
`PhotoViewHolder` 包括两个属性和一个构造函数：

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

在此代码示例中，将向 `PhotoViewHolder` 包装的父项视图（`CardView`）传递引用 `PhotoViewHolder` 构造函数。 请注意，始终将父项视图转发到基构造函数。 `PhotoViewHolder` 构造函数对父项视图调用 `FindViewById`，以查找其每个子视图引用、`ImageView` 和 `TextView`，并分别将结果存储在 `Image` 和 `Caption` 属性中。 该适配器在更新此 `CardView`的子视图和新数据时，将从这些属性中检索视图引用。

有关 `RecyclerView.ViewHolder`的详细信息，请参阅[RecyclerView. ViewHolder 类引用](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ViewHolder.html)。

### <a name="adapter"></a>适配器

适配器使用特定照片的数据加载每个 `RecyclerView` 行。 例如，对于位于第*p*行的给定照片，适配器将在数据源中的位置*p*处查找关联的数据，并将此数据复制到 `RecyclerView` 集合中位置*p*处的行项。 适配器使用视图持有者查找 `ImageView` 的引用并在该位置 `TextView`，因此当用户滚动照片集合并重复使用视图时，无需对这些视图重复调用 `FindViewById`。

在**RecyclerViewer**中，适配器类派生自 `RecyclerView.Adapter` 来创建 `PhotoAlbumAdapter`：

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

`mPhotoAlbum` 成员包含传入构造函数的数据源（相册）;构造函数将相册复制到此成员变量中。 实现以下必需 `RecyclerView.Adapter` 方法：

- **`OnCreateViewHolder`** &ndash; 实例化项布局文件和视图持有者。

- **`OnBindViewHolder`** &ndash; 将位于指定位置的数据加载到其引用存储在给定视图持有者中的视图中。

- **`ItemCount`** &ndash; 返回数据源中的项数。

布局管理器在 `RecyclerView`中定位项时调用这些方法。 以下各节将对这些方法的实现进行了检查。

#### <a name="oncreateviewholder"></a>OnCreateViewHolder

当 `RecyclerView` 需要新的视图持有者来表示项时，布局管理器会调用 `OnCreateViewHolder`。 `OnCreateViewHolder` 从视图的布局文件增加项目视图，并在新 `PhotoViewHolder` 实例中包装视图。 `PhotoViewHolder` 构造函数在布局中查找并存储对子视图的引用，如前面的 "[查看刀柄](#view-holder)" 中所述。

每个行项由一个 `CardView` 表示，其中包含 `ImageView` （对于照片）和 `TextView` （对于标题）。 此布局位于 PhotoCardView 文件中 **。 main.axml**：

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

此布局表示 `RecyclerView`中的单个行项。 `OnBindViewHolder` 方法（如下所述）将数据从数据源复制到此布局的 `ImageView` 和 `TextView`。
`OnCreateViewHolder` 在 `RecyclerView` 中为给定照片位置增加此布局，并实例化一个新的 `PhotoViewHolder` 实例（该实例在关联 `TextView` 布局中查找并缓存对 `ImageView` 和 `CardView` 子视图的引用）:

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

生成的视图持有者实例 `vh`返回到调用方（布局管理器）。

#### <a name="onbindviewholder"></a>OnBindViewHolder

当布局管理器准备好在 `RecyclerView`的可见屏幕区域中显示特定视图时，它将调用适配器的 `OnBindViewHolder` 方法，以便使用数据源中的内容来填充指定行位置处的项。 `OnBindViewHolder` 获取指定行位置（照片的图像资源和照片标题的字符串）的照片信息，并将此数据复制到关联的视图中。 视图通过存储在视图容器对象中的引用来定位（通过 `holder` 参数传入）：

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

在使用传入的视图持有者对象之前，必须先将该对象强制转换为派生的视图持有者类型（在本例中为 `PhotoViewHolder`）。
适配器将图像资源加载到视图持有者的 `Image` 属性所引用的视图中，并将标题文本复制到视图持有者的 `Caption` 属性所引用的视图中。 这会将关联视图与其数据*绑定*在一起。

请注意，`OnBindViewHolder` 是直接处理数据结构的代码。 在这种情况下，`OnBindViewHolder` 了解如何将 `RecyclerView` 项位置映射到数据源中其关联数据项。 在这种情况下，映射很简单，因为位置可用作相册中的数组索引;但是，更复杂的数据源可能需要额外的代码来建立此类映射。

#### <a name="itemcount"></a>ItemCount

`ItemCount` 方法返回数据集合中的项数。 在示例照片查看器应用程序中，项计数是相册中照片的数目：

```csharp
public override int ItemCount
{
    get { return mPhotoAlbum.NumPhotos; }
}
```

有关 `RecyclerView.Adapter`的详细信息，请参阅[RecyclerView 类引用](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.Adapter.html)。

### <a name="putting-it-all-together"></a>将其全部放在一起

示例照片应用的生成的 `RecyclerView` 实现包括创建数据源、布局管理器和适配器的 `MainActivity` 代码。 `MainActivity` 创建 `mRecyclerView` 实例，实例化数据源和适配器，并插入布局管理器和适配器：

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

`PhotoViewHolder` 查找并缓存视图引用：

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

`PhotoAlbumAdapter` 实现了三个必需的方法重写：

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

[通过垂直滚动照片卡![两个屏幕截图](recyclerview-example-images/03-recyclerviewer-basic-sml.png)](recyclerview-example-images/03-recyclerviewer-basic.png#lightbox)

如果未绘制阴影（如上面的屏幕截图中所示），请编辑**Properties/androidmanifest.xml** ，并将以下属性设置添加到 `<application>` 元素：

```xml
android:hardwareAccelerated="true"
```

此基本应用仅支持浏览相册。 它不响应项触控事件，也不处理基础数据中的更改。 此功能是在[扩展 RecyclerView 示例](~/android/user-interface/layouts/recycler-view/extending-the-example.md)中添加的。

### <a name="changing-the-layoutmanager"></a>更改 LayoutManager

由于 `RecyclerView`的灵活性，因此可轻松地将应用程序修改为使用不同的布局管理器。 在下面的示例中，将对其进行修改，以显示带有水平滚动而不是垂直线性布局的网格布局的相册。 为此，布局管理器实例化将被修改为使用 `GridLayoutManager` 如下所示：

```csharp
mLayoutManager = new GridLayoutManager(this, 2, GridLayoutManager.Horizontal, false);
```

此代码更改将垂直 `LinearLayoutManager` 替换为一个 `GridLayoutManager`，该网格显示由两行组成的网格，这两行沿水平方向滚动。 再次编译并运行应用程序时，将看到照片显示在网格中，并且滚动是水平而不是垂直的：

[在网格中使用水平滚动照片的应用的![示例屏幕截图](recyclerview-example-images/04-gridlayoutmanager-sml.png)](recyclerview-example-images/04-gridlayoutmanager.png#lightbox)

通过只更改一行代码，可以修改照片查看应用程序，以使用具有不同行为的不同布局。
请注意，适配器代码和布局 XML 都必须修改以更改布局样式。 

在下一主题中，[扩展了 RecyclerView 示例](~/android/user-interface/layouts/recycler-view/extending-the-example.md)，扩展了此基本示例应用程序以处理项单击事件，并在基础数据源更改时更新 `RecyclerView`。

## <a name="related-links"></a>相关链接

- [RecyclerViewer （示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer)
- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [RecyclerView 部件和功能](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)
- [扩展 RecyclerView 示例](~/android/user-interface/layouts/recycler-view/extending-the-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
