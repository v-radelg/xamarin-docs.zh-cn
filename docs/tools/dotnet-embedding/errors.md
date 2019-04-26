---
title: .NET 嵌入的错误
description: 本文档介绍.NET 嵌入所生成错误。 错误代码列出并提供描述以帮助进行故障排除。
ms.prod: xamarin
ms.assetid: 932C3F0C-D968-42D1-BB14-D97C73361983
author: lobrien
ms.author: laobri
ms.date: 04/11/2018
ms.openlocfilehash: 5c3dd406f1132f51a86ddf574ab7ad0b279bc9ec
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61215336"
---
# <a name="net-embedding-errors"></a>.NET 嵌入的错误

## <a name="em0xxx-binding-error-messages"></a>EM0xxx:绑定错误消息

例如， 参数、 环境

<!-- 0xxx: the generator itself, e.g. parameters, environment -->

<a name="EM0000" />

### <a name="em0000-unexpected-error---please-fill-a-bug-report-at-httpsgithubcommonoembeddinator-4000issues"></a>EM0000:意外的错误-请填写在 bug 报告 https://github.com/mono/Embeddinator-4000/issues

出现意外的错误条件。 请[提出问题](https://github.com/mono/Embeddinator-4000/issues)提供尽可能多的信息可能，包括：

* 完整的最大详细级别与生成日志，
* 重新生成错误的最小测试用例
* 所有版本信息

若要获取确切的版本信息的最简单方法是使用**Xamarin Studio**菜单中，**关于 Xamarin Studio**项，**显示详细信息**按钮，然后复制/粘贴版本信息 (可以使用**复制信息**按钮)。

<a name="EM0001" />

### <a name="em0001-could-not-create-output-directory-x"></a>EM0001:无法创建输出目录 `X`

指定的目录名称`-o=DIR`不存在并且无法创建。 它可能是文件系统的名称无效。

<a name="EM0002" />

### <a name="em0002-option-x-is-not-supported"></a>EM0002:选项`X`不支持

该工具不支持选项`X`。 它有可能，另一个版本的工具支持它或者，它不适用于此环境中。

<a name="EM0003" />

### <a name="em0003-the-platform-x-is-not-valid"></a>EM0003:在平台`X`无效。

该工具不支持该平台`X`。 它有可能，另一个版本的工具支持它或者，它不适用于此环境中。

<a name="EM0004" />

### <a name="em0004-the-target-x-is-not-valid"></a>EM0004:目标`X`无效。

该工具不支持目标`X`。 它有可能，另一个版本的工具支持它或者，它不适用于此环境中。

<a name="EM0005" />

### <a name="em0005-the-compilation-target-x-is-not-valid"></a>EM0005:编译目标`X`无效。

该工具不支持编译目标`X`。 它有可能，另一个版本的工具支持它或者，它不适用于此环境中。

<a name="EM0006" />

### <a name="em0006-could-not-find-the-xcode-location"></a>EM0006:找不到 Xcode 位置。

该工具找不到当前所选的 Xcode 位置使用`xcode-select -p`命令。 请验证此命令成功，并返回正确的 Xcode 位置。

<a name="EM0007" />

### <a name="em0007-could-not-get-the-sdk-version-for-sdk"></a>EM0007:无法获取 {sdk} sdk 版本。

该工具无法获取 SDK 版本使用`xcrun --show-sdk-version --sdk {sdk}`命令。 请验证此命令成功，并返回的 SDK 版本。

<a name="EM0008" />

### <a name="em0008-the-architecture-arch-is-not-valid-for-platform-valid-architectures-for-platform-are-architectures"></a>EM0008:体系结构 {arch} 为 {平台} 无效。 为 {平台} 的有效体系结构是: {体系结构}。

中的错误消息的体系结构不能用于目标平台。 请验证-abi 选项传递了有效的体系结构。

<a name="EM0009" />

### <a name="em0009-the-feature-x-is-not-currently-implemented-by-the-generator"></a>EM0009:该功能`X`生成器当前未实现

这是我们想要在生成器的未来版本中修复的已知的问题。 欢迎使用发布内容。

<a name="EM0010" />

### <a name="em0010-cant-merge-the-frameworks-simulatorframework-and-deviceframework-because-the-file-file-exists-in-both"></a>EM0010:不能合并框架 {simulatorFramework} 和 {deviceFramework}，因为在这种存在文件 {file}。

该工具不能合并错误消息中提及的框架，因为它们之间没有通用的文件。

这可能表示.NET 嵌入; 中的 bug请提交在 bug 报告[ https://github.com/mono/Embeddinator-4000/issues ](https://github.com/mono/Embeddinator-4000/issues)与测试用例。

<a name="EM0011" />

### <a name="em0011-the-assembly-x-does-not-exist"></a>EM0011:程序集`X`不存在。

该工具找不到程序集`X`参数中指定。

<a name="EM0012" />

### <a name="em0012-the-assembly-name-x-is-not-unique"></a>EM0012:程序集名称`X`不是唯一的

提供的多个程序集具有相同的内部名称，它就不可能区分它们在运行时。

最可能的原因是程序集指定一次，在命令行参数。 但是它是原始名称和多个副本已重命名程序集仍保持不能共存。

<a name="EM0013" />

### <a name="em0013-cant-find-the-assembly-x-referenced-by-y"></a>EM0013:找不到程序集 X，引用的 Y。

该工具找不到 X，Y 该程序集引用的程序集。 请确保所有引用的程序集位于要绑定的程序集所在的目录。

<a name="EM0014" />

### <a name="em0014-could-not-find-product-product-minversion-is-required"></a>EM0014:找不到 {product} （{product} {min_version} 是必需的）。

在系统上找不到错误消息中提及的依赖关系。

<a name="EM0015" />

### <a name="em0015-could-not-find-a-valid-version-of-product-found-version-but-at-least-minversion-is-required"></a>EM0015:找不到 {product} 的有效版本 (找到 {version}，但至少是必需的 {min_version})。

依赖关系错误中提到，在系统上，找到消息，但它是太旧。 请更新到较新版本。

<a name="EM0016" />

### <a name="em0016-could-not-create-symlink-file---target-error-number"></a>EM0016:无法创建符号链接 {file}-> {target}： 错误 {number}

无法创建符号链接的错误消息中所述。

<a name="EM0026" />

### <a name="em0026-could-not-parse-the-command-line-argument-a-"></a>EM0026 无法分析命令行自变量 A: *

提供的命令行选项的语法`A`无法分析工具。 很可能不正确，请与文档或正确的语法的帮助。

<a name="EM0099" />

### <a name="em0099-internal-error--please-file-a-bug-report-with-a-test-case-httpsgithubcommonoembeddinator-4000issues"></a>EM0099:内部错误 *。 请提交 bug 报告与测试用例 ( https://github.com/mono/Embeddinator-4000/issues)。

.NET 嵌入在内部一致性检查失败时报告此错误消息。

这表示.NET 嵌入; 中的 bug请提交在 bug 报告[ https://github.com/mono/Embeddinator-4000/issues ](https://github.com/mono/Embeddinator-4000/issues)与测试用例。

<!-- 1xxx: code processing -->

## <a name="em1xxx-code-processing"></a>EM1xxx:代码会处理

<a name="EM1010" />

### <a name="em1010-type-t-is-not-generated-because-x-are-not-supported"></a>EM1010:类型`T`，因此未生成`X`不受支持。

这是**警告**，类型`T`将被忽略 （即执行任何操作将生成） 因为它使用`X`，不支持的功能。

注意:支持的功能将用新版本，该工具的发展。

<a name="EM1011" />

### <a name="em1011-type-t-is-not-generated-because-it-lacks-marshaling-code-with-a-native-counterpart"></a>EM1011:类型`T`不会生成，因为它缺少与对应的本机封送处理代码。

这是**警告**的类型`T`将被忽略 （即执行任何操作将生成） 因为它公开需要额外封送处理在.NET framework 中的内容。

注意:这是可能获取支持，有一些限制，该工具的未来版本中的内容。

<a name="EM1020" />

### <a name="em1020-constructor-c-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1020:构造函数`C`由于参数类型而不产生`T`不受支持。

这是**警告**的构造函数`C`将被忽略 （即执行任何操作将生成） 因为类型参数的`T`不受支持。

应该有为什么提供详细信息的早期警告键入`T`不受支持。

注意:支持的功能将用新版本，该工具的发展。

<a name="EM1021" />

### <a name="em1021-constructor-c-has-default-values-for-which-no-wrapper-is-generated"></a>EM1021:构造函数`C`具有默认值为其生成任何包装器。

这是**警告**的构造函数的默认参数`C`不生成任何额外的代码。 最常见原因是，现有方法已有相同的签名。 例如， 在.net 中很可能有：

```
public class MyType {
    public MyType () { ... }
    public MyType (int i = 0) { ... }
}
```

在生成此类情况下只有两个`init`将创建选择器，这两种调入 Mono，但存在更高版本不包装器。

<a name="EM1030" />

### <a name="em1030-method-m-is-not-generated-because-return-type-t-is-not-supported"></a>EM1030:方法`M`，因此未生成返回类型`T`不受支持。

这是**警告**的方法`M`将被忽略 （即执行任何操作将生成） 因为它是返回类型`T`不受支持。

应该有为什么提供详细信息的早期警告键入`T`不受支持。

注意:支持的功能将用新版本，该工具的发展。

<a name="EM1031" />

### <a name="em1031-method-m-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1031:方法`M`由于参数类型而不产生`T`不受支持。

这是**警告**的方法`M`将被忽略 （即执行任何操作将生成） 因为类型参数的`T`不受支持。

应该有为什么提供详细信息的早期警告键入`T`不受支持。

注意:支持的功能将用新版本，该工具的发展。

<a name="EM1032" />

### <a name="em1032-method-m-has-default-values-for-which-no-wrapper-is-generated"></a>EM1032:方法`M`具有默认值为其生成任何包装器。

这是**警告**的方法的默认参数`M`不生成任何额外的代码。 最常见原因是，现有方法已有相同的签名。 例如， 在.net 中很可能有：

```
public class MyType {
    public int Increment () { ... }
    public int Increment (int i = 0) { ... }
}
```

在生成此类情况下只有两个`increment`将创建选择器，这两种调入 Mono，但存在更高版本不包装器。

<a name="EM1033" />

### <a name="em1033-method-m-is-not-generated-because-another-method-exposes-the-operator-with-a-friendly-name"></a>EM1033:方法`M`不会生成，因为另一种方法公开具有友好名称的运算符。

这是**警告**的方法`M`不会生成，因为另一种方法公开具有友好名称的运算符。 (https://msdn.microsoft.com/library/ms229032(v=vs.110).aspx)

<a name="EM1034" />

### <a name="em1034-extension-method-m-is-not-generated-inside-a-category-because-they-cannot-be-created-on-primitive-type-t-a-normal-static-method-was-generated"></a>EM1034:扩展方法`M`也不能创建基元类型，因此未生成某一类别内`T`。 正常的静态方法生成。

这是**警告**primivite 上的扩展方法键入 (例如`System.Int32`) 找到。 Objective C 中不能对基元类型创建类别。 而是生成器将生成一个正常的静态方法。

<a name="EM1040" />

### <a name="em1040-property-p-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1040:属性`P`由于参数类型而不产生`T`不受支持。

这是**警告**的属性`P`将被忽略 （即执行任何操作将生成） 因为公开的类型`T`不受支持。

应该有为什么提供详细信息的早期警告键入`T`不受支持。

注意:支持的功能将用新版本，该工具的发展。

<a name="EM1041" />

### <a name="em1041-indexed-properties-on-t-is-not-generated-because-multiple-indexed-properties-are-not-supported"></a>EM1041:索引属性上`T`不会生成，因为不支持多个索引的属性。

这是**警告**的上的索引的属性`T`将被忽略 （即执行任何操作将生成） 因为不支持多个索引的属性。

<a name="EM1050" />

### <a name="em1050-field-f-is-not-generated-because-of-field-type-t-is-not-supported"></a>EM1050:字段`F`由于字段类型而不产生`T`不受支持。

这是**警告**的字段`F`将被忽略 （即执行任何操作将生成） 因为公开的类型`T`不受支持。

应该有为什么提供详细信息的早期警告键入`T`不受支持。

注意:支持的功能将用新版本，该工具的发展。

<a name="EM1051" />

### <a name="em1051-element-e-is-generated-instead-as-f-because-its-name-conflicts-with-an-important-objective-c-selector"></a>EM1051:元素`E`，则生成为`F`因为其名称与重要的 objective c 选择器冲突。

这是**警告**的元素`E`将改为生成作为`F`因为其名称与重要的 objective c 选择器冲突。

选择器[NSObjectProtocol](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc) objective c 中具有重要意义，并且必须小心地重写。

注意:保留选择器的列表将发展与新版本的工具。

<a name="EM1052" />

### <a name="em1052-element-e-is-not-generated-its-name-conflicts-with-other-elements-on-the-same-class"></a>EM1052:元素`E`不生成其名称与同一类上的其他元素冲突。

这是**警告**该元素`E`不会生成如其名称与同一类上的其他元素冲突。

<a name="EM1053" />

### <a name="em1053-target-e-is-not-supported-for-xamarinios-and-xamarinmac-only-the-framework-option-is-considered-supported-and-should-be-used"></a>EM1053:目标`E`不支持 Xamarin.iOS 和 Xamarin.Mac。 仅`framework`选项被视为受支持，应使用。

这是**警告**面向`E`被视为不受支持的对于 Xamarin.iOS 和 Xamarin.Mac 用例。 

使用静态或动态嵌入.NET 库可能需要额外的工作步骤或调整，并应避免在大多数用例。

请考虑删除你`--target`参数或 pass`--target=framework`相反。

<!-- 2xxx: code generation -->

## <a name="em2xxx-code-generation"></a>EM2xxx:代码生成

<!-- 3xxx: reserved -->
<!-- 4xxx: reserved -->
<!-- 5xxx: reserved -->
<!-- 6xxx: reserved -->
<!-- 7xxx: reserved -->
<!-- 8xxx: reserved -->
<!-- 9xxx: reserved -->
