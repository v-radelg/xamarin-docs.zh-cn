---
ms.assetid: 7C132A7C-4973-4B2D-98DC-3661C08EA33F
title: WPF 与 Xamarin 应用程序生命周期
description: 本文档比较 Xamarin 和 WPF 应用程序的应用程序生命周期的相似性和差异。 它还查看可视化树、图形、资源和样式。
author: davidortinau
ms.author: daortin
ms.date: 04/26/2017
ms.openlocfilehash: 725af8aebb111ac620a7d1ca5eebf73f31ee4a5e
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016476"
---
# <a name="wpf-vs-xamarinforms-app-lifecycle"></a>WPF 与 Xamarin 应用程序生命周期

Xamarin 从其前面的基于 XAML 的框架中获取了很多设计指南，尤其是 WPF。 但在其他方面，它会明显偏离，这可能是尝试迁移的用户的一个粘滞点。 本文档将尝试识别其中一些问题，并提供指导以将 WPF 知识桥接到 Xamarin。

## <a name="app-lifecycle"></a>应用生命周期

WPF 和 Xamarin 之间的应用程序生命周期类似。 在外部（平台）代码中启动并通过方法调用启动 UI。 不同之处在于 Xamarin。 Forms 始终以特定于平台的程序集开头，该程序集将初始化和创建应用程序的 UI。

**WPF**

- `Main method > App > MainWindow`

> [!NOTE]
> 默认情况下，`Main` 方法是自动生成的，在代码中不可见。

**Xamarin.Forms**

- **iOS** &ndash; `Main method > AppDelegate > App > ContentPage`
- **Android** &ndash; `MainActivity > App > ContentPage`
- **UWP** &ndash; `Main method > App(UWP) > MainPage(UWP) > App > ContentPage`

### <a name="application-class"></a>应用程序类

WPF 和 Xamarin 都具有作为单一实例创建的 `Application` 类。 在大多数情况下，应用将从此类派生以提供自定义应用程序，但在 WPF 中并不是绝对必需的。 这两者都公开了一个 `Application.Current` 属性来查找创建的单一实例。

### <a name="global-properties--persistence"></a>全局属性 + 持久性

WPF 和 Xamarin 都具有 `Application.Properties` 字典，你可以在其中存储可在应用程序的任何位置访问的全局应用程序级对象。 主要差别在于，在应用挂起时，Forms 将_保留_存储在集合中的任何基元类型，并在变时重新加载它们。 WPF 不会自动支持这种行为，而是大多数开发人员依赖于独立存储或利用内置 `Settings` 支持。

## <a name="defining-pages-and-the-visual-tree"></a>定义页和可视化树

WPF 使用 `Window` 作为任何顶级可视元素的根元素。 这会在 Windows 世界中定义一个 HWND 来显示信息。 您可以在 WPF 中同时创建和显示任意数量的窗口。

在 Xamarin 中，顶级视觉对象始终由平台定义-例如，在 iOS 上，它是 `UIWindow`。 Xamarin 使用 `Page` 类将其内容呈现到这些本机平台表示形式中。 Xamarin 中的每个 `Page` 都表示应用程序中唯一的 "页"，其中一次只显示一个。

WPFs `Window` 和 Xamarin. Forms `Page` 都包含一个 `Title` 属性来影响显示的标题，并且两者都具有 `Icon` 属性以显示页面的特定图标（**请注意**，title 和 Icon 在 Xamarin 中并不总是可见。). 此外，您还可以更改这两种属性，如背景色或图像。

从技术上讲，可以呈现到两个不同的平台视图（例如，定义两个 `UIWindow` 对象并将第二个对象呈现为外部显示或 AirPlay），它需要特定于平台的代码来执行此操作，并且不是的直接支持的功能Xamarin。窗体本身。

### <a name="views"></a>Views

这两个框架的视觉层次结构类似。 WPF 更深入，因为它对 WYSIWYG 文档的支持。

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

Xamarin 的主要目的是围绕移动方案。 因此，当用户与应用程序交互时，应用程序会被_激活_、_挂起_和_重新激活_。 这类似于在 WPF 应用程序中单击 "`Window`"，并且有一组方法和相应的事件可以重写或挂钩来监视此行为。

| 目标 | WPF 方法 | Xamarin. Forms 方法 |
|--- |--- |--- |
|初始激活|ctor + OnLoaded|ctor + 页 OnStart|
|显示|IsVisibleChanged|页面。显示|
|Hidden|IsVisibleChanged|页面。消失|
|暂停/丢失焦点|OnDeactivated|OnSleep|
|已激活/获得焦点|OnActivated|OnResume|
|Closed|OnClosing + OnClosed|不可用|

两者都支持隐藏/显示子控件，在 WPF 中，它是三状态属性 `IsVisible` （可见、隐藏和折叠）。 在 Xamarin 中，它只是通过 "`IsVisible`" 属性显示或隐藏。

### <a name="layout"></a>布局

页面布局发生在 WPF 中发生的同一2遍处理（测量/排列）中。 可以通过在 Xamarin. Forms `Page` 类中重写以下方法来挂钩到页面布局：

| 方法 | 目标 |
|--- |--- |
|OnChildMeasureInvalidated|子元素的首选大小已更改。|
|OnSizeAllocated|已为页面分配宽度/高度。|
|LayoutChanged 事件|页面的布局/大小已更改。|

目前没有调用的全局布局事件，也没有在 WPF 中找到的全局 `CompositionTarget.Rendering` 事件。

#### <a name="common-layout-properties"></a>公共布局属性

WPF 和 Xamarin 都支持 `Margin` 来控制元素周围的间距，并 `Padding` 控制元素_内_的间距。 此外，大多数 Xamarin. Forms 布局视图都有属性来控制间距（例如行或列）。

此外，大多数元素都有属性来影响它们在父容器中的定位方式：

| WPF | Xamarin.Forms | 目标 |
|--- |--- |--- |
|HorizontalAlignment|HorizontalOptions|左/居中/右/拉伸选项|
|VerticalAlignment|VerticalOptions|顶部/中心/底部/拉伸选项|

> [!NOTE]
> 这些属性的实际解释依赖于父容器。

#### <a name="layout-views"></a>布局视图

WPF 和 Xamarin 均使用布局控件来定位子元素。 在大多数情况下，这些功能在功能方面非常接近。

| WPF | Xamarin.Forms | 布局样式 |
|--- |--- |--- |
|StackPanel|StackLayout|从左到右或从上到下的无限堆栈|
|Grid|Grid|表格格式（行和列）|
|DockPanel|不可用|停靠到窗口边缘|
|Canvas|AbsoluteLayout|像素/坐标定位|
|WrapPanel|不可用|包装堆栈|
|不可用|RelativeLayout|相对规则的定位|

> [!NOTE]
> Xamarin. Forms 不支持 `GridSplitter`。

这两个平台都使用_附加属性_来微调子级。

### <a name="rendering"></a>“呈现”

WPF 和 Xamarin 的呈现机制截然不同。 在 WPF 中，你创建的控件直接将内容呈现给屏幕上的像素。 WPF 维护两个对象图（_树_）来表示这一点-_逻辑树_表示代码或 XAML 中定义的控件，而_可视化树_表示在屏幕上发生的实际呈现直接由视觉对象（通过虚拟绘图方法）或通过 XAML 定义 `ControlTemplate` 来替换或自定义。 通常，可视化树更复杂，因为它包含控件周围的边框、隐式内容的标签等内容。WPF 包含一组 Api （`LogicalTreeHelper` 和 `VisualTreeHelper`）以检查这两个对象图。

在 Xamarin 中，在 `Page` 中定义的控件实际上只是简单的数据对象。 它们类似于逻辑树表示形式，但绝不会自行呈现内容。 相反，它们是影响元素呈现的_数据模型_。 实际渲染是通过一[组单独的_可视化渲染_器来完成的，这些渲染器会映射到每个控件类型](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)。 这些呈现器通过特定于平台的 Xamarin 程序集在每个特定于平台的项目中进行注册。 可在[此处](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)查看列表。 除了替换或扩展呈现器外，Xamarin 还支持可用于影响基于每个平台的本机呈现的[效果](~/xamarin-forms/app-fundamentals/effects/index.md)。

#### <a name="the-logicalvisual-tree"></a>逻辑/可视化树

没有公开的 API 来遍历 Xamarin 中的逻辑树。窗体-但可以使用反射来获取相同的信息。 例如，[下面是一个可使用反射来枚举逻辑子级的方法](https://github.com/xamarinhq/xamu-infrastructure/blob/master/src/XamU.Infrastructure/Extensions/ElementExtensions.cs#L108)。

## <a name="graphics"></a>图形

Xamarin 不包括简单矩形（`BoxView`）以外的基元的图形系统。 可以包括第三方库（如[SkiaSharp](~/graphics-games/skiasharp/index.md) ）以获取跨平台2d 绘图，或[UrhoSharp](~/graphics-games/urhosharp/index.md) for 3d。

## <a name="resources"></a>资源

WPF 和 Xamarin 都具有资源和资源字典的概念。 你可以将任何对象类型放入具有密钥的 `ResourceDictionary` 中，然后使用 `{StaticResource}` 查找不会更改的对象，或者在运行时字典中可更改的项的 `{DynamicResource}`。 用法和机制是相同的，区别在于： Xamarin。 Forms 要求您定义分配给 `Resources` 属性的 `ResourceDictionary`，而 WPF 预先创建一个属性并为您分配它。

例如，请参阅下面的定义：

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

如果未定义 `ResourceDictionary`，则会生成运行时错误。

## <a name="styles"></a>样式

Xamarin 中也完全支持样式，并可用于为组成 UI 的 Xamarin. Forms 元素设置主题。 它们支持触发器（属性、事件和数据）、通过 `BasedOn`的继承和值的资源查找。 样式通过 `Style` 属性显式应用于元素，或通过不提供资源键（就像 WPF 一样）隐式应用。

### <a name="device-styles"></a>设备样式

WPF 具有一组预定义的属性（以静态值的形式存储在一组静态类（如 `SystemColors`）上，这些属性以值和资源键的形式指示系统颜色、字体和指标。 Xamarin 类似，但定义一组[设备样式](~/xamarin-forms/user-interface/styles/device.md)来表示相同的内容。 这些样式由框架提供，并根据运行时环境（如辅助功能）设置为值。

**WPF**

```xaml
<Label Text="Title" Foreground="{DynamicResource {x:Static SystemColors.DesktopBrushKey}}" />
```

**Xamarin.Forms**

```xaml
<Label Text="Title" Style="{DynamicResource TitleStyle}" />
```
