---
title: 资源字典
description: XAML 资源是可以共享和重复使用在 Xamarin.Forms 应用程序的对象。
ms.prod: xamarin
ms.assetid: DF103686-4A92-40FA-9CF1-A9376293B13C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/28/2019
ms.custom: video
ms.openlocfilehash: f41bcc3ccaa6b4dd8c001ceb5ead165486745283
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68650263"
---
# <a name="resource-dictionaries"></a>资源字典

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)

_XAML 资源是可以共享和重复使用 Xamarin.Forms 应用程序在整个对象的定义。_

这些资源对象存储在资源字典中。 本文介绍如何创建和使用的资源字典，以及如何合并资源字典。

## <a name="overview"></a>概述

一个[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)是 Xamarin.Forms 应用程序所使用的资源的存储库。 中存储的典型资源`ResourceDictionary`包括[样式](~/xamarin-forms/user-interface/styles/index.md)，[控件模板](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md)，[数据模板](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)，颜色和转换器。

在 XAML，资源存储在`ResourceDictionary`可检索和使用应用于元素`StaticResource`标记扩展。 在 C# 中，资源也可以定义在`ResourceDictionary`然后检索并应用到通过使用基于字符串的索引器元素。 但是，没有什么优势使用`ResourceDictionary`在 C# 中，如共享的对象可以只需存储为字段或属性，并不直接访问不会第一个检索这些字典中。

## <a name="creating-and-consuming-a-resourcedictionary"></a>创建和使用 ResourceDictionary

资源定义中[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) ，则设置为下列任一`Resources`属性：

- [ `Resources` ](xref:Xamarin.Forms.Application.Resources)派生自任何类的属性 [`Application`](xref:Xamarin.Forms.Application)
- [ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources)派生自任何类的属性 [`VisualElement`](xref:Xamarin.Forms.Application)

Xamarin.Forms 程序仅包含一个类派生自`Application`通常使用许多派生自的类，但`VisualElement`，包括页面、 布局和控件。 任何这些对象可以具有其`Resources`属性设置为`ResourceDictionary`。 选择在何处放置特定`ResourceDictionary`，可以使用的资源的影响：

- 中的资源`ResourceDictionary`如附加到视图`Button`或`Label`只能应用于该特定对象，因此这不是很有用。
- 中的资源`ResourceDictionary`如附加到布局`StackLayout`或`Grid`可应用于的布局和布局的所有子级。
- 中的资源`ResourceDictionary`定义的页级别可应用到的页和到其所有子项。
- 中的资源`ResourceDictionary`定义在应用程序级别可以应用于整个应用程序。

以下 XAML 演示应用程序级别中定义的资源`ResourceDictionary`中**App.xaml**标准 Xamarin.Forms 程序的一部分创建的文件：

```xaml
<Application ...>
    <Application.Resources>
        <ResourceDictionary>
            <Color x:Key="PageBackgroundColor">Yellow</Color>
            <Color x:Key="HeadingTextColor">Black</Color>
            <Color x:Key="NormalTextColor">Blue</Color>
            <Style x:Key="LabelPageHeadingStyle" TargetType="Label">
                <Setter Property="FontAttributes" Value="Bold" />
                <Setter Property="HorizontalOptions" Value="Center" />
                <Setter Property="TextColor" Value="{StaticResource HeadingTextColor}" />
            </Style>
        </ResourceDictionary>
    </Application.Resources>
</Application>
```

这`ResourceDictionary`定义了三个[ `Color` ](xref:Xamarin.Forms.Color)资源和一个[ `Style` ](xref:Xamarin.Forms.Style)资源。 有关详细信息`App`类，请参阅[App 类](~/xamarin-forms/app-fundamentals/application-class.md)。

Xamarin.Forms 从 3.0 开始，显式`ResourceDictionary`标记不是必需的。 `ResourceDictionary`对象自动创建的并且可以插入资源之间直接`Resources`属性元素标记：

```xaml
<Application ...>
    <Application.Resources>
        <Color x:Key="PageBackgroundColor">Yellow</Color>
        <Color x:Key="HeadingTextColor">Black</Color>
        <Color x:Key="NormalTextColor">Blue</Color>
        <Style x:Key="LabelPageHeadingStyle" TargetType="Label">
            <Setter Property="FontAttributes" Value="Bold" />
            <Setter Property="HorizontalOptions" Value="Center" />
            <Setter Property="TextColor" Value="{StaticResource HeadingTextColor}" />
        </Style>
    </Application.Resources>
</Application>
```

每个资源都有一个使用指定的密钥`x:Key`属性，将它作为字典键中的`ResourceDictionary`。 该密钥用于检索从一个资源`ResourceDictionary`由[ `StaticResource` ](xref:Xamarin.Forms.Xaml.StaticResourceExtension)标记扩展，如下面显示了其他资源中定义的 XAML 代码示例中所示`StackLayout`:

```xaml
<StackLayout Margin="0,20,0,0">
  <StackLayout.Resources>
    <ResourceDictionary>
      <Style x:Key="LabelNormalStyle" TargetType="Label">
        <Setter Property="TextColor" Value="{StaticResource NormalTextColor}" />
      </Style>
      <Style x:Key="MediumBoldText" TargetType="Button">
        <Setter Property="FontSize" Value="Medium" />
        <Setter Property="FontAttributes" Value="Bold" />
      </Style>
    </ResourceDictionary>
  </StackLayout.Resources>
  <Label Text="ResourceDictionary Demo" Style="{StaticResource LabelPageHeadingStyle}" />
    <Label Text="This app demonstrates consuming resources that have been defined in resource dictionaries."
           Margin="10,20,10,0"
           Style="{StaticResource LabelNormalStyle}" />
    <Button Text="Navigate"
            Clicked="OnNavigateButtonClicked"
            TextColor="{StaticResource NormalTextColor}"
            Margin="0,20,0,0"
            HorizontalOptions="Center"
            Style="{StaticResource MediumBoldText}" />
</StackLayout>
```

第一个[ `Label` ](xref:Xamarin.Forms.Label)实例检索并使用`LabelPageHeadingStyle`中的应用程序级别定义的资源`ResourceDictionary`，与第二个`Label`实例检索和使用`LabelNormalStyle`控制级别中定义的资源`ResourceDictionary`。 同样， [ `Button` ](xref:Xamarin.Forms.Button)实例检索并使用`NormalTextColor`中的应用程序级别定义的资源`ResourceDictionary`，和`MediumBoldText`中的控制级别定义资源`ResourceDictionary`。 这会导致下面的屏幕截图中所示的外观：

[![](resource-dictionaries-images/screenshots-sml.png "使用 ResourceDictionary 资源")](resource-dictionaries-images/screenshots.png#lightbox "消耗 ResourceDictionary 资源")

> [!NOTE]
> 不应在应用程序级别的资源字典中，这种情况时所需的页面资源将随后在应用程序启动，而不是可以解析包含特定于单个页面的资源。 有关详细信息，请参阅[减小应用程序资源字典大小](~/xamarin-forms/deploy-test/performance.md)。

## <a name="overriding-resources"></a>重写的资源

当`ResourceDictionary`资源共享`x:Key`属性值的视图层次结构中更低时定义的资源将优先于更高版本定义了。 例如，设置`PageBackgroundColor`资源`Blue`在应用程序级别将会重写页面级`PageBackgroundColor`资源设置为`Yellow`。 同样，页面级`PageBackgroundColor`资源将被重写由控件级别`PageBackgroundColor`资源。 下面的 XAML 代码示例演示此优先顺序：

```xaml
<ContentPage ... BackgroundColor="{StaticResource PageBackgroundColor}">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Color x:Key="PageBackgroundColor">Blue</Color>
            <Color x:Key="NormalTextColor">Yellow</Color>
        </ResourceDictionary>
    </ContentPage.Resources>
    <StackLayout Margin="0,20,0,0">
        ...
        <Label Text="ResourceDictionary Demo" Style="{StaticResource LabelPageHeadingStyle}" />
        <Label Text="This app demonstrates consuming resources that have been defined in resource dictionaries."
               Margin="10,20,10,0"
               Style="{StaticResource LabelNormalStyle}" />
        <Button Text="Navigate"
                Clicked="OnNavigateButtonClicked"
                TextColor="{StaticResource NormalTextColor}"
                Margin="0,20,0,0"
                HorizontalOptions="Center"
                Style="{StaticResource MediumBoldText}" />
    </StackLayout>
</ContentPage>
```

原始`PageBackgroundColor`并`NormalTextColor`的情况下，应用程序级别定义将取代`PageBackgroundColor`和`NormalTextColor`页级别定义的实例。 因此，页面背景颜色变为蓝色，并且页面上的文本将变为黄色，如以下屏幕截图中所示：

[![](resource-dictionaries-images/overridding-screenshots-sml.png "重写 ResourceDictionary 资源")](resource-dictionaries-images/overridding-screenshots.png#lightbox "重写 ResourceDictionary 资源")

但请注意，背景栏[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)仍为黄色，因为[ `BarBackgroundColor` ](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor)属性设置的值为`PageBackgroundColor`应用程序中定义的资源级别`ResourceDictionary`。

下面是考虑`ResourceDictionary`优先顺序的另一种方法:当 XAML 分析器遇到`StaticResource`时, 它将通过使用它找到的第一个匹配项向上遍历可视化树来搜索匹配的键。 如果此搜索结束的页上且仍未找到键，XAML 分析器将搜索`ResourceDictionary`附加到`App`对象。 如果仍未找到该键，则引发异常。

## <a name="stand-alone-resource-dictionaries"></a>独立的资源字典

一个类派生自`ResourceDictionary`也可以在单独的独立文件。 (更准确地说，派生自的类`ResourceDictionary`通常需要_对_的文件由于在 XAML 文件，但具有的代码隐藏文件中定义资源`InitializeComponent`调用也是有必要。)然后，可以在应用程序间共享生成的文件。

若要创建此类文件，添加一个新**内容视图**或**内容页**项与项目 (但不是**内容视图**或者**内容页**与仅 C# 文件）。 在 XAML 文件和 C# 文件上，从基类的名称更改`ContentView`或`ContentPage`到`ResourceDictionary`。 在 XAML 文件中，基类的名称是顶级元素。

下面的 XAML 示例演示[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)名为`MyResourceDictionary`:

```xaml
<ResourceDictionary xmlns="http://xamarin.com/schemas/2014/forms"
                    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                    x:Class="ResourceDictionaryDemo.MyResourceDictionary">
    <DataTemplate x:Key="PersonDataTemplate">
        <ViewCell>
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="0.5*" />
                    <ColumnDefinition Width="0.2*" />
                    <ColumnDefinition Width="0.3*" />
                </Grid.ColumnDefinitions>
                <Label Text="{Binding Name}" TextColor="{StaticResource NormalTextColor}" FontAttributes="Bold" />
                <Label Grid.Column="1" Text="{Binding Age}" TextColor="{StaticResource NormalTextColor}" />
                <Label Grid.Column="2" Text="{Binding Location}" TextColor="{StaticResource NormalTextColor}" HorizontalTextAlignment="End" />
            </Grid>
        </ViewCell>
    </DataTemplate>
</ResourceDictionary>
```

这`ResourceDictionary`包含单个资源，这是一个对象类型`DataTemplate`。

可以实例化`MyResourceDictionary`通过将它置于一对之间`Resources`属性元素标记，例如，在`ContentPage`:

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <local:MyResourceDictionary />
    </ContentPage.Resources>
    ...
</ContentPage>
```

实例`MyResourceDictionary`设置为`Resources`属性的`ContentPage`对象。

但是, 这种方法有一些限制:`ResourceDictionary`的`Resources` 属性只`ContentPage`引用此项。 在大多数情况下，所需的选项包括其他`ResourceDictionary`实例和可能是其他资源。

此任务需要合并的资源字典。

## <a name="merged-resource-dictionaries"></a>合并资源字典

合并的资源字典合并一个或多`ResourceDictionary`到另一个实例`ResourceDictionary`。 可以通过设置执行此 XAML 文件中[ `MergedDictionaries` ](xref:Xamarin.Forms.ResourceDictionary.MergedDictionaries)属性设置为一个或多个将合并到应用程序、 页或控件级别的资源字典`ResourceDictionary`。

> [!IMPORTANT]
> `ResourceDictionary` 此外定义了[ `MergedWith` ](xref:Xamarin.Forms.ResourceDictionary.MergedWith)属性。 不使用此属性;它开始已弃用 Xamarin.Forms 3.0。

实例`MyResourceDictionary`可以合并到任何应用程序、 页或控件级别`ResourceDictionary`。 下面的 XAML 代码示例显示了其合并为一个页级别`ResourceDictionary`使用`MergedDictionaries`属性：

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <ResourceDictionary>
            <ResourceDictionary.MergedDictionaries>
                <local:MyResourceDictionary />
            </ResourceDictionary.MergedDictionaries>
        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

该标记显示了仅的实例`MyResourceDictionary`添加到`ResourceDictionary`但你还可以引用其他`ResourceDictionary`实例内`MergedDictionary`属性元素标记，并位于这些标记之外的其他资源：

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <ResourceDictionary>

            <!-- Add more resources here -->

            <ResourceDictionary.MergedDictionaries>

                <!-- Add more resource dictionaries here -->

                <local:MyResourceDictionary />

                <!-- Add more resource dictionaries here -->

            </ResourceDictionary.MergedDictionaries>

            <!-- Add more resources here -->

        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

可能只有一个`MergedDictionaries`主题中`ResourceDictionary`，但您可以将任意多个`ResourceDictionary`根据需要在这里实例。

合并时[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)资源共享相同`x:Key`属性值，Xamarin.Forms 使用以下资源优先级：

1. 资源字典的本地资源中。
1. 合并资源字典中包含的资源通过已弃用[ `MergedWith` ](xref:Xamarin.Forms.ResourceDictionary.MergedWith)属性。
1. 已通过合并资源字典中包含的资源`MergedDictionaries`集合中的列出顺序反向`MergedDictionaries`属性。

> [!NOTE]
> 搜索资源字典可以是计算密集型任务，如果应用程序包含多个大型资源字典。 因此，若要避免不需要的搜索，您应确保应用程序中的每一页仅使用适用于页的资源字典。

## <a name="merging-dictionaries-in-xamarinforms-30"></a>Xamarin.Forms 3.0 中的合并字典

Xamarin.Forms 3.0，合并在一起的过程开头[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)实例已成为某种程度上更轻松、 更灵活。 `MergedDictionaries`属性元素标记不再需要。 相反，您将添加到资源字典另`ResourceDictionary`与新的标记[ `Source` ](xref:Xamarin.Forms.ResourceDictionary.Source)属性设置为使用的资源的 XAML 文件的文件名：

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <ResourceDictionary>

            <!-- Add more resources here -->

            <ResourceDictionary Source="MyResourceDictionary.xaml" />

            <!-- Add more resources here -->

        </ResourceDictionary>
    </ContentPage.Resources>
    ...
</ContentPage>
```

因为 Xamarin.Forms 3.0 自动实例化`ResourceDictionary`，这两个外部`ResourceDictionary`标记不再需要：

```xaml
<ContentPage ...>
    <ContentPage.Resources>

        <!-- Add more resources here -->

        <ResourceDictionary Source="MyResourceDictionary.xaml" />

        <!-- Add more resources here -->

    </ContentPage.Resources>
    ...
</ContentPage>
```

这种新语法 does_不_实例化`MyResourceDictionary`类。 相反，它引用的 XAML 文件。 有关原因代码隐藏文件 (**MyResourceDictionary.xaml.cs**) 不再需要。 您还可以删除`x:Class`的根标记的特性**MyResourceDictionary.xaml**文件。

## <a name="summary"></a>总结

本文介绍了如何创建和使用[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)，以及如何合并资源字典。 一个`ResourceDictionary`允许在单个位置中，定义和重新整个 Xamarin.Forms 应用程序中使用的资源。

## <a name="related-links"></a>相关链接

- [资源字典 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-resourcedictionaries)
- [样式](~/xamarin-forms/user-interface/styles/index.md)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)

## <a name="related-video"></a>相关视频

> [!Video https://channel9.msdn.com/Shows/XamarinShow/XamarinForms-101-Application-Resources/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]

