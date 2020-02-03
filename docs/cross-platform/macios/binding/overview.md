---
title: 目标-C 绑定概述
description: 本文档概述了创建C#目标 C 代码的绑定的不同方法，包括命令行绑定、绑定项目和目标 Sharpie。 还介绍了绑定的工作原理。
ms.prod: xamarin
ms.assetid: 9EE288C5-8952-C5A9-E542-0BD847300EC6
author: davidortinau
ms.author: daortin
ms.date: 11/25/2015
ms.openlocfilehash: be2f7f555b76d472f7a66d95e661bb2f5884c58f
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76725339"
---
# <a name="overview-of-objective-c-bindings"></a>目标-C 绑定概述

_绑定过程工作原理的详细信息_

绑定与 Xamarin 一起使用的目标-C 库的过程分为三个步骤：

1. 编写C# "API 定义" 来说明如何在 .net 中公开本机 API，以及如何将其映射到基础目标-C。 这是使用标准C#构造（如 `interface` 和各种绑定**属性**）完成的（请参阅这个简单的[示例](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_an_API)）。

2. 在中C#编写 "API 定义" 后，可对其进行编译以生成 "绑定" 程序集。 这可以在[**命令行**](#commandline)上或在 Visual Studio for Mac 或 Visual Studio 中使用[**绑定项目**](#bindingproject)完成。

3. 然后，该程序集将添加到你的 Xamarin 应用程序项目中，因此你可以使用你定义的 API 来访问本机功能。
   绑定项目与应用程序项目完全不同。

   > [!NOTE]
   > 步骤1可以通过[**目标 Sharpie**](#objectivesharpie)帮助自动完成。 它会检查目标-C API 并生成建议C#的 "API 定义"。 您可以自定义目标 Sharpie 创建的文件，并在绑定项目（或命令行）中使用它们来创建绑定程序集。 客观 Sharpie 不会自行创建绑定，它仅仅是更大的过程的可选部分。

你还可以阅读更多有关[其工作](#howitworks)原理的技术详细信息，这将帮助你编写绑定。

<a name="Command_Line_Bindings" /><a name="commandline" />

## <a name="command-line-bindings"></a>命令行绑定

你可以使用 Xamarin 的 `btouch-native` （如果你使用的是 Xamarin，则 `bmac-native`）来直接生成绑定。 它的工作方式是C#将你手动创建的 API 定义（或使用客观 Sharpie）传递到命令行工具（适用于 iOS 的`btouch-native` 或 Mac 的 `bmac-native`）。

调用这些工具的一般语法为：

```csharp
# Use this for Xamarin.iOS:
bash$ /Developer/MonoTouch/usr/bin/btouch-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

```csharp
# Use this for Xamarin.Mac:
bash$ bmac-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

上面的命令将在当前目录中生成 `cocos2d.dll` 文件，并且它将包含可以在项目中使用的完全绑定库。 如果使用绑定项目（[如下](#bindingproject)所述），这是 Visual Studio for Mac 用于创建绑定的工具。

<a name="bindingproject" />

## <a name="binding-project"></a>绑定项目

可以在 Visual Studio for Mac 或 Visual Studio 中创建绑定项目（Visual Studio 仅支持 iOS 绑定），并使你可以更轻松地编辑和生成绑定的 API 定义（而不是使用命令行）。

遵循本[入门指南](~/cross-platform/macios/binding/objective-c-libraries.md#Getting_Started)，了解如何创建和使用绑定项目以生成绑定。

<a name="objectivesharpie" />

## <a name="objective-sharpie"></a>目标 Sharpie

客观 Sharpie 是另一种独立的命令行工具，可帮助创建绑定的初始阶段。 它不会自行创建绑定，而是自动执行为目标本机库生成 API 定义的初始步骤。

阅读[目标 Sharpie 文档](~/cross-platform/macios/binding/objective-sharpie/index.md)，了解如何将本机库、本机框架和 CocoaPods 分析为可内置到绑定中的 API 定义。

<a name="howitworks" />

## <a name="how-binding-works"></a>绑定的工作原理

可以同时使用[[Register]](xref:Foundation.RegisterAttribute)特性、 [[Export]](xref:Foundation.ExportAttribute)特性和[手动目标 C 选择器调用](~/ios/internals/objective-c-selectors.md)来手动绑定新的（以前未绑定的）目标 c 类型。

首先，查找要绑定的类型。 为了方便讨论，我们将绑定[NSEnumerator](https://developer.apple.com/documentation/foundation/nsenumerator)类型（已在[NSEnumerator](xref:Foundation.NSEnumerator)中绑定）; 下面的实现仅用于示例。

其次，我们需要创建C#类型。 我们可能想要将其放入命名空间;由于目标-C 不支持命名空间，因此需要使用 `[Register]` 特性来更改 Xamarin 将向目标 C 运行时注册的类型名称。 该C#类型还必须从[NSObject](xref:Foundation.NSObject)继承：

```csharp
namespace Example.Binding {
    [Register("NSEnumerator")]
    class NSEnumerator : NSObject
    {
        // see steps 3-5
    }
}
```

第三，查看目标-C 文档，并为要使用的每个选择器创建[ObjCRuntime](xref:ObjCRuntime.Selector)实例。 将它们放在类体中：

```csharp
static Selector selInit       = new Selector("init");
static Selector selAllObjects = new Selector("allObjects");
static Selector selNextObject = new Selector("nextObject");
```

第四，你的类型将需要提供构造函数。 您*必须*将构造函数调用链接到基类构造函数。 `[Export]` 特性允许目标 C 代码调用具有指定选择器名称的构造函数：

```csharp
[Export("init")]
public NSEnumerator()
    : base(NSObjectFlag.Empty)
{
    Handle = Messaging.IntPtr_objc_msgSend(this.Handle, selInit.Handle);
}
```

```csharp
// This constructor must be present so that Xamarin.iOS
// can create instances of your type from Objective-C code.
public NSEnumerator(IntPtr handle)
    : base(handle)
{
}
```

第五，为步骤3中声明的每个选择器提供方法。 这些将使用 `objc_msgSend()` 来调用本机对象的选择器。 请注意， [GetNSObject （）](xref:ObjCRuntime.Runtime.GetNSObject*)用于将 `IntPtr` 转换为适当类型的 `NSObject` （子）类型。 如果要从目标-C 代码调用方法，则成员*必须*是**虚拟**的。

```csharp
[Export("nextObject")]
public virtual NSObject NextObject()
{
    return Runtime.GetNSObject(
        Messaging.IntPtr_objc_msgSend(this.Handle, selNextObject.Handle));
}
```

```csharp
// Note that for properties, [Export] goes on the get/set method:
public virtual NSArray AllObjects {
    [Export("allObjects")]
    get {
        return (NSArray) Runtime.GetNSObject(
            Messaging.IntPtr_objc_msgSend(this.Handle, selAllObjects.Handle));
    }
}
```

将其全部放在一起：

```csharp
using System;
using Foundation;
using ObjCRuntime;

namespace Example.Binding {
    [Register("NSEnumerator")]
    class NSEnumerator : NSObject
    {
        static Selector selInit       = new Selector("init");
        static Selector selAllObjects = new Selector("allObjects");
        static Selector selNextObject = new Selector("nextObject");

        [Export("init")]
        public NSEnumerator()
            : base(NSObjectFlag.Empty)
        {
            Handle = Messaging.IntPtr_objc_msgSend(this.Handle,
                selInit.Handle);
        }

        public NSEnumerator(IntPtr handle)
            : base(handle)
        {
        }

        [Export("nextObject")]
        public virtual NSObject NextObject()
        {
            return Runtime.GetNSObject(
                Messaging.IntPtr_objc_msgSend(this.Handle,
                    selNextObject.Handle));
        }

        // Note that for properties, [Export] goes on the get/set method:
        public virtual NSArray AllObjects {
            [Export("allObjects")]
            get {
                return (NSArray) Runtime.GetNSObject(
                    Messaging.IntPtr_objc_msgSend(this.Handle,
                        selAllObjects.Handle));
            }
        }
    }
}
```
