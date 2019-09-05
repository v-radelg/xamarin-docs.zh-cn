---
title: 是否可以从 Visual Studio 创建 .xcarchive 存档？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 417D84FB-1BA9-4DB9-A683-66E960BA3D0D
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: 6d35827b00a4ccc9bbe3e71444536425e4e1c3b1
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70288114"
---
# <a name="is-it-possible-to-create-a-xcarchive-archive-from-visual-studio"></a>是否可以从 Visual Studio 创建 .xcarchive 存档？

## <a name="for-xamarin-4"></a>适用于 Xamarin 4

`.xcarchive`自 Xamarin 4.x 开始，现在可以通过`ArchiveOnBuild`将属性设置为`true`，从 Windows 创建。 例如，在命令`MSBuild`行上使用：

```bash
msbuild /p:Configuration=Release /p:ServerAddress=10.211.55.2 /p:ServerUser=xamUser /p:Platform=iPhone /p:ArchiveOnBuild=true /t:"Build" MyProject.csproj
```

将放置在 Mac 生成主机上的目录中，Xcode和XamarinStudio搜索以显示以前生成的存档。`$HOME/Library/Developer/Xcode/Archives` `.xcarchive`

有关`ArchiveOnBuild`属性的一些简短附加注释，请参阅此[Xamarin 论坛文章](https://forums.xamarin.com/discussion/comment/156635/#Comment_156635)。 有关`ServerAddress`和属性的其他详细信息， `ServerUser`请参阅[Windows 上的有关 Xamarin 的 Windows 版本](~/ios/get-started/installation/windows/connecting-to-mac/index.md)的文档。

* * *

## <a name="for-xamarin-3-and-earlier"></a>适用于 Xamarin 3 及更早版本

Xamarin 1.x Visual Studio 扩展不提供生成`.xcarchive`存档的机制。 也就是说，[此处描述](https://bugzilla.xamarin.com/show_bug.cgi?id=35#c5)了用于在 Mac `.xcarchive`的 Xamarin Studio 中创建存档的逻辑，因此，如果需要，可以手动创建`.xcarchive` 。

但值得注意的是，无需将`.xcarchive`提交到应用商店。 只要 IPA 文件使用应用商店分发配置文件（而不是即席分发配置文件）进行签名，就可以提交该文件。

事实上，您甚至`.app`可以将捆绑包（使用 App store 分发配置文件进行签名），并将该`.zip`文件提交到 app store。

在任一情况下，都可以使用应用程序加载程序应用程序来提交应用程序（而不是 Xcode）。

