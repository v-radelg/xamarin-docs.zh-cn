---
title: 企业应用程序导航
description: 本章介绍 eShopOnContainers 移动应用程序如何执行从视图模型的视图模型第一个导航。
ms.prod: xamarin
ms.assetid: 4cad57b5-7fe4-4527-a988-d9b60c9620b4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: d306b0c1c0d08129671e27b96911ec771acb658e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61298925"
---
# <a name="enterprise-app-navigation"></a>企业应用程序导航

Xamarin.Forms 包括对页面导航、 从用户的交互与 UI 或从应用本身由于内部逻辑驱动的状态更改时，通常会支持。 但是，导航可以是复杂，无法在应用中使用模型-视图-视图模型 (MVVM) 模式的实现必须满足以下挑战：

-   如何确定视图以导航到，使用一种方法，不会引入的紧密耦合和视图之间的依赖项。
-   如何协调要导航到的视图是实例化并初始化过程。 在使用 MVVM，视图和视图模型需要实例化并通过该视图的绑定上下文相互关联。 在应用程序使用依赖关系注入容器，视图和视图模型的实例化可能需要特定构造机制。
-   是否要执行的第一个视图的导航窗格中，或查看模型的第一个导航。 与第一个视图的导航窗格中，页后，可以导航到引用的视图类型的名称。 在导航窗格中，指定的视图实例化时，以及其对应的视图模型和其他从属服务。 另一种方法是使用视图模型第一个导航栏中，导航到的页引用视图模型类型的名称。
-   如何来顺利跨视图和视图模型中分离应用程序的导航行为。 MVVM 模式提供了应用程序的 UI 和其演示文稿和业务逻辑之间的分隔。 但是，应用的导航行为通常将跨越应用的 UI 和演示文稿的部分。 用户通常将启动的导航从一个视图，视图将替换由于导航。 但是，导航通常还需要启动或从视图模型中进行协调。
-   如何在用于初始化导航期间传递的参数。 例如，如果用户导航到要更新订单详细信息的视图时，将订单数据将必须传递给视图，以便它可以显示正确的数据。
-   如何对坐标导航栏中，以确保遵守某些业务规则。 例如，可能会离开一个视图，以便它们可以更正所有无效的数据也会提示你提交或放弃所做的视图中的任何数据更改前提示用户。

通过提供这一章来应对这些挑战`NavigationService`类，用于执行视图模型第一个页面导航。

> [!NOTE]
> `NavigationService`由应用程序仅用于执行 ContentPage 实例之间的分层导航。 使用服务来导航其他页类型之间可能会导致意外行为。

## <a name="navigating-between-pages"></a>页面之间导航

导航逻辑可以驻留在视图的代码隐藏中，或在数据绑定的视图模型。 将导航逻辑放在视图中可能是最简单的方法，虽然并不通过单元测试轻松测试。 将导航逻辑放在视图模型类意味着通过单元测试可在逻辑。 此外，视图模型接下来即可实现用于控制导航的逻辑，以确保强制实施特定业务规则。 例如，应用可能不允许用户导航离开页面而不必首先确保输入的数据有效。

一个`NavigationService`类通常从视图模型，以提升可测试性调用。 但是，从视图模型导航到视图，将需要引用视图，和尤其是活动视图模型与不相关联，这不推荐的视图的视图模型。 因此，`NavigationService`显示此处指定为要导航到的目标的视图模型类型。

EShopOnContainers 移动应用使用`NavigationService`类以提供视图模型第一个导航。 此类实现`INavigationService`接口，在下面的代码示例所示：

```csharp
public interface INavigationService  
{  
    ViewModelBase PreviousPageViewModel { get; }  
    Task InitializeAsync();  
    Task NavigateToAsync<TViewModel>() where TViewModel : ViewModelBase;  
    Task NavigateToAsync<TViewModel>(object parameter) where TViewModel : ViewModelBase;  
    Task RemoveLastFromBackStackAsync();  
    Task RemoveBackStackAsync();  
}
```

此接口指定实现类必须提供以下方法：

|方法|用途|
|--- |--- |
|`InitializeAsync`|在应用启动时执行导航到两个页面之一。|
|`NavigateToAsync`|执行分层导航到指定页。|
|`NavigateToAsync(parameter)`|执行指定的页上，将参数传递到的分层导航。|
|`RemoveLastFromBackStackAsync`|从导航堆栈中删除前一页。|
|`RemoveBackStackAsync`|从导航堆栈中删除所有以前的页面。|

此外，`INavigationService`接口指定实现类必须提供`PreviousPageViewModel`属性。 此属性返回与前一页导航堆栈中关联的视图模型类型。

> [!NOTE]
> `INavigationService`接口却通常还指定`GoBackAsync`方法，用于以编程方式返回到导航堆栈中的上一页。 但是，此方法是 eShopOnContainers 移动应用中缺少，因为这不是必需的。

### <a name="creating-the-navigationservice-instance"></a>创建 NavigationService 实例

`NavigationService`类，该类实现`INavigationService`接口中，注册为单一实例使用 Autofac 依赖关系注入容器，如下面的代码示例中所示：

```csharp
builder.RegisterType<NavigationService>().As<INavigationService>().SingleInstance();
```

`INavigationService`接口中已解决`ViewModelBase`类构造函数，如下面的代码示例中所示：

```csharp
NavigationService = ViewModelLocator.Resolve<INavigationService>();
```

这将返回的引用`NavigationService`创建的 Autofac 依赖关系注入容器中存储的对象`InitNavigation`中的方法`App`类。 有关详细信息，请参阅[启动导航时应用](#navigating_when_the_app_is_launched)。

`ViewModelBase`类存储`NavigationService`实例中`NavigationService`类型的属性， `INavigationService`。 因此，所有视图模型类，派生自`ViewModelBase`类中，可以使用`NavigationService`属性访问由指定的方法`INavigationService`接口。 这可以避免注入的开销`NavigationService`Autofac 依赖关系注入容器到每个视图模型类中的对象。

### <a name="handling-navigation-requests"></a>处理导航请求

Xamarin.Forms 提供了[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)类，该类实现用户是在其中可以向前和向后，根据需要导航页面，通过提供分层导航体验。 有关分层导航的详细信息，请参阅[分层导航](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)。

而不是使用[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)类直接，eShopOnContainers 应用程序包装`NavigationPage`类`CustomNavigationView`类，如下面的代码示例中所示：

```csharp
public partial class CustomNavigationView : NavigationPage  
{  
    public CustomNavigationView() : base()  
    {  
        InitializeComponent();  
    }  

    public CustomNavigationView(Page root) : base(root)  
    {  
        InitializeComponent();  
    }  
}
```

此包装的目的是为了便于样式[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)类的 XAML 文件中的实例。

调用其中一个视图模型类中执行导航`NavigateToAsync`方法，指定正在导航到，如下面的代码示例中所示的页面的视图模型类型：

```csharp
await NavigationService.NavigateToAsync<MainViewModel>();
```

下面的代码示例演示`NavigateToAsync`提供的方法`NavigationService`类：

```csharp
public Task NavigateToAsync<TViewModel>() where TViewModel : ViewModelBase  
{  
    return InternalNavigateToAsync(typeof(TViewModel), null);  
}  

public Task NavigateToAsync<TViewModel>(object parameter) where TViewModel : ViewModelBase  
{  
    return InternalNavigateToAsync(typeof(TViewModel), parameter);  
}
```

每个方法，派生自任何视图模型类`ViewModelBase`类，以通过调用执行分层导航`InternalNavigateToAsync`方法。 此外，第二个`NavigateToAsync`方法便可导航数据指定为参数传递到视图模型要导航到，它通常用于执行初始化。 有关详细信息，请参阅[将参数传递期间导航](#passing_parameters_during_navigation)。

`InternalNavigateToAsync`方法执行导航请求，并在下面的代码示例所示：

```csharp
private async Task InternalNavigateToAsync(Type viewModelType, object parameter)  
{  
    Page page = CreatePage(viewModelType, parameter);  

    if (page is LoginView)  
    {  
        Application.Current.MainPage = new CustomNavigationView(page);  
    }  
    else  
    {  
        var navigationPage = Application.Current.MainPage as CustomNavigationView;  
        if (navigationPage != null)  
        {  
            await navigationPage.PushAsync(page);  
        }  
        else  
        {  
            Application.Current.MainPage = new CustomNavigationView(page);  
        }  
    }  

    await (page.BindingContext as ViewModelBase).InitializeAsync(parameter);  
}  

private Type GetPageTypeForViewModel(Type viewModelType)  
{  
    var viewName = viewModelType.FullName.Replace("Model", string.Empty);  
    var viewModelAssemblyName = viewModelType.GetTypeInfo().Assembly.FullName;  
    var viewAssemblyName = string.Format(  
                CultureInfo.InvariantCulture, "{0}, {1}", viewName, viewModelAssemblyName);  
    var viewType = Type.GetType(viewAssemblyName);  
    return viewType;  
}  

private Page CreatePage(Type viewModelType, object parameter)  
{  
    Type pageType = GetPageTypeForViewModel(viewModelType);  
    if (pageType == null)  
    {  
        throw new Exception($"Cannot locate page type for {viewModelType}");  
    }  

    Page page = Activator.CreateInstance(pageType) as Page;  
    return page;  
}
```

`InternalNavigateToAsync`方法执行到视图模型导航通过首先调用`CreatePage`方法。 此方法可查找对应于指定的视图模型类型，并创建并返回此视图类型的实例的视图。 定位到视图模型类型相对应的视图使用基于约定的方法，这将假定：

-   视图是视图模型类型相同的程序集中。
-   视图位于。视图子命名空间。
-   视图模型以。ViewModels 子命名空间。
-   视图名称对应于"模型"中删除视图的模型名称。

实例化视图时，它都有其对应的视图模型进行关联。 有关如何发生这种情况的详细信息，请参阅[会自动使用视图模型定位符创建视图模型](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator)。

如果正在创建的视图是`LoginView`，它包装内的新实例`CustomNavigationView`类，并分配给[ `Application.Current.MainPage` ](xref:Xamarin.Forms.Application.MainPage)属性。 否则为`CustomNavigationView`检索，并提供其不为 null，实例[ `PushAsync` ](xref:Xamarin.Forms.NavigationPage)调用方法以将推送到导航堆栈上所创建的视图。 但是，如果检索到`CustomNavigationView`实例是`null`，所创建的视图中的新实例包装`CustomNavigationView`类，并分配给`Application.Current.MainPage`属性。 此机制可确保在导航期间，页面将添加正确到导航堆栈时为空，并包含数据时。

> [!TIP]
> 请考虑缓存页。 页面缓存中当前未显示的视图的内存占用情况的结果。 但是，而无需页面缓存它意味着 XAML 分析和构造的页面和其视图模型在每次新的页导航到，这会产生性能影响的复杂页面时将发生。 对于不使用过多的控件设计良好页上，性能应该足够。 但是，在页面缓存可能有助于，如果遇到缓慢页面加载时间。

创建并导航到，该视图后`InitializeAsync`执行方法的视图关联的视图模型。 有关详细信息，请参阅[将参数传递期间导航](#passing_parameters_during_navigation)。

<a name="navigating_when_the_app_is_launched" />

### <a name="navigating-when-the-app-is-launched"></a>导航时该应用启动

当启动应用时，`InitNavigation`中的方法`App`类调用。 下面的代码示例演示此方法：

```csharp
private Task InitNavigation()  
{  
    var navigationService = ViewModelLocator.Resolve<INavigationService>();  
    return navigationService.InitializeAsync();  
}
```

该方法创建一个新`NavigationService`Autofac 依赖关系注入容器中的对象并返回到它，然后再调用一个引用其`InitializeAsync`方法。

> [!NOTE]
> 当`INavigationService`接口通过解决`ViewModelBase`类中，容器返回的引用`NavigationService`InitNavigation 方法调用时创建的对象。

下面的代码示例演示`NavigationService``InitializeAsync`方法：

```csharp
public Task InitializeAsync()  
{  
    if (string.IsNullOrEmpty(Settings.AuthAccessToken))  
        return NavigateToAsync<LoginViewModel>();  
    else  
        return NavigateToAsync<MainViewModel>();  
}
```

`MainView`导航到，如果应用了缓存的访问令牌，用于进行身份验证。 否则为`LoginView`导航到。

有关 Autofac 依赖关系注入容器的详细信息，请参阅[依赖关系注入简介](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md#introduction_to_dependency_injection)。

<a name="passing_parameters_during_navigation" />

### <a name="passing-parameters-during-navigation"></a>在导航过程传递参数

之一`NavigateToAsync`方法，通过指定`INavigationService`接口，实现导航数据指定为参数传递到视图模型要导航到，它通常用于执行初始化。

例如，`ProfileViewModel`类包含`OrderDetailCommand`时用户选择订单上执行的`ProfileView`页。 反过来，此时将执行`OrderDetailAsync`方法，在下面的代码示例所示：

```csharp
private async Task OrderDetailAsync(Order order)  
{  
    await NavigationService.NavigateToAsync<OrderDetailViewModel>(order);  
}
```

此方法将调用到导航`OrderDetailViewModel`，并传入`Order`实例，它表示用户选择的顺序`ProfileView`页。 当`NavigationService`类创建`OrderDetailView`，则`OrderDetailViewModel`实例化并分配给该视图的类[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)。 导航到`OrderDetailView`，则`InternalNavigateToAsync`方法执行`InitializeAsync`视图的方法关联的视图模型。

`InitializeAsync`方法中定义`ViewModelBase`为可重写的方法的类。 此方法指定`object`表示要在导航操作期间传递到视图模型的数据的参数。 因此，想要从导航操作接收数据的视图模型类提供其自己的实现`InitializeAsync`方法来执行所需的初始化。 下面的代码示例演示`InitializeAsync`方法从`OrderDetailViewModel`类：

```csharp
public override async Task InitializeAsync(object navigationData)  
{  
    if (navigationData is Order)  
    {  
        ...  
        Order = await _ordersService.GetOrderAsync(  
                        Convert.ToInt32(order.OrderNumber), authToken);  
        ...  
    }  
}
```

此方法检索`Order`实例，在导航操作时，传递到视图模型并使用它来检索完整的订单详细信息从`OrderService`实例。

<a name="invoking_navigation_using_behaviors" />

### <a name="invoking-navigation-using-behaviors"></a>使用行为调用导航

导航通常由用户交互触发从视图。 例如，`LoginView`执行导航后成功进行身份验证。 下面的代码示例演示如何导航调用行为：

```xaml
<WebView ...>  
    <WebView.Behaviors>  
        <behaviors:EventToCommandBehavior  
            EventName="Navigating"  
            EventArgsConverter="{StaticResource WebNavigatingEventArgsConverter}"  
            Command="{Binding NavigateCommand}" />  
    </WebView.Behaviors>  
</WebView>
```

在运行时，`EventToCommandBehavior`将会响应与交互[ `WebView` ](xref:Xamarin.Forms.WebView)。 当`WebView`导航到 web 页上， [ `Navigating` ](xref:Xamarin.Forms.WebView.Navigating)会触发事件，其将执行`NavigateCommand`中`LoginViewModel`。 默认情况下，会将该事件的事件自变量传递给命令。 此数据将转换源和目标之间传递中指定的转换器`EventArgsConverter`属性，它返回[ `Url` ](xref:Xamarin.Forms.WebNavigationEventArgs.Url)从[ `WebNavigatingEventArgs` ](xref:Xamarin.Forms.WebNavigatingEventArgs)。 因此，当`NavigationCommand`是执行，web 页的 Url 作为参数传递到已注册`Action`。

依次`NavigationCommand`执行`NavigateAsync`方法，在下面的代码示例所示：

```csharp
private async Task NavigateAsync(string url)  
{  
    ...          
    await NavigationService.NavigateToAsync<MainViewModel>();  
    await NavigationService.RemoveLastFromBackStackAsync();  
    ...  
}
```

此方法将调用到导航`MainViewModel`，并遵循导航栏中，将删除`LoginView`从导航堆栈的页。

### <a name="confirming-or-cancelling-navigation"></a>确认或取消导航

应用程序可能需要与用户交互在导航操作中，以便用户可以确认或取消导航。 这可能是有必要，例如，当用户尝试之前已完全完成数据进入页导航。 在这种情况下，应用应提供一条通知，允许用户导航离开页面，或者发生前取消导航操作。 这可以通过在视图模型类中使用来自通知的响应来控制调用导航。

## <a name="summary"></a>总结

Xamarin.Forms 包含对页面导航的支持，通常在逻辑驱动的状态更改时，因用户与 UI 交互或通过应用本身而引起页面导航。 但是，在使用 MVVM 模式的应用中实现导航可能较为复杂。

本章介绍`NavigationService`类，用于执行从视图模型的视图模型第一个导航。 将导航逻辑放置在视图模型类中意味着可通过自动测试来运用该逻辑。 此外，视图模型接下来即可实现用于控制导航的逻辑，以确保强制实施特定业务规则。


## <a name="related-links"></a>相关链接

- [下载电子书 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （示例）](https://github.com/dotnet-architecture/eShopOnContainers)
