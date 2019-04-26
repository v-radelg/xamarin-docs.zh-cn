---
title: TextureView
ms.prod: xamarin
ms.assetid: DD1F3D68-5DD8-4644-8A13-08AE7719DE30
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/30/2017
ms.openlocfilehash: 8f20666ddf5f74ff9451a22e289790eb14071f90
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61076102"
---
# <a name="textureview"></a>TextureView

`TextureView`类是一个使用硬件加速 2D 呈现若要启用的视频或 OpenGL 内容流，要显示的视图。 例如，下面的屏幕截图显示了`TextureView`显示设备的摄像机从实时源：

[![从设备的摄像机实时图像的示例屏幕截图](texture-view-images/22-textureviewcamera.png)](texture-view-images/22-textureviewcamera.png#lightbox)

与不同`SurfaceView`类，还可用于显示 OpenGL 或视频内容，TextureView 不呈现到一个单独的窗口。
因此，`TextureView`能够支持与任何其他视图一样视图转换。 例如，旋转`TextureView`可以通过只需设置来实现其`Rotation`属性中，通过设置其透明度其`Alpha`属性中，依次类推。

因此，对于`TextureView`我们现在可以从照相机中执行操作，例如显示实时流和转换，如下面的代码中所示：

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

上面的代码创建`TextureView`中的活动实例`OnCreate`方法，并设置为活动`TextureView`的`SurfaceTextureListener`。 要`SurfaceTextureListener`，活动实现`TextureView.ISurfaceTextureListener`接口。 系统将调用`OnSurfaceTextAvailable`方法时`SurfaceTexture`随时可供使用。 在此方法中，我们需要`SurfaceTexture`，将传递中，并将其设置为照相机的预览纹理。 然后我们立即执行正常基于视图的操作，例如设置`Rotation`和`Alpha`，如上面的示例。 生成的应用程序，在设备上运行如下所示：

[![显示图像的设备上运行的应用程序示例](texture-view-images/17-textureviewdemo.png)](texture-view-images/17-textureviewdemo.png#lightbox)

若要使用`TextureView`，硬件加速必须启用，它将默认情况下，从 API 级别 14 开始。 此外，因为此示例使用照相机，同时`android.permission.CAMERA`权限和`android.hardware.camera`必须在设置功能**AndroidManifest.xml**。



## <a name="related-links"></a>相关链接

- [TextureViewDemo （示例）](https://developer.xamarin.com/samples/monodroid/TextureViewDemo/)
- [引入 Ice Cream Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 平台](https://developer.android.com/sdk/android-4.0.html)
