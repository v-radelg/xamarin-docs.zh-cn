---
title: 对 Xamarin.iOS 应用进行单元测试
description: 本文档概述了如何对 Xamarin.iOS 应用程序进行单元测试。 其中介绍了如何创建单元测试项目、写入测试以及运行测试。
ms.prod: xamarin
ms.assetid: BD959779-3239-79B6-5289-3A9ECDFBD973
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/19/2017
ms.openlocfilehash: 44ced93605ff595fe2fd7f09f88948e5b0e1914c
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70282461"
---
# <a name="unit-testing-xamarinios-apps"></a>对 Xamarin.iOS 应用进行单元测试

本文档介绍如何为 Xamarin.iOS 项目创建单元测试。
可使用 Touch.Unit 框架对 Xamarin.iOS 执行单元测试，该框架同时包含 iOS 测试运行程序 [Touch.Unit](https://github.com/xamarin/Touch.Unit) 的 NUnit 修改版，可提供一组熟悉的 API 用于编写单元测试。

## <a name="setting-up-a-test-project-in-visual-studio-for-mac"></a>在 Visual Studio for Mac 中安装测试项目

若要为项目创建单元测试框架，只需向解决方案添加“iOS 单元测试项目”  类型的项目。 为此，请右键单击解决方案，然后选择“添加”>“添加新项目”  。 从列表中选择“iOS”>“测试”>“统一 API”>“iOS 单元测试项目”  （可选择 C# 或 F#）。

![](touch.unit-images/00.png "选择 C# 或 F#")

上述操作将创建一个包含基本运行程序，并引用新 MonoTouch.NUnitLite 程序集的基本项目，项目如下所示：

![](touch.unit-images/01.png "解决方案资源管理器中的项目")

`AppDelegate.cs` 类包含测试运行程序，如下所示：

```csharp
[Register ("AppDelegate")]
public partial class AppDelegate : UIApplicationDelegate
{
    UIWindow window;
    TouchRunner runner;

    public override bool FinishedLaunching (UIApplication app, NSDictionary options)
    {
        // create a new window instance based on the screen size
        window = new UIWindow (UIScreen.MainScreen.Bounds);
        runner = new TouchRunner (window);

        // register every tests included in the main application/assembly
        runner.Add (System.Reflection.Assembly.GetExecutingAssembly ());

        window.RootViewController = new UINavigationController (runner.GetViewController ());

        // make the window visible
        window.MakeKeyAndVisible ();

        return true;
    }
}
```

> [!NOTE]
> iOS 单元测试项目类型在 Windows 上的 Visual Studio 2019 或 Visual Studio 2017 中不可用。

## <a name="writing-some-tests"></a>编写测试

准备好基本 shell 后，可开始编写第一组测试。

方法是通过创建类进行编写并对测试应用 `[TestFixture]` 属性。 在每个 TestFixture 类中，应对测试运行程序需调用的每种方法应用 `[Test]` 属性。 实际测试装置可位于测试项目的任一文件中。

若要快速开始，请选择“添加”/“添加新文件”  ，然后在 Xamarin.iOS 组中选择“UnitTests”  。 这将添加一个主干文件，其中包含三个测试（一个通过、一个失败，一个已忽略），文件如下所示：

```csharp
using System;
using NUnit.Framework;

namespace Fixtures {

    [TestFixture]
    public class Tests {

        [Test]
        public void Pass ()
        {
                Assert.True (true);
        }

        [Test]
        public void Fail ()
        {
                Assert.False (true);
        }

        [Test]
        [Ignore ("another time")]
        public void Ignore ()
        {
                Assert.True (false);
        }
    }
}
```

## <a name="running-your-tests"></a>运行测试

若要在解决方案内运行此项目，请右键单击该项目，然后选择“调试项目”  或“运行项目”  。

通过测试运行程序，可查看已注册的测试，并单独选择可执行的测试。

[![](touch.unit-images/02-sml.png "已注册测试的列表")](touch.unit-images/02.png#lightbox) 
[![](touch.unit-images/03-sml.png "单个文本")](touch.unit-images/03.png#lightbox) 

[![](touch.unit-images/04-sml.png "运行结果")](touch.unit-images/04.png#lightbox)

从嵌套视图中选择测试装置可运行单个测试装置，或选择“全部运行”运行所有测试。 如果运行默认测试，则应包含三个测试（一个通过、一个失败，一个已忽略）。 报表如下所示，你可以直接向下钻取失败测试并找出有关失败的详细信息：

[![](touch.unit-images/05-sml.png "示例报表")](touch.unit-images/05.png#lightbox) [![](touch.unit-images/06-sml.png "示例报表")](touch.unit-images/06.png#lightbox) [![](touch.unit-images/07-sml.png "示例报表")](touch.unit-images/07.png#lightbox)

还可在 IDE 中查看“应用程序输出”窗口，以查看正执行的测试及其当前状态。

## <a name="writing-new-tests"></a>编写新测试

NUnitLite 是 [Touch.Unit](https://github.com/xamarin/Touch.Unit) 项目的 NUnit 修改版本。 它是一个用于 .NET 的轻量级测试框架，以 [NUnit](http://nunit.com/) 中的概念为基础，并提供其中的部分功能。
该框架使用的资源极少并可在资源受限的平台（例如嵌入式开发和移动开发所用的平台）上运行。 Xamarin.iOS 中为你提供了 NUnitLite API。 对于此单元测试模板所提供的基本主干，主入口点应为 [Assert 类](xref:NUnit.Framework.Assert)方法。

除 assert 类方法外，还会在 NUnitLite 包含的以下命名空间上拆分单元测试功能：

- [NUnit.Framework](xref:NUnit.Framework)
- [NUnit.Constraints](xref:NUnit.Framework.Constraints)
- [NUniteLite.Runner](xref:NUnitLite.Runner)
