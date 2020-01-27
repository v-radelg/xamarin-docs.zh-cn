---
title: Xamarin TextureView
ms.prod: xamarin
ms.assetid: DD1F3D68-5DD8-4644-8A13-08AE7719DE30
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/30/2017
ms.openlocfilehash: 2ffa544789e0d605a241c8e038c790650a7fc6a3
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76724974"
---
# <a name="xamarinandroid-textureview"></a>Xamarin TextureView

`TextureView` 类是一种视图，它使用硬件加速2D 呈现来启用视频或 OpenGL 内容流。 例如，以下屏幕截图显示了从设备相机显示实时源的 `TextureView`：

[![设备相机中的实时图像的示例屏幕截图](texture-view-images/22-textureviewcamera.png)](texture-view-images/22-textureviewcamera.png#lightbox)

与用于显示 OpenGL 或视频内容的 `SurfaceView` 类不同，TextureView 不会呈现在单独的窗口中。
因此，`TextureView` 可以像任何其他视图一样支持视图转换。 例如，旋转 `TextureView` 只需通过设置其 `Rotation` 属性、其透明度设置 `Alpha` 属性等即可实现。

因此，通过 `TextureView` 我们现在可以执行一些操作，例如显示照相机中的实时流并对其进行转换，如以下代码所示：

```csharp
public class TextureViewActivity : Activity,
    TextureView.ISurfaceTextureListener
{
    Camera _camera;
    TextureView _textureView;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
        _textureView = new TextureView (this);
        _textureView.SurfaceTextureListener = this;

        SetContentView (_textureView);
    }

    public void OnSurfaceTextureAvailable (
        Android.Graphics.SurfaceTexture surface,
        int width, int height)
    {
        _camera = Camera.Open ();
        var previewSize = _camera.GetParameters ().PreviewSize;
        _textureView.LayoutParameters =
            new FrameLayout.LayoutParams (previewSize.Width,
                previewSize.Height, (int)GravityFlags.Center);

        try {
            _camera.SetPreviewTexture (surface);
            _camera.StartPreview ();
        } catch (Java.IO.IOException ex) {
            Console.WriteLine (ex.Message);
        }

        // this is the sort of thing TextureView enables
        _textureView.Rotation = 45.0f;
        _textureView.Alpha = 0.5f;
    }
    …
}
```

上面的代码在活动的 `OnCreate` 方法中创建一个 `TextureView` 实例，并将该活动设置为 `TextureView`的 `SurfaceTextureListener`。 为了成为 `SurfaceTextureListener`，活动实现了 `TextureView.ISurfaceTextureListener` 接口。 当 `SurfaceTexture` 可供使用时，系统将调用 `OnSurfaceTextAvailable` 方法。 在此方法中，我们将采用传入的 `SurfaceTexture`，并将其设置为相机的预览纹理。 然后，可以根据上面的示例中所示，随意执行基于视图的常规操作，例如设置 `Rotation` 和 `Alpha`。 在设备上运行的生成的应用程序如下所示：

[在设备上运行的应用的 ![示例，显示图像](texture-view-images/17-textureviewdemo.png)](texture-view-images/17-textureviewdemo.png#lightbox)

若要使用 `TextureView`，必须启用硬件加速，默认情况下，它将在 API 级别14下启用。 此外，由于本示例使用了照相机，因此必须在**androidmanifest.xml**中设置 `android.permission.CAMERA` 权限和 `android.hardware.camera` 功能。

## <a name="related-links"></a>관련 링크

- [TextureViewDemo （示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/textureviewdemo)/）
