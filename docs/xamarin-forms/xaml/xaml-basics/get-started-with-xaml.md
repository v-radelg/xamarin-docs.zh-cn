---
title: 第 1 部分。 XAML 入门
description: 在 Xamarin.Forms 应用程序中，XAML 是主要用于定义页面和一同使用的代码隐藏文件的可视内容。
ms.prod: xamarin
ms.assetid: 9073FA0E-BD5A-4492-8A93-54C466F6EDB9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/30/2019
ms.openlocfilehash: a35a3750957073168bca4903e1cef79ed53a3de9
ms.sourcegitcommit: bb6a103de620386187a08151a4cdc29a4dc53a10
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679966"
---
# <a name="part-1-getting-started-with-xaml"></a>第 1 部分。 XAML 入门

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

_XAML 在 Xamarin.Forms 应用程序中，主要用于定义页面的可视内容和一起使用C#代码隐藏文件。_

代码隐藏文件提供代码支持的标记。 在一起，这两个文件会导致包含子视图和属性初始化的新类定义。 在 XAML 文件中，类和属性引用的 XML 元素和属性，并建立标记和代码之间的链接。

## <a name="creating-the-solution"></a>创建解决方案

若要开始编辑第一个 XAML 文件，请使用 Visual Studio 或 Visual Studio for Mac，以创建新的 Xamarin.Forms 解决方案。 （选择下面与你的环境相对应的选项卡。）

<!-- markdownlint-disable MD001 -->

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

在 Windows 中，启动 Visual Studio 2019，然后在 "开始" 窗口中单击 "**创建新项目**" 以创建新项目：

![新建解决方案窗口](get-started-with-xaml-images/win/new-solution-2019.png)

在 "新建**项目**" 窗口的 "**项目类型**" 下拉菜单中选择 "**移动**"，选择 "**移动应用（Xamarin）** " 模板，然后单击 "**下一步**" 按钮：

!["新建项目" 窗口](get-started-with-xaml-images/win/new-project-2019.png)

在 "**配置新项目**" 窗口中，将**项目名称**设置为**XamlSamples** （或你喜欢的任何内容），然后单击 "**创建**" 按钮。

在 "**新建跨平台应用**" 对话框中，单击 "**空白**"，然后单击 **"确定"** 按钮：

![新建应用程序对话框](get-started-with-xaml-images/win/new-cross-platform-app.png)

在解决方案中创建四个项目： **XamlSamples** .NET Standard 库**XamlSamples.Android**， **XamlSamples.iOS**，和通用 Windows 平台解决方案中， **XamlSamples.UWP**。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

在 Visual Studio for Mac 中，选择**文件 > 新建解决方案**菜单中。 中**新的项目**对话框中，选择**多平台 > 应用**左侧，并**空白窗体应用程序** (*不* **窗体应用**) 从模板列表：

![新建项目对话框1](get-started-with-xaml-images/mac/newprojectdialog1.png)

按**下一步**。

在下一步的对话框中，为项目的命名**XamlSamples** （或所需的任意）。 请确保**使用.NET Standard**单选按钮处于选中状态：

![新建项目对话框2](get-started-with-xaml-images/mac/newprojectdialog2.png)

按**下一步**。

在以下对话框中，可以选择该项目的位置：

![新项目对话框3](get-started-with-xaml-images/mac/newprojectdialog3.png)

按**创建**

在解决方案中创建三个项目： **XamlSamples** .NET Standard 库**XamlSamples.Android**，并**XamlSamples.iOS**。

-----

在创建后**XamlSamples**解决方案中，你可能想要测试部署环境，通过为解决方案启动项目，选择各种平台项目和生成和部署简单的应用程序创建的phone 仿真程序或真实的设备的项目模板。

除非您需要编写特定于平台的代码共享**XamlSamples** .NET Standard 库项目是在其中你将会花费几乎所有编程时间。 这些文章不将该项目外推进。

### <a name="anatomy-of-a-xaml-file"></a>XAML 文件的剖析

内**XamlSamples** .NET 标准库是一对具有以下名称的文件：

- **App.xaml**，XAML 文件中; 和
- **App.xaml.cs**、 C# *代码隐藏*与 XAML 文件相关联的文件。

将需要单击旁边的箭头**App.xaml**若要查看的代码隐藏文件。

这两**App.xaml**并**App.xaml.cs**到一个名为类参与`App`派生`Application`。 XAML 文件与大多数其他类派生的类参与`ContentPage`; 这些文件使用 XAML 来定义整个页面的可视内容。 这是中的其他两个文件，则返回 true **XamlSamples**项目：

- **MainPage.xaml**，XAML 文件中; 和
- **MainPage.xaml.cs**、C#代码隐藏文件。

**MainPage.xaml** （尽管可能稍有不同的格式设置），文件如下所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:XamlSamples"
             x:Class="XamlSamples.MainPage">

    <StackLayout>
        <!-- Place new controls here -->
        <Label Text="Welcome to Xamarin Forms!"
               VerticalOptions="Center"
               HorizontalOptions="Center" />
    </StackLayout>

</ContentPage>
```

这两个 XML 命名`xmlns`空间（）声明引用了 uri、第一个看起来是 Xamarin 网站上的第二个和第二个。 不去费神检查哪些这些 Uri 指向。 不存在。 它们是只需通过 Xamarin 和 Microsoft 所拥有的 Uri 和他们基本上充当版本标识符。

第一个 XML 命名空间声明意味着在没有任何前缀的 XAML 文件中定义的标记如指在 Xamarin.Forms 中，类`ContentPage`。 第二个命名空间声明定义前缀为`x`。 这使用多个元素和属性的 XAML 中的内部本身和它们支持 XAML 的其他实现。 但是，这些元素和属性是略有不同，具体取决于嵌入在 URI 中的年份。 Xamarin.Forms 支持 2009 XAML 规范，但它不是所有。

`local`命名空间声明，可从.NET Standard 库项目中访问其他类。

该第一个标记末尾`x`前缀用于名为的属性`Class`。 因为这一点的用法`x`前缀是几乎通用的 XAML 命名空间，XAML 属性，如`Class`几乎总是称为`x:Class`。

`x:Class`特性指定完全限定的.NET 类名称：`MainPage`类中`XamlSamples`命名空间。 这意味着此 XAML 文件定义一个名为的新类`MainPage`中`XamlSamples`派生的命名空间`ContentPage`-在其中标记`x:Class`属性将会显示。

`x:Class`属性只能出现在要定义一个派生的 XAML 文件的根元素C#类。 这是在 XAML 文件中定义的唯一的新类。 所有其他 XAML 文件中显示的内容是改为只需从现有的类实例化并初始化。

**MainPage.xaml.cs**文件如下所示 (除了未使用`using`指令):

```csharp
using Xamarin.Forms;

namespace XamlSamples
{
    public partial class MainPage : ContentPage
    {
        public MainPage()
        {
            InitializeComponent();
        }
    }
}
```

`MainPage`类派生自`ContentPage`，但请注意`partial`类定义。 这表明应为另一个分部类定义`MainPage`，但之类的问题？ 和新增功能的`InitializeComponent`方法？

当 Visual Studio 生成项目时，它会分析要生成的 XAML 文件C#代码文件。 如果查看**XamlSamples\XamlSamples\obj\Debug**目录中，您会发现名为的文件**XamlSamples.MainPage.xaml.g.cs**。 G 表示生成的。 这是其他分部类定义的`MainPage`，其中包含的定义`InitializeComponent`方法从调用`MainPage`构造函数。 这些两个部分`MainPage`然后一起编译的类定义。 具体取决于是否或不编译 XAML，XAML 文件或二进制形式的 XAML 文件嵌入可执行文件中。

在运行时，代码在特定平台项目调用`LoadApplication`方法，并向它传递的新实例`App`.NET Standard 库中的类。 `App`类构造函数实例化`MainPage`。 该类的构造函数调用`InitializeComponent`，后者随后调用`LoadFromXaml`从.NET Standard 库中提取的 XAML 文件 （或其编译的二进制文件） 的方法。 `LoadFromXaml` 初始化 XAML 文件中定义的所有对象、 将它们连接在父-子关系中的组合在一起，将附加到 XAML 文件中设置的事件的代码中定义的事件处理程序并将对象的结果树设置页的内容。

尽管通常不需要花多长时间的生成的代码文件，但有时运行时异常会引发代码在所生成的文件，因此您应了解它们的用法。

编译和运行此程序时`Label`元素出现在页面的中心，如 XAML 所示：

[![默认的 Xamarin。窗体显示](get-started-with-xaml-images/xamlsamples.png)](get-started-with-xaml-images/xamlsamples-large.png#lightbox)

对于更有趣的视觉对象，你只需进行更多有趣的 XAML。

## <a name="adding-new-xaml-pages"></a>添加新的 XAML 页面

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

若要将其他基于 XAML @no__t 的类添加到项目中，请选择 " **XamlSamples** " .NET Standard 库项目，右键单击，然后选择 "**添加 > 新项 ...** "。在 "**添加新项**" 对话框中，选择 "**可视C#项" > Xamarin. Forms > 内容页**（而不是**内容页（C#）** ，它创建仅限代码的页或**内容视图**，不是页面）。 例如，为页面提供一个名称， **HelloXamlPage**:

!["添加新项" 对话框](get-started-with-xaml-images/win/add-new-item-dialog-2019.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

若要添加其他基于 XAML 的`ContentPage`类到你的项目，选择**XamlSamples** .NET Standard 库项目，然后调用**文件 > 新建文件**菜单项。 左侧**新的文件**对话框中，选择**窗体**左侧，和**窗体 ContentPage Xaml** (不**窗体 ContentPage**、 哪些创建一个仅限代码的页面，或**内容视图**，这不是一个页面)。 例如，为页面提供一个名称， **HelloXamlPage**:

![“新建文件”对话框](get-started-with-xaml-images/mac/newfiledialog.png)

-----

两个文件添加到项目中， **HelloXamlPage.xaml**和代码隐藏文件**HelloXamlPage.xaml.cs**。

## <a name="setting-page-content"></a>设置页面内容

编辑**HelloXamlPage.xaml**文件，以便仅标记就是那些用于`ContentPage`和`ContentPage.Content`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.HelloXamlPage">
    <ContentPage.Content>

    </ContentPage.Content>
</ContentPage>
```

`ContentPage.Content`标记是唯一的 XAML 语法的一部分。 首先，它们可能会显示为无效的 XML，但它们是合法的。 段不是 XML 中的特殊字符。

`ContentPage.Content`标记称为*property 元素*标记。 `Content` 是的一个属性`ContentPage`，并通常将设置为一个视图或具有子视图的布局。 通常情况下属性变为 XAML 中的属性，但它很难设置`Content`属性为复杂对象。 出于此原因，该属性表示为类名和句点分隔的属性名称组成的 XML 元素。 现在`Content`属性可以设置之间`ContentPage.Content`标记，如下：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.HelloXamlPage"
             Title="Hello XAML Page">
    <ContentPage.Content>

        <Label Text="Hello, XAML!"
               VerticalOptions="Center"
               HorizontalTextAlignment="Center"
               Rotation="-15"
               IsVisible="true"
               FontSize="Large"
               FontAttributes="Bold"
               TextColor="Blue" />

    </ContentPage.Content>
</ContentPage>
```

另请注意，`Title`特性已设置的根标记。

此时，类、属性和 XML 之间的关系应该是显而易见的：Xamarin 类（如`ContentPage`或`Label`）作为 XML 元素出现在 XAML 文件中。 该类的属性，包括`Title`上`ContentPage`和七个属性的`Label`— 通常显示为 XML 属性。

多个快捷方式存在以便为设置这些属性的值。 某些属性是基本数据类型：例如`Title` ， `true`和`Double`属性的类型为，`IsVisible`并且为类型，并且（默认情况下，在此处设置为仅用于说明）的类型为`Rotation` `String` `Text` `Boolean`.

`HorizontalTextAlignment`属性属于类型`TextAlignment`，这是一个枚举。 对于任何枚举类型的属性，只需提供有成员名称。

对于更复杂的类型的属性中，但是，转换器用于分析 XAML。 这些是在 Xamarin.Forms 中派生的类`TypeConverter`。 许多都是公共类，但有些不。 对于此特定的 XAML 文件，这些类的几个播放在后台角色：

- `LayoutOptionsConverter` 有关`VerticalOptions`属性
- `FontSizeConverter` 有关`FontSize`属性
- `ColorTypeConverter` 有关`TextColor`属性

这些转换器控制允许的属性设置的语法。

`ThicknessTypeConverter`可以处理一个、 两个，或由逗号分隔的四个数字。 如果提供一个数字，则它将适用于所有四个边。 使用两个数字，第一个是左侧和右侧填充，第二项是顶部和底部。 中的顺序从左、 顶部、 右侧和底部有四个数字。

`LayoutOptionsConverter`可以将转换的公共静态字段的名称`LayoutOptions`结构类型的值与`LayoutOptions`。

`FontSizeConverter`可以处理`NamedSize`成员或数字字体大小。

`ColorTypeConverter`接受公共静态字段的名称`Color`结构或十六进制 RGB 值，无论 alpha 通道，前面有数字符号 （#）。 下面是 alpha 通道没有语法：

 `TextColor="#rrggbb"`

每个小的字母是十六进制数字。 下面是包含 alpha 通道的方式：

 `TextColor="#aarrggbb">`

Alpha 通道，请注意 FF 完全不透明，00 是完全透明。

其他两种格式，可以指定仅为每个通道的单个十六进制数字：

 `TextColor="#rgb"` `TextColor="#argb"`

在这些情况下，该数字将重复以形成的值。 例如，#CF3 是 RGB 颜色抄送 FF 33。

## <a name="page-navigation"></a>页面导航

在运行时**XamlSamples**程序，`MainPage`显示。 若要查看新`HelloXamlPage`可以作为新的启动页中设置**App.xaml.cs**文件，或导航到新页上，从`MainPage`。

若要实现导航，请首先更改中的代码**App.xaml.cs**构造函数，以便`NavigationPage`创建对象：

```csharp
public App()
{
    InitializeComponent();
    MainPage = new NavigationPage(new MainPage());
}
```

在中**MainPage.xaml.cs**构造函数中，可以创建一个简单`Button`，并使用事件处理程序以导航到`HelloXamlPage`:

```csharp
public MainPage()
{
    InitializeComponent();

    Button button = new Button
    {
        Text = "Navigate!",
        HorizontalOptions = LayoutOptions.Center,
        VerticalOptions = LayoutOptions.Center
    };

    button.Clicked += async (sender, args) =>
    {
        await Navigation.PushAsync(new HelloXamlPage());
    };

    Content = button;
}
```

设置`Content`属性页的替换设置`Content`XAML 文件中的属性。 在编译和部署此程序的新版本时，在屏幕上会出现一个按钮。 因此按下导航到`HelloXamlPage`。 下面是在 iPhone、 Android 和 UWP 结果页：

[![旋转标签文本](get-started-with-xaml-images/helloxaml1.png)](get-started-with-xaml-images/helloxaml1-large.png#lightbox)

您可以导航回`MainPage`使用 **< 返回**在 iOS 上，使用向左的箭头在页的顶部或底部的手机在 Android 上，或在 Windows 10 上的页的顶部使用向左的箭头按钮。

随意尝试不同的方式来呈现 XAML `Label`。 如果你需要在文本中嵌入的任何 Unicode 字符，可以使用标准的 XML 语法。 例如，若要将问候语弯引号中，使用：

 `<Label Text="&#x201C;Hello, XAML!&#x201D;" … />`

下面是如下所示：

[![带有 Unicode 字符的旋转标签文本](get-started-with-xaml-images/helloxaml2.png)](get-started-with-xaml-images/helloxaml2-large.png#lightbox)

## <a name="xaml-and-code-interactions"></a>XAML 和代码交互

**HelloXamlPage**示例仅包含单个`Label`的页上，但这是很少。 大多数`ContentPage`派生类集`Content`布局的某些属性进行排序，如`StackLayout`。 `Children`的属性`StackLayout`定义的类型为`IList<View>`但实际类型的对象`ElementCollection<View>`，并可以与多个视图或其他布局填充集合。 在 XAML 中，这些父-子关系被建立与普通 XML 层次结构。 下面是一个名为的新页的 XAML 文件**XamlPlusCodePage**:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.XamlPlusCodePage"
             Title="XAML + Code Page">
    <StackLayout>
        <Slider VerticalOptions="CenterAndExpand" />

        <Label Text="A simple Label"
               Font="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Button Text="Click Me!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

此 XAML 文件是语法上完成，而以下是如下所示：

[![页面上的多个控件](get-started-with-xaml-images/xamlpluscode1.png)](get-started-with-xaml-images/xamlpluscode1-large.png#lightbox)

但是，您很可能要考虑此应用程序是在功能上不足之处。 也许`Slider`应该会导致`Label`若要显示的当前值和`Button`可能用于执行一些在程序中操作。

正如您将看到在[第 4 部分。数据绑定基础知识](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)，显示的作业`Slider`值使用`Label`包含数据绑定可以完全在 XAML 中处理。 但首先看到的代码解决方案很有用。 即便如此，处理`Button`单击绝对需要的代码。 这意味着的代码隐藏文件`XamlPlusCodePage`必须包含的处理程序`ValueChanged`的事件`Slider`并且`Clicked`的事件`Button`。 让我们添加它们：

```csharp
namespace XamlSamples
{
    public partial class XamlPlusCodePage
    {
        public XamlPlusCodePage()
        {
            InitializeComponent();
        }

        void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
        {

        }

        void OnButtonClicked(object sender, EventArgs args)
        {

        }
    }
}
```

这些事件处理程序不需要是公共的。

返回在 XAML 文件中，`Slider`并`Button`标记需要包括的属性`ValueChanged`和`Clicked`引用这些处理程序的事件：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="XamlSamples.XamlPlusCodePage"
             Title="XAML + Code Page">
    <StackLayout>
        <Slider VerticalOptions="CenterAndExpand"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="A simple Label"
               Font="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Button Text="Click Me!"
                HorizontalOptions="Center"
                VerticalOptions="CenterAndExpand"
                Clicked="OnButtonClicked" />
    </StackLayout>
</ContentPage>
```

请注意，将一个处理程序分配到一个事件具有与分配到属性的值相同的语法。

如果处理程序`ValueChanged`的事件`Slider`将使用`Label`若要显示的当前值，处理程序需要在代码中引用该对象。 `Label`需要一个名称，使用指定`x:Name`属性。

```xaml
<Label x:Name="valueLabel"
       Text="A simple Label"
       Font="Large"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand" />
```

`x`前缀的`x:Name`属性指示此属性为 XAML 中的内部。

名称将分配给`x:Name`属性具有相同的规则C#变量的名称。 例如，它必须以字母或下划线开头并包含任何嵌入的空格。

现在`ValueChanged`事件处理程序可以设置`Label`以显示新`Slider`值。 新值是从事件参数可用：

```csharp
void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
{
    valueLabel.Text = args.NewValue.ToString("F3");
}
```

或处理程序无法获取`Slider`生成此事件的对象`sender`自变量，并获取`Value`中的属性：

```csharp
void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
{
    valueLabel.Text = ((Slider)sender).Value.ToString("F3");
}
```

首次运行该程序时`Label`不会显示`Slider`值，因为`ValueChanged`尚未尚未触发事件。 但任何操作`Slider`导致要显示的值：

[![显示的滑块值](get-started-with-xaml-images/xamlpluscode2.png)](get-started-with-xaml-images/xamlpluscode2-large.png#lightbox)

现在为`Button`。 我们来模拟的响应`Clicked`通过显示的警报事件`Text`的按钮。 事件处理程序可以安全地强制转换`sender`自变量`Button`，然后访问其属性：

```csharp
async void OnButtonClicked(object sender, EventArgs args)
{
    Button button = (Button)sender;
    await DisplayAlert("Clicked!",
        "The button labeled '" + button.Text + "' has been clicked",
        "OK");
}
```

该方法定义为`async`因为`DisplayAlert`方法是异步的应在其前面加`await`运算符，该方法完成时返回。 因为此方法获取`Button`触发此事件从`sender`参数，同一处理程序无法用于多个按钮。

您已了解在 XAML 中定义的对象可触发在代码隐藏文件中，处理的事件和代码隐藏文件，可以访问使用分配给与它的名称的 XAML 中定义的对象`x:Name`属性。 这些是代码和 XAML 进行交互的两种基本方法。

一些更深入了解如何通过检查新生成扔掉 XAML 的工作原理**XamlPlusCode.xaml.g.cs 文件**，它现在包括任何名称分配给任何`x:Name`为私有字段的属性。 下面是该文件的简化的版本：

```csharp
public partial class XamlPlusCodePage : ContentPage {

    private Label valueLabel;

    private void InitializeComponent() {
        this.LoadFromXaml(typeof(XamlPlusCodePage));
        valueLabel = this.FindByName<Label>("valueLabel");
    }
}
```

此字段的声明允许将自由地在任意位置使用该变量`XamlPlusCodePage`下您所在地区的分部类文件。 在运行时，该字段之后，分配已分析 XAML。 这意味着`valueLabel`字段是`null`时`XamlPlusCodePage`构造函数开始后但有效`InitializeComponent`调用。

之后`InitializeComponent`返回控件返回到构造函数，就像它们具有已实例化并在代码中初始化已构造的页的视觉对象。 XAML 文件不能再在类中扮演任何角色。 你能够在任何你想，例如，通过添加到视图的方式上这些对象`StackLayout`，或设置`Content`为其他页的属性完全。 你可以"遍历树"通过检查`Content`页面中的项的属性`Children`布局的集合。 可以在这种方式访问的视图上设置属性或向其动态分配事件处理程序。

可以自由。 它是您的页面，并且 XAML 是仅用于生成其内容的工具。

## <a name="summary"></a>总结

使用这个简介中，您已了解到某个类定义，如何影响 XAML 文件和代码文件和 XAML 和代码的文件如何交互。 但是，XAML 还具有其自己独特的语法功能，使其能够以非常灵活的方式使用。 你可以开始浏览这些[第 2 部分。基本 XAML 语法](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)。

## <a name="related-links"></a>相关链接

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [第 2 部分：基本 XAML 语法](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [第 3 部分：XAML 标记扩展](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [第 4 部分：数据绑定基础知识](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [第 5 部分：从数据绑定到 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
