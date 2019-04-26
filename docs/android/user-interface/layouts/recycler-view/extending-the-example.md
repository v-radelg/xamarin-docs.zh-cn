---
title: 扩展 RecyclerView 示例
description: 将项的单击事件处理程序添加到 RecyclerView 示例应用。
ms.prod: xamarin
ms.assetid: 707EE1CE-C164-485B-944C-82C6795E8A24
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/13/2018
ms.openlocfilehash: eca0f58a470228ce8e6331defe88c1ef727cef57
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61035977"
---
# <a name="extending-the-recyclerview-example"></a>扩展 RecyclerView 示例


基本应用程序中所述[基本 RecyclerView 示例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)实际上并未执行其他操作&ndash;它只需将滚动，并显示照片项，以方便浏览的固定的列表。 在实际应用程序中，用户希望能够通过点击中显示的项与应用程序进行交互。 此外，可以更改基础数据源 （或由应用更改），并显示的内容必须保持与这些更改相一致。 在以下部分中，您将学习如何以处理项的单击事件并更新`RecyclerView`当基础数据源发生更改。


### <a name="handling-item-click-events"></a>处理项单击事件

当用户触摸中的项`RecyclerView`，生成的项的单击事件以通知项已使用哪种应用。 此事件不由生成`RecyclerView`&ndash;相反，项视图 （这包装在视图持有者） 检测到收尾工作了，并报告这些收尾工作了单击事件。

为了说明如何处理项单击事件，以下步骤介绍如何向报表有哪些照片已使用过的用户修改基本的照片查看应用。 示例应用程序中的项的单击事件时，按以下顺序发生：

1.  照片的`CardView`检测项 click 事件，并向适配器通知。

2.  该适配器将 （具有项的位置信息） 的事件转发到活动的项的单击处理程序。

3.  活动的项的单击处理程序对应项的单击事件。

首先，调用事件处理程序成员`ItemClick`添加到`PhotoAlbumAdapter`类定义：

```csharp
public event EventHandler<int> ItemClick;
```

接下来，将一个项的单击事件处理程序方法添加到`MainActivity`。
此处理程序会短暂地显示 toast 通知，指示已使用哪些照片项：

```csharp
void OnItemClick (object sender, int position)
{
    int photoNum = position + 1;
    Toast.MakeText(this, "This is photo number " + photoNum, ToastLength.Short).Show();
}

```

接下来，需要一行代码来注册`OnItemClick`处理程序替换`PhotoAlbumAdapter`。 执行此操作的好时机是后立即`PhotoAlbumAdapter`创建： 

```csharp
mAdapter = new PhotoAlbumAdapter (mPhotoAlbum);
mAdapter.ItemClick += OnItemClick;

```

在此基本示例中，处理程序注册会在主活动中的发生`OnCreate`方法，但生产应用程序可能注册中的处理程序`OnResume`并将其在注销`OnPause`&ndash;请参阅[活动生命周期](~/android/app-fundamentals/activity-lifecycle/index.md)有关详细信息。

`PhotoAlbumAdapter` 现在，将调用`OnItemClick`当它收到的项的单击事件。 下一步是在引发此适配器中创建一个处理程序`ItemClick`事件。 下面的方法， `OnClick`，紧跟在其后适配器的添加`ItemCount`方法：

```csharp
void OnClick (int position)
{
    if (ItemClick != null)
        ItemClick (this, position);
}
```

这`OnClick`方法是适配器*侦听器*从项视图项的单击事件。 项视图 （通过项目视图的视图持有者），可以注册此侦听器之前`PhotoViewHolder`构造函数，必须修改以接受作为附加参数，此方法并注册`OnClick`与项目视图`Click`事件。
下面是修改后`PhotoViewHolder`构造函数：

```csharp
public PhotoViewHolder (View itemView, Action<int> listener)
    : base (itemView)
{
    Image = itemView.FindViewById<ImageView> (Resource.Id.imageView);
    Caption = itemView.FindViewById<TextView> (Resource.Id.textView);

    itemView.Click += (sender, e) => listener (base.LayoutPosition);
}

```

`itemView`参数包含对引用`CardView`，已使用过的用户。 请注意视图持有者基类知道项的布局位置 (`CardView`)，它表示 (通过`LayoutPosition`属性)，并且此位置传递给适配器的`OnClick`发生某项的单击事件的方法。 该适配器`OnCreateViewHolder`方法修改传递适配器的`OnClick`视图所有者的构造函数的方法：

```csharp
PhotoViewHolder vh = new PhotoViewHolder (itemView, OnClick);
```

现在当生成并运行示例照片查看应用，点击显示在照片将导致 toast 通知显示报告已使用的照片：

[![点击照片卡时，将显示的示例 Toast](extending-the-example-images/01-photo-selected-sml.png)](extending-the-example-images/01-photo-selected.png#lightbox)

此示例演示一种方法用于实现事件处理程序替换`RecyclerView`。 无法在此处使用的另一种方法是将事件放在视图持有者并让订阅这些事件的适配器。 如果示例照片应用程序提供照片编辑功能，将需要单独的事件`ImageView`并`TextView`在每个`CardView`： 涉及`TextView`在启动时`EditView`使用户可以编辑的对话框标题和涉及`ImageView`在启动时使用户可以裁剪或旋转照片的照片 touchup 工具。 具体取决于您的应用程序的需要，您必须设计用于处理和响应触摸事件的最佳做法。

若要演示如何`RecyclerView`时可以修改数据集发生更改，示例照片查看应用程序以随机数据源中选择一张照片，并与第一张照片，将其切换可更新。 首先，**随机选取**按钮添加到示例照片应用**Main.axml**布局：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <Button
        android:id="@+id/randPickButton"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:gravity="center_horizontal"
        android:textAppearance="?android:attr/textAppearanceLarge"
        android:text="Random Pick" />
    <android.support.v7.widget.RecyclerView
        android:id="@+id/recyclerView"
        android:scrollbars="vertical"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent" />
</LinearLayout>
```

接下来，代码将添加到主活动的末尾`OnCreate`方法查找`Random Pick`按钮布局，并将一个处理程序附加到它：

```csharp
Button randomPickBtn = FindViewById<Button>(Resource.Id.randPickButton);

randomPickBtn.Click += delegate
{
    if (mPhotoAlbum != null)
    {
        // Randomly swap a photo with the first photo:
        int idx = mPhotoAlbum.RandomSwap();
    }
};

```

此处理程序调用照片相册`RandomSwap`方法时**随机选取**点击按钮。 `RandomSwap`方法随机交换数据源中的第一个照片的照片，然后返回随机交换照片的索引。 当编译和运行此代码示例应用时，点击**随机选取**因为，按钮不会导致显示更改`RecyclerView`并不知道对数据源的更改。

要保留`RecyclerView`后的数据源的更改，更新**随机选取**单击处理程序必须进行修改，以调用适配器的`NotifyItemChanged`方法的已更改的集合中每个项 （在这种情况下，两个项具有更改： 第一张照片和交换的照片)。 这将导致`RecyclerView`以更新其显示，以便与数据源的新状态保持一致：

```csharp
Button randomPickBtn = FindViewById<Button>(Resource.Id.randPickButton);

randomPickBtn.Click += delegate
{
    if (mPhotoAlbum != null)
    {
        int idx = mPhotoAlbum.RandomSwap();

        // First photo has changed:
        mAdapter.NotifyItemChanged(0);

        // Swapped photo has changed:
        mAdapter.NotifyItemChanged(idx);
    }
};

```

现在，当**随机选取**点击按钮，`RecyclerView`更新显示器以显示，照片进一步向下集合中具有尚未被替换集合中第一张照片：

[![第一个交换，交换后的第二个屏幕快照前的屏幕截图](extending-the-example-images/02-random-pick-sml.png)](extending-the-example-images/02-random-pick.png#lightbox)

当然`NotifyDataSetChanged`可能已调用而不是使两个对`NotifyItemChanged`，但这样做因此将强制`RecyclerView`刷新整个集合，即使集合中的只有两个项已更改。 调用`NotifyItemChanged`是相对于调用变得更加有效`NotifyDataSetChanged`。


## <a name="related-links"></a>相关链接

- [RecyclerViewer （示例）](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)
- [RecyclerView 部件和功能](~/android/user-interface/layouts/recycler-view/parts-and-functionality.md)
- [基本 RecyclerView 示例](~/android/user-interface/layouts/recycler-view/recyclerview-example.md)
- [RecyclerView](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
