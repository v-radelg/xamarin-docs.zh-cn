---
title: ListView 性能
description: 尽管 ListView 是功能强大的视图，以显示数据，但它具有一些限制。 本文介绍如何确保与应用程序中的 Xamarin.Forms ListView 优异的性能。
ms.prod: xamarin
ms.assetid: 1B085639-652C-4862-86EB-5D55D32B9395
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2017
ms.openlocfilehash: ed920db129d2af203046a648c0069580f99a295e
ms.sourcegitcommit: a5ef4497db04dfa016865bc7454b3de6ff088554
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2019
ms.locfileid: "70998184"
---
# <a name="listview-performance"></a>ListView 性能

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)

如果编写移动应用程序，性能很重要。 用户都希望能平滑滚动并快速加载。 未能满足用户的期望将您的应用程序商店中的评级或成本对于业务线应用程序，成本的组织的时间和金钱。

Xamarin [`ListView`](xref:Xamarin.Forms.ListView)是用于显示数据的功能强大的视图，但有一些限制。 使用自定义单元时，尤其是当它们包含深层嵌套的视图层次结构或使用某些需要复杂度量的布局时，滚动性能可能会受到影响。 幸运的是，有可用于避免性能不佳的技术。

## <a name="caching-strategy"></a>缓存策略

Listview 通常用于显示比屏幕容纳更多的数据。 例如，音乐应用可能有一个歌曲库，其中包含上千个条目。 为每个条目创建一项会浪费宝贵的内存，并且性能会下降。 经常创建和销毁行需要应用程序不断地实例化和清理对象，这也会导致性能下降。

为了节省内存，每个[`ListView`](xref:Xamarin.Forms.ListView)平台的本机等效项具有用于重用行的内置功能。 仅在屏幕上可见的单元加载到内存中并**内容**加载到现有的单元格。 此模式可防止应用程序实例化数千个对象，从而节省了时间和内存。

Xamarin 允许[`ListView`](xref:Xamarin.Forms.ListView) [`ListViewCachingStrategy`](xref:Xamarin.Forms.ListViewCachingStrategy)通过枚举实现单元格重用，此枚举具有以下值：

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

[ `RetainElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement)缓存策略指定[ `ListView` ](xref:Xamarin.Forms.ListView)将在列表中，生成的每个项的单元格，并且是默认`ListView`行为。 应在以下情况下使用它：

- 每个单元格有大量绑定（20-30 +）。
- 单元模板会频繁更改。
- 测试表明`RecycleElement`缓存策略会导致执行速度降低。

务必要识别所带来的后果[ `RetainElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement)缓存策略时使用的自定义单元格。 任何单元格初始化代码将需要为每个单元格创建过程中，运行的可为每秒多次。 在这种情况下，在页面上正常运行的布局技术（如使用[`StackLayout`](xref:Xamarin.Forms.StackLayout)多个嵌套实例）在用户滚动时被实时设置并销毁时会变得性能瓶颈。

### <a name="recycleelement"></a>RecycleElement

[ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement)缓存策略指定[ `ListView` ](xref:Xamarin.Forms.ListView)将尝试通过回收列表单元格，最小化其内存占用量和执行速度。 此模式并非始终能改善性能，并应执行测试来确定是否有任何改进。 但这是首选选项，应在以下情况下使用：

- 每个单元格的绑定数小到中等。
- 每个单元[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)格都定义了所有单元数据。
- 每个单元格在很大程度上相似，单元模板不变。

在虚拟化期间，该单元格都有更新，其绑定上下文，因此如果应用程序使用此模式下则必须确保适当地处理绑定上下文更新。 有关单元格的所有数据必须都来自绑定上下文或可能会出现一致性错误。 可以通过使用数据绑定显示单元数据来避免此问题。 或者，应将单元格数据设置`OnBindingContextChanged`重写，而不是在自定义单元格的构造函数，如下面的代码示例中所示：

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

### <a name="set-the-caching-strategy"></a>设置缓存策略

[ `ListViewCachingStrategy` ](xref:Xamarin.Forms.ListViewCachingStrategy)枚举值指定具有[ `ListView` ](xref:Xamarin.Forms.ListView)构造函数重载，如下面的代码示例中所示：

```csharp
var listView = new ListView(ListViewCachingStrategy.RecycleElement);
```

在 xaml 中，按`CachingStrategy`下面的 xaml 中所示设置属性：

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

此方法与在C#的构造函数中设置缓存策略参数的效果相同。

#### <a name="set-the-caching-strategy-in-a-subclassed-listview"></a>在子类 ListView 中设置缓存策略

由于上[`ListView`](xref:Xamarin.Forms.ListView) 没有属性`CachingStrategy` ，因此在一个子类上设置 XAML 的属性将不会生成所需的行为。`CachingStrategy` `ListView` 此外，如果启用了[XAMLC](~/xamarin-forms/xaml/xamlc.md) ，则将生成以下错误消息：**找不到属性、可绑定的属性或 "CachingStrategy" 的事件**

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

## <a name="listview-performance-suggestions"></a>ListView 性能建议

可通过`ListView`许多方法来提高性能。 以下建议可以提高 ListView 的性能

- 将绑定`ItemsSource`属性设置为`IList<T>`而不是集合`IEnumerable<T>`集合，因为`IEnumerable<T>`集合不支持随机访问。
- 使用`TextCell`内置单元（如 /  `SwitchCell` ），而不是`ViewCell`每次都可以。
- 使用更少元素。 例如，请考虑使用单个`FormattedString`标签而不是多个标签。
- 替换`ListView`与`TableView`显示非同构的数据-即，不同类型的数据时。
- 限制的使用[ `Cell.ForceUpdateSize` ](xref:Xamarin.Forms.Cell.ForceUpdateSize)方法。 如果过度使用，它会降低性能。
- 在 Android 上，避免设置`ListView`的行分隔符可见性或颜色后它已经实例化，因为它会导致对较大性能产生负面影响。
- 避免更改基于单元格布局[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)。 更改布局会产生大量的度量和初始化成本。
- 避免将深度嵌套的布局层次结构。 使用`AbsoluteLayout`或`Grid`以帮助减少嵌套。
- 避免特定`LayoutOptions`于`Fill` （`Fill`计算成本最便宜）。
- 避免将置于`ListView`内`ScrollView`原因如下：
  - `ListView`实现其自己的滚动。
  - `ListView`不会收到任何手势，因为父对象将处理`ScrollView`。
  - `ListView`可以提供自定义的页眉和页脚滚动的列表中，有可能，提供的功能元素`ScrollView`所用的。 有关详细信息，请参阅[页眉和页脚](~/xamarin-forms/user-interface/listview/customizing-list-appearance.md#headers-and-footers)。
- 如果你需要在单元格中提供特定的复杂设计，则请考虑使用自定义呈现器。

`AbsoluteLayout`无需执行单个度量值即可执行布局，使其具有高性能。 如果`AbsoluteLayout`不能使用，请考虑[ `RelativeLayout` ](xref:Xamarin.Forms.RelativeLayout)。 如果使用`RelativeLayout`，直接传递约束将远远快于使用 API 的表达式。 此方法的速度更快，因为表达式 API 使用 JIT，而在 iOS 上必须解释此树，这会降低。 表达式 API 是适用于页面布局，只是要求初始布局和旋转，但在`ListView`，其中在滚动，过程不断地运行会损害性能。

构建自定义呈现器[ `ListView` ](xref:Xamarin.Forms.ListView)或其单元格是一种方法减少的布局计算上滚动性能的影响。 有关详细信息，请参阅[自定义 ListView](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md)并[自定义 ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md)。

## <a name="related-links"></a>相关链接

- [自定义呈现器视图 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)
- [自定义呈现器 ViewCell （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-viewcell)
- [ListViewCachingStrategy](xref:Xamarin.Forms.ListViewCachingStrategy)
