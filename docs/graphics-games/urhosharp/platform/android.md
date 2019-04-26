---
title: UrhoSharp Android 支持
description: 本文档介绍特定于 Android 的安装程序和功能相关 UrhoSharp 的信息。 具体而言，它讨论了支持的体系结构，如何创建配置并启动 Urho，和 Urho 的自定义嵌入的项目。
ms.prod: xamarin
ms.assetid: 8409BD81-B1A6-4F5D-AE11-6BBD3F7C6327
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: e7371fa85fd5955e9a0fd285adb32844001821b3
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61285164"
---
# <a name="urhosharp-android-support"></a>UrhoSharp Android 支持

_Android 的特定安装程序和功能_

尽管 Urho 是可移植类库，并且允许相同的 API，用于跨各种平台的游戏逻辑，仍需要初始化 Urho 在平台特定驱动程序，并在某些情况下，将想要充分利用平台特定功能.

在以下页中，假定`MyGame`是一个的子类`Application`类。

## <a name="architectures"></a>体系结构

**支持的体系结构**: x86、 armeabi，armeabi-v7a

## <a name="create-a-project"></a>创建项目

创建 Android 项目，并添加 UrhoSharp NuGet 包。

添加数据包含你的资产**资产**目录并确保所有文件都有**AndroidAsset**作为**生成操作**。

![项目安装程序](android-images/image-3.png "包含资产的资产目录中添加数据")

## <a name="configure-and-launching-urho"></a>配置和启动 Urho

添加 using 语句`Urho`和`Urho.Android`命名空间，以及如何将这段代码用于初始化 Urho，以及启动你的应用程序。

若要运行游戏时，MyGame 类中实现的最简单方法是调用

```csharp
UrhoSurface.RunInActivity<MyGame>();
```

这将作为内容与游戏打开全屏幕活动。

## <a name="custom-embedding-of-urho"></a>自定义嵌入 Urho

您可以或者使 Urho 接管整个应用程序屏幕上，并将其用作应用程序的组件，可以创建`SurfaceView`通过：

```csharp
var surface = UrhoSurface.CreateSurface<MyGame>(activity)
```

您还需要一些将事件转发从您活动到 UrhoSharp，例如：

```csharp
protected override void OnPause()
{
    UrhoSurface.OnPause();
    base.OnPause();
}
```

您必须对执行相同操作： `OnResume`， `OnPause`， `OnLowMemory`， `OnDestroy`，`DispatchKeyEvent`和`OnWindowFocusChanged`。

这显示了启动游戏的典型活动：

```csharp
[Activity(Label = "MyUrhoApp", MainLauncher = true,
    Icon = "@drawable/icon", Theme = "@android:style/Theme.NoTitleBar.Fullscreen",
    ConfigurationChanges = ConfigChanges.KeyboardHidden | ConfigChanges.Orientation,
    ScreenOrientation = ScreenOrientation.Portrait)]
public class MainActivity : Activity
{
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        var mLayout = new AbsoluteLayout(this);
        var surface = UrhoSurface.CreateSurface<MyUrhoApp>(this);
        mLayout.AddView(surface);
        SetContentView(mLayout);
    }

    protected override void OnResume()
    {
        UrhoSurface.OnResume();
        base.OnResume();
    }

    protected override void OnPause()
    {
        UrhoSurface.OnPause();
        base.OnPause();
    }

    public override void OnLowMemory()
    {
        UrhoSurface.OnLowMemory();
        base.OnLowMemory();
    }

    protected override void OnDestroy()
    {
        UrhoSurface.OnDestroy();
        base.OnDestroy();
    }

    public override bool DispatchKeyEvent(KeyEvent e)
    {
        if (!UrhoSurface.DispatchKeyEvent(e))
            return false;
        return base.DispatchKeyEvent(e);
    }

    public override void OnWindowFocusChanged(bool hasFocus)
    {
        UrhoSurface.OnWindowFocusChanged(hasFocus);
        base.OnWindowFocusChanged(hasFocus);
    }
}
```

