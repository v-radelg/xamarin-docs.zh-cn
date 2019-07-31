---
title: 在 Xamarin.iOS 中的代码中创建 iOS 用户界面
description: 本文档介绍如何使用代码以生成 Xamarin.iOS 应用程序的用户界面。 它讨论了构建视图层次结构中，处理旋转、 和的详细信息视图控制器。
ms.prod: xamarin
ms.assetid: 7CB1FEAE-0BB3-4CDC-9076-5BD555003F1D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/03/2018
ms.openlocfilehash: 105d06c7cea23ac0873c8de68d7ad1ad62ae1ffc
ms.sourcegitcommit: 84764b9c51e769d6d6570a362af8451607c7e0d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68665704"
---
# <a name="creating-ios-user-interfaces-in-code-in-xamarinios"></a>在 Xamarin.iOS 中的代码中创建 iOS 用户界面

IOS 应用程序的用户界面类似于店面，首先 – 应用程序通常只获取一个窗口，但它可以填满窗口使用如许多对象需要以及根据什么应用程序可以更改对象和排列方式要显示的。 此情形中的对象（用户看到的内容）称为视图。 若要生成的应用程序中的单个屏幕，视图相互堆叠在内容视图层次结构中，并由单个视图控制器管理层次结构。 具有多个屏幕的应用程序具有多个内容视图层次结构（各自具有自己的视图控制器），应用程序会将视图置于窗口中以基于用户所处的屏幕创建不同的内容视图层次结构。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

下图显示了窗口、视图、子视图与视图控制器之间的关系，它们向设备屏幕提供了用户界面：

[![](ios-code-only-images/image9.png "此图描述了窗口、 视图、 子视图和视图控制器之间的关系")](ios-code-only-images/image9.png#lightbox)

可以使用构造这些视图层次结构[适用于 iOS 的 Xamarin 设计器](~/ios/user-interface/designer/index.md)在 Visual Studio 中，但是最好有基本的了解了如何完全在代码中处理。 本文将指导完成一些基本的点来启动和运行仅限代码的用户界面开发。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

下图显示了窗口、视图、子视图与视图控制器之间的关系，它们向设备屏幕提供了用户界面：

[![](ios-code-only-images/image9.png "此图描述了窗口、 视图、 子视图和视图控制器之间的关系")](ios-code-only-images/image9.png#lightbox)

可以使用构造这些视图层次结构[适用于 iOS 的 Xamarin 设计器](~/ios/user-interface/designer/index.md)在 Visual Studio for Mac，但是最好有基本的了解了如何完全在代码中处理。 本文将指导完成一些基本的点来启动和运行仅限代码的用户界面开发。

-----

## <a name="creating-a-code-only-project"></a>创建仅限代码的项目

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="ios-blank-project-template"></a>iOS 空白项目模板

首先，创建 Visual Studio 中使用的 iOS 项目**文件 > 新建项目 > Visual C# > iPhone 和 iPad > iOS 应用 (Xamarin)** 项目，如下所示：

[![新建项目对话框](ios-code-only-images/blankapp.w157-sml.png)](ios-code-only-images/blankapp.w157.png#lightbox)

然后选择**空白应用**项目模板：

[![选择模板对话框](ios-code-only-images/blankapp-2.w157-sml.png)](ios-code-only-images/blankapp-2.w157.png#lightbox)

空项目模板将 4 个文件添加到项目：

[![项目文件](ios-code-only-images/empty-project.w157-sml.png "项目文件")](ios-code-only-images/empty-project.w157.png#lightbox)

1. **AppDelegate.cs** -包含`UIApplicationDelegate`子类， `AppDelegate` ，用于处理从 iOS 应用程序事件。 在中创建应用程序窗口`AppDelegate`的`FinishedLaunching`方法。
1. **Main.cs** -包含应用程序指定的类的入口点为`AppDelegate`。
1. **Info.plist** -包含应用程序的配置信息的属性列表文件。
1. **Entitlements.plist** – 包含有关功能和权限的应用程序的信息的属性列表文件。


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

## <a name="ios-templates"></a>iOS 模板


Visual Studio for Mac 不提供一个空的模板。 所有模板都都具有支持情节提要，Apple 建议将它作为创建 UI 的主要方式。 但是，就可以完全在代码中创建你的 UI。

执行以下步骤将引导你完成从应用程序中删除情节提要：


1. 使用单一视图应用模板来创建新的 iOS 项目：

    [![](ios-code-only-images/single-view-app.png "使用单一视图应用模板")](ios-code-only-images/single-view-app.png#lightbox)

1. 删除`Main.Storyboard`和`ViewController.cs`文件。 不要**不**删除`LaunchScreen.Storyboard`。 应删除视图控制器，因为它是在情节提要中创建的视图控制器的隐藏代码：
1. 请务必选择**删除**从弹出的对话框：

    [![](ios-code-only-images/delete.png "从弹出对话框中选择删除")](ios-code-only-images/delete.png#lightbox)

1. 在 Info.plist 中，删除内部信息**部署信息 > 主界面**选项：

    [![](ios-code-only-images/main-interface.png "删除主界面选项内的信息")](ios-code-only-images/main-interface.png#lightbox)

1. 最后，将以下代码添加到你`FinishedLaunching`AppDelegate 类中的方法：

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

已添加到代码`FinishedLaunching`上述，步骤 5 中的方法是最少量的代码创建一个窗口，以在 iOS 应用程序所必需的。

-----

使用生成 iOS 应用程序[MVC 模式](~/ios/get-started/hello-ios-multiscreen/hello-ios-multiscreen-deepdive.md#model-view-controller-mvc)。 从窗口的根视图控制器创建应用程序显示的第一个屏幕。 请参阅[Hello，iOS 多屏显示](~/ios/get-started/hello-ios-multiscreen/index.md)指导有关 MVC 的详细信息模式本身。

实现`AppDelegate`添加的模板创建应用程序窗口的其中只有一个每个 iOS 应用程序，并使其显示与下面的代码：

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

如果要运行此应用程序现在，很可能会得到消息，指出引发的异常`Application windows are expected to have a root view controller at the end of application launch`。 让我们添加一个控制器，并使其应用程序的根视图控制器。

## <a name="adding-a-controller"></a>添加控制器

您的应用程序可以包含多个视图控制器，但它需要有一个根视图控制器，以控制所有视图控制器。  将控制器添加到窗口中，通过创建`UIViewController`实例并将其设置为`Window.RootViewController`属性：

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

每个控制器都有关联的视图，可从访问`View`属性。 上面的代码中更改视图的`BackgroundColor`属性设置为`UIColor.LightGray`，以便将会显示，如下所示：

 [![](ios-code-only-images/image1.png "该视图的背景是可见的浅灰色")](ios-code-only-images/image1.png#lightbox)

我们无法设置任何`UIViewController`作为子类`RootViewController`以这种方式，包括从 UIKit，以及我们自己编写的控制器。 例如，下面的代码添加`UINavigationController`作为`RootViewController`:

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

这会生成嵌套在导航控制器，如下所示的控制器：

 [![](ios-code-only-images/image2.png "在控制器嵌套在导航控制器")](ios-code-only-images/image2.png#lightbox)

## <a name="creating-a-view-controller"></a>创建视图控制器

现在，我们已了解如何添加控制器作为`RootViewController`的窗口，让我们了解如何在代码中创建自定义视图控制器。

添加新的类名为`CustomViewController`，如下所示：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](ios-code-only-images/customviewcontroller.w157-sml.png "添加新的类名为 CustomViewController")](ios-code-only-images/customviewcontroller.w157.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![](ios-code-only-images/new-file.png "添加新的类名为 CustomViewController")](ios-code-only-images/new-file.png#lightbox)

-----

类应继承自`UIViewController`，后者位于`UIKit`命名空间，如所示：

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

`UIViewController` 包含一个名为方法`ViewDidLoad`视图控制器首次加载到内存时调用。 这是视图的更好地进行初始化，例如，设置它的属性。

例如，以下代码将添加一个按钮和一个事件处理程序以新视图控制器推送到导航堆栈时按下按钮：

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

若要加载此控制器中您的应用程序，并演示的简单导航，请创建的新实例`CustomViewController`。 创建新的导航控制器、 传入视图控制器实例并将新的导航控制器设置为窗口的`RootViewController`在`AppDelegate`像以前一样：

```csharp
var cvc = new CustomViewController ();

var navController = new UINavigationController (cvc);

Window.RootViewController = navController;
```

现在应用程序加载时，`CustomViewController`内导航控制器加载：

 [![](ios-code-only-images/customvc.png "内部导航控制器加载 CustomViewController")](ios-code-only-images/customvc.png#lightbox)

单击按钮，将_推送_到导航堆栈上新的视图控制器：

[![](ios-code-only-images/customvca.png "新视图控制器推送到导航堆栈上")](ios-code-only-images/customvca.png#lightbox)

## <a name="building-the-view-hierarchy"></a>生成的视图层次结构

在上面的示例中，我们开始在代码中创建用户界面，通过将一个按钮添加到视图控制器。

iOS 用户界面由视图层次结构组成。 其他视图，例如标签、 按钮、 滑块等添加为子视图的某些父视图。

例如，让我们编辑`CustomViewController`来创建用户可以在其中输入用户名和密码登录屏幕。 屏幕将由两个文本字段和一个按钮组成。

### <a name="adding-the-text-fields"></a>添加文本字段

首先，删除的按钮和事件处理程序中添加了[初始化视图](#initializing-the-view)部分。 

添加一个用于用户名的控件通过创建并初始化`UITextField`然后将它添加到视图层次结构，如下所示：

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

当我们创建`UITextField`，我们将设置`Frame`属性来定义其位置和大小。 在 iOS 中的 0，0 坐标是在右侧 + x 左上角和 + 向下的 y。 设置后`Frame`以及几个其他属性，我们调用`View.AddSubview`以添加`UITextField`到视图层次结构。 这使得`usernameField`的子视图`UIView`实例的`View`属性引用。 其 z-顺序高于其父视图，使其显示在屏幕上的父视图的前面添加子视图。

使用应用程序`UITextField`包含如下所示：

 [![](ios-code-only-images/image4.png "具有包含 UITextField 的应用程序")](ios-code-only-images/image4.png#lightbox)

我们可以添加`UITextField`对于类似的方式中的密码，但这次我们把`SecureTextEntry`属性设置为 true，如下所示：

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

设置`SecureTextEntry = true`隐藏中输入的文本`UITextField`用户，如下所示：

 [![](ios-code-only-images/image4a.png "设置 SecureTextEntry true 隐藏用户输入的文本")](ios-code-only-images/image4a.png#lightbox)

### <a name="adding-the-button"></a>添加按钮

接下来，我们将添加一个按钮，以便用户可以提交的用户名和密码。 通过将它作为参数传递到父视图的按钮添加到类似于任何其他控件的视图层次结构`AddSubview`再次方法。

以下代码添加的按钮，并注册的事件处理程序`TouchUpInside`事件：

```csharp
var submitButton = UIButton.FromType (UIButtonType.RoundedRect);

submitButton.Frame = new CGRect (10, 170, w - 20, 44);
submitButton.SetTitle ("Submit", UIControlState.Normal);

submitButton.TouchUpInside += (sender, e) => {
    Console.WriteLine ("Submit button pressed");
};

View.AddSubview(submitButton);
```

与此位置中，登录屏幕现在显示如下所示：

 [![](ios-code-only-images/image5.png "登录屏幕")](ios-code-only-images/image5.png#lightbox)

与在以前版本的 iOS、 默认按钮背景是透明的。 更改按钮的`BackgroundColor`属性更改此：

```csharp
submitButton.BackgroundColor = UIColor.White;
```

这将导致方形按钮，而不是典型的舍入有边的按钮。 若要获取的圆角的边缘，请使用以下代码片段：

```csharp
submitButton.Layer.CornerRadius = 5f;
```

这些更改，该视图将如下所示：

[![](ios-code-only-images/image6.png "视图的示例运行")](ios-code-only-images/image6.png#lightbox)

## <a name="adding-multiple-views-to-the-view-hierarchy"></a>将多个视图添加到的视图层次结构

iOS 提供的工具，使用到的视图层次结构中添加多个视图`AddSubviews`。

```csharp
View.AddSubviews(new UIView[] { usernameField, passwordField, submitButton });
```

## <a name="adding-button-functionality"></a>添加按钮功能

单击按钮时，用户希望发生这种情况的内容。 例如，会显示一个警告或导航执行到另一个屏幕。

让我们添加一些代码，将第二个视图控制器推送到导航堆栈上。

首先，创建第二个视图控制器：

```csharp
var loginVC = new UIViewController () { Title = "Login Success!"};
loginVC.View.BackgroundColor = UIColor.Purple;
```

然后，将功能添加到`TouchUpInside`事件：

```csharp
submitButton.TouchUpInside += (sender, e) => {
                this.NavigationController.PushViewController (loginVC, true);
            };
```

在导航窗格如下所示：

[![](ios-code-only-images/navigation.png "在导航窗格是在此图中所示")](ios-code-only-images/navigation.png#lightbox)

请注意，默认情况下，当使用导航控制器，iOS 赋予应用程序导航栏和后退按钮，可用于向后移动到堆栈。

## <a name="iterating-through-the-view-hierarchy"></a>循环访问的视图层次结构

就可以循环访问子视图层次结构并找出任何特定视图。 例如，若要查找每个`UIButton`，并为该按钮提供一个不同`BackgroundColor`，可以使用以下代码片段

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

此操作，但是不起作用的循环访问视图是否`UIView`如所有视图将都会返回不会成为`UIView`与对象添加到父视图本身继承`UIView`。

## <a name="handling-rotation"></a>处理旋转

如果在用户旋转设备为横向，控件不调整大小相应地，如以下屏幕截图所示：

[![](ios-code-only-images/image7.png "如果在用户旋转设备为横向，控件是否未相应地调整大小")](ios-code-only-images/image7.png#lightbox)

若要解决此问题的一种方法是通过设置`AutoresizingMask`上每个视图的属性。 在这种情况下，我们希望水平拉伸的控件，因此我们将设置每个`AutoresizingMask`。 以下示例适用于`usernameField`，但相同需要应用于视图层次结构中每个小工具。

```csharp
usernameField.AutoresizingMask = UIViewAutoresizing.FlexibleWidth;
```

现在当我们旋转设备或模拟器，所有内容拉伸以填充附加空间，如下所示：

[![](ios-code-only-images/image8.png "所有控件都拉伸以填充更多空间")](ios-code-only-images/image8.png#lightbox)

## <a name="creating-custom-views"></a>创建自定义视图

除了使用属于的 UIKit 控件，还可以使用自定义视图。 可以通过继承创建自定义视图`UIView`并重写`Draw`。 让我们来创建自定义视图，并将其添加到视图层次结构，以演示。

### <a name="inheriting-from-uiview"></a>从 UIView 继承

我们需要做的第一件事是创建自定义视图的类。 我们将使用执行此类情况的操作**类**在 Visual Studio 中添加一个名为的空类模板`CircleView`。 类的基类应设置为`UIView`，后者我们回想一下位于`UIKit`命名空间。 我们还需要`System.Drawing`命名空间。 其他各种`System.*`命名空间不会使用在此示例中，因此可以删除它们。

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

### <a name="drawing-in-a-uiview"></a>UIView 中绘制

每个`UIView`具有`Draw`需要绘制时由系统调用的方法。 `Draw` 应永远不会直接调用。 它是由系统调用，在运行的循环处理过程。 第一次循环运行后视图添加到视图层次结构，其`Draw`调用方法。 对后续调用`Draw`出现在视图标记为无需通过调用绘制`SetNeedsDisplay`或`SetNeedsDisplayInRect`在视图上。

可以通过将此类中被重写的代码添加到我们的视图添加绘制代码`Draw`方法，如下所示：

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

由于`CircleView`是`UIView`，我们还可以设置`UIView`属性。 例如，我们可以设置`BackgroundColor`构造函数中：

```csharp
public CircleView()
{
    BackgroundColor = UIColor.White;
}
```

若要使用`CircleView`我们刚刚创建，我们可以或者将其添加作为子视图到视图层次结构中现有的控制器，如我们一样`UILabels`和`UIButton`更早版本，或者我们可以将其加载为新的控制器的视图。 让我们执行后一操作。

### <a name="loading-a-view"></a>正在加载视图

 `UIViewController` 具有一个名为方法`LoadView`调用由控制器来创建其视图。 这是更好地创建一个视图并将其分配给控制器的`View`属性。

首先，我们需要一个控制器，因此，创建新的空类命名为`CircleController`。

在中`CircleController`添加以下代码以设置`View`到`CircleView`(不应调用`base`实现重写中的):

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

最后，我们需要在运行时提供的控制器。 让我们执行此操作通过提交按钮，我们添加了前面部分中，按如下所示添加事件处理程序：

```csharp
submitButton.TouchUpInside += delegate
{
    Console.WriteLine("Submit button clicked");

    //circleController is declared as class variable
    circleController = new CircleController();
    PresentViewController(circleController, true, null);
};
```

现在，当我们运行该应用程序，并点击提交按钮，将显示具有一个圆形的新视图：

[![](ios-code-only-images/circles.png "显示与某一圆形的新视图")](ios-code-only-images/circles.png#lightbox)

## <a name="creating-a-launch-screen"></a>创建启动屏幕

一个[启动屏幕](~/ios/app-fundamentals/images-icons/launch-screens.md)作为一种方式向其可以响应用户显示您的应用程序启动时显示。 由于您的应用程序加载时显示启动屏幕，它无法创建在代码中这是因为应用程序仍在加载到内存中。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

在 Visual Studio 中创建一个 iOS 项目时，为你的.xib 文件，可在窗体中提供了启动屏幕**资源**内你的项目文件夹中。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

当您在 for Mac 在 Visual Studio 中创建一个 iOS 项目时，会为您的情节提要文件形式提供启动屏幕。

-----

这可以通过双单击它并在 iOS 设计器中打开进行编辑。

Apple 建议.xib 或情节提要文件用于应用程序面向 iOS 8 或更高版本，当启动 iOS 设计器中的任一文件时，您将使用大小类和自动布局以适应你的布局，以便它看起来没问题，并显示正确，所有设备大小。 静态启动图像可以除了.xib 或情节提要，用于允许对面向早期版本的应用程序的支持。

有关创建启动屏幕的详细信息，请参阅以下文档：

- [创建使用.xib 的启动屏幕](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/templates/launchscreen-xib)
- [管理使用演示图板启动屏幕](~/ios/app-fundamentals/images-icons/launch-screens.md)

> [!IMPORTANT]
> 截至 iOS 9，Apple 建议情节提要应作为创建启动屏幕的主要方法。

### <a name="creating-a-launch-image-for-pre-ios-8-applications"></a>创建的启动映像的预 iOS 8 的应用程序

如果应用程序的所有面向 iOS 8 之前的版本不同，静态图像可以使用除.xib 或情节提要启动屏幕。

可以将设置此静态图像，在 Info.plist 文件中，或在应用程序中 （适用于 iOS 7) 资产目录。 您需要为你的应用程序可以运行每个设备大小 （320x480、 640 x 960 640 x 1136） 提供单独的映像。 启动屏幕大小的详细信息，请查看[启动屏幕图像](~/ios/app-fundamentals/images-icons/launch-screens.md)指南。

> [!IMPORTANT]
> 如果你的应用程序没有启动屏幕，你可能会注意到，它并不完全适合屏幕的大小。 如果是这样，您应确保至少，包含名为 640 x 1136 映像`Default-568@2x.png`向你的 Info.plist。

## <a name="summary"></a>总结

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

本文讨论了如何开发以编程方式在 Visual Studio 中的 iOS 应用程序。 我们介绍了如何构建一个项目的空项目模板，讨论了如何创建并将根视图控制器添加到窗口。 然后，我们介绍了如何使用控件从 UIKit 创建视图层次结构控制器中的开发应用程序屏幕。 接下来，我们探讨如何使视图布局相应地在不同的方向和我们已了解如何创建自定义视图通过子类化`UIView`，以及如何加载的视图控制器中。 最后，我们探讨了如何将启动屏幕添加到应用程序。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

本文讨论了如何开发 iOS 应用程序以编程方式在 Visual Studio for mac。 我们了解了如何生成的项目从一个视图模板，讨论了如何创建并将根视图控制器添加到窗口。 然后，我们介绍了如何使用控件从 UIKit 创建视图层次结构控制器中的开发应用程序屏幕。 接下来，我们探讨如何使视图布局相应地在不同的方向和我们已了解如何创建自定义视图通过子类化`UIView`，以及如何加载的视图控制器中。 最后，我们探讨了如何将启动屏幕添加到应用程序。

-----

## <a name="related-links"></a>相关链接

- [SimpleLogin （示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/simplelogin)
