---
title: Xamarin. Forms 按钮
description: 该按钮将响应一个点击或单击，指示应用程序执行特定任务。
ms.prod: xamarin
ms.assetid: 62CAEB63-0800-44F4-9B8C-EE632138C2F5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/19/2018
ms.openlocfilehash: 0dde561b3b494415986e0cddf99f8ad145e34687
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696575"
---
# <a name="xamarinforms-button"></a>Xamarin. Forms 按钮

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos)

_该按钮将响应一个点击或单击，指示应用程序执行特定任务。_

[@No__t_1](xref:Xamarin.Forms.Button)是所有 Xamarin. Forms 中最基本的交互式控件。 @No__t_0 通常会显示一个表示命令的短文本字符串，但它还可以显示位图图像或文本和图像的组合。 用户使用手指按下 `Button` 或用鼠标单击以启动该命令。

下面讨论的大多数主题对应于[**system.windows.forms.datagridview.buttondemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos)示例中的页面。

## <a name="handling-button-clicks"></a>处理按钮单击

`Button` 定义一个[`Clicked`](xref:Xamarin.Forms.Button.Clicked)事件，当用户使用手指或鼠标指针点击 `Button` 时，将触发该事件。 当从 `Button` 的表面释放手指或鼠标按钮时，将触发事件。 @No__t_0 必须将其[`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled)属性设置为 "`true`" 才能响应点击。

[**System.windows.forms.datagridview.buttondemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos)示例中的 "**基本" 按钮单击**页演示了如何在 XAML 中实例化 `Button` 并处理其 `Clicked` 事件。 **BasicButtonClickPage**文件包含具有 `Label` 和 `Button` 的 `StackLayout`：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ButtonDemos.BasicButtonClickPage"
             Title="Basic Button Click">
    <StackLayout>

        <Label x:Name="label"
               Text="Click the Button below"
               FontSize="Large"
               VerticalOptions="CenterAndExpand"
               HorizontalOptions="Center" />

        <Button Text="Click to Rotate Text!"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                Clicked="OnButtonClicked" />

    </StackLayout>
</ContentPage>
```

@No__t_0 倾向于占用它所允许的所有空间。 例如，如果未将 `Button` 的 `HorizontalOptions` 属性设置为 `Fill` 之外的任何内容，则 `Button` 将占用其父项的完整宽度。

默认情况下，`Button` 是矩形，但你可以通过使用[`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius)属性为其指定圆角，如下面的 "" 部分[**按钮外观**](#button-appearance)中所述。

[`Text`](xref:Xamarin.Forms.Button.Text) 属性指定在 `Button` 中显示的文本。 [@No__t_1](xref:Xamarin.Forms.Button.Clicked)事件设置为名为 `OnButtonClicked` 的事件处理程序。 此处理程序位于代码隐藏文件**BasicButtonClickPage.xaml.cs**中：

```csharp
public partial class BasicButtonClickPage : ContentPage
{
    public BasicButtonClickPage ()
    {
        InitializeComponent ();
    }

    async void OnButtonClicked(object sender, EventArgs args)
    {
        await label.RelRotateTo(360, 1000);
    }
}
```

点击 `Button` 时，`OnButtonClicked` 方法执行。 @No__t_0 参数是负责此事件的 `Button` 对象。 您可以使用此访问 `Button` 对象，或区分共享同一 `Clicked` 事件的多个 `Button` 对象。

此特定 `Clicked` 处理程序将调用一个动画函数，该函数将 `Label` 360 度旋转1000毫秒。 下面是在 iOS 和 Android 设备上运行的程序，以及在 Windows 10 桌面上作为通用 Windows 平台（UWP）应用程序：

[![基本按钮单击](button-images/BasicButtonClick.png "基本按钮单击")](button-images/BasicButtonClick-Large.png#lightbox "基本按钮单击")

请注意，`OnButtonClicked` 方法包括 `async` 修饰符，因为 `await` 是在事件处理程序中使用的。 仅当处理程序的主体使用 `await` 时，`Clicked` 事件处理程序才需要 `async` 修饰符。

每个平台都以特定方式呈现 `Button`。 在 "[**按钮外观**](#button-appearance)" 部分中，你将了解如何设置颜色，并使 `Button` 边框对于更多的自定义外观可见。 `Button` 实现[`IFontElement`](xref:Xamarin.Forms.Internals.IFontElement)接口，因此它包括[`FontFamily`](xref:Xamarin.Forms.Button.FontFamily)、 [`FontSize`](xref:Xamarin.Forms.Button.FontSize)和[`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes)属性。

## <a name="creating-a-button-in-code"></a>在代码中创建按钮

通常在 XAML 中实例化 `Button`，但也可以在代码中创建 `Button`。 当应用程序需要基于使用 `foreach` 循环枚举的数据创建多个按钮时，这可能很方便。

"**代码按钮" 单击**"页面" 演示了如何创建一个与**基本按钮单击**页面功能完全相同的页面， C#而完全在中：

```csharp
public class CodeButtonClickPage : ContentPage
{
    public CodeButtonClickPage ()
    {
        Title = "Code Button Click";

        Label label = new Label
        {
            Text = "Click the Button below",
            FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)),
            VerticalOptions = LayoutOptions.CenterAndExpand,
            HorizontalOptions = LayoutOptions.Center
        };

        Button button = new Button
        {
            Text = "Click to Rotate Text!",
            VerticalOptions = LayoutOptions.CenterAndExpand,
            HorizontalOptions = LayoutOptions.Center
        };
        button.Clicked += async (sender, args) => await label.RelRotateTo(360, 1000);

        Content = new StackLayout
        {
            Children =
            {
                label,
                button
            }
        };
    }
}
```

所有内容都在类的构造函数中完成。 由于 `Clicked` 处理程序的长度仅为一条语句，因此可以将它附加到事件中非常简单：

```csharp
button.Clicked += async (sender, args) => await label.RelRotateTo(360, 1000);
```

当然，您还可以将事件处理程序定义为单独的方法（就像**基本按钮单击**中的 `OnButtonClick` 方法一样），然后将该方法附加到事件：

```csharp
button.Clicked += OnButtonClicked;
```

## <a name="disabling-the-button"></a>禁用按钮

有时，应用程序处于特定状态，其中特定 `Button` 单击是无效操作。 在这些情况下，应通过将 `Button` 的 `IsEnabled` 属性设置为 `false` 来禁用。 典型示例是文件打开 `Button` 的文件名 `Entry` 控件：只有在 `Entry` 中键入某些文本后，才应启用 `Button`。
您可以使用此任务的 `DataTrigger`，如[**数据触发器**](~/xamarin-forms/app-fundamentals/triggers.md#data-triggers)一文中所示。

## <a name="using-the-command-interface"></a>使用命令界面

应用程序可以响应 `Button` 点击，而不处理 `Clicked` 事件。 @No__t_0 实现了一种称为_命令或命令_性接口的替代通知机制。 这包括两个属性：

- [`ICommand`](xref:System.Windows.Input.ICommand)类型的[`Command`](xref:Xamarin.Forms.Button.Command) ， [`System.Windows.Input`](xref:System.Windows.Input)命名空间中定义了一个接口。
- [`Object`](xref:System.Object)类型的[`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter)属性。

此方法特别适用于与数据绑定的连接，尤其是在实现模型-视图-ViewModel （MVVM）体系结构时。 这些主题将在 "[数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)"、"[从数据绑定到 mvvm](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)" 和 " [mvvm](~/xamarin-forms/enterprise-application-patterns/mvvm.md)" 中进行讨论。

在 MVVM 应用程序中，viewmodel 定义 `ICommand` 的类型的属性，然后使用数据绑定将其连接到 XAML `Button` 元素。 Xamarin 还定义了[`Command`](xref:Xamarin.Forms.Command)和[`Command<T>`](xref:Xamarin.Forms.Command`1)类，这些类实现了 `ICommand` 接口，并协助 viewmodel 定义类型 `ICommand` 的属性。

[**命令界面**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md)一文中更详细地介绍了命令，但[**system.windows.forms.datagridview.buttondemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos)示例中的 "**基本" 按钮命令**页显示了基本方法。

@No__t_0 类是一种非常简单的 viewmodel，用于定义名为 `Number` `double` 类型的属性，以及名为 `MultiplyBy2Command` 和 `DivideBy2Command` 的类型 `ICommand` 的两个属性：

```csharp
class CommandDemoViewModel : INotifyPropertyChanged
{
    double number = 1;

    public event PropertyChangedEventHandler PropertyChanged;

    public CommandDemoViewModel()
    {
        MultiplyBy2Command = new Command(() => Number *= 2);

        DivideBy2Command = new Command(() => Number /= 2);
    }

    public double Number
    {
        set
        {
            if (number != value)
            {
                number = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Number"));
            }
        }
        get
        {
            return number;
        }
    }

    public ICommand MultiplyBy2Command { private set; get; }

    public ICommand DivideBy2Command { private set; get; }
}
```

这两个 `ICommand` 属性在类的构造函数中进行初始化，其中两个对象类型为 `Command`。 @No__t_0 构造函数包含一个称为 `execute` 构造函数参数的小函数（称为 "构造函数" 参数），该函数可为 `Number` 属性加倍或半部分。

**BasicButtonCommand**文件将其 `BindingContext` 设置为 `CommandDemoViewModel` 的实例。 @No__t_0 元素和两个 `Button` 元素包含 `CommandDemoViewModel` 中的三个属性的绑定：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ButtonDemos"
             x:Class="ButtonDemos.BasicButtonCommandPage"
             Title="Basic Button Command">

    <ContentPage.BindingContext>
        <local:CommandDemoViewModel />
    </ContentPage.BindingContext>

    <StackLayout>
        <Label Text="{Binding Number, StringFormat='Value is now {0}'}"
               FontSize="Large"
               VerticalOptions="CenterAndExpand"
               HorizontalOptions="Center" />

        <Button Text="Multiply by 2"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                Command="{Binding MultiplyBy2Command}" />

        <Button Text="Divide by 2"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                Command="{Binding DivideBy2Command}" />
    </StackLayout>
</ContentPage>
```

当点击两个 `Button` 元素时，将执行命令，数值将更改：

[![基本按钮命令](button-images/BasicButtonCommand.png "基本按钮命令")](button-images/BasicButtonCommand-Large.png#lightbox)

此方法对 `Clicked` 处理程序的优势在于，涉及此页功能的所有逻辑都位于 viewmodel 中，而不是代码隐藏文件中，这样就能更好地将用户界面与业务逻辑分离。

@No__t_0 对象还可以控制 `Button` 元素的启用和禁用。 例如，假设您想要限制 2<sup>10</sup>到 2<sup> &ndash;10</sup>之间数值的范围。 如果应启用 `Button`，则可以将另一个函数添加到返回 `true` 的构造函数（称为 `canExecute` 参数）。 下面是对 `CommandDemoViewModel` 构造函数的修改：

```csharp
class CommandDemoViewModel : INotifyPropertyChanged
{
    ···
    public CommandDemoViewModel()
    {
        MultiplyBy2Command = new Command(
            execute: () =>
            {
                Number *= 2;
                ((Command)MultiplyBy2Command).ChangeCanExecute();
                ((Command)DivideBy2Command).ChangeCanExecute();
            },
            canExecute: () => Number < Math.Pow(2, 10));

        DivideBy2Command = new Command(
            execute: () =>
            {
                Number /= 2;
                ((Command)MultiplyBy2Command).ChangeCanExecute();
                ((Command)DivideBy2Command).ChangeCanExecute();
            },
            canExecute: () => Number > Math.Pow(2, -10));
    }
    ···
}
```

需要对 `Command` 的 `ChangeCanExecute` 方法的调用，以便 `Command` 方法可以调用 `canExecute` 方法并确定是否应禁用 `Button`。 在此代码更改的情况下，当数字达到限制时，将禁用 `Button`：

[![基本按钮命令-已修改](button-images/BasicButtonCommandModified.png "基本按钮命令-已修改")](button-images/BasicButtonCommandModified-Large.png#lightbox)

两个或多个 `Button` 元素可以绑定到相同的 `ICommand` 属性。 可以使用 `Button` 的[`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter)属性来区分 `Button` 元素。 在这种情况下，你将需要使用泛型[`Command<T>`](xref:Xamarin.Forms.Command`1)类。 然后 `CommandParameter` 对象作为参数传递到 `execute` 和 `canExecute` 方法。 [**命令界面**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding)一文的[**基本**](~/xamarin-forms/app-fundamentals/data-binding/commanding.md#basic-commanding)命令部分中详细显示了此方法。

[**System.windows.forms.datagridview.buttondemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos)示例还在其 `MainPage` 类中使用了此方法。 **MainPage**文件包含示例的每一页的 `Button`：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ButtonDemos"
             x:Class="ButtonDemos.MainPage"
             Title="Button Demos">
    <ScrollView>
        <FlexLayout Direction="Column"
                    JustifyContent="SpaceEvenly"
                    AlignItems="Center">

            <Button Text="Basic Button Click"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:BasicButtonClickPage}" />

            <Button Text="Code Button Click"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:CodeButtonClickPage}" />

            <Button Text="Basic Button Command"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:BasicButtonCommandPage}" />

            <Button Text="Press and Release Button"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:PressAndReleaseButtonPage}" />

            <Button Text="Button Appearance"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:ButtonAppearancePage}" />

            <Button Text="Toggle Button Demo"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:ToggleButtonDemoPage}" />

            <Button Text="Image Button Demo"
                    Command="{Binding NavigateCommand}"
                    CommandParameter="{x:Type local:ImageButtonDemoPage}" />

        </FlexLayout>
    </ScrollView>
</ContentPage>
```

每个 `Button` 都将其 `Command` 属性绑定到名为 `NavigateCommand` 的属性，并且 `CommandParameter` 设置为对应于项目中的某个页面类的[`Type`](xref:System.Type)对象。

该 `NavigateCommand` 属性的类型 `ICommand`，在代码隐藏文件中定义：

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();

        NavigateCommand = new Command<Type>(async (Type pageType) =>
        {
            Page page = (Page)Activator.CreateInstance(pageType);
            await Navigation.PushAsync(page);
        });

        BindingContext = this;
    }

    public ICommand NavigateCommand { private set; get; }
}
```

构造函数将 `NavigateCommand` 属性初始化为 `Command<Type>` 对象，因为 `Type` 是 XAML 文件中 `CommandParameter` 对象集的类型。 这意味着 `execute` 方法具有与此 `CommandParameter` 对象相对应 `Type` 类型的参数。 函数实例化页面，然后导航到该页面。

请注意，构造函数通过将其 `BindingContext` 设置为自身来结束。 这对于 XAML 文件中的属性绑定到 `NavigateCommand` 属性是必需的。

## <a name="pressing-and-releasing-the-button"></a>按下并释放按钮

除了 `Clicked` 事件，`Button` 还定义了 [`Pressed`](xref:Xamarin.Forms.Button.Pressed) 和 [`Released`](xref:Xamarin.Forms.Button.Released) 事件。 当鼠标按 `Button` 上，或在鼠标指针位于 `Button` 上并按下鼠标按钮时，将发生 `Pressed` 事件。 当鼠标右键或鼠标松开时，会发生 `Released` 事件。 通常情况下，`Clicked` 事件同时与 `Released` 事件同时触发，但是，如果手指或鼠标指针在被释放之前离开 `Button` 表面，则 `Clicked` 事件可能不会发生。

通常不使用 `Pressed` 和 `Released` 事件，但它们可用于特殊目的，如 "**按下和释放" 按钮**页中所示。 XAML 文件包含一个 `Label`，以及一个为 `Pressed` 和 `Released` 事件附加了处理程序的 `Button`：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ButtonDemos.PressAndReleaseButtonPage"
             Title="Press and Release Button">
    <StackLayout>

        <Label x:Name="label"
               Text="Press and hold the Button below"
               FontSize="Large"
               VerticalOptions="CenterAndExpand"
               HorizontalOptions="Center" />

        <Button Text="Press to Rotate Text!"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                Pressed="OnButtonPressed"
                Released="OnButtonReleased" />

    </StackLayout>
</ContentPage>
```

当 `Pressed` 事件发生时，代码隐藏文件会对 `Label` 进行动画处理，但当发生 `Released` 事件时挂起旋转：

```csharp
public partial class PressAndReleaseButtonPage : ContentPage
{
    bool animationInProgress = false;
    Stopwatch stopwatch = new Stopwatch();

    public PressAndReleaseButtonPage ()
    {
        InitializeComponent ();
    }

    void OnButtonPressed(object sender, EventArgs args)
    {
        stopwatch.Start();
        animationInProgress = true;

        Device.StartTimer(TimeSpan.FromMilliseconds(16), () =>
        {
            label.Rotation = 360 * (stopwatch.Elapsed.TotalSeconds % 1);

            return animationInProgress;
        });
    }

    void OnButtonReleased(object sender, EventArgs args)
    {
        animationInProgress = false;
        stopwatch.Stop();
    }
}
```

结果是，`Label` 仅在手指与 `Button` 接触时才进行旋转，并在使用 finger 时停止：

[![按下并松开按钮](button-images/PressAndReleaseButton.png "按下并松开按钮")](button-images/PressAndReleaseButton-Large.png)

这种行为具有游戏应用程序：在 `Button` 上保存的手指可能会使屏幕上对象按特定方向移动。

<a name="button-appearance" />

## <a name="button-appearance"></a>按钮外观

@No__t_0 继承或定义多个影响其外观的属性：

- [`TextColor`](xref:Xamarin.Forms.Button.TextColor)是 `Button` 文本的颜色
- [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor)是该文本的背景色
- [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor)是围绕 `Button` 的区域的颜色
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily)是用于文本的字体系列
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize)是文本的大小
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes)指示文本是斜体还是粗体
- [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth)是边框的宽度
- [`CornerRadius`](xref:Xamarin.Forms.Button.CornerRadius)是 `Button` 的角半径
- `CharacterSpacing` 是 `Button` 文本中的字符之间的间距

> [!NOTE]
> @No__t_0 类还具有控制 `Button` 布局行为的[`Margin`](xref:Xamarin.Forms.View.Margin)和[`Padding`](xref:Xamarin.Forms.Button.Padding)属性。 有关详细信息，请参阅[边距和填充](~/xamarin-forms/user-interface/layouts/margin-and-padding.md)。

其中六个属性的效果（`FontFamily` 和 `FontAttributes` 除外）在**按钮外观**页中进行演示。 "[**使用位图方式" 按钮**](#image-button)部分讨论了[`Image`](xref:Xamarin.Forms.Button.ImageSource)的另一个属性。

"**按钮外观**" 页中的所有视图和数据绑定都在 XAML 文件中定义：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ButtonDemos"
             x:Class="ButtonDemos.ButtonAppearancePage"
             Title="Button Appearance">
    <StackLayout>
        <Button x:Name="button"
                Text="Button"
                VerticalOptions="CenterAndExpand"
                HorizontalOptions="Center"
                TextColor="{Binding Source={x:Reference textColorPicker},
                                    Path=SelectedItem.Color}"
                BackgroundColor="{Binding Source={x:Reference backgroundColorPicker},
                                          Path=SelectedItem.Color}"
                BorderColor="{Binding Source={x:Reference borderColorPicker},
                                      Path=SelectedItem.Color}" />

        <StackLayout BindingContext="{x:Reference button}"
                     Padding="10">

            <Slider x:Name="fontSizeSlider"
                    Maximum="48"
                    Minimum="1"
                    Value="{Binding FontSize}" />

            <Label Text="{Binding Source={x:Reference fontSizeSlider},
                                  Path=Value,
                                  StringFormat='FontSize = {0:F0}'}"
                   HorizontalTextAlignment="Center" />

            <Slider x:Name="borderWidthSlider"
                    Minimum="-1"
                    Maximum="12"
                    Value="{Binding BorderWidth}" />

            <Label Text="{Binding Source={x:Reference borderWidthSlider},
                                  Path=Value,
                                  StringFormat='BorderWidth = {0:F0}'}"
                   HorizontalTextAlignment="Center" />

            <Slider x:Name="cornerRadiusSlider"
                    Minimum="-1"
                    Maximum="24"
                    Value="{Binding CornerRadius}" />

            <Label Text="{Binding Source={x:Reference cornerRadiusSlider},
                                  Path=Value,
                                  StringFormat='CornerRadius = {0:F0}'}"
                   HorizontalTextAlignment="Center" />

            <Grid>
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                </Grid.RowDefinitions>

                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>

                <Grid.Resources>
                    <Style TargetType="Label">
                        <Setter Property="VerticalOptions" Value="Center" />
                    </Style>
                </Grid.Resources>

                <Label Text="Text Color:"
                       Grid.Row="0" Grid.Column="0" />

                <Picker x:Name="textColorPicker"
                        ItemsSource="{Binding Source={x:Static local:NamedColor.All}}"
                        ItemDisplayBinding="{Binding FriendlyName}"
                        SelectedIndex="0"
                        Grid.Row="0" Grid.Column="1" />

                <Label Text="Background Color:"
                       Grid.Row="1" Grid.Column="0" />

                <Picker x:Name="backgroundColorPicker"
                        ItemsSource="{Binding Source={x:Static local:NamedColor.All}}"
                        ItemDisplayBinding="{Binding FriendlyName}"
                        SelectedIndex="0"
                        Grid.Row="1" Grid.Column="1" />

                <Label Text="Border Color:"
                       Grid.Row="2" Grid.Column="0" />

                <Picker x:Name="borderColorPicker"
                        ItemsSource="{Binding Source={x:Static local:NamedColor.All}}"
                        ItemDisplayBinding="{Binding FriendlyName}"
                        SelectedIndex="0"
                        Grid.Row="2" Grid.Column="1" />
            </Grid>
        </StackLayout>
    </StackLayout>
</ContentPage>
```

页面顶部 `Button` 有三个绑定到页面底部 `Picker` 元素的 `Color` 属性。 @No__t_0 元素中的项是项目中包含的 `NamedColor` 类的颜色。 三个 `Slider` 元素包含对 `Button` 的 `FontSize`、`BorderWidth` 和 `CornerRadius` 属性的双向绑定。

此程序允许您试验所有这些属性的组合：

[![按钮外观](button-images/ButtonAppearance.png "按钮外观")](button-images/ButtonAppearance-Large.png)

若要查看 `Button` 的边框，需要将 `BorderColor` 设置为 `Default` 以外的值，并将 `BorderWidth` 设置为正值。

在 iOS 上，你会注意到大的边框宽度强行进入到 `Button` 的内部，并干扰文本显示。 如果选择将边框与 iOS `Button` 一起使用，则可能需要开始和结束具有空格的 `Text` 属性以保留其可见性。

在 UWP 上，选择超过一半 `Button` 高度的 `CornerRadius` 会引发异常。

## <a name="button-visual-states"></a>按钮视觉状态

[`Button`](xref:Xamarin.Forms.Button)具有 `Pressed` [`VisualState`](xref:Xamarin.Forms.VisualState) ，可用于在用户按下时启动对 `Button` 的视觉更改（前提是已启用）。

下面的 XAML 示例演示如何为 `Pressed` 状态定义可视状态：

```xaml
<Button Text="Click me!"
        ...>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>
                    <Setter Property="Scale"
                            Value="1" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Pressed">
                <VisualState.Setters>
                    <Setter Property="Scale"
                            Value="0.8" />
                </VisualState.Setters>
            </VisualState>

        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Button>
```

@No__t_0 [`VisualState`](xref:Xamarin.Forms.VisualState)指定在按下[`Button`](xref:Xamarin.Forms.Button)时，其[`Scale`](xref:Xamarin.Forms.VisualElement.Scale)属性将从其默认值1更改为0.8。 @No__t_0 `VisualState` 指定当 `Button` 处于正常状态时，其 `Scale` 属性将设置为1。 因此，整体效果是，在按下 `Button` 时，重新缩放会略小一些，释放 `Button` 时，它将被重新缩放为其默认大小。

有关可视状态的详细信息，请参阅[Xamarin。窗体可视状态管理器](~/xamarin-forms/user-interface/visual-state-manager.md)。

## <a name="creating-a-toggle-button"></a>创建切换按钮

可以 `Button` 为其划分子类，使其类似于 "关显" 开关：点击按钮一次，切换按钮，然后再次点击以将其关闭。

以下 `ToggleButton` 类派生自 `Button`，并定义名为 `Toggled` 的新事件以及名为 `IsToggled` 的布尔值属性。 这些属性与 Xamarin 所定义的两个属性相同。 Forms [`Switch`](xref:Xamarin.Forms.Switch)：

```csharp
class ToggleButton : Button
{
    public event EventHandler<ToggledEventArgs> Toggled;

    public static BindableProperty IsToggledProperty =
        BindableProperty.Create("IsToggled", typeof(bool), typeof(ToggleButton), false,
                                propertyChanged: OnIsToggledChanged);

    public ToggleButton()
    {
        Clicked += (sender, args) => IsToggled ^= true;
    }

    public bool IsToggled
    {
        set { SetValue(IsToggledProperty, value); }
        get { return (bool)GetValue(IsToggledProperty); }
    }

    protected override void OnParentSet()
    {
        base.OnParentSet();
        VisualStateManager.GoToState(this, "ToggledOff");
    }

    static void OnIsToggledChanged(BindableObject bindable, object oldValue, object newValue)
    {
        ToggleButton toggleButton = (ToggleButton)bindable;
        bool isToggled = (bool)newValue;

        // Fire event
        toggleButton.Toggled?.Invoke(toggleButton, new ToggledEventArgs(isToggled));

        // Set the visual state
        VisualStateManager.GoToState(toggleButton, isToggled ? "ToggledOn" : "ToggledOff");
    }
}
```

@No__t_0 构造函数将处理程序附加到 `Clicked` 事件，以便它可以更改 `IsToggled` 属性的值。 @No__t_0 方法激发 `Toggled` 事件。

@No__t_0 方法的最后一行用两个文本字符串 "ToggledOn" 和 "ToggledOff" 调用静态的 `VisualStateManager.GoToState` 方法。 你可以阅读有关此方法的信息，以及你的应用程序可以如何响应[**Xamarin. Forms 视觉状态管理器**](~/xamarin-forms/user-interface/visual-state-manager.md)一文中的可视状态。

由于 `ToggleButton` 对 `VisualStateManager.GoToState` 进行调用，因此类本身不需要包含任何其他工具来根据其 `IsToggled` 状态更改按钮的外观。 这是承载 `ToggleButton` 的 XAML 的责任。

**切换按钮**的 "演示" 页包含两个 `ToggleButton` 实例，其中包括基于可视状态设置按钮的 `Text`、`BackgroundColor` 和 `TextColor` 的可视状态管理器标记：

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ButtonDemos"
             x:Class="ButtonDemos.ToggleButtonDemoPage"
             Title="Toggle Button Demo">

    <ContentPage.Resources>
        <Style TargetType="local:ToggleButton">
            <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            <Setter Property="HorizontalOptions" Value="Center" />
        </Style>
    </ContentPage.Resources>

    <StackLayout Padding="10, 0">
        <local:ToggleButton Toggled="OnItalicButtonToggled">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ToggleStates">
                    <VisualState Name="ToggledOff">
                        <VisualState.Setters>
                            <Setter Property="Text" Value="Italic Off" />
                            <Setter Property="BackgroundColor" Value="#C0C0C0" />
                            <Setter Property="TextColor" Value="Black" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState Name="ToggledOn">
                        <VisualState.Setters>
                            <Setter Property="Text" Value=" Italic On " />
                            <Setter Property="BackgroundColor" Value="#404040" />
                            <Setter Property="TextColor" Value="White" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </local:ToggleButton>

        <local:ToggleButton Toggled="OnBoldButtonToggled">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ToggleStates">
                    <VisualState Name="ToggledOff">
                        <VisualState.Setters>
                            <Setter Property="Text" Value="Bold Off" />
                            <Setter Property="BackgroundColor" Value="#C0C0C0" />
                            <Setter Property="TextColor" Value="Black" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState Name="ToggledOn">
                        <VisualState.Setters>
                            <Setter Property="Text" Value=" Bold On " />
                            <Setter Property="BackgroundColor" Value="#404040" />
                            <Setter Property="TextColor" Value="White" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </local:ToggleButton>

        <Label x:Name="label"
               Text="Just a little passage of some sample text that can be formatted in italic or boldface by toggling the two buttons."
               FontSize="Large"
               HorizontalTextAlignment="Center"
               VerticalOptions="CenterAndExpand" />

    </StackLayout>
</ContentPage>
```

@No__t_0 事件处理程序在代码隐藏文件中。 它们负责根据按钮的状态设置 `Label` 的 `FontAttributes` 属性：

```csharp
public partial class ToggleButtonDemoPage : ContentPage
{
    public ToggleButtonDemoPage ()
    {
        InitializeComponent ();
    }

    void OnItalicButtonToggled(object sender, ToggledEventArgs args)
    {
        if (args.Value)
        {
            label.FontAttributes |= FontAttributes.Italic;
        }
        else
        {
            label.FontAttributes &= ~FontAttributes.Italic;
        }
    }

    void OnBoldButtonToggled(object sender, ToggledEventArgs args)
    {
        if (args.Value)
        {
            label.FontAttributes |= FontAttributes.Bold;
        }
        else
        {
            label.FontAttributes &= ~FontAttributes.Bold;
        }
    }
}
```

下面是在 iOS、Android 和 UWP 上运行的程序：

[![切换按钮演示](button-images/ToggleButtonDemo.png "切换按钮演示")](button-images/ToggleButtonDemo-Large.png#lightbox)

<a name="image-button" />

## <a name="using-bitmaps-with-buttons"></a>将位图与按钮一起使用

@No__t_0 类定义了一个[`ImageSource`](xref:Xamarin.Forms.Button.Image)属性，该属性允许您在 `Button` 上显示位图图像（单独显示或与文本一起显示）。 还可以指定文本和图像的排列方式。

@No__t_0 属性的类型[`ImageSource`](xref:Xamarin.Forms.ImageSource)，这意味着可以从文件、嵌入的资源、URI 或流加载位图。

Xamarin 支持的每个平台都允许在多个大小中存储图像，以使应用程序可能运行的不同设备的像素分辨率不同。 这些位图以这样一种方式命名或存储，操作系统可为设备的视频显示分辨率选取最佳匹配项。

对于 `Button` 上的位图，最佳大小通常介于32与64设备无关单位之间，具体取决于所需的大小。 本示例中使用的映像基于与48设备无关的单位大小。

在 iOS 项目中， **Resources**文件夹包含三种大小的图像：

- 存储为 **/Resources/MonkeyFace.png**的48像素正方形位图
- 作为 **/Resource/MonkeyFace@2x.png** 存储的96像素正方形位图
- 作为 **/Resource/MonkeyFace@3x.png** 存储的144像素正方形位图

为所有三个位图提供了**BundleResource**的**生成操作**。

对于 Android 项目，位图都具有相同的名称，但它们存储在**Resources**文件夹的不同子文件夹中：

- 存储为 **/Resources/drawable-hdpi/MonkeyFace.png**的72像素正方形位图
- 存储为 **/Resources/drawable-xhdpi/MonkeyFace.png**的96像素正方形位图
- 存储为 **/Resources/drawable-xxhdpi/MonkeyFace.png**的144像素正方形位图
- 存储为 **/Resources/drawable-xxxhdpi/MonkeyFace.png**的192像素正方形位图

提供了**AndroidResource**的**生成操作**。

在 UWP 项目中，可以将位图存储在项目中的任何位置，但它们通常存储在自定义文件夹或**资产**现有文件夹中。 UWP 项目包含以下位图：

- 存储为 **/Assets/MonkeyFace.scale-100.png**的48像素正方形位图
- 存储为 **/Assets/MonkeyFace.scale-200.png**的96像素正方形位图
- 存储为 **/Assets/MonkeyFace.scale-400.png**的192像素正方形位图

它们都是**内容**的**生成操作**。

您可以使用 `Button` 的[`ContentLayout`](xref:Xamarin.Forms.Button.ContentLayout)属性指定 `Button` 上的 `Text` 和 `ImageSource` 属性的排列方式。 此属性的类型为[`ButtonContentLayout`](xref:Xamarin.Forms.Button.ButtonContentLayout)，它是 `Button` 中的嵌入类。 [构造函数](xref:Xamarin.Forms.Button.ButtonContentLayout.%23ctor(Xamarin.Forms.Button.ButtonContentLayout.ImagePosition,System.Double))具有两个参数：

- [@No__t_1](xref:Xamarin.Forms.Button.ButtonContentLayout.ImagePosition)枚举的成员： `Left`、`Top`、`Right` 或 `Bottom`，指示位图相对于文本的显示方式。
- 位图和文本之间的间距的 `double` 值。

默认值为 `Left` 和10个单位。 @No__t_0 名为[`Position`](xref:Xamarin.Forms.Button.ButtonContentLayout.Position)的两个只读属性， [`Spacing`](xref:Xamarin.Forms.Button.ButtonContentLayout.Spacing)提供这些属性的值。

在代码中，可以创建 `Button` 并设置 `ContentLayout` 属性，如下所示：

```csharp
Button button = new Button
{
    Text = "button text",
    ImageSource = new FileImageSource
    {
        File = "image filename"
    },
    ContentLayout = new Button.ButtonContentLayout(Button.ButtonContentLayout.ImagePosition.Right, 20)
};
```

在 XAML 中，只需以逗号分隔的任何顺序仅指定枚举成员或间距，或同时指定这两者：

```xaml
<Button Text="button text"
        ImageSource="image filename"
        ContentLayout="Right, 20" />
```

"**图像" 按钮演示**页使用 `OnPlatform` 为 IOS、ANDROID 和 UWP 位图文件指定不同的文件名。 如果要对每个平台使用相同的文件名，并避免使用 `OnPlatform`，则需要在项目的根目录中存储 UWP 位图。

"**图像" 按钮演示**页上的第一个 `Button` 设置 `Image` 属性，但不是 `Text` 属性：

```xaml
<Button>
    <Button.ImageSource>
        <OnPlatform x:TypeArguments="ImageSource">
            <On Platform="iOS, Android" Value="MonkeyFace.png" />
            <On Platform="UWP" Value="Assets/MonkeyFace.png" />
        </OnPlatform>
    </Button.ImageSource>
</Button>
```

如果 UWP 位图存储在项目的根目录中，则可大大简化此标记：

```xaml
<Button ImageSource="MonkeyFace.png" />
```

为了避免**ImageButtonDemo**文件中出现大量重复标记，还定义了一个隐式 `Style` 来设置 `ImageSource` 属性。 此 `Style` 会自动应用到五个其他 `Button` 元素。 下面是完整的 XAML 文件：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ButtonDemos.ImageButtonDemoPage">

    <FlexLayout Direction="Column"
                JustifyContent="SpaceEvenly"
                AlignItems="Center">

        <FlexLayout.Resources>
            <Style TargetType="Button">
                <Setter Property="ImageSource">
                    <OnPlatform x:TypeArguments="ImageSource">
                        <On Platform="iOS, Android" Value="MonkeyFace.png" />
                        <On Platform="UWP" Value="Assets/MonkeyFace.png" />
                    </OnPlatform>
                </Setter>
            </Style>
        </FlexLayout.Resources>

        <Button>
            <Button.ImageSource>
                <OnPlatform x:TypeArguments="ImageSource">
                    <On Platform="iOS, Android" Value="MonkeyFace.png" />
                    <On Platform="UWP" Value="Assets/MonkeyFace.png" />
                </OnPlatform>
            </Button.ImageSource>
        </Button>

        <Button Text="Default" />

        <Button Text="Left - 10"
                ContentLayout="Left, 10" />

        <Button Text="Top - 10"
                ContentLayout="Top, 10" />

        <Button Text="Right - 20"
                ContentLayout="Right, 20" />

        <Button Text="Bottom - 20"
                ContentLayout="Bottom, 20" />
    </FlexLayout>
</ContentPage>
```

最后四个 `Button` 元素使用 `ContentLayout` 属性来指定文本和位图的位置和间距：

[![图像按钮演示](button-images/ImageButtonDemo.png "图像按钮演示")](button-images/ImageButtonDemo-Large.png#lightbox)

现在，您已经看到了可处理 `Button` 事件并更改 `Button` 外观的各种方式。

## <a name="related-links"></a>相关链接

- [System.windows.forms.datagridview.buttondemos 示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-buttondemos)
- [按钮 API](xref:Xamarin.Forms.Button)
