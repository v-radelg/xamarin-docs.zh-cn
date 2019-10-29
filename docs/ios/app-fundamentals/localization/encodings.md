---
title: Xamarin 中的国际化编码
description: 本文档介绍了 Xamarin 中的国际化编码，讨论了可用的编码，以及如何将它们添加到应用中。
ms.prod: xamarin
ms.assetid: F5117294-28BB-4583-B6A0-A339B050FDE1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/28/2017
ms.openlocfilehash: 2d204aeac18fc08bf6eb2221b6f64ecbbded8121
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73009993"
---
# <a name="internationalization-encodings-in-xamarinios"></a>Xamarin 中的国际化编码

默认情况下，并非所有编码都包含在 Xamarin 类库中。

为了减小应用程序的大小，Xamarin 不包括任何特定的编码，并且必须指示 mtouch 包含包含所需编码支持的程序集。

这是通过在 Visual Studio for Mac 或 Visual Studio 中的 "iOS 生成"/"高级" 窗格中选择额外的编码来完成的：

 [![](encodings-images/00.png "Selecting the extra encodings")](encodings-images/00.png#lightbox)

 [![](encodings-images/00a.png "Selecting the extra encodings")](encodings-images/00a.png#lightbox)

您可以选择以下其中一项：

- cjk： for Chineese，日语和韩语
- mideast：阿拉伯语、希伯来语、土耳其语和 Latin5。
- 其他：西里尔语、波罗的语、越南语、乌克兰语和泰语
- 罕见： EBCDIC 编码和其他罕见代码页
- 西部：拉丁语语言、复活节和西欧
- 全部

 <a name="cjk" />

## <a name="cjk"></a>cjk

- CP51932
- CP932
- CP936
- CP949
- CP950
- CP54936

 <a name="mideast" />

## <a name="mideast"></a>mideast

- CP1254
- CP1255
- CP1256
- CP28596
- CP28598
- CP28599
- CP38598

 <a name="other" />

## <a name="other"></a>其他

- CP1251
- CP1257
- CP1258
- CP20866
- CP21866
- CP28594
- CP28595
- CP57002
- CP874

 <a name="rare" />

## <a name="rare"></a>极少数

- CP1026
- CP1047
- CP1140
- CP1141
- CP1142
- CP1143
- CP1144
- CP1145
- CP1146
- CP1147
- CP1148
- CP1149
- CP20273
- CP20277
- CP20278
- CP20280
- CP20284
- CP20285
- CP20290
- CP20297
- CP20420
- CP20424
- CP20871
- CP21025
- CP37
- CP500
- CP708
- CP852
- CP855
- CP857
- CP858
- CP862
- CP864
- CP866
- CP869
- CP870
- CP875

 <a name="west" />

## <a name="west"></a>西部

- CP10000
- CP10079
- CP1250
- CP1252
- CP1253
- CP28592
- CP28593
- CP28597
- CP28605
- CP437
- CP850
- CP860
- CP861
- CP863
- CP865
