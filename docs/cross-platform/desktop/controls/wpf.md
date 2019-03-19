---
ms.assetid: 1BB412D1-FC3D-4E69-8B01-B976A3DB6328
title: WPF 与。Xamarin.Forms:相似性和差异
description: 本文档进行比较和对比 WPF 到 Xamarin.Forms。 它讨论控件模板、 XAML、 绑定基础结构、 数据模板、 ItemsControl、 用户控件、 导航和 URL 导航。
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: 990253cbd31ad79bc47f086dc5bd2b99233f2032
ms.sourcegitcommit: 64d6da88bb6ba222ab2decd2fdc8e95d377438a6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58175234"
---
# <a name="wpf-vs-xamarinforms-similarities--differences"></a>WPF 与。Xamarin.Forms:相似性和差异

## <a name="control-templates"></a>控件模板

WPF 支持的概念*控件模板*提供控件的可视化效果说明 (`Button`， `ListBox`，等等。)。 如上所述，Xamarin.Forms 使用具体_呈现_与本机平台 （iOS、 Android 等） 来可视化控件进行交互的类。

但是，Xamarin.Forms _does_有`ControlTemplate`类型-用于主题`Page`对象。 它提供的定义`Page`，提供了一致的内容，但页的用户可以更改颜色、 字体、 等，甚至添加元素，以使应用程序唯一。

此常见用法的一些事项，如身份验证对话框，提示，并提供可自定义应用中标准化、 但可主题化页面外观和感觉。 作为此支持的一部分，使用许多熟悉的 WPF 命名控件：

1. `ContentPage`
2. `ContentView`
3. `ContentPresenter`
4. `TemplateBinding`

但务必要知道它们是_不_在 Xamarin.Forms 中提供相同的目的。 有关此功能的详细信息，请参阅[文档页](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md)。

## <a name="xaml"></a>XAML

XAML 用作 WPF 和 Xamarin.Forms 的声明性标记语言。 大多数情况下的语法完全相同-的主要区别是 XAML 图形定义/创建的对象。

- Xamarin.Forms 支持[XAML 2009 规范](/dotnet/framework/xaml-services/xaml-2009-language-features/); 这使得更轻松地定义数据，如`string`s， `int`s、 等等也为定义的泛型类型和向构造函数传递参数。

- 当前没有方法动态加载 XAML，如 WPF 可以与`XamlReader`。 就可以使用相同的基本功能[NuGet 包](https://www.nuget.org/packages/Xamarin.Forms.Dynamic/)尽管。

### <a name="markup-extensions"></a>标记扩展

Xamarin.Forms 支持 XAML 扩展通过标记扩展，与 WPF 非常相似。 默认情况下，它具有相同的基本构建基块：

1. `{x:Array}`
2. `{Binding}`
3. `{DynamicResource}`
4. `{x:Null}`
5. `{x:Static}`
6. `{StaticResource}`
7. `{x:Type}`

此外，它包含`{x:Reference}`从 XAML 2009 规范，和一个`{TemplateBinding}`标记扩展的专用版本用于`ControlTemplate`Xamarin.Forms 支持。

> [!WARNING]
> `ControlTemplate`支持并不相同的即使它具有相同的名称。

Xamarin.Forms 支持-自定义标记扩展，但实现起来略有不同。 在 WPF 中，您必须派生自`MarkupExtension`的抽象基类的类。 在 Xamarin.Forms 中，它将被替换为一个接口`IMarkupExtension`或`IMarkupExtension<T>`即更灵活。

就像 WPF 中，单个所需的方法是`ProvideValue`方法以从标记扩展返回的值。

## <a name="binding-infrastructure"></a>绑定基础结构

结转的核心概念之一是数据绑定基础结构，以连接到.NET 数据属性的视觉对象属性。 这样，如 MVVM 体系结构模式。 基本设计等同-您有一个可绑定的基本类[BindableObject](xref:Xamarin.Forms.BindableObject)，这是的 WPF 中[DependencyObject](xref:System.Windows.DependencyObject)类。 此基类用作将参与作为目标数据绑定中的所有对象的根的上级。 然后，派生的类公开[BindableProperty](xref:Xamarin.Forms.BindableProperty)对象充当属性值的后备存储 (它们定义为[DependencyProperty](xref:System.Windows.DependencyProperty) WPF 中的对象)。

### <a name="defining-bindable-properties"></a>定义可绑定属性

在 Xamarin.Forms 中可绑定属性的定义是 WPF 相同：
1. 对象必须派生自`BindableObject`。
2. 必须有类型的公共静态字段`BindableProperty`声明定义属性的后备存储密钥。
3. 应使用的公共实例属性包装`GetValue`和`SetValue`来检索和更改属性值。

有关完整示例，请参阅[Xamarin.Forms 中的可绑定属性](~/xamarin-forms/xaml/bindable-properties.md)。

### <a name="attached-properties"></a>附加的属性

附加的属性是可绑定属性的子集和它们的工作的方式与在 WPF 中相同。 主要区别在于属性包装器在这种情况下是省略和替换为一组所属的类上的静态 get/set 方法。 请参阅[附加属性在 Xamarin.Forms 中](~/xamarin-forms/xaml/attached-properties.md)有关详细信息。

### <a name="using-the-binding-engine"></a>使用绑定引擎

因为它是在 WPF 中，使用绑定引擎的过程都是相同的。 它可以在代码隐藏中通过创建利用`Binding`对象绑定到源对象 （任何.NET 类型） 和可选属性值 (如果省略，它会将源对象属性本身-就像 WPF)。 然后，可以使用`SetBinding`任何`BindableObject`要关联到绑定`BindableProperty`。

或者，可以在 XAML 中使用定义的绑定关系`BindingExtension`。 它在 WPF 中具有相同的基本扩展值。

不止是 WPF 类似的 Silverlight 实现绑定支持和引擎。 有几个缺少的功能，这不在 Xamarin.Forms 中实现：

- 目前不支持绑定中的下列功能：
    - BindingGroupName
    - BindsDirectlyToSource
    - IsAsync
    - 多重绑定
    - NotifyOnSourceUpdated
    - NotifyOnTargetUpdated
    - NotifyOnValidationError
    - UpdateSourceTrigger
    - UpdateSourceExceptionFilter
    - ValidatesOnDataErrors
    - ValidatesOnExceptions
    - ValidationRules 集合
    - XPath
    - XmlNamespaceManager

#### <a name="relativesource"></a>RelativeSource

对于不支持`RelativeSource`绑定。 在 WPF 中，它们允许您将绑定到 XAML 中定义其他可视元素。 在 Xamarin.Forms 中，此相同的功能可以使用`{x:Reference}`标记扩展。 例如，假设我们有具有名称"otherControl"具有 Text 属性的控件，我们可以绑定到它像这样：

**WPF**

```xaml
Text={Binding RelativeSource={RelativeSource otherControl}, Path=Text}
```

**Xamarin.Forms**

```xaml
Text={Binding Source={x:Reference otherControl}, Path=Text}
```

相同的功能可用于`{RelativeSource Self}`功能。 但是没有查找类型的上级的支持 (`{RelativeSource FindAncestor}`)。

#### <a name="binding-context"></a>绑定上下文

在 WPF 中，可以定义`DataContext`属性值的 reprents 对默认的绑定源。 如果未定义的绑定源，则使用此属性的值。 减小可视化树，使其能够在较高级别定义，然后由子继承值。

在 Xamarin.Forms 中，此相同功能可用，但属性名称是`BindingContext`。

#### <a name="value-converters"></a>值转换器

Xamarin.Forms-就像 WPF 一样完全支持值转换器。 使用相同的接口形状，但 Xamarin.Forms 中定义的接口`Xamarin.Forms`命名空间。

### <a name="model-view-viewmodel"></a>Model-View-ViewModel

MVVM 是完全受支持 WPF 与 Xamarin.Forms。

WPF 包括一个内置的`RoutedCommand`有时用于;Xamarin.Forms 具有超出没有内置命令支持`ICommand`接口定义。 可以包含多种 MVVM 框架以添加必要的基础类来实现 MVVM。

#### <a name="inotifypropertychanged-and-inotifycollectionchanged"></a>INotifyPropertyChanged 和 INotifyCollectionChanged

Xamarin.Forms 绑定中完全支持这两个接口。 与许多基于 XAML 的框架，可以在 Xamarin.Forms （就像 WPF) 中的后台线程上引发属性更改通知和绑定引擎将正确地转换到 UI 线程。

此外，支持这两个环境`SynchronziationContext`并`async` / `await`执行适当的线程封送处理。 WPF 还包括`Dispatcher`类上所有可视化元素，Xamarin.Forms 具有一个静态方法`Device.BeginInvokeOnMainThread`这还可以使用 (虽然`SynchronizationContext`是跨平台编写代码的首选)。

- Xamarin.Forms 具有`ObservableCollection<T>`支持集合更改通知。
- 可以使用`BindingBase.EnableCollectionSynchronization`以启用跨线程更新集合。 该 API 与 WPF 变体，略有不同[检查使用情况详细信息文档](xref:Xamarin.Forms.BindingBase.EnableCollectionSynchronization*)。

## <a name="data-templates"></a>数据模板

在 Xamarin.Forms 自定义的呈现中支持数据模板`ListView`行 （单元格）。 与 WPF 可能使用不同`DataTemplate`s 的任何面向内容的控件，Xamarin.Forms 当前仅使用其用于`ListView`。 模板定义可以是以内联方式定义 (分配给`ItemTemplate`属性)，或为中的资源`ResourceDictionary`。

此外，它们不作为其 WPF 对应非常灵活。

1. 根元素`DataTemplate`必须_始终_是`ViewCell`对象。
2. 数据触发器完全支持在数据模板中，但必须包括`DataType`属性，指示该触发器与之关联的属性的类型。
3. `DataTemplateSelector` 此外支持，但派生自`DataTemplate`并因此只需直接向分配`ItemTemplate`属性 (与`ItemTemplateSelector`在 WPF 中)。

## <a name="itemscontrol"></a>ItemsControl

没有任何内置等效于`ItemsControl`在 Xamarin.Forms 中; 但没有[自定义一个用于 Xamarin.Forms 可从以下站点](https://github.com/xamarinhq/xamu-infrastructure/blob/master/src/XamU.Infrastructure/Controls/ItemsControl.cs)。

## <a name="user-controls"></a>用户控件

在 WPF 中， `UserControl`s 用于提供 UI，这都会带来相关行为的一部分。 在 Xamarin.Forms 中，我们将使用`ContentView`实现相同目的。 都支持在 XAML 中绑定和包含。

## <a name="navigation"></a>导航

WPF 还包括很少使用`NavigationService`这可用来提供一种"类似于浏览器的"导航功能。 大多数应用程序不愿意在与此但是，而不是使用不同`Window`元素或窗口中显示数据的不同部分。

电话在设备上，不同_屏幕_通常是该解决方案并因此 Xamarin.Forms 支持几种形式的导航：

| 导航样式 | 页类型 |
|--- |--- |
|基于堆栈的 （推送/弹出）|NavigationPage|
|母版/详细信息|MasterDetailPage|
|制表符|TabbedPage|
|向左/右轻扫|CarouselView|

`NavigationPage`是最常用的方法，并且每一页具有`Navigation`属性可以用于推送或弹出页和导航堆栈中弹出。 这是最接近等效于`NavigationService`在 WPF 中找到。

### <a name="url-navigation"></a>URL 导航

WPF 是一种面向桌面的技术，并且可以接受命令行参数指示启动行为。 可以使用 Xamarin.Forms[深层 URL 链接](https://blog.xamarin.com/deep-link-content-with-xamarin-forms-url-navigation/)跳转到页面上启动。
