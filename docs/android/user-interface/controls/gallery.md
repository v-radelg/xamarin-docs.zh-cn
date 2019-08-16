---
title: Android 库控件
ms.prod: xamarin
ms.assetid: 3112E68A-7853-B147-90A6-6295CA2C4CB5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/15/2018
ms.openlocfilehash: 04fac4084328c0af962282dea7a31e3c00457bef
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69523005"
---
# <a name="xamarinandroid-gallery-control"></a>Xamarin Android 库控件

[`Gallery`](xref:Android.Widget.Gallery)是一个布局小组件, 用于显示水平滚动列表中的项, 并将当前所选内容置于视图的中心。

> [!IMPORTANT]
> Android 4.1 (API 级别 16) 已弃用此小组件。 

在本教程中, 你将创建一个照片库, 并在每次选择库项时显示 toast 消息。

为内容视图设置`Gallery` [`FindViewById`](xref:Android.App.Activity.FindViewById*)布局后, 将从布局中捕获。 `Main.axml`
此[`Adapter`](xref:Android.Widget.AdapterView.RawAdapter)
然后, 使用属性将自定义适配器 ( `ImageAdapter`) 设置为要在 dallery 中显示的所有项的源。 `ImageAdapter`在下一步中创建。

若要在单击库中的项时执行操作, 请将匿名委托订阅到[`ItemClick`](xref:Android.Widget.AdapterView.ItemClick)
事件的参数。 它显示了[`Toast`](xref:Android.Widget.Toast)
这会显示 theselected 项的索引位置 (从零开始) (在实际情况下, 该位置可用于获取其他某个任务的完整大小的图像)。

首先, 有几个成员变量, 其中包括引用可绘制资源目录中保存的图像的 Id 的数组 (**资源/可绘制**)。

接下来是类构造函数, 其中[`Context`](xref:Android.Content.Context)
`ImageAdapter`为实例定义并保存到本地字段。
接下来, 实现从继承的[`BaseAdapter`](xref:Android.Widget.BaseAdapter)一些必需方法。
构造函数和[`Count`](xref:Android.Widget.BaseAdapter.Count)
属性一目了然。 往常[`GetItem(int)`](xref:Android.Widget.BaseAdapter.GetItem*)
应返回适配器中指定位置的实际对象, 但对于此示例, 它将被忽略。 同样[`GetItemId(int)`](xref:Android.Widget.BaseAdapter.GetItemId*)
应返回项的行 id, 但此处不需要。

方法执行工作以将映像应用到[`ImageView`](xref:Android.Widget.ImageView)
将嵌入到[`Gallery`](xref:Android.Widget.Gallery)
在此方法中, 成员[`Context`](xref:Android.Content.Context)
用于创建新[`ImageView`](xref:Android.Widget.ImageView)的。
此[`ImageView`](xref:Android.Widget.ImageView)
通过从可绘制资源的本地数组应用映像来准备, 并将[`Gallery.LayoutParams`](xref:Android.Widget.Gallery.LayoutParams)
图像的高度和宽度, 将刻度设置为适合[`ImageView`](xref:Android.Widget.ImageView)
维度, 最后将背景设置为使用构造函数中获取的 styleable 属性。

有关[`ImageView.ScaleType`](xref:Android.Widget.ImageView.ScaleType)其他图像缩放选项, 请参阅。

## <a name="walkthrough"></a>演练

启动名为*HelloGallery*的新项目。

[![新建解决方案对话框中的新 Android 项目的屏幕截图](gallery-images/hellogallery1-sml.png)](gallery-images/hellogallery1.png#lightbox)

查找要使用的一些照片, 或[下载这些示例图像](https://developer.android.com/shareables/sample_images.zip)。
将图像文件添加到项目的**资源/可绘制**目录。 在 "**属性**" 窗口中, 将 "生成操作" 设置为 " **AndroidResource**"。

打开 **Resources/Layout/Main.axml** 并插入以下代码：

```xml
<?xml version="1.0" encoding="utf-8"?>
<Gallery xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/gallery"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
/>
```

打开`MainActivity.cs`并插入以下代码:[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
付款方式

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Set our view from the "main" layout resource
    SetContentView (Resource.Layout.Main);

    Gallery gallery = (Gallery) FindViewById<Gallery>(Resource.Id.gallery);

    gallery.Adapter = new ImageAdapter (this);

    gallery.ItemClick += delegate (object sender, Android.Widget.AdapterView.ItemClickEventArgs args) {
        Toast.MakeText (this, args.Position.ToString (), ToastLength.Short).Show ();
    };
}
```

创建名`ImageAdapter`为子类[`BaseAdapter`](xref:Android.Widget.BaseAdapter)的新类:

```csharp
public class ImageAdapter : BaseAdapter
{
    Context context;

    public ImageAdapter (Context c)
    {
          context = c;
    }

    public override int Count { get { return thumbIds.Length; } }

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
          ImageView i = new ImageView (context);

          i.SetImageResource (thumbIds[position]);
          i.LayoutParameters = new Gallery.LayoutParams (150, 100);
          i.SetScaleType (ImageView.ScaleType.FitXy);

          return i;
    }

    // references to our images
    int[] thumbIds = {
            Resource.Drawable.sample_1,
            Resource.Drawable.sample_2,
            Resource.Drawable.sample_3,
            Resource.Drawable.sample_4,
            Resource.Drawable.sample_5,
            Resource.Drawable.sample_6,
            Resource.Drawable.sample_7
     };
}

```

运行该应用程序。 它应类似于以下屏幕截图:

![显示示例图像的 HelloGallery 屏幕截图](gallery-images/hellogallery3.png)

## <a name="references"></a>参考资料

- [`BaseAdapter`](xref:Android.Widget.BaseAdapter)
- [`Gallery`](xref:Android.Widget.Gallery)
- [`ImageView`](xref:Android.Widget.ImageView)

_此页面的某些部分是基于 Android 开源项目创建和共享的工作的修改, 并根据[创造性 Commons 2.5 归属许可证](http://creativecommons.org/licenses/by/2.5/)中所述的条款使用。_
