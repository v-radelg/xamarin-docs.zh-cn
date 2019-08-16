---
title: 解决与 system.exception 和 TPL 数据流相关的问题。
description: PCL 案例研究：如何解决与 Microsoft TPL 数据流 NuGet 包的 System.Diagnostics.Tracing 相关的问题？
ms.prod: xamarin
ms.assetid: 7986A556-382D-4D00-ACCF-3589B4029DE8
ms.date: 04/17/2018
author: asb3993
ms.author: amburns
ms.openlocfilehash: 09aef14efdce93e28326deb78292da98f1969ea1
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69521562"
---
# <a name="pcl-case-study-how-can-i-resolve-problems-related-to-systemdiagnosticstracing-for-the-microsoft-tpl-dataflow-nuget-package"></a>PCL 案例研究：如何解决与 Microsoft TPL 数据流 NuGet 包的 System.Diagnostics.Tracing 相关的问题？

> [!IMPORTANT]
> 默认情况下, `System.Diagnostic.Tracing`在最新版本的 Xamarin 中, 此特定的示例不再产生任何错误。 尽管建议的解决方法仍有效, 但请注意, "错误层" 一节中提到的一些 bug 已修复。
> 而且, 您应该注意到 .NET Standard 现在是实现跨平台 .NET Api 的首选方式。

## <a name="summary"></a>总结

Xamarin 和 Xamarin 不会实现它们允许作为引用的每个 PCL 配置文件的 100%。 为了在 Visual Studio for Mac、Visual Studio 和 NuGet 包管理器中提供切实可行的便利, Xamarin 项目允许使用几个只具有_不完整_实现的配置文件。 例如, Xamarin 和 Xamarin 目前都不包括 "system.exception" PCL 命名空间中的类型的完整实现。 尝试使用默认`portable-net45+win8+wpa81`版本的 Microsoft TPL 数据流 NuGet 包时, 此限制将导致出现三个错误层。

## <a name="workaround-switch-the-app-project-to-reference-the-portable-net45win8wp8wpa81-version-of-the-tpl-dataflow-library"></a>解决方法：切换应用程序项目以引用`portable-net45+win8+wp8+wpa81` TPL 数据流库的版本

(这将避免所有三层错误, 并适用于 Xamarin 的所有最新版本。)

1. 在文本编辑器中打开应用程序项目 **.csproj**文件。

2. 找到如下所示的行:

    ```xml
    <HintPath>..\packages\Microsoft.Tpl.Dataflow.4.5.24\lib\portable-net45+win8+wpa81\System.Threading.Tasks.Dataflow.dll</HintPath>
    ```

3. 更改`portable-net45+win8+wpa81`为`portable-net45+win8+wp8+wpa81` (`+wp8`添加):

    ```xml
    <HintPath>..\packages\System.Threading.Tasks.Dataflow.4.5.25\lib\portable-net45+win8+wp8+wpa81\System.Threading.Tasks.Dataflow.dll</HintPath>
    ```

### <a name="explanation"></a>说明

库的版本根本不引用 system.exception, 因此它完全避免了所有三个层问题。 `portable-net45+win8+wp8+wpa81`

### <a name="limitations"></a>限制

- `portable-net45+win8+wpa81`库的`portable-net45+win8+wp8+wpa81`版本可能不包括版本的功能的 100%。

- 默认情况下, NuGet 包`portable-net45+win8+wpa81`管理器会安装 PCL NuGet 包版本, 因此您必须手动调整引用。

## <a name="details-about-the-three-layers-of-errors"></a>有关三个错误层的详细信息

1. 从所有 Mac 版本的 xamarin (非公共 Bug 34888) 中, 当前不存在 "XamarinVS" 外观程序集, 且所有 xamarin 版本低于 9.0 (或低于 Windows 上的 "3.11.1443")[Bug 32388](https://bugzilla.xamarin.com/show_bug.cgi?id=32388))。 此问题会导致以下错误之一, 具体取决于部署目标和链接器设置:

    - Xamarin.Android.Common.targets:错误：加载程序集时出现异常:System.IO.FileNotFoundException:未能加载程序集 "4.0.0.0, Version =, Culture = 中立, PublicKeyToken = b03f5f7f11d50a3a"。 它可能不存在于 Android 的 Mono 配置文件中？

    - 未能加载文件或程序集 ' system.exception ' 或它的某个依赖项。 系统找不到指定的文件。 (System.IO.FileNotFoundException)

    - MTOUCH: 错误 MT3001:无法对程序集 "/Users/macuser/Projects/TPLDataflow/UnifiedSingleViewIphone1/obj/iPhone/Debug/mtouch-cache/64/Build/System.Threading.Tasks.Dataflow.dll" 进行 AOT

    - MTOUCH: 错误 MT2002:未能解析程序集:"4.0.0.0, Version =, Culture = 中立, PublicKeyToken = b03f5f7f11d50a3a"

2. ["System.object" 中类型的当前 Mono 实现](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs)缺少某些方法重载 ([Bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337))。 此问题会导致生成 Xamarin 应用时出现以下链接器错误之一:

    - /Library/Frameworks/Mono.framework/External/xbuild/Xamarin/Android/Xamarin.Android.Common.targets: 错误:执行任务 LinkAssemblies 时出错: 错误 XA2006:对元数据项 "WriteEvent:: 4.5.24.0 (system.string, System.object [])" 的引用 (在 "(system.string, Version =, Culture = 中立, PublicKeyToken = b03f5f7f11d50a3a" 中定义)无法解析 from "4.5.24.0, Version =, Culture = 中立, PublicKeyToken = b03f5f7f11d50a3a"。

    - MTOUCH: 错误 MT2002:未能解析来自 "WriteEvent, Version = 4.0.0.0, Culture = 中立, PublicKeyToken = b03f5f7f11d50a3a" 的 ":: (system.string, system.web::, system.object [])" 引用

3. ["System.object" 中类型的当前 Mono 实现](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs)当前也是_空_的 "虚拟" 实现 ([Bug 34890](https://bugzilla.xamarin.com/show_bug.cgi?id=34890))。 因此, 在运行时尝试使用这些方法可能会产生意外的结果。 在 Microsoft TPL 数据流库的_特定_情况下, 对大多数库的行为而言`WriteEvent(System.Int32,System.Object[])` , 对的调用似乎并不重要, 因此, "第2层" ([Bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337), 添加空实现) 的修复可能已足够大多数 Microsoft TPL 数据流用例。

## <a name="questions--answers"></a>问题 & 解答

### <a name="i-was-able-to-leave-linking-enabled-with-the-portable-net45win8wpa81-version-of-the-library-on-older-versions-of-xamarinios-or-on-xamarinandroid-how-did-that-work"></a>我能够在较旧版本的 xamarin 或`portable-net45+win8+wpa81` xamarin 上启用与库版本的链接。 这是如何工作的？

#### <a name="answer"></a>答案

如果包含对`System.Diagnostics.Tracing.dll` _引用程序集_ \[1\]的引用,则可以在较旧版本的xamarin或Mac上将生成"已成功"(启用链接)完成而不是_外观程序集_ \[2], 但不幸的是, 这不是一种 "正确的" 解决方法。 引用程序集仅用于构建_可移植库_, 而不是特定于平台的代码 (如应用)。 尝试_运行_引用程序集内包含的代码 (而不是仅针对它构建的代码) 可能会产生意外的结果。 对于 Mono 团队, 正确的修复将添加`WriteEvent(System.Int32,System.Object[])` [`EventSource`](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs)到类型 ([Bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337)) 中。 现在, 最佳方法是切换到`portable-net45+win8+wp8+wpa81`前面 "解决方法" 一节中讨论的 Microsoft TPL 数据流库版本。

(对于可能在查看 StackOverflow (<https://stackoverflow.com/a/23591322/2561894>) 中的简要的相关更早答案后阅读本文的任何人, 请注意, 此处_未_提及引用程序集和外观程序集之间的差异。)

**\[1\] "引用程序集" 位置**

Windows：`C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETPortable\v4.5\System.Diagnostics.Tracing.dll`

Mac (Mono):`/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/xbuild-frameworks/.NETPortable/v4.5/System.Diagnostics.Tracing.dll`

**\[2\] "外观程序集" 位置**

Windows：`C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.5\Facades\System.Diagnostics.Tracing.dll`

Mac (Mono):`/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/4.5/Facades/System.Diagnostics.Tracing.dll`


### <a name="will-it-help-if-i-manually-add-a-reference-to-the-systemdiagnosticstracing-facade-assembly"></a>如果手动添加对 "" 的 "" 诊断 "外观程序集的引用, 是否会有帮助？

_具体而言, 我是否可以使用这2个步骤来解决问题？_

1. _`System.Diagnostics.Tracing.dll`将外观程序集复制到应用程序项目文件夹中的以下位置之一:_

    Windows：`C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.5\Facades\System.Diagnostics.Tracing.dll`

    Mac (Mono):`/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/4.5/Facades/System.Diagnostics.Tracing.dll`

2. _在 Xamarin 或 Xamarin Android 应用程序项目中添加对外观程序集的引用。_

#### <a name="answer"></a>答案

不可以, 这不会有帮助。

- 对于 Windows 上的 Xamarin 9.0 或最新版本的 Xamarin, 此解决方法是严格冗余的, 并且可能会导致类似于 "已导入具有相同标识的程序集 '

- 对于 Xamarin 8.10 或更低版本或 Mac 上的 Xamarin, 此解决方法将有助于但_仅_针对 "第1层" 缺少程序集问题。 它_不_会解决 "第2层" 链接器错误, 因此它不是一个完整的解决方案。

### <a name="can-i-use-the-systemdiagnosticstracing-nuget-packagehttpswwwnugetorgpackagessystemdiagnosticstracing-to-solve-the-problem"></a>是否可以使用 "[诊断" NuGet 包](https://www.nuget.org/packages/System.Diagnostics.Tracing/)解决该问题？

#### <a name="answer"></a>答案

不能, NuGet 3.0 "netcore50" 包仅包含 "DNXCore50" 和 "" 的特定于平台的实现。 它显式_省略_xamarin Android ("MonoAndroid") 和 xamarin ("monotouch.dialog" 和 "xamarinios") 的实现。 这意味着安装包对于 Xamarin 和 Xamarin 项目将_不起作用_。 NuGet 包假定这两个平台都提供_自己_的类型实现。 这种假设是 "正确的", 因为 Mono 确实具有命名空间的实现, 但正如上面的 " \#关于错误\#三层的详细信息" 中的 "点 2" 和 "3" 中所述, 实现当前为完整. 因此, Mono 团队将解决[bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337)和[bug 34890](https://bugzilla.xamarin.com/show_bug.cgi?id=34890)。

## <a name="next-steps"></a>后续步骤

若要获得更多帮助, 请联系我们, 或者, 如果在使用上述信息后仍出现此问题, 请参阅[哪些支持选项可用于 Xamarin？](~/cross-platform/troubleshooting/support-options.md)有关联系人选项、建议的信息, 以及如何在需要时记录新 bug.
