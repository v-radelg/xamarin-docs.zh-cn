---
title: Xamarin.Forms Shell
description: Xamarin.Forms Shell 通过提供大多数应用程序需要的基本 UI 功能降低了 Xamarin.Forms 应用程序的复杂性，本文介绍它的用法。
ms.prod: xamarin
ms.assetid: 1A674212-72DB-4AA4-B626-A4EC135AD1A0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2018
ms.openlocfilehash: dec1bb2f3f2eead3a1d50f37a0f1281523548b66
ms.sourcegitcommit: 6d41b5d48fd626d3f649809ed5480e5356755f14
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2019
ms.locfileid: "55986271"
---
# <a name="xamarinforms-shell"></a>Xamarin.Forms Shell

![预览](~/media/shared/preview.png)

[![下载示例](~/media/shared/download.png) 下载示例](https://github.com/Microsoft/TailwindTraders-Mobile)

Xamarin.Forms Shell 是应用程序容器，提供大多数应用程序需要的基本 UI 功能，让你能够专注于处理应用程序的核心工作负载。 Shell 应用程序具有以下功能：

- 一个用于描述应用程序的视觉对象结构的位置。
- 常见的导航用户界面。
- 具有深层链接的导航服务。
- 集成的搜索处理程序。

上述功能可简化应用程序，并提高开发者的工作效率。 此外，编写 Shell 时综合考虑了渲染速度和内存占用率。

> [!IMPORTANT]
> 现有的 iOS 和 Android 应用程序可以采用 Shell，立即获益于导航、性能和扩展性方面的改进。

Shell 提供了基于浮出控件和选项卡的固定导航用户界面。 Shell 应用程序中的最顶级导航是一个浮出控件：

![浮出控件](shell-images/flyout.png "Flyout")

下一级导航是底部选项卡栏：

![底部选项卡](shell-images/bottom-tabs-full.png "Bottom tabs")

浮出控件未打开时，系统将底部选项卡栏视作顶级导航

在各个底部选项卡内，下一级导航为顶部选项卡栏，其中每个选项卡均为 `ContentPage`：

![顶部选项卡](shell-images/top-tabs-full.png "Top tabs")

在每个 `ContentPage` 内，可以添加其他 `ContentPage` 实例或从导航堆栈中删除这些实例。

## <a name="bootstrapping-a-shell-application"></a>启动 Shell 应用程序

将 `App` 类的 `MainPage` 属性设置为 Shell 文件的实例，即可启动 Shell 应用程序：

```csharp
namespace TailwindTraders.Mobile
{
    public partial class App : Application
    {
        public App()
        {
            InitializeComponent();

            MainPage = new TheShell();
        }
    }
}
```

`TheShell` 类是描述应用程序的视觉对象结构的 XAML 文件。

> [!IMPORTANT]
> Shell 当前处于试验阶段，只能通过以下方式使用：在调用 `Forms.Init` 方法前，向平台项目添加 `Forms.SetFlags("Shell_Experimental");`。

# <a name="androidtabandroid"></a>[Android](#tab/android)

```csharp
public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        global::Xamarin.Forms.Forms.SetFlags("Shell_Experimental");

        TabLayoutResource = Resource.Layout.Tabbar;
        ToolbarResource = Resource.Layout.Toolbar;

        base.OnCreate(savedInstanceState);

        global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
        LoadApplication(new App());
    }
}
```

# <a name="iostabios"></a>[iOS](#tab/ios)

```csharp
public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate
{
    public override bool FinishedLaunching(UIApplication app, NSDictionary options)
    {
        global::Xamarin.Forms.Forms.SetFlags("Shell_Experimental");

        global::Xamarin.Forms.Forms.Init();
        LoadApplication(new App());

        return base.FinishedLaunching(app, options);
    }
}
```

----

## <a name="shell-file-hierarchy"></a>Shell 文件层次结构

Shell 文件由三个层次结构元素组成：

- `ShellItem`。 浮出控件中的一个或多个项。 每个 `ShellItem` 都是 `Shell` 的子元素。
- `ShellSection`。 分组内容，可通过底部选项卡导航。 每个 `ShellSection` 都是 `ShellItem` 的子元素。
- `ShellContent`。 应用程序中的 `ContentPage` 实例，可通过顶部选项卡导航。 每个 `ShellContent` 都是 `ShellSection` 的子元素。

上述元素均不表示任何用户界面，而表示应用程序视觉对象结构的组织。 Shell 使用这些元素，生成内容的导航用户界面。

以下 XAML 演示一个简单的 Shell 文件示例：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:local="clr-namespace:TailwindTraders.Mobile.Features.Shell"
       x:Class="TailwindTraders.Mobile.Features.Shell.TheShell"
       Title="TailwindTraders">
    <ShellItem Title="Home">
        <ShellSection>
            <ShellContent>
                <local:HomePage />
            </ShellContent>
        </ShellSection>
    </ShellItem>
</Shell>
```

> [!NOTE]
> 每个 `ShellItem` 还可以设置 `ImageSource` 的 `FlyoutIcon` 属性（显示在标题左侧）。

此 XAML 显示 `HomePage`因为它是 Shell 文件中声明的第一个内容项：

![主页](shell-images/homepage.png "Home page")

按汉堡按钮显示浮出控件：

![打开的浮出控件](shell-images/flyout-initial.png "Flyout open")

向 Shell 文件添加更多 `ShellItem` 实例可增加浮出控件中的项数。 但请注意，`HomePage` 是在应用程序启动期间创建的，使用此方法添加其他 `ShellItem` 实例会导致在应用程序启动期间也创建这些页面。 将 `ShellContent.ContentTemplate` 属性设置为 `DataTemplate` 可避免此现象：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:local="clr-namespace:TailwindTraders.Mobile.Features.Shell"
       x:Class="TailwindTraders.Mobile.Features.Shell.TheShell"
       Title="TailwindTraders">
    <ShellItem Title="Home">
        <ShellSection>
            <ShellContent>
                <local:HomePage />
            </ShellContent>
        </ShellSection>
    <ShellItem>
    <ShellContent Title="Profile"
                  ContentTemplate="{DataTemplate local:ProfilePage}" />
</Shell>
```

通过这种方法，`ProfilePage` 仅在用户导航到时创建，而不是在应用程序启动时创建。 还请注意，对于 `ProfilePage`而言，省去了 `ShellItem` 和 `ShellSection` 对象，而 `Title` 属性是在 `ShellContent` 实例上定义的。 这种方法很简洁，需要的标记较少，Shell 只需提供必要的逻辑包装器（无需向可视化树引入额外的视图）。

此外，每个 `ShellItem` 外观均可通过将 `Shell.ItemTemplate` 属性设置为 `DataTemplate` 来进行自定义：

```xaml
<Shell.ItemTemplate>
    <DataTemplate>
        <ContentView HeightRequest="32">
            <ContentView.Padding>
                <Thickness Left="32"
                           Top="16" />
            </ContentView.Padding>
            <Label Text="{Binding Title}" />
        </ContentView>
    </DataTemplate>
</Shell.ItemTemplate>          
```

此代码只是为浮出控件内每个 `ShellItem` 的文本进行了重新定位。 请注意，Shell 向 `DataTemplate` 的 `BindingContext` 提供 `Title`（和 `Icon`）属性。

## <a name="flyout"></a>浮出控件

浮出控件是应用程序的根菜单，由浮出控件标头和菜单项组成：

![带批注的浮出控件](shell-images/flyout-annotated.png "Annotated flyout")

### <a name="flyout-behavior"></a>浮出控件行为

访问浮出控件的方式有两种：按汉堡按钮或从屏幕一侧轻扫。 但，将 `Shell.FlyoutBehavior` 属性设置为 `FlyoutBehavior` 枚举成员之一可以更改此行为：

```xaml
<Shell ...
       FlyoutBehavior="Disabled">
    ...
</Shell>
```

将 `FlyoutBehavior` 属性设置为 `Disabled` 可隐藏浮出控件，只有一个 `ShellItem` 时此设置非常有用。 其他有效的 `FlyoutBehavior` 值为 `Flyout`（默认）和 `Locked`。

### <a name="flyout-header"></a>浮出控件标头

浮出控件标头是根据需要显示在浮出控件顶部的内容，使用可通过 `Shell.FlyoutHeader` 属性值设置的 `View` 定义其外观：

```xaml
<Shell.FlyoutHeader>
    <local:FlyoutHeader />
</Shell.FlyoutHeader>
```

还可以通过将 `Shell.FlyoutHeaderTemplate` 属性设置为 `DataTemplate` 的方式来定义浮出控件标头的外观：

```xaml
<Shell.FlyoutHeaderTemplate>
    <DataTemplate>
        <StackLayout HorizontalOptions="Fill"
                     VerticalOptions="Fill"
                     BackgroundColor="White"
                     Padding="16">
            <Label FontSize="Medium"
                   Text="Smart Shopping">
                <Label.Margin>
                    <Thickness Left="8" />
                </Label.Margin>
            </Label>
            <Button Image="photo"
                    Text="By taking a photo">
                <Button.Margin>
                    <Thickness Top="16" />
                </Button.Margin>
            </Button>
            <Button Image="ia"
                    Text="By using AR">
                <Button.Margin>
                    <Thickness Top="8" />
                </Button.Margin>
            </Button>
        </StackLayout>
    </DataTemplate>
</Shell.FlyoutHeaderTemplate>
```

此 XAML 生成以下浮出控件标头：

![浮出控件标头](shell-images/flyout-header.png "Flyout header")

默认情况下，在浮出控件中，浮出控件标头是固定的，在有足够的项时会滚动其下方的内容。 但，将 `Shell.FlyoutHeaderBehavior` 属性设置为 `FlyoutHeaderBehavior` 枚举成员之一可以更改此行为：

```xaml
<Shell ...
       FlyoutHeaderBehavior="CollapseOnScroll">
    ...
</Shell>
```

将 `FlyoutHeaderBehavior` 设置为 `CollapseOnScroll` 会在发生滚动时折叠浮出控件。 其他有效的 `FlyoutHeaderBehavior` 值为 `Default``Fixed` 和 `Scroll`（滚动菜单项）。

## <a name="menu-items"></a>菜单项

增加浮出控件中的项数的方式是添加更多 `ShellItem` 实例。 也可以向浮出控件添加 `MenuItem` 实例。 这样，就可以实现在通过 `MenuItem.CommandParameter` 属性传递数据时导航到相同页面等方案。

应将 `MenuItem` 实例添加到 `Shell.MenuItems` 集合中：

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:local="clr-namespace:TailwindTraders.Views"
       x:Class="TailwindTraders.Shell"
       FlyoutHeaderBehavior="Fixed"
       Title="Tailwind Traders"
       ...>
    <Shell.MenuItems>
        <MenuItem Command="{Binding ProductTypeCommand}"
                  CommandParameter="1"
                  Text="Holiday decorations" />
        <MenuItem Command="{Binding ProductTypeCommand}"
                  CommandParameter="2"
                  Text="Appliances" />
        <MenuItem Command="{Binding ProductTypeCommand}"
                  CommandParameter="3"
                  Text="Bathrooms" />
        ...
    </Shell.MenuItems>
    <ShellItem Title="Home">
        <ShellSection>
            <ShellContent>
                <local:HomePage />
            </ShellContent>
        </ShellSection>
    </ShellItem>    
</Shell>
```

> [!NOTE]
> 每个 `MenuItem` 还可以设置 `FileImageSource` 的 `Icon` 属性（将显示在文本左侧）。

此 XAML 生成以下浮出控件：

![完整的浮出控件](shell-images/flyout-full.png "Full flyout")

此外，将 `Shell.MenuItemTemplate` 属性设置为 `DataTemplate` 可自定义 `MenuItem` 的外观：

```xaml
<Shell.MenuItemTemplate>
    <DataTemplate>
        <ContentView HeightRequest="32">
            <ContentView.Padding>
                <Thickness Left="32"
                           Top="16" />
            </ContentView.Padding>
            <Label Text="{Binding Text}"
                   FontAttributes="Bold" />
        </ContentView>
    </DataTemplate>
</Shell.MenuItemTemplate>
```

结果，`MenuItem` 实例使用粗体呈现文本：

![加粗的菜单项](shell-images/menuitems-bold.png "Bold menu items")

## <a name="tabs"></a>制表符

倘若一个 `ShellItem` 中有多个 `ShellSection` 实例，则 `ShellSection` 实例呈现为底部选项卡：

```xaml
<ShellItem Title="Bottom Tab Sample"
           Style="{StaticResource BaseStyle}">
    <ShellSection Title="AR" Icon="ia.png">
        <ShellContent ContentTemplate="{DataTemplate local:ARPage}"/>
    </ShellSection>
    <ShellSection Title="Photo" Icon="photo.png">
        <ShellContent ContentTemplate="{DataTemplate local:PhotoPage}"/>
    </ShellSection>
</ShellItem>
```

在此示例中，`ShellSection` 实例将以底部选项卡的形式呈现：

![底部选项卡](shell-images/tabs-bottom.png "Bottom tabs")

倘若一个 `ShellSection` 内有多个 `ShellContent` 实例，则 `ShellContent` 项呈现为顶部选项卡：

```xaml
<ShellItem Title="Store Home"
           Shell.TitleView="Store Home"
           Style="{StaticResource BaseStyle}">
    <ShellSection Title="Browse Product">
        <ShellContent Title="Featured"
                      ContentTemplate="{DataTemplate local:FeaturedPage}" />
        <ShellContent Title="On Sale"
                      ContentTemplate="{DataTemplate local:SalePage}" />
    </ShellSection>
</ShellItem>
```

在此示例中，`ShellContent` 实例将以顶部选项卡的形式呈现：

![顶部选项卡](shell-images/tabs-top.png "Top tabs")

可通过以下方式设置选项卡的样式：使用 XAML 样式或提供自定义呈现器。 例如，下面的示例演示设置选项卡颜色的 XAML 样式：

```xaml
<Style x:Key="BaseStyle"
       TargetType="Element">
    <Setter Property="Shell.ShellTabBarBackgroundColor"
            Value="#3498DB" />
    <Setter Property="Shell.ShellTabBarTitleColor"
            Value="White" />
    <Setter Property="Shell.ShellTabBarUnselectedColor"
            Value="#B4FFFFFF" />
</Style>
```

## <a name="navigation"></a>导航

Shell 包括基于 URI 的导航体验。 URI 提供改良的导航体验：可以导航到应用程序中的任何页面，而无需遵循设置的导航层次结构。 此外，它还能够向后导航，不必访问导航堆栈上的所有页面。

基于 URI 的导航通过路由完成，路由是用于在应用程序内导航的 URI 段。 Shell 文件必须声明路由方案、路由主机和路由：

```xaml
<Shell ...
       Route="tailwindtraders"
       RouteHost="www.microsoft.com"
       RouteScheme="app">
    ...
</Shell>
```

`RouteScheme``RouteHost` 和 `Route` 属性值三者结合，构成 `app://www.microsoft.com/tailwindtraders` 根 URI。

Shell 文件中的每个元素还可以定义可在编程导航中使用的路由属性。

在 Shell 文件构造函数或者在调用路由前运行的其他位置中，可为不由 Shell 元素（例如 `MenuItem` 元素）表示的任何页面显式地注册其他路由：

```csharp
Routing.RegisterRoute("productcategory", typeof(ProductCategoryPage));
```

### <a name="implementing-navigation"></a>实现导航

菜单项公开可用于实现导航的 `Command` 属性：

```csharp
public ICommand ProductTypeCommand { get; } = new Command<string>(NavigateToProductType);

static void NavigateToProductType(string typeId)
{
  (App.Current.MainPage as Xamarin.Forms.Shell).GoToAsync($"app://tailwindtraders/productcategory?id={typeId}", true);
}
```

要调用导航，必须获得通过 `Application` 类的 `MainPage` 属性对应用程序 `Shell` 的引用。 然后，可以传递一个有效的 URI 作为参数，通过调用 `GoToAsync` 方法调用导航。 `GoToAsync` 方法使用 `ShellNavigationState` 对象进行导航，该对象是从 `string` 或 `Uri` 构造的。

### <a name="passing-data"></a>传递数据

可以通过查询字符串参数在页面间传递数据。 Shell 在你向类中添加查询属性特性时设置 `ContentPage` 或 ViewModel 上的查询字符串参数值：

```csharp
[QueryProperty("TypeID", "id")]
public partial class ProductCategoryPage : ContentPage
{
    private string _typeId;

    public ProductCategoryPage()
    {
        InitializeComponent();

        BindingContext = new ProductCategoryViewModel();
    }

    public string TypeID
    {
        get => _typeId;
        set => MyLabel.Text = value;
    }
}
```

`QueryProperty` 特性指定 `TypeID` 将接收从 `GoToAsync` 方法调用中的 URI 传入 `id` 查询字符串参数的数据。

### <a name="intercepting-navigation"></a>截获导航

通过 Shell，可以在导航完成前或完成后，通过挂钩接入导航路由。 可以通过分别为 `Navigating` 和 `Navigated` 事件注册事件处理程序来实现此操作：

```xaml
<Shell ...
       Navigating="OnShellNavigating">
    ...
</Shell>
```

```csharp
void OnShellNavigating(object sender, ShellNavigatingEventArgs e)
{
  if (...)
  {
    e.Cancel(); // Cancel the navigation
  }
}
```

`ShellNavigatingEventArgs` 类提供以下属性：

| Property | 类型 | 说明​​ |
|---|---|---|
| 当前 | `ShellNavigationState` | 当前页的 URI。 |
| 源 | `ShellNavigationState` | 表示导航起始位置的 URI。 |
| 目标 | `ShellNavigationSource`  | 表示导航目标位置的 URI。 |
| CanCancel  | `bool` | 指示是否可以取消导航的值。 |
| 已取消  | `bool` | 指示是否已取消导航的值。 |

此外，`ShellNavigatingEventArgs` 类提供 `Cancel` 方法。

`ShellNavigatedEventArgs` 类提供以下属性：

| Property | 类型 | 说明​​ |
|---|---|---|
| 当前 | `ShellNavigationState` | 当前页的 URI。 |
| 上一个| `ShellNavigationState` | 上一页的 URI。 |
| 源  | `ShellNavigationSource` | 表示导航起始位置的 URI。 |

此外，Shell 提供了可重写的 `OnBackButtonPressed` 方法，可以用来截获按后退按钮的操作。

## <a name="related-links"></a>相关链接

- [Tailwind Traders（示例）](https://github.com/Microsoft/TailwindTraders-Mobile)
