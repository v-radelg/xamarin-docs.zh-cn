---
title: 使用 ActionBar 的选项卡式的布局
description: 本指南介绍，并介绍了如何使用 ActionBar Api 创建 Xamarin.Android 应用程序中的选项卡式的用户界面。
ms.prod: xamarin
ms.assetid: B7E60AAF-BDA5-4305-9000-675F0438734D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 6ce8099aa4230a11a12f4fe8aeffe850f9ef2ce9
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61304482"
---
# <a name="tabbed-layouts-with-the-actionbar"></a>使用 ActionBar 的选项卡式的布局

_本指南介绍，并介绍了如何使用 ActionBar Api 创建 Xamarin.Android 应用程序中的选项卡式的用户界面。_


## <a name="overview"></a>概述

在操作栏是 Android UI 模式，它用于为选项卡、 应用程序标识、 菜单和搜索等主要功能提供一致的用户界面。 在 Android 3.0 （API 级别为 11） 中，Google 引入了 ActionBar Api 向 Android 平台。 ActionBar Api 引入了用户界面主题，以提供一致的外观和允许的选项卡式的用户界面的类。 本指南介绍如何将操作栏选项卡添加到 Xamarin.Android 应用程序。 它还讨论了如何使用 Android 支持库 v7 到向后移植到面向 Android 2.1 到 Android 2.3 的 Xamarin.Android 应用程序的操作栏选项卡。 

请注意，`Toolbar`是一个更高版本和更通用的操作栏组件，应使用而不是`ActionBar`(`Toolbar`旨在替换`ActionBar`)。 有关详细信息，请参阅[工具栏](~/android/user-interface/controls/tool-bar/index.md)。 



## <a name="requirements"></a>要求

所有 Xamarin.Android 应用程序面向 API 级别 11 (Android 3.0) 或更高版本的本机 Android Api 一部分中有权访问 ActionBar Api。 

某些 ActionBar Api 返回移植到了 API 级别 7 (Android 2.1)，通过[V7 AppCompat 库](https://developer.android.com/tools/support-library/features.html#v7-appcompat)，可由适用于 Xamarin.Android 应用通过[Xamarin Android 支持库-V7](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)包。



## <a name="introducing-tabs-in-the-actionbar"></a>引入了操作栏中的选项卡

在操作栏尝试同时显示所有其选项卡并使所有选项卡的大小基于最宽的选项卡标签的宽度相同。 这是下面的屏幕截图所示： 

![ActionBar 的所有大小相等的选项卡所示的示例屏幕截图](with-action-bar-images/image1.png)

当 ActionBar 无法显示所有选项卡后时，它将设置可水平滚动视图中的选项卡。 左侧或右侧才能看到其余的选项卡，用户可能往下轻扫。 从 Google Play 此屏幕截图显示了此示例： 

![可水平滚动视图中的选项卡的屏幕截图示例](with-action-bar-images/image2.png)

应与之关联的操作栏中的每个选项卡[*片段*](~/android/platform/fragments/index.md)。 当用户选择一个选项卡时，则应用程序将显示选项卡与相关联的片段。ActionBar 不负责向用户显示相应的片段。 相反，ActionBar 会通知应用程序通过实现 ActionBar.ITabListener 接口的类的选项卡中的状态更改。 此接口提供了三个选项卡的状态更改时，Android 将调用的回调方法： 

-  **OnTabSelected** -当用户选择选项卡时调用此方法。它应该会显示该片段。

-  **OnTabReselected** -当选项卡已被选中，但用户再次选择时调用此方法。 通常使用此回叫来刷新/更新显示的片段。

-  **OnTabUnselected** -当用户选择另一个选项卡时调用此方法。此回叫用于将状态保存在显示的片段中之前就会消失。

Xamarin.Android 包装`ActionBar.ITabListener`上的事件与`ActionBar.Tab`类。 应用程序可能会将事件处理程序分配给一个或多个这些事件。 有三个事件 (一个用于在每个方法`ActionBar.ITabListener`) 将引发操作栏选项卡： 

-  TabSelected
-  TabReselected
-  TabUnselected



### <a name="adding-tabs-to-the-actionbar"></a>向 ActionBar 添加选项卡

ActionBar 是本机 Android 3.0 （API 级别为 11） 和更高版本，并且可供任何针对此 API 是一个最少的 Xamarin.Android 应用程序。 

以下步骤演示了如何将 ActionBar 选项卡添加到 Android 活动： 

1. 在中`OnCreate`方法的活动&ndash;*之前初始化任何 UI 小组件*&ndash;应用程序必须设置`NavigationMode`上`ActionBar`到`ActionBar.NavigationModeTabs`此代码中所示代码片段：

   ```csharp
   ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
   SetContentView(Resource.Layout.Main);
   ```

2. 创建新选项卡上使用`ActionBar.NewTab()`。

3. 分配事件处理程序或提供自定义`ActionBar.ITabListener`将会响应用户交互操作栏选项卡时引发的事件的实现。

4. 添加到在上一步中创建的选项卡`ActionBar`。


以下代码是使用以下步骤将选项卡添加到使用事件处理程序的状态更改进行响应的应用程序的一个示例： 

```csharp
protected override void OnCreate(Bundle bundle)
{
    ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
    SetContentView(Resource.Layout.Main);

    ActionBar.Tab tab = ActionBar.NewTab();
    tab.SetText(Resources.GetString(Resource.String.tab1_text));
    tab.SetIcon(Resource.Drawable.tab1_icon);
    tab.TabSelected += (sender, args) => {
                           // Do something when tab is selected
                       };
    ActionBar.AddTab(tab);

    tab = ActionBar.NewTab();
    tab.SetText(Resources.GetString(Resource.String.tab2_text));
    tab.SetIcon(Resource.Drawable.tab2_icon);
    tab.TabSelected += (sender, args) => {
                           // Do something when tab is selected
                       };
    ActionBar.AddTab(tab);
}
```


#### <a name="event-handlers-vs-actionbaritablistener"></a>事件处理程序 vs ActionBar.ITabListener

应用程序应使用事件处理程序和`ActionBar.ITabListener`为不同的方案。 事件处理程序确实提供一定量的语法上方便;他们无需创建一个类并实现保存您`ActionBar.ITabListener`。 这种便利确实代价&ndash;Xamarin.Android 执行此转换的创建一个类和实现`ActionBar.ITabListener`为您。 当应用程序具有有限的数量的选项卡时，这是没问题。 

多个选项卡上，在处理时共享操作栏选项卡之间的常见功能，也可以是内存和性能，以创建一个自定义类，实现更高效`ActionBar.ITabListener`，和共享单个类的实例。 这将减少 GREF 的 Xamarin.Android 应用程序使用的数目。 



### <a name="backwards-compatibility-for-older-devices"></a>向后兼容性较旧的设备

[Android 支持库 v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)后端口操作栏选项卡添加到 Android 2.1 （API 级别 7）。 此组件添加到项目后，选项卡是可在 Xamarin.Android 应用程序中访问。

若要使用 ActionBar，活动必须子类`ActionBarActivity`和使用 AppCompat 主题，如下面的代码段中所示：

```csharp
[Activity(Label = "@string/app_name", Theme = "@style/Theme.AppCompat", MainLauncher = true, Icon = "@drawable/ic_launcher")]
public class MainActivity: ActionBarActivity
```

活动可能会获取对从其 ActionBar 的引用`ActionBarActivity.SupportingActionBar`属性。 下面的代码段说明了设置 ActionBar 在活动中的示例：

```csharp
[Activity(Label = "@string/app_name", Theme = "@style/Theme.AppCompat", MainLauncher = true, Icon = "@drawable/ic_launcher")]
public class MainActivity : ActionBarActivity, ActionBar.ITabListener
{
    static readonly string Tag = "ActionBarTabsSupport";

    public void OnTabReselected(ActionBar.Tab tab, FragmentTransaction ft)
    {
        // Optionally refresh/update the displayed tab.
        Log.Debug(Tag, "The tab {0} was re-selected.", tab.Text);
    }

    public void OnTabSelected(ActionBar.Tab tab, FragmentTransaction ft)
    {
        // Display the fragment the user should see
        Log.Debug(Tag, "The tab {0} has been selected.", tab.Text);
    }

    public void OnTabUnselected(ActionBar.Tab tab, FragmentTransaction ft)
    {
        // Save any state in the displayed fragment.
        Log.Debug(Tag, "The tab {0} as been unselected.", tab.Text);
    }

    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        SupportActionBar.NavigationMode = ActionBar.NavigationModeTabs;
        SetContentView(Resource.Layout.Main);
    }

    void AddTabToActionBar(int labelResourceId, int iconResourceId)
    {
        ActionBar.Tab tab = SupportActionBar.NewTab()
                                            .SetText(labelResourceId)
                                            .SetIcon(iconResourceId)
                                            .SetTabListener(this);
        SupportActionBar.AddTab(tab);
    }
}
```


## <a name="summary"></a>总结

在本指南中我们讨论了如何在 Xamarin.Android 中使用 ActionBar 创建选项卡式的用户界面。 我们介绍了如何将选项卡添加到 ActionBar 和活动与通过选项卡事件的交互方式`ActionBar.ITabListener`接口。 我们还了解到 Android 支持库 v7 AppCompat 包 backports ActionBar 与旧版本的 Android 的选项卡。 


## <a name="related-links"></a>相关链接

- [ActionBarTabs （示例）](https://developer.xamarin.com/samples/monodroid/UserInterface/ActionBarTabs/)
- [工具栏](~/android/user-interface/controls/tool-bar/index.md)
- [片段](~/android/platform/fragments/index.md)
- [ActionBar](https://developer.android.com/guide/topics/ui/actionbar.html)
- [ActionBarActivity](https://developer.android.com/reference/android/support/v7/app/ActionBarActivity.html)
- [操作栏模式](https://developer.android.com/design/patterns/actionbar.html)
- [Android v7 AppCompat](https://developer.android.com/tools/support-library/features.html#v7-appcompat)
- [Xamarin.Android 支持库 v7 AppCompat NuGet 包](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
