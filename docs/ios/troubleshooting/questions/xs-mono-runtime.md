---
title: 如何在 Xamarin Studio 中为 iOS 项目设置 Mono 运行时环境变量？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 1176CEA9-C7F1-411B-8F1A-99374E8AFF33
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/31/2017
ms.openlocfilehash: ba74e316f706e5bf22f973de4dad38d94ccd0db9
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61417653"
---
# <a name="how-do-i-set-mono-runtime-environment-variables-for-ios-projects-in-xamarin-studio"></a>如何在 Xamarin Studio 中为 iOS 项目设置 Mono 运行时环境变量？

如果需要为 Mono 设置任何运行时环境变量，可以将它们设置**项目选项 > 运行 > 常规**页。

注意:SGen 垃圾收集环境变量 (MONO\_GC\_PARAMS) 从 Xamarin Studio 启动时，才会使用这种方式的设置。 如果您启动从设备应用，将忽略 Sgen 的设置。 

若要永久设置环境变量的应用，需要将这添加作为其他 mtouch 参数 （适用于所有相关的配置）：

```csharp
   --setenv=NAME=VALUE
```

若要查看可设置的环境变量，请参阅 Mono 手册页：[http://docs.go-mono.com/?link=man%3amono(1)](http://docs.go-mono.com/?link=man%3amono(1)) 请参阅以下节： `ENVIRONMENT VARIABLES`

![](xs-mono-runtime-images/environment-variables.jpg "为项目设置环境变量")
