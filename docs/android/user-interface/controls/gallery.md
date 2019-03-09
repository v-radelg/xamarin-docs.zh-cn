---
title: 库
ms.prod: xamarin
ms.assetid: 3112E68A-7853-B147-90A6-6295CA2C4CB5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/15/2018
ms.openlocfilehash: f9b73428531deeacc7bdea271cdc0c2872038e99
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57666954"
---
# <a name="gallery"></a>库

[`Gallery`](https://developer.xamarin.com/api/type/Android.Widget.Gallery/) 是用于在水平滚动的列表中显示项的布局小组件并将当前所选内容放置在视图的中心。

> [!IMPORTANT]
> 在 Android 4.1 （API 级别 16） 中已弃用此小组件。 

在本教程中，将创建的照片库，然后选择库项每次显示的 toast 消息。

之后`Main.axml`为内容视图中，设置布局`Gallery`与布局从捕获[ `FindViewById` ](https://developer.xamarin.com/api/member/Android.App.Activity.FindViewById/p/System.Int32/)。
的 [`Adapter`](https://developer.xamarin.com/api/property/Android.Widget.AdapterView.RawAdapter/)
然后使用属性来设置自定义适配器 ( `ImageAdapter`) 作为显示在 dallery 中所有项目的源。 `ImageAdapter`在下一步中创建。

若要单击库中的项时执行某些操作，一个匿名委托，它订阅 [`ItemClick`](https://developer.xamarin.com/api/event/Android.Widget.AdapterView.ItemClick/)
事件的参数。 它显示了 [`Toast`](https://developer.xamarin.com/api/type/Android.Widget.Toast/)
显示选项的索引位置 （从零开始） （在实际方案中，位置可用来获取另一项任务的实际尺寸的图像）。

首先，有几个成员变量，包括的引用保存在可绘制资源目录中的映像的 Id 的数组 (**可绘制资源/**)。

接下来是类构造函数，其中 [`Context`](https://developer.xamarin.com/api/type/Android.Content.Context/)
有关`ImageAdapter`定义实例并将其保存到本地字段。
接下来，这会实现继承自某些所需的方法[ `BaseAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/)。
构造函数和 [`Count`](https://developer.xamarin.com/api/property/Android.Widget.BaseAdapter.Count/)
属性都很容易理解。 通常情况下， [`GetItem(int)`](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetItem/p/System.Int32/)
应返回在适配器中，指定的位置的实际对象，但它对于此示例中，将忽略。 同样， [`GetItemId(int)`](https://developer.xamarin.com/api/member/Android.Widget.BaseAdapter.GetItemId/p/System.Int32/)
应返回的行 id 的项，但此处不需要它。

方法执行的工作要应用到图像 [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)
将嵌入的 [`Gallery`](https://developer.xamarin.com/api/type/Android.Widget.Gallery/)
在此方法中，成员 [`Context`](https://developer.xamarin.com/api/type/Android.Content.Context/)
用于创建一个新[ `ImageView` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)。
的 [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)
通过应用的可绘制资源，设置本地数组中的映像准备 [`Gallery.LayoutParams`](https://developer.xamarin.com/api/type/Android.Widget.Gallery+LayoutParams/)
高度和宽度设置缩放以适合的图像 [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)
维度，然后再最后设置使用促升属性构造函数中获取的背景。

请参阅[ `ImageView.ScaleType` ](https://developer.xamarin.com/api/type/Android.Widget.ImageView+ScaleType/)其他图像缩放选项的。

## <a name="walkthrough"></a>演练

启动一个名为的新项目*HelloGallery*。

[![新的 Android 项目中的新解决方案对话框的屏幕截图](gallery-images/hellogallery1-sml.png)](gallery-images/hellogallery1.png#lightbox)

找到你想要使用，一些照片或[下载这些示例图像](https://developer.android.com/shareables/sample_images.zip)。
将图像文件添加到项目的**资源/Drawable**目录。 在中**属性**窗口中，将生成操作设置为**AndroidResource**。

打开 **Resources/Layout/Main.axml** 并插入以下代码：

```xml
<?xml version="1.0" encoding="utf-8"?>
<Gallery xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/gallery"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
/>
```

打开`MainActivity.cs`并插入以下代码 [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/)
方法：

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

创建一个名为的新类`ImageAdapter`子类[ `BaseAdapter` ](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/):

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

运行该应用程序。 它应如以下屏幕截图所示：

![显示示例图像 HelloGallery 的屏幕截图](gallery-images/hellogallery3.png)



## <a name="references"></a>参考资料

-   [`BaseAdapter`](https://developer.xamarin.com/api/type/Android.Widget.BaseAdapter/)
-   [`Gallery`](https://developer.xamarin.com/api/type/Android.Widget.Gallery/)
-   [`ImageView`](https://developer.xamarin.com/api/type/Android.Widget.ImageView/)

*此页的部分是基于工作创建和共享通过 Android 的开放源项目和使用中所述的条款的修改*
[*Creative Commons 2.5 Attribution 许可证*](http://creativecommons.org/licenses/by/2.5/).


