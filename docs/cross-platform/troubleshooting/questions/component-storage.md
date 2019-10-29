---
title: 组件保存在计算机的什么位置？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5EBB49EE-39E5-428B-866F-9FC1BB215B31
author: davidortinau
ms.author: daortin
ms.date: 05/08/2018
ms.openlocfilehash: 9447cf903c8789078e66082e720eeecfa7bb3e0d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73014241"
---
# <a name="where-are-the-components-stored-on-my-machine"></a>组件保存在计算机的什么位置？

每次将 Xamarin 组件安装到应用项目中时，都会将其放在两个位置：

1. 位于解决方案文件夹根级别的 "组件" 文件夹中。 如果从解决方案中的所有项目中删除该组件，则该组件也将从此文件夹中删除。

2. 副本还存储在下列位置：
    - Windows：`%LocalAppData%\Xamarin\Cache\Components`
    - Mac： `~/Library/Caches/Xamarin/Components`

因此，若要彻底删除系统中的组件，请从项目/解决方案中将其删除，并从上面的缓存文件夹中将其删除。
