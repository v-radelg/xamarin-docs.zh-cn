---
title: 一般常见问题
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C7E6E54D-3957-407D-BB87-22B095148C6B
author: davidortinau
ms.author: daortin
ms.date: 05/08/2018
ms.openlocfilehash: 0e49ef8fa0bf00d5ed41f3411393ffaf4891c1b8
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73013996"
---
# <a name="general-frequently-asked-questions"></a>一般常见问题

## <a name="portable-class-libraries"></a>可移植类库

### <a name="how-can-i-view-what-libraries-are-supported-in-a-pclpcl-support-librariesmd"></a>[如何查看 PCL 中支持的库？](pcl-support-libraries.md)
本指南列出了用于确定不同 PCL 目标平台是否支持现有库，或可转换为 PCL 配置文件的资源和方法。

### <a name="pcl-reflection-apipcl-reflectionmd"></a>[PCL 反射 API](pcl-reflection.md)
Microsoft 开发了新的反射 API，可在可移植类库中使用。 如果要将一些现有的反射代码移到 PCL，则它可能不起作用。

### <a name="pcl-case-study-how-can-i-resolve-problems-related-to-systemdiagnosticstracing-for-the-microsoft-tpl-dataflow-nuget-packagepcl-case-studymd"></a>[PCL 案例研究：如何解决与 Microsoft TPL 数据流 NuGet 包的 System.Diagnostics.Tracing 相关的问题？](pcl-case-study.md)
Xamarin 和 Xamarin 不会实现它们允许作为引用的每个 PCL 配置文件的100%。 为了在 Visual Studio for Mac、Visual Studio 和 NuGet 包管理器中提供切实可行的便利，Xamarin 项目允许使用几个只具有不完整实现的配置文件。 例如，Xamarin 和 Xamarin 目前都不包括 `System.Diagnostics.Tracing` PCL 命名空间中的类型的完整实现。 若要解决此情况，可以切换应用程序项目，以引用 TPL 数据流库的 net45 + win8 + wp8 + wpa81 版本。

## <a name="nuget-packages--xamarin-components"></a>& Xamarin 组件的 NuGet 包
### <a name="how-can-i-update-nugetnuget-updatemd"></a>[如何更新 NuGet？](nuget-update.md)
NuGet 更新、扩展和外接程序可在**Nuget 包管理器**的 "**更新**" 选项卡中找到。 在本指南中查找 Visual Studio for Mac & Visual Studio 中的更新的详细导航。

### <a name="how-do-i-downgrade-a-nuget-packagenuget-package-downgrademd"></a>[如何降级 NuGet 包？](nuget-package-downgrade.md)
& Visual Studio 的 Visual Studio for Mac 都具有用于选择旧版本包和自动安装包的功能;与更新包的工作方式类似。

### <a name="missing-packages-error-after-updating-nuget-packagesnuget-packages-missingmd"></a>[在更新 Nuget 包后出现缺失包错误](nuget-packages-missing.md)
此问题主要在 Xamarin. Forms 示例应用解决方案上进行报告，但对于使用 NuGet 包的任何项目，可能会发生此问题。

### <a name="unifying-google-play-services-components-and-nugetgps-components-nugetmd"></a>[统一 Google Play Services 组件和 NuGet](gps-components-nuget.md)
这里已有多个 Google Play Services 组件和 NuGet 程序包，但为了使开发人员可以更轻松地进行开发，我们现在已将我们的组件和 NuGet 包合并为两个。 几乎在所有情况下，都应使用 Google Play Services。 使用（Froyo）包的唯一理由是，如果你积极定位 Froyo。

### <a name="where-are-the-components-stored-on-my-machinecomponent-storagemd"></a>[组件保存在计算机的什么位置？](component-storage.md)
每次将 Xamarin 组件安装到应用项目中时，都会将其放入本指南中列出的两个位置。

## <a name="troubleshooting"></a>疑难解答
### <a name="where-can-i-find-my-version-information-and-logsversion-logsmd"></a>[在哪里可以找到我的版本信息和日志？](version-logs.md)
本指南详细介绍了在哪里可以找到可用于排查 Xamarin 问题的大多数诊断信息。

### <a name="when-and-how-should-i-file-a-bug-reporthowto-file-bugmd"></a>[应何时以及如何提交 bug 报告？](howto-file-bug.md)
本指南提供了有关如何将高质量 bug 报告存档的提示，以便我们的工程师能够更有效地确定问题的原因（以及任何可能的修补程序）。

### <a name="why-isnt-jenkins-supported-by-xamarinxamarin-jenkinsmd"></a>[为什么 Xamarin 不支持 Jenkins？](xamarin-jenkins.md)
Jenkins 是开源 CI 套件;由于此 Jenkins*本身*直接导致的许多问题都需要在您收到代码的位置被归档为问题;例如，[主 Jenkins](https://github.com/jenkinsci/jenkins)存储库或[Jenkins](https://github.com/stisti/jenkins-app)的存储库。

### <a name="what-project-settings-are-required-for-the-debuggerdebugger-settingsmd"></a>[调试器需要哪些项目设置？](debugger-settings.md)
为了使调试器能够按预期工作（命中断点、显示调试日志等），必须同时启用开发人员检测和调试信息显示。 本指南详细介绍了如何查找和激活这些设置。
