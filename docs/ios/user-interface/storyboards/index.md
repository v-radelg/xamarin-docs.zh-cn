---
title: Xamarin 中的情节提要简介
description: 本文档介绍了 Xamarin 中的情节提要。 其中介绍了如何使用情节提要来定义用户界面 segue，以及如何使用 iOS 设计器编辑情节提要文件。
ms.prod: xamarin
ms.assetid: A3339BD2-9F56-7965-25F5-4B7C991EB775
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/22/2017
ms.openlocfilehash: cf181cf6c27476b7073073467ef186c352645e39
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768882"
---
# <a name="introduction-to-storyboards-in-xamarinios"></a>Xamarin 中的情节提要简介

在本指南中，我们将介绍什么是情节提要，并检查部分关键组件（例如 Segue）。 我们将介绍如何创建和使用情节提要，以及它们对于开发人员有哪些优点。

在 Apple 将情节提要文件格式引入到 iOS 应用程序的 UI 的可视化表示形式之前，开发人员已为每个视图控制器创建 XIB 文件，并在每个视图之间手动设计导航。  使用情节提要，开发人员可以在设计图面上定义视图控制器和它们之间的导航，并使应用程序的用户界面具有所见即所得的编辑。

可以使用 Xamarin iOS 设计器创建、打开和编辑情节提要。 本指南还将演练如何使用设计器来创建情节提要，同时使用C#来编程导航。

## <a name="requirements"></a>要求

演示图板可与 Visual Studio for Mac 中的 iOS 设计器或安装有 Xamarin 工作负荷的 Visual Studio 2017 一起使用。

## <a name="what-is-a-storyboard"></a>什么是情节提要？

情节提要是应用程序中所有屏幕的可视表示形式。 它包含一系列场景，每个场景表示一个*视图控制器*及其*视图*。 这些视图可能包含允许用户与应用程序进行交互的对象和[控件](~/ios/user-interface/controls/index.md)。 此视图和控件（或*子视图*）的集合称为*内容视图层次结构*。 场景由 segue 对象连接，这些对象表示视图控制器之间的转换。 通常通过在初始视图上的对象和连接视图之间创建 segue 来实现此目的。 设计图面上的关系在下图中进行了说明：

 [![](images/storyboardsview.png "此图中演示了设计图面上的关系")](images/storyboardsview.png#lightbox)

如图所示，演示图板会将每个场景布局为已呈现的内容，并说明它们之间的连接。  值得一提的是，当我们谈到 iPhone 上的场景时，可以放心地假定情节提要上的一个*场景*等于设备上的一*屏*内容。 但是，对于 iPad，可以同时出现多个场景–例如，使用 Segue 视图控制器。

使用情节提要创建应用程序的 UI 时，尤其是在使用 Xamarin 时，有很多好处。 首先，它是 UI 的可视化表示形式，因为所有对象（包括[自定义控件](~/ios/user-interface/designer/ios-designable-controls-overview.md)）都是在设计时呈现的。 这意味着在生成或部署应用程序之前，可以可视化其外观和流。 如图所示。 我们可以从设计图面中快速了解有多少场景、每个视图的布局以及所有相关内容。 这就是使情节提要如此强大的功能。

使用情节提要可以更好地管理事件，特别是在使用 iOS 设计器时。 大多数 UI 控件将在 Properties Pad 中有可能的事件列表。 此事件处理程序可在此处添加并在视图控制器类的分部方法中完成。

情节提要的内容存储为 XML 文件。 在生成时，所有`.storyboard`文件都编译为二进制文件（称为 nibs）。 在运行时，将初始化并实例化这些 nibs 以创建新视图。

## <a name="segues"></a>Segue

*Segue*或*Segue 对象*用于在 iOS 开发中表示场景间的转换。 若要创建 segue，请按住**Ctrl**键，然后单击-将一个场景拖动到另一个场景。 当我们拖动鼠标时，将显示一个蓝色连接器，指示 segue 在下图中所示的位置：

 [![](images/createsegue.png "此时将显示一个蓝色连接器，指示 segue 在此图中的显示位置")](images/createsegue.png#lightbox)

鼠标悬停时，将显示一个菜单，让我们为 segue 选择操作。 它看起来可能与下图类似： 

**IOS 之前8类和大小类**：

[![](images/segue1.png "无大小类的操作 Segue 下拉列表")](images/segue1.png#lightbox)

**使用大小类和自适应 Segue 时**：

[![](images/16new.png "带有大小类的操作 Segue 下拉列表")](images/16new.png#lightbox)

> [!IMPORTANT]
> 如果你使用的是适用于 Windows 虚拟机的 VMWare，则在默认情况下，按下右键单击将映射为_右键单击_鼠标按钮。 若要创建 Segue，请通过**首选项** > **键盘 & 鼠标** > **鼠标快捷方式**编辑键盘首选项，并重新映射**辅助按钮**，如下所示：
> 
> [![](images/image22.png "键盘和鼠标首选项设置")](images/image22.png#lightbox)
> 
> 现在应能够在视图控制器之间添加 segue。

存在不同类型的转换，每个转换都控制如何向用户显示新视图控制器，以及如何与情节提要中的其他视图控制器交互。 如下所述。 还可以为 segue 对象划分子类，以实现自定义转换：

- **显示/推送**–推送 segue 将视图控制器添加到导航堆栈。 它假定发起推送的视图控制器与要添加到堆栈中的视图控制器属于同一导航控制器。 这与相同`pushViewController` ，通常在屏幕上的数据之间存在某种关系时使用。 使用 push segue，你可以使用一个导航栏，其中包含向堆栈上的每个视图添加的 "后退" 按钮和标题，从而允许在视图层次结构中向下钻取导航。
- **Modal** –模式 segue 在项目中的任意两个视图控制器之间创建关系，并在其中显示动画过渡的选项。 进入 "查看" 时，子视图控制器会完全掩盖父视图控制器。 不同于推送 segue，后者将向我们添加一个 "后退" 按钮;使用模式 segue `DismissViewController`时，必须使用模式，才能返回到上一个视图控制器。
- **Custom** –可以将任意自定义 segue 创建为的子类`UIStoryboardSegue`。
- **展开**–展开 segue 可用于通过推送或模式 segue 导航回来–例如，通过关闭按模式显示的视图控制器。 除此之外，你不仅可以通过一个展开操作来遍历一系列推送和模式 segue，并返回导航层次结构中的多个步骤。 若要了解如何在 iOS 中使用展开 segue，请阅读[创建展开 segue](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/storyboard/unwind_segue)食谱。
- **Sourceless** – Sourceless segue 指示包含初始视图控制器的场景，从而显示用户首先看到的视图。 它由如下所示的 segue 表示：  

    [![](images/sourcelesssegue.png "Sourceless segue")](images/sourcelesssegue.png#lightbox)

### <a name="adaptive-segue-types"></a>自适应 Segue 类型

 iOS 8 引入了[大小类](~/ios/user-interface/storyboards/unified-storyboards.md#size-classes)，以允许 iOS 情节提要文件与所有可用的屏幕大小一起使用，使开发人员能够为所有 iOS 设备创建一个 UI。 默认情况下，所有新的 Xamarin iOS 应用程序都将使用大小类。 若要从较旧的项目使用大小类，请参阅[统一情节提要指南简介](~/ios/user-interface/storyboards/unified-storyboards.md)。 

使用大小类的任何应用程序也将使用新的[*自适应 segue*](~/ios/user-interface/storyboards/unified-storyboards.md)。 使用大小类时，请记住，我们不会直接指定该使用 iPhone 或 iPad。 换句话说，我们创建一个 UI，该 UI 始终外观相同，而不考虑它需要使用的实际空间。 自适应 Segue 通过判断环境，并确定如何以最佳方式呈现内容。 自适应 Segue 如下所示： 

[![](images/adaptivesegue.png "自适应 Segue 下拉列表")](images/adaptivesegue.png#lightbox)

|Segue|描述|
|--- |--- |
|显示|这与推送 segue 非常相似，但它会将屏幕内容纳入考虑。|
|显示详细信息|如果应用显示主视图和详细信息视图（例如，在 iPad 上的拆分视图控制器中），则内容将替换详细信息视图。 如果应用仅显示主节点或详细信息，则内容将替换视图控制器堆栈的顶部。|
|展示|这类似于模式 segue，并允许选择显示和转换样式。|
|Segue 演示文稿|这会以 segue 的形式呈现内容|

### <a name="transferring-data-with-segues"></a>通过 Segue 传输数据

Segue 的好处并不是通过转换来完成的。 它们还可用于管理视图控制器之间的数据传输。 这是通过重写初始`PrepareForSegue`视图控制器上的方法以及自行处理数据实现的。 触发 segue 时（例如，使用按钮按下），应用程序将调用此方法，以便在进行任何导航*之前*准备新的视图控制器。 下面的代码（来自[Phoneword](https://docs.microsoft.com/samples/xamarin/ios-samples/hello-ios)示例）演示了这一点： 

```csharp
public override void PrepareForSegue (UIStoryboardSegue segue, 
NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    var callHistoryController = segue.DestinationViewController 
                                  as CallHistoryController;

    if (callHistoryController != null) {
        callHistoryController.PhoneNumbers = PhoneNumbers;
    }
}
```

在此示例中， `PrepareForSegue`当用户触发 segue 时，将调用方法。 首先，必须创建 "接收" 视图控制器的实例，并将其设置为 segue 的目标视图控制器。 这是通过下面的代码行完成的：

```csharp
var callHistoryController = segue.DestinationViewController as CallHistoryController;
```

方法现在能够在上`DestinationViewController`设置属性。 在此示例中，我们通过将名`PhoneNumbers`为的`CallHistoryController`列表传递给，并将其分配给同名的对象来利用这一点：

```csharp
if (callHistoryController != null) {
        callHistoryController.PhoneNumbers = PhoneNumbers;
    }
```

转换完成后，用户将看到带有填充列表的`CallHistoryController` 。

## <a name="adding-a-storyboard-to-a-non-storyboard-project"></a>将情节提要添加到非情节提要项目

有时，可能需要将情节提要添加到以前的非情节提要文件。 执行此操作后，可以执行以下步骤来简化 Visual Studio for Mac：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 通过浏览到**文件 > 新文件 > iOS > 情节提要**来创建新的情节提要文件，如下所示： 
    
    [![](images/new-storyboard-xs.png "\"新建文件\" 对话框")](images/new-storyboard-xs.png#lightbox)

2. 将情节提要名称添加到**info.plist**的**Main Interface**节，如下所示：
    
    [![](images/infoplist.png "Info.plist 编辑器")](images/infoplist.png#lightbox)
    
    这等效于在应用委托内实例化`FinishedLaunching`方法中的初始视图控制器。 设置此选项后，应用程序将实例化窗口（见下文），加载主情节提要，并将情节提要的初始视图控制器（sourceless Segue）的实例分配为`RootViewController`窗口的属性，然后使屏幕上显示的窗口。

3. 在中`Window` ，用以下代码替换默认方法，以实现窗口属性： `AppDelegate`

    ```csharp
    public override UIWindow Window {
        get;
        set;
    }
    ```

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 右键单击项目以**添加 > 新文件 > iOS > 空情节提要**，如下所示，创建新的情节提要文件。 
    
    [![](images/new-storyboard-vs.png "\"新建项\" 对话框")](images/new-storyboard-vs.png#lightbox)

2. 将情节提要名称添加到 iOS 应用程序的**主界面**部分，如下所示：
    
    [![](images/ios-app.png "Info.plist 编辑器")](images/ios-app.png#lightbox)
    
    这等效于在应用委托内实例化`FinishedLaunching`方法中的初始视图控制器。 设置此选项后，应用程序将实例化窗口（见下文），加载主情节提要，并将情节提要的初始视图控制器（sourceless Segue）的实例分配为`RootViewController`窗口的属性，然后使屏幕上显示的窗口。

3. 在中`Window` ，用以下代码替换默认方法，以实现窗口属性： `AppDelegate`

    ```csharp
    public override UIWindow Window {
        get;
        set;
    }
    ```

-----

## <a name="creating-a-storyboard-with-the-ios-designer"></a>使用 iOS 设计器创建情节提要

可以使用与 Visual Studio for Mac 和 Visual Studio 无缝集成的 Xamarin Designer for iOS 创建情节提要。

若要开始使用 iOS 设计器创建情节提要，请参阅[Hello，IOS 多屏显示](~/ios/get-started/hello-ios-multiscreen/index.md)指南。 在本演练中，你将了解使用 Segue 的视图控制器之间的导航，以及如何处理控件上的事件。

## <a name="instantiate-storyboards-manually"></a>手动实例化情节提要

情节提要完全替换项目中的单个 XIB 文件，但情节提要中的单个视图控制器仍可使用`Storyboard.InstantiateViewController`实例化。

有时，应用程序具有不能使用设计器提供的内置情节提要转换来处理的特殊要求。 例如，如果我们要创建一个从同一按钮启动不同屏幕的应用程序，则根据应用程序的当前状态，我们可能需要手动实例化视图控制器，并为转换编程。

下面的屏幕截图显示了设计图面上的两个视图控制器，它们之间没有任何 segue。 下一部分将逐步讲解如何在代码中设置该转换。

 [![](images/viewcontrollerspink.png "此屏幕截图显示了设计图面上的两个视图控制器，它们之间没有 segue")](images/viewcontrollerspink.png#lightbox)

1. 将_空的 IPhone 情节提要_添加到现有项目项目：
    
    [![](images/add-storyboard1.png "添加情节提要")](images/add-storyboard1.png#lightbox)

2. 双击新创建的情节提要将其打开，然后将新的**导航控制器**添加到设计图面。 由于导航控制器不是 UI，因此默认情况下，它会附带一个根视图控制器，如下图所示：

    [![](images/uinavigationcontroller.png "用 Segue 查看控制器")](images/uinavigationcontroller.png#lightbox)

3. 单击底部的黑色栏，选择_视图控制器_。 在设计器的**属性板**中，在 "**标识**" 下，可以指定自定义类以及视图控制器的唯一 ID。 将**类名称**和**情节提要 ID**设置`MainViewController`为。

    [![](images/identitypanelnew.png "指定自定义类")](images/identitypanelnew.png#lightbox)

4. 稍后，我们需要从情节提要实例化视图控制器，并将使用情节提要 ID 在代码中引用它们。 如果需要还原状态，则将还原 ID 设置为与情节提要 ID 匹配可确保正确地重新创建视图控制器。

5. 目前只有一个视图控制器。 将另一个视图控制器拖动到设计图面上。 在**属性板**的 "标识" 下，将类和情节提要 ID `PinkViewController`设置为，如下所示：

    [![](images/pinkvcnew.png "属性板")](images/pinkvcnew.png#lightbox)
    
    IDE 将为视图控制器创建这些自定义类。 可以在**Solution Pad**中查看这些内容，如以下屏幕截图中所示：
    
    [![](images/solution-pad.png "Solution Pad")](images/solution-pad.png#lightbox)

6. 在中`PinkViewController`，通过单击控制器框架中心来选择视图。 在 Properties Pad 的 "视图" 下，将**背景**更改为洋红色：
    
    [![](images/pinkcontroller.png "设置背景色")](images/pinkcontroller.png#lightbox)

7. 最后，将 "**工具箱**" 中的按钮拖`MainViewController`到上。 在 Properties Pad 中，为其指定名称`PinkButton`和标题 GoToPink，如下所示：

    [![](images/pinkbutton.png "设置按钮名称")](images/pinkbutton.png#lightbox)

情节提要已完成，但如果现在部署项目，将看到一个空白屏幕。 这是因为我们仍需要告诉 IDE 使用我们的情节提要，并设置一个根视图控制器以用作第一个视图。 通常可以通过项目选项完成此操作，如上所示。 但在此示例中，我们通过将以下代码添加到**AppDelegate**来实现相同的结果：

```csharp
public partial class AppDelegate : UIApplicationDelegate
    {
        UIWindow window;
        public static UIStoryboard Storyboard = UIStoryboard.FromName ("MainStoryboard", null);
        public static UIViewController initialViewController;

        public override bool FinishedLaunching (UIApplication app, NSDictionary options)
        {
            window = new UIWindow (UIScreen.MainScreen.Bounds);

            initialViewController = Storyboard.InstantiateInitialViewController () as UIViewController;

            window.RootViewController = initialViewController;
            window.MakeKeyAndVisible ();
            return true;
        }

    }
```

这是很多代码，但只有几行不熟悉。 首先，通过传入情节提要的名称**mainstoryboard.storyboard**，将情节提要注册到**AppDelegate** 。 接下来，我们告知应用程序通过在情节提要上调用`InstantiateInitialViewController`来从情节提要实例化初始视图控制器，并将该视图控制器设置为应用程序的根视图控制器。 此方法确定用户看到的第一个屏幕，并创建该视图控制器的新实例。

请注意，在 "解决方案" 窗格中，IDE `MainViewcontroller.cs`已创建类， `corresponding designer.cs`并在将类名称添加到步骤4中的 Properties Pad 时使用。 我们可以看到，此类创建了一个包含基类的特殊构造函数：

```csharp
public MainViewController (IntPtr handle) : base (handle) 
{
}
```

使用设计器创建情节提要时，IDE 将自动在`designer.cs`类的顶部添加[[Register]](xref:Foundation.RegisterAttribute)特性，并传入字符串标识符，该标识符与在上一步中指定的情节提要 ID 完全相同。 这会将链接C#到情节提要中的相关场景。

在某些时候，你可能需要添加一个**未**在设计器中创建的现有类。 在这种情况下，应将此类注册为普通：

```csharp
[Register ("MainViewController")]
public partial class MainViewController : UIViewController
{
public MainViewController (IntPtr handle) : base (handle) 
{
}

...
}
```

有关注册类和方法的详细信息，请参阅[类型注册](http://docs.xamarin.com/guides/ios/advanced_topics/registrar/)器文档。

此类中的最后一步是将按钮向上绑定到粉红色视图控制器。 我们将`PinkViewController`从情节提要中实例化，然后使用`PushViewController`将推送 segue，如下面的示例代码所示：

```csharp
public partial class MainViewController : UIViewController
{
    UIViewController pinkViewController;

    public MainViewController (IntPtr handle) : base (handle)
    {

    }

    public override void AwakeFromNib ()
    {
    // Called when loaded from xib or storyboard.

    this.Initialize ();
    }

    public void Initialize(){

    //Instantiating View Controller with Storyboard ID 'PinkViewController'
    pinkViewController = Storyboard.InstantiateViewController ("PinkViewController") as PinkViewController;
    }

    public override void ViewDidLoad ()
    {
    base.ViewDidLoad ();

    //When we push the button, we will push the pinkViewController onto our current Navigation Stack
    PinkButton.TouchUpInside += (o, e) =&gt; {
        this.NavigationController.PushViewController (pinkViewController, true);
    };
    }

}
```

运行应用程序将生成一个2屏幕应用程序：

![](images/finishedstoryboard.png "示例应用运行屏幕")

## <a name="conditional-segues"></a>条件 Segue

通常，从一个视图控制器移到下一个视图控制器会依赖于特定的条件。 例如，如果我们要创建一个简单的登录屏幕，则只需在验证用户名和密码后转到下*一屏幕。*

在下面的示例中，我们将向上面的示例中添加一个密码字段。 如果用户输入了正确的密码，则用户将只能访问*PinkViewController* ，否则将显示错误。

在开始之前，请先完成上面的步骤 1-8。 在这些步骤中，我们创建了情节提要，开始创建 UI，并告诉我们的应用程序将使用哪种视图控制器作为 RootViewController。

1. 现在，让我们来构建 UI，并向添加列出的`MainViewController`其他视图，使其看起来像下面的屏幕截图中所示：

    - UITextField
        - 姓名:PasswordTextField
        - Placeholder"输入密钥密码"
    - UILabel
        - 全文条密码错误。 不应传递！
        - 颜色：红色
        - 关联居中
        - 行2
        - 选中 "隐藏" 复选框    
        
    [![](images/passwordvc.png "轴线")](images/passwordvc.png#lightbox)
    
2. 通过按 Ctrl 并将*PinkButton*拖到*PinkViewController*上，然后选择 "在鼠标上**推送**"，在 "跳到粉红色" 按钮和视图控制器之间创建 Segue。 

3. 单击 Segue 并为其指定*标识符* `SegueToPink`：

    [![](images/namesegue.png "单击 Segue 并为其指定标识符 SegueToPink")](images/namesegue.png#lightbox)  

4. 最后，将以下 ShouldPerformSegue 方法添加到`MainViewController`类：

    ```csharp
    public override bool ShouldPerformSegue (string segueIdentifier, NSObject sender)
    {
        
        if(segueIdentifier == "SegueToPink"){
            if (PasswordTextField.Text == "password") {
                PasswordTextField.ResignFirstResponder ();
                return true;
            }
            else{
                ErrorLabel.Hidden = false;
                return false;
            }
        }
        return base.ShouldPerformSegue (segueIdentifier, sender);
    }
    ```

在此代码中，我们已将 segueIdentifier 与`SegueToPink`我们的 segue 相匹配，因此，我们可以测试一个条件; 在本例中为有效密码。 如果条件返回`true`，则 Segue 将执行，并将`PinkViewController`显示。 如果`false`为，则不会显示新的视图控制器。

可以通过将 segueIdentifier 参数用于 ShouldPerformSegue 方法，将此方法应用于此视图控制器上的任何 Segue。 在这种情况下，我们只有一个 Segue `SegueToPink`标识符–。

请参阅演示图板的 "[手册" 演示图板示例](https://docs.microsoft.com/samples/xamarin/ios-samples/manualstoryboard)中的条件性解决方案。

<a name="Using-Storyboard-References" />

## <a name="using-storyboard-references"></a>使用情节提要引用

使用情节提要引用，您可以进行大量复杂的情节提要设计，并将其分解成更小的情节提要，使其从原始情节提要中引用，从而消除复杂性并使生成的各个演示图板更易于设计和维护。

此外，情节提要引用可以提供指向同一情节提要中的另一个场景或不同场景中的特定场景的_定位点_。

<a name="Referencing-an-External-Storyboard" />

### <a name="referencing-an-external-storyboard"></a>引用外部情节提要

若要添加对外部情节提要的引用，请执行以下操作：

1. 在**解决方案资源管理器**中，右键单击项目名称，然后选择 "**添加** > " "**新文件 ...** " > iOS情节 > **提要**。 输入新情节提要的**名称**，然后单击 "**新建**" 按钮：
    
    [![](images/ref01.png "\"新建文件\" 对话框")](images/ref01.png#lightbox)
    
2. 按通常的方式设计新情节提要的幕后布局，并保存所做的更改： 
    
    [![](images/ref02.png "新场景的布局")](images/ref02.png#lightbox)
    
3. 打开要在 iOS 设计器中添加对的引用的情节提要。

4. 将**情节提要引用**从**工具箱**拖动到 Design Surface： 
    
    [![](images/ref03.png "情节提要引用")](images/ref03.png#lightbox)
    
5. 在 "**属性资源管理器**" 的 "**小组件**" 选项卡中，选择前面创建的**情节提要**的名称： 

    [![](images/ref04.png "\"小组件\" 选项卡")](images/ref04.png#lightbox)
    
6. 在现有场景上，单击鼠标右键单击 UI 小组件（例如按钮），并创建一个新的 Segue 到刚刚创建的**情节提要引用**： 

    [![](images/ref05.png "创建 segue")](images/ref05.png#lightbox) 
    
7. 从弹出菜单中，选择 "**显示**" 以完成 Segue： 

    [![](images/ref06.png "选择 \"显示\" 以完成 Segue")](images/ref06.png#lightbox) 
    
8. 保存对情节提要所做的更改。

当应用程序运行时，用户单击你从其创建 Segue 的 UI 元素时，将显示情节提要引用中指定的外部情节提要的初始视图控制器。

<a name="Referencing-a-Specific-Scene-in-an-External-Storyboard" />

### <a name="referencing-a-specific-scene-in-an-external-storyboard"></a>引用外部情节提要中的特定场景

若要添加对外部情节提要（而非初始视图控制器）的特定场景的引用，请执行以下操作：

1. 在**解决方案资源管理器**中，双击外部情节提要将其打开以进行编辑。

2. 添加新场景并按常规方式设计其布局： 

    [![](images/ref07.png "新的场景布局")](images/ref07.png#lightbox)
    
3. 在 "**属性资源管理器**" 的 "**小组件**" 选项卡中，输入新场景的视图控制器的**情节提要 ID** ： 

    [![](images/ref08.png "为新的场景视图控制器输入情节提要 ID")](images/ref08.png#lightbox)
    
4. 打开要在 iOS 设计器中添加对的引用的情节提要。

5. 将**情节提要引用**从**工具箱**拖动到 Design Surface： 

    [![](images/ref03.png "情节提要引用")](images/ref03.png#lightbox)
    
6. 在 "**属性资源管理器**" 的 "**小组件**" 选项卡中，选择你在上面创建的场景的**情节提要**名称和**引用 id** （情节提要 id）： 

    [![](images/ref09.png "\"小组件\" 选项卡")](images/ref09.png#lightbox)
    
7. 在现有场景上，单击鼠标右键单击 UI 小组件（例如按钮），并创建一个新的 Segue 到刚刚创建的**情节提要引用**： 

    [![](images/ref10.png "创建 segue")](images/ref10.png#lightbox) 
    
8. 从弹出菜单中，选择 "**显示**" 以完成 Segue： 

    [![](images/ref06.png "选择 \"显示\" 以完成 Segue")](images/ref06.png#lightbox) 
    
9. 保存对情节提要所做的更改。

当应用程序运行并且用户单击你从其创建 Segue 的 UI 元素时，将显示具有情节提要引用中指定的外部情节提要的给定**情节提要 ID**的场景。

<a name="Referencing-a-Specific-Scene-in-the-Same-Storyboard" />

### <a name="referencing-a-specific-scene-in-the-same-storyboard"></a>引用同一情节提要中的特定场景

若要将引用添加到相同情节提要的特定场景，请执行以下操作：

1. 在**解决方案资源管理器**中，双击情节提要将其打开以进行编辑。

2. 添加新场景并按常规方式设计其布局： 

    [![](images/ref11.png "新的场景布局")](images/ref11.png#lightbox)

3. 在 "**属性资源管理器**" 的 "**小组件**" 选项卡中，输入新场景的视图控制器的**情节提要 ID** ： 

    [![](images/ref12.png "\"小组件\" 选项卡")](images/ref12.png#lightbox)
    
4. 将**情节提要引用**从**工具箱**拖动到 Design Surface： 

   [![](images/ref03.png "情节提要引用")](images/ref03.png#lightbox)
    
5. 在 "**属性资源管理器**" 的 "**小组件**" 选项卡中，选择上面创建的场景的**引用 id** （情节提要 id）： 

    [![](images/ref13.png "\"小组件\" 选项卡")](images/ref13.png#lightbox)
    
6. 在现有场景上，单击鼠标右键单击 UI 小组件（例如按钮），并创建一个新的 Segue 到刚刚创建的**情节提要引用**： 

    [![](images/ref14.png "创建 segue")](images/ref14.png#lightbox) 
    
7. 从弹出菜单中，选择 "**显示**" 以完成 Segue： 

    [![](images/ref06.png "选择 \"显示\" 以完成 Segue")](images/ref06.png#lightbox) 
    
8. 保存对情节提要所做的更改。

当应用程序运行，并且用户单击你从其创建 Segue 的 UI 元素时，将显示在情节提要引用中指定的同一情节提要中具有给定**情节提要 ID**的场景。

## <a name="summary"></a>总结

本文介绍了情节提要的概念，以及如何在开发 iOS 应用程序时对其有利。 它讨论场景、查看控制器、视图和视图层次结构，以及如何将场景与不同类型的 Segue 链接在一起。  它还探讨了如何从情节提要手动实例化视图控制器，以及如何创建条件 Segue。

## <a name="related-links"></a>相关链接

- [手动情节提要（示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/manualstoryboard/)
- [IOS 设计器简介](~/ios/user-interface/designer/introduction.md)
- [转换为情节提要](https://developer.apple.com/library/ios/#releasenotes/Miscellaneous/RN-AdoptingStoryboards/)
- [UIStoryboard 类引用](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UIStoryboard_Class/Reference/Reference.html)
