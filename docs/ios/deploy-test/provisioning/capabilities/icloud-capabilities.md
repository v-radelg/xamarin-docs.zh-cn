---
title: Xamarin.iOS 中的 iCloud 功能
description: 向应用程序添加功能通常需要其他预配设置。 本指南将介绍 iCloud 功能所需的设置。
ms.prod: xamarin
ms.assetid: 3CBAC982-D8DE-48DD-97CD-32B551D9DB85
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/15/2017
ms.openlocfilehash: 3fd3ec0207ac3ae1e5fcaa7d11d91247cd73666d
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70762910"
---
# <a name="icloud-capabilities-in-xamarinios"></a>Xamarin.iOS 中的 iCloud 功能

_向应用程序添加功能通常需要其他预配设置。本指南将介绍 iCloud 功能所需的设置。_

iCloud 向 iOS 用户提供了一种方便简单的方式来存储内容并在设备之间共享内容。 开发人员可采用四种方法使用 iCloud 来为用户提供存储方式：键值存储、UIDocument 存储、CoreData 和使用 CloudKit 直接为单个文件和目录提供存储。 有关这些方面的详细信息，请参阅 [iCloud 简介](~/ios/data-cloud/introduction-to-icloud.md)指南。

由于容器  的原因，将 iCloud 功能添加到应用程序与其他应用服务相比难度略大。 容器在 iCloud 中用来存储应用的信息，并允许单个 iCloud 帐户中包含的所有信息被隔离，类似用户 iOS 设备上的沙盒。 有关容器的详细信息，请参阅 [CloudKit 简介](~/ios/data-cloud/intro-to-cloudkit.md)指南。

> [!IMPORTANT]
> Apple [提供工具](https://developer.apple.com/support/allowing-users-to-manage-data/)，用于帮助开发人员正确处理欧盟一般数据保护条例 (GDPR)。

<a name="icloud-developer-center" />

## <a name="developer-center"></a>开发人员中心

通过开发人员中心预配新应用时需采取两个步骤：

1. 创建容器。
2. 创建具有 iCloud 功能的应用 ID 并向其添加容器。
3. 创建包含此应用 ID 的预配配置文件

下述步骤可引导你完成这些步骤：

1. 浏览到 [Apple 开发人员中心](https://developer.apple.com/account/)，然后转到“证书、标识符和配置文件”部分： 
    
     ![Apple 开发人员中心主页](icloud-capabilities-images/image22.png)

2. 在“标识符”  下，选择“iCloud 容器”  ，然后选择“+”  来创建新的容器：  
    
    ![iCloud 容器屏幕](icloud-capabilities-images/image23.png)

3. 为 iCloud 容器输入**描述**和唯一**标识符**： 
    
    ![iCloud 容器注册屏幕](icloud-capabilities-images/image24.png)

4. 按“继续”  ，确保信息正确，然后按“注册”  ，创建 iCloud 容器：  
    
    ![iCloud 容器注册屏幕](icloud-capabilities-images/image25.png)

若要创建新的应用 ID 并向其添加容器，请执行以下操作：

1. 在[开发人员中心](https://developer.apple.com/account/)中的“标识符”  下，单击“应用 ID”  ： 
    
    ![开发人员中心中的标识符选择](icloud-capabilities-images/image26.png)

2. 选择“+”  按钮来添加新的应用 ID： 
    
    ![添加新的应用 ID 按钮](icloud-capabilities-images/image27.png)

3. 输入应用 ID 的**名称**，并赋予**显式应用 ID**：
    
    ![输入新的应用 ID 详细信息](icloud-capabilities-images/image28.png)

4. 在“应用服务”  下选择“iCloud”  ，然后选择“包含 CloudKit 支持”  ：
    
    ![选择 iCloud 应用服务](icloud-capabilities-images/image29.png)

5. 选择“继续”  ，然后选择“注册”  。 请注意，在确认屏幕上，iCloud 会以黄色符号显示已选择“可配置”的内容：   
    
    ![确认屏幕](icloud-capabilities-images/image30.png)

6. 返回应用 ID 列表，并选择刚才创建的应用 ID： 
    
    ![选择应用 ID 屏幕](icloud-capabilities-images/image31.png)

7. 向下滚动到此展开部分的底部，然后单击“编辑”  ：
    
    ![编辑应用 ID](icloud-capabilities-images/image32.png)

8. 在列表中向下滚动到 iCloud，然后单击“编辑”  按钮：  
    
    ![编辑 iCloud 应用 ID](icloud-capabilities-images/image33.png)

9. 选择要用于此应用 ID 的容器：  
    
    ![选择容器屏幕](icloud-capabilities-images/image34.png)

10. 确认容器分配，然后按“分配”  。

现在此应用 ID 可用于生成或再生成新的预配配置文件，正如[使用功能](~/ios/deploy-test/provisioning/capabilities/index.md)指南中所述。 

有关使用 iCloud 的详细信息，请参阅以下指南：

* [iCloud 简介](~/ios/data-cloud/introduction-to-icloud.md)
* [CloudKit 简介](~/ios/data-cloud/intro-to-cloudkit.md)
* [文档选取器简介](~/ios/platform/document-picker.md)

## <a name="next-steps"></a>后续步骤

下表列出了可能需要采取的其他步骤：

* 在应用中使用框架命名空间。
* 将所需权利添加到应用。 有关所需权利以及添加方法的详细信息，请参阅[使用权利](~/ios/deploy-test/provisioning/entitlements.md)指南。
* 在应用的“ **iOS 捆绑包签名**”中，确保“ **自定义权利**”设置为“**Entitlements.plist**”。 对“调试”和“iOS 模拟器”生成来说，这不是默认设置。 __  

如果遇到与应用服务有关的问题，请参考主要指南的[故障排除](~/ios/deploy-test/provisioning/capabilities/index.md)部分。
