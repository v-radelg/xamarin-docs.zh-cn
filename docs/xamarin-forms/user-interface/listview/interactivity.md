---
title: ListView 交互
description: 本文介绍如何通过实现选择、上下文操作和请求刷新来向 Xamarin 中添加交互。
ms.prod: xamarin
ms.assetid: CD14EB90-B08C-4E8F-A314-DA0EEC76E647
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/25/2019
ms.openlocfilehash: aa717792bdaefe24d957c9781934933b67aaf92b
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696871"
---
# <a name="listview-interactivity"></a>ListView 交互

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)

Xamarin [`ListView`](xref:Xamarin.Forms.ListView)类支持用户与它所呈现的数据进行交互。

## <a name="selection-and-taps"></a>选择和点击

通过将[`ListView.SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode)属性设置为[`ListViewSelectionMode`](xref:Xamarin.Forms.ListViewSelectionMode)枚举的值，可以控制[`ListView`](xref:Xamarin.Forms.ListView)选择模式：

- [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single)指示可以选择单个项，突出显示选定项。 这是默认值。
- [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None)指示无法选择项。

当用户点击某项时，将触发两个事件：

- 选择新项时触发[`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 。
- 点击项时[`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped)激发。

点击同一项两次将引发两个[`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped)事件，但只引发单个[`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected)事件。

> [!NOTE]
> [`ItemTappedEventArgs`](xref:Xamarin.Forms.ItemTappedEventArgs)类，其中包含[`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped)事件的事件参数，具有[`Group`](xref:Xamarin.Forms.ItemTappedEventArgs.Group)和[`Item`](xref:Xamarin.Forms.ItemTappedEventArgs.Item)属性，其值表示在攻丝的[`ItemIndex`](xref:Xamarin.Forms.ListView)中的索引。内容. 同样，包含[`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected)事件的事件参数的[`SelectedItemChangedEventArgs`](xref:Xamarin.Forms.SelectedItemChangedEventArgs)类具有[`SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem)属性 `SelectedItemIndex`，其值表示所选项的 `ListView` 中的索引。

[`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode)属性设置为[`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single)时，可以选择[`ListView`](xref:Xamarin.Forms.ListView)中的项，将触发[`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected)和[`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped)事件，并且[`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem)属性将设置为选定的项。

当[`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode)属性设置为[`None`](xref:Xamarin.Forms.ListViewSelectionMode.None)时，不能选择[`ListView`](xref:Xamarin.Forms.ListView)中的项，不会激发[`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected)事件， [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem)属性仍将保持为 `null`。 但是，仍会激发[`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped)事件，并在点击过程中会短暂突出显示分项。

如果已选择某项并且[`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode)属性从[`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single)更改为[`None`](xref:Xamarin.Forms.ListViewSelectionMode.None)，则[`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem)属性将设置为 `null` 并将使用`ItemSelected`项激发[`null`](xref:Xamarin.Forms.ListView.ItemSelected)事件。

以下屏幕截图显示了默认选择模式下的[`ListView`](xref:Xamarin.Forms.ListView) ：

![](interactivity-images/selection-default.png "ListView with Selection Enabled")

### <a name="disable-selection"></a>禁用选定内容

若要禁用[`ListView`](xref:Xamarin.Forms.ListView)选择，请将[`SelectionMode`](xref:Xamarin.Forms.ListView.SelectionMode)属性设置为[`None`](xref:Xamarin.Forms.ListViewSelectionMode.None)：

```xaml
<ListView ... SelectionMode="None" />
```

```csharp
var listView = new ListView { ... SelectionMode = ListViewSelectionMode.None };
```

## <a name="context-actions"></a>上下文操作

通常，用户需要对 `ListView`中的项执行操作。 例如，请考虑邮件应用中的电子邮件列表。 在 iOS 上，可以通过轻扫来删除消息：

![](interactivity-images/context-default.png "ListView with Context Actions")

上下文操作可以在和 XAML C#中实现。 下面你会找到这两种方案的具体指南，但首先我们来了解一下这两种情况的一些关键实现细节。

上下文操作是使用 `MenuItem` 元素创建的。 点击 `MenuItem` 本身引发 `MenuItems` 对象的事件，而不是 `ListView`。 这不同于对单元格的点击事件处理方式，其中 `ListView` 引发事件而不是单元。 由于 `ListView` 引发事件，因此会为其事件处理程序提供键信息，如选择或点击的项。

默认情况下，`MenuItem` 无法了解它属于哪个单元格。 `CommandParameter` 属性 `MenuItem` 可用于存储对象，如 `MenuItem``ViewCell`后面的对象。 `CommandParameter` 属性可以同时在 XAML 和C#中设置。

### <a name="xaml"></a>XAML

可以在 XAML 集合中创建 `MenuItem` 元素。 下面的 XAML 演示了一个自定义单元格，其中实现了两个上下文操作：

```xaml
<ListView x:Name="ContextDemoList">
  <ListView.ItemTemplate>
    <DataTemplate>
      <ViewCell>
         <ViewCell.ContextActions>
            <MenuItem Clicked="OnMore"
                      CommandParameter="{Binding .}"
                      Text="More" />
            <MenuItem Clicked="OnDelete"
                      CommandParameter="{Binding .}"
                      Text="Delete" IsDestructive="True" />
         </ViewCell.ContextActions>
         <StackLayout Padding="15,0">
              <Label Text="{Binding title}" />
         </StackLayout>
      </ViewCell>
    </DataTemplate>
  </ListView.ItemTemplate>
</ListView>
```

在代码隐藏文件中，确保已实现 `Clicked` 方法：

```csharp
public void OnMore (object sender, EventArgs e)
{
    var mi = ((MenuItem)sender);
    DisplayAlert("More Context Action", mi.CommandParameter + " more context action", "OK");
}

public void OnDelete (object sender, EventArgs e)
{
    var mi = ((MenuItem)sender);
    DisplayAlert("Delete Context Action", mi.CommandParameter + " delete context action", "OK");
}
```

> [!NOTE]
> 适用于 Android 的 `NavigationPageRenderer` 具有可重写的 `UpdateMenuItemIcon` 方法，该方法可用于从自定义 `Drawable`加载图标。 通过此重写，可以在 Android 上的 `MenuItem` 实例中使用 SVG 图像作为图标。

### <a name="code"></a>代码

通过创建 `MenuItem` 实例并将其添加到单元格的 `ContextActions` 集合，可以在任何 `Cell` 子类中实现上下文操作（前提是它不用作组标头）。 您可以为上下文操作配置以下属性：

- &ndash; 菜单项中显示的字符串的**文本**。
- **单击该项时，&ndash; 事件**。
- **IsDestructive** &ndash; （可选）如果为 true，则在 iOS 上以不同的方式呈现项。

可以将多个上下文操作添加到一个单元，但只有一个可以将 `IsDestructive` 设置为 `true`。 下面的代码演示如何将上下文操作添加到 `ViewCell`：

```csharp
var moreAction = new MenuItem { Text = "More" };
moreAction.SetBinding (MenuItem.CommandParameterProperty, new Binding ("."));
moreAction.Clicked += async (sender, e) =>
{
    var mi = ((MenuItem)sender);
    Debug.WriteLine("More Context Action clicked: " + mi.CommandParameter);
};

var deleteAction = new MenuItem { Text = "Delete", IsDestructive = true }; // red background
deleteAction.SetBinding (MenuItem.CommandParameterProperty, new Binding ("."));
deleteAction.Clicked += async (sender, e) =>
{
    var mi = ((MenuItem)sender);
    Debug.WriteLine("Delete Context Action clicked: " + mi.CommandParameter);
};
// add to the ViewCell's ContextActions property
ContextActions.Add (moreAction);
ContextActions.Add (deleteAction);
```

## <a name="pull-to-refresh"></a>请求刷新

用户希望向下下拉数据列表将刷新该列表。 [`ListView`](xref:Xamarin.Forms.ListView)控件支持此项。 若要启用请求刷新功能，请将[`IsPullToRefreshEnabled`](xref:Xamarin.Forms.ListView.IsPullToRefreshEnabled)设置为 `true`：

```xaml
<ListView ...
          IsPullToRefreshEnabled="true" />
```

等效 C# 代码如下：

```csharp
listView.IsPullToRefreshEnabled = true;
```

在刷新过程中会出现一个微调框，默认情况下为黑色。 但是，可以通过将 `RefreshControlColor` 属性设置为[`Color`](xref:Xamarin.Forms.Color)，在 IOS 和 Android 上更改微调按钮颜色：

```xaml
<ListView ...
          IsPullToRefreshEnabled="true"
          RefreshControlColor="Red" />
```

等效 C# 代码如下：

```csharp
listView.RefreshControlColor = Color.Red;
```

以下屏幕截图显示了在用户请求时的请求刷新：

![](interactivity-images/refresh-start.png "ListView Pull to Refresh In-Progress")

下面的屏幕截图显示了在用户释放请求之后的请求刷新，并在[`ListView`](xref:Xamarin.Forms.ListView)更新时显示微调框：

![](interactivity-images/refresh-in-progress.png "ListView Pull to Refresh Complete")

[`ListView`](xref:Xamarin.Forms.ListView)激发[`Refreshing`](xref:Xamarin.Forms.ListView.Refreshing)事件以启动刷新， [`IsRefreshing`](xref:Xamarin.Forms.ListView.IsRefreshing)属性将设置为 `true`。 若要刷新 `ListView` 内容所需的任何代码，则应由 `Refreshing` 事件的事件处理程序或由[`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand)执行的方法执行。 刷新 `ListView` 后，`IsRefreshing` 属性应设置为 `false`，或者应调用[`EndRefresh`](xref:Xamarin.Forms.ListView.EndRefresh)方法，以指示刷新已完成。

> [!NOTE]
> 定义[`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand)时，可以指定命令的 `CanExecute` 方法以启用或禁用该命令。

## <a name="detect-scrolling"></a>检测滚动

[`ListView`](xref:Xamarin.Forms.ListView)定义一个 `Scrolled` 事件，该事件将激发以指示发生滚动。 下面的 XAML 示例显示了一个 `ListView`，该为 `Scrolled` 事件设置事件处理程序：

```xaml
<ListView Scrolled="OnListViewScrolled">
    ...
</ListView>
```

等效 C# 代码如下：

```csharp
ListView listView = new ListView();
listView.Scrolled += OnListViewScrolled;
```

在此代码示例中，当引发 `Scrolled` 事件时，将执行 `OnListViewScrolled` 事件处理程序：

```csharp
void OnListViewScrolled(object sender, ScrolledEventArgs e)
{
    Debug.WriteLine("ScrollX: " + e.ScrollX);
    Debug.WriteLine("ScrollY: " + e.ScrollY);  
}
```

`OnListViewScrolled` 事件处理程序输出事件随附的 `ScrolledEventArgs` 对象的值。

## <a name="related-links"></a>相关链接

- [ListView 交互（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)
