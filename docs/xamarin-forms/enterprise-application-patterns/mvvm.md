---
title: 模型-视图-ViewModel 模式
description: 本章介绍了 eShopOnContainers 移动应用如何使用 MVVM 模式将应用的业务和表示逻辑与用户界面完全分离。
ms.prod: xamarin
ms.assetid: dd8c1813-df44-4947-bcee-1a1ff2334b87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: d6c9b74c9abc1a2c493c31699b52969a7d129429
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760200"
---
# <a name="the-model-view-viewmodel-pattern"></a>模型-视图-ViewModel 模式

Xamarin Forms 开发人员体验通常涉及在 XAML 中创建一个用户界面，然后添加在用户界面上操作的代码隐藏。 当修改应用，并且大小和范围增加时，可能会出现复杂的维护问题。 这些问题包括 UI 控件和业务逻辑之间的紧密耦合，这会增加进行 UI 修改的成本，以及单元测试此类代码的困难。

模型-视图-视图模型 (MVVM) 模式有助于将应用程序的的业务和演示逻辑与其用户界面 (UI) 隔离开来。 始终清晰隔离应用程序逻辑和 UI 有助于解决诸多开发问题，还可使应用程序更加易于测试、维护和改进。 这样做还可以显著改善代码重用机会，并允许开发人员和 UI 设计人员在开发各自的应用部分时能够更轻松地进行协作。

## <a name="the-mvvm-pattern"></a>MVVM 模式

MVVM 模式中有三个核心组件：模型、视图和视图模型。 每个服务都有一个不同的用途。 图2-1 显示了这三个组件之间的关系。

![](mvvm-images/mvvm.png "MVVM 模式")

**图 2-1**：MVVM 模式

除了了解每个组件的职责外，还必须了解这些组件之间的交互方式。 在较高级别，视图 "了解" 视图模型，视图模型 "了解" 模型，但模型不知道视图模型，并且视图模型不知道视图。 因此，视图模型将视图与模型隔离开来，并允许模型独立于视图进行发展。

使用 MVVM 模式的优点如下：

- 如果现有模型实现封装了现有业务逻辑，更改它可能会比较困难或风险。 在此方案中，视图模型充当模型类的适配器，并使您可以避免对模型代码进行任何重大更改。
- 开发人员可以为视图模型和模型创建单元测试，而无需使用视图。 视图模型的单元测试可以执行与视图所使用的功能完全相同的功能。
- 可在不触及代码的情况下重新设计应用 UI，前提是视图完全以 XAML 实现。 因此，新版本的视图应使用现有的视图模型。
- 设计人员和开发人员可以在开发过程中独立、同时在其组件上运行。 设计人员可以专注于视图，而开发人员可以处理视图模型和模型组件。

使用 MVVM 的关键是要了解如何将应用程序代码分解为正确的类，并了解类的交互方式。 以下各节讨论 MVVM 模式中每个类的职责。

### <a name="view"></a>视图

视图负责定义用户在屏幕上看到的内容的结构、布局和外观。 理想情况下，在 XAML 中定义每个视图，但不包含业务逻辑的代码隐藏有限。 但是，在某些情况下，代码隐藏可能包含用于实现视觉对象的 UI 逻辑，该行为在 XAML 中很难表达，例如动画。

在 Xamarin 窗体应用程序中，视图通常[`Page`](xref:Xamarin.Forms.Page)是派生类或[`ContentView`](xref:Xamarin.Forms.ContentView)派生类。 但是，视图也可以通过数据模板来表示，后者指定在显示对象时要使用的 UI 元素以直观方式表示该对象。 作为视图的数据模板没有任何代码隐藏，旨在绑定到特定视图模型类型。

> [!TIP]
> 避免在代码隐藏中启用和禁用 UI 元素。 确保视图模型负责定义逻辑状态更改，这些更改会影响视图显示的某些方面，例如命令是否可用，或指示操作挂起。 因此，通过绑定来启用和禁用 UI 元素，而不是在代码隐藏中启用和禁用这些属性。

有多个选项可用于对视图模型执行代码以响应视图上的交互，如按钮单击或项选择。 如果控件支持命令，则可以将控件`Command`的属性数据绑定`ICommand`到视图模型上的属性。 调用控件的命令时，将执行视图模型中的代码。 除了命令外，行为还可以附加到视图中的对象，并且可以侦听要调用的命令或要引发的事件。 作为响应，该行为随后可以`ICommand`对视图模型或视图模型上的方法调用。

### <a name="viewmodel"></a>ViewModel

视图模型实现视图可数据绑定到的属性和命令，并通过更改通知事件向视图通知任何状态更改。 视图模型提供的属性和命令定义 UI 提供的功能，但视图确定如何显示该功能。

> [!TIP]
> 使 UI 保持异步操作的响应能力。 移动应用应阻止 UI 线程取消阻止，以提高用户对性能的认知度。 因此，在视图模型中，使用异步方法执行 i/o 操作，并引发事件以异步通知属性更改的视图。

视图模型还负责协调视图与所需的任何模型类的交互。 视图模型与模型类之间通常存在一对多关系。 视图模型可能会选择将模型类直接公开给视图，以便视图中的控件可以直接与它们进行数据绑定。 在这种情况下，模型类需要设计为支持数据绑定和更改通知事件。

每个视图模型都提供视图可以轻松使用的窗体中的数据。 若要实现此目的，视图模型有时会执行数据转换。 将此数据转换放入视图模型是一个不错的想法，因为它提供了视图可以绑定到的属性。 例如，视图模型可能组合了两个属性的值，以便可以更轻松地显示视图。

> [!TIP]
> 在转换层集中进行数据转换。 还可以使用转换器作为视图模型和视图之间的单独数据转换层。 例如，当数据需要视图模型不提供的特殊格式时，这可能是必需的。

为了使视图模型可以参与视图的双向数据绑定，其属性必须引发`PropertyChanged`事件。 视图模型通过实现`INotifyPropertyChanged`接口满足此要求，并在属性发生更改时`PropertyChanged`引发事件。

对于集合，提供友好`ObservableCollection<T>`的视图。 此集合实现集合更改通知，使开发人员无需在集合上`INotifyCollectionChanged`实现接口。

### <a name="model"></a>模型

模型类是封装应用数据的非视觉类。 因此，可以将模型视为表示应用的域模型，此模型通常包含一个数据模型以及业务和验证逻辑。 模型对象的示例包括数据传输对象（Dto）、普通旧 CLR 对象（Poco）以及生成的实体和代理对象。

Model 类通常与封装数据访问和缓存的服务或存储库结合使用。

## <a name="connecting-view-models-to-views"></a>将视图模型连接到视图

可以使用 Xamarin 的数据绑定功能将视图模型连接到视图。 有多种方法可用于构造视图和查看模型，并在运行时将它们关联起来。 这些方法分为两个类别（称为 "查看第一个组合"）和 "查看模型优先"。 选择 "查看第一种组合" 和 "查看模型优先"，这是首选项和复杂性问题。 但是，所有方法都具有相同的目标，这是为了使视图具有分配给其 BindingContext 属性的视图模型。

通过查看第一撰写，应用在概念上由连接到其所依赖的视图模型的视图组成。 此方法的主要优点是可以轻松构造松散耦合的单元可测试应用，因为视图模型本身不依赖于视图。 还可以通过遵循其视觉对象来轻松了解应用的结构，而无需跟踪代码执行以了解如何创建和关联类。 此外，查看第一次构造与 Xamarin. Forms 导航系统（负责浏览导航时的页面）保持一致，使视图模型优先于平台进行比较。

使用视图模型优先组合时，应用程序在概念上由视图模型组成，服务负责查找视图模型的视图。 对某些开发人员来说，查看模型优先编写起来更加自然，因为视图创建可以被抽象掉，使其能够专注于应用程序的逻辑非 UI 结构。 此外，它还允许其他视图模型创建视图模型。 但是，这种方法通常很复杂，并且很难理解如何创建和关联应用程序的各个部分。

> [!TIP]
> 使视图模型和视图保持独立。 视图与数据源中的属性的绑定应为视图在其对应的视图模型上的依赖关系。 具体而言，请不要从视图模型引用[`Button`](xref:Xamarin.Forms.Button)视图[`ListView`](xref:Xamarin.Forms.ListView)类型（如和）。 遵循此处所述的原则，可以隔离的方式测试视图模型，从而通过限制范围来降低软件缺陷的可能性。

以下各节介绍了将视图模型连接到视图的主要方法。

### <a name="creating-a-view-model-declaratively"></a>以声明方式创建视图模型

最简单的方法是在 XAML 中以声明方式实例化其相应的视图模型。 构造视图时，还将构造相应的视图模型对象。 下面的代码示例演示了此方法：

```xaml
<ContentPage ... xmlns:local="clr-namespace:eShop">  
    <ContentPage.BindingContext>  
        <local:LoginViewModel />  
    </ContentPage.BindingContext>  
    ...  
</ContentPage>
```

创建后，将自动构造的一个实例`LoginViewModel` ，并将其设置为视图的[`BindingContext`。](xref:Xamarin.Forms.BindableObject.BindingContext) [`ContentPage`](xref:Xamarin.Forms.ContentPage)

视图模型通过此声明性构造和分配视图模型的优点是很简单，但它的缺点是它需要在视图模型中使用默认（无参数）构造函数。

### <a name="creating-a-view-model-programmatically"></a>以编程方式创建视图模型

视图可以在代码隐藏文件中包含代码，这会导致视图模型被分配到其[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)属性。 这通常在视图的构造函数中完成，如下面的代码示例所示：

```csharp
public LoginView()  
{  
    InitializeComponent();  
    BindingContext = new LoginViewModel(navigationService);  
}
```

在视图的代码隐藏内，以编程方式构造和分配视图模型的优点是很简单。 但是，这种方法的主要缺点是，视图需要提供具有任何所需依赖关系的视图模型。 使用依赖关系注入容器有助于维持视图模型与视图模型之间的松散耦合。 有关详细信息，请参阅[依赖关系注入](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md)。

### <a name="creating-a-view-defined-as-a-data-template"></a>创建定义为数据模板的视图

视图可以定义为数据模板并与视图模型类型相关联。 数据模板可以定义为资源，也可以在将显示视图模型的控件内以内联方式定义数据模板。 控件的内容为视图模型实例，使用数据模板直观地表示它。 此方法的一个示例是首先实例化视图模型，然后创建视图。

<a name="automatically_creating_a_view_model_with_a_view_model_locator" />

### <a name="automatically-creating-a-view-model-with-a-view-model-locator"></a>自动创建具有视图模型定位器的视图模型

视图模型定位符是一个自定义类，它管理视图模型的实例化以及它们与视图的关联。 在 eShopOnContainers 移动应用中， `ViewModelLocator`类具有一个附加`AutoWireViewModel`属性，该属性用于将视图模型与视图相关联。 在视图的 XAML 中，此附加属性设置为 true 以指示视图模型应自动连接到视图，如下面的代码示例所示：

```xaml
viewModelBase:ViewModelLocator.AutoWireViewModel="true"
```

属性是一个被初始化为 false 的可绑定属性，当它的值`OnAutoWireViewModelChanged`更改时，将调用事件处理程序。 `AutoWireViewModel` 此方法解析视图的视图模型。 下面的代码示例演示如何实现此目的：

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

此`OnAutoWireViewModelChanged`方法尝试使用基于约定的方法解析视图模型。 此约定假定：

- 视图模型与视图类型位于同一程序集中。
- 视图在中。查看子命名空间。
- 视图模型在中。Viewmodel 子命名空间。
- 视图模型名称与视图名称对应，以 "ViewModel" 结尾。

最后， `OnAutoWireViewModelChanged`方法[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)将视图类型的设置为已解析的视图模型类型。 有关解析视图模型类型的详细信息，请参阅[解决方法](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#resolution)。

此方法的优点是，应用具有一个类，该类负责实例化视图模型及其与视图的连接。

> [!TIP]
> 使用视图模型定位符简化替换。 视图模型定位符还可用作依赖项的替代实现的替代点，如用于单元测试或设计时数据。

## <a name="updating-views-in-response-to-changes-in-the-underlying-view-model-or-model"></a>更新视图以响应基础视图模型或模型中的更改

视图可以访问的所有视图模型和模型类都应该实现`INotifyPropertyChanged`接口。 当基础属性值更改时，在视图模型或模型类中实现此接口可使类向视图中的任何数据绑定控件提供更改通知。

应用应通过满足以下要求，设计为正确使用属性更改通知：

- 如果公共属性`PropertyChanged`的值发生更改，则总是引发事件。 不要假设引发`PropertyChanged`事件的原因在于知道 XAML 绑定的发生方式。
- 始终为其`PropertyChanged`值由视图模型或模型中的其他属性使用的任何计算属性引发事件。
- 始终在方法`PropertyChanged`的末尾引发事件，该方法将更改属性，或者已知对象处于安全状态。 引发事件时，会通过同步调用事件的处理程序来中断操作。 如果在操作过程中发生这种情况，则当对象处于不安全且部分更新的状态时，它可能会将对象公开给回调函数。 此外， `PropertyChanged`事件还可以触发级联更改。 级联更改通常需要完成更新后，才能安全地执行级联更改。
- 如果属性未`PropertyChanged`更改，则永远不引发事件。 这意味着，在引发`PropertyChanged`事件之前，必须比较旧值和新值。
- 如果正在初始化`PropertyChanged`属性，则决不会在视图模型的构造函数中引发事件。 此视图中的数据绑定控件此时不会订阅接收更改通知。
- 永远不要在类的`PropertyChanged`公共方法的单个同步调用中引发多个具有相同属性名称参数的事件。 例如`NumberOfItems` ，给定一个后备存储是字段的`_numberOfItems`属性，如果方法在循环执行期间`_numberOfItems`增加了50次，则它`NumberOfItems`应只引发一次属性更改通知，完成所有工作之后。 对于异步方法，为异步`PropertyChanged`延续链的每个同步段中的给定属性名称引发事件。

EShopOnContainers 移动应用使用`ExtendedBindableObject`类提供更改通知，如以下代码示例所示：

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

Xamarin 类实现`INotifyPropertyChanged`接口，并提供[方法。`OnPropertyChanged`](xref:Xamarin.Forms.BindableObject.OnPropertyChanged(System.String)) [`BindableObject`](xref:Xamarin.Forms.BindableObject) 类提供调用属性`RaisePropertyChanged`更改通知的方法，在此过程中使用`BindableObject`类提供的功能。 `ExtendedBindableObject`

EShopOnContainers 移动应用中的每个视图模型类均派生`ViewModelBase`自类，后者又派生`ExtendedBindableObject`自类。 因此，每个视图模型类都使用`RaisePropertyChanged` `ExtendedBindableObject`类中的方法来提供属性更改通知。 下面的代码示例演示了 eShopOnContainers 移动应用如何使用 lambda 表达式调用属性更改通知：

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

请注意，以这种方式使用 lambda 表达式涉及较小的性能开销，因为必须为每个调用计算 lambda 表达式。 尽管性能开销很小，并且通常不会影响应用程序，但当存在许多更改通知时，成本可能会发生变化。 但是，这种方法的优点是在重命名属性时提供编译时类型安全和重构支持。

## <a name="ui-interaction-using-commands-and-behaviors"></a>使用命令和行为的 UI 交互

在移动应用中，通常会调用操作来响应用户操作（如按钮单击），该操作可通过在代码隐藏文件中创建事件处理程序来实现。 但在 MVVM 模式下，实现该操作的责任在于查看模型，应避免在代码隐藏中放置代码。

命令提供了一种简便的方法来表示可绑定到 UI 中控件的操作。 它们封装了实现该操作的代码，有助于使其与视图中的可视化表示形式保持分离。 Xamarin 包含可通过声明方式连接到命令的控件，当用户与控件交互时，这些控件将调用该命令。

行为还允许以声明方式将控件连接到命令。 但是，可以使用行为来调用与控件引发的一系列事件关联的操作。 因此，行为可解决许多与启用命令的控件相同的方案，同时提供更强的灵活性和控制度。 此外，还可以使用行为将命令对象或方法与未专门设计为与命令交互的控件相关联。

### <a name="implementing-commands"></a>实现命令

视图模型通常会从视图中公开用于实现`ICommand`接口的对象实例的命令属性。 许多 Xamarin 控件提供`Command`了一个属性，该属性可以是绑定到视图模型所提供的`ICommand`对象的数据。 接口定义一个`Execute`方法，该方法封装操作本身、一个`CanExecute`方法（指示是否可以调用`CanExecuteChanged`此命令）以及发生更改时发生的事件，该事件会影响是否`ICommand`应执行该命令。 `T` `ICommand` `Execute`由 Xamarin [`Command<T>`](xref:Xamarin.Forms.Command)提供的`CanExecute`和类实现接口，其中是和的参数的类型。 [`Command`](xref:Xamarin.Forms.Command)

在视图模型中，对于类型[`Command`](xref:Xamarin.Forms.Command) `ICommand`为的视图模型中的[`Command<T>`](xref:Xamarin.Forms.Command)每个公共属性，都应该有一个类型为或的对象。 或构造函数`ICommand.Execute` 需要`Action`调用方法时调用的回调对象。 `Command<T>` `Command` 方法是可选的构造函数参数， `Func`是返回`bool`的。 `CanExecute`

下面的代码演示如何[`Command`](xref:Xamarin.Forms.Command)通过指定`Register`视图模型方法的委托来构造表示 register 命令的实例：

```csharp
public ICommand RegisterCommand => new Command(Register);
```

命令通过返回对的引用`ICommand`的属性向视图公开。 [`Command`](xref:Xamarin.Forms.Command)对对象调用`Command`方法时，只需通过在构造函数中指定的委托，将调用转发到视图模型中的方法。 `Execute`

在指定命令的`async` `Execute`委托时，可以通过使用和`await`关键字来调用异步方法。 这表示回调为`Task` ，应等待。 例如，下面的代码演示如何[`Command`](xref:Xamarin.Forms.Command)通过指定`SignInAsync`视图模型方法的委托来构造表示登录命令的实例：

```csharp
public ICommand SignInCommand => new Command(async () => await SignInAsync());
```

通过使用`Execute` `CanExecute` [类实例化命令，可以将参数传递`Command<T>`](xref:Xamarin.Forms.Command)给和操作。 例如，下面的代码演示如何`Command<T>`使用实例来指示该`NavigateAsync`方法需要类型`string`为的参数：

```csharp
public ICommand NavigateCommand => new Command<string>(NavigateAsync);
```

[`Command`](xref:Xamarin.Forms.Command)在和[`Command<T>`](xref:Xamarin.Forms.Command)类中`CanExecute` ，每个构造函数中的方法的委托都是可选的。 如果未指定委托， `Command`将为`CanExecute`返回`true` 。 但是，视图模型可以通过对`CanExecute` `Command`对象`ChangeCanExecute`调用方法来指示命令状态的更改。 这会`CanExecuteChanged`引发事件。 绑定到该命令的 UI 中的所有控件都将更新其启用状态以反映数据绑定命令的可用性。

#### <a name="invoking-commands-from-a-view"></a>从视图中调用命令

下面的代码示例[`Grid`](xref:Xamarin.Forms.Grid)演示如何`LoginView`使用`RegisterCommand` `LoginViewModel` [实例在类中`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)绑定到的。

```xaml
<Grid Grid.Column="1" HorizontalOptions="Center">  
    <Label Text="REGISTER" TextColor="Gray"/>  
    <Grid.GestureRecognizers>  
        <TapGestureRecognizer Command="{Binding RegisterCommand}" NumberOfTapsRequired="1" />  
    </Grid.GestureRecognizers>  
</Grid>
```

此外，还可以使用[`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter)属性定义命令参数。 预期参数的类型是在`Execute`和`CanExecute`目标方法中指定的。 当用户与附加控件交互时，[`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)将自动调用目标命令。 命令参数（如果提供）将作为参数传递给命令的`Execute`委托。

<a name="implementing_behaviors" />

### <a name="implementing-behaviors"></a>实现行为

行为允许向 UI 控件添加功能，而无需对这些控件添加子类。 功能是在行为类中实现的，并附加到控件上，就像它本身就是控件的一部分。 利用行为，你可以实现通常必须作为代码隐藏来编写的代码，因为它直接与控件的 API 交互，这种方法可以将其简洁地附加到控件，并打包以便在多个视图或应用上重复使用。 在 MVVM 的上下文中，行为是将控件连接到命令的有用方法。

通过附加属性附加到控件的行为称为*附加行为*。 然后，该行为可以使用它附加到的元素的公开 API 向该控件的可视化树中的控件或其他控件添加功能。 EShopOnContainers 移动应用包含`LineColorBehavior`类，该类是附加的行为。 有关此行为的详细信息，请参阅[显示验证错误](~/xamarin-forms/enterprise-application-patterns/validation.md#displaying_validation_errors)。

Xamarin. 窗体行为是从[`Behavior`](xref:Xamarin.Forms.Behavior)或[`Behavior<T>`](xref:Xamarin.Forms.Behavior`1)类派生的类，其中`T`是该行为应应用到的控件的类型。 这些类提供`OnAttachedTo`了`OnDetachingFrom`和方法，应重写这些方法，以提供在将行为附加到控件并将其与控件分离时执行的逻辑。

在 eShopOnContainers 移动应用中， `BindableBehavior<T>`类派生[`Behavior<T>`](xref:Xamarin.Forms.Behavior`1)自类。 `BindableBehavior<T>`类的目的是为 Xamarin. 窗体行为提供一个基类，要求将该[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)行为的设置为附加的控件。

[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) `OnDetachingFrom` `BindingContext`类提供可重写`OnAttachedTo`的方法，该方法用于设置该行为的，以及一个清除的可重写方法。 `BindableBehavior<T>` 此外，该类还在 `AssociatedObject` 属性中存储对附加控件的引用。

EShopOnContainers 移动应用包括`EventToCommandBehavior`类，该类可执行命令以响应事件发生。 此类派生自`BindableBehavior<T>`类，以便在使用该行为时，该行为可以绑定到并`Command` `ICommand`执行由属性指定的。 以下代码示例演示 `EventToCommandBehavior` 类：

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

和方法用于为`EventName`属性中定义的事件注册和取消注册事件处理程序。 `OnDetachingFrom` `OnAttachedTo` 然后，当事件引发`OnFired`时，将调用方法，这会执行命令。

在事件激发时使用`EventToCommandBehavior`来执行命令的优点是，可以将命令与未设计为与命令交互的控件相关联。 此外，此操作将事件处理代码移到查看模型，可以在其中进行单元测试。

#### <a name="invoking-behaviors-from-a-view"></a>从视图调用行为

`EventToCommandBehavior`对于将命令附加到不支持命令的控件，特别有用。 `ProfileView`例如， `OrderDetailCommand`如果[`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped)事件在列出`EventToCommandBehavior`用户的订单的[`ListView`](xref:Xamarin.Forms.ListView)上引发，则使用来执行，如以下代码所示：

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

在运行时， `EventToCommandBehavior`将响应与的[`ListView`](xref:Xamarin.Forms.ListView)交互。 `ListView`当在中选择某个项时，将[`ItemTapped`](xref:Xamarin.Forms.ListView.ItemTapped)激发该事件`OrderDetailCommand` ，该事件`ProfileViewModel`将在中执行。 默认情况下，事件的事件参数将传递给命令。 此数据将`EventArgsConverter`在通过属性中指定的转换器在源和目标之间传递时进行转换，这会`ListView`从[`Item`](xref:Xamarin.Forms.ItemTappedEventArgs.Item) [`ItemTappedEventArgs`](xref:Xamarin.Forms.ItemTappedEventArgs)返回。 因此，当执行`OrderDetailCommand`时，所选`Order`的将作为参数传递给注册的操作。

有关行为的详细信息，请参阅[行为](~/xamarin-forms/app-fundamentals/behaviors/index.md)。

## <a name="summary"></a>总结

模型-视图-视图模型 (MVVM) 模式有助于将应用程序的的业务和演示逻辑与其用户界面 (UI) 隔离开来。 始终清晰隔离应用程序逻辑和 UI 有助于解决诸多开发问题，还可使应用程序更加易于测试、维护和改进。 这样做还可以显著改善代码重用机会，并允许开发人员和 UI 设计人员在开发各自的应用部分时能够更轻松地进行协作。

使用 MVVM 模式，应用的 UI 和基础表示形式和业务逻辑分为三个单独的类：视图，它封装 UI 和 UI 逻辑;用于封装表示逻辑和状态的视图模型;和模型，它封装应用的业务逻辑和数据。

## <a name="related-links"></a>相关链接

- [下载电子书（2Mb）](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers （GitHub）（示例）](https://github.com/dotnet-architecture/eShopOnContainers)
