---
title: Xamarin TableLayout
ms.prod: xamarin
ms.assetid: 0C7B9C95-5E5F-A069-BA37-984E49F7DCAD
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 1c477f030dc69394ba601b31d71a772f5037af48
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69522306"
---
# <a name="xamarinandroid-tablelayout"></a>Xamarin TableLayout

[`TableLayout`](xref:Android.Widget.TableLayout)是[`ViewGroup`](xref:Android.Views.ViewGroup)
显示子元素的[`View`](xref:Android.Views.View)
行和列中的元素。

启动名为**HelloTableLayout**的新项目。

打开**Resources/Layout/main.axml**文件, 并插入以下内容:

```xml
<?xml version="1.0" encoding="utf-8"?>
<TableLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:stretchColumns="1">

    <TableRow>
        <TextView
            android:layout_column="1"
            android:text="Open..."
            android:padding="3dip"/>
        <TextView
            android:text="Ctrl-O"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <TableRow>
        <TextView
            android:layout_column="1"
            android:text="Save..."
            android:padding="3dip"/>
        <TextView
            android:text="Ctrl-S"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <TableRow>
        <TextView
            android:layout_column="1"
            android:text="Save As..."
            android:padding="3dip"/>
        <TextView
            android:text="Ctrl-Shift-S"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <View
        android:layout_height="2dip"
        android:background="#FF909090"/>

    <TableRow>
        <TextView
            android:text="X"
            android:padding="3dip"/>
        <TextView
            android:text="Import..."
            android:padding="3dip"/>
    </TableRow>

    <TableRow>
        <TextView
            android:text="X"
            android:padding="3dip"/>
        <TextView
            android:text="Export..."
            android:padding="3dip"/>
        <TextView
            android:text="Ctrl-E"
            android:gravity="right"
            android:padding="3dip"/>
    </TableRow>

    <View
        android:layout_height="2dip"
        android:background="#FF909090"/>

    <TableRow>
        <TextView
            android:layout_column="1"
            android:text="Quit"
            android:padding="3dip"/>
    </TableRow>
</TableLayout>
```

请注意, 这与 HTML 表的结构类似。 此[`TableLayout`](xref:Android.Widget.TableLayout)
元素类似于 HTML `<table>`元素;[`TableRow`](xref:Android.Widget.TableRow)
与`<tr>`元素类似, 但对于单元, 可以使用任何类型的[`View`](xref:Android.Views.View)元素。 在此示例中,[`TextView`](xref:Android.Widget.TextView)
用于每个单元格。 在某些行之间, 还有一个用于绘制水平线的 " [`View`](xref:Android.Views.View)基本"。

请确保**HelloTableLayout**活动在[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
付款方式

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

[`Activity`](xref:Android.App.Activity) &mdash; `Resource.Layout.Main` ) 方法加载的布局文件, 该文件由资源 ID 指定, 表示资源/布局/main.axml 布局文件。 [`SetContentView(int)`](xref:Android.App.Activity.SetContentView*)

运行该应用程序。 应该会看到以下内容:

[![显示多个表行的 TableLayout 应用程序的示例屏幕截图](table-layout-images/helloviews3.png)](table-layout-images/helloviews3.png#lightbox)



## <a name="references"></a>参考资料

- [`TableLayout`](xref:Android.Widget.TableLayout)
- [`TableRow`](xref:Android.Widget.TableRow)
- [`TextView`](xref:Android.Widget.TextView)

_此页面的某些部分是基于 Android 开源项目创建和共享的工作的修改, 并根据[创造性 Commons 2.5 归属许可证](http://creativecommons.org/licenses/by/2.5/)中所述的条款使用。_
