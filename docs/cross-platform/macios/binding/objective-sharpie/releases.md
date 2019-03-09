---
title: 目标 Sharpie 发行历史记录
description: 本文档介绍了目标 Sharpie，用来自动创建的工具的版本历史记录C#绑定到 OBJECTIVE-C 代码。
ms.prod: xamarin
ms.assetid: 1F4A1BE1-7205-43F4-89D0-6C8672F52598
author: asb3993
ms.author: amburns
ms.date: 10/11/2017
ms.openlocfilehash: 03e4a5ac8906d2593cbdf3c15f6b2d1f4a2c6d19
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57667252"
---
# <a name="objective-sharpie-release-history"></a>目标 Sharpie 发行历史记录

## <a name="34-october-11-2017"></a>3.4 (2017 年 10 月 11 日)

[下载 v3.4.0](https://dl.xamarin.com/objective-sharpie/ObjectiveSharpie-3.4.0.pkg)

* 支持 Xcode 9: iOS 11，macOS 10.13，11，tvOS 和 watchOS 4
* 现在应修复单指令多数据和 tgmath 问题
* 已完全删除遥测数据

## <a name="33-august-3-2016"></a>3.3 (2016 年 8 月 3 日)

[下载 v3.3.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.3.0.pkg)

* 支持以 Xcode 8 Beta 4，iOS 10、 macOS 10.12、 tvOS 10 和 watchOS 3。
* 更新为最新 Clang 做主版本 (2016年-08-02)
* [保留遥测提交选项](https://twitter.com/Symbiatch/status/760373403878559744)从`sharpie pod bind`到`sharpie bind`。

## <a name="32-june-14-2016"></a>3.2 (2016 年 6 月 14 日)

[下载 v3.2.3](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.2.3.pkg)

* 支持以 Xcode 8 Beta 1、 iOS 10、 macOS 10.12、 tvOS 10 和 watchOS 3。

## <a name="31-may-31-2016"></a>3.1 （2016 年 5 月 31）

[下载 v3.1.1](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.1.1.pkg)

* CocoaPods 1.0 的支持
* 通过第一个构建本机改进 CocoaPods 绑定可靠性`.framework`，然后将该绑定
* 复制 CocoaPods`.framework`并将绑定到定义`Binding`目录来简化与 Xamarin.iOS 和 Xamarin.Mac 绑定项目的集成

## <a name="30-october-5-2015"></a>3.0 (2015 年 10 月 5 日)

[下载 v3.0.8](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.0.8.pkg)

* 对包括轻型泛型和为 null 性，Xcode 7 中引入的新 Objective C 语言功能的支持
* 支持最新的 iOS 和 Mac Sdk。
* 构建和分析支持的 Xcode 项目 ！ 现在，你可以将完整的 Xcode 项目传递到目标 Sharpie 以及它将做了最大努力找出最右侧的操作 (例如`sharpie bind Project.xcodeproj -sdk ios`)。
* [CocoaPods](https://cocoapods.org)支持 ！ 现在可以配置、 构建和直接从目标 Sharpie 绑定 CocoaPods (例如`sharpie pod init ios AFNetworking && sharpie pod bind`)。
* 如果框架支持它们，从而导致最正确分析一个框架，因为框架结构定义的绑定时框架，将启用 Clang 模块及其`module.modulemap`。
* 对于生成 framework 产品的 Xcode 项目，分析该产品而不是中间产品目标，因为在 Xcode 项目中的非框架目标可能仍具有二义性且不能为自动解决。

## <a name="216-march-17-2015"></a>2.1.6 (2015 年 3 月 17日日)

[下载 v2.1.6](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.1.6.pkg)

* 固定的二进制运算符表达式绑定： 表达式的左侧被错误地与其右侧 (例如`1 << 0`错误地被绑定为`0 << 1`)。 感谢您到 Adam Kemp 的注意到这 ！
* 修复了`NSInteger`并`NSUInteger`绑定为`int`并`uint`而不是`nint`和`nuint`上 i386;`-DNS_BUILD_32_LIKE_64`现在被传递到 Clang 使分析`objc/NSObjCRuntime.h`i386 上按预期运行。
* Mac OS X Sdk 的默认体系结构 (例如`-sdk macosx10.10`) 是现在 x86_64 而不是 i386，那么`-arch`除非需要重写默认值，则可以省略。

## <a name="210-march-15-2015"></a>2.1.0 (2015 年 3 月 15日日)

[下载 v2.1.0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.1.0.pkg)

* [bxc#27849](https://bugzilla.xamarin.com/show_bug.cgi?id=27849):请确保`using ObjCRuntime;`时，将生成`ArgumentSemantic`使用。
* [bxc#27850](https://bugzilla.xamarin.com/show_bug.cgi?id=27850):请确保`using System.Runtime.InteropServices;`时，将生成`DllImport`使用。
* [bxc#27852](https://bugzilla.xamarin.com/show_bug.cgi?id=27852):默认值`DllImport`加载已从符号`__Internal`。
* [bxc#27848](https://bugzilla.xamarin.com/show_bug.cgi?id=27848):跳过前向声明 Objective C 容器声明。
* [bxc#27846](https://bugzilla.xamarin.com/show_bug.cgi?id=27846):绑定协议与作为具体接口的单一限定的类型 (`id<Foo>`作为`Foo`而不是`Foundation.NSObject<Foo>`)。
* [bxc#28037](https://bugzilla.xamarin.com/show_bug.cgi?id=28037):将绑定`UInt32`， `UInt64`，并`Int64`文字作为`Int32`若要删除`u`和/或`uL`后缀时这些值可以安全地适合于`Int32`。
* [bxc#28038](https://bugzilla.xamarin.com/show_bug.cgi?id=28038):修复枚举的名称映射，在原始的本机名称开头`k`前缀。
* `sizeof` 其参数类型不映射到 C 表达式C#将计算在 Clang 中基元类型，并将其绑定为整数文字，以避免产生无效C#。
* 修复的属性的类型是一个块的 Objective C 语法 （Objective C 代码绑定声明上方的注释中会显示）。
* 绑定发生衰变类型为其原始类型 (`int[]`到衰减`int*`期间在 Clang 中的语义分析，但将其绑定与作为写入原始`int[]`相反)。

用于报告在此点版本中修复的 bug 的许多 Dave Dunkin 到很大程度，谢谢 ！

## <a name="200-march-9-2015"></a>2.0.0:2015 年 3 月 9日日

[下载 2.0.0 版](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.0.0.pkg)

目标 Sharpie 2.0 是改进的基于 Clang 的驱动程序和分析器和新的基于 NRefactory 绑定引擎功能的主要版本。 这些改进的组件还提供有关_很多_更好的绑定，特别是围绕类型绑定。 已进行内部目标 Sharpie 其会生成许多用户可见功能，未来版本中的许多其他改进。

目标 Sharpie 2.0 基于 Clang 3.6.1。

### <a name="type-binding-improvements"></a>类型绑定的改进

* 现在支持 OBJECTIVE-C 的块。 这包括匿名/内联块和块通过名为`typedef`。 匿名块将作为绑定`System.Action`或`System.Func`委托，而将作为强命名绑定命名的块`delegate`类型。

* 没有改进命名启发式算法对后面紧跟的匿名枚举`typedef`如解析为内置整型`long`或`int`。

* C 指针现在绑定为C#`unsafe`而不是指针`System.IntPtr`。 这会导致更高时你可能想要启用到指针参数的绑定中的清晰度`out`或`ref`参数。 不能始终推断是否应为参数`out`或`ref`，因此指针保留在要更轻松审核允许的绑定。

* 上述的指针绑定的一个例外是指向 OBJECTIVE-C 对象的 2 排名遇到作为参数时。 在这些情况下，约定是占主导地位，将作为绑定参数`out`(例如`NSError **error`→ `out NSError error`)。

### <a name="verify-attribute"></a>验证特性。

通常会发现生成的目标 Sharpie 绑定现在将使用批注`[Verify]`属性。 这些属性指示你应_验证_目标 Sharpie 通过比较与原始 C/Objective C 声明 （它将在绑定声明上方的注释中提供） 的绑定未正确的做法。

验证_建议_所有绑定声明，但很可能是_必需_为声明使用批注`[Verify]`属性。 这是因为在许多情况下，没有足够的元数据中原始的本机源代码来推断如何最好地生成一个绑定。 您可能需要引用文档或要做出最佳的绑定决策的标头文件中的代码注释。

请参阅[验证属性](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md)文档了解详细信息。

### <a name="other-notable-improvements"></a>其他值得注意的改进

* `using` 现在，基于绑定的类型生成语句。 例如，如果`NSURL`绑定，`using Foundation;`还将生成语句。

* `struct` 并`union`声明现在，将绑定使用`[FieldOffset]`联合的技巧。

* 枚举值的常量表达式初始值设定项现在能正确地绑定;完整的表达式将转换为C#。

* 现在绑定可变参数的方法和块。

* 框架现在支持通过`-framework`选项。 在查看文档[绑定本机框架](https://developer.xamarin.com/guides/ios/advanced_topics/binding_objective-c/objective_sharpie/#frameworks)的更多详细信息。

* Objective C 源的代码将自动检测到现在，这应无需传递`-ObjC`或`-xobjective-c`来手动 Clang。

* Clang 模块使用情况 (`@import`) 现已自动检测到其应无需传递`-fmodules`到 Clang 手动为在 Clang 中使用新的模块支持的库。

* Xamarin 统一 API 现在是默认绑定目标;使用`-classic`面向 32 位仅经典 API 的选项。

### <a name="notable-bug-fixes"></a>值得注意的 bug 修复

* 修复`instancetype`绑定时使用在 Objective C 的类别
* 完全名称 Objective C 类别
* 前缀包含协议的 Objective C `I` (例如`INSCopying`而不是`NSCopying`)

## <a name="1135-december-21-2014"></a>1.1.35:2014 年 12 月 21日日

[下载 v1.1.35](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-1.1.35.pkg)

小 bug 修复。

## <a name="111-december-15-2014"></a>1.1.1:2014 年 12 月 15日日

[下载 v1.1.1](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-1.1.1.pkg)

1.1.1 是内部使用情况和在 2013 年 4 月按照目标 Sharpie 的初始预览版的 Xamarin 开发的 1.5 年后的第一个主版本。 此版本是第一个通常被视为稳定和可用的各种本机库，具有一个新的 Clang 后端。

