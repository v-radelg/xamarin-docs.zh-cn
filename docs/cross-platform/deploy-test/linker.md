---
title: 自定义链接器配置
description: 本文档介绍了可以用于配置链接器的 XML 文件，显式确保所需代码不会从链接的应用程序中消除。
ms.prod: xamarin
ms.assetid: F8A99E3F-2197-4399-AC81-F1DBAB5729C9
author: conceptdev
ms.author: crdun
ms.date: 03/22/2017
ms.openlocfilehash: f0d51d9aa5cde17498124b74cc35011a6d942fae
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70284848"
---
# <a name="custom-linker-configuration"></a>自定义链接器配置

如果默认的选项集不够，可以使用描述从链接器获取所需内容的 XML 文件驱动链接过程。

你可以提供链接器的额外定义，以确保不会消除应用程序中的类型、方法和/或字段。 在你自己的代码中，首选方式是使用 `[Preserve]` 自定义特性，如[在 iOS 上链接](~/ios/deploy-test/linker.md)和[在 Android 上链接](~/android/deploy-test/linker.md)指南中所述。
但是，如果你需要一些来自 SDK 或产品程序集中的定义，则使用 XML 文件可能是最好的解决方案（而不是添加确保链接器不会消除所需内容的代码）。

若要执行此操作，可以使用顶级元素 `<linker>` 定义 XML 文件，它包括反过来包括类型  节点的程序集  节点，它又包括方法  和字段  节点。

拥有此链接器说明文件后，将其添加到项目并执行下列操作：

- 对于 Android  ：将生成操作  设置为 LinkDescription 
- 对于 iOS  ：将生成操作  设置为 LinkDescription 


下面的示例显示了 XML 文件的外观：

```xml
<linker>
        <assembly fullname="mscorlib">
                <type fullname="System.Environment">
                        <field name="mono_corlib_version" />
                        <method name="get_StackTrace" />
                </type>
        </assembly>
        <assembly fullname="My.Own.Assembly">
                <type fullname="Foo" preserve="fields">
                        <method name=".ctor" />
                </type>
                <type fullname="Bar">
                        <method signature="System.Void .ctor(System.String)" />
                        <field signature="System.String _blah" />
                </type>
                <namespace fullname="My.Own.Namespace" />
                <type fullname="My.Other*" />
        </assembly>
</linker>
```

在上面的示例中，链接器将读取并应用 `mscorlib.dll`（随附 Mono for Android 发货）和 `My.Own.Assembly`（用户代码）程序集上的说明。

第一个部分中，对 `mscorlib.dll` 而言，将确保 `System.Environment` 类型保留其名为 `mono_corlib_version` 的字段和 `get_StackTrace` 方法。
注意，必须使用 getter 和/或 setter 方法名称，因为链接器适用于 IL，且不能理解 C# 属性。

第二个部分中，对 `My.Own.Assembly.dll` 而言，将确保 `Foo` 类型保留其所有字段（即 `preserve="fields"` 属性）及其所有构造函数（即 IL 中所有名为 `.ctor` 的方法）。 `Bar` 类型将为一个构造函数（接受单个字符串参数）和特定字符串字段 `_blah` 保留特定签名（而不是名称）。
`My.Own.Namespace` 命名空间将保留其包含的所有类型。
最后，任何全名（包括命名空间）与通配符模式“My.Other\*”匹配的类型都会保留其所有字段和方法。 可在“type fullname”模式内多次包含通配符 `*`。



## <a name="related-links"></a>相关链接

- [在 iOS 上链接](~/ios/deploy-test/linker.md)
- [在 Android 上链接](~/android/deploy-test/linker.md)
