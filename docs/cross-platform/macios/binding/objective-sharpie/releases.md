---
title: 客观 Sharpie 发行历史记录
description: 本文档介绍客观 Sharpie 的发布历史记录，该工具用于自动创建到目标- C# C 代码的绑定。
ms.prod: xamarin
ms.assetid: 1F4A1BE1-7205-43F4-89D0-6C8672F52598
author: conceptdev
ms.author: crdun
ms.date: 10/11/2017
ms.openlocfilehash: fa50ae16b69436936f0a7a8a5cf0aeaa54dfedfb
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70765672"
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
* 将[遥测提交选项](https://twitter.com/Symbiatch/status/760373403878559744)从`sharpie pod bind`保存`sharpie bind`到。

## <a name="32-june-14-2016"></a>3.2 （6月 14 2016 日）

[下载3.2。3](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.2.3.pkg)

* 支持 Xcode 8 Beta 1、iOS 10、macOS 10.12、tvOS 10 和 watchOS 3。

## <a name="31-may-31-2016"></a>3.1 （5月31日，2016）

[下载3.1。1](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.1.1.pkg)

* 支持 CocoaPods 1。0
* 通过首先构建本机`.framework` ，然后绑定，提高了 CocoaPods 绑定的可靠性
* 将 CocoaPods `.framework`和绑定定义复制到`Binding`一个目录中，以便更轻松地与 xamarin 和 xamarin 绑定项目集成

## <a name="30-october-5-2015"></a>3.0 （2015年10月5日）

[下载 rdbms-3.0。8](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-3.0.8.pkg)

* 支持新的目标-C 语言功能，包括轻型泛型和可为 null 性，如 Xcode 7 中所述
* 支持最新的 iOS 和 Mac Sdk。
* Xcode 项目生成和分析支持！ 现在，你可以将一个完整的 Xcode 项目传递给目标 Sharpie，它将尽力确定要执行的操作（例如`sharpie bind Project.xcodeproj -sdk ios`）。
* [CocoaPods](https://cocoapods.org)支持！ 你现在可以直接从目标 Sharpie （例如`sharpie pod init ios AFNetworking && sharpie pod bind`）中配置、生成和绑定 CocoaPods。
* 绑定框架时，如果框架支持 Clang 模块，将启用这些模块，从而最正确地分析框架，因为框架的结构由其`module.modulemap`定义。
* 对于构建框架产品的 Xcode 项目，在 Xcode 项目中分析该产品而不是作为非框架目标的中间产品目标可能仍具有不能自动解决的歧义。

## <a name="216-march-17-2015"></a>2.1.6 （2015年3月17日）

[下载2.1。6](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.1.6.pkg)

* 修复了二元运算符表达式绑定：表达式的左侧未正确地与右端交换（例如`1 << 0` ，未正确地绑定为`0 << 1`）。 感谢 Adam Kemp 注意到这一点！
* 修复了和绑定`NSInteger` `NSUInteger` `int` 为，`uint`而不是`nint` 在i386上绑定的问题;`nuint`现在会传递到 Clang，以便在 i386 上按预期方式进行分析`objc/NSObjCRuntime.h`。 `-DNS_BUILD_32_LIKE_64`
* Mac OS X sdk （例如`-sdk macosx10.10`）的默认体系结构现在是 x86_64 而不是 i386，因此`-arch`可以省略，除非需要替代默认值。

## <a name="210-march-15-2015"></a>2.1.0 （2015年3月15日）

[下载2.1。0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.1.0.pkg)

* [bxc#27849](https://bugzilla.xamarin.com/show_bug.cgi?id=27849):使用`using ObjCRuntime;` 时`ArgumentSemantic` ，请确保生成。
* [bxc#27850](https://bugzilla.xamarin.com/show_bug.cgi?id=27850):使用`using System.Runtime.InteropServices;` 时`DllImport` ，请确保生成。
* [bxc#27852](https://bugzilla.xamarin.com/show_bug.cgi?id=27852):默认`DllImport`为从`__Internal`加载符号。
* [bxc#27848](https://bugzilla.xamarin.com/show_bug.cgi?id=27848):跳过前向声明的目标-C 容器声明。
* [bxc#27846](https://bugzilla.xamarin.com/show_bug.cgi?id=27846):使用单个限定将协议类型绑定为具体接口（`id<Foo>` `Foo`而不是`Foundation.NSObject<Foo>`）。
* [bxc#28037](https://bugzilla.xamarin.com/show_bug.cgi?id=28037):当`UInt32`值`UInt64`可以安全`Int64`地适应`Int32` `u` 时，`uL`将、和文本绑定为以删除和/或后缀。 `Int32`
* [bxc#28038](https://bugzilla.xamarin.com/show_bug.cgi?id=28038):当原始本地名称以`k`前缀开头时，修复枚举名称映射。
* `sizeof`将在 Clang 中计算参数类型不映射为C#基元类型的 C 表达式，并将其绑定为整数文本，以避免生成C#无效的。
* 为类型为块的属性（目标-C 代码出现在绑定声明的注释中）修复目标 C 语法。
* 将 decayed 类型绑定为其原始类型`int[]` （在`int*` Clang 中语义分析期间将 decays 绑定到，但`int[]`改为将其绑定为原始类型）。

感谢您 Dave Dunkin，以便报告在此版本中修复的许多 bug！

## <a name="200-march-9-2015"></a>2.0.02015年3月9日

[下载2.0。0](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-2.0.0.pkg)

客观 Sharpie 2.0 是一个主要版本，它具有改进的基于 Clang 的驱动程序和分析器以及基于 NRefactory 的新绑定引擎。 这些改进的组件提供_更好的_绑定，特别是围绕类型绑定。 在目标 Sharpie 中进行了许多其他改进，这会在将来的版本中产生许多用户可见的功能。

目标 Sharpie 2.0 基于 Clang 3.6.1。

### <a name="type-binding-improvements"></a>类型绑定改进

* 目标-C 块现在受支持。 这包括匿名/内联块和通过`typedef`命名的块。 匿名块将绑定为`System.Action`或`System.Func`委托，而已命名的块将绑定为强命名`delegate`类型。

* 为匿名枚举提供了一个更好的命名试探法，这种`typedef`试探会立即解析为内置整数类型`long` ， `int`如或。

* 现在，C 指针绑定为C# `unsafe`指针，而`System.IntPtr`不是。 当你可能想要将指针参数转换为`out`或`ref`参数时，这会使绑定中的更清晰。 不能始终推断参数是`out`还是应为或`ref`，因此，指针保留在绑定中，以便更容易审核。

* 当遇到作为参数的目标 C 对象的2排名指针时，上述指针绑定是一个例外。 在这些情况下，约定为 "主要"，参数将绑定`out`为（ `NSError **error`例如→ `out NSError error`）。

### <a name="verify-attribute"></a>验证属性

你经常会发现，客观 Sharpie 生成的绑定现在将使用`[Verify]`属性进行批注。 这些属性指示您应该通过将绑定与原始 C/目标-C 声明（将在绑定声明中的注释中提供）进行比较来_验证_目标 Sharpie 是否执行了正确的操作。

_建议_对所有绑定声明进行验证，但对于使用`[Verify]`特性批注的声明，最有可能_需要_进行验证。 这是因为在许多情况下，原始本机源代码中没有足够的元数据来推断如何最好地生成绑定。 你可能需要在标头文件中引用文档或代码注释以做出最佳的绑定决策。

有关更多详细信息，请参阅[验证属性](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md)文档。

### <a name="other-notable-improvements"></a>其他显著改进

* `using`现在，语句是根据绑定的类型生成的。 例如，如果`NSURL`绑定了`using Foundation;` ，则也会生成语句。

* `struct`现在`union` ，将`[FieldOffset]`使用用于联合的技巧对和声明进行绑定。

* 具有常量表达式初始值设定项的枚举值现在将正确绑定;完整的表达式转换为C#。

* 现在绑定了可变参数方法和块。

* 现在， `-framework`可以通过选项来支持框架。 有关更多详细信息，请参阅关于[绑定本机框架](~/cross-platform/macios/binding/objective-sharpie/index.md)的文档。

* 现在，将自动检测到目标-C 源代码，这将不再需要通过`-ObjC`或`-xobjective-c`手动 Clang。

* Clang 模块用法（`@import`）现在是自动检测的，这应该无需为使用 Clang `-fmodules`中新模块支持的库手动传递到 Clang。

* Xamarin Unified API 现在为默认绑定目标;`-classic`使用选项仅针对32位 Classic API。

### <a name="notable-bug-fixes"></a>值得注意的 bug 修复

* 解决`instancetype`在目标-C 类别中使用的绑定问题
* 完全名称目标-C 类别
* 将目标为 C 协议`I` （ `INSCopying`例如，而不是`NSCopying`）

## <a name="1135-december-21-2014"></a>1.1.35:2014年12月21日

[下载1.1.35](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-1.1.35.pkg)

次要 bug 修复。

## <a name="111-december-15-2014"></a>1.1.12014年12月15日

[下载版本1.1。1](https://download.xamarin.com/objective-sharpie/ObjectiveSharpie-1.1.1.pkg)

在 2005 2013 年4月的目标 Sharpie 的初始预览后，1.1.1 是1.5 年后的第一个主要发布。 这一版本通常被视为稳定的，可用于各种本机库，这是一个新的 Clang 后端。
