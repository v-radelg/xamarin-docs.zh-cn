---
title: 在 Xamarin.iOS ARKit 简介
description: 本文档介绍与 ARKit iOS 11 中的增强的现实。 它讨论了如何将三维模型添加到应用程序、 配置视图、 会话委托实施，在世界中，定位三维模型和暂停增强的现实会话。
ms.prod: xamarin
ms.assetid: 70291430-BCC1-445F-9D41-6FBABE87078E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/30/2017
ms.openlocfilehash: 348d2f2090105ed693da7be5a44c82ef18bd2a89
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61176690"
---
# <a name="introduction-to-arkit-in-xamarinios"></a>在 Xamarin.iOS ARKit 简介

_对于 iOS 11 增强的现实_

ARKit 使各种增强的现实应用程序和游戏。 此节涵盖以下主题：

- [ARKit 入门](#gettingstarted)
- [ARKit 使用 UrhoSharp](urhosharp.md)

<a name="gettingstarted" />

## <a name="getting-started-with-arkit"></a>ARKit 入门

若要开始使用增强现实，下面的说明，引导完成简单的应用程序： 定位三维模型并让 ARKit 就地保留的模型，使用其跟踪功能。

![Jet 浮点照相机中图像的三维模型](images/jet-sml.png)

### <a name="1-add-a-3d-model"></a>1.添加三维模型

应将资产添加到与项目**SceneKitAsset**生成操作。

![在项目中的 SceneKit 资产](images/scene-assets.png)


### <a name="2-configure-the-view"></a>2.配置视图

在视图控制器`ViewDidLoad`方法中，加载场景资产并将设置`Scene`在视图上的属性：

```csharp
ARSCNView SceneView = (View as ARSCNView);

// Create a new scene
var scene = SCNScene.FromFile("art.scnassets/ship");

// Set the scene to the view
SceneView.Scene = scene;
```

### <a name="3-optionally-implement-a-session-delegate"></a>3.可以选择实现会话委托

尽管无需进行简单的情况下，实现会话委托可能有助于调试 ARKit 会话 （以及在实际的应用程序，为用户提供反馈） 的状态。 创建一个简单的委派，使用以下代码：

```csharp
public class SessionDelegate : ARSessionDelegate
{
  public SessionDelegate() {}
  public override void CameraDidChangeTrackingState(ARSession session, ARCamera camera)
  {
    Console.WriteLine("{0} {1}", camera.TrackingState, camera.TrackingStateReason);
  }
}
```

分配中的委托中`ViewDidLoad`方法：

```csharp
// Track changes to the session
SceneView.Session.Delegate = new SessionDelegate();
```

### <a name="4-position-the-3d-model-in-the-world"></a>4.在世界中定位的三维模型

在`ViewWillAppear`，下面的代码建立 ARKit 会话并设置相对于设备的摄像机的空间中的三维模型的位置：

```csharp
// Create a session configuration
var configuration = new ARWorldTrackingConfiguration {
  PlaneDetection = ARPlaneDetection.Horizontal,
  LightEstimationEnabled = true
};

// Run the view's session
SceneView.Session.Run(configuration, ARSessionRunOptions.ResetTracking);

// Find the ship and position it just in front of the camera
var ship = SceneView.Scene.RootNode.FindChildNode("ship", true);

ship.Position = new SCNVector3(2f, -2f, -9f);
```

每次运行该应用程序或将其恢复后，将在摄像机前放置三维模型。 一旦定位模型，使照相机并观察 ARKit 保留定位模型。

### <a name="5-pause-the-augmented-reality-session"></a>5.暂停增强的现实会话

它是很好的做法暂停 ARKit 会话不可见时的视图控制器 (在`ViewWillDisappear`方法：

```csharp
SceneView.Session.Pause();
```

## <a name="summary"></a>总结

上面的代码会导致一个简单的 ARKit 应用程序。 更复杂的示例所期望的托管实现在增强的现实会话的视图控制器`IARSCNViewDelegate`，并实现其他方法。

ARKit 提供大量更复杂的功能，如图面上跟踪和用户交互。 请参阅[UrhoSharp 演示](urhosharp.md)有关组合使用 UrhoSharp 跟踪 ARKit 示例。


## <a name="related-links"></a>相关链接

- [增强的现实 (Apple)](https://developer.apple.com/arkit/)
- [ARKit 使用 UrhoSharp](urhosharp.md)
- [简单 ARKit (Jet) 示例](https://developer.xamarin.com/samples/monotouch/ios11/ARKitSample/)
- [ARKit 放置对象 （示例）](https://developer.xamarin.com/samples/monotouch/ios11/ARKitPlacingObjects/)
- [引入 ARKit-适用于 iOS (WWDC) （视频） 的增强现实](https://developer.apple.com/videos/play/wwdc2017/602/)
