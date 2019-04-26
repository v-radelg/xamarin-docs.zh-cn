---
title: 与编程 UrhoSharpF#
description: 本文档介绍如何创建简单你好世界 UrhoSharp 应用程序使用F#在 Visual Studio for mac。
ms.prod: xamarin
ms.assetid: F976AB09-0697-4408-999A-633977FEFF64
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 99e8f8bf04465d0d61086139ba9889eae141207e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61160574"
---
# <a name="programming-urhosharp-with-f"></a>与编程 UrhoSharpF#

可以用编程 UrhoSharpF#使用相同的库和使用的概念C#程序员。 [使用 UrhoSharp](~/graphics-games/urhosharp/using.md)文章概述了 UrhoSharp 引擎和在这篇文章之前应阅读。

像很多库中发起的C++世界中，许多 UrhoSharp 函数会返回布尔值或整数，指示成功或失败。 应使用`|> ignore`忽略这些值。

[示例程序](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp)是从 UrhoSharp"Hello World" F#。

## <a name="creating-an-empty-project"></a>创建一个空项目

有没有F#模板用于 UrhoSharp 又可用，因此，若要创建既可以使用启动自己 UrhoSharp 项目[示例](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp)或执行以下步骤：

1. 从 Visual Studio for Mac 中，创建一个新**解决方案**。 选择**iOS > 应用程序 > 单一视图应用**，然后选择**F#** 作为实现语言。 
1. 删除**Main.storyboard**文件。 打开**Info.plist**文件并在**iPhone / iPod 部署信息**窗格中，删除`Main`中的字符串**主界面**下拉列表。
1. 删除**ViewController.fs**文件。

## <a name="building-hello-world-in-urho"></a>构建中 Urho 的 Hello World

现在您就可以开始定义您的游戏的类。 至少，您将需要定义的子类`Urho.Application`并重写其`Start`方法。 若要创建此文件，请右键单击在F#项目中，选择**添加新文件...** 并添加一个空F#到你的项目的类。 新文件将添加到你的项目中的文件列表的末尾，但以使其显示必须将其拖*之前*中使用**AppDelegate.fs**。

1. 添加对 Urho NuGet 包的引用。
1. 从现有 Urho 项目中，复制 （大型） 目录**CoreData /** 并**数据 /** 到你的项目**资源 /** 目录。 在你F#项目中，右键单击**资源**文件夹，然后使用**添加 / 添加现有文件夹**将所有这些文件添加到你的项目。

项目结构现在应如下所示：

![](fsharp-images/solutionpane.png "项目结构现在应如下所示")

将你新创建的类定义的子类型为`Urho.Application`并重写其`Start`方法：

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

代码是非常简单。 它使用`Urho.Gui.Text`类，以显示特定字体和颜色大小的中心对齐字符串。 

在运行此代码之前，不过，必须初始化 UrhoSharp。 

打开 AppDelegate.fs 文件并修改`FinishedLaunching`方法，如下所示：

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

`ApplicationOptions.Default`提供横向模式下应用程序的默认选项。 将这些信息传递`ApplicationOptions`的默认构造函数为你`Application`子类 (请注意，当您定义`HelloWorld`类，在行`inherit Application(o)`调用基类构造函数)。 

`Run`方法在`Application`启动程序。 它被定义为返回`int`，这可以输送到`ignore`。 

生成的程序应如下所示：

![](fsharp-images/helloworldfsharp.png "生成的程序应如下所示")








## <a name="related-links"></a>相关链接

- [在 GitHub （示例） 上浏览](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp)
