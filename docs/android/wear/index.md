---
title: Android 穿戴设备
description: 生成适用于 Android 可穿戴设备的应用。
ms.prod: xamarin
ms.assetid: 3BE4A128-2D88-4500-9E48-20375EA99A49
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/16/2018
ms.openlocfilehash: 63fb9c3b1484026cb390a8a475c711562dfd0771
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028594"
---
# <a name="android-wear"></a>Android 穿戴设备

Android 磨损是适用于智能手表等可穿戴设备的 Android 版本。 本部分包含有关如何安装和配置应用程序所需的工具的说明，这是创建第一个磨损设备的分步演练，以及可用于创建自己的应用程序的示例列表。

## <a name="getting-startedandroidwearget-startedindexmd"></a>[入门](~/android/wear/get-started/index.md)

介绍 Android 磨损，介绍如何安装和配置计算机以进行磨损开发，并提供相关步骤来帮助您在模拟器或磨损设备上创建和运行第一个 Android 应用程序。

## <a name="user-interfaceandroidwearuser-interfaceindexmd"></a>[用户界面](~/android/wear/user-interface/index.md)

介绍 Android 特定于控件的控件，并提供指向演示如何使用这些控件的示例的链接。

## <a name="platform-featuresandroidwearplatformindexmd"></a>[平台功能](~/android/wear/platform/index.md)

本部分中的文档涵盖特定于 Android 磨损的功能。 可在此处找到介绍如何创建 WatchFace 的主题。

## <a name="screen-sizesandroidwearscreen-sizesmd"></a>[屏幕大小](~/android/wear/screen-sizes.md)

预览并优化可用屏幕大小的用户界面。

## <a name="deployment--testingandroidweardeploy-testindexmd"></a>[部署和测试](~/android/wear/deploy-test/index.md)

介绍如何将 Android 应用部署到 Android 磨损设备或配置为磨损的 Android 仿真程序。 它还包括有关如何在开发计算机和 Android 设备之间设置蓝牙连接的调试提示和信息。

## <a name="wear-apishttpsdeveloperandroidcomreferenceandroidsupportwearable"></a>[磨损 Api](https://developer.android.com/reference/android/support/wearable)

Android 开发人员网站提供了有关关键损耗 Api 的详细信息，如[可穿戴活动](https://developer.android.com/reference/android/support/wearable/activity/package-summary.html)、[意向](https://developer.android.com/reference/com/google/android/wearable/intent/package-summary.html)、[身份验证](https://developer.android.com/reference/android/support/wearable/authentication/package-summary.html)、[复杂性](https://developer.android.com/reference/android/support/wearable/complications/package-summary.html)、[复杂渲染](https://developer.android.com/reference/android/support/wearable/complications/rendering/package-summary.html)、[通知](https://developer.android.com/reference/android/support/wearable/notifications/package-summary.html) [视图](https://developer.android.com/reference/android/support/wearable/view/package-summary.html)和[WatchFace](https://developer.android.com/reference/android/support/wearable/watchface/package-summary.html)。

## <a name="samples"></a>示例

可以使用 Android 磨损查找一些[示例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Android+wear)（或直接前往[github](https://github.com/xamarin/monodroid-samples/tree/master/wear)）。

|示例|描述|屏幕快照|
|--- |--- |--- |
|[SkeletonWear](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-skeletonwear)|可穿戴项目基础知识的简单示例，包括 GridViewPager 和交互式通知。|![Skeletonwear 的屏幕截图](images/skeleton.png)|
|[WatchViewStub](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-watchviewstub)|WatchViewStub 控件的简单演示，可检测屏幕形状并自动加载正确的布局。 了解 WatchViewStub 在**Resources/layout/main_activity**布局中的工作方式。|![WatchViewStub 的屏幕截图](images/watchview.png)|
|[RecipeAssistant](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-recipeassistant)|用食谱步骤的形式演示了磨损通知页。 通知是在 RecipeService.cs 中创建的。|![RecipeAssistant 的屏幕截图](images/recipeassist.png)|
|[ElizaChat](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-elizachat)|与名为 Eliza 的 "个人助手" 交互的有趣示例，使用磨损交互式通知创建使用固定响应的会话。|![ElizaChat 的屏幕截图](images/eliza.png)|
|[GridViewPager](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-gridviewpager)|GridViewPager 实现2D 导航模式，用户可在其中垂直 swipes，然后水平滚动以通过选项和内容进行导航。|![GridViewPager 的屏幕截图](images/gridviewpager.png)|
|[WatchFace](https://docs.microsoft.com/samples/xamarin/monodroid-samples/wear-watchface)|WatchFace 是一种自定义的监视面，具有模拟样式的小时、分钟和秒。 此示例演示如何创建一个用于绘制当前时间并处理环境模式和可见性更改事件的手表人脸服务。 它包括侦听时区更改的广播接收器，并会相应地自动更新时间。|![WatchFace 的屏幕截图](images/gridviewpager.png)|

## <a name="videos"></a>视频

查看以下视频链接，其中讨论了支持磨损的 Xamarin：

|描述|屏幕快照|
|--- |--- |
|[Android l，更多](https://blog.xamarin.com/webinar-recording-android-l-and-so-much-more/)&ndash; Android l 开发者预览版引入了很多的新 api，使开发人员可以利用，包括材料设计、通知和新动画等。|![演示的视频屏幕截图](images/video-android-l.png)|
|[在我的耳中以及我的眼睛中： Google 玻璃和 Android 磨损&ndash;可穿戴计算可能与未来（或检查工具小剧集）类似，但很多人现在已经欢迎使用！ C# ](https://www.youtube.com/watch?v=80H8tXByZQc) C#开发人员了解到这一点，并且已经掌握了用于充分利用可穿戴设备功能的工具和技能（从2014演变为）。|![演示的视频屏幕截图](images/video-eyes-ears.png)|
|[Xamarin 中的新增功能](https://www.youtube.com/watch?v=Gpqc2XZIQfU)&ndash; android L、android 磨损、android 电视机、android 自动、材料设计和艺术;这对你作为 Xamarin 开发人员意味着什么？from 演化为2014。|![演示的视频屏幕截图](Images/video-whats-new.png)|

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
