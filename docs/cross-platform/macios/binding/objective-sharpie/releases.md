---
title: 客观 Sharpie 发行历史记录
description: 本文档介绍客观 Sharpie 的发布历史记录，该工具用于自动创建到目标- C# C 代码的绑定。
ms.prod: xamarin
ms.assetid: 1F4A1BE1-7205-43F4-89D0-6C8672F52598
author: davidortinau
ms.author: daortin
ms.date: 10/11/2017
ms.openlocfilehash: f60be3f7dc14749f5cd58d5228c17fa85282cd78
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76725348"
---
# <a name="objective-sharpie-release-history"></a>客观 Sharpie 发行历史记录

## <a name="34-october-11-2017"></a>3.4 （10月11日，2017）

[下载3.4。0](https://dl.xamarin.com/objective-sharpie/ObjectiveSharpie-3.4.0.pkg)

* 支持 Xcode 9： iOS 11、macOS 10.13、tvOS 11 和 watchOS 4
* 现在应修复 SIMD 和 t h. 的问题
* 已完全删除遥测

## <a name="33-august-3-2016"></a>3.3 （8月3日，2016）

[下载3.3。0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.3.0.pkg)

* 支持 Xcode 8 Beta 4、iOS 10、macOS 10.12、tvOS 10 和 watchOS 3。
* 已更新到最新的 Clang 主版本（2016-08-02）
* 将[遥测提交选项](https://twitter.com/Symbiatch/status/760373403878559744)从 `sharpie pod bind` 保存到 `sharpie bind`。

## <a name="32-june-14-2016"></a>3.2 （6月 14 2016 日）

[下载3.2。3](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.2.3.pkg)

* 支持 Xcode 8 Beta 1、iOS 10、macOS 10.12、tvOS 10 和 watchOS 3。

## <a name="31-may-31-2016"></a>3.1 （5月31日，2016）

[下载3.1。1](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.1.1.pkg)

* 支持 CocoaPods 1。0
* 通过首先构建本机 `.framework` 并将其绑定，提高了 CocoaPods 绑定的可靠性
* 将 CocoaPods `.framework` 和绑定定义复制到 `Binding` 目录中，以便更轻松地与 Xamarin 和 Xamarin 绑定项目集成

## <a name="30-october-5-2015"></a>3.0 （2015年10月5日）

[下载 rdbms-3.0。8](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.0.8.pkg)

* 支持新的目标-C 语言功能，包括轻型泛型和可为 null 性，如 Xcode 7 中所述
* 支持最新的 iOS 和 Mac Sdk。
* Xcode 项目生成和分析支持！ 现在，你可以将一个完整的 Xcode 项目传递给目标 Sharpie，它将尽力确定要执行的操作（例如 `sharpie bind Project.xcodeproj -sdk ios`）。
* [CocoaPods](https://cocoapods.org)支持！ 你现在可以直接从目标 Sharpie （例如 `sharpie pod init ios AFNetworking && sharpie pod bind`）配置、生成和绑定 CocoaPods。
* 绑定框架时，如果框架支持 Clang 模块，将启用这些模块，从而最正确地分析框架，因为框架的结构由其 `module.modulemap`定义。
* 对于构建框架产品的 Xcode 项目，在 Xcode 项目中分析该产品而不是作为非框架目标的中间产品目标可能仍具有不能自动解决的歧义。

## <a name="216-march-17-2015"></a>2.1.6 （2015年3月17日）

* 修复了二元运算符表达式绑定：表达式的左侧未正确地与右边缘交换（例如，`1 << 0` 未正确绑定为 `0 << 1`）。 感谢 Adam Kemp 注意到这一点！
* 修复了 `NSInteger` 和 `NSUInteger` 绑定为 `int` 和 `uint` 而不是在 i386 上 `nint` 和 `nuint` 的问题;现在 `-DNS_BUILD_32_LIKE_64` 传递给 Clang，以便分析 `objc/NSObjCRuntime.h` 在 i386 上按预期方式工作。
* Mac OS X Sdk （如 `-sdk macosx10.10`）的默认体系结构现在 x86_64 而不是 i386，因此可以省略 `-arch`，除非需要替代默认值。

## <a name="210-march-15-2015"></a>2.1.0 （2015年3月15日）

[下载2.1。0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.1.0.pkg)

* [bxc # 27849](https://bugzilla.xamarin.com/show_bug.cgi?id=27849)：确保在使用 `ArgumentSemantic` 时生成 `using ObjCRuntime;`。
* [bxc # 27850](https://bugzilla.xamarin.com/show_bug.cgi?id=27850)：确保在使用 `DllImport` 时生成 `using System.Runtime.InteropServices;`。
* [bxc # 27852](https://bugzilla.xamarin.com/show_bug.cgi?id=27852)：默认 `DllImport` 从 `__Internal`加载符号。
* [bxc # 27848](https://bugzilla.xamarin.com/show_bug.cgi?id=27848)：跳过前向声明的目标-C 容器声明。
* [bxc # 27846](https://bugzilla.xamarin.com/show_bug.cgi?id=27846)：将协议类型作为具体接口绑定到单个限定（`id<Foo>` 为 `Foo`，而不是 `Foundation.NSObject<Foo>`）。
* [bxc # 28037](https://bugzilla.xamarin.com/show_bug.cgi?id=28037)：将 `UInt32`、`UInt64`和 `Int64` 文本作为 `Int32` 绑定，以便在可以安全地适应 `u` 时删除 `uL` 和/或 `Int32`后缀。
* [bxc # 28038](https://bugzilla.xamarin.com/show_bug.cgi?id=28038)：当原始本地名称以 `k` 前缀开头时修复枚举名称映射。
* 在 Clang 中，将在中计算参数类型C#不映射到基元类型的 C 表达式，并将其绑定为整数文本以C#避免生成无效的。 `sizeof`
* 为类型为块的属性（目标-C 代码出现在绑定声明的注释中）修复目标 C 语法。
* 将 decayed 类型绑定为其原始类型（`int[]` decays，以在 Clang 中的语义分析期间进行 `int*`，但将其绑定为替代 `int[]` 的原始类型）。

感谢您 Dave Dunkin，以便报告在此版本中修复的许多 bug！

## <a name="200-march-9-2015"></a>2.0.0：2015年3月9日

[下载2.0。0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.0.0.pkg)

客观 Sharpie 2.0 是一个主要版本，它具有改进的基于 Clang 的驱动程序和分析器以及基于 NRefactory 的新绑定引擎。 这些改进的组件提供_更好的_绑定，特别是围绕类型绑定。 在目标 Sharpie 中进行了许多其他改进，这会在将来的版本中产生许多用户可见的功能。

目标 Sharpie 2.0 基于 Clang 3.6.1。

### <a name="type-binding-improvements"></a>类型绑定改进

* 目标-C 块现在受支持。 这包括匿名/内联块和通过 `typedef`命名的块。 匿名块将绑定为 `System.Action` 或 `System.Func` 委托，命名块将绑定为强名称 `delegate` 类型。

* 对于匿名枚举，有一种更好的命名试探法，它们前面的 `typedef` 解析为内置整数类型，如 `long` 或 `int`。

* 现在，C 指针绑定为C# `unsafe` 指针，而不是 `System.IntPtr`。 当你可能想要将指针参数转换为 `out` 或 `ref` 参数时，这会使绑定更清晰。 不能始终推断参数是应 `out` 还是 `ref`，因此，指针保留在绑定中，以便更容易审核。

* 当遇到作为参数的目标 C 对象的2排名指针时，上述指针绑定是一个例外。 在这些情况下，约定为 "主要"，参数将绑定 `out` （例如 `NSError **error` → `out NSError error`）。

### <a name="verify-attribute"></a>验证属性

你经常会发现，客观 Sharpie 生成的绑定现在将使用 `[Verify]` 属性进行批注。 这些属性指示您应该通过将绑定与原始 C/目标-C 声明（将在绑定声明中的注释中提供）进行比较来_验证_目标 Sharpie 是否执行了正确的操作。

_建议_对所有绑定声明进行验证，但对于使用 `[Verify]` 属性进行批注的声明，这很可能是_必需_的。 这是因为在许多情况下，原始本机源代码中没有足够的元数据来推断如何最好地生成绑定。 你可能需要在标头文件中引用文档或代码注释以做出最佳的绑定决策。

有关更多详细信息，请参阅[验证属性](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md)文档。

### <a name="other-notable-improvements"></a>其他显著改进

* `using` 语句现在是根据绑定的类型生成的。 例如，如果绑定了 `NSURL`，则也会生成 `using Foundation;` 语句。

* 现在会使用联合的 `[FieldOffset]` 技巧来绑定 `struct` 和 `union` 声明。

* 具有常量表达式初始值设定项的枚举值现在将正确绑定;完整的表达式转换为C#。

* 现在绑定了可变参数方法和块。

* 现在，可以通过 `-framework` 选项来支持框架。 有关更多详细信息，请参阅关于[绑定本机框架](~/cross-platform/macios/binding/objective-sharpie/index.md)的文档。

* 现在会自动检测到目标-C 源代码，这应该无需将 `-ObjC` 或 `-xobjective-c` 手动传递到 Clang。

* Clang 模块用法（`@import`）现在是自动检测的，这应该无需将 `-fmodules` 手动传递到在 Clang 中使用新模块支持的库。

* Xamarin Unified API 现在为默认绑定目标;使用 `-classic` 选项仅面向32位 Classic API。

### <a name="notable-bug-fixes"></a>值得注意的 bug 修复

* 解决在目标-C 类别中使用的 `instancetype` 绑定
* 完全名称目标-C 类别
* 用 `I` （例如，`INSCopying` 而不是 `NSCopying`）为 C 协议前缀。

## <a name="1135-december-21-2014"></a>1.1.35：2014年12月21日

[下载1.1.35](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-1.1.35.pkg)

사소한 버그가 수정되었습니다.

## <a name="111-december-15-2014"></a>1.1.1：2014年12月15日

[下载版本1.1。1](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-1.1.1.pkg)

在 2005 2013 年4月的目标 Sharpie 的初始预览后，1.1.1 是1.5 年后的第一个主要发布。 这一版本通常被视为稳定的，可用于各种本机库，这是一个新的 Clang 后端。
