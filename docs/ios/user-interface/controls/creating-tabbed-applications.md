---
title: 选项卡栏和 Xamarin.iOS 中的选项卡栏控制器
description: 本文档介绍 iOS 选项卡栏控制器和如何使用 Xamarin.iOS。 它演示了如何设置 UITabBarController、 处理图像、 设置徽章值、 处理事件，和的详细信息。
ms.prod: xamarin
ms.assetid: 7C772899-2900-F139-D642-F3C4F3F14DDC
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 25d8563288cce614bc2823b0146e5121688c6f02
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76725478"
---
# <a name="tab-bars-and-tab-bar-controllers-in-xamarinios"></a>Xamarin 中的选项卡和制表符栏控制器

在 iOS 中使用选项卡式应用程序以支持多个屏幕可以访问其中的用户界面不按特定顺序。 通过 `UITabBarController` 类，应用程序可以很容易地为此类多屏幕方案提供支持。 `UITabBarController` 负责多屏幕管理，使应用程序开发人员可以专注于每个屏幕的详细信息。

通常情况下，选项卡式应用程序是用 `UITabBarController` 是主窗口的 `RootViewController` 生成的。 但是，与一些其他代码，选项卡式应用程序还可在其他一些初始屏幕，如应用程序首先显示登录屏幕，跟选项卡式界面的方案的连续。

此页讨论了这两种方案：选项卡位于应用程序视图层次结构的根中，同时还讨论非`RootViewController` 方案。

## <a name="introducing-uitabbarcontroller"></a>引入 UITabBarController

`UITabBarController` 支持通过以下方式进行选项卡式应用程序开发：

- 允许多个控制器添加到它。
- 通过 `UITabBar` 类提供选项卡式用户界面，以允许用户在控制器和视图之间切换。

通过 `ViewControllers` 属性将控制器添加到 `UITabBarController`，该属性是 `UIViewController` 数组。 `UITabBarController` 本身处理加载适当的控制器并根据所选的选项卡显示其视图。

选项卡是 `UITabBarItem` 类的实例，包含在 `UITabBar` 实例中。 每个 `UITabBar` 实例可通过每个选项卡中的控制器的 `TabBarItem` 属性进行访问。

若要了解如何使用 `UITabBarController`，我们来演练如何构建使用一个应用程序的简单应用程序。

## <a name="tabbed-application-walkthrough"></a>选项卡式应用程序演练

在本演练中我们将创建以下应用程序：

[![示例选项卡式应用](creating-tabbed-applications-images/00-app.png)](creating-tabbed-applications-images/00-app.png#lightbox)

尽管 Visual Studio for Mac 中已经有了一个选项卡式应用程序模板，但在此示例中，这些说明适用于空项目，以更好地了解应用程序的构建方式。

### <a name="creating-the-application"></a>创建应用程序

首先创建一个新的应用程序。

选择该**文件 > 新建 > 解决方案**"菜单项 Visual Studio for Mac 并选择" **IOS > 应用程序 > 空项目**模板，将项目命名为 `TabbedApplication`，如下所示：

[![](creating-tabbed-applications-images/newsolution1.png "Select the Empty Project template")](creating-tabbed-applications-images/newsolution1.png#lightbox)

[![](creating-tabbed-applications-images/newsolution2.png "Name the project TabbedApplication")](creating-tabbed-applications-images/newsolution2.png#lightbox)

### <a name="adding-the-uitabbarcontroller"></a>添加 UITabBarController

接下来，通过选择 "文件" **> 新文件**并选择 "**常规：空类**模板" 来添加一个空类。 将该文件命名 `TabController` 如下所示：

[![](creating-tabbed-applications-images/02-newclass.png "Add the TabController class")](creating-tabbed-applications-images/02-newclass.png#lightbox)

`TabController` 类将包含将管理 `UIViewControllers`数组的 `UITabBarController` 的实现。 当用户选择某个选项卡时，`UITabBarController` 将负责为相应的视图控制器呈现视图。

若要实现 `UITabBarController` 需要执行以下操作：

1. 将 `TabController` 的基类设置为 `UITabBarController`。
1. 创建要添加到 `TabController` `UIViewController` 实例。
1. 将 `UIViewController` 实例添加到分配给 `TabController` 的 `ViewControllers` 属性的数组。

将以下代码添加到 `TabController` 类，以实现以下步骤：

```csharp
using System;
using UIKit;

namespace TabbedApplication {
    public class TabController : UITabBarController {

        UIViewController tab1, tab2, tab3;

        public TabController ()
        {
            tab1 = new UIViewController();
            tab1.Title = "Green";
            tab1.View.BackgroundColor = UIColor.Green;

            tab2 = new UIViewController();
            tab2.Title = "Orange";
            tab2.View.BackgroundColor = UIColor.Orange;

            tab3 = new UIViewController();
            tab3.Title = "Red";
            tab3.View.BackgroundColor = UIColor.Red;

            var tabs = new UIViewController[] {
                tab1, tab2, tab3
            };

            ViewControllers = tabs;
        }
    }
}
```

请注意，对于每个 `UIViewController` 实例，我们设置了 `UIViewController`的 `Title` 属性。 将控制器添加到 `UITabBarController`时，`UITabBarController` 将读取每个控制器的 `Title`，并将其显示在关联的选项卡的标签上，如下所示：

[![](creating-tabbed-applications-images/00-app.png "The sample app run")](creating-tabbed-applications-images/00-app.png#lightbox)

#### <a name="setting-the-tabcontroller-as-the-rootviewcontroller"></a>设置为 RootViewController TabController

控制器在选项卡中的排列顺序对应于它们添加到 `ViewControllers` 数组的顺序。

若要获取作为第一个屏幕加载的 `UITabController`，需要将其设置为窗口的 `RootViewController`，如下面的 `AppDelegate`代码所示：

```csharp
[Register ("AppDelegate")]
public partial class AppDelegate : UIApplicationDelegate
{
    UIWindow window;
    TabController tabController;

    public override bool FinishedLaunching (UIApplication app, NSDictionary options)
    {
        window = new UIWindow (UIScreen.MainScreen.Bounds);

        tabController = new TabController ();
        window.RootViewController = tabController;

        window.MakeKeyAndVisible ();

        return true;
    }
}
```

如果现在运行应用程序，则默认情况下，将使用所选的第一个选项卡加载 `UITabBarController`。 选择任何其他选项卡会导致关联控制器的视图由 `UITabBarController,` 显示，如下所示，最终用户已选择了第二个选项卡：

![显示的第二个选项卡](creating-tabbed-applications-images/03-secondtab-sml.png)

### <a name="modifying-tabbaritems"></a>修改 TabBarItems

现在，我们有了一个正在运行的选项卡应用程序，接下来修改 `TabBarItem` 以更改显示的图像和文本，并将徽章添加到其中一个选项卡。

#### <a name="setting-a-system-item"></a>设置系统项

首先，让我们来设置要使用系统项的第一个选项卡。 在 `TabController`的构造函数中，删除为 `tab1` 实例设置控制器 `Title` 的行，并将其替换为以下代码，以设置控制器的 `TabBarItem` 属性：

```csharp
tab1.TabBarItem = new UITabBarItem (UITabBarSystemItem.Favorites, 0);
```

使用 `UITabBarSystemItem`创建 `UITabBarItem` 时，iOS 会自动提供标题和图像，如以下屏幕截图中所示，在第一个选项卡上显示 "**收藏夹**" 图标和标题：

 ![带有星号图标的第一个选项卡](creating-tabbed-applications-images/04a-tabimage-sml.png)

#### <a name="setting-the-image"></a>设置图像

除了使用系统项，还可以将 `UITabBarItem` 的标题和图像设置为自定义值。 例如，更改用于设置名为 `tab2` 的控制器的 `TabBarItem` 属性的代码，如下所示：

```csharp
tab2 = new UIViewController ();
tab2.TabBarItem = new UITabBarItem ();
tab2.TabBarItem.Image = UIImage.FromFile ("second.png");
tab2.TabBarItem.Title = "Second";
tab2.View.BackgroundColor = UIColor.Orange;
```

上面的代码假定已将名为 `second.png` 的图像添加到项目的根目录（或**资源**目录）。 若要支持所有屏幕密度，需要三个图像，如下所示：

![添加到项目中的图像](creating-tabbed-applications-images/tabbedimages7new.png)

有关正确维度的指导，请参阅[Apple 自定义图标页面](https://developer.apple.com/design/human-interface-guidelines/ios/icons-and-images/custom-icons/)的**选项卡栏图标大小**部分。 建议大小取决于图像的样式（圆形、方形、宽或高）。

`Image` 属性只需设置为**第二个 .png**文件名，则 iOS 会在需要时自动加载更高分辨率的文件。 有关详细信息，请参阅使用[图像](~/ios/app-fundamentals/images-icons/index.md)指南。 默认选项卡栏项都是灰色、 用蓝色的浅色选择时。

#### <a name="overriding-the-title"></a>覆盖标题

当 `TabBarItem`上直接设置 `Title` 属性时，它将覆盖控制器本身上为 `Title` 设置的任何值。

此屏幕截图中的第二个（中间）选项卡显示自定义标题和图像：

![第二个选项卡带有正方形图标](creating-tabbed-applications-images/05-customtab-sml.png)

#### <a name="setting-the-badge-value"></a>设置徽章值

一个选项卡还可以显示一个徽章。 例如，添加代码以将一个徽章设置第三个选项卡上的以下行：

```csharp
tab3.TabBarItem.BadgeValue = "Hi";
```

运行此命令会导致具有如下所示的选项卡的左上角中的字符串"Hi"的红色标签：

![带有徽标的第二个选项卡](creating-tabbed-applications-images/06-badge-sml.png)

徽章通常用于显示未读邮件的数字指示新项。 若要删除徽章，请将 `BadgeValue` 设置为 null，如下所示：

```csharp
tab3.TabBarItem.BadgeValue = null;
```

## <a name="tabs-in-non-rootviewcontroller-scenarios"></a>非 RootViewController 方案中的选项卡

在上面的示例中，我们演示了如何在窗口 `RootViewController` 时使用 `UITabBarController`。 在此示例中，我们将检查如何使用不是 `RootViewController` 的 `UITabBarController`，并演示如何使用情节提要来创建它。

### <a name="initial-screen-example"></a>初始屏幕示例

对于这种情况，初始屏幕将从不是 `UITabBarController`的控制器加载。 如果用户通过点击按钮与屏幕进行交互，则会将同一个视图控制器加载到 `UITabBarController`中，然后向用户显示该控制器。 下面的屏幕截图显示了应用程序流：

[![](creating-tabbed-applications-images/inital-screen-application.png "This screenshot shows the application flow")](creating-tabbed-applications-images/inital-screen-application.png#lightbox)

首先，此示例中的新应用程序。 同样，我们将使用**iPhone > 应用 > 空项目C#（）** 模板，这一次将项目命名 `InitialScreenDemo`。

在此示例中，演示图板用于设计视图控制器。 添加情节提要：

- 右键单击项目名称，然后选择 "**添加 > 新文件**"。

- 出现 "新建文件" 对话框时，导航到 " **iOS > 空 IPhone 情节提要**"。

接下来，我们将这个新的情节提要**mainstoryboard.storyboard** ，如下所示：

[![](creating-tabbed-applications-images/new-file-dialog.png "Add a MainStoryboard file to the project")](creating-tabbed-applications-images/new-file-dialog.png#lightbox)

将情节提要添加到[之前的非](~/ios/user-interface/storyboards/index.md)情节提要文件时，有几个需要注意的重要步骤。 这些位置包括：

1. 将情节提要名称添加到 `Info.plist`的**主界面**部分：

    [![](creating-tabbed-applications-images/project-options.png "Set the Main Interface to MainStoryboard")](creating-tabbed-applications-images/project-options.png#lightbox)
1. 在 `App Delegate`中，用以下代码替代窗口方法：

    ```csharp
    public override UIWindow Window {
        get;
        set;
    }
    ```

我们将此示例需要三个视图控制器。 名为 `ViewController1`的名称将用作初始视图控制器和第一个选项卡。另两个命名 `ViewController2` 和 `ViewController3`，分别在第二个和第三个选项卡中使用。

以打开 mainstoryboard.storyboard 文件中，打开设计器拖到设计图面上的三个视图控制器。 我们希望其中每个视图控制器都具有与上述名称对应的类，因此，在 "**标识 > 类**" 下键入其名称，如下面的屏幕截图中所示：

[![](creating-tabbed-applications-images/class-name.png "Set the Class to ViewController1")](creating-tabbed-applications-images/class-name.png#lightbox)

Visual Studio for Mac 将自动生成的类和设计器所需的文件，这可以看到在解决方案面板中，如下图所示：

[![](creating-tabbed-applications-images/solution-pad2.png "Auto-generated files in the project")](creating-tabbed-applications-images/solution-pad2.png#lightbox)

#### <a name="creating-the-ui"></a>创建 UI

接下来，我们将为每个 ViewController 的视图，使用 Xamarin iOS 设计器创建一个简单的用户界面。

我们想要从右侧的 **"工具箱**" 中将 `Label` 和 `Button` 拖到 "ViewController1"。 接下来我们将使用属性面板来编辑在名称和文本的控件所示：

- **标签**： `Text` = **一个**
- **按钮**： `Title` = **用户执行一些初始操作**

我们将在 `TouchUpInside` 事件中控制按钮的可见性，我们需要在代码隐藏中引用它。 让我们用 Properties Pad 中的 `aButton`**名称**标识它，如以下屏幕截图中所示：

[![](creating-tabbed-applications-images/abutton-properties.png "Set the Name to aButton in the Properties Pad")](creating-tabbed-applications-images/abutton-properties.png#lightbox)

设计图面上应类似于下面的屏幕截图：

[![](creating-tabbed-applications-images/design-surface1.png "Your Design Surface should now look similar to this screenshot")](creating-tabbed-applications-images/design-surface1.png#lightbox)

接下来，通过向每个标签添加一个标签，并分别将文本更改为 "2" 和 "3"，将更多详细信息添加到 `ViewController2` 和 `ViewController3`。 它突出显示了向用户正在查看的选项卡/视图。

#### <a name="wiring-up-the-button"></a>设置按钮的连线

当应用程序首次启动时，将加载 `ViewController1`。 当用户点击按钮时，将隐藏按钮，并在第一个选项卡中使用 `ViewController1` 实例加载 `UITabBarController`。

当用户释放 `aButton`时，我们希望触发 TouchUpInside 事件。 选择 "" 按钮，然后在 "属性" 面板的 "**事件" 选项卡**中声明事件处理程序– `InitialActionCompleted` –因此可以在代码中引用它。 下面的屏幕截图所示：

[![](creating-tabbed-applications-images/event-handler.png "When the user releases the aButton, trigger a TouchUpInside event")](creating-tabbed-applications-images/event-handler.png#lightbox)

现在，我们需要告知视图控制器在事件激发时隐藏按钮 `InitialActionCompleted`。 在 `ViewController1`中，添加以下分部方法：

```csharp
partial void InitialActionCompleted (UIButton sender)
{
    aButton.Hidden = true;  
}
```

保存该文件，并运行应用程序。 应该会看到其中一个出现的屏幕和上不显示修饰按钮。

#### <a name="adding-the-tab-bar-controller"></a>添加选项卡栏控制器

现在，我们有初始视图按预期方式工作。 接下来，我们想要将其添加到 `UITabBarController`，以及视图2和3。 让我们在设计器中打开情节提要。

在 "**工具箱**" 中，搜索 "控制器 & 对象" 下的**选项卡**，并将其拖到 "Design Surface"。 您可以看到以下屏幕截图中，选项卡栏控制器是无用户界面，并因此使其与默认情况下的两个视图控制器：

[![](creating-tabbed-applications-images/tabbarcontroller.png "Adding a Tab Bar Controller to the layout")](creating-tabbed-applications-images/tabbarcontroller.png#lightbox)

通过选择底部的黑色栏并按 delete 键删除这些新的视图控制器。

在我们的情节提要，我们可以使用 segues 设计来处理 TabBarController 和我们的视图控制器之间的转换。 后与初始视图进行交互，我们想要将其加载到 TabBarController 向用户显示。 让我们对此进行设置设计器中。

**按住 Ctrl**并从按钮**拖动**到 TabBarController。 鼠标弹起上将显示一个上下文菜单。 我们想要使用模式的 segue。

若要设置每个选项**卡，请按下从 TabBarController**到每个视图控制器的顺序，从1到3，然后从上下文菜单中选择 "关系 **" 选项卡**，如下所示：

[![](creating-tabbed-applications-images/context-menu.png "Select the Tab Relationship")](creating-tabbed-applications-images/context-menu.png#lightbox)

你的情节提要应类似于下面的屏幕截图：

[![](creating-tabbed-applications-images/segue-layout.png "The Storyboard should resemble this screenshot")](creating-tabbed-applications-images/segue-layout.png#lightbox)

如果我们单击其中一个选项卡栏项并探索属性面板，可以看到大量不同的选项，如下图所示：

[![](creating-tabbed-applications-images/properties-panel.png "Setting the tab options in the Properties Explorer")](creating-tabbed-applications-images/properties-panel.png#lightbox)

使用此工具可以编辑某些属性，如徽章、标题和 iOS 标识符，等等。

如果我们保存并运行应用程序现在，我们会发现该按钮 ViewController1 实例加载到 TabBarController 时再次出现。 让我们来解决此问题通过检查以查看当前视图是否具有父视图控制器。 如果是这样，我们知道我们位于 TabBarController，且因此应隐藏按钮。 让我们将以下代码添加到 ViewController1 类：

```csharp
public override void ViewDidLoad ()
{
    if (ParentViewController != null){
        aButton.Hidden = true;
    }
}
```

加载时在应用程序运行与用户点击第一个屏幕，UITabBarController 按钮，从放置在第一个选项卡中，如下所示的第一个屏幕视图：

[![示例应用程序输出](creating-tabbed-applications-images/first-view-sml.png)](creating-tabbed-applications-images/first-view.png#lightbox)

## <a name="summary"></a>摘要

本文介绍了如何在应用程序中使用 `UITabBarController`。 我们介绍了如何将控制器加载到每个选项卡，以及如何在选项卡上设置属性，此类的标题、 图像和徽章。 然后，使用演示图板，当它不是窗口的 `RootViewController` 时，如何在运行时加载 `UITabBarController`。

## <a name="related-links"></a>相关链接

- [创建选项卡式应用程序（示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/creatingtabbedapplications)
- [图像 .zip](https://github.com/xamarin/ios-samples/blob/master/CreatingTabbedApplications/Resources/images.zip?raw=true)
- [UITabBarController 类引用](https://developer.apple.com/library/ios/#documentation/uikit/reference/UITabBarController_Class/Reference/Reference.html)
