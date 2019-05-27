---
title: 在 Xamarin.iOS ARKit 2
description: 本文档介绍到 ARKit iOS 12 中的更新。 它重点介绍使用引用对象和映像以进行检测，包括代码环境纹理，并讨论了 ARKit 编程中常见的问题。
ms.prod: xamarin
ms.assetid: af758092-1523-4ab7-aa53-c37a81fb156a
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/22/2018
ms.openlocfilehash: 81d9ab12a4b8e8184e0a61dc9b6d53d72004d25c
ms.sourcegitcommit: b986460787677cf8c2fc7cc8c03f4bc60c592120
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/24/2019
ms.locfileid: "66213330"
---
# <a name="arkit-2-in-xamarinios"></a>在 Xamarin.iOS ARKit 2

ARKit 已显著逐步演化问世 iOS 11 中的上一年后。 首先，这一您现在可以检测垂直和水平平面，这极大地提高了室内增强的现实体验的实用性。 此外，还有新的功能：

* 识别交接点之间的实际和数字影像作为参考映像和对象
* 新的照明模式，用于模拟现实世界照明
* 共享和保留 AR 环境的功能
* 新的存储 AR 内容首选的文件格式

## <a name="recognizing-reference-objects"></a>识别引用对象

ARKit 2 中的一个展示功能是能够识别参考映像和对象。 参考映像可以加载从正常的图像文件 ([稍后讨论](#more-tracking-configurations))，但必须扫描的对象，使用开发人员为中心的引用[ `ARObjectScanningConfiguration` ](xref:ARKit.ARObjectScanningConfiguration)。

### <a name="sample-app-scanning-and-detecting-3d-objects"></a>应用程序示例：扫描和检测三维对象

[扫描和检测 3D 物体](https://developer.xamarin.com/samples/monotouch/ios12/ScanningAndDetecting3DObjects/)示例是一个端口的端口[Apple 项目](https://developer.apple.com/documentation/arkit/scanning_and_detecting_3d_objects?language=objc)演示：

* 应用程序状态管理使用[ `NSNotification` ](xref:Foundation.NSNotification)对象
* 自定义可视化效果
* 复杂手势
* 扫描的对象
* 存储 [`ARReferenceObject`](xref:ARKit.ARReferenceObject)

扫描的引用对象是电池和处理器密集型和较旧的设备通常具有难以实现稳定的跟踪。

### <a name="state-management-using-nsnotification-objects"></a>使用 NSNotification 对象的状态管理

此应用程序使用以下状态之间转换的状态机：

* `AppState.StartARSession`
* `AppState.NotReady`
* `AppState.Scanning`
* `AppState.Testing`

另外使用一组嵌入的状态和转换在`AppState.Scanning`:

* `Scan.ScanState.Ready`
* `Scan.ScanState.DefineBoundingBox`
* `Scan.ScanState.Scanning`
* `Scan.ScanState.AdjustingOrigin`

该应用使用发布到的状态转换通知的被动体系结构[ `NSNotificationCenter` ](xref:Foundation.NSNotificationCenter) ，这些通知订阅。 安装程序类似于此代码片段从`ViewController.cs`:

```csharp
// Configure notifications for application state changes
var notificationCenter = NSNotificationCenter.DefaultCenter;

notificationCenter.AddObserver(Scan.ScanningStateChangedNotificationName, State.ScanningStateChanged);
notificationCenter.AddObserver(ScannedObject.GhostBoundingBoxCreatedNotificationName, State.GhostBoundingBoxWasCreated);
notificationCenter.AddObserver(ScannedObject.GhostBoundingBoxRemovedNotificationName, State.GhostBoundingBoxWasRemoved);
notificationCenter.AddObserver(ScannedObject.BoundingBoxCreatedNotificationName, State.BoundingBoxWasCreated);
notificationCenter.AddObserver(BoundingBox.ScanPercentageChangedNotificationName, ScanPercentageChanged);
notificationCenter.AddObserver(BoundingBox.ExtentChangedNotificationName, BoundingBoxExtentChanged);
notificationCenter.AddObserver(BoundingBox.PositionChangedNotificationName, BoundingBoxPositionChanged);
notificationCenter.AddObserver(ObjectOrigin.PositionChangedNotificationName, ObjectOriginPositionChanged);
notificationCenter.AddObserver(NSProcessInfo.PowerStateDidChangeNotification, DisplayWarningIfInLowPowerMode);

```

典型的通知处理程序将更新 UI，并可能修改应用程序状态，例如当扫描该对象更新此处理程序：

```csharp
private void ScanPercentageChanged(NSNotification notification)
{
    var pctNum = TryGet<NSNumber>(notification.UserInfo, BoundingBox.ScanPercentageUserKey);
    if (pctNum == null)
    {
        return;
    }
    double percentage = pctNum.DoubleValue;
    // Switch to the next state if scan is complete
    if (percentage >= 100.0)
    {
        State.SwitchToNextState();
    }
    else
    {
        DispatchQueue.MainQueue.DispatchAsync(() => navigationBarController.SetNavigationBarTitle($"Scan ({percentage})"));
    }
}

```

最后，`Enter{State}`方法修改模型和用户体验根据新的状态：

```csharp
internal void EnterStateTesting()
{
    navigationBarController.SetNavigationBarTitle("Testing");
    navigationBarController.ShowBackButton(false);
    loadModelButton.Hidden = true;
    flashlightButton.Hidden = false;
    nextButton.Enabled = true;
    nextButton.SetTitle("Share", UIControlState.Normal);

    testRun = new TestRun(sessionInfo, sceneView);
    TestObjectDetection();
    CancelMaxScanTimeTimer();
}
```

### <a name="custom-visualization"></a>自定义可视化效果

低级别"云"点对象的边界框内包含投影到检测到水平面上，应用程序所示。

此点云是为开发人员提供[ `ARFrame.RawFeaturePoints` ](xref:ARKit.ARFrame.RawFeaturePoints)属性。 有效地可视化点云可以是一个复杂的问题。 循环访问点，然后创建并将每个点的新 SceneKit 节点放入会终止帧速率。 或者，如果以异步方式完成，则会存在延迟。 该示例会保留三部分组成的策略的性能：

* 使用的 pin 中的数据将放置和解释数据作为原始字节缓冲区到不安全代码。
* 转换到该原始缓冲区[ `SCNGeometrySource` ](xref:SceneKit.SCNGeometrySource)并创建一个"模板" [ `SCNGeometryElement` ](xref:SceneKit.SCNGeometryElement)对象。
* 快速"拼接"原始数据和模板使用 [`SCNGeometry.Create(SCNGeometrySource[], SCNGeometryElement[])`](xref:SceneKit.SCNGeometry.Create(SceneKit.SCNGeometrySource[],SceneKit.SCNGeometryElement[]))

```csharp
internal static SCNGeometry CreateVisualization(NVector3[] points, UIColor color, float size)
{
  if (points.Length == 0)
  {
    return null;
  }

  unsafe
  {
    var stride = sizeof(float) * 3;

    // Pin the data down so that it doesn't move
    fixed (NVector3* pPoints = &amp;points[0])
    {
      // Important: Don't unpin until after `SCNGeometry.Create`, because geometry creation is lazy

      // Grab a pointer to the data and treat it as a byte buffer of the appropriate length
      var intPtr = new IntPtr(pPoints);
      var pointData = NSData.FromBytes(intPtr, (System.nuint) (stride * points.Length));

      // Create a geometry source (factory) configured properly for the data (3 vertices)
      var source = SCNGeometrySource.FromData(
        pointData,
        SCNGeometrySourceSemantics.Vertex,
        points.Length,
        true,
        3,
        sizeof(float),
        0,
        stride
      );

      // Create geometry element
      // The null and bytesPerElement = 0 look odd, but this is just a template object
      var template = SCNGeometryElement.FromData(null, SCNGeometryPrimitiveType.Point, points.Length, 0);
      template.PointSize = 0.001F;
      template.MinimumPointScreenSpaceRadius = size;
      template.MaximumPointScreenSpaceRadius = size;

      // Stitch the data (source) together with the template to create the new object
      var pointsGeometry = SCNGeometry.Create(new[] { source }, new[] { template });
      pointsGeometry.Materials = new[] { Utilities.Material(color) };
      return pointsGeometry;
    }
  }
}
```

结果如下所示：

![point_cloud](images/arkit_point_cloud.jpeg)

### <a name="complex-gestures"></a>复杂手势

用户可缩放、 旋转和拖动的目标对象周围的边界框。 有关联的笔势识别器在两个有趣的事情。

首先，所有手势识别器激活仅后已超过阈值;例如，一个手指已拖动这么多的像素为单位或旋转超过一些角度。 技术是累积移动，直到阈值超过了，然后将其以增量方式应用：

```csharp
// A custom rotation gesture recognizer that fires only when a threshold is passed
internal partial class ThresholdRotationGestureRecognizer : UIRotationGestureRecognizer
{
    // The threshold after which this gesture is detected.
    const double threshold = Math.PI / 15; // (12°)

    // Indicates whether the currently active gesture has exceeded the threshold
    private bool thresholdExceeded = false;

    private double previousRotation = 0;
    internal double RotationDelta { get; private set; }

    internal ThresholdRotationGestureRecognizer(IntPtr handle) : base(handle)
    {
    }

    // Observe when the gesture's state changes to reset the threshold
    public override UIGestureRecognizerState State
    {
        get => base.State;
        set
        {
            base.State = value;

            switch(value)
            {
                case UIGestureRecognizerState.Began :
                case UIGestureRecognizerState.Changed :
                    break;
                default :
                    // Reset threshold check
                    thresholdExceeded = false;
                    previousRotation = 0;
                    RotationDelta = 0;
                    break;
            }
        }
    }

    public override void TouchesMoved(NSSet touches, UIEvent evt)
    {
        base.TouchesMoved(touches, evt);

        if (thresholdExceeded)
        {
            RotationDelta = Rotation - previousRotation;
            previousRotation = Rotation;
        }

        if (! thresholdExceeded && Math.Abs(Rotation) > threshold)
        {
            thresholdExceeded = true;
            previousRotation = Rotation;
        }
    }
}
```

第二个值得注意的是相对于手势完成是边界框移动相对于检测到实际平面的方法。 中讨论此方面[此 Xamarin 博客文章](https://blog.xamarin.com/exploring-new-ios-12-arkit-capabilities-with-xamarin/)。

## <a name="other-new-features-in-arkit-2"></a>ARKit 2 中的其他新功能

### <a name="more-tracking-configurations"></a>更多的跟踪配置

现在，你可以使用以下任一作为基础的混合现实体验：

* 仅设备加速计 ([`AROrientationTrackingConfiguration`](xref:ARKit.AROrientationTrackingConfiguration)，iOS 11)
* 人脸 ([`ARFaceTrackingConfiguration`](xref:ARKit.ARFaceTrackingConfiguration)，iOS 11)
* 引用图像 ([`ARImageTrackingConfiguration`](xref:ARKit.ARImageTrackingConfiguration)，iOS 12)
* 扫描三维对象 ([`ARObjectScanningConfiguration`](xref:ARKit.ARObjectScanningConfiguration)，iOS 12)
* Visual 惯性 odometry ([`ARWorldTrackingConfiguration`](xref:ARKit.ARWorldTrackingConfiguration)、 改进了 iOS 12 中)

`AROrientationTrackingConfiguration`中, 所述[这篇博客文章和F#示例](https://github.com/lobrien/FSharp_Face_AR)，是最受限制为仅放置数字对象相对于设备的动作，而不尝试将该设备绑定到屏幕上提供了混合现实体验不佳现实世界中。

`ARImageTrackingConfiguration`可以识别实际 2D 图像 （绘图、 徽标等） 并将它们与定位点数字影像：

```csharp
var imagesAndWidths = new[] {
    ("cover1.jpg", 0.185F),
    ("cover2.jpg", 0.185F),
     //...etc...
    ("cover100.jpg", 0.185F),
};

var referenceImages = new NSSet<ARReferenceImage>(
    imagesAndWidths.Select( imageAndWidth =>
    {
      // Tuples cannot be destructured in lambda arguments
        var (image, width) = imageAndWidth;
        // Read the image
        var img = UIImage.FromFile(image).CGImage;
        return new ARReferenceImage(img, ImageIO.CGImagePropertyOrientation.Up, width);
    }).ToArray());

configuration.TrackingImages = referenceImages;
```

有两个有趣方面，此配置：

* 它是有效并可用于引用映像可能较大数量
* 数字影像锚定到映像，即使在现实生活中将该映像 （例如，如果识别书籍的封面，它将跟踪本书现成，布局等等，向下拉取。）。

`ARObjectScanningConfiguration`介绍过[以前](#recognizing-reference-objects)，用于扫描三维对象是以开发人员为中心的配置。 它是高度处理器和密集型的电池，因此不应在最终用户应用程序中。 该示例[扫描和检测三维对象](https://developer.xamarin.com/samples/monotouch/ios12/ScanningAndDetecting3DObjects/)演示如何使用此配置。

最后一个跟踪配置`ARWorldTrackingConfiguration`，是大多数混合现实体验的作业主力。 此配置使用"visual 惯性 odometry"实际"功能点"与数字影像。 相对于实际的水平和垂直飞机定位或相对于检测到数字的几何或 sprite`ARReferenceObject`实例。 在此配置中，世界是与对齐重力，z 轴空间中的照相机的原始位置和数字的对象"将保持不变"相对于现实世界中的对象。

### <a name="environmental-texturing"></a>环境纹理

ARKit 2 支持"环境纹理"捕获的图像用于估计照明和甚至到发光物体应用反射高光。 环境立方体贴图动态生成，并后照相机已在所有方向上，查看可能产生令人印象深刻真实体验：

![环境纹理绘制演示图像](images/arkit_env_texturing.png)

若要使用环境纹理：

* 你[ `SCNMaterial` ](xref:SceneKit.SCNMaterial)对象必须使用[ `SCNLightingModel.PhysicallyBased` ](xref:SceneKit.SCNLightingModel.PhysicallyBased)并分配一个值为 0 到 1 范围内的[ `Metalness.Contents` ](xref:SceneKit.SCNMaterial.Metalness)和[ `Roughness.Contents`](xref:SceneKit.SCNMaterialProperty.Contents)和
* 跟踪配置必须设置[ `EnvironmentTexturing` ](xref:ARKit.ARWorldTrackingConfiguration.EnvironmentTexturing)  =  [ `AREnvironmentTexturing.Automatic` ](xref:ARKit.AREnvironmentTexturing.Automatic) :

```csharp
var sphere = SCNSphere.Create(0.33F);
sphere.FirstMaterial.LightingModelName = SCNLightingModel.PhysicallyBased;
// Shiny metallic sphere
sphere.FirstMaterial.Metalness.Contents = new NSNumber(1.0F);
sphere.FirstMaterial.Roughness.Contents = new NSNumber(0.0F);

// Session configuration:
var configuration = new ARWorldTrackingConfiguration
{
    PlaneDetection = ARPlaneDetection.Horizontal | ARPlaneDetection.Vertical,
    LightEstimationEnabled = true,
    EnvironmentTexturing = AREnvironmentTexturing.Automatic
};
```

尽管前面的代码片段中所示的完美反射纹理是一个示例中的有趣，但环境纹理是可能更好地避免它触发"出使 valley"响应 （纹理只是一个估计基于何种照相机用于避免失误记录）。


### <a name="shared-and-persistent-ar-experiences"></a>共享和持久性 AR 体验

是另一项主要 ARKit 2 新增[ `ARWorldMap` ](xref:ARKit.ARWorldMap)类，该类允许您以共享或存储世界跟踪数据。 获取与当前的世界地图[ `ARSession.GetCurrentWorldMapAsync` ](xref:ARKit.ARSession.GetCurrentWorldMapAsync)或[ `GetCurrentWorldMap(Action<ARWorldMap,NSError>)` ](xref:ARKit.ARSession.GetCurrentWorldMap(System.Action{ARKit.ARWorldMap,Foundation.NSError})) :

```csharp
// Local storage
var PersistentWorldPath => Environment.GetFolderPath(Environment.SpecialFolder.Personal) + "/arworldmap";

// Later, after scanning the environment thoroughly...
var worldMap = await Session.GetCurrentWorldMapAsync();
if (worldMap != null)
{
    var data = NSKeyedArchiver.ArchivedDataWithRootObject(worldMap, true, out var err);
    if (err != null)
    {
        Console.WriteLine(err);
    }
    File.WriteAllBytes(PersistentWorldPath, data.ToArray());
}
```

若要共享或还原世界地图：

1. 将数据从文件加载
2. 取消存档到`ARWorldMap`对象，
3. 将它的值用作[ `ARWorldTrackingConfiguration.InitialWorldMap` ](xref:ARKit.ARWorldTrackingConfiguration.InitialWorldMap)属性：

```csharp
var data = NSData.FromArray(File.ReadAllBytes(PersistentWorldController.PersistenWorldPath));
var worldMap = (ARWorldMap)NSKeyedUnarchiver.GetUnarchivedObject(typeof(ARWorldMap), data, out var err);

var configuration = new ARWorldTrackingConfiguration
{
    PlaneDetection = ARPlaneDetection.Horizontal | ARPlaneDetection.Vertical,
    LightEstimationEnabled = true,
    EnvironmentTexturing = AREnvironmentTexturing.Automatic,
    InitialWorldMap = worldMap
};
```

`ARWorldMap`仅包含不可见的世界跟踪数据并[ `ARAnchor` ](xref:ARKit.ARAnchor)对象，它执行_不_包含数字资产。 若要共享 geometry 或图像，您将需要开发你自己的策略适用于具体的用例 (也许是通过存储/传输位置和方向的几何图形，并将其应用到静态`SCNGeometry`或也许是通过存储/传输序列化的对象）。 好处`ARWorldMap`是相对于共享一次放置资产， `ARAnchor`，设备或会话之间一致地出现。

### <a name="universal-scene-description-file-format"></a>通用场景描述文件格式

ARKit 2 的最终要闻功能是 Apple 的 Pixar 的采用[通用场景描述](https://graphics.pixar.com/usd/docs/Introduction-to-USD.html)文件格式。 此格式替换为共享和存储 ARKit 资产的首选格式的 Collada DAE 格式。 用于可视化资产的支持已内置到 iOS 12 和 Mojave。 USDZ 文件扩展名为包含美元文件未经压缩且未经加密的 zip 存档。 Pixar[提供了用于处理美元文件工具](https://graphics.pixar.com/usd/docs/USD-Toolset.html#USDToolset-usdedit)但还没有得多的第三方支持。

## <a name="arkit-programming-tips"></a>ARKit 编程提示

### <a name="manual-resource-management"></a>手动资源管理

ARKit，在手动管理资源至关重要。 不仅这允许高的帧速率，它实际上是_必要_为了避免混淆"屏幕冻结。" ARKit 框架是延迟提供新的照相机帧 ([`ARSession.CurrentFrame`](xref:ARKit.ARSession.CurrentFrame)。 直到当前[ `ARFrame` ](xref:ARKit.ARFrame)已`Dispose()`对其进行调用，ARKit 将提供一个新框架 ！ 这会导致视频"冻结"即使其余应用程序可以响应。 解决方法是始终访问`ARSession.CurrentFrame`与`using`阻止或手动调用`Dispose()`上它。

所有对象都派生自`NSObject`都`IDisposable`并`NSObject`实现[Dispose 模式](https://docs.microsoft.com/dotnet/standard/design-guidelines/dispose-pattern)，因此，通常应遵循[此模式，用于实现`Dispose`上都派生类](https://docs.microsoft.com/dotnet/standard/garbage-collection/implementing-dispose)。

### <a name="manipulating-transform-matrices"></a>操作转换矩阵

在任何 3D 应用程序，你将需要处理使用简洁描述如何将移动、 旋转和扭曲的对象通过 3D 空间的 4 × 4 转换矩阵。 SceneKit，在这些事务是[ `SCNMatrix4` ](xref:SceneKit.SCNMatrix4)对象。  

[ `SCNNode.Transform` ](xref:SceneKit.SCNNode.Transform)属性将返回`SCNMatrix4`的转换矩阵[ `SCNNode` ](xref:SceneKit.SCNNode) _因为由支持_行优先`simdfloat4x4`类型。 因此，例如：

```csharp
var node = new SCNNode { Position = new SCNVector3(2, 3, 4) };  
var xform = node.Transform;
Console.WriteLine(xform);
// Output is: "(1, 0, 0, 0)\n(0, 1, 0, 0)\n(0, 0, 1, 0)\n(2, 3, 4, 1)"
```  

正如您所看到的位置被编码的底部行的前三个元素中。

在 Xamarin，用于操作转换矩阵的公共类型是`NVector4`，按照惯例被列优先的方式解释。 也就是说，转换/位置组件应在 M14、 m24 感染的主机、 M34、 不 M41、 M42、 M43:

![行优先 vs 列优先](images/arkit_row_vs_column.png)

确保一致的矩阵解释选择正确的行为对至关重要。 由于 3D 转换矩阵是 4 × 4，一致性错误将不会产生任何类型的编译时或甚至运行时异常 — 它只是操作将出现意外操作。 如果你 SceneKit / ARKit 对象似乎会卡滞，飞出，或抖动，不正确的转换矩阵是非常不错。 解决方法很简单： [ `NMatrix4.Transpose` ](xref:OpenTK.NMatrix4.Transpose*)将执行的元素的就地转置。

## <a name="related-links"></a>相关链接

- [示例应用程序 – 扫描和检测三维对象](https://developer.xamarin.com/samples/monotouch/iOS12/ScanningAndDetecting3DObjects/)
- [什么是 ARKit 2 (WWDC 2018) 中的新增功能](https://developer.apple.com/videos/play/wwdc2018/602/)
- [了解 ARKit 跟踪和检测 (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/610/)
- [在 Xamarin.iOS ARKit 简介](https://docs.microsoft.com/xamarin/ios/platform/introduction-to-ios11/arkit/)
