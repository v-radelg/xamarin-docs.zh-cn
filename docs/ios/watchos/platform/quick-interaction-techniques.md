---
title: 用于 watchOS 3 在 Xamarin 中的快速交互技术
description: 本文介绍如何快速交互技术 watchOS 3 和如何实现它们在 Xamarin.iOS 中转换为 Apple Watch 中添加了 Apple。
ms.prod: xamarin
ms.assetid: 26697F68-AF7E-4A36-988F-85E2674A4DD1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 5086724b565fb95274c4988ca1b6e4bb11064575
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61082235"
---
# <a name="quick-interaction-techniques-for-watchos-3-in-xamarin"></a>用于 watchOS 3 在 Xamarin 中的快速交互技术

_本文介绍如何快速交互技术 watchOS 3 和如何实现它们在 Xamarin.iOS 中转换为 Apple Watch 中添加了 Apple。_

提供快速的用户交互对创建引人注目的 Apple Watch 应用程序和复杂性问题至关重要。 新 watchOS 3，到 Apple 添加了对手势识别器，访问数字 Crown 和新的用户通知和导航技术支持。 此操作，同时还添加了支持 SceneKit 和 SpriteKit，允许开发人员能够轻松创建丰富、 glanceable 界面快速且响应迅速。

## <a name="what-are-quick-interactions"></a>快速交互有哪些？

用于创建适用于 iOS 或 macOS （其中花与应用交互的时间量以分钟或小时单位） 的应用程序开发人员，程序设计的 Apple Watch 成功的应用程序可能是一个挑战，需要不同方法。

在 watchOS 中，用户通常想要引发其手腕、 快速与应用交互 （通常为简要几秒钟），然后删除其手腕和继续其是他们所做的任何内容。

Apple Watch 上的典型快速交互的几个示例如下：

- 正在启动一个计时器。
- 正在检查天气。
- 将标记待办事项列表项。

若要实现这些目标，必须是 Apple Watch 上的应用程序：

- **Glanceable** -这意味着，快速浏览用户应该能够获得所需的信息。 
- **可操作**-这意味着用户应该能够做出快速、 明智的决策。
- **响应式**-这意味着用户绝不应该等待以接收所需的信息，或以实现所需的操作。

### <a name="quick-interactions-length"></a>快速交互长度

由于 Apple Watch 应用的 glanceable 性质，Apple 建议快速交互的理想长度应为两秒或更少。 由于此两个第二个限制时，开发人员将需要花费相当长的时间同时设计和实现的 Apple Watch 应用。 

## <a name="new-watchos-3-features-and-apis"></a>新 watchOS 3 个功能和 Api

Apple 已添加到 WatchKit 来帮助你开发人员添加到 Apple Watch 应用程序的快速交互的几个新功能和 Api:

- watchOS 3 提供对新类型的用户输入如访问：
    - 笔势识别器
    - 数字 Crown 旋转 
- watchOS 3 提供了新的显示方式和更新的信息，如：
    - 表的增强的导航
    - 新的用户通知框架支持
    - SpriteKit 和 SceneKit 集成

通过实现这些新功能，开发人员可以确保其 watchOS 3 应用程序是 Glanceable、 可操作和响应式。

### <a name="gesture-recognizer-support"></a>手势识别器支持

如果开发人员已在 iOS 中实现手势识别器，它们应非常熟悉手势识别器如何在 watchOS 3 中。 若要刷新，手势识别器是分析为可识别的、 预定义的手势的低级别触控事件的对象。

watchOS 3 将支持四个以下的手势识别器：

- 离散手势类型：
    - 轻扫手势 (`WKSwipeGestureRecognizer`)。
    - 点击手势 (`WKTapGestureRecognizer`)。
- 连续手势类型：
    - 平移手势 (`WKPanGestureRecognizer`)。
    - 长时间按手势 (`WKLongPressGestureRecognizer`)。

若要实现一个新的手势识别器，只需 Mac 将其拖至设计图面上，在 iOS 设计器在 Visual Studio 中，并配置其属性。

在代码中，响应以处理未由用户触发的笔势识别器的操作。 同样，这是在相同的方式为在 iOS 中将已处理。

#### <a name="discrete-gesture-states"></a>离散手势状态

对于离散笔势，操作称为时识别出的笔势和状态 (`WKGestureRecognizerState`) 被指定为：

[![](quick-interaction-techniques-images/quick01.png "离散手势状态")](quick-interaction-techniques-images/quick01.png#lightbox)

所有离散手势的初始`Possible`状态并转换为任一`Failed`或`Recognized`状态。 在使用离散手势，开发人员通常不会处理直接状态。 相反，它们依赖于仅识别出的笔势后调用的操作。

#### <a name="continuous-gesture-states"></a>连续手势状态

连续手势是离散的手势，其中操作多次调用如识别出的笔势略有不同：

[![](quick-interaction-techniques-images/quick02.png "连续手势状态")](quick-interaction-techniques-images/quick02.png#lightbox)

同样，连续手势启动时处于`Possible`状态，但它们随多个更新进度。 在这里，开发人员将需要考虑识别器的状态和更新过程的应用程序的 UI`Changed`阶段之前存在笔势，最后`Recognized`或`Canceled`。

#### <a name="gesture-recognizer-usage-tips"></a>笔势识别器使用提示

WatchOS 3 中使用笔势识别器时，Apple 提供以下建议：

- 将手势识别器添加到组元素，而不是单独的控件。 由于 Apple Watch 具有较小的物理屏幕大小，往往是更大的元素进行分组和更容易为用户按目标。 此外，手势识别器可能会发生冲突与内置的手势已在本机 UI 控件。
- 监视应用的情节提要中设置的依赖关系。
- 一些手势的优先级高于其他手势类型，如：
    - 滚动
    - 强制触摸
 
### <a name="digital-crown-rotation"></a>数字 Crown 旋转

通过在其 watchOS 3 应用程序中实现数字 Crown 支持，开发人员可以提供更高的导航速度和精度之间的交互为其用户。

WatchOS 2，因为 Apple Watch 应用可以使用`WKInterfacePicker`对象来访问通过提供一系列数字 Crown`WKPickerItems`和选取器的样式 （列表、 堆积或映像序列）。 watchOS 则允许用户使用数字 Crown 可以从列表中选择的项。

当使用`WKInterfacePicker`，WatchKit 处理大部分的工作：

- 绘制列表和单个界面元素。
- 在处理数字 Crown 事件。
- 调用操作时，选定的项。

新增到 watchOS 3，开发人员现在可以直接访问数字 Crown 旋转事件使管理员能够创建他们自己对旋转值做出响应的 UI 元素。

数字 Crown 访问提供的以下元素：

- `WKCrownSequencer` -提供每秒旋转的访问。
- `WKCrownDelegate` -提供对旋转增量事件的访问。

#### <a name="rotations-per-second"></a>每秒旋转

从数字 Crown 访问旋转每秒时，物理引擎使用基于动画。 若要访问的旋转每秒，请使用`CrownSequencer`属性的`WKInterfaceController`的监视扩展。 例如：

```csharp
var rotationsPerSecond = CrownSequencer.RotationsPerSecond;
```

#### <a name="rotational-deltas"></a>旋转增量

使用从数字 Crown 旋转增量旋转数目进行计数。 使用`CrownDidRotate`重写的方法`WKCrownDelegate`访问旋转增量。 例如：

```csharp
using System;
using WatchKit;
using Foundation;

namespace MonkeyWatch.MonkeySeeExtension
{
    public class CrownDelegate : WKCrownDelegate
    {
        #region Computed Properties
        public double AccumulatedRotations { get; set;}
        #endregion

        #region Constructors
        public CrownDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void CrownDidRotate (WKCrownSequencer crownSequencer, double rotationalDelta)
        {
            base.CrownDidRotate (crownSequencer, rotationalDelta);

            // Accumulate rotations
            AccumulatedRotations += rotationalDelta;
        }
        #endregion
    }
}
```

此处，该应用程序维护累加器 (`AccumulatedRotations`) 来确定旋转的数目。 一个完全轮换数字 Crown 等于的累积的增量`1.0`一半旋转会`0.5`。

Apple 已离开它由开发人员确定如何旋转计数对应于正在更新的 UI 元素上的更改的敏感度。

符号 (`+/-`) 指示用户是否转数字 Crown 方向的旋转增量的：

[![](quick-interaction-techniques-images/quick03.png "指示用户打开数字 Crown 方向的旋转增量的符号")](quick-interaction-techniques-images/quick03.png#lightbox)


如果用户向上滚动，WatchKit 将返回正增量而如果向下滚动，则否定增量将返回，无论何种方向用户佩戴中的监视。

#### <a name="digital-crown-focus"></a>数字 Crown 焦点

就像任何其他界面元素，数字 Crown 具有焦点的概念。 这种焦点可以离开数字 Crown 转换到基于如何在用户交互与监视其他界面元素。 

例如，以下控件的任何可能会窃取数字 Crown 的重点：

- 选取器
- Slider
- 滚动控制器

它是由开发人员确定其自定义界面元素必须是数字 Crown 焦点时。 Apple 建议使用新的手势识别器获得自定义 UI 元素中的焦点。

### <a name="vertical-paging"></a>垂直分页

用户导航 watchOS 应用中的表视图的标准方法是滚动到所需的数据块，点击要显示详细的视图，请点击后退按钮完成查看详细信息的特定行，然后重复该过程的任何其他信息，y 感兴趣从表中：

[![](quick-interaction-techniques-images/quick04.png "表和详细信息视图之间移动")](quick-interaction-techniques-images/quick04.png#lightbox)

新增到 watchOS 3，开发人员可以垂直分页上启用其表视图控件。 启用此功能，用户可以滚动以查找表视图行，然后点击要查看其详细信息为之前的行。 但是，它们可以现在往下轻扫向上选择的下一步的行的表中或以选择上一行 （或使用数字 Crown），都不需要返回到表视图首先：

[![](quick-interaction-techniques-images/quick05.png "表和详细信息视图之间移动和轻扫向上和向下移动其他行之间")](quick-interaction-techniques-images/quick05.png#lightbox)

若要启用此模式下，在 Xcode 中打开 watchOS 应用的情节提要以进行编辑、 选择表视图，并检查**垂直详细信息分页**复选框：

[![](quick-interaction-techniques-images/quick06.png "检查垂直详细信息分页复选框")](quick-interaction-techniques-images/quick06.png#lightbox)

请确保表使用 segues 设计以显示详细的视图并将所做的更改保存到情节提要并返回到 Visual Studio for Mac 进行同步。

开发人员可以以编程方式吸引垂直分页到使用以下代码对表视图的特定行：

```csharp
// Segue into Vertical Paging and select the first row
MenuTable.PerformSegue (0);
```

在使用垂直分页时，开发人员需要注意 WatchKit 将自动处理的控制器预加载，并且实际可见 UI 之前结果，可以调用某些控制器生命周期方法。

### <a name="notification-enhancements"></a>通知增强功能

通知是用户通常会遇到 watchOS 的快速交互的主要形式和第一个 Apple Watch 和 watchOS 1 以来，已可用。

典型的通知快速交互如下所示：

1. 收到新的通知后，用户感觉通知 Haptic。
2. 它们会引发其手腕若要通知查看短查找接口。
3. 如果它们继续保留引发其手腕，watchOS 自动转换为长查找通知接口。

有几种方法，用户可能会对通知做出响应：

- 答是这样定义并显示通知，为用户将不执行任何操作，只需关闭通知。
- 它们还可能会点击通知以启动 watchOS 应用。
- 对于支持自定义操作的通知，则用户可能会选择一个自定义操作。 这些可以是：
    - **前台操作**-这些启动该应用程序执行的操作。
    - **后台操作**-始终路由到 iPhone watchOS 2 中，但可以将路由到 watchApp watchOS 3 中。

WatchOS 3： 的新功能

* 通知在所有平台 （iOS、 watchOS、 tvOS 和 macOS） 上使用类似 API。
* 可以在 Apple Watch 上安排本地通知。
* 如果在 Apple Watch 上将它们安排，背景通知将路由到应用程序的扩展。

#### <a name="notification-scheduling-and-delivery"></a>通知计划和传递

从用户的 iPhone 通知将转发到 Apple Watch 时将发生以下情况：

* 这部 iphone 手机的屏幕处于关闭状态。
* Apple Watch 佩戴是和已解锁。

在 watchOS 3，本地通知可以在 Apple Watch 上计划，并仅根据监视提供。 它是开发人员能够安排相应 iPhone 通知，如果它必需的应用程序。

通过包括在 Apple Watch 和 iPhone 版本的通知上相同的通知标识符，它会阻止上观看显示重复的通知。 通知的 Apple Watch 版本将优先于的是 iPhone 版。

由于 watchOS 3 使用相同`UINotification`API 框架为 iOS 10，请参阅我们的 iOS 10[用户通知框架](~/ios/platform/user-notifications/index.md)文档了解详细信息。

### <a name="using-spritekit-and-scenekit"></a>使用 SpriteKit 和 SceneKit

新增到 watchOS 3，开发人员现在可以使用 SpritKit 和 SceneKit 对象在其应用程序的用户界面设计中呈现二维和三维图形。

若要支持此功能，已添加两个新的接口类：

- `WKInterfaceSKScene` -对于使用 SpriteKit 2D 图形。
- `WKInterfaceSCNScene` -对于使用 SceneKit 3D 图形。

若要使用这些对象，只需将它们拖到设计图面上，在 Xcode 的 Interface Builder 中监视应用的情节提要，并使用**属性检查器**进行配置。

从这里开始，使用 SpriteKit 或 SceneKit 场景的工作方式相同内部 iOS 应用程序一样。 Watch 应用会显示`WKInterfaceSKScene`通过调用之一`Present`方法。 SceneKit，只需设置`Scene`属性的`WKInterfaceSCNScene`对象。

## <a name="actionable-complications"></a>可操作的并发数据

在 watchOS 2，Apple 引入复杂了第三方应用。 在 watchOS 3，Apple 已扩展开发人员可以在 WatchKit 复杂性中包含的功能。 

此外，多个内置监视中的人脸现在可以包含复杂情况和现有观看的人脸，已受支持的并发数据可以现在包含更多的并发数据。

此外的新增功能是用户快速扫左或向右，通过它们已安装在其 Apple Watch 表盘的所有转换。 在 Apple Watch 辅助 iPhone 应用程序使用新库，用户可以添加和自定义新表盘和任何这些语句可以包含的复杂因素。

由于这些新功能，Apple 建议在 Apple Watch 上的每个应用程序还应包括至少一个复杂性，然后在这种情况下，所有本机 Apple Watch 应用现在具有复杂情况。

复杂情况提供到应用程序的以下功能：

- 它们是高度 glanceable，因为它们始终存在于手表表盘上。
- WatchOS 会经常更新的并发数据。 任何应用，其中包括用户的当前显示的 watch 表盘上一个问题是至少两次每小时更新。
- 用户的当前显示的 watch 表盘上一个问题与任何应用程序保留在内存，这使快速启动应用并提高应用程序的响应速度。
- 复杂情况轻松用户启动的 watchOS 应用中的特定功能。

## <a name="glanceable-notification"></a>Glanceable 通知

Apple Watch 上的通知提供一种很好可自定义的方式来快速通知事件或新信息，例如传入的消息或实现健身应用中的目标的用户。

通过使用一条通知，有价值的信息可以快速呈现给用户。 在许多情况下，设计良好的通知可以删除用户实际启动该应用程序的必要性。

熟悉 watchOS 3，所有通知现在支持：

- SpriteKit
- SceneKit
- 内联视频

## <a name="enhanced-ui-with-spritekit-and-scenekit"></a>使用 SpriteKit 和 SceneKit 增强的 UI

通常情况下，开发人员可能会认为的游戏 UI 提到 SpriteKit SceneKit 时。 但是，可用于创建非游戏包括自定义的布局，内容的 Ui 和动画，否则不能在单独的 WatchKit SpriteKit 和 SceneKit。

例如，来自照片共享应用程序的用户通知可以使用 SpriteKit 通过包括发布实际的图像和其他自定义的信息，使其更加丰富的用户体验以及图片的用户提供丰富的用户体验。

此外，SpriteKit 和 SceneKit 可以混合使用应用程序的用户界面设计中的标准 WatchKit UI 元素。

## <a name="simple-navigation"></a>简单的导航

watchOS 3 介绍了几种方法，开发人员可以简化如新 watchOS 应用中的导航[垂直分页](#vertical-paging)，[手势识别器支持](#gesture-recognizer-support)和[数字 Crown旋转](#digital-crown-rotation)上面介绍的功能。

数字 Crown 是唯一的 Apple Watch，可在许多不同的方式来简化导航。 例如，计时器应用程序可以使用数字 Crown 要通过可用计时器长度清理。

自定义笔势可以提供新的唯一方式与 watch 应用进行交互的用户，并还可用于简化应用导航。

Apple 建议寻找方法来组合所有 watchOS 3 提供丰富、 轻松、 快速使用 watchOS 应用接口中添加的新快速交互功能。

## <a name="finishing-the-quick-interaction"></a>完成快速交互

设计良好的快速交互体验将为用户提供的置信度，若要删除其手腕 （和取消与该应用程序） 当他们已完成当前的交互。

具体而言，这就在执行任何类型的网络连接或使用其辅助 iPhone 应用程序共享信息 watch 应用时是一个问题。 这常常会导致等待指示符在事务开始执行时，其快速交互期间不需要这样做。 请参见以下示例：

[![](quick-interaction-techniques-images/quick07.png "Watch 应用执行的网络连接和使用其辅助 iPhone 应用程序共享信息的关系图")](quick-interaction-techniques-images/quick07.png#lightbox)

1. 用户选择要购买 watch 上的项。
2. 用户点击购买按钮。
3. 应用程序启动网络事务，并显示加载指示器。
4. 一段时间后，在事务完成，并应用显示是购买确认。
5. 用户删除其手腕并与该应用程序中脱开。

从用户点击购买按钮，直到事务完成的时，它们具有引发其手腕查看加载指示器。 若要解决这种情况下，Apple 建议开发人员应呈现给用户而不是显示加载指示器的即时反馈。 

使用 Apple 的建议的模型，看看相同快速交互试：

[![](quick-interaction-techniques-images/quick08.png "Apple 建议的模型关系图")](quick-interaction-techniques-images/quick08.png#lightbox)

1. 用户选择要购买 watch 上的项。
2. 用户点击购买按钮。
3. 应用程序启动网络事务，并显示一条消息指出已成功启动购买。
4. 用户删除其手腕并与该应用程序中脱开。
5. 当事务已成功完成一段时间后时，应用会显示本地通知来通知成功购买的用户。

此时，只要用户点击它们会显示一条消息指出购买已启动，以便可以更有信心地放置其手腕并结束快速交互此时购买按钮。 更高版本他们通知所做的成功或失败的用户通知中的事务。 这样一来，用户仅与应用交互的过程"活动"阶段。

对于正在网络的应用程序，它们可以使用背景`NSURLSession`来处理与下载任务的网络通信。 这将允许应用程序以唤醒在后台处理下载的信息。 对于需要后台处理的应用程序，使用后台任务断言来处理所需的处理。

## <a name="quick-interaction-design-tips"></a>快速交互设计提示

由于快速交互所需的长度为 2 秒或更少，开发人员应该关注的应用程序的交互从一开始设计过程的设计。 找到其中这些交互 （使用上面介绍的技术） 可以简化和 watchOS 3 的新功能用于使应用程序快速且高度可响应的区域。

Apple 建议遵照以下：

- 通过向前将应用的最常用的功能，从而关注快速交互。
- 使用复杂情况和用户通知以呈现常见特性和功能。
- 使用 SceneKit 和 SpriteKit 创建丰富、 glanceable 用户界面。
- 只要有可能，简化了在应用程序中的导航。
- 永远不会让用户等待，允许它们进行删除其手腕并尽可能快地取消与该应用程序。

## <a name="summary"></a>总结

本文只讨论了快速交互技术 watchOS 3 和如何实现它们在 Xamarin.iOS 中转换为 Apple Watch 中添加了 Apple。

## <a name="related-links"></a>相关链接

- [watchOS 示例](https://developer.xamarin.com/samples/watchos/all/)
