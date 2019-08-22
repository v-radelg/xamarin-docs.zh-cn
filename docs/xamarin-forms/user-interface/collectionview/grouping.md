---
title: Xamarin CollectionView 分组
description: CollectionView 可以通过将其 IsGrouped 属性设置为 true 来显示正确分组的数据。
ms.prod: xamarin
ms.assetid: 7E494245-FDBD-49D6-B7FA-CEF976EB59BB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/24/2019
ms.openlocfilehash: 8fd37999428c2813bbf96de3bcbd6ebd1fe0879d
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69894024"
---
# <a name="xamarinforms-collectionview-grouping"></a>Xamarin CollectionView 分组

![](~/media/shared/preview.png "此 API 当前为预发布版本")

[![下载示例](~/media/shared/download.png) 下载示例](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CollectionViewDemos)

经常滚动列表中的大型数据集可能会变得难以使用。 在这种情况下, 将数据组织成组可以更轻松地导航数据, 从而改善用户体验。

[`CollectionView`](xref:Xamarin.Forms.CollectionView)支持显示分组数据, 并定义以下属性, 这些属性控制其显示方式:

- `IsGrouped`类型`bool`为的, 指示是否应在组中显示基础数据。 此属性的默认值为 `false`。
- `GroupHeaderTemplate`, 类型[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)为, 要用于每个组的标头的模板。
- `GroupFooterTemplate`, 类型[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)为, 要用于每个组的脚注的模板。

这些属性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持的, 这意味着属性可以是数据绑定的目标。

有关数据模板的详细信息，请参阅 [Xamarin.Forms 数据模板](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。

> [!IMPORTANT]
> 目前仅支持[`CollectionView`](xref:Xamarin.Forms.CollectionView)在 iOS 上对数据进行分组。

## <a name="group-data"></a>组数据

必须先对数据进行分组, 然后才能显示。 这可以通过创建组列表来实现, 其中每个组都是项的列表。 组列表应为`IEnumerable<T>`集合, 其中`T`定义了两个数据片段:

- 组名称。
- 定义属于组的项的集合。`IEnumerable`

因此, 对数据进行分组的过程是:

- 创建对单个项建模的类型。
- 创建一个为一组项建模的类型。
- 创建集合, 其中`T`是对一组项进行建模的类型。 `IEnumerable<T>` 因此, 此集合为存储分组数据的组的集合。
- 向`IEnumerable<T>`集合中添加数据。

### <a name="example"></a>示例

在对数据进行分组时, 第一步是创建一个为单个项建模的类型。 下面的示例演示示例`Animal`应用程序中的类:

```csharp
public class Animal
{
    public string Name { get; set; }
    public string Location { get; set; }
    public string Details { get; set; }
    public string ImageUrl { get; set; }
}
```

`Animal`类对单个项建模。 然后就可以创建一组项的类型。 下面的示例演示示例`AnimalGroup`应用程序中的类:

```csharp
public class AnimalGroup : List<Animal>
{
    public string Name { get; private set; }

    public AnimalGroup(string name, List<Animal> animals) : base(animals)
    {
        Name = name;
    }
}
```

类从类继承, 并添加一个`Name`表示组名称的属性。 `List<T>` `AnimalGroup`

然后, 可以创建组的集合:`IEnumerable<T>`

```csharp
public List<AnimalGroup> Animals { get; private set; } = new List<AnimalGroup>();
```

此代码定义一个名为`Animals`的集合, 其中集合中的每一`AnimalGroup`项都是一个对象。 每`AnimalGroup`个对象都包含一个名称和`List<Animal>`一个定义`Animal`组中的对象的集合。

然后, 可以将分组的`Animals`数据添加到集合中:

```csharp
Animals.Add(new AnimalGroup("Bears", new List<Animal>
{
    new Animal
    {
        Name = "American Black Bear",
        Location = "North America",
        Details = "Details about the bear go here.",
        ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/0/08/01_Schwarzbär.jpg"
    },
    new Animal
    {
        Name = "Asian Black Bear",
        Location = "Asia",
        Details = "Details about the bear go here.",
        ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/b/b7/Ursus_thibetanus_3_%28Wroclaw_zoo%29.JPG/180px-Ursus_thibetanus_3_%28Wroclaw_zoo%29.JPG"
    },
    // ...
}));

Animals.Add(new AnimalGroup("Monkeys", new List<Animal>
{
    new Animal
    {
        Name = "Baboon",
        Location = "Africa & Asia",
        Details = "Details about the monkey go here.",
        ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
    },
    new Animal
    {
        Name = "Capuchin Monkey",
        Location = "Central & South America",
        Details = "Details about the monkey go here.",
        ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/4/40/Capuchin_Costa_Rica.jpg/200px-Capuchin_Costa_Rica.jpg"
    },
    new Animal
    {
        Name = "Blue Monkey",
        Location = "Central and East Africa",
        Details = "Details about the monkey go here.",
        ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/8/83/BlueMonkey.jpg/220px-BlueMonkey.jpg"
    },
    // ...
}));
```

此代码在`Animals`集合中创建两个组。 第一个`AnimalGroup`命名`Bears`为, 并包含一`List<Animal>`组熊详细信息。 第二`AnimalGroup`个命名`Monkeys`为, 包含`List<Animal>`猴子详细信息的集合。

## <a name="display-grouped-data"></a>显示分组数据

[`CollectionView`](xref:Xamarin.Forms.CollectionView)通过将`IsGrouped`属性设置为`true`, 可显示分组数据, 前提是数据已正确分组:

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                IsGrouped="true">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                ...
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

等效 C# 代码如下：

```csharp
CollectionView collectionView = new CollectionView
{
    IsGrouped = true
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Animals");
// ...
```

通过[`CollectionView`](xref:Xamarin.Forms.CollectionView) [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)将属性设置为, 可以定义中每个项的外观。 [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) 有关详细信息, 请参阅[定义项外观](~/xamarin-forms/user-interface/collectionview/populate-data.md#define-item-appearance)。

> [!NOTE]
> 默认情况下[`CollectionView`](xref:Xamarin.Forms.CollectionView) , 将在组标头和表尾中显示组名。 可以通过自定义组头和组尾来更改此行为。

## <a name="customize-the-group-header"></a>自定义组标头

可以通过将`CollectionView.GroupHeaderTemplate`属性设置[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)为来自定义每个组标头的外观:

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                IsGrouped="true">
    ...
    <CollectionView.GroupHeaderTemplate>
        <DataTemplate>
            <Label Text="{Binding Name}"
                   BackgroundColor="LightGray"
                   FontSize="Large"
                   FontAttributes="Bold" />
        </DataTemplate>
    </CollectionView.GroupHeaderTemplate>
</CollectionView>
```

在此示例中, 每个组标头设置[`Label`](xref:Xamarin.Forms.Label)为, 其中显示组名称, 并设置了其他外观属性。

## <a name="customize-the-group-footer"></a>自定义组尾

可以通过将`CollectionView.GroupFooterTemplate`属性设置[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)为来自定义每个组脚注的外观:

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                IsGrouped="true">
    ...
    <CollectionView.GroupFooterTemplate>
        <DataTemplate>
            <Label Text="{Binding Count, StringFormat='Total animals: {0:D}'}"
                   Margin="0,0,0,10" />
        </DataTemplate>
    </CollectionView.GroupFooterTemplate>
</CollectionView>
```

在此示例中, 每个组脚注均设置[`Label`](xref:Xamarin.Forms.Label)为, 以显示组中的项数。

## <a name="empty-groups"></a>空组

[`CollectionView`](xref:Xamarin.Forms.CollectionView)当显示分组的数据时, 它会显示任何空组。 此类组将连同组头和表尾一起显示, 这表示该组为空。

> [!NOTE]
> 在 iOS 10 和更低版本中, 空组的组头和组尾可能会全部显示在`CollectionView`顶部。

## <a name="group-without-templates"></a>无模板组

[`CollectionView`](xref:Xamarin.Forms.CollectionView)可以显示正确的[`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)分组数据, 而不将属性设置为:

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                IsGrouped="true" />
```

在此方案中, 可以通过重写对单个项`ToString`建模的类型中的方法和对一组项建模的类型来显示有意义的数据。

## <a name="related-links"></a>相关链接

- [CollectionView (示例)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CollectionViewDemos/)
- [Xamarin. Forms 数据模板](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
