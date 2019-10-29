---
title: 使用 UrhoSharp 构建三维游戏
description: 本文档提供 UrhoSharp 的概述，介绍场景、组件、形状、照相机、操作、用户输入、声音等。
ms.prod: xamarin
ms.assetid: D9BEAD83-1D9E-41C3-AD4B-3D87E13674A0
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: cb4e524977b53f1a17552298c509d43ccf460f08
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73011654"
---
# <a name="using-urhosharp-to-build-a-3d-game"></a>使用 UrhoSharp 构建三维游戏

编写您的第一个游戏之前，您需要了解熟悉的基础知识：如何设置场景，如何加载资源（这包含您的图稿）以及如何为您的游戏创建简单交互。

<a name="scenenodescomponentsandcameras"/>

## <a name="scenes-nodes-components-and-cameras"></a>场景、节点、组件和照相机

场景模型可以描述为基于组件的场景图。 场景包含场景节点的层次结构，从根节点开始，后者也表示整个场景。 每个 `Node` 都有一个三维转换（位置、旋转和缩放）、名称、ID 和任意数量的组件。  组件使某个节点成为生存期，它们可以添加一个直观的表示形式（`StaticModel`），可以发出声音（`SoundSource`），它们可以提供冲突边界等等。

您可以使用[Urho 编辑器](#urhoeditor)创建场景和安装节点，也可以从C#代码中执行操作。  在本文档中，我们将探讨如何使用代码进行设置，因为它们阐释了在屏幕上显示内容所需的元素

除了设置场景外，还需要设置 `Camera`，这决定了将向用户显示的内容。

### <a name="setting-up-your-scene"></a>设置场景

你通常会创建以下形式的启动方法：

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

通过调用 `CreateComponent<T>()`，可以通过在节点中创建不同的组件，来呈现3D 对象、声音播放、物理学和脚本化逻辑更新。  例如，设置节点和光源组件，如下所示：

```csharp
// Create a directional light to the world so that we can see something. The
// light scene node's orientation controls the light direction; we will use
// the SetDirection() function which calculates the orientation from a forward
// direction vector.
// The light will use default settings (white light, no shadows)
var lightNode = scene.CreateChild("DirectionalLight");
lightNode.SetDirection (new Vector3(0.6f, -1.0f, 0.8f));
```

我们创建了一个名为 "`DirectionalLight`" 的节点，并为其设置了一个方向，但没有其他任何内容。  现在，可以通过向 `CreateComponent`中附加 `Light` 组件，将上述节点转换为一个发光节点：

```csharp
var light = lightNode.CreateComponent<Light>();
```

在 `Scene` 中创建的组件具有特殊的角色：用于实现场景范围内的功能。 它们应在所有其他组件之前创建，并包括以下各项：

 `Octree`：实现空间分区和加速可见性查询。 不能呈现此三维对象。
 `PhysicsWorld`：实现物理学模拟。 如果没有此功能，物理组件（如 `RigidBody` 或 `CollisionShape`）可能无法正常运行。
 `DebugRenderer`：实现调试几何呈现。

`Light`、`Camera` 或 `StaticModel` 等普通组件
不应直接在 `Scene`中创建，而应创建为子节点。

该库附带了各种组件，您可以将这些组件附加到您的节点以使它们能够使用：用户可见的元素（模型）、声音、刚性体、冲突形状、照相机、光源、粒子发射器等。

### <a name="shapes"></a>形状

为方便起见，可将各种形状作为 Urho 命名空间中的简单节点提供。  其中包括框、球、圆锥、圆柱和飞机。

### <a name="camera-and-viewport"></a>照相机和视区

与光一样，照相机是组件，因此需要将组件附加到节点，如下所示：

```csharp
var CameraNode = scene.CreateChild ("camera");
camera = CameraNode.CreateComponent<Camera>();
CameraNode.Position = new Vector3 (0, 5, 0);
```

这样一来，您已经创建了一个摄像机，并将该照相机置于3D 世界，下一步就是通知 `Application` 这是您要使用的摄像机，这是使用以下代码完成的：

```csharp
Renderer.SetViewPort (0, new Viewport (Context, scene, camera, null))
```

现在，您应该可以看到创建的结果。

### <a name="identification-and-scene-hierarchy"></a>标识和场景层次结构

与节点不同，组件没有名称;同一节点内的组件仅按其类型进行标识，在节点的 "组件" 列表中按创建顺序进行索引，例如，您可以按如下所示检索 `lightNode` 对象的 `Light` 组件：

```csharp
var myLight = lightNode.GetComponent<Light>();
```

你还可以获取所有组件的列表，方法是检索 `Components` 属性，该属性将返回可使用的 `IList<Component>`。

创建时，节点和组件均获取场景全局整数 Id。 可以通过使用 `GetNode(uint id)` 和 `GetComponent(uint id)`的函数从场景中查询它们。 这比执行基于名称的递归场景节点查询更快。

没有实体或游戏对象的内置概念;相反，程序员需要确定节点层次结构，而在哪个节点中放置任何脚本化的逻辑。 通常，会将三维世界中的自由移动对象创建为根节点的子对象。 使用 `CreateChild`，可以使用或不使用名称创建节点。 不强制使用节点名称的唯一性。

每次有层次结构组合时，建议（事实上，因为组件没有自己的3D 转换）来创建子节点。

例如，如果某个字符的手中包含一个对象，则该对象应具有其自己的节点，该节点将是该字符的手形骨骼（也是 `Node`）的父级。  物理 `CollisionShape`是物理的，它可以 offsetted，并与节点进行单独旋转。

请注意，在计算子节点的世界派生转换时，`Scene`的自己的转换被忽略为优化，因此更改它不起作用，应保持不变（位置不变，无旋转，无缩放）。

`Scene` 节点可以自由地重定父级。 相反，组件始终属于它们附加到的节点，无法在节点之间移动。 节点和组件都提供 `Remove` 函数来完成此工作，而无需通过父节点。 删除节点后，在调用该函数后，不会对所涉及的节点或组件进行任何操作。

还可以创建不属于场景的 `Node`。 这种情况很有用，例如，在可能加载或保存的场景中移动照相机时，照相机不会与实际场景一起保存，并且在加载场景时不会被销毁。 但是，请注意，将 geometry、物理学或 script 组件创建到未附加的节点，然后将其移动到场景中会导致这些组件无法正常工作。

### <a name="scene-updates"></a>场景更新

启用了更新的场景（默认值）将在每个主循环迭代中自动更新。  将对应用程序的 `SceneUpdate` 事件处理程序进行调用。

可以通过禁用场景更新来排除节点和组件，请参阅 `Enabled`。  行为取决于特定的组件，但例如，禁用可绘制组件还会使其不可见，同时禁用静音的声音源组件。 如果某个节点处于禁用状态，则它的所有组件都将被视为禁用，而不考虑它们各自的启用/禁用状态。

## <a name="adding-behavior-to-your-components"></a>向组件添加行为

构建游戏的最佳方式是构建自己的组件，将参与者或元素封装到游戏中。  这使得功能自行包含在用于显示其行为的资产中。

向组件添加行为的最简单方法是使用操作，这些操作是可以进行排队并将其与C#异步编程结合使用的说明。  这允许组件的行为非常高，并使其更易于理解发生的情况。

或者，您可以通过更新每个帧上的组件属性（在基于帧的行为部分中讨论）来精确控制您的组件所发生的情况。

### <a name="actions"></a>操作

可以很轻松地使用操作将行为添加到节点。  操作可以更改各种节点属性并在一段时间内执行这些属性，也可以使用给定的动画曲线多次重复执行操作。

例如，请考虑场景中的 "云" 节点，可以像下面这样淡化：

```csharp
await cloud.RunActionsAsync (new FadeOut (duration: 3))
```

操作是不可变对象，可用于对驱动不同对象重复使用该操作。

常见的用法是创建执行反向操作的操作：

```csharp
var gotoExit = new MoveTo (duration: 3, position: exitLocation);
var return = gotoExit.Reverse ();
```

下面的示例将在3秒的时间段内淡化对象。  你还可以在另一个操作之后运行一个操作，例如，你可以先移动云，然后隐藏它：

```csharp
await cloud.RunActionsAsync (
    new MoveBy  (duration: 1.5f, position: new Vector3(0, 0, 15),
    new FadeOut (duration: 3));
```

如果希望同时执行这两项操作，可以使用并行操作，并提供要并行执行的所有操作：

```csharp
  await cloud.RunActionsAsync (
    new Parallel (
      new MoveBy  (duration: 3, position: new Vector3(0, 0, 15),
      new FadeOut (duration: 3)));
```

在上面的示例中，云将同时移动和淡出。

你会注意到这些是使用C#`await`，这使你可以线性地考虑你要实现的行为。

### <a name="basic-actions"></a>基本操作

这些是 UrhoSharp 中支持的操作：

- 移动节点： `MoveTo`、`MoveBy`、`Place`、`BezierTo`、`BezierBy`、`JumpTo`、`JumpBy`
- 旋转节点： `RotateTo`、`RotateBy`
- 缩放节点： `ScaleTo`、`ScaleBy`
- 褪色节点： `FadeIn`、`FadeTo`、`FadeOut`、`Hide`、`Blink`
- 淡色： `TintTo`、`TintBy`
- 时刻： `Hide`、`Show`、`Place`、`RemoveSelf`、`ToggleVisibility`
- 循环： `Repeat`、`RepeatForever``ReverseTime`

其他高级功能包括 `Spawn` 和 `Sequence` 操作的组合。

### <a name="easing---controlling-the-speed-of-your-actions"></a>缓动-控制操作的速度

缓动是一种指示动画将展开的方式的方法，它可以让你的动画更加愉快。  默认情况下，你的操作将具有线性行为，例如，`MoveTo` 操作会产生非常的机器人运动。  你可以将操作打包为缓动操作，以更改行为，例如，一种会慢慢地开始移动、加速和缓慢到达结束（`EasyInOut`）的行为。

为此，可将现有操作包装到缓动操作中，例如：

```csharp
await cloud.RunActionAsync (
   new EaseInOut (
     new MoveTo (duration: 3, position: new Vector (0,0,15)), rate:1))
```

有很多缓动模式，下图显示了各种缓动类型及其在一段时间内（从开始到完成）所控制的对象的值的行为：

![缓动模式](using-images/easing.png "此图显示了各种缓动类型及其在一段时间内控制的对象的值的行为")

### <a name="using-actions-and-async-code"></a>使用操作和异步代码

在 `Component` 子类中，应引入一个异步方法，该方法可准备组件行为并为其提供功能。
然后，可以使用程序的其他部分C# （`Application.Start`方法或响应应用程序中的用户或情景点）中的`await`关键字调用此方法。

例如:

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

在上面的 `Launch` 方法中，启动了三个操作：机器人进入场景，此操作将在0.6 秒的时间段内改变节点的位置。  由于这是一个异步选项，因此，这将同时作为下一个指令调用 `MoveRandomly`。  此方法会将机器人的位置并行改变为随机位置。  这是通过执行两个复合操作来实现的，即，移动到一个新位置并返回到原始位置，并重复执行此操作，只要机器人保持活动状态。  为了使事情更有趣，自动机将继续进行诊断。  每隔0.1 秒开始拍摄一次。

### <a name="frame-based-behavior-programming"></a>基于帧的行为编程

如果只想按帧控制组件的行为，而不是使用操作，则需要重写 `Component` 子类的 `OnUpdate` 方法。  此方法每个帧调用一次，并且仅当您将 ReceiveSceneUpdates 属性设置为 true 时才会调用。

下面演示了如何创建 `Rotator` 组件，该组件随后附加到节点，这会使节点旋转：

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
            RotationSpeed.X  timeStep,
            RotationSpeed.Y  timeStep,
            RotationSpeed.Z  timeStep),
            TransformSpace.Local);
    }
}
```

这就是将此组件附加到节点的方法：

```csharp
Node boxNode = new Node();
var rotator = new Rotator() { RotationSpeed = rotationSpeed };
boxNode.AddComponent (rotator);
```

### <a name="combining-styles"></a>组合样式

您可以使用基于异步/操作的模型来对很大的行为进行编程，这非常适合于编程的暂时的样式，但您也可以对组件的行为进行微调，同时在每个帧上运行一些更新代码。

例如，在 SamplyGame 演示中，此方法在 `Enemy` 类中用于对基本行为使用操作进行编码，但它还通过使用 `Node.LookAt`设置节点的方向来确保组件指向用户：

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

可以采用 XML 格式加载和保存场景;请参阅函数 `LoadXml` 和 `SaveXML`。 加载场景后，将首先删除其中的所有现有内容（子节点和组件）。 将不保存标记为带有 `Temporary` 属性的临时节点和组件。 序列化程序处理所有内置组件和属性，但它并不是足够智能地处理在组件子类中定义的自定义属性和字段。 但它为此提供了两个虚拟方法：

 `OnSerialize`，你可以在其中注册用于序列化的自定义状态

 `OnDeserialized`，你可以在其中获取已保存的自定义状态。

通常，自定义组件如下所示：

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

### <a name="object-prefabs"></a>对象 prototyping

仅加载或保存整个场景对于需要动态创建新对象的游戏而言不够灵活。 另一方面，创建复杂对象并在代码中设置其属性也是一种单调乏味的。 出于此原因，还可以保存包含其子节点、组件和属性的场景节点。 稍后可以以组的形式方便地加载这些。  此类保存的对象通常称为 prefab。 有三种方法可以实现此操作：

- 在代码中通过调用节点上的 `Node.SaveXml`
- 在编辑器中，通过在 "层次结构" 窗口中选择节点，然后从 "文件" 菜单中选择 "将节点另存为"。
- 使用 `AssetImporter`中的 "node" 命令，该命令将保存场景节点层次结构和输入资产中包含的任何模型（例如 Collada 文件）

若要将已保存的节点实例化到场景中，请调用 `InstantiateXml`。 该节点将创建为场景的子节点，但可以在之后自由重定父级。 需要指定放置节点的位置和旋转。 下面的代码演示如何使用所需的位置和旋转，将 prefab `Ninja.xm` 实例化到场景：

```csharp
var prefabPath = Path.Combine (FileSystem.ProgramDir,"Data/Objects/Ninja.xml");
using (var file = new File(Context, prefabPath, FileMode.Read))
{
    scene.InstantiateXml(file, desiredPos, desiredRotation,
        CreateMode.Replicated);
}
```

## <a name="events"></a>事件

UrhoObjects 引发多个事件，这些事件在生成它们C#的各种类上呈现为事件。  除了基于的C#事件模型之外，还可以使用`SubscribeToXXX`方法，该方法将允许您订阅和保留以后可用于取消订阅的订阅令牌。  不同之处在于前者允许多个调用方订阅，而第二个调用方只允许使用一个，但允许使用更好 lambda 样式的方法，并允许轻松删除订阅。  它们是互斥的。

订阅事件时，必须提供一个方法，该方法采用带有相应事件参数的自变量。

例如，这是你订阅鼠标按钮按下事件的方式：

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

采用 lambda 样式：

```csharp
public void override Start ()
{
    UI.MouseButtonDown += args => {
        Console.WriteLine ("button pressed");
    };
}
```

有时，您需要停止接收事件的通知，在这种情况下，请从对 `SubscribeTo` 方法的调用中保存返回值，并对其调用取消订阅方法：

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

事件处理程序接收的参数是一个强类型事件参数类，它将特定于每个事件并包含事件负载。

## <a name="responding-to-user-input"></a>响应用户输入

您可以订阅各种事件，例如，通过订阅事件，并响应要传递的输入：

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

但在许多情况下，你希望场景更新处理程序在更新时检查密钥的当前状态，并相应地更新代码。  例如，可以使用以下内容根据键盘输入来更新相机位置：

```csharp
protected override void OnUpdate(float timeStep)
{
    Input input = Input;
    // Movement speed as world units per second
    const float moveSpeed = 4.0f;
    // Read WASD keys and move the camera scene node to the
    // corresponding direction if they are pressed
    if (input.GetKeyDown(Key.W))
        CameraNode.Translate(Vector3.UnitY  moveSpeed  timeStep, TransformSpace.Local);
    if (input.GetKeyDown(Key.S))
        CameraNode.Translate(new Vector3(0.0f, -1.0f, 0.0f)  moveSpeed  timeStep, TransformSpace.Local);
    if (input.GetKeyDown(Key.A))
        CameraNode.Translate(new Vector3(-1.0f, 0.0f, 0.0f)  moveSpeed  timeStep, TransformSpace.Local);
    if (input.GetKeyDown(Key.D))
        CameraNode.Translate(Vector3.UnitX  moveSpeed  timeStep, TransformSpace.Local);
}
```

## <a name="resources-assets"></a>资源（资产）

资源包括在初始化或运行时从大容量存储加载的 UrhoSharp 中的大部分内容：

- `Animation`-用于框架式动画
- `Image`-表示以多种图形格式存储的图像
- `Model`-三维模型
- 用于呈现模型的 `Material` 材料。
- `ParticleEffect`- [介绍](https://urho3d.github.io/documentation/1.4/_particles.html)了粒子发射器的工作原理，请参阅下面的 "[粒子](#particles)"。
- `Shader` 自定义着色器
- `Sound`-播放声音，请参阅下面的 "[声音](#sound)"。
- `Technique`-材料呈现技术
- `Texture2D`-2D 纹理
- `Texture3D`-3D 纹理
- `TextureCube` 多维数据集纹理
- `XmlFile`

它们由 `ResourceCache` 子系统管理和加载（作为 `Application.ResourceCache`提供）。

资源本身由其文件路径（相对于已注册的资源目录或包文件）进行标识。 默认情况下，引擎将 `Data` 和 `CoreData`中注册资源目录，否则包 `Data.pak` 并 `CoreData.pak` （如果存在）。

如果加载资源失败，则将记录错误并返回空引用。

下面的示例演示从资源缓存提取资源的典型方法。  在这种情况下，UI 元素的纹理使用 `Application` 类中的 `ResourceCache` 属性。

```csharp
healthBar.SetTexture(ResourceCache.GetTexture2D("Textures/HealthBarBorder.png"));
```

还可以手动创建资源并将其存储到资源缓存，就好像它们已从磁盘中加载一样。

可按资源类型设置内存预算：如果资源消耗的内存超过允许的内存量，则将从缓存中删除最旧的资源（如果不再使用）。 默认情况下，内存预算设置为 "无限制"。

### <a name="bringing-3d-models-and-images"></a>引入3D 模型和图像

Urho3D 尝试尽可能使用现有文件格式，并仅在绝对必要的情况下定义自定义文件格式（对于模型（mdl）和动画（ani））。 对于这些类型的资产，Urho 提供了[AssetImporter](https://urho3d.github.io/documentation/1.4/_tools.html) ，它可以使用多种流行的3d 格式，如 fbx、dae、3ds 和 obj 等。

还有一个用于 Blender [https://github.com/reattiva/Urho3D-Blender](https://github.com/reattiva/Urho3D-Blender)的便利外接程序，可将 Blender 资产导出为适用于 Urho3D 的格式。

### <a name="background-loading-of-resources"></a>资源的后台加载

通常，当使用 `ResourceCache`的 `Get` 方法之一请求资源时，这些资源会立即加载到主线程中，所有必需的步骤（从磁盘加载文件、分析数据、根据需要上传到 GPU）可能需要几毫秒的时间。因此，以帧速率为下降。

如果事先知道所需的资源，可以通过调用 `BackgroundLoadResource`，请求将其加载到后台线程中。 您可以使用 `SubscribeToResourceBackgroundLoaded` 方法订阅资源后台加载的事件。 它会告知加载是成功还是失败。 根据资源的不同，只能将加载过程的一部分移动到后台线程，例如完成 GPU 上载步骤始终需要在主线程中发生。 请注意，如果为排队等待后台加载的资源调用某个资源加载方法，主线程将停止，直到其加载完成。

异步场景加载功能 `LoadAsync` 和 `LoadAsyncXML` 在继续加载场景内容之前，可以选择后台加载资源。 通过指定 `LoadMode.ResourcesOnly`，它还可用于仅加载资源而无需修改场景。 这允许准备场景或对象 prefab 文件以实现快速实例化。

最后，可以通过设置 `ResourceCache`上的 "`FinishBackgroundResourcesMs`" 属性来配置完成后台加载资源上每个帧所用的最长时间（以毫秒为单位）。

<a name="sound"/>

## <a name="sound"></a>段

声音是玩游戏的重要组成部分，UrhoSharp 框架提供了一种在游戏中播放声音的方法。  通过附加 `SoundSource` 播放声音
组件到 `Node` 然后从资源播放指定的文件。

这就是它的执行方式：

```csharp
var explosionNode = Scene.CreateChild();
var sound = explosionNode.CreateComponent<SoundSource>();
soundSource.Play(Application.ResourceCache.GetSound("Sounds/ding.wav"));
soundSource.Gain = 0.5f;
soundSource.AutoRemove = true;
```

<a name="particles"/>

## <a name="particles"></a>粒子

粒子提供了一种简单的方法，可向应用程序添加一些简单、廉价的效果。  使用[http://onebyonedesign.com/flash/particleeditor/](http://onebyonedesign.com/flash/particleeditor/)之类的工具，可以使用 PEX 格式存储的粒子。

粒子是可以添加到节点中的组件。  需要调用节点的 `CreateComponent<ParticleEmitter2D>` 方法来创建粒子，然后通过将 "效果" 属性设置为从资源缓存加载的2D 效果来配置粒子。

例如，您可以对组件调用此方法，以显示某些粒子在达到时呈现为分解：

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

上面的代码将创建一个附加到当前组件的分解节点。在此分解节点中，我们将创建一个2D 粒子发射器，并通过设置效果属性来对其进行配置。  我们运行了两个操作，一个将节点缩放到较小的节点，另一个操作的大小为0.5 秒。  然后删除分解，这也会从屏幕中删除粒子效果。

使用球体纹理时，上述粒子如下所示：

![带有球体纹理的粒子](using-images/image-1.png "使用球体纹理时，上面的粒子呈现如下")

如果你使用 blocky 纹理，这就是：

![带有 box 纹理的粒子](using-images/image-2.png "这就是使用 blocky 纹理时的样子")

## <a name="multi-threading-support"></a>多线程支持

UrhoSharp 是一个线程库。  这意味着，不应尝试从后台线程调用 UrhoSharp 中的方法，也不应会损坏应用程序状态，并可能导致应用程序崩溃。

如果要在后台运行某些代码，然后在主 UI 上更新 Urho 组件，则可以使用 `Application.InvokeOnMain(Action)`
方法。  此外，还可以使用C# await 和 .Net 任务 api 来确保在适当的线程上执行代码。

## <a name="urhoeditor"></a>UrhoEditor

你可以从[Urho 网站](http://urho3d.github.io/)下载适用于你的平台的 Urho 编辑器，请参阅下载并选择最新版本。

## <a name="copyrights"></a>版权

此文档包含来自 Xamarin Inc. 的原始内容，但会广泛地从 Urho3D 项目的开源文档中进行绘制，并包含 Cocos2d 为后盾项目的屏幕截图。
