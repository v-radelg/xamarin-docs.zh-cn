---
title: GridView
ms.prod: xamarin
ms.assetid: 6992C4FF-ECBB-3493-AEE6-3E063C1A8C54
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 63164d90419f3a49d9eb52a52d02e05fbee43dbf
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61310244"
---
# <a name="gridview"></a>GridView

[`GridView`](https://developer.xamarin.com/api/type/Android.Widget.GridView/) 是 [`ViewGroup`](https://developer.xamarin.com/api/type/Android.Views.ViewGroup/)
二维、 可滚动的网格中显示项。 网格项自动插入布局使用[ `ListAdapter` ](https://developer.xamarin.com/api/property/Android.App.ListActivity.ListAdapter/)。

在本教程中，将创建的图像缩略图的网格。 选择某个项后的 toast 消息将显示的图像的位置。

启动一个名为的新项目**HelloGridView**。

找到你想要使用，一些照片或[下载这些示例图像](https://developer.android.com/shareables/sample_images.zip)。 将图像文件添加到项目的**资源/Drawable**目录。 在中**属性**窗口中，将生成操作设置为**AndroidResource**。

打开**Resources/Layout/Main.axml**文件并插入以下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridView xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/gridview"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:columnWidth="90dp"
    android:numColumns="auto_fit"
    android:verticalSpacing="10dp"
    android:horizontalSpacing="10dp"
    android:stretchMode="columnWidth"
    android:gravity="center"
/>
```

这[ `GridView` ](https://developer.xamarin.com/api/type/Android.Widget.GridView/)将填充整个屏幕。 而是可以自我说明包含以下属性。 有关有效的特性的详细信息，请参阅[ `GridView` ](https://developer.xamarin.com/api/type/Android.Widget.GridView/)引用。

打开`HelloGridView.cs`并插入以下代码 [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/)
方法：

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    SetContentView (Resource.Layout.Main);

    var gridview = FindViewById<GridView> (Resource.Id.gridview);
    gridview.Adapter = new ImageAdapter (this);

    gridview.ItemClick += delegate (object sender, AdapterView.ItemClickEventArgs args) {
        Toast.MakeText (this, args.Position.ToString (), ToastLength.Short).Show ();
    };
}
```

之后**Main.axml**的内容视图中，设置布局[ `GridView` ](https://developer.xamarin.com/api/type/Android.Widget.GridView/)与布局从捕获[ `FindViewById` ](https://developer.xamarin.com/api/member/Android.App.Activity.FindViewById/)。 的 [`Adapter`](https://developer.xamarin.com/api/property/Android.Widget.AdapterView.RawAdapter/)
然后使用属性来设置自定义适配器 (`ImageAdapter`) 作为要在网格中显示的所有项的源。 `ImageAdapter`在下一步中创建。

若要在网格中的某个项单击时执行某些操作，一个匿名委托，它订阅[ `ItemClick` ](https://developer.xamarin.com/api/event/Android.Widget.AdapterView.ItemClick/)事件。
它显示[ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/) ，它显示选定项的索引位置 （从零开始） （在实际方案中，位置可用来获取另一项任务的实际尺寸的图像）。 请注意 Java 样式侦听器类，可以使用而不是.NET 事件。

创建一个名为的新类`ImageAdapter`子类[ `BaseAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/):

```csharp
public class ImageAdapter : BaseAdapter
{
    Context context;

    public ImageAdapter (Context c)
    {
        context = c;
    }

    public override int Count {
        get { return thumbIds.Length; }
    }

    public override Java.Lang.Object GetItem (int position)
    {
        return null;
    }

    public override long GetItemId (int position)
    {
        return 0;
    }

    // create a new ImageView for each item referenced by the Adapter
    public override View GetView (int position, View convertView, ViewGroup parent)
    {
        ImageView imageView;

        if (convertView == null) {  // if it's not recycled, initialize some attributes
            imageView = new ImageView (context);
            imageView.LayoutParameters = new GridView.LayoutParams (85, 85);
            imageView.SetScaleType (ImageView.ScaleType.CenterCrop);
            imageView.SetPadding (8, 8, 8, 8);
        } else {
            imageView = (ImageView)convertView;
        }

        imageView.SetImageResource (thumbIds[position]);
        return imageView;
    }

    // references to our images
    int[] thumbIds = {
        Resource.Drawable.sample_2, Resource.Drawable.sample_3,
        Resource.Drawable.sample_4, Resource.Drawable.sample_5,
        Resource.Drawable.sample_6, Resource.Drawable.sample_7,
        Resource.Drawable.sample_0, Resource.Drawable.sample_1,
        Resource.Drawable.sample_2, Resource.Drawable.sample_3,
        Resource.Drawable.sample_4, Resource.Drawable.sample_5,
        Resource.Drawable.sample_6, Resource.Drawable.sample_7,
        Resource.Drawable.sample_0, Resource.Drawable.sample_1,
        Resource.Drawable.sample_2, Resource.Drawable.sample_3,
        Resource.Drawable.sample_4, Resource.Drawable.sample_5,
        Resource.Drawable.sample_6, Resource.Drawable.sample_7
    };
}
```

首先，这会实现继承自某些所需的方法[ `BaseAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/)。 构造函数和[ `Count` ](https://developer.xamarin.com/api/property/Android.Widget.BaseAdapter.Count/)属性都很容易理解。 通常情况下， [`GetItem(int)`](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetItem/)
应返回在适配器中，指定的位置的实际对象，但它对于此示例中，将忽略。 同样， [`GetItemId(int)`](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetItemId/)
应返回的行 id 的项，但此处不需要它。

第一种方法需要[ `GetView()` ](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetView/)。
此方法创建一个新 [`View`](https://developer.xamarin.com/api/type/Android.Views.View/)
每个映像添加到`ImageAdapter`。 此调用时， [`View`](https://developer.xamarin.com/api/type/Android.Views.View/)
传递中，这通常是回收的对象 (至少一次已被调用后)，因此可以进行检查以查看该对象是否为 null。 如果它*是*为 null， [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)
实例化并使用映像演示文稿的所需属性配置：

- [`LayoutParams`](https://developer.xamarin.com/api/property/Android.Views.View.LayoutParameters/) 设置视图的高度和宽度&mdash;这可确保，无论可绘制的大小，每个图像进行大小调整和裁剪这些维度，根据需要中容纳不下。

- [`SetScaleType()`](https://developer.xamarin.com/api/member/Android.Widget.ImageView.SetScaleType/) 声明应向中心裁剪图像，就 （如有必要）。

- [`SetPadding(int, int, int, int)`](https://developer.xamarin.com/api/member/Android.Views.View.SetPadding/) 定义所有边的填充。 （请注意，是否映像具有不同的纵横比，则更少填充将导致为多个裁剪图像的如果提供给 imageview 各自的维数不匹配。）

如果[ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/)传递给[ `GetView()` ](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetView/)是*不*为 null，则本地 [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)
初始化与回收[ `View` ](https://developer.xamarin.com/api/type/Android.Views.View/)对象。

在末尾 [`GetView()`](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetView/)
方法中，`position`整数传递给该方法用于选择中的映像`thumbIds`数组，它被设置为映像资源[ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)。

剩下的就是定义`thumbIds`可绘制资源的数组。

运行该应用程序。 网格布局应如下所示：

[![GridView 显示 15 图像的屏幕截图示例](grid-view-images/helloviews4.png)](grid-view-images/helloviews4.png#lightbox)

尝试试验的行为[ `GridView` ](https://developer.xamarin.com/api/type/Android.Widget.GridView/)和 [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)
通过调整它们的属性的元素。 例如，而不是使用[ `LayoutParams` ](https://developer.xamarin.com/api/property/Android.Views.View.LayoutParameters/)尝试使用[ `SetAdjustViewBounds()` ](https://developer.xamarin.com/api/member/Android.Widget.ImageView.SetAdjustViewBounds/)。


## <a name="references"></a>参考资料

-   [`GridView`](https://developer.xamarin.com/api/type/Android.Widget.GridView/) 
-   [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)
-   [`BaseAdapter`](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/)。

*此页的部分是基于工作创建和共享通过 Android 的开放源项目和使用中所述的条款的修改*
[*Creative Commons 2.5 Attribution 许可证*](http://creativecommons.org/licenses/by/2.5/).
