---
title: 在 XAML 中的本机视图
description: 从 iOS、 Android 和通用 Windows 平台的本机视图可以直接引用 Xamarin.Forms XAML 文件中。 上的本机视图，可以设置属性和事件处理程序，它们可以与 Xamarin.Forms 视图进行交互。 本文演示如何使用 Xamarin.Forms XAML 文件中的本机视图。
ms.prod: xamarin
ms.assetid: 7A856D31-B300-409E-9AEB-F8A4DB99B37E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/24/2016
ms.openlocfilehash: 7a5c09bfe46b9e775383889e07fd93094ba9bf68
ms.sourcegitcommit: a9c60f50b40203dd784e3e790b0d83e2bfc86129
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65731526"
---
# <a name="native-views-in-xaml"></a>在 XAML 中的本机视图

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/NativeViews/NativeSwitch/)

_从 iOS、 Android 和通用 Windows 平台的本机视图可以直接引用 Xamarin.Forms XAML 文件中。上的本机视图，可以设置属性和事件处理程序，它们可以与 Xamarin.Forms 视图进行交互。本文演示如何使用 Xamarin.Forms XAML 文件中的本机视图。_

本文讨论以下主题：

- [使用本机视图](#consuming)– 使用 XAML 中的本机视图的过程。
- [使用本机绑定](#native_bindings)– 数据绑定到和从本机视图的属性。
- [将参数传递给本机视图](#passing_arguments)– 将实参传递给本机视图构造函数，并调用工厂方法的本机视图。
- [从代码中引用的本机视图](#native_view_code)– 正在检索本机视图实例声明在 XAML 文件中，从其代码隐藏文件。
- [子类化的本机视图](#subclassing)– 子类化来定义 XAML 友好 API 的本机视图。  

<a name="overview" />

## <a name="overview"></a>概述

若要嵌入到 Xamarin.Forms XAML 文件的本机视图：

1. 添加`xmlns`包含本机视图的命名空间的 XAML 文件中的命名空间声明。
1. 在 XAML 文件中创建本机视图的实例。

> [!IMPORTANT]
> 必须为使用本机视图任何 XAML 页禁用已编译的 XAML。 这可以通过修饰与 XAML 页面的代码隐藏类来实现`[XamlCompilation(XamlCompilationOptions.Skip)]`属性。 有关 XAML 编译的详细信息，请参阅[Xamarin.Forms 中 XAML 编译](~/xamarin-forms/xaml/xamlc.md)。

若要从代码隐藏文件引用本机视图，必须使用共享资产项目 (SAP)，并使用条件编译指令将特定于平台的代码包装。 有关详细信息请参阅[从代码中引用的本机视图](#native_view_code)。

<a name="consuming" />

## <a name="consuming-native-views"></a>使用本机视图

下面的代码示例演示如何使用 Xamarin.Forms 到每个平台的本机视图[ `ContentPage` ](xref:Xamarin.Forms.ContentPage):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:win="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        x:Class="NativeViews.NativeViewDemo">
    <StackLayout Margin="20">
        <ios:UILabel Text="Hello World" TextColor="{x:Static ios:UIColor.Red}" View.HorizontalOptions="Start" />
        <androidWidget:TextView Text="Hello World" x:Arguments="{x:Static androidLocal:MainActivity.Instance}" />
        <win:TextBlock Text="Hello World" />
    </StackLayout>
</ContentPage>
```

以及指定`clr-namespace`并`assembly`本机视图命名空间，`targetPlatform`还必须指定。 此属性应设置为的值之一[ `TargetPlatform` ](xref:Xamarin.Forms.TargetPlatform)枚举，并通常将设置为`iOS`， `Android`，或`Windows`。 在运行时，XAML 分析器将忽略具有任何 XML 命名空间前缀`targetPlatform`这不匹配其运行应用程序的平台。

每个命名空间声明可以用于引用来自指定命名空间的任何类或结构。 例如，`ios`命名空间声明可用于引用在 iOS 中的任何类或结构`UIKit`命名空间。 可以通过 XAML，设置本机视图的属性，但属性和对象类型必须匹配。 例如，`UILabel.TextColor`属性设置为`UIColor.Red`使用`x:Static`标记扩展和`ios`命名空间。

可绑定属性和附加可绑定属性也可以设置上的本机视图通过使用`Class.BindableProperty="value"`语法。 每个本机视图包装在平台特定`NativeViewWrapper`实例，派生自[ `Xamarin.Forms.View` ](xref:Xamarin.Forms.View)类。 本机视图上设置可绑定属性或附加可绑定属性传输到包装器的属性值。 例如，可以通过设置指定居中的水平布局`View.HorizontalOptions="Center"`本机视图上。

> [!NOTE]
> 请注意样式不能用于本机视图，因为样式仅可以由支持的属性为目标`BindableProperty`对象。

Android 小组件的构造函数通常需要 Android`Context`对象作为参数，并且此便可提供通过中的静态属性`MainActivity`类。 因此，当在 XAML 中, 创建 Android 小组件`Context`通常必须将对象传递给小组件的构造函数使用`x:Arguments`属性与`x:Static`标记扩展。 有关详细信息，请参阅[将参数传递到本机视图](#passing_arguments)。

> [!NOTE]
> 请注意命名与本机视图`x:Name`不能在.NET Standard 库项目或共享资产项目 (SAP)。 执行此操作将生成的变量的本机类型，这将导致编译错误。 但是，可以在包装的本机视图`ContentView`实例并检索在代码隐藏文件中，前提使用 SAP。 有关详细信息，请参阅[从代码引用本机视图](#native_view_code)。

<a name="native_bindings" />

## <a name="native-bindings"></a>本机绑定

数据绑定用来同步其数据源，使用 UI，并简化 Xamarin.Forms 应用程序显示的方式，并使用其数据进行交互。 提供的源对象实现`INotifyPropertyChanged`接口中的更改*源*对象会自动推送到*目标*对象的绑定框架和中的更改*目标*对象可以选择性地推送到*源*对象。

本机视图的属性还可以使用数据绑定。 下面的代码示例演示如何使用本机视图的属性的数据绑定：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:win="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:local="clr-namespace:NativeSwitch"
        x:Class="NativeSwitch.NativeSwitchPage">
    <StackLayout Margin="20">
        <Label Text="Native Views Demo" FontAttributes="Bold" HorizontalOptions="Center" />
        <Entry Placeholder="This Entry is bound to the native switch" IsEnabled="{Binding IsSwitchOn}" />
        <ios:UISwitch On="{Binding Path=IsSwitchOn, Mode=TwoWay, UpdateSourceEventName=ValueChanged}"
            OnTintColor="{x:Static ios:UIColor.Red}"
            ThumbTintColor="{x:Static ios:UIColor.Blue}" />
        <androidWidget:Switch x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
            Checked="{Binding Path=IsSwitchOn, Mode=TwoWay, UpdateSourceEventName=CheckedChange}"
            Text="Enable Entry?" />
        <win:ToggleSwitch Header="Enable Entry?"
            OffContent="No"
            OnContent="Yes"
            IsOn="{Binding IsSwitchOn, Mode=TwoWay, UpdateSourceEventName=Toggled}" />
    </StackLayout>
</ContentPage>

```

该页面包含[ `Entry` ](xref:Xamarin.Forms.Entry)其[ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled)属性绑定到`NativeSwitchPageViewModel.IsSwitchOn`属性。 [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)页的设置为的新实例`NativeSwitchPageViewModel`类在代码隐藏文件中，与 ViewModel 类实现`INotifyPropertyChanged`接口。

此页还包含用于每个平台的本机交换机。 使用每个本机交换机[ `TwoWay` ](xref:Xamarin.Forms.BindingMode.TwoWay)要更新的值绑定`NativeSwitchPageViewModel.IsSwitchOn`属性。 因此，此开关处于关闭状态，`Entry`处于禁用状态，以及何时开关为开，`Entry`已启用。 下面的屏幕截图显示了每个平台上的此功能：

![](xaml-images/native-switch-disabled.png "本机交换机已禁用")
![](xaml-images/native-switch-enabled.png "本机开关已启用")

自动支持双向绑定，前提是本机属性实现`INotifyPropertyChanged`，在 iOS 上，支持键值对观察 (KVO)，或者是`DependencyProperty`UWP 上。 但是，许多本机视图不支持属性更改通知。 对于这些视图中，您可以指定[ `UpdateSourceEventName` ](xref:Xamarin.Forms.Binding.UpdateSourceEventName)用作绑定表达式的一部分的属性值。 此属性应设置为目标属性已更改时发出信号的本机视图中的事件的名称。 然后，本机开关的值发生更改时，`Binding`类会通知用户已更改开关值和`NativeSwitchPageViewModel.IsSwitchOn`更新属性值。

<a name="passing_arguments" />

## <a name="passing-arguments-to-native-views"></a>传递自变量到本机视图

可以将构造函数自变量传递给使用的本机视图`x:Arguments`属性与`x:Static`标记扩展。 此外，本机视图工厂方法 (`public static`返回对象或值类或结构，它定义的方法的类型相同的方法) 可以通过指定方法的调用名称使用`x:FactoryMethod`特性，并且其自变量使用`x:Arguments`属性。

下面的代码示例演示了这两种技术：

```xaml
<ContentPage ...
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidGraphics="clr-namespace:Android.Graphics;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:winControls="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255, Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:winMedia="clr-namespace:Windows.UI.Xaml.Media;assembly=Windows, Version=255.255.255.255, Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:winText="clr-namespace:Windows.UI.Text;assembly=Windows, Version=255.255.255.255, Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:winui="clr-namespace:Windows.UI;assembly=Windows, Version=255.255.255.255, Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows">
        ...
        <ios:UILabel Text="Simple Native Color Picker" View.HorizontalOptions="Center">
            <ios:UILabel.Font>
                <ios:UIFont x:FactoryMethod="FromName">
                    <x:Arguments>
                        <x:String>Papyrus</x:String>
                        <x:Single>24</x:Single>
                    </x:Arguments>
                </ios:UIFont>
            </ios:UILabel.Font>
        </ios:UILabel>
        <androidWidget:TextView x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
                    Text="Simple Native Color Picker"
                    TextSize="24"
                    View.HorizontalOptions="Center">
            <androidWidget:TextView.Typeface>
                <androidGraphics:Typeface x:FactoryMethod="Create">
                    <x:Arguments>
                        <x:String>cursive</x:String>
                        <androidGraphics:TypefaceStyle>Normal</androidGraphics:TypefaceStyle>
                    </x:Arguments>
                </androidGraphics:Typeface>
            </androidWidget:TextView.Typeface>
        </androidWidget:TextView>
        <winControls:TextBlock Text="Simple Native Color Picker"
                    FontSize="20"
                    FontStyle="{x:Static winText:FontStyle.Italic}"
                    View.HorizontalOptions="Center">
            <winControls:TextBlock.FontFamily>
                <winMedia:FontFamily>
                    <x:Arguments>
                        <x:String>Georgia</x:String>
                    </x:Arguments>
                </winMedia:FontFamily>
            </winControls:TextBlock.FontFamily>
        </winControls:TextBlock>
        ...
</ContentPage>
```

[ `UIFont.FromName` ](xref:UIKit.UIFont.FromName*)工厂方法用于设置[ `UILabel.Font` ](xref:UIKit.UILabel.Font)属性设置为一个新[ `UIFont` ](xref:UIKit.UIFont)在 iOS 上。 `UIFont`子级的方法参数通过指定名称和大小`x:Arguments`属性。

[ `Typeface.Create` ](https://developer.xamarin.com/api/member/Android.Graphics.Typeface.Create/p/System.String/Android.Graphics.TypefaceStyle/)工厂方法用于设置[ `TextView.Typeface` ](https://developer.xamarin.com/api/property/Android.Widget.TextView.Typeface/)属性设置为一个新[ `Typeface` ](https://developer.xamarin.com/api/type/Android.Graphics.Typeface/)在 Android 上。 `Typeface`子级的方法参数通过指定系列名称和样式`x:Arguments`属性。

[ `FontFamily` ](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.fontfamily)构造函数用于设置[ `TextBlock.FontFamily` ](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.fontfamily)属性设置为一个新`FontFamily`通用 Windows 平台 (UWP)。 `FontFamily`的子级的方法参数通过指定名称`x:Arguments`属性。

> [!NOTE]
> 参数必须与构造函数或工厂方法所需的类型匹配。

以下屏幕截图显示指定工厂方法和构造函数参数，若要将字体设置不同的本机视图上的结果：

![](xaml-images/passing-arguments.png "设置字体上的本机视图")

有关在 XAML 中传递自变量的详细信息，请参阅[在 XAML 中传递参数](~/xamarin-forms/xaml/passing-arguments.md)。

<a name="native_view_code" />

## <a name="referring-to-native-views-from-code"></a>从代码中引用的本机视图

尽管不能使用本机视图命名为`x:Name`属性，它是可以检索在共享访问项目中，其代码隐藏文件中的 XAML 文件中声明的本机视图实例，前提是本机视图是子级[ `ContentView` ](xref:Xamarin.Forms.ContentView) ，它指定`x:Name`属性值。 然后，在代码隐藏文件中的条件编译指令内您应该：

1. 检索[ `ContentView.Content` ](xref:Xamarin.Forms.ContentView.Content)属性值，并将其转换为特定于平台的`NativeViewWrapper`类型。
1. 检索`NativeViewWrapper.NativeElement`属性并将其转换为本机视图类型。

然后执行所需的操作的本机视图调用本机 API。 此方法还提供了好处，针对不同平台的多个 XAML 本机视图可以是相同的子级[ `ContentView` ](xref:Xamarin.Forms.ContentView)。 下面的代码示例演示了此种方法：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:androidWidget="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:winControls="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:local="clr-namespace:NativeViewInsideContentView"
        x:Class="NativeViewInsideContentView.NativeViewInsideContentViewPage">
    <StackLayout Margin="20">
        <ContentView x:Name="contentViewTextParent" HorizontalOptions="Center" VerticalOptions="CenterAndExpand">
            <ios:UILabel Text="Text in a UILabel" TextColor="{x:Static ios:UIColor.Red}" />
            <androidWidget:TextView x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
                Text="Text in a TextView" />
              <winControls:TextBlock Text="Text in a TextBlock" />
        </ContentView>
        <ContentView x:Name="contentViewButtonParent" HorizontalOptions="Center" VerticalOptions="EndAndExpand">
            <ios:UIButton TouchUpInside="OnButtonTap" View.HorizontalOptions="Center" View.VerticalOptions="Center" />
            <androidWidget:Button x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
                Text="Scale and Rotate Text"
                Click="OnButtonTap" />
            <winControls:Button Content="Scale and Rotate Text" />
        </ContentView>
    </StackLayout>
</ContentPage>
```

在上面的示例中，为每个平台的本机视图是子级[ `ContentView` ](xref:Xamarin.Forms.ContentView)控件，具有`x:Name`属性值用于检索`ContentView`代码隐藏中：

```csharp
public partial class NativeViewInsideContentViewPage : ContentPage
{
    public NativeViewInsideContentViewPage()
    {
        InitializeComponent();

#if __IOS__
        var wrapper = (Xamarin.Forms.Platform.iOS.NativeViewWrapper)contentViewButtonParent.Content;
        var button = (UIKit.UIButton)wrapper.NativeView;
        button.SetTitle("Scale and Rotate Text", UIKit.UIControlState.Normal);
        button.SetTitleColor(UIKit.UIColor.Black, UIKit.UIControlState.Normal);
#endif
#if __ANDROID__
        var wrapper = (Xamarin.Forms.Platform.Android.NativeViewWrapper)contentViewTextParent.Content;
        var textView = (Android.Widget.TextView)wrapper.NativeView;
        textView.SetTextColor(Android.Graphics.Color.Red);
#endif
#if WINDOWS_UWP
        var textWrapper = (Xamarin.Forms.Platform.UWP.NativeViewWrapper)contentViewTextParent.Content;
        var textBlock = (Windows.UI.Xaml.Controls.TextBlock)textWrapper.NativeElement;
        textBlock.Foreground = new Windows.UI.Xaml.Media.SolidColorBrush(Windows.UI.Colors.Red);
        var buttonWrapper = (Xamarin.Forms.Platform.UWP.NativeViewWrapper)contentViewButtonParent.Content;
        var button = (Windows.UI.Xaml.Controls.Button)buttonWrapper.NativeElement;
        button.Click += (sender, args) => OnButtonTap(sender, EventArgs.Empty);
#endif
    }

    async void OnButtonTap(object sender, EventArgs e)
    {
        contentViewButtonParent.Content.IsEnabled = false;
        contentViewTextParent.Content.ScaleTo(2, 2000);
        await contentViewTextParent.Content.RotateTo(360, 2000);
        contentViewTextParent.Content.ScaleTo(1, 2000);
        await contentViewTextParent.Content.RelRotateTo(360, 2000);
        contentViewButtonParent.Content.IsEnabled = true;
    }
}
```

[ `ContentView.Content` ](xref:Xamarin.Forms.ContentView.Content)访问属性来检索为特定于平台的已包装的本机视图`NativeViewWrapper`实例。 `NativeViewWrapper.NativeElement`然后访问属性来检索作为其本机类型的本机视图。 然后调用本机视图 API 来执行所需的操作。

IOS 和 Android 的本机按钮共用同一个`OnButtonTap`事件处理程序，因为每个本机按钮使用`EventHandler`响应触摸事件委托。 但是，通用 Windows 平台 (UWP) 均使用单独`RoutedEventHandler`，这反过来将会占用`OnButtonTap`在此示例中的事件处理程序。 因此，单击本机按钮时，`OnButtonTap`事件处理程序执行，该缩放和旋转内包含的本机控件[ `ContentView` ](xref:Xamarin.Forms.ContentView)名为`contentViewTextParent`。 下面的屏幕截图演示了此每个平台上发生：

![](xaml-images/contentview.png "包含的本机控件 ContentView")

<a name="subclassing" />

## <a name="subclassing-native-views"></a>子类化本机视图

许多 iOS 和 Android 的本机视图并不适合因为它们使用的方法，而不是属性，若要设置该控件在 XAML 中实例化。 此问题的解决方案是为子类中定义多个 XAML 友好 API，使用属性来设置该控件，并使用独立于平台的事件的包装器的本机视图。 已包装的本机视图可放置在共享资产项目 (SAP) 和使用条件编译指令括住或置于特定于平台的项目中和从 XAML 引用.NET Standard 库项目中。

下面的代码示例演示了使用 Xamarin.Forms 页面创建子类的本机视图：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
        xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
        xmlns:ios="clr-namespace:UIKit;assembly=Xamarin.iOS;targetPlatform=iOS"
        xmlns:iosLocal="clr-namespace:SubclassedNativeControls.iOS;assembly=SubclassedNativeControls.iOS;targetPlatform=iOS"
        xmlns:android="clr-namespace:Android.Widget;assembly=Mono.Android;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SimpleColorPicker.Droid;assembly=SimpleColorPicker.Droid;targetPlatform=Android"
        xmlns:androidLocal="clr-namespace:SubclassedNativeControls.Droid;assembly=SubclassedNativeControls.Droid;targetPlatform=Android"
        xmlns:winControls="clr-namespace:Windows.UI.Xaml.Controls;assembly=Windows, Version=255.255.255.255,
            Culture=neutral, PublicKeyToken=null, ContentType=WindowsRuntime;targetPlatform=Windows"
        xmlns:local="clr-namespace:SubclassedNativeControls"
        x:Class="SubclassedNativeControls.SubclassedNativeControlsPage">
    <StackLayout Margin="20">
        <Label Text="Subclassed Native Views Demo" FontAttributes="Bold" HorizontalOptions="Center" />
        <StackLayout Orientation="Horizontal">
          <Label Text="You have chosen:" />
          <Label Text="{Binding SelectedFruit}" />      
        </StackLayout>
        <iosLocal:MyUIPickerView ItemsSource="{Binding Fruits}"
            SelectedItem="{Binding SelectedFruit, Mode=TwoWay, UpdateSourceEventName=SelectedItemChanged}" />
        <androidLocal:MySpinner x:Arguments="{x:Static androidLocal:MainActivity.Instance}"
            ItemsSource="{Binding Fruits}"
            SelectedObject="{Binding SelectedFruit, Mode=TwoWay, UpdateSourceEventName=ItemSelected}" />
        <winControls:ComboBox ItemsSource="{Binding Fruits}"
            SelectedItem="{Binding SelectedFruit, Mode=TwoWay, UpdateSourceEventName=SelectionChanged}" />
    </StackLayout>
</ContentPage>
```

该页面包含[ `Label` ](xref:Xamarin.Forms.Label)显示用户选择的本机控件从水果。 `Label`绑定到`SubclassedNativeControlsPageViewModel.SelectedFruit`属性。 [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)页的设置为的新实例`SubclassedNativeControlsPageViewModel`类在代码隐藏文件中，与 ViewModel 类实现`INotifyPropertyChanged`接口。

该页还包含每个平台的本机选取器视图。 每个本机视图显示水果的集合的绑定及其`ItemSource`属性设置为`SubclassedNativeControlsPageViewModel.Fruits`集合。 这允许用户选取水果，如以下屏幕截图中所示：

![](xaml-images/sub-classed.png "子类化的本机视图")

IOS 和 Android 上本机选取器使用方法设置这些控件。 因此，这些选取器必须子类化来公开属性，以使它们适合于 XAML 应用。 在通用 Windows 平台 (UWP)，`ComboBox`已 XAML 友好，因此，不需要子类化。

### <a name="ios"></a>iOS

IOS 实现子类[ `UIPickerView` ](xref:UIKit.UIPickerView)视图中，并公开属性和从 XAML 可轻松使用的事件：

```csharp
public class MyUIPickerView : UIPickerView
{
    public event EventHandler<EventArgs> SelectedItemChanged;

    public MyUIPickerView()
    {
        var model = new PickerModel();
        model.ItemChanged += (sender, e) =>
        {
            if (SelectedItemChanged != null)
            {
                SelectedItemChanged.Invoke(this, e);
            }
        };
        Model = model;
    }

    public IList<string> ItemsSource
    {
        get
        {
            var pickerModel = Model as PickerModel;
            return (pickerModel != null) ? pickerModel.Items : null;
        }
        set
        {
            var model = Model as PickerModel;
            if (model != null)
            {
                model.Items = value;
            }
        }
    }

    public string SelectedItem
    {
        get { return (Model as PickerModel).SelectedItem; }
        set { }
    }
}
```

`MyUIPickerView`类公开`ItemsSource`并`SelectedItem`属性，和一个`SelectedItemChanged`事件。 一个[ `UIPickerView` ](xref:UIKit.UIPickerView)要求基础[ `UIPickerViewModel` ](xref:UIKit.UIPickerViewModel)数据模型中，将访问`MyUIPickerView`属性和事件。 `UIPickerViewModel`数据模型提供的`PickerModel`类：

```csharp
class PickerModel : UIPickerViewModel
{
    int selectedIndex = 0;
    public event EventHandler<EventArgs> ItemChanged;
    public IList<string> Items { get; set; }

    public string SelectedItem
    {
        get
        {
            return Items != null && selectedIndex >= 0 && selectedIndex < Items.Count ? Items[selectedIndex] : null;
        }
    }

    public override nint GetRowsInComponent(UIPickerView pickerView, nint component)
    {
        return Items != null ? Items.Count : 0;
    }

    public override string GetTitle(UIPickerView pickerView, nint row, nint component)
    {
        return Items != null && Items.Count > row ? Items[(int)row] : null;
    }

    public override nint GetComponentCount(UIPickerView pickerView)
    {
        return 1;
    }

    public override void Selected(UIPickerView pickerView, nint row, nint component)
    {
        selectedIndex = (int)row;
        if (ItemChanged != null)
        {
            ItemChanged.Invoke(this, new EventArgs());
        }
    }
}
```

`PickerModel`类提供的基础存储`MyUIPickerView`类，通过`Items`属性。 每当中的选定的项`MyUIPickerView`更改， [ `Selected` ](xref:UIKit.UIPickerViewModel.Selected*)执行方法时，该更新所选的索引和激发`ItemChanged`事件。 这可确保`SelectedItem`属性将始终返回由用户选择的最后一项。 此外，`PickerModel`类重写方法用于设置`MyUIPickerView`实例。

### <a name="android"></a>Android

Android 实现子类[ `Spinner` ](https://developer.xamarin.com/api/type/Android.Widget.Spinner/)视图中，并公开属性和从 XAML 可轻松使用的事件：

```csharp
class MySpinner : Spinner
{
    ArrayAdapter adapter;
    IList<string> items;

    public IList<string> ItemsSource
    {
        get { return items; }
        set
        {
            if (items != value)
            {
                items = value;
                adapter.Clear();

                foreach (string str in items)
                {
                    adapter.Add(str);
                }
            }
        }
    }

    public string SelectedObject
    {
        get { return (string)GetItemAtPosition(SelectedItemPosition); }
        set
        {
            if (items != null)
            {
                int index = items.IndexOf(value);
                if (index != -1)
                {
                    SetSelection(index);
                }
            }
        }
    }

    public MySpinner(Context context) : base(context)
    {
        ItemSelected += OnBindableSpinnerItemSelected;

        adapter = new ArrayAdapter(context, Android.Resource.Layout.SimpleSpinnerItem);
        adapter.SetDropDownViewResource(Android.Resource.Layout.SimpleSpinnerDropDownItem);
        Adapter = adapter;
    }

    void OnBindableSpinnerItemSelected(object sender, ItemSelectedEventArgs args)
    {
        SelectedObject = (string)GetItemAtPosition(args.Position);
    }
}
```

`MySpinner`类公开`ItemsSource`并`SelectedObject`属性，和一个`ItemSelected`事件。 显示的项目`MySpinner`类提供的[ `Adapter` ](https://developer.xamarin.com/api/type/Android.Widget.Adapter/)与视图关联和项填充到`Adapter`时`ItemsSource`首次设置属性。 每当中的选定的项`MySpinner`类的更改，`OnBindableSpinnerItemSelected`事件处理程序更新`SelectedObject`属性。

## <a name="summary"></a>总结

本文演示了如何使用 Xamarin.Forms XAML 文件中的本机视图。 上的本机视图，可以设置属性和事件处理程序，它们可以与 Xamarin.Forms 视图进行交互。


## <a name="related-links"></a>相关链接

- [NativeSwitch （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/NativeViews/NativeSwitch/)
- [Forms2Native （示例）](https://developer.xamarin.com/samples/xamarin-forms/Forms2Native/)
- [NativeViewInsideContentView （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/NativeViews/NativeViewInsideContentView/)
- [SubclassedNativeControls （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/NativeViews/SubclassedNativeControls/)
- [本机窗体](~/xamarin-forms/platform/native-forms.md)
- [在 XAML 中传递自变量](~/xamarin-forms/xaml/passing-arguments.md)
