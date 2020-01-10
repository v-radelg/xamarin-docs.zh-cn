---
title: 添加 AppCompat 和材料设计
description: 本文介绍如何转换现有的 Xamarin. Android 应用程序以使用 AppCompat 和材料设计。
ms.prod: xamarin
ms.assetid: 045FBCDF-4D45-48BB-9911-BD3938C87D58
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/27/2017
ms.openlocfilehash: 36c5733c347e3493b5ed423c52766c7e33fbdb3d
ms.sourcegitcommit: 4691b48f14b166afcec69d1350b769ff5bf8c9f6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2020
ms.locfileid: "75728325"
---
# <a name="adding-appcompat-and-material-design"></a>添加 AppCompat 和材料设计

_请按照以下步骤转换现有 Xamarin。 Android 应用程序以使用 AppCompat 和材料设计_

<!-- source https://gist.github.com/jassmith/a3b2a543f99126782936
https://blog.xamarin.com/material-design-for-your-xamarin-forms-android-apps/ -->

## <a name="overview"></a>概述

这些说明解释了如何更新现有的 Xamarin. Forms Android 应用程序以使用 AppCompat 库，并在 Android 版本的 Xamarin. Forms 应用中启用材料设计。

### <a name="1-update-xamarinforms"></a>1. 更新 Xamarin. Forms

确保解决方案使用的是 Xamarin. Forms 2.0 或更高版本。 如果需要，请将 Xamarin NuGet 包更新为2.0。

### <a name="2-check-android-version"></a>2. 检查 Android 版本

确保 Android 项目的目标框架为 Android 6.0 （Marshmallow）。 检查**Android 项目 > 选项 "> 生成 > 常规**设置"，确保选择 corrent 框架：

 ![](appcompat-images/target-android-6-sml.png "Android General Build Configuration")

### <a name="3-add-new-themes-to-support-material-design"></a>3. 添加新主题以支持材料设计

在 Android 项目中创建以下三个文件，并粘贴以下内容。 Google 提供了[样式指南](https://www.google.com/design/spec/style/color.html#color-color-palette)和[调色板生成器](https://www.materialpalette.com/)，可帮助你为指定的配色方案选择一个替代配色方案。

**Resources/values/colors.xml**

```xml
<resources>
  <color name="primary">#2196F3</color>
  <color name="primaryDark">#1976D2</color>
  <color name="accent">#FFC107</color>
  <color name="window_background">#F5F5F5</color>
</resources>
```

**Resources/values/style.xml**

```xml
<resources>
  <style name="MyTheme" parent="MyTheme.Base">
  </style>
  <style name="MyTheme.Base" parent="Theme.AppCompat.Light.NoActionBar">
    <item name="colorPrimary">@color/primary</item>
    <item name="colorPrimaryDark">@color/primaryDark</item>
    <item name="colorAccent">@color/accent</item>
    <item name="android:windowBackground">@color/window_background</item>
    <item name="windowActionModeOverlay">true</item>
  </style>
</resources>
```

在 Android 棒糖和更高版本上运行时，必须在**v21**文件夹中包含附加样式，以应用特定属性。

**Resources/values-v21/style.xml**

```xml
<resources>
  <style name="MyTheme" parent="MyTheme.Base">
    <!--If you are using MasterDetailPage you will want to set these, else you can leave them out-->
    <!--<item name="android:windowDrawsSystemBarBackgrounds">true</item>
    <item name="android:statusBarColor">@android:color/transparent</item>-->
  </style>
</resources>
```

### <a name="4-update-androidmanifestxml"></a>4. 更新 Androidmanifest.xml

若要确保使用这一新的主题信息，请在**androidmanifest.xml**文件中添加 "主题"，方法是添加 `android:theme="@style/MyTheme"` （保留 XML 的其余部分）。

**Properties/AndroidManifest.xml**

```xml
...
<application android:label="AppName" android:icon="@drawable/icon"
  android:theme="@style/MyTheme">
...
```

### <a name="5-provide-toolbar-and-tab-layouts"></a>5. 提供工具栏和选项卡布局

在**资源/布局**目录中创建**选项卡栏 main.axml**和**main.axml**文件，并粘贴以下内容：

**Resources/layout/Tabbar.axml**

```xml
<android.support.design.widget.TabLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/sliding_tabs"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:background="?attr/colorPrimary"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
    app:tabIndicatorColor="@android:color/white"
    app:tabGravity="fill"
    app:tabMode="fixed" />
```

已设置选项卡的几个属性，包括选项卡的重心到 `fill` 和模式 `fixed`。
如果有很多选项卡，你可能想要将此选项卡切换到可滚动-通过 Android [TabLayout 文档](https://developer.android.com/reference/android/support/design/widget/TabLayout.html)阅读，以了解详细信息。

**Resources/layout/Toolbar.axml**

```xml
<android.support.v7.widget.Toolbar
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/toolbar"
    android:layout_width="match_parent"
    android:layout_height="?attr/actionBarSize"
    android:minHeight="?attr/actionBarSize"
    android:background="?attr/colorPrimary"
    android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
    app:popupTheme="@style/ThemeOverlay.AppCompat.Light"
    app:layout_scrollFlags="scroll|enterAlways" />
```

在这些文件中，我们将为你的应用程序的工具栏创建特定主题。
请参阅[Hello 工具栏](https://blog.xamarin.com/android-tips-hello-toolbar-goodbye-action-bar/)博客文章了解详细信息。

### <a name="6-update-the-mainactivity"></a>6. 更新 `MainActivity`

在现有 Xamarin. Forms 应用中， **MainActivity.cs**类将从 `FormsApplicationActivity`继承。 必须将其替换为 `FormsAppCompatActivity`，才能启用新的功能。

**MainActivity.cs**

```csharp
public class MainActivity : FormsAppCompatActivity  // was FormsApplicationActivity
```

最后，从 `OnCreate` 方法的步骤5中的新布局 "向上连接"，如下所示：

```csharp
protected override void OnCreate(Bundle bundle)
{
  // set the layout resources first
  FormsAppCompatActivity.ToolbarResource = Resource.Layout.Toolbar;
  FormsAppCompatActivity.TabLayoutResource = Resource.Layout.Tabbar;

  // then call base.OnCreate and the Xamarin.Forms methods
  base.OnCreate(bundle);
  Forms.Init(this, bundle);
  LoadApplication(new App());
}
```
