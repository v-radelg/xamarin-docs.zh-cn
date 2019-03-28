---
title: 使用 UrhoSharp 生成 3D 游戏
description: 本文档提供 UrhoSharp，的概述介绍场景、 组件、 形状、 照相机、 操作、 用户输入、 声音、 和的详细信息。
ms.prod: xamarin
ms.assetid: D9BEAD83-1D9E-41C3-AD4B-3D87E13674A0
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 5e5c4f1545d39befde6574338ec4c1ca4037ad8b
ms.sourcegitcommit: a7170494e1975f0f1be547a45444752fd8e57819
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2019
ms.locfileid: "58507157"
---
# <a name="using-urhosharp-to-build-a-3d-game"></a>使用 UrhoSharp 生成 3D 游戏

编写你的首个游戏之前，你想要掌握基础知识： 如何设置您的场景，如何加载的资源 （包含您的作品） 以及如何创建简单交互为您的游戏。

<a name="scenenodescomponentsandcameras"/>

## <a name="scenes-nodes-components-and-cameras"></a>场景、 节点、 组件和相机

场景模型可以描述为基于组件的场景图。 场景包含节点的层次结构场景，从根节点，还表示整个场景开始。 每个[ `Node` ](https://developer.xamarin.com/api/type/Urho.Node/)具有三维转换 （位置、 旋转和缩放）、 名称、 ID 以及任意数量的组件。  组件将节点变为现实，它们会使添加可视表示形式 ([`StaticModel`](https://developer.xamarin.com/api/type/Urho.StaticModel))，它们可以发出声音 ([`SoundSource`](https://developer.xamarin.com/api/type/Urho.Audio.SoundSource))，它们可以提供冲突边界，依此类推。

您可以创建您的场景和安装程序节点上使用[Urho 编辑器](#urhoeditor)，或可以在 C# 代码中执行操作。  本文档中我们将探讨设置各项使用的代码，因为它们说明了使内容显示在屏幕上所需的元素

除了设置您的场景，你需要设置[ `Camera` ](https://developer.xamarin.com/api/type/Urho.Camera/)，这是向用户将获取显示的内容由什么决定。

### <a name="setting-up-your-scene"></a>设置您的场景

通常要创建此窗体启动方法：

```csharp
var scene = new Scene ();
// Create the Octree component to the scene. This is required before
// adding any drawable components, or else nothing will show up. The
// default octree volume will be from -1000, -1000, -1000) to
//(1000, 1000, 1000) in world coordinates; it is also legal to place
// objects outside the volume but their visibility can then not be
// checked in a hierarchically optimizing manner
scene.CreateComponent<Octree> ();
// Create a child scene node (at world origin) and a StaticModel
// component into it. Set the StaticModel to show a simple plane mesh
// with a "stone" material. Note that naming the scene nodes is
// optional. Scale the scene node larger (100 x 100 world units)
var planeNode = scene.CreateChild("Plane");
planeNode.Scale = new Vector3 (100, 1, 100);
var planeObject = planeNode.CreateComponent<StaticModel> ();
planeObject.Model = ResourceCache.GetModel ("Models/Plane.mdl");
planeObject.SetMaterial(ResourceCache.GetMaterial("Materials/StoneTiled.xml"));
```

### <a name="components"></a>组件数

呈现三维对象，声音播放、 物理和脚本化的逻辑更新情况下全都启用为节点创建不同的组件，通过调用[ `CreateComponent<T>()` ](https://developer.xamarin.com/api/member/Urho.Node.CreateComponent%3CT%3E/p/Urho.CreateMode/System.UInt32/)。  例如，安装程序节点和浅组件如下：

```csharp
// Create a directional light to the world so that we can see something. The
// light scene node's orientation controls the light direction; we will use
// the SetDirection() function which calculates the orientation from a forward
// direction vector.
// The light will use default settings (white light, no shadows)
var lightNode = scene.CreateChild("DirectionalLight");
lightNode.SetDirection (new Vector3(0.6f, -1.0f, 0.8f));
```

我们已创建了具有该名称节点上方"`DirectionalLight`"并为它，但仅限于设置方向。  现在，我们可以将上面的节点转换 light 发出的节点，通过将附加[ `Light` ](https://developer.xamarin.com/api/type/Urho.Light/)组件，使用`CreateComponent`:

```csharp
var light = lightNode.CreateComponent<Light>();
```

创建到组件`Scene`本身具有特殊的角色： 若要实现场景级功能。 它们应在所有其他组件之前创建，并且包括以下各项：

* [`Octree`](https://developer.xamarin.com/api/type/Urho.Octree/)： 实现空间分区，并加速可见性查询。 而无需此 3D 不可以呈现的对象。
* [`PhysicsWorld`](https://developer.xamarin.com/api/type/Urho.Physics.PhysicsWorld/)： 实现物理模拟。 如物理组件[ `RigidBody` ](https://developer.xamarin.com/api/type/Urho.Physics.RigidBody/)或[ `CollisionShape` ](https://developer.xamarin.com/api/type/Urho.Physics.CollisionShape/)可以不正常而无需这的工作。
* [`DebugRenderer`](https://developer.xamarin.com/api/type/Urho.DebugRenderer/)： 实现调试 geometry 呈现。

等普通组件[ `Light` ](https://developer.xamarin.com/api/type/Urho.Light)， [ `Camera` ](https://developer.xamarin.com/api/type/Urho.Camera)或 [`StaticModel`](https://developer.xamarin.com/api/type/Urho.StaticModel)
不应直接创建[ `Scene` ](https://developer.xamarin.com/api/type/Urho.Scene)，但而不是为子节点。

库附带了各种可以附加到节点将转换为现实的组件： 用户可见的元素 （模型）、 声音、 刚体、 冲突形状、 照相机、 光源、 粒子发射器和其他更多。

### <a name="shapes"></a>形状

为方便起见，各种形状都可用作简单 Urho.Shapes 命名空间中的节点。  其中包括框、 球体、 圆锥、 圆柱形和平面。

### <a name="camera-and-viewport"></a>照相机和视区

就像光，相机是组件，因此需要将该组件附加到一个节点，这是如下所示：

```csharp
var CameraNode = scene.CreateChild ("camera");
camera = CameraNode.CreateComponent<Camera>();
CameraNode.Position = new Vector3 (0, 5, 0);
```

与此，创建相机、 并已放置在 3D 世界中的照相机下, 一步是告知`Application`，这你想要使用的照相机，这是使用以下代码：

```csharp
Renderer.SetViewPort (0, new Viewport (Context, scene, camera, null))
```

和现在，应能够看到创建的结果。

### <a name="identification-and-scene-hierarchy"></a>标识和场景的层次结构

与节点不同组件没有名称;在同一节点内的组件仅由其类型和在该节点的组件列表中，按创建顺序填充该索引，例如，可以检索[ `Light` ](https://developer.xamarin.com/api/type/Urho.Light)组件共`lightNode`对象更高版本如下：

```csharp
var myLight = lightNode.GetComponent<Light>();
```

此外可以通过检索来获取所有组件的列表[ `Components` ](https://developer.xamarin.com/api/property/Urho.Node.Components/)属性返回`IList<Component>`，可以使用。

在创建时，节点和组件将获取场景全局整数 Id。 它们可以从场景查询，通过使用函数[ `GetNode(uint id)` ](https://developer.xamarin.com/api/member/Urho.Scene.GetNode/p/System.UInt32/)并[ `GetComponent(uint id)` ](https://developer.xamarin.com/api/member/Urho.Scene.GetComponent/p/System.UInt32/)。 这是比例如执行递归名称基于场景的节点查询快得多。

不存在的实体或游戏对象; 内置概念而是由编程人员决定节点层次结构，并在哪些节点将任何已编写脚本的逻辑。 通常情况下，作为根节点的子级创建免费移动三维世界中的对象。 可以使用或不使用的名称，可以创建节点[ `CreateChild()` ](https://developer.xamarin.com/api/member/Urho.Node.CreateChild/p/System.String/Urho.CreateMode/System.UInt32/)。 不强制执行节点名称的唯一性。

只要某些层次结构的组合，它是建议 （并且确实有必要，因为组件不具有其自己的 3D 转换） 来创建一个子节点。

例如如果字符已持有手中的一个对象，该对象应具有其自己的节点，将父级设置为该字符的手的骨骼 (还[ `Node` ](https://developer.xamarin.com/api/type/Urho.Node/))。  例外情况是物理学[ `CollisionShape` ](https://developer.xamarin.com/api/type/Urho.Physics.CollisionShape)，可以是 offsetted 和旋转单独与节点相关。

请注意， [ `Scene`](https://developer.xamarin.com/api/type/Urho.Node/)的拥有转换有意忽略作为一种优化，计算派生的世界转换的子节点时，因此更改都无效，它应保留原样 （原点，没有旋转位置无需进行扩展。)

[`Scene`](https://developer.xamarin.com/api/type/Urho.Node/) 可以自由地重新设定节点。 与此相反组件始终属于它们附加到，并不能移动的节点之间的节点。 节点和组件提供[ `Remove()` ](https://developer.xamarin.com/api/member/Urho.Node.Remove()/)函数来实现此目的而无需经历父级。 删除节点后，在调用该函数后不是安全组件有问题的节点上的任何操作。

还有可能创建`Node`，不属于一个场景。 这是有用例如带相机的场景可能加载或保存，因为移动然后照相机将不会实际场景，以及保存和加载场景时不会被破坏。 但请注意，创建到未连接的节点，geometry、 物理或脚本组件，然后将其移到一个场景更高版本将导致这些组件无法正常工作。

### <a name="scene-updates"></a>场景更新

启用其更新场景 （默认值） 将自动更新每个主循环迭代上。  应用程序的[ `SceneUpdate` ](https://developer.xamarin.com/api/event/Urho.Scene.SceneUpdate/)在其上调用事件处理程序。

节点和组件可以通过禁用从场景更新中排除，请参阅[ `Enabled` ](https://developer.xamarin.com/api/member/Urho.Node.Enabled)。  行为取决于特定的组件，但如禁用可绘制组件也将使其不可见的而禁用声音源组件使静音。 如果禁用了节点，其所有组件将被视为为禁用，而不考虑其自己的启用/禁用状态。

## <a name="adding-behavior-to-your-components"></a>将行为添加到你的组件

构建您的游戏的最佳方法是使自己封装的执行组件或您的游戏的元素的组件。  这样，该功能自包含从用来显示它，其行为的资产。

将行为添加到组件的最简单的方法是使用操作，它们是可以进行排队并加上 C# 异步编程的说明。  这允许您为非常高的级别的组件的行为，并使其更容易理解发生了什么情况。

或者，可以控制完全会发生什么情况组件通过更新 （在基于框架的行为部分中讨论） 每个帧上您组件的属性。

### <a name="actions"></a>操作

可以将行为添加到节点非常轻松地使用操作。  操作可以更改各种节点属性和执行通过一段时间，或重复这些与给定的动画曲线次数。

例如，假设您的场景上的"云"节点，淡化如下：

```csharp
await cloud.RunActionsAsync (new FadeOut (duration: 3))
```

操作是不可变对象，以便您可以重复使用用于驱动不同的对象的操作。

一个常用方法是创建执行反向操作的操作：

```csharp
var gotoExit = new MoveTo (duration: 3, position: exitLocation);
var return = gotoExit.Reverse ();
```

三秒内，下面的示例将为您淡对象。  您还可以运行一个操作，例如，您无法先将移云，再然后将其隐藏：

```csharp
await cloud.RunActionsAsync (
    new MoveBy  (duration: 1.5f, position: new Vector3(0, 0, 15),
    new FadeOut (duration: 3));
```

如果你想要在同一时间发生这两个操作，可使用并行操作，并提供所有所需的操作同时进行的：

```csharp
  await cloud.RunActionsAsync (
    new Parallel (
      new MoveBy  (duration: 3, position: new Vector3(0, 0, 15),
      new FadeOut (duration: 3)));
```

在上面的示例在云中将移动，并同时淡出。

你会注意到，将使用这些 C# await，该编辑器可以以线性方式考虑你想要实现的行为。

### <a name="basic-actions"></a>基本操作

以下是在 UrhoSharp 中受支持的操作：

* 移动节点： [ `MoveTo` ](https://developer.xamarin.com/api/type/Urho.Actions.MoveTo)， [ `MoveBy` ](https://developer.xamarin.com/api/type/Urho.Actions.MoveBy)， [ `Place` ](https://developer.xamarin.com/api/type/Urho.Actions.Place)， [ `BezierTo` ](https://developer.xamarin.com/api/type/Urho.Actions.BezierTo)， [ `BezierBy` ](https://developer.xamarin.com/api/type/Urho.Actions.BezierBy), [`JumpTo`](https://developer.xamarin.com/api/type/Urho.Actions.JumpTo), [`JumpBy`](https://developer.xamarin.com/api/type/Urho.Actions.JumpBy)
* 旋转节点： [ `RotateTo` ](https://developer.xamarin.com/api/type/Urho.Actions.RotateTo)， [`RotateBy`](https://developer.xamarin.com/api/type/Urho.Actions.RotateBy)
* 缩放节点： [ `ScaleTo` ](https://developer.xamarin.com/api/type/Urho.Actions.ScaleTo)， [`ScaleBy`](https://developer.xamarin.com/api/type/Urho.Actions.ScaleBy)
* 淡出节点： [ `FadeIn` ](https://developer.xamarin.com/api/type/Urho.Actions.FadeIn)， [ `FadeTo` ](https://developer.xamarin.com/api/type/Urho.Actions.FadeTo)， [ `FadeOut` ](https://developer.xamarin.com/api/type/Urho.Actions.FadeOut)， [ `Hide` ](https://developer.xamarin.com/api/type/Urho.Actions.Hide)， [`Blink`](https://developer.xamarin.com/api/type/Urho.Actions.Blink)
* 色调： [ `TintTo` ](https://developer.xamarin.com/api/type/Urho.Actions.TintTo)， [`TintBy`](https://developer.xamarin.com/api/type/Urho.Actions.TintBy)
* Instants: [ `Hide` ](https://developer.xamarin.com/api/type/Urho.Actions.Hide)， [ `Show` ](https://developer.xamarin.com/api/type/Urho.Actions.Show)， [ `Place` ](https://developer.xamarin.com/api/type/Urho.Actions.Place)， [ `RemoveSelf` ](https://developer.xamarin.com/api/type/Urho.Actions.RemoveSelf)， [`ToggleVisibility`](https://developer.xamarin.com/api/type/Urho.Actions.ToggleVisibility)
* 循环： [ `Repeat` ](https://developer.xamarin.com/api/type/Urho.Actions.Repeat)， [ `RepeatForever` ](https://developer.xamarin.com/api/type/Urho.Actions.RepeatForever)， [`ReverseTime`](https://developer.xamarin.com/api/type/Urho.Actions.ReverseTime)

其他高级的功能包括的组合[ `Spawn` ](https://developer.xamarin.com/api/type/Urho.Actions.Spawn)并[ `Sequence` ](https://developer.xamarin.com/api/type/Urho.Actions.Sequence)操作。

### <a name="easing---controlling-the-speed-of-your-actions"></a>缓动-控制你的操作的速度

缓动是一种方法，将定向的方式，动画将展开，并且它可使动画起来更加得心应手。  默认情况下你的操作会有线性的行为，例如[ `MoveTo` ](https://developer.xamarin.com/api/type/Urho.Actions.MoveTo)操作者必须非常机器人的移动。  可以包装你的操作转换为一个缓动操作来改变此行为，例如，一个用于将缓慢启动移动、 加速并缓慢到达末尾 ([`EasyInOut`](https://developer.xamarin.com/api/type/Urho.Actions.EasyInOut))。

执行此操作通过包装现有的操作转换为一个缓动操作，例如：

```csharp
await cloud.RunActionAsync (
   new EaseInOut (
     new MoveTo (duration: 3, position: new Vector (0,0,15)), rate:1))
```

有许多缓动模式下, 图显示了各种缓动类型以及它们的行为在它们正在控制的时间段内从开始到结束的对象的值：

![缓动模式](using-images/easing.png "此图表显示了各种缓动类型以及它们的行为上它们正在控制的时间段内的对象的值")

### <a name="using-actions-and-async-code"></a>使用操作和异步代码

在你[ `Component` ](https://developer.xamarin.com/api/type/Urho.Component/)子类，应引入的异步方法，准备组件行为并为其驱动器的功能。
然后将调用此方法使用 C#`await`关键字的程序，另一部分中任一你`Application.Start`方法或为了响应你的应用程序中的用户或情景点。

例如：

```csharp
class Robot : Component {
    public bool IsAlive;
    async void Launch ()
    {
        // Dress up our robot
        var cache = Application.ResourceCache;
        var model = node.CreateComponent<StaticModel>();
        model.Model = cache.GetModel ("robot.mdl"));
        model.SetMaterial (cache.GetMaterial ("robot.xml"));
        Node.SetScale (1);

        // Bring the robot into our scene
        await Node.RunActionsAsync(
            new MoveBy(duration: 0.6f, position: new Vector3(0, -2, 0)));
        // Make him move around to avoid the user fire
        MoveRandomly(minX: 1, maxX: 2, minY: -3, maxY: 3, duration: 1.5f);
        // And simultaneously have him shoot at the user
        StartShooting();
    }

    protected async void MoveRandomly (float minX, float maxX,
                                       float minY, float maxY,
                       float duration)
    {
        while (IsAlive){
            var moveAction = new MoveBy(duration,
                new Vector3(RandomHelper.NextRandom(minX, maxX),
                            RandomHelper.NextRandom(minY, maxY), 0));
            await Node.RunActionsAsync(moveAction, moveAction.Reverse());
        }
    }
    protected async void StartShooting()
    {
        while (IsAlive && Node.Components.Count > 0){
            foreach (var weapon in Node.Components.OfType<Weapon>()){
                await weapon.FireAsync(false);
                if (!IsAlive)
                    return;
            }
            await Node.RunActionsAsync(new DelayTime(0.1f));
        }
    }
}
```

在`Launch`上述三个操作方法是否已启动： 机器人发送到场景中时，此操作会剩下 0.6 秒内更改节点的位置。  由于这是一个异步选项，这将是同时发生的下一个指令，这是调用作为到`MoveRandomly`。  此方法将更改以并行方式对随机位置机器人的位置。  这通过执行两个复合的操作，移动到新位置，并返回到原始位置并重复此操作，只要机器人保持活动状态。  并且，若要使事情更有趣，机器人将保留排除同时。  排除才会启动每个 0.1 秒。

### <a name="frame-based-behavior-programming"></a>基于框架的行为编程

如果你想要控制而不是使用操作按逐个框架组件的行为，就像是重写[ `OnUpdate` ](https://developer.xamarin.com/api/member/Urho.Component.OnUpdate)方法的你[ `Component` ](https://developer.xamarin.com/api/type/Urho.Component)子类。  此方法会调用一次每帧，并仅当 ReceiveSceneUpdates 属性设置为 true，则调用。

以下示例演示如何创建`Rotator`组件，然后附加到一个节点，这会导致要旋转的节点：

```csharp
class Rotator : Component {
    public Rotator()
    {
        ReceiveSceneUpdates = true;
    }
    public Vector3 RotationSpeed { get; set; }
    protected override void OnUpdate(float timeStep)
    {
        Node.Rotate(new Quaternion(
            RotationSpeed.X * timeStep,
            RotationSpeed.Y * timeStep,
            RotationSpeed.Z * timeStep),
            TransformSpace.Local);
    }
}
```

而这是到的节点，您就会附加此组件：

```csharp
Node boxNode = new Node();
var rotator = new Rotator() { RotationSpeed = rotationSpeed };
boxNode.AddComponent (rotator);
```

### <a name="combining-styles"></a>组合样式

可用于异步/操作基于模型编程很多非常适合的编程中，即发即弃样式的行为，但可以还精细调整组件的行为还在每个帧上运行一些更新代码。

例如，在 SamplyGame 演示使用这一是在`Enemy`类将编码的基本行为使用操作，但它还确保该组件通过设置的节点的方向朝向用户点`Node.LookAt`:

```csharp
    protected override void OnUpdate(SceneUpdateEventArgs args)
    {
        Node.LookAt(
            new Vector3(0, -3, 0),
            new Vector3(0, 1, -1),
            TransformSpace.World);
        base.OnUpdate(args);
    }
```

## <a name="loading-and-saving-scenes"></a>加载和保存场景

可以加载和保存为 XML 格式; 场景请参阅函数[ `LoadXml` ](https://developer.xamarin.com/api/member/Urho.Scene.LoadXml)并[ `SaveXML()` ](https://developer.xamarin.com/api/member/Urho.Scene.SaveXml)。 加载一个场景时，先删除它 （子节点和组件） 中的所有现有内容。 节点和标记为临时使用的组件`Temporary`属性将不会保存。 序列化程序处理的所有内置组件和属性，但它不是智能，它能够处理自定义属性和组件子类中定义的字段。 但是它提供用于这两个虚拟方法：

* [`OnSerialize`](https://developer.xamarin.com/api/member/Urho.Component.OnSerialize) 可以在其中您注册您自定义序列化状态

* [`OnDeserialized`](https://developer.xamarin.com/api/member/Urho.Component.OnDeserialize) 您可从中获取已保存的自定义状态。

通常情况下，自定义组件将如下所示：

```csharp
class MyComponent : Component {
    // Constructor needed for deserialization
    public MyComponent(IntPtr handle) : base(handle) { }
    public MyComponent() { }
    // user defined properties (managed state):
    public Quaternion MyRotation { get; set; }
    public string MyName { get; set; }

    public override void OnSerialize(IComponentSerializer ser)
    {
        // register our properties with their names as keys using nameof()
        ser.Serialize(nameof(MyRotation), MyRotation);
        ser.Serialize(nameof(MyName), MyName);
    }

    public override void OnDeserialize(IComponentDeserializer des)
    {
        MyRotation = des.Deserialize<Quaternion>(nameof(MyRotation));
        MyName = des.Deserialize<string>(nameof(MyName));
    }
    // called when the component is attached to some node
    public override void OnAttachedToNode()
    {
        var node = this.Node;
    }
}
```

### <a name="object-prefabs"></a>对象预设

只需加载或保存整个场景不具有足够的灵活性游戏的新对象需要动态创建。 但是，创建复杂的对象和在代码中设置其属性也将单调乏味。 出于此原因，还有可能要保存的场景节点及其子节点、 组件和属性包括。 作为一个组可以更高版本可以方便地加载这些功能。  此类已保存的对象通常称为预设。 有三种方法可以实现此操作：

- 在代码中通过调用[ `Node.SaveXml` ](https://developer.xamarin.com/api/member/Urho.Node.SaveXml)在节点上
- 在编辑器中，通过在层次结构窗口中选择节点，然后选择"另存为节点"从"文件"菜单。
- 使用中的"节点"命令`AssetImporter`，这将保存场景节点层次结构和所有模型中的输入资产 （例如都包含。 Collada 文件）

若要实例化到场景的已保存的节点，请调用[ `InstantiateXml()` ](https://developer.xamarin.com/api/member/Urho.Scene.InstantiateXml)。 节点将创建为场景的子级，但此后可以自由地父级。 位置和旋转放置节点需要指定。 下面的代码演示如何实例化预设`Ninja.xm`到场景中使用所需的位置和旋转：

```csharp
var prefabPath = Path.Combine (FileSystem.ProgramDir,"Data/Objects/Ninja.xml");
using (var file = new File(Context, prefabPath, FileMode.Read))
{
    scene.InstantiateXml(file, desiredPos, desiredRotation,
        CreateMode.Replicated);
}
```

## <a name="events"></a>事件

UrhoObjects 引发的事件数，这些生成它们的各种类显示为 C# 事件。  除了 C#-基于的事件模型中，还有可能要使用`SubscribeToXXX`方法，您可以订阅并将其保存订阅标记更高版本可用于取消订阅。  区别在于，前者将允许多个调用方可以订阅，而第二个仅允许一个，但可实现更好的 lambda 式方法要使用并尚，允许订阅的方式方便地删除。  它们是互斥的。

当你订阅事件时，必须提供采用相应的事件参数的自变量的方法。

例如，这是你订阅了鼠标按钮按下事件的方式：

```csharp
public void override Start ()
{
    UI.MouseButtonDown += HandleMouseButtonDown;
}

void HandleMouseButtonDown(MouseButtonDownEventArgs args)
{
    Console.WriteLine ("button pressed");
}
```

使用 lambda 样式：

```csharp
public void override Start ()
{
    UI.MouseButtonDown += args => {
        Console.WriteLine ("button pressed");
    };
}
```

有时会想要停止接收通知的事件，在这些情况下，将保存返回值调用`SubscribeTo`方法，并调用其上的取消订阅方法：

```csharp
Subscription mouseSub;

public void override Start ()
{
    mouseSub = UI.SubscribeToMouseButtonDown (args => {
    Console.WriteLine ("button pressed");
      mouseSub.Unsubscribe ();
    };
}
```

事件处理程序接收的参数是一个将特定于每个事件，并包含事件负载的强类型化的事件参数类。

## <a name="responding-to-user-input"></a>响应用户输入

您可以订阅各种事件，如键击下通过订阅事件，并对传递的输入的响应：

```csharp
Start ()
{
    UI.KeyDown += HandleKeyDown;
}

void HandleKeyDown (KeyDownEventArgs arg)
{
     switch (arg.Key){
     case Key.Esc: Engine.Exit (); return;
}
```

但在许多情况下，您希望场景更新处理程序时它们正在更新，并相应地更新你的代码检查的键的当前状态。  例如，以下可用于更新基于键盘输入的照相机位置：

```csharp
protected override void OnUpdate(float timeStep)
{
    Input input = Input;
    // Movement speed as world units per second
    const float moveSpeed = 4.0f;
    // Read WASD keys and move the camera scene node to the
    // corresponding direction if they are pressed
    if (input.GetKeyDown(Key.W))
        CameraNode.Translate(Vector3.UnitY * moveSpeed * timeStep, TransformSpace.Local);
    if (input.GetKeyDown(Key.S))
        CameraNode.Translate(new Vector3(0.0f, -1.0f, 0.0f) * moveSpeed * timeStep, TransformSpace.Local);
    if (input.GetKeyDown(Key.A))
        CameraNode.Translate(new Vector3(-1.0f, 0.0f, 0.0f) * moveSpeed * timeStep, TransformSpace.Local);
    if (input.GetKeyDown(Key.D))
        CameraNode.Translate(Vector3.UnitX * moveSpeed * timeStep, TransformSpace.Local);
}
```

## <a name="resources-assets"></a>资源 （资产）

资源包括 UrhoSharp 过程初始化或运行时从大容量存储中加载的大多数问题：

- [`Animation`](https://developer.xamarin.com/api/type/Urho.Animation/) -用于主干动画
- [`Image`](https://developer.xamarin.com/api/type/Urho.Resources.Image) -表示不同的图形格式存储的图像
- [`Model`](https://developer.xamarin.com/api/type/Urho.Model/) -三维模型
- [`Material`](https://developer.xamarin.com/api/type/Urho.Material) -用于呈现模型的材料。
- [`ParticleEffect`](https://developer.xamarin.com/api/type/Urho.ParticleEffect)- [介绍](http://urho3d.github.io/documentation/1.4/_particles.html)粒子发射器的工作原理，请参阅"[粒子](#particles)"下面。
- [`Shader`](https://developer.xamarin.com/api/type/Urho.Shader) -自定义着色器
- [`Sound`](https://developer.xamarin.com/api/type/Urho.Audio.Sound) -声音播放，请参阅"[声音](#sound)"下面。
- [`Technique`](https://developer.xamarin.com/api/type/Urho.Technique/) -材料呈现技术
- [`Texture2D`](https://developer.xamarin.com/api/type/Urho.Urho2D.Texture2D/) -2D 纹理
- [`Texture3D`](https://developer.xamarin.com/api/type/Urho.Texture3D/) -3D 纹理
- [`TextureCube`](https://developer.xamarin.com/api/type/Urho.TextureCube/) -多维数据集纹理
- `XmlFile`

托管和加载[ `ResourceCache` ](https://developer.xamarin.com/api/type/Urho.Resources.ResourceCache/)子系统 (可用作[ `Application.ResourceCache` ](https://developer.xamarin.com/api/property/Urho.Application.ResourceCache/))。

资源本身进行标识其文件路径，相对于已注册的资源目录或包文件。 默认情况下，该引擎注册的资源目录`Data`并`CoreData`，或包`Data.pak`和`CoreData.pak`如果它们存在。

如果加载资源失败，将记录错误并返回空引用。

下面的示例演示从资源缓存提取资源的典型方式。  在这种情况下，用户界面元素的纹理，这将使用`ResourceCache`属性从`Application`类。

```csharp
healthBar.SetTexture(ResourceCache.GetTexture2D("Textures/HealthBarBorder.png"));
```

可手动创建和存储到资源缓存，因为如果返回了从磁盘中加载资源。

内存预算可以设置每种资源类型： 如果资源占用较多的内存超过了允许，最旧的资源将会删除缓存中如果不使用了。 默认情况下内存预算设置为不受限制。

### <a name="bringing-3d-models-and-images"></a>将三维模型和映像

Urho3D 尝试使用现有的文件格式，只要有可能，并定义自定义文件格式，如模型绝对必要时仅 (*.mdl) 和动画 (*.ani)。 对于这些类型的资产，Urho 提供了一个转换器- [AssetImporter](http://urho3d.github.io/documentation/1.4/_tools.html)这可以使用许多流行的 3D 格式，例如 fbx、 dae、 3ds，和 obj，等等。

此外，还有一个便捷外接程序的 Blender [ https://github.com/reattiva/Urho3D-Blender ](https://github.com/reattiva/Urho3D-Blender) ，可以导出格式适用于 Urho3D Blender 资产。

### <a name="background-loading-of-resources"></a>资源的后台加载

通常情况下，请求使用的一个资源时`ResourceCache`的`Get`方法，它们是在主线程，这可能会花几秒钟时间进行所有必要的步骤中立即加载 （从磁盘加载文件、 分析数据，如有必要将上载到 GPU) 并因此可能会导致帧速率下降。

如果您知道提前所需的资源，可以将它们加载在后台线程通过调用请求`BackgroundLoadResource()`。 您可以通过使用订阅资源后台加载事件`SubscribeToResourceBackgroundLoaded`方法。 它将告诉加载实际是否成功或失败。 根据资源，仅加载过程的一部分可能会移到后台线程，例如分页装订的 GPU 上传步骤始终需要在主线程中发生。 请注意，是否调用资源加载的资源将排队，等待后台加载的方法之一时，主线程将挂起之前其加载已完成。

异步加载功能的场景`LoadAsync()`和`LoadAsyncXML()`还首先继续场景内容加载的资源提供了背景负载选项。 此外可用于仅加载的资源而无需修改场景中，通过指定`LoadMode.ResourcesOnly`。 这样就可以准备快速实例化的场景或对象的预设文件。

最后上花费的最大时间 （以毫秒为单位） 每个帧完成加载的资源可以通过设置配置的背景`FinishBackgroundResourcesMs`属性上的`ResourceCache`。

<a name="sound"/>

## <a name="sound"></a>声音

声音是玩游戏的一个重要部分和 UrhoSharp framework 提供了一种方法在游戏中播放声音。  通过将附加播放声音 [`SoundSource`](https://developer.xamarin.com/api/type/Urho.Audio.SoundSource/)
组件[ `Node` ](https://developer.xamarin.com/api/type/Urho.Node) ，然后播放从你的资源的命名的文件。

这是如何执行操作：

```csharp
var explosionNode = Scene.CreateChild();
var sound = explosionNode.CreateComponent<SoundSource>();
soundSource.Play(Application.ResourceCache.GetSound("Sounds/ding.wav"));
soundSource.Gain = 0.5f;
soundSource.AutoRemove = true;
```

<a name="particles"/>

## <a name="particles"></a>粒子

粒子提供一种将一些简单且成本低廉的效果添加到你的应用程序的简单方法。  可以使用存储在 PEX 格式中的粒子使用之类的工具[ http://onebyonedesign.com/flash/particleeditor/ ](http://onebyonedesign.com/flash/particleeditor/)。

粒子是组件，可以添加到节点。  您需要调用节点的`CreateComponent<ParticleEmitter2D>`从资源缓存加载方法以创建粒子，然后通过将影响属性设置为 2D 效果配置粒子。

例如，你可以让组件能够显示抵达时呈现爆炸式增长为某些粒子上调用此方法：

```csharp
public async void Explode (Component target)
{
    // show a small explosion when the missile reaches an aircraft.
    var cache = Application.ResourceCache;
    var explosionNode = Scene.CreateChild();
    explosionNode.Position = target.Node.WorldPosition;
    explosionNode.SetScale(1f);
    var particle = explosionNode.CreateComponent<ParticleEmitter2D>();
    particle.Effect = cache.GetParticleEffect2D("explosion.pex");
    var scaleAction = new ScaleTo(0.5f, 0f);
    await explosionNode.RunActionsAsync(
        scaleAction, new DelayTime(0.5f));
    explosionNode.Remove();
}
```

上面的代码将创建附加到当前组件的爆炸式增长节点，在此爆炸式增长节点内，我们创建 2D 粒子发射器并设置效果属性来配置它。  我们运行两个操作、 一个缩放为更小的节点，另一个 0.5 秒将它留在此大小。  然后我们会删除爆炸式增长，同时从屏幕中删除粒子效果。

上述粒子如下所示使用球体纹理时：

![用球体纹理粒子](using-images/image-1.png "使用球体纹理时，上述粒子呈现此类")

而这是它看起来如果使用块状纹理：

![与框纹理粒子](using-images/image-2.png "，这是它看起来如果使用块状纹理")

## <a name="multithreading-support"></a>多线程支持

UrhoSharp 是一个线程的库。  这意味着您不应尝试调用方法中 UrhoSharp 从后台线程，或风险会损坏应用程序状态，并使你的应用程序可能崩溃。

如果你想要在后台运行一些代码，然后更新 Urho 组件上的主要用户界面，则可以使用 [`Application.InvokeOnMain(Action)`](https://developer.xamarin.com/api/member/Urho.Application.InvokeOnMain)
方法。  此外，你可以的使用 C# await 和.NET 任务 Api，以确保在适当的线程上执行的代码。

## <a name="urhoeditor"></a>UrhoEditor

您可以从你的平台下载 Urho 编辑器[Urho 网站](http://urho3d.github.io/)，转到下载，然后选择最新版本。

## <a name="copyrights"></a>版权

本文档包含从 Xamarin Inc 的原始内容，但从 Urho3D 项目的开放源代码文档广泛地绘制，包含 Cocos2D 项目中的屏幕快照。
