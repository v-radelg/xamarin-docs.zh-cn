---
title: 为什么不是由 Microsoft 支持 Jenkins？
description: 在高级别，本文档介绍与 Jenkins CI 系统的 Xamarin 的交互。 它还讨论了使用 Jenkins 时提出的一些常见问题。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9951F980-2C6C-47C0-8A35-A78F06C20BEB
author: asb3993
ms.author: amburns
ms.date: 06/05/2018
ms.openlocfilehash: c2e409b796d5ef2525079e02aafdd0c6e8db5d81
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61158931"
---
# <a name="why-isnt-jenkins-supported-by-microsoft"></a>为什么不是由 Microsoft 支持 Jenkins？

## <a name="jenkins-support-explanation"></a>Jenkins 支持说明

Jenkins 是一组开放源代码 CI;由于这么多问题直接引发由 Jenkins*本身*需要被归为问题对你的代码; 例如[主 Jenkins 存储库](https://github.com/jenkinsci/jenkins)，或为存储库[Jenkins.app](https://github.com/stisti/jenkins-app)。

此规则的例外是可以隔离到 Xamarin 的工具; 中的特定错误的问题如果怀疑存在这种情况可以检查您[支持选项](~/cross-platform/troubleshooting/support-options.md)中使用，但同样，此问题可能是一些外部 Xamarin 支持团队可以*直接*帮助。

## <a name="setup-jenkins-with-xamarin"></a>使用 Xamarin 安装程序 Jenkins

如上文所述直接由我们的团队; 不支持 Jenkins 问题时[使用 Xamarin 使用 Jenkins](~/tools/ci/jenkins-walkthrough.md)指南可用于设置与 Xamarin 集成 Jenkins CI 服务器。 

## <a name="fixes-for-common-issues"></a>常见的问题的修补程序

### <a name="jenkins-is-unable-to-find-the-android-sdk"></a>Jenkins 是找不到 Android SDK

此问题的错误消息是类似如下：

> 错误 XA5205:找不到 Android SDK 目录。 Please set via /p:AndroidSdkDirectory

设置 SDK 位置的选项可能会有所不同，具体取决于您使用; 的确切 Jenkins Android 插件查找有关如何将此项设置的好时机是插件指南中。 例如，[Android 仿真程序插件](https://wiki.jenkins-ci.org/display/JENKINS/Android+Emulator+Plugin#AndroidEmulatorPlugin-Systemconfiguration)自动寻找的 SDK，但如果它找不到它; 也可以通过该插件的 Jenkins 系统配置页设置位置。 


## <a name="deprecated-errors"></a>不推荐使用的错误

> [!IMPORTANT]
> 最新版本的 Xamarin 中已解决此问题。 但是，如果最新版本的软件会出现此问题，请提出[新 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md)与完整的版本控制信息和完整生成日志输出。



### <a name="jenkins-reports-an-invalid-xamarin-license"></a>Jenkins 报告 Xamarin 许可证无效
此问题的错误消息通常是类似于

> XA9008 错误：从命令行生成需要业务许可证

或

> 错误：Starter Edition 的 Xamarin.iOS 不支持构建 Xamarin Studio 外部 

此方案中的最常见原因是通过使用不与 Xamarin 许可证关联的用户帐户登录使用 Jenkins。 此操作，请解决的最简单方法是作为直接通过用户帐户的应用安装 Jenkins。 此处介绍的过程和一些其他注意事项： [https://forums.xamarin.com/discussion/comment/99397/#Comment_99397](https://forums.xamarin.com/discussion/comment/99397/#Comment_99397)
