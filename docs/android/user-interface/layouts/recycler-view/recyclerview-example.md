---
title: 基本 RecyclerView 示例
description: 演示如何使用 RecyclerView 示例应用程序。
ms.prod: xamarin
ms.assetid: A50520D2-1214-40E1-9B27-B0891FE11584
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/30/2018
ms.openlocfilehash: d71c4f0f3221d06c22876329a5933273d8d6f92d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61184674"
---
# <a name="a-basic-recyclerview-example"></a>基本 RecyclerView 示例

若要了解如何`RecyclerView`典型的应用程序中的工作原理，本主题将探讨[RecyclerViewer](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer/)示例应用程序，使用一个简单的代码示例`RecyclerView`以显示大量的照片集合： 

[![使用 CardViews 显示照片的 RecyclerView 应用程序的两个屏幕快照](recyclerview-example-images/01-recyclerviewer-sml.png)](recyclerview-example-images/01-recyclerviewer.png#lightbox)

**RecyclerViewer**使用[CardView](~/android/user-interface/controls/card-view.md)实现中的每个照片项`RecyclerView`布局。 由于`RecyclerView`的性能优势，此示例应用是能够顺利且不明显延迟的情况下快速滚动大型照片集合。


### <a name="an-example-data-source"></a>示例数据源

在此示例应用中，"相册"数据源 (由`PhotoAlbum`类) 提供`RecyclerView`项内容。
`PhotoAlbum` 是一系列包含隐藏式字幕; 的照片在实例化它时，将获得现成 32 照片集合：

```csharp
PhotoAlbum mPhotoAlbum = new PhotoAlbum ();
```

在每个照片实例`PhotoAlbum`公开的属性使您可以阅读其映像资源 ID `PhotoID`，和它的标题字符串， `Caption`。 照片集合被组织方法规定索引器可以访问每张照片。 例如，以下代码行访问映像资源 ID 和标题的集合中的第十个照片：

```csharp
int imageId = mPhotoAlbum[9].ImageId;
string caption = mPhotoAlbum[9].Caption;
```

`PhotoAlbum` 此外提供了`RandomSwap`可调用以交换第一张照片集合中与集合中的其他位置的随机选择照片的方法：

```csharp
mPhotoAlbum.RandomSwap ();
```

因为的实现细节`PhotoAlbum`了解与不相关`RecyclerView`，则`PhotoAlbum`此处不提供源代码。 向源代码`PhotoAlbum`网址[PhotoAlbum.cs](https://github.com/xamarin/monodroid-samples/blob/master/android5.0/RecyclerViewer/RecyclerViewer/PhotoAlbum.cs)中[RecyclerViewer](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer/)示例应用程序。


### <a name="layout-and-initialization"></a>布局和初始化

布局文件中， **Main.axml**，包含单个`RecyclerView`内`LinearLayout`:

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

请注意，必须使用完全限定名称**android.support.v7.widget.RecyclerView**因为`RecyclerView`打包在支持库中。 `OnCreate`方法的`MainActivity`初始化此布局，在实例化适配器，并准备基础数据源：

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

此代码将执行以下操作：

1. 实例化`PhotoAlbum`数据源。

2. 将照片唱片集数据源传递到构造函数的适配器， `PhotoAlbumAdapter` （稍后在本指南中定义）。 
   请注意，它被视为最佳做法将数据源作为参数传递给适配器的构造函数。 

3. 获取`RecyclerView`从布局。

4. 插入到适配器`RecyclerView`实例通过调用`RecyclerView``SetAdapter`方法，如上面所示。

### <a name="layout-manager"></a>布局管理器

中的每项`RecyclerView`组成`CardView`，其中包含照片图像和照片标题 (中介绍详细信息[视图持有者](#view-holder)下面一节)。 预定义`LinearLayoutManager`用于每个布局`CardView`垂直滚动的排列方式：

```csharp
mLayoutManager = new LinearLayoutManager (this);
mRecyclerView.SetLayoutManager (mLayoutManager);
```

此代码驻留在主活动`OnCreate`方法。 布局管理器构造函数需要*上下文*，因此`MainActivity`使用传递`this`如上图所示。

而不是使用预定义`LinearLayoutManager`，可以显示两个自定义布局管理器中插入`CardView`项的并排方案，实现要遍历的照片集合翻页动画效果。 稍后在本指南中，将看到举例说明如何通过交换不同的布局管理器中修改布局。

<a name="view-holder" />

### <a name="view-holder"></a>视图持有者

调用视图持有者类`PhotoViewHolder`。 每个`PhotoViewHolder`实例包含对引用`ImageView`并`TextView`中的布局的相关的行项的`CardView`如图解此处：

[![包含 ImageView 和 TextView CardView 的关系图](recyclerview-example-images/02-cardview-layout-sml.png)](recyclerview-example-images/02-cardview-layout.png#lightbox)

`PhotoViewHolder` 派生自`RecyclerView.ViewHolder`并且包含用于存储对引用的属性`ImageView`和`TextView`上述布局中所示。
`PhotoViewHolder` 包含两个属性和一个构造函数：

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
在此代码示例中，`PhotoViewHolder`构造函数传递到父项目视图的引用 ( `CardView`) 的`PhotoViewHolder`包装。 请注意，你始终将转发父项视图到基构造函数。 `PhotoViewHolder`构造函数调用`FindViewById`对父项视图来查找其子视图引用的每个`ImageView`并`TextView`，将存储中的结果`Image`和`Caption`属性，分别。 适配器更高版本中检索视图引用从这些属性时它会更新此`CardView`的子视图使用新数据。

有关详细信息`RecyclerView.ViewHolder`，请参阅[RecyclerView.ViewHolder 类引用](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ViewHolder.html)。


### <a name="adapter"></a>适配器

适配器将加载每个`RecyclerView`具有特定照片的数据行。 为给定行位置处照片*P*，例如，适配器找到位置处的关联的数据*P*行到此数据在位置中的数据源和副本项*P*在`RecyclerView`集合。 适配器使用视图持有者来查找其引用`ImageView`并`TextView`中的该位置，使其不包含重复调用`FindViewById`的那些视图当用户滚动浏览照片集合和重用视图。

在中**RecyclerViewer**，一种适配器类派生自`RecyclerView.Adapter`若要创建`PhotoAlbumAdapter`:

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

`mPhotoAlbum`成员包含传递到构造函数中的数据源 （相册）; 构造函数将照片唱片集复制到此成员变量。 下列必需`RecyclerView.Adapter`方法实现：

-   **`OnCreateViewHolder`** &ndash; 实例化项布局文件和视图持有者。

-   **`OnBindViewHolder`** &ndash; 将指定位置处的数据加载到其引用存储在给定的视图持有者的视图。

-   **`ItemCount`** &ndash; 在数据源中返回的项数。

布局管理器调用这些方法，它将定位中的项而`RecyclerView`。 以下各节中检查这些方法的实现。


#### <a name="oncreateviewholder"></a>OnCreateViewHolder

布局管理器调用`OnCreateViewHolder`时`RecyclerView`需要新的视图拥有者，代表项目。 `OnCreateViewHolder` 增大该视图的布局文件从项目视图并将包装在新视图`PhotoViewHolder`实例。 `PhotoViewHolder`构造函数查找并存储在布局中的子视图的引用，如前面所述[视图持有者](#view-holder)。

每个行项都由`CardView`，其中包含`ImageView`（适用于照片） 和一个`TextView`（适用于标题）。 此布局文件中驻留**PhotoCardView.axml**:

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

此布局表示中的单个行项`RecyclerView`。 `OnBindViewHolder`方法 （如下所述） 将数据复制到数据源中`ImageView`和`TextView`的此布局。
`OnCreateViewHolder` 增大此布局中的给定的照片位置`RecyclerView`，并实例化一个新`PhotoViewHolder`实例 (其定位和缓存对引用`ImageView`和`TextView`中关联的子视图`CardView`布局):

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

生成的视图持有者实例， `vh`，返回到调用方 （布局管理器）。


#### <a name="onbindviewholder"></a>OnBindViewHolder

布局管理器何时可以显示中的特定视图`RecyclerView`的屏幕可视区域中，它会调用适配器的`OnBindViewHolder`方法来填充数据源中的内容的指定的行位置处的项。 `OnBindViewHolder` 获取指定的行位置 （照片的图像资源和照片的标题的字符串） 的照片信息并将此数据复制到关联的视图。 视图位于通过引用存储在视图持有者对象 (其传递通过`holder`参数):

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

传入的视图持有者对象必须先转换为派生的视图持有者类型 (在这种情况下， `PhotoViewHolder`) 使用它之前。
该适配器将图像资源加载到视图持有者的引用的视图`Image`属性，并将标题文本复制到引用的视图持有者的视图`Caption`属性。 这*绑定*其数据与关联的视图。

请注意，`OnBindViewHolder`是直接处理数据的结构的代码。 在这种情况下，`OnBindViewHolder`了解如何将映射`RecyclerView`项到其关联的数据项目在数据源中的位置。 映射是直接在这种情况下由于位置可用作到相册; 数组索引但是，更复杂的数据源可能需要额外的代码来建立此类映射。


#### <a name="itemcount"></a>ItemCount

`ItemCount`方法返回数据集合中项的数目。 在示例照片查看器应用中，项的计数是中的照片相册的照片数：

```csharp
public override int ItemCount
{
    get { return mPhotoAlbum.NumPhotos; }
}
```

有关详细信息`RecyclerView.Adapter`，请参阅[RecyclerView.Adapter 类引用](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.Adapter.html)。


### <a name="putting-it-all-together"></a>汇总所有内容

得到`RecyclerView`的示例照片应用程序的实现包括`MainActivity`创建数据源、 布局管理器和适配器的代码。 `MainActivity` 创建`mRecyclerView`实例，实例化的数据源和适配器，并插入布局管理器和适配器：

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

`PhotoAlbumAdapter` 实现三个所需的方法重写：

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

编译并运行此代码将创建基本的照片查看应用程序，如以下屏幕截图中所示：

[![照片查看使用垂直滚动照片卡应用程序的两个屏幕快照](recyclerview-example-images/03-recyclerviewer-basic-sml.png)](recyclerview-example-images/03-recyclerviewer-basic.png#lightbox)

如果 （如上面的屏幕截图中所示），将不会绘制阴影，编辑**properties/Androidmanifest.xml**并添加以下属性设置为`<application>`元素：

```xml
android:hardwareAccelerated="true"
```

此基本应用仅支持浏览照片相册。 它不响应以项的触摸事件，也不会处理基础数据中的更改。 此功能中添加[扩展 RecyclerView 示例](~/android/user-interface/layouts/recycler-view/extending-the-example.md)。




### <a name="changing-the-layoutmanager"></a>更改 LayoutManager

由于`RecyclerView`的灵活性，很容易地修改应用程序以使用不同的布局管理器。 在下面的示例，它修改为显示具有水平滚动的网格布局而不是具有垂直线性布局上的相册。 若要执行此操作，布局管理器实例化改为使用`GridLayoutManager`，如下所示：

```csharp
mLayoutManager = new GridLayoutManager(this, 2, GridLayoutManager.Horizontal, false);
```

此代码更改取代了垂直`LinearLayoutManager`与`GridLayoutManager`这将显示在水平方向上滚动的两个行组成的网格。 如果编译并再次运行该应用，将看到一个网格中显示的照片和滚动是水平，而不是垂直：

[![使用水平滚动网格中的照片应用程序的示例屏幕截图](recyclerview-example-images/04-gridlayoutmanager-sml.png)](recyclerview-example-images/04-gridlayoutmanager.png#lightbox)

通过更改只有一行代码，则可以修改照片查看应用程序以不同的行为与使用不同的布局。
请注意，适配器代码既不布局 XML 必须进行修改以更改布局样式。 

在下一主题[扩展 RecyclerView 示例](~/android/user-interface/layouts/recycler-view/extending-the-example.md)，此基本示例应用程序扩展以处理项的单击事件并更新`RecyclerView`当基础数据源发生更改。



## <a name="related-links"></a>相关链接

- [RecyclerViewer （示例）](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [RecyclerView 部件和功能](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)
- [扩展 RecyclerView 示例](~/android/user-interface/layouts/recycler-view/extending-the-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
