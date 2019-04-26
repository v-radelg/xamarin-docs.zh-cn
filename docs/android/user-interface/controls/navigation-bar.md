---
title: 导航栏
ms.prod: xamarin
ms.assetid: 6023DB7E-9E72-4B90-A96A-11BC297B8A3D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/01/2017
ms.openlocfilehash: 9455cac81a0f9ea81e08cf63397e45c1698e1c1b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61153595"
---
# <a name="navigation-bar"></a>导航栏

Android 4 引入了名为的新系统用户界面功能*导航栏*，这样就不包含的硬件按钮的设备上的导航控件**主页**，**返回**，并**菜单**。
下面的屏幕截图显示了从 Nexus 素数设备导航栏：

 [![Android 导航栏的示例](navigation-bar-images/19-navbar.png)](navigation-bar-images/19-navbar.png#lightbox)

可以使用几个新的标记，以控制导航栏和其控件的可见性以及 Android 3 中引入的系统栏的可见性。 在定义标志`Android.View.View`类，如下所示：

-   `SystemUiFlagVisible` &ndash; 使导航栏可见。 
-   `SystemUiFlagLowProfile` &ndash; 在导航栏中的控件进行测试，dims。 
-   `SystemUiFlagHideNavigation` &ndash; 隐藏导航栏。 


这些标志可以通过设置应用于的任意视图中的视图层次结构`SystemUiVisibility`属性。 如果多个视图将此属性设置，系统将它们与或运算结合起来，并将其应用，只要设置了标志窗口保留焦点。 当您删除一个视图时，它已设置任何标志也将被删除。

下面的示例演示一个简单的应用程序在其中单击任一按钮更改`SystemUiVisibility`:

 [![演示可见、 低配置文件，隐藏 SystemUiVisibility 的屏幕截图](navigation-bar-images/18-systemuivisibility.png)](navigation-bar-images/18-systemuivisibility.png#lightbox)

若要更改的代码`SystemUiVisibility`上的属性设置`TextView`从每个按钮的单击事件处理程序，如下所示：

```csharp
var tv = FindViewById<TextView> (Resource.Id.systemUiFlagTextView);
var lowProfileButton = FindViewById<Button>(Resource.Id.lowProfileButton);
var hideNavButton = FindViewById<Button> (Resource.Id.hideNavigation);
var visibleButton = FindViewById<Button> (Resource.Id.visibleButton);
           
lowProfileButton.Click += delegate {
    tv.SystemUiVisibility =
        (StatusBarVisibility)View.SystemUiFlagLowProfile;
};
           
hideNavButton.Click += delegate {
    tv.SystemUiVisibility =
       (StatusBarVisibility)View.SystemUiFlagHideNavigation;        
};
           
visibleButton.Click += delegate {
    tv.SystemUiVisibility = (StatusBarVisibility)View.SystemUiFlagVisible;
}
```

此外，`SystemUiVisibility`更改引发`SystemUiVisibilityChange`事件。 就像设置`SystemUiVisibility`属性中，处理程序`SystemUiVisibilityChange`层次结构中的任何视图，可以注册事件。 例如，以下代码使用`TextView`要注册的事件实例：

```csharp
tv.SystemUiVisibilityChange +=
  delegate(object sender, View.SystemUiVisibilityChangeEventArgs e) {
        tv.Text = String.Format ("Visibility = {0}", e.Visibility);
  };
```



## <a name="related-links"></a>相关链接

- [SystemUIVisibilityDemo （示例）](https://developer.xamarin.com/samples/monodroid/SystemUIVisibilityDemo/)
- [引入 Ice Cream Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 平台](https://developer.android.com/sdk/android-4.0.html)
