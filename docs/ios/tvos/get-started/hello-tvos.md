---
title: 你好，tvOS 快速入门指南
description: 本指南介绍如何创建第一个 Xamarin.tvOS 应用和其开发工具链。 它还引入了 Xamarin 设计器中，公开给代码中，UI 控件，并说明了如何生成、 运行和测试 Xamarin.tvOS 应用程序。
ms.prod: xamarin
ms.assetid: 6E0AFE58-A13B-492F-861E-D5D73EB1C4A3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 02/02/2018
ms.openlocfilehash: 859bbd22640ba3d09324fcd3853cda26e563a1cd
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865279"
---
# <a name="hello-tvos-quick-start-guide"></a>你好，tvOS 快速入门指南

_本指南介绍如何创建第一个 Xamarin.tvOS 应用和其开发工具链。它还引入了 Xamarin 设计器中，公开给代码中，UI 控件，并说明了如何生成、 运行和测试 Xamarin.tvOS 应用程序。_

Apple 已发布的第五个 Apple TV 中，Apple TV 4k、 运行 tvOS 11 的新一代。

Apple TV 平台是打开开发人员，使他们能够创建丰富的沉浸式应用程序和通过 Apple tv 设备内置 App Store 发布它们。

如果您熟悉 Xamarin.iOS 开发，您会发现转换到 tvOS 相当简单。 Api 和功能的大部分都是相同，但是，许多常见的 Api （例如，WebKit) 不可用。 此外，使用与 Siri 远程会带来基于触摸屏的 iOS 设备中不存在的一些设计难题。

本指南将向介绍使用 Xamarin 应用中的 tvOS。 TvOS 的详细信息，请参阅 Apple[使可供 Apple TV 4k](https://developer.apple.com/tvos/)文档。

## <a name="overview"></a>概述

Xamarin.tvOS 允许你开发完全本机 Apple TV 应用中的C#和使用的 OS X 库和界面控件中进行开发时所使用的.NET *Swift* (或*Objective C*) 和*Xcode*。

此外，因为在编写应用程序所的 Xamarin.tvOSC#和.NET 中，通用后, 端代码可与 Xamarin.iOS、 Xamarin.Android 和 Xamarin.Mac 应用; 共享同时在每个平台上实现本机体验。

本文将向您介绍创建通过引导您完成生成基本的过程中使用 Xamarin.tvOS 和 Visual Studio 的 Apple TV 应用所需的关键概念**你好，tvOS**的次数进行计数的应用程序的按钮具有已单击：

[![](hello-tvos-images/run05.png "运行示例应用")](hello-tvos-images/run05.png#lightbox)

我们将介绍以下概念：

-  **Visual Studio for Mac** – Visual Studio for Mac，以及如何使用它创建 Xamarin.tvOS 应用程序的简介。
-  **Xamarin.tvOS 应用剖析**– 什么 Xamarin.tvOS 应用组成。
-  **创建用户界面**– 如何使用适用于 iOS 的 Xamarin 设计器来创建用户界面。
-  **部署和测试**– 如何运行和 tvOS 模拟器中和实际 tvOS 硬件上测试您的应用程序。

## <a name="starting-a-new-xamarintvos-app-in-visual-studio-for-mac"></a>在 Visual Studio for Mac 中启动新的 Xamarin.tvOS 应用

如上面所述，我们将创建名为 Apple TV 应用`Hello-tvOS`将一个按钮和标签添加到主屏幕。 点击此按钮后，标签会显示已点击次数。

若要开始，让我们执行以下操作：

1. 启动 Visual Studio for Mac：

    [![](hello-tvos-images/setup01.png "Visual Studio for Mac")](hello-tvos-images/setup01.png#lightbox)
2. 单击**新的解决方案...** 以打开屏幕的左上角中的链接**新建项目**对话框。
3. 选择**tvOS** > **应用** > **单一视图应用**然后单击**下一步**按钮：

    [![](hello-tvos-images/setup02.png "选择单一视图应用")](hello-tvos-images/setup02.png#lightbox)
4. 输入`Hello, tvOS`有关**应用名称**，输入你**组织标识符**然后单击**下一步**按钮：

    [![](hello-tvos-images/setup04.png "输入你好，tvOS")](hello-tvos-images/setup04.png#lightbox)
5. 输入`Hello_tvOS`有关**项目名称**然后单击**创建**按钮：

    [![](hello-tvos-images/setup03.png "输入 HellotvOS")](hello-tvos-images/setup03.png#lightbox)

Visual Studio for Mac 将创建新的 Xamarin.tvOS 应用，并显示添加到应用程序的解决方案的默认文件：

 [![](hello-tvos-images/project01.png "默认文件视图")](hello-tvos-images/project01.png#lightbox)

Visual Studio for Mac 使用**解决方案**并**项目**，在 Visual Studio 执行完全相同的方式。 解决方案是可包含一个或多个项目的容器；项目可包含应用程序、支持库和测试应用程序等。在这种情况下，Visual Studio for Mac 已创建一个解决方案和应用程序项目为您。

如果愿意，可以创建一个或多个代码库项目包含通用共享代码。 可以使用的应用程序项目或与其他 Xamarin.tvOS 应用项目共享这些库项目 （或 Xamarin.iOS、 Xamarin.Android 和 Xamarin.Mac 基于代码的类型） 时，就像在生成标准的.NET 应用程序时一样。

## <a name="anatomy-of-a-xamarintvos-app"></a>Xamarin.tvOS 应用剖析

如果您熟悉 iOS 编程，您会发现大量此处的相似之处。 事实上，tvOS 9 是 iOS 9 的子集，因此这里将跨许多概念。

让我们看看该项目中的文件：

-   `Main.cs` – 此文件包含应用的主入口点。 启动应用时，其中会包含运行的首个类和方法。
-   `AppDelegate.cs` – 此文件包含主应用程序类，它负责侦听来自操作系统的事件。
-   `Info.plist` – 此文件包含应用程序属性，如应用程序名称、 图标等。
-   `ViewController.cs` – 这是表示主窗口和控件的生命周期的类。
-   `ViewController.designer.cs` – 此文件包含可帮助你将与主屏幕用户界面集成的 plumbing 代码。
-  `Main.storyboard` – 主窗口 UI。 此文件可以是由创建和维护用于 iOS 的 Xamarin 设计器。

在以下部分中，我们将简要介绍其中的某些文件。 我们将探讨这些更详细地更高版本，但最好现在了解相关基础知识。

### <a name="maincs"></a>Main.cs

`Main.cs`文件包含一个静态`Main`方法创建一个新的 Xamarin.tvOS 应用实例并将其传递的类的名称将处理 OS 事件，这在本例中是`AppDelegate`类：

```csharp
using UIKit;

namespace Hello_tvOS
{
    public class Application
    {
        // This is the main entry point of the application.
        static void Main (string[] args)
        {
            // if you want to use a different Application Delegate class from "AppDelegate"
            // you can specify it here.
            UIApplication.Main (args, null, "AppDelegate");
        }
    }
}
```

### <a name="appdelegatecs"></a>AppDelegate.cs

`AppDelegate.cs`文件包含我们`AppDelegate`类，该类负责创建窗口和侦听 OS 事件：

```csharp
using Foundation;
using UIKit;

namespace Hello_tvOS
{
    // The UIApplicationDelegate for the application. This class is responsible for launching the
    // User Interface of the application, as well as listening (and optionally responding) to application events from iOS.
    [Register ("AppDelegate")]
    public class AppDelegate : UIApplicationDelegate
    {
        // class-level declarations

        public override UIWindow Window {
            get;
            set;
        }

        public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
        {
            // Override point for customization after application launch.
            // If not required for your application you can safely delete this method

            return true;
        }

        public override void OnResignActivation (UIApplication application)
        {
            // Invoked when the application is about to move from active to inactive state.
            // This can occur for certain types of temporary interruptions (such as an incoming phone call or SMS message)
            // or when the user quits the application and it begins the transition to the background state.
            // Games should use this method to pause the game.
        }

        public override void DidEnterBackground (UIApplication application)
        {
            // Use this method to release shared resources, save user data, invalidate timers and store the application state.
            // If your application supports background execution this method is called instead of WillTerminate when the user quits.
        }

        public override void WillEnterForeground (UIApplication application)
        {
            // Called as part of the transition from background to active state.
            // Here you can undo many of the changes made on entering the background.
        }

        public override void OnActivated (UIApplication application)
        {
            // Restart any tasks that were paused (or not yet started) while the application was inactive.
            // If the application was previously in the background, optionally refresh the user interface.
        }

        public override void WillTerminate (UIApplication application)
        {
            // Called when the application is about to terminate. Save data, if needed. See also DidEnterBackground.
        }
    }
}
```

此代码是可能不熟悉，除非已生成 iOS 应用程序之前，但它是非常简单。 让我们检查重要的行。

首先，让我们看看类级别变量声明：

```csharp
public override UIWindow Window {
            get;
            set;
        }

```

`Window`属性提供对主窗口访问。 tvOS 使用所谓*模型视图控制器*(MVC) 模式。 通常情况下，为你创建的每个窗口 （以及窗口内许多其他项），是控制器，它负责窗口生命周期，例如显示，并将新视图 （控件） 添加到它，等等。

接下来，我们必须`FinishedLaunching`方法。 已实例化应用程序，并负责实际创建应用程序窗口和开始在其中显示视图的过程后，在运行此方法。 因为我们的应用程序使用情节提要来定义其 UI，其他不不需要任何代码此处。

如模板中提供的许多其他方法`DidEnterBackground`和`WillEnterForeground`。 可以安全地删除这些如果未在应用中使用的应用程序事件。

### <a name="viewcontrollercs"></a>ViewController.cs

`ViewController`类是主窗口的控制器。 这意味着其负责主窗口的生命周期。 我们将仔细地检查这更高版本，现在我们只看看快速了解即可：

```csharp
using System;
using Foundation;
using UIKit;

namespace Hello_tvOS
{
    public partial class ViewController : UIViewController
    {
        public ViewController (IntPtr handle) : base (handle)
        {
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();
            // Perform any additional setup after loading the view, typically from a nib.
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
    }
}
```

#### <a name="viewcontrollerdesignercs"></a>ViewController.Designer.cs

主窗口类的设计器文件为空，但它将自动进行填充由 Visual Studio for Mac 我们使用 iOS 设计器创建用户界面：

```csharp
using Foundation;

namespace HellotvOS
{
    [Register ("ViewController")]
    partial class ViewController
    {
        void ReleaseDesignerOutlets ()
        {
        }
    }
}
```

我们不通常涉及使用设计器文件，因为它们只需通过 Visual Studio for Mac 自动管理，并只需提供可访问控件，我们将添加到任何窗口或在我们的应用程序中查看必备项探测代码。

现在，我们已创建 Xamarin.tvOS 应用，并且我们已基本了解其组件，让我们来学习如何创建 UI。

<a name="Creating-the-User-Interface" />

## <a name="creating-the-user-interface"></a>创建用户界面

无需使用适用于 iOS 的 Xamarin 设计器来创建 Xamarin.tvOS 应用的用户界面，可以直接从创建 UIC#代码，但不在本文的范围。 为简单起见，我们将使用 iOS 设计器来创建我们的 UI 完成本教程的剩余部分。

若要开始创建您的 UI，让我们双击`Main.storyboard`文件中**解决方案资源管理器**打开 iOS 设计器中为进行编辑：

[![](hello-tvos-images/designer01.png "解决方案资源管理器中的 Main.storyboard 文件")](hello-tvos-images/designer01.png#lightbox)

这应该启动设计器，如下所示：

[![](hello-tvos-images/designer02.png "在设计器")](hello-tvos-images/designer02.png#lightbox)

有关 iOS 设计器的详细信息和其工作原理，请参阅[适用于 iOS 的 Xamarin 设计器简介](~/ios/user-interface/designer/introduction.md)指南。

现在，我们可以开始将控件添加到我们 Xamarin.tvOS 的应用程序的设计图面。

请执行以下操作：

1. 找到**工具箱**，应在设计图面右侧：

    [![](hello-tvos-images/designer03.png "工具箱")](hello-tvos-images/designer03.png#lightbox)

    如果你不能在此处找到它，浏览到**视图 > 面板 > 工具箱**查看它。
2. 拖动**标签**从**工具箱**到设计图面：

    [![](hello-tvos-images/designer04.png "从工具箱中拖动一个标签")](hello-tvos-images/designer04.png#lightbox)
3. 单击**标题**属性中的**属性填充**和按钮的标题更改为`Hello, tvOS`并设置**字体大小**128 到：

    [![](hello-tvos-images/designer05.png "设置到你好，tvOS 标题并将字体大小设置为 128")](hello-tvos-images/designer05.png#lightbox)
4. 调整标签的大小以便的所有单词可见，并将其居中窗口的顶部附近：

    [![](hello-tvos-images/designer06.png "调整大小并使标签居中")](hello-tvos-images/designer06.png#lightbox)
5. 标签将现在需要为它的位置约束，以便它显示按预期方式。 而不考虑屏幕大小。 若要执行此操作，请单击标签之前*T 状手柄*显示：

    [![](hello-tvos-images/designer07.png "T 状手柄")](hello-tvos-images/designer07.png#lightbox)
6. 若要水平限定标签，请选择中心方形，并将其拖动到垂直虚线：

    [![](hello-tvos-images/designer08.png "选择中心方形")](hello-tvos-images/designer08zoom.png#lightbox)

     标签应变为橙色。
7. 选择顶部的标签，T 句柄并将其拖至窗口上边缘：

    [![](hello-tvos-images/designer09.png "将句柄拖到窗口的上边缘")](hello-tvos-images/designer09.png#lightbox)
8. 接下来，单击宽度，然后选择高度*骨骼句柄*如下图所示：

    [![](hello-tvos-images/designer10.png "宽度和高度骨骼句柄")](hello-tvos-images/designer10.png#lightbox)

     当每个*骨骼句柄*是单击，同时选择宽度和高度分别设置固定的维度。
9. 完成后，你的约束应查找类似于属性面板的布局选项卡中：

    [![](hello-tvos-images/designer11.png "示例约束")](hello-tvos-images/designer11.png#lightbox)
10. 拖动**按钮**从**工具箱**并将其放在标签下。
11. 单击**标题**属性中的**属性填充**并将该按钮的标题改为`Click Me`:

    [![](hello-tvos-images/designer12.png "将对我来说单击按钮标题更改")](hello-tvos-images/designer12.png#lightbox)
12. 重复步骤 5 到 8 上述约束 tvOS 窗口中的按钮。 但是，而不是将 T 句柄拖到 （如步骤 #7 中） 窗口的顶部，将其拖到标签的底部：

    [![](hello-tvos-images/designer14.png "约束按钮")](hello-tvos-images/designer14.png#lightbox)
13. 拖动的按钮下的另一个标签，将其作为第一个标签并将设置相同的宽度大小及其**对齐**到**Center**:

    [![](hello-tvos-images/designer15.png "拖动的按钮下的另一个标签，它是第一个标签的宽度相同，并将其对齐方式设置为中心的大小")](hello-tvos-images/designer15.png#lightbox)
14. 如第一个标签和按钮，设置此标签居中，并将其固定到的位置和大小：

    [![](hello-tvos-images/designer16.png "固定到的位置和大小的标签")](hello-tvos-images/designer16.png#lightbox)
15. 将所做的更改保存到用户界面。

已调整大小和移动控件时，您应该已经注意到，在设计器为您提供了有用的快捷提示基于[Apple TV 人机接口指南 》](https://developer.apple.com/tvos/human-interface-guidelines/)。 这些指南将帮助您创建高质量应用程序将拥有 Apple TV 用户熟悉的外观和感觉。

如果查看**文档大纲**部分中，请注意，布局和层次结构构成了我们的用户界面的元素的显示方式：

[![](hello-tvos-images/designer17.png "文档大纲部分")](hello-tvos-images/designer17.png#lightbox)

在这里可以选择要编辑或通过拖动对 UI 元素进行重新排序，如果所需的项。 例如，如果 UI 元素已被另一元素覆盖，你可以将其拖到要使其成为窗口上最顶层的项的列表的底部。

现在，我们已创建用户界面，我们需要公开 UI 项，以便 Xamarin.tvOS 可以访问和中与之交互C#代码。

### <a name="accessing-the-controls-in-the-code-behind"></a>访问代码隐藏中的控件

有两种主要方法来访问已在 iOS 设计器从代码中添加的控件：

* 在控件上创建一个事件处理程序。
* 为控件提供一个名称，以便我们稍后可以引用它。

任何一种方法添加时中的分部类`ViewController.designer.cs`将更新以反映所做的更改。 这样，你可以然后访问视图控制器中的控件。

### <a name="creating-an-event-handler"></a>创建事件处理程序

在此示例应用程序中，单击按钮时我们希望_内容_发生这种情况，因此需要添加到按钮上的特定事件的事件处理程序。 若要设置此功能，请执行以下操作：

1. 在 Xamarin iOS 设计器中，选择视图控制器上的按钮。
2. 在属性面板中，选择**事件**选项卡：

    [![](hello-tvos-images/event1.png "事件选项卡")](hello-tvos-images/event1.png#lightbox)
3. 找到 TouchUpInside 事件，并为其提供事件处理程序名为`Clicked`:

    [![](hello-tvos-images/event2.png "TouchUpInside 事件")](hello-tvos-images/event2.png#lightbox)
4. 当您按下**Enter**，则**ViewController**.cs 文件将打开，建议您在代码中的事件处理程序的位置。 使用键盘上的箭头键来设置的位置：

    [![](hello-tvos-images/event3.png "将位置设置")](hello-tvos-images/event3.png#lightbox)
5. 这将创建的分部方法，如下所示：

    [![](hello-tvos-images/event4.png "分部方法")](hello-tvos-images/event4.png#lightbox)

现在我们就可以开始添加一些代码来允许对函数的按钮。

### <a name="naming-a-control"></a>命名控件

单击按钮时，应更新标签基于的点击数。 若要执行此操作，我们将需要访问代码中的标签。 这是为其指定名称。 请执行以下操作：

1. 打开情节提要，然后选择底部的视图控制器的标签。
2. 在属性面板中，选择**小组件**选项卡：

    [![](hello-tvos-images/name1.png "选择小组件选项卡")](hello-tvos-images/name1.png#lightbox)
3. 下**标识 > 名称**，添加`ClickedLabel`:

    [![](hello-tvos-images/name2.png "设置 ClickedLabel")](hello-tvos-images/name2.png#lightbox)

我们现已准备好开始更新标签 ！

### <a name="how-controls-are-accessed"></a>如何访问控件

如果选择`ViewController.designer.cs`中**解决方案资源管理器**您将能够看到如何`ClickedLabel`标签并`Clicked`事件处理程序已映射到**插座**和**操作**在C#:

[![](hello-tvos-images/accesscontrol.png "输出口和操作")](hello-tvos-images/accesscontrol.png#lightbox)

你可能注意`ViewController.designer.cs`是一个分部类，以便 Visual Studio for Mac 不必修改`ViewController.cs`它会覆盖我们已对类进行任何更改。

公开这种方式，UI 元素，可在视图控制器中访问它们。

通常情况下将永远不需要打开`ViewController.designer.cs`自己，此处提供仅为了。

<a name="Writing-the-Code" />

## <a name="writing-the-code"></a>编写代码

使用我们创建的用户界面和通过向代码公开其 UI 元素**输出口**并**操作**，我们是终于准备好编写代码来向提供程序功能。

在我们的应用程序，每次单击第一个按钮时，我们将更新我们的标签以显示单击该按钮的次数。 若要实现此目的，我们需要打开`ViewController.cs`文件进行编辑中双击**Solution Pad**:

[![](hello-tvos-images/code01.png "在 Solution Pad")](hello-tvos-images/code01.png#lightbox)

首先，我们需要创建中的类级变量我们`ViewController`类，以跟踪已发生的点击数。 编辑类定义，使其类似于如下所示：

```csharp
using System;
using Foundation;
using UIKit;

namespace Hello_tvOS
{
    public partial class ViewController : UIViewController
    {
        private int numberOfTimesClicked = 0;
        ...
```

接下来，在同一个类 (`ViewController`)，我们需要重写**ViewDidLoad**方法并添加一些代码来设置我们的标签的初始消息：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Set the initial value for the label
    ClickedLabel.Text = "Button has not been clicked yet.";
}
```

我们需要使用`ViewDidLoad`，而不是等其他方法`Initialize`，因为`ViewDidLoad`称为*后*OS 已加载并实例化从用户界面`.storyboard`文件。 如果我们尝试访问标签控件前的`.storyboard`、 文件已完全加载和实例化，则会得到`NullReferenceException`错误由于会尚未创建标签控件。

接下来，我们需要添加代码以响应用户单击按钮。 将以下代码添加到分部类为我们创建：

```csharp
partial void Clicked (UIButton sender)
{
    ClickedLabel.Text = string.Format("The button has been clicked {0} time{1}.", ++numberOfTimesClicked, (numberOfTimesClicked
```

每当用户单击按钮，将调用此代码。

一切就绪后，我们现已准备用于生成和测试我们 Xamarin.tvOS 的应用程序。

## <a name="testing-the-application"></a>测试应用程序

就可以生成并运行应用程序以确保它按预期运行。 我们可以生成并运行所有在一个步骤中，或者我们可以把它生成而无需运行它。

每当我们构建一个应用程序，我们可以选择我们希望生成哪种：

-   **调试**– 调试版本会编译到 ' 可用于调试应用程序运行时所发生的额外元数据 （应用程序） 文件。
-   **释放**– 发布版本也会创建文件，但它不包括调试信息，因此它更小并且更快地执行。  

您可以选择从生成的类型**配置选择器**左上角的 Visual Studio for Mac 屏幕：

[![](hello-tvos-images/run01.png "选择生成的类型")](hello-tvos-images/run01.png#lightbox)

### <a name="building-the-application"></a>生成应用程序

在本例中，我们只是想调试版本，因此我们必须确保**调试**处于选中状态。 让我们构建我们的应用程序首先通过按**⌘ B**，或从**构建**菜单中，选择**生成所有**。

如果没有任何错误，则将看到**生成成功**Visual Studio 中的 Mac 的状态栏的消息。 如果出现了错误，查看你的项目，并确保已正确执行的步骤。 首先确认你代码 （Xcode 和 Visual Studio for Mac 中） 与本教程中匹配的代码。

### <a name="running-the-application"></a>运行应用程序

若要运行该应用程序，我们有三个选项：

-  按 **⌘+Enter**。
-  在“运行”  菜单中，选择“调试”  。
-  单击 Visual Studio for Mac 工具栏中的“播放”  按钮（位于“解决方案资源管理器”  上方）。

（如果它尚未生成），将生成应用程序、 将启动在调试模式下，tvOS 模拟器中的启动和应用程序将启动并显示主界面窗口：

[![示例应用主页屏幕](hello-tvos-images/run03.png)](hello-tvos-images/run03.png#lightbox)

从**硬件**菜单中选择**显示 Apple TV 远程**以便您可以控制模拟器。

[![](hello-tvos-images/run04.png "选择显示 Apple TV 远程操作")](hello-tvos-images/run04.png#lightbox)

使用模拟器的遥控器上，如果单击按钮几次的标签时，应使用计数来更新：

[![](hello-tvos-images/run05.png "具有更新计数标签")](hello-tvos-images/run05.png#lightbox)

祝贺你！ 我们讨论了很多方面，但如果您从开始到结束遵循本教程中，现在应具有深入了解 Xamarin.tvOS 应用，以及用来创建它们的工具组件。

## <a name="where-to-next"></a>下一步？

开发几个难题由于用户和用户界面 （它不在用户的手在室内） 限制 tvOS 之间断开连接的 Apple TV 应用提供对应用程序大小和存储。

因此，我们强烈建议，你的阅读以下文档之前跳转到 Xamarin.tvOS 应用的设计：

- [TvOS 9 简介](~/ios/tvos/platform/tvos9.md)– 这篇文章介绍的所有新的和修改 Api 和 tvOS 9 中的可用功能适用于 Xamarin.tvOS 开发人员。
- [使用导航和焦点](~/ios/tvos/app-fundamentals/navigation-focus.md)– Xamarin.tvOS 应用程序的用户将不与它的接口直接作为 ios 其中用户点击映像在设备的屏幕上，但间接内聊天室使用 Siri 远程交互。 本文介绍的概念，焦点以及如何使用它来处理 Xamarin.tvOS 应用的用户界面中的导航。
- [Siri 远程和蓝牙控制器](~/ios/tvos/platform/remote-bluetooth.md)– 用户将与 Apple TV 和 Xamarin.tvOS 应用进行交互，是通过包含 Siri 远程的主要方式。 如果您的应用程序是一个游戏，可以根据需要生成的第三方，所做的支持 iOS (MFI) 蓝牙游戏控制器在您的应用程序中。 本文介绍如何支持新的 Siri 远程和蓝牙游戏控制器 Xamarin.tvOS 应用程序中。
- [资源和数据存储](~/ios/tvos/app-fundamentals/resources-data-storage.md)– 与不同的是 iOS 设备，新的 Apple TV 不提供对 tvOS 应用持久的本地存储。 因此，如果 Xamarin.tvOS 应用需要继续将信息 （如用户首选项） 它必须存储和从 iCloud 检索该数据。 本文介绍如何使用资源和 Xamarin.tvOS 应用中的持久数据存储。
- [使用图标和图像](~/ios/tvos/app-fundamentals/icons-images.md)– 创建极富感染力的图标和图像是开发你的 Apple TV 应用的引人入胜的用户体验的重要组成部分。 本指南将介绍如何创建并将包含所需的图形资产为 Xamarin.tvOS 应用所需的步骤。
- [用户界面](~/ios/tvos/user-interface/index.md)– 与 xamarin.tvos 一起使用时，常规用户体验 (UX) 覆盖率包括用户界面 (UI) 控件，将使用 Xcode 的 Interface Builder 和 UX 设计原则。
- [部署和测试](~/ios/tvos/deploy-test/index.md)– 本部分介绍了用于测试，以及如何应用以将其分配的主题。 此处的主题包括用于调试，测试人员和如何发布到 Apple TV App Store 的应用程序部署到的工具等。

如果遇到任何问题与 xamarin.tvos 一起使用时，请参阅我们[故障排除](~/ios/tvos/troubleshooting.md)文档上的列表中的已知问题和解决方案。

## <a name="summary"></a>总结

本文提供使用开发应用程序适用于 tvOS Visual Studio for Mac 通过创建一个简单 Hello，tvOS 应用的快速入门。 它包括 tvOS 设备预配，接口创建、 编码 tvOS 和 tvOS 模拟器上测试基础的知识。


## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [适用于 tvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
- [构建适用于使用 Xamarin （视频） 的 tvOS 应用](https://university.xamarin.com/lightninglectures/tvos-with-xamarin)
