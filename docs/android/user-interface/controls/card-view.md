---
title: CardView
description: Cardview 小组件是在类似于卡的视图中显示的文本和图像内容的 UI 组件。 本指南介绍如何使用和自 CardView Xamarin.Android 应用程序中定义同时保持向后兼容早期版本的 Android。
ms.prod: xamarin
ms.assetid: CF12FE85-D03A-4E64-95D2-D7115061A500
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: cdb75207bff3f15a54d0cdd90fa0833da9c145e6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61077735"
---
# <a name="cardview"></a>CardView

_Cardview 小组件是在类似于卡的视图中显示的文本和图像内容的 UI 组件。本指南介绍如何使用和自 CardView Xamarin.Android 应用程序中定义同时保持向后兼容早期版本的 Android。_


## <a name="overview"></a>概述

`Cardview`小组件中，在 Android 5.0 (Lollipop) 中引入的是一个 UI 组件，类似于卡的视图中显示的文本和图像的内容。 `CardView` 作为实现`FrameLayout`带有圆的角和阴影的小组件。 通常情况下，`CardView`用来表示中的单个行项`ListView`或`GridView`查看组。 例如，下面的屏幕截图是实现一个旅行预订应用的示例`CardView`-基于在可滚动的旅行目标卡`ListView`:

![对每个行程目标使用 CardView 示例应用](card-view-images/01-cardview-example.png)

本指南介绍如何添加`CardView`包到你的 Xamarin.Android 项目，如何添加`CardView`到你的布局，以及如何自定义的外观`CardView`应用程序中。 此外，本指南提供的详细的列表`CardView`您可以更改，包括可帮助你使用的属性的特性`CardView`早于 Android 5.0 Lollipop 版本的 Android 上。

<a name="requirements" />

## <a name="requirements"></a>要求

以下所需使用新的 Android 5.0 及更高版本的功能 (包括`CardView`) 的基于 Xamarin 的应用中：

-  **Xamarin.Android** &ndash; Xamarin.Android 4.20 或更高版本必须安装并配置与 Visual Studio 或 Visual Studio for mac。

-  **Android SDK** &ndash; Android 5.0 (API 21) 或更高版本必须安装通过 Android SDK 管理器。

-  **Java JDK 1.8** &ndash;可以使用 JDK 1.7，如果您是专门针对 API 级别 23 和更早版本。 提供了 JDK 1.8 [Oracle](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)。

您的应用程序还必须包括`Xamarin.Android.Support.v7.CardView`包。 若要添加`Xamarin.Android.Support.v7.CardView`Visual Studio for Mac 中的包：

1. 打开你的项目，右键单击**包**，然后选择**添加包**。

2. 在中**添加包**对话框中，搜索**CardView**。

3. 选择**Xamarin 支持库 v7 CardView**，然后单击**添加包**。

若要添加`Xamarin.Android.Support.v7.CardView`Visual Studio 中的包：

1. 打开你的项目，右键单击**引用**节点 (在**解决方案资源管理器**窗格中)，然后选择**管理 NuGet 包...**.

2. 当**管理 NuGet 包**显示对话框中，输入**CardView**在搜索框中。

3. 当**Xamarin 支持库 v7 CardView**出现，请单击**安装**。

若要了解如何配置 Android 5.0 应用程序项目，请参阅[设置了 Android 5.0 项目](~/android/platform/lollipop.md)。
有关安装 NuGet 包的详细信息，请参阅[演练：在项目中包括 NuGet](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)。


## <a name="introducing-cardview"></a>引入 CardView

默认值`CardView`类似于带有最少圆的角和一些阴影白色卡。 下面的示例**Main.axml**布局显示单个`CardView`包含的小组件`TextView`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:gravity="center_horizontal"
    android:padding="5dp">
    <android.support.v7.widget.CardView
        android:layout_width="fill_parent"
        android:layout_height="245dp"
        android:layout_gravity="center_horizontal">
        <TextView
            android:text="Basic CardView"
            android:layout_marginTop="0dp"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:gravity="center"
            android:layout_centerVertical="true"
            android:layout_alignParentRight="true"
            android:layout_alignParentEnd="true" />
    </android.support.v7.widget.CardView>
</LinearLayout>
```

如果使用此 XML 替换现有内容**Main.axml**，请务必注释掉中的任何代码**MainActivity.cs** ，是指在之前的 XML 的资源。

此布局的示例创建一个默认`CardView`通过一行文本，如以下屏幕截图中所示：

[![屏幕截图的 CardView 具有白色背景和文本行](card-view-images/02-basic-cardview-sml.png)](card-view-images/02-basic-cardview.png#lightbox)

在此示例中，应用样式设置为浅材料主题 (`Theme.Material.Light`)，以便`CardView`阴影和边缘都更轻松地查看。 有关主题 Android 5.0 应用程序的详细信息，请参阅[材料主题](~/android/user-interface/material-theme.md)。 在下一步部分中，我们将了解如何自定义`CardView`应用程序。


## <a name="customizing-cardview"></a>自定义 CardView

您可以修改基本`CardView`特性以自定义的外观`CardView`应用程序中。 例如，提升`CardView`可以增加要强制转换更大的卷影 （这会使卡看起来为浮点型上面在后台更高版本）。 此外，可以增加圆角半径进行更多的卡片的舍入的角。

在下一步布局示例中，自定义`CardView`用于创建打印照片 （"快照"） 的模拟。 `ImageView`添加到`CardView`用于显示图像和一个`TextView`定位下面`ImageView`用于显示的图像标题。
在此示例布局中，`CardView`具有以下自定义设置：

-  `cardElevation`增加到 4dp 转换更大的卷影。
-  `cardCornerRadius`增加到 5dp 以使显示更加圆角的边角。

因为`CardView`提供通过 Android v7 支持库，其属性未提供的`android:`命名空间。 因此，您必须定义 XML 命名空间，并使用为该命名空间`CardView`属性前缀。 在下面的布局示例中，我们将使用这行来定义命名空间`cardview`:

```xml
    xmlns:cardview="http://schemas.android.com/apk/res-auto"
```

您可以调用此命名空间`card_view`甚至`myapp`如果你选择 （它是只能在此文件的作用域中访问）。 无论您选择要调用此命名空间，则必须使用其前缀`CardView`你想要修改的属性。 在此布局的示例中，`cardview`命名空间是前缀`cardElevation`和`cardCornerRadius`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:cardview="http://schemas.android.com/apk/res-auto"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:gravity="center_horizontal"
    android:padding="5dp">
    <android.support.v7.widget.CardView
        android:layout_width="fill_parent"
        android:layout_height="245dp"
        android:layout_gravity="center_horizontal"
        cardview:cardElevation="4dp"
        cardview:cardCornerRadius="5dp">
        <LinearLayout
            android:layout_width="fill_parent"
            android:layout_height="240dp"
            android:orientation="vertical"
            android:padding="8dp">
            <ImageView
                android:layout_width="fill_parent"
                android:layout_height="190dp"
                android:id="@+id/imageView"
                android:scaleType="centerCrop" />
            <TextView
                android:layout_width="fill_parent"
                android:layout_height="wrap_content"
                android:textAppearance="?android:attr/textAppearanceMedium"
                android:textColor="#333333"
                android:text="Photo Title"
                android:id="@+id/textView"
                android:layout_gravity="center_horizontal"
                android:layout_marginLeft="5dp" />
        </LinearLayout>
    </android.support.v7.widget.CardView>
</LinearLayout>
```

此布局示例用于在照片查看应用中，显示的图像时`CardView`具有照片快照的外观，以下屏幕截图中所示：

[![包含图像和标题图像下的 CardView](card-view-images/03-photo-cardview-sml.png)](card-view-images/03-photo-cardview.png#lightbox)

此屏幕截图取自[RecyclerViewer](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)示例应用，使用`RecyclerView`小组件显示的滚动列表`CardView`查看照片的映像。 有关详细信息`RecyclerView`，请参阅[RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)指南。

请注意，`CardView`可以在其内容区域中显示多个子视图。 例如，在上面的照片查看应用示例，在内容区域组成`ListView`，其中包含`ImageView`和一个`TextView`。 尽管`CardView`实例通常垂直排列，也可以水平排列它们 (请参阅[创建自定义视图样式](~/android/user-interface/material-theme.md#customview)示例屏幕快照)。


### <a name="cardview-layout-options"></a>CardView 布局选项

`CardView` 通过设置影响其填充、 提升、 圆角半径和背景色的一个或多个属性，可以自定义布局：

[![CardView 属性的关系图](card-view-images/04-attributes-sml.png)](card-view-images/04-attributes.png#lightbox)

每个属性还可以更改动态通过调用对应`CardView`方法 (有关详细信息`CardView`方法，请参阅[CardView 类引用](https://developer.android.com/reference/android/support/v7/widget/CardView.html))。
请注意，这些属性 （除外的背景色） 接受一个维度值，该值是跟单位的十进制数字。 例如，`11.5dp`指定 11.5 密度无关的像素。


#### <a name="padding"></a>填充
`
CardView` 提供了五个填充属性来定位数据卡中的内容。 可以将它们设置在布局 XML 中也可以在代码中调用类似方法：

[![CardView 填充属性的关系图](card-view-images/05-padding-sml.png)](card-view-images/05-padding.png#lightbox)

填充特性进行了说明，如下所示：

-  `contentPadding` &ndash; 内部填充的子视图之间`CardView`和所有边缘的卡。

-  `contentPaddingBottom` &ndash; 内部填充的子视图之间`CardView`和卡的下边缘。

-  `contentPaddingLeft` &ndash; 内部填充的子视图之间`CardView`和卡片的左边的缘。

-  `contentPaddingRight` &ndash; 内部填充的子视图之间`CardView`卡片的右边缘。

-  `contentPaddingTop` &ndash; 内部填充的子视图之间`CardView`卡的上边缘。

内容填充属性是相对于边界的内容区域，而不是位于内容区域内的任何给定小组件。
例如，如果`contentPadding`在照片查看应用程序，充分地增加了`CardView`会裁剪图像和卡片上显示的文本。



#### <a name="elevation"></a>提升

`CardView` 提供了两个提升属性来控制其进行提升，因此，其阴影的大小：

[![CardView 提升属性的关系图](card-view-images/06-elevation-sml.png)](card-view-images/06-elevation.png#lightbox)

提升属性进行了说明，如下所示：

-  `cardElevation` &ndash; 提升`CardView`（表示其 Z 轴）。

-  `cardMaxElevation` &ndash; 最大值`CardView`的提升。

较大的值`cardElevation`增加卷影大小以使`CardView`看起来为浮点型上面在后台更高版本。 `cardElevation`属性还确定重叠的视图的绘制顺序; 这就是，则`CardView`将绘制具有较高的提升权限设置及更高版本具有较低的提升设置任何重叠视图的另一个重叠视图下。
`cardMaxElevation`设置可用于您的应用程序发生更改时提升动态&ndash;它会阻止阴影扩展过去使用此设置定义的限制。


#### <a name="corner-radius-and-background-color"></a>圆角半径和背景色

`CardView` 提供了可用于控制其圆角半径和其背景色的属性。 这两个属性允许您更改的整体样式`CardView`:

[![CardView 角周围和背景颜色属性的关系图](card-view-images/07-radius-bgcolor-sml.png)](card-view-images/07-radius-bgcolor.png#lightbox)

这些属性解释如下：

-  `cardCornerRadius` &ndash; 所有的角的圆角半径`CardView`。

-  `cardBackgroundColor` &ndash; 背景色`CardView`。

在此图中，`cardCornerRadius`设置为更舍入 10dp 并`cardBackgroundColor`设置为`"#FFFFCC"`（浅黄色）。


## <a name="compatibility"></a>兼容性

可以使用`CardView`早于 Android 5.0 Lollipop 版本的 Android 上。 因为`CardView`属于的 Android v7 支持库，可以使用`CardView`Android 2.1 （API 级别 7） 及更高版本。
但是，必须安装`Xamarin.Android.Support.v7.CardView`包中所述[要求](#requirements)、 更高版本。

`CardView` 表现出之前 Lollipop （API 级别 21） 的设备上略有不同的行为：

-  `CardView` 使用添加附加的填充的卷影以编程方式实现。

-  `CardView` 不会剪切与相交的子视图`CardView`的圆角。

若要帮助管理这些兼容性差异，`CardView`提供了几个可以配置在布局中的其他属性：

-   `cardPreventCornerOverlap` &ndash; 将此属性设置为`true`以您的应用程序运行之前的 Android 版本 （API 级别 20 及更早版本） 时，将添加填充。 此设置可防止`CardView`内容从与相交`CardView`的圆角。

-   `cardUseCompatPadding` &ndash; 将此属性设置为`true`时您的应用程序运行在 Android 或大于 API 级别 21 的版本中添加填充。 如果你想要使用`CardView`预棒糖形设备上，并让它看起来一样棒糖形上 （或更高版本），将此属性设置为`true`。 启用此特性后，`CardView`添加附加的填充要绘制阴影时在预棒糖形设备上运行。 这有助于克服填充预棒糖形以编程方式隐藏实现生效时引入的差异。

有关保持与早期版本的 Android 的兼容性的详细信息，请参阅[维护兼容性](https://developer.android.com/training/material/compatibility.html)。


## <a name="summary"></a>总结

本指南介绍了新`CardView`小组件包含在 Android 5.0 (Lollipop)。 它演示了默认值`CardView`外观，并说明了如何自定义`CardView`通过更改其提升，角圆度，内容填充和背景色。 它列出`CardView`布局特性 （与引用关系图），并介绍了如何使用`CardView`早于 Android 5.0 Lollipop 的 Android 设备上。 有关详细信息`CardView`，请参阅[CardView 类引用](https://developer.android.com/reference/android/support/v7/widget/CardView.html)。


## <a name="related-links"></a>相关链接

- [RecyclerView （示例）](https://developer.xamarin.com/samples/monodroid/android5.0/RecyclerViewer)
- [棒糖形简介](~/android/platform/lollipop.md)
- [CardView 类引用](https://developer.android.com/reference/android/support/v7/widget/CardView.html)
