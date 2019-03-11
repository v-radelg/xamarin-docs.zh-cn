---
title: 生成过程
ms.prod: xamarin
ms.assetid: 3BE5EE1E-3FF6-4E95-7C9F-7B443EE3E94C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 12/03/2018
ms.openlocfilehash: 99b5798e8d3cd5723f99aa2483d5d1c0eff8d57c
ms.sourcegitcommit: 6655cccf9d3be755773c2f774b5918e0b141bf84
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2019
ms.locfileid: "57305641"
---
# <a name="build-process"></a>生成过程

## <a name="overview"></a>概述

Xamarin.Android 生成过程负责将所有内容集合在一起：[生成 `Resource.designer.cs`](~/android/internals/api-design.md)，支持 `AndroidAsset`、`AndroidResource` 和其他[生成操作](#Build_Actions)，生成 [Android 可调用的包装器](~/android/platform/java-integration/android-callable-wrappers.md)，以及生成 `.apk` 以在 Android 设备上执行。

## <a name="application-packages"></a>应用程序包

在广义上说，Xamarin.Android 生成系统可以生成两种类型的 Android 应用程序包（`.apk` 文件）： 

-   发行版本，完全是自包含的，不需要额外的程序包来执行。 这些是提供给应用商店的程序包。 

-   调试版本则不是。 

并非巧合的是，这些版本与生成程序包的 MSBuild `Configuration` 相匹配。

### <a name="shared-runtime"></a>共享运行时

“共享运行时”是一对额外的 Android 程序包，它们提供基类库（`mscorlib.dll` 等）和 Android 绑定库（`Mono.Android.dll` 等）。 调试版本依赖共享运行时替代在 Android 应用程序包中包含基类库和绑定程序集，从而使调试程序包更小。

通过将 `$(AndroidUseSharedRuntime)` 属性设置为 `False`，可以在调试版本中禁用共享运行时。 

<a name="Fast_Deployment" />

### <a name="fast-deployment"></a>快速部署

“快速部署”与共享运行时协同工作，进一步缩小 Android 应用程序包的大小。 可以通过不在程序包内捆绑应用的程序集来完成此操作。 而是通过 `adb push` 将它们复制到目标上。 此进程加快了生成/部署/调试周期，因为如果只更改了程序集，则不会重新安装该程序包。 相反，只有更新的程序集才会重新同步到目标设备。 

已知快速部署在阻止 `adb` 同步到目录 `/data/data/@PACKAGE_NAME@/files/.__override__` 的设备上会失败。

快速部署在默认情况下处于启用状态，可以通过将 `$(EmbedAssembliesIntoApk)` 属性设置为 `True` 在调试版本中禁用。


## <a name="msbuild-projects"></a>MSBuild 项目

Xamarin.Android 生成过程基于 MSBuild，它也是 Visual Studio for Mac 和 Visual Studio 使用的项目文件格式。
通常，用户不需要手工编辑 MSBuild 文件 &ndash; IDE 将创建功能齐全的项目并使用所做的全部更改进行更新，根据需要自动调用生成目标。 

高级用户可能希望执行 IDE 的 GUI 不支持的操作，因此，可通过直接编辑项目文件来自定义生成过程。 本页仅记录 Xamarin.Android 特定的功能和自定义 &ndash; 可以使用正常的 MSBuild 项目、属性和目标执行更多的操作。 

<a name="Build_Targets" />

## <a name="build-targets"></a>生成目标

以下的生成目标是为 Xamarin.Android 项目定义的：

-   生成 &ndash; 生成程序包。

-   清理 &ndash; 删除由生成过程生成的所有文件。

-   安装 &ndash; 将程序包安装到默认设备或虚拟设备。

-   卸载 &ndash; 从默认设备或虚拟设备中卸载程序包。

-   SignAndroidPackage &ndash; 创建并对程序包进行签名 (`.apk`)。 用于 `/p:Configuration=Release`，生成自包含的“发行”包。

-   UpdateAndroidResources &ndash; 更新 `Resource.designer.cs` 文件。 将新的资源添加到项目中时，这个目标通常由 IDE 调用。


## <a name="build-properties"></a>生成属性

MSBuild 属性控制目标的行为。 它们是在项目文件中指定的，例如 [MSBuild PropertyGroup 元素](https://docs.microsoft.com/visualstudio/msbuild/propertygroup-element-msbuild)中的 MyApp.csproj。 

-   配置 &ndash; 指定要使用的生成配置，例如“调试”或“发行”。 配置属性用于确定其他属性（确定目标行为）的默认值。 其他配置可能会在 IDE 中创建。

    默认情况下，`Debug` 配置将导致 `Install` 和 `SignAndroidPackage` 目标创建更小的 Android 程序包，这需要提供其他文件和包进行操作。

    默认 `Release` 配置会导致 `Install` 和 `SignAndroidPackage` 目标创建独立的 Android 包，无需安装其他任何包或文件，即可使用此包。

-   DebugSymbols &ndash; 确定 Android 程序包是否为*可调试*以及 `$(DebugType)` 属性的布尔值。 可调试包包含调试符号，将 `//application/@android:debuggable` 属性设置为 `true`，并自动添加 `INTERNET` 权限，以便调试器可以附加到该过程。 如果 `DebugSymbols` 是 `True`，并且 `DebugType` 是空字符串或 `Full`，则应用程序是可调试的。

-   DebugType &ndash; 指定要生成的[调试符号的类型](https://docs.microsoft.com/visualstudio/msbuild/csc-task)作为版本的一部分，它还会影响应用程序是否可调试。 可能的值包括：

    - “完整”：生成完整符号。 如果 `DebugSymbols` MSBuild 属性也为 `True`，则应用程序包是可调试的。

    - PdbOnly：生成“PDB”符号。 应用程序包将不可调试。

    如果 `DebugType` 未设置或为空字符串，则 `DebugSymbols` 属性控制应用程序是否可调试。

    - **AndroidGenerateLayoutBindings** &ndash; 设置为 `true` 能够生成[布局代码隐藏](https://github.com/xamarin/xamarin-android/blob/master/Documentation/guides/LayoutCodeBehind.md)，设置为 `false` 完全禁用。 默认值为 `false`。
    
### <a name="install-properties"></a>安装属性

安装属性控制 `Install` 和 `Uninstall` 目标的行为。

-   AdbTarget &ndash; 指定 Android 包可能要安装到或从中删除的 Android 目标设备。 此属性的值与[`adb`目标设备选项](http://developer.android.com/tools/help/adb.html#issuingcommands)相同：

    ```bash
    # Install package onto emulator via -e
    # Use `/Library/Frameworks/Mono.framework/Commands/msbuild` on OS X
    MSBuild /t:Install ProjectName.csproj /p:AdbTarget=-e
    ```


### <a name="packaging-properties"></a>打包属性

打包属性控制如何创建 Android 包，由 `Install` 和 `SignAndroidPackage` 目标使用。
打包发布应用程序时，[签名属性](#Signing_Properties)也是相关的。


-   **AndroidApkSigningAlgorithm** &ndash; 字符串值，用于指定对 `jarsigner -sigalg` 使用的签名算法。

    默认值为 `md5withRSA`。

    在 Xamarin.Android 8.2 中新增。

-   AndroidApplication &ndash; 一个布尔值，指示项目是用于 Android 应用程序 (`True`) 还是用于 Android 库项目（`False` 或不存在）。

    在 Android 包中，可能只存在一个具有 `<AndroidApplication>True</AndroidApplication>` 的项目。 （遗憾的是，这点尚未得到验证，这可能会导致与 Android 资源有关的微妙和奇怪的错误。）

-   AndroidBuildApplicationPackage &ndash; 一个布尔值，指示是否创建包 (.apk) 并为其签名。 将此值设置为 `True` 相当于使用 [SignAndroidPackage](#Build_Targets) 生成目标。

    在 Xamarin.Android 7.1 之后添加了对该属性的支持。

    该属性默认为 `False`。

-   **AndroidD8JarPath** &ndash; 指向 `d8.jar` 的路径，供与 D8 Dex 编译器结合使用。 默认为 Xamarin.Android 安装中的路径。 有关详细信息，请参阅 [D8 和 R8][d8-r8] 相关文档。

-   **AndroidDexTool** &ndash; 枚举样式的属性，有效值为 `dx` 或 `d8`。 指示在 Xamarin.Android 生成过程中使用的 Android [Dex][dex] 编译器。
    当前默认为 `dx`。 有关详细信息，请参阅 [D8 和 R8][d8-r8] 相关文档。

    [dex]: https://source.android.com/devices/tech/dalvik/dalvik-bytecode
    [d8-r8]: https://github.com/xamarin/xamarin-android/blob/master/Documentation/guides/D8andR8.md

-   **AndroidEnableDesugar** &ndash; 确定是否启用了 `desugar` 的布尔属性。 Android 当前不支持所有 Java 8 功能；默认工具链通过对 `javac` 编译器的输出执行称为 `desugar` 的字节码转换，实现新的语言功能。 如果使用 `AndroidDexTool=dx`，默认为 `False`；如果使用 `AndroidDexTool=d8`，默认为`True`。

-   AndroidEnableMultiDex &ndash; 一个布尔属性，用于确定是否将在最终的 `.apk` 中使用 multi-dex 支持。

    Xamarin.Android 5.1 中增加了对该属性的支持。

    该属性默认为 `False`。

-   AndroidEnableSGenConcurrent &ndash; 一个布尔属性，用于确定是否使用 Mono 的[并发垃圾收集器](http://www.mono-project.com/docs/about-mono/releases/4.8.0/#concurrent-sgen)。

    在 Xamarin.Android 7.2 中增加了对该属性的支持。

    该属性默认为 `False`。

-   **AndroidErrorOnCustomJavaObject** &ndash; 布尔属性，用于确定类型能否实现 `Android.Runtime.IJavaObject`
    ，而无需同时继承自 `Java.Lang.Object` 或 `Java.Lang.Throwable`：

    ```csharp
    class BadType : IJavaObject {
        public IntPtr Handle {
            get {return IntPtr.Zero;}
        }

        public void Dispose()
        {
        }
    }
    ```

    若为 True，这些类型生成 XA4212 错误；否则，生成 XA4212 警告。

    Xamarin.Android 8.1 现已开始支持此属性。

    该属性默认为 `True`。

-   AndroidFastDeploymentType &ndash; `:`（冒号）分隔的值列表，`$(EmbedAssembliesIntoApk)` MSBuild 属性为 `False` 时可用于控制部署到目标设备上的[快速部署目录](#Fast_Deployment)的类型。 如果资源是快速部署的，则不会嵌入到生成的 `.apk` 中，这样做可以加快部署时间。 （部署的速度越快，`.apk` 需要重建的频率越低，安装过程可能会更快。）有效值包括：

    - `Assemblies`：部署应用程序集。

    - `Dexes`：部署 `.dex` 文件、Android 资源和 Android 资产。 **此值仅可以在运行 Android 4.4 或更高版本 (API-19) 的设备上使用。**

    默认值为 `Assemblies`。

    “实验”。 已在 Xamarin.Android 6.1 中添加。

-   AndroidApplicationJavaClass &ndash; 类继承自 [Android.App.Application](https://developer.xamarin.com/api/type/Android.App.Application/) 时，用于替代 `android.app.Application` 的完整 Java 类名称。

    此属性通常由其他属性（如 `$(AndroidEnableMultiDex)` MSBuild 属性）设置。

    已在 Xamarin.Android 6.1 中添加。

-   **AndroidHttpClientHandlerType** &ndash; 控制 `System.Net.Http.HttpClient` 默认构造函数使用的默认 `System.Net.Http.HttpMessageHandler` 实现。 值是 `HttpMessageHandler` 子类的程序集限定类型名称，适用于 [`System.Type.GetType(string)`](/dotnet/api/system.type.gettype?view=netcore-2.0#System_Type_GetType_System_String_)。

    默认值为 `System.Net.Http.HttpClientHandler, System.Net.Http`。

    这可能会被重写为包含 `Xamarin.Android.Net.AndroidClientHandler`，后者使用 Android Java API 执行网络请求。 这样，如果基础 Android 版本支持 TLS 1.2，就可以访问 TLS 1.2 URL。  
    只有 Android 5.0 及更高版本通过 Java 可靠提供 TLS 1.2 支持。

    *说明*：如果需要在低于 Android 5.0 的版本上具备 TLS 1.2 支持，或者 TLS 1.2 支持需要与 `System.Net.WebClient` 及相关 API 一起使用，则应使用 `$(AndroidTlsProvider)`。

    *说明*：通过设置 [`XA_HTTP_CLIENT_HANDLER_TYPE` 环境变量](~/android/deploy-test/environment.md)可使用对此属性的支持。
    在生成操作为 `@(AndroidEnvironment)` 的文件中，发现的 `$XA_HTTP_CLIENT_HANDLER_TYPE` 值的优先级更高。

    已在 Xamarin.Android 6.1 中添加。

-   AndroidTlsProvider &ndash; 一个字符串值，指定应用程序中应使用哪个 TLS 提供程序。 可能的值有：

    - `btls`：针对与 [HttpWebRequest](xref:System.Net.HttpWebRequest) 的通信，使用 [Boring SSL](https://boringssl.googlesource.com/boringssl)。
      这样，可以对所有 Android 版本使用 TLS 1.2。

    - `legacy`：对于网络交互使用之前托管的 SSL 实现。 这不支持 TLS 1.2。

    - `default`：允许 Mono 选择默认 TLS 提供程序。
      这相当于 `legacy`，即使在 Xamarin.Android 7.3 中，也不例外。  
      *说明*：此值不可能出现在 `.csproj` 值中，因为 IDE 的“默认”值会导致删除 `$(AndroidTlsProvider)` 属性。

    - 取消设置/空字符串：在 Xamarin.Android 7.1 中，这等效于 `legacy`。  
      在 Xamarin.Android 7.3 中，这相当于 `btls`。

    默认值为空字符串。

    已在 Xamarin.Android 7.1 中添加。

-   AndroidLinkMode &ndash; 指定应在 Android 包中包含的程序集上执行[链接](~/android/deploy-test/linker.md)的类型。 仅在 Android 应用程序项目中使用。 默认值是 SdkOnly。 有效值为：

    - “无”：将不尝试任何链接。

    - SdkOnly：将仅在基类库上（不在用户的程序集上）执行链接。

    - “完整”：将在基类库和用户程序集上执行链接。 **注意：** 使用“完整”这一 `AndroidLinkMode` 值常会导致应用损坏，尤其是使用放射时。 除非你真正知道在做什么，否则请避免。

    ```xml
    <AndroidLinkMode>SdkOnly</AndroidLinkMode>
    ```

-   AndroidLinkSkip &ndash; 指定不应链接的程序集名称的分号分隔 (`;`) 列表，没有文件扩展名。 仅在 Android 应用程序项目中使用。

    ```xml
    <AndroidLinkSkip>Assembly1;Assembly2</AndroidLinkSkip>
    ```

-   **AndroidLinkTool** &ndash; 枚举样式的属性，有效值为 `proguard` 或 `r8`。 指示用于 Java 代码的代码压缩器。 当前默认为空字符串；如果 `$(AndroidEnableProguard)` 是 `True`，则为 `proguard`。 有关详细信息，请参阅 [D8 和 R8][d8-r8] 相关文档。

    [d8-r8]: https://github.com/xamarin/xamarin-android/blob/master/Documentation/guides/D8andR8.md

-   **LinkerDumpDependencies** &ndash; 布尔属性，能够生成链接器依赖项文件。 可将此文件用作 [illinkanalyzer](https://github.com/mono/linker/blob/master/src/analyzer/README.md) 工具的输入。

    默认值为 False。

-   AndroidManagedSymbols &ndash; 一个布尔属性，用于控制是否生成序列点，以便可以从 `Release` 堆栈跟踪中提取文件名和行号信息。

    已在 Xamarin.Android 6.1 中添加。

-   AndroidManifest &ndash; 指定用于应用 [`AndroidManifest.xml`](~/android/platform/android-manifest.md) 的模板的文件名。
    在生成期间，将合并任何其他必要的值以生成实际的 `AndroidManifest.xml`。
    `$(AndroidManifest)` 必须在 `/manifest/@package` 属性中包含程序包名称。

-   **AndroidR8JarPath** &ndash; 指向 `r8.jar` 的路径，供与 R8 Dex 编译器和压缩器结合使用。 默认为 Xamarin.Android 安装中的路径。 有关详细信息，请参阅 [D8 和 R8][d8-r8] 相关文档。

-   AndroidSdkBuildToolsVersion &ndash; Android SDK 生成工具包提供 aapt 和 zipalign 工具等。 可以同时安装多个不同版本的生成工具包。 若要选择用于打包的生成工具包，请检查是否有“首选”生成工具版本。如果有，请使用它；如果没有“首选”版本，请使用版本最高的已安装生成工具包。

    `$(AndroidSdkBuildToolsVersion)` MSBuild 属性包含首选的生成工具版本。 如果（例如）已知上一 aapt 版本可用，而此时最新的 aapt 发生崩溃，则 Xamarin.Android 生成系统会在 `Xamarin.Android.Common.targets` 中提供默认值，并且可在项目文件中替代该默认值，选择备用的生成工具版本。

-   AndroidSupportedAbis &ndash; 包含分号 (`;`) 分隔的 ABI 列表的字符串属性，应包含到 `.apk` 中。

    支持的值包括：

    -   `armeabi-v7a`
    -   `x86`
    -   `arm64-v8a`：需要 Xamarin.Android 5.1 及更高版本。
    -   `x86_64`：需要 Xamarin.Android 5.1 及更高版本。

-   AndroidUseSharedRuntime &ndash; 一个布尔属性，用于确定是否需要“共享运行时包”才能在目标设备上运行应用程序。 依靠共享运行时包以允许应用程序包更小，加快包创建和部署过程，从而加快生成/部署/调试周转周期。

    对于调试版本，该属性应为 `True`，对于发行项目应为 `False`。

-   AotAssemblies &ndash; 一个布尔属性，用于确定程序集是否会被预编译为本机代码并包含在 `.apk` 中。

    Xamarin.Android 5.1 中增加了对该属性的支持。

    该属性默认为 `False`。

-   EmbedAssembliesIntoApk &ndash; 一个布尔属性，用于确定应用程序的程序集是否应嵌入到应用程序包中。

    对于发行版本，该属性应为 `True`，对于调试版本应为 `False`。 如果“快速部署”不支持目标设备，则调试版本中可能必须为 `True`。

    该属性为 `False` 时，`$(AndroidFastDeploymentType)` MSBuild 属性还会控制嵌入到 `.apk` 中的内容，这会影响部署和重新生成时间。

-   **EnableLLVM** &ndash; 一个布尔属性，用于确定在将程序集先编译为本机代码时是否使用 LLVM。

    Xamarin.Android 5.1 中增加了对该属性的支持。

    该属性默认为 `False`。

    除非 `$(AotAssemblies)` MSBuild 属性为 `True`，否则该属性将被忽略。

-   EnableProguard &ndash; 一个布尔属性，用于确定是否将 [proguard](http://developer.android.com/tools/help/proguard.html) 作为打包过程的一部分运行以链接 Java 代码。

    Xamarin.Android 5.1 中增加了对该属性的支持。

    该属性默认为 `False`。

    如果 `True`，[ProguardConfiguration](#ProguardConfiguration) 文件将用于控制 `proguard` 的执行。

-   JavaMaximumHeapSize &ndash; 指定构建 `.dex` 文件作为打包过程一部分时使用的 java
    `-Xmx` 参数值的值。 如果未指定，则 `-Xmx` 选项向 java 提供值 `1G`。 我们发现与其他平台相比，Windows 常常要求这样设置。

    如果 [`_CompileDex` 目标引发 `java.lang.OutOfMemoryError`](https://bugzilla.xamarin.com/show_bug.cgi?id=18327)，则指定该属性是必需的。

    通过如下更改自定义值：
    ```xml
    <JavaMaximumHeapSize>1G</JavaMaximumHeapSize>
    ```

-   JavaOptions &ndash; 指定在生成 `.dex` 文件时传递给 java 的附加命令行选项。

-   MandroidI18n &ndash; 指定应用程序附带的国际化支持，例如排序规则和排序表。 该值是以下一个或多个不区分大小写值的以逗号或分号分隔的列表：

    -   “无”：不包括任何额外的编码。

    -   “所有”：包括所有可用编码。

    -   CJK：包括中文、日语和韩语编码，例如日语 (EUC) \[enc-jp, CP51932\]、日语 (Shift-JIS) \[iso-2022-jp, shift\_jis, CP932\]、日语 (JIS) \[CP50220\]、简体中文 (GB2312) \[gb2312, CP936\]、韩语 (UHC) \[ks\_c\_5601-1987, CP949\]、韩语 (EUC) \[euc-kr, CP51949\]、繁体中文 (Big5) \[big5, CP950\] 和简体中文 (GB18030) \[GB18030, CP54936\]。

    -   “中东”：包括中东编码，例如土耳其 (Windows) \[iso-8859-9, CP1254\]、希伯来语 (Windows) \[windows-1255, CP1255\]、阿拉伯语(Windows) \[windows-1256, CP1256\]、阿拉伯语 (ISO) \[iso-8859-6, CP28596\]、希伯来语 (ISO) \[iso-8859-8, CP28598\]、拉丁语Latin 5 (ISO) \[iso-8859-9, CP28599\] 和希伯来语 (Iso Alternative) \[iso-8859-8, CP38598\]。

    -   “其他”：包括其他编码，例如西里尔字母 (Windows) \[CP1251\]、波罗的海文 (Windows) \[iso-8859-4, CP1257\]、越南语 (Windows) \[CP1258\]、西里尔字母 (KOI8-R) \[koi8-r, CP1251\]、乌克兰语 (KOI8-U) \[koi8-u, CP1251\]、波罗的海文 (ISO) \[iso-8859-4, CP1257\]、西里尔字母 (ISO) \[iso-8859-5, CP1251\]、ISCII 天城体梵文字母 \[x-iscii-de, CP57002\]、ISCII 孟加拉语 \[x-iscii-be, CP57003\]、ISCII 泰米尔语 \[x-iscii-ta, CP57004\]、ISCII 泰卢固语 \[x-iscii-te, CP57005\]、ISCII 阿萨姆语 \[x-iscii-as, CP57006\]、ISCII 奥里雅语 \[x-iscii-or, CP57007\]、ISCII 坎那达语 \[x-iscii-ka, CP57008\]、ISCII 马拉雅拉姆语 \[x-iscii-ma, CP57009\]、ISCII 古吉拉特语 \[x-iscii-gu, CP57010\]、ISCII 旁遮普语 \[x-iscii-pa, CP57011\] 和泰语 (Windows) \[CP874\]。

    -   “稀有”：包括稀有编码，例如 IBM EBCDIC (土耳其语) \[CP1026\]、IBM EBCDIC (开放系统拉丁语 1) \[CP1047\]、IBM EBCDIC (美国-加拿大-欧洲) \[CP1140\]、IBM EBCDIC (德国-欧洲) \[CP1141\]、IBM EBCDIC (丹麦/挪威-欧洲) \[CP1142\]、IBM EBCDIC (芬兰/瑞典-欧洲) \[CP1143\]、IBM EBCDIC (意大利-欧洲) \[CP1144\]、IBM EBCDIC (拉丁美洲/西班牙-欧洲) \[CP1145\]、IBM EBCDIC (英国-欧洲) \[CP1146\]、IBM EBCDIC (法国-欧洲) \[CP1147\]、IBM EBCDIC (国际-欧洲) \[CP1148\]、IBM EBCDIC (冰岛语-欧洲) \[CP1149\]、IBM EBCDIC (德语) \[CP20273\]、IBM EBCDIC (丹麦/挪威) \[CP20277\]IBM EBCDIC (芬兰/瑞典) \[CP20278\]、IBM EBCDIC (意大利) \[CP20280\]、IBM EBCDIC (拉丁美洲/西班牙) \[CP20284\]、IBM EBCDIC (英国) \[CP20285\]、IBM EBCDIC (扩展式日文片假名) \[CP20290\]、IBM EBCDIC (法国) \[CP20297\]、IBM EBCDIC (阿拉伯语) \[CP20420\]、IBM EBCDIC (希伯来语) \[CP20424\]、IBM EBCDIC (冰岛语) \[CP20871\]、IBM EBCDIC (西里尔字母 - 塞尔维亚语, 保加利亚语) \[CP21025\]、IBM EBCDIC (美国-加拿大) \[CP37\]IBM EBCDIC (国际) \[CP500\]、阿拉伯语 (ASMO 708) \[CP708\]、欧洲中部 (DOS) \[CP852\]、西里尔字母 (DOS) \[CP855\]、土耳其语 (DOS) \[CP857\]、欧洲西部 (DOS-欧洲) \[CP858\]、希伯来语 (DOS) \[CP862\]、阿拉伯语 (DOS) \[CP864\]俄语 (DOS) \[CP866\]、希腊语 (DOS) \[CP869\]、IBM EBCDIC (拉丁语 2) \[CP870\] 和 BM EBCDIC (希腊语) \[CP875\]。

    -   “西部”：包括西部编码，例如欧洲西部 (Mac) \[macintosh, CP10000\]、冰岛语 (Mac) \[x-mac-icelandic, CP10079\]、欧洲中部 (Windows) \[iso-8859-2, CP1250\]、欧洲西部 (Windows) \[iso-8859-1, CP1252\]、希腊语 (Windows) \[iso-8859-7, CP1253\]、欧洲中部 (ISO) \[iso-8859-2, CP28592\]、拉丁语 3 (ISO) \[iso-8859-3, CP28593\]、拉丁语 (ISO) \[iso-8859-7, CP28597\]、拉丁语 9 (ISO) \[iso-8859-15, CP28605\]OEM 美国 \[CP437\]、欧洲西部 (DOS) \[CP850\]、葡萄牙语 (DOS) \[CP860\]、冰岛语 (DOS) \[CP861\]、法国加拿大语 (DOS) \[CP863\] 和日耳曼语 (DOS) \[CP865\]。


    ```xml
    <MandroidI18n>West</MandroidI18n>
    ```

-   MonoSymbolArchive &ndash; 一个布尔属性，用于控制是否创建 `.mSYM` 项目供以后与 `mono-symbolicate` 一起使用，从版本堆栈跟踪中提取真实&ldquo;&rdquo;文件名和行号信息。

    对于已启用调试符号的&ldquo;发行&rdquo;应用，默认情况下为 True：`$(EmbedAssembliesIntoApk)` 为 True，`$(DebugSymbols)` 为 True 且 `$(Optimize)` 为 True。

    已在 Xamarin.Android 7.1 中添加。

-   AndroidVersionCodePattern &ndash; 允许开发人员在清单中自定义 `versionCode` 的字符串属性。
    有关决定 `versionCode` 的信息，请参阅[为 APK 创建版本代码](~/android/deploy-test/building-apps/abi-specific-apks.md)。
    
    例如，如果 `abi`是 `armeabi`，清单中的 `versionCode` 为 `123`，则当 `$(AndroidCreatePackagePerAbi)` 为 True 时，`{abi}{versionCode}` 将生成 `1123` 的 versionCode，否则将生成值 123。
    如果 `abi` 是 `x86_64`，则清单中的 `versionCode` 是 `44`。 当 `$(AndroidCreatePackagePerAbi)` 为 True 时，这将生成 `544`，否则会生成值 `44`。

    如果我们包含左填充格式字符串 `{abi}{versionCode:0000}`，则会生成 `50044`，因为我们用 `0` 在左边填充 `versionCode`。 此外，也可以使用十进制填充（例如 `{abi}{versionCode:D4}`），
    该操作与前一个示例的效果相同。

    由于值必须是整数，因此只支持 0 和 Dx 填充格式字符串。
    
    预定义的键项

    -   abi &ndash; 插入应用程序的目标 abi
        -   2 &ndash; `armeabi-v7a`
        -   3 &ndash; `x86`
        -   4 &ndash; `arm64-v8a`
        -   5 &ndash; `x86_64`

    -   minSDK &ndash; 如果没有定义，则插入 `AndroidManifest.xml` 或 `11` 中支持的最小 SDK 值。

    -   **versionCode** &ndash; 直接使用 `Properties\AndroidManifest.xml` 中的版本代码。 

    你可以使用（下文中定义的）`$(AndroidVersionCodeProperties)` 属性定义自定义项。

    默认情况下，值设置为 `{abi}{versionCode:D6}`。 如果开发人员要保留旧行为，可将 `$(AndroidUseLegacyVersionCode)` 属性设置为 `true`，从而替代默认值

    已在 Xamarin.Android 7.2 中添加。

-   AndroidVersionCodeProperties &ndash; 一个字符串属性，它允许开发人员定义要与 `AndroidVersionCodePattern` 一起使用的自定义项。 它们采用 `key=value` 对的形式。 `value` 中的所有项都应是整数值。 例如：`screen=23;target=$(_AndroidApiLevel)`。 正如你所看到的，你可以使用字符串中现有或自定义的 MSBuild 属性。

    已在 Xamarin.Android 7.2 中添加。

-   **AndroidUseLegacyVersionCode** &ndash; 一个布尔属性，允许开发人员将 versionCode 计算还原到先前的 Xamarin.Android 8.2 旧行为。 这只能适用于在 Google Play 商店中已发布应用程序的开发人员。 强烈建议使用新 `$(AndroidVersionCodePattern)` 属性。

    在 Xamarin.Android 8.2 中新增。

-  **AndroidUseManagedDesignTimeResourceGenerator** &ndash; 布尔属性，用于将设计时生成切换为使用受管理资源分析程序，而不是 `aapt`。

    在 Xamarin.Android 8.1 中新增。

-  **AndroidUseApkSigner** &ndash; 布尔属性，允许开发人员使用 `apksigner` 工具，而不是 `jarsigner`。

    在 Xamarin.Android 8.2 中新增。

-  **AndroidApkSignerAdditionalArguments** &ndash; 字符串属性，允许开发人员向 `apksigner` 工具提供其他参数。

    在 Xamarin.Android 8.2 中新增。

-  **AndroidLintEnabled** &ndash; 布尔属性，使开发人员能够在打包过程中运行 Android `lint` 工具。

    -   **AndroidLintEnabledIssues** &ndash; 要启用的 lint 问题逗号分隔列表。

    -   **AndroidLintDisabledIssues** &ndash; 要禁用的 lint 问题逗号分隔列表。

    -   **AndroidLintCheckIssues** &ndash; 要检查的 lint 问题逗号分隔列表。 
       请注意：只检查这些问题。

    -   **AndroidLintConfig** &ndash; 这是 lint 样式配置文件的生成操作。 可用于启用/禁用要检查的问题。 多个文件均可使用此生成操作，因为它们的内容会被合并。

    请参阅 [Lint 帮助](http://www.androiddocs.com/tools/help/lint.html)，了解有关 Android `lint` 工具的详细信息。

-  **AndroidGenerateJniMarshalMethods** &ndash; 布尔属性，能够在生成过程中生成 JNI 封送方法。 大大减少了在绑定帮助程序代码中对 System.Reflection 的使用。

   默认设置为 False。 如果开发者希望使用新的 JNI 封送方法功能，他们可以进行如下设置：

    ```xml
    <AndroidGenerateJniMarshalMethods>True</AndroidGenerateJniMarshalMethods>
    ```

    （其 csproj 中）。 或者通过如下设置，在命令行上提供该属性：

    `/p:AndroidGenerateJniMarshalMethods=True`

    “实验”。 在 Xamarin.Android 9.2 中新增。
    默认值为 False。

- **AndroidGenerateJniMarshalMethodsAdditionalArguments** &ndash; 字符串属性，可用于向 `jnimarshalmethod-gen.exe` 调用添加额外的参数。  对调试非常有用，以便可使用 `-v`、`-d` 或 `--keeptemp` 等选项。

   默认值为空字符串。 可以在 csproj 文件中或命令行上设置。 例如:

    ```xml
    <AndroidGenerateJniMarshalMethodsAdditionalArguments>-v -d --keeptemp</AndroidGenerateJniMarshalMethodsAdditionalArguments>
    ```

   或：

    `/p:AndroidGenerateJniMarshalMethodsAdditionalArguments="-v -d --keeptemp"`

    在 Xamarin.Android 9.2 中新增。

- **AndroidMultiDexClassListExtraArgs** &ndash; 字符串属性，使开发人员能够在生成 `multidex.keep` 文件时，向 `com.android.multidex.MainDexListBuilder` 传递额外的参数。 

    具体事例：是否在 `dx` 编译期间发生以下错误。

        com.android.dex.DexException: Too many classes in --main-dex-list, main dex capacity exceeded

    如果发生此错误，可以向 .csproj 添加以下内容。

    ```xml
    <DxExtraArguments>--force-jumbo </DxExtraArguments>
    <AndroidMultiDexClassListExtraArgs>--disable-annotation-resolution-workaround</AndroidMultiDexClassListExtraArgs>
    ```

    这样，`dx` 步骤才能成功。

    在 Xamarin.Android 8.3 中新增。

### <a name="binding-project-build-properties"></a>绑定项目生成属性

以下 MSBuild 属性与[绑定项目](~/android/platform/binding-java-library/index.md)一起使用：

-   AndroidClassParser &ndash; 一个字符串属性，用于控制如何分析 `.jar` 文件。 可能的值包括：

    - class-parse：使用 `class-parse.exe` 直接分析 Java 字节码，而不使用 JVM。 此值处于试验阶段。 


    - jar2xml：通过 `jar2xml.jar` 使用 use Java 反射以提取 `.jar` 文件中的类型和成员。

    `class-parse` 优于 `jar2xml` 的优势是：

    - `class-parse` 能够从包含调试符号的 Java 字节码（例如，用 `javac -g` 编译的字节码）提取参数名称。

    - `class-parse` 不会“跳过”从无法解析的类型继承或者包含无法解析类型成员的类。

    “实验”。 已在 Xamarin.Android 6.0 中添加。

    默认值为 `jar2xml`。

    默认值将会在未来版本中更改。

-   AndroidCodegenTarget &ndash; 控制代码生成目标 ABI 的字符串属性。 可能的值包括：

    - XamarinAndroid：使用 Mono for Android 1.0 中存在的 JNI 绑定 API。 使用 Xamarin.Android 5.0 或更高版本生成的绑定程序集只能在 Xamarin.Android 5.0 或更高版本（API/ABI 附加程序）上运行，但源与先前的产品版本兼容。

    - XAJavaInterop1：对JNI 调用使用 Java.Interop。 只能通过 Xamarin.Android 6.1 或更高版本构建和执行使用 `XAJavaInterop1` 的绑定程序集。 Xamarin.Android 6.1 和更高版本会将 `Mono.Android.dll` 与此值绑定。

      `XAJavaInterop1` 的好处包括：

      - 程序集较小。

      - 只要继承层次结构中的所有其他绑定类型均使用 `XAJavaInterop1` 或更高版本构建，即可使用 `base` 方法调用的 `jmethodID` 缓存。

      - 用于托管子类的 Java Callable Wrapper 构造函数的 `jmethodID` 缓存。

    默认值为 `XAJavaInterop1`。


### <a name="resource-properties"></a>资源属性

资源属性控制 `Resource.designer.cs` 文件的生成，该文件提供对 Android 资源的访问。 

-   AndroidResgenExtraArgs &ndash; 指定处理 Android 资产和资源时传递给 aapt 命令的附加命令行选项。

-   AndroidResgenFile &ndash; 指定要生成的资源文件的名称。 默认模板将其设置为 `Resource.designer.cs`。

-   MonoAndroidResourcePrefix &ndash; 通过 `AndroidResource` 生成操作指定从文件名开头删除的“路径前缀”。 这是为了允许更改资源所在的位置。

    默认值为 `Resources`。 将此项更改为 `res` 以获得 Java 项目结构。

-   AndroidExplicitCrunch &ndash; 如果你正在生成具有大量本地绘图的应用，则需要花费数分钟才能完成初始生成（或重新生成）。 要加快生成过程，请尝试包含该属性并将其设置为 `True`。 设置该属性时，生成过程会预处理 .png 文件。

    注意:此选项与 `$(AndroidUseAapt2)` 选项不兼容。 如果启用了 `$(AndroidUseAapt2)`，将禁用此功能。 如果希望继续使用此功能，请将 `$(AndroidUseAapt2)` 设置为 `False`。

    “实验”。 已在 Xamarin.Android 7.0 中添加。

-  **AndroidUseAapt2** &ndash; 布尔属性，使开发者能够控制 `aapt2` 打包工具的使用。
    该属性默认设置为 false，我们要使用的是 `aapt`。
    如果开发者要使用新的 `aapt2` 功能，他们可以进行如下设置：
        
    ```xml
    <AndroidUseAapt2>True</AndroidUseAapt2>
    ```
        
    （其 csproj 中）。 或者通过如下设置，在命令行上提供该属性：

    `/p:AndroidUseAapt2=True`

    在 Xamarin.Android 8.3 中新增。

-   **AndroidAapt2CompileExtraArgs** &ndash; 指定处理 Android 资产和资源时传递给 aapt2 compile 命令的附加命令行选项。

    在 Xamarin.Android 9.1 中新增。

-   **AndroidAapt2LinkExtraArgs** &ndash; 指定处理 Android 资产和资源时传递给 aapt2 link 命令的附加命令行选项。

    在 Xamarin.Android 9.1 中新增。

<a name="Signing_Properties" />

### <a name="signing-properties"></a>签名属性

签名属性控制应用程序包的签名方式，以便将其安装到 Android 设备上。 为了更快地生成迭代，Xamarin.Android 任务不会在生成过程中为包签名，因为签名很慢。 相反，它们在安装之前或导出过程中由 IDE 或安装生成目标进行签名（如有必要）。 调用 SignAndroidPackage 目标将生成一个在输出目录中带有 `-Signed.apk` 后缀的包。

默认情况下，如果需要，签名目标会生成一个新的调试签名密钥。 如果你希望使用特定的密钥，例如在生成服务器上，则可以使用以下 MSBuild 属性：

-   AndroidKeyStore &ndash; 一个布尔值，指示是否应使用自定义签名信息。 默认值是 `False`，这意味着将使用默认的调试签名密钥来对包进行签名。

-   AndroidSigningKeyAlias &ndash; 指定密钥存储中密钥的别名。 这是创建密钥存储时使用的 keytool -alias 值。 

-   AndroidSigningKeyPass &ndash; 指定密钥存储文件中密钥的密码。 这是在 `keytool` 要求“输入 $(AndroidSigningKeyAlias) 的密匙密码”时输入的值。

-   AndroidSigningKeyStore &ndash; 指定由 `keytool` 创建的密钥存储文件的文件名。 这对应于提供给 keytool -keystore 选项的值。

-   AndroidSigningStorePass &ndash; 指定 `$(AndroidSigningKeyStore)` 的密码。 这是在创建密钥存储文件并要求“输入密钥存储密码:”时为 `keytool` 提供的值。 

例如，请考虑以下 `keytool` 调用：

```shell
$ keytool -genkey -v -keystore filename.keystore -alias keystore.alias -keyalg RSA -keysize 2048 -validity 10000
Enter keystore password: keystore.filename password
Re-enter new password: keystore.filename password
...
Is CN=... correct?
  [no]:  yes

Generating 2,048 bit RSA key pair and self-signed certificate (SHA1withRSA) with a validity of 10,000 days
        for: ...
Enter key password for keystore.alias
        (RETURN if same as keystore password): keystore.alias password
[Storing filename.keystore]
```

要使用上面生成的密钥存储，请使用属性组：

```xml
<PropertyGroup>
    <AndroidKeyStore>True</AndroidKeyStore>
    <AndroidSigningKeyStore>filename.keystore</AndroidSigningKeyStore>
    <AndroidSigningStorePass>keystore.filename password</AndroidSigningStorePass>
    <AndroidSigningKeyAlias>keystore.alias</AndroidSigningKeyAlias>
    <AndroidSigningKeyPass>keystore.alias password</AndroidSigningKeyPass>
</PropertyGroup>
```

-   **AndroidDebugKeyAlgorithm** &ndash; 指定要用于 `debug.keystore` 的默认算法。 默认为 `RSA`。

-   **AndroidDebugKeyValidity** &ndash; 指定要用于 `debug.keystore` 的默认有效期。 默认值为 `10950`、`30 * 365` 或 `30 years`。

<a name="Build_Actions" />

## <a name="build-actions"></a>生成操作

生成操作将[应用于项目中的文件](https://docs.microsoft.com/visualstudio/msbuild/common-msbuild-project-items)，并控制文件的处理方式。 

<a name="AndroidEnvironment" />

### <a name="androidenvironment"></a>AndroidEnvironment

生成操作为 `AndroidEnvironment` 的文件用于[在过程启动期间初始化环境变量和系统属性](~/android/deploy-test/environment.md)。
`AndroidEnvironment` 生成操作可能会应用于多个文件，并且它们将以特定顺序进行评估（因此，不要在多个文件中指定相同的环境变量或系统属性）。


### <a name="androidjavasource"></a>AndroidJavaSource

生成操作为 `AndroidJavaSource` 的文件是 Java 源代码，将包含在最终的 Android 程序包中。


### <a name="androidjavalibrary"></a>AndroidJavaLibrary

生成操作为 `AndroidJavaLibrary` 的文件是 Java 归档（`.jar` 文件），它将包含在最终的 Android 程序包中。


### <a name="androidresource"></a>AndroidResource

生成操作为 AndroidResource 的所有文件都将在生成过程中编译为 Android 资源，并可通过 `$(AndroidResgenFile)` 访问。

```xml
<ItemGroup>
  <AndroidResource Include="Resources\values\strings.xml" />
</ItemGroup>
```

更高级的用户可能希望在不同的配置中使用不同的资源，但使用相同的有效路径。 为此，可以使用多个资源目录并使具有相同相对路径的文件放在这些不同的目录中，以及使用 MSBuild 条件来有条件地在不同配置中包括不同文件。 例如:

```xml
<ItemGroup Condition="'$(Configuration)'!='Debug'">
  <AndroidResource Include="Resources\values\strings.xml" />
</ItemGroup>
<ItemGroup  Condition="'$(Configuration)'=='Debug'">
  <AndroidResource Include="Resources-Debug\values\strings.xml"/>
</ItemGroup>
<PropertyGroup>
  <MonoAndroidResourcePrefix>Resources;Resources-Debug<MonoAndroidResourcePrefix>
</PropertyGroup>
```

LogicalName &ndash; 显式指定资源路径。 允许使用 &ldquo;aliasing&rdquo; 文件，以便它们可用作多个不同的资源名称。

```xml
<ItemGroup Condition="'$(Configuration)'!='Debug'">
  <AndroidResource Include="Resources/values/strings.xml"/>
</ItemGroup>
<ItemGroup Condition="'$(Configuration)'=='Debug'">
  <AndroidResource Include="Resources-Debug/values/strings.xml">
    <LogicalName>values/strings.xml</LogicalName>
  </AndroidResource>
</ItemGroup>
```

### <a name="androidboundlayout"></a>AndroidBoundLayout

指示 `AndroidGenerateLayoutBindings` 属性设置为 `false` 时，系统会为布局文件生成代码隐藏。 在所有其他情况下，它与上面所述的 `AndroidResource` 相同。 此操作仅适用于布局文件：

```xml
<AndroidBoundLayout Include="Resources\layout\Main.axml" />
```

### <a name="androidfragmenttype"></a>AndroidFragmentType

指定生成布局绑定代码时，要用于所有 `<fragment>` 布局元素的默认完全限定的类型。 该属性默认为标准的 Android `Android.App.Fragment` 类型。


### <a name="androidnativelibrary"></a>AndroidNativeLibrary

通过将生成操作设置为 `AndroidNativeLibrary`，将[本机库](~/android/platform/native-libraries.md)添加到版本中。

请注意，由于 Android 支持多个应用程序二进制接口 (ABI)，因此生成系统必须知道本机库是为哪个 ABI 生成的。 可以通过两种方法完成：

1.  路径“探查”。
2.  使用 `Abi` 项目属性。

通过路径探查，本机库的父目录名称用于指定库的目标 ABI。 因此，如果将 `lib/armeabi-v7a/libfoo.so` 添加到版本中，则 ABI 将被“探查”为 `armeabi-v7a`。 


#### <a name="item-attribute-name"></a>项属性名称

Abi &ndash; 指定本机库的 ABI。

```xml
<ItemGroup>
  <AndroidNativeLibrary Include="path/to/libfoo.so">
    <Abi>armeabi-v7a</Abi>
  </AndroidNativeLibrary>
</ItemGroup>
```


### <a name="androidaarlibrary"></a>AndroidAarLibrary

生成操作 `AndroidAarLibrary` 应用于直接引用 .aar 文件。 Xamarin 组件最常使用此生成操作。 也就是说，要添加对 .aar 文件的引用，它们是 Google Play 和其他服务正常运行所必需。

包含此生成操作的文件的处理方式类似于库项目中嵌入的资源。 .aar 会被提取到中间目录。 然后，任何资产、资源和 .jar 文件都会被添加到相应项组中。 

### <a name="androidlintconfig"></a>AndroidLintConfig

应将“AndroidLintConfig”生成操作与 `AndroidLintEnabled` 生成属性结合使用。 系统将使用此生成操作的文件合并起来并传递给 Android `lint` 工具。 它们应当是包含要启用/禁用哪些测试的信息的 xml 文件。

有关详细信息，请参阅 [lint 文档](http://www.androiddocs.com/tools/help/lint.html)。

### <a name="content"></a>内容

不支持正常的 `Content` 生成操作（因为我们还未想出如何在没有成本可能昂贵的首次运行步骤的情况下支持它）。

从 Xamarin.Android 5.1 开始，尝试使用 `@(Content)` 生成操作将导致 `XA0101` 警告。

### <a name="linkdescription"></a>LinkDescription

生成操作为 LinkDescription 的文件用于[控制链接器行为](~/cross-platform/deploy-test/linker.md)。


<a name="ProguardConfiguration" />

### <a name="proguardconfiguration"></a>ProguardConfiguration

生成操作为 ProguardConfiguration 的文件包含用于控制 `proguard` 行为的选项。 有关此生成操作的更多信息，请参阅 [ProGuard](~/android/deploy-test/release-prep/proguard.md)。

除非 `$(EnableProguard)` MSBuild 属性为 `True`，否则这些文件将被忽略。


## <a name="target-definitions"></a>目标定义

生成过程的 Xamarin.Android 特定部分在 `$(MSBuildExtensionsPath)\Xamarin\Android\Xamarin.Android.CSharp.targets` 中定义，但生成该程序集时也需要使用正常的语言特定目标，如 Microsoft.CSharp.targets。

导入任何语言目标之前，必须设置以下生成属性：

```xml
<PropertyGroup>
  <TargetFrameworkIdentifier>MonoDroid</TargetFrameworkIdentifier>
  <MonoDroidVersion>v1.0</MonoDroidVersion>
  <TargetFrameworkVersion>v2.2</TargetFrameworkVersion>
</PropertyGroup>
```

通过导入 Xamarin.Android.CSharp.targets，可在 C# 中包含所有这些目标和属性： 

```xml
<Import Project="$(MSBuildExtensionsPath)\Xamarin\Android\Xamarin.Android.CSharp.targets" />
```

该文件可轻松适应于其他语言。
