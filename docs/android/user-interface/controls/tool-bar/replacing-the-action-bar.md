---
title: 替换操作栏
ms.prod: xamarin
ms.assetid: 5341D28E-B203-478D-8464-6FAFDC3A4110
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/27/2018
ms.openlocfilehash: 19ac5a023b1f97b2e08bbe1821a2b9259280fc98
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68645152"
---
# <a name="replacing-the-action-bar"></a>替换操作栏

## <a name="overview"></a>概述

最常见的用途`Toolbar`之一是将默认操作栏替换为自定义`Toolbar` (创建新的 Android 项目后, 它将使用默认操作栏)。 `Toolbar`由于可以将品牌徽标、标题、菜单项、导航按钮甚至自定义视图添加到活动 UI 的 "应用栏" 部分, 因此它提供了对默认操作栏的重大升级。

若要将应用的默认操作栏`Toolbar`替换为: 

1.  创建新的自定义主题, 并修改应用的属性, 使其使用此新主题。 

2.  禁用自`windowActionBar`定义主题中的属性, 并`windowNoTitle`启用属性。

3.  定义的`Toolbar`布局。

4.  在活动的**main.axml**布局文件中包含布局。`Toolbar` 

5.  向活动的`OnCreate`方法中添加代码, 以便`Toolbar`查找`ToolBar`并调用`SetActionBar`以将安装为操作栏。

以下部分详细说明了此过程。 将创建一个简单的应用, 并将其操作栏替换为`Toolbar`自定义的。 



## <a name="start-an-app-project"></a>启动应用项目

创建名为**ToolbarFun**的新 Android 项目 (有关创建新的 android 项目的详细信息, 请参阅[Hello、Android](~/android/get-started/hello-android/hello-android-quickstart.md) )。 创建该项目后, 将目标和最低 Android API 级别设置为**android 5.0 (API 级别 21-棒糖)** 或更高版本。 有关设置 Android 版本级别的详细信息, 请参阅[了解 ANDROID API 级别](~/android/app-fundamentals/android-api-levels.md)。 当应用程序生成并运行时, 它会显示默认操作栏, 如以下屏幕截图所示:

[![默认操作栏的屏幕截图](replacing-the-action-bar-images/01-before-sml.png)](replacing-the-action-bar-images/01-before.png#lightbox)



## <a name="create-a-custom-theme"></a>创建自定义主题

打开 "**资源/值**" 目录, 并创建一个名为 "**样式 .xml**" 的新文件。 将其内容替换为以下 XML: 

```xml
<?xml version="1.0" encoding="utf-8" ?>
<resources>
  <style name="MyTheme" parent="@android:style/Theme.Material.Light.DarkActionBar">
    <item name="android:windowNoTitle">true</item>
    <item name="android:windowActionBar">false</item>
    <item name="android:colorPrimary">#5A8622</item>
  </style>
</resources>
```

此 XML 定义了一个名为**MyTheme**的新自定义主题, 该主题基于棒糖形中的**DarkActionBar**主题。 将属性设置为`true`以隐藏标题栏: `windowNoTitle` 

```xml
<item name="android:windowNoTitle">true</item>
```

若要显示自定义工具栏, 必须`ActionBar`禁用默认值: 

```xml
<item name="android:windowActionBar">false</item>
```

橄榄绿`colorPrimary`设置用于工具栏的背景色: 
 
```xml
<item name="android:colorPrimary">#5A8622</item>
```

## <a name="apply-the-custom-theme"></a>应用自定义主题

编辑**Properties/androidmanifest.xml** , 并将以下`android:theme`属性添加到`<application>` `MyTheme`元素中, 以便应用使用自定义主题: 

```xml
<application android:label="@string/app_name" android:theme="@style/MyTheme"></application>
```

有关将自定义主题应用于应用的详细信息, 请参阅[使用自定义主题](~/android/user-interface/material-theme.md#customtheme)。 



## <a name="define-a-toolbar-layout"></a>定义工具栏布局

在**资源/布局**目录中, 创建一个名为 " **toolbar .xml**" 的新文件。 将其内容替换为以下 XML: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<Toolbar xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/toolbar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:minHeight="?android:attr/actionBarSize"
    android:background="?android:attr/colorPrimary"
    android:theme="@android:style/ThemeOverlay.Material.Dark.ActionBar"/>
```

此 XML 定义替换默认`Toolbar`操作栏的自定义。 的`Toolbar`最小高度设置为它所替换的操作栏的大小: 

```csharp
android:minHeight="?android:attr/actionBarSize"
```

的背景色`Toolbar`设置为之前在**样式 .xml**中定义的橄榄色。

```csharp
android:background="?android:attr/colorPrimary"
```

从棒糖形开始`android:theme` , 属性可用于对单个视图进行样式。 通过`ThemeOverlay.Material`棒糖形引入的主题, 可以覆盖默认`Theme.Material`主题, 覆盖相关属性, 使其看起来浅或深色。 在此示例中, `Toolbar`使用了一个深色主题, 使其内容是颜色浅的: 

```csharp
android:theme="@android:style/ThemeOverlay.Material.Dark.ActionBar"
```

使用此设置, 使得菜单项与较暗的背景色相比较。



## <a name="include-the-toolbar-layout"></a>包含工具栏布局

编辑布局文件**Resources/layout/main.axml** , 并将其内容替换为以下 XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <include
        android:id="@+id/toolbar"
        layout="@layout/toolbar" />
</RelativeLayout>
```

此布局包括在`Toolbar` **toolbar**中定义的, 并使用`RelativeLayout`来指定`Toolbar`要放置在 UI 的最顶部 (在按钮上方)。 



## <a name="find-and-activate-the-toolbar"></a>查找并激活工具栏

编辑**MainActivity.cs**并添加以下 using 语句:

```csharp
using Android.Views;
```

另外, 将以下代码行添加到`OnCreate`方法的末尾:

```csharp
var toolbar = FindViewById<Toolbar>(Resource.Id.toolbar);
SetActionBar(toolbar);
ActionBar.Title = "My Toolbar";
```

此代码`Toolbar`将查找`Toolbar`并调用`SetActionBar` , 以使将采用默认操作栏特性。 工具栏的标题将更改为 **"我的工具栏**"。 如此代码示例所`ToolBar`示, 可以直接将作为操作栏来引用。 编译并运行此应用&ndash;程序将`Toolbar`显示自定义的, 以替代默认操作栏: 

[![带有绿色配色方案的自定义工具栏屏幕截图](replacing-the-action-bar-images/02-after-sml.png)](replacing-the-action-bar-images/02-after.png#lightbox)

请注意, `Toolbar`的样式与应用于`Theme.Material.Light.DarkActionBar`应用程序其余部分的主题无关。 

如果运行应用时发生异常, 请参阅下面的[故障排除](#troubleshooting)部分。

 
## <a name="add-menu-items"></a>添加菜单项 

在此部分中, 将向`Toolbar`中添加菜单。 为菜单项&ndash;保留的右上`ToolBar`方区域每个菜单项 (也称为*操作项*) 可以在当前活动中执行操作, 也可以代表整个应用执行操作。 

向添加菜单`Toolbar`: 

1.  将菜单图标 (如果需要) 添加到`mipmap-`应用项目的文件夹中。 Google 在 "[材料图标](https://design.google.com/icons/)" 页上提供一组免费菜单图标。 

2.  通过在 "**资源/菜单**" 下添加新的菜单资源文件来定义菜单项的内容。 

3.  实现此方法增加菜单项&ndash;的活动的方法。`OnCreateOptionsMenu` 

4.  实现活动&ndash;的方法此方法在点击菜单项时执行操作。 `OnOptionsItemSelected` 

以下部分详细说明了此过程, 方法是将 "**编辑**" 和 "**保存**" `Toolbar`菜单项添加到自定义的。 



### <a name="install-menu-icons"></a>安装菜单图标

`ToolbarFun`继续示例应用, 将菜单图标添加到应用项目。 下载[工具栏图标](https://github.com/xamarin/monodroid-samples/blob/master/Supportv7/AppCompat/Toolbar/Resources/toolbar-icons-plus.zip?raw=true), 解压缩, 并将提取的*mipmap*文件夹的内容复制到**ToolbarFun/资源**下的项目*mipmap*文件夹, 并将每个添加的图标文件包含在项目中。


### <a name="define-a-menu-resource"></a>定义菜单资源

在 "**资源**" 下创建新的**菜单**子目录。 在**菜单**子目录中创建一个名为**top_menus**的新菜单资源文件, 并将其内容替换为以下 xml: 

```xml
<?xml version="1.0" encoding="utf-8" ?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item
       android:id="@+id/menu_edit"
       android:icon="@mipmap/ic_action_content_create"
       android:showAsAction="ifRoom"
       android:title="Edit" />
  <item
       android:id="@+id/menu_save"
       android:icon="@mipmap/ic_action_content_save"
       android:showAsAction="ifRoom"
       android:title="Save" />
  <item
       android:id="@+id/menu_preferences"
       android:showAsAction="never"
       android:title="Preferences" />
</menu>
```

此 XML 将创建三个菜单项:

-   使用`ic_action_content_create.png`图标的**编辑**菜单项 (铅笔)。 

-   使用`ic_action_content_save.png`图标 (磁盘) 的 "**保存**" 菜单项。 

-   没有图标的**首选项**菜单项。

`Toolbar` &ndash; `ifRoom` `showAsAction` "**编辑**" 和 "**保存**" 菜单项的属性设置为此设置会导致这些菜单项显示在中 (如果有足够的空间显示这些菜单项)。 "**首选项**" 菜单`showAsAction`项`never`设置为&ndash;这会导致 "**首选项**" 菜单显示在*溢出*菜单中 (三个垂直点)。 


### <a name="implement-oncreateoptionsmenu"></a>实现 OnCreateOptionsMenu

将以下方法添加到**MainActivity.cs**:

```csharp
public override bool OnCreateOptionsMenu(IMenu menu)
{
    MenuInflater.Inflate(Resource.Menu.top_menus, menu);
    return base.OnCreateOptionsMenu(menu);
}
```

Android 会调用`OnCreateOptionsMenu`方法, 以便应用可以指定活动的菜单资源。 在此方法中, **top_menus**资源被扩充到传递`menu`的中。 此代码会导致新的 "**编辑**"、"**保存**" 和 "**首选项**" 菜单`Toolbar`项显示在中。 



### <a name="implement-onoptionsitemselected"></a>实现 OnOptionsItemSelected

将以下方法添加到**MainActivity.cs**:

```csharp
public override bool OnOptionsItemSelected(IMenuItem item)
{
    Toast.MakeText(this, "Action selected: " + item.TitleFormatted,
        ToastLength.Short).Show();
    return base.OnOptionsItemSelected(item);
}
```

当用户点击某个菜单项时, Android 会调用`OnOptionsItemSelected`方法并传入选定的菜单项。 在此示例中, 实现只显示一个 toast 来指示点击了哪个菜单项。 

生成并运行`ToolbarFun` , 以便在工具栏中查看新菜单项。 此时`Toolbar`将显示三个菜单图标, 如以下屏幕截图所示: 

[![阐释编辑、保存和溢出菜单项的位置的关系图](replacing-the-action-bar-images/04-menu-items-sml.png)](replacing-the-action-bar-images/04-menu-items.png#lightbox)

当用户点击 "**编辑**" 菜单项时, 将显示一个 toast, 指示调用`OnOptionsItemSelected`了方法: 

[![点击 "编辑项目" 时显示的 Toast 屏幕截图](replacing-the-action-bar-images/05-toast-displayed-sml.png)](replacing-the-action-bar-images/05-toast-displayed.png#lightbox)

当用户点击溢出菜单时, 将显示 "**首选项**" 菜单项。 通常情况下, 不太常见的操作应该放在溢出&ndash;菜单中。此示例使用**首选项**的溢出菜单, 因为它不像**编辑**和**保存**时经常使用: 

[![出现在溢出菜单中的 "首选项" 菜单项的屏幕截图](replacing-the-action-bar-images/06-preferences-sml.png)](replacing-the-action-bar-images/06-preferences.png#lightbox)

有关 Android 菜单的详细信息, 请参阅 Android 开发人员[菜单](https://developer.android.com/guide/topics/ui/menus.html)主题。 
 

## <a name="troubleshooting"></a>疑难解答

以下提示可帮助调试用工具栏替换操作栏时可能发生的问题。

### <a name="activity-already-has-an-action-bar"></a>活动已有操作栏

如果应用未正确配置为使用自定义主题中所述的自定义[主题, 则](#apply-the-custom-theme)在运行该应用程序时可能会出现以下异常:

![未使用自定义主题时可能出现的错误](replacing-the-action-bar-images/03-theme-not-defined.png)

此外, 可能会产生如下错误消息:_IllegalStateException:此活动已包含由窗口 décor 提供的操作栏。_ 

若要更正此错误, 请验证`android:theme`是否已将自定义主题的属性`<application>`添加到 (在**Properties/androidmanifest.xml**中), 如前面的[应用自定义主题](#apply-the-custom-theme)中所述。 此外, 如果`Toolbar`布局或自定义主题配置不正确, 则可能会导致此错误。


## <a name="related-links"></a>相关链接

- [棒糖形工具栏 (示例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-toolbar)
- [AppCompat 工具栏 (示例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar)
