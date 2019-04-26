---
title: Xamarin.Mac 错误消息 (mmp)
description: 本文档列出了由 mmp，用来打包到可执行文件的 Mac 应用程序编译的程序集的工具生成的错误。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5B26339F-A202-4E41-9229-D0BC9E77868E
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/27/2018
ms.openlocfilehash: 640d1adc048bec167508d8c288b62d498f061b0d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61233197"
---
# <a name="xamarinmac-error-messages-mmp"></a>Xamarin.Mac 错误消息 (mmp)

## <a name="mm0xxx-mmp-error-messages"></a>MM0xxx: mmp 错误消息

例如， 参数，环境中，缺少工具。

<a name="MM0000" />

#### <a name="mm0000-unexpected-error---please-file-a-bug-report-at-httpsgithubcomxamarinxamarin-maciosissuesnew"></a>MM0000:意外的错误-请文件 bug 报告在 https://github.com/xamarin/xamarin-macios/issues/new

出现意外的错误条件。 请[提交 bug 报告](https://github.com/xamarin/xamarin-macios/issues/new)提供尽可能多的信息可能，包括：

* 完整生成日志，与最大详细级别 (例如`-v -v -v -v`中**其他 mmp 参数**);
* 重新生成错误; 的最小测试用例和
* 所有版本信息

若要获取确切的版本信息的最简单方法是使用**Xamarin Studio**菜单中，**关于 Xamarin Studio**项，**显示详细信息**按钮，然后复制/粘贴版本信息 (可以使用**复制信息**按钮)。

<a name="MM0001" />

#### <a name="mm0001-this-version-of-xamarinmac-requires-mono-0-the-current-mono-version-is-1-please-update-the-monoframework-from-httpmono-projectcomdownloads"></a>MM0001:此版本的 Xamarin.Mac 需要 Mono {0} (当前的 Mono 版本是{1})。 请更新从 Mono.framework http://mono-project.com/Downloads

<a name="MM0003" />

#### <a name="mm0003-application-name-0exe-conflicts-with-an-sdk-or-product-assembly-dll-name"></a>MM0003:应用程序名称{0}.exe 与 SDK 或产品程序集 (.dll) 名称冲突。

<a name="MM0007" />

#### <a name="mm0007-the-root-assembly-0-does-not-exist"></a>MM0007:根程序集{0}不存在

<a name="MM0008" />

#### <a name="mm0008-you-should-provide-one-root-assembly-only-found-0-assemblies-1"></a>MM0008:应提供一个根程序集唯一，但发现{0}程序集:{1}

<a name="MM0010" />

#### <a name="mm0010-could-not-parse-the-command-line-arguments-0"></a>MM0010:无法分析命令行参数： {0}

<!-- 0013 is unused -->

<a name="MM0016" />

#### <a name="mm0016-the-option-0-has-been-deprecated"></a>MM0016:选择{0}已弃用。

<a name="MM0017" />

#### <a name="mm0017-you-should-provide-a-root-assembly"></a>MM0017:应提供根程序集

<a name="MM0018" />

#### <a name="mm0018-unknown-command-line-argument-0"></a>MM0018:未知的命令行参数:{0}

<a name="MM0020" />

#### <a name="mm0020-the-valid-options-for-0-are-1"></a>MM0020:有效选项为 '{0}是{1}。

<a name="MM0023" />

#### <a name="mm0023-application-name-0exe-conflicts-with-another-user-assembly"></a>MM0023:应用程序名称{0}.exe 与另一个用户程序集冲突。

<a name="MM0026" />

#### <a name="mm0026-could-not-parse-the-command-line-argument-0-1"></a>MM0026:无法分析命令行参数{0}: {1}

<a name="MM0043" />

#### <a name="mm0043-the-boehm-garbage-collector-is-not-supported-the-sgen-garbage-collector-has-been-selected-instead"></a>MM0043:Boehm 垃圾回收器不支持。 选择 SGen 垃圾回收器。

<a name="MM0050" />

#### <a name="mm0050-you-cannot-provide-a-root-assembly-if---no-root-assembly-is-passed"></a>MM0050:如果-传递否根程序集，不能提供根程序集。

<a name="MM0051" />

#### <a name="mm0051-an-output-directory---output-is-required-if---no-root-assembly-is-passed"></a>MM0051:输出目录 (-输出) 如果-传递否根程序集是必需的。

<a name="MM0053" />

#### <a name="mm0053-cannot-disable-new-refcount-with-the-unified-api"></a>MM0053:不能禁用使用统一 API 的新引用计数。

<a name="MM0056" />

#### <a name="mm0056-cannot-find-xcode-in-any-of-our-default-locations-please-install-xcode-or-pass-a-custom-path-using---sdkrootpath"></a>MM0056:我们的默认位置中找不到 Xcode。 请安装 Xcode，或通过使用--sdkroot 的自定义路径 =<path>

<a name="MM0059" />

#### <a name="mm0059-could-not-find-the-currently-selected-xcode-on-the-system-0"></a>MM0059:找不到系统上当前所选的 Xcode: {0};

<a name="MM0060" />

#### <a name="mm0060-could-not-find-the-currently-selected-xcode-on-the-system-xcode-select---print-path-returned-0-but-that-directory-does-not-exist"></a>MM0060:在系统上找不到当前所选的 Xcode。 ' xcode-select--打印路径返回{0}，但该目录不存在。

<a name="MM0068" />

#### <a name="mm0068-invalid-value-for-target-framework-0"></a>MM0068:目标框架的值无效： {0}。

<a name="MM0071" />

#### <a name="mm0071-unknown-platform--this-usually-indicates-a-bug-in-xamarinmac-please-file-a-bug-report-at-httpsbugzillaxamarincom-with-a-test-case"></a>MM0071:未知的平台: *。 这通常表示 Xamarin.Mac; 中的 bug在一个 bug 报告，请记录 https://bugzilla.xamarin.com 与测试用例。

这通常表示 Xamarin.Mac; 中的 bug请提交在 bug 报告[ https://bugzilla.xamarin.com ](https://bugzilla.xamarin.com/enter_bug.cgi?product=Xamarin.Mac)与测试用例。

<a name="MM0079" />

#### <a name="mm0079-internal-error---no-executable-was-copied-into-the-app-bundle-please-contact-supportxamarincom"></a>MM0079:内部错误-没有可执行文件复制到应用程序捆绑包。 请联系support@xamarin.com

<a name="MM0080" />

#### <a name="mm0080-disabling-newrefcount---new-refcountfalse-is-deprecated"></a>MM0080:禁用 NewRefCount、--new-refcount:false，已弃用。

<!-- 0088 used by mtouch -->
<!-- 0089 used by mtouch -->

<a name="MM0091" />

#### <a name="mm0091-this-version-of-xamarinmac-requires-the--sdk-shipped-with-xcode--either-upgrade-xcode-to-get-the-required-header-files-or-use-the-dynamic-registrar-or-set-the-managed-linker-behaviour-to-link-platform-or-link-framework-sdks-only-to-try-to-avoid-the-new-apis"></a>MM0091:此版本的 Xamarin.Mac 需要 * SDK (随 Xcode *)。 可以将升级 Xcode 以获取必需的标头文件或使用动态注册机构或托管链接器行为设置为链接平台或仅上链接 Framework Sdk （若要尽量避免新的 Api）。

Xamarin.Mac 需要标头文件，从要生成应用程序上的静态注册的错误消息中指定的 SDK 版本。 若要修复此错误的建议的方法是升级 Xcode 以获取所需的 SDK，这将包括所有必需的标头文件。 如果有多个版本的 Xcode 安装，或者想要使用 Xcode 中的非默认位置，请务必在 IDE 的首选项中设置正确的 Xcode 位置。

一种潜在的备用解决方案，是能够托管链接器。 这将删除未使用 API 包括，在大多数情况下，新的 API 标头文件的丢失 （或不完整） 的位置。 但是这不起如果项目使用了较新的 SDK 比一个你的 Xcode 中引入的 API 提供。

第二个潜在的备用解决方案中，改为使用动态注册机构。 这将通过动态注册类型施加的启动成本，但删除标头文件要求。 

最后的办法-就解决方案是使用较旧版本的 Xamarin.Mac，另一个支持你的项目的 SDK 需要。

<a name="MM0097" />

#### <a name="mm0097-machineconfig-file-0-can-not-be-found"></a>MM0097: machine.config 文件{0}找不到。

<a name="MM0098" />

#### <a name="mm0098-aot-compilation-is-only-available-on-unified"></a>MM0098:AOT 编译上才统一

<a name="MM0099" />

#### <a name="mm0099-internal-error-0-please-file-a-bug-report-with-a-test-case-httpbugzillaxamarincom"></a>MM0099:内部错误{0}。 请提交 bug 报告与测试用例 ( http://bugzilla.xamarin.com)。

<a name="MM0114" />

#### <a name="mm0114-hybrid-aot-compilation-requires-all-assemblies-to-be-aot-compiled"></a>MM0114:混合 AOT 编译需要要进行 AOT 编译的所有程序集。

## <a name="mm1xxx-file-copy--symlinks-project-related"></a>MM1xxx： 文件复制 / 符号链接 （相关项目）

<a name="MM1034" />

#### <a name="mm1034-could-not-create-symlink-file---target-error-number"></a>MM1034:无法创建符号链接 {file}-> {target}： 错误 {number}

### <a name="mm14xx-product-assemblies"></a>MM14xx:产品程序集

<a name="MM1401" />

#### <a name="mm1401-the-required-0-assembly-is-missing-from-the-references"></a>MM1401:所需{0}从引用缺少程序集

<a name="MM1402" />

#### <a name="mm1402-the-assembly-0-is-not-compatible-with-this-tool"></a>MM1402:程序集{0}与此工具不兼容

<a name="MM1403" />

#### <a name="mm1403-0-1-could-not-be-found-target-framework-0-is-unusable-to-package-the-application"></a>MM1403: {0} {1}未找到。 目标框架{0}不可用，将应用程序打包。

<a name="MM1404" />

#### <a name="mm1404-target-framework-0-is-invalid"></a>MM1404:目标框架{0}无效。

<a name="MM1405" />

#### <a name="mm1405-usefullxammacframework-must-always-target-framework-net-45-not-0-which-is-invalid"></a>MM1405: useFullXamMacFramework 必须始终 framework 为目标.NET 4.5 中，不{0}的无效

<a name="MM1406" />

#### <a name="mm1406-target-framework-0-is-invalid-when-targetting-xamarinmac-45-net-framwork"></a>MM1406:目标框架{0}是无效针对 Xamarin.Mac 4.5.NET framwork。

<a name="MM1407" />

#### <a name="mm1407-mismatch-between-xamarinmac-reference-0-and-target-framework-selected-1"></a>MM1407:Xamarin.Mac 引用不匹配{0}并选择目标框架{1}。

### <a name="mm15xx-assembly-gathering-not-requiring-linker-errors"></a>MM15xx:程序集 （不需要链接器） 正在收集错误

<a name="MM1501" />

#### <a name="mm1501-can-not-resolve-reference-0"></a>MM1501:无法解析引用： {0}

### <a name="machocs"></a>MachO.cs

<a name="MM1600" />

#### <a name="mm1600-not-a-mach-o-dynamic-library-unknown-header-0x0-1"></a>MM1600:不是 MACH-O 动态库 (未知标头"0 x{0}): {1}。

<a name="MM1601" />

#### <a name="mm1601-not-a-static-library-unknown-header-0-1"></a>MM1601:不是静态库 (未知标头{0}): {1}。

<a name="MM1602" />

#### <a name="mm1602-not-a-mach-o-dynamic-library-unknown-header-0x0-1"></a>MM1602:不是 MACH-O 动态库 (未知标头"0 x{0}): {1}。

<a name="MM1603" />

#### <a name="mm1603-unknown-format-for-fat-entry-at-position-0-in-1"></a>MM1603:Fat 位置处的项的格式未知{0}在{1}。

<a name="MM1604" />

#### <a name="mm1604-file-of-type-0-is-not-a-macho-file-1"></a>MM1604:类型的文件{0}不是 MachO 文件 ({1})。

## <a name="mm2xxx-linker"></a>MM2xxx:链接器

### <a name="mm20xx-linker-general-errors"></a>MM20xx:链接器 （常规） 错误

<a name="MM2001" />

#### <a name="mm2001-could-not-link-assemblies"></a>MM2001:无法将链接的程序集

<a name="MM2002" />

#### <a name="mm2002-can-not-resolve-reference-0"></a>MM2002:无法解析引用： {0}

<a name="MM2003" />

#### <a name="mm2003-option-0-will-be-ignored-since-linking-is-disabled"></a>MM2003:选项{0}将被忽略，因为禁用了链接

<a name="MM2004" />

#### <a name="mm2004-extra-linker-definitions-file-0-could-not-be-located"></a>MM2004:额外的链接器定义文件{0}找不到。

<a name="MM2005" />

#### <a name="mm2005-definitions-from-0-could-not-be-parsed"></a>MM2005:定义从{0}无法分析。

<a name="MM2006" />

#### <a name="mm2006-native-library-0-was-referenced-but-could-not-be-found"></a>MM2006:本机库{0}引用，但未找到。

<a name="MM2007" />

#### <a name="mm2007-xamarinmac-unified-api-against-a-full-net-profile-does-not-support-linking-pass-the--nolink-flag"></a>MM2007:Xamarin.Mac Unified API 针对完整.NET 配置文件不支持链接。 传递-nolink 标志。

<a name="MM2009" />

#### <a name="mm2009-referenced-by-01------this-message-is-related-to-mm2006-"></a>MM2009:所引用的{0}。{1}    * * 此消息与 MM2006 * *

<a name="MM2010" />

#### <a name="mm2010-unknown-httpmessagehandler-0-valid-values-are-httpclienthandler-default-cfnetworkhandler-or-nsurlsessionhandler"></a>MM2010:未知的 HttpMessageHandler `{0}`。 有效值为 HttpClientHandler （默认值）、 CFNetworkHandler 或 NSUrlSessionHandler

<a name="MM2011" />

#### <a name="mm2011-unknown-tlsprovider-0--valid-values-are-default-or-appletls"></a>MM2011:未知的 TLSProvider{0}。  有效的值为默认值或 appletls

<a name="MM2012" />

#### <a name="mm2012-only-first-0-of-1-referenced-by-warnings-shown--this-message-related-to-2009-"></a>MM2012:仅第一个{0}的{1}"引用"所示的警告。 ** 此消息与 2009年 \*\*

<a name="MM2013" />

#### <a name="mm2013-failed-to-resolve-the-reference-to-0-referenced-in-1-the-app-will-not-include-the-referenced-assembly-and-may-fail-at-runtime"></a>MM2013:无法解析到引用"{0}"，在被引用的"{1}"。 该应用将不包括引用的程序集，并在运行时可能会失败。

<a name="MM2014" />

#### <a name="mm2014-xamarinmac-extensions-do-not-support-linking-request-for-linking-will-be-ignored--this-message-is-obsolete-in-xm-36-"></a>MM2014:Xamarin.Mac 扩展不支持链接。 请求的链接将被忽略。 ** 此消息在中已过时 XM 3.6 + \*\*

<!-- 2015 used by mtouch -->

<a name="MM2016" />

#### <a name="mm2016-invalid-tlsprovider-0-option-the-only-valid-value-1-will-be-used"></a>MM2016:无效 TlsProvider`{0}`选项。 唯一有效的值`{1}`将使用。

<a name="MM2017" />

#### <a name="mm2017-could-not-process-xml-description-0"></a>MM2017:无法处理 XML 说明： {0}

<a name="MM202x" />

#### <a name="mm202x-binding-optimizer-failed-processing-"></a>MM202x:绑定优化器处理失败`...`。

<a name="MM2100" />

#### <a name="mm2100-xamarinmac-classic-api-does-not-support-platform-linking"></a>MM2100:Xamarin.Mac 经典 API 不支持平台链接。

<a name="MM2103" />

#### <a name="mm2103-error-processing-assembly--"></a>MM2103:错误处理程序集\*: *

处理程序集时发生意外的错误。

错误消息中名为导致出现此问题的程序集。 若要解决此问题的程序集将需要在中提供[bug 报表](https://bugzilla.xamarin.com)以及完整的生成日志包含详细级别启用 (即`-v -v -v -v`中**其他 mtouch 参数**)。

<a name="MM2104" />

#### <a name="mm2104-unable-to-link-assembly-0-as-it-is-mixed-mode"></a>MM2104:无法链接程序集{0}按原样混合模式。

链接器不会处理混合模式程序集。

请参阅 https://msdn.microsoft.com/library/x0w2664k.aspx 为混合模式程序集的详细信息。

## <a name="mm3xxx-aot"></a>MM3xxx:AOT

### <a name="mm30xx-aot-general-errors"></a>MM30xx:AOT （常规） 错误

<a name="MM3001" />

#### <a name="mm3001-could-not-aot-the-assembly-0"></a>MM3001:可以不 AOT 的程序集{0}

<!-- 3002 used by mtouch -->
<!-- 3003 used by mtouch -->
<!-- 3004 used by mtouch -->
<!-- 3005 used by mtouch -->
<!-- 3006 used by mtouch -->
<!-- 3007 used by mtouch -->
<!-- 3008 used by mtouch -->

<a name="MM3009" />

#### <a name="mm3009-aot-of-0-was-requested-but-was-not-found"></a>MM3009:AOT{0}已请求但找不到

<a name="MM3010" />

#### <a name="mm3010-exclusion-of-aot-of-0-was-requested-but-was-not-found"></a>MM3010:排除的 AOT{0}已请求但找不到

## <a name="mm4xxx-code-generation"></a>MM4xxx： 代码生成

### <a name="mm40xx-driverm"></a>MM40xx: driver.m

<a name="MM4001" />

#### <a name="mm4001-the-main-template-could-not-be-expanded-to-0"></a>MM4001:主模板无法扩展到`{0}`。

### <a name="mm41xx-registrar"></a>MM41xx： 注册机构

<a name="MM4134" />

#### <a name="mm4134-your-application-is-using-the-0-framework-which-isnt-included-in-the-macos-sdk-youre-using-to-build-your-app-this-framework-was-introduced-in-osx-2-while-youre-building-with-the-macos-1-sdk-this-configuration-is-not-supported-with-the-static-registrar-pass---registrardynamic-as-an-additional-mmp-argument-in-your-projects-mac-build-option-to-select-alternatively-select-a-newer-sdk-in-your-apps-mac-build-options"></a>MM4134:使用你的应用程序{0}框架，不包括在 MacOS SDK 要使用来构建您的应用程序 (此框架在 OSX 中引入{2}，而正在生成的 MacOS {1} SDK。)此配置不支持使用静态注册机构 (pass-注册机构： 动态作为在您的项目的 Mac 生成选项中选择其他 mmp 参数)。 或者您的应用程序的 Mac 生成选项中选择更高版本的 SDK。

## <a name="mm5xxx-gcc-and-toolchain"></a>MM5xxx:GCC 和工具链

### <a name="mm51xx-compilation"></a>MM51xx： 编译

<a name="MM5101" />

#### <a name="mm5101-missing-0-compiler-please-install-xcode-command-line-tools-component"></a>MM5101:缺少{0}编译器。 请安装 Xcode 命令行工具组件。

<!-- 5102 used by mtouch -->

<a name="MM5103" />

#### <a name="mm5103-failed-to-compile-error-code---0-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MM5103:编译失败。 错误代码- {0}。 请提交在 bug 报告 http://bugzilla.xamarin.com

<!-- 5104 used by mtouch -->

### <a name="mm52xx-linking"></a>MM52xx： 链接

<a name="MM5202" />

#### <a name="mm5202-monoframework-mdk-is-missing-please-install-the-mdk-for-your-monoframework-version-from-httpmono-projectcomdownloads"></a>MM5202:找不到 Mono.framework MDK。 请安装适用于从你 Mono.framework 版本 MDK http://mono-project.com/Downloads

<a name="MM5203" />

#### <a name="mm5203-cant-find-libxammaca-likely-because-of-a-corrupted-xamarinmac-installation-please-reinstall-xamarinmac"></a>MM5203:找不到 libxammac.a，可能由于 Xamarin.Mac 安装已损坏。 请重新安装 Xamarin.Mac。

<a name="MM5204" />

#### <a name="mm5204-invalid-architecture-x8664-is-only-supported-on-non-classic-profiles"></a>MM5204:无效的体系结构。 x86_64 仅支持对非经典配置文件。

<a name="MM5205" />

#### <a name="mm5205-invalid-architecture-0-valid-architectures-are-i386-and-x8664-when---profilemobile"></a>MM5205:无效的体系结构{0}。 有效的体系结构是 i386 和 x86_64 (当-配置文件 = 移动)。

<a name="MM5218" />

#### <a name="mm5218-cant-ignore-the-dynamic-symbol-symbol---ignore-dynamic-symbolsymbol-because-it-was-not-detected-as-a-dynamic-symbol"></a>MM5218:不能忽略动态符号 {符号} (-忽略动态符号 = {符号}) 由于未被检测为动态的符号。

请参阅[等效 mtouch 警告](~/ios/troubleshooting/mtouch-errors.md#MT5218)。

<!-- 5206 used by mtouch -->
<!-- 5207 used by mtouch -->
<!-- 5208 used by mtouch -->
<!-- 5209 used by mtouch -->
<!-- 5210 used by mtouch -->
<!-- 5211 used by mtouch -->
<!-- 5212 used by mtouch -->
<!-- 5213 used by mtouch -->
<!-- 5214 used by mtouch -->
<!-- 5215 used by mtouch -->
<!-- 5216 used by mtouch -->
<!-- 5217 used by mtouch -->

### <a name="mm53xx-other-tools"></a>其他工具 MM53xx:

<a name="MM5301" />

#### <a name="mm5301-pkg-config-could-not-be-found-please-install-the-monoframework-from-httpmono-projectcomdownloads"></a>MM5301： 找不到包配置。 请安装从 Mono.framework http://mono-project.com/Downloads

<!-- 5302 used by mtouch -->
<!-- 5303 used by mtouch -->
<!-- 5304 used by mtouch -->

<a name="MM5305" />

#### <a name="mm5305-missing-otool-tool-please-install-xcode-command-line-tools-component"></a>MM5305:缺少 otool 工具。 请安装 Xcode 命令行工具组件

<a name="MM5306" />

#### <a name="mm5306-missing-dependencies-please-install-xcode-command-line-tools-component"></a>MM5306:缺少的依赖项。 请安装 Xcode 命令行工具组件

<a name="MM5308" />

#### <a name="mm5308-xcode-license-agreement-may-not-have-been-accepted--please-launch-xcode"></a>MM5308:可能不接受 Xcode 许可证协议。  请启动 Xcode。

<a name="MM5309" />

#### <a name="mm5309-native-linking-failed-with-error-code-1--check-build-log-for-details"></a>MM5309:本机链接失败，错误代码为 1。  有关详细信息的生成日志中检查。

<a name="MM5310" />

#### <a name="mm5310-installnametool-failed-with-an-error-code-0-check-build-log-for-details"></a>MM5310: install_name_tool 失败，错误代码{0}。 有关详细信息的生成日志中检查。

<!-- MM6xxx: mmp internal tools -->
<!-- MM7xxx: reserved -->

## <a name="mm8xxx-runtime"></a>MM8xxx： 运行时

### <a name="mm800x-misc"></a>MM800x： 杂项

<!-- 8000 used by mtouch -->
<!-- 8001 used by mtouch -->
<!-- 8002 used by mtouch -->
<!-- 8003 used by mtouch -->
<!-- 8004 used by mtouch -->
<!-- 8005 used by mtouch -->
<!-- 8006 used by mtouch -->
<!-- 8007 used by mtouch -->
<!-- 8008 used by mtouch -->
<!-- 8009 used by mtouch -->
<!-- 8010 used by mtouch -->
<!-- 8011 used by mtouch -->
<!-- 8012 used by mtouch -->
<!-- 8013 used by mtouch -->
<!-- 8014 used by mtouch -->
<!-- 8015 used by mtouch -->
<!-- 8016 used by mtouch -->

<a name="MM8017" />

#### <a name="mm8017-the-boehm-garbage-collector-is-not-supported-please-use-sgen-instead"></a>MM8017:Boehm 垃圾回收器不支持。 请改为使用 SGen。

