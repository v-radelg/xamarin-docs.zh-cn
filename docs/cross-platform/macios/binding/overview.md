---
title: OBJECTIVE-C 绑定的概述
description: 本文档概述的不同方式创建C#Objective C 代码，包括命令行绑定、 绑定项目和目标 Sharpie 的绑定。 它还讨论了绑定的工作原理。
ms.prod: xamarin
ms.assetid: 9EE288C5-8952-C5A9-E542-0BD847300EC6
author: asb3993
ms.author: amburns
ms.date: 11/25/2015
ms.openlocfilehash: d29239d986ebfe153381915dbe0f4bfbbe738007
ms.sourcegitcommit: 495680e74c72e7c570e68cde95d3d3643b1fcc8a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2019
ms.locfileid: "58870334"
---
# <a name="overview-of-objective-c-bindings"></a>OBJECTIVE-C 绑定的概述

_在绑定过程的工作原理的详细信息_

绑定 OBJECTIVE-C 的库为通过 Xamarin 使用采用三个步骤：

1. 编写C#"API 定义"来描述如何本机 API 公开在.NET 中，以及它如何映射到基础 Objective-c。 这是使用标准C#构造喜欢`interface`和各种绑定**特性**(请参阅此[简单示例](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_an_API))。

2. 一旦您编写"API 定义" C#，其生成"绑定"程序集进行编译。 这可以在[**命令行**](#commandline)也可以使用[**绑定项目**](#bindingproject) Visual Studio for Mac 或 Visual Studio 中。

3. 该"绑定"程序集然后添加到 Xamarin 应用程序项目中，以便可以访问使用你定义的 API 的本机功能。
  绑定项目是完全独立于应用程序项目。

**注意：** 可以使用的帮助自动执行步骤 1 [**目标 Sharpie**](#objectivesharpie)。 它会检查 Objective C API 并生成建议C#"API 定义"。 您可以自定义目标 Sharpie 创建的文件并将其绑定项目 （或在命令行上） 来创建绑定程序集。 目标 Sharpie 不会创建单独的绑定，而是只是较大过程的一个可选部分。

您还可以阅读更多技术细节[其工作原理](#howitworks)，这将帮助你编写您的绑定。

<a name="Command_Line_Bindings" /><a name="commandline" />

## <a name="command-line-bindings"></a>命令行绑定

可以使用`btouch-native`适用于 Xamarin.iOS (或`bmac-native`如果使用 Xamarin.Mac) 能够直接生成的绑定。 它的工作方式传递C#API 手动创建的定义 （或使用目标 Sharpie） 给命令行工具 (`btouch-native`适用于 iOS 或`bmac-native`for Mac)。


用于调用这些工具的常规语法是：

```csharp
# Use this for Xamarin.iOS:
bash$ /Developer/MonoTouch/usr/bin/btouch-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

```csharp
# Use this for Xamarin.Mac:
bash$ bmac-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

上述命令将生成文件`cocos2d.dll`当前目录中，并且它包含可以在项目中使用的完全绑定的库。 这是 Visual Studio for Mac 使用以创建您的绑定，如果您使用绑定项目的工具 (所述[如下](#bindingproject))。


<a name="bindingproject" />

## <a name="binding-project"></a>绑定项目

绑定项目可以创建在 Visual Studio for Mac 或 Visual Studio （Visual Studio 仅支持 iOS 绑定），并使其更轻松地编辑和生成 API 定义 （而不是使用命令行） 的绑定。

按照这[入门指南](~/cross-platform/macios/binding/objective-c-libraries.md#Getting_Started)若要了解如何创建和使用一个绑定项目以生成绑定。

<a name="objectivesharpie" />

## <a name="objective-sharpie"></a>目标 Sharpie

目标 Sharpie 是另一个单独的命令行工具，可帮助创建绑定的初始阶段使用。 它不会单独创建一个绑定，而不是它可以自动生成 API 定义目标本机库的初始步骤。

读取[目标 Sharpie docs](~/cross-platform/macios/binding/objective-sharpie/index.md)若要了解如何将本机库、 本机框架和 CocoaPods 解析为可以绑定到生成的 API 定义。

<a name="howitworks" />

## <a name="how-binding-works"></a>绑定的工作原理

可以使用[[注册]](xref:Foundation.RegisterAttribute)属性， [[导出]](xref:Foundation.ExportAttribute)属性，并[手动 OBJECTIVE-C 的选择器调用](~/ios/internals/objective-c-selectors.md)一起以手动绑定新 （以前未绑定） 的 Objective C 类型。

首先，找到你想要绑定的类型。 讨论目的 （以及简单起见），我们会将绑定[NSEnumerator](https://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSEnumerator_Class/Reference/Reference.html)类型 (其中已绑定中[Foundation.NSEnumerator](xref:Foundation.NSEnumerator); 下面的实现是只需例如目的)。

其次，我们需要创建C#类型。 我们可能想要将此置于一个命名空间;Objective C 不支持命名空间，因为我们将需要使用`[Register]`属性来更改 Xamarin.iOS 将注册到 OBJECTIVE-C 运行时类型名称。 C#类型还必须继承自[Foundation.NSObject](xref:Foundation.NSObject):

```csharp
namespace Example.Binding {
    [Register("NSEnumerator")]
    class NSEnumerator : NSObject
    {
        // see steps 3-5
    }
}
```

第三，查看 OBJECTIVE-C 文档并创建[ObjCRuntime.Selector](xref:ObjCRuntime.Selector)你想要使用每个选择器的实例。 将这些类正文中放置：

```csharp
static Selector selInit       = new Selector("init");
static Selector selAllObjects = new Selector("allObjects");
static Selector selNextObject = new Selector("nextObject");
```

第四，您的类型将需要提供构造函数。 您*必须*链接对基类构造函数将构造函数调用。 `[Export]`属性允许 Objective C 代码调用的构造函数使用指定的选择器名称：

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

第五步： 提供在步骤 3 中声明选择器的每个方法。 这些将使用`objc_msgSend()`要调用的本机对象上的选择器。 请注意，使用[Runtime.GetNSObject()](xref:ObjCRuntime.Runtime.GetNSObject*)转换`IntPtr`到相应的类型化`NSObject`（sub） 类型。 如果您希望可从 OBJECTIVE-C 代码，该成员调用的方法*必须*进行**虚拟**。

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

将所有组合在一起：

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

## <a name="related-links"></a>相关链接

- [Xamarin 大学课程：生成一个 Objective C 绑定库](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Xamarin 大学课程：生成与目标 Sharpie Objective C 绑定库](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)