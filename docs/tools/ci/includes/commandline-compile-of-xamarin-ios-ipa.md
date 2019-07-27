---
ms.openlocfilehash: e383bbccd4e76be8a208f5680e5cf21e45a0dbc3
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511927"
---

以下命令行为 iPhone 指定解决方案**SOLUTION_FILE**的发布版本。 可以通过在命令行上指定`IpaPackageDir`属性来设置 IPA 的位置:

- 在 Mac 上, 使用**xbuild**:

        xbuild /p:Configuration="Release" \ 
           /p:Platform="iPhone" \ 
           /p:IpaPackageDir="$HOME/Builds" \
           /t:Build MyProject.sln

**Xbuild**命令通常位于目录 **/Library/Frameworks/Mono.framework/Commands**中。

- 在 Windows 上, 使用**msbuild**:

        msbuild /p:Configuration="Release" 
            /p:Platform="iPhone" 
            /p:IpaPackageDir="%USERPROFILE%\Builds" 
            /p:ServerAddress="192.168.1.3" /p:ServerUser="macuser"  
            /t:Build MyProject.sln


**msbuild**不会自动扩展`$( )`命令行传入的表达式。 出于此原因, 建议您在命令行设置`IpaPackageDir`时使用完整路径。

有关该`IpaPackageDir`属性的更多详细信息, 请参阅[iOS 9.8 的发行说明](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/xamarin.ios_9/xamarin.ios_9.8.md#new-msbuild-property-ipapackagedir-to-customize-ipa-output-location)。
