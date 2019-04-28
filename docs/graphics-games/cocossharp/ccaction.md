---
title: 进行动画处理与 ccaction 进行动画处理
description: Ccaction 进行动画处理类简化了对 CocosSharp 游戏添加动画。 若要实现的功能或添加波兰语，则可以使用这些动画。
ms.prod: xamarin
ms.assetid: 74DBD02A-6F10-4104-A61B-08CB49B733FB
author: conceptdev
ms.author: crdun
ms.date: 03/24/2017
ms.openlocfilehash: c486bb2e78579360e0f935219cd82958fedee34b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61357604"
---
# <a name="animating-with-ccaction"></a>进行动画处理与 ccaction 进行动画处理

_Ccaction 进行动画处理类简化了对 CocosSharp 游戏添加动画。若要实现的功能或添加波兰语，则可以使用这些动画。_

`CCAction` 是可用于 CocosSharp 对象进行动画处理的基类。 本指南介绍了内置`CCAction`实现常见任务，例如定位、 缩放和旋转。 它还会查看如何通过继承创建自定义实现`CCAction`。

本指南使用一个名为项目**ActionProject**这[可以在此处下载](https://developer.xamarin.com/samples/mobile/CCAction)。 本指南使用`CCDrawNode`类，这会在介绍[绘图使用 CCDrawNode 的几何图形](~/graphics-games/cocossharp/ccdrawnode.md)指南。


## <a name="running-the-actionproject"></a>运行 ActionProject

**ActionProject**是一种 CocosSharp 解决方案，可以生成适用于 iOS 和 Android。 如何使用一个代码示例既可以它`CCAction`类并为常见的实时演示`CCAction`实现。

三个运行时，显示 ActionProject`CCLabel`左侧的屏幕和视觉对象绘制的两个实例`CCDrawNode`用于查看各种操作的实例：

![](ccaction-images/image1.png "ActionProject 屏幕和由两个 CCDrawNode 实例绘制用于查看各种操作的视觉对象的左侧将显示三个 CCLabel 实例")

在左侧的标签指示哪一种`CCAction`点击屏幕上时，将创建。 默认情况下**位置**选择值，从而导致`CCMove`操作被创建并应用于的红色圆圈：

![](ccaction-images/image2.gif "选择位置值，从而导致 CCMove 操作被创建并应用于该红色圆形")

单击左侧的标签更改哪种类型的`CCAction`在圆上执行。 例如，单击**位置**标签之间循环，可以更改的不同值：

![](ccaction-images/image3.gif "单击的位置标签循环，可以更改的不同值")

## <a name="common-variable-changing-ccaction-classes"></a>常见变量更改 ccaction 进行动画处理类

**ActionProject**使用以下`CCAction`-继承的类，是 CocosSharp 的一部分：

 - `CCMoveTo` – 更改`CCNode`实例的`Position`属性
 - `CCScaleTo` – 更改`CCNode`实例的`Scale`属性
 - `CCRotateTo` – 更改`CCNode`实例的`Rotation`属性

本指南参考了这些操作，如*变量更改*，这意味着它们直接影响的变量`CCNode`添加到。 其他类型的操作*缓动*本指南中稍后介绍的操作。

**ActionProject**演示这些操作的目的是随着时间的推移修改变量。 具体而言，这些`CCActions`构造函数采用两个参数： 现在可以和要分配的值的长度。 以下代码段显示了如何创建三种类型的操作：


```csharp
switch (VariableOptions [currentVariableIndex])
{
    case "Position":
        coreAction = new CCMoveTo(timeToTake, touch.Location);

        break;
    case "Scale":
        var distance = CCPoint.Distance (touch.Location, drawNodeRoot.Position);
        var desiredScale = distance / DefaultCircleRadius;
        coreAction = new CCScaleTo(timeToTake, desiredScale);

        break;
    case "Rotation":
        float differenceY = touch.Location.Y - drawNodeRoot.PositionY;
        float differenceX = touch.Location.X - drawNodeRoot.PositionX;

        float angleInDegrees = -1 * CCMathHelper.ToDegrees(
            (float)System.Math.Atan2(differenceY, differenceX));

        coreAction = new CCRotateTo (timeToTake, angleInDegrees);

        break; 
...
}
```

一旦创建了操作，它添加到 CCNode，如下所示：


```csharp
nodeToAddTo.AddAction (coreAction); 
```

`AddAction` 启动`CCAction`实例的行为，并且它将自动执行其逻辑框架之后-帧直至完成为止。

每个类型上述词结尾*到*这意味着`CCAction`将修改`CCNode`，以便完成该操作后，参数值表示最终状态。 例如，创建`CCMoveTo`与位置的 X = 100 和 Y = 200 导致`CCNode`实例的`Position`设置为 X = 100，Y = 200 指定，则无论什么时间结束时`CCNode`实例的起始位置。

每个"To"类还具有"通过"版本，会将其上的参数值添加到当前值`CCNode`。 例如，创建`CCMoveBy`与位置的 X = 100 和 Y = 200 将导致`CCNode`正在从它所处操作开始时的位置中移动到右侧 100 单位和 up 200 个单位的实例。


## <a name="easing-actions"></a>缓动操作

默认情况下，变量更改操作将执行*线性内插*– 操作将逐渐实现所需的值的固定速度。 如果在插值*位置*线性增长，移动的对象将立即启动和停止的开头和结尾的操作，在移动和其速度会保持不变如将执行该操作。 

非线性内插是不太不和谐并且添加波兰语的元素，因此 CocosSharp 提供了各种各样的缓动操作用于修改变量更改操作。

在中**ActionProject**示例中，我们可以切换通过单击第二个标签的缓动操作这些类型 (其默认值为 **<None>**):

![](ccaction-images/image4.gif "用户可以通过单击第二个标签的缓动操作这些类型之间进行切换")

缓动操作是特别强大，因为它们不受限于任何特定的变量设置操作。 这意味着可以使用相同的缓动操作分配位置、 旋转、 缩放、 或自定义操作 （如将会在本指南后面所示）。

缓动操作包装变量设置操作 (前提是继承自变量设置操作`CCFiniteTimeAction`) 通过接受作为其构造函数中的参数的变量设置操作。

例如，如果标签设置为**位置**， **CCEaseElastic**，然后检测到触摸屏输入时，将执行下面的代码 （请注意代码已被省略以突出显示的相关行）：


```csharp
CCFiniteTimeAction coreAction = null; 
...
coreAction = new CCMoveTo(timeToTake, touch.Location); 
...
CCAction easing = null; 
...
easing = new CCEaseSineOut (coreAction); 
...
nodeToAddTo.AddAction (easing); 
```

由应用程序所示，确切的同一个缓动可应用于其他变量设置操作如`CCRotateTo`:

![](ccaction-images/image5.gif "确切的同一个缓动可应用于其他变量设置操作，例如 CCRotateTo")


## <a name="easing-in-out-and-inout"></a>缓动中、，Out 和 InOut

所有缓动操作都有`In`， `Out`，或`InOut`追加到缓动类型。 缓动应用时这些术语是指：`In`意味着在开始时，将应用缓动`Out`意味着在结束时，和`InOut`意味着在开头和结尾。

`In`缓动操作会影响变量可应用于整个 （无论是在开始和结束），整个内插的方式，但通常最知名的缓动操作特征会开始时进行。 同样，`Out`缓动操作的特征是内插结束时其行为。 例如，`CCEaseBounceOut`会导致弹跳操作结束时的对象。


### <a name="out"></a>Out

`Out` 缓动通常应用内插结尾处最显著的变化。 例如，`CCEaseExponentialOut`接近目标值将降低速度的不断变化的变量的变动率：

![](ccaction-images/image6.gif "CCEaseExponentialOut 会降低不断变化的变量的更改的速率，接近目标值")


### <a name="in"></a>内

`In` 缓动通常适用的内插开头的最明显变化。 例如，`CCEaseExponentialIn`将更慢的操作的开始处：

![](ccaction-images/image7.gif "CCEaseExponentialIn 将更慢的操作的开始处")


### <a name="inout"></a>InOut

`InOut` 通常适用最显著的变化在开头和结尾。 `InOut` 缓动是通常对称。 例如，`CCEaseExponentialInOut`移动速度慢的开头和结尾的操作：

![](ccaction-images/image8.gif "CCEaseExponentialInOut 移动渐变的开始和结束操作")


## <a name="implementing-a-custom-ccaction"></a>实现自定义 ccaction 进行动画处理

CocosSharp 以提供通用功能中将包括所有我们到目前为止讨论的类。 自定义`CCAction`实现可以提供更大的灵活性。 例如，`CCAction`它可以控制的体验栏已填充的比率可以使用，以便体验栏平稳增长每当用户获得的体验。

`CCAction` 实现通常需要两个类：

 - `CCFiniteTimeAction` 实现 – 有限时间操作类负责启动操作。 它是它实例化类，可以直接添加到`CCNode`或缓动操作。 它必须实例化并返回`CCFiniteTimeActionState`，这将执行更新。
 - `CCFiniteTimeActionState` 实现 – 有限时间操作状态类负责更新操作中涉及的变量。 它必须实现更新函数，可将时间值根据目标系统上的值。 此类外部的未显式引用`CCFiniteTimeAction`用于创建它。 它只需在"后台"运行。

**ActionProject**提供一个自定义`CCFiniteTimeAction`实现调用`LineWidthAction,`用于调整的红色圆圈之上绘制黄色线的宽度。 请注意，其唯一工作就是以实例化并返回`LineWidthState`实例：


```csharp
public class LineWidthAction : CCFiniteTimeAction
{
    float endWidth;

    public LineWidthAction (float duration, float width) : base(duration)
    {
        endWidth = width;
    }

    public override CCFiniteTimeAction Reverse ()
    {
        throw new NotImplementedException ();
    }

    protected override CCActionState StartAction (CCNode target)
    {
        return new LineWidthState (this, target, endWidth);
    }
}
```

如前面所述`LineWidthState`执行分配的行的工作`Width`属性根据多少`time`已通过：


```csharp
public class LineWidthState : CCFiniteTimeActionState
{
    float deltaWidth;
    float startWidth;

    LineNode castedTarget;

    public LineWidthState(LineWidthAction action, CCNode target, float endWidth) : base(action, target)
    {
        castedTarget = target as LineNode;

        if (castedTarget == null)
        {
            throw new InvalidOperationException ("The argument target must be a LineNode");
        }

        startWidth = castedTarget.Width;
        deltaWidth = endWidth - startWidth;
    }

    public override void Update (float time)
    {
        castedTarget.Width = startWidth + deltaWidth * time;
    }
} 
```

可以与任何缓动操作更改的行宽度以各种方式组合 LineWidthAction，如下面的动画中所示：

![](ccaction-images/image9.gif "此动画中所示，可以与任何缓动操作更改的行宽度以各种方式组合 LineWidthAction")


### <a name="interpolation-and-the-update-method"></a>内插和更新方法

除了在上面的类中存储值的唯一逻辑位于`LineWidthState.Update`方法。 `startWidth`变量存储目标的宽度`LineNode`操作，开始时和`deltaWidth`变量存储量的值将更改操作的过程。

通过用`time`变量的值为 0，我们可以看到目标`LineNode`将在其起始位置：


```csharp
castedTarget.Width = startWidth + deltaWidth * 0; 
```

同样，我们可以看到目标`LineNode`将在其目标，只需替换值为 1 时变量：


```csharp
castedTarget.Width = startWidth + deltaWidth * 1; 
```

`time`值将通常介于 0 和 1-但并不总是-和`Update`实现不应假定这些边界。 某些缓动方法 (如`CCEaseBackIn`和`CCEaseBackOut`) 将提供的 0 到 1 范围之外的时间值。


## <a name="conclusion"></a>结束语

内插和缓动创建经过精心设计的游戏，尤其是在创建用户界面的重要组成部分。 本指南介绍了如何使用`CCActions`来内插如位置和旋转的标准值以及自定义值。 `LineWidthState`和`LineWidthAction`类演示如何实现自定义操作。

## <a name="related-links"></a>相关链接

- [CCAction](https://developer.xamarin.com/api/type/CocosSharp.CCAction)
- [CCMoveTo](https://developer.xamarin.com/api/type/CocosSharp.CCMoveTo)
- [CCScaleTo](https://developer.xamarin.com/api/type/CocosSharp.CCScaleTo)
- [CCRotateTo](https://developer.xamarin.com/api/type/CocosSharp.CCRotateTo)
- [CCDrawNode](https://developer.xamarin.com/api/type/CocosSharp.CCDrawNode)
- [完整的示例](https://developer.xamarin.com/samples/mobile/CCAction/)
