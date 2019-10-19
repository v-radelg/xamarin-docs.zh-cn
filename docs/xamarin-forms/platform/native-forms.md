---
title: Xamarin 本机项目中的 xamarin 窗体
description: 本文介绍如何使用直接添加到 Xamarin 本机项目的 ContentPage 派生页面，以及如何在这些页面之间导航。
ms.prod: xamarin
ms.assetid: f343fc21-dfb1-4364-a332-9da6705d36bc
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/19/2019
ms.openlocfilehash: 0c84b844455b8a792b8cbe2f4dac97097e5ebd97
ms.sourcegitcommit: dad4dfcd194b63ec9e903363351b6d9e543d4888
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2019
ms.locfileid: "69621058"
---
# <a name="xamarinforms-in-xamarin-native-projects"></a>Xamarin 本机项目中的 xamarin 窗体

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/native2forms)

通常，Xamarin 窗体应用程序包含一个或多个派生自[`ContentPage`](xref:Xamarin.Forms.ContentPage)的页面，这些页面由 .NET Standard 库项目或共享项目中的所有平台共享。 但是，本机窗体允许将 `ContentPage` 派生页面直接添加到本机 Xamarin iOS、Xamarin 和 UWP 应用程序。 与本机项目 `ContentPage` 从 .NET Standard 库项目或共享项目中派生的页面相比，将页面直接添加到本机项目的优点在于，可以使用本机视图来扩展页面。 然后，可以在 XAML 中将本机视图命名为 `x:Name` 并从代码隐藏中引用。 有关本机视图的详细信息，请参阅[本机视图](~/xamarin-forms/platform/native-views/index.md)。

在本机项目中使用 Xamarin 窗体[`ContentPage`](xref:Xamarin.Forms.ContentPage)派生页的过程如下所示：

1. 将 Xamarin NuGet 包添加到本机项目。
1. 将[`ContentPage`](xref:Xamarin.Forms.ContentPage)派生的页和任何依赖项添加到本机项目。
1. 调用 `Forms.Init` 方法。
1. 使用以下扩展方法之一构造[`ContentPage`](xref:Xamarin.Forms.ContentPage)派生页面的实例，并将其转换为适当的本机类型：适用于 iOS 的 `CreateViewController`、适用于 Android 的 `CreateSupportFragment` 或 UWP 的 `CreateFrameworkElement`。
1. 使用本机导航 API 定位到[`ContentPage`](xref:Xamarin.Forms.ContentPage)派生页面的本机类型表示形式。

在本机项目可以构造[`ContentPage`](xref:Xamarin.Forms.ContentPage)派生页面之前，必须通过调用 `Forms.Init` 方法来初始化 Forms。 选择执行此操作的时间主要取决于在应用程序流中最便利的时间，可以在应用程序启动时执行，也可以在构造 `ContentPage` 派生页面之前执行。 在本文和随附的示例应用程序中，将在应用程序启动时调用 `Forms.Init` 方法。

> [!NOTE]
> **NativeForms**示例应用程序解决方案不包含任何 Xamarin 项目。 相反，它包括 Xamarin iOS 项目、Xamarin Android 项目和 UWP 项目。 每个项目都是使用本机窗体来使用[`ContentPage`](xref:Xamarin.Forms.ContentPage)派生页的本机项目。 但是，本机项目无法使用 .NET Standard 库项目或共享项目中 `ContentPage` 派生页面的原因。

使用本机窗体时，Xamarin. [`DependencyService`](xref:Xamarin.Forms.DependencyService)、 [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter)和数据绑定引擎等窗体功能都仍然有效。 但是，必须使用本机导航 API 执行页面导航。

## <a name="ios"></a>iOS

在 iOS 上，在 `AppDelegate` 类中的 `FinishedLaunching` 重写通常是执行与应用程序启动相关的任务的位置。 它在应用程序启动后调用，通常会被重写以配置主窗口和视图控制器。 下面的代码示例演示示例应用程序中的 `AppDelegate` 类：

```csharp
[Register("AppDelegate")]
public class AppDelegate : UIApplicationDelegate
{
    public static AppDelegate Instance;
    UIWindow _window;
    AppNavigationController _navigation;

    public static string FolderPath { get; private set; }

    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        Forms.Init();

        Instance = this;
        _window = new UIWindow(UIScreen.MainScreen.Bounds);

        UINavigationBar.Appearance.SetTitleTextAttributes(new UITextAttributes
        {
            TextColor = UIColor.Black
        });

        FolderPath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData));
        UIViewController mainPage = new NotesPage().CreateViewController();
        mainPage.Title = "Notes";

        _navigation = new AppNavigationController(mainPage);
        _window.RootViewController = _navigation;
        _window.MakeKeyAndVisible();

        return true;
    }
    // ...
}
```

@No__t_0 方法将执行以下任务：

- Xamarin. 通过调用 `Forms.Init` 方法初始化窗体。
- 对 `AppDelegate` 类的引用存储在 "`static` `Instance`" 字段中。 这是为了为其他类提供一种机制，用于调用 `AppDelegate` 类中定义的方法。
- 创建 `UIWindow`，它是用于本机 iOS 应用程序中视图的主要容器。
- @No__t_0 属性被初始化为设备上存储注释数据的路径。
- @No__t_0 类是在 XAML 中定义的 Xamarin [`ContentPage`](xref:Xamarin.Forms.ContentPage)派生页面，使用 `CreateViewController` 扩展方法构造和转换为 `UIViewController`。
- 设置 `UIViewController` 的 `Title` 属性，该属性将显示在 `UINavigationBar` 上。
- 创建用于管理分层导航的 `AppNavigationController`。 这是一个自定义导航控制器类，派生自 `UINavigationController`。 @No__t_0 对象管理视图控制器的堆栈，并且传递到构造函数的 `UIViewController` 最初在 `AppNavigationController` 加载时呈现。
- 将 `AppNavigationController` 对象设置为 `UIWindow` 的顶级 `UIViewController`，并将 `UIWindow` 设置为应用程序的键窗口，并使其可见。

执行 `FinishedLaunching` 方法后，将显示 Xamarin `NotesPage` 类中定义的 UI，如以下屏幕截图所示：

[![使用 XAML 中定义的 UI 的 Xamarin iOS 应用程序的屏幕截图](native-forms-images/ios-notespage.png "使用 XAML UI 的 Xamarin iOS 应用")](native-forms-images/ios-notespage-large.png#lightbox "使用 XAML UI 的 Xamarin iOS 应用")

与 UI 交互的方式（例如，通过在 **+** " [`Button`](xref:Xamarin.Forms.Button)上点击，将导致 `NotesPage` 代码隐藏中执行以下事件处理程序：

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    AppDelegate.Instance.NavigateToNoteEntryPage(new Note());
}
```

"@No__t_0 `AppDelegate.Instance`" 字段允许调用 `AppDelegate.NavigateToNoteEntryPage` 方法，如以下代码示例所示：

```csharp
public void NavigateToNoteEntryPage(Note note)
{
    var noteEntryPage = new NoteEntryPage
    {
        BindingContext = note
    }.CreateViewController();
    noteEntryPage.Title = "Note Entry";
    _navigation.PushViewController(noteEntryPage, true);
}
```

@No__t_0 方法使用 `CreateViewController` 扩展方法将 Xamarin [`ContentPage`](xref:Xamarin.Forms.ContentPage)派生的页转换为 `UIViewController`，并设置 `Title` 的 `UIViewController` 属性。 然后，`PushViewController` 方法将 `UIViewController` 推送到 `AppNavigationController` 中。 因此，会显示 Xamarin. Forms `NoteEntryPage` 类中定义的 UI，如以下屏幕截图所示：

[![使用 XAML 中定义的 UI 的 Xamarin iOS 应用程序的屏幕截图](native-forms-images/ios-noteentrypage.png "使用 XAML UI 的 Xamarin iOS 应用")](native-forms-images/ios-noteentrypage-large.png#lightbox "使用 XAML UI 的 Xamarin iOS 应用")

显示 `NoteEntryPage` 时，后退导航将从 `AppNavigationController` 弹出 `NoteEntryPage` 类的 `UIViewController`，并将用户返回到 `UIViewController` 类的 `NotesPage`。 但是，从 iOS 本机导航堆栈中弹出 `UIViewController` 不会自动释放 `UIViewController` 并附加 `Page` 对象。 因此，`AppNavigationController` 类将重写 `PopViewController` 方法，以释放反向导航中的视图控制器：

```csharp
public class AppNavigationController : UINavigationController
{
    //...
    public override UIViewController PopViewController(bool animated)
    {
        UIViewController topView = TopViewController;
        if (topView != null)
        {
            // Dispose of ViewController on back navigation.
            topView.Dispose();
        }
        return base.PopViewController(animated);
    }
}
```

@No__t_0 重写对从 iOS 本机导航堆栈中弹出的 `UIViewController` 对象调用 `Dispose` 方法。 如果未执行此操作，则会导致 `UIViewController` 并使附加 `Page` 对象变得孤立。

> [!IMPORTANT]
> 不能对孤立对象进行垃圾回收，因此会导致内存泄露。

## <a name="android"></a>Android

在 Android 上，`MainActivity` 类中的 `OnCreate` 重写通常是执行与应用程序启动相关的任务的位置。 下面的代码示例演示示例应用程序中的 `MainActivity` 类：

```csharp
public class MainActivity : AppCompatActivity
{
    public static string FolderPath { get; private set; }

    public static MainActivity Instance;

    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);

        Forms.Init(this, bundle);
        Instance = this;

        SetContentView(Resource.Layout.Main);
        var toolbar = FindViewById<Toolbar>(Resource.Id.toolbar);
        SetSupportActionBar(toolbar);
        SupportActionBar.Title = "Notes";

        FolderPath = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.LocalApplicationData));
        Android.Support.V4.App.Fragment mainPage = new NotesPage().CreateSupportFragment(this);
        SupportFragmentManager
            .BeginTransaction()
            .Replace(Resource.Id.fragment_frame_layout, mainPage)
            .Commit();
        ...
    }
    ...
}
```

@No__t_0 方法将执行以下任务：

- Xamarin. 通过调用 `Forms.Init` 方法初始化窗体。
- 对 `MainActivity` 类的引用存储在 "`static` `Instance`" 字段中。 这是为了为其他类提供一种机制，用于调用 `MainActivity` 类中定义的方法。
- @No__t_0 内容是从布局资源设置的。 在示例应用程序中，布局包含一个包含 `Toolbar` 的 `LinearLayout` 和一个用作片段容器的 `FrameLayout`。
- 检索 `Toolbar`，并将其设置为 `Activity` 的操作栏，并设置操作栏标题。
- @No__t_0 属性被初始化为设备上存储注释数据的路径。
- @No__t_0 类是在 XAML 中定义的 Xamarin [`ContentPage`](xref:Xamarin.Forms.ContentPage)派生页面，使用 `CreateSupportFragment` 扩展方法构造和转换为 `Fragment`。
- @No__t_0 类创建并提交一个事务，该事务将 `FrameLayout` 实例替换为 `NotesPage` 类的 `Fragment`。

有关片段的详细信息，请参阅[片段](~/android/platform/fragments/index.md)。

执行 `OnCreate` 方法后，将显示 Xamarin `NotesPage` 类中定义的 UI，如以下屏幕截图所示：

[![使用 XAML 中定义的 UI 的 Xamarin Android 应用程序的屏幕截图](native-forms-images/android-notespage.png "使用 XAML UI 的 Xamarin Android 应用")](native-forms-images/android-notespage-large.png#lightbox "使用 XAML UI 的 Xamarin Android 应用")

与 UI 交互的方式（例如，通过在 **+** " [`Button`](xref:Xamarin.Forms.Button)上点击，将导致 `NotesPage` 代码隐藏中执行以下事件处理程序：

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    MainActivity.Instance.NavigateToNoteEntryPage(new Note());
}
```

"@No__t_0 `MainActivity.Instance`" 字段允许调用 `MainActivity.NavigateToNoteEntryPage` 方法，如以下代码示例所示：

```csharp
public void NavigateToNoteEntryPage(Note note)
{
    Android.Support.V4.App.Fragment noteEntryPage = new NoteEntryPage
    {
        BindingContext = note
    }.CreateSupportFragment(this);
    SupportFragmentManager
        .BeginTransaction()
        .AddToBackStack(null)
        .Replace(Resource.Id.fragment_frame_layout, noteEntryPage)
        .Commit();
}
```

@No__t_0 方法使用 `CreateSupportFragment` 扩展方法将 Xamarin [`ContentPage`](xref:Xamarin.Forms.ContentPage)派生的页转换为 `Fragment`，并将该 `Fragment` 添加到片段的堆栈中。 因此，将显示 Xamarin `NoteEntryPage` 中定义的 UI，如以下屏幕截图所示：

[![使用 XAML 中定义的 UI 的 Xamarin Android 应用程序的屏幕截图](native-forms-images/android-noteentrypage.png "使用 XAML UI 的 Xamarin Android 应用")](native-forms-images/android-noteentrypage-large.png#lightbox "使用 XAML UI 的 Xamarin Android 应用")

显示 `NoteEntryPage` 时，点击后退箭头将从片段向后堆栈中弹出 `NoteEntryPage` 的 `Fragment`，并将用户返回到 `NotesPage` 类的 `Fragment` 中。

### <a name="enable-back-navigation-support"></a>启用后退导航支持

@No__t_0 类有一个 `BackStackChanged` 事件，每当片段的内容发生更改时，将触发该事件。 @No__t_1 类中的 `OnCreate` 方法包含此事件的匿名事件处理程序：

```csharp
SupportFragmentManager.BackStackChanged += (sender, e) =>
{
    bool hasBack = SupportFragmentManager.BackStackEntryCount > 0;
    SupportActionBar.SetHomeButtonEnabled(hasBack);
    SupportActionBar.SetDisplayHomeAsUpEnabled(hasBack);
    SupportActionBar.Title = hasBack ? "Note Entry" : "Notes";
};
```

此事件处理程序在操作栏上显示一个 "后退" 按钮，前提是该片段上有一个或多个 `Fragment` 实例。 通过 `OnOptionsItemSelected` 替代来处理点击 "后退" 按钮的响应：

```csharp
public override bool OnOptionsItemSelected(Android.Views.IMenuItem item)
{
    if (item.ItemId == global::Android.Resource.Id.Home && SupportFragmentManager.BackStackEntryCount > 0)
    {
        SupportFragmentManager.PopBackStack();
        return true;
    }
    return base.OnOptionsItemSelected(item);
}
```

只要选择 "选项" 菜单中的项，就会调用 `OnOptionsItemSelected` 重写。 如果已选择 "后退" 按钮，并且片段反向堆栈上有一个或多个 `Fragment` 实例，则此实现会从片段返回堆栈中弹出当前片段。

### <a name="multiple-activities"></a>多个活动

当应用程序由多个活动组成时，可以将[`ContentPage`](xref:Xamarin.Forms.ContentPage)派生的页面嵌入到每个活动中。 在这种情况下，只需在嵌入 Xamarin. Forms `ContentPage` 的第一个 `Activity` 的 `OnCreate` 重写中调用 `Forms.Init` 方法。 但是，这会产生以下影响：

- 将从调用 `Forms.Init` 方法的 `Activity` 中获取 `Xamarin.Forms.Color.Accent` 的值。
- @No__t_0 的值将与调用 `Forms.Init` 方法的 `Activity` 相关联。

### <a name="choose-a-file"></a>选择文件

在嵌入使用需要支持 HTML "选择文件" 按钮的[`WebView`](xref:Xamarin.Forms.WebView) [`ContentPage`](xref:Xamarin.Forms.ContentPage)派生页面时，`Activity` 将需要重写 `OnActivityResult` 方法：

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    ActivityResultCallbackRegistry.InvokeCallback(requestCode, resultCode, data);
}
```

## <a name="uwp"></a>UWP

在 UWP 上，本机 `App` 类通常用于执行与应用程序启动相关的任务。 Xamarin. 窗体通常在 Xamarin. 窗体 UWP 应用程序中进行初始化，在本机 `App` 类的 `OnLaunched` 重写中，用于将 `LaunchActivatedEventArgs` 参数传递到 `Forms.Init` 方法。 出于此原因，使用[`ContentPage`](xref:Xamarin.Forms.ContentPage)Xamarin 的本机 UWP 应用程序可以很容易地从 `App.OnLaunched` 方法调用 `Forms.Init` 方法。

默认情况下，本机 `App` 类将 `MainPage` 类作为应用程序的第一页启动。 下面的代码示例演示示例应用程序中的 `MainPage` 类：

```csharp
public sealed partial class MainPage : Page
{
    public static MainPage Instance;

    public static string FolderPath { get; private set; }

    public MainPage()
    {
        this.InitializeComponent();
        this.NavigationCacheMode = NavigationCacheMode.Enabled;
        Instance = this;
        FolderPath = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.LocalApplicationData));
        this.Content = new Notes.UWP.Views.NotesPage().CreateFrameworkElement();
    }
    ...
}
```

@No__t_0 构造函数执行以下任务：

- 为页面启用了缓存，以便在用户向后导航到页面时不会构造新的 `MainPage`。
- 对 `MainPage` 类的引用存储在 "`static` `Instance`" 字段中。 这是为了为其他类提供一种机制，用于调用 `MainPage` 类中定义的方法。
- @No__t_0 属性被初始化为设备上存储注释数据的路径。
- @No__t_0 类是在 XAML 中定义的 Xamarin [`ContentPage`](xref:Xamarin.Forms.ContentPage)派生页面，使用 `CreateFrameworkElement` 扩展方法构造和转换为 `FrameworkElement`，然后将其设置为 `MainPage` 类的内容。

执行 `MainPage` 构造函数后，将显示 Xamarin `NotesPage` 类中定义的 UI，如以下屏幕截图所示：

[![使用用 Xamarin 定义的 UI 的 UWP 应用程序的屏幕截图](native-forms-images/uwp-notespage.png "具有 Xamarin. Forms XAML UI 的 UWP 应用")](native-forms-images/uwp-notespage-large.png#lightbox "具有 Xamarin. Forms XAML UI 的 UWP 应用")

与 UI 交互的方式（例如，通过在 **+** " [`Button`](xref:Xamarin.Forms.Button)上点击，将导致 `NotesPage` 代码隐藏中执行以下事件处理程序：

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    MainPage.Instance.NavigateToNoteEntryPage(new Note());
}
```

"@No__t_0 `MainPage.Instance`" 字段允许调用 `MainPage.NavigateToNoteEntryPage` 方法，如以下代码示例所示：

```csharp
public void NavigateToNoteEntryPage(Note note)
{
    this.Frame.Navigate(new NoteEntryPage
    {
        BindingContext = note
    });
}
```

UWP 中的导航通常使用 `Frame.Navigate` 方法执行，该方法采用 `Page` 参数。 Xamarin. Forms 定义了一个采用[`ContentPage`](xref:Xamarin.Forms.ContentPage)派生页面实例的 `Frame.Navigate` 扩展方法。 因此，当 `NavigateToNoteEntryPage` 方法执行时，将显示 Xamarin `NoteEntryPage` 中定义的 UI，如以下屏幕截图所示：

[![使用用 Xamarin 定义的 UI 的 UWP 应用程序的屏幕截图](native-forms-images/uwp-noteentrypage.png "具有 Xamarin. Forms XAML UI 的 UWP 应用")](native-forms-images/uwp-noteentrypage-large.png#lightbox "具有 Xamarin. Forms XAML UI 的 UWP 应用")

显示 `NoteEntryPage` 时，点击后退箭头将从应用程序后端堆栈中弹出 `NoteEntryPage` 的 `FrameworkElement`，并将用户返回到 `NotesPage` 类的 `FrameworkElement` 中。

### <a name="enable-back-navigation-support"></a>启用后退导航支持

在 UWP 上，应用程序必须在不同的设备外观上为所有硬件和软件后退按钮启用后退导航。 这可以通过注册 `BackRequested` 事件的事件处理程序来完成，该事件可在本机 `App` 类的 `OnLaunched` 方法中执行：

```csharp
protected override void OnLaunched(LaunchActivatedEventArgs e)
{
    Frame rootFrame = Window.Current.Content as Frame;

    if (rootFrame == null)
    {
        ...      
        // Place the frame in the current Window
        Window.Current.Content = rootFrame;

        SystemNavigationManager.GetForCurrentView().BackRequested += OnBackRequested;
    }
    ...
}
```

当应用程序启动时，`GetForCurrentView` 方法将检索与当前视图关联的 `SystemNavigationManager` 对象，然后为 `BackRequested` 事件注册事件处理程序。 仅当应用程序为前台应用程序时，应用程序才会收到此事件，并在响应中调用 `OnBackRequested` 事件处理程序：

```csharp
void OnBackRequested(object sender, BackRequestedEventArgs e)
{
    Frame rootFrame = Window.Current.Content as Frame;
    if (rootFrame.CanGoBack)
    {
        e.Handled = true;
        rootFrame.GoBack();
    }
}
```

@No__t_0 事件处理程序对应用程序的根框架调用 `GoBack` 方法，并将 `BackRequestedEventArgs.Handled` 属性设置为 `true` 以将事件标记为已处理。 如果无法将事件标记为 "已处理"，则可能导致事件被忽略。

该应用程序选择是否在标题栏上显示 "后退" 按钮。 为此，可将 `AppViewBackButtonVisibility` 属性设置为 `AppViewBackButtonVisibility` 枚举值之一：

```csharp
void OnNavigated(object sender, NavigationEventArgs e)
{
    SystemNavigationManager.GetForCurrentView().AppViewBackButtonVisibility =
        ((Frame)sender).CanGoBack ? AppViewBackButtonVisibility.Visible : AppViewBackButtonVisibility.Collapsed;
}
```

在发生页面导航时，为响应 `Navigated` 事件激发而执行的 `OnNavigated` 事件处理程序将更新标题栏后退按钮的可见性。 这可确保在应用程序后端堆栈不为空时，标题栏的 "后退" 按钮可见，或在应用内返回堆栈为空的情况下从标题栏中删除。

有关 UWP 的后退导航支持的详细信息，请参阅[uwp 应用的导航历史记录和向后导航](/windows/uwp/design/basics/navigation-history-and-backwards-navigation/)。

## <a name="related-links"></a>相关链接

- [NativeForms （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/native2forms)
- [本机视图](~/xamarin-forms/platform/native-views/index.md)
