---
title: ListView 性能
description: 尽管 ListView 是功能强大的视图，以显示数据，但它具有一些限制。 本文介绍如何确保与应用程序中的 Xamarin.Forms ListView 优异的性能。
ms.prod: xamarin
ms.assetid: 1B085639-652C-4862-86EB-5D55D32B9395
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2017
ms.openlocfilehash: 4a0a7a4db4b0ca982a162ec3a0b67dc729af0ed2
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655939"
---
# <a name="listview-performance"></a>ListView 性能

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)

如果编写移动应用程序，性能很重要。 用户都希望能平滑滚动并快速加载。 未能满足用户的期望将您的应用程序商店中的评级或成本对于业务线应用程序，成本的组织的时间和金钱。

尽管[ `ListView` ](xref:Xamarin.Forms.ListView)是一个功能强大的视图用于显示数据，它具有一些限制。 使用自定义单元格，尤其是当它们包含深度嵌套的视图层次结构或使用需要大量度量的特定布局时，滚动性能会降低。 幸运的是，有可用于避免性能不佳的技术。

<a name="cachingstrategy" />

## <a name="caching-strategy"></a>缓存策略

Listview 通常用于显示更多的数据不是可以在屏幕上的合适大小。 例如，考虑一个音乐应用程序。 歌曲的库可能会有成千上万个条目。 最简单的方法，就是创建每首歌曲的行，会使性能变差。 该方法会浪费宝贵的内存，并可能会降低滚动到爬网。 另一种方法是创建和销毁行，如将数据滚动到视图。 这需要常量实例化并清除视图对象，可能会很慢。

若要节省内存，本机[ `ListView` ](xref:Xamarin.Forms.ListView)为每个平台的等效项具有内置功能来重新使用的行。 仅在屏幕上可见的单元加载到内存中并**内容**加载到现有的单元格。 这可以防止无需实例化的对象，从而节省时间和内存的数千个应用程序。

Xamarin.Forms 允许[ `ListView` ](xref:Xamarin.Forms.ListView)通过重复使用单元格[ `ListViewCachingStrategy` ](xref:Xamarin.Forms.ListViewCachingStrategy)枚举，它具有以下值：

```csharp
public enum ListViewCachingStrategy
{
  RetainElement,   // the default value
  RecycleElement,
  RecycleElementAndDataTemplate
}
```

> [!NOTE]
> 通用 Windows 平台 (UWP) 将忽略[ `RetainElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement)缓存策略，因为它始终使用缓存来提高性能。 因此，默认情况下其行为如同[ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement)应用缓存策略。

### <a name="retainelement"></a>RetainElement

[ `RetainElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement)缓存策略指定[ `ListView` ](xref:Xamarin.Forms.ListView)将在列表中，生成的每个项的单元格，并且是默认`ListView`行为。 它通常应在以下情况下使用：

- 每个单元格时有大量的绑定 (20-30 +)。
- 当单元格模板频繁更改。
- 当测试中发现的`RecycleElement`缓存策略导致降低的执行速度。

务必要识别所带来的后果[ `RetainElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement)缓存策略时使用的自定义单元格。 任何单元格初始化代码将需要为每个单元格创建过程中，运行的可为每秒多次。 在这种情况下，在页上，没有布局技术，如使用多个嵌套[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)实例时安装程序会成为性能瓶颈和销毁在真实时间中，当用户滚动。

<a name="recycleelement" />

### <a name="recycleelement"></a>RecycleElement

[ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement)缓存策略指定[ `ListView` ](xref:Xamarin.Forms.ListView)将尝试通过回收列表单元格，最小化其内存占用量和执行速度。 此模式下不会始终提供性能改进，并应执行测试以确定任何改进。 但是，它通常是理想之选，并应在以下情况下使用：

- 当每个单元格有一个小到中等数量的绑定。
- 当每个单元格[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)定义所有的单元格数据。
- 每个单元格时很大程度上类似，与单元格模板不变。

在虚拟化期间，该单元格都有更新，其绑定上下文，因此如果应用程序使用此模式下则必须确保适当地处理绑定上下文更新。 有关单元格的所有数据必须都来自绑定上下文或可能会出现一致性错误。 这可以通过使用数据绑定显示单元格数据。 或者，应将单元格数据设置`OnBindingContextChanged`重写，而不是在自定义单元格的构造函数，如下面的代码示例中所示：

```csharp
public class CustomCell : ViewCell
{
  Image image = null;

  public CustomCell ()
  {
    image = new Image();
    View = image;
  }

  protected override void OnBindingContextChanged ()
  {
    base.OnBindingContextChanged ();

    var item = BindingContext as ImageItem;
    if (item != null) {
      image.Source = item.ImageUrl;
    }
  }
}
```

有关详细信息，请参阅[绑定上下文更改](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#binding-context-changes)。

在 iOS 和 Android 上，如果单元格使用自定义呈现器，它们必须确保正确实现属性更改通知。 重用单元格时其属性值将更改时的绑定上下文将与更新为的一个可用的单元格，`PropertyChanged`引发的事件的。 有关详细信息，请参阅[自定义 ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)。

#### <a name="recycleelement-with-a-datatemplateselector"></a>使用 DataTemplateSelector RecycleElement

当[ `ListView` ](xref:Xamarin.Forms.ListView)使用[ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector)选择[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)，则[ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement)缓存策略不会缓存`DataTemplate`s。 相反，`DataTemplate`为每个项列表中的数据选择。

> [!NOTE]
> [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement)缓存策略具有必备条件，在 Xamarin.Forms 2.4 中引入的当[ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector)需要选择[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)每个`DataTemplate`必须返回相同[ `ViewCell` ](xref:Xamarin.Forms.ViewCell)类型。 例如，给定[ `ListView` ](xref:Xamarin.Forms.ListView)与`DataTemplateSelector`，可以返回`MyDataTemplateA`(其中`MyDataTemplateA`返回`ViewCell`类型的`MyViewCellA`)，或`MyDataTemplateB`(其中`MyDataTemplateB`将返回`ViewCell`类型的`MyViewCellB`)，当`MyDataTemplateA`将返回它必须返回`MyViewCellA`，否则将引发异常。

### <a name="recycleelementanddatatemplate"></a>RecycleElementAndDataTemplate

[ `RecycleElementAndDataTemplate` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate)缓存策略是基于[ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement)此外确保，当缓存策略[ `ListView` ](xref:Xamarin.Forms.ListView)使用[`DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector)以选择[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)， `DataTemplate`s 所缓存的列表中的项的类型。 因此， `DataTemplate`s 每个项类型，而不是每个项实例一次选择一次。

> [!NOTE]
> [ `RecycleElementAndDataTemplate` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate)缓存策略具有必备组件的`DataTemplate`s 返回[ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector)必须使用[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Type))构造函数采用`Type`。

### <a name="setting-the-caching-strategy"></a>设置缓存策略

[ `ListViewCachingStrategy` ](xref:Xamarin.Forms.ListViewCachingStrategy)枚举值指定具有[ `ListView` ](xref:Xamarin.Forms.ListView)构造函数重载，如下面的代码示例中所示：

```csharp
var listView = new ListView(ListViewCachingStrategy.RecycleElement);
```

在 XAML 中，设置`CachingStrategy`特性，如下面的代码中所示：

```xaml
<ListView CachingStrategy="RecycleElement">
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
              ...
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

这具有相同的效果与设置的缓存策略参数的构造函数中C#;请注意，没有任何`CachingStrategy`属性上的`ListView`。

#### <a name="setting-the-caching-strategy-in-a-subclassed-listview"></a>子类化 ListView 中设置缓存策略

设置`CachingStrategy`子类化，从 XAML 的特性[ `ListView` ](xref:Xamarin.Forms.ListView)不会生成所需的行为，因为没有任何`CachingStrategy`属性`ListView`。 此外, 如果启用了[XAMLC](~/xamarin-forms/xaml/xamlc.md) , 则将生成以下错误消息:**找不到属性、可绑定的属性或 "CachingStrategy" 的事件**

此问题的解决方案是指定构造函数上子类[ `ListView` ](xref:Xamarin.Forms.ListView)接受[ `ListViewCachingStrategy` ](xref:Xamarin.Forms.ListViewCachingStrategy)参数并将其传递到基类：

```csharp
public class CustomListView : ListView
{
  public CustomListView (ListViewCachingStrategy strategy) : base (strategy)
  {
  }
  ...
}
```

然后[ `ListViewCachingStrategy` ](xref:Xamarin.Forms.ListViewCachingStrategy)枚举值可以通过使用指定从 XAML`x:Arguments`语法：

```xaml
<local:CustomListView>
  <x:Arguments>
    <ListViewCachingStrategy>RecycleElement</ListViewCachingStrategy>
  </x:Arguments>
</local:CustomListView>
```

<a name="improving-performance" />

## <a name="improving-listview-performance"></a>提高 ListView 性能

有许多方法来提高性能的`ListView`:

-  将绑定`ItemsSource`属性设置为`IList<T>`而不是集合`IEnumerable<T>`集合，因为`IEnumerable<T>`集合不支持随机访问。
-  使用内置的单元格 (如`TextCell`  /  `SwitchCell` ) 而不是`ViewCell`每当可以。
-  使用更少元素。 例如，请考虑使用单个`FormattedString`而不是多个标签的标签。
-  替换`ListView`与`TableView`显示非同构的数据-即，不同类型的数据时。
-  限制的使用[ `Cell.ForceUpdateSize` ](xref:Xamarin.Forms.Cell.ForceUpdateSize)方法。 如果过度使用，它会降低性能。
-  在 Android 上，避免设置`ListView`的行分隔符可见性或颜色后它已经实例化，因为它会导致对较大性能产生负面影响。
-  避免更改基于单元格布局[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)。 这会导致大型的布局和初始化成本。
-  避免将深度嵌套的布局层次结构。 使用`AbsoluteLayout`或`Grid`以帮助减少嵌套。
-  避免特定`LayoutOptions`而不`Fill`（填充是便宜计算）。
-  避免将置于`ListView`内`ScrollView`原因如下：
    - `ListView`实现其自己的滚动。
    - `ListView`不会收到任何手势，因为父对象将处理`ScrollView`。
    - `ListView`可以提供自定义的页眉和页脚滚动的列表中，有可能，提供的功能元素`ScrollView`所用的。 有关详细信息请参阅[页眉和页脚](~/xamarin-forms/user-interface/listview/customizing-list-appearance.md#Headers_and_Footers)。
-  如果您需要在单元中提供一个非常具体的复杂设计，请考虑自定义呈现器。

`AbsoluteLayout` 有可能执行而无需单个度量值调用的布局。 这样就非常强大的性能。 如果`AbsoluteLayout`不能使用，请考虑[ `RelativeLayout` ](xref:Xamarin.Forms.RelativeLayout)。 如果使用`RelativeLayout`，直接传递约束将远远快于使用 API 的表达式。 这是因为表达式 API 使用 JIT，并在 iOS 上的树包含解释，这是速度较慢。 表达式 API 是适用于页面布局，只是要求初始布局和旋转，但在`ListView`，其中在滚动，过程不断地运行会损害性能。

构建自定义呈现器[ `ListView` ](xref:Xamarin.Forms.ListView)或其单元格是一种方法减少的布局计算上滚动性能的影响。 有关详细信息，请参阅[自定义 ListView](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md)并[自定义 ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)。


## <a name="related-links"></a>相关链接

- [自定义呈现器视图 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)
- [自定义呈现器 ViewCell （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-viewcell)
- [ListViewCachingStrategy](xref:Xamarin.Forms.ListViewCachingStrategy)
