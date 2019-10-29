---
title: 为什么 Microsoft 不支持 Jenkins？
description: 本文档概括地介绍了 Xamarin 与 Jenkins CI 系统的交互。 它还讨论了在使用 Jenkins 时出现的几个常见问题。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9951F980-2C6C-47C0-8A35-A78F06C20BEB
author: davidortinau
ms.author: daortin
ms.date: 06/05/2018
ms.openlocfilehash: 7be60eaa1135284522ef1e6ce81c911a68ff9915
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73012218"
---
# <a name="why-isnt-jenkins-supported-by-microsoft"></a>为什么 Microsoft 不支持 Jenkins？

## <a name="jenkins-support-explanation"></a>Jenkins 支持说明

Jenkins 是开源 CI 套件;由于此 Jenkins*本身*直接导致的许多问题都需要在您收到代码的位置被归档为问题;例如，[主 Jenkins](https://github.com/jenkinsci/jenkins)存储库或[Jenkins](https://github.com/stisti/jenkins-app)的存储库。

这种情况的例外是，对于可与 Xamarin 的工具中的特定 bug 隔离的问题，如果你怀疑这是你可以查看支持选项的情况，则可能会再次检查你的[支持选项](~/cross-platform/troubleshooting/support-options.md)，这可能是 Xamarin 支持团队无法*直接*帮助的内容。

## <a name="setup-jenkins-with-xamarin"></a>设置 Jenkins 和 Xamarin

尽管如上所述，我们的团队不会直接支持 Jenkins 问题，将[Jenkins 与 xamarin](~/tools/ci/jenkins-walkthrough.md)指南结合使用，可以设置与 xamarin 集成的 Jenkins CI 服务器。 

## <a name="fixes-for-common-issues"></a>常见问题的修复

### <a name="jenkins-is-unable-to-find-the-android-sdk"></a>Jenkins 找不到 Android SDK

此问题的错误消息如下所示：

> 错误 XA5205：找不到 Android SDK 目录。 请通过/p： AndroidSdkDirectory 设置

设置 SDK 位置的选项可能会因所使用的具体 Jenkins Android 插件而异;若要查看如何进行设置，请参阅插件指南。 例如，[Android Emulator 插件](https://wiki.jenkins-ci.org/display/JENKINS/Android+Emulator+Plugin#AndroidEmulatorPlugin-Systemconfiguration)会自动查找 SDK，但如果找不到，则为;还可以通过该插件的 Jenkins 系统配置页设置该位置。 

## <a name="deprecated-errors"></a>弃用的错误

> [!IMPORTANT]
> 此问题已在最新版本的 Xamarin 中解决。 但是，如果该软件的最新版本发生问题，请使用完整的版本信息和完整的生成日志输出来记录[新的 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) 。

### <a name="jenkins-reports-an-invalid-xamarin-license"></a>Jenkins 报告了无效的 Xamarin 许可证
此问题的错误消息通常类似于

> XA9008 错误：从命令行生成需要业务许可证

或

> 错误： Xamarin 的 Starter 版本不支持在 Xamarin Studio 之外生成 

此方案最常见的原因是，使用 Jenkins 的方法是使用未与 Xamarin 许可证关联的用户帐户登录。 解决此情况的最简单方法是直接通过用户帐户将 Jenkins 安装为应用。 此过程以及一些其他注意事项如下所述： [https://forums.xamarin.com/discussion/comment/99397/#Comment_99397](https://forums.xamarin.com/discussion/comment/99397/#Comment_99397)
