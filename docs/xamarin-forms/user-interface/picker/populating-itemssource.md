---
title: 设置选取器的 ItemsSource 属性
description: 选取器视图是一个用于选择文本项中的数据列表控件。 本文介绍如何通过设置 ItemsSource 属性填充数据选取器以及如何响应用户的项选择。
ms.prod: xamarin
ms.assetid: 8ECF390C-9DB2-4441-B9A3-101AE7E5AEC5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2019
ms.openlocfilehash: 803cd1babc32e3a42d957c4bac0cc93c4552fb8c
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656124"
---
# <a name="setting-a-pickers-itemssource-property"></a>设置选取器的 ItemsSource 属性

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-monkeyapppicker)

_选取器视图是一个用于选择文本项中的数据列表控件。本文介绍如何通过设置 ItemsSource 属性填充数据选取器以及如何响应用户的项选择。_

已增强，Xamarin.Forms 2.3.4 [ `Picker` ](xref:Xamarin.Forms.Picker)视图中添加的功能要用数据填充通过设置其[ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource)属性，以及从检索所选的项[`SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem)属性。 此外，通过设置更改为选定项文本的颜色[ `TextColor` ](xref:Xamarin.Forms.Picker.TextColor)属性设置为[ `Color` ](xref:Xamarin.Forms.Color)。

## <a name="populating-a-picker-with-data"></a>使用数据填充选取器

一个[ `Picker` ](xref:Xamarin.Forms.Picker)可以通过设置使用数据填充其[ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource)属性设置为`IList`集合。 集合中的每个项必须是的或派生自类型`object`。 项可以在 XAML 中通过初始化中添加`ItemsSource`属性从项的数组：

```xaml
<Picker x:Name="picker"
        Title="Select a monkey"
        TitleColor="Red">
  <Picker.ItemsSource>
    <x:Array Type="{x:Type x:String}">
      <x:String>Baboon</x:String>
      <x:String>Capuchin Monkey</x:String>
      <x:String>Blue Monkey</x:String>
      <x:String>Squirrel Monkey</x:String>
      <x:String>Golden Lion Tamarin</x:String>
      <x:String>Howler Monkey</x:String>
      <x:String>Japanese Macaque</x:String>
    </x:Array>
  </Picker.ItemsSource>
</Picker>
```

> [!NOTE]
> 请注意，`x:Array`元素需要`Type`属性，指示数组中的项的类型。

等效的 C# 代码如下所示：

```csharp
var monkeyList = new List<string>();
monkeyList.Add("Baboon");
monkeyList.Add("Capuchin Monkey");
monkeyList.Add("Blue Monkey");
monkeyList.Add("Squirrel Monkey");
monkeyList.Add("Golden Lion Tamarin");
monkeyList.Add("Howler Monkey");
monkeyList.Add("Japanese Macaque");

var picker = new Picker { Title = "Select a monkey", TitleColor = Color.Red };
picker.ItemsSource = monkeyList;
```

## <a name="responding-to-item-selection"></a>响应项目选择

一个[ `Picker` ](xref:Xamarin.Forms.Picker)支持一次的一项选择。 当用户选择某个项， [ `SelectedIndexChanged` ](xref:Xamarin.Forms.Picker.SelectedIndexChanged)事件触发时， [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex)属性更新为一个整数，表示在列表中，所选的项的索引和[`SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem)属性更新为`object`表示所选的项。 [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex)属性是一个从零开始的数字，指示用户所选的项。 如果未不选择任何项，这是这种情况时[ `Picker` ](xref:Xamarin.Forms.Picker)首次创建和初始化，`SelectedIndex`将为-1。

> [!NOTE]
> 项中的选择行为[ `Picker` ](xref:Xamarin.Forms.Picker)可以上具有平台特定的 iOS 自定义。 有关详细信息，请参阅[控制选取器项选择](~/xamarin-forms/platform/ios/picker-selection.md)。

下面的代码示例演示如何检索[ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem)属性值从[ `Picker` ](xref:Xamarin.Forms.Picker)在 XAML 中：

```xaml
<Label Text="{Binding Source={x:Reference picker}, Path=SelectedItem}" />
```

等效的 C# 代码如下所示：

```csharp
var monkeyNameLabel = new Label();
monkeyNameLabel.SetBinding(Label.TextProperty, new Binding("SelectedItem", source: picker));
```

此外，可以是一个事件处理程序时执行[ `SelectedIndexChanged` ](xref:Xamarin.Forms.Picker.SelectedIndexChanged)触发事件：

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs e)
{
  var picker = (Picker)sender;
  int selectedIndex = picker.SelectedIndex;

  if (selectedIndex != -1)
  {
    monkeyNameLabel.Text = (string)picker.ItemsSource[selectedIndex];
  }
}
```

此方法获取[ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex)属性值，并使用该值来检索中的选定的项[ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource)集合。 这是功能上等效于检索中的选定的项[ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem)属性。 请注意，在每个项`ItemsSource`集合属于类型`object`，，因此必须强制转换为`string`进行显示。

> [!NOTE]
> 一个[ `Picker` ](xref:Xamarin.Forms.Picker)可初始化以通过设置显示特定项[ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex)或者[ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem)属性。 但是，这些属性必须设置初始化后[ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource)集合。

## <a name="populating-a-picker-with-data-using-data-binding"></a>使用数据绑定填充包含数据的选取器

一个[ `Picker` ](xref:Xamarin.Forms.Picker)可以还使用填充数据通过使用数据绑定将绑定其[ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource)属性设置为`IList`集合。 在 XAML 中此，可以使用[ `Binding` ](xref:Xamarin.Forms.Xaml.BindingExtension)标记扩展：

```xaml
<Picker Title="Select a monkey"
        TitleColor="Red"
        ItemsSource="{Binding Monkeys}"
        ItemDisplayBinding="{Binding Name}" />
```

等效的 C# 代码如下所示：

```csharp
var picker = new Picker { Title = "Select a monkey", TitleColor = Color.Red };
picker.SetBinding(Picker.ItemsSourceProperty, "Monkeys");
picker.ItemDisplayBinding = new Binding("Name");
```

[ `ItemsSource` ](xref:Xamarin.Forms.Picker.ItemsSource)属性数据绑定到`Monkeys`已连接的视图模型，它将返回属性`IList<Monkey>`集合。 下面的代码示例演示`Monkey`类，该类包含四个属性：

```csharp
public class Monkey
{
  public string Name { get; set; }
  public string Location { get; set; }
  public string Details { get; set; }
  public string ImageUrl { get; set; }
}
```

当绑定到一系列对象， [ `Picker` ](xref:Xamarin.Forms.Picker)必须告知要显示的每个对象的属性。 这通过设置来实现[ `ItemDisplayBinding` ](xref:Xamarin.Forms.Picker.ItemDisplayBinding)到所需的属性从每个对象的属性。 在上面的代码示例`Picker`设置为以显示每个`Monkey.Name`属性值。

### <a name="responding-to-item-selection"></a>响应项目选择

数据绑定可用于将对象设置[ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem)属性值发生更改时：

```xaml
<Picker Title="Select a monkey"
        TitleColor="Red"
        ItemsSource="{Binding Monkeys}"
        ItemDisplayBinding="{Binding Name}"
        SelectedItem="{Binding SelectedMonkey}" />
<Label Text="{Binding SelectedMonkey.Name}" ... />
<Label Text="{Binding SelectedMonkey.Location}" ... />
<Image Source="{Binding SelectedMonkey.ImageUrl}" ... />
<Label Text="{Binding SelectedMonkey.Details}" ... />
```

等效的 C# 代码如下所示：

```csharp
var picker = new Picker { Title = "Select a monkey", TitleColor = Color.Red };
picker.SetBinding(Picker.ItemsSourceProperty, "Monkeys");
picker.SetBinding(Picker.SelectedItemProperty, "SelectedMonkey");
picker.ItemDisplayBinding = new Binding("Name");

var nameLabel = new Label { ... };
nameLabel.SetBinding(Label.TextProperty, "SelectedMonkey.Name");

var locationLabel = new Label { ... };
locationLabel.SetBinding(Label.TextProperty, "SelectedMonkey.Location");

var image = new Image { ... };
image.SetBinding(Image.SourceProperty, "SelectedMonkey.ImageUrl");

var detailsLabel = new Label();
detailsLabel.SetBinding(Label.TextProperty, "SelectedMonkey.Details");
```

[ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem)属性数据绑定到`SelectedMonkey`已连接的视图模型，这是类型的属性`Monkey`。 因此，当用户选择中的项[ `Picker` ](xref:Xamarin.Forms.Picker)，则`SelectedMonkey`属性将设置与所选`Monkey`对象。 `SelectedMonkey`通过在用户界面中显示对象数据[ `Label` ](xref:Xamarin.Forms.Label)并[ `Image` ](xref:Xamarin.Forms.Image)视图：

![](populating-itemssource-images/monkeys.png "选取器项选择")

> [!NOTE]
> 请注意， [ `SelectedItem` ](xref:Xamarin.Forms.Picker.SelectedItem)并[ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex)属性都默认情况下支持双向绑定。

## <a name="related-links"></a>相关链接

- [选取器演示 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-pickerdemo)
- [Monkey 应用 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-monkeyapppicker)
- [可绑定选取器 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablepicker)
- [选取器 API](xref:Xamarin.Forms.Picker)
