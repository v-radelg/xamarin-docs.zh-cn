---
title: 使用 CocosSharp 的 2D 数学
description: 本指南介绍了开发游戏的 2D 数学。 它使用 CocosSharp 来演示如何执行常见的游戏开发任务，并解释了这些任务背后的数学函数。
ms.prod: xamarin
ms.assetid: 5C241AB4-F97E-4B61-B93C-F5D307BCD517
author: conceptdev
ms.author: crdun
ms.date: 03/27/2017
ms.openlocfilehash: ac84d5b28b0f211dccb1697a4b3dbbc9cedf81e9
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57670151"
---
# <a name="2d-math-with-cocossharp"></a>使用 CocosSharp 的 2D 数学

_本指南介绍了开发游戏的 2D 数学。它使用 CocosSharp 来演示如何执行常见的游戏开发任务，并解释了这些任务背后的数学函数。_

放置和移动代码的对象是各种规模的开发游戏的核心部分。 定位和移动需要数学计算，的使用是否游戏需要移动沿一条直线或使用三角函数来旋转对象。 本文档将介绍以下主题：

 - 速度
 - 加速
 - 旋转 CocosSharp 对象
 - 使用速度旋转

用户不具有强数学背景，或谁拥有早已被忘记从 school，这些主题的开发人员无需担心 – 本文档将分解为概念分解部分，并将伴随理论上的说明，以及实际示例。 简单地说，本文将回答老数学学生问题："何时我实际上需要使用这款产品？"


## <a name="requirements"></a>要求

尽管本文档重点介绍主要的数学 CocosSharp 一端，代码示例假定使用对象继承窗体`CCNode`。 此外，由于`CCNode`不包含值的代码以速度，并加速，假定使用提供值，例如 VelocityX、 VelocityY、 AccelerationX 和 AccelerationY 的实体。 实体的详细信息，请参阅我们的演练上[CocosSharp 中的实体](~/graphics-games/cocossharp/entities.md)。


## <a name="velocity"></a>速度

游戏开发人员使用的术语*速度*来描述如何将对象作为移动 – 尤其是如何快速有移动和方向，因此，并移动。 

使用两种类型的单位定义速度： 位置单元和时间单位。 例如，一辆汽车的速度被指每小时英里或公里每小时。 每秒的时间来定义如何快速对象通常使用像素移动游戏开发人员。 例如，一个项目符号可能会移 300 像素，每秒的速度。 也就是说，如果移动速度 300 像素 / 第二个项目符号，然后它将已移 600 单位在两秒和 900 单位在三秒中，依次类推。 一般来说，速度值*添加*到对象的位置 （如我们如下所示）。

虽然我们使用速度来解释速度的单位，但术语速度通常是指独立于方向的值而术语速度是指速度和方向。 因此，项目符号的速度 （假定项目符号一个类，其中包括必需的属性） 的分配可能如下所示：


```csharp
// This bullet is not moving horizontally, so set VelocityX to 0:
bulletInstance.VelocityX = 0;
// Positive Y is "up" so move the bullet up 300 units per second:
bulletInstance.VelocityY = 300;
```


### <a name="implementing-velocity"></a>实现速度

CocosSharp 未实现速度，因此需要移动的对象需要实现其自己的移动逻辑。 通常实现速度的新游戏开发人员错误地使其速度取决于帧速率。 也就是说，以下*实现不正确*将看起来，以提供正确的结果，但将基于游戏的帧速率：

```csharp
// VelocityX and VelocityY will be added every time this code executes
this.PositionX += this.VelocityX;
this.PositionY += this.VelocityY;
```

如果游戏运行以较高的帧速率 （如每秒而不是每秒 30 帧的 60 帧），然后将显示该对象比在速度较慢的帧速率是否运行更快地移动。 同样，如果游戏无法处理帧为高的帧速率 （这可能由后台进程使用的设备的资源），则将显示游戏速度下降。

若要考虑到这一点，速度通常实现使用时间值。 例如，如果`seconds`变量表示的秒数 （或部分） 由于已应用的最后一个时间速度，则下面的代码会导致帧速率无论一致移动的对象：

```csharp
// VelocityX and VelocityY will be added every time this code executes
this.PositionX += this.VelocityX * seconds;
this.PositionY += this.VelocityY * seconds;
```

请考虑其运行速度较低的帧速率的游戏会频繁地更新其较少的对象的位置。 因此，每个更新将导致移动会更频繁地更新游戏了进一步的对象。 `seconds`值帐户为此，通过报告自上次更新以来已经过了多少时间。

有关如何添加基于时间的移动的示例，请参阅[跨越的时间该方案基于移动](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/game_development/time_based_movement)。


### <a name="calculating-positions-using-velocity"></a>使用速度计算位置

若要对其中一个对象后将一定量的时间的推移，进行预测或者帮助优化对象的行为，而无需运行游戏，可以使用速度。 例如，一名开发人员正在实现的触发项目符号移动需要它实例化之后设置项目符号的速度。 可以使用屏幕大小，以提供用于设置速度的基础。 也就是说，如果开发人员知道项目符号应在 2 秒内移动屏幕的高度则方向的速度应设置为除以 2 屏幕的高度。 如果屏幕为 800 像素、 高，该项目符号的速度将设置为 400 （即 800/2）。

同样，在游戏逻辑可能需要计算对象需要多长时间访问给定其速度目标。 这可以通过将由移动对象的速度移动的距离来计算。 例如，下面的代码演示如何将文本分配给一个标签，其中显示了多长时间直到导弹达到其目标：


```csharp
// We'll assume only the X axis for this example
float distanceX = target.PositionX - missile.PositionX;

float secondsToReachTarget = distanceX / missile.VelocityX;

label.Text = secondsToReachTarget + " seconds to reach target"; 
```


## <a name="acceleration"></a>加速

*加速*是游戏开发中的常见概念，它与速度共用许多相似之处。 加速量化对象是加速还是减的前提下 （如何速度值随时间变化）。 加速*添加*速度，就像速度添加定位。 常见的加速的应用程序包括重力、 加快，一辆汽车和激发其 thrusters 空间发货。 

类似于速度，加速中定义的位置和时间单位;但是，加速的时间单位被称为*平方*单元，这反映了数学上定义加速的方式。 也就是说，游戏加速通常以单位*每秒的像素为单位的平方*。

如果对象具有 10 个单位 / 平方秒 X 加速，这意味着，其速度将增加 10 每隔一秒。 如果从停滞不前，它将在 10 个单位每秒后, 两个移动的一秒后秒每 20 个单位，第二个，依次类推。

在两个维度的加速需要的 X 和 Y 的组件，因此可能为它分配，如下所示：


```csharp
// No horizontal acceleration:
icicle.AccelerationX = 0;
// Simulate gravity with Y acceleration. Negative Y is down, so assign a negative value:
icicle.AccelerationY = -50; 
```


### <a name="acceleration-vs-deceleration"></a>加速和减速

有时，加速和减速效果进行了区分在日常生活语音，尽管没有技术之间区别这两个。 重力是这样，在加速力。 如果对象引发向上然后重力会减慢其速度 （减速），但一旦对象已停止不断增加，这就会导致重力的方向相同然后重力加快它 （加快）。 如下所示，加速应用程序是否正在方向或相反方向应用是移动的相同。 


### <a name="implementing-acceleration"></a>实现加速

在实现时，加速是类似于速度 – 未自动实现的 CocosSharp，和基于时间的加速是 （而不是基于帧的加速） 所需的实现。 因此 （以及速度） 的简单加速实现可能如下所示：

```csharp
this.VelocityX += this.AccelerationX * seconds;
this.VelocityY += this.AccelerationY * seconds;
this.PositionX += this.VelocityX * seconds;
this.PositionY += this.VelocityY * seconds;
```

上面的代码是什么被称为*线性近似段之间*加速实现。 实际上，它实现加速了相当接近大程度的准确性，但它不是非常精确的模型的加速。 它为了上面帮助解释如何实现加速的概念。

下面的实现是加速和速度的计算准确的应用程序：


```csharp
float halfSecondsSquared = (seconds * seconds) / 2.0f;

this.PositionX += 
    this.Velocity.X * seconds + this.AccelerationX * halfSecondsSquared;
this.PositionY += 
    this.Velocity.Y * seconds + this.AccelerationY * halfSecondsSquared;

this.VelocityX += this.AccelerationX * seconds;
this.VelocityY += this.AccelerationY * seconds; 
```

上面的代码最明显的区别是`halfSecondsSquared`变量和要应用加速来定位其使用情况。 数学原因不在本教程的范围内，但开发人员感兴趣的数学原理此可找到详细信息中的[有关集成加速此讨论。](http://www.cliffsnotes.com/math/calculus/calculus/integration/distance-velocity-and-acceleration)

实际影响`halfSecondSquare`是该加速的表现与数学上准确地以可预测方式而不考虑帧速率。 加速的线性近似段之间可能会发生帧速率 – 帧速率越低删除变得不太准确的近似值。 使用`halfSecondsSquared`保证代码的行为相同，与帧速率无关。


## <a name="angles-and-rotation"></a>角度和旋转

如视觉对象`CCSprite`支持通过旋转`Rotation`变量。 这可以赋予一个值，以度为单位设置其旋转。 例如，下面的代码演示如何旋转`CCSprite`实例：


```csharp
CCSprite unrotatedSprite = new CCSprite("star.png");
unrotatedSprite.IsAntialiased = false;
unrotatedSprite.PositionX = 100;
unrotatedSprite.PositionY = 100;
this.AddChild (unrotatedSprite);

CCSprite rotatedSprite = new CCSprite("star.png");
rotatedSprite.IsAntialiased = false;
// This sprite is moved to the right so it doesn’t overlap the first
rotatedSprite.PositionX = 130;
rotatedSprite.PositionY = 100;
rotatedSprite.Rotation = 45;
this.AddChild (rotatedSprite); 
```

这将产生以下结果：

![](math-images/image1.png "这会导致此屏幕截图")

请注意，旋转 45 度顺时针 （即出于历史原因旋转如何数学上应用的相反）：

![](math-images/image2.png "请注意，旋转是顺时针旋转 45 度它出于历史原因是如何通过数学方式应用旋转的相反")

一般情况下旋转 CocosSharp 和正则数学可以可视化，如下所示：

![](math-images/image3.png "一般情况下进行轮换，以便 CocosSharp 和正则数学可视化如下")

![](math-images/image4.png "一般情况下进行轮换，以便 CocosSharp 和正则数学可视化如下")

这一区别很重要因为`System.Math`类使用开始沿逆时针方向旋转，因此熟悉此类的开发人员需要处理时反转角度`CCNode`实例。

我们应注意上面显示的图，以度为单位; 显示旋转但是，某些数学函数 (例如在函数`System.Math`命名空间) 期望和返回值中的*弧度*而不是度。 我们将介绍如何在本指南中稍后在两个单位类型之间进行转换。


### <a name="rotating-to-face-a-direction"></a>旋转面临着一个方向

如上所示`CCSprite`可以使用旋转`Rotation`属性。 `Rotation`提供的属性`CCNode`(类的基类`CCSprite`)，这意味着可以旋转实体继承`CCNode`也。 

某些游戏要求以使它们面临目标旋转的对象。 示例包括开枪，播放机目标或向用户触摸屏幕的位置的点在不断充电空间运送计算机控制的防范对象。 但是，旋转值必须首先将根据计算要轮换的实体的位置和人脸的目标的位置。

此过程需要多个步骤：

 - 标识*偏移量*的目标。 偏移量是指的旋转实体和目标实体之间的 X 和 Y 的距离。
 - 使用 （下面将详细介绍） 的反正切值 trigonometry 函数计算的偏移量的角度。
 - 调整的箭头指向右和方向的旋转实体点时未旋转 0 度之间的差异。
 - 调整的数学旋转 （开始沿逆时针方向） 和 CocosSharp 旋转 （顺时针） 之间的差异。

下面的函数 （假定实体中写入） 旋转要面临着一个目标的实体：


```csharp
// This function assumes that it is contained in a CCNode-inheriting object
public void FacePoint(float targetX, float targetY)
{
    // Calculate the offset - the target's position relative to "this"
    float xOffset = targetX - this.PositionX;
    float yOffset = targetY - this.PositionY;

    // Make sure the target isn't the same point as "this". If so,
    // then rotation cannot be calculated.
    if (targetX != this.PositionX || targetY != this.Position.Y)
    {

        // Call Atan2 to get the radians representing the angle from 
        // "this" to the target
        float radiansToTarget = (float)System.Math.Atan2 (yOffset, xOffset);

        // Since CCNode uses degrees for its rotation, we need to convert
        // from radians
        float degreesToTarget = CCMathHelper.ToDegrees (radiansToTarget);

        // The direction that the entity faces when unrotated. In this case
        // the entity is facing "up", which is 90 degrees 
        const float forwardAngle = 90;

        // Adjust the angle we want to rotate by subtracting the
        // forward angle.
        float adjustedForDirecitonFacing = degreesToTarget - forwardAngle;

        // Invert the angle since CocosSharp uses clockwise rotation
        float cocosSharpAngle = adjustedForDirecitonFacing * -1;

        // Finally assign the rotation
        this.Rotation = rotation = cocosSharpAngle;
    }
} 
```

上面的代码无法用于旋转实体以使其面向用户按如下所示触摸屏幕，该点：


```csharp
private void HandleInput(System.Collections.Generic.List<CCTouch> touches, CCEvent touchEvent)
{
    if(touches.Count > 0)
    {
        CCTouch firstTouch = touches[0];
        FacePoint (firstTouch.Location.X, firstTouch.Location.Y);
    }
} 
```

此代码会导致以下行为：

![](math-images/image5.gif "此代码会导致这种行为")

#### <a name="using-atan2-to-convert-offsets-to-angles"></a>使用 Atan2 将转换为角度偏移量

`System.Math.Atan2` 可用于将某一偏移量转换为角度。 函数名称`Atan2`来自三角函数反正切值。 "2"后缀区分此函数从标准`Atan`函数，严格匹配数学行为的反正切值。 反正切值是一个函数，它返回一个值介于-90 之间和 + 90 度 （或以弧度为单位的等效项）。 许多应用程序，包括计算机游戏，通常需要完整的 360 度的值，因此`Math`类包括`Atan2`来满足此需求。

请注意，上面的代码将传递 Y 参数第一次，然后 X 参数调用时`Atan2`方法。 这是向后从常用的 X，Y 位置坐标的顺序。 有关详细信息[，请参阅 Atan2](https://msdn.microsoft.com/library/system.math.atan2(v=vs.110).aspx)。

还有一点值得一的返回值从`Atan2`是以弧度为单位，这是用于测量的另一个单元。 本指南不涵盖弧度的详细信息，但请记住，在所有三角函数`System.Math`命名空间使用弧度为单位，因此任何值必须用在 CocosSharp 对象上之前转换为度。 找不到弧度为单位的详细信息[弧度维基百科网页中](https://en.wikipedia.org/wiki/Radian)。

#### <a name="forward-angle"></a>正向角度

一次`FacePoint`方法将角度转换成弧度，它还定义`forwardAngle`值。 此值表示当其旋转值等于 0 时，该实体面向的角度。 在此示例中，我们假定，该实体朝上，这 90 度时使用的数学旋转 （而不是 CocosSharp 旋转）。 由于我们尚未尚未反转 CocosSharp 的旋转，我们此处使用数学旋转。

下面显示了哪些实体具有`forwardAngle`的 90 度可能如下所示：

![](math-images/image6.png "这将显示具有 90 度的 forwardAngle 的实体可能如下所示")


### <a name="angled-velocity"></a>倾斜的速度

到目前为止我们已经了解了如何将偏移量转换成角度。 本部分中出现另一种方法 – 将角度，并将其转换到 X 和 Y 值。 常见示例包括移动中它对着，方向或排除项目符号中随附了面向方向移空间发货一辆汽车。 

从概念上讲，可以通过第一个定义所需的速度时未旋转，则旋转速度停止到面向某个实体的角度计算速度。 为了帮助说明这一概念，速度 （和加速） 可以看作是 2 维*向量*（其中通常绘制为一个箭头）。 一个向量，速度值与 X = 100 和 Y = 0，如下所示进行可视化：

![](math-images/image7.png "一个向量，速度值与 X = 100 和 Y = 0，如下所示进行可视化")

可以将此向量旋转以生成新的速度。 例如，通过 （使用逆时针旋转） 的 45 度旋转的向量结果所示：

![](math-images/image8.png "在此使用逆时针旋转 45 度旋转矢量")

幸运的是，速度、 加速和甚至位置所有可旋转，而不考虑如何应用值相同的代码。 下面的常规用途函数可用于旋转矢量通过 CocosSharp 旋转值：


```csharp
// Rotates the argument vector by degrees specified by
// cocosSharpDegrees. In other words, the rotation
// value is expected to be clockwise.
// The vector parameter is modified, so it is both an in and out value
void RotateVector(ref CCVector2 vector, float cocosSharpDegrees)
{
    // Invert the rotation to get degrees as is normally
    // used in math (counterclockwise)
    float mathDegrees = -cocosSharpDegrees;

    // Convert the degrees to radians, as the System.Math
    // object expects arguments in radians
    float radians = CCMathHelper.ToRadians (mathDegrees);

    // Calculate the "up" and "right" vectors. This is essentially
    // a 2x2 matrix that we'll use to rotate the vector
    float xAxisXComponent = (float)System.Math.Cos (radians);
    float xAxisYComponent = (float)System.Math.Sin (radians);
    float yAxisXComponent = (float)System.Math.Cos (radians + CCMathHelper.Pi / 2.0f);
    float yAxisYComponent = (float)System.Math.Sin (radians + CCMathHelper.Pi / 2.0f);

    // Store the original vector values which will be used
    // below to perform the final operation of rotation.
    float originalX = vector.X;
    float originalY = vector.Y;

    // Use the axis values calculated above (the matrix values)
    // to rotate and assign the vector.
    vector.X = originalX * xAxisXComponent + originalY * yAxisXComponent;
    vector.Y = originalX * xAxisYComponent + originalY * yAxisYComponent;
} 
```

在完全理解`RotateVector`方法需要熟悉余弦值和正弦三角函数以及一些线性代数，超出了本文的讨论范围。 但是，一次实现`RotateVector`方法可用于任何矢量，包括速度向量旋转。 例如，下面的代码可能会激发中指定的方向的项目符号`rotation`值：


```csharp
// Create a Bullet instance
Bullet newBullet = new Bullet();

// Define the velocity of the bullet when 
// rotation is 0
CCVector2 velocity = new CCVector2 (0, 100);

// Modify the velocity according to rotation
RotateVector (ref velocity, rotation);

// Assign the newBullet's velocity using the
// rotated vector
newBullet.VelocityX = velocity.X;
newBullet.VelocityY = velocity.Y;

// Set the bullet's rotation so it faces
// the direction that it's flying
newBullet.Rotation = rotation; 
```

此代码可能会产生类似于：

![](math-images/image9.png "此代码可能会产生类似于此屏幕截图")


## <a name="summary"></a>总结

本指南介绍了在 2D 游戏开发的基本数学概念。 它显示了如何分配和实现速度和加速，并介绍了如何旋转对象和矢量中任意方向移动。
