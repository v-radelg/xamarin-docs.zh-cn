---
title: 棒糖形功能
description: 本文对 Android 5.0 (棒糖) 中引入的新功能进行了简要概述。 这些功能包括新的用户界面样式 (称为 "材料主题") 以及新的支持功能, 如动画、视图阴影和绘制色调。 Android 5.0 还包括增强的通知、两个新的 UI 小组件、一个新的作业计划程序和一些新的 Api, 以改善存储、网络、连接和多媒体功能。
ms.prod: xamarin
ms.assetid: 1CE99CFE-FAAC-49FC-AEDC-1A21FC6E946E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: c89b78e58918d2b561c6236ac273ffdb872211f0
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511401"
---
# <a name="lollipop-features"></a>棒糖形功能

_本文对 Android 5.0 (棒糖) 中引入的新功能进行了简要概述。这些功能包括新的用户界面样式 (称为 "材料主题") 以及新的支持功能, 如动画、视图阴影和绘制色调。Android 5.0 还包括增强的通知、两个新的 UI 小组件、一个新的作业计划程序和一些新的 Api, 以改善存储、网络、连接和多媒体功能。_

## <a name="lollipop-overview"></a>棒糖形概述

Android 5.0 (棒糖形) 引入了一种新的设计语言和*材料设计*, 并支持新功能的强制转换, 使应用更易于、更直观。 使用实质性设计, Android 5.0 不仅为 Android 手机提供 facelift;它还为基于 Android 的平板电脑、桌面计算机、手表和智能电视提供一组新的设计规则。 这些设计规则强调了简易性和 minimalism, 同时使用熟悉的 tactile 属性 (如实际的表面和边缘提示) 来帮助用户快速直观地了解接口。

*材料主题*是 Android 中这些 UI 设计原则的体现。 本文首先介绍材料主题的支持功能:

-   **动画**触摸反馈动画、活动过渡动画、查看状态转换动画和显示效果。 &ndash;

-   **查看阴影和提升**视图现在具有属性; 值较高`elevation`的视图在背景上转换较大的阴影。 `elevation` &ndash;

-   **颜色特征**可绘制着色使你可以通过更改图像资产的颜色来重复使用图像资产, 而*突出的颜色提取*可帮助你基于图像中的颜色动态地为应用提供主题。 &ndash;

许多材料主题功能已内置于 Android 5.0 UI 体验中, 而另一些则必须显式添加到应用中。 例如, 某些标准视图 (如按钮) 已经包含触摸反馈动画, 而应用程序必须启用大多数视图阴影。

除了通过材料主题引入的 UI 改进之外, Android 5.0 还包括本文中所述的一些其他新功能:

-   **增强的通知**Android 5.0 中的通知已使用全新的外观 (对锁屏通知的支持) 和新的打印头通知演示格式进行了重大更新。 &ndash;

-   **新的 UI 小组件**新的小组件使应用程序更易于传达大数据集和复杂信息, 而新`CardView`小组件提供了一种简化的、类似于卡的表示形式, 用于显示文本和图像。 `RecyclerView` &ndash;

-   **新 api**&ndash; Android 5.0 添加了用于多个网络支持的新 api、更高的蓝牙连接性、更轻松的存储管理和更灵活的多媒体播放机和照相机设备控制。 新作业计划功能可用于在计划时间异步运行任务。 此功能有助于缩短电池使用时间, 例如, 在设备接通电源和充电时要执行的任务。


## <a name="requirements"></a>要求

若要在基于 Xamarin 的应用中使用新的 Android 5.0 功能, 需要满足以下要求:

-   必须安装和配置**Xamarin** xamarin4.20或更高版本,并且必须安装VisualStudio或VisualStudioforMac。&ndash; 

-   **Android SDK**&ndash; Android 5.0 (API 21) 或更高版本必须通过 Android SDK 管理器进行安装。

-   **Java 开发人员工具包** &ndash; Xamarin.Android 需要 [JDK 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 如果要针对 API 级别24或更高版本进行开发, 则需要安装 Xamarin 1.8 或更高版本 (JDK 1.8 还支持早于24的 api 级别, 包括棒糖形)。 如果使用的是自定义控件或窗体预览器, 则需要64位版本的 JDK 1.8。

如果要专门针对 API 级别23或更早版本进行开发, 可以继续使用[JDK 1.7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) 。


## <a name="setting-up-an-android-50-project"></a>设置 Android 5.0 项目

若要创建 Android 5.0 项目, 必须安装最新的工具和 SDK 包。 使用以下步骤设置面向 Android 5.0 的 Xamarin Android 项目:

1. 安装 Xamarin Android 工具并激活 Xamarin 许可证。 有关安装 Xamarin 的详细信息, 请参阅[设置和安装](~/android/get-started/installation/index.md)。

2. 如果使用 Visual Studio for Mac, 请安装最新的 Android 5.0 更新。

3. 启动 Android SDK 管理器 (在 Visual Studio for Mac 中,**使用&gt;工具打开 Android SDK&hellip;管理器**) 并安装 Android SDK Tools 23.0.5 或更高版本:

    [![在 Android SDK 管理器中选择 Android SDK 工具](lollipop-images/android-l-tools-sml.png)](lollipop-images/android-l-tools.png#lightbox)

   同时, 安装最新的 Android 5.0 SDK 包 (API 21 或更高版本):

    [![在 Android SDK 管理器中安装 Android 5.0 SDK 包](lollipop-images/android-l-sdk-pkgs-sml.png)](lollipop-images/android-l-sdk-pkgs.png#lightbox)

   有关使用 Android SDK 管理器的详细信息, 请参阅[SDK 管理器](https://developer.android.com/tools/help/sdk-manager.html)。

4. 创建新的 Xamarin Android 项目。 如果你不熟悉通过 Xamarin 进行 Android 开发, 请参阅[Hello, android](~/android/get-started/hello-android/index.md)了解如何创建 android 项目。 创建 Android 项目时, 请务必配置 Android 5.0 的版本设置。
   在 Visual Studio for Mac 中, 导航到 "**项目&gt;选项&gt; " "生成常规**", 并将 "**目标框架**" 设置为**Android 5.0 (棒糖)** 或更高

    ![将目标 Framwework 设置为 Android 5.0 棒糖形](lollipop-images/target-framework.png)

   在 "**项目&gt;选项&gt; " "生成 android 应用程序**" 下, 将 "最低" 和 "目标 Android 版本" 设置为**自动使用目标框架版本**:

    ![将最低和目标 Android 版本设置为自动](lollipop-images/minimum-android-version.png)

5. 配置仿真程序或 Android 设备以测试您的应用程序。 如果使用的是仿真程序, 请参阅[Android Emulator 安装程序](~/android/get-started/installation/android-emulator/index.md)以了解如何配置用于 Xamarin Studio 或 Visual Studio 的 Android 仿真程序。 如果使用 Android 设备, 请参阅[设置预览版 SDK](https://developer.android.com/preview/setup-sdk.html) , 了解如何更新适用于 android 5.0 的设备。 若要配置 Android 设备以运行和调试 Xamarin Android 应用程序, 请参阅[设置设备以进行开发](~/android/get-started/installation/set-up-device-for-development.md)。

注意:如果要更新面向 Android L 预览版的现有 Android 项目, 则必须将**目标 Framework**和**Android 版本**更新为上述值。

## <a name="important-changes"></a>重要更改

Android 5.0 中的更改可能会影响以前发布的 Android 应用。 具体而言, Android 5.0 将使用新的运行时和明显更改的通知格式。

### <a name="android-runtime"></a>Android 运行时

Android 5.0 使用新的 Android 运行时 (ART) 作为默认运行时, 而不是 Dalvik。 ART 实现了几个主要的新功能:

-   **预先 (AOT) 编译**&ndash;在首次启动应用程序之前, AOT 可以通过编译应用程序代码来提高应用程序性能。 在安装应用程序时, ART 将为目标设备生成已编译的应用程序可执行文件。

-   **改进的垃圾回收 (GC)** &ndash;作品的 GC 改进还可以提高应用程序性能。 垃圾回收现在使用一个 GC 暂停, 而不是两个, 并发 GC 操作以更及时的方式完成。

-   **改进的应用程序调试**&ndash; ART 提供更多诊断详细信息, 以帮助分析异常和故障报告。

现有应用应在不更改的情况&ndash;下正常工作, 除非应用程序利用了以前的 Dalvik 运行时所独有的技术, 而这可能不会在艺术中使用。 有关这些更改的详细信息, 请参阅[验证 Android 运行时上的应用行为 (ART)](https://developer.android.com/guide/practices/verifying-apps-art.html)。


### <a name="notification-changes"></a>通知更改

在 Android 5.0 中, 通知发生了重大更改:

-   **声音和振动的处理方式不同**`Ringtone` `MediaPlayer` `Vibrator`通知声音和 vibrations 现在由`Notification.Builder`而不是、和来处理。 &ndash;

-   **新配色方案**&ndash;根据材料主题, 在白色或非常浅的背景上, 用暗文本呈现通知。 此外, 可以通过 Android 修改通知图标中的 alpha 通道, 以与系统颜色方案协调。 

-   **锁屏通知**&ndash;通知现在可以显示在设备锁屏上。

-   **打印头**&ndash;当设备解锁并且屏幕打开时, 高优先级通知现在会显示在小型浮动窗口 (打印头提示) 中。

在大多数情况下, 将现有应用通知功能移植到 Android 5.0 需要执行以下步骤:

1.  将代码转换为使用`Notification.Builder` (或`NotificationsCompat.Builder`) 来创建通知。 

2.  验证你的现有通知资产是否可在新的材料主题配色方案中查看。

3.  确定通知显示在锁屏上时应具有的可见性。 如果通知不是公共的, 则锁屏上应显示哪些内容？

4.  设置通知的类别, 使其在新的 Android 5.0 "请勿*打扰*" 模式下得到正确处理。

如果通知显示传输控件、显示媒体播放状态、使用`RemoteControlClient`或调用`ActivityManager.GetRecentTasks`, 请参阅重要的[行为更改](https://developer.android.com/preview/api-overview.html#Behaviors), 了解有关更新 Android 5.0 通知的详细信息。

有关在 Android 中创建通知的信息, 请参阅[本地通知](~/android/app-fundamentals/notifications/local-notifications.md)。

## <a name="material-theme"></a>材料主题

新的 Android 5.0 材料主题提供对 Android UI 的外观的丰富更改。 视觉对象现在使用 tactile 表面, 这些图面采用了基于打印的设计的粗体图形、版式和明亮颜色。 下面的屏幕截图中描述了材料主题的示例:

[![材料主题主屏幕、应用屏幕和设置屏幕的屏幕截图](lollipop-images/android-5-gallery-labeled-sml.png)](lollipop-images/android-5-gallery-labeled.png#lightbox)

Android 5.0 打招呼你的主屏幕显示在左侧。 中心屏幕截图是应用列表的第一个屏幕, 右侧屏幕截图是 "**设置**" 屏幕。 Google 的[材料设计](https://material.io/guidelines/material-design/introduction.html)规范介绍了新的材料主题概念背后的底层设计规则。

材料主题包括三种可在应用中使用的内置风格: `Theme.Material`深色主题 (默认设置) `Theme.Material.Light` 、主题和`Theme.Material.Light.DarkActionBar`主题: 

[![深色、浅色和 DarkActionBar 主题的屏幕截图](lollipop-images/three-material-themes-sml.png)](lollipop-images/three-material-themes.png#lightbox)

有关在 Xamarin Android 应用中使用材料主题功能的详细信息, 请参阅[材料主题](~/android/user-interface/material-theme.md)。


## <a name="animations"></a>Animations

Android 5.0 提供触摸反馈动画、活动过渡动画和查看状态转换动画, 使应用接口更直观地使用。 此外, Android 5.0 应用可以使用*显示效果*动画来隐藏或显示视图。 您可以使用*弯曲动作*设置来配置呈现速度和缓慢动画的方式。


### <a name="touch-feedback-animations"></a>触摸反馈动画

触摸反馈动画在接触视图时为用户提供视觉反馈。 例如, 按钮在触摸&ndash;时显示波纹效果, 这是 Android 5.0 中默认的触摸反馈动画。 波纹动画由新`RippleDrawable`类实现。 波纹效果可以配置为在视图边界处结束, 或超出视图的边界。 例如, 以下屏幕截图序列说明了触摸动画期间按钮中的波纹效果:

![按钮上的波纹动画的帧按帧屏幕截图](lollipop-images/touch-animation.png)

按钮的初始接触联系出现在左侧的第一个图像中, 而剩余序列 (从左到右) 说明了波纹效果如何扩散到按钮的边缘。 当波纹动画结束时, 视图将恢复为其原始外观。 默认波纹动画的发生比例为一秒, 但是动画的长度可自定义为更长或更短的时间长度。

有关 Android 5.0 中触摸反馈动画的详细信息, 请参阅[自定义触摸反馈](https://developer.android.com/training/material/animations.html#Touch)。


### <a name="activity-transition-animations"></a>活动过渡动画

活动过渡动画使用户能够在一个活动转换到另一个活动时了解可视连续性。 应用可指定三种类型的过渡动画:

-   **输入转换**&ndash;当活动进入场景时。

-   **退出转换**&ndash;当活动退出场景时。

-   **共享元素转换**&ndash;对于两个活动共用的视图, 当第一个活动转换到下一个活动时, 将更改为。

例如, 下面的屏幕截图序列说明了共享元素转换:

[![共享元素过渡动画的帧按帧屏幕截图](lollipop-images/activity-transition-sml.png)](lollipop-images/activity-transition.png#lightbox)

共享元素 (caterpillar 的照片) 是第一个活动的几个视图中的一个;它会放大为第二个活动中的唯一视图, 第一个活动转换为第二个活动。

#### <a name="enter-transition-animation-types"></a>输入过渡动画类型

对于 enter 转换, Android 5.0 提供了三种类型的动画:

-   **分解动画**&ndash;将视图从场景中心放大。

-   **滑动动画**&ndash;将视图从场景的一个边缘移入。

-   **淡化动画**&ndash;将视图淡化为场景。

#### <a name="exit-transition-animation-types"></a>退出过渡动画类型

对于退出转换, Android 5.0 提供了三种类型的动画:

-   **分解动画**&ndash;将视图缩小到场景的中心。

-   **滑动动画**&ndash;将视图移到场景的一个边缘。

-   **淡化动画**&ndash;将视图淡出场景。

#### <a name="shared-element-transition-animation-types"></a>共享元素过渡动画类型

共享元素转换支持多种动画类型, 如:

-   更改视图的布局或剪辑边界。

-   更改视图的缩放和旋转。

-   更改视图的大小和缩放类型。

有关 Android 5.0 中活动过渡动画的详细信息, 请参阅[自定义活动过渡](https://developer.android.com/training/material/animations.html#Transitions)。


### <a name="view-state-transition-animations"></a>查看状态转换动画

Android 5.0 使动画在视图状态发生更改时可以运行。 您可以使用以下方法之一对视图状态转换进行动画处理:

-   创建对与特定视图关联的状态更改进行动画处理的绘图。 新`AnimatedStateListDrawable`类使你能够创建绘图, 用于显示视图状态更改之间的动画。

-   定义视图状态更改时运行的动画功能。 使用新`StateListAnimator`的类可以定义在视图状态更改时运行的 animator。

有关 Android 5.0 中的视图状态转换动画的详细信息, 请参阅[动画视图状态更改](https://developer.android.com/training/material/animations.html#ViewState)。


### <a name="reveal-effect"></a>显示效果

*显示效果*是更改 radius 以显示或隐藏视图的剪辑圆圈。 您可以通过设置剪辑圆的初始半径和最终半径来控制此效果。 下面的屏幕截图序列说明了屏幕中心的 "显示效果动画":

[![显示动画的帧按帧屏幕截图](lollipop-images/reveal-center-sml.png)](lollipop-images/reveal-center.png#lightbox)

下一序列说明了在屏幕左下角发生的显示效果动画:

[![剪裁动画的帧按帧屏幕截图](lollipop-images/reveal-left-sml.png)](lollipop-images/reveal-left.png#lightbox)

可以反转显示动画;也就是说, 剪辑圆形可以缩小以隐藏视图, 而不是放大显示视图。

有关中的 Android 5.0 显示效果的详细信息, 请参阅[使用 "显示效果"](https://developer.android.com/training/material/animations.html#Reveal)。


### <a name="curved-motion"></a>弯曲动作

除了这些动画功能, Android 5.0 还提供了新的 Api, 使您能够指定动画的时间和运动曲线。 Android 5.0 使用这些曲线在动画过程中插入临时和空间移动。 在 Android 5.0 中定义了三条曲线:

-   **快速扩展线性\_快速加速,直到动画结束时才继续。\_\_** &ndash;

-   **在动画结束\_时, 快速缓慢加速速度快、缓慢减速。\_\_** &ndash;

-   **线性\_输出\_速度慢\_** 于从峰值速度开始,慢慢减速到动画结束。&ndash;

您可以使用新`PathInterpolator`类来指定运动内插的发生方式。 `PathInterpolator`是根据指定的控制点和运动曲线遍历动画路径的插值程序。 有关如何在 Android 5.0 中指定弯曲动作设置的详细信息, 请参阅[使用弯曲动作](https://developer.android.com/training/material/animations.html#CurvedMotion)。


## <a name="view-shadows--elevation"></a>视图阴影 & 提升

在 Android 5.0 中, 可以通过设置新`Z`属性来指定视图的提升。 值越大`Z` , 视图在背景上转换的阴影越大, 就会使视图在背景之上显得更高。 您可以通过在布局中配置`elevation`视图的属性来设置视图的初始提升。

下面的示例演示了一个空控件在其`TextView`提升特性设置为2dp、4dp 和6dp 时, 由空控件强制转换的阴影:

[![Progessively 放大视图的屏幕截图](lollipop-images/view-shadows-sml.png)](lollipop-images/view-shadows.png#lightbox)

视图阴影设置可以是静态的 (如上所示), 也可以在动画中使用, 以使视图显示在视图的背景之上。 可以使用`ViewPropertyAnimator`类对视图的提升进行动画处理。 视图的提升是其布局`elevation`设置`translationZ`和可通过`ViewPropertyAnimator`方法调用设置的属性的总和。

有关 Android 5.0 中的视图阴影的详细信息, 请参阅[定义阴影和剪切视图](https://developer.android.com/training/material/shadows-clipping.html)。


## <a name="color-features"></a>颜色特征

Android 5.0 提供了两个用于在应用中管理颜色的新功能:

-   利用可*绘制的着色*, 可以通过更改布局特性来更改图像资产的颜色。

-   *突出颜色提取*使你能够动态自定义应用的颜色主题, 以与所显示图像的调色板进行协调。


### <a name="drawable-tinting"></a>绘制色调

Android 5.0 布局识别了一个`tint`新属性, 可用于设置绘图的颜色, 而无需创建这些资产的多个版本即可显示不同的颜色。 若要使用此功能, 请将位图定义为 alpha 掩码, 并使用`tint`属性定义资产的颜色。 这样, 你就可以创建一次资产, 并在布局中对它们进行着色, 使其与你的主题匹配。

在以下示例中, 单个图像资产&ndash;具有透明背景&ndash;的白色徽标用于创建淡色变体:

![带有透明背景的白色 Xamarin 徽标](lollipop-images/xamarin-logo-white.png)

此徽标显示在蓝色圆形背景之上, 如以下示例中所示。 左侧的图像是在没有`tint`设置的情况下显示徽标的方式。 在中心图像中, 徽标的`tint`属性设置为深灰色。 在右侧的图像中, `tint`将设置为浅灰色:

![具有不同淡色设置的上述徽标的示例](lollipop-images/drawable-tinting.png)

有关 Android 5.0 中的 "绘制颜色" 的详细信息, 请参阅[绘制淡色](https://developer.android.com/training/material/drawables.html#DrawableTint)。


### <a name="prominent-color-extraction"></a>突出颜色提取

利用新的 Android `Palette` 5.0 类, 您可以从图像中提取颜色, 以便可以将它们动态应用于自定义调色板。 `Palette`类从图像中提取六个颜色, 并根据颜色饱和度和亮度的相对级别来标记这些颜色:

-   活跃

-   鲜暗

-   强光

-   柔

-   黑色暗

-   静音

例如, 在下面的屏幕截图中, 照片查看应用程序从显示的图像中提取突出显示的颜色, 并使用这些颜色来调整应用程序的配色方案, 使其与图像匹配:

[![绿色、粉色和蓝色主题颜色提取的屏幕截图](lollipop-images/prominent-color-extraction-sml.png)](lollipop-images/prominent-color-extraction.png#lightbox)

在上面的屏幕截图中, 操作栏设置为已提取的 "鲜艳亮度" 颜色, 背景设置为已提取的 "鲜艳暗" 颜色。 在上述每个示例中, 都包含一个小颜色方块行, 用于说明从图像中提取的调色板颜色。

有关 Android 5.0 中的颜色提取的详细信息, 请参阅[从图像中提取明显颜色](https://developer.android.com/training/material/drawables.html#ColorExtract)。


## <a name="new-ui-widgets"></a>新的 UI 小组件

Android 5.0 引入了两个新的 UI 小组件:

-   `RecyclerView`&ndash;显示可滚动项列表的视图组。

-   `CardView`&ndash;带有圆角的基本布局。

这两个小组件都包含融入支持材料主题功能;例如, `RecyclerView`使用动画来添加和删除视图, 并`CardView`使用视图阴影使每个卡片显示在背景之上。 以下屏幕截图显示了这些新小组件的示例:

[![用 RecyclerView 生成的应用程序的屏幕截图](lollipop-images/recyclerview-cardview-sml.png)](lollipop-images/recyclerview-cardview.png#lightbox)

左侧屏幕截图是在电子邮件应用中`RecyclerView`使用的示例, 右侧的屏幕截图是在旅行预订应用中使用的`CardView`示例。


### <a name="recyclerview"></a>RecyclerView

`RecyclerView`类似于`ListView,` , 但它更适合包含动态变化元素的大型视图或列表集。 与`ListView,`指定适配器访问基础数据集一样。 但是, 与`ListView,`使用*布局管理器*在中`RecyclerView`定位项不同。 布局管理器还负责查看回收;它管理不再向用户显示的项视图的重复使用。

使用`RecyclerView`小组件时, 必须`LayoutManager`指定和适配器。 如图所示, `LayoutManager`是适配器`RecyclerView`与之间的中介:

![具有支持 LayoutManager、适配器和 Dataset 的 RecyclerView 关系图](lollipop-images/recyclerview-diagram.png)

下面的屏幕快照演示`RecyclerView`了一个包含100项的 (每个项`ImageView`都包含`TextView`一个和一个):

[![RecyclerView 应用滚动浏览图像的屏幕截图](lollipop-images/recyclerview-scroll-sml.png)](lollipop-images/recyclerview-scroll.png#lightbox)

`RecyclerView`在此示例应用程序中, &ndash;此示例应用程序仅需几秒钟, 就可以轻松地从列表的开头滚动到列表末尾。 `RecyclerView`还支持动画;事实上, 默认情况下会启用添加和删除项的动画。 将项添加到`RecyclerView`时, 会将项淡入, 如以下屏幕截图序列中所示:

[![每照片项目的帧屏幕截图淡入](lollipop-images/recyclerview-animation-sml.png)](lollipop-images/recyclerview-animation.png#lightbox)

有关的详细`RecyclerView`信息, 请参阅[RecyclerView](~/android/user-interface/layouts/recycler-view/index.md)。


### <a name="cardview"></a>CardView

`CardView`是一个简单的视图, 用于模拟带有圆角的浮动卡。 由于`CardView`具有内置的视图阴影, 因此它提供了一种简单的方法来向应用程序添加可视深度。 以下屏幕截图显示了三个面向文本的`CardView`示例:

[![使用 RecyclerView 和基于 CardView 的项的应用的示例屏幕截图](lollipop-images/recyclerview-cardview-sml.png)](lollipop-images/recyclerview-cardview.png#lightbox)

上面示例中的每个卡片都包含一个`TextView`; 背景色是`cardBackgroundColor`通过属性设置的。

有关的详细`CardView`信息, 请参阅[CardView](~/android/user-interface/controls/card-view.md)。


## <a name="enhanced-notifications"></a>增强的通知

Android 5.0 中的通知系统已使用全新的视觉格式和新功能进行了重大更新。 通知在 Android 5.0 中具有新的外观。 例如, Android 5.0 中的通知现在使用浅色背景上的黑色文本:

![未展开 Android 5.0 通知的示例](lollipop-images/expanded-notification-contracted.png)

如果通知中显示大图标 (如上面的示例所示), Android 5.0 会将小图标显示为大图标上的徽章。 

在 Android 5.0 中, 通知也可以出现在设备锁屏上。
例如, 以下是使用单个通知的锁屏的示例屏幕截图:

[![锁定屏幕上显示的通知屏幕截图](lollipop-images/lockscreen-notification-sml.png)](lollipop-images/lockscreen-notification.png#lightbox)

用户可以在锁屏上双击通知来解锁设备, 跳转到源自该通知的应用程序, 或轻扫以消除通知。 通知具有新的*可见性*设置, 可确定锁屏上可显示的内容量。 用户可以选择是否允许敏感内容显示在锁屏通知中。

Android 5.0 引入了新的高优先级通知演示文稿格式, 称为 "*打印头*"。 "打印头通知" 从屏幕顶部向下滑动几秒钟, 然后回到屏幕顶部的通知底纹。 "打印头通知" 使系统 UI 可以将重要信息放在用户前面, 而不会中断当前正在运行的活动。 下面的示例演示了一个在应用程序顶部显示的简单打印头通知:

[![打印头通知示例](lollipop-images/heads-up-notification-sml.png)](lollipop-images/heads-up-notification.png#lightbox)

打印头通知通常用于以下事件:

-   新的下一条消息

-   传入的电话呼叫

-   电池电量不足指示

-   警报

仅当 Android 5.0 具有高优先级或最高优先级设置时, Android 才会显示该通知。

在 Android 5.0 中, 可以提供通知元数据, 以帮助 Android 更智能地排序和显示通知。 Android 5.0 根据 "优先级"、"可见性" 和 "类别" 组织通知。
通知类别用于筛选设备处于 "*不干扰*" 模式时可以显示的通知。

有关创建和启动包含最新 Android 5.0 功能的通知的详细信息, 请参阅[本地通知](~/android/app-fundamentals/notifications/local-notifications.md)。


## <a name="new-apis"></a>新 API

除了上述全新的外观功能, Android 5.0 还添加了新的 Api, 用于扩展现有多媒体、存储和无线/连接功能的功能。 此外, Android 5.0 包括新的 Api, 可提供对新作业计划程序功能的支持。

### <a name="camera"></a>照相机

Android 5.0 提供了几个新的 Api, 用于增强照相机功能。 新`Android.Hardware.Camera2`命名空间包含用于访问连接到 Android 设备的各个照相机设备的功能。 同时, `Android.Hardware.Camera2`将每个照相机设备建模为管道: 它接受捕获请求, 捕获图像, 然后输出结果。 此方法使应用能够将多个捕获请求排队到照相机设备上。

以下 Api 可以实现这些新功能:

-   `CameraManager.GetCameraIdList`帮助你以编程方式访问照相机设备; 用于`CameraManager.OpenCamera`连接到特定相机设备。 &ndash;

-   `CameraCaptureSession`&ndash;捕获或流式传输照相机设备中的图像。 实现`CameraCaptureSession.CaptureListener`接口以处理新的映像捕获事件。

-   `CaptureRequest`&ndash;定义捕获参数。

-   `CaptureResult`&ndash;提供映像捕获操作的结果。

有关 Android 5.0 中新相机 Api 的详细信息, 请参阅[媒体](https://developer.android.com/about/versions/android-5.0.html#Media)。

### <a name="audio-playback"></a>音频播放

Android 5.0 更新`AudioTrack`类, 以便更好地播放音频:

-   `ENCODING_PCM_FLOAT`将配置`AudioTrack`为接受浮点格式的音频数据, 以获得更好的动态范围、更大的空间和更高的质量 (感谢精度增加)。 &ndash; 而且, 浮点格式有助于避免音频剪辑。

-   `ByteBuffer`你现在可以将音频数据作为字节数组提供给`AudioTrack`。 &ndash;

-   `WRITE_NON_BLOCKING`&ndash;此选项可简化某些应用的缓冲和多线程处理。

有关 Android 5.0 `AudioTrack`中的改进的详细信息, 请参阅[媒体](https://developer.android.com/about/versions/android-5.0.html#Media)。

### <a name="media-playback-control"></a>媒体播放控件

Android 5.0 引入了新`Android.Media.MediaController`类, 它将`RemoteControlClient`替换。 `Android.Media.MediaController`提供简化的传输控制 Api, 并为 UI 上下文外的播放提供线程安全的控制。 以下新 Api 处理传输控制:

-   `Android.Media.Session.MediaSession`&ndash;处理多个控制器的媒体控制会话。 调用`MediaSession.GetSessionToken`以请求应用用于与会话进行交互的令牌。

-   `MediaController.TransportControls`处理传输命令, 如**播放**、**停止**和**跳过。** &ndash;

此外, 还可以使用新`Android.App.Notification.MediaStyle`类将媒体会话与丰富通知内容 (如提取和显示唱片集画面) 相关联。

有关 Android 5.0 中新增的媒体播放控制功能的详细信息, 请参阅[媒体](https://developer.android.com/about/versions/android-5.0.html#Media)。

### <a name="storage"></a>存储

Android 5.0 更新存储访问框架, 使应用程序能够更轻松地使用目录和文档:

-   若要选择目录子树, 可以构建并发送`Android.Intent.Action.OPEN_DOCUMENT_TREE`意向。 这种意向会使系统显示支持子树选择的所有提供程序实例;然后, 用户浏览并选择一个目录。

-   若要在子树下的任意位置创建和管理新文档或目录, `CreateDocument`请`RenameDocument`使用的`DeleteDocument` `DocumentsContract`新的、和方法。

-   若要获取所有共享存储设备上的媒体目录的路径, 请调用`Android.Content.Context.GetExternalMediaDirs`新方法。

有关 Android 5.0 中的新存储 Api 的详细信息, 请参阅[存储](https://developer.android.com/preview/api-overview.html#Storage)。

### <a name="wireless--connectivity"></a>无线 & 连接

Android 5.0 添加了以下适用于无线和连接的 API 增强功能:

-   新的*多网络*api, 使应用程序能够在建立连接前使用特定功能查找和选择网络。

-   蓝牙广播功能, 可使 Android 5.0 设备充当低能耗蓝牙外设。

-   NFC 增强功能使你可以更轻松地使用近域通信功能与其他设备共享数据。

有关 Android 5.0 中新增的无线和连接 Api 的详细信息, 请参阅[无线和连接](https://developer.android.com/preview/api-overview.html#Wireless)。

### <a name="job-scheduling"></a>作业计划

Android 5.0 引入了一个`JobScheduler`新的 API, 可通过将特定任务计划为仅在设备接通电源和充电时运行, 来帮助用户最大限度地减少电池消耗。 此作业计划程序功能还可用于计划当条件更适合该任务时要运行的任务, 例如, 当设备通过 Wi-fi 网络而不是按流量计费的网络进行连接时, 下载大文件。

有关 Android 5.0 中新作业计划 Api 的详细信息, 请参阅[计划作业](https://developer.android.com/preview/api-overview.html#JobScheduler)。

## <a name="summary"></a>总结

本文提供了适用于 Xamarin 的 5.0 Android 应用开发人员的重要新功能概述:

-   材料主题

-   Animations

-   查看阴影和提升

-   颜色特征, 例如, 绘制色调和突出颜色提取

-   新`RecyclerView`的和`CardView`小组件

-   通知增强功能

-   用于照相机、音频播放、媒体控制、存储、无线/连接和作业计划的新 Api

如果你不熟悉 Xamarin Android 开发, 请阅读[安装和安装](~/android/get-started/installation/index.md)以帮助你开始安装 xamarin。
[你好, android](~/android/get-started/hello-android/index.md)是了解如何创建 android 项目的绝佳简介。



## <a name="related-links"></a>相关链接

- [Android L 开发者预览版](https://developer.android.com/preview/index.html)
- [获取 Android SDK](https://developer.android.com/sdk/index.html#Other)
- [材料设计](https://developer.android.com/preview/material/index.html)
- [材料设计原则](http://static.googleusercontent.com/media/www.google.com/en/us/design/material-design.pdf)
