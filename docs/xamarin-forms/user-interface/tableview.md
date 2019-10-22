---
title: Xamarin. Forms TableView
description: 本文介绍如何使用 Xamarin TableView 类在应用程序中呈现滚动菜单、设置和输入窗体。
ms.prod: xamarin
ms.assetid: D1619D19-A74F-40DF-8E53-B1B7DFF7A3FB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/25/2019
ms.openlocfilehash: 67625aa413880023cce6d3e5e21e4d3bd0ec8e4c
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72695982"
---
# <a name="xamarinforms-tableview"></a>Xamarin. Forms TableView

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-tableview)

[`TableView`](xref:Xamarin.Forms.TableView)是一个视图，用于显示数据的可滚动列表或可选择的行，这些行不共享同一模板。 与[ListView](~/xamarin-forms/user-interface/listview/index.md)不同，`TableView` 没有 `ItemsSource` 的概念，因此必须手动将项添加为子级。

![TableView 示例](tableview-images/tableview-all-sml.png)

<a name="Use_Cases" />

## <a name="use-cases"></a>用例

[`TableView`](xref:Xamarin.Forms.TableView)在以下情况中非常有用：

- 显示设置的列表，
- 收集窗体中的数据，或
- 显示与行和行不同的显示数据（例如数字、百分比和图像）。

[`TableView`](xref:Xamarin.Forms.TableView)处理引人注目的部分中的行滚动和布局，这是上述方案的常见需求。 @No__t_0 控件使用每个平台的基础等效视图（如果有），从而为每个平台创建本机查找。

<a name="TableView_Structure" />

## <a name="structure"></a>结构

[@No__t_1](xref:Xamarin.Forms.TableView)中的元素组织为多个部分。 @No__t_0 的根是[`TableRoot`](xref:Xamarin.Forms.TableRoot)，它是一个或多个[`TableSection`](xref:Xamarin.Forms.TableSection)实例的父级。 每个[`TableSection`](xref:Xamarin.Forms.TableSection)包含一个标题和一个或多个[`ViewCell`](xref:Xamarin.Forms.ViewCell)实例：

```xaml
<TableView Intent="Settings">
    <TableRoot>
        <TableSection Title="Ring">
            <SwitchCell Text="New Voice Mail" />
            <SwitchCell Text="New Mail" On="true" />
        </TableSection>
    </TableRoot>
</TableView>
```

等效 C# 代码如下：

```csharp
Content = new TableView
{
    Root = new TableRoot
    {
        new TableSection("Ring")
        {
          // TableSection constructor takes title as an optional parameter
          new SwitchCell { Text = "New Voice Mail" },
          new SwitchCell { Text = "New Mail", On = true }
        }
    },
    Intent = TableIntent.Settings
};
```

<a name="TableView_Appearance" />

## <a name="appearance"></a>外观

[`TableView`](xref:Xamarin.Forms.TableView)公开[`Intent`](xref:Xamarin.Forms.TableView.Intent)属性，该属性可以设置为任何[`TableIntent`](xref:Xamarin.Forms.TableIntent)枚举成员：

- `Data` –在显示数据条目时使用。 请注意， [ListView](~/xamarin-forms/user-interface/listview/index.md)对于滚动数据列表可能是更好的选择。
- `Form` –当 TableView 作为窗体时使用。
- `Menu` –在显示选择菜单时使用。
- `Settings` –在显示配置设置列表时使用。

选择的[`TableIntent`](xref:Xamarin.Forms.TableIntent)值可能会影响[`TableView`](xref:Xamarin.Forms.TableView)在每个平台上的显示方式。 即使没有明显的差异，最佳做法也是选择最符合您要使用表的方式的 `TableIntent`。

此外，通过将 `TextColor` 属性设置为[`Color`](xref:Xamarin.Forms.Color)，可以更改为每个[`TableSection`](xref:Xamarin.Forms.TableSection)显示的文本颜色。

<a name="Built-In_Cells" />

## <a name="built-in-cells"></a>内置单元

Xamarin. Forms 附带了用于收集和显示信息的内置单元。 尽管[`ListView`](xref:Xamarin.Forms.ListView)和[`TableView`](xref:Xamarin.Forms.TableView)可以使用所有相同的单元格， [`SwitchCell`](xref:Xamarin.Forms.SwitchCell)和[`EntryCell`](xref:Xamarin.Forms.EntryCell)对于 `TableView` 方案最为相关。

有关[TextCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#textcell)和[ImageCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#imagecell)的详细说明，请参阅[ListView 单元格外观](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)。

<a name="switchcell" />

### <a name="switchcell"></a>SwitchCell

[`SwitchCell`](xref:Xamarin.Forms.SwitchCell)是用于显示和捕获开启/关闭或 `true` / `false` 状态的控件。 它定义以下属性：

- `Text` –要在开关旁显示的文本。
- `On` –开关是显示为打开还是关闭。
- `OnColor` –交换机处于开启位置时的[`Color`](xref:Xamarin.Forms.Color) 。

所有这些属性都是可绑定属性。

[`SwitchCell`](xref:Xamarin.Forms.SwitchCell)还公开了 `OnChanged` 事件，使您可以对单元格状态的更改做出响应。

![SwitchCell 示例](tableview-images/switch-cell.png)

<a name="entrycell" />

### <a name="entrycell"></a>EntryCell

当您需要显示用户可编辑的文本数据时， [`EntryCell`](xref:Xamarin.Forms.EntryCell)非常有用。 它定义以下属性：

- `Keyboard` –编辑时要显示的键盘。 其中包括数值、电子邮件、电话号码等选项，[请参阅 API 文档](xref:Xamarin.Forms.Keyboard)。
- `Label` –要显示在文本输入字段左侧的标签文本。
- `LabelColor` –标签文本的颜色。
- `Placeholder` –当输入字段为 null 或为空时显示的文本。 此文本在文本输入开始时消失。
- `Text` – "输入" 字段中的文本。
- `HorizontalTextAlignment` –文本的水平对齐方式。 值为居中、左对齐或右对齐。 [请参阅 API 文档](xref:Xamarin.Forms.TextAlignment)。
- `VerticalTextAlignment` –文本的垂直对齐方式。 值为 `Start`、`Center` 或 `End`。

[`EntryCell`](xref:Xamarin.Forms.EntryCell)还公开了 `Completed` 事件，当用户在编辑文本时在键盘上的 "完成" 按钮时激发。

![EntryCell 示例](tableview-images/entry-cell.png)

<a name="Custom_Cells" />

## <a name="custom-cells"></a>自定义单元

当内置单元格不够时，可使用自定义单元以对应用有意义的方式呈现和捕获数据。 例如，你可能想要显示一个滑块，以允许用户选择图像的不透明度。

所有自定义单元必须派生自[`ViewCell`](xref:Xamarin.Forms.ViewCell)，这是所有内置单元类型使用的相同基类。

下面是自定义单元格的示例：

![自定义单元格示例](tableview-images/custom-cell.png)

下面的示例演示了在上面的屏幕截图中创建[`TableView`](xref:Xamarin.Forms.TableView)所用的 XAML：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DemoTableView.TablePage"
             Title="TableView">
      <TableView Intent="Settings">
          <TableRoot>
              <TableSection Title="Getting Started">
                  <ViewCell>
                      <StackLayout Orientation="Horizontal">
                          <Image Source="bulb.png" />
                          <Label Text="left"
                                 TextColor="#f35e20" />
                          <Label Text="right"
                                 HorizontalOptions="EndAndExpand"
                                 TextColor="#503026" />
                      </StackLayout>
                  </ViewCell>
              </TableSection>
          </TableRoot>
      </TableView>
</ContentPage>
```

等效 C# 代码如下：

```csharp
var table = new TableView();
table.Intent = TableIntent.Settings;
var layout = new StackLayout() { Orientation = StackOrientation.Horizontal };
layout.Children.Add (new Image() { Source = "bulb.png"});
layout.Children.Add (new Label()
{
    Text = "left",
    TextColor = Color.FromHex("#f35e20"),
    VerticalOptions = LayoutOptions.Center
});
layout.Children.Add (new Label ()
{
    Text = "right",
    TextColor = Color.FromHex ("#503026"),
    VerticalOptions = LayoutOptions.Center,
    HorizontalOptions = LayoutOptions.EndAndExpand
});
table.Root = new TableRoot ()
{
    new TableSection("Getting Started")
    {
        new ViewCell() {View = layout}
    }
};
Content = table;
```

[@No__t_1](xref:Xamarin.Forms.TableView)下的根元素是[`TableRoot`](xref:Xamarin.Forms.TableRoot)，在 `TableRoot` 的紧下方有一个[`TableSection`](xref:Xamarin.Forms.TableSection) 。 [@No__t_1](xref:Xamarin.Forms.ViewCell)直接在 `TableSection` 下定义，而[`StackLayout`](xref:Xamarin.Forms.StackLayout)用于管理自定义单元格的布局，尽管可以在此处使用任何布局。

> [!NOTE]
> 与[`ListView`](xref:Xamarin.Forms.ListView)不同， [`TableView`](xref:Xamarin.Forms.TableView)不需要在 `ItemTemplate` 中定义自定义（或任何）单元。

## <a name="row-height"></a>行高

[@No__t_1](xref:Xamarin.Forms.TableView)类有两个属性，可用于更改单元格的行高：

- [`RowHeight`](xref:Xamarin.Forms.TableView.RowHeight) –将每个行的高度设置为 `int`。
- [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows) –如果设置为 `true`，则行具有不同的高度。 请注意，在将此属性设置为 `true` 时，将自动计算行高度并由 Xamarin 应用。

更改[`TableView`](xref:Xamarin.Forms.TableView)中单元格的内容高度时，行高会在 Android 和通用 WINDOWS 平台（UWP）上隐式更新。 但是，在 iOS 上，必须通过将[`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows)属性设置为 `true` 并调用[`Cell.ForceUpdateSize`](xref:Xamarin.Forms.Cell.ForceUpdateSize)方法来强制更新。

下面的 XAML 示例显示了一个包含[`ViewCell`](xref:Xamarin.Forms.ViewCell)的[`TableView`](xref:Xamarin.Forms.TableView) ：

```xaml
<ContentPage ...>
    <TableView ...
               HasUnevenRows="true">
        <TableRoot>
            ...
            <TableSection ...>
                ...
                <ViewCell x:Name="_viewCell"
                          Tapped="OnViewCellTapped">
                    <Grid Margin="15,0">
                        <Grid.RowDefinitions>
                            <RowDefinition Height="Auto" />
                            <RowDefinition Height="Auto" />
                        </Grid.RowDefinitions>
                        <Label Text="Tap this cell." />
                        <Label x:Name="_target"
                               Grid.Row="1"
                               Text="The cell has changed size."
                               IsVisible="false" />
                    </Grid>
                </ViewCell>
            </TableSection>
        </TableRoot>
    </TableView>
</ContentPage>
```

点击[`ViewCell`](xref:Xamarin.Forms.ViewCell)时，将执行 `OnViewCellTapped` 事件处理程序：

```csharp
void OnViewCellTapped(object sender, EventArgs e)
{
    _target.IsVisible = !_target.IsVisible;
    _viewCell.ForceUpdateSize();
}
```

@No__t_0 事件处理程序显示或隐藏[`ViewCell`](xref:Xamarin.Forms.ViewCell)中的第二个[`Label`](xref:Xamarin.Forms.Label) ，并通过调用[`Cell.ForceUpdateSize`](xref:Xamarin.Forms.Cell.ForceUpdateSize)方法显式更新该单元格的大小。

下面的屏幕截图显示了在点击之前的单元格：

![调整大小之前的 ViewCell](tableview-images/cell-beforeresize.png)

下面的屏幕截图显示了点击后的单元格：

![调整大小后的 ViewCell](tableview-images/cell-afterresize.png)

> [!IMPORTANT]
> 如果此功能过度使用，则可能会导致性能下降。

## <a name="related-links"></a>相关链接

- [TableView （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-tableview)
