---
title: iOS 中 Xamarin.iOS 的扩展
description: 本文档介绍了扩展，它们是由 iOS 如通知中心内的标准上下文中的小组件。 它讨论了如何创建扩展，并从父应用程序与之进行通信。
ms.prod: xamarin
ms.assetid: 3DEB3D43-3E4A-4099-8331-93C1E7A77095
ms.technology: xamarin-ios
ms.custom: xamu-video
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: fb5cea018180966e914a5bce5e85cb83fbdf901d
ms.sourcegitcommit: 0cb62b02a7efb5426f2356d7dbdfd9afd85f2f4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/13/2019
ms.locfileid: "65557250"
---
# <a name="ios-extensions-in-xamarinios"></a>在 Xamarin.iOS 中的 iOS 扩展

> [!VIDEO https://youtube.com/embed/Sd0-ch9Udmk]

**在 iOS 中创建扩展视频**

专用扩展，如在 iOS 8 中引入`UIViewControllers`，按呈现 iOS 标准上下文内此类为内**通知中心**，如用户请求执行的自定义键盘类型专用化输入或其他上下文如编辑的照片，扩展可以提供特殊效果筛选器。

所有扩展 （与使用 64 位统一 Api 编写这两个元素） 安装在与容器应用程序一起使用，并且从特定的扩展点在主机应用程序中激活。 因为它们将用作补充现有的系统函数，它们必须高效，且可靠的高性能。 

## <a name="extension-points"></a>扩展点

|类型|描述|扩展点|主机应用程序|
|--- |--- |--- |--- |
|操作|专用的编辑器或特定媒体类型的查看器|`com.apple.ui-services`|任意|
|文档提供程序|允许应用程序以使用远程文档存储|`com.apple.fileprovider-ui`|使用应用[UIDocumentPickerViewController](xref:UIKit.UIDocumentPickerViewController)|
|键盘|备用键盘|`com.apple.keyboard-service`|任意|
|照片编辑|照片处理和编辑|`com.apple.photo-editing`|Photos.app 编辑器|
|共享|与社交网络，消息传送服务等共享数据。|`com.apple.share-services`|任意|
|今天|显示在今日屏幕或通知中心上的"小组件"|`com.apple.widget-extensions`|今天和通知中心|

[其他扩展点](~/ios/platform/introduction-to-ios10/index.md#app-extensions)iOS 10 中已添加。

## <a name="limitations"></a>限制

扩展具有许多限制，其中一些通用于所有类型 （对于实例，没有任何类型的扩展插件可以访问照相机或麦克风） 而其他类型的扩展可能会对其使用情况 （例如，自定义键盘产生具体的限制不能用于保护数据输入字段如密码）。 

通用的限制是：

- [运行状况工具包](~/ios/platform/healthkit.md)并[事件工具包 UI](~/ios/platform/eventkit.md)框架将不可用
- 扩展不能使用[扩展后台模式](https://developer.xamarin.com/guides/cross-platform/application_fundamentals/backgrounding/part_3_ios_backgrounding_techniques/registering_applications_to_run_in_background/)
- 扩展无法访问设备的摄像机或麦克风 （尽管它们可能访问现有的媒体文件）
- 扩展不能将收到以无线方式删除数据 （但它们可以传输通过以无线方式删除数据）
- [UIActionSheet](xref:UIKit.UIActionSheet)并[UIAlertView](xref:UIKit.UIAlertView)不可用; 扩展必须使用[看到](xref:UIKit.UIAlertController)
- 多个成员[UIApplication](xref:UIKit.UIApplication)都不可用：[UIApplication.SharedApplication](xref:UIKit.UIApplication.SharedApplication)， [UIApplication.OpenUrl](xref:UIKit.UIApplication.OpenUrl(Foundation.NSUrl))， [UIApplication.BeginIgnoringInteractionEvents](xref:UIKit.UIApplication.BeginIgnoringInteractionEvents)和[UIApplication.EndIgnoringInteractionEvents](xref:UIKit.UIApplication.EndIgnoringInteractionEvents)
- iOS 增强今天的扩展了 16 MB 内存使用情况限制。
- 默认情况下键盘扩展无网络访问权限。 这会影响调试在设备上 （该限制不在模拟器中强制执行），因为 Xamarin.iOS 需要网络访问权限来调试工作。 它通过设置就可以请求网络访问权限`Requests Open Access`到项目的 Info.plist 中的值`Yes`。 请参阅 Apple[自定义键盘指南](https://developer.apple.com/library/content/documentation/General/Conceptual/ExtensibilityPG/CustomKeyboard.html)有关键盘扩展限制的详细信息。

有关单独的限制，请参阅 Apple[应用程序扩展编程指南](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/)。

## <a name="distributing-installing-and-running-extensions"></a>分发、 安装和运行扩展

容器应用程序，而后者，已提交并通过 App Store 分发中，从分发扩展。 此时，安装应用程序一起分发的扩展插件，但用户必须显式启用每个扩展。 在不同的方式; 中启用了不同类型的扩展多个要求用户导航到**设置**应用并启用从该处。 在使用，例如发送照片时启用共享的扩展点启用时其他人。 

应用程序在其中 （其中，则在用户遇到此扩展点） 使用该扩展被称为**主机应用**，因为它是托管扩展，在执行时的应用程序。 安装扩展的应用程序是**容器应用**，因为它是安装时包含扩展的应用。  

通常情况下，容器应用介绍扩展，并指导用户完成启用它的过程。

## <a name="extension-lifecycle"></a>扩展生命周期

扩展可以作为单个一样简单[UIViewController](xref:UIKit.UIViewController)或显示 UI 的多个屏幕的更复杂的扩展。 当用户遇到_扩展点_(例如共享映像)，它们将有机会从注册该扩展点的扩展中进行选择。 

如果他们选择您的应用程序的扩展，其`UIViewController`将实例化并开始正常视图控制器生命周期。 但是，与常规应用，后者虽然是挂起，但通常不终止用户完成与它们交互时，不同扩展加载、 执行，并重复然后终止。

扩展可以与通过其主机应用程序进行通信[NSExtensionContext](xref:Foundation.NSExtensionContext)对象。 某些扩展具有接收结果的异步回调的操作。 将在后台线程上执行这些回调和扩展必须考虑这点;例如，通过使用[NSObject.InvokeOnMainThread](xref:Foundation.NSObject.InvokeOnMainThread*)如果用户想要更新的用户界面。 请参阅[与主机应用程序通信](#communicating-with-the-host-app)部分获取更多详细信息。

默认情况下，扩展和其容器应用程序可以不进行通信，尽管一起安装。 在某些情况下，容器应用是实质上是一个空"shipping"的容器安装扩展后，就会提供其用途。 但是，如果规定的情况下，容器应用程序和扩展可能会共享公共区域中的资源。 此外，**今天扩展**可能会请求自己的容器应用程序来打开 URL。 此行为所示[事件倒计时小组件](https://github.com/xamarin/ios-samples/tree/master/intro-to-extensions)。

## <a name="creating-an-extension"></a>创建扩展

扩展 （和其容器应用程序） 必须是 64 位二进制文件和使用 Xamarin.iOS 生成[统一 Api](https://developer.xamarin.com/guides/cross-platform/macios/unified)。 在开发时扩展，你的解决方案将包含至少两个项目： 容器应用程序，另一个项目的每个扩展容器提供。 

### <a name="container-app-project-requirements"></a>容器应用程序项目要求

用于安装该扩展的容器应用具有以下要求：

- 它必须保留对扩展项目的引用。   
- 它必须是一个完整的应用 （必须能够启动并成功运行），即使它不执行任何操作超过提供的方法安装的扩展。 
- 它必须具有一个捆绑包标识符，是扩展的捆绑包标识符的基础项目 （请参见下面的更多详细信息部分）。

### <a name="extension-project-requirements"></a>扩展项目要求

此外，扩展的项目具有以下要求：

- 它必须具有以其容器应用的捆绑包标识符开头的捆绑标识符。 例如，如果容器应用的具有的捆绑包标识符`com.myCompany.ContainerApp`，可能是扩展的标识符`com.myCompany.ContainerApp.MyExtension`: 

    ![](extensions-images/bundleidentifiers.png) 
- 它必须定义该密匙`NSExtensionPointIdentifier`，使用适当的值 (如`com.apple.widget-extension`有关**今天**通知中心小组件)，请在其`Info.plist`文件。
- 它还必须定义*任一*`NSExtensionMainStoryboard`密钥或`NSExtensionPrincipalClass`键中其`Info.plist`文件使用适当的值：
    - 使用`NSExtensionMainStoryboard`键以指定为扩展显示的主要用户界面的情节提要的名称 (减去`.storyboard`)。 例如，`Main`为`Main.storyboard`文件。
    - 使用`NSExtensionPrincipalClass`键以指定启动扩展时将初始化的类。 值必须匹配**注册**的值在`UIViewController`: 

    ![](extensions-images/registerandprincipalclass.png)

特定类型的扩展可能具有其他要求。 例如，**今天**或**通知中心**扩展的主体类必须实现[INCWidgetProviding](xref:NotificationCenter.INCWidgetProviding)。

> [!IMPORTANT]
> 如果启动一个使用 Visual Studio for Mac 提供的扩展模板的项目，将提供并为您自动由模板满足大多数 （如果并非所有） 这些要求。

## <a name="walkthrough"></a>演练 

在下面的演练，你将创建一个示例**今天**小组件，用于计算日期和年份中的剩余天数：

[![](extensions-images/carpediemscreenshot-sm.png "计算的日期和年份中的剩余天数示例今天小组件")](extensions-images/carpediemscreenshot.png#lightbox)

### <a name="creating-the-solution"></a>创建解决方案

若要创建所需的解决方案，请执行以下操作：

1. 首先，创建一个新的 iOS**单一视图应用**项目，然后单击**下一步**按钮： 

    [![](extensions-images/today01.png "首先，创建新的 iOS、 单一视图应用程序项目并单击下一步按钮")](extensions-images/today01.png#lightbox)
2. 调用项目`TodayContainer`然后单击**下一步**按钮： 

    [![](extensions-images/today02.png "调用项目 TodayContainer，然后单击下一步按钮")](extensions-images/today02.png#lightbox)
3. 验证是否**项目名称**并**SolutionName**然后单击**创建**按钮以创建解决方案： 

    [![](extensions-images/today03.png "验证项目名称和解决方案名称，然后单击创建按钮以创建解决方案")](extensions-images/today03.png#lightbox)
4. 接下来，在**解决方案资源管理器**，右键单击解决方案并添加一个新**iOS 扩展**从项目**今天扩展**模板： 

    [![](extensions-images/today04.png "接下来，在解决方案资源管理器，右键单击解决方案并从今天扩展模板中添加新的 iOS 扩展项目")](extensions-images/today04.png#lightbox)
5. 调用项目`DaysRemaining`然后单击**下一步**按钮： 

    [![](extensions-images/today05.png "调用项目 DaysRemaining，然后单击下一步按钮")](extensions-images/today05.png#lightbox)
6. 查看项目，然后单击**创建**按钮来创建它： 

    [![](extensions-images/today06.png "检查项目，并单击创建按钮来创建它")](extensions-images/today06.png#lightbox)

生成的解决方案现在应具有两个项目，如下所示：

[![](extensions-images/today07.png "生成的解决方案现在应有两个项目，如下所示")](extensions-images/today07.png#lightbox)

### <a name="creating-the-extension-user-interface"></a>创建扩展用户界面

接下来，你将需要设计的界面您**今天**小组件。 这既可以使用情节提要或通过在代码中创建用户界面。 这两种方法会将详细介绍如下。

#### <a name="using-storyboards"></a>使用演示图板

若要生成使用情节提要 UI，执行以下操作：

1. 在中**解决方案资源管理器**，双击扩展项目`Main.storyboard`文件将其打开进行编辑： 

    [![](extensions-images/today08.png "双击要打开以进行编辑的扩展项目 Main.storyboard 文件")](extensions-images/today08.png#lightbox)
2. 选择的标签，已自动添加到 UI 模板并为其提供**名称**`TodayMessage`中**小组件**选项卡**属性资源管理器**: 

    [![](extensions-images/today09.png "选择的标签，已自动添加到 UI 模板并为其提供在属性资源管理器小组件选项卡名称 TodayMessage")](extensions-images/today09.png#lightbox)
3. 将所做的更改保存到情节提要。

#### <a name="using-code"></a>使用代码

若要生成的 UI 代码中，执行以下操作： 

1. 在中**解决方案资源管理器**，选择**DaysRemaining**项目中，添加一个新类并调用其`CodeBasedViewController`: 

    [![](extensions-images/code01.png "Aelect DaysRemaining 项目中，添加一个新类，调用它 CodeBasedViewController")](extensions-images/code01.png#lightbox)
2. 同样，在**解决方案资源管理器**，双击扩展的`Info.plist`文件将其打开进行编辑： 

    [![](extensions-images/code02.png "双击要打开以进行编辑的扩展 Info.plist 文件")](extensions-images/code02.png#lightbox)
3. 选择**源视图**（从屏幕的底部），然后打开`NSExtension`节点： 

    [![](extensions-images/code03.png "从屏幕的底部选择源视图，然后打开 NSExtension 节点")](extensions-images/code03.png#lightbox)
4. 删除`NSExtensionMainStoryboard`密钥，然后添加`NSExtensionPrincipalClass`具有值`CodeBasedViewController`: 

    [![](extensions-images/code04.png "删除 NSExtensionMainStoryboard 密钥，并添加值 CodeBasedViewController NSExtensionPrincipalClass")](extensions-images/code04.png#lightbox)
5. 保存更改。

接下来，编辑`CodeBasedViewController.cs`文件，并使其看起来如下所示：

```csharp
using System;
using Foundation;
using UIKit;
using NotificationCenter;
using CoreGraphics;

namespace DaysRemaining
{
    [Register("CodeBasedViewController")]
    public class CodeBasedViewController : UIViewController, INCWidgetProviding
    {
        public CodeBasedViewController ()
        {
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Add label to view
            var TodayMessage = new UILabel (new CGRect (0, 0, View.Frame.Width, View.Frame.Height)) {
                TextAlignment = UITextAlignment.Center
            };

            View.AddSubview (TodayMessage);
            
            // Insert code to power extension here...

        }
    }
}
```

请注意，`[Register("CodeBasedViewController")]`与为指定的值匹配`NSExtensionPrincipalClass`上面。

### <a name="coding-the-extension"></a>编码扩展

用户界面创建后，打开`TodayViewController.cs`或`CodeBasedViewController.cs`（基于用于创建更高版本的用户界面的方法），文件更改**ViewDidLoad**方法，并使其如下所示：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Calculate the values
    var dayOfYear = DateTime.Now.DayOfYear;
    var leapYearExtra = DateTime.IsLeapYear (DateTime.Now.Year) ? 1 : 0;
    var daysRemaining = 365 + leapYearExtra - dayOfYear;

    // Display the message
    if (daysRemaining == 1) {
        TodayMessage.Text = String.Format ("Today is day {0}. There is one day remaining in the year.", dayOfYear);
    } else {
        TodayMessage.Text = String.Format ("Today is day {0}. There are {1} days remaining in the year.", dayOfYear, daysRemaining);
    }
}
```

如果使用基于代码的用户界面方法，将为`// Insert code to power extension here...`使用上面提供的新代码的注释。 之后调用基实现 （和插入的代码基于版本标签），此代码执行简单计算，以获取按年和剩余多少天的日期。 然后，它在该标签中显示消息 (`TodayMessage`) 在 UI 设计中创建。

请注意此过程为编写应用程序的正常过程的相似程度。 扩展的`UIViewController`只不过扩展没有后台模式并不会挂起时用户已完成在应用中，具有相同的生命周期视图控制器使用它们。 相反，扩展重复初始化并根据需要取消分配。

### <a name="creating-the-container-app-user-interface"></a>创建容器应用程序用户界面

本演练中，容器应用只需用作一种方法用来传送和安装该扩展，并不提供其自己的任何功能。 编辑 TodayContainer`Main.storyboard`文件，并添加一些文本定义的扩展函数和如何安装它：

[![](extensions-images/today10.png "编辑 TodayContainers Main.storyboard 文件并添加一些文本定义扩展函数和如何安装它")](extensions-images/today10.png#lightbox)

将所做的更改保存到情节提要。

### <a name="testing-the-extension"></a>测试扩展

若要在 iOS 模拟器中测试你的扩展，运行**TodayContainer**应用。 将显示容器的主视图：

[![](extensions-images/run01.png "将显示容器的主视图")](extensions-images/run01.png#lightbox)

接下来，点击**主页**按钮在模拟器中，若要打开的屏幕顶部向下轻扫**通知中心**，选择**今天**卡，然后单击**编辑**按钮：

[![](extensions-images/run02.png "点击主页按钮在模拟器中，从要打开通知中心，选择今天选项卡，然后单击编辑按钮的屏幕顶部向下轻扫")](extensions-images/run02.png#lightbox)

添加**DaysRemaining**扩展**今天**查看，然后单击**完成**按钮：

[![](extensions-images/run03.png "将 DaysRemaining 扩展添加到今日视图，然后单击完成按钮")](extensions-images/run03.png#lightbox)

新的小组件将添加到**今天**视图和结果将显示：

[![](extensions-images/run04.png "新的小组件将添加到今日视图，并将显示结果")](extensions-images/run04.png#lightbox)

## <a name="communicating-with-the-host-app"></a>与主机应用程序进行通信

今天扩展上面创建的示例不使用其主机应用程序进行通信 (**今天**屏幕)。 如果有的话，它将使用[ExtensionContext](xref:Foundation.NSExtensionContext)的属性`TodayViewController`或`CodeBasedViewController`类。 

对于将从其主机应用接收数据的扩展，数据采用的数组的形式[NSExtensionItem](xref:Foundation.NSExtensionItem)对象存储在[InputItems](xref:Foundation.NSExtensionContext.InputItems)属性[ExtensionContext](xref:Foundation.NSExtensionContext)的扩展的`UIViewController`。

其他扩展名，例如照片编辑扩展，可能会区分用户完成或取消使用情况。 这将返回到主机应用程序通过发出信号[CompleteRequest](xref:Foundation.NSExtensionContext.CompleteRequest*)并[CancelRequest](xref:Foundation.NSExtensionContext.CancelRequest*)方法[ExtensionContext](xref:Foundation.NSExtensionContext)属性。

有关详细信息，请参阅 Apple[应用程序扩展编程指南](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214-CH20-SW1)。

## <a name="communicating-with-the-parent-app"></a>与父应用程序进行通信

应用组允许不同的应用程序（或一个应用程序及其扩展）访问共享文件存储位置。 应用组可以用于如下所示的数据：

- [Apple Watch 设置](~/ios/watchos/app-fundamentals/settings.md)。
- [共享 NSUserDefaults](~/ios/app-fundamentals/user-defaults.md)。
- [共享文件](~/ios/watchos/app-fundamentals/parent-app.md#files)。

有关详细信息，请参阅[应用组](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md)一部分我们**使用功能**文档。

## <a name="mobilecoreservices"></a>MobileCoreServices

在使用扩展，使用统一类型标识符 (UTI) 创建和操作应用程序、 其他应用程序和/或服务之间交换的数据。

`MobileCoreServices.UTType`静态类定义以下帮助程序属性与 Apple 的`kUTType...`定义：

- `kUTTypeAlembic` - `Alembic`
- `kUTTypeAliasFile` - `AliasFile`
- `kUTTypeAliasRecord` - `AliasRecord`
- `kUTTypeAppleICNS` - `AppleICNS`
- `kUTTypeAppleProtectedMPEG4Audio` - `AppleProtectedMPEG4Audio`
- `kUTTypeAppleProtectedMPEG4Video` - `AppleProtectedMPEG4Video`
- `kUTTypeAppleScript` - `AppleScript`
- `kUTTypeApplication` - `Application`
- `kUTTypeApplicationBundle` - `ApplicationBundle`
- `kUTTypeApplicationFile` - `ApplicationFile`
- `kUTTypeArchive` - `Archive`
- `kUTTypeAssemblyLanguageSource` - `AssemblyLanguageSource`
- `kUTTypeAudio` - `Audio`
- `kUTTypeAudioInterchangeFileFormat` - `AudioInterchangeFileFormat`
- `kUTTypeAudiovisualContent` - `AudiovisualContent`
- `kUTTypeAVIMovie` - `AVIMovie`
- `kUTTypeBinaryPropertyList` - `BinaryPropertyList`
- `kUTTypeBMP` - `BMP`
- `kUTTypeBookmark` - `Bookmark`
- `kUTTypeBundle` - `Bundle`
- `kUTTypeBzip2Archive` - `Bzip2Archive`
- `kUTTypeCalendarEvent` - `CalendarEvent`
- `kUTTypeCHeader` - `CHeader`
- `kUTTypeCommaSeparatedText` - `CommaSeparatedText`
- `kUTTypeCompositeContent` - `CompositeContent`
- `kUTTypeConformsToKey` - `ConformsToKey`
- `kUTTypeContact` - `Contact`
- `kUTTypeContent` - `Content`
- `kUTTypeCPlusPlusHeader` - `CPlusPlusHeader`
- `kUTTypeCPlusPlusSource` - `CPlusPlusSource`
- `kUTTypeCSource` - `CSource`
- `kUTTypeData` - `Database`
- `kUTTypeDelimitedText` - `DelimitedText`
- `kUTTypeDescriptionKey` - `DescriptionKey`
- `kUTTypeDirectory` - `Directory`
- `kUTTypeDiskImage` - `DiskImage`
- `kUTTypeElectronicPublication` - `ElectronicPublication`
- `kUTTypeEmailMessage` - `EmailMessage`
- `kUTTypeExecutable` - `Executable`
- `kUTExportedTypeDeclarationsKey` - `ExportedTypeDeclarationsKey`
- `kUTTypeFileURL` - `FileURL`
- `kUTTypeFlatRTFD` - `FlatRTFD`
- `kUTTypeFolder` - `Folder`
- `kUTTypeFont` - `Font`
- `kUTTypeFramework` - `Framework`
- `kUTTypeGIF` - `GIF`
- `kUTTypeGNUZipArchive` - `GNUZipArchive` 
- `kUTTypeHTML` - `HTML`
- `kUTTypeICO` - `ICO`
- `kUTTypeIconFileKey` - `IconFileKey`
- `kUTTypeIdentifierKey` - `IdentifierKey`
- `kUTTypeImage` - `Image`
- `kUTImportedTypeDeclarationsKey` - `ImportedTypeDeclarationsKey`
- `kUTTypeInkText` - `InkText`
- `kUTTypeInternetLocation` - `InternetLocation`
- `kUTTypeItem` - `Item`
- `kUTTypeJavaArchive` - `JavaArchive`
- `kUTTypeJavaClass` - `JavaClass`
- `kUTTypeJavaScript` - `JavaScript`
- `kUTTypeJavaSource` - `JavaSource`
- `kUTTypeJPEG` - `JPEG`
- `kUTTypeJPEG2000` - `JPEG2000`
- `kUTTypeJSON` - `JSON`
- `kUTType3dObject` - `k3dObject`
- `kUTTypeLivePhoto` - `LivePhoto`
- `kUTTypeLog` - `Log` 
- `kUTTypeM3UPlaylist` - `M3UPlaylist`
- `kUTTypeMessage` - `Message`
- `kUTTypeMIDIAudio` - `MIDIAudio`
- `kUTTypeMountPoint` - `MountPoint`
- `kUTTypeMovie` - `Movie`
- `kUTTypeMP3` - `MP3`
- `kUTTypeMPEG` - `MPEG`
- `kUTTypeMPEG2TransportStream` - `MPEG2TransportStream`
- `kUTTypeMPEG2Video` - `MPEG2Video`
- `kUTTypeMPEG4` - `MPEG4`
- `kUTTypeMPEG4Audio` - `MPEG4Audio`
- `kUTTypeObjectiveCPlusPlusSource` - `ObjectiveCPlusPlusSource`
- `kUTTypeObjectiveCSource` - `ObjectiveCSource`
- `kUTTypeOSAScript` - `OSAScript`
- `kUTTypeOSAScriptBundle` - `OSAScriptBundle`
- `kUTTypePackage` - `Package`
- `kUTTypePDF` - `PDF`
- `kUTTypePerlScript` - `PerlScript`
- `kUTTypePHPScript` - `PHPScript`
- `kUTTypePICT` - `PICT`
- `kUTTypePKCS12` - `PKCS12`
- `kUTTypePlainText` - `PlainText`
- `kUTTypePlaylist` - `Playlist`
- `kUTTypePluginBundle` - `PluginBundle`
- `kUTTypePNG` - `PNG`
- `kUTTypePolygon` - `Polygon`
- `kUTTypePresentation` - `Presentation`
- `kUTTypePropertyList` - `PropertyList`
- `kUTTypePythonScript` - `PythonScript`
- `kUTTypeQuickLookGenerator` - `QuickLookGenerator`
- `kUTTypeQuickTimeImage` - `QuickTimeImage`
- `kUTTypeQuickTimeMovie` - `QuickTimeMovie` 
- `kUTTypeRawImage` - `RawImage`
- `kUTTypeReferenceURLKey` - `ReferenceURLKey`
- `kUTTypeResolvable` - `Resolvable`
- `kUTTypeRTF` - `RTF`
- `kUTTypeRTFD` - `RTFD`
- `kUTTypeRubyScript` - `RubyScript`
- `kUTTypeScalableVectorGraphics` - `ScalableVectorGraphics`
- `kUTTypeScript` - `Script`
- `kUTTypeShellScript` - `ShellScript`
- `kUTTypeSourceCode` - `SourceCode`
- `kUTTypeSpotlightImporter` - `SpotlightImporter`
- `kUTTypeSpreadsheet` - `Spreadsheet`
- `kUTTypeStereolithography` - `Stereolithography`
- `kUTTypeSwiftSource` - `SwiftSource`
- `kUTTypeSymLink` - `SymLink`
- `kUTTypeSystemPreferencesPane` - `SystemPreferencesPane`
- `kUTTypeTabSeparatedText` - `TabSeparatedText`
- `kUTTagClassFilenameExtension` - `TagClassFilenameExtension`
- `kUTTagClassMIMEType` - `TagClassMIMEType`
- `kUTTypeTagSpecificationKey` - `TagSpecificationKey`
- `kUTTypeText` - `Text`
- `kUTType3DContent` - `ThreeDContent`
- `kUTTypeTIFF` - `TIFF`
- `kUTTypeToDoItem` - `ToDoItem`
- `kUTTypeTXNTextAndMultimediaData` - `TXNTextAndMultimediaData`
- `kUTTypeUniversalSceneDescription` - `UniversalSceneDescription`
- `kUTTypeUnixExecutable` - `UnixExecutable`
- `kUTTypeURL` - `URL` 
- `kUTTypeURLBookmarkData` - `URLBookmarkData`
- `kUTTypeUTF16ExternalPlainText` - `UTF16ExternalPlainText`
- `kUTTypeUTF16PlainText` - `UTF16PlainText`
- `kUTTypeUTF8PlainText` - `UTF8PlainText`
- `kUTTypeUTF8TabSeparatedText` - `UTF8TabSeparatedText`
- `kUTTypeVCard` - `VCard`
- `kUTTypeVersionKey` - `VersionKey` 
- `kUTTypeVideo` - `Video` 
- `kUTTypeVolume` - `Volume` 
- `kUTTypeWaveformAudio` - `WaveformAudio`
- `kUTTypeWebArchive` - `WebArchive`
- `kUTTypeWindowsExecutable` - `WindowsExecutable`
- `kUTTypeX509Certificate` - `X509Certificate`
- `kUTTypeXML` - `XML`
- `kUTTypeXMLPropertyList` - `XMLPropertyList`
- `kUTTypeXPCService` - `XPCService`
- `kUTTypeZipArchive` - `ZipArchive`

请参阅下面的示例：

```csharp
using MobileCoreServices;
...

NSItemProvider itemProvider = new NSItemProvider ();
itemProvider.LoadItem(UTType.PropertyList ,null, (item, err) => {
    if (err == null) {
        NSDictionary results = (NSDictionary )item;
        NSString baseURI =
results.ObjectForKey("NSExtensionJavaScriptPreprocessingResultsKey");
    }
});
```

有关详细信息，请参阅[应用组](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md)一部分我们**使用功能**文档。

## <a name="precautions-and-considerations"></a>预防措施和注意事项

扩展具有更少的内存提供给他们不是应用程序这样做。 它们应执行快速和具有对用户和应用程序中托管的最小入侵。 但是，扩展还应提供一个经过品牌打造的用户界面，允许用户标识扩展的开发人员与正在使用的应用或其所属的容器应用的独特、 有用函数。

给定这些严格要求，应仅将部署扩展，它已彻底进行测试和优化性能和内存消耗。 

## <a name="summary"></a>总结

本文档已介绍了扩展，它们是什么，扩展点以及由 iOS 规定的扩展的已知的限制的类型。 它介绍了创建、 分发、 安装和运行扩展和扩展生命周期。 提供创建一个简单的演练**今天**小组件显示两种方式创建小组件的 UI 使用情节提要或代码。 它介绍了如何在 iOS 模拟器中测试扩展。 最后，它简要介绍了与主机应用程序和一些预防措施和开发扩展时应采取的注意事项进行通信。 

## <a name="related-links"></a>相关链接

- [ContainerApp （示例）](https://developer.xamarin.com/samples/monotouch/intro-to-extensions)
- [在 Xamarin.iOS 中创建扩展 （视频）](https://university.xamarin.com/lightninglectures/creating-extensions-in-ios)
