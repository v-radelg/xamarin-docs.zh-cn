---
title: 添加特定于 iOS 的格式设置
description: 此文章介绍了如何设置特定于 iOS 的外观，而不使用 Xamarin.Forms 自定义呈现器。
ms.prod: xamarin
ms.assetid: CE50E207-D092-4D88-8439-1B51F178E7ED
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/29/2016
ms.openlocfilehash: 3b8a440617dedfbe23f869e865b3cedae21d6c5b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60946358"
---
# <a name="adding-ios-specific-formatting"></a>添加特定于 iOS 的格式设置

一种方法来设置特定于 iOS 的格式设置是创建[自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)的控件和设置特定于平台的样式和颜色的每个平台。

其他选项来控制包括 Xamarin.Forms iOS 应用的外观的方式：

* 配置显示在选项[ **Info.plist**](#info-plist)
* 设置控件样式通过[ `UIAppearance` API](#uiappearance)

下面将讨论以下替代方法。

<a name="info-plist"/>

## <a name="customizing-infoplist"></a>自定义 Info.plist

**Info.plist**文件允许您配置 iOS 应用程序 renderering，例如如何 （以及是否） 显示状态栏的某些方面。

例如，[待办事项示例](https://developer.xamarin.com/samples/xamarin-forms/Todo/)使用下面的代码在所有平台上设置的导航栏的颜色和文本颜色：

```csharp
var nav = new NavigationPage (new TodoListPage ());
nav.BarBackgroundColor = Color.FromHex("91CA47");
nav.BarTextColor = Color.White;
```

结果如下面的屏幕代码段所示。 请注意状态栏项是黑色 （这不能设置在 Xamarin.Forms 中由于它是特定于平台的功能）。

![](theme-images/status-default-sml.png "iOS 主题设置")

理想情况下状态栏也是白色-我们可以直接在 iOS 项目中完成的内容。 以下条目添加到**Info.plist**强制状态栏为白色：

![](theme-images/info-plist.png "iOS Info.plist 条目")

或编辑相应**Info.plist**文件直接以包括：

```xml
<key>UIStatusBarStyle</key>
<string>UIStatusBarStyleLightContent</string>
<key>UIViewControllerBasedStatusBarAppearance</key>
<false/>
```

现在，当应用运行时，在导航栏为绿色，其文本为白色 （由于 Xamarin.Forms 格式设置）*和*状态栏文本也是白色归功于特定于 iOS 的配置：

![](theme-images/status-white-sml.png "iOS 主题设置")

<a name="uiappearance"/>

## <a name="uiappearance-api"></a>UIAppearance API

[ `UIAppearance` API](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md)可用于 iOS 的许多控件上设置视觉对象属性*而无需*无需创建[自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)。

添加到代码的单行**AppDelegate.cs** `FinishedLaunching`方法可设置给定的类型使用的所有控件的都样式及其`Appearance`属性。 下面的代码包含两个示例的全局设置样式选项卡栏，切换控件：

**AppDelegate.cs** iOS 项目中

```csharp
public override bool FinishedLaunching (UIApplication app, NSDictionary options)
{
  // tab bar
    UITabBar.Appearance.SelectedImageTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
  // switch
    UISwitch.Appearance.OnTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
    // required Xamarin.Forms code
    Forms.Init ();
    LoadApplication (new App ());
    return base.FinishedLaunching (app, options);
}
```

### <a name="uitabbar"></a>UITabBar

默认情况下中的选定选项卡栏图标 [`TabbedPage`](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)
将蓝色：

![](theme-images/tabbar-default.png "默认 iOS TabbedPage 中的选项卡栏图标")

若要更改此行为，请设置`UITabBar.Appearance`属性：

```csharp
UITabBar.Appearance.SelectedImageTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
```

这将导致所选的选项卡以显示为绿色：

![](theme-images/tabbar-custom.png "绿色 iOS TabbedPage 中的选项卡栏图标")

使用此 API 允许您自定义外观的 Xamarin.Forms`TabbedPage`使用非常少的代码在 iOS 上。 请参阅[自定义选项卡方案](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/iOS/customize-tabs)有关使用自定义呈现器设置特定字体为选项卡的详细信息。

### <a name="uiswitch"></a>UISwitch

`Switch`控件是可以轻松地设置样式的另一个示例：

```csharp
UISwitch.Appearance.OnTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
```

这些两个屏幕截图显示默认值`UISwitch`上的左窗格和自定义的版本控制 (设置`Appearance`) 中右侧[待办事项示例](https://developer.xamarin.com/samples/xamarin-forms/Todo/):

![](theme-images/switch-default.png "默认 UISwitch 颜色") ![](theme-images/switch-custom.png "自定义 UISwitch 颜色")

### <a name="other-controls"></a>其他控件

其默认颜色和其他属性集使用多个 iOS 用户界面控件可以具有[ `UIAppearance` API](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md)。



## <a name="related-links"></a>相关链接

- [UIAppearance](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md)
- [自定义选项卡](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/iOS/customize-tabs)
