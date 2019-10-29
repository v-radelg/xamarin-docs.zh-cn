---
title: 在 Xamarin 中的代码中创建 iOS 用户界面
description: 本文档介绍了如何使用代码构建 Xamarin iOS 应用的用户界面。 其中讨论了视图控制器、构建视图层次结构、处理旋转等。
ms.prod: xamarin
ms.assetid: 7CB1FEAE-0BB3-4CDC-9076-5BD555003F1D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/03/2018
ms.openlocfilehash: 42a2694239fdd55efa91b7fa30be8a10cafb4cc5
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73010326"
---
# <a name="creating-ios-user-interfaces-in-code-in-xamarinios"></a>在 Xamarin 中的代码中创建 iOS 用户界面

IOS 应用程序的用户界面类似于店面–应用程序通常会获得一个窗口，但它可以使用所需的任意数量的对象填充窗口，并且可以根据应用程序要显示的内容更改对象和排列方式。 此情形中的对象（用户看到的内容）称为视图。 若要在应用程序中生成单个屏幕，视图在内容视图层次结构中彼此层叠，而层次结构由单一视图控制器管理。 具有多个屏幕的应用程序具有多个内容视图层次结构（各自具有自己的视图控制器），应用程序会将视图置于窗口中以基于用户所处的屏幕创建不同的内容视图层次结构。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

下图显示了窗口、视图、子视图与视图控制器之间的关系，它们向设备屏幕提供了用户界面：

[![](ios-code-only-images/image9.png "This diagram illustrates the relationships between the Window, Views, Subviews, and View Controller")](ios-code-only-images/image9.png#lightbox)

可以使用 Visual Studio 中的[Xamarin Designer for iOS](~/ios/user-interface/designer/index.md)来构造这些视图层次结构，但最好是了解如何完全使用代码。 本文介绍了一些基本要点，以便启动并运行仅限代码的用户界面开发。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

下图显示了窗口、视图、子视图与视图控制器之间的关系，它们向设备屏幕提供了用户界面：

[![](ios-code-only-images/image9.png "This diagram illustrates the relationships between the Window, Views, Subviews, and View Controller")](ios-code-only-images/image9.png#lightbox)

可以使用 Visual Studio for Mac 中的[Xamarin Designer for iOS](~/ios/user-interface/designer/index.md)来构造这些视图层次结构，但最好是了解如何完全使用代码。 本文介绍了一些基本要点，以便启动并运行仅限代码的用户界面开发。

-----

## <a name="creating-a-code-only-project"></a>创建仅限代码的项目

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="ios-blank-project-template"></a>iOS 空白项目模板

首先，在 Visual Studio 中创建一个 iOS 项目，使用**文件 > 新项目 > C# Visual > iPhone & IPad > IOS 应用（Xamarin）** 项目，如下所示：

[!["新建项目" 对话框](ios-code-only-images/blankapp.w157-sml.png)](ios-code-only-images/blankapp.w157.png#lightbox)

然后选择 "**空白应用程序**" 项目模板：

[!["选择模板" 对话框](ios-code-only-images/blankapp-2.w157-sml.png)](ios-code-only-images/blankapp-2.w157.png#lightbox)

空项目模板将4个文件添加到项目中：

[![项目文件](ios-code-only-images/empty-project.w157-sml.png "项目文件")](ios-code-only-images/empty-project.w157.png#lightbox)

1. **AppDelegate.cs** -包含一个 `UIApplicationDelegate` 子类 `AppDelegate`，该子类用于处理来自 iOS 的应用程序事件。 应用程序窗口是在 `AppDelegate`的 `FinishedLaunching` 方法中创建的。
1. **Main.cs** -包含应用程序的入口点，用于指定 `AppDelegate` 的类。
1. **Info.plist** -包含应用程序配置信息的属性列表文件。
1. **Info.plist** –属性列表文件，其中包含有关应用程序的功能和权限的信息。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

## <a name="ios-templates"></a>iOS 模板

Visual Studio for Mac 不提供空模板。 所有模板都附带了情节提要支持，Apple 建议使用这种方法创建 UI。 但是，可以完全通过代码创建 UI。

以下步骤将指导你完成从应用程序中删除情节提要的步骤：

1. 使用 "单一视图应用程序" 模板创建新的 iOS 项目：

    [![](ios-code-only-images/single-view-app.png "Use the Single View App template")](ios-code-only-images/single-view-app.png#lightbox)

1. 删除 `Main.Storyboard` 和 `ViewController.cs` 文件。 请勿**删除 `LaunchScreen.Storyboard`** 。 应删除视图控制器，因为它是在情节提要中创建的视图控制器的隐藏代码：
1. 请确保从弹出对话框中选择 "**删除**"：

    [![](ios-code-only-images/delete.png "Select Delete from the pop-up dialog")](ios-code-only-images/delete.png#lightbox)

1. 在 info.plist 中，删除 "**部署信息" > Main Interface** "选项中的信息：

    [![](ios-code-only-images/main-interface.png "Delete the information inside the Main Interface option")](ios-code-only-images/main-interface.png#lightbox)

1. 最后，将以下代码添加到 AppDelegate 类中的 `FinishedLaunching` 方法：

    ```csharp
    public override bool FinishedLaunching(UIApplication app, NSDictionary options)
    {
        // create a new window instance based on the screen size
        Window = new UIWindow(UIScreen.MainScreen.Bounds);

        // make the window visible
        Window.MakeKeyAndVisible();

        return true;
    }
    ```

在上面的步骤5中添加到 `FinishedLaunching` 方法的代码是为你的 iOS 应用程序创建窗口所需的最小代码量。

-----

iOS 应用程序是使用[MVC 模式](~/ios/get-started/hello-ios-multiscreen/hello-ios-multiscreen-deepdive.md#model-view-controller-mvc)生成的。 应用程序显示的第一个屏幕是从窗口的根视图控制器创建的。 有关 MVC 模式本身的详细信息，请参阅[Hello，IOS 多屏显示](~/ios/get-started/hello-ios-multiscreen/index.md)指南。

模板添加的 `AppDelegate` 的实现将创建应用程序窗口，其中每个 iOS 应用程序都只有一个应用程序窗口，并使其与以下代码一起显示：

```csharp
public class AppDelegate : UIApplicationDelegate
{
    public override UIWindow Window
            {
                get;
                set;
            }

    public override bool FinishedLaunching(UIApplication app, NSDictionary options)
    {
        // create a new window instance based on the screen size
        Window = new UIWindow(UIScreen.MainScreen.Bounds);

        // make the window visible
        Window.MakeKeyAndVisible();

        return true;
    }
}
```

如果你现在要运行此应用程序，则可能会遇到引发的异常，指出 `Application windows are expected to have a root view controller at the end of application launch`。 让我们添加一个控制器并将其设置为应用的根视图控制器。

## <a name="adding-a-controller"></a>添加控制器

您的应用程序可以包含多个视图控制器，但它需要一个根视图控制器来控制所有视图控制器。  通过创建 `UIViewController` 实例并将其设置为 `Window.RootViewController` 属性，将控制器添加到窗口：

```csharp
public class AppDelegate : UIApplicationDelegate
{
    // class-level declarations

    public override UIWindow Window
    {
        get;
        set;
    }

    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        // create a new window instance based on the screen size
        Window = new UIWindow(UIScreen.MainScreen.Bounds);

        var controller = new UIViewController();
        controller.View.BackgroundColor = UIColor.LightGray;

        Window.RootViewController = controller;

        // make the window visible
        Window.MakeKeyAndVisible();

        return true;
    }
}
```

每个控制器都具有关联的视图，该视图可从 `View` 属性访问。 上面的代码将视图的 `BackgroundColor` 属性更改为 "`UIColor.LightGray`，以便它可见，如下所示：

 [![](ios-code-only-images/image1.png "The View's background is a visible light gray")](ios-code-only-images/image1.png#lightbox)

我们也可以采用这种方式将任何 `UIViewController` 子类设置为 `RootViewController`，包括 UIKit 中的控制器以及我们自己编写的控制器。 例如，下面的代码添加了一个 `UINavigationController` 作为 `RootViewController`：

```csharp
public class AppDelegate : UIApplicationDelegate
{
    // class-level declarations

    public override UIWindow Window
    {
        get;
        set;
    }

    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        // create a new window instance based on the screen size
        Window = new UIWindow(UIScreen.MainScreen.Bounds);

        var controller = new UIViewController();
        controller.View.BackgroundColor = UIColor.LightGray;
        controller.Title = "My Controller";

        var navController = new UINavigationController(controller);

        Window.RootViewController = navController;

        // make the window visible
        Window.MakeKeyAndVisible();

        return true;
    }
}
```

这会生成嵌套在导航控制器中的控制器，如下所示：

 [![](ios-code-only-images/image2.png "The controller nested within the navigation controller")](ios-code-only-images/image2.png#lightbox)

## <a name="creating-a-view-controller"></a>创建视图控制器

现在，我们已了解如何将控制器添加为窗口的 `RootViewController`，接下来让我们看看如何在代码中创建自定义视图控制器。

添加一个名为 `CustomViewController` 的新类，如下所示：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](ios-code-only-images/customviewcontroller.w157-sml.png "Add a new class named CustomViewController")](ios-code-only-images/customviewcontroller.w157.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](ios-code-only-images/new-file.png "Add a new class named CustomViewController")](ios-code-only-images/new-file.png#lightbox)

-----

类应从 `UIKit` 命名空间中的 `UIViewController`继承，如下所示：

```csharp
using System;
using UIKit;

namespace CodeOnlyDemo
{
    class CustomViewController : UIViewController
    {
    }
}
```

## <a name="initializing-the-view"></a>初始化视图

`UIViewController` 包含一个名为 `ViewDidLoad` 的方法，当视图控制器首次加载到内存中时将调用此方法。 这是对视图进行初始化的适当位置，如设置其属性。

例如，下面的代码将添加一个按钮和一个事件处理程序，以便在按下按钮时将新视图控制器推送到导航堆栈上：

```csharp
using System;
using CoreGraphics;
using UIKit;

namespace CodyOnlyDemo
{
    public class CustomViewController : UIViewController
    {
        public CustomViewController ()
        {
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            View.BackgroundColor = UIColor.White;
            Title = "My Custom View Controller";

            var btn = UIButton.FromType (UIButtonType.System);
            btn.Frame = new CGRect (20, 200, 280, 44);
            btn.SetTitle ("Click Me", UIControlState.Normal);

            var user = new UIViewController ();
            user.View.BackgroundColor = UIColor.Magenta;

            btn.TouchUpInside += (sender, e) => {
                this.NavigationController.PushViewController (user, true);
            };

            View.AddSubview (btn);

        }
    }
}
```

若要在应用程序中加载此控制器并演示简单导航，请创建 `CustomViewController`的新实例。 创建一个新的导航控制器，传入视图控制器实例，并将新的导航控制器设置为 `AppDelegate` 中的窗口 `RootViewController`，如下所示：

```csharp
var cvc = new CustomViewController ();

var navController = new UINavigationController (cvc);

Window.RootViewController = navController;
```

现在，当应用程序加载时，`CustomViewController` 会加载到导航控制器中：

 [![](ios-code-only-images/customvc.png "The CustomViewController is loaded inside a navigation controller")](ios-code-only-images/customvc.png#lightbox)

单击该按钮将新视图控制器_推送_到导航堆栈：

[![](ios-code-only-images/customvca.png "A new View Controller pushed onto the navigation stack")](ios-code-only-images/customvca.png#lightbox)

## <a name="building-the-view-hierarchy"></a>构建视图层次结构

在上面的示例中，我们开始通过向视图控制器添加一个按钮，在代码中创建用户界面。

iOS 用户界面由视图层次结构组成。 其他视图（如标签、按钮、滑块等）作为某些父视图的子视图添加。

例如，让我们编辑 `CustomViewController`，以创建登录屏幕，用户可以在其中输入用户名和密码。 屏幕将包含两个文本字段和一个按钮。

### <a name="adding-the-text-fields"></a>添加文本字段

首先，删除在 "[初始化视图](#initializing-the-view)" 部分中添加的按钮和事件处理程序。 

通过创建和初始化 `UITextField` 然后将其添加到视图层次结构中，为用户名添加一个控件，如下所示：

```csharp
class CustomViewController : UIViewController
{
    UITextField usernameField;

    public override void ViewDidLoad()
    {
        base.ViewDidLoad();

        View.BackgroundColor = UIColor.Gray;

        nfloat h = 31.0f;
        nfloat w = View.Bounds.Width;

        usernameField = new UITextField
        {
            Placeholder = "Enter your username",
            BorderStyle = UITextBorderStyle.RoundedRect,
            Frame = new CGRect(10, 82, w - 20, h)
        };

        View.AddSubview(usernameField);
    }
}
```

创建 `UITextField`时，将设置 `Frame` 属性以定义其位置和大小。 在 iOS 中，0，0坐标位于左上角，+ x 向右，+ y 向下。 将 `Frame` 与另外几个属性一起设置后，调用 `View.AddSubview` 将 `UITextField` 添加到视图层次结构中。 这会使 `usernameField` `View` 属性引用的 `UIView` 实例的子视图。 使用比其父视图更高的 z 顺序添加子视图，因此它显示在屏幕上的父视图之前。

包含 `UITextField` 的应用程序如下所示：

 [![](ios-code-only-images/image4.png "The application with the UITextField included")](ios-code-only-images/image4.png#lightbox)

可以采用类似的方式为密码添加 `UITextField`，这种情况下，只会将 `SecureTextEntry` 属性设置为 true，如下所示：

```csharp
public class CustomViewController : UIViewController
{
    UITextField usernameField, passwordField;
    public override void ViewDidLoad()
    {
       // keep the code the username UITextField
        passwordField = new UITextField
        {
            Placeholder = "Enter your password",
            BorderStyle = UITextBorderStyle.RoundedRect,
            Frame = new CGRect(10, 114, w - 20, h),
            SecureTextEntry = true
        };

      View.AddSubview(usernameField);
      View.AddSubview(passwordField);
   }
}

```

设置 `SecureTextEntry = true` 将隐藏用户在 `UITextField` 中输入的文本，如下所示：

 [![](ios-code-only-images/image4a.png "Setting SecureTextEntry true hides the text entered by the user")](ios-code-only-images/image4a.png#lightbox)

### <a name="adding-the-button"></a>添加按钮

接下来，我们将添加一个按钮，以便用户可以提交用户名和密码。 该按钮将添加到视图层次结构中，就像任何其他控件一样，方法是将其作为自变量传递给父视图的 `AddSubview` 方法。

下面的代码将添加按钮并注册 `TouchUpInside` 事件的事件处理程序：

```csharp
var submitButton = UIButton.FromType (UIButtonType.RoundedRect);

submitButton.Frame = new CGRect (10, 170, w - 20, 44);
submitButton.SetTitle ("Submit", UIControlState.Normal);

submitButton.TouchUpInside += (sender, e) => {
    Console.WriteLine ("Submit button pressed");
};

View.AddSubview(submitButton);
```

这样，登录屏幕就会如下所示：

 [![](ios-code-only-images/image5.png "The login screen")](ios-code-only-images/image5.png#lightbox)

不同于以前版本的 iOS，默认按钮背景是透明的。 更改按钮的 `BackgroundColor` 属性将更改：

```csharp
submitButton.BackgroundColor = UIColor.White;
```

这将导致方形按钮而不是典型的圆角边缘按钮。 若要获取舍入边缘，请使用以下代码片段：

```csharp
submitButton.Layer.CornerRadius = 5f;
```

进行这些更改后，视图将如下所示：

[![](ios-code-only-images/image6.png "An example run of the view")](ios-code-only-images/image6.png#lightbox)

## <a name="adding-multiple-views-to-the-view-hierarchy"></a>向视图层次结构添加多个视图

iOS 提供一项功能，可通过使用 `AddSubviews`向视图层次结构添加多个视图。

```csharp
View.AddSubviews(new UIView[] { usernameField, passwordField, submitButton });
```

## <a name="adding-button-functionality"></a>添加按钮功能

单击某个按钮后，用户将会希望发生某些情况。 例如，将显示一个警报，或在另一个屏幕上执行导航。

让我们添加一些代码，将第二个视图控制器推送到导航堆栈上。

首先，创建第二个视图控制器：

```csharp
var loginVC = new UIViewController () { Title = "Login Success!"};
loginVC.View.BackgroundColor = UIColor.Purple;
```

然后，将该功能添加到 `TouchUpInside` 事件：

```csharp
submitButton.TouchUpInside += (sender, e) => {
                this.NavigationController.PushViewController (loginVC, true);
            };
```

导航如下所示：

[![](ios-code-only-images/navigation.png "The navigation is illustrated in this chart")](ios-code-only-images/navigation.png#lightbox)

请注意，默认情况下，当你使用导航控制器时，iOS 将为应用程序提供导航栏和 "后退" 按钮，以允许你通过堆栈向后移动。

## <a name="iterating-through-the-view-hierarchy"></a>遍历视图层次结构

可以循环访问子视图层次结构并选择任何特定的视图。 例如，若要查找每个 `UIButton` 并为该按钮提供不同的 `BackgroundColor`，可以使用以下代码片段

```csharp
foreach(var subview in View.Subviews)
{
    if (subview is UIButton)
    {
         var btn = subview as UIButton;
         btn.BackgroundColor = UIColor.Green;
    }
}
```

但是，如果要循环访问的视图是 `UIView`，则这种情况将不起作用，因为当添加到父视图的对象本身继承 `UIView`时，所有视图都将返回为 `UIView`。

## <a name="handling-rotation"></a>处理旋转

如果用户将设备旋转到横向，控件不会相应地调整大小，如以下屏幕截图所示：

[![](ios-code-only-images/image7.png "If the user rotates the device to landscape, the controls do not resize appropriately")](ios-code-only-images/image7.png#lightbox)

解决此问题的一种方法是设置每个视图的 `AutoresizingMask` 属性。 在这种情况下，我们希望控件水平拉伸，因此，我们将设置每个 `AutoresizingMask`。 下面的示例针对的是 `usernameField`，但需要将相同的应用于视图层次结构中的每个小工具。

```csharp
usernameField.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
```

现在，当我们旋转设备或模拟器时，所有内容都会拉伸以填充额外的空间，如下所示：

[![](ios-code-only-images/image8.png "All the controls stretch to fill the additional space")](ios-code-only-images/image8.png#lightbox)

## <a name="creating-custom-views"></a>创建自定义视图

除了使用 UIKit 包含的控件以外，还可以使用自定义视图。 可以通过继承 `UIView` 和重写 `Draw`来创建自定义视图。 让我们创建一个自定义视图，并将其添加到视图层次结构中以演示。

### <a name="inheriting-from-uiview"></a>从 UIView 继承

我们需要做的第一件事就是为自定义视图创建一个类。 我们将使用 Visual Studio 中的**类**模板来添加名为 `CircleView`的空类。 应将基类设置为 "`UIView`"，我们回忆这是 `UIKit` 命名空间中。 还需要 `System.Drawing` 命名空间。 在此示例中不会使用其他各种 `System.*` 命名空间，因此可以随意将其删除。

类应如下所示：

```csharp
using System;

namespace CodeOnlyDemo
{
    class CircleView : UIView
    {
    }
}
```

### <a name="drawing-in-a-uiview"></a>在 UIView 中绘制

每个 `UIView` 都有一个 `Draw` 方法，该方法在需要绘制时由系统调用。 永远不应直接调用 `Draw`。 它由系统在运行循环处理期间调用。 在将视图添加到视图层次结构之后，第一次通过运行循环时，将调用其 `Draw` 方法。 当视图被标记为需要通过在视图上调用 `SetNeedsDisplay` 或 `SetNeedsDisplayInRect` 进行绘制时，对 `Draw` 的后续调用。

可以通过在重写的 `Draw` 方法中添加此类代码，将绘图代码添加到视图中，如下所示：

```csharp
public override void Draw(CGRect rect)
{
    base.Draw(rect);

    //get graphics context
    using (var g = UIGraphics.GetCurrentContext())
    {
        // set up drawing attributes
        g.SetLineWidth(10.0f);
        UIColor.Green.SetFill();
        UIColor.Blue.SetStroke();

        // create geometry
        var path = new CGPath();
        path.AddArc(Bounds.GetMidX(), Bounds.GetMidY(), 50f, 0, 2.0f * (float)Math.PI, true);

        // add geometry to graphics context and draw
        g.AddPath(path);
        g.DrawPath(CGPathDrawingMode.FillStroke);
    }
}
```

由于 `CircleView` 是 `UIView`，因此还可以设置 `UIView` 属性。 例如，可以在构造函数中设置 `BackgroundColor`：

```csharp
public CircleView()
{
    BackgroundColor = UIColor.White;
}
```

若要使用我们刚刚创建的 `CircleView`，可以在现有控制器中将它作为子视图添加到视图层次结构中，就像我们前面的 `UILabels` 和 `UIButton` 一样，或者我们可以将其作为新控制器的视图进行加载。 接下来。

### <a name="loading-a-view"></a>加载视图

 `UIViewController` 具有一个名为 `LoadView` 的方法，控制器调用此方法来创建其视图。 这是创建视图并将其分配给控制器的 `View` 属性的适当位置。

首先，我们需要一个控制器，因此请创建一个名为 `CircleController`的新的空类。

在 `CircleController` 添加以下代码以将 `View` 设置为 `CircleView` （不应在重写中调用 `base` 实现）：

```csharp
using UIKit;

namespace CodeOnlyDemo
{
    class CircleController : UIViewController
    {
        CircleView view;

        public override void LoadView()
        {
            view = new CircleView();
            View = view;
        }
    }
}
```

最后，我们需要在运行时显示控制器。 为此，我们在前面添加的 "提交" 按钮上添加一个事件处理程序，如下所示：

```csharp
submitButton.TouchUpInside += delegate
{
    Console.WriteLine("Submit button clicked");

    //circleController is declared as class variable
    circleController = new CircleController();
    PresentViewController(circleController, true, null);
};
```

现在，运行应用程序并点击 "提交" 按钮时，会显示带有圆圈的新视图：

[![](ios-code-only-images/circles.png "The new view with a circle is displayed")](ios-code-only-images/circles.png#lightbox)

## <a name="creating-a-launch-screen"></a>创建启动屏幕

当你的应用程序启动时，将显示[启动屏幕](~/ios/app-fundamentals/images-icons/launch-screens.md)，以便向用户显示它的响应能力。 由于在加载应用时显示启动屏幕，因此无法在代码中创建该屏幕，因为应用程序仍会加载到内存中。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

当你在 Visual Studio 中创建 iOS 项目时，将以 xib 文件的形式提供一个启动屏幕，可以在项目中的**资源**文件夹中找到该文件。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

在 Visual Studio for Mac 中创建 iOS 项目时，将以情节提要文件的形式提供启动屏幕。

-----

可以通过双击它并在 iOS 设计器中打开它来编辑此项。

Apple 建议为面向 iOS 8 或更高版本的应用程序使用 xib 或情节提要文件，当你在 iOS 设计器中启动任一文件时，你将使用大小类和自动布局调整布局，使其看起来正确并正确地显示所有设备小时. 除了 xib 或情节提要以外，还可以使用静态启动映像，以支持面向更早版本的应用程序。

有关创建启动屏幕的详细信息，请参阅以下文档：

- [使用 xib 创建启动屏幕](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/templates/launchscreen-xib)
- [用情节提要管理启动屏幕](~/ios/app-fundamentals/images-icons/launch-screens.md)

> [!IMPORTANT]
> 在 iOS 9 中，Apple 建议使用情节提要作为创建启动屏幕的主要方法。

### <a name="creating-a-launch-image-for-pre-ios-8-applications"></a>为 iOS 8 之前的应用程序创建启动映像

如果应用程序面向 iOS 8 之前的版本，则除了 xib 或情节提要启动屏幕之外，还可以使用静态图像。

可以在 info.plist 文件中设置此静态图像，也可以在应用程序中将其设置为资产目录（对于 iOS 7）。 需要为应用程序可以运行的每个设备大小（320x480、640x960、640x1136）提供单独的映像。 有关启动屏幕大小的详细信息，请查看[启动屏幕图像](~/ios/app-fundamentals/images-icons/launch-screens.md)指南。

> [!IMPORTANT]
> 如果你的应用程序没有启动屏幕，你可能会注意到它不完全适合屏幕。 如果是这种情况，则应确保至少包含一个名为 `Default-568@2x.png` info.plist 的640x1136 映像。

## <a name="summary"></a>总结

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

本文介绍了如何在 Visual Studio 中以编程方式开发 iOS 应用程序。 我们介绍了如何根据空项目模板构建项目，讨论了如何创建根视图控制器并将其添加到窗口中。 接下来，我们演示了如何使用 UIKit 中的控件在控制器中创建视图层次结构，以开发应用程序屏幕。 接下来，我们探讨了如何使视图在不同的方向上正确布局，并了解了如何创建自定义视图（通过对 `UIView`进行子类化，以及如何在控制器中加载视图）。 最后，我们探讨了如何将启动屏幕添加到应用程序。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

本文介绍了如何以编程方式在 Visual Studio for Mac 中开发 iOS 应用程序。 我们介绍了如何根据单个视图模板构建项目，讨论了如何创建根视图控制器并将其添加到窗口中。 接下来，我们演示了如何使用 UIKit 中的控件在控制器中创建视图层次结构，以开发应用程序屏幕。 接下来，我们探讨了如何使视图在不同的方向上正确布局，并了解了如何创建自定义视图（通过对 `UIView`进行子类化，以及如何在控制器中加载视图）。 最后，我们探讨了如何将启动屏幕添加到应用程序。

-----

## <a name="related-links"></a>相关链接

- [SimpleLogin （示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/simplelogin)
