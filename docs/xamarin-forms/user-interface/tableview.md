---
title: Xamarin.Forms TableView
description: 此文章介绍了如何使用 Xamarin.Forms TableView 类在应用程序中显示滚动菜单、 设置和输入窗体。
ms.prod: xamarin
ms.assetid: D1619D19-A74F-40DF-8E53-B1B7DFF7A3FB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/14/2018
ms.openlocfilehash: fc3837cd0d69d49b9939b04da667010aac919fe2
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657125"
---
# <a name="xamarinforms-tableview"></a>Xamarin.Forms TableView

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-tableview)

[`TableView`](xref:Xamarin.Forms.TableView)是一个视图, 用于显示数据的可滚动列表或可选择的不共享同一模板的行。 与[ListView](~/xamarin-forms/user-interface/listview/index.md)不同`TableView` , 没有的概念`ItemsSource`, 因此必须手动将项添加为子级。

![](tableview-images/tableview-all-sml.png "TableView 示例")

<a name="Use_Cases" />

## <a name="use-cases"></a>用例

[`TableView`](xref:Xamarin.Forms.TableView)在以下情况中非常有用:

- 显示一系列的设置，
- 在窗体中收集数据或
- 显示呈现的数据以不同的方式从行与行 （例如数字、 百分比和图像）。

[`TableView`](xref:Xamarin.Forms.TableView)处理引人注目的部分中的行滚动和布局, 这是上述方案的常见需求。 `TableView`控件使用每个平台的基础等效的视图时可用，创建每个平台的本机外观。

<a name="TableView_Structure" />

## <a name="structure"></a>结构

中的[`TableView`](xref:Xamarin.Forms.TableView)元素组织为多个节。 的根`TableView` [`TableRoot`](xref:Xamarin.Forms.TableRoot)为, 它是一个或多个[`TableSection`](xref:Xamarin.Forms.TableSection)实例的父级。 每[`TableSection`](xref:Xamarin.Forms.TableSection)个都包含一个标题和一个或[`ViewCell`](xref:Xamarin.Forms.ViewCell)多个实例:

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

[`TableView`](xref:Xamarin.Forms.TableView)公开属性, 该属性可以设置为任何[`TableIntent`](xref:Xamarin.Forms.TableIntent)枚举成员: [`Intent`](xref:Xamarin.Forms.TableView.Intent)

- `Data`–在显示数据项时使用。 请注意， [ListView](~/xamarin-forms/user-interface/listview/index.md)可能是更好的选择进行滚动数据的列表。
- `Form`–当 TableView 充当窗体时使用。
- `Menu`–用于在显示选择菜单时使用。
- `Settings`–在显示配置设置列表时使用。

你[`TableIntent`](xref:Xamarin.Forms.TableIntent)选择的值可能会影响每[`TableView`](xref:Xamarin.Forms.TableView)个平台上显示的方式。 即使有不清除差异，它是最佳选择`TableIntent`的最匹配你想要使用表。

此外, 通过`TextColor`将属性设置为[`Color`](xref:Xamarin.Forms.Color), 可以更改为[`TableSection`](xref:Xamarin.Forms.TableSection)每个显示的文本的颜色。

<a name="Built-In_Cells" />

## <a name="built-in-cells"></a>内置单元

Xamarin.Forms 提供了内置的单元格，以收集和显示信息。 虽然[`ListView`](xref:Xamarin.Forms.ListView) [`SwitchCell`](xref:Xamarin.Forms.SwitchCell)和[`TableView`](xref:Xamarin.Forms.TableView)可以使用所有相同的单元, [`EntryCell`](xref:Xamarin.Forms.EntryCell)但与`TableView`方案最为相关。

请参阅[ListView 单元格的外观](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)有关的详细说明[TextCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#TextCell)并[ImageCell](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#ImageCell)。

<a name="switchcell" />

### <a name="switchcell"></a>SwitchCell

[`SwitchCell`](xref:Xamarin.Forms.SwitchCell) 是要用于显示和捕获的控件打开/关闭或`true` / `false`状态。 它定义以下属性:

- `Text`–要在开关旁显示的文本。
- `On`–开关是显示为打开还是关闭。
- `OnColor`[`Color`](xref:Xamarin.Forms.Color) –交换机处于开启位置时的。

所有这些属性都是可绑定属性。

[`SwitchCell`](xref:Xamarin.Forms.SwitchCell)还公开`OnChanged`事件, 使您可以对单元格状态的更改做出响应。

![](tableview-images/switch-cell.png "SwitchCell 示例")

<a name="entrycell" />

### <a name="entrycell"></a>EntryCell

[`EntryCell`](xref:Xamarin.Forms.EntryCell) 如果需要显示的用户可以编辑的文本数据时非常有用。 它定义以下属性:

- `Keyboard`–编辑时要显示的键盘。 有数字值、 电子邮件、 电话号码等之类的内容的选项。[请参阅 API 文档](xref:Xamarin.Forms.Keyboard)。
- `Label`–要显示在文本输入字段左侧的标签文本。
- `LabelColor`–标签文本的颜色。
- `Placeholder`–当输入字段为 null 或为空时显示的文本。 文本输入开始时，此文本将消失。
- `Text`– "输入" 字段中的文本。
- `HorizontalTextAlignment`–文本的水平对齐方式。 可以在中心，左侧或右侧对齐。 [请参阅 API 文档](xref:Xamarin.Forms.TextAlignment)。

[`EntryCell`](xref:Xamarin.Forms.EntryCell)还公开`Completed`事件, 当用户在编辑文本时在键盘上的 "完成" 按钮上命中时, 将触发该事件。

![](tableview-images/entry-cell.png "EntryCell 示例")

<a name="Custom_Cells" />

## <a name="custom-cells"></a>自定义单元格

当内置的单元格仍不足时，可以使用自定义单元格能够并捕获有关您的应用程序有意义的方式中的数据。 例如，您可能希望显示滑块，以允许用户选择的图像的不透明度。

自定义的所有单元格必须派生自[ `ViewCell` ](xref:Xamarin.Forms.ViewCell)，所有内置的单元格类型使用相同的基类。

这是自定义单元格的一个示例：

![](tableview-images/custom-cell.png "自定义单元格示例")

下面的示例演示了在上面的屏幕截图[`TableView`](xref:Xamarin.Forms.TableView)中创建的 XAML:

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

下[`TableView`](xref:Xamarin.Forms.TableView)的根元素[`TableRoot`](xref:Xamarin.Forms.TableRoot)是[`TableSection`](xref:Xamarin.Forms.TableSection) , 并且紧靠`TableRoot`在下。 直接在下定义[`StackLayout`](xref:Xamarin.Forms.StackLayout) , 而用于管理自定义单元格的布局, 尽管可以在此处使用任何布局。 `TableSection` [`ViewCell`](xref:Xamarin.Forms.ViewCell)

> [!NOTE]
> 与[`ListView`](xref:Xamarin.Forms.ListView)不同[`TableView`](xref:Xamarin.Forms.TableView) , 不要求在中定义自定义 (或任何) `ItemTemplate`单元。

## <a name="row-height"></a>行高

[ `TableView` ](xref:Xamarin.Forms.TableView)类具有可用于更改单元格的行的高度的两个属性：

- [`RowHeight`](xref:Xamarin.Forms.TableView.RowHeight) – 设置为每个行的高度`int`。
- [`HasUnevenRows`](xref:Xamarin.Forms.TableView.HasUnevenRows) – 如果行具有不同高度设置为`true`。 请注意，当此属性设置为`true`，行高度自动计算，并通过 Xamarin.Forms 应用。

当中某个单元格中的内容的高度[ `TableView` ](xref:Xamarin.Forms.TableView)更改行高度隐式更新 Android 和通用 Windows 平台 (UWP) 上。 但是，在 iOS 上它必须强制通过设置更新[ `HasUnevenRows` ](xref:Xamarin.Forms.TableView.HasUnevenRows)属性设置为`true`并通过调用[ `Cell.ForceUpdateSize` ](xref:Xamarin.Forms.Cell.ForceUpdateSize)方法。

下面的 XAML 示例演示[ `TableView` ](xref:Xamarin.Forms.TableView) ，其中包含[ `ViewCell` ](xref:Xamarin.Forms.ViewCell):

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

当[ `ViewCell` ](xref:Xamarin.Forms.ViewCell)点击，`OnViewCellTapped`执行事件处理程序：

```csharp
void OnViewCellTapped(object sender, EventArgs e)
{
    _target.IsVisible = !_target.IsVisible;
    _viewCell.ForceUpdateSize();
}
```

`OnViewCellTapped`事件处理程序显示或隐藏第二个[ `Label` ](xref:Xamarin.Forms.Label)中[ `ViewCell` ](xref:Xamarin.Forms.ViewCell)，并通过调用将显式更新该单元格的大小[ `Cell.ForceUpdateSize`](xref:Xamarin.Forms.Cell.ForceUpdateSize)方法。

以下屏幕截图显示之前在点击后的单元格：

![](tableview-images/cell-beforeresize.png "调整大小前的 ViewCell")

下面的屏幕截图显示在点击后的单元格：

![](tableview-images/cell-afterresize.png "调整大小后的 ViewCell")

> [!IMPORTANT]
> 如果过度使用此功能，则强可能导致性能下降。

## <a name="related-links"></a>相关链接

- [TableView （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-tableview)
