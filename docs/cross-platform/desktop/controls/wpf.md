---
ms.assetid: 1BB412D1-FC3D-4E69-8B01-B976A3DB6328
title: WPF 与Xamarin. Forms：相似 & 差异
description: 本文档将 WPF 与 Xamarin 进行比较和对比。 它讨论了控制模板、XAML、绑定基础结构、数据模板、System.windows.controls.itemscontrol>、UserControl、导航和 URL 导航。
author: conceptdev
ms.author: crdun
ms.date: 04/26/2017
ms.openlocfilehash: de8e3445679e185b488311e58221ae5f4c28f85c
ms.sourcegitcommit: 6b833f44d5fd8dc7ab7f8546e8b7d383e5a989db
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71106035"
---
# <a name="wpf-vs-xamarinforms-similarities--differences"></a>WPF 与Xamarin. Forms：相似 & 差异

## <a name="control-templates"></a>控件模板

WPF 支持*控件模板*的概念，这些模板提供控件（`Button`、 `ListBox`等）的可视化说明。 如上所述，Xamarin 使用具体的_呈现_类，此类与本机平台（IOS、Android 等）进行交互，以可视化控件。

但是， `ControlTemplate` _Xamarin 具有类型_-它用于主题`Page`对象。 它提供的定义`Page`提供一致的内容，但允许页的用户更改颜色、字体等，甚至添加元素以便使其对应用程序是唯一的。

这种情况的常见用法包括身份验证对话、提示和提供标准化，但 themable 的页面外观，可以在应用中自定义。 作为此支持的一部分，将使用很多熟悉的 WPF 命名控件：

1. `ContentPage`
2. `ContentView`
3. `ContentPresenter`
4. `TemplateBinding`

但必须知道，它们在 Xamarin 中的作用并_不_相同。 有关此功能的详细信息，请参阅[文档页](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md)。

## <a name="xaml"></a>XAML

XAML 用作 WPF 和 Xamarin 的声明性标记语言。 大多数情况下，语法是相同的，主要区别是 XAML 图形定义/创建的对象。

- Xamarin 支持[XAML 2009 规范](/dotnet/framework/xaml-services/xaml-2009-language-features/);这样，就可以更轻松地定义诸如`string`s、 `int`s 等数据，以及定义泛型类型和将参数传递给构造函数。

- 目前没有办法动态加载 XAML，如 WPF 可以通过`XamlReader`。 不过，你可以使用[NuGet 包](https://www.nuget.org/packages/Xamarin.Forms.Dynamic/)获得相同的基本功能。

### <a name="markup-extensions"></a>标记扩展

Xamarin 支持通过标记扩展扩展 XAML，与 WPF 非常类似。 它具有相同的基本构建基块：

1. `{x:Array}`
2. `{Binding}`
3. `{DynamicResource}`
4. `{x:Null}`
5. `{x:Static}`
6. `{StaticResource}`
7. `{x:Type}`

此外，它还包括`{x:Reference}` XAML 2009 规范`{TemplateBinding}`和用于由 Xamarin 支持的专用版本的`ControlTemplate`标记扩展。

> [!WARNING]
> `ControlTemplate`支持不是相同的，即使它具有相同的名称。

Xamarin 也支持自定义标记扩展，但实现略有不同。 在 WPF 中，必须从`MarkupExtension`抽象基类派生。 在 Xamarin 中，它被替换为接口`IMarkupExtension`或者`IMarkupExtension<T>`更灵活。

与 WPF 一样，单个必需的方法是`ProvideValue`从标记扩展返回值的方法。

## <a name="binding-infrastructure"></a>绑定基础结构

执行的核心概念之一是将视觉对象属性连接到 .NET 数据属性的数据绑定基础结构。 这将启用各种体系结构模式，如 MVVM。 基本设计完全相同-你具有可绑定的基类[msds-bindableobject](xref:Xamarin.Forms.BindableObject)，这是在 WPF 中，这是[system.windows.dependencyobject>](xref:System.Windows.DependencyObject)类。 此基类用作将作为数据绑定中的目标的所有对象的根上级。 然后，派生的类公开[BindableProperty](xref:Xamarin.Forms.BindableProperty)对象，这些对象充当属性值的后备存储（这些对象在 WPF 中定义为[DependencyProperty](xref:System.Windows.DependencyProperty)对象）。

### <a name="defining-bindable-properties"></a>定义可绑定属性

Xamarin 中的可绑定属性的定义与 WPF 相同：

1. 对象必须派生自`BindableObject`。
2. 必须有声明的类型`BindableProperty`的公共静态字段，才能定义属性的后备存储密钥。
3. 应有一个公共实例属性包装器，该包装`GetValue`器`SetValue`使用和来检索和更改属性值。

有关完整示例，请参阅[Xamarin 中的可绑定属性](~/xamarin-forms/xaml/bindable-properties.md)。

### <a name="attached-properties"></a>附加的属性

附加属性是可绑定属性的子集，它们的工作方式与在 WPF 中的工作方式相同。 主要区别在于，在这种情况下，属性包装省略，并替换为所属类的一组静态 get/set 方法。 有关详细信息，请参阅[Xamarin 中的附加属性](~/xamarin-forms/xaml/attached-properties.md)。

### <a name="using-the-binding-engine"></a>使用绑定引擎

使用绑定引擎的过程与在 WPF 中的过程相同。 可在代码隐藏中利用此方法：创建`Binding`绑定到源对象的对象（任何 .net 类型）和一个可选属性值（如果为省略，则它会将源对象视为属性本身，就像 WPF）。 然后，你可以`SetBinding`在任何`BindableObject`上使用将绑定`BindableProperty`关联到。

或者，可以使用`BindingExtension`在 XAML 中定义绑定关系。 它与 WPF 中的扩展具有相同的基本值。

与 WPF 相比，绑定支持和引擎更类似于 Silverlight 实现。 在 Xamarin 中未实现某些缺少的功能。 Forms：

- 不支持绑定中的以下功能：
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

不支持`RelativeSource`绑定。 在 WPF 中，它们允许绑定到 XAML 中定义的其他可视元素。 在 Xamarin 中，可以使用`{x:Reference}`标记扩展来实现这一功能。 例如，假设有一个名为 "otherControl" 的控件，该控件具有 Text 属性，则可以将其绑定到该控件，如下所示：

**WPF**

```xaml
Text={Binding RelativeSource={RelativeSource otherControl}, Path=Text}
```

**Xamarin.Forms**

```xaml
Text={Binding Source={x:Reference otherControl}, Path=Text}
```

此`{RelativeSource Self}`功能可用于功能。 但是，不支持按类型（`{RelativeSource FindAncestor}`）查找上级。

#### <a name="binding-context"></a>绑定上下文

在 WPF 中，可以定义`DataContext` reprents 默认绑定源的属性值。 如果未定义绑定的源，则使用此属性值。 此值在可视化树中向下继承，使其在更高级别上定义，并由子级使用。

在 Xamarin. Forms 中，这一功能是无可的，但属性名称`BindingContext`是。

#### <a name="value-converters"></a>值转换器

Xamarin 格式完全支持值转换器。格式与 WPF 一样。 使用相同的接口形状，但 Xamarin. Forms 具有在`Xamarin.Forms`命名空间中定义的接口。

### <a name="model-view-viewmodel"></a>Model-View-ViewModel

WPF 和 Xamarin 都完全支持 MVVM。

WPF 包含一个有时会`RoutedCommand`使用的内置的;Xamarin. Forms 不包含`ICommand`接口定义之外的内置命令支持。 可以包括多种 MVVM 框架，以添加所需的基类来实现 MVVM。

#### <a name="inotifypropertychanged-and-inotifycollectionchanged"></a>INotifyPropertyChanged 和 INotifyCollectionChanged

Xamarin. Forms 绑定完全支持这两个接口。 与许多基于 XAML 的框架不同，可在 Xamarin 中的后台线程上引发属性更改通知。窗体（就像 WPF）一样，绑定引擎会正确地转换到 UI 线程。

此外，这两个环境`SynchronziationContext`都`async`支持并/ `await`执行正确的线程封送处理。 WPF 包含所有`Dispatcher`视觉对象上的类，Xamarin。窗体有一个静态`Device.BeginInvokeOnMainThread`方法，还可以使用该方法`SynchronizationContext` （尽管这是跨平台编码的首选方法）。

- Xamarin. Forms 包含`ObservableCollection<T>`支持集合更改通知的。
- 可以使用`BindingBase.EnableCollectionSynchronization`为集合启用跨线程更新。 API 与 WPF 变体略有不同，请[查看文档中的使用情况详细信息](xref:Xamarin.Forms.BindingBase.EnableCollectionSynchronization*)。

## <a name="data-templates"></a>数据模板

数据模板在 Xamarin 中受支持，可自定义`ListView`行（单元）的呈现。 与 WPF 一起使用， `DataTemplate`它可以将用于任何面向内容的控件，Xamarin 目前仅将其用于`ListView`。 可以以内联方式（分配`ItemTemplate`给属性）或`ResourceDictionary`中的资源来定义模板定义。

此外，它们与 WPF 对应项并不完全相同。

1. 的根元素`DataTemplate`必须`ViewCell` _始终_为对象。
2. 数据模板完全支持数据触发器，但必须包括一个`DataType`属性，该属性指示与触发器关联的属性的类型。
3. `DataTemplateSelector`也受支持，但派生自`DataTemplate` ，因此只是直接分配`ItemTemplate`到属性（ `ItemTemplateSelector`在 WPF 中为）。

## <a name="itemscontrol"></a>ItemsControl

在 xamarin 中没有内置的`ItemsControl`等效项，但有一个适用于 xamarin 的[自定义项。此处提供了窗体](https://github.com/xamarinhq/xamu-infrastructure/blob/master/src/XamU.Infrastructure/Controls/ItemsControl.cs)。

## <a name="user-controls"></a>用户控件

在 WPF 中`UserControl`，用于提供具有关联行为的 UI 部分。 在 Xamarin. Forms 中，我们将`ContentView`用于相同的目的。 两者都支持 XAML 中的绑定和包含。

## <a name="navigation"></a>导航

WPF 包含很少使用`NavigationService`的，可用于提供 "类似于浏览器" 的导航功能。 大多数应用程序不会使用此数据，而是`Window`使用不同的元素或窗口的不同部分来显示数据。

在电话设备上，不同的_屏幕_通常是解决方案，因此 Xamarin. forms 提供多种形式的导航支持：

| 导航样式 | 页面类型 |
|--- |--- |
|基于堆栈（push/pop）|NavigationPage|
|母版/详细信息|MasterDetailPage|
|制表符|TabbedPage|
|向左或向右轻扫|CarouselView|

是最常用的方法，并且每个页面都有`Navigation`一个属性，该属性可用于在导航堆栈中或从导航堆栈中弹出页面。 `NavigationPage` 这是最接近于 WPF 中`NavigationService`找到的。

### <a name="url-navigation"></a>URL 导航

WPF 是一种面向桌面的技术，可以接受命令行参数来引导启动行为。 Xamarin 可以使用[深层 URL 链接](https://blog.xamarin.com/deep-link-content-with-xamarin-forms-url-navigation/)来跳转到启动时的页面。
