---
title: 模型-视图-视图模型模式
description: 本章介绍 eShopOnContainers 移动应用程序如何使用 MVVM 模式完全隔离其用户界面中的应用程序的业务和演示文稿逻辑。
ms.prod: xamarin
ms.assetid: dd8c1813-df44-4947-bcee-1a1ff2334b87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 87448c556c66ea086db70699848227e1f671792b
ms.sourcegitcommit: be51b459a0a148ae3adca31d7599f53f7b2c3a68
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2019
ms.locfileid: "59019381"
---
# <a name="the-model-view-viewmodel-pattern"></a>模型-视图-视图模型模式

在 XAML 中，创建一个用户界面，然后添加在用户界面运行的代码隐藏，通常涉及到 Xamarin.Forms 开发人员体验。 在应用程序进行修改，和增长大小和作用域中时，可能出现复杂的维护工作的问题。 这些问题包括 UI 控件和业务逻辑，这会增加进行 UI 修改和此类代码进行单元测试的难度的成本之间的紧密耦合。

模型-视图-视图模型 (MVVM) 模式有助于将应用程序的的业务和演示逻辑与其用户界面 (UI) 隔离开来。 始终清晰隔离应用程序逻辑和 UI 有助于解决诸多开发问题，还可使应用程序更加易于测试、维护和改进。 这样做还可以显著改善代码重用机会，并允许开发人员和 UI 设计人员在开发各自的应用部分时能够更轻松地进行协作。

## <a name="the-mvvm-pattern"></a>MVVM 模式

MVVM 模式中有三个核心组件： 模型、 视图和视图模型。 每个用于不同用途。 图 2-1 显示了三个组件之间的关系。

![](mvvm-images/mvvm.png "MVVM 模式")

**图 2-1**:MVVM 模式

除了了解每个组件的职责，还有必要了解它们如何相互交互。 在高级别视图"知道"视图模型和视图模型"知道"模型，但模型不会意识到视图模型的和视图模型不会意识到视图。 因此，视图模型隔离模型中的视图，并允许模型独立于视图发展。

使用 MVVM 模式的好处是，如下所示：

-   如果没有现有模型实现封装现有业务逻辑，它可以很难或对其进行更改的风险。 在此方案中，视图模型充当模型类的适配器，并使你可以避免对模型代码进行任何重大更改。
-   开发人员可以创建单元测试的视图模型和模型，而无需使用该视图。 视图模型的单元测试可以运用所使用的视图相同的功能。
-   前提是完全在 XAML 中实现该视图，则可以不必触动代码，重新设计应用程序 UI。 因此，视图的新版本应适用于现有的视图模型。
-   设计人员和开发人员可以从事独立地同时及其组件在开发过程中。 设计器可以精力集中在视图中，而开发人员可以从事视图模型和模型组件。

有效地使用 MVVM 的关键在于了解如何将应用到的正确类的代码并了解如何进行交互的类。 以下各节讨论每个 MVVM 模式中的类的职责。

### <a name="view"></a>视图

视图负责定义结构、 布局和外观的用户在屏幕上看到的内容。 理想情况下，每个视图中 XAML，定义具有有限隐藏代码不包含业务逻辑。 但是，在某些情况下，隐藏代码可能包含 UI 逻辑实现很难 express 中 XAML，如动画的可视行为。

在 Xamarin.Forms 应用程序中，视图是通常[ `Page` ](xref:Xamarin.Forms.Page)-派生或[ `ContentView` ](xref:Xamarin.Forms.ContentView)-派生的类。 但是，也可以通过数据模板，指定要用来以可视方式表示的对象，在显示时的 UI 元素表示视图。 为视图的数据模板不具有任何代码隐藏，旨在将绑定到特定的视图模型类型。

> [!TIP]
> 避免启用和禁用代码隐藏中的 UI 元素。 请确保视图模型是负责定义会影响该视图的显示器，例如命令是否可用，或指示操作处于挂起状态的某些方面的逻辑状态更改。 因此，启用和禁用 UI 元素的绑定以查看模型属性，而不是启用和禁用它们，在代码隐藏中。

有几个选项以响应交互在视图中，视图模型上执行代码，如按钮单击或项选择。 如果控件支持命令，该控件`Command`属性可以进行数据绑定到`ICommand`视图模型上的属性。 调用控件的命令时，将执行在视图模型中的代码。 除了命令，行为可以附加到视图中的对象，并且可以侦听要调用的命令或事件被引发。 在响应中，然后可以调用行为`ICommand`视图模型或视图模型上的方法。

### <a name="viewmodel"></a>ViewModel

视图模型实现的属性和该视图可以数据绑定到的命令，并通知视图的更改通知事件通过任何状态更改。 属性和视图模型提供的命令定义的功能，可提供 UI，但该视图确定该功能的显示方式。

> [!TIP]
> 保持 UI 响应使用异步操作。 移动应用程序应保持 UI 线程通畅来提高用户的角度看，性能。 因此，在视图模型中，异步方法用于 I/O 操作并引发事件以异步通知的属性更改视图。

视图模型也是负责协调与所需的任何模型类的视图的交互的。 通常是视图模型和模型类之间的一个对多关系。 视图模型可能会选择公开直接到视图模型类，以便在视图中的控件可以直接与他们的数据绑定。 在这种情况下，在 model 类将需要设计支持数据绑定并更改通知事件。

每个视图模型提供了窗体中可以轻松地使用视图模型中的数据。 若要实现此目的，视图模型有时会执行数据转换。 将此数据转换放入视图模型是一个好主意，因为它提供了该视图可以绑定到的属性。 例如，视图模型可能组合两个属性，以简化显示通过视图的值。

> [!TIP]
> 集中转换层中的数据转换。 还有可能要作为一个单独的数据转换层，位于视图模型和视图之间使用转换器。 这可以是有必要，例如，当数据需要特殊格式设置的视图模型不提供。

为了使要参与双向数据绑定与视图的视图模型，其属性必须引发`PropertyChanged`事件。 视图模型满足此要求通过实现`INotifyPropertyChanged`接口，并引发`PropertyChanged`时的属性更改事件。

对于集合，视图友好`ObservableCollection<T>`提供。 此集合实现集合更改通知，从而减轻开发人员无需实现`INotifyCollectionChanged`集合上的接口。

### <a name="model"></a>模型

模型类是封装应用程序的数据的非可视类。 因此，该模型可以认为的表示在应用的域模型，通常包括数据模型以及业务和验证逻辑。 模型对象的示例包括数据传输对象 (Dto)、 普通旧 CLR 对象 (Poco) 和生成的实体和代理对象。

模型类通常使用与服务或封装数据访问和缓存的存储库中。

## <a name="connecting-view-models-to-views"></a>连接到视图的视图模型

视图模型可以连接到视图中，通过使用 Xamarin.Forms 的数据绑定功能。 有许多可用于构造视图和视图模型，将其在运行时相关联的方法。 这些方法划分为两个类别，称为复合视图第一个和第一个视图模型的组合。 第一个复合视图和模型的第一个组合是首选项和复杂性的问题的视图之间进行选择。 但是，所有方法都共享相同的目的，这是要有分配给它的 BindingContext 属性视图模型的视图。

视图与第一个复合应用程序从概念上讲组成连接到它们所依赖的视图模型的视图。 这种方法的主要优点是，它可用于轻松地构建松散耦合，单元可测试应用程序因为视图模型都具有不依赖于视图本身。 它也很容易遵循其可视结构，而无需来跟踪代码执行，若要了解如何创建和关联类通过了解应用程序的结构。 此外，查看第一个构造符合 Xamarin.Forms 导航系统，它负责构造页导航时，这样可使视图模型第一个构成复杂且与平台未对齐。

与视图模型第一个组合应用程序是从概念上讲组成视图模型与服务负责查找视图模型的视图。 视图模型第一个组合感觉有些开发人员，更自然，因为视图创建，可将抽象化，从而可以专注于应用程序的逻辑非 UI 结构将。 此外，它允许创建的其他视图模型的视图模型。 但是，这种方法通常比较复杂并且它可能会比较困难，若要了解如何创建和关联应用程序的各个部分。

> [!TIP]
> 使视图模型和视图独立。 绑定到数据源中的属性的视图应该是视图的主体依赖于其相应的视图模型。 具体而言，不引用视图类型，如[ `Button` ](xref:Xamarin.Forms.Button)并[ `ListView` ](xref:Xamarin.Forms.ListView)，从视图模型。 按照此处列出的原则，视图模型可以进行测试以隔离方式，因此通过限制作用域中减少软件缺陷的可能性。

以下各节讨论连接到视图的视图模型的主要方法。

### <a name="creating-a-view-model-declaratively"></a>以声明方式创建视图模型

最简单方法是要以声明方式实例化其对应的视图模型在 XAML 中的视图。 当构造视图时，还将构造相应的视图模型对象。 下面的代码示例演示了此方法：

```xaml
<ContentPage ... xmlns:local="clr-namespace:eShop">  
    <ContentPage.BindingContext>  
        <local:LoginViewModel />  
    </ContentPage.BindingContext>  
    ...  
</ContentPage>
```

当[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)创建实例`LoginViewModel`自动构造和设置为视图[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)。

此声明性构造和分配由该视图的视图模型的优点是它很简单，但它需要视图模型中的默认 （无参数） 构造函数的缺点是。

### <a name="creating-a-view-model-programmatically"></a>以编程方式创建视图模型

可以在结果分配给在视图模型中的代码隐藏文件中具有代码视图及其[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)属性。 通常中完成此操作视图的构造函数，如下面的代码示例中所示：

```csharp
public LoginView()  
{  
    InitializeComponent();  
    BindingContext = new LoginViewModel(navigationService);  
}
```

以编程方式构造和分配视图的代码隐藏中的视图模型的优点是简单。 但是，这种方法的主要缺点是视图需要向视图模型提供所需依赖项。 使用依赖关系注入容器有助于保持松散耦合的视图和视图模型之间。 有关详细信息，请参阅[依赖关系注入](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md)。

### <a name="creating-a-view-defined-as-a-data-template"></a>创建视图定义为一个数据模板

视图可以定义为一个数据模板和与视图模型类型相关联。 数据模板可以定义为资源，或者它们可以是以内联方式定义中将显示视图模型的控件。 控件的内容视图模型实例，并且数据模板用于以可视方式表示。 这种方法是在视图模型实例化的第一次，然后创建该视图的情况下一个示例。

<a name="automatically_creating_a_view_model_with_a_view_model_locator" />

### <a name="automatically-creating-a-view-model-with-a-view-model-locator"></a>自动使用视图模型定位符创建视图模型

视图模型定位符是管理视图模型和其关联到视图的实例化一个自定义类。 在 eShopOnContainers 移动应用中，`ViewModelLocator`类具有附加的属性， `AutoWireViewModel`，用于将视图模型与视图相关联。 在该视图的 XAML，此附加的属性设置为 true 以指示视图模型应会自动连接到视图，如下面的代码示例中所示：

```xaml
viewModelBase:ViewModelLocator.AutoWireViewModel="true"
```

`AutoWireViewModel`属性是可绑定属性的已初始化为 false，并且其值更改时`OnAutoWireViewModelChanged`调用事件处理程序。 此方法解析视图的视图模型。 下面的代码示例演示如何实现此目的：

```csharp
private static void OnAutoWireViewModelChanged(BindableObject bindable, object oldValue, object newValue)  
{  
    var view = bindable as Element;  
    if (view == null)  
    {  
        return;  
    }  

    var viewType = view.GetType();  
    var viewName = viewType.FullName.Replace(".Views.", ".ViewModels.");  
    var viewAssemblyName = viewType.GetTypeInfo().Assembly.FullName;  
    var viewModelName = string.Format(  
        CultureInfo.InvariantCulture, "{0}Model, {1}", viewName, viewAssemblyName);  

    var viewModelType = Type.GetType(viewModelName);  
    if (viewModelType == null)  
    {  
        return;  
    }  
    var viewModel = _container.Resolve(viewModelType);  
    view.BindingContext = viewModel;  
}
```

`OnAutoWireViewModelChanged`方法尝试解决在视图模型中使用基于约定的方法。 此约定可假定：

-   视图模型以相同的程序集中的视图类型。
-   视图位于。视图子命名空间。
-   视图模型以。ViewModels 子命名空间。
-   视图模型名称与视图名称开头和结尾是"ViewModel"。

最后，`OnAutoWireViewModelChanged`方法设置[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)到解决的视图模型类型的视图类型。 有关解决视图模型类型的详细信息，请参阅[解析](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#resolution)。

此方法具有应用具有单个类，它负责的视图模型和其连接的视图实例化的优点。

> [!TIP]
> 使用视图模型定位符，以便于替换。 视图模型定位符可以还用于为点替换为备用实现的依赖项，如单元测试或设计时数据。

## <a name="updating-views-in-response-to-changes-in-the-underlying-view-model-or-model"></a>在响应中的基础的更改更新 Views 视图模型

所有视图模型和可访问到视图的模型类应都实现`INotifyPropertyChanged`接口。 视图模型或模型类中实现此接口允许基础属性值更改时提供更改通知到视图中的任何数据绑定控件的类。

应用程序应通过满足以下要求架构适用于在属性更改通知的正确用法：

-   始终引发`PropertyChanged`如果公共属性的值发生更改的事件。 不要假定该引发`PropertyChanged`事件可以忽略由于 XAML 绑定方式的知识。
-   始终引发`PropertyChanged`ㄆ ン 计算其值由视图中的其他属性的属性模型。
-   始终引发`PropertyChanged`末尾的方法，使属性更改，或当已知对象处于安全状态的事件。 通过以同步方式调用事件的处理程序引发事件中断该操作。 如果操作期间发生这种情况，它可能会公开给回调函数的对象时处于不安全的、 部分更新状态。 此外，它是由触发级联更改可能`PropertyChanged`事件。 级联更改通常要求要安全地执行级联更改才会在完成更新。
-   永远不会引发`PropertyChanged`如果属性不会更改的事件。 这意味着您必须在引发之前比较旧的和新值`PropertyChanged`事件。
-   永远不会引发`PropertyChanged`期间如果正在初始化属性的视图模型的构造函数的事件。 不具有订阅视图中的数据绑定控件以在此时接收更改通知。
-   永远不会引发多个`PropertyChanged`事件具有相同属性名称参数中的单个同步调用的类的公共方法。 例如，给定`NumberOfItems`其后备存储为的属性`_numberOfItems`字段中，如果方法为增量`_numberOfItems`50 次循环的执行，过程应只引发属性更改通知上`NumberOfItems`属性一次，所有工作完成后。 对于异步方法，引发`PropertyChanged`异步延续链中的每个同步段中的给定的属性名称的事件。

EShopOnContainers 移动应用使用`ExtendedBindableObject`类以提供更改通知，下面的代码示例所示：

```csharp
public abstract class ExtendedBindableObject : BindableObject  
{  
    public void RaisePropertyChanged<T>(Expression<Func<T>> property)  
    {  
        var name = GetMemberInfo(property).Name;  
        OnPropertyChanged(name);  
    }  

    private MemberInfo GetMemberInfo(Expression expression)  
    {  
        ...  
    }  
}
```

Xamarin.Form 的[ `BindableObject` ](xref:Xamarin.Forms.BindableObject)类实现`INotifyPropertyChanged`接口，并提供[ `OnPropertyChanged` ](xref:Xamarin.Forms.BindableObject.OnPropertyChanged(System.String))方法。 `ExtendedBindableObject`类提供了`RaisePropertyChanged`方法来调用属性更改通知，并在此过程中使用提供的功能`BindableObject`类。

在 eShopOnContainers 的移动应用中每个视图模型类派生`ViewModelBase`类，后者又派生`ExtendedBindableObject`类。 因此，使用每个视图模型类`RaisePropertyChanged`中的方法`ExtendedBindableObject`类以提供属性更改通知。 下面的代码示例显示了如何在 eShopOnContainers 的移动应用通过使用 lambda 表达式调用属性更改通知：

```csharp
public bool IsLogin  
{  
    get  
    {  
        return _isLogin;  
    }  
    set  
    {  
        _isLogin = value;  
        RaisePropertyChanged(() => IsLogin);  
    }  
}
```

请注意，在这种方式中使用 lambda 表达式涉及到较低的性能开销，原因是 lambda 表达式必须计算为每个调用。 虽然性能开销很小，并且通常不会影响应用程序，有很多更改通知时，可以会产生成本。 但是，此方法的好处是，它提供了编译时类型安全和重构支持重命名属性时。

## <a name="ui-interaction-using-commands-and-behaviors"></a>使用命令和行为的 UI 交互

在移动应用中，通常以响应用户操作，例如按钮单击，可以通过在代码隐藏文件中创建一个事件处理程序实现的调用操作。 但是，在 MVVM 模式下，实现操作的职责在于视图模型，并且应避免在代码隐藏中的放置代码。

命令提供了方便地表示可以绑定到 UI 中控件的操作。 它们封装实现操作的代码，并帮助以使其从视图中其可视表示形式中分离。 Xamarin.Forms 具有控件可以以声明方式连接到一个命令，并在用户与控件交互时，这些控件将调用该命令。

行为还允许以声明方式连接到命令的控件。 但是，可以使用行为调用操作与一系列由某个控件引发的事件相关联。 因此，行为可以解决许多相同的方案为启用了命令的控件，同时提供更高的灵活性和控制。 此外，行为，还可以使用能够不专门设计的命令与之交互的控件相关联的命令对象或方法。

### <a name="implementing-commands"></a>实现命令

视图模型通常公开命令属性，用于从视图中，绑定所实现的对象实例`ICommand`接口。 提供了许多的 Xamarin.Forms 控件`Command`属性，可以将数据绑定到`ICommand`视图模型提供的对象。 `ICommand`接口定义`Execute`方法，它封装操作本身，`CanExecute`方法，它指示是否可以调用该命令，与`CanExecuteChanged`是否发生影响更改时发生的事件应执行该命令。 [ `Command` ](xref:Xamarin.Forms.Command)并[ `Command<T>` ](xref:Xamarin.Forms.Command) Xamarin.Forms 中，通过提供的类实现`ICommand`接口，其中`T`是个自变量类型`Execute`和`CanExecute`。

在视图模型中，应类型的对象[ `Command` ](xref:Xamarin.Forms.Command)或[ `Command<T>` ](xref:Xamarin.Forms.Command)类型的视图模型中每个公共属性`ICommand`。 `Command`或`Command<T>`构造函数需要`Action`具有时调用的回调对象`ICommand.Execute`调用方法。 `CanExecute`方法是一个可选的构造函数参数，并将`Func`返回`bool`。

下面的代码演示如何[ `Command` ](xref:Xamarin.Forms.Command)通过指定的委托构造实例，它表示注册命令，`Register`查看模型的方法：

```csharp
public ICommand RegisterCommand => new Command(Register);
```

该命令就会遭受视图返回的引用的属性通过`ICommand`。 当`Execute`上调用方法[ `Command` ](xref:Xamarin.Forms.Command)对象，它只是将转发到视图模型中指定的委托通过中的方法调用`Command`构造函数。

异步方法可以通过使用调用某一命令`async`并`await`关键字指定的命令时`Execute`委托。 这表示回调是`Task`和应处于等待状态。 例如，下面的代码演示如何[ `Command` ](xref:Xamarin.Forms.Command)通过指定的委托构造实例，它表示登录命令，`SignInAsync`查看模型的方法：

```csharp
public ICommand SignInCommand => new Command(async () => await SignInAsync());
```

可以将参数传递给`Execute`并`CanExecute`使用操作[ `Command<T>` ](xref:Xamarin.Forms.Command)类来实例化该命令。 例如，下面的代码演示如何`Command<T>`实例用于指示`NavigateAsync`方法将要求类型的自变量`string`:

```csharp
public ICommand NavigateCommand => new Command<string>(NavigateAsync);
```

在这种[ `Command` ](xref:Xamarin.Forms.Command)并[ `Command<T>` ](xref:Xamarin.Forms.Command)类对委托`CanExecute`中每个构造函数的方法是可选的。 如果未指定一个委托，`Command`将返回`true`为`CanExecute`。 但是，视图模型可以指示该命令的变化`CanExecute`通过调用状态`ChangeCanExecute`方法`Command`对象。 这将导致`CanExecuteChanged`事件被引发。 任何 UI 中的控件绑定到该命令将更新其已启用的状态以反映数据绑定命令的可用性。

#### <a name="invoking-commands-from-a-view"></a>调用视图中的命令

下面的代码示例演示如何[ `Grid` ](xref:Xamarin.Forms.Grid)中`LoginView`绑定到`RegisterCommand`中`LoginViewModel`通过使用类[ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer)实例：

```xaml
<Grid Grid.Column="1" HorizontalOptions="Center">  
    <Label Text="REGISTER" TextColor="Gray"/>  
    <Grid.GestureRecognizers>  
        <TapGestureRecognizer Command="{Binding RegisterCommand}" NumberOfTapsRequired="1" />  
    </Grid.GestureRecognizers>  
</Grid>
```

命令参数还可以根据需要定义使用[ `CommandParameter` ](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter)属性。 中指定的预期自变量的类型`Execute`和`CanExecute`目标方法。 当用户与附加控件交互时，[`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)将自动调用目标命令。 命令参数，如果提供，作为参数传递给命令的`Execute`委托。

<a name="implementing_behaviors" />

### <a name="implementing-behaviors"></a>实现行为

行为允许添加到 UI 控件，而无需为子类它们的功能。 功能是在行为类中实现的，并附加到控件上，就像它本身就是控件的一部分。 行为，可实现您通常必须编写为代码隐藏中，因为它直接与 API 中的方式。 可以简洁地附加到控件，并跨多个视图或应用程序打包以供重复使用的控件进行交互的代码。 在 MVVM 的上下文中，行为是用于连接到命令的控件很有用的方法。

附加到通过附加属性的控件行为被称为*附加行为*。 该行为可以使用它连接到将功能添加到该控件或该视图的可视化树中的其他控件的元素公开的 API。 EShopOnContainers 移动应用中包含`LineColorBehavior`类，该类是一个附加的行为。 有关此行为的详细信息，请参阅[显示验证错误](~/xamarin-forms/enterprise-application-patterns/validation.md#displaying_validation_errors)。

Xamarin.Forms 行为是一个类，派生自[ `Behavior` ](xref:Xamarin.Forms.Behavior)或[ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1)类，其中`T `是一种行为要应用到的控件。 这些类提供`OnAttachedTo`和`OnDetachingFrom`方法，应重写以提供附加到该行为，将其从控件中分离时，将执行的逻辑。

在 eShopOnContainers 移动应用中，`BindableBehavior<T>`类派生自[ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1)类。 目的`BindableBehavior<T>`类是为需要的 Xamarin.Forms 行为提供基类[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)设置为附加的控件的行为。

`BindableBehavior<T>`类提供了可重写`OnAttachedTo`方法以设置[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)的行为，并可重写`OnDetachingFrom`清理方法`BindingContext`。 此外，该类还在 `AssociatedObject` 属性中存储对附加控件的引用。

EShopOnContainers 移动应用包含`EventToCommandBehavior`类，该类在事件发生的响应中执行的命令。 此类派生自`BindableBehavior<T>`类，使该行为可以绑定到或执行`ICommand`指定的`Command`属性时使用行为。 以下代码示例演示 `EventToCommandBehavior` 类：

```csharp
public class EventToCommandBehavior : BindableBehavior<View>  
{  
    ...  
    protected override void OnAttachedTo(View visualElement)  
    {  
        base.OnAttachedTo(visualElement);  

        var events = AssociatedObject.GetType().GetRuntimeEvents().ToArray();  
        if (events.Any())  
        {  
            _eventInfo = events.FirstOrDefault(e => e.Name == EventName);  
            if (_eventInfo == null)  
                throw new ArgumentException(string.Format(  
                        "EventToCommand: Can't find any event named '{0}' on attached type",   
                        EventName));  

            AddEventHandler(_eventInfo, AssociatedObject, OnFired);  
        }  
    }  

    protected override void OnDetachingFrom(View view)  
    {  
        if (_handler != null)  
            _eventInfo.RemoveEventHandler(AssociatedObject, _handler);  

        base.OnDetachingFrom(view);  
    }  

    private void AddEventHandler(  
            EventInfo eventInfo, object item, Action<object, EventArgs> action)  
    {  
        ...  
    }  

    private void OnFired(object sender, EventArgs eventArgs)  
    {  
        ...  
    }  
}
```

`OnAttachedTo`并`OnDetachingFrom`方法用于注册和取消注册事件处理程序中定义的事件`EventName`属性。 然后，当触发事件时，`OnFired`调用方法时，它将执行该命令。

使用的优点`EventToCommandBehavior`来执行命令时触发事件时，是命令可以与没有专门用于使用命令进行交互的控件相关联。 此外，此事件处理将代码移动到视图模型，它可以在其中进行单元测试。

#### <a name="invoking-behaviors-from-a-view"></a>从视图调用行为

`EventToCommandBehavior`将命令附加到不支持命令的控件特别有用。 例如，`ProfileView`使用`EventToCommandBehavior`执行`OrderDetailCommand`时[ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped)上触发事件时[ `ListView` ](xref:Xamarin.Forms.ListView)所示，它列出用户的订单在下面的代码：

```xaml
<ListView>  
    <ListView.Behaviors>  
        <behaviors:EventToCommandBehavior             
            EventName="ItemTapped"  
            Command="{Binding OrderDetailCommand}"  
            EventArgsConverter="{StaticResource ItemTappedEventArgsConverter}" />  
    </ListView.Behaviors>  
    ...  
</ListView>
```

在运行时，`EventToCommandBehavior`将会响应与交互[ `ListView` ](xref:Xamarin.Forms.ListView)。 中选择一项时`ListView`，则[ `ItemTapped` ](xref:Xamarin.Forms.ListView.ItemTapped)会触发事件，其将执行`OrderDetailCommand`中`ProfileViewModel`。 默认情况下，会将该事件的事件自变量传递给命令。 此数据将转换源和目标之间传递中指定的转换器`EventArgsConverter`属性，它返回[ `Item` ](xref:Xamarin.Forms.ItemTappedEventArgs.Item)的`ListView`从[ `ItemTappedEventArgs`](xref:Xamarin.Forms.ItemTappedEventArgs). 因此，当`OrderDetailCommand`执行时，所选`Order`作为参数传递给已注册的操作。

有关行为的详细信息，请参阅[行为](~/xamarin-forms/app-fundamentals/behaviors/index.md)。

## <a name="summary"></a>总结

模型-视图-视图模型 (MVVM) 模式有助于将应用程序的的业务和演示逻辑与其用户界面 (UI) 隔离开来。 始终清晰隔离应用程序逻辑和 UI 有助于解决诸多开发问题，还可使应用程序更加易于测试、维护和改进。 这样做还可以显著改善代码重用机会，并允许开发人员和 UI 设计人员在开发各自的应用部分时能够更轻松地进行协作。

使用 MVVM 模式，应用的 UI 和基本的呈现和业务逻辑分为三个单独的类： 视图，它封装 UI 和 UI 逻辑;封装表示逻辑和状态，则该视图模型和封装应用程序的业务逻辑和数据的模型。


## <a name="related-links"></a>相关链接

- [下载电子书 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （示例）](https://github.com/dotnet-architecture/eShopOnContainers)
