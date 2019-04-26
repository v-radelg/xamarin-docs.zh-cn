---
title: 是否可以从 Visual Studio 创建.xcarchive 存档？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 417D84FB-1BA9-4DB9-A683-66E960BA3D0D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/03/2018
ms.openlocfilehash: 952777b3178b82657c8f64d5882c532c188c7a79
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61422017"
---
# <a name="is-it-possible-to-create-a-xcarchive-archive-from-visual-studio"></a>是否可以从 Visual Studio 创建.xcarchive 存档？

## <a name="for-xamarin-4"></a>为 Xamarin 4

截至 Xamarin 4.x，现可创建`.xcarchive`从 Windows 通过设置`ArchiveOnBuild`属性设置为`true`。 例如，使用`MSBuild`命令行上：

```bash
msbuild /p:Configuration=Release /p:ServerAddress=10.211.55.2 /p:ServerUser=xamUser /p:Platform=iPhone /p:ArchiveOnBuild=true /t:"Build" MyProject.csproj
```

`.xcarchive`将被放入`$HOME/Library/Developer/Xcode/Archives`Xcode 和 Xamarin Studio 搜索显示以前生成的存档到 Mac 生成主机上的目录。

请参阅此[Xamarin 论坛发布](https://forums.xamarin.com/discussion/comment/156635/#Comment_156635)的一些简要的其他说明有关`ArchiveOnBuild`属性。 ，请参阅文档[Xamarin.iOS 命令行在 Windows 上生成](~/ios/get-started/installation/windows/connecting-to-mac/index.md)有关其他详细信息`ServerAddress`和`ServerUser`属性。

* * *

## <a name="for-xamarin-3-and-earlier"></a>对于 Xamarin 3 及更早版本

Xamarin 3.x Visual Studio 扩展不提供一种机制来生成`.xcarchive`存档。 话虽如此，用来创建的逻辑`.xcarchive`在 Mac 上的 Xamarin Studio 中将存档[此处所述](https://bugzilla.xamarin.com/show_bug.cgi?id=35#c5)，因此您可能无法创建你自己`.xcarchive`手动如果想。

但值得注意是，不需要`.xcarchive`要提交到 App Store。 只要使用 App Store 分发配置文件 （而非 Ad Hoc 分发配置文件） 进行签名，你可以提交的 IPA 文件。

事实上，您可以甚至只是进行压缩`.app`捆绑包 （即与应用程序存储分发配置文件签名），然后提交这`.zip`到 app store 的文件。

在任一情况下，您可以使用应用程序加载程序应用提交应用程序 （而非 Xcode）。

