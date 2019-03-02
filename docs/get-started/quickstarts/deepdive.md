---
title: Xamarin.Forms 快速入门的深入探讨
description: 本文介绍了使用 Xamarin.Forms 开发应用程序的基础知识。 涵盖的主题包括：Xamarin.Forms 应用程序剖析、体系结构和应用程序基础知识以及用户界面。
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 7B2340A1-6883-41D8-860C-0BB6C4E0C316
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/27/2018
ms.openlocfilehash: 8e466c80468551f8262cfe49556d9527a147de4c
ms.sourcegitcommit: d62732ce6f3f9d8dc929d72d4acac3e592cba073
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/01/2019
ms.locfileid: "57197545"
---
# <a name="xamarinforms-quickstart-deep-dive"></a>Xamarin.Forms 快速入门的深入探讨

在中[Xamarin.Forms 快速入门](~/get-started/index.yml)，说明应用程序。 本文对已生成的内容进行回顾，以深入了解有关 Xamarin.Forms 应用程序工作原理的基础知识。

::: zone pivot="windows"

## <a name="introduction-to-visual-studio"></a>Visual Studio 简介

Visual Studio 将代码组织为解决方案和项目。 解决方案是可以容纳一个或多个项目的容器。 项目可以是应用程序、支持库、测试应用程序等。 说明应用程序包含一个解决方案，其中包含四个项目，如以下屏幕截图中所示：

![](deepdive-images/vs/solution.png "Visual Studio 解决方案资源管理器")

这些项目如下：

- 说明-此项目是.NET Standard 库项目，其中包含的所有共享的代码和共享的 UI。
- Notes.Android-此项目包含 Android 特定代码，是 Android 应用程序的入口点。
- Notes.iOS-此项目包含 iOS 特定代码，是 iOS 应用程序的入口点。
- Notes.UWP-此项目包含通用 Windows 平台 (UWP) 的特定代码，是 UWP 应用程序的入口点。

## <a name="anatomy-of-a-xamarinforms-application"></a>Xamarin.Forms 应用程序剖析

下面的屏幕截图显示 Visual Studio 中的说明.NET Standard 库项目的内容：

![](deepdive-images/vs/net-standard-project.png "Phoneword .NET Standard 项目内容")

项目具有包含 NuGet 和 SDK 节点的依赖项节点：

- **NuGet** &ndash; Xamarin.Forms 和 sqlite net pcl 的 NuGet 包已添加到项目。
- **SDK** &ndash; `NETStandard.Library` 元包，它引用定义 .NET Standard 的一整套 NuGet 包。

::: zone-end
::: zone pivot="macos"

## <a name="introduction-to-visual-studio-for-mac"></a>Visual Studio for Mac 简介

[Visual Studio for Mac](/visualstudio/mac/) 遵循将代码组织为解决方案和项目的 Visual Studio 做法。 解决方案是可以容纳一个或多个项目的容器。 项目可以是应用程序、支持库、测试应用程序等。 说明应用程序包含一个解决方案，其中包含三个项目，如以下屏幕截图中所示：

![](deepdive-images/vsmac/solution.png "Visual Studio for Mac 解决方案窗格")

这些项目如下：

- 说明-此项目是.NET Standard 库项目，其中包含的所有共享的代码和共享的 UI。
- Notes.Android-此项目包含 Android 特定代码，是 Android 应用程序的入口点。
- Notes.iOS-此项目包含 iOS 特定代码，是 iOS 应用程序的入口点。

## <a name="anatomy-of-a-xamarinforms-application"></a>Xamarin.Forms 应用程序剖析

下面的屏幕截图显示说明.NET Standard 库项目的内容在 Visual Studio for Mac:

![](deepdive-images/vsmac/net-standard-project.png "Phoneword .NET Standard 库项目内容")

项目具有包含 NuGet 和 SDK 节点的依赖项节点：

- **NuGet** &ndash; Xamarin.Forms 和 sqlite net pcl 的 NuGet 包已添加到项目。
- **SDK** &ndash; `NETStandard.Library` 元包，它引用定义 .NET Standard 的一整套 NuGet 包。

::: zone-end

项目还包括多个文件：

- **Data\NoteDatabase.cs** – 此类包含代码以创建数据库、 从中读取数据、 将数据写入到它，并从其中删除数据。
- **Models\Note.cs** – 此类定义`Note`其实例的应用程序中存储有关每个说明数据模型。
- **App.xaml** - `App` 类的 XAML 标记，该类定义应用程序的资源字典。
- **App.xaml.cs** - `App` 类的代码隐藏，该类负责实例化应用程序在每个平台上将显示的首页，并处理应用程序生命周期事件。
- **NotesPage.xaml** – 用于 XAML 标记`NotesPage`类，该类定义应用程序启动时显示的页面的 UI。
- **NotesPage.xaml.cs** – 用于代码隐藏`NotesPage`类，该类包含在用户与页面交互时执行的业务逻辑。
- **NoteEntryPage.xaml** – 用于 XAML 标记`NoteEntryPage`类，该类定义当用户输入下所示的页面的 UI。
- **NoteEntryPage.xaml.cs** – 用于代码隐藏`NoteEntryPage`类，该类包含在用户与页面交互时执行的业务逻辑。

有关 Xamarin.iOS 应用程序剖析的详细信息，请参阅 [Xamarin.iOS 应用程序剖析](~/ios/get-started/hello-ios/hello-ios-deepdive.md#anatomy-of-a-xamarinios-application)。 有关 Xamarin.Android 应用程序剖析的详细信息，请参阅 [Xamarin Android 应用程序剖析](~/android/get-started/hello-android/hello-android-deepdive.md#anatomy)。

## <a name="architecture-and-application-fundamentals"></a>体系结构和应用程序基础知识

Xamarin.Forms 应用程序采用与传统跨平台应用程序相同的构建方式。 共享代码通常位于 .NET Standard 库中，平台特定应用程序将使用此共享代码。 下图显示了这种关系说明应用程序的概述：

::: zone pivot="windows"

![](deepdive-images/vs/architecture.png "说明体系结构")

::: zone-end
::: zone pivot="macos"

![](deepdive-images/vsmac/architecture.png "说明体系结构")

::: zone-end

若要最大限度重用启动代码，Xamarin.Forms 应用程序需有一个名为 `App` 的单个类，该类负责实例化应用程序在每个平台上将显示的首页，如以下代码示例所示：

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Xaml;

[assembly: XamlCompilation(XamlCompilationOptions.Compile)]
namespace Notes
{
    public partial class App : Application
    {
        public App()
        {
            InitializeComponent();
            MainPage = new NavigationPage(new NotesPage());
        }
        ...
    }
}
```

此代码将设置`MainPage`的属性`App`类来[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)实例，其内容是`NotesPage`实例。 此外，[`XamlCompilation`](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute) 属性可打开 XAML 编译器，以使 XAML 直接编译为中间语言。 有关详细信息，请参阅 [XAML 编译](~/xamarin-forms/xaml/xamlc.md)。

## <a name="launching-the-application-on-each-platform"></a>启动每个平台上的应用程序

### <a name="ios"></a>iOS

若要启动 Xamarin.Forms 初始页面在 iOS 中的，Notes.iOS 项目定义`AppDelegate`继承的类`FormsApplicationDelegate`类：

```csharp
namespace Notes.iOS
{
    [Register("AppDelegate")]
    public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate
    {
        public override bool FinishedLaunching(UIApplication app, NSDictionary options)
        {
            global::Xamarin.Forms.Forms.Init();
            LoadApplication(new App());
            return base.FinishedLaunching(app, options);
        }
    }
}
```

通过调用 `Init` 方法，`FinishedLaunching` 替代会初始化 Xamarin.Forms 框架。 这会导致在通过调用 `LoadApplication` 方法来设置根视图控制器之前，先将特定于 iOS 的 Xamarin.Forms 实现加载到应用程序。

### <a name="android"></a>Android

若要启动 Xamarin.Forms 初始页面在 Android 中的，Notes.Android 项目包括创建的代码`Activity`与`MainLauncher`属性，与活动继承自`FormsAppCompatActivity`类：

```csharp
namespace Notes.Droid
{
    [Activity(Label = "Notes",
              Icon = "@mipmap/icon",
              Theme = "@style/MainTheme",
              MainLauncher = true,
              ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
    {
        protected override void OnCreate(Bundle savedInstanceState)
        {
            TabLayoutResource = Resource.Layout.Tabbar;
            ToolbarResource = Resource.Layout.Toolbar;

            base.OnCreate(savedInstanceState);
            global::Xamarin.Forms.Forms.Init(this, savedInstanceState);
            LoadApplication(new App());
        }
    }
}
```

通过调用 `Init` 方法，`OnCreate` 替代会初始化 Xamarin.Forms 框架。 这会导致在加载 Xamarin.Forms 应用程序之前，将特定于 Android 的 Xamarin.Forms 实现加载到应用程序。

::: zone pivot="windows"

### <a name="universal-windows-platform"></a>通用 Windows 平台

在通用 Windows 平台 (UWP) 应用程序中，可从 `App` 类调用初始化 Xamarin.Forms 框架的 `Init` 方法：

```csharp
Xamarin.Forms.Forms.Init (e);

if (e.PreviousExecutionState == ApplicationExecutionState.Terminated)
{
  ...
}
```

这会将特定于 UWP 的 Xamarin.Forms 实现加载到应用程序。 由启动 Xamarin.Forms 初始页面`MainPage`类：

```csharp
namespace Notes.UWP
{
    public sealed partial class MainPage
    {
        public MainPage()
        {
            this.InitializeComponent();
            this.LoadApplication(new Notes.App());
        }
    }
}
```

可通过 `LoadApplication` 方法加载 Xamarin.Forms 应用程序。

> [!NOTE]
> 通用 Windows 平台应用程序可以使用 Xamarin.Forms 生成，但仅限于在 Windows 上使用 Visual Studio。

::: zone-end

## <a name="user-interface"></a>用户界面

有四个主要控件组，用于创建 Xamarin.Forms 应用程序的用户界面：

1. **页面** - Xamarin.Forms 页呈现跨平台移动应用程序屏幕。 说明应用程序使用[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)类，以显示单个屏幕。 有关页面的详细信息，请参阅 [Xamarin.Forms 页面](~/xamarin-forms/user-interface/controls/pages.md)。
1. **视图** - Xamarin.Forms 视图是显示在用户界面上的控件，如标签、按钮和文本输入框。 完成的说明应用程序使用[ `ListView` ](xref:Xamarin.Forms.ListView)， [ `Editor` ](xref:Xamarin.Forms.Editor)，以及[ `Button` ](xref:Xamarin.Forms.Button)视图。 有关视图的详细信息，请参阅 [Xamarin.Forms 视图](~/xamarin-forms/user-interface/controls/views.md)。
1. **布局** - Xamarin.Forms 布局是用于将视图组合到逻辑结构的容器。 说明应用程序使用[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)类来排列垂直堆栈中的视图和[ `Grid` ](xref:Xamarin.Forms.Grid)类以水平排列按钮。 有关布局的详细信息，请参阅 [Xamarin.Forms 布局](~/xamarin-forms/user-interface/controls/layouts.md)。
1. **单元格** - Xamarin.Forms 单元格是专门用于列表中的项的元素，描述列表中每个项的绘制方式。 说明应用程序使用[ `TextCell` ](xref:Xamarin.Forms.TextCell)在列表中显示的每一行的两个项。 有关单元格的详细信息，请参阅 [Xamarin.Forms 单元格](~/xamarin-forms/user-interface/controls/cells.md)。

在运行时，每个控件都会映射到其本身的本机等效项（即呈现的内容）。

### <a name="layout"></a>布局

说明应用程序使用[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)为了简化跨平台应用程序开发过程自动排列在屏幕上而不考虑屏幕大小的视图。 根据添加顺序，以垂直方式或水平方式逐个放置每个子元素。 `StackLayout` 使用的空间大小取决于 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 和 [`VerticalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 属性的设置方式，但默认情况下，`StackLayout` 尝试使用整个屏幕。

下面的 XAML 代码显示了使用的示例[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)到布局`NoteEntryPage`:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Notes.NoteEntryPage"
             Title="Note Entry">
    ...    
    <StackLayout Margin="{StaticResource PageMargin}">
        <Editor Placeholder="Enter your note"
                Text="{Binding Text}"
                HeightRequest="100" />
        <Grid>
            ...
        </Grid>
    </StackLayout>    
</ContentPage>
```

默认情况下[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)采用垂直方向。 但是，它可以更改为水平方向通过设置[ `StackLayout.Orientation` ](xref:Xamarin.Forms.StackLayout.Orientation)属性设置为[ `StackOrientation.Horizontal` ](xref:Xamarin.Forms.StackOrientation.Horizontal)枚举成员。

> [!NOTE]
> 可以通过设置视图的大小`HeightRequest`和`WidthRequest`属性。

有关 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 类的详细信息，请参阅 [StackLayout](~/xamarin-forms/user-interface/layouts/stack-layout.md)。

### <a name="responding-to-user-interaction"></a>响应用户交互

XAML 中定义的对象可触发在隐藏文件中处理的事件。 下面的代码示例演示`OnSaveButtonClicked`方法中的代码隐藏`NoteEntryPage`类，该类在响应中执行[ `Clicked` ](xref:Xamarin.Forms.Button.Clicked)事件触发*保存*按钮.

```csharp
async void OnSaveButtonClicked(object sender, EventArgs e)
{
    var note = (Note)BindingContext;
    note.Date = DateTime.UtcNow;
    await App.Database.SaveNoteAsync(note);
    await Navigation.PopAsync();
}
```

`OnSaveButtonClicked`方法将便笺保存在数据库中，并导航回前一页。

> [!NOTE]
> XAML 类的代码隐藏文件可使用为其分配的、具有 `x:Name` 属性的名称访问 XAML 中定义的对象。 分配给此属性的值与 C# 变量的规则相同，因为该值必须以字母或下划线开头，且不包含嵌入的空格。

保存绑定到按钮`OnSaveButtonClicked`方法时发生的 XAML 标记中`NoteEntryPage`类：

```xaml
<Button Text="Save"
        Clicked="OnSaveButtonClicked" />
```

### <a name="lists"></a>列表

[ `ListView` ](xref:Xamarin.Forms.ListView)负责垂直列表中显示的项的集合。 中的每项`ListView`都包含在单个单元格。

下面的代码示例演示[ `ListView` ](xref:Xamarin.Forms.ListView)从`NotesPage`:

```xaml
<ListView x:Name="listView"
          Margin="{StaticResource PageMargin}"
          ItemSelected="OnListViewItemSelected">
    <ListView.ItemTemplate>
        <DataTemplate>
            <TextCell Text="{Binding Text}"
                      Detail="{Binding Date}" />
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

中的每一行的布局[ `ListView` ](xref:Xamarin.Forms.ListView)中定义[ `ListView.ItemTemplate` ](xref:Xamarin.Forms.ItemsView`1.ItemTemplate)元素，并使用数据绑定，以显示由应用程序检索的任何备注。 [ `ListView.ItemsSource` ](xref:Xamarin.Forms.ItemsView`1.ItemsSource)属性设置为数据源，在`NotesPage.xaml.cs`:

```csharp
protected override async void OnAppearing()
{
    base.OnAppearing();

    listView.ItemsSource = await App.Database.GetNotesAsync();
}
```    

这段代码填入[ `ListView` ](xref:Xamarin.Forms.ListView)与存储在数据库中的任何备注。

选中一行[ `ListView` ](xref:Xamarin.Forms.ListView)，则[ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected)事件触发。 事件处理程序，名为`OnListViewItemSelected`，激发事件时执行：

```csharp
async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
{
    if (e.SelectedItem != null)
    {
        ...
    }
}
```

[ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected)事件可以访问与通过的单元格相关联的对象[ `e.SelectedItem` ](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem)属性。

有关详细信息[ `ListView` ](xref:Xamarin.Forms.ListView)类，请参阅[ListView](~/xamarin-forms/user-interface/listview/index.md)。

## <a name="navigation"></a>导航

Xamarin.Forms 提供多种不同的页导航体验，具体取决于使用的 [`Page`](xref:Xamarin.Forms.Page) 类型。 有关[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)实例导航可以是分层的或模式。 有关模式导航的信息，请参阅[Xamarin.Forms 模式页面](~/xamarin-forms/app-fundamentals/navigation/modal.md)。

> [!NOTE]
> [`CarouselPage`](xref:Xamarin.Forms.CarouselPage)[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 和 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 类提供替代导航体验。 有关详细信息，请参阅[导航](~/xamarin-forms/app-fundamentals/navigation/index.md)。

在分层导航[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)类用于导航的堆栈[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)对象时，向前和向后，根据需要。 此类将导航实现为 [`Page`](xref:Xamarin.Forms.Page) 对象的后进先出 (LIFO) 堆栈。 若要从一页移动到另一页，应用程序会将新页推送到导航堆栈中，在堆栈中，该页会变为活动页。 若要返回到前一页，应用程序会从导航堆栈弹出当前页，而使最顶层的页成为活动页。

`NavigationPage` 类还会将导航栏添加到页面顶部，此页面显示标题和平台相应的“返回”按钮，通过此按钮可返回上一页。

添加到导航堆栈中的第一页称为*根*页面应用程序，以及下面的代码示例演示如何实现这一点说明应用程序中：

```csharp
public App ()
{
    ...
    MainPage = new NavigationPage (new NotesPage ());
}
```

所有 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 实例都具有 [`Navigation`](xref:Xamarin.Forms.VisualElement.Navigation) 属性，可提供修改页面堆栈的方法。 应仅当应用程序包括 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 时，才调用这些方法。 若要导航到 `NoteEntryPage`，则必须调用 [`PushAsync`](xref:Xamarin.Forms.NavigationPage.PushAsync(Xamarin.Forms.Page)) 方法，如下面的代码示例中所示：

```csharp
await Navigation.PushAsync(new NoteEntryPage());
```

这将导致新`NoteEntryPage`对象推送到导航堆栈中，其中它成为活动页。

通过设备上的返回按钮（无论是设备上的物理按钮还是屏幕按钮），可以从导航堆栈中弹出活动页。 若要以编程方式返回原始页，`NoteEntryPage` 对象必须调用 [`PopAsync`](xref:Xamarin.Forms.NavigationPage.PopAsync) 方法，如下面的代码示例中所示：

```csharp
await Navigation.PopAsync();
```

有关分层导航的详细信息，请参阅[分层导航](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md)。

## <a name="data-binding"></a>数据绑定

数据绑定用于简化 Xamarin.Forms 应用程序显示及其与数据的交互方式。 它将在用户界面和基础应用程序之间建立连接。 [`BindableObject`](xref:Xamarin.Forms.BindableObject) 类包含大部分基础结构以支持数据绑定。

数据绑定连接两个对象，即源和目标。 源对象提供数据。 目标对象使用（并经常显示）来自源对象的数据。 例如， [ `Editor` ](xref:Xamarin.Forms.Editor) (*目标*对象) 通常将绑定其[ `Text` ](xref:Xamarin.Forms.Editor.Text)属性设置为公共`string`中属性*源*对象。 下图说明了这种绑定关系：

![](deepdive-images/data-binding.png "数据绑定")

数据绑定的主要优点是让你无需再担心视图和数据源之间的数据同步。 幕后的绑定框架源会将源对象中的更改自动推送到目标对象，且目标对象中的更改可选择性地推送回源对象。

建立数据绑定是一个两步过程：

- 目标对象的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 属性必须设置为源。
- 必须在目标和源之间建立绑定。 在 XAML 中，此过程可通过使用 [`Binding`](xref:Xamarin.Forms.Xaml.BindingExtension) 标记扩展实现。

在说明应用程序，是绑定目标[ `Editor` ](xref:Xamarin.Forms.Editor)显示一条注释，而`Note`实例设置为[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)的`NoteEntryPage`是绑定源。

`BindingContext`的`NoteEntryPage`在页面导航过程中设置下面的代码示例中所示：

```csharp
async void OnNoteAddedClicked(object sender, EventArgs e)
{
    await Navigation.PushAsync(new NoteEntryPage
    {
        BindingContext = new Note()
    });
}

async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
{
    if (e.SelectedItem != null)
    {
        await Navigation.PushAsync(new NoteEntryPage
        {
            BindingContext = e.SelectedItem as Note
        });
    }
}
```

在中`OnNoteAddedClicked`方法，执行时，新便笺添加到应用程序时， [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)的`NoteEntryPage`设置为一个新`Note`实例。 在`OnListViewItemSelected`方法中选择现有便笺时执行[ `ListView` ](xref:Xamarin.Forms.ListView)，则`BindingContext`的`NoteEntryPage`设置为所选`Note`实例，通过访问[ `e.SelectedItem` ](xref:Xamarin.Forms.SelectedItemChangedEventArgs.SelectedItem)属性。

> [!IMPORTANT]
> 虽然可以分别设置每个目标对象的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 属性，但没有必要。 `BindingContext` 是特殊属性，其所有子级都会继承该属性。 因此，当`BindingContext`上[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)设置为`Note`实例，所有的子级`ContentPage`具有相同`BindingContext`，可以将绑定到公共属性的`Note`对象。

[ `Editor` ](xref:Xamarin.Forms.Editor)中`NoteEntryPage`然后将绑定到`Text`属性`Note`对象：

```xaml
<Editor Placeholder="Enter your note"
        Text="{Binding Text}"
        ... />
```

在 [`Editor.Text`](xref:Xamarin.Forms.Editor.Text) 属性和源 对象的 `Text` 属性之间建立绑定。 中所做的更改`Editor`会自动传播到`Note`对象。 同样，如果对更改`Note.Text`属性，Xamarin.Forms 绑定引擎还将更新的内容`Editor`。 这称为双向绑定。

有关数据绑定的详细信息，请参阅[Xamarin.Forms 数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)。

## <a name="styling"></a>“样式”

Xamarin.Forms 应用程序通常包含多个具有相同的外观的可视元素。 设置每个可视元素的外观会重复且容易出错。 相反，样式可以创建定义的外观，然后应用于所需的可视元素。

[ `Style` ](xref:Xamarin.Forms.Style)类进行分组到一个对象，然后可以应用于多个可视元素实例的属性值的集合。 样式存储在[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)，在应用程序级别、 页面级别或视图级别。 选择定义的位置`Style`可以使用它的影响：

- [`Style`](xref:Xamarin.Forms.Style) 在应用程序级别定义的实例可以应用整个应用程序。
- [`Style`](xref:Xamarin.Forms.Style) 到页，及其子级，则可以应用在页面级别定义的实例。
- [`Style`](xref:Xamarin.Forms.Style) 可以应用于视图级别定义的实例，该视图以及与其子。

> [!IMPORTANT]
> 整个应用程序使用的任何样式存储在应用程序的资源字典以避免重复。 但是，是特定于页面的 XAML 不应包含在应用程序的资源字典，如时所需的页面资源将随后在应用程序启动，而不是可以解析。

每个[ `Style` ](xref:Xamarin.Forms.Style)实例包含一个或多个集合[ `Setter` ](xref:Xamarin.Forms.Setter)对象，每个`Setter`无[ `Property` ](xref:Xamarin.Forms.Setter.Property)和[`Value`](xref:Xamarin.Forms.Setter.Value). `Property`是该样式应用到，该元素的可绑定属性的名称和`Value`是应用于属性的值。 下面的代码示例显示了从样式`NoteEntryPage`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Notes.NoteEntryPage"
             Title="Note Entry">
    <ContentPage.Resources>
        <!-- Implicit styles -->
        <Style TargetType="{x:Type Editor}">
            <Setter Property="BackgroundColor"
                    Value="{StaticResource AppBackgroundColor}" />
        </Style>
        ...
    </ContentPage.Resources>
    ...
</ContentPage>
```

此样式应用到任意[ `Editor` ](xref:Xamarin.Forms.Editor)页实例。

创建时[ `Style` ](xref:Xamarin.Forms.Style)，则[ `TargetType` ](xref:Xamarin.Forms.Style.TargetType)并总是必需的属性。

> [!NOTE]
> 样式设置 Xamarin.Forms 应用程序通常通过使用 XAML 样式。 但是，Xamarin.Forms 还支持使用级联样式表 (CSS) 样式可视元素。 有关详细信息，请参阅[使用级联样式表 (CSS) 样式设置 Xamarin.Forms 应用](~/xamarin-forms/user-interface/styles/css/index.md)。

有关 XAML 样式的详细信息，请参阅[样式设置 Xamarin.Forms 应用使用 XAML 样式](~/xamarin-forms/user-interface/styles/xaml/index.md)。

### <a name="providing-platform-specific-styles"></a>提供特定于平台的样式

`OnPlatform`标记扩展可用于自定义根据每个平台的 UI 外观：

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Notes.App">
    <Application.Resources>
        ...
        <Color x:Key="iOSNavigationBarColor">WhiteSmoke</Color>
        <Color x:Key="AndroidNavigationBarColor">#2196F3</Color>
        <Color x:Key="iOSNavigationBarTextColor">Black</Color>
        <Color x:Key="AndroidNavigationBarTextColor">White</Color>

        <Style TargetType="{x:Type NavigationPage}">
            <Setter Property="BarBackgroundColor"
                    Value="{OnPlatform iOS={StaticResource iOSNavigationBarColor},
                                       Android={StaticResource AndroidNavigationBarColor}}" />
             <Setter Property="BarTextColor"
                    Value="{OnPlatform iOS={StaticResource iOSNavigationBarTextColor},
                                       Android={StaticResource AndroidNavigationBarTextColor}}" />           
        </Style>
        ...
    </Application.Resources>
</Application>
```

这[ `Style` ](xref:Xamarin.Forms.Style)设置不同[ `Color` ](xref:Xamarin.Forms.Color)值[ `BarBackgroundColor` ](xref:Xamarin.Forms.NavigationPage.BarBackgroundColor)和[ `BarTextColor` ](xref:Xamarin.Forms.NavigationPage.BarTextColor)属性[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)，取决于正在使用的平台。

若要深入了解 XAML 标记扩展，请参阅 [XAML 标记扩展](~/xamarin-forms/xaml/markup-extensions/index.md)。 璝惠`OnPlatform`标记扩展，请参阅[OnPlatform 标记扩展](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension)。

## <a name="testing-and-deployment"></a>测试和部署

Visual Studio for Mac 和 Visual Studio 均提供许多用于测试和部署应用程序的选项。 调试应用程序是应用程序开发生命周期的普遍一环，有助于诊断代码问题。 有关详细信息，请参阅[设置断点](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/set_a_breakpoint)、[逐步执行代码](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/step_through_code)和[日志窗口的输出信息](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide/debugging/output_information_to_log_window)。

模拟器是开始部署和测试应用程序的有利位置，其提供用于测试应用程序的有用功能。 但是，用户不会在模拟器中使用最终应用程序，因此应尽早并经常在实际设备上测试应用程序。 有关 iOS 设备预配的详细信息，请参阅[设备预配](~/ios/get-started/installation/device-provisioning/index.md)。 有关 Android 设备预配的详细信息，请参阅[设置设备进行开发](~/android/get-started/installation/set-up-device-for-development.md)。

## <a name="next-steps"></a>后续步骤

此深入了解已检查使用 Xamarin.Forms 开发应用程序的基础知识。 建议的后续步骤包括了解以下功能：

- 可使用 4 个主要控件组创建 Xamarin.Forms 应用程序的用户界面。 有关详细信息，请参阅[控件参考](~/xamarin-forms/user-interface/controls/index.md)。
- 数据绑定是一种链接的两个对象的属性，使一个属性中的更改会自动反映在另一个属性的方法。 有关详细信息，请参阅[数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)。
- Xamarin.Forms 提供了多种不同的页导航体验，取决于所使用的页类型。 有关详细信息，请参阅[导航](~/xamarin-forms/app-fundamentals/navigation/index.md)。
- 样式有助于减少重复性的标记，并允许应用程序外观以更轻松地更改。 有关详细信息，请参阅[样式设置 Xamarin.Forms 应用](~/xamarin-forms/user-interface/styles/index.md)。
- XAML 标记扩展元素特性，若要从文本字符串以外的源设置，从而扩展的功能和灵活性的 XAML。 有关详细信息，请参阅[XAML 标记扩展](~/xamarin-forms/xaml/markup-extensions/index.md)。
- 数据模板提供的功能支持的视图上定义数据的表示形式。 有关详细信息，请参阅[数据模板](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。
- 每个页面、 布局和视图以不同的方式上呈现每个平台使用`Renderer`类，该类又创建本机控件，排列在屏幕上，并将添加共享代码中指定的行为。 开发人员可以实现自定义 `Renderer` 类，以自定义控件的外观和/或行为。 有关详细信息，请参阅[自定义呈现器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)。
- 还可以自定义每个平台上的本机控件的效果。 特定于平台的项目中创建通过子类化效果[ `PlatformEffect` ](xref:Xamarin.Forms.PlatformEffect`2)类，并将其附加到相应的 Xamarin.Forms 控件使用。 有关详细信息，请参阅[效果](~/xamarin-forms/app-fundamentals/effects/index.md)。
- 共享代码可通过 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 类访问本机功能。 有关详细信息，请参阅[通过 DependencyService 访问本机功能](~/xamarin-forms/app-fundamentals/dependency-service/index.md)。

此外，也可以阅读 Charles Petzold 撰写的[使用 Xamarin.Forms 创建移动应用](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)一书，了解有关 Xamarin.Forms 的详细信息。 可获取此书的 PDF 版本或多种电子书格式的版本。

## <a name="related-links"></a>相关链接

- [可扩展应用程序标记语言 (XAML)](~/xamarin-forms/xaml/index.md)
- [数据绑定](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [控件引用](~/xamarin-forms/user-interface/controls/index.md)
- [XAML 标记扩展](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Xamarin.Forms 示例](https://developer.xamarin.com/samples/xamarin-forms/all/)
- [入门示例](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/)
- [Xamarin.Forms API 参考](xref:Xamarin.Forms)
- [免费自学教程（视频）](https://university.xamarin.com/self-guided/)
