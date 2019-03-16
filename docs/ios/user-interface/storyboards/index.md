---
title: 在 Xamarin.iOS 中的情节提要简介
description: 本文档介绍在 Xamarin.iOS 中的情节提要。 它介绍了如何使用情节提要来定义用户界面，segue，以及如何使用 iOS 设计器编辑情节提要文件。
ms.prod: xamarin
ms.assetid: A3339BD2-9F56-7965-25F5-4B7C991EB775
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
---

# <a name="introduction-to-storyboards-in-xamarinios"></a>在 Xamarin.iOS 中的情节提要简介

本指南中我们将介绍哪些情节提要是和检查的一些关键组件 – 例如 segues 设计。 我们将介绍如何创建和使用，情节提要，并且开发人员必须哪些益处。

情节提要文件格式的引入了通过 Apple 的 iOS 应用程序 UI 的可视化表示形式作为之前，开发人员创建 XIB 文件为每个视图控制器，并手动编程之间每个视图的导航。  使用情节提要，开发人员定义视图控制器和设计图面上在它们之间导航，并提供 WYSIWYG 编辑应用程序的用户界面。

可以创建、 打开和使用 Xamarin iOS 设计器编辑情节提要。 本指南还将演练如何使用设计器创建情节提要，同时使用 C# 进行编程导航。


## <a name="requirements"></a>要求

情节提要可与 iOS 设计器在 Visual Studio for Mac 或 Visual Studio 2017 与安装的 Xamarin 工作负荷。

## <a name="what-is-a-storyboard"></a>什么是情节提要？

情节提要是应用程序中的所有屏幕的可视表示形式。 它包含一系列具有每个场景元素表示场景*视图控制器*并将其*视图*。 这些视图可能包含的对象和[控件](~/ios/user-interface/controls/index.md)，将允许用户与你的应用程序进行交互。 此集合的视图和控件 (或*子视图*) 被称为*内容视图层次结构*。 连接的场景的 segue，这些对象表示视图控制器之间的转换。 这通常是通过创建的初始视图上的对象和连接的视图之间的 segue 实现的。 在设计图面上的关系如下图所示：

 [![](images/storyboardsview.png "在此图说明了在设计图面上的关系")](images/storyboardsview.png#lightbox)

如所示，情节提要将使用已呈现的内容设置您的场景的每个布局，并说明了它们之间的连接。  值得注意在此情况下，是，当我们谈及场景在 iPhone 上，则可以安全地假定一个*场景*情节提要上等同于一个*屏幕*的设备上的内容。 但是，的 iPad 是可以有多个场景出现在一次 – 例如，使用与弹出框视图控制器。

有很多好处使用演示图板创建应用程序的 UI，尤其是在使用 Xamarin。 首先，它是可视化表示形式，UI 的所有对象 – 包括[自定义控件](~/ios/user-interface/designer/ios-designable-controls-overview.md)– 在设计时呈现。 这意味着，在生成或部署其外观和流可以可视化应用程序之前。 以上图中，为例。 我们快速看一下图面上有多少场景是的设计、 每个视图的布局和如何相关的所有内容从中可以看到。 这是什么使情节提要如此强大。

事件是使用演示图板，更易于管理，尤其是在使用 iOS 设计器。 大多数 UI 控件将在属性面板中具有一系列可能的事件。 可以在此处添加并在视图控制器类中的分部方法中完成的事件处理程序...

情节提要的内容存储为 XML 文件。 在生成时，任何`.storyboard`文件编译到名为 nibs 的二进制文件。 在运行时，这些 nibs 初始化并实例化以创建新的视图。

## <a name="segues"></a>Segue

一个*Segue*，或*Segue 对象*，iOS 开发用来表示场景之间的转换。 若要创建 segue，按住**Ctrl**键和单击并拖动一个场景到另一个。 我们在拖动我们鼠标，将出现蓝色连接器，指示其中 segue 将会导致如下图中所示：

 [![](images/createsegue.png "蓝色连接器将出现，指示其中 segue 将导致此映像中所示")](images/createsegue.png#lightbox)

鼠标弹起上显示一个菜单将允许我们选择我们 segue 的操作。 它可能看起来类似于以下图像： 

**预 iOS 8 和大小类**:

[![](images/segue1.png "而无需大小类操作 Segue 下拉列表中")](images/segue1.png#lightbox)

**使用大小类和自适应 Segue 时**:

[![](images/16new.png "使用大小类操作 Segue 下拉列表中")](images/16new.png#lightbox)

> [!IMPORTANT]
> 如果使用 VMWare 对于 Windows 虚拟机，按住 Ctrl 单击映射为_右键单击_默认情况下的鼠标按钮。 若要创建 Segue，编辑通过键盘喜好**首选项** > **键盘和鼠标** > **鼠标快捷方式**并重新映射您**次要按钮**如下图所示：
> 
> [![](images/image22.png "键盘和鼠标首选项设置")](images/image22.png#lightbox)
> 
> 现在应能够像平常一样在视图控制器之间添加 segue。

有不同类型的转换，每个给定控制如何向用户显示的新视图控制器以及它如何与其他视图控制器情节提要中进行交互。 下面介绍了这些。 另外，还可以为要实现自定义转换的 segue 对象的子类：

-  **显示 / 推送**– 推送 segue 将视图控制器添加到导航堆栈。 它假定发起请求的视图控制器是同一导航控制器将被添加到堆栈的视图控制器的一部分。 这执行同样的操作为`pushViewController`，而通常用于在屏幕上的数据之间存在某种关系时。 使用推送 segue 为您提供了使导航工具栏中的后退按钮和标题添加到允许向下钻取导航的视图层次结构通过在堆栈上的每个视图。
-  **模式**– 模式 segue 创建项目，显示未经过动画处理的转换的选项中的任何两个视图控制器之间的关系。 子视图控制器会十分晦涩父视图控制器时放入视图。 与不同的推送 segue，这为我们; 添加后退按钮当使用在安装结束时 segue`DismissViewController`必须用于返回到上一个视图控制器。
-  **自定义**-任何自定义的子类可创建 segue ` UIStoryboardSegue`。
-  **展开**-展开的 segue 可用于在向后导航推送或模式 segue – 例如，通过消除有模式地显示视图控制器。 除此之外，您可以通过不只一个展开但推送和模式的一系列 segue 并返回具有单个导航层次结构中的多个步骤展开操作。 若要了解如何使用展开代码 segue 在 iOS 中，读取[创建展开 Segue](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/storyboard/unwind_segue)方案。
-  **无源**– sourceless segue 指示包含初始视图控制器场景，因此其查看用户首先看到。 为由 segue 如下所示：  

    [![](images/sourcelesssegue.png "Sourceless segue")](images/sourcelesssegue.png#lightbox)

### <a name="adaptive-segue-types"></a>自适应 Segue 类型

 iOS 8 引入[大小类](~/ios/user-interface/storyboards/unified-storyboards.md#size-classes)以允许使用所有可用的屏幕大小，使开发人员能够创建适用于所有的 iOS 设备的一个 UI 的 iOS 情节提要文件。 默认情况下，所有新的 Xamarin.iOS 应用程序将使用大小类。 若要使用的较旧项目的大小类，请参阅[统一情节提要简介](~/ios/user-interface/storyboards/unified-storyboards.md)指南。 
 
使用大小类的任何应用程序还将使用新[*自适应 Segue*](~/ios/user-interface/storyboards/unified-storyboards.md)。 使用大小类时，请记住，我们不直接指定做我们使用的是 iPhone 或 iPad。 换而言之，我们将创建一个 UI，将始终看起来相同，而不考虑它必须使用多少房地产。 通过判断环境，并确定如何最大程度提供内容的自适应 segues 设计工作。 自适应 Segue 如下所示： 

[![](images/adaptivesegue.png "自适应 Segue 的下拉列表中")](images/adaptivesegue.png#lightbox)

|Segue|描述|
|--- |--- |
|显示|这是非常类似于推送 segue，但它会考虑在屏幕的内容。|
|显示详细信息|如果应用显示母版和详细信息视图 （例如，在 iPad 上的拆分视图控制器），内容将替换的详细信息视图。 如果应用显示 master 或详细信息，内容将替换视图控制器堆栈的顶部。|
|展示|这类似于模式的 segue，并允许选定的演示文稿和过渡样式。|
|弹出框的演示文稿|这显示为弹出框的内容|

### <a name="transferring-data-with-segues"></a>将使用的数据传输 Segue

转换不结束 segue 的好处。 它们还可以用于管理视图控制器之间的数据传输。 这通过重写来实现`PrepareForSegue`初始视图控制器和自己处理数据的方法。 触发 segue 后 – 例如，按下按钮 – 与应用程序将调用此方法，提供准备新的视图控制器的机会*之前*发生任何导航。 下面的代码，从[Phoneword](https://developer.xamarin.com/samples/monotouch/Hello_iOS/)示例，说明了这一点： 


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

在此示例中，`PrepareForSegue`用户触发 segue 时，将调用方法。 我们首先需要创建的接收的视图控制器实例并将其设为 segue 的目标视图控制器。 这可通过下面的代码行：

```csharp
var callHistoryController = segue.DestinationViewController as CallHistoryController;
```

该方法现在具有设置的属性的能力`DestinationViewController`。 在此示例中我们已通过传递一个名为列表中采取这样做的优点`PhoneNumbers`到`CallHistoryController`并将其分配到具有相同名称的对象：

```csharp
if (callHistoryController != null) {
        callHistoryController.PhoneNumbers = PhoneNumbers;
    }
```

完成转换后，用户将看到`CallHistoryController`与填充的列表。

## <a name="adding-a-storyboard-to-a-non-storyboard-project"></a>将情节提要添加到非演示图板项目

有时您可能需要将演示图板添加到以前非演示图板文件。 一次执行此操作在 Visual Studio for Mac 可以简化通过执行以下步骤：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 通过浏览到创建一个新的情节提要文件**文件 > 新建文件 > iOS > 情节提要**，如下所示： 
    
    [![](images/new-storyboard-xs.png "新建文件对话框")](images/new-storyboard-xs.png#lightbox)

2. 添加到你的情节提要的名称**主界面**一部分**Info.plist**，如下所示：
    
    [![](images/infoplist.png "Info.plist 编辑器")](images/infoplist.png#lightbox)
    
    此命令的作用等效于实例化中的初始视图控制器`FinishedLaunching`中应用程序委托的方法。 设置此选项，该应用程序实例化的窗口 （见下文），然后加载主要情节提要，并将情节提要的初始视图控制器 （sourceless Segue 旁边的一个） 的实例指定为`RootViewController`的窗口，然后使属性在屏幕上可见的窗口。

3. 在中`AppDelegate`，重写默认`Window`方法，使用以下代码以实现窗口属性：
        
        public override UIWindow Window {
            get;
            set;
            }
            
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 右键单击为项目创建一个新的情节提要文件**添加 > 新文件 > iOS > 空情节提要**，如下所示： 
    
    [![](images/new-storyboard-vs.png "新建项对话框")](images/new-storyboard-vs.png#lightbox)

2. 添加到你的情节提要的名称**主界面**部分中的 iOS 应用程序，如下所示：
    
    [![](images/ios-app.png "Info.plist 编辑器")](images/ios-app.png#lightbox)
    
    此命令的作用等效于实例化中的初始视图控制器`FinishedLaunching`中应用程序委托的方法。 设置此选项，该应用程序实例化的窗口 （见下文），然后加载主要情节提要，并将情节提要的初始视图控制器 （sourceless Segue 旁边的一个） 的实例指定为`RootViewController`的窗口，然后使属性在屏幕上可见的窗口。

3. 在中`AppDelegate`，重写默认`Window`方法，使用以下代码以实现窗口属性：

        public override UIWindow Window {
            get;
            set;
            }
            
-----

## <a name="creating-a-storyboard-with-the-ios-designer"></a>使用 iOS 设计器创建情节提要

可以使用适用于 iOS，已集成无缝地使用 Visual Studio for Mac 和 Visual Studio 的 Xamarin 设计器创建情节提要。

若要开始使用 iOS 设计器来创建情节提要，请按照[Hello，iOS 多屏显示](~/ios/get-started/hello-ios-multiscreen/index.md)指南。 在本演练中将探讨使用 segues 设计，以及如何处理在控件上的事件的视图控制器之间导航。

## <a name="instantiate-storyboards-manually"></a>手动实例化的情节提要

情节提要完全替换单个 XIB 文件在项目中，但在情节提要中的单个视图控制器可以仍使用实例化`Storyboard.InstantiateViewController`。

有时应用程序具有特殊要求，不能与设计器提供的内置情节提要转换处理。 例如，如果我们要创建的应用程序将启动从相同的按钮，具体取决于应用程序的当前状态的不同屏幕我们可能想要手动实例化视图控制器和自己程序转换。

下面的屏幕截图显示了它们之间 segue 没有两个视图控制器我们设计图面上。 下一节将引导完成如何转换可以设置在代码中。

 [![](images/viewcontrollerspink.png "此屏幕截图显示在设计图面上没有的两个视图控制器 segue 它们之间")](images/viewcontrollerspink.png#lightbox)

1. 添加_空 iPhone 情节提要_到现有项目项目：
    
    [![](images/add-storyboard1.png "添加情节提要")](images/add-storyboard1.png#lightbox)

2. 双击新创建的情节提要，将其打开，并添加一个新**导航控制器**到设计图面。 因为导航控制器是无用户界面，默认情况下它是否会与根视图控制器，如下图所示：

    [![](images/uinavigationcontroller.png "与视图控制器 Segue")](images/uinavigationcontroller.png#lightbox)

3. 选择_视图控制器_通过单击底部的黑色栏。 在设计器中**属性面板**下**标识**我们可以为视图控制器中指定的自定义类，以及唯一 ID。 设置**类名**并**情节提要 ID**到`MainViewController`。

    [![](images/identitypanelnew.png "指定自定义类")](images/identitypanelnew.png#lightbox)

4. 更高版本，我们将需要实例化从情节提要，我们查看控制器并将使用情节提要 ID 在我们的代码中引用它们。 设置要与情节提要 ID 匹配的还原操作 ID 可确保如果需要为还原状态的视图控制器获取重新创建正确。

5. 目前仅有一个视图控制器。 将另一个视图控制器拖到设计图面。 在中**属性面板**，在标识下设置的类和情节提要 ID 到`PinkViewController`，如下所示：

    [![](images/pinkvcnew.png "属性面板")](images/pinkvcnew.png#lightbox)
    
    IDE 将创建这些自定义类的视图控制器。 可以在查看这些**Solution Pad**，如以下屏幕截图中所示：
    
    [![](images/solution-pad.png "Solution Pad")](images/solution-pad.png#lightbox)

6. 在`PinkViewController`，通过向控制器的框的中心单击选择的视图。 在属性面板中，在视图下更改**背景**洋红色到：
    
    [![](images/pinkcontroller.png "设置背景色")](images/pinkcontroller.png#lightbox)

7. 最后，将从一个按钮**工具箱**拖动到`MainViewController`。 在属性面板中，将其命名为`PinkButton`和标题 GoToPink，如下图所示：

    [![](images/pinkbutton.png "设置按钮名称")](images/pinkbutton.png#lightbox)

情节提要已完成，但是如果我们立即部署该项目，我们将得到空白屏幕。 这是因为我们仍然需要告诉 IDE 若要使用自己的情节提要，并要将根视图控制器设置为用作第一个视图。 通常这可以通过我们的项目选项，如上所示。 但是在此示例中我们将通过相同的结果在代码中，添加以下**AppDelegate**:

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

这是很多代码，但只需要几行不了解。 首先，我们注册与自己的情节提要**AppDelegate**通过传入情节提要的名称， **MainStoryboard**。 接下来，我们告知应用程序通过调用实例化从情节提要的初始视图控制器`InstantiateInitialViewController`上自己的情节提要，并且我们将该视图控制器设为我们的应用程序的根视图控制器。 此方法可确定用户可以看到，第一个屏幕，并创建该视图控制器的新实例。

请注意，在 IDE 已创建的解决方案窗格`MainViewcontroller.cs`类，并将其`corresponding designer.cs`我们的类名添加到在步骤 4 中属性面板时。 我们可以看到此类创建包括基类的特殊构造函数：

```csharp
public MainViewController (IntPtr handle) : base (handle) 
{
}
```


在创建情节提要使用设计器时，IDE 将自动添加[[注册]](xref:Foundation.RegisterAttribute)顶部的特性`designer.cs`类，并传入的字符串标识符，这就相当于中指定的情节提要 ID上一步。 这会链接 C# 到情节提要中相关的场景。

你可能想要添加现有类的某个时刻**不**设计器中创建。 在这种情况下，会将此类注册为正常：

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

有关注册的类和方法的详细信息，请参阅[类型注册机构](http://docs.xamarin.com/guides/ios/advanced_topics/registrar/)文档。

此类中的最后一步是连接按钮和过渡到粉红色视图控制器。 我们将实例化`PinkViewController`从情节提要; 然后，我们将程序推送 segue 与`PushViewController`，如下面的示例代码所示：

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

运行应用程序将生成一个 2 屏幕的应用程序：

![](images/finishedstoryboard.png "示例应用程序运行屏幕")

## <a name="conditional-segues"></a>条件 Segue

通常情况下，将从一个视图控制器移动到下一步是根据特定条件。 例如，如果我们在制作一个简单的登录屏幕我们只想要将移动到下一个屏幕*如果*具有已验证的用户名和密码。

下一个示例中我们会将密码字段添加到上面的示例。 用户仅能够访问*PinkViewController*如果输入了正确的密码，否则将显示错误。

在开始之前，请按照完成步骤 1 至 8 个以上。 在这些步骤中，我们创建自己的情节提要，开始创建我们的 UI，并告知我们应用程序委托使用的视图控制器 RootViewController 原样。

1. 现在，让我们来构建我们的 UI 和添加到列出的其他视图`MainViewController`以使其看起来在下面的屏幕截图中的类似的：

    - UITextField
        - 姓名:PasswordTextField
        - 占位符：输入密码
    - UILabel
        - 文本：错误：错误的密码。 不应传递 ！
        - 颜色：红色
        - 对齐方式：居中
        - 代码行：2
        - 选中隐藏复选框 
        
    [![](images/passwordvc.png "Center 行")](images/passwordvc.png#lightbox)
    
2. 创建转粉红色按钮和通过按住 Ctrl 键拖动从视图控制器之间的 Segue *PinkButton*到*PinkViewController*，然后选择**推送**上鼠标弹起. 

3. 单击 Segue 并为其提供*标识符* `SegueToPink`:

    [![](images/namesegue.png "单击 Segue 并为其提供标识符 SegueToPink")](images/namesegue.png#lightbox)  
    

4. 最后，添加以下 ShouldPerformSegue 方法`MainViewController`类：

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

在此代码中，我们具有匹配到 segueIdentifier 我们`SegueToPink`segue，然后，我们可以测试条件; 因此在此情况下有效的密码。 如果我们的条件返回`true`，Segue 将执行，并将呈现`PinkViewController`。 如果`false`，不会显示新视图控制器。

我们可以通过检查 ShouldPerformSegue 方法的 segueIdentifier 参数适用于此视图控制器上的任何 Segue 这种方法。 在这种情况下我们只能有一个 Segue 标识符 – `SegueToPink`。

中的 Storyboards.Conditional 解决方案是指[手动情节提要示例](https://developer.xamarin.com/samples/monotouch/ManualStoryboard/)有关工作示例。

<a name="Using-Storyboard-References" />

## <a name="using-storyboard-references"></a>使用情节提要引用

情节提要引用，可采用庞大而复杂的情节提要设计并将其分割为较小的情节提要获取引用从原始，从而消除复杂性，并将生成单独的情节提要设为更轻松地设计和维护。

另外，还可提供情节提要引用_定位点_到同一个情节提要或上一个不同的特定场景中的另一个场景。

<a name="Referencing-an-External-Storyboard" />

### <a name="referencing-an-external-storyboard"></a>引用外部情节提要

若要添加到外部情节提要的引用，请执行以下操作：

1. 在中**解决方案资源管理器**，右键单击项目名称并选择**添加** > **新文件...**  >  **iOS** > **情节提要**。 输入**名称**作为新情节提要，然后单击**新建**按钮：
    
    [![](images/ref01.png "新建文件对话框")](images/ref01.png#lightbox)
    
2. 设计新情节提要的场景的布局，因为通常类似于并保存所做的更改： 
    
    [![](images/ref02.png "新的场景的布局")](images/ref02.png#lightbox)
    
3. 打开要在 iOS 设计器中添加对引用的情节提要。

4. 拖动**引用的情节提要**从**工具箱**拖到设计图面： 
    
    [![](images/ref03.png "情节提要引用")](images/ref03.png#lightbox)
    
5. 在中**小组件**选项卡**属性资源管理器**，选择的名称**情节提要**上面创建的： 

    [![](images/ref04.png "小组件选项卡")](images/ref04.png#lightbox)
    
6. 单击在 UI 上的小组件 （如按钮） 现有场景并创建到新 Segue**情节提要引用**刚创建的： 

    [![](images/ref05.png "创建 segue")](images/ref05.png#lightbox) 
    
7. 从弹出菜单中选择**显示**完成 Segue: 

    [![](images/ref06.png "选择显示完成 Segue")](images/ref06.png#lightbox) 
    
8. 将所做的更改保存到情节提要。

将显示运行该应用并在用户单击 UI 元素从来自外部的情节提要的情节提要引用中指定的初始视图控制器创建 Segue 时。

<a name="Referencing-a-Specific-Scene-in-an-External-Storyboard" />

### <a name="referencing-a-specific-scene-in-an-external-storyboard"></a>引用外部情节提要中的特定场景

要添加对特定场景的引用外部情节提要 （和不初始视图控制器），执行以下操作：

1. 在中**解决方案资源管理器**，双击外部情节提要中，将其打开进行编辑。

2. 添加新的场景，并像通常那样设计其布局： 

    [![](images/ref07.png "新的场景布局")](images/ref07.png#lightbox)
    
3. 在中**小组件**选项卡**属性资源管理器**，输入**情节提要 ID**为新的场景的视图控制器： 

    [![](images/ref08.png "输入新的场景视图控制器情节提要 ID")](images/ref08.png#lightbox)
    
3. 打开要在 iOS 设计器中添加对引用的情节提要。

4. 拖动**引用的情节提要**从**工具箱**拖到设计图面： 

    [![](images/ref03.png "情节提要引用")](images/ref03.png#lightbox)
    
5. 在中**小组件**选项卡**属性资源管理器**，选择的名称**情节提要**并**引用 ID** (情节提要 ID) 的上面创建的场景： 

    [![](images/ref09.png "小组件选项卡 ")](images/ref09.png#lightbox)
    
6. 单击在 UI 上的小组件 （如按钮） 现有场景并创建到新 Segue**情节提要引用**刚创建的： 

    [![](images/ref10.png "创建 segue")](images/ref10.png#lightbox) 
    
7. 从弹出菜单中选择**显示**完成 Segue: 

    [![](images/ref06.png "选择显示完成 Segue")](images/ref06.png#lightbox) 
    
8. 将所做的更改保存到情节提要。

从场景创建 Segue 的 UI 元素时在应用正在运行且用户单击给定**情节提要 ID**将显示从情节提要引用中指定外部情节提要。

<a name="Referencing-a-Specific-Scene-in-the-Same-Storyboard" />

### <a name="referencing-a-specific-scene-in-the-same-storyboard"></a>引用同一个情节提要中的特定场景

若要添加到特定场景中相同的情节提要的引用，请执行以下操作：

1. 在中**解决方案资源管理器**，双击将其打开进行编辑的情节提要。

2. 添加新的场景，并像通常那样设计其布局： 

    [![](images/ref11.png "新的场景布局")](images/ref11.png#lightbox)

3. 在中**小组件**选项卡**属性资源管理器**，输入**情节提要 ID**为新的场景的视图控制器： 

    [![](images/ref12.png "小组件选项卡")](images/ref12.png#lightbox)
    
3. 拖动**引用的情节提要**从**工具箱**拖到设计图面： 

    [![](images/ref03.png "情节提要引用")](images/ref03.png#lightbox)
    
5. 在中**小组件**选项卡**属性资源管理器**，选择**引用 ID** (情节提要 ID) 的场景的上述步骤中创建的： 

    [![](images/ref13.png "小组件选项卡")](images/ref13.png#lightbox)
    
6. 单击在 UI 上的小组件 （如按钮） 现有场景并创建到新 Segue**情节提要引用**刚创建的： 

    [![](images/ref14.png "创建 segue")](images/ref14.png#lightbox) 
    
7. 从弹出菜单中选择**显示**完成 Segue: 

    [![](images/ref06.png "选择显示完成 Segue")](images/ref06.png#lightbox) 
    
8. 将所做的更改保存到情节提要。

从场景创建 Segue 的 UI 元素时在应用正在运行且用户单击给定**情节提要 ID**将显示在相同的情节提要的情节提要引用中指定。

## <a name="summary"></a>总结

本文介绍了情节提要和如何它们可以是有益的 iOS 应用程序开发中的概念。 它讨论的场景，视图控制器、 视图、 视图层次结构以及如何场景链接在一起的不同类型的 segues 设计。  它还介绍了实例化视图控制器手动从情节提要，并创建条件性 segues 设计。



## <a name="related-links"></a>相关链接

- [手动情节提要 （示例）](https://developer.xamarin.com/samples/ManualStoryboard/)
- [IOS 设计器简介](~/ios/user-interface/designer/introduction.md)
- [转换到情节提要](https://developer.apple.com/library/ios/#releasenotes/Miscellaneous/RN-AdoptingStoryboards/)
- [UIStoryboard 类引用](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UIStoryboard_Class/Reference/Reference.html)
