---
title: ListView 交互性
description: 此文章介绍了如何通过实现选择、 上下文操作和下拉刷新到 Xamarin.Forms ListView 添加交互性。
ms.prod: xamarin
ms.assetid: CD14EB90-B08C-4E8F-A314-DA0EEC76E647
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/27/2019
ms.openlocfilehash: 3949dd85492a8181ee53e23b3ba2e986e59f8f47
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70121626"
---
# <a name="listview-interactivity"></a>ListView 交互性

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)

[`ListView`](xref:Xamarin.Forms.ListView)支持与它所呈现的数据进行交互。

<a name="selectiontaps" />

## <a name="selection--taps"></a>选择和分流点

[ `ListView` ](xref:Xamarin.Forms.ListView)通过设置控制选择模式[ `ListView.SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode)属性的值[ `ListViewSelectionMode` ](xref:Xamarin.Forms.ListViewSelectionMode)枚举：

- [`Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single) 指示可以将选择单个项目，而被突出显示的选定项。 这是默认值。
- [`None`](xref:Xamarin.Forms.ListViewSelectionMode.None) 指示不能选择项。

当用户点击某个项时，会触发两个事件：

- [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 当选择了新项时激发。
- [`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped) 点击某个项时触发。

两次点击相同项会触发两个[ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped)事件，但将只触发单个[ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected)事件。

> [!NOTE]
> [`Item`](xref:Xamarin.Forms.ItemTappedEventArgs.Item) [`Group`](xref:Xamarin.Forms.ItemTappedEventArgs.Group) `ItemIndex` [`ListView`](xref:Xamarin.Forms.ListView)类, 它包含[`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped)事件的事件参数, 具有和属性, 并且其值表示点击项的中的索引。 [`ItemTappedEventArgs`](xref:Xamarin.Forms.ItemTappedEventArgs) [`SelectedItemChangedEventArgs`](xref:Xamarin.Forms.SelectedItemChangedEventArgs)同样, `ListView`包含[`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected)事件的事件参数的类具有[`SelectedItem`](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem)属性和`SelectedItemIndex`属性, 其值表示所选项的中的索引。

当[ `SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode)属性设置为[ `Single`](xref:Xamarin.Forms.ListViewSelectionMode.Single)中的项[ `ListView` ](xref:Xamarin.Forms.ListView)可以选择[ `ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected)并[ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped)将不再触发事件，并且[ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem)属性将设置为所选的项的值。

当[ `SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode)属性设置为[ `None`](xref:Xamarin.Forms.ListViewSelectionMode.None)中的项[ `ListView` ](xref:Xamarin.Forms.ListView)不能选择[ `ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected)将不会触发事件，并[ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem)属性将保持`null`。 但是， [ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped)仍将触发事件和项目将会短暂地突出显示分流期间。

当选择项并[ `SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode)属性更改从[ `Single` ](xref:Xamarin.Forms.ListViewSelectionMode.Single)到[ `None` ](xref:Xamarin.Forms.ListViewSelectionMode.None)，则[ `SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem)属性将设置为`null`并[ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected)事件将触发与`null`项。

下面的屏幕截图演示[ `ListView` ](xref:Xamarin.Forms.ListView)与默认选择模式：

![](interactivity-images/selection-default.png "与所选内容启用的 ListView")

### <a name="disabling-selection"></a>禁用所选内容

若要禁用[ `ListView` ](xref:Xamarin.Forms.ListView)选集[ `SelectionMode` ](xref:Xamarin.Forms.ListView.SelectionMode)属性设置为[ `None` ](xref:Xamarin.Forms.ListViewSelectionMode.None):

```xaml
<ListView ... SelectionMode="None" />
```

```csharp
var listView = new ListView { ... SelectionMode = ListViewSelectionMode.None };
```

<a name="Context_Actions" />

## <a name="context-actions"></a>上下文操作

通常情况下，用户将需要执行操作中的项`ListView`。 例如，请考虑在邮件应用中的电子邮件的列表。 在 iOS 上，您可以轻扫来删除一条消息::

![](interactivity-images/context-default.png "ListView 的上下文操作")

可以在 C# 和 XAML 实现上下文操作。 下面您会发现特定的指南的同时，但首先让我们看一看一些关键实现细节两个。

使用创建上下文操作`MenuItem`s。 菜单项的点击事件会引发 MenuItem 本身，而不 ListView。 这是不同于如何将点击事件处理对于 ListView 其中引发事件而不是该单元格的单元格。 因为 ListView 引发事件，其事件处理程序是给定密钥的信息，如其中选择或点击项。

默认情况下，菜单项具有无法知道它属于哪个单元格。 `CommandParameter` 可在上找到`MenuItem`来存储对象，如后面的 MenuItem ViewCell 对象。 `CommandParameter` 可以在 XAML 和 C# 中设置。

### <a name="c"></a>C\#

可以在任何实现上下文操作`Cell`子类 （只要它不被用作组标头） 通过创建`MenuItem`s 并将它们添加到`ContextActions`单元格的集合。 具有以下属性可配置的上下文操作：

- **文本**&ndash;菜单项中显示的字符串。
- **单击**&ndash;时单击项的事件。
- **IsDestructive** &ndash; （可选） 为 true 时呈现的项是以不同的方式在 iOS 上。

多个上下文操作可以添加到单元格，，但是只有一个应有`IsDestructive`设置为`true`。 下面的代码演示如何上下文操作将添加到`ViewCell`:

```csharp
var moreAction = new MenuItem { Text = "More" };
moreAction.SetBinding (MenuItem.CommandParameterProperty, new Binding ("."));
moreAction.Clicked += async (sender, e) => {
    var mi = ((MenuItem)sender);
    Debug.WriteLine("More Context Action clicked: " + mi.CommandParameter);
};

var deleteAction = new MenuItem { Text = "Delete", IsDestructive = true }; // red background
deleteAction.SetBinding (MenuItem.CommandParameterProperty, new Binding ("."));
deleteAction.Clicked += async (sender, e) => {
    var mi = ((MenuItem)sender);
    Debug.WriteLine("Delete Context Action clicked: " + mi.CommandParameter);
};
// add to the ViewCell's ContextActions property
ContextActions.Add (moreAction);
ContextActions.Add (deleteAction);
```

### <a name="xaml"></a>XAML

`MenuItem`s 也可以创建 XAML 集合中以声明方式。 以下 XAML 演示自定义单元格包含两个实现的上下文操作：

```xaml
<ListView x:Name="ContextDemoList">
  <ListView.ItemTemplate>
    <DataTemplate>
      <ViewCell>
         <ViewCell.ContextActions>
            <MenuItem Clicked="OnMore" CommandParameter="{Binding .}"
               Text="More" />
            <MenuItem Clicked="OnDelete" CommandParameter="{Binding .}"
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

在代码隐藏文件中，确保`Clicked`方法实现：

```csharp
public void OnMore (object sender, EventArgs e) {
    var mi = ((MenuItem)sender);
    DisplayAlert("More Context Action", mi.CommandParameter + " more context action", "OK");
}

public void OnDelete (object sender, EventArgs e) {
    var mi = ((MenuItem)sender);
    DisplayAlert("Delete Context Action", mi.CommandParameter + " delete context action", "OK");
}
```

> [!NOTE]
> `NavigationPageRenderer`适用于 Android 可重写`UpdateMenuItemIcon`方法，可以用来从自定义加载图标`Drawable`。 此重写就可以使用 SVG 图像以图标形式在`MenuItem`Android 上的实例。

<a name="Pull_to_Refresh" />

## <a name="pull-to-refresh"></a>下拉以刷新

用户都希望能拉下在列表中的数据将刷新该列表。 [`ListView`](xref:Xamarin.Forms.ListView)支持此项。 若要启用请求刷新功能, 请将设置[`IsPullToRefreshEnabled`](xref:Xamarin.Forms.ListView.IsPullToRefreshEnabled)为`true`:

```xaml
<ListView ...
          IsPullToRefreshEnabled="true" />
```

等效 C# 代码如下：

```csharp
listView.IsPullToRefreshEnabled = true;
```

在刷新过程中会出现一个微调框, 默认情况下为黑色。 但是, 可以通过将`RefreshControlColor`属性设置[`Color`](xref:Xamarin.Forms.Color)为, 在 iOS 和 Android 上更改微调按钮颜色:

```xaml
<ListView ...
          IsPullToRefreshEnabled="true"
          RefreshControlColor="Red" />
```

等效 C# 代码如下：

```csharp
listView.RefreshControlColor = Color.Red;
```

以下屏幕截图显示了在用户请求时的请求刷新:

![](interactivity-images/refresh-start.png "ListView 下拉以刷新正在进行中")

下面的屏幕截图显示了在用户释放请求之后的请求刷新, 并在更新时[`ListView`](xref:Xamarin.Forms.ListView)显示微调框:

![](interactivity-images/refresh-in-progress.png "ListView 请求刷新完成")

[`ListView`](xref:Xamarin.Forms.ListView)触发事件以启动刷新, [`IsRefreshing`](xref:Xamarin.Forms.ListView.IsRefreshing)并将属性设置为`true`。 [`Refreshing`](xref:Xamarin.Forms.ListView.Refreshing) 若要刷新的内容所需的`ListView`任何代码, 则应由`Refreshing`事件的事件处理程序或执行[`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand)的方法执行。 刷新后, 应将`false` `IsRefreshing` [属性`EndRefresh`](xref:Xamarin.Forms.ListView.EndRefresh)设置为, 或调用方法以指示刷新已完成。 `ListView`

> [!NOTE]
> 定义[`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand)时`CanExecute` , 可以指定命令的方法以启用或禁用该命令。

## <a name="related-links"></a>相关链接

- [ListView 交互性 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-interactivity)
