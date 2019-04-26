---
title: 在 Xamarin.iOS 中手动相机控件
description: 本文档介绍如何在 iOS AVFoundation framework 可用于通过 Xamarin.iOS 启用手动相机控件。 手动相机控件允许用户与控件焦点、 白平衡和曝光度设置。
ms.prod: xamarin
ms.assetid: 56340225-5F3C-4BFC-9A79-61496D7FE5B5
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: c61b3fee9009afb86ccd3fd0e16d7812a8e90feb
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61384055"
---
# <a name="manual-camera-controls-in-xamarinios"></a>在 Xamarin.iOS 中手动相机控件

提供的手动相机控件`AVFoundation Framework`在 iOS 8 中，允许在移动应用程序需要完全控制 iOS 设备的照相机。 此级别细粒度的控制可用于创建专业级别的相机应用程序，并通过创建静止图像或视频时调整相机的参数提供艺术家组合。

开发科学记数法或行业应用程序，其中结果不太方便你的正确性或映像的优点，更方便你突出显示某些功能或要获得的图像元素时，这些控件也可以很有用。

## <a name="avfoundation-capture-objects"></a>AVFoundation 捕获对象

是否采用视频或静止图像使用的 iOS 设备上的照相机，用于捕获这些映像的过程是很大程度上相同。 这是使用默认的自动照相机控件或一种是充分利用新的手动相机控件的应用程序，则返回 true:

 [![](intro-to-manual-camera-controls-images/image1.png "AVFoundation 捕获对象概述")](intro-to-manual-camera-controls-images/image1.png#lightbox)

输入来自`AVCaptureDeviceInput`成`AVCaptureSession`通过`AVCaptureConnection`。 结果是任一输出为静止图像或视频流。 整个过程由控制`AVCaptureDevice`。

## <a name="manual-controls-provided"></a>手动提供的控件

使用 iOS 8 提供的新 Api，应用程序可以采取以下的相机功能的控件：

-  **手动对焦**– 通过允许最终用户直接控制焦点，应用程序可以提供更好地控制获得的图像。
-  **手动曝光**– 应用程序可以通过提供手动控制曝光，向用户提供更多自由并允许他们获得风格化的外观。
-  **手动白平衡**– 白平衡用于调整图像中的颜色，通常以使其看起来真实。 不同光源具有不同的颜色温度，并在捕获映像使用的照相机设置调整以补偿这些差异。 同样，通过允许用户控制白平衡，用户可以进行调整，不能自动完成。


iOS 8 提供的扩展和增强功能到现有 iOS Api 以提供该映像对此进行精细控制捕获进程。

## <a name="bracketed-capture"></a>用括号括起来的捕获

括起来捕获取决于从手动相机控件上面介绍的设置，并允许应用程序中各种不同的方式捕获时，某个时间点。

简单地说，带中括号捕获是静止图像执行的各种图片从设置到图片的突然增加。

## <a name="requirements"></a>要求

以下被所要完成本文中介绍的步骤：

-  **Xcode 7 + 和 iOS 8 或更高版本**– Apple 的 Xcode 7 和 iOS 8 或较新的 Api 需要安装并配置开发人员的计算机上。
-  **Visual Studio for Mac** – 应安装并配置用户设备上的 Visual Studio for Mac 的最新版本。
-  **iOS 8 设备**– 运行 iOS 8 的最新版本的 iOS 设备。 不能在 iOS 模拟器中测试照相机功能。


## <a name="general-av-capture-setup"></a>常规 AV 捕获安装程序

录制视频的 iOS 设备上时，没有始终是必需的一些常规设置代码。 本部分介绍从 iOS 设备的摄像机的视频录制和显示该视频中的所需最少的设置中实时`UIImageView`。

### <a name="output-sample-buffer-delegate"></a>输出示例缓冲区委托

首要任务之一所需将是一个委托，以监视示例输出缓冲区，并显示到缓冲区中获取图像`UIImageView`应用程序 UI 中。

以下例程将监视示例缓冲区和更新用户界面：

```csharp
using System;
using Foundation;
using UIKit;
using System.CodeDom.Compiler;
using System.Collections.Generic;
using System.Linq;
using AVFoundation;
using CoreVideo;
using CoreMedia;
using CoreGraphics;

namespace ManualCameraControls
{
    public class OutputRecorder : AVCaptureVideoDataOutputSampleBufferDelegate
    {
        #region Computed Properties
        public UIImageView DisplayView { get; set; }
        #endregion

        #region Constructors
        public OutputRecorder ()
        {

        }
        #endregion

        #region Private Methods
        private UIImage GetImageFromSampleBuffer(CMSampleBuffer sampleBuffer) {

            // Get a pixel buffer from the sample buffer
            using (var pixelBuffer = sampleBuffer.GetImageBuffer () as CVPixelBuffer) {
                // Lock the base address
                pixelBuffer.Lock (0);

                // Prepare to decode buffer
                var flags = CGBitmapFlags.PremultipliedFirst | CGBitmapFlags.ByteOrder32Little;

                // Decode buffer - Create a new colorspace
                using (var cs = CGColorSpace.CreateDeviceRGB ()) {

                    // Create new context from buffer
                    using (var context = new CGBitmapContext (pixelBuffer.BaseAddress,
                        pixelBuffer.Width,
                        pixelBuffer.Height,
                        8,
                        pixelBuffer.BytesPerRow,
                        cs,
                        (CGImageAlphaInfo)flags)) {

                        // Get the image from the context
                        using (var cgImage = context.ToImage ()) {

                            // Unlock and return image
                            pixelBuffer.Unlock (0);
                            return UIImage.FromImage (cgImage);
                        }
                    }
                }
            }
        }
        #endregion

        #region Override Methods
        public override void DidOutputSampleBuffer (AVCaptureOutput captureOutput, CMSampleBuffer sampleBuffer, AVCaptureConnection connection)
        {
            // Trap all errors
            try {
                // Grab an image from the buffer
                var image = GetImageFromSampleBuffer(sampleBuffer);

                // Display the image
                if (DisplayView !=null) {
                    DisplayView.BeginInvokeOnMainThread(() => {
                        // Set the image
                        if (DisplayView.Image != null) DisplayView.Image.Dispose();
                        DisplayView.Image = image;

                        // Rotate image to the correct display orientation
                        DisplayView.Transform = CGAffineTransform.MakeRotation((float)Math.PI/2);
                    });
                }

                // IMPORTANT: You must release the buffer because AVFoundation has a fixed number
                // of buffers and will stop delivering frames if it runs out.
                sampleBuffer.Dispose();
            }
            catch(Exception e) {
                // Report error
                Console.WriteLine ("Error sampling buffer: {0}", e.Message);
            }
        }
        #endregion
    }
}
```

使用中的位置，此例程`AppDelegate`可以修改，以打开 AV 捕获会话以记录实时视频源。

### <a name="creating-an-av-capture-session"></a>创建 AV 捕获会话

AV 捕获会话用来控制 iOS 设备的照相机中的实时视频录制，并且需要获取视频到 iOS 应用程序。 由于该示例`ManualCameraControl`示例应用程序在多个不同位置使用在捕获会话，它将在中配置`AppDelegate`，供整个应用程序。

执行以下操作来修改应用程序的`AppDelegate`并添加所需的代码：


1. 双击`AppDelegate.cs`文件在解决方案资源管理器将其打开进行编辑。
1. 添加以下 using 语句的文件的顶部：
    
    ```
    using System;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    ```

1. 将以下私有变量和计算的属性添加`AppDelegate`类：
    
    ```
    #region Private Variables
    private NSError Error;
    #endregion
    
    #region Computed Properties
    public override UIWindow Window {get;set;}
    public bool CameraAvailable { get; set; }
    public AVCaptureSession Session { get; set; }
    public AVCaptureDevice CaptureDevice { get; set; }
    public OutputRecorder Recorder { get; set; }
    public DispatchQueue Queue { get; set; }
    public AVCaptureDeviceInput Input { get; set; }
    #endregion
    ```
  
1. 重写已完成的方法，并将其更改为：
    
    ```
    public override void FinishedLaunching (UIApplication application)
    {
        // Create a new capture session
        Session = new AVCaptureSession ();
        Session.SessionPreset = AVCaptureSession.PresetMedium;
    
        // Create a device input
        CaptureDevice = AVCaptureDevice.DefaultDeviceWithMediaType (AVMediaType.Video);
        if (CaptureDevice == null) {
            // Video capture not supported, abort
            Console.WriteLine ("Video recording not supported on this device");
            CameraAvailable = false;
            return;
        }
    
        // Prepare device for configuration
        CaptureDevice.LockForConfiguration (out Error);
        if (Error != null) {
            // There has been an issue, abort
            Console.WriteLine ("Error: {0}", Error.LocalizedDescription);
            CaptureDevice.UnlockForConfiguration ();
            return;
        }
    
        // Configure stream for 15 frames per second (fps)
        CaptureDevice.ActiveVideoMinFrameDuration = new CMTime (1, 15);
    
        // Unlock configuration
        CaptureDevice.UnlockForConfiguration ();
    
        // Get input from capture device
        Input = AVCaptureDeviceInput.FromDevice (CaptureDevice);
        if (Input == null) {
            // Error, report and abort
            Console.WriteLine ("Unable to gain input from capture device.");
            CameraAvailable = false;
            return;
        }
    
        // Attach input to session
        Session.AddInput (Input);
    
        // Create a new output
        var output = new AVCaptureVideoDataOutput ();
        var settings = new AVVideoSettingsUncompressed ();
        settings.PixelFormatType = CVPixelFormatType.CV32BGRA;
        output.WeakVideoSettings = settings.Dictionary;
    
        // Configure and attach to the output to the session
        Queue = new DispatchQueue ("ManCamQueue");
        Recorder = new OutputRecorder ();
        output.SetSampleBufferDelegate (Recorder, Queue);
        Session.AddOutput (output);
    
        // Let tabs know that a camera is available
        CameraAvailable = true;
    }
    ```  
  
1. 保存对文件所做的更改。


利用此代码，手动相机控件可以轻松地实现进行试验和测试。

## <a name="manual-focus"></a>手动焦点

通过允许最终用户来直接执行控件的焦点，应用程序可以提供更具艺术性控制获得的图像。

例如，专业摄影师可以减轻来实现的图像的焦点[散景效果](https://en.wikipedia.org/wiki/Bokeh):

[![](intro-to-manual-camera-controls-images/image2.png "散景效果")](intro-to-manual-camera-controls-images/image2.png#lightbox)

或者，创建[焦点拉取效果](http://www.mediacollege.com/video/camera/focus/pull.html)，如：

[![](intro-to-manual-camera-controls-images/image3.png "焦点请求效果")](intro-to-manual-camera-controls-images/image3.png#lightbox)

对于科学家或医疗应用程序的编写器，该应用程序可能想要以编程方式移动可重用功能区的试验。 无论哪种方式的新 API 允许执行最终用户或应用程序来控制焦点时该映像。

### <a name="how-focus-works"></a>焦点的工作原理

然后再讨论控制 IOS 8 应用程序中的焦点的详细信息。 让我们快速看一下焦点 iOS 的设备中的工作方式：

[![](intro-to-manual-camera-controls-images/image4.png "IOS 的设备中的焦点工作原理")](intro-to-manual-camera-controls-images/image4.png#lightbox)

光进入 iOS 设备上的相机镜头，并侧重于图像传感器。 从传感器可重用功能区的距离控制焦点 （该图像将显示清晰的区域） 是位置，相对于传感器。 可重用功能区从传感器是越远、 距离对象看起来清晰和接近附近对象看起来清晰。

在 iOS 设备，可重用功能区被移动更近或离传感器磁铁和 springs。 因此，可重用功能区的准确位置是不可能的的因为它将不同设备的并可能会受到参数，例如设备的方向或的设备和 spring 年龄。

### <a name="important-focus-terms"></a>重要的考虑条款

在处理时焦点，有一些开发人员应该已经熟悉的术语：

-  **视野深度**– 最接近和最远的焦点对象之间的距离。 
-  **宏**-这是焦点范围的接近末尾，是可重用功能区专心的最近距离。
-  **无穷大**– 这是焦点范围的远端，此时可重用功能区可以将精力集中最远距离。
-  **Hyperfocal 距离**– 这是焦点范围中的点在框架中最远的对象所在局限于焦点的远端。 换而言之，这是最大化深度的字段的重要位置。 
-  **可重用功能区位置**– 这是什么控制了上述所有其他条款。 这是焦点的控制器的可重用功能区从传感器，从而的距离。


这些条款和记住的知识，新的手动焦点控件可以在 iOS 8 应用程序中成功实现。

### <a name="existing-focus-controls"></a>现有焦点控件

iOS 7 和早期版本中，提供现有焦点控件通过`FocusMode`属性设置为：

-   `AVCaptureFocusModeLocked` – 焦点锁定为单个焦点。
-   `AVCaptureFocusModeAutoFocus` – 相机扫描所有焦点点通过可重用功能区，直到找到 sharp 焦点，然后保持不动。
-   `AVCaptureFocusModeContinuousAutoFocus` – 照相机 refocuses 只要它检测到聚焦不准条件。


现有的控件还提供了一个可设置通过关注点`FocusPointOfInterest`属性，以便用户可以点击能够专注于特定区域。 应用程序还可以通过监视跟踪可重用功能区移动`IsAdjustingFocus`属性。

此外，通过提供的范围限制`AutoFocusRangeRestriction`属性设置为：

-   `AVCaptureAutoFocusRangeRestrictionNear` – 将 autofocus 限制到附近的深度。 在如扫描 QR 码或条码的情况下很有用。
-   `AVCaptureAutoFocusRangeRestrictionFar` – 将 autofocus 限制进行存有一定距离的深度。 在已知是不相关的对象在视图的字段 （例如，窗口框架） 中的位置的情况下很有用。


最后还有`SmoothAutoFocus`速度变慢自动焦点算法和步骤以较小的增量，以避免当录制视频时移动项目的属性。

### <a name="new-focus-controls-in-ios-8"></a>IOS 8 中新的焦点控制

除了已提供 ios 7 及更高版本的功能，现可用于控制 iOS 8 中的焦点的以下功能：

-  当锁定焦点时的可重用功能区位置的完全手动控件。
-  键-值观测到的任何焦点模式中的可重用功能区位置。


若要实现上述功能`AVCaptureDevice`类进行了修改，以包含一个只读的`LensPosition`属性用于获取相机镜头的当前位置。

若要手动控制的可重用功能区位置，捕获设备必须在锁定焦点模式下。 示例:

 `CaptureDevice.FocusMode = AVCaptureFocusMode.Locked;`

`SetFocusModeLocked`捕获设备的方法用于调整相机可重用功能区的位置。 可以提供可选的回调例程，以使更改生效时获得通知。 示例:

```csharp
ThisApp.CaptureDevice.LockForConfiguration(out Error);
ThisApp.CaptureDevice.SetFocusModeLocked(Position.Value,null);
ThisApp.CaptureDevice.UnlockForConfiguration();
```

如上面的代码中所示，捕获设备必须锁定配置中可重用功能区位置的更改之前。 有效的可重用功能区位置值为介于 0.0 和 1.0 之间。

### <a name="manual-focus-example"></a>手动对焦示例

使用常规 AV 捕获设置代码中的位置，`UIViewController`可添加到应用程序的情节提要和配置，如下所示：

[![](intro-to-manual-camera-controls-images/image5.png "可以添加到应用程序的情节提要 UIViewController，并将其配置如下所示")](intro-to-manual-camera-controls-images/image5.png#lightbox)

该视图包含以下主要元素：

-  一个`UIImageView`将显示的视频源。
-  一个`UISegmentedControl`，将从自动更改焦点模式下，为已锁定。
-  一个`UISlider`，将显示和更新当前的可重用功能区位置。


执行以下操作来布置视图控制器为手动焦点控制：


1. 添加以下 using 语句：

    ```csharp
    using System;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    using System.Timers;
    ```  
  
1. 将以下私有变量添加：

    ```csharp
    #region Private Variables
    private NSError Error;
    private bool Automatic = true;
    #endregion
    ```  
  
1. 添加以下计算的属性：

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    public Timer SampleTimer { get; set; }
    #endregion
    ```  
  
1. 重写`ViewDidLoad`方法并添加以下代码：

    ```csharp
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();
    
        // Hide no camera label
        NoCamera.Hidden = ThisApp.CameraAvailable;
    
        // Attach to camera view
        ThisApp.Recorder.DisplayView = CameraView;
    
        // Create a timer to monitor and update the UI
        SampleTimer = new Timer (5000);
        SampleTimer.Elapsed += (sender, e) => {
            // Update position slider
            Position.BeginInvokeOnMainThread(() =>{
                Position.Value = ThisApp.Input.Device.LensPosition;
            });
        };
    
        // Watch for value changes
        Segments.ValueChanged += (object sender, EventArgs e) => {
    
            // Lock device for change
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
    
            // Take action based on the segment selected
            switch(Segments.SelectedSegment) {
            case 0:
                // Activate auto focus and start monitoring position
                Position.Enabled = false;
                ThisApp.CaptureDevice.FocusMode = AVCaptureFocusMode.ContinuousAutoFocus;
                SampleTimer.Start();
                Automatic = true;
                break;
            case 1:
                // Stop auto focus and allow the user to control the camera
                SampleTimer.Stop();
                ThisApp.CaptureDevice.FocusMode = AVCaptureFocusMode.Locked;
                Automatic = false;
                Position.Enabled = true;
                break;
            }
    
            // Unlock device
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    
        // Monitor position changes
        Position.ValueChanged += (object sender, EventArgs e) => {
    
            // If we are in the automatic mode, ignore changes
            if (Automatic) return;
    
            // Update Focus position
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
            ThisApp.CaptureDevice.SetFocusModeLocked(Position.Value,null);
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    }
    ```  
  
1. 重写`ViewDidAppear`方法并添加以下内容来开始录制在视图加载：

    ```csharp
    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);
    
        // Start udating the display
        if (ThisApp.CameraAvailable) {
            // Remap to this camera view
            ThisApp.Recorder.DisplayView = CameraView;
    
            ThisApp.Session.StartRunning ();
            SampleTimer.Start ();
        }
    }
    ```  
  
1. 在 Auto 模式中对照相机，滑块将如照相机调整焦点自动移动:

    [![](intro-to-manual-camera-controls-images/image6.png "滑块将自动移动，如照相机调整此示例应用程序中的焦点")](intro-to-manual-camera-controls-images/image6.png#lightbox)
1. 点击锁定段，并将位置滑块手动调整的可重用功能区位置：

    [![](intro-to-manual-camera-controls-images/image7.png "手动调整的可重用功能区位置")](intro-to-manual-camera-controls-images/image7.png#lightbox)
1. 停止应用程序。


上面的代码说明了如何监视可重用功能区位置照相机在自动模式下时或使用滑块来控制可重用功能区位置，当它处于锁定模式下时。

## <a name="manual-exposure"></a>手动曝光

曝光指的是相对于源亮度，图像的亮度和由光的多少命中传感器的持续时间以及传感器 （ISO 映射） 的提升级别确定。 通过提供手动控制曝光，应用程序可以更自由地向最终用户，并允许他们获得风格化的外观。

使用手动公开控件，用户可以采用深色和模式那样忧郁不切实际明亮的映像：

[![](intro-to-manual-camera-controls-images/image8.png "示例为深色和模式那样忧郁显示从不切实际亮暴露的图像")](intro-to-manual-camera-controls-images/image8.png#lightbox)

同样，这可以使用编程控制，自动为科学记数法的应用程序或通过手动提供的应用程序用户界面的控件。 无论哪种方式，新的 iOS 8 公开 Api 提供对照相机的曝光度设置进行精细控制。

### <a name="how-exposure-works"></a>公开的工作原理

然后再讨论控制公开程度 IOS 8 应用程序中的详细信息。 让我们快速看一下公开的工作原理：

[![](intro-to-manual-camera-controls-images/image9.png "公开的工作原理")](intro-to-manual-camera-controls-images/image9.png#lightbox)

结合使用来控制曝光的三个基本元素包括：

-  **快门速度**– 这是快门处于打开状态，以便到相机传感器上的光线的时间长度。 较短时间快门处于打开状态，更少 light 允许和更鲜艳的图像是 （更少的运动模糊）。 时间越长，快门处于打开状态，详细 light 让在和越动作发生的模糊。
-  **ISO 映射**– 这是从电影摄影借用的术语，指的是对象发光电影中化学品的敏感度。 在电影中的低 ISO 值具有更少的粒度和更精细度色彩重现;数字传感器上的低 ISO 值具有较少但更少亮度的传感器噪音。 ISO 值越大，则更亮图像，但有更多传感器噪音。 数字传感器上的"ISO"是度量值的[电子提升](https://en.wikipedia.org/wiki/Gain)，不是物理功能。 
-  **可重用功能区 Aperture** – 这是可重用功能区打开的大小。 所有 iOS 设备上被固定的镜头光圈，因此可用于调整曝光的只有两个值的快门速度和 ISO。


### <a name="how-continuous-auto-exposure-works"></a>如何持续自动公开的工作原理

在学习前手动曝光的工作原理，这是一个好办法了解如何持续自动公开适用于 iOS 设备。

[![](intro-to-manual-camera-controls-images/image10.png "连续自动公开 iOS 的设备中的工作原理")](intro-to-manual-camera-controls-images/image10.png#lightbox)

首先是自动暴露块，它计算理想暴露的作业，并持续进纸计数统计信息。它使用此信息来计算最佳混合的 ISO 和快门速度，以获取场景也亮起。 此周期称为 AE 循环。

### <a name="how-locked-exposure-works"></a>如何锁定的危害工作原理

接下来，让我们看一下如何锁定的公开适用于 iOS 设备。

[![](intro-to-manual-camera-controls-images/image11.png "如何锁定公开适用于 iOS 设备")](intro-to-manual-camera-controls-images/image11.png#lightbox)

同样，可以尝试计算最佳 iOS 和持续时间值自动公开块。 但是，在此模式下 AE 块已断开连接从计数统计信息引擎。

### <a name="existing-exposure-controls"></a>现有风险控件

iOS 7 和更高版本，提供以下现有公开控件通过`ExposureMode`属性：

-   `AVCaptureExposureModeLocked` – 一次示例场景，并使用这些值在整个场景。
-   `AVCaptureExposureModeContinuousAutoExposure` -示例场景持续以确保良好亮起。


`ExposurePointOfInterest`可以用于点击以选择目标对象上，公开公开场景和应用程序可以监视`AdjustingExposure`属性以查看时调整曝光。

### <a name="new-exposure-controls-in-ios-8"></a>在 iOS 8 中的新风险控件

除了已提供 ios 7 及更高版本的功能，现可用于控制 iOS 8 中的进行展示了以下功能：

-  完全手动自定义的风险。
-  Get、 Set 和键值对观察到 IOS 和快门速度 （持续时间）。


若要实现上述功能的新`AVCaptureExposureModeCustom`模式已添加。 自定义模式中的照相机时，以下代码可用于调整曝光持续时间和 ISO:

```csharp
CaptureDevice.LockForConfiguration(out Error);
CaptureDevice.LockExposure(DurationValue,ISOValue,null);
CaptureDevice.UnlockForConfiguration();
```

在自动和已锁定模式中，应用程序可以调整自动曝光例程使用下面的代码的偏差：

```csharp
CaptureDevice.LockForConfiguration(out Error);
CaptureDevice.SetExposureTargetBias(Value,null);
CaptureDevice.UnlockForConfiguration();
```

最小值和最大设置范围取决于的设备应用程序运行，因此它们应永远不会进行硬编码。 相反，使用以下属性以获取最小值和最大值的范围：

-   `CaptureDevice.MinExposureTargetBias` 
-   `CaptureDevice.MaxExposureTargetBias` 
-   `CaptureDevice.ActiveFormat.MinISO` 
-   `CaptureDevice.ActiveFormat.MaxISO` 
-   `CaptureDevice.ActiveFormat.MinExposureDuration` 
-   `CaptureDevice.ActiveFormat.MaxExposureDuration` 


如上面的代码中所示，捕获设备必须锁定配置中公开的更改之前。

### <a name="manual-exposure-example"></a>手动曝光示例

使用常规 AV 捕获设置代码中的位置，`UIViewController`可添加到应用程序的情节提要和配置，如下所示：

[![](intro-to-manual-camera-controls-images/image12.png "可以添加到应用程序的情节提要 UIViewController，并将其配置如下所示")](intro-to-manual-camera-controls-images/image12.png#lightbox)

该视图包含以下主要元素：

-  一个`UIImageView`将显示的视频源。
-  一个`UISegmentedControl`，将从自动更改焦点模式下，为已锁定。
-  四个`UISlider`将显示和更新的偏移量、 持续时间、 ISO 和偏置控件。


执行以下操作来布置视图控制器手动 Exposure Control 的：


1. 添加以下 using 语句：
    
    ```
    using System;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    using System.Timers;
    ```  
  
1. 将以下私有变量添加：

    ```csharp
    #region Private Variables
    private NSError Error; 
    private bool Automatic = true;
    private nfloat ExposureDurationPower = 5;
    private nfloat ExposureMinimumDuration = 1.0f/1000.0f;
    #endregion
    ```  
  
1. 添加以下计算的属性：

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    public Timer SampleTimer { get; set; }
    #endregion
    ```  
  
1. 重写`ViewDidLoad`方法并添加以下代码：

    ```csharp
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();
    
        // Hide no camera label
        NoCamera.Hidden = ThisApp.CameraAvailable;
    
        // Attach to camera view
        ThisApp.Recorder.DisplayView = CameraView;
    
        // Set min and max values
        Offset.MinValue = ThisApp.CaptureDevice.MinExposureTargetBias;
        Offset.MaxValue = ThisApp.CaptureDevice.MaxExposureTargetBias;
    
        Duration.MinValue = 0.0f;
        Duration.MaxValue = 1.0f;
    
        ISO.MinValue = ThisApp.CaptureDevice.ActiveFormat.MinISO;
        ISO.MaxValue = ThisApp.CaptureDevice.ActiveFormat.MaxISO;
    
        Bias.MinValue = ThisApp.CaptureDevice.MinExposureTargetBias;
        Bias.MaxValue = ThisApp.CaptureDevice.MaxExposureTargetBias;
    
        // Create a timer to monitor and update the UI
        SampleTimer = new Timer (5000);
        SampleTimer.Elapsed += (sender, e) => {
            // Update position slider
            Offset.BeginInvokeOnMainThread(() =>{
                Offset.Value = ThisApp.Input.Device.ExposureTargetOffset;
            });
    
            Duration.BeginInvokeOnMainThread(() =>{
                var newDurationSeconds = CMTimeGetSeconds(ThisApp.Input.Device.ExposureDuration);
                var minDurationSeconds = Math.Max(CMTimeGetSeconds(ThisApp.CaptureDevice.ActiveFormat.MinExposureDuration), ExposureMinimumDuration);
                var maxDurationSeconds = CMTimeGetSeconds(ThisApp.CaptureDevice.ActiveFormat.MaxExposureDuration);
                var p = (newDurationSeconds - minDurationSeconds) / (maxDurationSeconds - minDurationSeconds);
                Duration.Value = (float)Math.Pow(p, 1.0f/ExposureDurationPower);
            });
    
            ISO.BeginInvokeOnMainThread(() => {
                ISO.Value = ThisApp.Input.Device.ISO;
            });
    
            Bias.BeginInvokeOnMainThread(() => {
                Bias.Value = ThisApp.Input.Device.ExposureTargetBias;
            });
        };
    
        // Watch for value changes
        Segments.ValueChanged += (object sender, EventArgs e) => {
    
            // Lock device for change
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
    
            // Take action based on the segment selected
            switch(Segments.SelectedSegment) {
            case 0:
                // Activate auto exposure and start monitoring position
                Duration.Enabled = false;
                ISO.Enabled = false;
                ThisApp.CaptureDevice.ExposureMode = AVCaptureExposureMode.ContinuousAutoExposure;
                SampleTimer.Start();
                Automatic = true;
                break;
            case 1:
                // Lock exposure and allow the user to control the camera
                SampleTimer.Stop();
                ThisApp.CaptureDevice.ExposureMode = AVCaptureExposureMode.Locked;
                Automatic = false;
                Duration.Enabled = false;
                ISO.Enabled = false;
                break;
            case 2:
                // Custom exposure and allow the user to control the camera
                SampleTimer.Stop();
                ThisApp.CaptureDevice.ExposureMode = AVCaptureExposureMode.Custom;
                Automatic = false;
                Duration.Enabled = true;
                ISO.Enabled = true;
                break;
            }
    
            // Unlock device
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    
        // Monitor position changes
        Duration.ValueChanged += (object sender, EventArgs e) => {
    
            // If we are in the automatic mode, ignore changes
            if (Automatic) return;
    
            // Calculate value
            var p = Math.Pow(Duration.Value,ExposureDurationPower);
            var minDurationSeconds = Math.Max(CMTimeGetSeconds(ThisApp.CaptureDevice.ActiveFormat.MinExposureDuration),ExposureMinimumDuration);
            var maxDurationSeconds = CMTimeGetSeconds(ThisApp.CaptureDevice.ActiveFormat.MaxExposureDuration);
            var newDurationSeconds = p * (maxDurationSeconds - minDurationSeconds) +minDurationSeconds;
    
            // Update Focus position
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
            ThisApp.CaptureDevice.LockExposure(CMTime.FromSeconds(p,1000*1000*1000),ThisApp.CaptureDevice.ISO,null);
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    
        ISO.ValueChanged += (object sender, EventArgs e) => {
    
            // If we are in the automatic mode, ignore changes
            if (Automatic) return;
    
            // Update Focus position
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
            ThisApp.CaptureDevice.LockExposure(ThisApp.CaptureDevice.ExposureDuration,ISO.Value,null);
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    
        Bias.ValueChanged += (object sender, EventArgs e) => {
    
            // If we are in the automatic mode, ignore changes
            // if (Automatic) return;
    
            // Update Focus position
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
            ThisApp.CaptureDevice.SetExposureTargetBias(Bias.Value,null);
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    }
    ```  
  
1. 重写`ViewDidAppear`方法并添加以下内容来开始录制在视图加载：

    ```csharp
    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);
    
        // Start udating the display
        if (ThisApp.CameraAvailable) {
            // Remap to this camera view
            ThisApp.Recorder.DisplayView = CameraView;
    
            ThisApp.Session.StartRunning ();
            SampleTimer.Start ();
        }
    }
    ```  
  
1. 在 Auto 模式中对照相机，滑块将移动自动照相机调整曝光：

    [![](intro-to-manual-camera-controls-images/image13.png "如照相机调整曝光，滑块将自动移动")](intro-to-manual-camera-controls-images/image13.png#lightbox)
1. 点击锁定段，并拖动偏差滑块手动调整的自动风险偏差：

    [![](intro-to-manual-camera-controls-images/image14.png "手动调整自动风险的偏差")](intro-to-manual-camera-controls-images/image14.png#lightbox)
1. 点击自定义段，并将持续时间和 ISO 滑块手动控制曝光：

    [![](intro-to-manual-camera-controls-images/image15.png "将持续时间和 ISO 滑块拖动到手动控制公开")](intro-to-manual-camera-controls-images/image15.png#lightbox)
1. 停止应用程序。


上面的代码说明了如何在自动模式下，照相机时监视的曝光度设置，以及如何使用滑块来控制透露时已锁定或自定义模式。

## <a name="manual-white-balance"></a>手动白平衡

白平衡控件允许用户调整图像以使其看起来更加逼真中 colosr 的平衡。 不同光源具有不同的颜色温度，并在捕获映像使用的照相机设置必须进行调整以补偿这些差异。 同样，通过允许用户控制白平衡它们可以进行自动例程是不支持的以实现艺术效果的专业调整。

[![](intro-to-manual-camera-controls-images/image16.png "显示手动白平衡调整示例图像")](intro-to-manual-camera-controls-images/image16.png#lightbox)

例如，夏时制具有蓝灰色的强制转换，而 tungsten incandescent 灯具有暖、 黄色橙色色彩。 （困惑，"冷"颜色具有更高版本的颜色温度比"热"的颜色。 颜色温度是物理度量值，一个不感知。）

人类大脑是很好地补偿颜色温度之间的差异，但这一点相机不能做到。 照相机的工作原理是提升相反范畴内形成的颜色差异调整颜色。

新 iOS 8 公开 API 允许应用程序的进程的控制，并提供对照相机的白平衡设置进行精细控制。

### <a name="how-white-balance-works"></a>如何白色平衡工作原理

然后再讨论控制 IOS 8 应用程序中的白平衡的详细信息。 让我们快速看一下如何白色平衡工作原理：

中的颜色识别的研究[CIE 1931 RGB 颜色空间和 CIE 1931 XYZ 颜色空间](https://en.wikipedia.org/wiki/CIE_1931_color_space)是第一个数学上定义颜色空间。 在 1931年中创建由国际委员会照明 (CIE)。

[![](intro-to-manual-camera-controls-images/image17.png "CIE 1931 RGB 颜色空间和 CIE 1931 XYZ 颜色空间")](intro-to-manual-camera-controls-images/image17.png#lightbox)

上面的图表显示我们的所有颜色人眼中的可见蓝色为浅绿色到红色的深度。 可以使用 X 和 Y 值，绘制关系图上的任意点，如图中所示。

为在关系图中可见，有 X 和 Y 值可以是人视觉的范围之外的关系图上绘制的因此不能通过照相机重现这些颜色。

调用上述图表中的较小曲线[Planckian 轨迹](https://en.wikipedia.org/wiki/Planckian_locus)，这表示色温 （以度为单位开氏度），数字越大，蓝色的端 （年） 和较低数字红色端 (cooler)。 这些是适用于典型的照明的情况。

在混合的光照条件的白平衡调整将需要偏离 Planckian 轨迹，以使所需的更改。 在这些情况下，将需要调整要移动到绿色或红色/洋红色 CIE 端缩放。

iOS 设备通过提升的相反的颜色提高补救颜色转换。 例如，如果一个场景有太多的蓝色，然后红色提升将提升进行补偿。 这些值校准特定设备的因此它们与设备相关的提升。

### <a name="existing-white-balance-controls"></a>现有的白平衡控件

iOS 7 和更高版本提供以下现有白平衡控件通过`WhiteBalanceMode`属性：

-   `AVCapture WhiteBalance ModeLocked` – 采样一次场景和使用这些值在整个场景。
-   `AVCapture WhiteBalance ModeContinuousAutoExposure` -示例场景持续以确保良好平衡。


应用程序可以监视和`AdjustingWhiteBalance`属性以查看时调整曝光。

### <a name="new-white-balance-controls-in-ios-8"></a>在 iOS 8 中的新空白平衡控件

除了已提供 ios 7 及更高版本的功能，现可用于在 iOS 8 中的控件白平衡以下功能：

-  获得设备 RGB 的完全手动控制。
-  Get、 Set 和键-值观察设备 RGB 获得。
-  支持的白平衡使用灰色卡。
-  与设备无关的颜色空间转换例程。


若要实现上述功能`AVCaptureWhiteBalanceGain`添加结构具有以下成员：

-   `RedGain` 
-   `GreenGain` 
-   `BlueGain` 


最大的白平衡提升目前四 （4)，并可以方便从`MaxWhiteBalanceGain`属性。 因此在合法范围是从一 (1) 到`MaxWhiteBalanceGain`(4) 当前。

`DeviceWhiteBalanceGains`属性可用于观察当前值。 使用`SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains`要调整平衡获得照相机在锁定的白平衡模式下时。

#### <a name="conversion-routines"></a>转换例程

已添加到 iOS 8 帮助，来回转换，转换例程设备独立颜色空间。 若要实现转换例程`AVCaptureWhiteBalanceChromaticityValues`添加结构具有以下成员：

-   `X` -是一个介于 0 和 1 的值。
-   `Y` -是一个介于 0 和 1 的值。


`AVCaptureWhiteBalanceTemperatureAndTintValues`结构也已添加包含下列成员：

-   `Temperature` -是浮点点以开氏度为单位的值。
-   `Tint` -是从绿色或洋红色从 0 到 150 与针对绿色的方向值为正和负向洋红色中的偏移量。


使用`CaptureDevice.GetTemperatureAndTintValues`和`CaptureDevice.GetDeviceWhiteBalanceGains`温度和色彩、 色度和 RGB 之间进行转换方法获得颜色空间。

> [!NOTE]
> 要转换的值越接近 Planckian 轨迹，转换例程是更准确。




#### <a name="gray-card-support"></a>灰色卡支持

Apple 使用灰色世界的术语来指代内置到 iOS 8 的灰色卡支持。 它允许用户将焦点置于物理的灰色卡，介绍了在至少 50%的帧的中心，并使用其调整白平衡。 灰色卡旨在实现出现非特定语言的白色。

这可以在应用程序中实现，通过提示用户将放在摄像机前, 对物理灰色卡监视`GrayWorldDeviceWhiteBalanceGains`属性并等待，直到值静止。

应用程序然后会锁定的白平衡提升`SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains`方法使用从值`GrayWorldDeviceWhiteBalanceGains`属性，将应用所做的更改。

白平衡中的更改之前，必须配置锁定捕获设备。

### <a name="manual-white-balance-example"></a>手动白平衡示例

使用常规 AV 捕获设置代码中的位置，`UIViewController`可添加到应用程序的情节提要和配置，如下所示：

[![](intro-to-manual-camera-controls-images/image18.png "可以添加到应用程序的情节提要 UIViewController，并将其配置如下所示")](intro-to-manual-camera-controls-images/image18.png#lightbox)

该视图包含以下主要元素：

-  一个`UIImageView`将显示的视频源。
-  一个`UISegmentedControl`，将从自动更改焦点模式下，为已锁定。
-  两个`UISlider`将显示和更新的温度和色彩控件。
-  一个`UIButton`用于示例灰色卡 (灰色 World) 空间和设置白色平衡使用这些值。


执行以下操作来布置视图控制器为手动白色均衡控件：


1. 添加以下 using 语句：

    ```csharp
    using System;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    using System.Timers;
    ```  
  
1. 将以下私有变量添加：

    ```csharp
    #region Private Variables
    private NSError Error; 
    private bool Automatic = true;
    #endregion
    ```
  
1. 添加以下计算的属性：

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    public Timer SampleTimer { get; set; }
    #endregion
    ```  
  
1. 添加以下专用方法，以设置新的空白温度和色彩之间达到平衡：

    ```csharp
    #region Private Methods
    void SetTemperatureAndTint() {
        // Grab current temp and tint
        var TempAndTint = new AVCaptureWhiteBalanceTemperatureAndTintValues (Temperature.Value, Tint.Value);

        // Convert Color space
        var gains = ThisApp.CaptureDevice.GetDeviceWhiteBalanceGains (TempAndTint);

        // Set the new values
        if (ThisApp.CaptureDevice.LockForConfiguration (out Error)) {
            gains = NomralizeGains (gains);
            ThisApp.CaptureDevice.SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains (gains, null);
            ThisApp.CaptureDevice.UnlockForConfiguration ();
        }
    }
    
    AVCaptureWhiteBalanceGains NomralizeGains (AVCaptureWhiteBalanceGains gains)
    {
        gains.RedGain = Math.Max (1, gains.RedGain);
        gains.BlueGain = Math.Max (1, gains.BlueGain);
        gains.GreenGain = Math.Max (1, gains.GreenGain);

        float maxGain = ThisApp.CaptureDevice.MaxWhiteBalanceGain;
        gains.RedGain = Math.Min (maxGain, gains.RedGain);
        gains.BlueGain = Math.Min (maxGain, gains.BlueGain);
        gains.GreenGain = Math.Min (maxGain, gains.GreenGain);

        return gains;
    }
    #endregion
    ```   
  
1. 重写`ViewDidLoad`方法并添加以下代码：

    ```csharp
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();

        // Hide no camera label
        NoCamera.Hidden = ThisApp.CameraAvailable;

        // Attach to camera view
        ThisApp.Recorder.DisplayView = CameraView;

        // Set min and max values
        Temperature.MinValue = 1000f;
        Temperature.MaxValue = 10000f;

        Tint.MinValue = -150f;
        Tint.MaxValue = 150f;

        // Create a timer to monitor and update the UI
        SampleTimer = new Timer (5000);
        SampleTimer.Elapsed += (sender, e) => {
            // Convert color space
            var TempAndTint = ThisApp.CaptureDevice.GetTemperatureAndTintValues (ThisApp.CaptureDevice.DeviceWhiteBalanceGains);

            // Update slider positions
            Temperature.BeginInvokeOnMainThread (() => {
                Temperature.Value = TempAndTint.Temperature;
            });

            Tint.BeginInvokeOnMainThread (() => {
                Tint.Value = TempAndTint.Tint;
            });
        };

        // Watch for value changes
        Segments.ValueChanged += (sender, e) => {
            // Lock device for change
            if (ThisApp.CaptureDevice.LockForConfiguration (out Error)) {

                // Take action based on the segment selected
                switch (Segments.SelectedSegment) {
                case 0:
                // Activate auto focus and start monitoring position
                    Temperature.Enabled = false;
                    Tint.Enabled = false;
                    ThisApp.CaptureDevice.WhiteBalanceMode = AVCaptureWhiteBalanceMode.ContinuousAutoWhiteBalance;
                    SampleTimer.Start ();
                    Automatic = true;
                    break;
                case 1:
                // Stop auto focus and allow the user to control the camera
                    SampleTimer.Stop ();
                    ThisApp.CaptureDevice.WhiteBalanceMode = AVCaptureWhiteBalanceMode.Locked;
                    Automatic = false;
                    Temperature.Enabled = true;
                    Tint.Enabled = true;
                    break;
                }

                // Unlock device
                ThisApp.CaptureDevice.UnlockForConfiguration ();
            }
        };

        // Monitor position changes
        Temperature.TouchUpInside += (sender, e) => {

            // If we are in the automatic mode, ignore changes
            if (Automatic)
                return;

            // Update white balance
            SetTemperatureAndTint ();
        };

        Tint.TouchUpInside += (sender, e) => {

            // If we are in the automatic mode, ignore changes
            if (Automatic)
                return;

            // Update white balance
            SetTemperatureAndTint ();
        };

        GrayCardButton.TouchUpInside += (sender, e) => {

            // If we are in the automatic mode, ignore changes
            if (Automatic)
                return;

            // Get gray card values
            var gains = ThisApp.CaptureDevice.GrayWorldDeviceWhiteBalanceGains;

            // Set the new values
            if (ThisApp.CaptureDevice.LockForConfiguration (out Error)) {
                ThisApp.CaptureDevice.SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains (gains, null);
                ThisApp.CaptureDevice.UnlockForConfiguration ();
            }
        };
    }
    ``` 
  
1. 重写`ViewDidAppear`方法并添加以下内容来开始录制在视图加载：

    ```csharp
    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);
    
        // Start udating the display
        if (ThisApp.CameraAvailable) {
            // Remap to this camera view
            ThisApp.Recorder.DisplayView = CameraView;
    
            ThisApp.Session.StartRunning ();
            SampleTimer.Start ();
        }
    }
    ```  
  
1. 保存所做的更改进行编码和运行应用程序。
1. 在 Auto 模式中对照相机，滑块将移动自动照相机调整白平衡：

    [![](intro-to-manual-camera-controls-images/image19.png "滑块将自动移动，如照相机调整白平衡")](intro-to-manual-camera-controls-images/image19.png#lightbox)
1. 点击锁定段并拖动 Temp 和色彩滑块手动调整白平衡：

    [![](intro-to-manual-camera-controls-images/image20.png "拖动 Temp 和色彩滑块手动调整白平衡")](intro-to-manual-camera-controls-images/image20.png#lightbox)
1. 已锁定的段仍处于选中状态，将在前面的相机的物理灰色卡，然后点击灰色卡按钮以调整灰色世界的白平衡：

    [![](intro-to-manual-camera-controls-images/image21.png "点击灰色卡按钮以调整灰色世界的白平衡")](intro-to-manual-camera-controls-images/image21.png#lightbox)
1. 停止应用程序。

上面的代码说明了如何监视的白平衡设置照相机在自动模式下时或使用滑块来控制白平衡处于锁定模式下时。

## <a name="bracketed-capture"></a>用括号括起来的捕获

括起来捕获取决于从手动相机控件上面介绍的设置，并允许应用程序中各种不同的方式捕获时，某个时间点。

简单地说，带中括号捕获是静止图像执行的各种图片从设置到图片的突然增加。

[![](intro-to-manual-camera-controls-images/image22.png "带中括号捕获的工作原理")](intro-to-manual-camera-controls-images/image22.png#lightbox)

在 iOS 8 中使用带中括号捕获，应用程序可以预设一系列手动相机控件、 发布单独命令以及具有当前场景为每个手动预设返回一系列的图像。

### <a name="bracketed-capture-basics"></a>用括号括起来的捕获基础知识

同样，带中括号捕获是静止图像执行的各种设置图片从图片的突然增加。 带中括号捕获可用的类型包括：

-  **Auto 暴露括号**– 所有映像都具有不同的偏移量。
-  **手动公开括号**– 所有映像都具有不同的快门速度 （持续时间） 和 ISO 量。
-  **简单迸发括号**– 仍在短期内拍摄的图像的一系列。


### <a name="new-bracketed-capture-controls-in-ios-8"></a>带中括号捕获中新的控制 iOS 8

所有带中括号捕获命令实现中`AVCaptureStillImageOutput`类。 使用`CaptureStillImageBracket`方法以获取与给定数组设置的一系列的图像。

已实现两个新类来处理设置：

-   `AVCaptureAutoExposureBracketedStillImageSettings` -它具有一个属性， `ExposureTargetBias`，可用来设置自动公开括号的偏量。 
-   `AVCaptureManual`  `ExposureBracketedStillImageSettings` -它具有两个属性，`ExposureDuration`和`ISO`，可用来为手动曝光括号设置快门速度和 ISO。 


### <a name="bracketed-capture-controls-dos-and-donts"></a>用括号括起来的捕获控制的注意事项

#### <a name="dos"></a>注意事项

下面是使用带中括号捕获 iOS 8 中的控件时应完成的操作的列表：

-  准备的最坏的情况捕获这种情况的应用，通过调用`PrepareToCaptureStillImageBracket`方法。
-  假定示例缓冲区，将来自于相同的共享池。
-  若要释放的以前的准备调用已分配的内存，调用`PrepareToCaptureStillImageBracket`试并将其发送一个对象的数组。


#### <a name="donts"></a>注意事项

下面是使用带中括号捕获 iOS 8 中的控件时不应完成的操作的列表：

-  不要混合括起来捕获单个捕获中的设置类型。
-  不请求多个`MaxBracketedCaptureStillImageCount`中单个捕获的映像。


### <a name="bracketed-capture-details"></a>用括号括起来的捕获的详细信息

使用 iOS 8 中的带中括号捕获时，应考虑以下详细信息：

-  用括号括起来的设置会暂时覆盖`AVCaptureDevice`设置。
-  Flash 和映像稳定设置仍将被忽略。
-  所有映像必须都使用相同的输出格式 （jpeg、 png 等）
-  视频预览可能会丢弃帧。
-  与 iOS 8 兼容的所有设备上支持用括号括起来的捕获。


记住此信息，让我们看看在 iOS 8 中使用带中括号捕获的一个示例。

### <a name="bracket-capture-example"></a>括号捕获示例

使用常规 AV 捕获设置代码中的位置，`UIViewController`可添加到应用程序的情节提要和配置，如下所示：

[![](intro-to-manual-camera-controls-images/image23.png "可以添加到应用程序的情节提要 UIViewController，并将其配置如下所示")](intro-to-manual-camera-controls-images/image23.png#lightbox)

该视图包含以下主要元素：

-  一个`UIImageView`将显示的视频源。
-  三个`UIImageViews`，将显示捕获的结果。
-  一个`UIScrollView`以容纳的视频源和结果视图。
-  一个`UIButton`用于携带括起来捕获一些预设的设置。


执行以下操作来布置视图控制器为带中括号捕获：


1. 添加以下 using 语句：

    ```csharp
    using System;
    using System.Drawing;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    using CoreImage;
    ```  
  
1. 将以下私有变量添加：

    ```csharp
    #region Private Variables
    private NSError Error;
    private List<UIImageView> Output = new List<UIImageView>();
    private nint OutputIndex = 0;
    #endregion
    ```    
  
1. 添加以下计算的属性：

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    #endregion
    ```  
  
1. 添加以下专用方法，以生成所需的输出图像视图：

    ```csharp
    #region Private Methods
    private UIImageView BuildOutputView(nint n) {
    
        // Create a new image view controller
        var imageView = new UIImageView (new CGRect (CameraView.Frame.Width * n, 0, CameraView.Frame.Width, CameraView.Frame.Height));
    
        // Load a temp image
        imageView.Image = UIImage.FromFile ("Default-568h@2x.png");
    
        // Add a label
        UILabel label = new UILabel (new CGRect (0, 20, CameraView.Frame.Width, 24));
        label.TextColor = UIColor.White;
        label.Text = string.Format ("Bracketed Image {0}", n);
        imageView.AddSubview (label);
    
        // Add to scrolling view
        ScrollView.AddSubview (imageView);
    
        // Return new image view
        return imageView;
    }
    #endregion
    ```  
  
1. 重写`ViewDidLoad`方法并添加以下代码：
    
    ```
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();
    
        // Hide no camera label
        NoCamera.Hidden = ThisApp.CameraAvailable;
    
        // Attach to camera view
        ThisApp.Recorder.DisplayView = CameraView;
    
        // Setup scrolling area
        ScrollView.ContentSize = new SizeF (CameraView.Frame.Width * 4, CameraView.Frame.Height);
    
        // Add output views
        Output.Add (BuildOutputView (1));
        Output.Add (BuildOutputView (2));
        Output.Add (BuildOutputView (3));
    
        // Create preset settings
        var Settings = new AVCaptureBracketedStillImageSettings[] {
            AVCaptureAutoExposureBracketedStillImageSettings.Create(-2.0f),
            AVCaptureAutoExposureBracketedStillImageSettings.Create(0.0f),
            AVCaptureAutoExposureBracketedStillImageSettings.Create(2.0f)
        };
    
        // Wireup capture button
        CaptureButton.TouchUpInside += (sender, e) => {
            // Reset output index
            OutputIndex = 0;
    
            // Tell the camera that we are getting ready to do a bracketed capture
            ThisApp.StillImageOutput.PrepareToCaptureStillImageBracket(ThisApp.StillImageOutput.Connections[0],Settings,async (bool ready, NSError err) => {
                // Was there an error, if so report it
                if (err!=null) {
                    Console.WriteLine("Error: {0}",err.LocalizedDescription);
                }
            });
    
            // Ask the camera to snap a bracketed capture
            ThisApp.StillImageOutput.CaptureStillImageBracket(ThisApp.StillImageOutput.Connections[0],Settings, (sampleBuffer, settings, err) =>{
                // Convert raw image stream into a Core Image Image
                var imageData = AVCaptureStillImageOutput.JpegStillToNSData(sampleBuffer);
                var image = CIImage.FromData(imageData);
    
                // Display the resulting image
                Output[OutputIndex++].Image = UIImage.FromImage(image);
    
                // IMPORTANT: You must release the buffer because AVFoundation has a fixed number
                // of buffers and will stop delivering frames if it runs out.
                sampleBuffer.Dispose();
            });
        };
    }
    ```
    
  
1. 重写`ViewDidAppear`方法并添加以下代码：

    ```csharp
    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);
    
        // Start udating the display
        if (ThisApp.CameraAvailable) {
            // Remap to this camera view
            ThisApp.Recorder.DisplayView = CameraView;
    
            ThisApp.Session.StartRunning ();
        }
    }
    
    ```  
    
1. 保存所做的更改进行编码和运行应用程序。
1. 帧场景并点击捕获括号按钮：

    [![](intro-to-manual-camera-controls-images/image24.png "想象一个场景并点击捕获括号按钮")](intro-to-manual-camera-controls-images/image24.png#lightbox)
1. 轻扫从右到左查看三个执行的带中括号捕获的映像：

    [![](intro-to-manual-camera-controls-images/image25.png "轻扫从右到左查看三个执行的带中括号捕获的映像")](intro-to-manual-camera-controls-images/image25.png#lightbox)
1. 停止应用程序。


上面的代码说明了如何配置并在 iOS 8 中其执行自动泄露括起来捕获。

## <a name="summary"></a>总结

在本文中我们有涵盖 iOS 8 提供的新的手动相机控件简介，并介绍其功能和它们的工作原理的基础知识。 我们给出手动焦点、 手动曝光和手动白平衡的示例。 最后，我们给出示例采用括起来捕获使用前面所述的手动相机控件

## <a name="related-links"></a>相关链接

- [ManualCameraControls （示例）](https://developer.xamarin.com/samples/monotouch/ManualCameraControls)
- [iOS 8 简介](~/ios/platform/introduction-to-ios8.md)
