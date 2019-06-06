---
title: Android 穿戴设备
description: 构建适用于 Android 的可穿戴设备的应用程序。
ms.prod: xamarin
ms.assetid: 3BE4A128-2D88-4500-9E48-20375EA99A49
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/16/2018
ms.openlocfilehash: dda00760399572d714300f1487391212c6fa0998
ms.sourcegitcommit: d3f48bfe72bfe03aca247d47bc64bfbfad1d8071
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2019
ms.locfileid: "66740867"
---
# <a name="android-wear"></a>Android 穿戴设备

Android 穿戴设备是 android 的专为智能手表等可穿戴设备版本。 本部分包括有关如何安装和配置穿戴设备开发，创建第一个穿戴设备设备，并可以引用以创建你自己 Wear 应用的示例的列表的分步演练所需的工具的说明。

##  <a name="getting-startedandroidwearget-startedindexmd"></a>[入门](~/android/wear/get-started/index.md)

引入了 Android Wear、 介绍如何安装和配置计算机进行穿戴设备开发和提供了有助于创建和运行仿真程序或在穿戴设备上的第一个 Android Wear 应用的步骤。

##  <a name="user-interfaceandroidwearuser-interfaceindexmd"></a>[用户界面](~/android/wear/user-interface/index.md)

介绍了 Android Wear 特定控件，并提供指向演示如何使用这些控件的示例。

##  <a name="platform-featuresandroidwearplatformindexmd"></a>[平台功能](~/android/wear/platform/index.md)

在本部分中的文档介绍特定于 Android Wear 的功能。 此处，您会发现本主题介绍如何创建 WatchFace。

##  <a name="screen-sizesandroidwearscreen-sizesmd"></a>[屏幕大小](~/android/wear/screen-sizes.md)

预览并优化您的用户界面为可用的屏幕尺寸。

##  <a name="deployment--testingandroidweardeploy-testindexmd"></a>[部署和测试](~/android/wear/deploy-test/index.md)

介绍如何将 Android Wear 应用部署到 Android Wear 设备或 Android 仿真程序配置的损耗。 它还包括调试提示和有关如何设置开发计算机和 Android 设备之间的蓝牙连接的信息。

##  <a name="wear-apishttpsdeveloperandroidcomreferenceandroidsupportwearable"></a>[Wear Api](https://developer.android.com/reference/android/support/wearable)

Android 开发人员站点提供有关关键 Wear Api 详细的信息，如[可穿戴活动](https://developer.android.com/reference/android/support/wearable/activity/package-summary.html)，[意向](https://developer.android.com/reference/com/google/android/wearable/intent/package-summary.html)，[身份验证](https://developer.android.com/reference/android/support/wearable/authentication/package-summary.html)， [复杂性](https://developer.android.com/reference/android/support/wearable/complications/package-summary.html)，[呈现的复杂情况](https://developer.android.com/reference/android/support/wearable/complications/rendering/package-summary.html)，[通知](https://developer.android.com/reference/android/support/wearable/notifications/package-summary.html)，[视图](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)，并且[WatchFace](https://developer.android.com/reference/android/support/wearable/watchface/package-summary.html)。



## <a name="samples"></a>示例

您可以找到大量[示例](https://developer.xamarin.com/samples/android/Android%20Wear/)使用 Android Wear (或直接转到[github](https://github.com/xamarin/monodroid-samples/tree/master/wear))。

|示例|描述|屏幕快照|
|--- |--- |--- |
|[SkeletonWear](https://developer.xamarin.com/samples/monodroid/wear/SkeletonWear/)|可穿戴项目，包括 GridViewPager 和交互式通知的基础知识的简单示例。|![Skeletonwear 的屏幕截图](images/skeleton.png)|
|[WatchViewStub](https://developer.xamarin.com/samples/monodroid/wear/WatchViewStub/)|检测到屏幕形状并自动加载正确的布局的 WatchViewStub 控件的简单演示。 请参阅的 WatchViewStub 工作原理**Resources/layout/main_activity.xml**布局。|![WatchViewStub 的屏幕截图](images/watchview.png)|
|[RecipeAssistant](https://developer.xamarin.com/samples/monodroid/wear/RecipeAssistant/)|Wear 通知页，方案步骤形式的演示。 RecipeService.cs 中创建通知。|![RecipeAssistant 的屏幕截图](images/recipeassist.png)|
|[ElizaChat](https://developer.xamarin.com/samples/monodroid/wear/ElizaChat/)|与"个人助理"交互的有趣的示例调用 Eliza，穿戴设备交互式通知用于创建使用内置的响应的会话。|![ElizaChat 的屏幕截图](images/eliza.png)|
|[GridViewPager](https://developer.xamarin.com/samples/monodroid/wear/GridViewPager/)|GridViewPager 实现 2D 导航模式，其中在用户轻扫垂直，然后水平导航选项和内容。|![Screenshot of GridViewPager](images/gridviewpager.png)|
|[WatchFace](https://developer.xamarin.com/samples/monodroid/wear/WatchFace)|WatchFace 是自定义的观察人脸与模拟样式小时、 分钟和第二个指针。 此示例演示如何创建监视人脸服务用于绘制当前时间和句柄的环境模式和可见性更改事件。 它包括一个广播的接收器，侦听的时区更改并自动将相应地更新时间。|![WatchFace 的屏幕截图](images/gridviewpager.png)|


##  <a name="videos"></a>视频

请查看这些视频链接讨论 Xamarin.Android 使用穿戴设备的支持：

|描述|屏幕快照|
|--- |--- |
|[Android L 和很多](https://blog.xamarin.com/webinar-recording-android-l-and-so-much-more/) &ndash; Android L 开发者预览版引入了大量的新 Api，开发人员能够充分利用，包括 Material Design、 通知和新动画，仅举几例。|![演示文稿的视频屏幕快照](images/video-android-l.png)|
|[C#在我和我的眼睛就是：Google 玻璃效果和 Android Wear](https://www.youtube.com/watch?v=80H8tXByZQc) &ndash;可穿戴计算可能看起来像未来 （或检查器小工具集），但许多人已经立即利用未来 ！ C#开发人员知道这和已有的工具和技能，以利用可穿戴设备 （从发展 2014) 的功能。|![演示文稿的视频屏幕快照](images/video-eyes-ears.png)|
|[什么是 Xamarin.Android 中的新增功能](https://www.youtube.com/watch?v=Gpqc2XZIQfU) &ndash; Android L、 Android Wear、 Android TV、 Android Auto、 Material Design 和将来使用。 此 mean 到您为 Xamarin 开发人员？ 从发展 2014年。|![演示文稿的视频屏幕快照](Images/video-whats-new.png)|


<!--

March 18
https://blog.xamarin.com/android-wear/

August 14
https://blog.xamarin.com/android-l-developer-preview-android-wear-support/

August 27
https://blog.xamarin.com/tips-for-your-first-android-wear-app/

Watch Face
https://github.com/Redth/Xamarin.Wear.WatchFace
-->
