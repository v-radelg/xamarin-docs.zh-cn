---
title: 为什么我的 iOS 生成会失败，并显示“在 keychain 中未找到有效的 iPhone 代码签名密钥”？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9DF24C46-D521-4112-9B21-52EA4E8D90D0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: 0c777b8d5326963e959d8bb13d81d7058caa6bde
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030937"
---
# <a name="why-does-my-ios-build-fail-with-no-valid-iphone-code-signing-keys-found-in-keychain"></a>为什么我的 iOS 生成会失败，并显示“在 keychain 中未找到有效的 iPhone 代码签名密钥”？

## <a name="cause-of-the-error"></a>错误的原因

当有问题的项目正在查找有效的代码签名凭据但找不到它们时，会出现此错误消息。 在物理 iOS 设备上进行测试和部署需要进行代码签名;以及即席 & 应用商店版本。

### <a name="provisioning-devices"></a>预配设备

如果你之前未预配 iOS 设备，以下指南将指导你完成完整的分步过程：[设备预配指南](~/ios/get-started/installation/device-provisioning/index.md)

## <a name="bug-when-using-ios-simulator"></a>使用 iOS 模拟器时的 Bug

> [!NOTE]
> 此问题已在最新版本的 Xamarin for Visual Studio 中得到解决。 但是，如果该软件的最新版本发生问题，请使用完整的版本信息和完整的生成日志输出来记录[新的 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) 。

Xamarin 中存在 bug。 Visual Studio 3.11 导致了 Xamarin. Forms 模板中的 iOS 项目，以将 codesign info.plist 添加到模拟器生成;使用模拟器有效地阻止测试。

### <a name="how-to-fix"></a>如何修复

可以通过从 .csproj 文件中的调试生成中删除 `<CodesignEntitlements>` 标志来解决该问题。 你可以按如下所示进行操作：

> [!WARNING]
> .Csproj 文件中的错误可能会破坏您的项目，因此在尝试此操作之前，最好先备份您的文件。

1. 在解决方案窗格中右键单击 iOS 项目，然后选择 "**卸载项目**"
2. 再次右键单击该项目，然后选择 "**编辑 [项目名称] .csproj** "
3. 找到调试 PropertyGroups，它们应以如下所示的标志开头：
   - 调试： `<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Debug|iPhoneSimulator' ">`
   - 版本： `<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Release|iPhoneSimulator' ">`
4. 在使用模拟器的每个版本中，删除或注释掉以下属性： `<CodesignEntitlements>Entitlements.plist</CodesignEntitlements>`
5. 重载项目，你应该能够部署到模拟器。

### <a name="next-steps"></a>后续步骤
若要获得更多帮助，请联系我们，或者，如果在使用上述信息后仍出现此问题，请参阅[哪些支持选项可用于 Xamarin？](~/cross-platform/troubleshooting/support-options.md)有关联系人选项、建议的信息，以及如何在需要时记录新 bug.
