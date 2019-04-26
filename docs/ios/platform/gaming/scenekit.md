---
title: 在 Xamarin.iOS SceneKit
description: 本文档介绍 SceneKit，3D 场景图形 API，用于简化 3D 图形通过剥离 OpenGL 的复杂性。
ms.prod: xamarin
ms.assetid: 19049ED5-B68E-4A0E-9D57-B7FAE3BB8987
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/14/2017
ms.openlocfilehash: 0944978b34c8e164acd6e829db177bf4fd72dea9
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61376244"
---
# <a name="scenekit-in-xamarinios"></a>在 Xamarin.iOS SceneKit

SceneKit 是一个三维场景图形 API，用于简化 3D 图形。 它在 OS X 10.8 中首次引入，并具有现在转到 iOS 8。 使用 SceneKit 创建沉浸式 3D 可视化效果和休闲 3D 游戏不需要 OpenGL 的专业知识。 基于常见场景图形的概念，SceneKit 抽象化 OpenGL 和 OpenGL ES，需要很轻松地添加 3D 内容到应用程序的复杂性。 但是，如果你是 OpenGL 方面的专家，SceneKit 具有关联直接使用 OpenGL 以及强大的支持。 它还包括许多功能进行补充的 3D 图形，例如物理引擎，并与几个其他 Apple 框架，例如核心动画、 Core 映像和 Sprite Kit 很好地集成。

SceneKit 是非常易于使用。 它是负责呈现一个声明性 API。 只需设置一个场景，场景的呈现将属性添加到它，并 SceneKit 句柄。

若要使用 SceneKit 创建场景图形使用`SCNScene`类。 场景包含节点，由的实例表示的层次结构`SCNNode`，在 3D 空间中定义的位置。 下图所示，每个节点具有属性，如几何、 照明和影响其外观的材料：

![](scenekit-images/image7.png "SceneKit 层次结构") 

## <a name="create-a-scene"></a>创建一个场景

若要使屏幕上显示一个场景，您将其添加到`SCNView`将其分配给该视图的场景属性。 此外，如果对场景进行任何更改`SCNView`更新本身以显示所做的更改。

```csharp
scene = SCNScene.Create ();
sceneView = new SCNView (View.Frame);
sceneView.Scene = scene;
```

从三维建模工具，通过导出的文件或以编程方式从几何基元，可以填充的场景。 例如，下面介绍如何创建球体，并将其添加到场景：

```csharp
sphere = SCNSphere.Create (10.0f);
sphereNode = SCNNode.FromGeometry (sphere);
sphereNode.Position = new SCNVector3 (0, 0, 0);
scene.RootNode.AddChildNode (sphereNode);
```

## <a name="adding-light"></a>添加光线

此时球体不会显示任何内容因为场景中没有任何浅色。 附加`SCNLight`到节点的实例中 SceneKit 创建灯。 有几种类型的范围从各种形式的定向光照到环境照明的灯。 例如，下面的代码创建球体的一侧全向光：

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

全向照明会生成类似于用手电筒的导致甚至照明、 漫射反射。 创建环境光线非常相似，尽管它具有无方向，因为它的所有方向同样亮点。 将其想像的心情:) 照明

```csharp
// ambient light
ambientLight = SCNLight.Create ();
ambientLightNode = SCNNode.Create ();
ambientLight.LightType = SCNLightType.Ambient;
ambientLight.Color = UIColor.Purple;
ambientLightNode.Light = ambientLight;
scene.RootNode.AddChildNode (ambientLightNode);
```

使用就地灯，球体现已显示在场景。

![](scenekit-images/image8.png "球体中是可见的场景时照明")
 
## <a name="adding-a-camera"></a>添加照相机

向场景添加照相机 (SCNCamera) 更改的角度。 要添加照相机的模式是类似的。 创建照相机、 将其附加到一个节点并将节点添加到场景。

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

您可以看到从上面的代码，可以使用构造函数创建对象的 SceneKit 或 Create 工厂方法。 前者允许使用C#初始值设定项语法，但要使用哪一种很大程度的首选项。

使用就地照相机，整个球体是对用户可见：

![](scenekit-images/image9.png "整个球体是对用户可见")
 
向场景也可以添加其他灯。 下面是其外观与几个更多的全向光：

![](scenekit-images/image10.png "有几个更多的全向光源球体")
 
此外，通过设置`sceneView.AllowsCameraControl = true`，用户可以更改与触控笔势的角度。

### <a name="materials"></a>材料

使用 SCNMaterial 类创建的材料。 若要添加到球体的图面上的图像示例，将图像设置材料*漫射*内容。

```csharp
material = SCNMaterial.Create ();
material.Diffuse.Contents = UIImage.FromFile ("monkey.png");
sphere.Materials = new SCNMaterial[] { material };
```

此层到在节点上的映像，如下所示：

![](scenekit-images/image11.png "分层到球体上的映像")
 
材料可以设置为响应其他类型的太照明。 例如，该对象可有光泽并具有其反射内容设置为以显示反射高光反射，导致的亮点表面上看，如下所示：

![](scenekit-images/image12.png "进行发光的反射高光反射，从而导致的亮点图面上的对象")
 
材料也非常灵活，允许您将能够实现大量使用非常少的代码。 例如，而不是将图像设置为漫射内容，将其设置为反射内容改为。

```csharp
material.Reflective.Contents = UIImage.FromFile ("monkey.png");
```

现在看起来 monkey 内球体，独立于的角度直观地坐下来。

### <a name="animation"></a>动画

SceneKit 旨在很好地配合动画。 您可以创建隐式或显式动画，并从核心动画层树屏幕，甚至可以呈现。 SceneKit 创建隐式动画时, 提供其自己的转换类， `SCNTransaction`。

下面是旋转球体的示例：

```csharp
SCNTransaction.Begin ();
SCNTransaction.AnimationDuration = 2.0;
sphereNode.Rotation = new SCNVector4 (0, 1, 0, (float)Math.PI * 4);
SCNTransaction.Commit ();
```

您可以设置动画效果远远超出了旋转不过。 SceneKit 的许多属性都是可进行动画处理。 例如，下面的代码之间进行动画处理的材料`Shininess`增加反射高光反射。

```csharp
SCNTransaction.Begin ();
SCNTransaction.AnimationDuration = 2.0;
material.Shininess = 0.1f;
SCNTransaction.Commit ();
```

SceneKit 是使用非常简单。 它提供了大量的其他功能，包括约束、 物理特性、 声明性的操作、 三维文字、 字段支持、 Sprite Kit 的集成和核心映像集成来命名只需少量的深度。