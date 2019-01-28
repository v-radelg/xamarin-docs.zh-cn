---
title: Xamarin.iOS 中的应用组功能
description: 向应用程序添加功能通常需要其他预配设置。 本指南将介绍应用组功能所需的设置。
ms.prod: xamarin
ms.assetid: 0A61220B-BBAC-492B-9D3B-578986E64064
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/15/2017
ms.openlocfilehash: f75c4777ce7a47e983683bf3d92ac20ab1b15c1c
ms.sourcegitcommit: 190808013249005ceffbc798f9f4570e8cdc943a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54841349"
---
# <a name="app-group-capabilities-in-xamarinios"></a>Xamarin.iOS 中的应用组功能

_向应用程序添加功能通常需要其他预配设置。本指南将介绍应用组功能所需的设置。_

应用组允许不同的应用程序（或一个应用程序及其扩展）访问共享文件存储位置。 应用组可以用于如下所示的数据：

*   [Apple Watch 设置](~/ios/watchos/app-fundamentals/settings.md)
*   [共享 NSUserDefaults](~/ios/app-fundamentals/user-defaults.md)
*   [共享文件](~/ios/watchos/app-fundamentals/parent-app.md#files)

## <a name="configure-a-new-app-group"></a>配置新应用组

通过[应用组](https://developer.apple.com/library/content/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19)配置共享位置，应用组是在 [Apple 开发人员中心](https://developer.apple.com/account/)的“证书、标识符和配置文件”部分配置的。 每个项目的 Entitlements.plist 中还必须引用该值。

应用组会具有标识符（通常是组的捆绑 ID）。 前缀。 例如，捆绑 ID `com.xamarin.WatchSettings` 对应于应用组 `group.com.xamarin.WatchSettings`。

要创建新的应用组，请执行以下操作：

1.  访问 Apple 的  [iOS 开发人员中心](https://developer.apple.com/account/)，打开“帐户”并登录。
2.  选择“证书、ID 和配置文件”。
3.  在“标识符”下选择“应用组”，然后单击“+”按钮以创建新的组。
4.  输入新组的“名称”和“标识符”，并单击“继续”按钮： 
   
    ![添加应用组详细信息](app-groups-capabilities-images/image52.png)

5.  单击“注册”按钮创建组，然后单击“完成”按钮返回到已注册应用组的列表。

## <a name="configure-an-app-to-use-app-groups"></a>配置应用以使用应用组

创建应用组后，请配置应用 ID，以便应用可以使用此 ID。

请执行以下操作：

1.  请访问 Apple 的 [iOS 开发人员中心](https://developer.apple.com/account/)，并使用 Apple 开发人员帐户登录。
2.  从“程序资源”菜单中选择“证书、ID 和配置文件”。
3.  在“标识符”下选择“应用 ID”，然后单击“+”按钮以创建新的 ID。
4.  输入应用 ID 的名称，并赋予显式应用 ID。
5.  在“应用服务”下启用“应用组”，然后单击“继续”按钮：

    ![添加应用组应用服务](app-groups-capabilities-images/image53.png)

6.  验证设置，然后单击“注册”按钮，创建应用 ID。
7.  单击“完成”按钮，返回到已注册的应用 ID 列表。
8.  从列表中选择新创建的应用 ID，然后单击“编辑”按钮：

    ![从列表中选择应用 ID](app-groups-capabilities-images/image54.png)

9.  在“应用组”服务下，单击“编辑”按钮：

    ![从列表中选择应用 ID](app-groups-capabilities-images/image55.png)

10. 选择创建的上述应用组，然后单击“继续”按钮：

    ![添加应用组](app-groups-capabilities-images/image56.png)

11. 依次单击“分配”按钮和“完成”按钮，返回到已注册的应用 ID 列表。
12. 对将使用应用组的任何应用（或扩展）重复这些步骤。

## <a name="next-steps"></a>后续步骤
 
下表列出了可能需要采取的其他步骤：

* 在应用中使用框架命名空间。
* 将所需权利添加到应用。 有关所需权利以及添加方法的详细信息，请参阅[使用权利](~/ios/deploy-test/provisioning/entitlements.md)指南。
* 在应用的“iOS 捆绑包签名”中，确保“自定义权利”设置为“Entitlements.plist”。 对“调试”和“iOS 模拟器”生成来说，这不是默认设置。 __ 

如果遇到与应用服务有关的问题，请参考主要指南的[故障排除](~/ios/deploy-test/provisioning/capabilities/index.md)部分。
