---
title: MonoGame 游戏手柄引用
description: 本文档介绍了游戏板，用于访问 MonoGame 中的输入的设备的跨平台类。 它讨论了如何读取从游戏板输入，并提供示例代码。
ms.prod: xamarin
ms.assetid: 1F71F3E8-2397-4C6A-8163-6731ECFB7E03
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 235166b78dfbd4998086a2925a54137f1922f5d1
ms.sourcegitcommit: a7170494e1975f0f1be547a45444752fd8e57819
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2019
ms.locfileid: "58507001"
---
# <a name="monogame-gamepad-reference"></a>MonoGame 游戏手柄引用

_游戏板是用于访问 MonoGame 中的输入的设备的标准的跨平台的类。_

`GamePad` 可以用于读取输入中的输入设备上多个 MonoGame 平台。 本指南演示如何使用游戏板类。 由于每个输入的设备在布局和它提供的按钮数不同，本指南包含显示各种设备映射的关系图。

## <a name="gamepad-as-a-replacement-for-xbox360gamepad"></a>以替换 Xbox360GamePad 游戏手柄

提供的原始 XNA API`Xbox360GamePad`读取输入从 Xbox 360 或 PC 上的游戏控制器的类。 MonoGame 已取代这一点与`GamePad`类由于不能在大多数 MonoGame 平台 （如 iOS 或 Xbox One） 上使用 Xbox 360 控制器。 尽管名称更改的使用情况`GamePad`类是类似于`Xbox360GamePad`类。

## <a name="reading-input-from-gamepad"></a>读取输入从游戏手柄

`GamePad`类提供了标准化的方式读取输入任何 MonoGame 平台。 它通过两种方法提供的信息：

- `GetState` -返回控制器的按钮、 模拟记忆棒和方向键的当前状态。
- `GetCapabilities` -返回有关功能的信息的硬件，如控制器是否有某些按钮或支持振动。

### <a name="example-moving-a-character"></a>示例:移动一个字符

下面的代码演示如何使用左侧的滚动块记忆棒以将人物移动通过设置其`XVelocity`和`YVelocity`属性。 此代码假定`characterInstance`是某对象包含的实例`XVelocity`和`YVelocity`属性：

```csharp
// In Update, or some code called every frame:
var gamePadState = GamePad.GetState(PlayerIndex.One);
// Use gamePadState to move the character
characterInstance.XVelocity = gamePadState.ThumbSticks.Left.X * characterInstance.MaxSpeed;
characterInstance.YVelocity = gamePadState.ThumbSticks.Left.Y * characterInstance.MaxSpeed;
```

### <a name="example-detecting-pushes"></a>示例:检测推送

`GamePadState` 提供有关控制器，例如是否按下某个按钮的当前状态信息。 某些操作，例如让人物跳转，需要检查如果按钮已推送 （不是关闭最后一个帧，但已关闭此帧） 或释放 （为下最后一帧，但不是关闭此帧）。

若要执行此类型的逻辑，存储在前一个帧的局部变量`GamePadState`和当前帧的`GamePadState`必须创建。 下面的示例演示如何将存储并使用上一帧`GamePadState`实现一个在跳转：

```csharp
// At class scope:
// Store the last frame's and this frame's GamePadStates.
// "new" them so that code doesn't have to perform null checks:
GamePadState lastFrameGamePadState = new GamePadState();
GamePadState currentGamePadState = new GamePadState();
protected override void Update(GameTime gameTime)
{
    // store off the last state before reading the new one:
    lastFrameGamePadState = currentGamePadState;
    currentGamePadState = GamePad.GetState(PlayerIndex.One);
    bool wasAButtonPushed =
currentGamePadState.Buttons.A == ButtonState.Pressed
        && lastFrameGamePadState.Buttons.A == ButtonState.Released;
    if(wasAButtonPushed)
    {
        MakeCharacterJump();
    }
...
}
```

### <a name="example-checking-for-buttons"></a>示例:正在检查的按钮

`GetCapabilities` 可用于检查控制器是否有特定硬件，如特定按钮或模拟记忆棒。 下面的代码演示如何检查这需要两个按钮提供的游戏中的控制器上的 B 和 Y 按钮：

```csharp
var capabilities = GamePad.GetCapabilities(PlayerIndex.One);
bool hasBButton = capabilities.HasBButton;
bool hasXButton = capabilities.HasXButton;
if(!hasBButton || !hasXButton)
{
    NotifyUserOfMissingButtons();
}
```

## <a name="ios"></a>iOS

iOS 应用支持无线游戏控制器的输入。

> [!IMPORTANT]
> MonoGame 3.5 NuGet 包中未包含无线游戏控制器支持。 在 iOS 上使用游戏板类需要生成 MonoGame 3.5 源中或使用 MonoGame 3.6 NuGet 二进制文件。

### <a name="ios-game-controller"></a>iOS 游戏控制器

`GamePad`类返回从无线控制器中读取的属性。 中的属性`GamePad`提供好的覆盖率适用于标准 iOS 控制器硬件，如以下关系图中所示：

![](input-images/image1.png "游戏板中的属性提供好的覆盖率适用于标准 iOS 控制器硬件，此图中所示")

## <a name="apple-tv"></a>Apple 电视

Apple TV 游戏可以输入使用 Siri 远程或无线的游戏控制器。

### <a name="siri-remote"></a>Siri 远程

*Siri 远程*Apple TV 的是本机的输入的设备。 尽管可以通过事件读取 Siri 远程中的值 (如中所示[Siri 远程和蓝牙控制器指南](~/ios/tvos/platform/remote-bluetooth.md))，则`GamePad`类可以从使用 Siri 远程返回值。

请注意，`GamePad`只能读取输入从播放按钮和 touch 面：

![](input-images/image2.png "请注意，游戏板只能读取输入从播放按钮和 touch 面")

因为触摸屏输入图面上移动读完`DPad`属性，值将报告使用的移动`ButtonState`类。 换而言之，值为仅可用作`ButtonState.Pressed`或`ButtonState.Released`，而不是数字值或手势。

### <a name="apple-tv-game-controller"></a>Apple 电视游戏控制器

Apple TV 的游戏控制器到适用于 iOS 应用的游戏控制器具有相同行为。 有关详细信息，请参阅[iOS 游戏控制器](#ios-game-controller)部分。 

## <a name="xbox-one"></a>Xbox One

Xbox One 控制台支持从 Xbox One 的游戏控制器读取输入。

### <a name="xbox-one-game-controller"></a>Xbox One 游戏控制器

Xbox One 游戏控制器是适用于 Xbox One 最常见的输入的设备。 `GamePad`类提供了从游戏控制器硬件的输入的值。

![](input-images/image3.png "游戏手柄类提供了输入的值从游戏控制器硬件")

## <a name="summary"></a>总结

本指南提供的 MonoGame 的概述`GamePad`类，如何实现输入读取逻辑和关系图的常见`GamePad`实现。

## <a name="related-links"></a>相关链接

- [MonoGame 游戏手柄](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Input_GamePad)
