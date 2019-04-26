---
title: Siri 远程和蓝牙控制器对 tvOS 在 Xamarin 中
description: 本文介绍如何使用 Siri 远程和蓝牙游戏控制器中使用 Xamarin 编写的 tvOS 应用。
ms.prod: xamarin
ms.assetid: BDB9894A-236B-424B-9032-ACD12A6C5720
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 79022f7a454ea423fa3112a4c4ade2bcd471fbb8
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60932956"
---
# <a name="siri-remote-and-bluetooth-controllers-for-tvos-in-xamarin"></a>Siri 远程和蓝牙控制器对 tvOS 在 Xamarin 中

Xamarin.tvOS 应用程序的用户将不与它的接口直接作为 ios 其中用户点击设备的屏幕上的映像但间接从跨空间使用交互[Siri 远程](#The-Siri-Remote)。

如果您的应用程序是一个游戏，您可以根据需要生成的第三方，所做的支持 iOS (MFI)[蓝牙游戏控制器](#Bluetooth-Game-Controllers)在您的应用程序中。

[![](remote-bluetooth-images/intro01.png "蓝牙远程和游戏控制器")](remote-bluetooth-images/intro01.png#lightbox)

本文介绍[Siri 远程](#The-Siri-Remote)， [Touch 面手势](#Touch-Surface-Gestures)并[Siri 远程按钮](#Siri-Remote-Buttons)并演示如何使用它们通过[手势和情节提要](#Gestures-and-Storyboards)，[手势和代码](#Gestures-and-Code)并[低级别事件处理](#Low-Level-Event-Handling)。 最后，它讨论[使用游戏控制器](#Working-with-Game-Controllers)Xamarin.tvOS 应用中。

<a name="The-Siri-Remote" />

## <a name="the-siri-remote"></a>Siri 远程

用户将与 Apple TV 和 Xamarin.tvOS 应用进行交互的主要方式是通过包含 Siri 远程。 Apple 设计远程来桥接坐在沙发上和在同一房间电视屏幕上显示的 Apple tv 设备用户界面上的用户之间的距离。

您为 tvOS 应用程序开发人员的难题是创建利用 Siri 远程 touch 面、 加速计、 陀螺仪和按钮的快速、 简单易用而极具视觉表现力的用户界面。

[![](remote-bluetooth-images/remote01.png "Siri 远程")](remote-bluetooth-images/remote01.png#lightbox)

Siri 远程具有以下功能和 tvOS 应用程序中的预期的用法：

|功能|常规应用程序使用情况|游戏应用使用情况|
|---|---|---|
|**Touch 面**<br />轻扫，若要导航，请按键以选择并按住上下文菜单。|**Tap/Swipe**<br />UI 可获得焦点的项之间导航。<br /><br />**Click**<br />激活所选 （焦点） 项。|**Tap/Swipe**<br />取决于游戏设计和可用作 D-pad 通过边缘上点击。<br /><br />**Click**<br />执行的主要按钮功能。|
|**菜单**<br />按以返回到上一屏幕或菜单。|返回到上一屏幕并从主应用程序屏幕退出到 Apple 电视主页屏幕。|暂停和继续游戏玩法，返回到上一屏幕并退出到 Apple 电视主页屏幕从主应用程序屏幕。|
|**使用 Siri/搜索**<br />使用 Siri 国家/地区，请在按住语音控件，在所有其他国家/地区，显示搜索屏幕。|n/a|n/a|
|**播放/暂停**<br />播放和暂停媒体或提供应用中的辅助函数。|启动媒体的播放和暂停/恢复播放。|执行次要按钮的功能或跳过简介视频 (如果存在)。|
|**主文件夹**<br />按以返回到主页屏幕中，双击以显示正在运行的应用、 按下并保持设备进入睡眠状态。|n/a|n/a|
|**卷**<br />控件附加音频/视频设备卷。|n/a|n/a|

<a name="Touch-Surface-Gestures" />

## <a name="touch-surface-gestures"></a>触摸图面上的手势

Siri 远程 Touch 面是能够检测到各种单指手势，您可以响应 Xamarin.tvOS 应用程序中：

|轻扫|单击|点击|
|---|---|---|
|![](remote-bluetooth-images/Gesture01.png)|![](remote-bluetooth-images/Gesture02.png)|![](remote-bluetooth-images/Gesture03.png)|
|在屏幕上的 UI 元素之间移动选择 （焦点） （向上、 下左、 右）。 轻扫可以用于在大型列表中快速使用延时的内容滚动。|激活所选 （焦点） 项或的作用类似于在游戏中的主按钮。 单击并按住可激活上下文菜单或辅助函数。|轻轻点击上边缘的 Touch 面作用类似于在 D-板、 向上、 下、 左或右根据点击的区域将焦点移动方向按钮。 具体取决于应用程序中，可用来显示隐藏的控件。|

Apple 提供了以下建议以获得使用 Touch 面手势：

* **区分单击和分流点**-单击是由用户有意操作，非常适用于所选内容、 激活和游戏的主按钮。 点击复杂得多，因为用户通常在其手中持有 Siri 远程并可以轻松地意外激活点击事件应谨慎使用。
* **不重新定义标准手势**-用户具有特定手势将执行特定操作，假定条件下不应重新的含义或功能的这些手势定义应用程序中。 一个例外 active 玩游戏期间是游戏应用。
* **定义新手势谨慎**-用户再次重申，没有特定手势将执行特定操作的假定条件。 应避免定义自定义笔势以执行标准操作。 同样，游戏的最常见的异常在自定义笔势可以为游戏添加有趣且令人着迷的播放。
* **如果适用，响应 D-pad 点击**-轻轻点击的 Touch 面边缘将 react 像方向键上将焦点移游戏控制器或方向，向下左或向右角。 如果需要，您应响应应用或游戏中这些手势。

<a name="Siri-Remote-Buttons" />

## <a name="siri-remote-buttons"></a>Siri 远程按钮

除了 Touch 面上的手势，您的应用程序可以响应用户单击 Touch 面或按播放/暂停按钮。 如果您正在访问 Siri 远程使用游戏控制器框架，您还可以检测已按下菜单按钮。 

此外，按下菜单按钮可以检测到笔势识别器使用标准`UIKit`元素。 如果截获按下菜单按钮，将关闭当前的视图和视图控制器负责，并返回到上一个。

> [!IMPORTANT]
> 您应该**始终**将函数赋给在远程数据库上的播放/暂停按钮。 拥有非功能性按钮可使您查看最终用户到中断的应用程序。 如果您没有有效的函数的此按钮，将分配与主要按钮 （Touch 面单击） 相同的功能。

<a name="Gestures-and-Storyboards" />

## <a name="gestures-and-storyboards"></a>手势和情节提要

使用 Siri 远程 Xamarin.tvOS 应用程序中的最简单方法是将手势识别器添加到你的视图接口设计器中。

若要添加的笔势识别器，请执行以下操作：

1. 在中**解决方案资源管理器**，双击`Main.storyboard`文件，然后打开用于编辑界面设计器。
2. 拖动**点击手势识别器**从**库**并将其放在视图上： 

    [![](remote-bluetooth-images/storyboard01.png "点击手势识别器")](remote-bluetooth-images/storyboard01.png#lightbox)
3. 检查**选择**中**按钮**一部分**属性检查器**: 

    [![](remote-bluetooth-images/storyboard02.png "检查选择")](remote-bluetooth-images/storyboard02.png#lightbox)
4. **选择**意味着用户单击将响应手势**Touch 面**Siri 远程上。 此外可以选择响应**菜单**，**播放/暂停**，**向上**，**关闭**，**左侧**和**右**按钮。
5. 接下来，接通**操作**从**点击手势识别器**，并调用它`TouchSurfaceClicked`: 

    [![](remote-bluetooth-images/storyboard03.png "点击笔势识别器从一个操作")](remote-bluetooth-images/storyboard03.png#lightbox)
6. 保存所做的更改并返回到 Visual Studio for mac。

编辑视图控制器 (示例`FirstViewController.cs`) 文件，并添加以下代码以处理手势触发：

```csharp
using System;
using UIKit;

namespace tvRemote
{
    public partial class FirstViewController : UIViewController
    {
        ...

        #region Custom Actions
        partial void TouchSurfaceClicked (Foundation.NSObject sender) {
            // Handle click here
            ...
        }
        #endregion
    }
}
```

使用情节提要的详细信息，请参阅我们[你好，tvOS 快速入门指南](~/ios/tvos/get-started/hello-tvos.md)。 具体而言[创建用户界面](~/ios/tvos/get-started/hello-tvos.md#Creating-the-User-Interface)并[编写的代码具有输出口和操作](~/ios/tvos/get-started/hello-tvos.md#Writing-the-Code)部分。

<a name="Gestures-and-Code" />

## <a name="gestures-and-code"></a>手势和代码

（可选） 创建手势直接在C#代码并将其添加到您的用户界面中的视图。 例如，若要添加一系列轻扫手势识别器，编辑视图控制器，并添加以下代码：

```csharp
using System;
using UIKit;

namespace tvRemote
{
    public partial class SecondViewController : UIViewController
    {
        #region Constructors
        public SecondViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();    

            // Wire-up gestures
            var upGesture = new UISwipeGestureRecognizer (() => {
                RemoteView.ArrowPressed = "Up";
                ButtonLabel.Text = "Swiped Up";
            }) {
                Direction = UISwipeGestureRecognizerDirection.Up
            };
            this.View.AddGestureRecognizer (upGesture);

            var downGesture = new UISwipeGestureRecognizer (() => {
                RemoteView.ArrowPressed = "Down";
                ButtonLabel.Text = "Swiped Down";
            }) {
                Direction = UISwipeGestureRecognizerDirection.Down
            };
            this.View.AddGestureRecognizer (downGesture);

            var leftGesture = new UISwipeGestureRecognizer (() => {
                RemoteView.ArrowPressed = "Left";
                ButtonLabel.Text = "Swiped Left";
            }) {
                Direction = UISwipeGestureRecognizerDirection.Left
            };
            this.View.AddGestureRecognizer (leftGesture);

            var rightGesture = new UISwipeGestureRecognizer (() => {
                RemoteView.ArrowPressed = "Right";
                ButtonLabel.Text = "Swiped Right";
            }) {
                Direction = UISwipeGestureRecognizerDirection.Right
            };
            this.View.AddGestureRecognizer (rightGesture);
        }
        #endregion
    }
}
```

<a name="Low-Level-Event-Handling" />

## <a name="low-level-event-handling"></a>低级别事件处理

如果要创建自定义类型基于`UIKit`Xamarin.tvOS 应用程序中 (例如`UIView`)，还必须能够通过按下按钮进行低级别处理`UIPress`事件。 

一个`UIPress`对 tvOS 的事件是什么`UITouch`事件为 ios、 except`UIPress`返回 Siri 遥控器上按下按钮的信息或其他附加的蓝牙设备 （如游戏控制器）。 `UIPress` 事件描述按下按钮，其状态 （开始、 已取消、 Changed 或已结束）。 

对于在蓝牙游戏与控制器一样，设备上模拟按钮`UIPress`也会返回强制应用于按钮的量。 `Type`属性的`UIPress`事件定义的物理按钮具有已更改的状态，而其余的属性描述已发生的更改。

下面的代码显示了示例处理低级别`UIPress`事件`UIView`:

```csharp
using System;
using Foundation;
using UIKit;

namespace tvRemote
{
    public partial class EventView : UIView
    {
        #region Computed Properties
        public override bool CanBecomeFocused {
            get {
                return true;
            }
        }
        #endregion

        #region 
        public EventView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void PressesBegan (NSSet<UIPress> presses, UIPressesEvent evt)
        {
            base.PressesBegan (presses, evt);

            foreach (UIPress press in presses) {
                // Was the Touch Surface clicked?
                if (press.Type == UIPressType.Select) {
                    BackgroundColor = UIColor.Red;
                }
            }
        }

        public override void PressesCancelled (NSSet<UIPress> presses, UIPressesEvent evt)
        {
            base.PressesCancelled (presses, evt);

            foreach (UIPress press in presses) {
                // Was the Touch Surface clicked?
                if (press.Type == UIPressType.Select) {
                    BackgroundColor = UIColor.Clear;
                }
            }
        }

        public override void PressesChanged (NSSet<UIPress> presses, UIPressesEvent evt)
        {
            base.PressesChanged (presses, evt);
        }

        public override void PressesEnded (NSSet<UIPress> presses, UIPressesEvent evt)
        {
            base.PressesEnded (presses, evt);

            foreach (UIPress press in presses) {
                // Was the Touch Surface clicked?
                if (press.Type == UIPressType.Select) {
                    BackgroundColor = UIColor.Clear;
                }
            }
        }
        #endregion
    }
}
```

如同`UITouch`事件，如果您需要实现的任何`UIPress`事件替代，则应实现所有四个。

<a name="Bluetooth-Game-Controllers" />

## <a name="bluetooth-game-controllers"></a>蓝牙游戏控制器

除了标准 Siri 远程使用 Apple TV、 第三方、 所做的 iOS 随附可以与 Apple TV 配对并用于控制 Xamarin.tvOS 应用 (MFI) 蓝牙游戏控制器。

[![](remote-bluetooth-images/game01.png "蓝牙游戏控制器")](remote-bluetooth-images/game01.png#lightbox)

游戏控制器可以用于增强游戏玩法，并提供在游戏中的浸入式的。 它们还可以用于控制标准的 Apple TV 接口，以便使用不具有远程和控制器之间进行切换。

> [!IMPORTANT]
> 蓝牙游戏控制器的可选购买内容，可能会使最终用户，您的应用程序不能强制用户购买一个。 如果您的应用程序支持游戏控制器，它还必须支持 Siri 远程，以便游戏的 Apple TV 的所有用户。

游戏控制器具有以下功能和 tvOS 应用程序中的预期的用法：

|功能|常规应用程序使用情况|游戏应用使用情况|
|---|---|---|
|**D-Pad**|导航至用户界面元素 （更改焦点）。|取决于游戏。|
|**A**|激活所选 （焦点） 项。|执行主按钮的功能，并确认对话框中的操作。|
|**B**|返回到上一屏幕或退出到主屏幕中，如果应用程序的主屏幕上。|执行次要按钮的功能或返回到上一屏幕。|
|**X**|启动媒体的播放或暂停/恢复播放。|取决于游戏。|
|**Y**|n/a|取决于游戏。|
|**菜单**|返回到上一屏幕或退出到主屏幕中，如果应用程序的主屏幕上。|暂停/恢复游戏玩法，返回到上一屏幕或主页屏幕的退出如果应用程序的主屏幕上。|
|**左即时权限提升按钮**|导航到左侧。|取决于游戏。|
|**左侧的触发器**|导航到左侧。|取决于游戏。|
|**右即时权限提升按钮**|导航到右。|取决于游戏。|
|**正确的触发器**|右导航|取决于游戏。|
|**左的摇杆**|导航至用户界面元素 （更改焦点）。|取决于游戏。|
|**右摇杆**|n/a|取决于游戏。|

Apple 提供了以下建议以获得使用游戏控制器：

- **确认游戏控制器连接**-你的 tvOS 应用可以启动和停止在任何时候通过最终用户。 应始终检查游戏控制器在启动应用程序或唤醒状态的时间存在并根据需要采取措施。
- **确保你的应用适用于使用 Siri 远程和游戏控制器**-不需要用户 Siri 远程和游戏控制器以使用你的应用之间进行切换。 使用这两种类型的控制器确保所有内容易于导航，并按预期方式工作，通常测试您的应用程序。
- **方法返回提供**-按下菜单按钮应始终返回到上一屏幕。 如果用户是在主应用程序屏幕上，菜单按钮应返回其到 Apple 电视主页屏幕。 在游戏玩法，期间的菜单按钮，应显示警报使用户能够暂停/恢复在玩游戏或返回到主菜单。

<a name="Working-with-Game-Controllers" />

## <a name="working-with-game-controllers"></a>使用游戏控制器

如上面所述，除了可以选择性地附加 Apple TV 中，用户使用 Siri 远程附带的标准的第三方，所做的 iOS (MFI) 蓝牙游戏控制器和使用它来控制 Xamarin.tvOS 应用。

如果您的应用程序所需的低级控制器输入，可以使用 Apple[游戏控制器框架](https://developer.apple.com/library/prerelease/tvos/documentation/ServicesDiscovery/Conceptual/GameControllerPG/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013276)具有适用于 tvOS 的以下修改：

- 微型游戏控制器配置文件 (`GCMicroGamepad`) 已添加为面向 Siri 远程。
- 新`GCEventViewController`类可用于将游戏控制器通过您的应用程序的事件路由。 请参阅[确定游戏控制器输入](#determining-game-controller-input)部分获取更多详细信息。

<a name="Game-Controller-Support-Requirements" />

### <a name="game-controller-support-requirements"></a>游戏控制器支持要求

Apple 有几个特定的如果 Xamarin.tvOS 应用支持游戏控制器必须满足的要求：

- **必须支持 Siri 远程**-必须始终支持 Siri 远程。 您的游戏不能要求第三方游戏控制器可以播放。
- **必须支持扩展的控件布局**-所有 tvOS 游戏控制器都是 formfitting，扩展控制器。
- **游戏必须是具有独立控制器 Playable** -如果您的应用程序支持扩展游戏控制器，它必须仅与该游戏控制器可播放。
- **必须支持播放/暂停按钮**-期间游戏玩法，如果用户按播放/暂停按钮，则应显示为用户提供在玩游戏暂停/恢复的能力警报或返回到主菜单。

<a name="Enabling-Game-Controller-Support" />

### <a name="enabling-game-controller-support"></a>启用游戏控制器支持

若要启用游戏控制器支持 Xamarin.tvOS 应用程序中的，双击`Info.plist`文件中**解决方案资源管理器**打开进行编辑：

[![](remote-bluetooth-images/game02.png "Info.plist 编辑器")](remote-bluetooth-images/game02.png#lightbox)

下**游戏控制器**部分中，通过选中**启用游戏控制器**，然后检查所有应用将支持的游戏控制器类型。

<a name="Using-the-Siri-Remote-as-a-Game-Controller" />

### <a name="using-the-siri-remote-as-a-game-controller"></a>使用 Siri 远程作为游戏控制器

Siri 远程 Apple TV 附带可以用作有限的游戏控制器。 其他游戏与控制器一样，它会显示在游戏控制器框架作为`GCController`对象，并支持这两个`GCMotion`和`GCMicroGamepad`配置文件。

Siri 远程用作游戏控制器时具有以下特征：

- Touch 面可以用作 d-pad 提供模拟输入的数据。
- 远程可在纵向或横向方向和您的应用程序决定该配置文件对象是否应自动翻转输入的数据。
- 单击 Touch 面的作用类似于按下按钮**A**游戏控制器上。
- 播放/暂停按钮的作用类似于按钮**X**游戏控制器上。
- 菜单按钮应显示警报使用户能够暂停/恢复在玩游戏或返回到主菜单。

<a name="Summary" />

### <a name="determining-game-controller-input"></a>确定游戏控制器输入

与不同的是可以接收触摸事件与并行的游戏控制器事件的位置的 iOS、 tvOS 处理所有低级别的事件提供高级`UIKit`事件。 因此，如果需要较低级别的游戏控制器事件的访问权限，你将需要关闭`UIKit`的默认行为。

TvOS，当你想要直接需要使用处理游戏控制器输入上`GCEventViewController`（或子类），显示游戏的用户界面。 每当`GCEventViewController`是*第一个响应方*，将捕获游戏控制器输入，并将其传送到游戏控制器框架通过对应用程序。

可以使用`UserInteractionEnabled`属性的`GCEventViewController`切换如何处理和处理事件的类。

有关实现游戏控制器支持的信息，请参阅 Apple[使用游戏控制器](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/WorkingwithGameControllers.html)主题中[适用于 tvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/index.html)和[游戏控制器编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/ServicesDiscovery/Conceptual/GameControllerPG/Introduction/Introduction.html)。

<a name="Summary" />

## <a name="summary"></a>总结

本文只讨论了新的 Siri 远程 Apple TV、 Touch 面手势和 Siri 远程按钮。 接下来，其中包括如何使用笔势和情节提要、 手势和代码和低级别事件。 最后，如果所述使用游戏控制器。



## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [适用于 tvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
