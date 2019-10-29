---
title: .NET 嵌入错误
description: 本文档介绍 .NET 嵌入生成的错误。 错误按代码和提供的说明来帮助进行故障排除。
ms.prod: xamarin
ms.assetid: 932C3F0C-D968-42D1-BB14-D97C73361983
author: davidortinau
ms.author: daortin
ms.date: 04/11/2018
ms.openlocfilehash: b7cdf56ac4d917c8692f33d388adc003fabd9cac
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73007246"
---
# <a name="net-embedding-errors"></a>.NET 嵌入错误

## <a name="em0xxx-binding-error-messages"></a>EM0xxx：绑定错误消息

例如， 参数，环境

<!-- 0xxx: the generator itself, e.g. parameters, environment -->

<a name="EM0000" />

### <a name="em0000-unexpected-error---please-fill-a-bug-report-at-httpsgithubcommonoembeddinator-4000issues"></a>EM0000：意外错误-请在 https://github.com/mono/Embeddinator-4000/issues 填写 bug 报告

出现意外错误。 请尽可能多地说明[问题](https://github.com/mono/Embeddinator-4000/issues)，其中包括：

* 完整生成日志，最大详细级别
* 再现错误的最小测试用例
* 所有版本的

获取确切版本信息的最简单方法是使用 " **Xamarin Studio** " 菜单，**关于 Xamarin Studio**项，"**显示详细信息**" 按钮，然后复制/粘贴版本信息（可以使用 "**复制信息**" 按钮）。

<a name="EM0001" />

### <a name="em0001-could-not-create-output-directory-x"></a>EM0001：无法创建输出目录 `X`

`-o=DIR` 指定的目录名称不存在，无法创建。 它可能是无效的文件系统名称。

<a name="EM0002" />

### <a name="em0002-option-x-is-not-supported"></a>EM0002：不支持选项 `X`

该工具不支持选项 `X`。 此工具的另一个版本可能支持该版本，或者它不应用于此环境中。

<a name="EM0003" />

### <a name="em0003-the-platform-x-is-not-valid"></a>EM0003：平台 `X` 无效。

该工具不支持平台 `X`。 此工具的另一个版本可能支持该版本，或者它不应用于此环境中。

<a name="EM0004" />

### <a name="em0004-the-target-x-is-not-valid"></a>EM0004：目标 `X` 无效。

该工具不支持目标 `X`。 此工具的另一个版本可能支持该版本，或者它不应用于此环境中。

<a name="EM0005" />

### <a name="em0005-the-compilation-target-x-is-not-valid"></a>EM0005：编译目标 `X` 无效。

该工具不支持编译目标 `X`。 此工具的另一个版本可能支持该版本，或者它不应用于此环境中。

<a name="EM0006" />

### <a name="em0006-could-not-find-the-xcode-location"></a>EM0006：找不到 Xcode 位置。

该工具无法使用 `xcode-select -p` 命令找到当前选定的 Xcode 位置。 请验证此命令是否成功，并返回正确的 Xcode 位置。

<a name="EM0007" />

### <a name="em0007-could-not-get-the-sdk-version-for-sdk"></a>EM0007：无法获取 "{sdk}" 的 sdk 版本。

此工具无法使用 `xcrun --show-sdk-version --sdk {sdk}` 命令获取 SDK 版本。 请验证此命令是否成功，并返回 SDK 版本。

<a name="EM0008" />

### <a name="em0008-the-architecture-arch-is-not-valid-for-platform-valid-architectures-for-platform-are-architectures"></a>EM0008：体系结构 "{拱}" 对于 {platform} 无效。 {Platform} 的有效体系结构为： "{体系结构}"。

错误消息中的体系结构对于目标平台无效。 请验证是否向--abi 选项传递了有效的体系结构。

<a name="EM0009" />

### <a name="em0009-the-feature-x-is-not-currently-implemented-by-the-generator"></a>EM0009：此功能 `X` 当前不是由生成器实现的

这是一个已知问题，我们打算在将来的版本中修复此问题。 欢迎使用发布内容。

<a name="EM0010" />

### <a name="em0010-cant-merge-the-frameworks-simulatorframework-and-deviceframework-because-the-file-file-exists-in-both"></a>EM0010：无法合并框架 "{simulatorFramework}" 和 "{deviceFramework}"，因为这两个文件中都存在文件 "{file}"。

该工具无法合并错误消息中提到的框架，因为它们之间有一个公共文件。

这可能表示 .NET 嵌入中的 bug;请使用测试用例在[https://github.com/mono/Embeddinator-4000/issues](https://github.com/mono/Embeddinator-4000/issues)提交 bug 报告。

<a name="EM0011" />

### <a name="em0011-the-assembly-x-does-not-exist"></a>EM0011：程序集 `X` 不存在。

该工具无法找到参数中指定的程序集 `X`。

<a name="EM0012" />

### <a name="em0012-the-assembly-name-x-is-not-unique"></a>EM0012：程序集名称 `X` 不唯一

提供的多个程序集具有相同的内部名称，因此在运行时无法区分它们。

最可能的原因是在命令行参数上多次指定了某个程序集。 但是，重命名的程序集仍保留其原始名称，多个副本不能共存。

<a name="EM0013" />

### <a name="em0013-cant-find-the-assembly-x-referenced-by-y"></a>EM0013：找不到 "Y" 引用的程序集 "X"。

该工具找不到程序集 "Y" 引用的程序集 "X"。 请确保所有引用的程序集都与要绑定的程序集位于同一目录中。

<a name="EM0014" />

### <a name="em0014-could-not-find-product-product-min_version-is-required"></a>EM0014：找不到 {product} （需要 {product} {min_version}）。

在系统上找不到错误消息中提到的依赖项。

<a name="EM0015" />

### <a name="em0015-could-not-find-a-valid-version-of-product-found-version-but-at-least-min_version-is-required"></a>EM0015：找不到有效版本的 {product} （找到 {version}，但至少需要 {min_version}）。

在系统上找到错误消息中提到的依赖项，但它太旧。 请更新到较新的版本。

<a name="EM0016" />

### <a name="em0016-could-not-create-symlink-file---target-error-number"></a>EM0016：无法创建符号 "{file}"-> "{target}"：错误 {number}

无法创建错误消息中提到的符号。

<a name="EM0026" />

### <a name="em0026-could-not-parse-the-command-line-argument-a-"></a>EM0026 无法分析命令行参数 "A"： *

该工具无法分析为命令行选项 `A` 提供的语法。 它可能不正确，请查看文档或帮助以获取正确的语法。

<a name="EM0099" />

### <a name="em0099-internal-error--please-file-a-bug-report-with-a-test-case-httpsgithubcommonoembeddinator-4000issues"></a>EM0099：内部错误 *。 请使用测试用例（ https://github.com/mono/Embeddinator-4000/issues) ）来提交 bug 报告。

如果 .NET 嵌入中的内部一致性检查失败，则会报告此错误消息。

这表示 .NET 嵌入中的 bug;请使用测试用例在[https://github.com/mono/Embeddinator-4000/issues](https://github.com/mono/Embeddinator-4000/issues)提交 bug 报告。

<!-- 1xxx: code processing -->

## <a name="em1xxx-code-processing"></a>EM1xxx：代码处理

<a name="EM1010" />

### <a name="em1010-type-t-is-not-generated-because-x-are-not-supported"></a>EM1010：由于不支持 `X`，因此不生成类型 `T`。

这是一**条警告**，指出类型 `T` 将被忽略（即，不会生成任何内容），因为它使用 `X`，这是不受支持的功能。

注意：受支持的功能将随着新版本的工具而发展。

<a name="EM1011" />

### <a name="em1011-type-t-is-not-generated-because-it-lacks-marshaling-code-with-a-native-counterpart"></a>EM1011：不生成类型 `T`，因为它缺少包含本机对应项的封送处理代码。

这是一**条警告**，指出类型 `T` 将被忽略（即，不会生成任何内容），因为它公开了需要额外封送处理的 .net framework 中的某些内容。

注意：此工具的未来版本中可能会有一些限制，但有一些限制。

<a name="EM1020" />

### <a name="em1020-constructor-c-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1020：由于不支持参数类型 `T`，因此不生成构造函数 `C`。

这是一**条警告**，指出将忽略构造函数 `C` （即，不会生成任何内容），因为不支持类型 `T` 的参数。

应该会出现一个更早的警告，其中提供了不支持类型 `T` 的详细信息。

注意：受支持的功能将随着新版本的工具而发展。

<a name="EM1021" />

### <a name="em1021-constructor-c-has-default-values-for-which-no-wrapper-is-generated"></a>EM1021：构造函数 `C` 具有默认值，而不会生成任何包装。

这是构造函数 `C` 的默认参数未生成任何额外代码的**警告**。 最常见的原因是现有方法已具有相同的签名。 例如， 在 .net 中，可以：

```csharp
public class MyType {
    public MyType () { ... }
    public MyType (int i = 0) { ... }
}
```

在这种情况下，只会创建两个生成的 `init` 选择器，这两个均调入 Mono，但后面没有包装。

<a name="EM1030" />

### <a name="em1030-method-m-is-not-generated-because-return-type-t-is-not-supported"></a>EM1030：方法 `M` 不是生成的，因为不支持返回类型 `T`。

这是一**条警告**，指出方法 `M` 将被忽略（即，不会生成任何内容），因为它的返回类型不受支持 `T`。

应该会出现一个更早的警告，其中提供了不支持类型 `T` 的详细信息。

注意：受支持的功能将随着新版本的工具而发展。

<a name="EM1031" />

### <a name="em1031-method-m-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1031：由于不支持参数类型 `T`，因此不生成方法 `M`。

这是一**条警告**，指出 `M` 将忽略方法（即，不会生成任何内容），因为不支持类型 `T` 的参数。

应该会出现一个更早的警告，其中提供了不支持类型 `T` 的详细信息。

注意：受支持的功能将随着新版本的工具而发展。

<a name="EM1032" />

### <a name="em1032-method-m-has-default-values-for-which-no-wrapper-is-generated"></a>EM1032：方法 `M` 具有默认值，而不会生成任何包装。

这是方法 `M` 的默认参数未生成任何额外代码的**警告**。 最常见的原因是现有方法已具有相同的签名。 例如， 在 .net 中，可以：

```csharp
public class MyType {
    public int Increment () { ... }
    public int Increment (int i = 0) { ... }
}
```

在这种情况下，只会创建两个生成的 `increment` 选择器，这两个均调入 Mono，但后面没有包装。

<a name="EM1033" />

### <a name="em1033-method-m-is-not-generated-because-another-method-exposes-the-operator-with-a-friendly-name"></a>EM1033：方法 `M` 未生成，因为另一个方法使用友好名称公开该运算符。

这是一个**警告**，指出不会生成方法 `M`，因为另一个方法使用友好名称公开运算符。 （ https://msdn.microsoft.com/library/ms229032(v=vs.110).aspx)

<a name="EM1034" />

### <a name="em1034-extension-method-m-is-not-generated-inside-a-category-because-they-cannot-be-created-on-primitive-type-t-a-normal-static-method-was-generated"></a>EM1034：不会在类别内生成扩展方法 `M`，因为不能在 `T`基元类型上创建扩展方法。 已生成正常的静态方法。

这是一个**警告**，指出 primivite 类型的扩展方法（例如 `System.Int32`）已找到。 在客观-C 中，无法在基元类型上创建类别。 而是生成一个正常静态方法。

<a name="EM1040" />

### <a name="em1040-property-p-is-not-generated-because-of-parameter-type-t-is-not-supported"></a>EM1040：由于不支持参数类型 `T`，因此不生成属性 `P`。

这是一**条警告**，指出将忽略属性 `P` （即，不会生成任何内容），因为不支持公开的类型 `T`。

应该会出现一个更早的警告，其中提供了不支持类型 `T` 的详细信息。

注意：受支持的功能将随着新版本的工具而发展。

<a name="EM1041" />

### <a name="em1041-indexed-properties-on-t-is-not-generated-because-multiple-indexed-properties-are-not-supported"></a>EM1041：不会生成 `T` 上的索引属性，因为不支持多个索引属性。

这是一**条警告**，指出 `T` 上的索引属性将被忽略（即，不会生成任何内容），因为不支持多个索引属性。

<a name="EM1050" />

### <a name="em1050-field-f-is-not-generated-because-of-field-type-t-is-not-supported"></a>EM1050：由于不支持字段类型 `T`，因此不生成字段 `F`。

这是一**条警告**，指出字段 `F` 将被忽略（即，不会生成任何内容），因为不支持公开的类型 `T`。

应该会出现一个更早的警告，其中提供了不支持类型 `T` 的详细信息。

注意：受支持的功能将随着新版本的工具而发展。

<a name="EM1051" />

### <a name="em1051-element-e-is-generated-instead-as-f-because-its-name-conflicts-with-an-important-objective-c-selector"></a>EM1051：将生成元素 `E` 而不是 `F`，因为其名称与重要的目标-c 选择器冲突。

这是一个**警告**，该元素 `E` 将改 `F` 为生成元素，因为其名称与重要的目标-c 选择器冲突。

[NSObjectProtocol](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc)上的选择器在目标-c 中具有重要意义，必须谨慎地重写。

注意：保留的选择器列表将随着工具的新版本而发展。

<a name="EM1052" />

### <a name="em1052-element-e-is-not-generated-its-name-conflicts-with-other-elements-on-the-same-class"></a>EM1052：没有生成元素 `E` 其名称与同一类上的其他元素冲突。

这是一**条警告**，指出不会生成 `E` 元素，因为其名称与同一类中的其他元素冲突。

<a name="EM1053" />

### <a name="em1053-target-e-is-not-supported-for-xamarinios-and-xamarinmac-only-the-framework-option-is-considered-supported-and-should-be-used"></a>EM1053：不支持将目标 `E` 用于 Xamarin 和 Xamarin。 仅 `framework` 选项被视为受支持，应使用。

这是针对 Xamarin 和 Xamarin 的目标 `E` 被视为不受支持的**警告**。 

静态或动态 .NET 嵌入库的使用可能需要额外的工作步骤或进行调整，因此在大多数使用情况下应避免使用。

请考虑删除 `--target` 参数或改为传递 `--target=framework`。

<!-- 2xxx: code generation -->

## <a name="em2xxx-code-generation"></a>EM2xxx：代码生成

<!-- 3xxx: reserved -->
<!-- 4xxx: reserved -->
<!-- 5xxx: reserved -->
<!-- 6xxx: reserved -->
<!-- 7xxx: reserved -->
<!-- 8xxx: reserved -->
<!-- 9xxx: reserved -->
