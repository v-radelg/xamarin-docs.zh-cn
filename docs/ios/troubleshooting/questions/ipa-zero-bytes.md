---
title: IPA 文件为 0 字节
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 376BBA27-8694-4E63-9976-BF60349D42D8
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/21/2017
ms.openlocfilehash: 3ef916dbd277544309f1803f923cde302494e4f1
ms.sourcegitcommit: 61a35d0643eb3bf5adb8f8831da54771d8dde626
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033506"
---
# <a name="ipa-file-is-0-bytes"></a>IPA 文件为 0 字节

> [!IMPORTANT]
> 此问题已在最新版本的 Xamarin 中解决。 但是，如果该软件的最新版本发生问题，请使用完整的版本信息和完整的生成日志输出来记录[新的 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) 。

以前版本的 Xamarin 中存在一些已知问题，这些问题可能会导致 Windows 上的 IPA 文件为0字节。 

### <a name="fixed-in-xamarin-for-visual-studio-311584"></a>在适用于 Visual Studio 的 Xamarin 3.11.584 中修复 

- [Bug 24416-从命令行生成 "即席" 配置不会将 IPA 文件复制到 Windows](https://bugzilla.xamarin.com/show_bug.cgi?id=24416)
- [Bug 24417-更改 "项目属性-> iOS IPA 选项-> 包名称" 阻止将 IPA 复制回 Windows](https://bugzilla.xamarin.com/show_bug.cgi?id=24417)
- [Bug 29822-[XVS 3.11] 设置 "Build" 号与 "Version" 数字不同，导致 IPA 不会复制到 Windows](https://bugzilla.xamarin.com/show_bug.cgi?id=29822)

### <a name="fixed-in-xamarin-for-visual-studio-410496"></a>在适用于 Visual Studio 的 Xamarin 4.1.0.496 中修复

- [Bug 27989-如果程序集名称与项目名称不匹配，则在生成服务器上显示 ipa 文件将失败](https://bugzilla.xamarin.com/show_bug.cgi?id=27989)
