---
title: 第 5 部分。 从数据绑定到 MVVM
description: MVVM 模式强制分隔三个软件层（XAML 用户界面，称作视图）：基本数据，称为模型;在视图和模型之间使用中间名为 ViewModel。
ms.prod: xamarin
ms.custom: video
ms.assetid: 48B37D44-4FB1-41B2-9A5E-6D383B041F81
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
ms.openlocfilehash: 80386780d52f9a28d421d2a83981085956d06ea5
ms.sourcegitcommit: efbc69acf4ea484d8815311b058114379c9db8a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73842941"
---
# <a name="part-5-from-data-bindings-to-mvvm"></a>第 5 部分。 从数据绑定到 MVVM

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_模型-视图-ViewModel （MVVM）体系结构模式是在概念上利用 XAML 来构建的。此模式强制分离三个软件层（XAML 用户界面，称作视图）：基本数据，称为模型;在视图和模型之间使用中间名为 ViewModel。视图和 ViewModel 通常通过 XAML 文件中定义的数据绑定连接。视图的 BindingContext 通常是 ViewModel 的实例。_

## <a name="a-simple-viewmodel"></a>简单的 ViewModel

作为 Viewmodel 的简介，让我们先看一看没有一个程序的程序。
之前，你已了解如何定义新的 XML 命名空间声明，以允许 XAML 文件引用其他程序集中的类。 下面是一个程序，它为 `System` 命名空间定义了 XML 命名空间声明：

```csharp
xmlns:sys="clr-namespace:System;assembly=mscorlib"
```

程序可以使用 `x:Static` 从静态 `DateTime.Now` 属性获取当前日期和时间，并将该 `DateTime` 值设置为 `StackLayout`上的 `BindingContext`：

```xaml
<StackLayout BindingContext="{x:Static sys:DateTime.Now}" …>
```

`BindingContext` 是一种特殊的属性：在元素上设置 `BindingContext` 时，该元素的所有子级都将继承该属性。 这意味着 `StackLayout` 的所有子级都具有相同的 `BindingContext`，并且它们可以包含与该对象的属性的简单绑定。

在**一键式 DateTime**程序中，有两个子元素包含与 `DateTime` 值的属性的绑定，但两个其他子级包含似乎缺少绑定路径的绑定。 这意味着，`DateTime` 值本身用于 `StringFormat`：

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

问题在于，在第一次生成页面时，日期和时间设置一次，并且永远不会发生更改：

[![](data-bindings-to-mvvm-images/oneshotdatetime.png "View Displaying Date and Time")](data-bindings-to-mvvm-images/oneshotdatetime-large.png#lightbox "View Displaying Date and Time")

XAML 文件可以显示始终显示当前时间的时钟，但它需要一些代码来帮助你解决问题。当考虑 MVVM 时，模型和 ViewModel 是完全用代码编写的类。 视图通常是一个 XAML 文件，它引用 ViewModel 中通过数据绑定定义的属性。

适当的模型是未知的 ViewModel，并有适当的 ViewModel 未知的。 但是，通常情况下，程序员会将 ViewModel 公开的数据类型定制到与特定用户界面相关联的数据类型。 例如，如果某个模型访问的数据库包含8位字符 ASCII 字符串，则 ViewModel 需要在这两个字符串之间进行转换，以便在用户界面中独占使用 Unicode。

在 MVVM 的简单示例（如此处所示的示例）中，通常根本没有模型，该模式只涉及与数据绑定关联的视图和 ViewModel。

下面是一个 ViewModel，它只包含一个名为 `DateTime`的属性，每秒更新 `DateTime` 属性：

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

Viewmodel 通常实现 `INotifyPropertyChanged` 接口，这意味着每当该类的一个属性发生更改时，该类就会激发 `PropertyChanged` 事件。 Xamarin 中的数据绑定机制。窗体将处理程序附加到此 `PropertyChanged` 事件，以便当属性发生更改时可以通知，并使用新值更新目标。

基于此 ViewModel 的时钟非常简单，如下所示：

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

请注意如何使用属性元素标记将 `ClockViewModel` 设置为 `Label` 的 `BindingContext`。 此外，还可以实例化 `Resources` 集合中的 `ClockViewModel`，并通过 `StaticResource` 标记扩展将其设置为 `BindingContext`。 或者，代码隐藏文件可以实例化 ViewModel。

`Label` 的 `Text` 属性的 `Binding` 标记扩展将 `DateTime` 属性设置格式。 显示内容如下：

[![](data-bindings-to-mvvm-images/clock.png "View Displaying Date and Time via ViewModel")](data-bindings-to-mvvm-images/clock-large.png#lightbox "View Displaying Date and Time via ViewModel")

还可以通过用句点分隔属性，来访问 ViewModel 的 `DateTime` 属性的各个属性：

```xaml
<Label Text="{Binding DateTime.Second, StringFormat='{0}'}" … >
```

## <a name="interactive-mvvm"></a>交互式 MVVM

对于基于基础数据模型的交互式视图，MVVM 通常与双向数据绑定一起使用。

下面是一个名为 `HslViewModel` 的类，它将 `Color` 值转换为 `Hue`、`Saturation`和 `Luminosity` 值，反之亦然：

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

对 `Hue`、`Saturation`和 `Luminosity` 属性的更改将导致 `Color` 属性更改，而对 `Color` 的更改会导致其他三个属性更改。 这似乎是一个无限循环，只不过类不会调用 `PropertyChanged` 事件，除非该属性已更改。 这会将一个端置于另一个不可控的反馈循环。

以下 XAML 文件包含一个 `BoxView`，其 `Color` 属性绑定到 ViewModel 的 `Color` 属性，三个 `Slider` 和三个绑定到 `Label`、`Hue`和 `Saturation`属性的 `Luminosity` 视图：

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

每个 `Label` 上的绑定是默认 `OneWay`。 它只需要显示值。 但 `TwoWay`每个 `Slider` 上的绑定。 这允许从 ViewModel 初始化 `Slider`。 请注意，在对 ViewModel 进行实例化时，`Color` 属性设置为 `Aqua`。 但 `Slider` 中的更改还需要为 ViewModel 中的属性设置新值，然后计算新的颜色。

[![](data-bindings-to-mvvm-images/hslcolorscroll.png "MVVM using Two-Way Data Bindings")](data-bindings-to-mvvm-images/hslcolorscroll-large.png#lightbox "MVVM using Two-Way Data Bindings")

## <a name="commanding-with-viewmodels"></a>用 Viewmodel 进行命令

在许多情况下，MVVM 模式限制为数据项目的操作： ViewModel 中的视图并行数据对象中的用户界面对象。

但有时，视图需要包含在 ViewModel 中触发各种操作的按钮。 但 ViewModel 不能包含按钮 `Clicked` 处理程序，因为这会将 ViewModel 与特定用户界面范例关联。

若要允许 Viewmodel 更独立于特定用户界面对象，但仍允许在 ViewModel 中调用方法，则可以使用*命令*界面。 Xamarin 中的以下元素支持此命令界面：

- `Button`
- `MenuItem`
- `ToolbarItem`
- `SearchBar`
- `TextCell` （还 `ImageCell`）
- `ListView`
- `TapGestureRecognizer`

除了 `SearchBar` 和 `ListView` 元素，这些元素定义了两个属性：

- 类型 `Command` `System.Windows.Input.ICommand`
- 类型 `CommandParameter` `Object`

`SearchBar` 定义 `SearchCommand` 和 `SearchCommandParameter` 属性，而 `ListView` 定义类型 `RefreshCommand` 的 `ICommand`属性。

`ICommand` 接口定义了两个方法和一个事件：

- `void Execute(object arg)`
- `bool CanExecute(object arg)`
- `event EventHandler CanExecuteChanged`

ViewModel 可以定义 `ICommand`类型的属性。 然后，可以将这些属性绑定到每个 `Button` 或其他元素的 `Command` 属性，或者可能是实现此接口的自定义视图。 您可以选择性地设置 `CommandParameter` 属性来标识绑定到此 ViewModel 属性的单个 `Button` 对象（或其他元素）。 在内部，当用户点击 `Button`时，`Button` 将调用 `Execute` 方法，并将其 `CommandParameter`传递到 `Execute` 方法。

`CanExecute` 方法和 `CanExecuteChanged` 事件用于 `Button` 点击当前可能无效的情况，在这种情况下，`Button` 应禁用自身。 首次设置 `Command` 属性和触发 `CanExecuteChanged` 事件时，`Button` 调用 `CanExecute`。 如果 `CanExecute` 返回 `false`，则 `Button` 会禁用自身，而不会生成 `Execute` 调用。

若要获取将命令添加到 Viewmodel 的帮助，Xamarin 请定义两个实现 `ICommand`的类： `Command` 和 `Command<T>`，其中 `T` 是 `Execute` 和 `CanExecute`的参数的类型。 这两个类定义了多个构造函数，以及一个 `ChangeCanExecute` 方法，ViewModel 可以调用该方法来强制 `Command` 对象激发 `CanExecuteChanged` 事件。

下面是用于输入电话号码的简单键盘的 ViewModel。 请注意，`Execute` 和 `CanExecute` 方法在构造函数中定义为 lambda 函数：

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

此 ViewModel 假定 `AddCharCommand` 属性绑定到多个按钮的 `Command` 属性（或具有命令界面的任何其他按钮），其中每个按钮都由 `CommandParameter`标识。 这些按钮将字符添加到 `InputString` 属性，然后将其格式设置为 `DisplayText` 属性的电话号码。

还有一个类型 `ICommand` 类型为 `DeleteCharCommand`的第二个属性。 此项已绑定到后退间距按钮，但如果没有要删除的字符，则应禁用该按钮。

以下小键盘并不像它那样非常复杂。 相反，此标记已缩小为最小值，以演示更清晰地使用命令界面：

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

此标记中显示的第一个 `Button` 的 `Command` 属性绑定到 `DeleteCharCommand`;其余部分将绑定到 `AddCharCommand`，其 `CommandParameter` 与 `Button` 面上出现的字符相同。 下面是操作中的程序：

[![](data-bindings-to-mvvm-images/keypad.png "Calculator using MVVM and Commands")](data-bindings-to-mvvm-images/keypad-large.png#lightbox "Calculator using MVVM and Commands")

### <a name="invoking-asynchronous-methods"></a>调用异步方法

命令还可以调用异步方法。 这是通过在指定 `Execute` 方法时使用 `async` 和 `await` 关键字实现的：

```csharp
DownloadCommand = new Command (async () => await DownloadAsync ());
```

这表明 `DownloadAsync` 方法是 `Task` 并且应等待：

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

包含此系列文章中所有源代码的[XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)程序在其主页中使用 ViewModel。 此 ViewModel 是一个 short 类的定义，它具有三个名为 `Type`、`Title`和 `Description` 的属性，其中包含每个示例页的类型、标题和简短说明。 此外，ViewModel 定义了一个名为 `All` 的静态属性，该属性是程序中所有页面的集合：

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

`MainPage` 的 XAML 文件定义一个 `ListBox`，其 `ItemsSource` 属性设置为该 `All` 属性，其中包含用于显示每个页面的 `TextCell` 和 `Title` 属性的 `Description`：

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

页面显示在可滚动列表中：

[![](data-bindings-to-mvvm-images/mainpage.png "Scrollable list of pages")](data-bindings-to-mvvm-images/mainpage-large.png#lightbox "Scrollable list of pages")

当用户选择某一项时，将触发代码隐藏文件中的处理程序。 处理程序将 `ListBox` 的 `SelectedItem` 属性设置回 `null`，然后实例化所选页面并导航到该页面：

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

**Xamarin 演化2016：通过 Xamarin 和 Prism 实现了更简单的操作**

## <a name="summary"></a>总结

XAML 是一种功能强大的工具，用于在 Xamarin. Forms 应用程序中定义用户界面，尤其是在使用数据绑定和 MVVM 时。 结果是用户界面的简洁、优雅且可能的 toolable 表示形式，其中包含代码中的所有背景支持。

## <a name="related-links"></a>相关链接

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [第1部分。与 XAML 入门](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [第2部分。基本 XAML 语法](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [第3部分。XAML 标记扩展](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [第4部分。数据绑定基础知识](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)

## <a name="related-videos"></a>相关视频

> [!Video https://channel9.msdn.com/Series/Xamarin-101/XamarinForms-MVVM-with-XAML-6-of-11/player]

> [!Video https://channel9.msdn.com/Series/Xamarin-101/XamarinForms-Navigation-with-XAML-7-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
