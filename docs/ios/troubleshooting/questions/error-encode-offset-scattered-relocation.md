---
title: 编译错误:无法在生成的分散重定位中编码偏移 X
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 84158C42-FE79-415A-A44A-D48C9E5E6760
ms.technology: xamarin-ios
author: chamons
ms.author: chhamo
ms.date: 08/07/2019
ms.openlocfilehash: df974649c8c9f1d1fb4c13d6d801eb0f6a3d1e77
ms.sourcegitcommit: 2e5a6b8bcd1a073b54604f51538fd108e1c2a8e5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68876226"
---
# <a name="compile-error-can-not-encode-offset-x-in-resulting-scattered-relocation"></a>编译错误:无法在生成的分散重定位中编码偏移 X

```
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Xamarin\iOS\Xamarin.iOS.Common.targets(804,3): error GDC116A36: can not encode offset ‘0x1155504’ in resulting scattered relocation.
1> .long mono_aot_Xamarin_iOS_got - . + 12 (TaskId:141)
1> ^ (TaskId:141)
1>C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Xamarin\iOS\Xamarin.iOS.Common.targets(804,3): error GDC116A36: can not encode offset ‘0x11555B8’ in resulting scattered relocation.
1> .long mono_aot_Xamarin_iOS_got - . + 16 (TaskId:141)
```

当最终二进制文件对本机工具链过大时, 为32位体系结构 (如 ARMv7) 生成时, 将出现此问题。

若要解决此问题:

- 在项目选项的 "iOS 生成" 窗格中删除此体系结构的生成。
- 启用链接或手动删除代码以减少最终的可执行文件大小
