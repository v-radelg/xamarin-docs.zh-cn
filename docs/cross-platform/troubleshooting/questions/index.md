---
title: 常规方面的常见问题
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C7E6E54D-3957-407D-BB87-22B095148C6B
author: asb3993
ms.author: amburns
ms.date: 05/08/2018
ms.openlocfilehash: 06aa6569301d1bfdbf9f6fd1e7397a38a9beb6f6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61157188"
---
# <a name="general-frequently-asked-questions"></a>常规方面的常见问题

## <a name="portable-class-libraries"></a>可移植类库

### <a name="how-can-i-view-what-libraries-are-supported-in-a-pclpcl-support-librariesmd"></a>[如何查看 PCL 中支持的库？](pcl-support-libraries.md)
本指南列出了资源和用于确定是否在现有库支持的各种 PCL 目标平台，或可以转换为 PCL 配置文件的方法。

### <a name="pcl-reflection-apipcl-reflectionmd"></a>[PCL 反射 API](pcl-reflection.md)
Microsoft 开发了一个新的反射 API 可移植类库中使用。 如果你有想要移动到一个 PCL 一些现有反射代码，可能无法工作。

### <a name="pcl-case-study-how-can-i-resolve-problems-related-to-systemdiagnosticstracing-for-the-microsoft-tpl-dataflow-nuget-packagepcl-case-studymd"></a>[PCL 案例研究：如何解决 Microsoft TPL 数据流 NuGet 包的 system.diagnostics.tracing 相关的问题？](pcl-case-study.md)
Xamarin.iOS 和 Xamarin.Android 不会实现它们以引用方式允许每个 PCL 配置文件的 100%。 为 Visual Studio for Mac，Visual Studio 和 NuGet 包管理器中的实际方便起见，Xamarin 项目允许使用仅具有不完整的实现的多个配置文件。 例如，Xamarin.iOS 和 Xamarin.Android 都不当前包括中的类型的完整实现`System.Diagnostics.Tracing`PCL 命名空间。 您可以解决此问题通过切换应用程序项目以引用 portable-net45 + win8 + wp8 + wpa81 TPL 数据流库版本。

## <a name="nuget-packages--xamarin-components"></a>NuGet 包和 Xamarin 组件
### <a name="how-can-i-update-nugetnuget-updatemd"></a>[如何更新 NuGet？](nuget-update.md)
可以下找到 NuGet 更新、 扩展和外接程序**更新**选项卡**NuGet 包管理器**。 本指南中有详细的导航以了解更新 Visual Studio 中 Mac 和 Visual Studio。

### <a name="how-do-i-downgrade-a-nuget-packagenuget-package-downgrademd"></a>[如何降级 NuGet 包？](nuget-package-downgrade.md)
Visual Studio for Mac 和 Visual Studio 这两个具有选择较旧版本的包和自动保存功能。 安装它们的功能类似于如何更新包的工作原理。

### <a name="missing-packages-error-after-updating-nuget-packagesnuget-packages-missingmd"></a>[在更新 Nuget 包后出现缺失包错误](nuget-packages-missing.md)
此问题主要已报告的 Xamarin.Forms 示例应用程序解决方案，但可在任何使用 NuGet 包的项目上发生此问题的可能性。

### <a name="unifying-google-play-services-components-and-nugetgps-components-nugetmd"></a>[统一 Google Play Services 组件和 NuGet](gps-components-nuget.md)
存在用于将多个 Google Play Services 组件和 NuGet 包，而面向开发人员简化操作，现在，统一我们的组件和 NuGet 包复制到以下两个。 在大多数情况下，应使用 Google Play Services。 若要使用 (Froyo) 包的唯一原因是如果您的主动目标 Froyo。

### <a name="where-are-the-components-stored-on-my-machinecomponent-storagemd"></a>[组件保存在计算机的什么位置？](component-storage.md)
每当你的 Xamarin 组件安装到应用程序项目时，它将被放置在本指南中所列的两个位置。


## <a name="troubleshooting"></a>疑难解答
### <a name="where-can-i-find-my-version-information-and-logsversion-logsmd"></a>[在哪里可以找到我的版本信息和日志？](version-logs.md)
本指南详细介绍在何处可以找到大多数可使用 Xamarin 的问题进行疑难解答的诊断信息。

### <a name="when-and-how-should-i-file-a-bug-reporthowto-file-bugmd"></a>[应何时以及如何提交 bug 报告？](howto-file-bug.md)
本指南提供提示以供填写高质量 bug 报告，以便我们的工程师能够问题更有效地确定原因 （以及任何可能的修补程序）。

### <a name="why-isnt-jenkins-supported-by-xamarinxamarin-jenkinsmd"></a>[为什么 Xamarin 不支持 Jenkins？](xamarin-jenkins.md)
Jenkins 是一组开放源代码 CI;由于这么多问题直接引发由 Jenkins*本身*需要被归为问题对你的代码; 例如[主 Jenkins 存储库](https://github.com/jenkinsci/jenkins)，或为存储库[Jenkins.app](https://github.com/stisti/jenkins-app)。

### <a name="what-project-settings-are-required-for-the-debuggerdebugger-settingsmd"></a>[调试器需要哪些项目设置？](debugger-settings.md)
为了使调试器能够正常运行 （命中的断点、 显示调试日志等），开发人员检测和调试信息显示必须同时启用。 本指南详细介绍了如何查找和激活这些设置。

