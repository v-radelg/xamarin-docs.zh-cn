---
title: 为何我的 Android 发布版本无法连接到 Internet？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A6FE770B-A19A-4BF8-95E9-2CF880D4AFC5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: dc84ecc0ee3a71cc4e1d4233f4d6d5f22f597b07
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69523480"
---
# <a name="why-cant-my-android-release-build-connect-to-the-internet"></a>为何我的 Android 发布版本无法连接到 Internet？

## <a name="cause"></a>原因

此问题的最常见原因是: **INTERNET**权限自动包括在调试版本中, 但必须为发布版本手动设置。 这是因为 Internet 权限用于允许调试器附加到进程, 如[此处](~/android/deploy-test/building-apps/build-process.md)"DebugSymbols" 所述。


## <a name="fix"></a>修补程序

若要解决此问题, 你可以在 Android 清单中要求 Internet 权限。 可以通过清单编辑器或清单的源代码来完成此操作:

- 在编辑器中修复:在 Android 项目中转到**Properties-> androidmanifest.xml-> 必需权限**并选中**Internet**

- 在源代码中修复:在源编辑器中打开 androidmanifest.xml, 并在`<Manifest>`标记中添加权限标记:

    ```xml
    <Manifest>
    ...
    <uses-permission android:name="android.permission.INTERNET" />
    </Manifest>
    ```
