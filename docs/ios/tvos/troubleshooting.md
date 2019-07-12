---
title: 使用 Xamarin 生成的 tvOS 应用进行故障排除
description: 本文提供了各种提示来帮助解决在使用 Xamarin 生成的 tvOS 应用的开发过程。 它描述了已知的问题和特定错误。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 124E4953-4DFA-42B0-BCFC-3227508FE4A6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 6830df267aa0b9c4f12fbd53520206ea94fc8a38
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67831893"
---
# <a name="troubleshooting-tvos-apps-built-with-xamarin"></a>使用 Xamarin 生成的 tvOS 应用进行故障排除

_本文介绍了解使用 Xamarin 的 tvOS 支持时可能遇到的问题。_

<a name="Known-Issues" />

## <a name="known-issues"></a>已知问题

当前版本的 Xamarin 的 tvOS 支持具有以下已知的问题：

- **Mono 框架**– Mono 4.3 Cryptography.ProtectedData 失败从 Mono 4.2 的数据进行解密。 结果是，NuGet 包都将失败并出现错误还原`Data unprotection failed`如果受保护的 NuGet 源配置。
    - **解决方法**– 在 Visual Studio for Mac 将需要重新添加任何 NuGet 包源然后再重新尝试还原包的使用密码身份验证。
- **Visual Studio for Mac w /F#外接程序**– 在创建时的错误F#在 Windows 上的 Android 模板。 这应仍正常工作的 mac 上
- **Xamarin.Mac** – 当运行 Xamarin.Mac 统一的模板项目目标框架设置为`Unsupported`，弹出框`Could not connect to the debugger`可能会出现。
    - **可能的变通方法**– 降级我们稳定通道中可用的 Mono 框架版本。
- **Xamarin Visual Studio 和 Xamarin.iOS** -在 Visual studio 中，该错误的 WatchKit 应用程序部署时`The file ‘bin\iPhoneSimulator\Debug\WatchKitApp1WatchKitApp.app\WatchKitApp1WatchKitApp’ does not exist`可能会出现。

报表任何 bug，请在上找到[GitHub](https://github.com/xamarin/xamarin-macios/issues/new)。

## <a name="troubleshooting"></a>疑难解答

以下部分列出了使用 tvOS 9 与 Xamarin.tvOS 和这些问题的解决方案时可能发生的一些已知的问题：

### <a name="invalid-executable---the-executable-does-not-contain-bitcode"></a>无效的可执行文件的可执行文件不包含 bitcode

在尝试以提交到 Apple TV App Store Xamarin.tvOS 应用程序时，可能会在窗体中获取一条错误消息 _"无效的可执行文件的可执行文件不包含 bitcode"_ 。

若要解决此问题，请执行以下操作：

1. 在 Visual Studio for Mac 中，右键单击你 Xamarin.tvOS 项目文件中**解决方案资源管理器**，然后选择**选项**。
2. 选择**tvOS 生成**，并确保你位于**发行**配置： 

    [![](troubleshooting-images/ts01.png "选择 tvOS 生成选项")](troubleshooting-images/ts01.png#lightbox)
3. 添加`--bitcode=asmonly`到**其他 mtouch 参数**字段，然后单击**确定**按钮。
4. 重新生成你的应用**版本**配置。

### <a name="verifying-that-your-tvos-app-contains-bitcode"></a>验证你的 tvOS 应用包含 Bitcode

若要验证生成的 Xamarin.tvOS 应用包含 Bitcode，打开终端应用并输入以下信息：

```csharp
otool -l /path/to/your/tv.app/tv
```

在输出中，查找以下：

```csharp
Section
  sectname __bundle
   segname __LLVM
      addr 0x0000000100001000
      size 0x000000000000124f
    offset 4096
     align 2^0 (1)
    reloff 0
    nreloc 0
     flags 0x00000000
 reserved1 0
 reserved2 0
```

`addr` 和`size`将会不同，但应与其他字段相同。

将需要确保静态任何第三方 (`.a`) 正在使用的库生成针对 tvOS 库 （非 iOS 库），它们还包括 bitcode 信息。

应用或库，包括有效 bitcode`size`将大于 1。 有某些情况下不包含有效 bitcode 还没有 bitcode 标记，一个库，可以在其中。 例如:

**无效的 Bitcode**

```csharp
 $ otool -arch arm64 libLibrary.a | grep __bitcode -A 3
   sect name __bitcode
   segname __LLVM
      add 0x0000000000000670
      size 0x0000000000000001
```

**有效 Bitcode** 

```csharp
$ otool -l -arch arm64 libDownloadableAgent-tvos.a |grep __bitcode -A 3
   sectname __bitcode
   segname __LLVM
      addr 0x000000000001d2d0
      size 0x0000000000045440
```

请注意在差异`size`之间在列出的示例中的两个库运行上面。 库必须从 Xcode 存档生成以生成启用 bitcode (Xcode 设置`ENABLE_BITCODE`) 作为此大小问题的解决方案。

### <a name="apps-that-only-contain-the-arm64-slice-must-also-have-arm64-in-the-list-of-uirequireddevicecapabilities-in-infoplist"></a>仅包含 arm64 切片应用的 Info.plist 中 UIRequiredDeviceCapabilities 列表中还必须具有"arm64"

当提交到 Apple TV App Store 发布的应用，你可能会遇到错误，在窗体中：

_"仅包含 arm64 切片应用还必须具有"arm64"UIRequiredDeviceCapabilities Info.plist 中的列表中"_

如果发生这种情况，编辑你`Info.plist`文件，并确保它具有以下项：

```xml
<key>UIRequiredDeviceCapabilities</key>
<array>
    <string>arm64</string>
</array>
```

重新编译你的应用版本并重新提交到 iTunes Connect。

### <a name="task-mtouch-execution----failed"></a>任务"MTouch"执行-失败

如果使用第三方库 （如 MonoGame) 和版本编译失败，出现错误消息中结束的一长串`Task "MTouch" execution -- FAILED`，请尝试添加`-gcc_flags="-framework OpenAL"`到你**额外触控参数**:

[![](troubleshooting-images/mtouch01.png "MTouch 执行任务")](troubleshooting-images/mtouch01.png#lightbox)

此外应包括`--bitcode=asmonly`中**额外触控参数**，链接器选项设置为**链接所有**执行干净的编译。

### <a name="itms-90471-error-the-large-icon-is-missing"></a>ITMS 90471 时出错。 大图标缺少

如果窗体中的消息"ITMS 90471 错误。 大图标缺少"尝试提交到版本中，Apple TV App Store Xamarin.tvOS 应用程序时请检查以下各项：

1. 确保你已包含在大图标资产你`Assets.car`使用创建的文件[应用图标](~/ios/tvos/app-fundamentals/icons-images.md#App-Icons)文档。
2. 确保您包括`Assets.car`文件从[使用的图标和图像](~/ios/tvos/app-fundamentals/icons-images.md)最终的应用程序捆绑包中的文档。

### <a name="invalid-bundle--an-app-that-supports-game-controllers-must-also-support-the-apple-tv-remote"></a>捆绑无效-游戏控制器支持的应用还必须支持 Apple TV 远程

或 

### <a name="invalid-bundle--apple-tv-apps-with-the-gamecontroller-framework-must-include-the-gcsupportedgamecontrollers-key-in-the-apps-infoplist"></a>捆绑无效-GameController framework 使用 Apple TV 应用必须在应用的 info.plist 文件中包含的 GCSupportedGameControllers 密钥

游戏控制器可以用于增强游戏玩法，并提供在游戏中的浸入式的。 它们还可以用于控制标准的 Apple TV 接口，以便用户不具有远程和控制器之间进行切换。

如果您要提交到 Apple TV 应用商店的游戏控制器支持包含的 Xamarin.tvOS 应用并收到一条错误消息的形式：


_我们已发现的"应用名称"最近交付的一个或多个问题。交付已成功，但你可能想要更正中下一步行动的以下问题：_

_捆绑无效-游戏控制器支持的应用还必须支持 Apple TV 远程。_

或 

_捆绑无效-GameController framework 使用 Apple TV 应用必须在应用的 info.plist 文件中包含的 GCSupportedGameControllers 密钥。_

解决方法是添加对 Siri 远程支持 (`GCMicroGamepad`) 到应用程序的`Info.plist`文件。 微型游戏控制器配置文件都已由 Apple Siri 远程目标。 例如，包括以下项：

```xml
<key>GCSupportedGameControllers</key>  
  <array>  
    <dict>  
      <key>ProfileName</key>  
      <string>ExtendedGamepad</string>  
    </dict>  
    <dict>  
      <key>ProfileName</key>  
      <string>MicroGamepad</string>  
    </dict>  
  </array>  
<key>GCSupportsControllerUserInteraction</key>  
<true/>
```

> [!IMPORTANT]
> 蓝牙游戏控制器的可选购买内容，可能会使最终用户，您的应用程序不能强制用户购买一个。 如果您的应用程序支持游戏控制器，它还必须支持 Siri 远程，以便游戏的 Apple TV 的所有用户。

有关详细信息，请参阅我们[使用游戏控制器](~/ios/tvos/platform/remote-bluetooth.md#Working-with-Game-Controllers)一部分我们[Siri 远程和蓝牙控制器](~/ios/tvos/platform/remote-bluetooth.md)文档。

### <a name="incompatible-target-framework-netportable-versionv45-profileprofile78"></a>目标框架不兼容:。NetPortable，版本 = v4.5 中，配置文件 = Profile78

尝试将以可移植类库 (PCL) 包含到 Xamarin.tvOS 项目时可能会出现一条消息以形成：

_目标框架不兼容:。NetPortable，版本 = v4.5 中，配置文件 = Profile78_

若要解决此问题，添加名为 XML 文件`Xamarin.TVOS.xml`包含以下内容：

```xml
<Framework Identifier="Xamarin.TVOS" MinimumVersion="1.0" Profile="*" DisplayName="Xamarin.TVOS"/>
```

到以下路径：

```csharp
/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/xbuild-frameworks/.NETPortable/v4.5/Profile/Profile259/SupportedFrameworks/

```

请注意，在路径中的配置文件数必须匹配的 PCL 配置文件数。

与此文件之后，您应能够成功地将 PCL 文件添加到 Xamarin.tvOS 项目。



## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [适用于 tvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
