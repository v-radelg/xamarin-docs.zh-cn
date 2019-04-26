---
title: 购买非易耗型产品在 Xamarin.iOS
description: 本文档介绍非易耗型产品在 Xamarin.iOS 中，是用户购买的功能，仍可无限期，而不考虑设备。
ms.prod: xamarin
ms.assetid: 635D9CA2-6BCA-53E1-7B10-968029AA3493
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 060403baf8ac28b9b160632a01471b9828735069
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61403191"
---
# <a name="purchasing-non-consumable-products-in-xamarinios"></a>购买非易耗型产品在 Xamarin.iOS

非易耗型产品是所有者的客户。 预期结果是用户始终必须访问它们，即使其设备已丢失/被盗或在购买一个新。 它们可用于书籍、 杂志期刊、 游戏级别、 照片筛选器、 pro 功能，等等。一旦用户已购买非易耗型产品，他们不必再次对其付费。 如果你的代码会意外地允许他们试一次，StoreKit 将显示一条消息，已购买。

## <a name="non-consumable-products-sample"></a>非易耗型产品示例

[InAppPurchaseSample 代码](https://developer.xamarin.com/samples/monotouch/StoreKit/)包含一个名为项目*NonConsumables*。 代码示例演示如何实现非易耗型产品，例如使用照片筛选器。 一旦您已购买一个筛选器则您可以将其反复地应用到照片。 永远不需要重新购买。   
   
   
   
 采购流程所示的屏幕截图-此系列**购买**按钮将变为功能激活按钮：   
   
   
   
 [![](purchasing-non-consumable-products-images/image34.png "在本系列的屏幕截图显示采购过程")](purchasing-non-consumable-products-images/image34.png#lightbox)   
   
   
   
 在购买过程等同于可使用产品中。主要区别是在购买应用程序代码中的跟踪方式。 在此示例中的购买按钮才可用，如果尚未购买产品，否则按钮会激活该功能本身。   
   
   
   

下图显示了类和要执行的非易耗型产品购买的应用商店服务器之间的交互：   
   
   
   
 [![](purchasing-non-consumable-products-images/image35.png "类和应用商店服务器执行非易耗型产品之间的交互购买")](purchasing-non-consumable-products-images/image35.png#lightbox)   
   
   
   
 可使用的示例中的主要区别是，购买过程完成之后用户界面将更新以防止重新购买。 在此示例中，更新用户界面执行成功的事务通知，以便**购买**按钮转换为一个按钮，将激活该功能本身。

## <a name="re-purchasing-non-consumable-products"></a>重新购买非易耗型产品

通常，你的代码应隐藏或产品已成功购买后，若要防止用户尝试再次购买该产品购买按钮重新调整其用途。 通过更改的示例应用程序执行此**购买**到按钮，可以处理的示例照片筛选器按钮。   
   
   
   
 在有些情况下，应用程序无法判断是否已购买非易耗型产品：

-  如果应用程序被删除并重新安装在设备上，购买的所有记录将都被删除 （除非 / 直到用户执行的备份还原）。 
-  如果用户具有应用程序安装在两个 （或多个） 设备上并进行购买时其中一个设备上。 其他设备将继续显示可供购买的产品。 
-  如果客户尝试重新购买非易耗型产品在这些情况下，应用商店将满足再次免费产品。 首先将显示用户界面来执行购买 （例如，将显示确认警报和 Apple ID 将为所需） 但是用户将看到一条消息提示，已购买产品。  
   
   
   
 在此方案中的代码路径是完全以常规方式购买相同，唯一的区别：

-  用户不会不会进行收费同样的产品。
-  `SKPaymentTransaction`对象传递给应用程序将具有`OriginalTransaction`指的在最初购买产品时生成的事务的属性。 
-  销售非易耗型产品的应用程序还必须实现的 StoreKit**还原**功能，可帮助用户检索现有购买。 
