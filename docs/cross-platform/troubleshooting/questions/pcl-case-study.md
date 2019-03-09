---
title: 解决与 System.Diagnostics.Tracing，TPL 数据流相关的问题
description: PCL 案例研究：如何解决与 Microsoft TPL 数据流 NuGet 包的 System.Diagnostics.Tracing 相关的问题？
ms.prod: xamarin
ms.assetid: 7986A556-382D-4D00-ACCF-3589B4029DE8
ms.date: 04/17/2018
author: asb3993
ms.author: amburns
ms.openlocfilehash: d9aa85b946f20addb7d69c559bff68c6b1f75429
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57669838"
---
# <a name="pcl-case-study-how-can-i-resolve-problems-related-to-systemdiagnosticstracing-for-the-microsoft-tpl-dataflow-nuget-package"></a>PCL 案例研究：如何解决与 Microsoft TPL 数据流 NuGet 包的 System.Diagnostics.Tracing 相关的问题？

> [!IMPORTANT]
> 此特定示例中的`System.Diagnostic.Tracing`不再默认情况下，在最新版本的 Xamarin 生成的任何错误。 虽然建议的解决方法仍将起作用，但请注意，某些"层的错误"一节中提到的 bug 已修复。
> 此外，您应该注意，.NET Standard 现在是实现跨平台.NET Api 的首选的方法。

## <a name="summary"></a>总结

Xamarin.iOS 和 Xamarin.Android 不会实现它们以引用方式允许每个 PCL 配置文件的 100%。 为 Visual Studio for Mac，Visual Studio 和 NuGet 包管理器中的实际方便起见，Xamarin 项目允许使用多个配置文件，只具有_不完整_实现。 例如，Xamarin.iOS 和 Xamarin.Android 都不当前包括"System.Diagnostics.Tracing"PCL 中的类型的完整实现命名空间。 尝试使用默认值时，到三个层，错误会导致这种限制`portable-net45+win8+wpa81`Microsoft TPL 数据流 NuGet 包的版本。

## <a name="workaround-switch-the-app-project-to-reference-the-portable-net45win8wp8wpa81-version-of-the-tpl-dataflow-library"></a>解决方法：切换应用程序项目以引用`portable-net45+win8+wp8+wpa81`TPL 数据流库版本

（这可以避免错误和适用于所有最新版本的 Xamarin 的所有三个的层。）

1. 打开应用程序项目 **.csproj**文本编辑器中的文件。

2. 查找类似于以下行：

    ```xml
    <HintPath>..\packages\Microsoft.Tpl.Dataflow.4.5.24\lib\portable-net45+win8+wpa81\System.Threading.Tasks.Dataflow.dll</HintPath>
    ```

3. 更改`portable-net45+win8+wpa81`到`portable-net45+win8+wp8+wpa81`(`+wp8`添加):

    ```xml
    <HintPath>..\packages\System.Threading.Tasks.Dataflow.4.5.25\lib\portable-net45+win8+wp8+wpa81\System.Threading.Tasks.Dataflow.dll</HintPath>
    ```

### <a name="explanation"></a>说明

`portable-net45+win8+wp8+wpa81`版本的库不引用**System.Diagnostics.Tracing.dll** _根本_，因此完全可以避免的问题的所有三个层。

### <a name="limitations"></a>限制

- `portable-net45+win8+wp8+wpa81`版本的库可能不会包括 100%的功能的`portable-net45+win8+wpa81`版本。

- NuGet 包管理器安装`portable-net45+win8+wpa81`默认情况下，因此必须手动调整引用 PCL NuGet 包的版本。

## <a name="details-about-the-three-layers-of-errors"></a>有关错误的三个层的详细信息

1. **System.Diagnostics.Tracing.dll**外观程序集是当前不存在从 Xamarin.Android (非公共 Bug 34888) 的所有 Mac 版本并不存在从所有 Xamarin.iOS 版本低于 9.0 （或小于 XamarinVS 3.11.1443在 Windows) 上 (在中修复[Bug 32388](https://bugzilla.xamarin.com/show_bug.cgi?id=32388))。 此问题将导致以下错误，具体取决于部署目标和链接器之一设置：

    - Xamarin.Android.Common.targets:错误：加载程序集时发生异常：System.IO.FileNotFoundException:无法加载程序集 System.Diagnostics.Tracing，Version = 4.0.0.0，区域性 = 中性，PublicKeyToken = b03f5f7f11d50a3a。 可能是它不存在于 Mono for Android 配置文件？

    - 无法加载文件或程序集 System.Diagnostics.Tracing 或其某个依赖项。 系统找不到指定的文件。 (System.IO.FileNotFoundException)

    - MTOUCH： 错误 MT3001:可以不 AOT 的程序集 ' / Users/macuser/Projects/TPLDataflow/UnifiedSingleViewIphone1/obj/iPhone/Debug/mtouch-cache/64/Build/System.Threading.Tasks.Dataflow.dll

    - MTOUCH： 错误 MT2002:未能解析程序集：System.Diagnostics.Tracing，Version = 4.0.0.0，区域性 = 中性，PublicKeyToken = b03f5f7f11d50a3a

2. 当前["System.Diagnostics.Tracing"中的类型的 Mono 实现](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs)缺少某些方法重载 ([Bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337))。 构建 Xamarin 应用程序时，此问题将导致以下链接器错误之一：

    - / Library/Frameworks/Mono.framework/External/xbuild/Xamarin/Android/Xamarin.Android.Common.targets： 错误：执行错误任务 LinkAssemblies： 错误 XA2006:对元数据引用项 System.Void System.Diagnostics.Tracing.EventSource::WriteEvent(System.Int32,System.Object[]) (中定义 System.Threading.Tasks.Dataflow，版本 = 4.5.24.0，区域性 = 中性，PublicKeyToken = b03f5f7f11d50a3a)从 System.Threading.Tasks.Dataflow，版本 = 4.5.24.0，区域性 = 中性，PublicKeyToken = b03f5f7f11d50a3a 无法解析。

    - MTOUCH： 错误 MT2002:未能解析"System.Void System.Diagnostics.Tracing.EventSource::WriteEvent(System.Int32,System.Object[])"的引用"System.Diagnostics.Tracing，Version = 4.0.0.0，区域性 = 中性，PublicKeyToken = b03f5f7f11d50a3a"

3. 当前["System.Diagnostics.Tracing"中的类型的 Mono 实现](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs)目前还_空_"虚拟"实现 ([Bug 34890](https://bugzilla.xamarin.com/show_bug.cgi?id=34890))。 因此，任何尝试在运行时使用这些方法可能产生意外的结果。 有关_特定_用例的 Microsoft TPL 数据流库，它看起来对调用`WriteEvent(System.Int32,System.Object[])`不是必需的大多数库的行为，因此修复"第 2 层"([Bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337)，添加空实现） 可能足以满足大多数 Microsoft TPL 数据流用例。

## <a name="questions--answers"></a>问题与解答

### <a name="i-was-able-to-leave-linking-enabled-with-the-codeportable-net45win8wpa81code-version-of-the-library-on-older-versions-of-xamarinios-or-on-xamarinandroid-how-did-that-work"></a>我就能够保留链接启用了<code>portable-net45+win8+wpa81</code>版本较旧版本 Xamarin.iOS 或 Xamarin.Android 的库。 工作原理？

#### <a name="answer"></a>答案

它是_可能_若要获取为完成"成功"生成 （具有链接已启用） 在较旧版本 Xamarin.iOS 中或在 Mac 上的 Xamarin.Android 中如果包括对引用`System.Diagnostics.Tracing.dll`_引用程序集_\[1\]而不是_外观程序集_ \[2]，但遗憾的是这不是"正确"解决方法。 引用程序集仅用于生成时，使用_可移植库_，例如应用不特定于平台的代码。 正在尝试_运行_引用程序集 （而不是针对它只是生成） 中包含的代码很可能产生意外的结果。 正确的修复程序将在 Mono 团队来添加缺少`WriteEvent(System.Int32,System.Object[])`到重载[ `EventSource` ](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs)类型 ([Bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337))。 现在的最佳选项是切换到`portable-net45+win8+wp8+wpa81`Microsoft TPL 数据流库上面的解决方法部分中所述的版本。

(任何人可能会看到一个相关的较旧的、 简要答案来自 StackOverflow 后再读取这篇文章 (<https://stackoverflow.com/a/23591322/2561894>)，请注意，已引用程序集和外观程序集之间的区别_不_那里所述。)

**\[1\] "引用程序集"的位置**

Windows：`C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETPortable\v4.5\System.Diagnostics.Tracing.dll`

Mac (Mono): `/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/xbuild-frameworks/.NETPortable/v4.5/System.Diagnostics.Tracing.dll`

**\[2\] "外观程序集"位置**

Windows：`C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.5\Facades\System.Diagnostics.Tracing.dll`

Mac (Mono): `/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/4.5/Facades/System.Diagnostics.Tracing.dll`


### <a name="will-it-help-if-i-manually-add-a-reference-to-the-systemdiagnosticstracing-facade-assembly"></a>它将帮助如果我手动添加到"System.Diagnostics.Tracing"外观程序集引用？

_特别是可以解决问题使用以下 2 个步骤？_

1. _复制`System.Diagnostics.Tracing.dll`到从以下位置之一的应用程序项目文件夹的外观程序集：_

    Windows：`C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.5\Facades\System.Diagnostics.Tracing.dll`

    Mac (Mono): `/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/4.5/Facades/System.Diagnostics.Tracing.dll`

2. _Xamarin.iOS 或 Xamarin.Android 应用程序项目中添加对外观程序集的引用。_

#### <a name="answer"></a>答案

否，这并不能在一起。

- 对于 Xamarin.iOS 9.0 或任何新的 Windows 上的 Xamarin.Android 版本，此解决方法是完全冗余的并且可能导致编译错误类似于"具有相同标识的程序集 System.Diagnostics.Tracing 具有已导入。"。

- 适用于 Xamarin.iOS 8.10 用或更低或适用于 Xamarin.Android 的 Mac 上，将有助于此解决方法，但_仅_"层 1"缺少程序集问题。 它将_不_解决"第 2 层"链接器错误，因此它不是完整的解决方案。

### <a name="can-i-use-the-systemdiagnosticstracing-nuget-packagehttpswwwnugetorgpackagessystemdiagnosticstracing-to-solve-the-problem"></a>可以使用[System.Diagnostics.Tracing NuGet 包](https://www.nuget.org/packages/System.Diagnostics.Tracing/)若要解决此问题？

#### <a name="answer"></a>答案

不可以，NuGet 3.0"System.Diagnostics.Tracing"包仅包含"DNXCore50"和"netcore50"的特定于平台的实现。 它显式_省略_Xamarin.Android ("MonoAndroid") 和 Xamarin.iOS （"MonoTouch"和"xamarinios"） 的实现。 这意味着，安装包将具有_不起作用_Xamarin.Android 和 Xamarin.iOS 项目。 NuGet 包假定这两个这些平台提供他们_自己_类型实现。 此假定是"正确"在这个意义上安装 Mono 实现的命名空间，而是作为讨论下点\#2 和\#3 的"详细信息有关的错误的三个层"更高版本，当前不完整实现。 使适当的修复程序将在 Mono 团队解决[Bug 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337)并[Bug 34890](https://bugzilla.xamarin.com/show_bug.cgi?id=34890)。

## <a name="next-steps"></a>后续步骤

获取进一步的帮助，请与我们联系，或如果此问题仍即使利用上述信息，请参阅[了可用于 Xamarin 的支持选项？](~/cross-platform/troubleshooting/support-options.md)有关联系人选项，建议的信息以及如何如果需要，提交新 bug。
