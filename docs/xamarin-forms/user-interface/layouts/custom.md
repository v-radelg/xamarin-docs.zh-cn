---
title: 创建自定义布局
description: 本文说明如何编写一个自定义布局的类，并演示跨页上，水平排列子项，然后将包装对其他行的后续子级的显示方向区分 WrapLayout 类。
ms.prod: xamarin
ms.assetid: B0CFDB59-14E5-49E9-965A-3DCCEDAC2E31
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/29/2017
ms.openlocfilehash: 11707a1e871b0988847ab4a2c266d268db063000
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68645207"
---
# <a name="creating-a-custom-layout"></a>创建自定义布局

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-customlayout-wraplayout)

_Xamarin.Forms 定义了四个布局的类，这些 StackLayout、 AbsoluteLayout、 RelativeLayout 和网格中，并且每个不同的方式排列子项。但是，有时有必要来组织页面内容使用 Xamarin.Forms 不提供布局。本文说明如何编写一个自定义布局的类，并演示跨页上，水平排列子项，然后将包装对其他行的后续子级的显示方向区分 WrapLayout 类。_

## <a name="overview"></a>概述

在 Xamarin.Forms 中，所有布局类都派生自[ `Layout<T>` ](xref:Xamarin.Forms.Layout`1)类和约束的泛型类型[ `View` ](xref:Xamarin.Forms.View)及其派生类型。 依次`Layout<T>`类派生自[ `Layout` ](xref:Xamarin.Forms.Layout)类，该类提供用于定位和大小调整子元素的机制。

每个可视元素负责确定其自身的首选的大小，这被称为*请求*大小。 [`Page`](xref:Xamarin.Forms.Page)[ `Layout` ](xref:Xamarin.Forms.Layout)，和[ `Layout<View>` ](xref:Xamarin.Forms.Layout`1)派生的类型负责确定位置和大小及其子级或子级相对于自身。 因此，布局涉及到父-子关系，其中父确定其子级的大小应该是什么，但将尝试容纳子级的请求的大小。

创建自定义布局需要全面了解 Xamarin.Forms 布局和失效周期。 现在将讨论这些循环。

## <a name="layout"></a>布局

在页上，使用的可视化树的顶部开始布局，并将经历的可视化树，以包含每个可视元素在页面上的所有分支。 其他元素的父元素负责大小和位置相对于本身及其子级。

[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)类定义[ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags))方法，用于测量布局操作的元素和一个[ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle))方法，它指定将在呈现元素的矩形区域。 当应用程序启动并显示的第一页时，*布局循环*包含的第一个`Measure`调用，，然后`Layout`调用，启动上[ `Page` ](xref:Xamarin.Forms.Page)对象：

1. 在布局周期中，每个父元素负责调用`Measure`方法及其子级。
1. 测量子级后，每个父元素负责调用`Layout`方法及其子级。

此周期可确保每个可视元素的页上收到对调用`Measure`和`Layout`方法。 该过程在下图中所示：

![](custom-images/layout-cycle.png "Xamarin.Forms 布局循环")

> [!NOTE]
> 请注意，布局周期是否也可能发生的可视化树的子集上发生更改会影响布局。 这包括添加或从例如，在集合中移除的项[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)，在更改[ `IsVisible` ](xref:Xamarin.Forms.VisualElement.IsVisible)元素或元素的大小的更改的属性。

具有每个 Xamarin.Forms 类`Content`或`Children`属性具有可重写[ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double))方法。 派生的自定义布局类[ `Layout<View>` ](xref:Xamarin.Forms.Layout`1)必须重写此方法，并确保[ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags))并[ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle))方法调用所有元素子级，以提供所需的自定义布局。

此外，每个类的派生[ `Layout` ](xref:Xamarin.Forms.Layout)或[ `Layout<View>` ](xref:Xamarin.Forms.Layout`1)必须重写[ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double))方法，它是在布局类确定它需要通过调用来的大小[ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags))其子级的方法。

> [!NOTE]
> 元素可确定其大小随着*约束*，这指示多少空间可用于该元素的父项中的元素。 约束传递给[ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags))并[ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double))方法可以介于 0 到`Double.PositiveInfinity`。 元素是*约束*，或*完全约束*接收到的调用时，其[ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags))方法使用无限的参数的约束元素为特定大小。 元素是*不受约束*，或*部分约束*接收到的调用时，其`Measure`具有至少一个参数等于方法`Double.PositiveInfinity`– 可以是无限的约束视为，该值指示自动调整大小。

## <a name="invalidation"></a>失效

失效是依据中的元素在页面上的更改将触发新的布局循环过程。 当他们不再具有正确的大小或位置时，元素将被视为无效。 例如，如果[ `FontSize` ](xref:Xamarin.Forms.Button.FontSize)的属性[ `Button` ](xref:Xamarin.Forms.Button)的更改，`Button`被认为是无效，因为它将不再具有正确的大小。 重设大小`Button`然后可能通过页面的其余部分的布局中已更改的波纹效果。

元素使自身通过调用[ `InvalidateMeasure` ](xref:Xamarin.Forms.VisualElement.InvalidateMeasure)方法中，通常该元素的属性更改时，可能会导致元素的新大小。 此方法将触发[ `MeasureInvalidated` ](xref:Xamarin.Forms.VisualElement.MeasureInvalidated)事件，该元素的父级的句柄来触发新的布局周期事件。

[ `Layout` ](xref:Xamarin.Forms.Layout)类设置的处理程序[ `MeasureInvalidated` ](xref:Xamarin.Forms.VisualElement.MeasureInvalidated)事件在每个的子活动添加到其`Content`属性或`Children`集合，并分离该处理程序时删除子。 因此，在具有子级的可视化树中的每个元素是向你发出警报时的一个子项更改大小。 下图说明了如何在可视化树中的元素大小的更改可能会导致 ripple 上一级树的更改：

![](custom-images/invalidation.png "可视化树中的失效")

但是，`Layout`类会尝试限制在页面的布局的孩子的大小的更改的影响。 如果布局，则受约束的大小，然后子大小更改不会影响任何大于可视化树中父布局。 但是，通常布局的大小的更改会影响布局如何排列子项。 因此中的布局大小的任何更改将开始布局，布局循环和布局将接收对调用其[ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double))并[ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double))方法。

[ `Layout` ](xref:Xamarin.Forms.Layout)类还定义[ `InvalidateLayout` ](xref:Xamarin.Forms.Layout.InvalidateLayout)方法具有相似的用途对[ `InvalidateMeasure` ](xref:Xamarin.Forms.VisualElement.InvalidateMeasure)方法。 `InvalidateLayout`时进行的更改会影响布局定位和调整大小及其子级的方式，应调用方法。 例如，`Layout`类调用`InvalidateLayout`方法只要添加或从布局中删除子级。

[ `InvalidateLayout` ](xref:Xamarin.Forms.Layout.InvalidateLayout)可以重写以实现缓存以最大程度减少反复调用[ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags))布局的子级的方法。 重写`InvalidateLayout`方法将提供的子级添加到或从布局中删除时的通知。 同样， [ `OnChildMeasureInvalidated` ](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated)可以重写方法的一个子级的布局大小更改时提供通知。 有关这两种方法重写自定义布局应响应通过清除缓存。 有关详细信息，请参阅[计算和缓存数据](#caching)。

## <a name="creating-a-custom-layout"></a>创建自定义布局

创建自定义布局的过程如下所示：

1. 创建一个从 `Layout<View>` 类派生的类。 有关详细信息，请参阅[创建 WrapLayout](#creating)。
1. [*可选*] 添加属性，由可绑定属性，应在布局类设置任何参数的支持。 有关详细信息，请参阅[添加由可绑定属性的属性支持](#adding_properties)。
1. 重写[ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double))方法来调用[ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags))布局上的布局的所有子级，并返回请求的大小的方法。 有关详细信息，请参阅[重写 OnMeasure 方法](#onmeasure)。
1. 重写[ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double))方法来调用[ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle))对布局的所有子对象的方法。 未能调用[ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle))在布局中的每个子方法会导致永远不会接收正确的大小或位置的子并因此子级将不会变得在页上可见。 有关详细信息，请参阅[重写 LayoutChildren 方法](#layoutchildren)。

  > [!NOTE]
>  在枚举中的子级[ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double))并[ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double))替代，请跳过任何子其[ `IsVisible` ](xref:Xamarin.Forms.VisualElement.IsVisible)属性设置为`false`. 这将确保自定义布局不会保留为不可见子级的空间。

1. [*可选*] 重写[ `InvalidateLayout` ](xref:Xamarin.Forms.Layout.InvalidateLayout)方法添加到或从布局中删除子级时收到通知。 有关详细信息，请参阅[重写 InvalidateLayout 方法](#invalidatelayout)。
1. [*可选*] 重写[ `OnChildMeasureInvalidated` ](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated)方法的一个子级的布局大小更改时收到通知。 有关详细信息，请参阅[重写 OnChildMeasureInvalidated 方法](#onchildmeasureinvalidated)。

> [!NOTE]
> 请注意， [ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double))不会调用重写，如果由其父级，而不是其子控制布局的大小。 如果一个或两个约束是无限的或如果布局类具有非默认但是，将调用重写[ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions)或[ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions)属性值。 出于此原因， [ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double))重写不能依赖于子大小期间获得[ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double))方法调用。 相反，`LayoutChildren`必须调用[ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags))对布局的子对象，然后再调用方法[ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle))方法。 子节点的大小或者，在中获得`OnMeasure`可以缓存重写以避免以后出现`Measure`中的调用`LayoutChildren`重写中，但布局类将需要知道何时需要重新获取大小。 有关详细信息，请参阅[计算和缓存布局数据](#caching)。

然后可以通过将其添加到已使用布局类[ `Page` ](xref:Xamarin.Forms.Page)，通过将子项添加到布局。 有关详细信息，请参阅[消耗 WrapLayout](#consuming)。

<a name="creating" />

### <a name="creating-a-wraplayout"></a>创建 WrapLayout

示例应用程序演示了方向区分`WrapLayout`类跨页上，水平排列子项，然后将包装对其他行的后续子级的显示。

`WrapLayout`的类为每个孩子，名为分配的空间量相同*单元格大小*根据子级的最大大小。 子级不是可与单元中定位单元格大小较小基于其[ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions)并[ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions)属性值。

`WrapLayout`类定义以下的代码示例所示：

```csharp
public class WrapLayout : Layout<View>
{
  Dictionary<Size, LayoutData> layoutDataCache = new Dictionary<Size, LayoutData>();
  ...
}
```

<a name="caching" />

#### <a name="calculating-and-caching-layout-data"></a>计算和缓存布局数据

`LayoutData`结构将有关子对象集合的数据存储在多个属性：

- `VisibleChildCount` – 在布局中可见的子级的个数。
- `CellSize` – 所有子级的布局大小调整的最大大小。
- `Rows` – 的行数。
- `Columns` – 的列数。

`layoutDataCache`字段用于存储多个`LayoutData`值。 当应用程序启动时，两个`LayoutData`对象将被缓存到`layoutDataCache`当前方向 – 一个用于约束参数的字典`OnMeasure`重写方法和一个用于`width`和`height`参数到`LayoutChildren`重写。 到横向方向旋转设备时`OnMeasure`重写并`LayoutChildren`重写将再次调用，这将导致另一个的两个`LayoutData`字典中的缓存的对象。 但是，当返回设备为纵向方向，没有进一步的计算需要，因为`layoutDataCache`已具有所需的数据。

下面的代码示例演示`GetLayoutData`方法，该计算的属性方法`LayoutData`结构化基于特定的大小：

```csharp
LayoutData GetLayoutData(double width, double height)
{
  Size size = new Size(width, height);

  // Check if cached information is available.
  if (layoutDataCache.ContainsKey(size))
  {
    return layoutDataCache[size];
  }

  int visibleChildCount = 0;
  Size maxChildSize = new Size();
  int rows = 0;
  int columns = 0;
  LayoutData layoutData = new LayoutData();

  // Enumerate through all the children.
  foreach (View child in Children)
  {
    // Skip invisible children.
    if (!child.IsVisible)
      continue;

    // Count the visible children.
    visibleChildCount++;

    // Get the child's requested size.
    SizeRequest childSizeRequest = child.Measure(Double.PositiveInfinity, Double.PositiveInfinity);

    // Accumulate the maximum child size.
    maxChildSize.Width = Math.Max(maxChildSize.Width, childSizeRequest.Request.Width);
    maxChildSize.Height = Math.Max(maxChildSize.Height, childSizeRequest.Request.Height);
  }

  if (visibleChildCount != 0)
  {
    // Calculate the number of rows and columns.
    if (Double.IsPositiveInfinity(width))
    {
      columns = visibleChildCount;
      rows = 1;
    }
    else
    {
      columns = (int)((width + ColumnSpacing) / (maxChildSize.Width + ColumnSpacing));
      columns = Math.Max(1, columns);
      rows = (visibleChildCount + columns - 1) / columns;
    }

    // Now maximize the cell size based on the layout size.
    Size cellSize = new Size();

    if (Double.IsPositiveInfinity(width))
      cellSize.Width = maxChildSize.Width;
    else
      cellSize.Width = (width - ColumnSpacing * (columns - 1)) / columns;

    if (Double.IsPositiveInfinity(height))
      cellSize.Height = maxChildSize.Height;
    else
      cellSize.Height = (height - RowSpacing * (rows - 1)) / rows;

    layoutData = new LayoutData(visibleChildCount, cellSize, rows, columns);
  }

  layoutDataCache.Add(size, layoutData);
  return layoutData;
}
```

`GetLayoutData`方法执行以下操作：

- 它确定是否计算`LayoutData`值是否已在缓存并返回它是否可用。
- 否则，它枚举所有子级，调用`Measure`方法上使用无限宽度和高度，每个子级并确定最大子的大小。
- 前提是没有至少一个可见子级，它计算的行和所需，列数，然后计算的维度所基于的子级的单元格大小`WrapLayout`。 请注意，单元格大小通常是稍宽于最大子大小，但是，这也可能是较小如果`WrapLayout`不够宽最宽子或 tall 足够的最高的子级。
- 它将存储新`LayoutData`缓存中的值。

<a name="adding_properties" />

#### <a name="adding-properties-backed-by-bindable-properties"></a>将属性添加受可绑定属性

`WrapLayout`类定义`ColumnSpacing`和`RowSpacing`属性，其值用于分隔的行和列在布局中，支持，其中受可绑定属性。 可绑定属性下面的代码示例所示：

```csharp
public static readonly BindableProperty ColumnSpacingProperty = BindableProperty.Create(
  "ColumnSpacing",
  typeof(double),
  typeof(WrapLayout),
  5.0,
  propertyChanged: (bindable, oldvalue, newvalue) =>
  {
    ((WrapLayout)bindable).InvalidateLayout();
  });

public static readonly BindableProperty RowSpacingProperty = BindableProperty.Create(
  "RowSpacing",
  typeof(double),
  typeof(WrapLayout),
  5.0,
  propertyChanged: (bindable, oldvalue, newvalue) =>
  {
    ((WrapLayout)bindable).InvalidateLayout();
  });
```

每个可绑定属性的属性更改处理程序调用`InvalidateLayout`方法重写以触发新的布局传递`WrapLayout`。 有关详细信息，请参阅[重写 InvalidateLayout 方法](#invalidatelayout)并[重写 OnChildMeasureInvalidated 方法](#onchildmeasureinvalidated)。

<a name="onmeasure" />

#### <a name="overriding-the-onmeasure-method"></a>重写 OnMeasure 方法

`OnMeasure`重写下面的代码示例所示：

```csharp
protected override SizeRequest OnMeasure(double widthConstraint, double heightConstraint)
{
  LayoutData layoutData = GetLayoutData(widthConstraint, heightConstraint);
  if (layoutData.VisibleChildCount == 0)
  {
    return new SizeRequest();
  }

  Size totalSize = new Size(layoutData.CellSize.Width * layoutData.Columns + ColumnSpacing * (layoutData.Columns - 1),
                layoutData.CellSize.Height * layoutData.Rows + RowSpacing * (layoutData.Rows - 1));
  return new SizeRequest(totalSize);
}
```

重写调用`GetLayoutData`方法和构造`SizeRequest`对象从返回的数据，同时还考虑`RowSpacing`和`ColumnSpacing`属性值。 有关详细信息`GetLayoutData`方法，请参阅[计算和缓存数据](#caching)。

> [!IMPORTANT]
> [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags))并[ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double))方法应永远不会通过返回请求无限维度[ `SizeRequest` ](xref:Xamarin.Forms.SizeRequest)属性设置为值`Double.PositiveInfinity`. 但是，在至少一个约束的参数`OnMeasure`可以是`Double.PositiveInfinity`。

<a name="layoutchildren" />

#### <a name="overriding-the-layoutchildren-method"></a>重写 LayoutChildren 方法

`LayoutChildren`重写下面的代码示例所示：

```csharp
protected override void LayoutChildren(double x, double y, double width, double height)
{
  LayoutData layoutData = GetLayoutData(width, height);

  if (layoutData.VisibleChildCount == 0)
  {
    return;
  }

  double xChild = x;
  double yChild = y;
  int row = 0;
  int column = 0;

  foreach (View child in Children)
  {
    if (!child.IsVisible)
    {
      continue;
    }

    LayoutChildIntoBoundingRegion(child, new Rectangle(new Point(xChild, yChild), layoutData.CellSize));
    if (++column == layoutData.Columns)
    {
      column = 0;
      row++;
      xChild = x;
      yChild += RowSpacing + layoutData.CellSize.Height;
    }
    else
    {
      xChild += ColumnSpacing + layoutData.CellSize.Width;
    }
  }
}
```

重写开始通过调用`GetLayoutData`方法，然后枚举所有子节点大小和位置在每个孩子的单元格内。 这通过调用来实现[ `LayoutChildIntoBoundingRegion` ](xref:Xamarin.Forms.Layout.LayoutChildIntoBoundingRegion(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle))方法，用于定位子基于在矩形内的其[ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions)并[ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions)属性值。 这相当于调用的子[ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle))方法。

> [!NOTE]
> 请注意，矩形传递给`LayoutChildIntoBoundingRegion`方法包含子级可以驻留在其中的整个区域。

有关详细信息`GetLayoutData`方法，请参阅[计算和缓存数据](#caching)。

<a name="invalidatelayout" />

#### <a name="overriding-the-invalidatelayout-method"></a>重写 InvalidateLayout 方法

[ `InvalidateLayout` ](xref:Xamarin.Forms.Layout.InvalidateLayout)重写调用时添加或删除从布局中，或一个子级的`WrapLayout`属性更改值，如下面的代码示例中所示：

```csharp
protected override void InvalidateLayout()
{
  base.InvalidateLayout();
  layoutInfoCache.Clear();
}
```

重写会使布局失效并放弃所有的缓存的布局信息。

> [!NOTE]
> 若要停止[ `Layout` ](xref:Xamarin.Forms.Layout)类调用[ `InvalidateLayout` ](xref:Xamarin.Forms.Layout.InvalidateLayout)方法添加或从布局中删除子级时重写[ `ShouldInvalidateOnChildAdded` ](xref:Xamarin.Forms.Layout.ShouldInvalidateOnChildAdded(Xamarin.Forms.View))并[ `ShouldInvalidateOnChildRemoved` ](xref:Xamarin.Forms.Layout.ShouldInvalidateOnChildRemoved(Xamarin.Forms.View))方法，并返回`false`。 添加或删除子级时，布局类然后可以实现一个自定义进程。

<a name="onchildmeasureinvalidated" />

#### <a name="overriding-the-onchildmeasureinvalidated-method"></a>重写 OnChildMeasureInvalidated 方法

[ `OnChildMeasureInvalidated` ](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated)重写调用时的一个布局的子级更改大小，并在下面的代码示例所示：

```csharp
protected override void OnChildMeasureInvalidated()
{
  base.OnChildMeasureInvalidated();
  layoutInfoCache.Clear();
}
```

重写使无效子布局，并放弃所有的缓存的布局信息。

<a name="consuming" />

### <a name="consuming-the-wraplayout"></a>使用 WrapLayout

`WrapLayout`类可以通过将它放置在消耗[ `Page` ](xref:Xamarin.Forms.Page)派生类型，如以下 XAML 代码示例所示：

```xaml
<ContentPage ... xmlns:local="clr-namespace:ImageWrapLayout">
    <ScrollView Margin="0,20,0,20">
        <local:WrapLayout x:Name="wrapLayout" />
    </ScrollView>
</ContentPage>
```

等效的 C# 代码如下所示：

```csharp
public class ImageWrapLayoutPageCS : ContentPage
{
  WrapLayout wrapLayout;

  public ImageWrapLayoutPageCS()
  {
    wrapLayout = new WrapLayout();

    Content = new ScrollView
    {
      Margin = new Thickness(0, 20, 0, 20),
      Content = wrapLayout
    };
  }
  ...
}
```

然后将子项添加到`WrapLayout`所需的方式。 下面的代码示例演示[ `Image` ](xref:Xamarin.Forms.Image)元素添加到`WrapLayout`:

```csharp
protected override async void OnAppearing()
{
    base.OnAppearing();

    var images = await GetImageListAsync();
    if (images != null)
    {
        foreach (var photo in images.Photos)
        {
            var image = new Image
            {
                Source = ImageSource.FromUri(new Uri(photo))
            };
            wrapLayout.Children.Add(image);
        }
    }
}

async Task<ImageList> GetImageListAsync()
{
    try
    {
        string requestUri = "https://raw.githubusercontent.com/xamarin/docs-archive/master/Images/stock/small/stock.json";
        string result = await _client.GetStringAsync(requestUri);
        return JsonConvert.DeserializeObject<ImageList>(result);
    }
    catch (Exception ex)
    {
        Debug.WriteLine($"\tERROR: {ex.Message}");
    }

    return null;
}
```

当页面包含`WrapLayout`出现，示例应用程序以异步方式访问包含一系列照片的远程 JSON 文件，创建[ `Image` ](xref:Xamarin.Forms.Image)元素为每个照片，并将其添加到`WrapLayout`. 这会导致下面的屏幕截图中所示的外观：

![](custom-images/portait-screenshots.png "示例应用程序纵向屏幕快照")

下面的屏幕截图演示`WrapLayout`已旋转为横向后：

![](custom-images/landscape-ios.png "示例 iOS 应用程序横向屏幕截图")
![](custom-images/landscape-android.png "示例 Android 应用程序横向屏幕快照")
![](custom-images/landscape-uwp.png "示例 UWP 应用程序横向屏幕快照")

每个行中的列数取决于照片大小、 屏幕宽度和每个与设备无关单位的像素数。 [ `Image` ](xref:Xamarin.Forms.Image)元素以异步方式加载照片，并因此`WrapLayout`类将接收到频繁调用其[ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double))方法作为每个`Image`在元素收到基于加载照片的新大小。

## <a name="related-links"></a>相关链接

- [WrapLayout （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-customlayout-wraplayout)
- [自定义布局](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter26.md)
- [在 Xamarin.Forms 中创建自定义布局 （视频）](https://evolve.xamarin.com/session/56e20f83bad314273ca4d81c)
- [布局<T>](xref:Xamarin.Forms.Layout`1)
- [布局](xref:Xamarin.Forms.Layout)
- [VisualElement](xref:Xamarin.Forms.VisualElement)
