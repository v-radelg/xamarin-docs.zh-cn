---
title: Xamarin.Forms 主题
description: 本文介绍了 Xamarin.Forms 主题定义的标准视图的特定视觉外观。
ms.prod: xamarin
ms.assetid: 3DFB7C55-69F6-4980-A501-588719143482
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/01/2017
ms.openlocfilehash: 9a34cfab0c3ed045968f48ac6c67b2f4b66990cc
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832454"
---
# <a name="xamarinforms-themes"></a>Xamarin.Forms 主题

[![下载示例](~/media/shared/download.png)下载示例](https://github.com/xamarin/xamarin-forms-samples/tree/master/Themes/ThemesDemo)

![](~/media/shared/preview.png "此 API 目前处于预览状态")

Xamarin.Forms 主题曾宣布在 Evolve 2016，并可作为客户试用并提供反馈的预览。

主题将添加到 Xamarin.Forms 应用程序中包括**Xamarin.Forms.Theme.Base** Nuget 包，以及其他包，用于定义特定的主题 （例如。 Xamarin.Forms.Theme.Light) 或其他本地主题可以定义应用程序。

请参阅[浅色主题](light.md)并[深色主题](dark.md)如何将它们添加到应用程序，或查看说明页[示例自定义主题](custom.md)。

> [!IMPORTANT]
> 您还应遵循的步骤[加载程序集 （下面） 为主题](#loadtheme)通过将一些样本代码添加到 iOS`AppDelegate`和 Android `MainActivity`。 这将在将来的预览版的版本中得到改进。


## <a name="control-appearance"></a>控件外观

[Light](light.md)并[深色](dark.md)这两个主题定义特定的可视化外观，对标准控件。 一旦应用程序的资源字典中添加了一个主题，将更改标准控件的外观。

下面的 XAML 标记显示了一些常见的控件：

```xaml
<StackLayout Padding="40">
    <Label Text="Regular label" />
    <Entry Placeholder="type here" />
    <Button Text="OK" />
    <BoxView Color="Yellow" />
    <Switch />
</StackLayout>
```

这些屏幕截图显示了与这些控件：

* 应用任何主题
* 浅色主题 （仅为具有无主题的细微差异）
* 深色主题

![](images/standard-none-sml.png "控件而不通过主题") ![](images/standard-light-sml.png "控件与浅色主题") ![](images/standard-dark-sml.png "深色主题的控件")

<a name="styleclass" />

## <a name="styleclass"></a>StyleClass

`StyleClass`属性允许视图的外观，以根据提供的主题的定义进行更改。

[Light](light.md)并[深色](dark.md)这两个主题定义的三个不同的外观`BoxView`: `HorizontalRule`， `Circle`，和`Rounded`。 此标记显示三个不同`BoxView`s，使用不同的样式类应用：

```xaml
<StackLayout Padding="40">
    <BoxView StyleClass="HorizontalRule" />
    <BoxView StyleClass="Circle" />
    <BoxView StyleClass="Rounded" />
</StackLayout>
```

这会使使用浅色和深色，如下所示：

![](images/boxview-light-sml.png "使用浅色主题 StyleClass 字数") ![](images/boxview-dark-sml.png "与深色主题 StyleClass 字数")

<a name="builtin" />

## <a name="built-in-classes"></a>内置类

除了自动设置样式公共控件光线和深色主题目前支持可通过设置应用的以下类`StyleClass`这些控件上：

**BoxView**

* HorizontalRule
* 圆圈
* 舍入

**Image**

* 圆圈
* 舍入
* 缩略图

**Button**

* 默认
* 基本
* 成功
* T:System.Diagnostics.Switch
* 警告
* 危险
* 链接
* 小
* 大型

**标签**

* Header
* 小标题
* 正文
* 链接
* 反函数


## <a name="troubleshooting"></a>疑难解答

<a name="loadtheme" />

### <a name="could-not-load-file-or-assembly-xamarinformsthemelight-or-one-of-its-dependencies"></a>无法加载文件或程序集 Xamarin.Forms.Theme.Light 或其某个依赖项

在预览版本中，主题可能不能在运行时加载。 添加代码，如下所示在相关项目来修复此错误。

**iOS**

在中**AppDelegate.cs**添加以下行后 `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.iOS.UnderlineEffect);
```

**Android**

在中**MainActivity.cs**添加以下行后 `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.Android.UnderlineEffect);
```


## <a name="related-links"></a>相关链接

- [ThemesDemo 示例](https://github.com/xamarin/xamarin-forms-samples/tree/master/Themes/ThemesDemo)
