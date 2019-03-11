---
title: 材料主题
description: 如何主题使用材料主题对 Xamarin.Android 应用程序
ms.prod: xamarin
ms.assetid: DC4CDBD0-3DF9-4B7E-B876-29128985E2A7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: ff94211086956e36da377445d90359789b62fc60
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57668317"
---
# <a name="material-theme"></a>材料主题

*材料主题*是一种用户界面样式，用于确定视图和开始使用 Android 5.0 (Lollipop) 的活动的外观。 材料主题是内置到 Android 5.0，因此它通常由系统用户界面以及应用程序。 材料主题不是"主题"意义上的用户可以动态设置菜单中选择一个系统范围外观选项。 相反，可以为一系列可用于自定义您的应用程序的外观的相关内置的基本样式的认为材料主题。

Android 提供了三种材料主题风格：

-  `Theme.Material` &ndash; 深色版本的材料主题使用;这是在 Android 5.0 默认风格。

-  `Theme.Material.Light` &ndash; 材料主题的轻量版本。

-  `Theme.Material.Light.DarkActionBar` &ndash; 材料主题，但使用深色操作栏的轻量版本。

此处显示这些材料主题类型的示例：

[![深色主题、 浅色主题和深色操作栏主题的示例屏幕快照](material-theme-images/three-flavors-sml.png)](material-theme-images/three-flavors.png#lightbox)

您可以从派生材料主题创建您自己的主题，重写部分或全部颜色特性。 例如，可以创建派生自一个主题`Theme.Material.Light`，但优先于应用栏颜色以匹配你的品牌颜色。 您还可以设置的样式的各个视图;例如，可以创建的样式[CardView](~/android/user-interface/controls/card-view.md)会有更多的圆，并使用较暗的背景色。

可将单个主题用于整个应用程序，或可以为不同的屏幕 （活动） 在应用中使用不同的主题。 上面的屏幕截图，例如，单个应用使用不同的主题针对每个活动来演示内置的颜色方案。 单选按钮切换到不同的活动，该应用程序和结果，显示不同的主题。

因为仅在 Android 5.0 及更高版本支持材料主题，则不能用于它 （或派生自材料主题自定义主题） 主题到您的应用程序在早期版本的 Android 上运行。 但是，可以配置应用以在 Android 5.0 设备上使用材料主题，并适当地回退到早期的主题在较旧版本的 Android 上运行时 (请参阅[兼容性](#compatibility)部分中的详细信息)。


## <a name="requirements"></a>要求

以下是所需的基于 Xamarin 的应用中使用新的 Android 5.0 材料主题功能：

-  **Xamarin.Android** &ndash; Xamarin.Android 4.20 或更高版本必须安装并配置与 Visual Studio 或 Visual Studio for mac。 

-  **Android SDK** &ndash; Android 5.0 (API 21) 或更高版本必须安装通过 Android SDK 管理器。

-  **Java JDK 1.8** &ndash;可以使用 JDK 1.7，如果您是专门针对 API 级别 23 和更早版本。 提供了 JDK 1.8 [Oracle](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)。

若要了解如何配置 Android 5.0 应用程序项目，请参阅[设置了 Android 5.0 项目](~/android/platform/lollipop.md)。


## <a name="using-the-built-in-themes"></a>使用内置的主题

使用材料主题的最简单方法是将应用配置为使用而无需自定义内置主题。 如果不想要显式配置了一个主题，您的应用程序将默认为`Theme.Material`（深色主题）。 如果你的应用程序只有一个活动，您可以在应用程序级别配置主题。 如果应用具有多个活动，可以以便它使用相同的主题可跨所有活动，或可以将不同的主题分配给不同的活动应用程序级别的配置主题。 以下部分介绍如何在应用级别和活动级别配置主题。


### <a name="theming-an-application"></a>主题设置应用程序

若要配置整个应用程序以使用材料主题风格，设置`android:theme`属性中的应用程序节点**AndroidManifest.xml**到以下项之一：

-  `@android:style/Theme.Material` &ndash; 深色主题。

-  `@android:style/Theme.Material.Light` &ndash; 浅色主题。

-  `@android:style/Theme.Material.Light.DarkActionBar` &ndash; 使用深色操作栏的浅色主题。

下面的示例配置应用程序*MyApp*使用浅色主题：

```xml
<application android:label="MyApp" 
             android:theme="@android:style/Theme.Material.Light">
</application>
```

或者，可以将应用程序设置`Theme`中的属性**AssemblyInfo.cs** (或**Properties.cs**)。 例如：

```C#
[assembly: Application(Theme="@android:style/Theme.Material.Light")]
```

如果应用程序主题设置为`@android:style/Theme.Material.Light`，在每个活动*MyApp*将使用显示`Theme.Material.Light`。


### <a name="theming-an-activity"></a>主题设置活动

将活动的主题，您将添加`Theme`设置为`[Activity]`活动声明上方特性并将分配`Theme`到你想要使用的材料主题风格。 将活动与下面的示例主题`Theme.Material.Light`:

```C#
[Activity(Theme = "@android:style/Theme.Material.Light",
          Label = "MyApp", MainLauncher = true, Icon = "@drawable/icon")]  
```

此应用中的其他活动，则使用默认`Theme.Material`深色方案 （或者，如果配置，应用程序主题设置）。

<a name="customtheme" />

## <a name="using-custom-themes"></a>使用自定义主题

可以通过创建设计样式与自己的品牌应用的自定义主题增强自己的品牌&rsquo;的颜色。 若要创建自定义主题，您定义新的样式从内置的材料主题风格，派生的重写你想要更改的颜色属性。 例如，可以定义派生的自定义主题`Theme.Material.Light.DarkActionBar`和屏幕背景色更改为米色而不是空白。

材料主题公开自定义的以下布局属性：

-  `colorPrimary` &ndash; 应用栏的颜色。

-  `colorPrimaryDark` &ndash; 状态栏和上下文应用栏的颜色这通常是深色版本`colorPrimary`。

-  `colorAccent` &ndash; UI 控件，如复选框、 单选按钮和文本的编辑框的颜色。

-  `windowBackground` &ndash; 屏幕背景的颜色。

-  `textColorPrimary` &ndash; 在应用栏中的 UI 文本的颜色。

-  `statusBarColor` &ndash; 状态栏的颜色。

-  `navigationBarColor` &ndash; 导航栏的颜色。

下图中标记这些屏幕区域：

[![属性和其相关联的屏幕区域的关系图](material-theme-images/screen-attributes-sml.png)](material-theme-images/screen-attributes.png#lightbox)

默认情况下`statusBarColor`设置的值为`colorPrimaryDark`。 可以设置`statusBarColor`为纯色，或将其设置为`@android:color/transparent`进行状态栏透明。 在导航栏还可以使可透明通过设置`navigationBarColor`到`@android:color/transparent`。


### <a name="creating-a-custom-app-theme"></a>创建自定义应用主题

可以通过创建和修改文件中的创建自定义应用主题**资源**应用项目的文件夹。 若要在设置应用与自定义主题样式，使用以下步骤：

-   创建**colors.xml**中的文件**资源/values** &mdash;此文件用于定义您的自定义主题颜色。 例如，可以将以下代码粘贴**colors.xml**来帮助你入门：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <color name="my_blue">#3498DB</color>
    <color name="my_green">#77D065</color>
    <color name="my_purple">#B455B6</color>
    <color name="my_gray">#738182</color>
</resources>
```

-   修改此示例文件，以定义的名称和颜色资源将在您的自定义主题中使用的颜色代码。

-   创建**资源/值-v21**文件夹。 在此文件夹中创建**styles.xml**文件：

    [![Styles.xml 资源/值-21.xml 文件夹中的位置](material-theme-images/values-v21-sml.png)](material-theme-images/values-v21.png#lightbox)

    请注意，**资源/值-v21**特定于 Android 5.0&ndash;较旧版本的 Android 不会读取此文件夹中的文件。

-   添加`resources`到节点**styles.xml** ，并定义`style`节点与自定义主题的名称。 例如，下面是**styles.xml**文件，用于定义*MyCustomTheme* (派生自内置`Theme.Material.Light`主题样式):

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <!-- Inherit from the light Material Theme -->
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Customizations go here -->
    </style>
</resources>
```

-   在此情况下，使用的应用程序*MyCustomTheme*将显示股票`Theme.Material.Light`而无需自定义项的主题：

    [![自定义主题外观后再自定义项](material-theme-images/custom-theme-before-sml.png)](material-theme-images/custom-theme-before.png#lightbox)

-   添加到颜色自定义**styles.xml**通过定义你想要更改的布局属性的颜色。 例如，若要更改到应用栏颜色`my_blue`并更改到 UI 控件的颜色`my_purple`，添加到颜色重写**styles.xml** ，请参阅配置中的颜色资源**colors.xml**:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<resources>
    <!-- Inherit from the light Material Theme -->
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Override the app bar color -->
        <item name="android:colorPrimary">@color/my_blue</item>
        <!-- Override the color of UI controls -->
        <item name="android:colorAccent">@color/my_purple</item>
    </style>
</resources>
```

这些更改后，使用一个应用程序，使用*MyCustomTheme*将显示在应用栏颜色`my_blue`和中的 UI 控件`my_purple`，但使用`Theme.Material.Light`其他地方的配色方案：

[![完成自定义后的自定义主题外观](material-theme-images/custom-theme-after-sml.png)](material-theme-images/custom-theme-after.png#lightbox)

在此示例中， *MyCustomTheme*借用从颜色`Theme.Material.Light`背景颜色、 状态栏和文本颜色，但它更改到应用栏的颜色`my_blue`设置单选按钮的颜色和`my_purple`.

<a name="customview" />

### <a name="creating-a-custom-view-style"></a>创建自定义视图样式

Android 5.0 还使您可以设置一个单独的视图的样式。 在创建后**colors.xml**并**styles.xml** （如在上一部分所述），可以添加到视图样式**styles.xml**。
若要设置样式的单个视图，使用以下步骤：

-   编辑**Resources/values-v21/styles.xml**并添加`style`节点与自定义视图样式的名称。 设置为在此视图的自定义的颜色特性`style`节点。 例如，若要创建自定义[CardView](~/android/user-interface/controls/card-view.md)更具有圆角和使用的样式`my_blue`卡片背景色，以添加`style`节点**styles.xml** (内`resources`节点) 和配置的背景颜色和角半径：

```xml
<!-- Theme an individual view: -->
<style name="CardView.MyBlue">

    <!-- Change the background color to Xamarin blue: -->
    <item name="cardBackgroundColor">@color/my_blue</item>

    <!-- Make the corners very round: -->
    <item name="cardCornerRadius">18dp</item>
</style>
```

-   在布局中，设置`style`该视图以匹配在上一步中选择的自定义样式名称的属性。 例如：

```xml
<android.support.v7.widget.CardView
    style="@style/CardView.MyBlue"
    android:layout_width="200dp"
    android:layout_height="100dp"
    android:layout_gravity="center_horizontal">
```

下面的屏幕截图举例说明默认值`CardView`（所示在左侧） 如相比`CardView`用自定义设计的`CardView.MyBlue`（显示在右侧） 的主题：

[![默认 CardView 和自定义 CardView 的示例](material-theme-images/custom-cardview-sml.png)](material-theme-images/custom-cardview.png#lightbox)

在此示例中，自定义`CardView`使用的背景色显示`my_blue`和 18dp 圆角半径。


## <a name="compatibility"></a>兼容性

若要设置您的应用程序，以便它在 Android 5.0 上使用材料主题，但会自动恢复到较旧的 Android 版本的向下兼容样式的样式，使用以下步骤：

-   定义中的一个自定义主题**Resources/values-v21/styles.xml**派生材料主题样式。 例如：

```xml
<resources>
    <style name="MyCustomTheme" parent="android:Theme.Material.Light">
        <!-- Your customizations go here -->
    </style>
</resources>
```

-   定义中的一个自定义主题**Resources/values/styles.xml** ，派生自一个较旧的主题，但使用与上述相同主题名称。 例如：

```xml
<resources>
    <style name="MyCustomTheme" parent="android:Theme.Holo.Light">
        <!-- Your customizations go here -->
    </style>
</resources>
```

-   在中**AndroidManifest.xml**，配置您的应用程序的自定义主题名称。 
    例如：

```xml
<application android:label="MyApp" 
             android:theme="@style/MyCustomTheme">
</application>
```

-   或者，你可以设置特定的活动中使用自定义主题样式：

```C#
[Activity(Label = "MyActivity", Theme = "@style/MyCustomTheme")]
```

如果您的主题使用在中定义的颜色**colors.xml**文件中，请务必将此文件中的放置**资源/值**(而非**资源/值-v21**)，以便这两个版本颜色定义可以访问您的自定义主题。

当你的应用在 Android 5.0 设备上运行时，它将使用中指定的主题定义**Resources/values-v21/styles.xml**。 当较旧的 Android 设备上运行此应用时，它将自动回退到中指定的主题定义**Resources/values/styles.xml**。

主题与较旧的 Android 版本的兼容性的详细信息，请参阅[备用资源](~/android/app-fundamentals/resources-in-android/alternate-resources.md)。

## <a name="summary"></a>总结

本文介绍了包括在 Android 5.0 (Lollipop) 的新材料主题用户界面样式。 它描述可用于在设置应用样式三种内置材料主题风格、 介绍了如何创建用于品牌打造您的应用程序，自定义主题和提供的示例为主题的单个视图。 最后，本文介绍了如何同时向下兼容较旧版本的 Android 应用程序中使用材料主题。



## <a name="related-links"></a>相关链接

- [ThemeSwitcher （示例）](https://developer.xamarin.com/samples/monodroid/android5.0/ThemeSwitcher)
- [棒糖形简介](../platform/lollipop.md)
- [CardView](controls/card-view.md)
- [备用资源](../app-fundamentals/resources-in-android/alternate-resources.md)
- [Android Lollipop](https://developer.android.com/about/versions/lollipop)
- [Android 饼图开发人员](https://developer.android.com/about/versions/pie/)
- [材料设计](https://developer.android.com/guide/topics/ui/look-and-feel/)
- [材料设计原则](https://material.io/design/)
- [保持兼容性](https://developer.android.com/training/backward-compatible-ui/)
