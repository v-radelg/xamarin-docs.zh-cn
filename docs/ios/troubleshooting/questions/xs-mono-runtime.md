---
title: 如何在 Xamarin Studio 中为 iOS 项目设置 Mono 运行时环境变量？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 1176CEA9-C7F1-411B-8F1A-99374E8AFF33
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/31/2017
ms.openlocfilehash: f8e3855b10a20bd4312420f8faf6c68dedde0c67
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292093"
---
# <a name="how-do-i-set-mono-runtime-environment-variables-for-ios-projects-in-xamarin-studio"></a>如何在 Xamarin Studio 中为 iOS 项目设置 Mono 运行时环境变量？

如果需要为 Mono 设置任何运行时环境变量，则可以在 "项目选项" 中设置 **> 运行 > 常规**"页。

注意:SGen （MONO\_GC\_PARAMS）集的垃圾回收环境变量仅在从 Xamarin Studio 启动时才会使用。 如果从设备中启动应用，则会忽略 Sgen 的设置。 

若要为应用永久设置环境变量，需要将此作为附加的 mtouch 参数添加（适用于所有相关配置）：

```csharp
   --setenv=NAME=VALUE
```

若要查看可设置的环境变量，请参阅 Mono 手册页：[http://docs.go-mono.com/?link=man%3amono(1)](http://docs.go-mono.com/?link=man%3amono(1))请参阅标题为的部分：`ENVIRONMENT VARIABLES`

![](xs-mono-runtime-images/environment-variables.jpg "为项目设置环境变量")
