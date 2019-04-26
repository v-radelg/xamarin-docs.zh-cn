---
title: 在 Xamarin.iOS.xib 代码生成
description: 本文档介绍了如何为 Xamarin.iOS 生成代码映射到的.xib 文件C#，使 visual 控件以编程方式访问。
ms.prod: xamarin
ms.assetid: 365991A8-E07A-0420-D28E-BC4D32065E1A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 57988374b4383f5659e29edff3834958b8f99f1b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61035893"
---
# <a name="xib-code-generation-in-xamarinios"></a>在 Xamarin.iOS.xib 代码生成

> [!IMPORTANT]
>  本文档介绍 Visual Studio 用于 Mac 的集成使用 Xcode 的 Interface Builder 仅，如操作和输出口未使用 Xamarin 设计器中的 iOS。 IOS 设计器的详细信息，请查看[iOS 设计器](~/ios/user-interface/designer/index.md)文档。

Apple Interface Builder 工具 ("IB") 可用于以可视方式设计用户界面。 创建的 IB 接口定义保存在 **.xib**文件。 小组件和中的其他对象 **.xib**文件可能会获得一些"类标识"，可以是自定义用户定义类型。 这允许您自定义小组件的行为和编写自定义小组件。

这些用户类通常是用户界面控制器类的子类。 它们具有*输出口*（类似于属性） 和*操作*（类似于事件），可以连接到接口的对象。 在运行时，加载 IB 文件时，创建的对象，并输出口和操作连接到各种 UI 对象动态。 在定义这些托管的类时，必须定义所有的操作和输出口以与 IB 要求相匹配。 Visual Studio for Mac 使用类似于代码隐藏文件的模型来简化此操作。 这是类似于 Xcode 功能适用于 OBJECTIVE-C 的但代码生成模型和约定具有已调整为更熟悉的.NET 开发人员。

使用 **.xib**文件当前不支持在 Xamarin.iOS for Visual Studio。

## <a name="xib-files-and-custom-classes"></a>.xib 文件和自定义类

以及使用 Cocoa Touch 从现有类型，则可以定义自定义类型 **.xib**文件。 还有可能要使用其他定义的类型 **.xib**文件，或仅在定义C#代码。 目前，Interface Builder 并不知道当前的外部定义的类型的详细信息 **.xib**文件，因此它不会列出这些或显示其自定义输出口和操作。 删除此限制已计划在将来一段时间。

可以在中定义的自定义类 **.xib**文件使用 Interface Builder"类"选项卡中的"添加子类"命令。 我们之所以将它们作为"代码隐藏"类。 如果 **.xib**文件具有"。 xib.designer.cs"项目，然后 Visual Studio for Mac 中的对应文件便会自动填充它的中的所有自定义类的分部类定义 **.xib**。 我们之所以将这些部分类称为"类设计器"。

## <a name="generating-code"></a>生成代码

对于任何 **{0}.xib**文件的生成操作*页*，如果 **{0}。 xib.designer.cs**文件也存在在项目中，Visual Studio for Mac将生成分部类，它可以在中找到的所有用户类设计器文件中 **.xib**文件，并且属性输出口和操作的所有分部方法。 只需通过存在此文件启用代码生成。

设计器文件会自动更新时 **.xib**文件更改和 Visual Studio Mac 重新获得焦点。 设计器文件在如更改都将覆盖下一次 Visual Studio Mac 更新该文件不应手动修改。

## <a name="registration-and-namespaces"></a>注册和命名空间

Visual Studio for Mac 生成使用设计器文件位置的项目的默认命名空间以使其与普通的.NET 项目 namespacing 一致的设计器类。 设计器文件的命名空间取决于项目的"默认命名空间"和其".NET 命名策略"设置。 请注意，是否你的项目的默认命名空间更改，MD 将再生成新的命名空间中的类因此可能会发现，分部类不再匹配。

若要使类可检测到通过 Objective C 运行时，Visual Studio for Mac 应用`[Register (name)]`到类属性。 尽管 Xamarin.iOS 会自动注册`NSObject`的派生类中，它使用完全限定的.NET 名称。 Mac 重写此设置以确保每个类由 Visual Studio 应用该属性注册中使用的名称与 **.xib**文件。 如果在 IB 中使用自定义类，而无需使用 Visual Studio for Mac 生成设计器文件，您可能需要将此手动以使您与预期的 Objective C 类名称匹配的托管的类。

不能在多个中定义的类 **.xib**，或它们将会发生冲突。

## <a name="non-designer-class-parts"></a>非设计器类部件

设计器分部类不应为要使用的是。 带的容器是专用的并指定没有基类。 预计每个类将在设置的基类，使用或公开输出口，并定义所需加载时实例化从本机代码的类的构造函数的另一个文件中具有相应的"非设计器"类部件 **.xib**。 默认值 **.xib**模板执行此操作，但定义中的任何其他自定义类 **.xib**，您必须手动添加非设计器部分。

这样做的原因是灵活性的需要。 例如，多个代码隐藏类可以一种常见管理抽象类，其中子类 IB 通过子类化的类的子类。

它是常规将这些 **{0}。 xib.cs**文件旁边 **{0}。 xib.designer.cs**设计器文件。

<a name="generated" />

## <a name="generated-actions-and-outlets"></a>生成的操作和输出口

中的部分的设计器类，Visual Studio for Mac 生成到 IB 和任何已连接的操作相对应的分部方法中定义的任何连接 outlet 相对应的属性。

### <a name="outlet-properties"></a>输出口属性

设计器类包含与带自定义类上定义的所有容器相对应的属性。 这些是属性的事实是实现详细信息的 Objective C 桥接，Xamarin.iOS，以启用延迟绑定。 应将其视为等效于私有字段，应使用只能从代码隐藏类。 如果你想要使其成为公共，将添加到非设计器类部件，取值函数属性，根据需要对任何其他私有字段。

如果电源插座属性被定义为具有一种`id`(等效于`NSObject`) 然后设计器代码生成器当前确定根据对象连接到该电源插座，为方便起见，最强的可能类型。
但是，这可能不支持在将来版本中，因此建议定义自定义类时显式强键入输出口。

### <a name="action-properties"></a>操作属性

设计器类包含分部方法对应于定义的自定义类上的所有操作。 这些是不包含实现的方法。 分部方法的目的是双重的：

1.  如果键入`partial`在非设计器类部件的类正文中，Visual Studio for Mac 将提供自动完成的所有非实现分部方法签名。
2.  分部方法签名具有将其公开到 OBJECTIVE-C 的世界中，以便获取作为相应的操作处理这些属性应用。


如果您愿意，可能忽略分部方法，并将特性应用到一个不同的方法，实现的操作或让它贯穿到基类。

如果定义操作的类型为发件人`id`(等效于`NSObject`)，然后设计器代码生成器当前确定基于连接到该操作的对象的最强的可能类型。 但是，这可能不支持在将来版本中，因此建议定义自定义类时显式强类型的操作。

请注意，这些分部方法创建仅用于C#，这是因为 CodeDOM 不支持分部方法，因此它们不生成对于其他语言。

## <a name="cross-xib-class-usage"></a>跨 XIB 类使用情况

有时，用户希望从多个引用同一个类 **.xib**文件，例如，使用选项卡上的控制器。 这可以通过从另一个引用类定义的显式 **.xib**文件，或通过在第二个定义相同的类名 **.xib**。

后一种情况可能会出现由于 Visual Studio 进行 Mac 处理问题 **.xib**单独文件。 它不能自动检测并合并重复的定义，因此您可能采用了将应用注册属性多次时相同的分部类定义在设计器的多个文件中的冲突。 最新版本的 Visual Studio for Mac 尝试解决此问题，但它可能无法始终按预期工作。 这在将来很可能不受支持，并改为 Visual Studio for Mac 将在所有中定义的所有类型 **.xib**文件和托管的代码中直接显示从所有项目 **.xib**文件。

## <a name="type-resolution"></a>类型解析

IB 中使用的类型是 OBJECTIVE-C 的类型名称。 它们被映射到 CLR 类型通过使用寄存器特性。 在生成时输出口和操作代码，Visual Studio for Mac 将解析所有包装 Xamarin.iOS 核心的 Objective C 类型相应的 CLR 类型和完全限定其类型名称。

但是，代码生成器目前不能解析 Objective C 类型名称，在用户代码或库中的 CLR 类型，因此在这种情况下它将输出逐字字符串的类型名称。 这意味着相应的 CLR 类型必须具有相同的名称为 Objective C 类型而且必须在相同的命名空间正在使用它的代码。 计划要修复一段时间在将来通过在代码生成过程中考虑项目中的所有 Objective C 类型。
