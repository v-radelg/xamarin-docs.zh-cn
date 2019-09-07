---
title: 第 19 章的摘要。 集合视图
description: 用 Xamarin 创建移动应用程序：第 19 章的摘要。 集合视图
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 0AEC3A5C-586E-4D0F-9895-67E99A053A79
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: bad46bead106304713bae91a7bf752db8080760a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760611"
---
# <a name="summary-of-chapter-19-collection-views"></a>第 19 章的摘要。 集合视图

[![下载示例](~/media/shared/download.png)下载示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)

> [!NOTE] 
> 此页上的说明表明其中 Xamarin.Forms 已脱离一书中介绍的内容的区域。

Xamarin.Forms 定义了三个视图的保留集合并显示其元素：

- [`Picker`](xref:Xamarin.Forms.Picker) 是相对较短的字符串项列表，从而允许用户选择一个
- [`ListView`](xref:Xamarin.Forms.ListView) 通常是通常的相同类型的项的长列表和格式设置，还允许用户选择一个
- [`TableView`](xref:Xamarin.Forms.TableView) 是一系列*单元格*（通常的各种类型和外观） 若要显示的数据或管理用户输入

很常见的 MVVM 应用程序使用`ListView`以显示可选择对象的集合。

## <a name="program-options-with-picker"></a>选取器与程序选项

[ `Picker` ](xref:Xamarin.Forms.Picker)是一个不错的选择时需要允许用户选择一个选项中相对较短的列表进行`string`项。

### <a name="the-picker-and-event-handling"></a>选取器和事件处理

[ **PickerDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerDemo)示例演示如何使用 XAML 来设置`Picker` [ `Title` ](xref:Xamarin.Forms.Picker.Title)属性，并添加`string`项到[ `Items` ](xref:Xamarin.Forms.Picker.Items)集合。 当用户选择`Picker`，它将显示中的项`Items`方式取决于平台的集合。

[ `SelectedIndexChanged` ](xref:Xamarin.Forms.Picker.SelectedIndexChanged)事件表示用户选择某个项。 从零开始[ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex)属性然后指示选定的项。 如果未不选择任何项，`SelectedIndex`等于&ndash;1。

此外可以使用`SelectedIndex`初始化所选的项，但它必须设置后`Items`填充集合。 在 XAML，这意味着，您可能会使用属性元素可以设置`SelectedIndex`。

### <a name="data-binding-the-picker"></a>数据绑定在选取器

`SelectedIndex`由可绑定的属性支持属性，但`Items`不使用数据绑定与`Picker`很难。 一种解决方案是使用`Picker`搭配[ `ObjectToIndexConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ObjectToIndexConverter.cs)中的一个如[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库。 [ **PickerBinding** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/PickerBinding)说明这是如何工作。

> [!NOTE] 
> Xamarin.Forms`Picker`现在包括`ItemsSource`和`SelectedItem`支持数据绑定的属性。 请参阅[选取器](~/xamarin-forms/user-interface/picker/index.md)。

## <a name="rendering-data-with-listview"></a>使用 ListView 的呈现数据

[ `ListView` ](xref:Xamarin.Forms.ListView)是唯一的类派生自[ `ItemsView<TVisual>` ](xref:Xamarin.Forms.ItemsView`1)它所继承[ `ItemsSource` ](xref:Xamarin.Forms.ItemsView`1.ItemsSource)和[ `ItemTemplate`](xref:Xamarin.Forms.ItemsView`1.ItemTemplate)属性。

`ItemsSource` 类型`IEnumerable`但`null`默认情况下，必须显式初始化或通过数据绑定 （更常见） 设置为集合。 此集合中的项可以是任何类型。

`ListView` 定义[ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem)属性，则设置为一个中的项`ItemsSource`集合或`null`如果未不选择任何项。 `ListView` 激发[ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected)事件时选择了新项。

### <a name="collections-and-selections"></a>集合和选择

[ **ListViewList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewList)示例填充`ListView`17`Color`中的值以`List<Color>`集合。 是否可选择的项，但默认情况下，它们会显示具有其不严重`ToString`表示形式。 在这一章中的几个示例演示如何修复该显示并使其根据需要为极具吸引力。

### <a name="the-row-separator"></a>行分隔符

在 iOS 和 Android 的显示，细线分隔行。 您可以使用此控制[ `SeparatorVisibility` ](xref:Xamarin.Forms.ListView.SeparatorVisibility)并[ `SeparatorColor` ](xref:Xamarin.Forms.ListView.SeparatorColor)属性。 `SeparatorVisibility` 属性属于类型[ `SeparatorVisibility` ](xref:Xamarin.Forms.SeparatorVisibility)，具有两个成员的枚举：

- [`Default`](xref:Xamarin.Forms.SeparatorVisibility.Default)默认设置
- [`None`](xref:Xamarin.Forms.SeparatorVisibility.None)

### <a name="data-binding-the-selected-item"></a>数据绑定的选定的项

`SelectedItem`属性由可绑定的属性，因此它可以是源或目标的数据绑定。 其默认值`BindingMode`是`OneWayToSource`，但它通常是双向数据绑定，尤其是在 MVVM 方案的目标。 [ **ListViewArray** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewArray)示例演示了此类型的绑定。

### <a name="the-observablecollection-difference"></a>ObservableCollection 差异

[ **ListViewLogger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewLogger)示例集`ItemsSource`属性的`ListView`到`List<DateTime>`集合，然后逐渐添加一个新`DateTime`到集合的对象每个使用计时器的第二个。

但是，`ListView`不会自动更新本身由于`List<T>`集合不具有通知机制，以指示何时添加到或从集合中移除项。

多更好地类要在这种情况下是[ `ObservableCollection<T>` ](xref:System.Collections.ObjectModel.ObservableCollection`1)中定义`System.Collections.ObjectModel`命名空间。 此类实现[ `INotifyCollectionChanged` ](xref:System.Collections.Specialized.INotifyCollectionChanged)接口，从而触发[ `CollectionChanged` ](xref:System.Collections.ObjectModel.ObservableCollection`1.CollectionChanged)事件添加到或从集合中，或删除时它们需要替换或中移动项时集合中。 当`ListView`在内部检测到的类实现`INotifyCollectionChanged`已设置为其`ItemsSource`属性，它将附加到一个处理程序`CollectionChanged`事件并更新其显示集合发生更改时。

[ **ObservableLogger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ObservableLogger)示例演示如何使用`ObservableCollection`。

### <a name="templates-and-cells"></a>模板和单元格

默认情况下`ListView`使用每个项的集合中显示项`ToString`方法。 更好的方法包括定义要显示的项的模板。

若要体验此功能，可以使用[ `NamedColor` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColor.cs)类[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库。 此类定义一个静态`All`类型的属性`IList<NamedColor>`，其中包含 141`NamedColor`对象的公共字段相对应的`Color`结构。

[ **NaiveNamedColorList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/NaiveNamedColorList)示例集`ItemsSource`的`ListView`此`NamedColor.All`属性，但只有的完全限定的类名称`NamedColor`对象显示。

`ListView` 需要模板，以显示这些项。 在代码中，可以设置[ `ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1.ItemTemplate)定义的属性`ItemsView<TVisual>`到[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)对象使用[`DataTemplate`构造函数](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Type))，引用的派生[ `Cell` ](xref:Xamarin.Forms.Cell)类。 `Cell` 具有五个派生类：

- [`TextCell`](xref:Xamarin.Forms.TextCell) &mdash; 包含两个`Label`视图 （从概念上讲）
- [`ImageCell`](xref:Xamarin.Forms.ImageCell) &mdash; 添加`Image`视图 `TextCell`
- [`EntryCell`](xref:Xamarin.Forms.EntryCell) &mdash; 包含`Entry`视图与 `Label`
- [`SwitchCell`](xref:Xamarin.Forms.SwitchCell) &mdash; 包含`Switch`与 `Label`
- [`ViewCell`](xref:Xamarin.Forms.ViewCell) &mdash; 可以是任何`View`（很可能使用子级）

然后调用[ `SetValue` ](xref:Xamarin.Forms.DataTemplate.SetValue(Xamarin.Forms.BindableProperty,System.Object))并[ `SetBinding` ](xref:Xamarin.Forms.DataTemplate.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase))上`DataTemplate`对象将与值相关联`Cell`属性，或者对其设置数据绑定`Cell`属性引用中的项的属性`ItemsSource`集合。 了这一点[ **TextCellListCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListCode)示例。

每个项显示的`ListView`、 从模板中，构造一个小型的可视树和此可视化树中的项和的元素的属性之间建立数据绑定。 可以通过安装处理程序中获取此过程的大致[ `ItemAppearing` ](xref:Xamarin.Forms.ListView.ItemAppearing)并[ `ItemDisappearing` ](xref:Xamarin.Forms.ListView.ItemDisappearing)事件`ListView`，或通过使用一种替代方法[ `DataTemplate`构造函数](xref:Xamarin.Forms.DataTemplate.%23ctor(System.Func{System.Object}))使用项的可视化树，必须创建每次调用的函数。

[ **TextCellListXaml** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/TextCellListXaml)完全在 XAML 中显示的功能相同的程序。 一个`DataTemplate`标记设置为`ItemTemplate`的属性`ListView`，然后`TextCell`设置为`DataTemplate`。 直接在上设置到集合中项的属性的绑定[ `Text` ](xref:Xamarin.Forms.TextCell.Text)并[ `Detail` ](xref:Xamarin.Forms.TextCell.Detail)的属性`TextCell`。

### <a name="custom-cells"></a>自定义单元格

在 XAML 很可能设置[ `ViewCell` ](xref:Xamarin.Forms.ViewCell)到`DataTemplate`，然后定义自定义可视化树[ `View` ](xref:Xamarin.Forms.ViewCell.View)属性`ViewCell`。 (`View`内容的属性`ViewCell`因此`ViewCell.View`标记并不需要。)[ **CustomNamedColorList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CustomNamedColorList)示例演示了此种方法：

[![自定义名为颜色列表的三个屏幕截图](images/ch19fg11-small.png "自定义名为颜色列表")](images/ch19fg11-large.png#lightbox "自定义名为颜色列表")

获取适合所有平台大小调整可能比较棘手。 [ `RowHeight` ](xref:Xamarin.Forms.ListView.RowHeight)属性很有用，但在某些情况下，将想要借助[ `HasUnevenRows` ](xref:Xamarin.Forms.ListView.HasUnevenRows)属性，它是效率较低，但强制`ListView`调整行的大小。 对于 iOS 和 Android，你必须使用这两个属性之一来获取正确的行大小调整。

### <a name="grouping-the-listview-items"></a>对 ListView 项进行分组

`ListView` 支持对项进行分组和在这些组之间导航。 必须`ItemsSource`将属性设置为集合的集合：设置为的`ItemsSource`对象必须实现`IEnumerable`，并且集合中的每个项还必须实现`IEnumerable`。 每个组应包括两个属性： 组和三个字母缩写形式的文本说明。

[ `NamedColorGroup` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NamedColorGroup.cs)类[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库创建的七个组`NamedColor`对象。 [ **ColorGroupList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorGroupList)示例演示如何使用与这些组[ `IsGroupingEnabled` ](xref:Xamarin.Forms.ListView.IsGroupingEnabled)属性`ListView`设置为`true`，和[ `GroupDisplayBinding` ](xref:Xamarin.Forms.ListView.GroupDisplayBinding)并[ `GroupShortNameBinding` ](xref:Xamarin.Forms.ListView.GroupShortNameBinding)属性绑定到每个组中的属性。

### <a name="custom-group-headers"></a>自定义组标头

可以创建自定义标头`ListView`通过替换组`GroupDisplayBinding`具有属性[ `GroupHeaderTemplate` ](xref:Xamarin.Forms.ListView.GroupHeaderTemplate)定义标头的模板。

### <a name="listview-and-interactivity"></a>ListView 和交互性

通常，应用程序获取与用户交互`ListView`通过将附加到一个处理程序`ItemSelected`或[ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped)事件，或通过设置数据绑定在`SelectedItem`属性。 但某些单元格类型 (`EntryCell`和`SwitchCell`) 允许用户交互，并且它仍可以通过创建自定义单元格本身与用户交互。 [ **InteractiveListView** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/InteractiveListView)创建的 100 个实例[ `ColorViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs) ，并允许用户更改使用了三个每种颜色`Slider`元素。 该程序也使用[ `ColorToContrastColorConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorToContrastColorConverter.cs)中[ **Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)。

## <a name="listview-and-mvvm"></a>ListView 和 MVVM

`ListView` 在 MVVM 方案中扮演着重要角色。 每当`IEnumerable`ViewModel 中存在集合，它通常绑定到`ListView`。 此外，通常在集合中的项实现`INotifyPropertyChanged`要与模板中的属性一起绑定。

### <a name="a-collection-of-viewmodels"></a>Viewmodel 集合

若要浏览此操作，请[ **SchoolOfFineArts** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt)库创建基于多个类[XML 数据文件](http://xamarin.github.io/xamarin-forms-book-samples/SchoolOfFineArt/students.xml)和图像的虚构的学生可免费此虚构的学校。

[ `Student` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/Student.cs)类派生自[ `ViewModelBase` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/ViewModelBase.cs)。 [ `StudentBody` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/StudentBody.cs)类是一系列`Student`对象，并还派生`ViewModelBase`。 [ `SchoolViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/SchoolOfFineArt/SchoolOfFineArt/SchoolViewModel.cs)下载的 XML 文件，并组合它们的所有对象。

[ **StudentList** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/StudentList)程序使用`ImageCell`以显示学生和在其映像`ListView`:

[![学生列表的三个屏幕截图](images/ch19fg18-small.png "学生列表")](images/ch19fg18-large.png#lightbox "学生列表")

[ **ListViewHeader** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ListViewHeader)示例添加[ `Header` ](xref:Xamarin.Forms.ListView.Header)属性，但它仅在 Android 上出现。

### <a name="selection-and-the-binding-context"></a>选择和绑定上下文

[ **SelectedStudentDetail** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/SelectedStudentDetail)程序绑定`BindingContext`的`StackLayout`到`SelectedItem`属性`ListView`。 这允许程序，用于显示有关所选学生的详细的信息。

### <a name="context-menus"></a>上下文菜单

单元格可以定义上下文菜单中的特定于平台的方式实现。 若要创建此菜单，添加[ `MenuItem` ](xref:Xamarin.Forms.MenuItem)对象添加到[ `ContextActions` ](xref:Xamarin.Forms.Cell.ContextActions)属性`Cell`。

`MenuItem` 定义了五个属性：

- [`Text`](xref:Xamarin.Forms.MenuItem.Text) 类型 `string`
- [`Icon`](xref:Xamarin.Forms.MenuItem.Icon) 类型 `FileImageSource`
- [`IsDestructive`](xref:Xamarin.Forms.MenuItem.IsDestructive) 类型 `bool`
- [`Command`](xref:Xamarin.Forms.MenuItem.Command) 类型 `ICommand`
- [`CommandParameter`](xref:Xamarin.Forms.MenuItem.CommandParameter) 类型 `object`

`Command`和`CommandParameter`属性表示每个项 ViewModel 包含用于执行所需的菜单命令的方法。 在非 MVVM 方案中，`MenuItem`还定义了[ `Clicked` ](xref:Xamarin.Forms.MenuItem.Clicked)事件。

[ **CellContextMenu** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/CellContextMenu)演示此技术。 `Command`每个属性`MenuItem`绑定到类型的属性`ICommand`中`Student`类。 设置`IsDestructive`属性设置为`true`为`MenuItem`，移除或删除所选的对象。

### <a name="varying-the-visuals"></a>不同的视觉对象

有时，可能需要稍有不同的视觉对象中的中的项`ListView`基于的属性。 例如，当学生的等级点平均值低于 2.0，时才[ **ColorCodedStudents** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ColorCodedStudents)示例以红色显示该学生的名称。
这通过使用绑定值转换器的实现[ `ThresholdToObjectConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ThresholdToObjectConverter.cs)，在[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库。

### <a name="refreshing-the-content"></a>刷新内容

`ListView`支持手势刷新其数据。 该程序必须设置[ `IsPullToRefresh` ](xref:Xamarin.Forms.ListView.IsPullToRefreshEnabled)属性设置为`true`要启用此功能。 `ListView`响应笔势通过设置其[ `IsRefreshing` ](xref:Xamarin.Forms.ListView.IsRefreshing)属性`true`，并通过引发[ `Refreshing` ](xref:Xamarin.Forms.ListView.Refreshing)事件和 （对于 MVVM 方案） 调用`Execute`方法其[ `RefreshCommand` ](xref:Xamarin.Forms.ListView.RefreshCommand)属性。

代码处理`Refresh`事件或`RefreshCommand`然后可能更新所显示的数据`ListView`，并设置`IsRefreshing`回`false`。

[ **RssFeed** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/RssFeed)示例演示如何使用[ `RssFeedViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/RssFeed/RssFeed/RssFeed/RssFeedViewModel.cs)实现`RefreshCommand`和`IsRefreshing`数据绑定属性。

## <a name="the-tableview-and-its-intents"></a>TableView 和其意向

虽然`ListView`通常显示的相同类型的多个实例[ `TableView` ](xref:Xamarin.Forms.TableView)通常侧重于各种类型的多个属性提供的用户界面。 每个项目均有其自身相关联[ `Cell` ](xref:Xamarin.Forms.Cell)派生的显示属性或提供给它的用户界面。

### <a name="properties-and-hierarchies"></a>属性和层次结构

`TableView` 定义只需四个属性：

- [`Intent`](xref:Xamarin.Forms.TableView.Intent) 类型的[ `TableIntent` ](xref:Xamarin.Forms.TableIntent)，枚举
- [`Root`](xref:Xamarin.Forms.TableView.Root) 类型的[ `TableRoot`](xref:Xamarin.Forms.TableRoot)的 content 属性 `TableView`
- [`RowHeight`](xref:Xamarin.Forms.TableView.RowHeight) 类型 `int`
- [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows) 类型 `bool`

`TableIntent`枚举指示想要使用`TableView`:

- [`Data`](xref:Xamarin.Forms.TableIntent.Data)
- [`Form`](xref:Xamarin.Forms.TableIntent.Form)
- [`Settings`](xref:Xamarin.Forms.TableIntent.Settings)
- [`Menu`](xref:Xamarin.Forms.TableIntent.Menu)

这些成员还建议的一些用途`TableView`。

定义表中，会涉及其他几个类：

- [`TableSectionBase`](xref:Xamarin.Forms.TableSectionBase) 是一个抽象类派生自`BindableObject`，并定义[ `Title` ](xref:Xamarin.Forms.TableSectionBase.Title)属性

- [`TableSectionBase<T>`](xref:Xamarin.Forms.TableSectionBase`1) 是一个抽象类派生自`TableSectionBase`并实现`IList<T>`和 `INotifyCollectionChanged`

- [`TableSection`](xref:Xamarin.Forms.TableSection) 派生自 `TableSectionBase<Cell>`

- [`TableRoot`](xref:Xamarin.Forms.TableRoot) 派生自 `TableSectionBase<TableSection>`

简单地说，`TableView`已`Root`属性设置为`TableRoot`对象，它是集合的`TableSection`对象，其中每个包含一系列`Cell`对象。 表具有多个部分，并且每个部分具有多个单元格。 表本身可以有一个标题，并且每个部分可以具有一个标题。 尽管`TableView`利用`Cell`派生类，它不会使利用`DataTemplate`。

### <a name="a-prosaic-form"></a>乏味的窗体

[ **EntryForm** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/EntryForm)示例定义[ `PersonalInformation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs)视图模型，它的实例将成为`BindingContext`的`TableView`。 每个`Cell`中派生其`TableSection`随后可以绑定到的属性`PersonalInformation`类。

### <a name="custom-cells"></a>自定义单元格

[ **ConditionalCells** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalCells)示例扩展**EntryForm**。 [ `ProgrammerInformation` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter19/EntryForm/EntryForm/EntryForm/PersonalInformation.cs)类包含一个布尔属性，控制两个附加属性的适用性。 这两个其他属性，该程序使用自定义`PickerCell`基于[PickerCell.xaml](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml)并[PickerCell.xaml.cs](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/PickerCell.xaml.cs)中[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库。

尽管`IsEnabled`的两个属性`PickerCell`元素绑定到中的布尔属性`ProgrammerInformation`，这种方法似乎不起作用，此时会提示下一个示例。

### <a name="conditional-sections"></a>条件部分

[ **ConditionalSection** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/ConditionalSection)示例会将在单独的布尔值项的所选条件的两个项`TableSection`。 代码隐藏文件中删除此部分从`TableView`或将它返回根据布尔值属性添加。

### <a name="a-tableview-menu"></a>TableView 菜单

另一个用途`TableView`是一个菜单。 [ **MenuCommands** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19/MenuCommands)示例演示一个菜单，让您可以将稍有移动`BoxView`在屏幕上四处。

## <a name="related-links"></a>相关链接

- [第 19 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch19-Apr2016.pdf)
- [第 19 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter19)
- [选取器](~/xamarin-forms/user-interface/picker/index.md)
- [ListView](~/xamarin-forms/user-interface/listview/index.md)
- [TableView](~/xamarin-forms/user-interface/tableview.md)
