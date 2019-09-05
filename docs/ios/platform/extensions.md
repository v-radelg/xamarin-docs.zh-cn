---
title: Xamarin 中的 iOS 扩展
description: 本文档介绍扩展，这些扩展是 iOS 在标准上下文中提供的小组件，如通知中心内。 它讨论了如何通过父应用程序创建扩展并与其进行通信。
ms.prod: xamarin
ms.assetid: 3DEB3D43-3E4A-4099-8331-93C1E7A77095
ms.technology: xamarin-ios
ms.custom: xamu-video
author: conceptdev
ms.author: crdun
ms.date: 03/22/2017
ms.openlocfilehash: 5995ba06873b2fb5f75c593fbc7136806e50d982
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290607"
---
# <a name="ios-extensions-in-xamarinios"></a>Xamarin 中的 iOS 扩展

> [!VIDEO https://youtube.com/embed/Sd0-ch9Udmk]

**在 iOS 视频中创建扩展**

Ios 8 中引入的扩展是专用`UIViewControllers`的，由 ios 在**通知中心**内的标准上下文中提供，作为用户请求的自定义键盘类型以执行专用输入或其他上下文例如，编辑可提供特殊效果筛选器的照片。

所有扩展都与容器应用一起安装（同时使用64位统一 Api 编写两个元素），并从主机应用程序中的特定扩展点激活。 而且，由于它们将用作对现有系统功能的补充，因此它们必须是高性能、精益和稳健。 

## <a name="extension-points"></a>扩展点

|类型|描述|扩展点|主机应用|
|--- |--- |--- |--- |
|操作|特定媒体类型的专用编辑器或查看器|`com.apple.ui-services`|任意|
|文档提供程序|允许应用使用远程文档存储|`com.apple.fileprovider-ui`|使用[UIDocumentPickerViewController](xref:UIKit.UIDocumentPickerViewController)的应用|
|键盘|备用键盘|`com.apple.keyboard-service`|任意|
|照片编辑|照片操作和编辑|`com.apple.photo-editing`|照片应用编辑器|
|共享|与社交网络、消息服务等共享数据。|`com.apple.share-services`|任意|
|今天|出现在 "今天" 屏幕或通知中心的 "小组件"|`com.apple.widget-extensions`|今天和通知中心|

IOS 10 中添加了[其他扩展点](~/ios/platform/introduction-to-ios10/index.md#app-extensions)。

## <a name="limitations"></a>限制

扩展有很多限制，其中一些限制为所有类型（例如，没有类型的扩展可以访问相机或麦克风），而其他类型的扩展可能对其使用有特定限制（例如，自定义键盘）。不能用于安全数据项（例如密码）。 

通用限制如下：

- [运行状况工具包](~/ios/platform/healthkit.md)和[事件工具包 UI](~/ios/platform/eventkit.md)框架不可用
- 扩展无法使用[扩展后台模式](~/ios/app-fundamentals/backgrounding/ios-backgrounding-techniques/registering-applications-to-run-in-background.md)
- 扩展无法访问设备的照相机或麦克风（尽管可能会访问现有的媒体文件）
- 扩展无法接收空气丢弃数据（尽管它们可以通过空气丢弃传输数据）
- [UIActionSheet](xref:UIKit.UIActionSheet)和[UIAlertView](xref:UIKit.UIAlertView)不可用;扩展必须使用[UIAlertController](xref:UIKit.UIAlertController)
- [UIApplication](xref:UIKit.UIApplication)的多个成员不可用：[UIApplication.SharedApplication](xref:UIKit.UIApplication.SharedApplication), [UIApplication.OpenUrl](xref:UIKit.UIApplication.OpenUrl(Foundation.NSUrl)), [UIApplication.BeginIgnoringInteractionEvents](xref:UIKit.UIApplication.BeginIgnoringInteractionEvents) and [UIApplication.EndIgnoringInteractionEvents](xref:UIKit.UIApplication.EndIgnoringInteractionEvents)
- iOS 在当前扩展上强制实施 16 MB 的内存使用量限制。
- 默认情况下，键盘扩展没有网络访问权限。 这会影响设备上的调试（模拟器中不会强制使用限制），因为 Xamarin 需要网络访问权限才能进行调试。 可以通过将项目的 info.plist 中的`Requests Open Access`值设置为来`Yes`请求网络访问。 有关键盘扩展限制的详细信息，请参阅 Apple 的[自定义键盘指南](https://developer.apple.com/library/content/documentation/General/Conceptual/ExtensibilityPG/CustomKeyboard.html)。

有关单独的限制，请参阅 Apple 的[应用扩展编程指南](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/)。

## <a name="distributing-installing-and-running-extensions"></a>分发、安装和运行扩展

扩展是从容器应用内分发的，后者反过来会通过 App Store 提交和分发。 此时将安装与应用一起分发的扩展，但用户必须显式启用每个扩展。 不同类型的扩展以不同的方式启用;需要用户导航到 "**设置**" 应用，并从中启用。 其他人可以在使用时启用，如在发送照片时启用共享扩展。 

使用扩展的应用程序（用户遇到扩展点）称为**宿主应用**，因为它是在执行时承载扩展的应用程序。 安装扩展的应用是**容器应用**，因为它是安装时包含扩展的应用。  

通常，容器应用描述扩展，并引导用户完成启用该扩展的过程。

## <a name="extension-lifecycle"></a>扩展生命周期

扩展可以像提供多个屏幕 UI 的单个[UIViewController](xref:UIKit.UIViewController)或更复杂的扩展一样简单。 当用户遇到_扩展点_（例如共享映像时）时，他们将有机会从为该扩展点注册的扩展中进行选择。 

如果他们选择应用的扩展之一，则会实例`UIViewController`化它并开始正常的视图控制器生命周期。 但是，与普通应用程序不同，该应用程序在用户与之交互后会被挂起但一般不会终止，而是加载、执行并重复终止扩展。

扩展可以通过[NSExtensionContext](xref:Foundation.NSExtensionContext)对象与其主机应用通信。 某些扩展具有接收包含结果的异步回调的操作。 这些回调将在后台线程上执行，扩展必须考虑到这一点;例如，如果要更新用户界面，请使用[InvokeOnMainThread。](xref:Foundation.NSObject.InvokeOnMainThread*) 有关更多详细信息，请参阅下面的[与主机应用通信](#communicating-with-the-host-app)部分。

默认情况下，即使一起安装，扩展及其容器应用仍无法通信。 在某些情况下，容器应用实质上是一个空的 "发货" 容器，其用途是在安装扩展后提供的。 但是，如果环境规定，则容器应用和扩展可以共享公共区域中的资源。 此外，**今日扩展**可能会请求其容器应用打开 URL。 此行为显示在[事件倒计时小组件](https://github.com/xamarin/ios-samples/tree/master/intro-to-extensions)中。

## <a name="creating-an-extension"></a>创建扩展

扩展（及其容器应用）必须是64位二进制文件，并使用 Xamarin iOS[统一 api](~/cross-platform/macios/unified/index.md)构建。 开发扩展时，解决方案将包含至少两个项目：容器应用和容器提供的每个扩展的一个项目。

### <a name="container-app-project-requirements"></a>容器应用项目要求

用于安装扩展的容器应用具有以下要求：

- 它必须维护对扩展项目的引用。   
- 它必须是一个完整的应用程序（必须能够成功启动和运行），即使它只是提供安装扩展的方法。 
- 它必须具有一个捆绑标识符，它是扩展项目的捆绑标识符的基础（有关详细信息，请参阅以下部分）。

### <a name="extension-project-requirements"></a>扩展项目要求

此外，该扩展的项目具有以下要求：

- 它必须有一个以其容器应用的捆绑标识符开头的捆绑标识符。 例如，如果容器应用的包标识符`com.myCompany.ContainerApp`为，则该扩展的标识符可能是： `com.myCompany.ContainerApp.MyExtension` 

  ![](extensions-images/bundleidentifiers.png) 
- 它必须`NSExtensionPointIdentifier`定义密钥，并在其`Info.plist`文件中使用适当的`com.apple.widget-extension`值（例如，对于**今天**通知中心小组件）。
- 它还必须在其`NSExtensionMainStoryboard` `NSExtensionPrincipalClass` `Info.plist`文件中使用*适当的值定义密钥*或密钥：
  - 使用 " `NSExtensionMainStoryboard`密钥" 指定显示扩展（减号`.storyboard`）的主 UI 的情节提要的名称。 例如， `Main` `Main.storyboard`对于文件。
  - `NSExtensionPrincipalClass`使用键指定启动扩展时要初始化的类。 该值必须与的**Register**值`UIViewController`匹配： 

  ![](extensions-images/registerandprincipalclass.png)

特定类型的扩展可能具有其他要求。 例如，**今天**或**通知中心**的主体类必须实现[INCWidgetProviding](xref:NotificationCenter.INCWidgetProviding)。

> [!IMPORTANT]
> 如果你使用 Visual Studio for Mac 提供的扩展模板来启动项目，则模板将为你自动提供并满足这些要求。

## <a name="walkthrough"></a>演练 

在下面的演练中，你将创建一个示例 "**今日**" 小组件来计算一年中剩余的日期和天数：

[![](extensions-images/carpediemscreenshot-sm.png "计算一年中的日期和剩余天数的示例今日小组件")](extensions-images/carpediemscreenshot.png#lightbox)

### <a name="creating-the-solution"></a>创建解决方案

若要创建所需的解决方案，请执行以下操作：

1. 首先，创建一个新的 iOS、**单一视图应用**项目，然后单击 "**下一步**" 按钮： 

    [![](extensions-images/today01.png "首先，创建一个新的 iOS、单一视图应用项目，然后单击 \"下一步\" 按钮")](extensions-images/today01.png#lightbox)
2. 调用项目`TodayContainer` ，然后单击 "**下一步**" 按钮： 

    [![](extensions-images/today02.png "调用项目 TodayContainer，并单击 \"下一步\" 按钮")](extensions-images/today02.png#lightbox)
3. 验证**项目名称**和**解决方案**名称，并单击 "**创建**" 按钮以创建解决方案： 

    [![](extensions-images/today03.png "验证项目名称和解决方案名称，并单击 \"创建\" 按钮创建解决方案")](extensions-images/today03.png#lightbox)
4. 接下来，在 "**解决方案资源管理器**中，右键单击解决方案并从"**今日扩展**"模板添加新的**iOS 扩展**项目： 

    [![](extensions-images/today04.png "接下来，在 \"解决方案资源管理器中，右键单击解决方案并从\" 今日扩展 \"模板添加新的 iOS 扩展项目")](extensions-images/today04.png#lightbox)
5. 调用项目`DaysRemaining` ，然后单击 "**下一步**" 按钮： 

    [![](extensions-images/today05.png "调用项目 DaysRemaining，并单击 \"下一步\" 按钮")](extensions-images/today05.png#lightbox)
6. 查看项目，然后单击 "**创建**" 按钮以创建它： 

    [![](extensions-images/today06.png "查看项目，并单击 \"创建\" 按钮以创建它")](extensions-images/today06.png#lightbox)

生成的解决方案现在应具有两个项目，如下所示：

[![](extensions-images/today07.png "生成的解决方案现在应具有两个项目，如下所示")](extensions-images/today07.png#lightbox)

### <a name="creating-the-extension-user-interface"></a>创建扩展用户界面

接下来，你将需要为**今日**小组件设计接口。 这可以使用情节提要完成，也可以通过代码创建 UI 来完成。 下面将详细介绍这两种方法。

#### <a name="using-storyboards"></a>使用情节提要

若要使用情节提要生成 UI，请执行以下操作：

1. 在**解决方案资源管理器**中，双击扩展项目的`Main.storyboard`文件以将其打开进行编辑： 

    [![](extensions-images/today08.png "双击扩展项目的 \"storyboard\" 文件以将其打开以供编辑")](extensions-images/today08.png#lightbox)
2. 选择按模板自动添加到 UI 的标签，然后在 "**属性资源管理器**" 的 **"小组件**" 选项卡中为其指定**名称** `TodayMessage` ： 

    [![](extensions-images/today09.png "选择按模板自动添加到 UI 的标签，并在属性资源管理器的 \"小组件\" 选项卡中为其指定名称 TodayMessage")](extensions-images/today09.png#lightbox)
3. 保存对情节提要所做的更改。

#### <a name="using-code"></a>使用代码

若要在代码中生成 UI，请执行以下操作： 

1. 在**解决方案资源管理器**中，选择**DaysRemaining**项目，添加一个新类并调用它`CodeBasedViewController`： 

    [![](extensions-images/code01.png "Aelect DaysRemaining 项目中，添加一个新类并将其调用 CodeBasedViewController")](extensions-images/code01.png#lightbox)
2. 同样，在**解决方案资源管理器**中，双击扩展`Info.plist`文件以将其打开进行编辑： 

    [![](extensions-images/code02.png "双击 \"Extension info.plist\" 文件以将其打开以供编辑")](extensions-images/code02.png#lightbox)
3. 选择**源视图**（位于屏幕底部），并打开`NSExtension`节点： 

    [![](extensions-images/code03.png "选择屏幕底部的 \"源\" 视图，然后打开 \"NSExtension\" 节点")](extensions-images/code03.png#lightbox)
4. 删除密钥并`NSExtensionPrincipalClass`添加具有以下值`CodeBasedViewController`的： `NSExtensionMainStoryboard` 

    [![](extensions-images/code04.png "删除 NSExtensionMainStoryboard 项并添加值为 CodeBasedViewController 的 NSExtensionPrincipalClass")](extensions-images/code04.png#lightbox)
5. 保存更改。

接下来，编辑`CodeBasedViewController.cs`文件，使其类似于以下内容：

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

请注意，与你`NSExtensionPrincipalClass`为上述指定的值匹配。`[Register("CodeBasedViewController")]`

### <a name="coding-the-extension"></a>编码扩展

创建用户界面后，打开`TodayViewController.cs` `CodeBasedViewController.cs`或文件（基于用于创建用户界面的方法），更改**ViewDidLoad**方法并使其类似于以下内容：

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

如果使用基于代码的用户界面方法，请将`// Insert code to power extension here...`注释替换为上面的新代码。 在调用基实现（并为基于代码的版本插入标签）后，此代码将执行简单计算以获取一年中的某一天以及剩余多少天。 然后，它将在用户界面设计中`TodayMessage`创建的标签（）中显示消息。

请注意，此过程与编写应用程序的正常过程的相似之处。 扩展与应用`UIViewController`中的视图控制器具有相同的生命周期，只不过扩展没有背景模式，并且在用户使用完毕后不会被挂起。 相反，扩展会根据需要反复初始化和取消分配。

### <a name="creating-the-container-app-user-interface"></a>创建容器应用用户界面

对于本演练，容器应用仅用作提供和安装扩展的方法，并不提供自己的功能。 编辑 TodayContainer 的`Main.storyboard`文件并添加一些定义扩展功能的文本，以及如何安装它：

[![](extensions-images/today10.png "编辑 TodayContainers 的主情节提要文件，并添加一些定义 Extension 函数的文本以及如何安装它")](extensions-images/today10.png#lightbox)

保存对情节提要所做的更改。

### <a name="testing-the-extension"></a>测试扩展

若要在 iOS 模拟器中测试扩展，请运行**TodayContainer**应用。 将显示容器的主视图：

[![](extensions-images/run01.png "将显示容器主视图")](extensions-images/run01.png#lightbox)

接下来，单击模拟器中的 "**主页**" 按钮，从屏幕顶部向下轻扫，打开**通知中心**，选择 "**今天**" 选项卡，然后单击 "**编辑**" 按钮：

[![](extensions-images/run02.png "点击模拟器中的 \"主页\" 按钮，从屏幕顶部向下轻扫，打开通知中心，选择 \"今天\" 选项卡，然后单击 \"编辑\" 按钮")](extensions-images/run02.png#lightbox)

将**DaysRemaining**扩展添加到 "**今日**" 视图，然后单击 "**完成**" 按钮：

[![](extensions-images/run03.png "将 DaysRemaining 扩展添加到 \"今日\" 视图，然后单击 \"完成\" 按钮")](extensions-images/run03.png#lightbox)

新小组件将添加到 "**今日**" 视图中，结果将显示：

[![](extensions-images/run04.png "新小组件将添加到 \"今日\" 视图，结果将显示")](extensions-images/run04.png#lightbox)

## <a name="communicating-with-the-host-app"></a>与宿主应用程序进行通信

前面创建的示例 "今日扩展" 不与其主机应用（"**当前**" 屏幕）通信。 如果已完成, 则它将使用 `TodayViewController`或`CodeBasedViewController`类的 [ExtensionContext](xref:Foundation.NSExtensionContext) 属性。 

对于将从其主机应用接收数据的扩展，数据以[NSExtensionItem](xref:Foundation.NSExtensionItem)对象数组的形式存储在扩展的`UIViewController` [ExtensionContext 的](xref:Foundation.NSExtensionContext)的[InputItems](xref:Foundation.NSExtensionContext.InputItems)属性中。

其他扩展，如照片编辑扩展，可以区分用户完成或取消使用情况。 这会通过[ExtensionContext](xref:Foundation.NSExtensionContext)属性的[CompleteRequest](xref:Foundation.NSExtensionContext.CompleteRequest*)和[CancelRequest](xref:Foundation.NSExtensionContext.CancelRequest*)方法发回主机应用。

有关详细信息，请参阅 Apple 的[应用扩展编程指南](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214-CH20-SW1)。

## <a name="communicating-with-the-parent-app"></a>与父应用程序通信

应用组允许不同的应用程序（或一个应用程序及其扩展）访问共享文件存储位置。 应用组可以用于如下所示的数据：

- [Apple Watch 设置](~/ios/watchos/app-fundamentals/settings.md)。
- [共享 NSUserDefaults](~/ios/app-fundamentals/user-defaults.md)。
- [共享文件](~/ios/watchos/app-fundamentals/parent-app.md#files)。

有关详细信息，请参阅我们使用**功能**文档中的 "[应用组](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md)" 部分。

## <a name="mobilecoreservices"></a>MobileCoreServices

使用扩展时，使用统一类型标识符（UTI）来创建和操作在应用、其他应用和/或服务之间交换的数据。

静态类定义以下与 Apple 的`kUTType...`定义相关的帮助器属性： `MobileCoreServices.UTType`

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

请参阅以下示例：

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

有关详细信息，请参阅我们使用**功能**文档中的 "[应用组](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md)" 部分。

## <a name="precautions-and-considerations"></a>注意事项和注意事项

对于应用程序而言，扩展的可用内存要少得多。 它们应该会快速执行，并将用户及其所在的应用程序的最小入侵权降到最低。 但是，扩展还应为使用中的应用程序提供一个独特的有用函数，该函数允许用户标识其所属的开发人员或容器应用程序。

考虑到这些严格要求，只应部署经过全面测试和优化的扩展，以提高性能和内存消耗。 

## <a name="summary"></a>总结

本文档包含扩展、扩展的类型、扩展点的类型以及 iOS 对扩展施加的已知限制。 本文介绍了如何创建、分发、安装和运行扩展和扩展生命周期。 其中提供了创建简单的 "**今日**" 小组件的演练，其中显示了使用情节提要或代码创建小组件 UI 的两种方法。 其中介绍了如何在 iOS 模拟器中测试扩展。 最后，它简要介绍了如何与主机应用程序进行通信，以及在开发扩展时应采取的一些预防措施和注意事项。 

## <a name="related-links"></a>相关链接

- [ContainerApp （示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/intro-to-extensions)
- [在 Xamarin 中创建扩展（视频）](https://university.xamarin.com/lightninglectures/creating-extensions-in-ios)
