---
title: Xamarin 中的 SceneKit
description: 本文档介绍了 SceneKit，这是一个三维场景图形 API，可通过抽象掉 OpenGL 的复杂性来简化3D 图形的处理。
ms.prod: xamarin
ms.assetid: 19049ED5-B68E-4A0E-9D57-B7FAE3BB8987
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/14/2017
ms.openlocfilehash: d6e6ff02fef3d2919e9716dc8a456aabd9533820
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292803"
---
# <a name="scenekit-in-xamarinios"></a>Xamarin 中的 SceneKit

SceneKit 是一个3D 场景图形 API，可简化使用三维图形的操作。 它是在 OS X 10.8 中首次引入的，现已推出 iOS 8。 借助 SceneKit 创建沉浸式三维可视化效果和休闲三维游戏，不需要在 OpenGL 中提供专业知识。 SceneKit 是在常见场景图概念上构建的，它可以简化 OpenGL 和 OpenGL ES 的复杂性，使你可以轻松地将3D 内容添加到应用程序。 但是，如果您是 OpenGL 专家，则 SceneKit 也有很大的支持直接与 OpenGL 结合使用。 它还包括许多补充3D 图形的功能，如物理学，并与多个其他 Apple 框架（如核心动画、核心图像和 Sprite 工具包）紧密集成。

SceneKit 非常容易使用。 它是一种声明性 API，负责呈现。 只需设置场景、向其添加属性，SceneKit 将处理场景的渲染。

若要使用 SceneKit，请使用`SCNScene`类创建场景关系图。 场景包含节点的层次结构，由的实例`SCNNode`表示，用于定义三维空间中的位置。 每个节点都具有影响其外观的各种属性，如几何、照明和材料，如下图所示：

![](scenekit-images/image7.png "SceneKit 层次结构")

## <a name="create-a-scene"></a>创建场景

若要使场景显示在屏幕上，可`SCNView`通过将其分配给视图的场景属性，将其添加到中。 此外，如果对场景进行了任何更改， `SCNView`则会自行更新以显示所做的更改。

```csharp
scene = SCNScene.Create ();
sceneView = new SCNView (View.Frame);
sceneView.Scene = scene;
```

可以从通过3d 建模工具导出的文件或从几何基元以编程方式填充场景。 例如，以下是如何创建一个球体并将其添加到场景：

```csharp
sphere = SCNSphere.Create (10.0f);
sphereNode = SCNNode.FromGeometry (sphere);
sphereNode.Position = new SCNVector3 (0, 0, 0);
scene.RootNode.AddChildNode (sphereNode);
```

## <a name="adding-light"></a>添加光

此时，球不会显示任何内容，因为场景中没有光源。 将`SCNLight`实例附加到节点会在 SceneKit 中创建光线。 有多种类型的光源，涵盖各种形式的定向光照和环境照明。 例如，下面的代码在球一侧创建全向光源：

```csharp
// omnidirectional light
var light = SCNLight.Create ();
var lightNode = SCNNode.Create ();
light.LightType = SCNLightType.Omni;
light.Color = UIColor.Blue;
lightNode.Light = light;
lightNode.Position = new SCNVector3 (-40, 40, 60);
scene.RootNode.AddChildNode (lightNode);
```

全向照明产生了一种漫射反射，产生了偶光源，如照射闪光灯。 创建环境光源的方式与此类似，但它没有方向，因为它在所有方向上都相同。 将其视为心情光源:)

```csharp
// ambient light
ambientLight = SCNLight.Create ();
ambientLightNode = SCNNode.Create ();
ambientLight.LightType = SCNLightType.Ambient;
ambientLight.Color = UIColor.Purple;
ambientLightNode.Light = ambientLight;
scene.RootNode.AddChildNode (ambientLightNode);
```

灯就地亮起后，球现在就会显示在场景中。

![](scenekit-images/image8.png "球在亮起时显示在场景中")

## <a name="adding-a-camera"></a>添加照相机

将相机（SCNCamera）添加到场景会更改视图的点。 用于添加照相机的模式类似。 创建照相机，将其附加到节点，然后将节点添加到场景中。

```csharp
// camera
camera = new SCNCamera {
    XFov = 80,
    YFov = 80
};
cameraNode = new SCNNode {
    Camera = camera,
    Position = new SCNVector3 (0, 0, 40)
};
scene.RootNode.AddChildNode (cameraNode);
```

正如上面的代码所示，可以使用构造函数或从 Create factory 方法创建 SceneKit 对象。 前者允许使用C#初始值设定项语法，但要使用哪种语法很重要。

照相机就绪后，用户可以看到整个球：

![](scenekit-images/image9.png "整个球体对用户可见")

还可以将其他灯光添加到场景中。 下面是几个全向光源的外观：

![](scenekit-images/image10.png "带有几个全向光源的球")

此外，通过设置`sceneView.AllowsCameraControl = true`，用户可以使用触摸手势更改视图的点。

### <a name="materials"></a>资料

材料是通过 SCNMaterial 类创建的。 例如，若要将图像添加到球体的图面上，请将图像设置为材料的*漫射*内容。

```csharp
material = SCNMaterial.Create ();
material.Diffuse.Contents = UIImage.FromFile ("monkey.png");
sphere.Materials = new SCNMaterial[] { material };
```

这会将图像分层到节点上，如下所示：

![](scenekit-images/image11.png "将图像分层到球体上")

还可以将材料设置为响应其他类型的照明。 例如，对象可以变为发亮，并将其反射内容设置为显示反射反射，从而导致表面上出现一个明亮的点，如下所示：

![](scenekit-images/image12.png "对象通过反射反射变为发亮，导致表面上出现亮点")

材料非常灵活，只需很少的代码即可实现。 例如，不是将图像设置为漫射内容，而是将其设置为反射内容。

```csharp
material.Reflective.Contents = UIImage.FromFile ("monkey.png");
```

现在，猴子看起来像是在球内，与观点无关。

### <a name="animation"></a>动画

SceneKit 适用于动画。 你可以创建隐式或显式动画，甚至可以从核心动画层树呈现场景。 创建隐式动画时，SceneKit 提供了其自己的转换`SCNTransaction`类。

下面是旋转球的示例：

```csharp
SCNTransaction.Begin ();
SCNTransaction.AnimationDuration = 2.0;
sphereNode.Rotation = new SCNVector4 (0, 1, 0, (float)Math.PI * 4);
SCNTransaction.Commit ();
```

不过，您可以像旋转一样进行动画处理。 SceneKit 的许多属性都是动画处理。 例如，下面的代码对材料的`Shininess`进行了动画处理，以提高反射反射。

```csharp
SCNTransaction.Begin ();
SCNTransaction.AnimationDuration = 2.0;
material.Shininess = 0.1f;
SCNTransaction.Commit ();
```

SceneKit 非常简单。 它提供了大量附加功能，包括约束、物理、声明性操作、3D 文本、现场支持深度、Sprite 工具包集成和核心映像集成。
