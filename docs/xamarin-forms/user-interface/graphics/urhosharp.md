---
title: 在 Xamarin.Forms 中使用 UrhoSharp
description: 本文介绍如何使用 UrhoSharp 将 3D 图形添加到 Xamarin.Forms 应用程序的高级可视化效果。
ms.prod: xamarin
ms.assetid: 0646B98E-CC04-4537-9715-9F82338FD7FF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/11/2016
ms.openlocfilehash: fc316a9e6ab4261eaa956a987b47aeaf546344a2
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67675275"
---
# <a name="using-urhosharp-in-xamarinforms"></a>在 Xamarin.Forms 中使用 UrhoSharp

[![下载示例](~/media/shared/download.png)下载示例](https://github.com/xamarin/urho-samples/tree/master/FormsSample)

## <a name="what-is-urhosharp"></a>什么是 UrhoSharp？

[UrhoSharp](~/graphics-games/urhosharp/index.md)是 Xamarin 和.NET 开发人员的功能强大的 3D 引擎。 [简介](~/graphics-games/urhosharp/introduction.md)更详细说明 UrhoSharp 库，并[这些说明](~/graphics-games/urhosharp/using.md)描述如何进行编程的场景和操作。

可以使用 UrhoSharp 来呈现在 Xamarin.Forms 应用程序中的图形。
这[示例](https://github.com/xamarin/urho-samples/tree/master/FormsSample)演示了如何使用 UrhoSharp 构造交互式 3D 图表：

![](urhosharp-images/ios-animation.gif "在 iOS 上 UrhoSharp 三维的交互式图表")
![](urhosharp-images/android-animation.gif "UrhoSharp 三维在 Android 上的交互式图表")

## <a name="adding-the-urhosharp-nuget-packages"></a>添加 UrhoSharp Nuget 包

然后再使用 UrhoSharp，开发人员需要将 UrhoSharp Nuget 包添加到他们的解决方案。 本指南假定一个 Xamarin.Forms 项目，与 iOS、 Android 和.NET Standard 类库项目。 .NET Standard 库项目中; 将写入的所有代码但 UrhoSharp Nuget 必须也添加到 iOS 和 Android 项目。

UrhoSharp.Forms Nuget 程序包中包含的所有创建 UrhoSharp 对象所需对象。 UrhoSharp.Forms nuget 包包含`UrhoSurface`类，该类用于托管 UrhoSharp 在 Xamarin.Forms 中。
若要开始，请右键单击**包**在.NET Standard 库项目中，选择文件夹**添加包...** .输入搜索词**UrhoSharp.Forms**，选择**适用于 Xamarin.Forms 的 UrhoSharp**，然后单击**添加包**。

[![](urhosharp-images/add-package-sml.png "添加包对话框")](urhosharp-images/add-package.png#lightbox "添加包对话框")

UrhoSharp.Forms NuGet 包将添加到项目：

![](urhosharp-images/packages.png "包文件夹")

对于特定于平台的项目 （如 iOS 和 Android） 重复上述步骤。

## <a name="walkthrough-adding-urhosharp-to-a-xamarinforms-app"></a>演练：向 Xamarin.Forms 应用添加 UrhoSharp

以下步骤介绍了 Xamarin.Forms UrhoSharp 示例中的代码：

1. [创建 Xamarin 窗体页](#1)
2. [添加 UrhoSurface](#2)
3. [生成 Urho 应用程序](#3)
4. [将图表类添加到 UrhoSurface](#4)
5. [交互使用 UrhoSharp](#5)

请注意，此示例使用C#6 功能，并在较旧版本的 Visual Studio 可能无法编译。

<a name="1"/>

### <a name="1-create-a-xamarin-forms-page"></a>1.创建 Xamarin 窗体页

下面的代码显示了 Xamarin.Forms 页面`UrhoPage`添加 Urho 相关的所有代码之前：

```csharp
public class UrhoPage : ContentPage
{
  Slider selectedBarSlider, rotationSlider;

  public UrhoPage()
  {
    // we'll add Urho later

    rotationSlider = new Slider(0, 500, 250);

    selectedBarSlider = new Slider(0, 5, 2.5);

    Title = " UrhoSharp + Xamarin.Forms";
    Content = new StackLayout {
      Padding = new Thickness (12, 12, 12, 40),
      VerticalOptions = LayoutOptions.FillAndExpand,
      Children = {
        rotationSlider,
        new Label { Text = "SELECTED VALUE:" },
        selectedBarSlider,
      }
    };
  }
```

<a name="2"/>

### <a name="2-add-the-urhosurface"></a>2.添加 UrhoSurface

UrhoSharp 可以托管在`ContentPage`其他 Xamarin.Forms 控件一样。
所示的代码段`UrhoSurface`添加到 Xamarin.Forms 页面：

```csharp
using Urho;
using Urho.Forms;
...
public class UrhoPage : ContentPage
{
  UrhoSurface urhoSurface;

  public UrhoPage()
  {
    urhoSurface = new UrhoSurface();
    urhoSurface.VerticalOptions = LayoutOptions.FillAndExpand;
...
    Content = new StackLayout {
    Padding = new Thickness (12, 12, 12, 40),
    VerticalOptions = LayoutOptions.FillAndExpand,
    Children = {
      urhoSurface,  // added
      new Label { Text = "ROTATION:" },
      rotationSlider,
      new Label { Text = "SELECTED VALUE:" },
      selectedBarSlider,
    }
  };
```

<a name="3"/>

### <a name="3-build-a-urho-application"></a>3.生成 Urho 应用程序

请参阅`Charts`的此示例中使用 Urho 3D 图形的实现。 基本代码大纲显示下面-请注意，此类实现`Urho.Application`到不同`Xamarin.Forms.Application`类中实现**App.cs**。

```csharp
using Urho;
using Urho.Actions;
using Urho.Gui;
using Urho.Shapes;

namespace FormsSample
{
    public class Charts : Urho.Application
    {
    public Charts (ApplicationOptions options = null) : base(options) { }
    protected override void Start ()
    {
      ...
    }
    protected override void OnUpdate(float timeStep)
    {
      ...
    }
```

[UrhoSharp 文档](~/graphics-games/urhosharp/index.md)包含有关如何生成 3D 场景和操作的详细信息。

<a name="4"/>

### <a name="4-add-the-charts-class-to-the-urhosurface"></a>4.将图表类添加到 UrhoSurface

使用`UrhoSurface.Show<T>`泛型方法添加到 Xamarin.Forms 页面 Urho 应用程序。 下面的代码段演示创建所需的附加代码`Charts`类：

```csharp
public class UrhoPage : ContentPage
{
  Charts urhoApp;
  ...
  protected override async void OnAppearing ()
  {
    urhoApp = await urhoSurface.Show<Charts> (new ApplicationOptions(assetsFolder: null)
      { Orientation = ApplicationOptions.OrientationType.Portrait });
  }
```

注意：`Show<T>`方法是异步的应使用调用`await`关键字。

<a name="5"/>

### <a name="5-interacting-with-urhosharp"></a>5.交互使用 UrhoSharp

该示例允许图表条来选择和修改。 `Charts`类公开`Bars`和`SelectedBar`若要启用这种交互。

每个"栏"中的所选内容事件处理程序之后添加`Charts`已呈现类，通过循环已公开`Bars`集合：

```csharp
protected override async void OnAppearing ()
{
  urhoApp = await urhoSurface.Show<Charts>(new ApplicationOptions(assetsFolder: null) { Orientation = ApplicationOptions.OrientationType.Portrait });
  foreach (var bar in urhoApp.Bars)
  {
    bar.Selected += OnBarSelection;
  }
}
```

事件处理程序使用的 Xamarin.Forms 值`Slider`控件可调整给定条的高度：

```csharp
private void OnBarSelection(Bar bar)
{
  //reset value
  selectedBarSlider.ValueChanged -= OnValuesSliderValueChanged;
  selectedBarSlider.Value = bar.Value;
  selectedBarSlider.ValueChanged += OnValuesSliderValueChanged;
}

void OnValuesSliderValueChanged(object sender, ValueChangedEventArgs e)
{  // C# 6
  if (urhoApp?.SelectedBar != null)
  {
    urhoApp.SelectedBar.Value = (float)e.NewValue;
  }
}
```

最后，连接这两个`Slider`控件，使其值更改时影响 UrhoSharp 画布。 第一个滑块旋转三维图表图像并在第二个滑块来调整所选条形的高度。

```csharp
rotationSlider = new Slider(0, 500, 250);
rotationSlider.ValueChanged += (s, e) => urhoApp?.Rotate((float)(e.NewValue - e.OldValue));

selectedBarSlider = new Slider(0, 5, 2.5);
selectedBarSlider.ValueChanged += OnValuesSliderValueChanged;
```

在动画[页面顶部](#what-is-urhosharp)显示运行的示例。

## <a name="summary"></a>总结

此页显示了如何使用 UrhoSharp 将三维数据可视化效果添加到 Xamarin.Forms。 读取[UrhoSharp 文档](~/graphics-games/urhosharp/index.md)有关如何生成可以使用如上所示的方法的 Xamarin.Forms 应用中包含的 Urho 场景的详细信息。


## <a name="related-links"></a>相关链接

- [图表示例 (C# 6)](https://github.com/xamarin/urho-samples/tree/master/FormsSample)
- [UrhoSharp](~/graphics-games/urhosharp/index.md)
