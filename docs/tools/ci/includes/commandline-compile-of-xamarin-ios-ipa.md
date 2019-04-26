---
ms.openlocfilehash: 05f1017f8c4b306996d3e8e165511ff9062a1026
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61047484"
---

下面的命令行，以指定解决方案的发布版本**SOLUTION_FILE.sln**适用于 iPhone。 可以通过指定设置的 IPA 位置`IpaPackageDir`命令行上的属性：

 - 在 Mac 上使用**xbuild**:

        xbuild /p:Configuration="Release" \ 
           /p:Platform="iPhone" \ 
           /p:IpaPackageDir="$HOME/Builds" \
           /t:Build MyProject.sln

**Xbuild**命令通常在目录中找到 **/Library/Frameworks/Mono.framework/Commands**。

 - 在 Windows 中，使用**msbuild**:

        msbuild /p:Configuration="Release" 
            /p:Platform="iPhone" 
            /p:IpaPackageDir="%USERPROFILE%\Builds" 
            /p:ServerAddress="192.168.1.3" /p:ServerUser="macuser"  
            /t:Build MyProject.sln


**msbuild**将不会自动展开`$( )`表达式通过命令行中传递。 出于此原因建议设置时使用的完整路径`IpaPackageDir`在命令行。


请参阅[发行说明适用于 iOS 9.8](https://developer.xamarin.com/releases/ios/xamarin.ios_9/xamarin.ios_9.8/#New_MSBuild_property_IpaPackageDir_to_customize_.ipa_output_location)有关详细信息`IpaPackageDir`属性。
