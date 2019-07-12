---
title: UrhoSharp iOS 和 tvOS 的支持
description: 本文档介绍了 iOS 和 tvOS 支持 UrhoSharp。 它介绍如何创建项目、 配置和启动 Urho，并执行 Urho 自定义嵌入。
ms.prod: xamarin
ms.assetid: 7B06567E-E789-4EA1-A2A9-F3B2212EDD23
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 3efdbfcfdd670525dbf3198deb17c4631a889c56
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832458"
---
# <a name="urhosharp-ios-and-tvos-support"></a>UrhoSharp iOS 和 tvOS 支持

尽管 Urho 是可移植类库，并且允许相同的 API，用于跨各种平台的游戏逻辑，仍需要初始化 Urho 在平台特定驱动程序，并在某些情况下，将想要充分利用平台特定功能.

在以下页中，假定`MyGame`是的 sublcass`Application`类。

## <a name="ios-and-tvos"></a>iOS 和 tvOS

**支持的体系结构：** armv7，arm64，i386

## <a name="creating-a-project"></a>创建项目

创建 iOS 项目，然后将数据添加到资源目录，并确保所有文件都具有**BundleResource**作为**生成操作**。

![项目安装程序](ios-images/image-4.png "为 Resources 目录中添加数据")

## <a name="configuring-and-launching-urho"></a>配置和启动 Urho

添加 using 语句`Urho`和`Urho.iOS`命名空间，以及如何将这段代码用于初始化 Urho，以及启动你的应用程序：

```csharp
new MyGame().Run();
```

请注意，因为 iOS 期望`FinishedLaunching`若要完成，您应排队调用`Run()`若要运行在方法完成后，这是一个常用方法：

```csharp
public override bool FinishedLaunching(UIApplication app, NSDictionary options)
{
    LaunchGame();
    return true;
}

async void LaunchGame()
{
    await Task.Yield();
    new SamplyGame().Run();
}
```

务必禁用 PNG 优化，因为默认 iOS PNG 优化器将生成 Urho 不可以正确当前使用的映像

## <a name="custom-embedding-of-urho"></a>自定义嵌入 Urho

您可以或者使 Urho 接管整个应用程序屏幕上，并将其用作应用程序的组件，可以创建`UrhoSurface`即`UIView`，可以在现有的应用程序中嵌入。

这是你需要：

```csharp
var view = new UrhoSurface () {
    Frame = new CGRect (100,100,200,200),
    BackgroundColor = UIColor.Red
}
window.AddSubview (view);
```

因此，这将承载您 Urho 的类，然后将执行操作：

```csharp
new MyGame().Run ();
```
