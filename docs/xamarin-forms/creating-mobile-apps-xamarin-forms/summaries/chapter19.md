---
title: 第19章的摘要。 集合视图
description: 用 Xamarin 创建移动应用程序：第19章的摘要。 集合视图
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 0AEC3A5C-586E-4D0F-9895-67E99A053A79
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: bffbd2dec4a8494723597ba6e0f0af69e57f3718
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032855"
---
# <a name="summary-of-chapter-19-collection-views"></a>第19章的摘要。 集合视图

[![下载示例](~/media/shared/download.png) 下载示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)

> [!NOTE] 
> 此页上的说明指出了 Xamarin 在书籍中提供的材料中分离的区域。

Xamarin. 窗体定义了三个视图来维护集合并显示其元素：

- [`Picker`](xref:Xamarin.Forms.Picker)是一个相对较短的字符串项列表，允许用户选择一个
- [`ListView`](xref:Xamarin.Forms.ListView)通常是相同类型和格式的项的长列表，还允许用户选择一项
- [`TableView`](xref:Xamarin.Forms.TableView)是用于显示数据或管理用户输入的*单元格*集合（通常为各种类型和外观）

MVVM 应用程序通常使用 `ListView` 显示可选择的对象集合。

## <a name="program-options-with-picker"></a>带有选取器的程序选项

当你需要允许用户从相对较短的 `string` 项列表中选择一个选项时， [`Picker`](xref:Xamarin.Forms.Picker)是一个不错的选择。

### <a name="the-picker-and-event-handling"></a>选取器和事件处理

[**PickerDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerDemo)示例演示如何使用 XAML 将 `Picker` [`Title`](xref:Xamarin.Forms.Picker.Title)属性设置为，并将 `string` 项添加到[`Items`](xref:Xamarin.Forms.Picker.Items)集合。 当用户选择 `Picker`时，它将以与平台相关的方式显示 `Items` 集合中的项。

[`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged)事件指示用户已选择了某个项。 然后，从零开始的[`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex)属性指示选定的项。 如果未选择任何项，`SelectedIndex` 等于 &ndash;1。

你还可以使用 `SelectedIndex` 初始化选定项，但必须在填充 `Items` 集合后设置该项。 在 XAML 中，这意味着你可能会使用属性元素来设置 `SelectedIndex`。

### <a name="data-binding-the-picker"></a>数据绑定器

`SelectedIndex` 属性受可绑定属性（但 `Items` 不受支持）的支持，因此，对 `Picker` 使用数据绑定非常困难。 一种解决方法是将 `Picker` 与[`ObjectToIndexConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ObjectToIndexConverter.cs) （如[**FormsBook**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库中的一个）结合使用。 [**PickerBinding**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerBinding)演示了它的工作原理。

> [!NOTE] 
> Xamarin `Picker` 现在包含支持数据绑定的 `ItemsSource` 和 `SelectedItem` 属性。 请参阅[选取器](~/xamarin-forms/user-interface/picker/index.md)。

## <a name="rendering-data-with-listview"></a>用 ListView 呈现数据

[`ListView`](xref:Xamarin.Forms.ListView)是派生自[`ItemsView<TVisual>`](xref:Xamarin.Forms.ItemsView`1)的唯一类，从该类继承[`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource)和[`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate)属性。

`ItemsSource` 属于 `IEnumerable` 类型，但它在默认情况下是 `null` 的，并且必须通过数据绑定将其显式初始化或（更常见）设置为集合。 此集合中的项可以是任何类型。

`ListView` 定义一个[`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem)属性，该属性设置为 `ItemsSource` 集合中的一个项，或者，如果未选择任何项，则为 `null`。 选择新项时 `ListView` 激发[`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected)事件。

### <a name="collections-and-selections"></a>集合和选择

[**ListViewList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewList)示例使用 17 `Color` 值填充 `List<Color>` 集合中的 `ListView`。 这些项是可选的，但在默认情况下，这些项将显示在其不严重 `ToString` 表示形式。 本章中的几个示例说明了如何修复此显示，并使其在理想情况下更具吸引力。

### <a name="the-row-separator"></a>行分隔符

在 iOS 和 Android 上显示时，细线分隔行。 可以通过[`SeparatorVisibility`](xref:Xamarin.Forms.ListView.SeparatorVisibility)和[`SeparatorColor`](xref:Xamarin.Forms.ListView.SeparatorColor)属性对此进行控制。 `SeparatorVisibility` 属性的类型为[`SeparatorVisibility`](xref:Xamarin.Forms.SeparatorVisibility)，这是一个包含两个成员的枚举：

- [`Default`](xref:Xamarin.Forms.SeparatorVisibility.Default)，默认设置
- [`None`](xref:Xamarin.Forms.SeparatorVisibility.None)

### <a name="data-binding-the-selected-item"></a>数据绑定选定项

`SelectedItem` 属性受可绑定属性支持，因此它可以是数据绑定的源或目标。 它的默认 `BindingMode` 是 `OneWayToSource`的，但它通常是双向数据绑定的目标，尤其是在 MVVM 方案中。 [**ListViewArray**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewArray)示例演示了这种类型的绑定。

### <a name="the-observablecollection-difference"></a>ObservableCollection 差异

[**ListViewLogger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewLogger)示例将 `ListView` 的 `ItemsSource` 属性设置为一个 `List<DateTime>` 集合，然后每隔一秒使用计时器逐步将一个新的 `DateTime` 对象添加到该集合。

不过，`ListView` 不会自动更新自身，因为 `List<T>` 集合没有指示何时在集合中添加或删除项的通知机制。

在此类方案中，一个更好的类是[`ObservableCollection<T>`](xref:System.Collections.ObjectModel.ObservableCollection`1)在 `System.Collections.ObjectModel` 命名空间中定义的。 此类实现[`INotifyCollectionChanged`](xref:System.Collections.Specialized.INotifyCollectionChanged)接口，因此当在集合中添加或移除项时，或者当在集合中被替换或移动项时，将激发[`CollectionChanged`](xref:System.Collections.ObjectModel.ObservableCollection`1.CollectionChanged)事件。 当 `ListView` 内部检测到实现 `INotifyCollectionChanged` 的类已设置为其 `ItemsSource` 属性时，它会将一个处理程序附加到 `CollectionChanged` 事件，并在集合更改时更新其显示。

[**ObservableLogger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ObservableLogger)示例演示如何使用 `ObservableCollection`。

### <a name="templates-and-cells"></a>模板和单元

默认情况下，`ListView` 使用每个项的 `ToString` 方法显示其集合中的项。 更好的方法包括定义用于显示项的模板。

若要尝试此功能，可以使用[**FormsBook**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库中的[`NamedColor`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs)类。 此类定义类型 `IList<NamedColor>` 的静态 `All` 属性，该属性包含与 `Color` 结构的公共字段对应的 141 `NamedColor` 对象。

[**NaiveNamedColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/NaiveNamedColorList)示例将 `ListView` 的 `ItemsSource` 设置为此 `NamedColor.All` 属性，但仅显示 `NamedColor` 对象的完全限定类名。

`ListView` 需要一个模板来显示这些项。 在代码中，可以使用引用[`Cell`](xref:Xamarin.Forms.Cell)类导数的[`DataTemplate` 构造函数](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Type))将 `ItemsView<TVisual>` 定义的[`ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate)属性设置为[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)对象。 `Cell` 具有五个派生：

- [`TextCell`](xref:Xamarin.Forms.TextCell) &mdash; 包含两个 `Label` 视图（从概念上讲）
- [`ImageCell`](xref:Xamarin.Forms.ImageCell) &mdash; 将 `Image` 视图添加到 `TextCell`
- [`EntryCell`](xref:Xamarin.Forms.EntryCell) &mdash; 包含带有 `Label` 的 `Entry` 视图
- [`SwitchCell`](xref:Xamarin.Forms.SwitchCell) &mdash; 包含具有 `Label` 的 `Switch`
- [`ViewCell`](xref:Xamarin.Forms.ViewCell) &mdash; 可以是任何 `View` （可能包含子级）

然后在 `DataTemplate` 对象上调用[`SetValue`](xref:Xamarin.Forms.DataTemplate.SetValue(Xamarin.Forms.BindableProperty,System.Object))和[`SetBinding`](xref:Xamarin.Forms.DataTemplate.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) ，将值与 `Cell` 属性相关联，或在 `Cell` 集合中引用项属性的 `ItemsSource` 属性上设置数据绑定。 [**TextCellListCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListCode)示例演示了这一点。

当 `ListView`显示每一项时，将从模板构造一个小的可视化树，并且在此可视化树中的元素的项和属性之间建立数据绑定。 您可以通过以下方法了解此过程的概念：安装[`ItemAppearing`](xref:Xamarin.Forms.ListView.ItemAppearing)的处理程序和 `ListView`的[`ItemDisappearing`](xref:Xamarin.Forms.ListView.ItemDisappearing)事件，或使用替代的[`DataTemplate` 构造函数](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Func{System.Object}))（该函数使用每次项的可视化树都必须调用的函数）要创建的。

[**TextCellListXaml**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListXaml)完全在 XAML 中显示了功能完全相同的程序。 `DataTemplate` 标记设置为 `ListView`的 `ItemTemplate` 属性，然后 `TextCell` 设置为 `DataTemplate`。 绑定到集合中的项的属性时，将直接在 `TextCell`的[`Text`](xref:Xamarin.Forms.TextCell.Text)和[`Detail`](xref:Xamarin.Forms.TextCell.Detail)属性上设置。

### <a name="custom-cells"></a>自定义单元

在 XAML 中，可以将[`ViewCell`](xref:Xamarin.Forms.ViewCell)设置为 `DataTemplate`，然后将自定义可视化树定义为 `ViewCell`的[`View`](xref:Xamarin.Forms.ViewCell.View)属性。 （`View` 是 `ViewCell` 的 content 属性，因此不需要 `ViewCell.View` 标记。）[**CustomNamedColorList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CustomNamedColorList)示例演示了此方法：

[![自定义命名颜色列表的三个屏幕截图](images/ch19fg11-small.png "自定义命名颜色列表")](images/ch19fg11-large.png#lightbox "自定义命名颜色列表")

为所有平台获取大小调整权限可能比较棘手。 [`RowHeight`](xref:Xamarin.Forms.ListView.RowHeight)属性非常有用，但在某些情况下，你可能需要利用[`HasUnevenRows`](xref:Xamarin.Forms.ListView.HasUnevenRows)属性，这种方法效率较低，但强制 `ListView` 为行调整大小。 对于 iOS 和 Android，必须使用这两个属性之一才能获得正确的行大小。

### <a name="grouping-the-listview-items"></a>将 ListView 项分组

`ListView` 支持项分组，并在这些组之间导航。 `ItemsSource` 属性必须设置为集合的集合：`ItemsSource` 设置为的对象必须实现 `IEnumerable`，并且集合中的每一项还必须实现 `IEnumerable`。 每个组应包括两个属性：组的文本说明和三个字母的缩写。

[**FormsBook**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库中的[`NamedColorGroup`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColorGroup.cs)类创建了七组 `NamedColor` 对象。 [**ColorGroupList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorGroupList)示例演示如何将这些组与 `ListView` 设置为 `true`的[`IsGroupingEnabled`](xref:Xamarin.Forms.ListView.IsGroupingEnabled)属性以及绑定到每个组中的属性的[`GroupDisplayBinding`](xref:Xamarin.Forms.ListView.GroupDisplayBinding)和[`GroupShortNameBinding`](xref:Xamarin.Forms.ListView.GroupShortNameBinding)属性一起使用。

### <a name="custom-group-headers"></a>自定义组标头

可以通过将 `GroupDisplayBinding` 属性替换为定义标题模板的[`GroupHeaderTemplate`](xref:Xamarin.Forms.ListView.GroupHeaderTemplate) ，为 `ListView` 组创建自定义标头。

### <a name="listview-and-interactivity"></a>ListView 和交互性

通常，应用程序通过将处理程序附加到 `ItemSelected` 或[`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped)事件，或在 `SelectedItem` 属性上设置数据绑定来获取与 `ListView` 的用户交互。 但某些单元类型（`EntryCell` 和 `SwitchCell`）允许用户交互，还可以创建自己与用户交互的自定义单元。 [**InteractiveListView**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/InteractiveListView)创建[`ColorViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs)的100实例，并允许用户使用 `Slider` 元素的三个更改每种颜色。 该程序还使用[**FormsBook**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)中的[`ColorToContrastColorConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorToContrastColorConverter.cs) 。

## <a name="listview-and-mvvm"></a>ListView 和 MVVM

`ListView` 在 MVVM 方案中发挥着重要作用。 每当 ViewModel 中存在 `IEnumerable` 集合时，它通常绑定到 `ListView`。 此外，集合中的项通常实现 `INotifyPropertyChanged` 以便与模板中的属性绑定。

### <a name="a-collection-of-viewmodels"></a>Viewmodel 的集合

为了探索这一点， [**SchoolOfFineArts**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt)库创建了多个基于[XML 数据文件](https://xamarin.github.io/xamarin-forms-book-samples/SchoolOfFineArt/students.xml)的类，以及此虚构学校的虚构学生映像。

[`Student`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/Student.cs)类派生自[`ViewModelBase`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/ViewModelBase.cs)。 [`StudentBody`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/StudentBody.cs)类是 `Student` 对象的集合，也派生自 `ViewModelBase`。 [`SchoolViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/SchoolViewModel.cs)下载 XML 文件并汇编所有对象。

[**StudentList**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/StudentList)程序使用 `ImageCell` 在 `ListView`中显示学生及其图像：

[![学生列表的三向屏幕截图](images/ch19fg18-small.png "学生列表")](images/ch19fg18-large.png#lightbox "学生列表")

[**ListViewHeader**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewHeader)示例添加了一个[`Header`](xref:Xamarin.Forms.ListView.Header)属性，但它仅在 Android 上显示。

### <a name="selection-and-the-binding-context"></a>选择和绑定上下文

[**SelectedStudentDetail**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/SelectedStudentDetail)程序将 `StackLayout` 的 `BindingContext` 绑定到 `ListView`的 `SelectedItem` 属性。 这允许程序显示有关所选学生的详细信息。

### <a name="context-menus"></a>上下文菜单

单元可以定义以平台特定的方式实现的上下文菜单。 若要创建此菜单，请将[`MenuItem`](xref:Xamarin.Forms.MenuItem)对象添加到 `Cell`的[`ContextActions`](xref:Xamarin.Forms.Cell.ContextActions)属性。

`MenuItem` 定义五个属性：

- `string` 类型的 [`Text`](xref:Xamarin.Forms.MenuItem.Text)
- `FileImageSource` 类型的 [`Icon`](xref:Xamarin.Forms.MenuItem.Icon)
- `bool` 类型的 [`IsDestructive`](xref:Xamarin.Forms.MenuItem.IsDestructive)
- `ICommand` 类型的 [`Command`](xref:Xamarin.Forms.MenuItem.Command)
- `object` 类型的 [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter)

`Command` 和 `CommandParameter` 属性表示每一项的 ViewModel 包含用于执行所需菜单命令的方法。 在非 MVVM 方案中，`MenuItem` 还定义了[`Clicked`](xref:Xamarin.Forms.MenuItem.Clicked)事件。

[**CellContextMenu**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CellContextMenu)演示了此方法。 每个 `MenuItem` 的 `Command` 属性都绑定到 `Student` 类中 `ICommand` 类型的属性。 将 `IsDestructive` 属性设置为删除或删除所选对象的 `MenuItem` 的 `true`。

### <a name="varying-the-visuals"></a>改变视觉对象

有时，您需要根据属性在 `ListView` 中的项的视觉对象上略有不同。 例如，如果学生的评分点平均低于2.0，则[**ColorCodedStudents**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorCodedStudents)示例会以红色显示该学生的姓名。
这可通过在[**FormsBook**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)中使用绑定值转换器[`ThresholdToObjectConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ThresholdToObjectConverter.cs)来实现。

### <a name="refreshing-the-content"></a>刷新内容

`ListView` 支持用于刷新其数据的下拉手势。 要启用此设置，程序必须将[`IsPullToRefresh`](xref:Xamarin.Forms.ListView.IsPullToRefreshEnabled)属性设置为 `true`。 `ListView` 通过将其[`IsRefreshing`](xref:Xamarin.Forms.ListView.IsRefreshing)属性设置为 `true`，并引发[`Refreshing`](xref:Xamarin.Forms.ListView.Refreshing)事件，并引发其[`Execute`](xref:Xamarin.Forms.ListView.RefreshCommand)属性的`RefreshCommand`方法，来响应下拉手势。

处理 `Refresh` 事件或 `RefreshCommand` 的代码可能会更新 `ListView` 显示的数据，并将 `IsRefreshing` 设置为 `false`。

[**Rss 源**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/RssFeed)示例演示如何使用实现数据绑定 `RefreshCommand` 和 `IsRefreshing` 属性的[`RssFeedViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/RssFeed/RssFeed/RssFeed/RssFeedViewModel.cs) 。

## <a name="the-tableview-and-its-intents"></a>TableView 及其意图

虽然 `ListView` 通常显示同一类型的多个实例，但[`TableView`](xref:Xamarin.Forms.TableView)通常重点是为各种类型的多个属性提供用户界面。 每一项都与其自己的[`Cell`](xref:Xamarin.Forms.Cell)导数相关联，以显示属性或向其提供用户界面。

### <a name="properties-and-hierarchies"></a>属性和层次结构

`TableView` 仅定义四个属性：

- 类型[`TableIntent`](xref:Xamarin.Forms.TableIntent)的[`Intent`](xref:Xamarin.Forms.TableView.Intent) ，枚举
- 类型[`TableRoot`](xref:Xamarin.Forms.TableRoot) [`Root`](xref:Xamarin.Forms.TableView.Root) ，`TableView` 的内容属性
- `int` 类型的 [`RowHeight`](xref:Xamarin.Forms.TableView.RowHeight)
- `bool` 类型的 [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows)

`TableIntent` 枚举指示你打算如何使用 `TableView`：

- [`Data`](xref:Xamarin.Forms.TableIntent.Data)
- [`Form`](xref:Xamarin.Forms.TableIntent.Form)
- [`Settings`](xref:Xamarin.Forms.TableIntent.Settings)
- [`Menu`](xref:Xamarin.Forms.TableIntent.Menu)

这些成员还建议 `TableView`的一些用途。

定义表涉及多个其他类：

- [`TableSectionBase`](xref:Xamarin.Forms.TableSectionBase)是派生自 `BindableObject` 并定义[`Title`](xref:Xamarin.Forms.TableSectionBase.Title)属性的抽象类。

- [`TableSectionBase<T>`](xref:Xamarin.Forms.TableSectionBase`1)是派生自 `TableSectionBase` 并实现 `IList<T>` 和 `INotifyCollectionChanged` 的抽象类。

- [`TableSection`](xref:Xamarin.Forms.TableSection)派生自 `TableSectionBase<Cell>`

- [`TableRoot`](xref:Xamarin.Forms.TableRoot)派生自 `TableSectionBase<TableSection>`

简而言之，`TableView` 具有设置为 `TableRoot` 对象的 `Root` 属性，该对象为 `TableSection` 对象的集合，其中每个对象都是 `Cell` 对象的集合。 一个表包含多个部分，每个部分都有多个单元格。 表本身可以有标题，每个部分可以有一个标题。 尽管 `TableView` 利用 `Cell` 派生，但它不使用 `DataTemplate`。

### <a name="a-prosaic-form"></a>Prosaic 窗体

[**EntryForm**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/EntryForm)示例定义[`PersonalInformation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs)视图模型，该模型的实例成为 `TableView`的 `BindingContext`。 然后，其 `TableSection` 中的每个 `Cell` 导数都可以绑定到 `PersonalInformation` 类的属性。

### <a name="custom-cells"></a>自定义单元

[**ConditionalCells**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalCells)示例在**EntryForm**上展开。 [`ProgrammerInformation`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs)类包含一个布尔值属性，该属性控制两个附加属性的适用性。 对于这两个附加属性，该程序使用基于 [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) 库中的 [PickerCell.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml) 和 [PickerCell.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml.cs)。`PickerCell`

尽管两个 `PickerCell` 元素的 `IsEnabled` 属性绑定到 `ProgrammerInformation`中的布尔属性，但此方法似乎不起作用，这会提示下一个示例。

### <a name="conditional-sections"></a>条件部分

[**ConditionalSection**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalSection)示例将在单独 `TableSection`中选择布尔项时有条件的两个项放置在一起。 代码隐藏文件从 `TableView` 中移除此部分，或根据布尔值属性将其重新添加。

### <a name="a-tableview-menu"></a>TableView 菜单

`TableView` 的另一种用法是菜单。 [**Menucommand**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/MenuCommands)示例演示了一个菜单，该菜单使你可以在屏幕周围移动一点 `BoxView`。

## <a name="related-links"></a>相关链接

- [第19章完整文本（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch19-Apr2016.pdf)
- [第19章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)
- [选取器](~/xamarin-forms/user-interface/picker/index.md)
- [ListView](~/xamarin-forms/user-interface/listview/index.md)
- [TableView](~/xamarin-forms/user-interface/tableview.md)
