---
title: Xamarin TableLayout
ms.prod: xamarin
ms.assetid: 0C7B9C95-5E5F-A069-BA37-984E49F7DCAD
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: 4175b1fa62b2bc0e4209d13934c2bdbdd1e2a085
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028751"
---
# <a name="xamarinandroid-tablelayout"></a>Xamarin TableLayout

[`TableLayout`](xref:Android.Widget.TableLayout)是[`ViewGroup`](xref:Android.Views.ViewGroup)
显示子[`View`](xref:Android.Views.View)
行和列中的元素。

启动名为**HelloTableLayout**的新项目。

打开**Resources/Layout/main.axml**文件，并插入以下内容：

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

请注意，这与 HTML 表的结构类似。 [`TableLayout`](xref:Android.Widget.TableLayout)
元素类似于 HTML `<table>` 元素;[`TableRow`](xref:Android.Widget.TableRow)
与 `<tr>` 元素类似;但对于单元，可以使用任何类型的[`View`](xref:Android.Views.View)元素。 在此示例中， [`TextView`](xref:Android.Widget.TextView)
用于每个单元格。 在某些行之间，还有一个用于绘制水平线的基本[`View`](xref:Android.Views.View)。

请确保**HelloTableLayout**活动会在[`OnCreate()`](xref:Android.App.Activity.OnCreate*)中加载此布局。
付款方式

```csharp
protected override void OnCreate (Bundle savedInstanceState)
{
    base.OnCreate (savedInstanceState);
    SetContentView (Resource.Layout.Main);
}
```

[`SetContentView(int)`](xref:Android.App.Activity.SetContentView*)）方法加载资源 ID &mdash; 指定的[`Activity`](xref:Android.App.Activity)的布局文件，`Resource.Layout.Main` 引用**资源/布局 main.axml**布局文件。

运行该应用程序。 应该会看到以下内容：

[显示多个表行的 TableLayout 应用的示例屏幕截图![](table-layout-images/helloviews3.png)](table-layout-images/helloviews3.png#lightbox)

## <a name="references"></a>reference

- [`TableLayout`](xref:Android.Widget.TableLayout)
- [`TableRow`](xref:Android.Widget.TableRow)
- [`TextView`](xref:Android.Widget.TextView)

_此页面的某些部分是基于 Android 开源项目创建和共享的工作的修改，并根据[创造性 Commons 2.5 归属许可证](https://creativecommons.org/licenses/by/2.5/)中所述的条款使用。_
