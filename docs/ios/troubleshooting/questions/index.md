---
title: Xamarin.iOS 方面的常见问题
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 65E04188-185D-493D-BA3C-A89711CB6CAF
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
---

# <a name="ios-frequently-asked-questions"></a>iOS Frequently Asked Questions

## <a name="general-questions"></a>一般问题

### <a name="can-i-use-a-mac-vm-with-xamarinmac-vmmd"></a>[是否可以通过 Xamarin 使用 Mac VM？](mac-vm.md)
是的但只能在 Mac 硬件上。

### <a name="how-can-i-downgrade-xcodedowngrade-xcodemd"></a>[如何降级 Xcode？](downgrade-xcode.md)
本指南提供的链接可访问以前版本的 Xcode 以及最新版本。

### <a name="where-can-i-set-my-ios-sdk-locationsios-sdkmd"></a>[可以在哪里设置 iOS SDK 位置？](ios-sdk.md)
对于大多数用户将其设置为正确的位置自动。 本指南列出了默认 SDK 位置以及如何根据需要进行更改。

### <a name="how-can-i-reenable-developer-options-after-updating-iosupdate-developer-optionsmd"></a>[如何重新开发人员选项启用更新 iOS 后？](update-developer-options.md)
IOS bug 可能会导致开发人员选项消失更新 iOS 版本后，会出现此问题时切换到 iOS 8.x。 本指南介绍选项可以重新启用。

### <a name="user-location-not-working-in-ios-8ios8-user-locationmd"></a>[用户位置在 iOS 8 中不起作用](ios8-user-location.md)
本指南介绍如何编辑 info.plist 在 iOS 8 中启用用户的位置。

### <a name="where-can-i-find-the-dsym-file-to-symbolicate-ios-crash-logssymbolicate-ios-crashmd"></a>[在哪里可以找到符号化 iOS 崩溃日志的 .dSYM 文件？](symbolicate-ios-crash.md)
本指南介绍 symbolicating iOS 崩溃日志帮助进行诊断冲突的基本步骤。 它还链接到更高级的符号化技术和解释 iOS 崩溃日志的信息的其他资源。


### <a name="how-do-i-set-mono-runtime-environment-variables-for-ios-projects-in-xamarin-studioxs-mono-runtimemd"></a>[如何在 Xamarin Studio 中为 iOS 项目设置 Mono 运行时环境变量？](xs-mono-runtime.md)
如果需要为 Mono 设置任何运行时环境变量，可以将它们设置**项目选项 > 运行 > 常规**页。

## <a name="publishing-questions"></a>发布问题

### <a name="error-when-submitting-to-app-store-invalid-bundle---options-not-allowed-to-be-embedded-in-bitcode-are-detected-in-the-submissioninvalid-bundle-bitcodemd"></a>[提交到 App Store 时的错误："捆绑无效-不允许在 bitcode 中嵌入的选项中检测到的提交"](invalid-bundle-bitcode.md)

提交应用程序的_需要_bitcode，例如 watchOS 和 tvOS 应用程序，必须通过 Xcode 9。

### <a name="can-i-change-the-output-path-of-the-ipa-fileipa-output-pathmd"></a>[可以更改 IPA 文件的输出路径？](ipa-output-path.md)
截至 Xamarin 周期 7，可以使用自定义的 MSBuild 目标来实现此目的。

### <a name="how-can-i-copy-ipa-output-files-to-the-tfs-drop-folderipa-tfsmd"></a>[如何将 IPA 输出文件复制到 TFS 放置文件夹？](ipa-tfs.md)
是的本指南介绍了如何。

### <a name="can-i-add-files-to-or-remove-files-from-an-ipa-file-after-building-it-in-visual-studiomodify-ipamd"></a>[可以将文件添加到或 IPA 文件在 Visual Studio 生成后删除文件？](modify-ipa.md)
是的但它通常将需要重新签名`.app`捆绑包之后进行更改。 请注意该修改`.ipa`文件不需要在正常使用。 本文是仅提供信息之目的。

### <a name="is-it-possible-to-create-a-xcarchive-archive-from-visual-studiocreate-xcarchivemd"></a>[是否可以从 Visual Studio 创建.xcarchive 存档？](create-xcarchive.md)
截至 Xamarin 4，则现在可以创建`.xcarchive`从 Windows 通过设置`ArchiveOnBuild`属性设置为`true`。

### <a name="why-does-my-app-submission-fail-with-disallowed-paths--itunesmetadataplist--found-at--itunesmetadata-disallowed-pathsmd"></a>[为何应用提交失败，且随附：“不允许的路径 ( "iTunesMetadata.plist" ) 位于...”？](itunesmetadata-disallowed-paths.md)
此错误是 Apple 的 App Store 验证过程中更改的结果。 此特定错误_不_相关的特定版本的已安装的 Xamarin，因此降级将_不_帮助。 此指南链接到有关如何解决此问题的详细信息。


## <a name="diagnosing-specific-error-messages"></a>诊断特定错误消息

### <a name="ios-designer-error-with-registerserviceporterror-registerserviceportmd"></a>[含有 RegisterServicePort 的 iOS 设计器错误](error-registerserviceport.md)
使用错误`RegisterServicePort`和类似如上面的错误消息通常是间谍软件/恶意软件的计算机上的问题。 本指南详述了确认的诊断和删除间谍软件/恶意软件的信息。

### <a name="why-does-my-ios-build-fail-with-no-valid-iphone-code-signing-keys-found-in-keychainno-codesigning-keysmd"></a>[为什么我的 iOS 生成会失败，并显示“在 keychain 中未找到有效的 iPhone 代码签名密钥”？](no-codesigning-keys.md)
此错误消息相关的项目正在寻找代码签名的有效凭据时，会发生，但找不到它们。 代码签名才可进行测试和部署在物理 iOS 设备;以及 Ad hoc 和应用存储的生成。

### <a name="why-does-my-ios-9-app-fail-with-systemexception-failed-to-marshal-the-objective-c-objectexception-marshal-obj-cmd"></a>[为何 iOS 9 应用失败，且随附：“System.Exception:无法封送 Objective-C 对象”？](exception-marshal-obj-c.md)
IOS 9 中的 API 更改需要作为基础 API 调用非托管的代码，需要它时，使用回调构造函数。

### <a name="runtime-error-the-assembly-mscorlibdll-was-not-found-or-could-not-be-loadederror-mscorlib-not-foundmd"></a>[运行时错误：找不到或无法加载程序集 mscorlib.dll](error-mscorlib-not-found.md)
出现此问题时*隐藏*`.monotouch-32`并`.monotouch-64`文件夹是缺少`.xcarchive`进行签名 / IPA 创建过程中，运行时错误。

## <a name="deprecated"></a>不推荐使用

> [!IMPORTANT]
> 下面的文章适用于 Xamarin 的最新版本中已解决的问题。 但是，如果最新版本的软件会出现此问题，请提出[新 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md)与完整的版本控制信息和完整生成日志输出。



### <a name="ipa-file-is-0-bytesipa-zero-bytesmd"></a>[IPA 文件为 0 字节](ipa-zero-bytes.md)
在以前版本的 Xamarin，可能会导致为 0 字节的 Windows 上的 IPA 文件没有一些已知的问题。

### <a name="ibtool-error-the-operation-couldnt-be-completederror-ibtoolmd"></a>[IBTool 错误：无法完成该操作。](error-ibtool.md)
Apple[修复](https://developer.apple.com/library/ios/releasenotes/DeveloperTools/RN-Xcode/Chapters/xc6_release_notes.html)这`ibtool`Xcode 6.1.1，因此升级到 Xcode 6.1.1 或更高版本中的 bug 是最简单的修复程序。

### <a name="error-mt1009-could-not-copy-the-assemblyerror-mt1009md"></a>[MT1009 错误：无法复制程序集](error-mt1009.md)
这会影响运行 Xamarin.iOS 7.2.6 的用户。 此问题的原因需要更高的权限时使用不同的用户帐户安装 Xamarin.iOS 的文件权限然后开发人员的主帐户。

### <a name="systemexception-amdevicenotificationsubscribe-returned-exception-amddevicenotificationsubscribemd"></a>[System.Exception AMDeviceNotificationSubscribe 已返回...](exception-amddevicenotificationsubscribe.md)
此消息可以出现在错误对话框，在首次启动 Visual Studio for Mac 中，或在`mtbserver.log`文件。 请注意，这是不常见的问题。 如果 Visual Studio 无法连接到 Mac 生成主机，还有更有可能出现在其他错误`mtbserver.log`文件。

### <a name="mdocarchivetomsxdocconverterexe-not-found-rverbasecommandonrequestmdocarchivetomsxdocconverter-not-foundmd"></a>[MDocArchiveToMsxDocConverter.exe 未找到 rver.BaseCommand.OnRequest](mdocarchivetomsxdocconverter-not-found.md)
此错误可能会出现在`Mac Server Log`Visual Studio 中。
