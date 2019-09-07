---
title: Xamarin iOS 常见问题
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 65E04188-185D-493D-BA3C-A89711CB6CAF
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/21/2017
ms.openlocfilehash: a8549e03c96a5e21f7a235064ebd72fc671cf2b8
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769284"
---
# <a name="ios-frequently-asked-questions"></a>iOS 常见问题

## <a name="general-questions"></a>一般问题

### <a name="can-i-use-a-mac-vm-with-xamarinmac-vmmd"></a>[是否可以通过 Xamarin 使用 Mac VM？](mac-vm.md)
是，但仅在 Mac 硬件上。

### <a name="how-can-i-downgrade-xcodedowngrade-xcodemd"></a>[如何降级 Xcode？](downgrade-xcode.md)
本指南提供了访问以前版本的 Xcode 和最新版本的链接。

### <a name="where-can-i-set-my-ios-sdk-locationsios-sdkmd"></a>[可以在哪里设置 iOS SDK 位置？](ios-sdk.md)
对于大多数用户，它们会自动设置为正确的位置。 本指南列出了默认 SDK 位置以及如何在需要时进行更改。

### <a name="how-can-i-reenable-developer-options-after-updating-iosupdate-developer-optionsmd"></a>[如何在更新 iOS 后重新启用开发人员选项？](update-developer-options.md)
IOS bug 可能会导致开发人员选项在更新 iOS 版本后消失，但在切换到 iOS 8.x 时已观察到。 本指南介绍如何重新启用选项。

### <a name="user-location-not-working-in-ios-8ios8-user-locationmd"></a>[用户位置在 iOS 8 中不起作用](ios8-user-location.md)
本指南将说明如何编辑 info.plist 以在 iOS 8 中启用用户位置。

### <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logssymbolicate-ios-crashmd"></a>[在哪里可以找到符号化 iOS 崩溃日志的 .dSYM 文件？](symbolicate-ios-crash.md)
本指南介绍 symbolicating iOS 崩溃日志以帮助诊断故障的基本步骤。 它还链接到其他资源，以获得更高级的带符号化技术，& 有关解释 iOS 崩溃日志的信息。

### <a name="how-do-i-set-mono-runtime-environment-variables-for-ios-projects-in-xamarin-studioxs-mono-runtimemd"></a>[如何在 Xamarin Studio 中为 iOS 项目设置 Mono 运行时环境变量？](xs-mono-runtime.md)
如果需要为 Mono 设置任何运行时环境变量，则可以在 "项目选项" 中设置 **> 运行 > 常规**"页。

## <a name="publishing-questions"></a>发布问题

### <a name="error-when-submitting-to-app-store-invalid-bundle---options-not-allowed-to-be-embedded-in-bitcode-are-detected-in-the-submissioninvalid-bundle-bitcodemd"></a>[提交到 App Store 时出错："无效的捆绑包-在提交中检测不到 bitcode 中的选项"](invalid-bundle-bitcode.md)

提交_需要_bitcode 的应用程序（如 WatchOS 和 tvOS 应用程序）必须在 Xcode 9 中完成。

### <a name="can-i-change-the-output-path-of-the-ipa-fileipa-output-pathmd"></a>[能否更改 IPA 文件的输出路径？](ipa-output-path.md)
在 Xamarin 循环7中，你可以使用自定义的 MSBuild 目标来实现此目的。

### <a name="how-can-i-copy-ipa-output-files-to-the-tfs-drop-folderipa-tfsmd"></a>[如何将 IPA 输出文件复制到 TFS 放置文件夹？](ipa-tfs.md)
是的，本指南介绍了如何操作。

### <a name="can-i-add-files-to-or-remove-files-from-an-ipa-file-after-building-it-in-visual-studiomodify-ipamd"></a>[在 Visual Studio 中生成文件后，是否可以在 IPA 文件中添加或删除文件？](modify-ipa.md)
是的，但这种情况通常会要求你在进行更改后对`.app`包进行重新签名。 请注意，在`.ipa`正常使用时不需要修改文件。 本文仅用于提供信息。

### <a name="is-it-possible-to-create-a-xcarchive-archive-from-visual-studiocreate-xcarchivemd"></a>[是否可以从 Visual Studio 创建 .xcarchive 存档？](create-xcarchive.md)
`.xcarchive`自 Xamarin 4 开始，现在可以通过`ArchiveOnBuild`将属性设置为`true`，从 Windows 创建。

### <a name="why-does-my-app-submission-fail-with-disallowed-paths--itunesmetadataplist--found-at--itunesmetadata-disallowed-pathsmd"></a>[为何应用提交失败，且随附：“不允许的路径 ( "iTunesMetadata.plist" ) 位于...”？](itunesmetadata-disallowed-paths.md)
此错误是 Apple 的应用商店验证过程中发生更改的结果。 此特定错误与您已安装的 Xamarin 的特定_版本无关，_ 因此降级_不_会有帮助。 本指南链接到有关如何解决此问题的详细信息。

## <a name="diagnosing-specific-error-messages"></a>诊断特定错误消息

### <a name="ios-designer-error-with-registerserviceporterror-registerserviceportmd"></a>[含有 RegisterServicePort 的 iOS 设计器错误](error-registerserviceport.md)
错误消息`RegisterServicePort`和类似错误消息通常是计算机上的间谍软件/恶意软件的问题。 本指南详细介绍了有关删除间谍软件/恶意软件的诊断信息和信息。

### <a name="why-does-my-ios-build-fail-with-no-valid-iphone-code-signing-keys-found-in-keychainno-codesigning-keysmd"></a>[为什么我的 iOS 生成会失败，并显示“在 keychain 中未找到有效的 iPhone 代码签名密钥”？](no-codesigning-keys.md)
当有问题的项目正在查找有效的代码签名凭据但找不到它们时，会出现此错误消息。 在物理 iOS 设备上进行测试和部署需要进行代码签名;以及即席 & 应用商店版本。

### <a name="why-does-my-ios-9-app-fail-with-systemexception-failed-to-marshal-the-objective-c-objectexception-marshal-obj-cmd"></a>[为何 iOS 9 应用失败，且随附：“System.Exception:无法封送 Objective-C 对象”？](exception-marshal-obj-c.md)
IOS 9 中的 API 更改要求在调用非托管代码时使用回调构造函数，因为基础 API 现在需要用到它。

### <a name="runtime-error-the-assembly-mscorlibdll-was-not-found-or-could-not-be-loadederror-mscorlib-not-foundmd"></a>[运行时错误：找不到或无法加载程序集 mscorlib.dll](error-mscorlib-not-found.md)
如果在创建 IPA 的 `.monotouch-32`过程中`.monotouch-64`缺少`.xcarchive`隐藏的和文件夹，则会出现此问题，并触发运行时错误。

### <a name="compile-error-can-not-encode-offset-x-in-resulting-scattered-relocationerror-encode-offset-scattered-relocationmd"></a>[编译错误：无法在生成的分散重定位中编码偏移 X](error-encode-offset-scattered-relocation.md)
当最终二进制文件对本机工具链过大时，为32位体系结构（如 ARMv7）生成时，将出现此问题。

## <a name="deprecated"></a>已弃用

> [!IMPORTANT]
> 下面的文章适用于最新版本的 Xamarin 中已解决的问题。 但是，如果该软件的最新版本发生问题，请使用完整的版本信息和完整的生成日志输出来记录[新的 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) 。

### <a name="ipa-file-is-0-bytesipa-zero-bytesmd"></a>[IPA 文件为 0 字节](ipa-zero-bytes.md)
以前版本的 Xamarin 中存在一些已知问题，这些问题可能会导致 Windows 上的 IPA 文件为0字节。

### <a name="ibtool-error-the-operation-couldnt-be-completederror-ibtoolmd"></a>[IBTool 错误：无法完成该操作。](error-ibtool.md)
Apple[修复](https://developer.apple.com/library/ios/releasenotes/DeveloperTools/RN-Xcode/Chapters/xc6_release_notes.html)了`ibtool` Xcode 6.1.1 中的此 bug，因此升级到 Xcode 6.1.1 或更高版本是最简单的解决方法。

### <a name="error-mt1009-could-not-copy-the-assemblyerror-mt1009md"></a>[MT1009 错误：无法复制程序集](error-mt1009.md)
这会影响运行 Xamarin 7.2.6 的用户。 此问题的原因是，如果使用不同的用户帐户安装了 Xamarin iOS，则需要更高权限的文件权限，即开发人员的主帐户。

### <a name="systemexception-amdevicenotificationsubscribe-returned-exception-amddevicenotificationsubscribemd"></a>[System.Exception AMDeviceNotificationSubscribe 已返回...](exception-amddevicenotificationsubscribe.md)
首次启动 Visual Studio for Mac 或`mtbserver.log`文件时，此消息可能出现在错误对话框中。 请注意，这是一个不常见的问题。 如果 Visual Studio 在连接到 Mac 生成主机时遇到问题，则会有其他错误，更有可能出现在`mtbserver.log`文件中。

### <a name="mdocarchivetomsxdocconverterexe-not-found-rverbasecommandonrequestmdocarchivetomsxdocconverter-not-foundmd"></a>[MDocArchiveToMsxDocConverter.exe 未找到 rver.BaseCommand.OnRequest](mdocarchivetomsxdocconverter-not-found.md)
此错误可能会出现`Mac Server Log`在 Visual Studio 中。
