---
title: 替换操作栏
ms.prod: xamarin
ms.assetid: 5341D28E-B203-478D-8464-6FAFDC3A4110
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/27/2018
ms.openlocfilehash: 9e9fa1e2651661670f89baac7fcd438b3d14bfb3
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61200744"
---
# <a name="replacing-the-action-bar"></a>替换操作栏

## <a name="overview"></a>概述

对于最常见的一个用途`Toolbar`是默认操作栏替换为自定义`Toolbar`（创建新的 Android 项目后，它使用默认操作栏）。 因为`Toolbar`提供的功能将品牌的徽标、 标题、 菜单项、 导航按钮和甚至是自定义视图添加到应用程序栏部分中的活动的 UI，它提供重大升级通过默认操作栏。

若要替换为与应用程序的默认操作栏`Toolbar`: 

1.  创建一个新的自定义主题，并修改应用程序的属性，使其使用此新主题。 

2.  禁用`windowActionBar`属性中的自定义主题，并启用`windowNoTitle`属性。

3.  定义布局`Toolbar`。

4.  包括`Toolbar`中的活动的布局**Main.axml**布局文件。 

5.  将代码添加到活动的`OnCreate`方法来查找`Toolbar`，并调用`SetActionBar`安装`ToolBar`作为操作栏。

以下部分介绍此过程的详细信息。 创建一个简单的应用程序和其操作栏替换为自定义`Toolbar`。 



## <a name="start-an-app-project"></a>启动应用程序项目

创建一个名为的新的 Android 项目**ToolbarFun** (请参阅[Hello，Android](~/android/get-started/hello-android/hello-android-quickstart.md)有关创建新的 Android 项目的详细信息)。 创建此项目后，将目标和最低 Android API 级别设置为**Android 5.0 (API 级别 21-棒糖形)** 或更高版本。 有关设置 Android 版本级别的详细信息，请参阅[了解 Android API 级别](~/android/app-fundamentals/android-api-levels.md)。 当生成和运行应用程序时，它会显示默认操作栏，此屏幕截图中所示：

[![默认操作栏的屏幕截图](replacing-the-action-bar-images/01-before-sml.png)](replacing-the-action-bar-images/01-before.png#lightbox)



## <a name="create-a-custom-theme"></a>创建自定义主题

打开**资源/values**目录并创建一个新的文件称为**styles.xml**。 其内容替换为以下 XML: 

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

此 XML 定义新的自定义主题名为**MyTheme**基于**Theme.Material.Light.DarkActionBar**棒糖形中的主题。 `windowNoTitle`属性设置为`true`隐藏的标题栏： 

```xml
<item name="android:windowNoTitle">true</item>
```

若要显示的自定义工具栏，默认值`ActionBar`必须禁用： 

```xml
<item name="android:windowActionBar">false</item>
```

Olive-green`colorPrimary`设置用于在工具栏的背景色： 
 
```xml
<item name="android:colorPrimary">#5A8622</item>
```

## <a name="apply-the-custom-theme"></a>应用自定义主题

编辑**properties/Androidmanifest.xml**并添加以下`android:theme`归于`<application>`元素，以便该应用使用`MyTheme`自定义主题： 

```xml
<application android:label="@string/app_name" android:theme="@style/MyTheme"></application>
```

有关将自定义主题应用到应用程序的详细信息，请参阅[使用自定义主题](~/android/user-interface/material-theme.md#customtheme)。 



## <a name="define-a-toolbar-layout"></a>定义工具栏布局

在中**资源/布局**目录中，创建名为的新文件**toolbar.xml**。 其内容替换为以下 XML: 

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

此 XML 定义自定义`Toolbar`替换默认操作栏。 最小高度`Toolbar`设置它将替换操作栏的大小为： 

```csharp
android:minHeight="?android:attr/actionBarSize"
```

背景色`Toolbar`设置为前面定义的 olive-green 颜色**styles.xml**:

```csharp
android:background="?android:attr/colorPrimary"
```

棒糖形，从开始`android:theme`属性可用于设置样式的单个视图。 `ThemeOverlay.Material`棒糖形中引入的主题，让可以覆盖默认`Theme.Material`覆盖相关的属性，以使它们浅色或深色主题。 在此示例中，`Toolbar`使用深色主题，以便其内容是亮色： 

```csharp
android:theme="@android:style/ThemeOverlay.Material.Dark.ActionBar"
```

使用此设置，以便菜单项的较暗的背景色与之相反。



## <a name="include-the-toolbar-layout"></a>包括工具栏布局

编辑布局文件**Resources/layout/Main.axml**并将其内容替换为以下 XML:

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

此布局包括`Toolbar`中定义**toolbar.xml** ，并使用`RelativeLayout`以指定`Toolbar`要放置在 （上面显示的按钮） 的用户界面的最顶部。 



## <a name="find-and-activate-the-toolbar"></a>查找和激活工具栏

编辑**MainActivity.cs**并添加以下 using 语句：

```csharp
using Android.Views;
```

此外，将以下代码行添加到末尾`OnCreate`方法：

```csharp
var toolbar = FindViewById<Toolbar>(Resource.Id.toolbar);
SetActionBar(toolbar);
ActionBar.Title = "My Toolbar";
```

此代码将查找`Toolbar`并调用`SetActionBar`以便`Toolbar`将执行默认操作栏特征。 工具栏的标题更改为**我工具栏**。 在此代码示例中，所示`ToolBar`可以作为操作栏直接引用。 编译并运行此应用&ndash;的自定义`Toolbar`代替默认操作栏显示： 

[![绿色配色方案的自定义工具栏的屏幕截图](replacing-the-action-bar-images/02-after-sml.png)](replacing-the-action-bar-images/02-after.png#lightbox)

请注意，`Toolbar`独立于样式`Theme.Material.Light.DarkActionBar`应用于应用程序的其余部分的主题。 

如果运行该应用程序时出现异常，请参阅[故障排除](#troubleshooting)下面一节。

 
## <a name="add-menu-items"></a>添加菜单项 

在本部分中，菜单将添加到`Toolbar`。 右上角区域`ToolBar`是保留的菜单项&ndash;每个菜单项 (也称为*操作项*) 可以执行的当前活动中的操作或它可以执行代表整个应用程序的操作。 

若要添加到菜单`Toolbar`: 

1.  添加到菜单图标 （如果需要）`mipmap-`的应用程序项目的文件夹。 Google 上提供了一套免费的菜单图标[材料图标](https://design.google.com/icons/)页。 

2.  通过添加新的菜单资源文件下定义的菜单项的内容**资源/菜单**。 

3.  实现`OnCreateOptionsMenu`活动的方法&ndash;此方法增大的菜单项。 

4.  实现`OnOptionsItemSelected`活动的方法&ndash;点击的菜单项时，此方法执行的操作。 

以下各节演示此过程的详细信息，通过添加**编辑**并**保存**的自定义的菜单项`Toolbar`。 



### <a name="install-menu-icons"></a>安装菜单图标

在继续进行`ToolbarFun`示例应用将菜单图标添加到应用程序项目。 下载[工具栏图标](https://github.com/xamarin/monodroid-samples/blob/master/Supportv7/AppCompat/Toolbar/Resources/toolbar-icons-plus.zip?raw=true)、 解压缩，然后将所提取的内容复制*mipmap-* 到项目文件夹*mipmap-* 下的文件夹**ToolbarFun /资源**和在项目中包含的每个添加的图标文件。


### <a name="define-a-menu-resource"></a>定义菜单资源

创建一个新**菜单**下的子目录**资源**。 在中**菜单**子目录中，创建名为新的菜单资源文件**top_menus.xml**并将其内容替换为以下 XML: 

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

此 XML 将创建三个菜单项：

-   **编辑**使用的菜单项`ic_action_content_create.png`图标 （铅笔）。 

-   一个**保存**使用的菜单项`ic_action_content_save.png`图标 （磁盘）。 

-   一个**首选项**没有图标的菜单项。

`showAsAction`的特性**编辑**并**保存**菜单项设置为`ifRoom`&ndash;此设置会导致这些菜单项显示在`Toolbar`是否存在足够的空间，才能显示。 **首选项**菜单项集`showAsAction`到`never`&ndash;这将导致**首选项**菜单中显示*溢出*菜单 （三个垂直点）。 


### <a name="implement-oncreateoptionsmenu"></a>实现 OnCreateOptionsMenu

添加以下方法**MainActivity.cs**:

```csharp
public override bool OnCreateOptionsMenu(IMenu menu)
{
    MenuInflater.Inflate(Resource.Menu.top_menus, menu);
    return base.OnCreateOptionsMenu(menu);
}
```

Android 调用`OnCreateOptionsMenu`方法，以便应用程序可以指定活动的菜单资源。 在此方法中， **top_menus.xml**资源被放大到传递`menu`。 此代码会导致新**编辑**，**保存**，并**首选项**菜单项显示在`Toolbar`。 



### <a name="implement-onoptionsitemselected"></a>实现 OnOptionsItemSelected

添加以下方法**MainActivity.cs**:

```csharp
public override bool OnOptionsItemSelected(IMenuItem item)
{
    Toast.MakeText(this, "Action selected: " + item.TitleFormatted,
        ToastLength.Short).Show();
    return base.OnOptionsItemSelected(item);
}
```

当用户点击的菜单项时，Android 会调用`OnOptionsItemSelected`方法，并传递所选菜单项中的。 在此示例中，实现只是显示 toast 通知来指示点击的菜单项的。 

生成并运行`ToolbarFun`以查看在工具栏中的新菜单项。 `Toolbar`现在显示三个菜单图标，如以下屏幕截图中所示： 

[![图示演示位置的编辑、 保存、 并溢出菜单项](replacing-the-action-bar-images/04-menu-items-sml.png)](replacing-the-action-bar-images/04-menu-items.png#lightbox)

当用户点击**编辑**显示菜单项，toast 通知，指示`OnOptionsItemSelected`方法被调用： 

[![显示编辑项点击 Toast 的屏幕截图](replacing-the-action-bar-images/05-toast-displayed-sml.png)](replacing-the-action-bar-images/05-toast-displayed.png#lightbox)

当用户点击溢出菜单中，**首选项**显示菜单项。 通常情况下，不太常见的操作应放置在溢出菜单&ndash;此示例使用的溢出菜单**首选项**因为不经常使用它作为**编辑**和**保存**: 

[![在溢出菜单中显示的屏幕截图的首选项菜单项](replacing-the-action-bar-images/06-preferences-sml.png)](replacing-the-action-bar-images/06-preferences.png#lightbox)

有关 Android 菜单的详细信息，请参阅 Android 开发人员[菜单](https://developer.android.com/guide/topics/ui/menus.html)主题。 
 

## <a name="troubleshooting"></a>疑难解答

以下提示可以帮助调试在操作栏替换工具栏时可能发生的问题。

### <a name="activity-already-has-an-action-bar"></a>活动已具有操作栏

如果应用未正确配置为使用自定义主题中所述[应用自定义主题](#apply-the-custom-theme)，出现以下异常时运行该应用程序可能会发生：

![不使用自定义主题时，可以发生的错误](replacing-the-action-bar-images/03-theme-not-defined.png)

此外，可能会生成一条错误消息如下所示：_Java.Lang.IllegalStateException:此活动已由窗口装潢提供操作栏。_ 

若要更正此错误，请确认`android:theme`属性 (attribute) 的自定义主题添加到`<application>`(在**properties/Androidmanifest.xml**) 中所述[应用自定义主题](#apply-the-custom-theme). 如果此外，可能会导致此错误`Toolbar`布局或自定义主题未正确配置。


## <a name="related-links"></a>相关链接

- [棒糖形工具栏 （示例）](https://developer.xamarin.com/samples/monodroid/android5.0/Toolbar/)
- [AppCompat 工具栏 （示例）](https://developer.xamarin.com/samples/monodroid/Supportv7/AppCompat/Toolbar/)
