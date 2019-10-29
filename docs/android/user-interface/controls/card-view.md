---
title: CardView
description: Cardview 小组件是一个 UI 组件，用于在类似卡片的视图中提供文本和图像内容。 本指南说明如何在 Xamarin Android 应用程序中使用和自定义 CardView，同时保持与早期版本的 Android 的向后兼容性。
ms.prod: xamarin
ms.assetid: CF12FE85-D03A-4E64-95D2-D7115061A500
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: 053847426d770408826297d9a80b6e38d7f6bc44
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029283"
---
# <a name="xamarinandroid-cardview"></a>Xamarin CardView

_Cardview 小组件是一个 UI 组件，用于在类似卡片的视图中提供文本和图像内容。本指南说明如何在 Xamarin Android 应用程序中使用和自定义 CardView，同时保持与早期版本的 Android 的向后兼容性。_

## <a name="overview"></a>概述

Android 5.0 （棒糖形）中引入的 `Cardview` 小组件是一个 UI 组件，用于在类似卡片的视图中提供文本和图像内容。 `CardView` 作为带有圆角和阴影的 `FrameLayout` 小组件实现。 通常，`CardView` 用于在 `ListView` 或 `GridView` 视图组中显示单个行项。 例如，以下屏幕截图是在可滚动的 `ListView`中实现基于 `CardView`的旅游目标卡的旅行预订应用程序的一个示例：

![为每个旅行目标使用 CardView 的示例应用](card-view-images/01-cardview-example.png)

本指南说明如何将 `CardView` 包添加到你的 Xamarin Android 项目，如何将 `CardView` 添加到你的布局中，以及如何在你的应用程序中自定义 `CardView` 的外观。 此外，本指南还提供了可以更改的 `CardView` 属性的详细列表，其中包括一些属性，可帮助您在早于 Android 5.0 棒糖的 Android 版本上使用 `CardView`。

<a name="requirements" />

## <a name="requirements"></a>要求

在基于 Xamarin 的应用中使用新的 Android 5.0 及更高版本功能（包括 `CardView`）时，需要满足以下要求：

- **Xamarin** &ndash; xamarin 4.20 或更高版本必须安装并配置 Visual Studio 或 Visual Studio for Mac。

- 必须通过 Android SDK 管理器安装**Android SDK** &ndash; Android 5.0 （API 21）或更高版本。

- 如果专门针对 API 级别23和更早版本，可以使用**JAVA jdk 1.8** &ndash; JDK 1.7。 JDK 1.8 可从[Oracle](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)获得。

应用还必须包含 `Xamarin.Android.Support.v7.CardView` 包。 若要在 Visual Studio for Mac 中添加 `Xamarin.Android.Support.v7.CardView` 包：

1. 打开项目，右键单击 "**包**"，然后选择 "**添加包**"。

2. 在 "**添加包**" 对话框中，搜索 " **CardView**"。

3. 选择 " **Xamarin 支持库 V7 CardView**"，并单击 "**添加包**"。

若要在 Visual Studio 中添加 `Xamarin.Android.Support.v7.CardView` 包：

1. 打开项目，右键单击 "**引用**" 节点（在 "**解决方案资源管理器**" 窗格中），然后选择 "**管理 NuGet 包 ...** "。

2. 显示 "**管理 NuGet 包**" 对话框时，请在 "搜索" 框中输入**CardView** 。

3. 出现 " **Xamarin 支持库 V7 CardView** " 时，单击 "**安装**"。

若要了解如何配置 Android 5.0 应用项目，请参阅[设置 android 5.0 项目](~/android/platform/lollipop.md)。
有关安装 NuGet 包的详细信息，请参阅[演练：在项目中包括 NuGet](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)。

## <a name="introducing-cardview"></a>CardView 简介

默认 `CardView` 类似于具有最小圆角和轻微阴影的白色卡片。 下面的示例**main.axml**布局显示了包含 `TextView`的单个 `CardView` 小组件：

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

如果你使用此 XML 来替换**main.axml**的现有内容，请确保注释掉引用前面 XML 中的资源的**MainActivity.cs**中的任何代码。

此布局示例创建一个默认的 `CardView`，其中包含一行文本，如以下屏幕截图所示：

[用白色背景和文本行![CardView 屏幕截图](card-view-images/02-basic-cardview-sml.png)](card-view-images/02-basic-cardview.png#lightbox)

在此示例中，应用样式设置为光源材料主题（`Theme.Material.Light`），以便更易于查看 `CardView` 阴影和边缘。 有关主题 Android 5.0 应用的详细信息，请参阅[材料主题](~/android/user-interface/material-theme.md)。 在下一部分中，我们将了解如何为应用程序自定义 `CardView`。

## <a name="customizing-cardview"></a>自定义 CardView

您可以修改基本 `CardView` 属性，以自定义应用程序中 `CardView` 的外观。 例如，可以增加 `CardView` 的提升量来强制转换较大的阴影（这使得卡片看起来更大的背景）。 另外，还可以增加角半径，使纸牌的拐角更大一些。

在下一个布局示例中，自定义的 `CardView` 用于创建打印照片（"快照"）的模拟。 `ImageView` 将添加到用于显示图像的 `CardView` 中，`TextView` 定位在用于显示图像标题的 `ImageView` 下。
在此示例布局中，`CardView` 具有下列自定义项：

- `cardElevation` 增加到4dp，以转换更大的阴影。
- `cardCornerRadius` 增加到5dp，以使拐角显得更大。

由于 Android v7 支持库提供 `CardView`，因此 `android:` 命名空间中不提供其属性。 因此，你必须定义自己的 XML 命名空间，并使用该命名空间作为 `CardView` 特性前缀。 在下面的布局示例中，将使用此行定义名为 `cardview`的命名空间：

```xml
    xmlns:cardview="http://schemas.android.com/apk/res-auto"
```

您可以调用此命名空间 `card_view` 甚至 `myapp` （如果选择）（只能在此文件的作用域内访问）。 无论你选择调用此命名空间，你都必须使用它为你要修改的 `CardView` 属性加上前缀。 在此布局示例中，`cardview` 命名空间是 `cardElevation` 和 `cardCornerRadius`的前缀：

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

当此布局示例用于显示照片查看应用中的图像时，`CardView` 具有照片快照的外观，如以下屏幕截图所示：

[在图像下方使用图像和标题![CardView](card-view-images/03-photo-cardview-sml.png)](card-view-images/03-photo-cardview.png#lightbox)

此屏幕截图取自[RecyclerViewer](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer)示例应用程序，该应用程序使用 `RecyclerView` 小组件提供用于查看照片的 `CardView` 图像的滚动列表。 有关 `RecyclerView`的详细信息，请参阅[RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)指南。

请注意，`CardView` 可以在其内容区域中显示一个以上的子视图。 例如，在上述照片查看应用程序示例中，内容区域由包含 `ImageView` 和 `TextView`的 `ListView` 组成。 尽管 `CardView` 实例通常是垂直排列的，但您也可以水平排列它们（有关示例屏幕截图，请参阅[创建自定义视图样式](~/android/user-interface/material-theme.md#customview)）。

### <a name="cardview-layout-options"></a>CardView 布局选项

可以通过设置一个或多个属性来自定义 `CardView` 布局，这些特性会影响其填充、仰角、角半径和背景色：

[CardView 特性![关系图](card-view-images/04-attributes-sml.png)](card-view-images/04-attributes.png#lightbox)

还可以通过调用与 `CardView` 方法对应的方法，动态更改每个特性（有关 `CardView` 方法的详细信息，请参阅[CardView 类引用](https://developer.android.com/reference/android/support/v7/widget/CardView.html)）。
请注意，这些属性（背景颜色除外）接受一个维度值，该值是一个十进制数，后跟单位。 例如，`11.5dp` 指定11.5 与密度无关的像素。

#### <a name="padding"></a>填充

`CardView` 提供五个填充属性来定位卡片内的内容。 可以在布局 XML 中进行设置，也可以在代码中调用类似的方法：

[CardView 填充特性![关系图](card-view-images/05-padding-sml.png)](card-view-images/05-padding.png#lightbox)

填充属性如下所述：

- `contentPadding` &ndash; `CardView` 的子视图和卡片的所有边缘之间的内部填充。

- `contentPaddingBottom` &ndash; `CardView` 的子视图与卡的下边缘之间的内部填充。

- `contentPaddingLeft` &ndash; `CardView` 的子视图与卡片左边缘之间的内部填充。

- `contentPaddingRight` &ndash; `CardView` 的子视图与卡片的右边缘之间的内部填充。

- `contentPaddingTop` &ndash; `CardView` 的子视图与卡片上边缘之间的内部填充。

内容填充属性相对于内容区域的边界，而不是相对于内容区域内的任何给定小组件。
例如，如果在照片查看应用中充分增加了 `contentPadding`，则 `CardView` 会裁剪图像和卡片上显示的文本。

#### <a name="elevation"></a>仰角

`CardView` 提供了两个提升特性来控制其提升，因此，其阴影的大小为：

[CardView 提升特性![关系图](card-view-images/06-elevation-sml.png)](card-view-images/06-elevation.png#lightbox)

提升属性如下所述：

- `cardElevation` &ndash; `CardView` 的仰角（表示其 Z 轴）。

- `cardMaxElevation` &ndash; `CardView`提升的最大值。

`cardElevation` 的值越大，就会增加阴影大小，使其看起来 `CardView` 在背景上方更高。 `cardElevation` 属性还确定了重叠视图的绘制顺序;也就是说，`CardView` 将在另一个具有较高提升设置的重叠视图下绘制，并在具有较低提升设置的任何重叠视图上绘制。
当应用动态更改提升时，`cardMaxElevation` 设置非常有用 &ndash; 它会阻止阴影超出你用此设置定义的限制。

#### <a name="corner-radius-and-background-color"></a>角半径和背景色

`CardView` 提供了可用于控制其角半径及其背景色的特性。 这两个属性允许您更改 `CardView`的总体样式：

[![CardView 角 radious 和背景色属性的图示](card-view-images/07-radius-bgcolor-sml.png)](card-view-images/07-radius-bgcolor.png#lightbox)

这些属性的解释如下：

- `cardCornerRadius` &ndash; `CardView`所有拐角的角半径。

- `cardBackgroundColor` &ndash; `CardView`的背景色。

在此图中，将 `cardCornerRadius` 设置为更大的10dp，并将 `cardBackgroundColor` 设置为 `"#FFFFCC"` （浅黄色）。

## <a name="compatibility"></a>兼容性

可以在 android 5.0 以前版本的 Android 上使用 `CardView`。 由于 `CardView` 是 Android v7 支持库的一部分，因此可以使用适用于 Android 2.1 （API 级别7）和更高版本的 `CardView`。
但是，必须按照上面的[要求](#requirements)中所述安装 `Xamarin.Android.Support.v7.CardView` 包。

在棒糖形之前，`CardView` 在设备上出现略微不同的行为（API 级别21）：

- `CardView` 使用添加附加填充的编程阴影实现。

- `CardView` 不会剪裁与 `CardView`圆角相交的子视图。

为了帮助管理这些兼容性差异，`CardView` 提供了可在布局中配置的几个附加属性：

- `cardPreventCornerOverlap` &ndash; 将此特性设置为 `true`，以便在应用程序在早期 Android 版本（API 级别20和更早版本）上运行时添加填充。 此设置可防止 `CardView` 内容与 `CardView`的圆角相交。

- `cardUseCompatPadding` &ndash; 将此特性设置为 `true`，以便在应用程序运行时，在 Android 版本或大于 API 级别21时添加填充。 如果要在棒糖形设备上使用 `CardView`，并使其在棒糖形（或更高版本）上看起来相同，请将此属性设置为 `true`。 如果启用此属性，则 `CardView` 在运行于棒糖的设备上时，添加用于绘制阴影的附加填充。 这有助于克服当棒式编程影子实现生效时所引入的填充的差别。

有关保持与早期版本的 Android 兼容性的详细信息，请参阅[维护兼容性](https://developer.android.com/training/material/compatibility.html)。

## <a name="summary"></a>总结

本指南介绍 Android 5.0 （棒糖形）中包含的新 `CardView` 小组件。 它演示了默认 `CardView` 外观，并说明了如何通过更改其提升、角圆度、内容填充和背景色自定义 `CardView`。 它列出了 `CardView` 布局特性（包含引用关系图），并说明了如何在 android 5.0 棒糖之前的 Android 设备上使用 `CardView`。 有关 `CardView`的详细信息，请参阅[CardView 类参考](https://developer.android.com/reference/android/support/v7/widget/CardView.html)。

## <a name="related-links"></a>相关链接

- [RecyclerView （示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-recyclerviewer)
- [棒糖形简介](~/android/platform/lollipop.md)
- [CardView 类引用](https://developer.android.com/reference/android/support/v7/widget/CardView.html)
