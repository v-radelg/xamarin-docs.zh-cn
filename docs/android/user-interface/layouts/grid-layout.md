---
title: GridLayout
ms.prod: xamarin
ms.assetid: B69A4BF5-9CFB-443A-9F7B-062D1E498F61
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 45e625b28bbdf0009b5cfcf661b00ce17638771d
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57667225"
---
# <a name="gridlayout"></a>GridLayout

`GridLayout`是一种新`ViewGroup`支持布局类似于 HTML 表的 2D 网格中的视图，如下所示的子类：

 [![裁剪 GridLayout 显示四个单元格](grid-layout-images/21-gridlayoutcropped.png)](grid-layout-images/21-gridlayoutcropped.png#lightbox)

 `GridLayout` 适用于平面视图层次结构，其中子视图及其位置的网格中设置通过指定行和其应有的列。 这样一来， *GridLayout*能够在网格中定位视图，而无需任何中间视图提供的表结构，如 TableLayout 中使用的表行中所示。 通过维护平面层次结构中， *GridLayout*能够将更迅速布局其子视图。 让我们看看示例演示了这一概念的实际含义在代码中。


## <a name="creating-a-grid-layout"></a>创建网格布局

以下 XML 添加了多种`TextView`控件添加到*GridLayout*。

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="2"
        android:columnCount="2">
     <TextView
            android:text="Cell 0"
            android:textSize="14dip" />
     <TextView
            android:text="Cell 1"
            android:textSize="14dip" />
     <TextView
            android:text="Cell 2"
            android:textSize="14dip" />
     <TextView
            android:text="Cell 3"
            android:textSize="14dip" />
</GridLayout>
```

布局将调整行和列大小以便单元格可以容纳其内容，如以下关系图所示：

 [![显示在左侧的两个单元格小于右侧的布局的关系图](grid-layout-images/gridlayout-cells.png)](grid-layout-images/gridlayout-cells.png#lightbox)

这会导致以下用户界面的应用程序中运行时：

 [![显示四个单元格的屏幕截图的 GridLayoutDemo 应用](grid-layout-images/01-gridlayout.png)](grid-layout-images/01-gridlayout.png#lightbox)



## <a name="specifying-orientation"></a>指定方向

请注意，在 XML 更高版本，每个`TextView`未指定的行或列。 如果这些未指定，`GridLayout`分配每个按顺序，根据方向的子视图。 例如，让我们更改从默认情况下，水平、 此类为垂直的 GridLayout 的方向：

```xml
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="2"
        android:columnCount="2"
        android:orientation="vertical">
</GridLayout>
```

现在，`GridLayout`将放置从上到下每个列，而不是从左到右中的单元格，如下所示：

 [![说明如何在垂直方向定位单元格的关系图](grid-layout-images/gridlayoutorientation.png)](grid-layout-images/gridlayoutorientation.png#lightbox)

这会导致在运行时的以下用户界面：

 [![与单元格定位在垂直方向排列的 GridLayoutDemo 屏幕快照](grid-layout-images/02-gridlayout.png)](grid-layout-images/02-gridlayout.png#lightbox)



### <a name="specifying-explicit-position"></a>指定显式位置

如果我们想要显式控制中的子视图的位置`GridLayout`，我们可以设置其`layout_row`和`layout_column`属性。 例如，以下 XML 将导致第一个屏幕截图所示 （如上所示），而不考虑方向的布局。

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="2"
        android:columnCount="2">
     <TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0" />
     <TextView
            android:text="Cell 1"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="1" />
     <TextView
            android:text="Cell 2"
            android:textSize="14dip"
            android:layout_row="1"
            android:layout_column="0" />
     <TextView
            android:text="Cell 3"
            android:textSize="14dip"
            android:layout_row="1"
            android:layout_column="1"  />
</GridLayout>
```



### <a name="specifying-spacing"></a>指定的间距

我们有几个选项可将提供的视图子级之间的间距`GridLayout`。 我们可以使用`layout_margin`属性来直接设置的边距上每个子视图，如下所示

```xml
<TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0"
            android:layout_margin="10dp" />
```

此外，在 Android 4 中，新的通用间距视图名为`Space`现已推出。 若要使用它，只需将其添加为子视图中。
例如，下面的 XML 添加到一个附加行`GridLayout`通过设置其`rowcount`为 3，并添加`Space`提供之间的间距的视图`TextViews`。

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="3"
        android:columnCount="2"
        android:orientation="vertical">
     <TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0" />
     <TextView
            android:text="Cell 1"
            android:textSize="14dip"
            android:layout_row="0"        
            android:layout_column="1" />
     <Space
            android:layout_row="1"
            android:layout_column="0"
            android:layout_width="50dp"         
            android:layout_height="50dp" />    
     <TextView
            android:text="Cell 2"
            android:textSize="14dip"
            android:layout_row="2"        
            android:layout_column="0" />
     <TextView
            android:text="Cell 3"
            android:textSize="14dip"
            android:layout_row="2"        
            android:layout_column="1" />
</GridLayout>
```

此 XML 创建间距以`GridLayout`，如下所示：

 [![说明如何使用间距更大的单元格 GridLayoutDemo 的屏幕截图](grid-layout-images/03-gridlayout.png)](grid-layout-images/03-gridlayout.png#lightbox)

使用新的好处`Space`视图是它允许间距，并且不需要我们对每个子视图设置属性。



### <a name="spanning-columns-and-rows"></a>跨越行和列

`GridLayout`还支持跨多个列和行的单元格。 例如，假设我们添加另一行包含一个按钮`GridLayout`，如下所示：

```xml
<?xml version="1.0" encoding="utf-8"?>
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"    
        android:rowCount="4"
        android:columnCount="2"
        android:orientation="vertical">
     <TextView
            android:text="Cell 0"
            android:textSize="14dip"
            android:layout_row="0"
            android:layout_column="0" />
     <TextView
            android:text="Cell 1"
            android:textSize="14dip"
            android:layout_row="0"        
            android:layout_column="1" />
     <Space
            android:layout_row="1"
            android:layout_column="0"
            android:layout_width="50dp"        
            android:layout_height="50dp" />   
     <TextView
            android:text="Cell 2"
            android:textSize="14dip"
            android:layout_row="2"        
            android:layout_column="0" />
     <TextView
            android:text="Cell 3"
            android:textSize="14dip"        
            android:layout_row="2"        
            android:layout_column="1" />
     <Button
            android:id="@+id/myButton"
            android:text="@string/hello"        
            android:layout_row="3"
            android:layout_column="0" />
</GridLayout>
```

这将导致的第一列`GridLayout`被拉伸以适应大小的按钮，正如我们在此处看到：

[![屏幕截图的 GridLayoutDemo 跨越仅第一列的按钮](grid-layout-images/04-gridlayout.png)](grid-layout-images/04-gridlayout.png#lightbox)

若要防止拉伸的第一列，我们可以设置按钮以通过设置此类及其 columnspan 跨越两个列：

```xml
<Button
    android:id="@+id/myButton"
    android:text="@string/hello"       
    android:layout_row="3"
    android:layout_column="0"
    android:layout_columnSpan="2" />
```

执行此操作导致的布局`TextViews`类似于我们已经有了更早版本，使用添加到底部的按钮布局`GridLayout`，如下所示：

 [![屏幕截图的 GridLayoutDemo 跨这两个列的按钮](grid-layout-images/05-gridlayout.png)](grid-layout-images/05-gridlayout.png#lightbox)


## <a name="related-links"></a>相关链接

- [GridLayoutDemo （示例）](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ICS_Samples/GridLayoutDemo/)
- [引入 Ice Cream Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 平台](https://developer.android.com/sdk/android-4.0.html)
