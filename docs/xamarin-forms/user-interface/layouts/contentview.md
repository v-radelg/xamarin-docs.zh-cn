---
title: Xamarin. Forms ContentView
description: 本文介绍如何使用 ContentView 类来创建自定义控件, 例如 CardView 示例。
ms.prod: xamarin
ms.assetid: 638402E7-CA44-456B-863B-791F6B6B561D
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 08/14/2019
ms.openlocfilehash: e340b45148c7528eff1aa511ee9902a4ac2658c0
ms.sourcegitcommit: 9178e2e689f027212ea3e623b556b312985d79fe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69658154"
---
# <a name="xamarinforms-contentview"></a>Xamarin. Forms ContentView

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/en-us/samples/xamarin/xamarin-forms-samples/userinterface-cardview/)

Xamarin [`ContentView`](xref:Xamarin.Forms.ContentView)类是一`Layout`种包含一个子元素的类型, 通常用于创建自定义的可重用控件。 类继承自[`TemplatedView`。](xref:Xamarin.Forms.TemplatedView) `ContentView` 本文和相关示例说明了如何创建基于`CardView` `ContentView`类的自定义控件。

以下屏幕截图显示了`CardView`一个派生`ContentView`自类的控件:

[![CardView 示例应用程序屏幕快照](contentview-images/cardview-list-cropped.png)](contentview-images/cardview-list.png#lightbox)

`ContentView`类定义单个属性:

* [`Content`](xref:Xamarin.Forms.ContentView.Content)是一个`View`对象。 此属性由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)对象支持, 因此它可以是数据绑定的目标。

还继承`TemplatedView`类的属性: `ContentView`

* [`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate)`ControlTemplate`可定义或重写控件外观的。

有关`ControlTemplate`属性的详细信息, 请参阅[使用 system.windows.controls.controltemplate> 自定义外观](#customize-appearance-with-a-controltemplate)。

## <a name="create-a-custom-control"></a>创建自定义控件

`ContentView`类本身提供很少的功能, 但可用于创建自定义控件。 该示例项目定义了`CardView`一个控件-一个 UI 元素, 该元素在类似卡片的布局中显示图像、标题和说明。

创建自定义控件的过程如下:

1. 使用 Visual Studio 2019 中的`ContentView`模板创建新类。
1. 为新的自定义控件定义代码隐藏文件中的任何唯一属性或事件。
1. 为自定义控件创建 UI。

> [!NOTE]
> 可以创建自定义控件, 其布局是在代码中定义的, 而不是在 XAML 中定义的。 为简单起见, 示例应用程序仅定义`CardView`了一个具有 XAML 布局的类。 但是, 示例应用程序包含一个**CardViewCodePage**类, 该类显示在代码中使用自定义控件的过程。

### <a name="create-code-behind-properties"></a>创建代码隐藏属性

`CardView`自定义控件定义以下属性:

* `CardTitle`: 一个`string`对象, 表示卡片上显示的标题。
* `CardDescription`: 一个`string`对象, 该对象表示卡上显示的说明。
* `IconImageSource`: 一个`ImageSource`对象, 该对象表示卡上显示的图像。
* `IconBackgroundColor`: 一个`Color`对象, 该对象表示卡上显示的图像的背景色。
* `BorderColor`: 一个`Color`对象, 表示卡片边框、图像边框和分隔线的颜色。
* `CardColor`: 表示卡片背景色的对象。`Color`

> [!NOTE]
> 出于演示的目的,属性会影响多个项。`BorderColor` 如果需要, 可以将此属性分解为三个属性。

每个属性都由`BindableProperty`实例支持。 支持`BindableProperty`使用 MVVM 模式使每个属性都具有样式和界限。 有关详细信息, 请参阅[通过 MVVM 绑定数据](#bind-data-with-mvvm)。

下面的示例演示如何创建后备`BindableProperty`:

```csharp
public static readonly BindableProperty CardTitleProperty = BindableProperty.Create(
    "CardTitle",        // the name of the bindable property
    typeof(string),     // the bindable property type
    typeof(CardView),   // the parent object type
    string.Empty);      // the default value for the property
```

自定义属性使用`GetValue`和`SetValue`方法`BindableProperty`来获取和设置对象值:

```csharp
public string CardTitle
{
    get => (string)GetValue(CardView.CardTitleProperty);
    set => SetValue(CardView.CardTitleProperty, value);
}
```

有关`BindableProperty`对象的详细信息, 请参阅可[绑定属性](~/xamarin-forms/xaml/bindable-properties.md)。

### <a name="define-ui"></a>定义 UI

自定义控件 UI 使用`ContentView`作为`CardView`控件的根元素。 下面的示例演示`CardView`了 XAML:

```XAML
<ContentView ...
             x:Name="this"
             x:Class="CardViewDemo.Controls.CardView">
    <Frame BindingContext="{x:Reference this}"
            BackgroundColor="{Binding CardColor}"
            BorderColor="{Binding BorderColor}"
            ...>
        <Grid>
            ...
            <Frame BorderColor="{Binding BorderColor}"
                   BackgroundColor="{Binding IconBackgroundColor}"
                   ...>
                <Image Source="{Binding IconImageSource}"
                       .. />
            </Frame>
            <Label Text="{Binding CardTitle}"
                   ... />
            <BoxView BackgroundColor="{Binding BorderColor}"
                     ... />
            <Label Text="{Binding CardDescription}"
                   ... />
        </Grid>
    </Frame>
</ContentView>
```

元素将属性设置为**this, 这**可用于访问绑定`CardView`到实例的对象。 `x:Name` `ContentView` 布局中的元素会将其属性的绑定设置为绑定对象上定义的值。

有关数据绑定的详细信息，请参阅 [Xamarin.Forms 数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)。

## <a name="instantiate-a-custom-control"></a>实例化自定义控件

必须将对自定义控件命名空间的引用添加到实例化自定义控件的页中。 下面的示例演示了一个名为**控件**的命名空间`ContentPage`引用, 这些控件添加到了 XAML 的实例中:

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:CardViewDemo.Controls" >
```

添加引用后, `CardView`可以在 XAML 中实例化并定义其属性:

```xaml
<controls:CardView BorderColor="DarkGray"
                   CardTitle="Slavko Vlasic"
                   CardDescription="Lorem ipsum dolor sit..."
                   IconBackgroundColor="SlateGray"
                   IconImageSource="user.png"/>
```

也`CardView`可以在代码中实例化:

```csharp
CardView card = new CardView
{
    BorderColor = Color.DarkGray,
    CardTitle = "Slavko Vlasic",
    CardDescription = "Lorem ipsum dolor sit...",
    IconBackgroundColor = Color.SlateGray,
    IconImageSource = ImageSource.FromFile("user.png")
};
```

### <a name="bind-data-with-mvvm"></a>通过 MVVM 绑定数据

类中的`BindableProperty`对象允许模型-视图-ViewModel (MVVM) 样式绑定。 `CardView` 示例应用程序包含`PersonCollectionViewModel`定义单个集合属性的类:

```csharp
public class PersonCollectionViewModel : INotifyPropertyChanged
{
    ...
    public List<PersonViewModel> Items
    {
        get
        {
            return items;
        }
        set
        {
            items = value;
            NotifyPropertyChanged();
        }
    }
    ...
}
```

`PersonViewModel`类表示个人配置文件:

```csharp
public class PersonViewModel : INotifyPropertyChanged
{
    ...

    string photo;
    public string Photo
    {
        get
        {
            return photo;
        }
        set
        {
            photo = value;
            NotifyPropertyChanged();
        }
    }

    string name;
    public string Name
    {
        get
        {
            return name;
        }
        set
        {
            name = value;
            NotifyPropertyChanged();
        }
    }

    string bio;
    public string Bio
    {
        get
        {
            return bio;
        }
        set
        {
            bio = value;
            NotifyPropertyChanged();
        }
    }
    ...
}
```

可用于以卡片列表的形式呈现`PersonViewModel`对象的集合。 `CardView` 下面的示例演示如何将`PersonViewCollection`实例绑定到 XAML 中的`StackLayout`实例:

```xaml
<StackLayout HorizontalOptions="Fill"
             VerticalOptions="Fill"
             BindableLayout.ItemsSource="{Binding Items}">
    <BindableLayout.ItemTemplate>
        <DataTemplate>
            <controls:CardView Margin="4"
                               BorderColor="DarkGray"
                               IconBackgroundColor="SlateGray"
                               BindingContext="{Binding .}"
                               CardTitle="{Binding Name}"
                               CardDescription="{Binding Bio}"
                               IconImageSource="{Binding Photo}"/>
        </DataTemplate>
    </BindableLayout.ItemTemplate>
</StackLayout>
```

实例的`Items`属性使用可绑定布局绑定到。`StackLayout` `PersonViewCollection` 定义每个`CardView`对象的外观, 并将数据绑定到的`PersonViewModel`属性。 `DataTemplate` 如果设置了, 则将`CardView` `PersonView`为集合中的每个对象创建一个对象。 `Items` `BindingContext` `BindingContext`已设置, 如以下示例中所示:

```csharp
public partial class CardViewMvvmPage : ContentPage
{
    public CardViewMvvmPage()
    {
        InitializeComponent();
        BindingContext = DataService.GetPersonCollection();
    }
}
```

有关数据绑定的详细信息, 请参阅[Xamarin。窗体数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)。 有关`BindableProperty`对象的详细信息, 请参阅可[绑定属性](~/xamarin-forms/xaml/bindable-properties.md)。

## <a name="customize-appearance-with-a-controltemplate"></a>使用 System.windows.controls.controltemplate> 自定义外观

派生`ContentView`自类的自定义控件可以使用 XAML、代码或根本不定义外观来定义外观。 不管如何定义外观, `ControlTemplate`对象都可以使用自定义布局替代外观。

对于`CardView`某些用例, 布局可能占用过多的空间。 `ControlTemplate`可以重写布局以提供更紧凑的视图,`CardView`适用于简洁列表:

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <ControlTemplate x:Key="CardViewCompressed">
            <Grid>
                <Grid.RowDefinitions>
                    <RowDefinition Height="100" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="100" />
                    <ColumnDefinition Width="100*" />
                </Grid.ColumnDefinitions>
                <Image Grid.Row="0"
                       Grid.Column="0"
                       Source="{TemplateBinding IconImageSource}"
                       BackgroundColor="{TemplateBinding IconBackgroundColor}"
                       WidthRequest="100"
                       HeightRequest="100"
                       Aspect="AspectFill"
                       HorizontalOptions="Center"
                       VerticalOptions="Center"/>
                <StackLayout Grid.Row="0"
                             Grid.Column="1">
                    <Label Text="{TemplateBinding CardTitle}"
                           FontAttributes="Bold" />
                    <Label Text="{TemplateBinding CardDescription}" />
                </StackLayout>
            </Grid>
        </ControlTemplate>
    </ResourceDictionary>
</ContentPage.Resources>
```

中`ControlTemplate`的数据绑定`TemplateBinding`使用标记扩展来指定绑定。 然后, 可以`x:Key`使用该属性的值将属性设置为定义的system.windows.controls.controltemplate>对象。`ControlTemplate` 下面的示例演示如何`ControlTemplate` `CardView`在实例上设置属性:

```xaml
<controls:CardView ControlTemplate="{StaticResource CardViewCompressed}"/>
```

以下屏幕截图显示标准`CardView`实例, 并`CardView`已将其`ControlTemplate`重写:

[![CardView System.windows.controls.controltemplate> 屏幕快照](contentview-images/cardview-controltemplates-cropped.png)](contentview-images/cardview-controltemplates.png#lightbox)

有关控件模板的详细信息, 请参阅[Xamarin. Forms 控件模板](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md)。

## <a name="related-links"></a>相关链接

* [CardView 示例应用程序](https://docs.microsoft.com/en-us/samples/xamarin/xamarin-forms-samples/userinterface-cardview/)
* [Xamarin. 窗体数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)
* 可[绑定属性](~/xamarin-forms/xaml/bindable-properties.md)。
* [Xamarin. Forms 控件模板](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md)
