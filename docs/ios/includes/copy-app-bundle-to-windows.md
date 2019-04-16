---
ms.openlocfilehash: 5bcf548c0ff907df0913e77a1def2fe27f3eecfd
ms.sourcegitcommit: e7f27ba75cae5099ef053b819b84132a77d4f9e7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2019
ms.locfileid: "52294942"
---

在 Visual Studio 和 Mac 生成代理中生成 iOS 应用时，.app 应用程序包不会复制回 Windows 计算机。 Xamarin Tools for Visual Studio 7.4 新增了 `CopyAppBundle` 属性，以便 CI 内部版本可以将 .app 应用程序包复制回 Windows。

若要使用此功能，请将 `CopyAppBundle` 属性添加到 .csproj 中要应用此功能的属性组下。 例如，下面的示例展示了如何为定目标到“iPhoneSimulator”的“调试”内部版本，将 .app 应用程序包复制回 Windows 计算机：

    <PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Debug|iPhoneSimulator' ">
        <CopyAppBundle>true</CopyAppBundle>
    </PropertyGroup>

