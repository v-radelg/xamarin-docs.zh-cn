---
title: 绘制使用 CCDrawNode 的几何图形
description: 本文档介绍 CCDrawNode，提供用于绘制的基元对象，例如线条、 圆形和三角形的方法。
ms.prod: xamarin
ms.assetid: 46A3C3CE-74CC-4A3A-AB05-B694AE182ADB
author: conceptdev
ms.author: crdun
ms.date: 03/24/2017
ms.openlocfilehash: b910e136366c429de8bd2ba1ac959882b4d7201d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61377511"
---
# <a name="drawing-geometry-with-ccdrawnode"></a>绘制使用 CCDrawNode 的几何图形

_`CCDrawNode` 提供用于绘制的基元对象，例如线条、 圆形和三角形方法。_

`CCDrawNode` CocosSharp 中的类提供多种方法来绘制常见几何形状。 它继承自`CCNode`类，并通常添加到`CCLayer`实例。 本指南介绍了如何使用`CCDrawNode`的实例来执行自定义呈现。 它还与屏幕截图和代码示例提供了可用绘图函数的完整列表。


## <a name="creating-a-ccdrawnode"></a>创建 CCDrawNode

`CCDrawNode`类可用于绘制圆形、 矩形和线条等的几何对象。 例如，下面的代码示例演示如何创建`CCDrawNode`实例中绘制一个圆圈`CCLayer`实现类：


```csharp
public class GameLayer : CCLayer
{
    public GameLayer ()
    {
        var drawNode = new CCDrawNode ();
        this.AddChild (drawNode);
        // Origin is bottom-left of the screen. This moves
        // the drawNode 100 pixels to the right and 100 pixels up
        drawNode.PositionX = 100;
        drawNode.PositionY = 100;

        drawNode.DrawCircle (
            center: new CCPoint (0, 0),
            radius: 20,
            color: CCColor4B.White);

    }
} 
```

此代码生成以下圆在运行时：

![](ccdrawnode-images/image1.png "此代码生成在运行时此圆圈")


## <a name="draw-method-details"></a>绘制方法的详细信息

让我们看看几个与绘制相关的详细信息`CCDrawNode`:


### <a name="draw-methods-positions-are-relative-to-the-ccdrawnode"></a>绘制位置是相对于 CCDrawNode 的方法

所有 draw 方法都要求至少一个位置值进行绘制。 此位置的值是相对于`CCDrawNode`实例。 这意味着`CCDrawNode`本身具有位置，并所有绘图调用对`CCDrawNode`还采用一个或多个位置值。 若要帮助您了解如何组合这些值，让我们看一些示例。

首先我们将介绍`DrawCircle`上面示例中：


```csharp
...
drawNode.PositionX = 100;
drawNode.PositionY = 100;

drawNode.DrawCircle (center: new CCPoint (0, 0),
...
```

在这种情况下，`CCDrawNode`定位在 (100100)，并绘制的圆形 (0，0) 是相对于`CCDrawNode`，从而导致正在居中 100 像素，最多和右侧的游戏屏幕的左下角的圆。

`CCDrawNode`也位于原点 （在屏幕的左下角） 依赖于的偏移量圆：


```csharp
...
drawNode.PositionX = 0;
drawNode.PositionY = 0;

drawNode.DrawCircle (center: new CCPoint (50, 60),
...
```

导致圆的中心，50 个单位，上面的代码 (`drawNode.PositionX` + `CCPoint.X`) 右侧的左侧和右侧的屏幕，而且 60 (`drawNode.PositionY` + `CCPoint.Y`) 在屏幕的底部之上的单位。

除非已调用 draw 方法后不能修改绘制的对象`CCDrawNode.Clear`方法调用，因此任何重定位需要在完成`CCDrawNode`本身。

对象由绘制`CCNodes`也会受到`CCNode`实例的`Rotation`和`Scale`属性。


### <a name="draw-methods-do-not-need-to-be-called-every-frame"></a>Draw 方法不需要调用每一帧

Draw 方法需要一次调用创建的永久性视觉对象。 在示例中，在调用`DrawCircle`的构造函数中`GameLayer`–`DrawCircle`不需要调用每个帧来刷新屏幕时重新绘制圆。

这不同于 MonoGame，这通常会呈现内容只有一个框架，在屏幕上和其必须调用每个帧中的 draw 方法。

如果每个帧调用 draw 方法，则对象最终将累积在调用`CCDrawNode`实例，从而导致帧速率下降，如绘制多个对象。


### <a name="each-ccdrawnode-supports-multiple-draw-calls"></a>每个 CCDrawNode 支持多个绘图调用

`CCDrawNode` 实例可用于绘制多个形状。 这样复杂的视觉对象以保存单个对象中。 例如，下面的代码可用于呈现多个圆圈，一个`CCDrawNode`:


```csharp
for (int i = 0; i < 8; i++)
{
    drawNode.DrawCircle (
        center: new CCPoint (i*15, 0),
        radius: 20,
        color: CCColor4B.White);
} 
```

这会导致下图：

![](ccdrawnode-images/image2.png "这会导致此图形")


## <a name="draw-call-examples"></a>绘制调用示例

以下的绘图调用均位于`CCDrawNode`:

- [`DrawCatmullRom`](#drawcatmullrom)
- [`DrawCircle`](#drawcircle)
- [`DrawCubicBezier`](#drawcubicbezier)
- [`DrawEllipse`](#drawellipse)
- [`DrawLineList`](#drawlinelist)
- [`DrawPolygon`](#drawpolygon)
- [`DrawQuadBezier`](#drawquadbezier)
- [`DrawRect`](#drawrect)
- [`DrawSegment`](#drawsegment)
- [`DrawSolidArc`](#drawsolidarc)
- [`DrawSolidCircle`](#drawsolidcircle)
- [`DrawTriangleList`](#drawtrianglelist)


### <a name="drawcardinalspline"></a>DrawCardinalSpline

`DrawCardinalSpline` 创建通过可变数量的点线的曲线。 

`config`参数定义样条会传递哪些点。 下面的示例显示了这四个点通过传递的样条。

`tension`参数控制如何尖锐或倒圆角上自由绘制曲线的点显示。 一个`tension`值为 0 将导致的曲线样条，和一个`tension`值为 1 将导致绘制的直线，直线和硬边缘的样条。

尽管样条曲线，但 CocosSharp 绘制直线，直线与曲线。 `segments`参数控制多少段用来绘制样条。 为更大数量会导致以性能为代价的顺利曲线样条。 

代码示例：


```csharp
var splinePoints = new List<CCPoint> ();
splinePoints.Add (new CCPoint (0, 0));
splinePoints.Add (new CCPoint (50, 70));
splinePoints.Add (new CCPoint (0, 140));
splinePoints.Add (new CCPoint (100, 210));

drawNode.DrawCardinalSpline (
    config: splinePoints,
    tension: 0,
    segments: 64,
    color:CCColor4B.Red); 
```

![](ccdrawnode-images/image3.png "段参数控制多少段用来绘制样条")


### <a name="drawcatmullrom"></a>DrawCatmullRom

`DrawCatmullRom` 创建通过可变数量的点，类似于曲线`DrawCardinalLine`。 此方法不包括张力参数。

代码示例：

```csharp
var splinePoints = new List<CCPoint> ();
splinePoints.Add (new CCPoint (0, 0));
splinePoints.Add (new CCPoint (80, 90));
splinePoints.Add (new CCPoint (100, 0));
splinePoints.Add (new CCPoint (0, 130)); 

drawNode.DrawCatmullRom (
    points: splinePoints,
    segments: 64); 
```

![](ccdrawnode-images/image4.png "DrawCatmullRom 创建通过可变数量的点，类似于 DrawCardinalLine 曲线")


### <a name="drawcircle"></a>DrawCircle

`DrawCircle` 创建一个圆的周长给定`radius`。

代码示例：

```csharp
drawNode.DrawCircle (
    center:new CCPoint (0, 0),
    radius:20,
    color:CCColor4B.Yellow); 
```

![](ccdrawnode-images/image5.png "DrawCircle 创建给定半径的圆形的外围")


### <a name="drawcubicbezier"></a>DrawCubicBezier

`DrawCubicBezier` 使用控件点设置两个点之间的路径的两个点之间绘制曲线的行。

代码示例：

```csharp
drawNode.DrawCubicBezier (
    origin: new CCPoint (0, 0),
    control1: new CCPoint (50, 150),
    control2: new CCPoint (250, 150),
    destination: new CCPoint (170, 0),
    segments: 64,
    lineWidth: 1,
    color: CCColor4B.Green); 
```

 ![](ccdrawnode-images/image6.png "DrawCubicBezier 两个点之间绘制曲线")


### <a name="drawellipse"></a>DrawEllipse

`DrawEllipse` 创建的轮廓*椭圆*，这通常称为一个椭圆 （尽管这两个不地理相同）。 可通过定义椭圆的形状`CCRect`实例。

代码示例：


```csharp
drawNode.DrawEllipse (
    rect: new CCRect (0, 0, 130, 90),
    lineWidth: 2,
    color: CCColor4B.Gray); 
```

![](ccdrawnode-images/image8.png "DrawEllipse 创建的椭圆，通常称为一个椭圆的边框")


### <a name="drawline"></a>DrawLine

`DrawLine` 连接到给定宽度的一条线的点。 此方法是类似于`DrawSegment`，但它会创建平面终结点而不是舍入的终结点。

代码示例：


```csharp
drawNode.DrawLine (
    from: new CCPoint (0, 0),
    to: new CCPoint (150, 30),
    lineWidth: 5,
    color:CCColor4B.Orange); 
```

![](ccdrawnode-images/image9.png "DrawLine 连接到给定宽度的一条线点")


### <a name="drawlinelist"></a>DrawLineList

`DrawLineList` 通过连接每个对指定的点来创建多个行`CCV3F_C4B`数组。 `CCV3F_C4B`结构包含的位置和颜色值。 `verts`参数应始终包含偶数个点，由两个点定义每个行。

代码示例：


```csharp
CCV3F_C4B[] verts = new CCV3F_C4B[] {
    // First line:
    new CCV3F_C4B( new CCPoint(0,0), CCColor4B.White),
    new CCV3F_C4B( new CCPoint(30,60), CCColor4B.White),
    // second line, will blend from white to red:
    new CCV3F_C4B( new CCPoint(60,0), CCColor4B.White),
    new CCV3F_C4B( new CCPoint(120,120), CCColor4B.Red)
};

drawNode.DrawLineList (verts); 
```

 ![](ccdrawnode-images/image10.png "Verts 参数应始终包含偶数个点，由两个点定义每个行")




### <a name="drawpolygon"></a>DrawPolygon

`DrawPolygon` 创建填充的多边形具有可变宽度和颜色的概述。

代码示例：


```csharp
CCPoint[] verts = new CCPoint[] {
    new CCPoint(0,0),
    new CCPoint(0, 100),
    new CCPoint(50, 150),
    new CCPoint(100, 100),
    new CCPoint(100, 0)
};

drawNode.DrawPolygon (verts,
    count: verts.Length,
    fillColor: CCColor4B.White,
    borderWidth: 5,
    borderColor: CCColor4B.Red,
    closePolygon: true); 
```

![](ccdrawnode-images/image11.png "DrawPolygon 创建填充的多边形具有可变宽度和颜色的概述")


### <a name="drawquadbezier"></a>DrawQuadBezier

`DrawQuadBezier` 一条线连接两个点。 它的行为方式类似于`DrawCubicBezier`，但只支持一个单一控制点。

代码示例：


```csharp
drawNode.DrawQuadBezier (
    origin:new CCPoint (0, 0),
    control:new CCPoint (200, 0),
    destination:new CCPoint (0, 300),
    segments:64,
    lineWidth:1,
    color:CCColor4B.White);
```

![](ccdrawnode-images/image12.png "DrawQuadBezier 用线连接两个点")


### <a name="drawrect"></a>DrawRect

`DrawRect` 创建带有可变宽度和颜色的边框的填充的矩形。

代码示例： 


```csharp
var shape = new CCRect (
    0, 0, 100, 200);
drawNode.DrawRect(shape,
    fillColor:CCColor4B.Blue,
    borderWidth: 4,
    borderColor:CCColor4B.White); 
```

![](ccdrawnode-images/image13.png "DrawRect 创建填充的矩形具有可变宽度和颜色的概述")


### <a name="drawsegment"></a>DrawSegment

`DrawSegment` 使用可变宽度和颜色的线连接两个点。 它相当于`DrawLine`，但它会创建圆形的终结点而不是平面终结点。

代码示例：


```csharp
drawNode.DrawSegment (from: new CCPoint (0, 0),
    to: new CCPoint (100, 200),
    radius: 5,
    color:new CCColor4F(1,1,1,1)); 
```

![](ccdrawnode-images/image14.png "DrawSegment 连接两个点的可变宽度和颜色")


### <a name="drawsolidarc"></a>DrawSolidArc

`DrawSolidArc` 创建给定的颜色和 radius 的填入楔形。

代码示例：


```csharp
drawNode.DrawSolidArc(
    pos:new CCPoint(100, 100),
    radius:200,
    startAngle:0,
    sweepAngle:CCMathHelper.Pi/2, // this is in radians, clockwise
    color:CCColor4B.White); 
```

![](ccdrawnode-images/image15.png "DrawSolidArc 创建给定的颜色和 radius 的填入楔形")


### <a name="drawsolidcircle"></a>DrawSolidCircle

`DrawCircle` 创建给定半径的填充的圆形。

代码示例：


```csharp
drawNode.DrawSolidCircle(
    pos: new CCPoint (100, 100),
    radius: 50,
    color: CCColor4B.Yellow); 
```

![](ccdrawnode-images/image16.png "DrawCircle 创建给定半径的填充的圆形")


### <a name="drawtrianglelist"></a>DrawTriangleList

`DrawTriangleList` 创建一组三角形。 每个三角形定义的三个`CCV3F_C4B`数组中的实例。 传递给数组中的顶点数目`verts`参数必须为三的倍数。 请注意，唯一的信息包含在`CCV3F_C4B`verts 和其颜色 – 位置`DrawTriangleList`方法不支持用纹理绘制三角形。

代码示例：


```csharp
CCV3F_C4B[] verts = new CCV3F_C4B[] {
    // First triangle:
    new CCV3F_C4B( new CCPoint(0,0), CCColor4B.White),
    new CCV3F_C4B( new CCPoint(30,60), CCColor4B.White),
    new CCV3F_C4B( new CCPoint(60,0), CCColor4B.White),
    // second triangle, each point has different colors:
    new CCV3F_C4B( new CCPoint(90,0), CCColor4B.Yellow),
    new CCV3F_C4B( new CCPoint(120,60), CCColor4B.Red),
    new CCV3F_C4B( new CCPoint(150,0), CCColor4B.Blue)
};

drawNode.DrawTriangleList (verts); 
```

![](ccdrawnode-images/image17.png "DrawTriangleList 创建一组三角形")


## <a name="summary"></a>总结

本指南介绍如何创建`CCDrawNode`和执行基于基元的呈现。 它提供了每个绘图调用的示例。

## <a name="related-links"></a>相关链接

- [CCDrawNode API](https://developer.xamarin.com/api/type/CocosSharp.CCDrawNode/)
- [完整的示例](https://developer.xamarin.com/samples/mobile/CCDrawNode/)
