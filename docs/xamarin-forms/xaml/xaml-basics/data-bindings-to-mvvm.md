---
title: 第 5 部分。 从数据绑定到 MVVM
description: MVVM 模式强制执行三个软件层之间的分隔 — XAML 用户界面，称为视图;基础数据，称为模型;并且在视图和模型之间的中介调用 ViewModel。
ms.prod: xamarin
ms.assetid: 48B37D44-4FB1-41B2-9A5E-6D383B041F81
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
ms.openlocfilehash: c7bf7ca28200004e2383631c68cdaa4299348ecb
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61174533"
---
# <a name="part-5-from-data-bindings-to-mvvm"></a>第 5 部分。 从数据绑定到 MVVM

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)

_模型-视图-视图模型 (MVVM) 体系结构模式的发明时与 XAML 记住。该模式会强制实施三个软件层之间的隔离，XAML 用户界面，称为视图;基础数据，称为模型;并且在视图和模型之间的中介调用 ViewModel。通过 XAML 文件中定义的数据绑定通常连接中的视图和 ViewModel。视图 BindingContext 通常是 ViewModel 的实例。_

## <a name="a-simple-viewmodel"></a>简单的 ViewModel

为 Viewmodel 的简介，让我们先来看一下不存在的程序。
前面你已了解如何在定义新的 XML 命名空间声明，以允许到其他程序集中的引用类的 XAML 文件。 下面是一个程序，用于定义 XML 命名空间声明为`System`命名空间：

```csharp
xmlns:sys="clr-namespace:System;assembly=mscorlib"
```

该程序可以使用`x:Static`若要获取当前日期和时间从静态`DateTime.Now`属性设置该`DateTime`值设置为`BindingContext`上`StackLayout`:

```xaml
<StackLayout BindingContext="{x:Static sys:DateTime.Now}" …>
```

`BindingContext` 是一个非常特殊属性：当您将设置`BindingContext`某个元素上，它由继承该元素的所有子级。 这意味着所有子级`StackLayout`具有此相同`BindingContext`，并且它们可以包含简单绑定到该对象的属性。

在中**One-Shot DateTime**程序，两个子级包含到这些属性的绑定`DateTime`值，但两个其他的子级包含似乎缺少绑定路径的绑定。 这意味着`DateTime`本身的值用于`StringFormat`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:sys="clr-namespace:System;assembly=mscorlib"
             x:Class="XamlSamples.OneShotDateTimePage"
             Title="One-Shot DateTime Page">

    <StackLayout BindingContext="{x:Static sys:DateTime.Now}"
                 HorizontalOptions="Center"
                 VerticalOptions="Center">

        <Label Text="{Binding Year, StringFormat='The year is {0}'}" />
        <Label Text="{Binding StringFormat='The month is {0:MMMM}'}" />
        <Label Text="{Binding Day, StringFormat='The day is {0}'}" />
        <Label Text="{Binding StringFormat='The time is {0:T}'}" />

    </StackLayout>
</ContentPage>
```

当然，最大的问题是日期和时间是集后首次生成页面时，并且永远不会更改：

[![](data-bindings-to-mvvm-images/oneshotdatetime.png "视图，用于显示日期和时间")](data-bindings-to-mvvm-images/oneshotdatetime-large.png#lightbox "视图，用于显示日期和时间")

XAML 文件可以显示时钟始终会显示当前时间，但它需要一些代码来帮助解决问题。当 MVVM、 模型和 ViewModel 方面的想法是完全用代码编写的类。 视图通常是 XAML 引用的文件，通过数据绑定在 ViewModel 中定义的属性。

正确的模型是未知的 ViewModel 中，并适当的 ViewModel 是未知的视图。 但是，通常一名程序员通过公开 ViewModel 与特定用户界面相关联的数据类型的数据类型。 例如，如果模型访问数据库，其中包含 8 位字符的 ASCII 字符串时，ViewModel 会需要这些字符串转换为 Unicode 字符串，以适应独占使用的用户界面中的 Unicode 之间转换。

在简单示例中的 MVVM （如那些如下所示），通常没有模型，和的模式涉及只是一个视图和 ViewModel 与数据绑定链接。

下面是具有只是单个属性名为时钟的 ViewModel `DateTime`，但该更新的`DateTime`属性每秒：

```csharp
using System;
using System.ComponentModel;
using Xamarin.Forms;

namespace XamlSamples
{
    class ClockViewModel : INotifyPropertyChanged
    {
        DateTime dateTime;

        public event PropertyChangedEventHandler PropertyChanged;

        public ClockViewModel()
        {
            this.DateTime = DateTime.Now;

            Device.StartTimer(TimeSpan.FromSeconds(1), () =>
                {
                    this.DateTime = DateTime.Now;
                    return true;
                });
        }

        public DateTime DateTime
        {
            set
            {
                if (dateTime != value)
                {
                    dateTime = value;

                    if (PropertyChanged != null)
                    {
                        PropertyChanged(this, new PropertyChangedEventArgs("DateTime"));
                    }
                }
            }
            get
            {
                return dateTime;
            }
        }
    }
}
```

通常实现 Viewmodel`INotifyPropertyChanged`接口，这意味着，将引发类`PropertyChanged`事件的一个属性发生更改时。 在 Xamarin.Forms 中的数据绑定机制将处理程序附加到此`PropertyChanged`事件以便其属性更改时可以收到通知并保留目标更新为新值。

基于此 ViewModel 时钟可以是简单，如下：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.ClockPage"
             Title="Clock Page">

    <Label Text="{Binding DateTime, StringFormat='{0:T}'}"
           FontSize="Large"
           HorizontalOptions="Center"
           VerticalOptions="Center">
        <Label.BindingContext>
            <local:ClockViewModel />
        </Label.BindingContext>
    </Label>
</ContentPage>
```

请注意如何`ClockViewModel`设置为`BindingContext`的`Label`使用属性元素标记。 或者，您可以实例化`ClockViewModel`中`Resources`集合并将其设置为`BindingContext`通过`StaticResource`标记扩展。 或者，代码隐藏文件可以实例化 ViewModel。

`Binding`上的标记扩展`Text`的属性`Label`格式`DateTime`属性。 下面是显示：

[![](data-bindings-to-mvvm-images/clock.png "视图，用于显示日期和时间通过 ViewModel")](data-bindings-to-mvvm-images/clock-large.png#lightbox "视图，用于显示日期和时间通过 ViewModel")

还有可能访问的各个属性`DateTime`用句点分隔属性 ViewModel 属性：

```xaml
<Label Text="{Binding DateTime.Second, StringFormat='{0}'}" … >
```

## <a name="interactive-mvvm"></a>交互式 MVVM

MVVM 是经常用于交互式视图基于的基础数据模型的双向数据绑定。

下面是一个名为类`HslViewModel`，用于将`Color`值到`Hue`， `Saturation`，和`Luminosity`值，反之亦然：

```csharp
using System;
using System.ComponentModel;
using Xamarin.Forms;

namespace XamlSamples
{
    public class HslViewModel : INotifyPropertyChanged
    {
        double hue, saturation, luminosity;
        Color color;

        public event PropertyChangedEventHandler PropertyChanged;

        public double Hue
        {
            set
            {
                if (hue != value)
                {
                    hue = value;
                    OnPropertyChanged("Hue");
                    SetNewColor();
                }
            }
            get
            {
                return hue;
            }
        }

        public double Saturation
        {
            set
            {
                if (saturation != value)
                {
                    saturation = value;
                    OnPropertyChanged("Saturation");
                    SetNewColor();
                }
            }
            get
            {
                return saturation;
            }
        }

        public double Luminosity
        {
            set
            {
                if (luminosity != value)
                {
                    luminosity = value;
                    OnPropertyChanged("Luminosity");
                    SetNewColor();
                }
            }
            get
            {
                return luminosity;
            }
        }

        public Color Color
        {
            set
            {
                if (color != value)
                {
                    color = value;
                    OnPropertyChanged("Color");

                    Hue = value.Hue;
                    Saturation = value.Saturation;
                    Luminosity = value.Luminosity;
                }
            }
            get
            {
                return color;
            }
        }

        void SetNewColor()
        {
            Color = Color.FromHsla(Hue, Saturation, Luminosity);
        }

        protected virtual void OnPropertyChanged(string propertyName)
        {
            PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
        }
    }
}
```

将更改为`Hue`， `Saturation`，并`Luminosity`属性的原因`Color`属性更改，并对更改`Color`会导致其他三个属性以更改。 这可能看起来好像无限循环，只不过不会调用类`PropertyChanged`事件除非实际上已更改的属性。 这样便禁止了到否则为无法控制反馈循环。

下面的 XAML 文件包含`BoxView`其`Color`属性绑定到`Color`ViewModel 和三个属性`Slider`和三个`Label`视图绑定到`Hue`， `Saturation`，和`Luminosity`属性：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.HslColorScrollPage"
             Title="HSL Color Scroll Page">
    <ContentPage.BindingContext>
        <local:HslViewModel Color="Aqua" />
    </ContentPage.BindingContext>

    <StackLayout Padding="10, 0">
        <BoxView Color="{Binding Color}"
                 VerticalOptions="FillAndExpand" />

        <Label Text="{Binding Hue, StringFormat='Hue = {0:F2}'}"
               HorizontalOptions="Center" />

        <Slider Value="{Binding Hue, Mode=TwoWay}" />

        <Label Text="{Binding Saturation, StringFormat='Saturation = {0:F2}'}"
               HorizontalOptions="Center" />

        <Slider Value="{Binding Saturation, Mode=TwoWay}" />

        <Label Text="{Binding Luminosity, StringFormat='Luminosity = {0:F2}'}"
               HorizontalOptions="Center" />

        <Slider Value="{Binding Luminosity, Mode=TwoWay}" />
    </StackLayout>
</ContentPage>
```

每个绑定`Label`是默认`OneWay`。 它只需要显示的值。 但每个绑定`Slider`是`TwoWay`。 这允许`Slider`从 ViewModel 进行初始化。 请注意，`Color`属性设置为`Aqua`ViewModel 实例化时。 但在更改`Slider`还需要在 ViewModel，然后计算新颜色中设置属性的新值。

[![](data-bindings-to-mvvm-images/hslcolorscroll.png "使用双向数据绑定的 MVVM")](data-bindings-to-mvvm-images/hslcolorscroll-large.png#lightbox "MVVM 使用双向数据绑定")

## <a name="commanding-with-viewmodels"></a>与 Viewmodel 命令

在许多情况下，MVVM 模式被限制为数据项的操作：在 ViewModel 中的查看并行数据对象中的用户界面对象。

但是，有时视图需要包含触发 ViewModel 中的各种操作的按钮。 但不能包含 ViewModel`Clicked`按钮处理程序，可能会阻塞到特定的用户界面范例 ViewModel 因为。

若要允许 Viewmodel 更独立于特定用户界面对象，但仍允许在 ViewModel 中，调用的方法*命令*存在接口。 此命令接口支持在 Xamarin.Forms 中的以下元素：

-  `Button`
-  `MenuItem`
-  `ToolbarItem`
-  `SearchBar`
-  `TextCell` (因此也`ImageCell`)
-  `ListView`
-  `TapGestureRecognizer`

除`SearchBar`和`ListView`元素，这些元素定义两个属性：

-  `Command` 类型  `System.Windows.Input.ICommand`
-  `CommandParameter` 类型  `Object`

`SearchBar`定义`SearchCommand`并`SearchCommandParameter`属性，而`ListView`定义`RefreshCommand`类型的属性`ICommand`。

`ICommand`接口定义两个方法和一个事件：

-  `void Execute(object arg)`
-  `bool CanExecute(object arg)`
-  `event EventHandler CanExecuteChanged`

ViewModel 可以定义类型的属性`ICommand`。 然后可以将绑定到这些属性`Command`每个属性`Button`或其他元素或可能是实现此接口的自定义视图。 可以选择性地设置`CommandParameter`属性标识单个`Button`对象 （或其他元素） 的绑定到此 ViewModel 属性。 在内部，`Button`调用`Execute`方法，只要用户点击`Button`，并传递到`Execute`方法及其`CommandParameter`。

`CanExecute`方法和`CanExecuteChanged`事件的情况下使用其中`Button`点击也可能是当前无效，在这种情况下`Button`应禁用其自身。 `Button`调用`CanExecute`时`Command`先设置属性和每当`CanExecuteChanged`触发事件。 如果`CanExecute`将返回`false`，则`Button`禁用其自身，不会生成`Execute`调用。

有关添加到将 Viewmodel 命令的帮助，Xamarin.Forms 定义了两个类实现`ICommand`:`Command`并`Command<T>`其中`T`是函数的参数的类型`Execute`和`CanExecute`。 这两个类定义多个构造函数加上`ChangeCanExecute`ViewModel 可调用以强制方法`Command`对象以触发`CanExecuteChanged`事件。

下面是一个简单的键盘，用于输入电话号码的 ViewModel。 请注意，`Execute`和`CanExecute`方法被定义为构造函数中的 lambda 函数权限：

```csharp
using System;
using System.ComponentModel;
using System.Windows.Input;
using Xamarin.Forms;

namespace XamlSamples
{
    class KeypadViewModel : INotifyPropertyChanged
    {
        string inputString = "";
        string displayText = "";
        char[] specialChars = { '*', '#' };

        public event PropertyChangedEventHandler PropertyChanged;

        // Constructor
        public KeypadViewModel()
        {
            AddCharCommand = new Command<string>((key) =>
                {
                    // Add the key to the input string.
                    InputString += key;
                });

            DeleteCharCommand = new Command(() =>
                {
                    // Strip a character from the input string.
                    InputString = InputString.Substring(0, InputString.Length - 1);
                },
                () =>
                {
                    // Return true if there's something to delete.
                    return InputString.Length > 0;
                });
        }

        // Public properties
        public string InputString
        {
            protected set
            {
                if (inputString != value)
                {
                    inputString = value;
                    OnPropertyChanged("InputString");
                    DisplayText = FormatText(inputString);

                    // Perhaps the delete button must be enabled/disabled.
                    ((Command)DeleteCharCommand).ChangeCanExecute();
                }
            }

            get { return inputString; }
        }

        public string DisplayText
        {
            protected set
            {
                if (displayText != value)
                {
                    displayText = value;
                    OnPropertyChanged("DisplayText");
                }
            }
            get { return displayText; }
        }

        // ICommand implementations
        public ICommand AddCharCommand { protected set; get; }

        public ICommand DeleteCharCommand { protected set; get; }

        string FormatText(string str)
        {
            bool hasNonNumbers = str.IndexOfAny(specialChars) != -1;
            string formatted = str;

            if (hasNonNumbers || str.Length < 4 || str.Length > 10)
            {
            }
            else if (str.Length < 8)
            {
                formatted = String.Format("{0}-{1}",
                                          str.Substring(0, 3),
                                          str.Substring(3));
            }
            else
            {
                formatted = String.Format("({0}) {1}-{2}",
                                          str.Substring(0, 3),
                                          str.Substring(3, 3),
                                          str.Substring(6));
            }
            return formatted;
        }

        protected void OnPropertyChanged(string propertyName)
        {
            PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
        }
    }
}
```

此 ViewModel 假定`AddCharCommand`属性绑定到`Command`属性的多个按钮 （或任何其他命令接口的操作），其中每个由`CommandParameter`。 这些按钮添加到字符`InputString`属性，然后格式化的电话号码为`DisplayText`属性。

此外，还有第二个类型的属性`ICommand`名为`DeleteCharCommand`。 这绑定到一个后间距按钮，但如果没有要删除的字符，则应禁用按钮。

因为这可能是以下键盘不是如直观复杂。 相反，标记已减至最少以演示更清楚地命令接口的使用：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples;assembly=XamlSamples"
             x:Class="XamlSamples.KeypadPage"
             Title="Keypad Page">

    <Grid HorizontalOptions="Center"
          VerticalOptions="Center">
        <Grid.BindingContext>
            <local:KeypadViewModel />
        </Grid.BindingContext>

        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="80" />
            <ColumnDefinition Width="80" />
            <ColumnDefinition Width="80" />
        </Grid.ColumnDefinitions>

        <!-- Internal Grid for top row of items -->
        <Grid Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="3">
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="Auto" />
            </Grid.ColumnDefinitions>

            <Frame Grid.Column="0"
                   OutlineColor="Accent">
                <Label Text="{Binding DisplayText}" />
            </Frame>

            <Button Text="&#x21E6;"
                    Command="{Binding DeleteCharCommand}"
                    Grid.Column="1"
                    BorderWidth="0" />
        </Grid>

        <Button Text="1"
                Command="{Binding AddCharCommand}"
                CommandParameter="1"
                Grid.Row="1" Grid.Column="0" />

        <Button Text="2"
                Command="{Binding AddCharCommand}"
                CommandParameter="2"
                Grid.Row="1" Grid.Column="1" />

        <Button Text="3"
                Command="{Binding AddCharCommand}"
                CommandParameter="3"
                Grid.Row="1" Grid.Column="2" />

        <Button Text="4"
                Command="{Binding AddCharCommand}"
                CommandParameter="4"
                Grid.Row="2" Grid.Column="0" />

        <Button Text="5"
                Command="{Binding AddCharCommand}"
                CommandParameter="5"
                Grid.Row="2" Grid.Column="1" />

        <Button Text="6"
                Command="{Binding AddCharCommand}"
                CommandParameter="6"
                Grid.Row="2" Grid.Column="2" />

        <Button Text="7"
                Command="{Binding AddCharCommand}"
                CommandParameter="7"
                Grid.Row="3" Grid.Column="0" />

        <Button Text="8"
                Command="{Binding AddCharCommand}"
                CommandParameter="8"
                Grid.Row="3" Grid.Column="1" />

        <Button Text="9"
                Command="{Binding AddCharCommand}"
                CommandParameter="9"
                Grid.Row="3" Grid.Column="2" />

        <Button Text="*"
                Command="{Binding AddCharCommand}"
                CommandParameter="*"
                Grid.Row="4" Grid.Column="0" />

        <Button Text="0"
                Command="{Binding AddCharCommand}"
                CommandParameter="0"
                Grid.Row="4" Grid.Column="1" />

        <Button Text="#"
                Command="{Binding AddCharCommand}"
                CommandParameter="#"
                Grid.Row="4" Grid.Column="2" />
    </Grid>
</ContentPage>
```

`Command`属性的第一个`Button`出现在此标记绑定到`DeleteCharCommand`; 其余绑定到`AddCharCommand`与`CommandParameter`显示的字符，它是相同`Button`人脸。 下面是该程序的操作：

[![](data-bindings-to-mvvm-images/keypad.png "使用 MVVM 和命令的计算器")](data-bindings-to-mvvm-images/keypad-large.png#lightbox "计算器使用 MVVM 和命令")

### <a name="invoking-asynchronous-methods"></a>调用异步方法

命令还可以调用异步方法。 这通过使用实现`async`并`await`关键字指定时`Execute`方法：

```csharp
DownloadCommand = new Command (async () => await DownloadAsync ());
```

这指示`DownloadAsync`方法是`Task`和应处于等待状态：

```csharp
async Task DownloadAsync ()
{
    await Task.Run (() => Download ());
}

void Download ()
{
    ...
}
```

## <a name="implementing-a-navigation-menu"></a>实现导航菜单

[XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)包含在本系列的文章中的所有源代码的程序使用 ViewModel 其主页。 此 ViewModel 是具有名为三个属性的简短类的定义`Type`， `Title`，和`Description`的包含类型的每个示例页面、 标题和简短说明。 此外，ViewModel 定义名为的静态属性`All`，它是在程序中的所有页的集合：

```csharp
public class PageDataViewModel
{
    public PageDataViewModel(Type type, string title, string description)
    {
        Type = type;
        Title = title;
        Description = description;
    }

    public Type Type { private set; get; }

    public string Title { private set; get; }

    public string Description { private set; get; }

    static PageDataViewModel()
    {
        All = new List<PageDataViewModel>
        {
            // Part 1. Getting Started with XAML
            new PageDataViewModel(typeof(HelloXamlPage), "Hello, XAML",
                                  "Display a Label with many properties set"),

            new PageDataViewModel(typeof(XamlPlusCodePage), "XAML + Code",
                                  "Interact with a Slider and Button"),

            // Part 2. Essential XAML Syntax
            new PageDataViewModel(typeof(GridDemoPage), "Grid Demo",
                                  "Explore XAML syntax with the Grid"),

            new PageDataViewModel(typeof(AbsoluteDemoPage), "Absolute Demo",
                                  "Explore XAML syntax with AbsoluteLayout"),

            // Part 3. XAML Markup Extensions
            new PageDataViewModel(typeof(SharedResourcesPage), "Shared Resources",
                                  "Using resource dictionaries to share resources"),

            new PageDataViewModel(typeof(StaticConstantsPage), "Static Constants",
                                  "Using the x:Static markup extensions"),

            new PageDataViewModel(typeof(RelativeLayoutPage), "Relative Layout",
                                  "Explore XAML markup extensions"),

            // Part 4. Data Binding Basics
            new PageDataViewModel(typeof(SliderBindingsPage), "Slider Bindings",
                                  "Bind properties of two views on the page"),

            new PageDataViewModel(typeof(SliderTransformsPage), "Slider Transforms",
                                  "Use Sliders with reverse bindings"),

            new PageDataViewModel(typeof(ListViewDemoPage), "ListView Demo",
                                  "Use a ListView with data bindings"),

            // Part 5. From Data Bindings to MVVM
            new PageDataViewModel(typeof(OneShotDateTimePage), "One-Shot DateTime",
                                  "Obtain the current DateTime and display it"),

            new PageDataViewModel(typeof(ClockPage), "Clock",
                                  "Dynamically display the current time"),

            new PageDataViewModel(typeof(HslColorScrollPage), "HSL Color Scroll",
                                  "Use a view model to select HSL colors"),

            new PageDataViewModel(typeof(KeypadPage), "Keypad",
                                  "Use a view model for numeric keypad logic")
        };
    }

    public static IList<PageDataViewModel> All { private set; get; }
}
```

XAML 文件`MainPage`定义`ListBox`其`ItemsSource`属性设置为该`All`属性，它包含`TextCell`用于显示`Title`和`Description`每一页的属性：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             x:Class="XamlSamples.MainPage"
             Padding="5, 0"
             Title="XAML Samples">

    <ListView ItemsSource="{x:Static local:PageDataViewModel.All}"
              ItemSelected="OnListViewItemSelected">
        <ListView.ItemTemplate>
            <DataTemplate>
                <TextCell Text="{Binding Title}"
                          Detail="{Binding Description}" />
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</ContentPage>
```

可滚动列表中显示页面：

[![](data-bindings-to-mvvm-images/mainpage.png "页的可滚动列表")](data-bindings-to-mvvm-images/mainpage-large.png#lightbox "页的可滚动列表")

当用户选择某个项时触发的代码隐藏文件中的处理程序。 处理程序集`SelectedItem`的属性`ListBox`回`null`然后实例化所选的页面并导航到它：

```csharp
private async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs args)
{
    (sender as ListView).SelectedItem = null;

    if (args.SelectedItem != null)
    {
        PageDataViewModel pageData = args.SelectedItem as PageDataViewModel;
        Page page = (Page)Activator.CreateInstance(pageData.Type);
        await Navigation.PushAsync(page);
    }
}
```

## <a name="video"></a>视频

> [!VIDEO https://youtube.com/embed/DYRLcqG2BAY]

**Xamarin Evolve 2016:可以轻松地 Xamarin.Forms 和 Prism MVVM**

## <a name="summary"></a>总结

XAML 是用于在 Xamarin.Forms 应用程序，尤其是在数据绑定中定义的用户界面的强大工具，使用 MVVM。 结果是在代码中的所有后台支持具有的用户界面的干净、 巧妙，且可能可工具化表示形式。


## <a name="related-links"></a>相关链接

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [第 1 部分：XAML 入门](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [第 2 部分：基本 XAML 语法](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [第 3 部分：XAML 标记扩展](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [第 4 部分：数据绑定基础知识](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
