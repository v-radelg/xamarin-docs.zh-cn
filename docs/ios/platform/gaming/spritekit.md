---
title: 在 Xamarin.iOS SpriteKit
description: 本文档介绍 SpriteKit，Apple 的 2D 图形框架与 SceneKit 集成，其中包含的物理和动画，包括支持照明和明暗度，以及的详细信息。 可以使用 SpriteKit 创建 2D 游戏。
ms.prod: xamarin
ms.assetid: 93971DAE-ED6B-48A8-8E61-15C0C79786BB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/14/2017
ms.openlocfilehash: ef1e9a98b76166f4ee5638d1ab9762896d1e3bc8
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61293859"
---
# <a name="spritekit-in-xamarinios"></a>在 Xamarin.iOS SpriteKit

SpriteKit，apple 的 2D 图形 framework 具有 iOS 8 和 OS X Yosemite 中一些有趣的新功能。 其中包括与 SceneKit、 着色器的支持、 照明、 阴影、 约束、 法线贴图生成和物理引擎增强功能的集成。 具体而言，新的物理功能使其很容易将逼真的效果添加到游戏。

## <a name="physics-bodies"></a>物理学正文

SpriteKit 包括 2D，刚体物理引擎 API。 每个 sprite 具有关联的物理正文 (`SKPhysicsBody`)，在物理世界中定义的物理属性 （例如大容量和阻力，） 和正文的几何图形。

## <a name="creating-a-physics-body-from-a-texture"></a>从纹理创建物理正文
SpriteKit 现在支持从其纹理派生的子画面物理学正文。 这样，可以轻松实现看上去更自然的冲突。

例如，请注意，在以下冲突几乎在每个图像表面 banana 和 monkey 的相冲突：
 
![](spritekit-images/image13.png "几乎在每个图像表面 banana 和 monkey 发生冲突")

SpriteKit 使创建物理正文使用一行代码成为可能。 只需调用`SKPhysicsBody.Create`纹理，大小： 子画面。PhysicsBody = SKPhysicsBody.Create （子画面。纹理，子画面。大小）;

## <a name="alpha-threshold"></a>Alpha 阈值

除了只需设置`PhysicsBody`直接向派生自纹理的几何图形的属性，则可以设置应用程序和 alpha 阈值来控制如何派生几何图形。 

Alpha 阈值定义像素必须具有要包含在生成的物理学正文中的最小 alpha 值。 例如，下面的代码会略有不同的物理正文中：

```chsarp
sprite.PhysicsBody = SKPhysicsBody.Create (sprite.Texture, 0.7f, sprite.Size);
```

调整此类的 alpha 阈值的效果调整上一冲突，以便 monkey 跌落香蕉与其碰撞时：

![](spritekit-images/image14.png "Monkey 跌落香蕉与其碰撞时")
 
## <a name="physics-fields"></a>物理字段

另一个很好的补充 SpriteKit 是新的物理字段支持。 这些插件，可以添加顶点字段等径向重力字段和 spring 字段，等等。

使用 SKFieldNode 类，它添加到场景中就像任何其他一样创建物理字段`SKNode`。 上有各种工厂方法`SKFieldNode`创建不同的物理字段。 您可以通过调用创建 spring 字段`SKFieldNode.CreateSpringField()`，通过调用一个径向重力字段`SKFieldNode.CreateRadialGravityField()`，依次类推。

`SKFieldNode` 此外具有属性来控制字段属性，例如字段强度、 字段区域和字段强制的衰减。

## <a name="spring-field"></a>Spring 字段

例如，以下代码创建 spring 字段，并将其添加到场景：

```csharp
SKFieldNode fieldNode = SKFieldNode.CreateSpringField ();
fieldNode.Enabled = true;
fieldNode.Position = new PointF (Size.Width / 2, Size.Height / 2);
fieldNode.Strength = 0.5f;
fieldNode.Region = new SKRegion(Frame.Size);
AddChild (fieldNode);
```

然后可以添加 sprite，并设置其`PhysicsBody`属性，使物理字段将影响子画面，如以下代码在用户触摸屏幕：

```csharp
public override void TouchesBegan (NSSet touches, UIEvent evt)
{
    var touch = touches.AnyObject as UITouch;
    var pt = touch.LocationInNode (this);
    var node = SKSpriteNode.FromImageNamed ("TinyBanana");
    node.PhysicsBody = SKPhysicsBody.Create (node.Texture, node.Size);
    node.PhysicsBody.AffectedByGravity = false;
    node.PhysicsBody.AllowsRotation = true;
    node.PhysicsBody.Mass = 0.03f;
    node.Position = pt;
    AddChild (node);
}
```

这将导致香蕉振动等 spring 围绕字段节点：

![](spritekit-images/image15.png "香蕉振动等字段节点周围 spring")
 
## <a name="radial-gravity-field"></a>径向重力字段

添加不同的字段是类似的。 例如，以下代码创建径向重力字段：

```csharp
SKFieldNode fieldNode = SKFieldNode.CreateRadialGravityField ();
fieldNode.Enabled = true;
fieldNode.Position = new PointF (Size.Width / 2, Size.Height / 2);
fieldNode.Strength = 10.0f;
fieldNode.Falloff = 1.0f;
```

这会导致不同的强制字段，其中香蕉放射拉取有关字段：

![](spritekit-images/image16.png "该字段周围放射拉取香蕉")
