---
title: Xamarin LinearLayout
ms.prod: xamarin
ms.assetid: B49D129C-AF24-3C5A-C833-5A34AFBB2442
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/07/2018
ms.openlocfilehash: 14e9b352a309de94a374b52141e3fd61715d8f75
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764385"
---
# <a name="xamarinandroid-linearlayout"></a>Xamarin LinearLayout

[`LinearLayout`](xref:Android.Widget.LinearLayout)是[`ViewGroup`](xref:Android.Views.ViewGroup)
显示子元素的[`View`](xref:Android.Views.View)
垂直或水平的线性方向的元素。

你应谨慎使用[`LinearLayout`](xref:Android.Widget.LinearLayout)。
如果开始嵌套多个[`LinearLayout`](xref:Android.Widget.LinearLayout)，则可能需要考虑使用[`RelativeLayout`](xref:Android.Widget.RelativeLayout)
是.

启动名为**HelloLinearLayout**的新项目。

打开 **Resources/Layout/Main.axml** 并插入以下代码：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation=    "vertical"
    android:layout_width=    "match_parent"
    android:layout_height=    "match_parent"    >

  <LinearLayout
      android:orientation=    "horizontal"
      android:layout_width=    "match_parent"
      android:layout_height=    "match_parent"
      android:layout_weight=    "1"    >
      <TextView
          android:text=    "red"
          android:gravity=    "center_horizontal"
          android:background=    "#aa0000"
          android:layout_width=    "wrap_content"
          android:layout_height=    "match_parent"
          android:layout_weight=    "1"    />
      <TextView
          android:text=    "green"
          android:gravity=    "center_horizontal"
          android:background=    "#00aa00"
          android:layout_width=    "wrap_content"
          android:layout_height=    "match_parent"
          android:layout_weight=    "1"    />
      <TextView
          android:text=    "blue"
          android:gravity=    "center_horizontal"
          android:background=    "#0000aa"
          android:layout_width=    "wrap_content"
          android:layout_height=    "match_parent"
          android:layout_weight=    "1"    />
      <TextView
          android:text=    "yellow"
          android:gravity=    "center_horizontal"
          android:background=    "#aaaa00"
          android:layout_width=    "wrap_content"
          android:layout_height=    "match_parent"
          android:layout_weight=    "1"    />
  </LinearLayout>
        
  <LinearLayout
    android:orientation=    "vertical"
    android:layout_width=    "match_parent"
    android:layout_height=    "match_parent"
    android:layout_weight=    "1"    >
    <TextView
        android:text=    "row one"
        android:textSize=    "15pt"
        android:layout_width=    "match_parent"
        android:layout_height=    "wrap_content"
        android:layout_weight=    "1"    />
    <TextView
        android:text=    "row two"
        android:textSize=    "15pt"
        android:layout_width=    "match_parent"
        android:layout_height=    "wrap_content"
        android:layout_weight=    "1"    />
    <TextView
        android:text=    "row three"
        android:textSize=    "15pt"
        android:layout_width=    "match_parent"
        android:layout_height=    "wrap_content"
        android:layout_weight=    "1"    />
    <TextView
        android:text=    "row four"
        android:textSize=    "15pt"
        android:layout_width=    "match_parent"
        android:layout_height=    "wrap_content"
       android:layout_weight=    "1"    />
  </LinearLayout>

</LinearLayout>
```

请仔细检查此 XML。 存在根[`LinearLayout`](xref:Android.Widget.LinearLayout)
，它将其方向定义为&ndash;垂直， [`View`](xref:Android.Views.View)所有子级（其具有两个）将垂直堆积。 第一个子级是另一个[`LinearLayout`](xref:Android.Widget.LinearLayout)
使用水平方向并且第二个子元素是[`LinearLayout`](xref:Android.Widget.LinearLayout)
使用垂直方向的。 其中每个嵌套[`LinearLayout`](xref:Android.Widget.LinearLayout)的都包含多个[`TextView`](xref:Android.Widget.TextView)
元素，这些元素以其父[`LinearLayout`](xref:Android.Widget.LinearLayout)项定义的方式进行定向。

现在打开**HelloLinearLayout.cs** ，并确保它在中加载**资源/布局/main.axml**布局[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
付款方式

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

[`Activity`](xref:Android.App.Activity) &ndash; `Resources.Layout.Main` ）方法加载的布局文件，该文件由资源 ID 指定，表示资源/布局/main.axml 布局文件。 [`SetContentView(int)`](xref:Android.App.Activity.SetContentView*)

运行该应用程序。 应该会看到以下内容：

[![应用第一 LinearLayout 的屏幕截图，横向垂直排列，秒](linear-layout-images/helloviews1.png)](linear-layout-images/helloviews1.png#lightbox)

请注意，XML 特性如何定义每个视图的行为。 尝试试验不同的`android:layout_weight`值，以查看屏幕的房地产如何根据每个元素的权重进行分布。 有关如何操作的详细信息，请参阅[通用布局对象](https://developer.android.com/guide/topics/ui/declaring-layout.html)文档[`LinearLayout`](xref:Android.Widget.LinearLayout)
`android:layout_weight`处理特性。

## <a name="references"></a>参考资料

- [`LinearLayout`](xref:Android.Widget.LinearLayout)
- [`TextView`](xref:Android.Widget.TextView)

_此页面的某些部分是基于 Android 开源项目创建和共享的工作的修改，并根据[创造性 Commons 2.5 归属许可证](http://creativecommons.org/licenses/by/2.5/)中所述的条款使用。_
