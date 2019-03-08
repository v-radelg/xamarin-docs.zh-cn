---
title: watchOS 故障排除
description: 本文档讨论的已知的问题和解决方法的 watchOS 使用 Xamarin 进行开发。 它描述了与手动添加界面控制器文件，启动命令行中，watch 应用的问题及其他映像。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 27C31DB8-451E-4888-BBC1-CE0DFC2F9DEC
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 70ef341c066c77e214761d75c173faef00266e4c
ms.sourcegitcommit: 2713f2c1d74e3582704c3d0ca65b6651119ed489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2019
ms.locfileid: "56321150"
---
# <a name="watchos-troubleshooting"></a>watchOS 故障排除

此页包含的其他信息和可能遇到的问题的解决方法。

- [已知问题](#knownissues)

- [从图标图像中删除 Alpha 通道](#noalpha)

- [手动添加界面控制器文件](#add)Xcode 接口生成器。

- [启动从命令行 WatchApp](#command_line)。

<a name="knownissues" />

## <a name="known-issues"></a>已知问题

### <a name="general"></a>常规

<a name="deploy" />

- Visual Studio for Mac 的早期版本不正确显示之一**AppleCompanionSettings**为 88 x 88 像素; 这会导致图标**缺少图标错误**如果尝试将提交到 App Store。
    此图标应为 87 x 87 像素 (29 单位 **@3x** Retina 屏幕)。 无法为 Mac-编辑图像资产在 Xcode 中解决此问题在 Visual Studio 中或手动编辑**Contents.json**文件 (以匹配[此示例](https://github.com/xamarin/monotouch-samples/blob/master/WatchKit/WatchKitCatalog/WatchApp/Resources/Images.xcassets/AppIcons.appiconset/Contents.json#L126-L132))。

- 如果监视扩展项目**Info.plist > WKApp 捆绑 ID**不是[正确设置](~/ios/watchos/get-started/project-references.md)以匹配 Watch 应用**捆绑 ID**，调试器将无法进行连接和 VisualStudio for Mac 将该消息以等待 *"正在等待调试程序连接"*。

- 在支持调试**通知**模式，但无法保证其准确性。 正在重试将有时会起作用。 确认 Watch 应用**Info.plist** `WKCompanionAppBundleIdentifier`设置为 iOS 父/容器应用的捆绑包标识符匹配 (ie。 在 iPhone 上运行)。

- iOS 设计器不显示为快速或通知界面控制器的入口点箭头。

- 不能添加两个`WKNotificationControllers`到情节提要。
    解决方法：`notificationCategory`情节提要 XML 中的元素始终插入具有相同`id`。 若要解决此问题，您可以添加两个 （或多个） 通知控制器、 文本编辑器中打开情节提要文件，然后手动更改`id`元素是唯一的。

    [![](troubleshooting-images/duplicate-id-sml.png "打开情节提要文件在文本编辑器中，并手动更改为唯一的 id 元素")](troubleshooting-images/duplicate-id.png#lightbox)

- 可能会看到错误"尚未生成应用程序"时尝试启动该应用程序。 之后将发生这种情况**清理**如果启动项目设置为监视扩展项目。
    解决方法是选择**生成 > 全部重新生成**，然后重新启动该应用程序。

### <a name="visual-studio"></a>Visual Studio

IOS 设计器支持的监视工具包*需要*要正确配置的解决方案。 如果未设置项目引用 (请参阅[如何设置引用](~/ios/watchos/get-started/project-references.md))，然后在设计图面将无法正常工作。

<a name="noalpha" />

## <a name="removing-the-alpha-channel-from-icon-images"></a>从图标图像中删除 Alpha 通道

图标不应包含 alpha 通道 （alpha 通道定义的图像的透明区域），并出现类似于以下的错误的 App Store 提交过程中，否则将拒绝应用：

```csharp
Invalid Icon - The watch application '...watchkitextension.appex/WatchApp.app'
contains an icon file '...watchkitextension.appex/WatchApp.app/Icon-27.5@2x.png'
with an alpha channel. Icons should not have an alpha channel.
```

很容易地删除 Mac OS X 使用 alpha 通道**预览版**应用：

1. 打开中的图标图像**预览版**，然后选择**文件 > 导出**。

2. 出现的对话框将包括**Alpha** alpha 通道是否存在的复选框。

    ![](troubleshooting-images/remove-alpha-sml.png "如果存在 alpha 通道则，出现的对话框将包含 Alpha 复选框")

3. *Untick* **Alpha**复选框并**保存**到正确的位置的文件。

4. 图标图像现在应通过 Apple 的验证检查。


<a name="add" />

## <a name="manually-adding-interface-controller-files"></a>手动添加界面控制器文件

> [!IMPORTANT]
> Xamarin 的 WatchKit 支持包括设计 iOS 设计器中 （在 Visual Studio for Mac 和 Visual Studio） 中，它不需要使用如下所述的步骤监视情节提要。 只需为提供界面控制器类名称在 Visual Studio 中 Mac 属性填充和C#将自动创建代码文件。


*如果*正在使用 Xcode Interface Builder，请执行以下步骤以创建新界面控制器监视应用并启用与 Xcode 同步，以便输出口和操作可在C#:

1. 打开 watch 应用**Interface.storyboard**中**Xcode Interface Builder**。
    
    ![](troubleshooting-images/add-6.png "在 Xcode Interface Builder 中打开情节提要")

2. 拖动新`InterfaceController`到情节提要上：

    ![](troubleshooting-images/add-1.png "A InterfaceController")

3. 你可现在将控件拖到接口控制器 （例如。 标签和按钮） 但不是能创建插座或操作，因为没有任何 **.h**标头文件。 以下步骤将使所需 **.h**创建标头文件。

    ![](troubleshooting-images/add-2.png "中布局的按钮")

4. 关闭情节提要并返回到 Visual Studio for mac。 创建一个新C#文件**MyInterfaceController.cs** （或您喜欢的任何名称） 中**观看应用扩展**项目 （不 watch 应用其中情节提要是本身）。 添加以下代码 （正在更新命名空间、 类名、 和构造函数名称）：

        using System;
        using WatchKit;
        using Foundation;
        
        namespace WatchAppExtension  // remember to update this
        {
            public partial class MyInterfaceController // remember to update this
            : WKInterfaceController
            {
                public MyInterfaceController // remember to update this
                (IntPtr handle) : base (handle)
                {
                }
                public override void Awake (NSObject context)
                {
                    base.Awake (context);
                    // Configure interface objects here.
                    Console.WriteLine ("{0} awake with context", this);
                }
                public override void WillActivate ()
                {
                    // This method is called when the watch view controller is about to be visible to the user.
                    Console.WriteLine ("{0} will activate", this);
                }
                public override void DidDeactivate ()
                {
                    // This method is called when the watch view controller is no longer visible to the user.
                    Console.WriteLine ("{0} did deactivate", this);
                }
            }
        }

5. 创建另一个新C#文件**MyInterfaceController.designer.cs**中**观看应用扩展**项目，并添加下面的代码。 务必要更新的命名空间、 类名和`Register`属性：

    ```csharp
    using Foundation;
    using System.CodeDom.Compiler;
    
    namespace HelloWatchExtension  // remember to update this
    {
        [Register ("MyInterfaceController")] // remember to update this
        partial class MyInterfaceController  // remember to update this
        {
            void ReleaseDesignerOutlets ()
            {
            }
        }
    }
    ```
    
    提示:（可选） 可以使其文件拖动到另一个子节点的第一个文件C#用于 Mac 的 Solution Pad 在 Visual Studio 中的文件。 它然后将显示如下：
    
    ![](troubleshooting-images/add-5.png "在 Solution pad")

6. 选择**生成 > 生成所有**Xcode 同步将识别新的类 (通过`Register`属性)，我们使用。

7. 右键单击监视应用情节提要文件，并选择重新打开情节提要**打开方式 > Xcode Interface Builder**:

    ![](troubleshooting-images/add-6.png "在 Interface Builder 中打开情节提要")

8. 选择新的界面控制器并为其提供更高版本，例如定义类名。 `MyInterfaceController`。
如果已正确成功，它应显示在中自动**类：** 下拉列表中，你可以从中选择它。

    ![](troubleshooting-images/add-4.png "设置自定义类")

9. 选择**助手编辑器**查看 Xcode （使用两个重叠圆圈图标） 中，以便您可以查看情节提要和代码的并排方案：

    ![](troubleshooting-images/add-7.png "助手编辑器工具栏项")

    当焦点位于代码窗格中时，确保您正在查看 **.h**标头文件，并且如果不在痕迹导航栏中右键单击并选择正确的文件 (**MyInterfaceController.h**)

    ![](troubleshooting-images/add-8.png "选择 MyInterfaceController")

10. 现在，您可以创建输出口和操作**Ctrl + 拖**从到情节提要 **.h**标头文件。

    ![](troubleshooting-images/add-9.png "创建输出口和操作")

    释放拖动时，您将会提示选择是否创建输出口或操作，并选择其名称：

    ![](troubleshooting-images/add-a.png "输出口和操作对话框")

11. 一旦保存情节提要更改后，Xcode 已关闭，返回到 Visual Studio for mac。 它将检测标头文件更改，并自动将代码添加到 **。 designer.cs**文件：


        [Register ("MyInterfaceController")]
        partial class MyInterfaceController
        {
            [Outlet]
            WatchKit.WKInterfaceButton myButton { get; set; }
        
            void ReleaseDesignerOutlets ()
            {
                if (myButton != null) {
                    myButton.Dispose ();
                    myButton = null;
                }
            }
        }


现在可以引用该控件 （或实现的操作） 在C#！


<a name="command_line" />

## <a name="launching-the-watch-app-from-the-command-line"></a>启动监视应用程序从命令行

> [!IMPORTANT]
> 可以在常规应用模式下启动监视应用程序，默认情况下，以及在**速览**或**通知**使用模式[自定义执行参数](~/ios/watchos/get-started/installation.md#custommodes)在 Visual Studio for Mac 和Visual Studio。


此外可以使用命令行来控制 iOS 模拟器。 用来启动监视应用程序的命令行工具是**mtouch**。

下面是一个完整的示例 （作为单个行在终端中执行）：

```bash
/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/bin/mtouch --sdkroot=/Applications/Xcode.app/Contents/Developer/ --device=:v2:runtime=com.apple.CoreSimulator.SimRuntime.iOS-8-2,devicetype=com.apple.CoreSimulator.SimDeviceType.iPhone-6
--launchsimwatch=/path/to/watchkitproject/watchsample/bin/iPhoneSimulator/Debug/watchsample.app
```

你需要更新以反映您的应用程序的参数是`launchsimwatch`:

### <a name="--launchsimwatch"></a>--launchsimwatch

主应用程序捆绑包的完整路径*包含监视应用和扩展的 iOS 应用*。

> [!NOTE]
> 你需要提供此路径适用于*iPhone 应用程序.app 文件*，即一个，将部署到 iOS 模拟器和包含的监视扩展和 watch 应用。

示例:

```bash
--launchsimwatch=/path/to/watchkitproject/watchsample/bin/iPhoneSimulator/Debug/watchsample.app
```


## <a name="notification-mode"></a>通知模式

若要测试的应用[**通知**模式](~/ios/watchos/platform/notifications.md)，将`watchlaunchmode`参数`Notification`并提供包含测试通知有效负载的 JSON 文件的路径。

有效负载参数是*需*通知模式。

例如，将这些参数添加到 mtouch 命令：

```bash
--watchlaunchmode=Notification --watchnotificationpayload=/path/to/file.json
```


## <a name="other-arguments"></a>其他参数

下面介绍了其余的参数：

### <a name="--sdkroot"></a>--sdkroot

必需。 指定到 Xcode （6.2 或更高版本） 的路径。

示例:

```bash
 --sdkroot /Applications/Xcode.app/Contents/Developer/
```

### <a name="--device"></a>--device

要执行的模拟器设备。 这可以在两种方法，使用特定设备的 udid 或使用运行时和设备类型的组合中指定。

确切值会有所变化，在计算机之间以及可以使用 Apple 的查询**simctl**工具：

```bash
/Applications/Xcode.app/Contents/Developer/usr/bin/simctl list
```

**UDID**

示例:

```bash
--device=:v2:udid=AAAAAAA-BBBB-CCCC-DDDD-EEEEEEEEEEEE
```

**运行时和设备类型**

示例:

```bash
--device=:v2:runtime=com.apple.CoreSimulator.SimRuntime.iOS-8-2,devicetype=com.apple.CoreSimulator.SimDeviceType.iPhone-6
```



## <a name="related-links"></a>相关链接

- [WatchKitCatalog （示例）](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [WatchTables （示例）](https://developer.xamarin.com/samples/monotouch/watchOS/WatchTables/)
