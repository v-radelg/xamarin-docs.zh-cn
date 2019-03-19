---
ms.assetid: 7C132A7C-4973-4B2D-98DC-3661C08EA33F
title: WPF 与。Xamarin.Forms 应用程序生命周期
description: 本文档会比较的相似之处和 Xamarin.Forms 和 WPF 应用程序的应用程序生命周期之间的差异。 它还会查看可视化树、 图形、 资源和样式。
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: 5f157f2bbf36076e542a5f96b912cb1788a99052
ms.sourcegitcommit: 64d6da88bb6ba222ab2decd2fdc8e95d377438a6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58175221"
---
# <a name="wpf-vs-xamarinforms-app-lifecycle"></a>WPF 与。Xamarin.Forms 应用程序生命周期

Xamarin.Forms 会占用大量的特征之前，尤其是 WPF 基于 XAML 的框架设计指南。 但是，在其他方面它偏差都显著可以是尝试迁移的人员的粘滞点。 本文档尝试确定这些问题的一些，并提供到 Xamarin.Forms 指南在可能到桥 WPF 知识的情况。

## <a name="app-lifecycle"></a>应用生命周期

WPF 与 Xamarin.Forms 之间的应用程序生命周期是类似的。 同时在外部 （平台） 代码中启动并启动方法调用通过用户界面。 不同之处在于，在然后初始化并创建应用程序的 UI 的特定于平台的程序集中始终启动 Xamarin.Forms。

**WPF**

- `Main method > App > MainWindow`

> [!NOTE]
> `Main`方法是，默认情况下，自动生成并在代码中不可见。

**Xamarin.Forms**

- **iOS** &ndash; `Main method > AppDelegate > App > ContentPage`
- **Android** &ndash; `MainActivity > App > ContentPage`
- **UWP** &ndash; `Main method > App(UWP) > MainPage(UWP) > App > ContentPage`

### <a name="application-class"></a>应用程序类

WPF 与 Xamarin.Forms 具有`Application`创建为单一实例的类。 在大多数情况下，应用将从此类派生以提供自定义应用程序，虽然这并非严格要求在 WPF 中。 两者都公开`Application.Current`属性查找创建的单一实例。

### <a name="global-properties--persistence"></a>全局属性 + 暂留

WPF 与 Xamarin.Forms 具有`Application.Properties`字典提供可用于存储应用程序中的任意位置访问的全局应用程序级别对象。 主要区别是 Xamarin.Forms 将会_持久保存_时应用已挂起，但它重新启动时重新加载这些集合中存储任何基元类型。 WPF 自动支持该行为-大多数开发人员而是依赖于独立存储，或利用内置`Settings`支持。

## <a name="defining-pages-and-the-visual-tree"></a>定义页和可视化树

WPF 使用`Window`作为任何顶层可视元素的根元素。 这将在 Windows 世界中，若要显示的信息定义 HWND。 可以创建和显示同时根据需要在 WPF 中的任意多个窗口。

在 Xamarin.Forms 中，顶层的视觉对象始终定义由平台-例如，在 iOS 上，它是`UIWindow`。 将内容划分为使用这些平台的本机表示形式的 Xamarin.Forms 呈现`Page`类。 每个`Page`在 Xamarin.Forms 中表示唯一"中的页"应用程序，其中仅有一个为可见一次。

这两个 WPFs`Window`和 Xamarin.Forms`Page`包括`Title`属性来影响显示的标题，并且两个具有`Icon`属性来显示特定图标页 (**注意**，标题和图标并不总是在 Xamarin.Forms 中可见）。 此外，您可以更改常见 visual 属性上都如背景色或图像。

从技术上讲可以呈现到两个单独平台视图 (例如定义两个`UIWindow`对象它们具有第二个呈现到外部显示器或 AirPlay)，它需要特定于平台的代码来执行此操作并不直接支持的功能Xamarin.Forms 本身。

### <a name="views"></a>Views

这两个框架的可视化层次结构是类似的。 WPF 是有点深入由于它对所见即所得的文档的支持。

**WPF**

```
DependencyObject - base class for all bindable things
   Visual - rendering mechanics
      UIElement - common events + interactions
         FrameworkElement - adds layout
            Shape - 2D graphics
            Control - interactive controls
```

**Xamarin.Forms**

```
BindableObject - base class for all bindable things
   Element - basic parent/child support + resources + effects
      VisualElement - adds visual rendering properties (color, fonts, transforms, etc.)
         View - layout + gesture support
```

### <a name="view-lifecycle"></a>视图生命周期

Xamarin.Forms 主要面向围绕移动的方案。 在这种情况下，应用程序都_激活_，_挂起_，并_重新激活_当用户与之交互。 这是类似于离开单击`Window`在 WPF 应用程序并且有一组方法和对应的事件可以重写，也可以挂接到要监视此行为。

| 目标 | WPF 方法 | Xamarin.Forms 方法 |
|--- |--- |--- |
|初始激活|ctor + Window.OnLoaded|ctor + Page.OnStart|
|所示|Window.IsVisibleChanged|Page.Appearing|
|Hidden|Window.IsVisibleChanged|Page.Disappearing|
|挂起/丢失焦点|Window.OnDeactivated|Page.OnSleep|
|激活/实际焦点|Window.OnActivated|Page.OnResume|
|Closed|Window.OnClosing + Window.OnClosed|n/a|


这两个支持显示/隐藏子控件，在 WPF 中它是三种状态属性`IsVisible`（可见、 隐藏和折叠）。 在 Xamarin.Forms 中，它是只是可见还是隐藏通过`IsVisible`属性。

### <a name="layout"></a>布局

页面布局发生同一 2-在阶段中 （度量值/排列） 发生在 WPF 中。 您可以通过重写在 Xamarin.Forms 中的以下方法挂钩到的页面布局`Page`类：

| 方法 | 目标 |
|--- |--- |
|OnChildMeasureInvalidated|子项的首选的大小已更改。|
|OnSizeAllocated|分配页的宽度/高度。|
|LayoutChanged 事件|布局/页的大小已更改。|

那里没有全局布局事件称为今天，也没有全局`CompositionTarget.Rendering`如下事件： 在 WPF 中找到。

#### <a name="common-layout-properties"></a>常见的布局属性

WPF 和都支持的 Xamarin.Forms`Margin`到控件元素，周围的间距和`Padding`控制调整间距_内_元素。 此外，大多数 Xamarin.Forms 布局视图具有属性来控制间距 （例如行或列）。

此外，大多数元素具有属性影响如何在父容器中放置：

| WPF | Xamarin.Forms | 目标 |
|--- |--- |--- |
|HorizontalAlignment|HorizontalOptions|左/Center/Right/Stretch 选项|
|VerticalAlignment|VerticalOptions|顶部/Center/底部/Stretch 选项|

> [!NOTE]
> 这些属性的实际的解释取决于父容器。

#### <a name="layout-views"></a>布局视图

WPF 与 Xamarin.Forms 使用布局控件来定位子元素。 在大多数情况下，它们是非常彼此靠近在功能方面。

| WPF | Xamarin.Forms | 布局样式 |
|--- |--- |--- |
|StackPanel|StackLayout|从左到右或从上到下无限堆叠|
|Grid|Grid|表格格式 （行和列）|
|DockPanel|n/a|停靠到窗口边缘|
|Canvas|AbsoluteLayout|像素/坐标定位|
|WrapPanel|n/a|包装堆栈|
|n/a|RelativeLayout|相对的基于规则的定位|

> [!NOTE]
> Xamarin.Forms 不支持`GridSplitter`。

使用这两个平台_附加属性_微调子级。

### <a name="rendering"></a>“呈现”

WPF 和 Xamarin.Forms 的呈现机制是全然不同。 在 WPF 中，直接创建的控件内容呈现到屏幕上的像素。 WPF 维护两个对象图 (_树_) 来表示此-_逻辑树_表示控件中的代码或 XAML，定义并_可视化树_表示这是在屏幕发生的实际呈现执行直接通过可视元素 （通过虚拟 draw 方法），或通过 XAML 定义`ControlTemplate`它们可以替换或自定义。 通常情况下，可视化树是更复杂，因为它包含控件、 隐式内容等标签周围的边框等。WPF 还包括一组 Api (`LogicalTreeHelper`和`VisualTreeHelper`) 若要检查这些两个对象图形。

在 Xamarin.Forms 中，控件中定义`Page`是实际上只是简单的数据对象。 它们类似于逻辑树表示形式，但永远不会呈现在其自己的内容。 相反，它们是_数据模型_这会影响元素的呈现。 实际呈现通过[单独的一套_visual 呈现器_该列映射到每个控件类型](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)。 这些呈现器均由特定于平台的 Xamarin.Forms 程序集注册每个特定于平台的项目中。 可以看到列表[此处](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。 Xamarin.Forms 还具有对支持替换或扩展呈现，除了[效果](~/xamarin-forms/app-fundamentals/effects/index.md)这可用于影响基于每个平台的本机呈现。

#### <a name="the-logicalvisual-tree"></a>逻辑/Visual 树

没有任何公开的 API 以 Xamarin.Forms-中遍历逻辑树，但可以使用反射来获取相同的信息。 例如，[以下是一种方法可可以枚举逻辑子级](https://github.com/xamarinhq/xamu-infrastructure/blob/master/src/XamU.Infrastructure/Extensions/ElementExtensions.cs#L108)使用反射。

## <a name="graphics"></a>图形

Xamarin.Forms 不包括对一个简单的矩形之外的基元的图形系统 (`BoxView`)。 您可以包括第三方库，如下所述[SkiaSharp](~/graphics-games/skiasharp/index.md)若要获取跨平台 2D 绘制或[UrhoSharp](~/graphics-games/urhosharp/index.md) 3d。

## <a name="resources"></a>资源

WPF 和 Xamarin.Forms 都具有资源和资源字典的概念。 可以将放置到任何对象类型`ResourceDictionary`键然后查找它与`{StaticResource}`的内容，不会更改，或`{DynamicResource}`的内容可以在运行时字典中进行更改。 使用情况和机制是相同，但有一个不同之处：Xamarin.Forms 要求您定义`ResourceDictionary`要分配给`Resources`属性而 WPF 预先创建一个并将其分配为您。

有关示例，请参阅以下定义：

**WPF**

```xaml
<Window.Resources>
   <Color x:Key="redColor">#ff0000</Color>
   ...
</Window.Resources>
```

**Xamarin.Forms**

```xaml
<ContentPage.Resources>
   <ResourceDictionary>
      <Color x:Key="redColor">#ff0000</Color>
      ...
   </ResourceDictionary>
</ContentPage.Resources>
```

如果未定义`ResourceDictionary`，则会生成运行时错误。

## <a name="styles"></a>样式

样式也完全支持在 Xamarin.Forms 中，可以是使用主题组成 UI 的 Xamarin.Forms 元素。 它们支持通过触发器 （属性、 事件和数据） 继承`BasedOn`，和值的资源查找。 样式应用于元素或者显式通过`Style`属性，或隐式不提供资源密钥-就像 WPF 一样。

### <a name="device-styles"></a>设备样式

WPF 具有一组预定义的属性 (如存储为一组静态类上的静态值`SystemColors`) 的规定系统颜色、 字体和值和资源键的窗体中的指标。 Xamarin.Forms 与此类似，但定义了一组[设备样式](~/xamarin-forms/user-interface/styles/device.md)来表示相同的操作。 这些样式是框架所提供的并将设置为根据运行时环境 （例如辅助功能） 的值。

**WPF**

```xaml
<Label Text="Title" Foreground="{DynamicResource {x:Static SystemColors.DesktopBrushKey}}" />
```

**Xamarin.Forms**

```xaml
<Label Text="Title" Style="{DynamicResource TitleStyle}" />
```
