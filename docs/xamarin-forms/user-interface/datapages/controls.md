---
title: DataPages 控件引用
description: 本文介绍了 Xamarin. Forms DataPages NuGet 包中提供的控件。
ms.prod: xamarin
ms.assetid: 891615D0-E8BD-4ACC-A7F0-4C3725FBCC31
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: e92669d9938b9fe48a1a589e0465acd03f129716
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70759883"
---
# <a name="datapages-controls-reference"></a>DataPages 控件引用

![](~/media/shared/preview.png "此 API 目前处于预览状态")

> [!IMPORTANT]
> DataPages 要求使用 Xamarin. Forms 主题引用来呈现。 这涉及到将[xamarin. Base](https://www.nuget.org/packages/Xamarin.Forms.Theme.Base/) nuget 包安装到项目中，然后将其后跟[xamarin](https://www.nuget.org/packages/Xamarin.Forms.Theme.Light/)或[xamarin. 暗体](https://www.nuget.org/packages/Xamarin.Forms.Theme.Dark/)nuget 包。

Xamarin DataPages Nuget 包含多个可以利用数据源绑定的控件。

若要在 XAML 中使用这些控件，请确保已包括命名空间，例如， `xmlns:pages`请参阅下面的声明：

```xaml
<ContentPage
    xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:pages="clr-namespace:Xamarin.Forms.Pages;assembly=Xamarin.Forms.Pages"
    x:Class="DataPagesDemo.Detail">
```

下面的示例包含`DynamicResource`需要在项目的资源字典中使用的引用。 此外，还提供了一个示例，说明如何生成[自定义控件](#custom)

## <a name="built-in-controls"></a>内置控件

* [HeroImage](#heroimage)
* [ListItem](#listitem)

<a name="heroimage" />

### <a name="heroimage"></a>HeroImage

`HeroImage`控件具有四个属性：

* 文本
* 详细信息
* ImageSource
* 方面

```xaml
<pages:HeroImage
    ImageSource="{ DynamicResource HeroImageImage }"
    Text="Keith Ballinger"
    Detail="Xamarin"
/>
```

**Android**

![](controls-images/heroimage-light-android.png "在 Android 上 HeroImage 控件") ![](controls-images/heroimage-dark-android.png "HeroImage 在 Android 上的控件")

**iOS**

![](controls-images/heroimage-light-ios.png "在 iOS 上 HeroImage 控件") ![](controls-images/heroimage-dark-ios.png "HeroImage 在 iOS 上的控件")

<a name="listitem" />

### <a name="listitem"></a>ListItem

`ListItem`控件的布局类似于本机 iOS 和 Android 列表或表行，但也可以用作常规视图。 在下面的示例代码中`StackLayout`，它显示在内，但也可用于数据绑定 scolling 列表控件中。

有五个属性：

* 标题
* 详细信息
* ImageSource
* PlaceholdImageSource
* 方面

```xaml
<StackLayout Spacing="0">
    <pages:ListItemControl
        Detail="Xamarin"
        ImageSource="{ DynamicResource UserImage }"
        Title="Miguel de Icaza"
        PlaceholdImageSource="{ DynamicResource IconImage }"
    />
```

这些屏幕截图`ListItem`在 iOS 和 Android 平台上使用浅色和深色主题显示：

**Android**

![](controls-images/listitem-light-android.png "Android 上的 \"")有![](controls-images/listitem-dark-android.png "Android 上的 \"")有

**iOS**

![](controls-images/listitem-light-ios.png "IOS 上的 \"")有![](controls-images/listitem-dark-ios.png "IOS 上的 \"")有

## <a name="custom-control-example"></a>自定义控件示例

此自定义`CardView`控件的目标是与本机 Android CardView 相似。

它将包含三个属性：

* 文本
* 详细信息
* ImageSource

目标是一个自定义控件，其外观类似于下面的代码（请注意， `xmlns:local`需要一个引用当前程序集的自定义）：

```xaml
<local:CardView
  ImageSource="{ DynamicResource CardViewImage }"
  Text="CardView Text"
  Detail="CardView Detail"
/>
```

它应类似于下面的屏幕截图，使用的颜色与内置的轻型和深色主题相对应：

**Android**

![](controls-images/cardview-light-android.png "Android 上的 CardView 自定义控件")![](controls-images/cardview-dark-android.png "Android 上的 CardView 自定义控件")

**iOS**

![](controls-images/cardview-light-ios.png "在 iOS 上 CardView 自定义控件") ![](controls-images/cardview-dark-ios.png "CardView 在 iOS 上的自定义控件")

<a name="custom" />

### <a name="building-the-custom-cardview"></a>构建自定义 CardView

1. [DataView 子类](#1)
2. [定义字体、布局和边距](#2)
3. [为控件的子级创建样式](#3)
4. [创建控件布局模板](#4)
5. [添加特定于主题的资源](#5)
6. [设置 CardView 类的 System.windows.controls.controltemplate>](#6)
7. [将控件添加到页面](#7)

<a name="1" />

#### <a name="1-dataview-subclass"></a>1.DataView 子类

的C#子类`DataView`定义控件的可绑定属性。

```csharp
public class CardView : DataView
{
    public static readonly BindableProperty TextProperty =
        BindableProperty.Create ("Text", typeof (string), typeof (CardView), null, BindingMode.TwoWay);

    public string Text
    {
        get { return (string)GetValue (TextProperty); }
        set { SetValue (TextProperty, value); }
    }

    public static readonly BindableProperty DetailProperty =
        BindableProperty.Create ("Detail", typeof (string), typeof (CardView), null, BindingMode.TwoWay);

    public string Detail
    {
        get { return (string)GetValue (DetailProperty); }
        set { SetValue (DetailProperty, value); }
    }

    public static readonly BindableProperty ImageSourceProperty =
        BindableProperty.Create ("ImageSource", typeof (ImageSource), typeof (CardView), null, BindingMode.TwoWay);

    public ImageSource ImageSource
    {
        get { return (ImageSource)GetValue (ImageSourceProperty); }
        set { SetValue (ImageSourceProperty, value); }
    }

    public CardView()
    {
    }
}
```

<a name="2" />

#### <a name="2-define-font-layout-and-margins"></a>2.定义字体、布局和边距

控件设计器会将这些值计算为自定义控件的用户界面设计的一部分。 如果需要平台特定的规范，则`OnPlatform`使用元素。

请注意，某些值是`StaticResource`指，这些值将在[步骤 5](#5)中定义。

```xml
<!-- CARDVIEW FONT SIZES -->
<OnPlatform x:TypeArguments="x:Double" x:Key="CardViewTextFontSize">
        <On Platform="iOS, Android" Value="15" />
</OnPlatform>

<OnPlatform x:TypeArguments="x:Double" x:Key="CardViewDetailFontSize">
        <On Platform="iOS, Android" Value="13" />
</OnPlatform>

<OnPlatform x:TypeArguments="Color"    x:Key="CardViewTextTextColor">
        <On Platform="iOS" Value="{StaticResource iOSCardViewTextTextColor}" />
        <On Platform="Android" Value="{StaticResource AndroidCardViewTextTextColor}" />
</OnPlatform>

<OnPlatform x:TypeArguments="Thickness"    x:Key="CardViewTextlMargin">
        <On Platform="iOS" Value="12,10,12,4" />
        <On Platform="Android" Value="20,0,20,5" />
</OnPlatform>

<OnPlatform x:TypeArguments="Color"    x:Key="CardViewDetailTextColor">
        <On Platform="iOS" Value="{StaticResource iOSCardViewDetailTextColor}" />
        <On Platform="Android" Value="{StaticResource AndroidCardViewDetailTextColor}" />
</OnPlatform>

<OnPlatform x:TypeArguments="Thickness"    x:Key="CardViewDetailMargin">
        <On Platform="iOS" Value="12,0,10,12" />
        <On Platform="Android" Value="20,0,20,20" />
</OnPlatform>

<OnPlatform x:TypeArguments="Color"    x:Key="CardViewBackgroundColor">
        <On Platform="iOS" Value="{StaticResource iOSCardViewBackgroundColor}" />
        <On Platform="Android" Value="{StaticResource AndroidCardViewBackgroundColor}" />
</OnPlatform>

<OnPlatform x:TypeArguments="x:Double" x:Key="CardViewShadowSize">
        <On Platform="iOS" Value="2" />
        <On Platform="Android" Value="5" />
</OnPlatform>

<OnPlatform x:TypeArguments="x:Double" x:Key="CardViewCornerRadius">
        <On Platform="iOS" Value="0" />
        <On Platform="Android" Value="4" />
</OnPlatform>

<OnPlatform x:TypeArguments="Color"    x:Key="CardViewShadowColor">
        <On Platform="iOS, Android" Value="#CDCDD1" />
</OnPlatform>
```

<a name="3" />

#### <a name="3-create-styles-for-the-controls-children"></a>3.为控件的子级创建样式

引用定义的所有元素，以创建将在自定义控件中使用的子元素：

```xml
<!-- EXPLICIT STYLES (will be Classes) -->
<Style TargetType="Label" x:Key="CardViewTextStyle">
    <Setter Property="FontSize" Value="{ StaticResource CardViewTextFontSize }" />
    <Setter Property="TextColor" Value="{ StaticResource CardViewTextTextColor }" />
    <Setter Property="HorizontalOptions" Value="Start" />
    <Setter Property="Margin" Value="{ StaticResource CardViewTextlMargin }" />
    <Setter Property="HorizontalTextAlignment" Value="Start" />
</Style>

<Style TargetType="Label" x:Key="CardViewDetailStyle">
    <Setter Property="HorizontalTextAlignment" Value="Start" />
    <Setter Property="TextColor" Value="{ StaticResource CardViewDetailTextColor }" />
    <Setter Property="FontSize" Value="{ StaticResource CardViewDetailFontSize }" />
    <Setter Property="HorizontalOptions" Value="Start" />
    <Setter Property="Margin" Value="{ StaticResource CardViewDetailMargin }" />
</Style>

<Style TargetType="Image" x:Key="CardViewImageImageStyle">
    <Setter Property="HorizontalOptions" Value="Center" />
    <Setter Property="VerticalOptions" Value="Center" />
    <Setter Property="WidthRequest" Value="220"/>
    <Setter Property="HeightRequest" Value="165"/>
</Style>
```

<a name="4" />

#### <a name="4-create-the-control-layout-template"></a>4.创建控件布局模板

使用上面定义的资源，在控件模板中显式声明自定义控件的可视化设计：

```xml
<!--- CARDVIEW -->
<ControlTemplate x:Key="CardViewControlControlTemplate">
  <StackLayout
    Spacing="0"
    BackgroundColor="{ TemplateBinding BackgroundColor }"
  >

    <!-- CARDVIEW IMAGE -->
    <Image
      Source="{ TemplateBinding ImageSource }"
      HorizontalOptions="FillAndExpand"
      VerticalOptions="StartAndExpand"
      Aspect="AspectFill"
      Style="{ StaticResource CardViewImageImageStyle }"
    />

    <!-- CARDVIEW TEXT -->
    <Label
      Text="{ TemplateBinding Text }"
      LineBreakMode="WordWrap"
      VerticalOptions="End"
      Style="{ StaticResource CardViewTextStyle }"
    />

    <!-- CARDVIEW DETAIL -->
    <Label
      Text="{ TemplateBinding Detail }"
      LineBreakMode="WordWrap"
      VerticalOptions="End"
      Style="{ StaticResource CardViewDetailStyle }" />

  </StackLayout>

</ControlTemplate>
```

<a name="5" />

#### <a name="5-add-the-theme-specific-resources"></a>5.添加特定于主题的资源

由于这是一个自定义控件，因此请添加与正在使用资源字典的主题匹配的资源：

##### <a name="light-theme-colors"></a>浅色主题颜色

```xaml
<Color x:Key="iOSCardViewBackgroundColor">#FFFFFF</Color>
<Color x:Key="AndroidCardViewBackgroundColor">#FFFFFF</Color>

<Color x:Key="AndroidCardViewTextTextColor">#030303</Color>
<Color x:Key="iOSCardViewTextTextColor">#030303</Color>

<Color x:Key="AndroidCardViewDetailTextColor">#8F8E94</Color>
<Color x:Key="iOSCardViewDetailTextColor">#8F8E94</Color>
```

##### <a name="dark-theme-colors"></a>深色主题颜色

```xaml
<!-- CARD VIEW COLORS -->
            <Color x:Key="iOSCardViewBackgroundColor">#404040</Color>
            <Color x:Key="AndroidCardViewBackgroundColor">#404040</Color>

            <Color x:Key="AndroidCardViewTextTextColor">#FFFFFF</Color>
            <Color x:Key="iOSCardViewTextTextColor">#FFFFFF</Color>

            <Color x:Key="AndroidCardViewDetailTextColor">#B5B4B9</Color>
            <Color x:Key="iOSCardViewDetailTextColor">#B5B4B9</Color>
```

<a name="6" />

#### <a name="6-set-the-controltemplate-for-the-cardview-class"></a>6.设置 CardView 类的 System.windows.controls.controltemplate>

最后，确保在C# [步骤 1](#1)中创建的类使用在`Style` `Setter` [步骤 4](#4)中使用元素定义的控件模板。

```xml
<Style TargetType="local:CardView">
    <Setter Property="ControlTemplate" Value="{ StaticResource CardViewControlControlTemplate }" />
  ... some custom styling omitted
  <Setter Property="BackgroundColor" Value="{ StaticResource CardViewBackgroundColor }" />
</Style>
```

<a name="7" />

#### <a name="7-add-the-control-to-a-page"></a>7.将控件添加到页面

`CardView`控件现在可以添加到页中。 下面的示例演示了它在中`StackLayout`托管的：

```xaml
<StackLayout Spacing="0">
  <local:CardView
    Margin="12,6"
    ImageSource="{ DynamicResource CardViewImage }"
    Text="CardView Text"
    Detail="CardView Detail"
  />
</StackLayout>

```
