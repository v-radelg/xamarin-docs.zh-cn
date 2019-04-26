---
title: 为什么我的 iOS 生成会失败，并显示“在 keychain 中未找到有效的 iPhone 代码签名密钥”？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9DF24C46-D521-4112-9B21-52EA4E8D90D0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/03/2018
ms.openlocfilehash: fe267db1f83695b3d0e8f3d828f91e01b56ba8ee
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61419660"
---
# <a name="why-does-my-ios-build-fail-with-no-valid-iphone-code-signing-keys-found-in-keychain"></a>为什么我的 iOS 生成会失败，并显示“在 keychain 中未找到有效的 iPhone 代码签名密钥”？

## <a name="cause-of-the-error"></a>错误的原因
此错误消息相关的项目正在寻找代码签名的有效凭据时，会发生，但找不到它们。 代码签名才可进行测试和部署在物理 iOS 设备;以及 Ad hoc 和应用存储的生成。 


### <a name="provisioning-devices"></a>预配设备
如果尚未预配 iOS 设备之前，以下指南将引导您完成完整分步过程：[设备预配指南](~/ios/get-started/installation/device-provisioning/index.md)


## <a name="bug-when-using-ios-simulator"></a>使用 iOS 模拟器时的 bug

> [!NOTE]
> 具有最新版本的 Xamarin 中已解决此问题，Visual studio。 但是，如果最新版本的软件会出现此问题，请提出[新 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md)与完整的版本控制信息和完整生成日志输出。


这会导致 iOS 项目中用于添加 codesign Entitlements.plist 到模拟器生成; 的 Xamarin.Forms 模板 Xamarin.Visual Studio 3.11 中出现 bug有效地阻止使用模拟器进行测试。

### <a name="how-to-fix"></a>如何修复错误
您可以解决此问题通过移除`<CodesignEntitlements>`.csproj 文件中生成的调试中的标志。 你可以按如下所示进行操作：

*警告：.Csproj 文件中的错误会中断你的项目，因此，最好在这种尝试之前备份您的文件。*

1. 右键单击解决方案窗格中的 iOS 项目，然后选择**卸载项目**
2. 右键单击项目并选择**编辑 [ProjectName].csproj**
3. 找到调试 PropertyGroups，它们应首先阅读标志，如下所示：
   - 调试： `<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Debug|iPhoneSimulator' ">`
   - 版本： `<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Release|iPhoneSimulator' ">`
4. 在每个使用模拟器的生成，删除或注释掉以下属性： `<CodesignEntitlements>Entitlements.plist</CodesignEntitlements>`
5. 重新加载项目，您应该能够将部署到模拟器。

### <a name="next-steps"></a>后续步骤
获取进一步的帮助，请与我们联系，或如果此问题仍即使利用上述信息，请参阅[了可用于 Xamarin 的支持选项？](~/cross-platform/troubleshooting/support-options.md)有关联系人选项，建议的信息以及如何如果需要，提交新 bug。 
