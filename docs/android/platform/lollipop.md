---
title: 棒糖形功能
description: 本文提供了在 Android 5.0 (Lollipop) 中引入的新功能的高级别概述。 这些功能包括新的用户界面样式称为材料主题还包含新的支持功能，如动画、 视图阴影和 drawable 色调。 Android 5.0 还包括增强的通知、 两个新的 UI 小组件、 新的作业计划程序和少量新的 Api 来提高存储、 网络、 连接性和多媒体功能。
ms.prod: xamarin
ms.assetid: 1CE99CFE-FAAC-49FC-AEDC-1A21FC6E946E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: d6173e1886eaf807decd960b07acc022bb17c04d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61177361"
---
# <a name="lollipop-features"></a>棒糖形功能

_本文提供了在 Android 5.0 (Lollipop) 中引入的新功能的高级别概述。这些功能包括新的用户界面样式称为材料主题还包含新的支持功能，如动画、 视图阴影和 drawable 色调。Android 5.0 还包括增强的通知、 两个新的 UI 小组件、 新的作业计划程序和少量新的 Api 来提高存储、 网络、 连接性和多媒体功能。_

## <a name="lollipop-overview"></a>棒糖形概述

Android 5.0 (Lollipop) 引入了新的设计语言， *Material Design*，并与之支持强制转换的新功能，可让应用更加轻松、 更直观地使用。 使用 Material Design Android 5.0 不只提供 Android 手机改动;它还提供一组新的设计规则，用于基于 Android 的平板电脑、 台式计算机、 监视和智能电视。 这些设计规则强调简单性和 minimalism 同时充分利用熟悉触觉属性 （如实际的图面和边缘提示），以帮助用户快速、 直观地了解到该接口。

*材料主题*是 Android UI 设计原则的体现。 本文首先介绍材料主题支持功能：

-   **动画** &ndash; *触摸反馈*动画*活动转换*动画*查看状态转换*动画和*显示效果*。

-   **查看阴影和提升**&ndash;视图现在具有`elevation`属性; 与视图更高版本`elevation`值强制转换可查看大阴影背景上的。

-   **颜色功能** &ndash; *Drawable 色调*使您可以通过更改其颜色，重用图像资产并*突出的颜色提取*可帮助你动态您的应用程序基于图像中颜色主题。

很多材料主题功能已内置到 Android 5.0 UI 体验，而其他人必须显式添加到应用程序。 例如，某些标准视图 （如按钮） 时，应用必须启用大多数视图阴影已包括触摸反馈动画。

除了通过材料主题带来的 UI 改进，Android 5.0 还在本文中包含几个介绍了其他新功能：

-   **增强的通知** &ndash; Android 5.0 中的通知已大幅更新使用全新的外观、 对锁屏通知的支持和新*平视*通知显示格式。

-   **新的 UI 小组件**&ndash;的新`RecyclerView`小组件可简化应用程序以提供大型数据集和复杂信息和新`CardView`小组件提供的简化类似于卡的显示格式，用于显示文本和映像。

-   **新的 Api** &ndash; Android 5.0 将添加新 Api，可用于多个网络支持，改进的蓝牙连接、 更简单的存储管理，以及更灵活地控制多媒体播放器和摄像机设备。 新的作业日程安排功能是可用于运行任务以异步方式在计划的时间。 此功能有助于提高电池使用寿命，例如，计划任务在插入设备时就位和收费。


## <a name="requirements"></a>要求

以下是所需的基于 Xamarin 的应用中使用新的 Android 5.0 功能：

-   **Xamarin.Android** &ndash; Xamarin.Android 4.20 或更高版本必须安装并配置与 Visual Studio 或 Visual Studio for mac。 

-   **Android SDK** &ndash; Android 5.0 (API 21) 或更高版本必须安装通过 Android SDK 管理器。

-   **Java 开发人员工具包** &ndash; Xamarin.Android 需要[JDK 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)或更高，如果你正在开发的 API 级别 24 或更高版本 （JDK 1.8 还支持 API 级别低于 24，包括棒糖形）。 如果使用的自定义控件或窗体预览程序，则需要 JDK 1.8 的 64 位版本。

你可以继续使用[JDK 1.7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)如果您是开发专门针对 API 级别 23 或更早版本。


## <a name="setting-up-an-android-50-project"></a>设置 Android 5.0 项目

若要创建 Android 5.0 项目时，必须安装最新工具和 SDK 包。 使用以下步骤设置 Xamarin.Android 项目面向 Android 5.0:

1. 安装 Xamarin.Android 工具并激活 Xamarin 许可证。 请参阅[设置和安装](~/android/get-started/installation/index.md)有关安装 Xamarin.Android 的详细信息。

2. 如果你使用 Visual Studio for Mac，安装最新的 Android 5.0 更新。

3. 启动 Android SDK 管理器 (在 Visual Studio for Mac 中，使用**工具&gt;打开 Android SDK 管理器&hellip;**) 并安装 Android SDK Tools 23.0.5 或更高版本：

    [![选择 Android SDK 管理器中的 Android SDK 工具](lollipop-images/android-l-tools-sml.png)](lollipop-images/android-l-tools.png#lightbox)

   此外，安装最新的 Android 5.0 SDK 包 (API 21 或更高版本):

    [![安装 Android SDK 管理器中的 Android 5.0 SDK 包](lollipop-images/android-l-sdk-pkgs-sml.png)](lollipop-images/android-l-sdk-pkgs.png#lightbox)

   有关使用 Android SDK 管理器的详细信息，请参阅[SDK 管理器](https://developer.android.com/tools/help/sdk-manager.html)。

4. 创建新的 Xamarin.Android 项目。 如果您不熟悉如何使用 Xamarin 进行 Android 开发，请参阅[Hello，Android](~/android/get-started/hello-android/index.md) ，了解如何创建 Android 项目。 创建 Android 项目时，请确保配置的 Android 5.0 版本设置。
   在 Visual Studio for Mac 中，导航到**项目选项&gt;构建&gt;常规**并设置**目标框架**到**Android 5.0 (Lollipop)** 或更高版本：

    ![将目标 Framwework 设置为 Android 5.0 Lollipop](lollipop-images/target-framework.png)

   下**项目选项&gt;构建&gt;Android 应用程序**，设置最小和目标 Android 版本**自动-使用目标框架版本**:

    ![为自动设置的最小值和目标 Android 版本](lollipop-images/minimum-android-version.png)

5. 配置要测试你的应用的仿真程序或 Android 设备。 如果要使用模拟器，请参阅[Android 仿真程序安装程序](~/android/get-started/installation/android-emulator/index.md)若要了解如何使用 Xamarin Studio 或 Visual Studio 中配置用于 Android 模拟器。 如果使用 Android 设备，请参阅[设置预览版 SDK](https://developer.android.com/preview/setup-sdk.html)若要了解如何为 Android 5.0 更新你的设备。 若要配置用于运行和调试 Xamarin.Android 应用程序在 Android 设备，请参阅[设置设备进行开发](~/android/get-started/installation/set-up-device-for-development.md)。

注意:如果要更新现有的 Android 项目是面向 Android 的 L 预览，则必须更新**目标框架**并**Android 版本**上面所述的值。

## <a name="important-changes"></a>重要的更改

以前已发布 Android 应用程序可能会影响 Android 5.0 中的更改。 具体而言，Android 5.0 使用新的运行时和显著更改的通知格式。

### <a name="android-runtime"></a>Android 运行时

Android 5.0 使用而不是 Dalvik 默认运行时作为新 Android 运行时 （图片）。 艺术实现几个主要的新增功能：

-   **提前时间的 (AOT) 编译** &ndash; AOT 编译应用程序代码之前首次启动应用程序可以提高应用性能。 安装应用时，画生成已编译的应用可执行文件的目标设备。

-   **改进了垃圾回收 (GC)** &ndash;画中的 GC 改进方面还可以提高应用性能。 现在垃圾回收使用一个 GC 暂停，而不是两个，并更及时的并发 GC 操作完成。

-   **改进了应用程序调试**&ndash;画面提供了更多诊断的详细信息，以帮助分析异常和故障报告。

现有应用程序应进行任何修改下画&ndash;除了利用技术到上一 Dalvik 运行时唯一的应用，这可能无法工作下画。 有关这些更改的详细信息，请参阅[验证应用行为在 Android 运行时 （艺术）](https://developer.android.com/guide/practices/verifying-apps-art.html)。


### <a name="notification-changes"></a>通知更改

通知发生了重大更改 Android 5.0 中：

-   **以不同方式处理声音和振动**&ndash;通知听起来和振动现在由`Notification.Builder`而不是`Ringtone`， `MediaPlayer`，并`Vibrator`。

-   **新的配色方案**&ndash;材料主题根据通知用深文本呈现在白色或非常浅色背景上。 此外，Android 来协调使用系统配色方案可能修改中通知图标的 alpha 通道。 

-   **锁屏通知**&ndash;现在可以在设备锁屏上显示通知。

-   **平视**&ndash;高优先级的通知现在显示在小的浮动窗口 （危险警告通知） 时该设备已解锁，并打开屏幕。

在大多数情况下，移植到 Android 5.0 的现有应用程序通知功能需要执行以下步骤：

1.  转换代码以使用`Notification.Builder`(或`NotificationsCompat.Builder`) 用于创建通知。 

2.  验证你的现有通知资产可在新材料主题配色方案中查看。

3.  决定您的通知时显示在锁屏上应具有何种可见性。 如果通知不是公共的哪些内容应显示在锁屏上？

4.  设置你的通知的类别，因此它们在新的 Android 5.0 中正确处理*请勿打扰*模式。

如果您的通知显示传输控件，显示媒体播放状态，使用`RemoteControlClient`，或调用`ActivityManager.GetRecentTasks`，请参阅[重要行为更改](https://developer.android.com/preview/api-overview.html#Behaviors)有关更新适用于 Android 通知的详细信息5.0。

在 Android 中创建通知的信息，请参阅[本地通知](~/android/app-fundamentals/notifications/local-notifications.md)。 [兼容性](~/android/app-fundamentals/notifications/local-notifications.md#compatibility)本文的部分说明了如何创建通知来向下兼容使用早期版本的 Android。


## <a name="material-theme"></a>材料主题

新的 Android 5.0 材料主题为 Android 用户界面的外观和感觉带来了重大改动。 可视元素现在使用边用粗体显示的图形、 版式和打印目的设计的明亮的颜色对其执行的图面。 材料主题的示例中的以下屏幕截图所示：

[![材料主题主页屏幕、 应用屏幕和设置屏幕的屏幕快照](lollipop-images/android-5-gallery-labeled-sml.png)](lollipop-images/android-5-gallery-labeled.png#lightbox)

Android 5.0 向您致意显示在左侧的主屏幕。 中心的屏幕截图是第一个屏幕的应用列表中，并在右侧屏幕截图是**设置**屏幕。 Google [Material Design](https://material.io/guidelines/material-design/introduction.html)规范说明新材料主题概念背后的基础设计规则。

材料主题包括可以在应用中使用的三种内置风格：`Theme.Material`深色主题 （默认值），`Theme.Material.Light`主题，和`Theme.Material.Light.DarkActionBar`主题： 

[![深色屏幕截图、 灯光和 DarkActionBar 主题](lollipop-images/three-material-themes-sml.png)](lollipop-images/three-material-themes.png#lightbox)

有关在 Xamarin.Android 应用中使用材料主题功能的详细信息，请参阅[材料主题](~/android/user-interface/material-theme.md)。


## <a name="animations"></a>Animations

Android 5.0 提供了触摸反馈动画、 活动过渡动画和视图状态过渡动画，以使应用界面更加直观使用。 此外，Android 5.0 应用程序可以使用*显示效果*动画来隐藏或显示视图。 可以使用*曲线运动*呈现需要如何快速配置设置或缓慢的动画。


### <a name="touch-feedback-animations"></a>触摸反馈动画

视图具有触摸时，触摸反馈动画向用户提供可视反馈。 例如，按钮现在显示波纹效果时接触&ndash;这是在 Android 5.0 默认触摸反馈动画。 Ripple 动画实现的新`RippleDrawable`类。 波纹效果可以配置为视图的边界处结束或扩展到视图的边界之外。 例如，下面的屏幕截图序列触摸动画期间说明按钮中波纹效果：

![框架中通过的波纹动画按钮上的帧屏幕快照](lollipop-images/touch-animation.png)

初始触控接触与按钮出现在左侧的第一个图像中的剩余序列 （从左到右） 说明了如何波纹效果向外扩散到按钮的边缘。 在 ripple 动画结束时，视图将返回到其原始的外观。 默认波纹动画发生在第二个，一小部分，但可以自定义动画的长度的时间更长或更短长度。

触摸反馈动画在 Android 5.0 中的详细信息，请参阅[自定义触摸反馈](https://developer.android.com/training/material/animations.html#Touch)。


### <a name="activity-transition-animations"></a>活动过渡动画

当转换到另一个活动，活动过渡动画为用户提供 visual 连续性的意义。 应用程序可以指定三种类型的过渡动画：

-   **输入过渡**&ndash;为当某个活动进入场景。

-   **退出过渡**&ndash;为活动退出场景时。

-   **共享元素过渡**&ndash;为当普遍适用于两个活动的视图更改为，第一个活动将转换为下一步。

例如，以下一系列屏幕截图显示了共享的元素转换：

[![框架中通过的共享的元素过渡动画帧屏幕快照](lollipop-images/activity-transition-sml.png)](lollipop-images/activity-transition.png#lightbox)

共享的元素 （毛虫照片） 是一个多个视图中的第一个活动;它将放大成为第二个活动的形式，第一个活动将转换为第二个中的唯一视图。

#### <a name="enter-transition-animation-types"></a>输入转换的动画类型

Enter 键转换为 Android 5.0 提供了三种类型的动画：

-   **Explode 动画**&ndash;放大场景的中心中的视图。

-   **滑动动画**&ndash;将视图中移动从一个场景的边缘。

-   **淡入动画**&ndash;淡场景的视图。

#### <a name="exit-transition-animation-types"></a>退出过渡动画类型

对退出过渡，Android 5.0 提供了三种类型的动画：

-   **Explode 动画**&ndash;收缩到中心的场景的视图。

-   **滑动动画**&ndash;将一个视图移到的一个场景的边缘。

-   **淡入动画**&ndash;淡入淡出场景的视图。

#### <a name="shared-element-transition-animation-types"></a>共享元素转换的动画类型

共享的元素转换支持多个类型的动画，如：

-   更改视图的布局或剪辑边界。

-   更改的规模和视图的旋转。

-   更改视图的大小和扩展类型。

有关在 Android 5.0 中的活动过渡动画的详细信息，请参阅[自定义活动转换](https://developer.android.com/training/material/animations.html#Transitions)。


### <a name="view-state-transition-animations"></a>视图状态过渡动画

Android 5.0 使动画运行时的视图状态更改。 您可以使用以下方法之一对视图状态转换进行动画处理：

-   创建进行动画处理的特定视图相关联的状态更改的绘图。 新`AnimatedStateListDrawable`类可用于创建显示视图状态更改之间的动画的绘图。

-   定义的视图状态更改时运行的动画功能。 新`StateListAnimator`类允许您定义的视图状态更改时运行动画。

有关在 Android 5.0 中的视图状态过渡动画的详细信息，请参阅[进行动画处理视图状态更改](https://developer.android.com/training/material/animations.html#ViewState)。


### <a name="reveal-effect"></a>显示效果

*显示效果*是一个剪辑的圆的更改半径，以显示或隐藏视图。 可以通过设置剪辑圆的初始和最终半径控制这种效果。 以下一系列屏幕截图演示了显示的效果动画从中心的屏幕：

[![帧屏幕截图显示动画的框架](lollipop-images/reveal-center-sml.png)](lollipop-images/reveal-center.png#lightbox)

下一步的序列演示了一个发生从屏幕的左下角的显示效果动画：

[![框架中通过的剪辑动画帧屏幕快照](lollipop-images/reveal-left-sml.png)](lollipop-images/reveal-left.png#lightbox)

显示动画，可以撤消;也就是说，剪辑圆圈可以缩小以隐藏视图而不是放大以显示该视图。

中的 Android 5.0 显示效果的详细信息，请参阅[使用显示效果](https://developer.android.com/training/material/animations.html#Reveal)。


### <a name="curved-motion"></a>曲线的动作

除了这些动画功能，Android 5.0 还提供了新的 Api，使您能够指定动画的时间和运动曲线。 Android 5.0 使用这些曲线内插在动画期间在临时和空间移动。 Android 5.0 中定义了三种曲线：

-   **快速\_出\_线性\_中**&ndash;快速加快并加速动画结束时才将继续。

-   **快速\_出\_慢速\_中**&ndash;加快快速和缓慢减速的动画结束时。

-   **线性\_出\_慢速\_中**&ndash;与峰值速度缓慢的开头为动画减速。

你可以使用新`PathInterpolator`类，以指定动画内插进行。 `PathInterpolator` 是遍历动画路径根据指定的控制点和运动曲线内插器。 有关如何在 Android 5.0 中指定曲线的运动设置的详细信息，请参阅[使用曲线运动](https://developer.android.com/training/material/animations.html#CurvedMotion)。


## <a name="view-shadows--elevation"></a>查看阴影和提升

在 Android 5.0 中，可以指定*提升*视图通过设置一个新的`Z`属性。 一个大于`Z`值导致要投影更大的背景，使视图显示转换为浮点型上面在后台更高版本上的视图。 可以通过配置来设置视图的初始权限提升其`elevation`布局中的属性。

下面的示例演示了一个空进行强制转换阴影`TextView`控制当其提升属性设置为 2dp、 4dp 和 6dp，分别：

[![Progessively 较大视图阴影的屏幕截图](lollipop-images/view-shadows-sml.png)](lollipop-images/view-shadows.png#lightbox)

查看卷影设置可以是静态的 （如上所示），或它们可在动画以使视图显示视图的背景上暂时上升。 可以使用`ViewPropertyAnimator`类进行动画处理视图的提升。 视图的仰角是其布局的总和`elevation`设置加上`translationZ`属性，可通过设置`ViewPropertyAnimator`方法调用。

有关 Android 5.0 中的视图阴影的详细信息，请参阅[定义阴影和剪辑视图](https://developer.android.com/training/material/shadows-clipping.html)。


## <a name="color-features"></a>颜色功能

Android 5.0 提供了用于管理应用中的颜色的两项新功能：

-   *可绘制色调*可以通过更改布局属性更改颜色的图像资产。

-   *突出的颜色提取*使动态自定义应用的颜色主题与显示的图像的颜色调色板进行协调。


### <a name="drawable-tinting"></a>可绘制色调

Android 5.0 布局识别新`tint`属性，可以用于绘图的颜色设置而无需创建多个版本的这些资产，以显示不同的颜色。 若要使用此功能，您定义位图为 alpha 掩码和使用`tint`属性来定义资产的颜色。 这使得您可以一次创建资产并在布局以匹配你的主题中的这些颜色。

在下面的示例中，单个图像资产&ndash;带有透明背景的白色徽标&ndash;用于创建色彩变体：

![使用透明背景的白色 Xamarin 徽标](lollipop-images/xamarin-logo-white.png)

此徽标会显示上面蓝色圆圈为背景，如以下示例所示。 在左侧图像，而无需徽标的显示方式`tint`设置。 在中心图中，该徽标的`tint`属性设置为的暗灰色。 在右侧，映像中`tint`设置为浅灰色：

![使用不同的色彩设置更高版本的徽标的示例](lollipop-images/drawable-tinting.png)

有关可绘制、 在 Android 5.0 中的详细信息，请参阅[Drawable、](https://developer.android.com/training/material/drawables.html#DrawableTint)。


### <a name="prominent-color-extraction"></a>突出的颜色提取

新的 Android 5.0`Palette`类使您可以从图像中提取颜色，以便您可以动态地将它们应用到自定义调色板。 `Palette`类从图像中提取六种颜色和标签这些颜色根据其相对的色彩饱和度和亮度级别：

-   充满活力

-   充满活力的深色

-   充满活力的光

-   静音

-   静音的深色

-   静音的光

例如，在下面的屏幕截图，照片查看应用程序从上显示的图像中提取突出的颜色，并使用这些颜色调整的应用程序配色方案，以匹配图像：

[![绿色、 粉红色，和蓝色主题颜色提取内容的屏幕截图](lollipop-images/prominent-color-extraction-sml.png)](lollipop-images/prominent-color-extraction.png#lightbox)

在上面的屏幕中，在操作栏设置为提取"充满活力浅色"颜色和背景设置为提取"充满活力深色"颜色。 在上述每个示例中，小型彩色的平方值的行是包括在内，以说明了从图像中提取的调色板颜色。

有关在 Android 5.0 中的颜色提取有关的详细信息，请参阅[从映像中提取突出显示颜色](https://developer.android.com/training/material/drawables.html#ColorExtract)。


## <a name="new-ui-widgets"></a>新的 UI 小组件

Android 5.0 引入了两个新的 UI 小组件：

-   `RecyclerView` &ndash; 一组视图，显示的可滚动项列表。

-   `CardView` &ndash; 具有圆角的基本布局。

这两个小组件包括中随附材料主题功能; 支持例如，`RecyclerView`用于添加和删除视图，使用动画和`CardView`使用查看使每个卡似乎浮在背景的阴影。 在下面的屏幕截图显示了这些新的小组件的示例：

[![使用 RecyclerView 构建应用程序的屏幕快照](lollipop-images/recyclerview-cardview-sml.png)](lollipop-images/recyclerview-cardview.png#lightbox)

在左侧的屏幕截图是一种`RecyclerView`上使用电子邮件应用和屏幕截图中右侧是举例说明`CardView`旅游预订应用中使用。


### <a name="recyclerview"></a>RecyclerView

`RecyclerView` 类似于`ListView,`但更好地适用于大型集的视图或动态更改的元素的列表。 如`ListView,`指定适配器以访问基础数据集。 但是，与不同`ListView,`则使用*布局管理器*若要定位项目内`RecyclerView`。 布局管理器还负责视图回收;它管理项将不再对用户可见的视图的重用。

当你使用`RecyclerView`小组件中，您必须指定`LayoutManager`和适配器。 此图中所示`LayoutManager`是适配器之间的媒介和`RecyclerView`:

![包含支持 LayoutManager、 适配器和数据集的 RecyclerView 的关系图](lollipop-images/recyclerview-diagram.png)

下面的屏幕截图显示`RecyclerView`，其中包含 100 个项 (每个项组成`ImageView`和一个`TextView`):

[![RecyclerView 应用滚动浏览映像的屏幕截图](lollipop-images/recyclerview-scroll-sml.png)](lollipop-images/recyclerview-scroll.png#lightbox)

`RecyclerView` 轻松地处理此大型数据集&ndash;滚动列表以结束开始从在此示例中的列表的应用程序需要仅几秒钟。 `RecyclerView` 此外支持动画;事实上，默认情况下启用用于添加和删除项的动画。 当某项添加到`RecyclerView`，它淡在这一序列的屏幕截图中所示：

[![框架中通过帧中照片项淡入淡出屏幕截图](lollipop-images/recyclerview-animation-sml.png)](lollipop-images/recyclerview-animation.png#lightbox)

有关详细信息`RecyclerView`，请参阅[RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)。


### <a name="cardview"></a>CardView

`CardView` 是一个简单的视图，用于模拟带有圆角的浮动卡。 因为`CardView`具有内置视图阴影，它提供了用于轻松对你要向应用添加视觉深度。 以下屏幕截图显示的三个面向文本的示例`CardView`:

[![使用 RecyclerView 以及基于 CardView 的项应用程序的示例屏幕快照](lollipop-images/recyclerview-cardview-sml.png)](lollipop-images/recyclerview-cardview.png#lightbox)

在上面的示例数据卡的每个包含`TextView`; 通过设置背景色`cardBackgroundColor`属性。

有关详细信息`CardView`，请参阅[CardView](~/android/user-interface/controls/card-view.md)。


## <a name="enhanced-notifications"></a>增强的通知

已使用新的可视化格式和新功能显著更新 Android 5.0 中的通知系统。 通知 Android 5.0 中拥有了新外观。 例如，Android 5.0 中的通知现在深文本通过使用浅色背景：

![未展开的 Android 5.0 通知的示例](lollipop-images/expanded-notification-contracted.png)

当大图标显示在通知 （如上面的示例中所示） 时，Android 5.0 会小图标的徽章显示大图标上。 

在 Android 5.0，通知也会出现在设备锁屏上。
例如，下面是使用一条通知锁屏的示例屏幕截图：

[![在锁定屏幕上显示通知的屏幕截图](lollipop-images/lockscreen-notification-sml.png)](lollipop-images/lockscreen-notification.png#lightbox)

用户可以双击锁屏以解锁设备，并跳转到源自该通知的应用的通知或轻扫以取消通知。 通知有一个新*可见性*设置确定可以在锁屏上显示内容的数量。 用户可以选择是否允许在锁屏通知中显示的敏感内容。

Android 5.0 引入了新的高优先级通知演示文稿格式称为*平视*。 危险警告通知从屏幕顶部几秒钟向下滑动，然后返回到屏幕的顶部通知阴影撤回。 危险警告通知使系统用户界面而不会中断当前正在运行的活动将用户的重要信息。 下面的示例演示了显示在应用程序之上的简单危险警告通知：

[![Heads-up 通知示例](lollipop-images/heads-up-notification-sml.png)](lollipop-images/heads-up-notification.png#lightbox)

危险警告通知通常用于以下事件：

-   新的下一条消息

-   传入的电话呼叫

-   指示电池电量不足

-   警报

仅当它具有高或最大优先级设置时，android 5.0 格式平视显示通知。

在 Android 5.0 中，可以提供通知元数据，帮助 Android 进行排序和更智能地显示通知。 Android 5.0 组织根据优先级、 可见性和类别的通知。
通知类别用于筛选器可以显示的通知，当设备处于*请勿打扰*模式。

有关创建和启动通知与最新的 Android 5.0 功能的详细信息，请参阅[本地通知](~/android/app-fundamentals/notifications/local-notifications.md)。


## <a name="new-apis"></a>新 API

除了上面所述的新外观和体验功能，Android 5.0 将添加新 Api 用于扩展现有的多媒体功能、 存储和无线/连接功能。 此外，Android 5.0 包括了新的 Api，为新的作业计划程序功能提供支持。

### <a name="camera"></a>照相机

Android 5.0 为增强的照相机功能提供了几个新 Api。 新`Android.Hardware.Camera2`命名空间包含的功能，为访问单个摄像机设备连接到 Android 设备。 此外，`Android.Hardware.Camera2`模型作为管道的每个照相机设备： 它接受捕获请求、 捕获映像，并输出结果。 这种方法使应用程序排队到摄像机设备的多个捕获请求。

以下 Api 实现这些新功能：

-   `CameraManager.GetCameraIdList` &ndash; 可帮助你以编程方式访问相机的设备;您使用`CameraManager.OpenCamera`连接到特定的摄像机设备。

-   `CameraCaptureSession` &ndash; 捕获或流式处理从照相机设备映像。 您实现`CameraCaptureSession.CaptureListener`接口以处理新映像捕获的事件。

-   `CaptureRequest` &ndash; 定义捕获参数。

-   `CaptureResult` &ndash; 提供的映像捕获操作的结果。

有关新相机 Android 5.0 中的 Api 的详细信息，请参阅[媒体](https://developer.android.com/about/versions/android-5.0.html#Media)。

### <a name="audio-playback"></a>音频播放

Android 5.0 更新`AudioTrack`更好的音频播放的类：

-   `ENCODING_PCM_FLOAT` &ndash; 配置`AudioTrack`以接受更好的动态范围、 更大的空间和更高质量 （这得益于增加的精度） 的浮点格式的音频数据。 此外，浮点格式有助于避免音频剪辑。

-   `ByteBuffer` &ndash; 现在可以提供音频数据到`AudioTrack`作为字节数组。

-   `WRITE_NON_BLOCKING` &ndash; 此选项可以简化缓冲和多线程处理对于某些应用。

有关详细信息`AudioTrack`改进在 Android 5.0 中，请参阅[媒体](https://developer.android.com/about/versions/android-5.0.html#Media)。

### <a name="media-playback-control"></a>媒体播放控件

Android 5.0 引入了新`Android.Media.MediaController`类，它取代了`RemoteControlClient`。 `Android.Media.MediaController` 提供简化的传输控制 Api 并提供线程安全控件的 UI 上下文以外的播放。 以下新的 Api 处理传输控件：

-   `Android.Media.Session.MediaSession` &ndash; 媒体控制处理多个控制器的会话。 在调用`MediaSession.GetSessionToken`请求您的应用程序使用与会话进行交互的令牌。

-   `MediaController.TransportControls` &ndash; 负责处理传输等命令**播放**，**停止**，并**跳过**。

此外，你可以使用新`Android.App.Notification.MediaStyle`类能够富通知内容 （如提取和显示唱片集画面） 相关联的媒体会话。

有关在 Android 5.0 中的新媒体播放控件功能的详细信息，请参阅[媒体](https://developer.android.com/about/versions/android-5.0.html#Media)。

### <a name="storage"></a>存储

Android 5.0 更新存储访问框架，以使其容易使应用程序使用目录和文档：

-   若要选择的目录子树，可以生成并发送`Android.Intent.Action.OPEN_DOCUMENT_TREE`意向。 此意向会导致系统显示所有支持的子树选择; 的提供程序实例然后，用户浏览并选择一个目录。

-   若要创建和管理新文档或下一个子树的任意位置的目录，你使用新`CreateDocument`， `RenameDocument`，并`DeleteDocument`方法的`DocumentsContract`。

-   若要获取所有共享的存储设备上的媒体目录路径，请调用新`Android.Content.Context.GetExternalMediaDirs`方法。

有关新存储 Android 5.0 中的 Api 的详细信息，请参阅[存储](https://developer.android.com/preview/api-overview.html#Storage)。

### <a name="wireless--connectivity"></a>无线和连接性

Android 5.0 将添加以下 API 用于无线和连接性的增强功能：

-   新*多网络*Api，使应用程序以查找并建立连接之前，选择具有特定功能的网络。

-   启用 Android 5.0 设备，使其作为低耗电蓝牙外围设备的蓝牙广播功能。

-   NFC 增强，使其更轻松地与其他设备共享数据使用近场通信功能。

有关新的无线和连接 Android 5.0 中的 Api 的详细信息，请参阅[无线和连接](https://developer.android.com/preview/api-overview.html#Wireless)。

### <a name="job-scheduling"></a>作业计划

Android 5.0 引入了新`JobScheduler`API，可帮助用户最小化电池耗尽，通过计划在插入设备时才运行某些任务和收费。 此作业计划程序功能还可用于计划任务运行时满足条件，更适合该任务，如通过 Wi-fi 网络，而不是按流量计费的网络连接设备时下载大型文件。

有关新的作业计划 Android 5.0 中的 Api 的详细信息，请参阅[计划作业](https://developer.android.com/preview/api-overview.html#JobScheduler)。

## <a name="summary"></a>总结

本文为 Xamarin.Android 应用程序开发人员提供的 Android 5.0 中的重要新功能的概述：

-   材料主题

-   Animations

-   查看阴影和提升

-   颜色功能，如 drawable、 并突出显示颜色提取

-   新`RecyclerView`和`CardView`小组件

-   通知增强功能

-   相机、 音频播放、 media 控件、 存储、 无线连接和作业计划的新 Api

如果 Xamarin Android 开发新手，请阅读[设置和安装](~/android/get-started/installation/index.md)来帮助你开始使用 Xamarin.Android。
[Hello，Android](~/android/get-started/hello-android/index.md)是极好的入门学习如何创建 Android 项目。



## <a name="related-links"></a>相关链接

- [Android L 开发者预览版](https://developer.android.com/preview/index.html)
- [获取 Android SDK](https://developer.android.com/sdk/index.html#Other)
- [材料设计](https://developer.android.com/preview/material/index.html)
- [材料设计原则](http://static.googleusercontent.com/media/www.google.com/en/us/design/material-design.pdf)
