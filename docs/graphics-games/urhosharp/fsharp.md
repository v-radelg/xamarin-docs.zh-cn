---
title: UrhoSharp 编程F#
description: 本文档介绍如何在 Visual Studio for Mac 中使用F#创建一个简单的 Hello world UrhoSharp 应用程序。
ms.prod: xamarin
ms.assetid: F976AB09-0697-4408-999A-633977FEFF64
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: d87749bd74cf2c478e96284060fed7386d10b853
ms.sourcegitcommit: 0df727caf941f1fa0aca680ec871bfe7a9089e7c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69621007"
---
# <a name="programming-urhosharp-with-f"></a>用 F 编程 UrhoSharp\#

F#使用C#程序员使用的相同库和概念对 UrhoSharp 进行编程。 [使用 UrhoSharp](~/graphics-games/urhosharp/using.md)一文概述了 UrhoSharp 引擎, 并应在本文之前阅读。

与C++世界各地的许多库一样, 许多 UrhoSharp 函数返回布尔值或整数, 指示成功或失败。 应使用`|> ignore`忽略这些值。

该[示例程序](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp)是 UrhoSharp 的 "Hello World" F#。

## <a name="creating-an-empty-project"></a>创建空项目

尚无F#适用于 UrhoSharp 的模板, 因此, 若要创建自己的 UrhoSharp 项目, 可以从[示例](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp)开始, 或执行以下步骤:

1. 在 Visual Studio for Mac 中, 创建一个新的**解决方案**。 选择**iOS > 应用 > 单视图应用**, 并**F#** 选择作为实现语言。 
1. 删除**主情节提要**文件。 打开**info.plist**文件, 并在 " **iPhone/iPod 部署信息**" 窗格中, 删除 " `Main` **主接口**" 下拉列表中的字符串。
1. 同时删除**ViewController**文件。

## <a name="building-hello-world-in-urho"></a>在 Urho 中生成 Hello World

你现在已准备好开始定义游戏的类。 至少需要定义的子类`Urho.Application`并重写其`Start`方法。 若要创建此文件, 请右键单击F#项目, 选择 "**添加新文件 ...** ", 并向项目中F#添加一个空类。 新文件将添加到项目中文件列表的末尾, 但必须将其拖动, 以使其在**AppDelegate**中使用*之前*显示。

1. 添加对 Urho NuGet 包的引用。
1. 从现有的 Urho 项目中, 将 (大型) 目录复制到你的项目的**资源/** 目录中。 在F#项目中, 右键单击 "**资源**" 文件夹, 并使用 "**添加/添加现有文件夹**" 将所有这些文件添加到项目。

项目结构现在应如下所示:

![](fsharp-images/solutionpane.png "项目结构现在应如下所示")

将新创建的类定义为的子类型`Urho.Application` , 并重写`Start`其方法:

```fsharp
namespace HelloWorldUrho1

open Urho
open Urho.Gui
open Urho.iOS

type HelloWorld(o : ApplicationOptions) =
    inherit Urho.Application(o) 

override this.Start() = 
        let cache = this.ResourceCache
        let helloText = new Text()

        helloText.Value <- "Hello World from Urho3D, Mono, and F#"
        helloText.HorizontalAlignment <- HorizontalAlignment.Center
        helloText.VerticalAlignment <- VerticalAlignment.Center

        helloText.SetColor (new Color(0.f, 1.f, 0.f))
        let f = cache.GetFont("Fonts/Anonymous Pro.ttf")

        helloText.SetFont(f, 30) |> ignore
                  
        this.UI.Root.AddChild(helloText)
            
```

代码非常简单。 它使用`Urho.Gui.Text`类来显示居中对齐的字符串, 该字符串具有特定的字体和颜色大小。 

但在此代码可以运行之前, 必须初始化 UrhoSharp。 

打开 AppDelegate 文件, 并按如下所示`FinishedLaunching`修改方法:

```fsharp
namespace HelloWorldUrho1

open System
open UIKit
open Foundation
open Urho
open Urho.iOS

[<Register ("AppDelegate")>]
type AppDelegate () =
    inherit UIApplicationDelegate ()

    override this.FinishedLaunching (app, options) =
        let o = ApplicationOptions.Default
     
        let g = new HelloWorld(o)
        g.Run() |> ignore
       
        true
```

为横向模式应用程序提供默认选项。`ApplicationOptions.Default` 将这些`ApplicationOptions`值传递给`Application`子类的默认构造函数 (请注意, 在定义`HelloWorld`类时, 该行`inherit Application(o)`将调用基类构造函数)。

的`Run` 方法`Application`启动程序。 它被定义为返回`int`, 这可以通过管道传递给。 `ignore`

生成的程序应类似于以下屏幕截图:

![结果程序的屏幕截图](fsharp-images/helloworldfsharp.png)

## <a name="related-links"></a>相关链接

- [在 GitHub 上浏览 (示例)](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp)
