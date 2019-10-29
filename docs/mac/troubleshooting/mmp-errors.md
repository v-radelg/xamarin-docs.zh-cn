---
title: Xamarin 错误消息（mmp）
description: 本文档列出了 mmp 生成的错误，该工具用于将编译的程序集打包到可执行的 Mac 应用程序。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5B26339F-A202-4E41-9229-D0BC9E77868E
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/27/2018
ms.openlocfilehash: 32fecaff7f6896c03f3ac094d478fef3338f844d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025724"
---
# <a name="xamarinmac-error-messages-mmp"></a>Xamarin 错误消息（mmp）

## <a name="mm0xxx-mmp-error-messages"></a>MM0xxx： mmp 错误消息

例如， 参数、环境、缺少的工具。

<a name="MM0000" />

#### <a name="mm0000-unexpected-error---please-file-a-bug-report-at-httpsgithubcomxamarinxamarin-maciosissuesnew"></a>MM0000：意外错误-请在 https://github.com/xamarin/xamarin-macios/issues/new 提交 bug 报告

出现意外错误。 请提交[错误报告](https://github.com/xamarin/xamarin-macios/issues/new)，其中包含尽可能多的信息，包括：

* 完整生成日志，具有最大详细级别（例如 `-v -v -v -v` 在**附加的 mmp 参数**中）;
* 再现错误的最小测试用例;与
* 所有版本的

获取确切版本信息的最简单方法是使用 " **Xamarin Studio** " 菜单，**关于 Xamarin Studio**项，"**显示详细信息**" 按钮，然后复制/粘贴版本信息（可以使用 "**复制信息**" 按钮）。

<a name="MM0001" />

#### <a name="mm0001-this-version-of-xamarinmac-requires-mono-0-the-current-mono-version-is-1-please-update-the-monoframework-from-httpmono-projectcomdownloads"></a>MM0001：此版本的 Xamarin 需要 Mono {0} （当前 Mono 版本 {1}）。 请从 http://mono-project.com/Downloads 更新 Mono framework

<a name="MM0003" />

#### <a name="mm0003-application-name-0exe-conflicts-with-an-sdk-or-product-assembly-dll-name"></a>MM0003：应用程序名称 "{0}" 与 SDK 或产品程序集（.dll）名称冲突。

<a name="MM0007" />

#### <a name="mm0007-the-root-assembly-0-does-not-exist"></a>MM0007：根程序集 "{0}" 不存在

<a name="MM0008" />

#### <a name="mm0008-you-should-provide-one-root-assembly-only-found-0-assemblies-1"></a>MM0008：应仅提供一个根程序集，但找到 {0} 程序集： "{1}"

<a name="MM0009" />

#### <a name="mm0009-error-while-loading-assemblies-"></a>MM0009：加载程序集时出错： *。

从根程序集引用加载程序集时出错。 生成输出中可能提供详细信息。

<a name="MM0010" />

#### <a name="mm0010-could-not-parse-the-command-line-arguments-0"></a>MM0010：无法分析命令行参数： {0}

<!-- 0013 is unused -->

<a name="MM0016" />

#### <a name="mm0016-the-option-0-has-been-deprecated"></a>MM0016：选项 "{0}" 已弃用。

<a name="MM0017" />

#### <a name="mm0017-you-should-provide-a-root-assembly"></a>MM0017：应提供根程序集

<a name="MM0018" />

#### <a name="mm0018-unknown-command-line-argument-0"></a>MM0018：未知的命令行参数： "{0}"

<a name="MM0020" />

#### <a name="mm0020-the-valid-options-for-0-are-1"></a>MM0020： "{0}" 的有效选项为 "{1}"。

<a name="MM0023" />

#### <a name="mm0023-application-name-0exe-conflicts-with-another-user-assembly"></a>MM0023：应用程序名称 "{0}" 与其他用户程序集冲突。

<a name="MM0026" />

#### <a name="mm0026-could-not-parse-the-command-line-argument-0-1"></a>MM0026：无法分析命令行参数 "{0}"： {1}

<a name="MM0043" />

#### <a name="mm0043-the-boehm-garbage-collector-is-not-supported-the-sgen-garbage-collector-has-been-selected-instead"></a>MM0043：不支持 Boehm 垃圾回收器。 改为选择 SGen 垃圾回收器。

<a name="MM0050" />

#### <a name="mm0050-you-cannot-provide-a-root-assembly-if---no-root-assembly-is-passed"></a>MM0050：如果未传递任何根程序集，则无法提供根程序集。

<a name="MM0051" />

#### <a name="mm0051-an-output-directory---output-is-required-if---no-root-assembly-is-passed"></a>MM0051：如果已传递--无根程序集，则需要输出目录（--output）。

<a name="MM0053" />

#### <a name="mm0053-cannot-disable-new-refcount-with-the-unified-api"></a>MM0053：无法通过 Unified API 禁用新的引用计数。

<a name="MM0056" />

#### <a name="mm0056-cannot-find-xcode-in-any-of-our-default-locations-please-install-xcode-or-pass-a-custom-path-using---sdkrootpath"></a>MM0056：在任何默认位置都找不到 Xcode。 请安装 Xcode，或使用--sdkroot =\<路径传递自定义路径 >

<a name="MM0059" />

#### <a name="mm0059-could-not-find-the-currently-selected-xcode-on-the-system-0"></a>MM0059：在系统上找不到当前选择的 Xcode： {0};

<a name="MM0060" />

#### <a name="mm0060-could-not-find-the-currently-selected-xcode-on-the-system-xcode-select---print-path-returned-0-but-that-directory-does-not-exist"></a>MM0060：在系统上找不到当前选择的 Xcode。 "xcode" 返回了 "{0}"，但该目录不存在。

<a name="MM0068" />

#### <a name="mm0068-invalid-value-for-target-framework-0"></a>MM0068：目标框架的值无效： {0}。

<a name="MM0071" />

#### <a name="mm0071-unknown-platform--this-usually-indicates-a-bug-in-xamarinmac-please-file-a-bug-report-at-httpsbugzillaxamarincom-with-a-test-case"></a>MM0071：未知平台： *。 这通常表示 Xamarin 中的 bug;请使用测试用例在 https://bugzilla.xamarin.com 提交 bug 报告。

这通常表示 Xamarin 中的 bug;请使用测试用例在[https://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=Xamarin.Mac)提交 bug 报告。

<a name="MM0073" />

#### <a name="mm0073-xamarinmac--does-not-support-a-deployment-target-of--the-minimum-is--please-select-a-newer-deployment-target-in-your-projects-infoplist"></a>MM0073： Xamarin \* 不支持 \* 的部署目标（最小为 \*）。 请在项目的 info.plist 中选择一个较新的部署目标。

最小部署目标是在错误消息中指定的目标;请在项目的 info.plist 中选择一个较新的部署目标。

如果无法更新部署目标，请使用较旧版本的 Xamarin。

<a name="MM0074" />

#### <a name="mm0074-xamarinmac--does-not-support-a-deployment-target-of--the-maximum-is--please-select-an-older-deployment-target-in-your-projects-infoplist-or-upgrade-to-a-newer-version-of-xamarinmac"></a>MM0074： Xamarin \* 不支持 \* 的部署目标（\*最大值）。 请在项目的 info.plist 中选择一个较旧的部署目标，或升级到最新版本的 Xamarin。

Xamarin 不支持将最低部署目标设置为比此特定版本的 Xamarin 版本更高的版本。

请在项目的 info.plist 中选择较早的最低部署目标，或升级到最新版本的 Xamarin。

<a name="MM0079" />

#### <a name="mm0079-internal-error---no-executable-was-copied-into-the-app-bundle-please-contact-supportxamarincom"></a>MM0079：内部错误-未将可执行文件复制到应用程序包中。 请联系 "support@xamarin.com"

<a name="MM0080" />

#### <a name="mm0080-disabling-newrefcount---new-refcountfalse-is-deprecated"></a>MM0080：禁用 NewRefCount，--引用计数： false，已弃用。

<!-- 0088 used by mtouch -->
<!-- 0089 used by mtouch -->

<a name="MM0091" />

#### <a name="mm0091-this-version-of-xamarinmac-requires-the--sdk-shipped-with-xcode--either-upgrade-xcode-to-get-the-required-header-files-or-use-the-dynamic-registrar-or-set-the-managed-linker-behaviour-to-link-platform-or-link-framework-sdks-only-to-try-to-avoid-the-new-apis"></a>MM0091：此版本的 Xamarin 需要 \* SDK （随附 Xcode \*）。 升级 Xcode 以获取所需的标头文件，或使用动态注册器或将托管链接器行为设置为仅链接平台或链接框架 Sdk （尝试避免新的 Api）。

Xamarin 需要来自错误消息中指定的 SDK 版本的标头文件，以利用静态注册器生成应用程序。 修复此错误的建议方法是升级 Xcode 以获取所需的 SDK，这将包括所有必需的标头文件。 如果安装了多个版本的 Xcode，或想要在非默认位置使用 Xcode，请确保在 IDE 的首选项中设置正确的 Xcode 位置。

一个可能的替代解决方案是启用托管链接器。 这会删除未使用的 API，在大多数情况下，包括缺少标头文件的新 API （或不完整）。 但是，如果你的项目使用的 API 与 Xcode 提供的 SDK 版本不同，则此操作不起作用。

另一种可能的替代解决方案是改用动态注册器。 这会通过动态注册类型来施加启动开销，但会删除标头文件要求。 

最后 straw 的解决方案是使用较旧版本的 Xamarin，其中一种支持你的项目所需的 SDK。

<a name="MM0097" />

#### <a name="mm0097-machineconfig-file-0-can-not-be-found"></a>MM0097：找不到 machine.config 文件 "{0}"。

<a name="MM0098" />

#### <a name="mm0098-aot-compilation-is-only-available-on-unified"></a>MM0098： AOT 编译仅适用于统一

<a name="MM0099" />

#### <a name="mm0099-internal-error-0-please-file-a-bug-report-with-a-test-case-httpsbugzillaxamarincom"></a>MM0099： {0}内部错误。 请使用测试用例（ https://bugzilla.xamarin.com) ）来提交 bug 报告。

<a name="MM0114" />

#### <a name="mm0114-hybrid-aot-compilation-requires-all-assemblies-to-be-aot-compiled"></a>MM0114：混合 AOT 编译要求将所有程序集编译为 AOT。

<a name="MM0129" />

#### <a name="mm0129-debugging-symbol-file-for--does-not-match-the-assembly-and-is-ignored"></a>MM0129： "*" 的调试符号文件与程序集不匹配，将被忽略。

调试符号–无法加载指定程序集的 .pdb （仅适用于可移植 pdb）或 .mdb 文件。

这通常意味着程序集比符号更高或更早。 由于它们不匹配，因此将忽略符号。

此警告不会影响正在生成的应用程序，但您可能无法完全调试它（特别是指定程序集中的代码）。 另外，异常、堆栈跟踪和崩溃报告可能缺少某些信息。

请将此问题报告给程序集包的发布者（例如，NuGet 作者），以便在将来的版本中修复此问题。

<a name="MM0130" />

#### <a name="mm0130-no-root-assemblies-found-you-should-provide-at-least-one-root-assembly"></a>MM0130：找不到根程序集。 应提供至少一个根程序集。

运行 `--runregistrar`时，应至少提供一个根程序集。

<a name="MM0131" />

#### <a name="mm0131-product-assembly-0-not-found-in-assembly-list-1"></a>MM0131：在程序集列表中找不到产品程序集 "{0}"： "{1}"

运行 `--runregistrar`时，程序集列表应包括产品程序集 XamMac。

<a name="MM0132" />

#### <a name="mm0132-unknown-optimization--valid-values-are-"></a>MM0132：未知优化： \*。 有效值为： \*

无法识别指定的优化。

接受的格式为 `[+|-]optimization-name`，其中 `optimization-name` 是错误消息中列出的值之一。

有关每个优化的完整说明，请参阅[生成优化](https://developer.xamarin.com/guides/cross-platform/macios/build-optimizations)。

<a name="MM0133" />

#### <a name="mm0133-found-more-than-1-assembly-matching-0-choosing-first-1"></a>MM0133：找到了多个与 "{0}" 匹配的程序集： "{1}"

<a name="MM0134" />

#### <a name="mm0134-32-bit-applications-should-be-migrated-to-64-bit"></a>MM0134：32位应用程序应迁移到64位。

Apple 宣布，它将不允许 macOS 应用商店提交32位应用（从年1月 1 2018 日开始）。 

另外，32位应用程序将不会在不受影响的 macOS 后的版本上运行。 

有关更多详细信息： https://developer.apple.com/news/?id=06282017a

请考虑将应用程序和任何依赖项更新为64位。

<a name="MM0135" />

#### <a name="mm0135-did-not-link-system-framework-0-referenced-by-assembly-1-because-it-was-introduced-in-2-3-and-were-using-the-2-4-sdk"></a>MM0135：未链接系统框架 "{0}" （由程序集 "{1}" 引用），因为它是在 {2} {3}中引入的，并且我们使用的是 {2} {4} SDK。

若要生成应用程序，Xamarin 必须与系统库链接，其中一些系统库依赖于错误消息中指定的 SDK 版本。 由于使用的是较旧版本的 SDK，对这些 Api 的调用可能会在运行时失败。

修复此错误的建议方法是升级 Xcode 以获取所需的 SDK。 如果安装了多个版本的 Xcode，或想要在非默认位置使用 Xcode，请确保在 IDE 的首选项中设置正确的 Xcode 位置。

此外，还可以让托管[链接器](https://docs.microsoft.com/xamarin/mac/deploy-test/linker)删除未使用的 api，包括（在大多数情况下）需要指定库的新 api。 但是，如果你的项目需要在比你的 Xcode 提供的 SDK 更高的 SDK 中引入的 Api，这将不起作用。

作为最后 straw 的解决方案，请使用较旧版本的 Xamarin，该版本不要求在生成过程中存在这些新的 Sdk。

## <a name="mm1xxx-file-copy--symlinks-project-related"></a>MM1xxx：文件复制/符号链接（项目相关）

<a name="MM1034" />

#### <a name="mm1034-could-not-create-symlink-file---target-error-number"></a>MM1034：无法创建符号 "{file}"-> "{target}"：错误 {number}

### <a name="mm14xx-product-assemblies"></a>MM14xx：产品程序集

<a name="MM1401" />

#### <a name="mm1401-the-required-0-assembly-is-missing-from-the-references"></a>MM1401：引用中缺少必需的 "{0}" 程序集

<a name="MM1402" />

#### <a name="mm1402-the-assembly-0-is-not-compatible-with-this-tool"></a>MM1402：程序集 "{0}" 与此工具不兼容

<a name="MM1403" />

#### <a name="mm1403-0-1-could-not-be-found-target-framework-0-is-unusable-to-package-the-application"></a>MM1403：找不到 {0} "{1}"。 目标框架 "{0}" 无法打包应用程序。

<a name="MM1404" />

#### <a name="mm1404-target-framework-0-is-invalid"></a>MM1404：目标框架 "{0}" 无效。

<a name="MM1405" />

#### <a name="mm1405-usefullxammacframework-must-always-target-framework-net-45-not-0-which-is-invalid"></a>MM1405： useFullXamMacFramework 必须始终面向 framework .NET 4.5，而不能是无效的 "{0}"

<a name="MM1406" />

#### <a name="mm1406-target-framework-0-is-invalid-when-targetting-xamarinmac-45-net-framwork"></a>MM1406：针对 Xamarin 4.5 .NET framwork 时，目标框架 "{0}" 无效。

<a name="MM1407" />

#### <a name="mm1407-mismatch-between-xamarinmac-reference-0-and-target-framework-selected-1"></a>MM1407： Xamarin. Mac 引用 "{0}" 和目标框架之间所选的 "{1}" 不匹配。

### <a name="mm15xx-assembly-gathering-not-requiring-linker-errors"></a>MM15xx：程序集收集（不需要链接器）错误

<a name="MM1501" />

#### <a name="mm1501-can-not-resolve-reference-0"></a>MM1501：无法解析引用： {0}

### <a name="machocs"></a>MachO.cs

<a name="MM1600" />

#### <a name="mm1600-not-a-mach-o-dynamic-library-unknown-header-0x0-1"></a>MM1600：不是 Mach-o-O 动态库（未知标头 "0x{0}"）： {1}。

<a name="MM1601" />

#### <a name="mm1601-not-a-static-library-unknown-header-0-1"></a>MM1601：不是静态库（未知标头 "{0}"）： {1}。

<a name="MM1602" />

#### <a name="mm1602-not-a-mach-o-dynamic-library-unknown-header-0x0-1"></a>MM1602：不是 Mach-o-O 动态库（未知标头 "0x{0}"）： {1}。

<a name="MM1603" />

#### <a name="mm1603-unknown-format-for-fat-entry-at-position-0-in-1"></a>MM1603： {1}中位置 {0} 的 fat 条目的格式未知。

<a name="MM1604" />

#### <a name="mm1604-file-of-type-0-is-not-a-macho-file-1"></a>MM1604：类型 {0} 的文件不是 MachO 文件（{1}）。

## <a name="mm2xxx-linker"></a>MM2xxx：链接器

### <a name="mm20xx-linker-general-errors"></a>MM20xx：链接器（常规）错误

<a name="MM2001" />

#### <a name="mm2001-could-not-link-assemblies"></a>MM2001：无法链接程序集

<a name="MM2002" />

#### <a name="mm2002-can-not-resolve-reference-0"></a>MM2002：无法解析引用： {0}

<a name="MM2003" />

#### <a name="mm2003-option-0-will-be-ignored-since-linking-is-disabled"></a>MM2003：将忽略选项 "{0}"，因为链接已禁用

<a name="MM2004" />

#### <a name="mm2004-extra-linker-definitions-file-0-could-not-be-located"></a>MM2004：找不到额外的链接器定义文件 "{0}"。

<a name="MM2005" />

#### <a name="mm2005-definitions-from-0-could-not-be-parsed"></a>MM2005：无法分析 "{0}" 中的定义。

<a name="MM2006" />

#### <a name="mm2006-native-library-0-was-referenced-but-could-not-be-found"></a>MM2006：引用了本机库 "{0}"，但找不到它。

<a name="MM2007" />

#### <a name="mm2007-xamarinmac-unified-api-against-a-full-net-profile-does-not-support-linking-pass-the--nolink-flag"></a>MM2007：针对完整 .NET 配置文件的 Xamarin Unified API 不支持链接。 传递-nolink 标志。

<a name="MM2009" />

#### <a name="mm2009-referenced-by-01------this-message-is-related-to-mm2006-"></a>MM2009：由 {0}引用。{1} @ no__t_2_\* 此消息与 MM2006 \*相关\*

<a name="MM2010" />

#### <a name="mm2010-unknown-httpmessagehandler-0-valid-values-are-httpclienthandler-default-cfnetworkhandler-or-nsurlsessionhandler"></a>MM2010：未知的 HttpMessageHandler `{0}`。 有效值为 HttpClientHandler （默认值）、CFNetworkHandler 或 NSUrlSessionHandler

<a name="MM2011" />

#### <a name="mm2011-unknown-tlsprovider-0--valid-values-are-default-or-appletls"></a>MM2011： TLSProvider '{0}未知。  有效值为 default 或 appletls

<a name="MM2012" />

#### <a name="mm2012-only-first-0-of-1-referenced-by-warnings-shown--this-message-related-to-2009-"></a>MM2012：仅显示 {1} "引用者" {0} 的第一个。 \*\* 与 2009 \*相关的消息\*

<a name="MM2013" />

#### <a name="mm2013-failed-to-resolve-the-reference-to-0-referenced-in-1-the-app-will-not-include-the-referenced-assembly-and-may-fail-at-runtime"></a>MM2013：无法解析对 "{1}" 中引用的 "{0}" 的引用。 应用将不包括引用的程序集，并且可能会在运行时失败。

<a name="MM2014" />

#### <a name="mm2014-xamarinmac-extensions-do-not-support-linking-request-for-linking-will-be-ignored--this-message-is-obsolete-in-xm-36-"></a>MM2014： Xamarin Extension 不支持链接。 将忽略链接请求。 \*\* 此消息在 XM 3.6 + \*中已过时\*

<!-- 2015 used by mtouch -->

<a name="MM2016" />

#### <a name="mm2016-invalid-tlsprovider-0-option-the-only-valid-value-1-will-be-used"></a>MM2016：无效的 TlsProvider `{0}` 选项。 将使用唯一的有效值 `{1}`。

<a name="MM2017" />

#### <a name="mm2017-could-not-process-xml-description-0"></a>MM2017：无法处理 XML 说明： {0}

<a name="MM202x" />

#### <a name="mm202x-binding-optimizer-failed-processing-"></a>MM202x：绑定优化器未能处理 `...`。

<a name="MM2100" />

#### <a name="mm2100-xamarinmac-classic-api-does-not-support-platform-linking"></a>MM2100： Xamarin Classic API 不支持平台链接。

<a name="MM2103" />

#### <a name="mm2103-error-processing-assembly--"></a>MM2103：处理程序集 "\*" 时出错： *

处理程序集时出现意外错误。

错误消息中命名了导致此问题的程序集。 若要解决此问题，需要在[bug 报告](https://bugzilla.xamarin.com)中提供程序集，并在启用详细级别的情况下提供完整的生成日志（即，`-v -v -v -v` 在**附加的 mtouch 参数**中）。

<a name="MM2104" />

#### <a name="mm2104-unable-to-link-assembly-0-as-it-is-mixed-mode"></a>MM2104：无法将程序集 "{0}" 链接为混合模式。

链接器无法处理混合模式程序集。

有关混合模式程序集的详细信息，请参阅 https://docs.microsoft.com/cpp/dotnet/mixed-native-and-managed-assemblies 。

<a name="MM2106" />

#### <a name="mm2106-could-not-optimize-the-call-to-blockliteralsetupblockunsafe-in--at-offset--because-"></a>MM2106：由于 \*，无法在偏移 \* 的 \* 中优化对 SetupBlock [Unsafe] 的调用。

链接器在无法优化对 `BlockLiteral.SetupBlock` 或 `Block.SetupBlockUnsafe`的调用时，将报告此警告。

该消息将指向调用 `BlockLiteral.SetupBlock[Unsafe]`的方法，并且它还可能会向线索显示为何无法优化调用。

请与完整的生成日志一起记录问题，以便我们能够调查出现的[问题](https://github.com/xamarin/xamarin-macios/issues/new)，并可能在将来启用更多方案。

<a name="MM2107" />

#### <a name="mm2107-its-not-safe-to-remove-the-dynamic-registrar-because-reasons"></a>MM2107：删除动态注册器是不安全的，因为 {原因}

当开发人员请求删除动态注册器（通过将 `--optimize:remove-dynamic-registrar` 传递到 mmp）时，链接器会报告此警告，但链接器会确定这是不安全的。

若要删除此警告，请删除 mmp 的优化参数，或传递 `--nowarn:2107` 将其忽略。

默认情况下，只要有可能和安全，此选项就会自动启用。

<a name="MM2108" />

#### <a name="mm2108-0-was-stripped-of-architectures-except-1-to-comply-with-app-store-restrictions-this-could-break-exisiting-codesigning-signatures-consider-stripping-the-library-with-lipo-or-disabling-with---optimize-trim-architectures"></a>MM2108： "{0}" 已去除体系结构，但 "{1}" 除外，不符合应用商店限制。 这可能会破坏现有的代码签名签名。 考虑通过 lipo 或通过--optimize =-trim-

应用商店现在会拒绝包含包含32位变体的库和框架的应用程序。 将库复制到最终应用程序捆绑包时，会去除未使用的体系结构。

这种情况通常是安全的，并会降低应用程序捆绑包的大小。 但是，任何已签名代码的绑定框架都将使其签名失效（如果应用程序已签名，则会在以后重新签名）。

请考虑使用 `lipo` 从源库中永久删除不需要的体系结构。 如果未将应用程序发布到 App Store，则可以通过将 `--optimize=-trim-architectures` 作为附加的 MMP 参数传递来禁用此删除操作。

<a name="MM2109"/>

#### <a name="mm2109-xamarinmac-classic-api-does-not-support-platform-linking"></a>MM2109： Xamarin Classic API 不支持平台链接。

## <a name="mm3xxx-aot"></a>MM3xxx： AOT

### <a name="mm30xx-aot-general-errors"></a>MM30xx： AOT （常规）错误

<a name="MM3001" />

#### <a name="mm3001-could-not-aot-the-assembly-0"></a>MM3001：无法对程序集 "{0}" 进行 AOT 操作

<!-- 3002 used by mtouch -->
<!-- 3003 used by mtouch -->
<!-- 3004 used by mtouch -->
<!-- 3005 used by mtouch -->
<!-- 3006 used by mtouch -->
<!-- 3007 used by mtouch -->
<!-- 3008 used by mtouch -->

<a name="MM3009" />

#### <a name="mm3009-aot-of-0-was-requested-but-was-not-found"></a>MM3009：请求了 "{0}" 的 AOT，但找不到它

<a name="MM3010" />

#### <a name="mm3010-exclusion-of-aot-of-0-was-requested-but-was-not-found"></a>MM3010：请求了 "{0}" 的 AOT 排除项，但找不到该排除项

## <a name="mm4xxx-code-generation"></a>MM4xxx：代码生成

### <a name="mm40xx-driverm"></a>MM40xx： driver

<a name="MM4001" />

#### <a name="mm4001-the-main-template-could-not-be-expanded-to-0"></a>MM4001：无法将主模板展开为 `{0}`。

### <a name="mm41xx-registrar"></a>MM41xx：注册机构

<a name="MM4134" />

#### <a name="mm4134-your-application-is-using-the-0-framework-which-isnt-included-in-the-macos-sdk-youre-using-to-build-your-app-this-framework-was-introduced-in-osx-2-while-youre-building-with-the-macos-1-sdk-this-configuration-is-not-supported-with-the-static-registrar-pass---registrardynamic-as-an-additional-mmp-argument-in-your-projects-mac-build-option-to-select-alternatively-select-a-newer-sdk-in-your-apps-mac-build-options"></a>MM4134：你的应用程序使用的是 "{0}" 框架，它不包含在用于构建应用的 MacOS SDK 中（此框架是在 OSX {2}中引入的，而你是使用 MacOS {1} SDK 生成的。）静态注册器（传递--注册程序： dynamic 作为要选择的项目 Mac 生成选项中的额外 mmp 参数）不支持此配置。 或者，在应用的 Mac 生成选项中选择一个较新的 SDK。

<a name="MM4173" />

#### <a name="mm4173-the-registrar-cant-compute-the-block-signature-for-the-delegate-of-type-delegate-type-in-the-method-method-because-"></a>MM4173：注册器无法在方法 {method} 中计算类型 {委托类型} 的委托的块签名，因为 *。

这是一个警告，指示注册器无法将指定方法的块签名注入生成的注册器代码，因为注册器无法计算它。

这意味着必须在运行时计算块签名，这会稍微慢一些。

目前有两个可能的原因导致此警告：

1. 托管委托的类型为 `System.Delegate` 或 `System.MulticastDelegate`。 这些类型不代表特定的签名，这意味着注册机构不能计算相应的本机签名。 在这种情况下，修补程序是为块使用特定委托类型（或者，可以通过将 `--nowarn:4173` 添加为项目的 Mac 生成选项中的附加 mmp 参数来忽略该警告。
2. 注册机构找不到该委托的 `Invoke` 方法。 这不应发生，因此，请在测试项目中指出问题，以便我们可以解决此[问题](https://github.com/xamarin/xamarin-macios/issues/new)。

<a name="MT4174" />

#### <a name="mt4174-unable-to-locate-the-block-to-delegate-conversion-method-for-the-method-methods-parameter-parameter"></a>MT4174：找不到要为方法 {method} 的参数 # {parameter} 委托转换方法的块。

这是一条警告，指示静态注册器找不到用于为目标-C 块创建委托的方法。 在运行时将尝试查找方法，但它可能也会失败（具有 MT8009 的异常）。

此警告的一个可能原因是为使用块的 API 手动编写绑定。 建议使用绑定项目来绑定目标 C 代码（特别是在它涉及到块时），因为手动执行此操作非常复杂。

如果不是这种情况，请在测试用例中[发布问题](https://github.com/xamarin/xamarin-macios/issues/new)。

## <a name="mm5xxx-gcc-and-toolchain"></a>MM5xxx： GCC 和工具链

### <a name="mm51xx-compilation"></a>MM51xx：编译

<a name="MM5101" />

#### <a name="mm5101-missing-0-compiler-please-install-xcode-command-line-tools-component"></a>MM5101：缺少 "{0}" 编译器。 请安装 Xcode "命令行工具" 组件。

<!-- 5102 used by mtouch -->

<a name="MM5103" />

#### <a name="mm5103-failed-to-compile-error-code---0-please-file-a-bug-report-at-httpbugzillaxamarincom"></a>MM5103：无法编译。 错误代码-{0}。 请在 http://bugzilla.xamarin.com 提交 bug 报告

<!-- 5104 used by mtouch -->

### <a name="mm52xx-linking"></a>MM52xx：链接

<a name="MM5202" />

#### <a name="mm5202-monoframework-mdk-is-missing-please-install-the-mdk-for-your-monoframework-version-from-httpmono-projectcomdownloads"></a>MM5202：缺少 MDK。 请从 http://mono-project.com/Downloads 安装 Mono framework 版本的 MDK

<a name="MM5203" />

#### <a name="mm5203-cant-find-libxammaca-likely-because-of-a-corrupted-xamarinmac-installation-please-reinstall-xamarinmac"></a>MM5203：找不到 libxammac，原因可能是安装了已损坏的 Xamarin。 请重新安装 Xamarin。

<a name="MM5204" />

#### <a name="mm5204-invalid-architecture-x86_64-is-only-supported-on-non-classic-profiles"></a>MM5204：结构无效。 x86_64 仅在非经典配置文件上受支持。

<a name="MM5205" />

#### <a name="mm5205-invalid-architecture-0-valid-architectures-are-i386-and-x86_64-when---profilemobile"></a>MM5205：体系结构 "{0}" 无效。 有效的体系结构为 i386 和 x86_64 （when--profile = mobile）。

<a name="MM5218" />

#### <a name="mm5218-cant-ignore-the-dynamic-symbol-symbol---ignore-dynamic-symbolsymbol-because-it-was-not-detected-as-a-dynamic-symbol"></a>MM5218：无法忽略动态符号 {symbol} （--ignore-symbol = {symbol}），因为它不是作为动态符号检测到的。

请参阅[等效的 mtouch 警告](~/ios/troubleshooting/mtouch-errors.md#MT5218)。

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

### <a name="mm53xx-other-tools"></a>MM53xx：其他工具

<a name="MM5301" />

#### <a name="mm5301-pkg-config-could-not-be-found-please-install-the-monoframework-from-httpmono-projectcomdownloads"></a>MM5301：找不到 .pkg。 请从 http://mono-project.com/Downloads 安装 Mono framework

<!-- 5302 used by mtouch -->
<!-- 5303 used by mtouch -->
<!-- 5304 used by mtouch -->

<a name="MM5305" />

#### <a name="mm5305-missing-otool-tool-please-install-xcode-command-line-tools-component"></a>MM5305：缺少 "otool" 工具。 请安装 Xcode "命令行工具" 组件

<a name="MM5306" />

#### <a name="mm5306-missing-dependencies-please-install-xcode-command-line-tools-component"></a>MM5306：缺少依赖项。 请安装 Xcode "命令行工具" 组件

<a name="MM5308" />

#### <a name="mm5308-xcode-license-agreement-may-not-have-been-accepted--please-launch-xcode"></a>MM5308：可能尚未接受 Xcode 许可协议。  请启动 Xcode。

<a name="MM5309" />

#### <a name="mm5309-native-linking-failed-with-error-code-1-check-build-log-for-details"></a>MM5309：本机链接失败，错误代码为1。 有关详细信息，请查看生成日志。

<a name="MM5310" />

#### <a name="mm5310-install_name_tool-failed-with-an-error-code-0-check-build-log-for-details"></a>MM5310： install_name_tool 失败，错误代码为 "{0}"。 有关详细信息，请查看生成日志。

<a name="MM5311" />

#### <a name="mm5311-lipo-failed-with-an-error-code-0-check-build-log-for-details"></a>MM5311： lipo 失败，错误代码为 "{0}"。 有关详细信息，请查看生成日志。

<!-- MM6xxx: mmp internal tools -->
<!-- MM7xxx: reserved -->

## <a name="mm8xxx-runtime"></a>MM8xxx：运行时

### <a name="mm800x-misc"></a>MM800x：杂项

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

#### <a name="mm8017-the-boehm-garbage-collector-is-not-supported-please-use-sgen-instead"></a>MM8017：不支持 Boehm 垃圾回收器。 请改用 SGen。

<a name="MM8025" />

#### <a name="mm8025-failed-to-compute-the-token-reference-for-the-type-typeassemblyqualifiedname-because-reasons"></a>MM8025：无法计算类型 "{type" 的令牌引用。AssemblyQualifiedName} "，因为 {原因}

这表明 Xamarin 中的 bug。 请在[https://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=Xamarin.Mac)提交 bug。

一种可能的解决方法是禁用 `register-protocols` 优化，方法是将 `--optimize:-register-protocols` 作为附加的 mmp 参数传递到项目的 Mac 生成选项中。

<a name="MM8026" />

#### <a name="mm8026--is-not-supported-when-the-dynamic-registrar-has-been-linked-away"></a>当动态注册器已链接在一起时，不支持 MM8026： *。

这通常表示 Xamarin 中的 bug，因为如果需要，不应将动态注册器链接在一起。 请在[https://bugzilla.xamarin.com](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS)提交 bug。

可以通过将 `--optimize=-remove-dynamic-registrar` 添加到项目的 Mac 生成选项中的其他 mmp 参数，强制链接器保留动态注册器。
