---
title: 事务和在 Xamarin.iOS 中验证
description: 本文档介绍如何允许以往的购买情况，在 Xamarin.iOS 应用程序中的还原操作。 它还讨论了如何保护购买和服务器提供的产品。
ms.prod: xamarin
ms.assetid: 84EDD2B9-3FAA-B3C7-F5E8-C1E5645B7C77
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 83f5fd233c004271169a4d00d0a65e70aa925b95
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61369082"
---
# <a name="transactions-and-verification-in-xamarinios"></a>事务和在 Xamarin.iOS 中验证

## <a name="restoring-past-transactions"></a>还原过去的事务

如果应用程序支持可恢复的产品类型，则必须包括一些用户界面元素，以允许用户还原这些购买。
此功能允许客户将产品添加到其他设备或还原到同一设备被擦除清理后的产品或删除和重新安装该应用程序。 以下产品类型是可还原的已：

-  非易耗型产品
-  自动续订订阅
-  免费订阅

还原过程应更新的记录保留在设备以满足您的产品上。 客户可以选择在任何时候，任何其设备上还原。 还原过程重新发送该用户; 所有以前的事务然后，应用程序代码必须确定要使用该信息 （例如，检查是否已被记录在设备上的相应购买和不是，创建一条记录的购买和启用产品的用户） 采取的操作。

### <a name="implementing-restore"></a>实现还原

用户界面**还原**按钮调用以下方法，这会在触发 RestoreCompletedTransactions `SKPaymentQueue`。

```csharp
public void Restore()
{
   // theObserver will be notified of when the restored transactions start arriving <- AppStore
   SKPaymentQueue.DefaultQueue.RestoreCompletedTransactions();
}
```

StoreKit 将以异步方式将还原请求发送到 Apple 的服务器中。   
   
因为`CustomPaymentObserver`注册为事务观察者，它将接收消息，Apple 的服务器响应时。 响应将包含此用户在执行此应用程序中 （跨所有设备） 的所有事务。 该代码将遍历每个事务，检测到还原状态，并调用`UpdatedTransactions`方法来处理它，如下所示：

```csharp
// called when the transaction status is updated
public override void UpdatedTransactions (SKPaymentQueue queue, SKPaymentTransaction[] transactions)
{
   foreach (SKPaymentTransaction transaction in transactions)
   {
       switch (transaction.TransactionState)
       {
       case SKPaymentTransactionState.Purchased:
          theManager.CompleteTransaction(transaction);
           break;
       case SKPaymentTransactionState.Failed:
          theManager.FailedTransaction(transaction);
           break;
       case SKPaymentTransactionState.Restored:
           theManager.RestoreTransaction(transaction);
           break;
default:
           break;
       }
   }
}
```

如果没有为用户，可还原的已产品`UpdatedTransactions`不调用。   
   
最简单的可能代码要还原在此示例中给定的事务的作用与购买何时会发生，不同之处在于相同的操作`OriginalTransaction`属性用于访问产品 ID:

```csharp
public void RestoreTransaction (SKPaymentTransaction transaction)
{
   // Restored Transactions always have an 'original transaction' attached
   var productId = transaction.OriginalTransaction.Payment.ProductIdentifier;
   // Register the purchase, so it is remembered for next time
   PhotoFilterManager.Purchase(productId); // it's as though it was purchased again
   FinishTransaction(transaction, true);
}
```

更复杂的实现可能会检查其他`transaction.OriginalTransaction`属性，例如原始日期和回执编号。 此信息将可用于某些产品类型 （如订阅）。

#### <a name="restore-completion"></a>还原完成

`CustomPaymentObserver`有两个附加方法时还原过程已完成 （无论成功还是失败），将调用由 StoreKit 如下所示：

```csharp
public override void PaymentQueueRestoreCompletedTransactionsFinished (SKPaymentQueue queue)
{
   Console.WriteLine(" ** RESTORE Finished ");
}
public override void RestoreCompletedTransactionsFailedWithError (SKPaymentQueue queue, NSError error)
{
   Console.WriteLine(" ** RESTORE FailedWithError " + error.LocalizedDescription);
}
```

在示例中这些方法不执行任何操作，但实际的应用程序可以选择实现消息发送给用户或某些其他功能。

## <a name="securing-purchases"></a>保护购买

在这两个示例文档使用`NSUserDefaults`跟踪购买：   
   
 **消费品**– 的信用额度购买的平衡是一个简单`NSUserDefaults`会在每次购买时递增的整数值。   
   
 **非消费品**– 每个照片筛选器购买存储中的键 / 值对为`NSUserDefaults`。

使用`NSUserDefaults`会示例代码保持简单，但不提供非常安全的解决方案，因为它可能是从技术上讲意识的用户可以更新 （跳过的付款机制） 的设置。   
   
注意:实际应用程序应采用一种安全机制来存储购买不受影响用户篡改的内容。 这可能涉及加密和/或其他技术，包括远程服务器的身份验证。   
   
 该机制还应能够充分利用内置的备份和恢复功能的 iOS、 iTunes 和 iCloud。 这将确保用户还原备份后完成以前的购买项目将会立即可用。   
   
请参阅 Apple 的安全编码指南以获取更多特定于 iOS 的指导原则。

## <a name="receipt-verification-and-server-delivered-products"></a>收入验证和服务器提供的产品

到目前为止本文档中的示例都仅包括应用程序直接与应用商店服务器进行通信以执行购买交易，解锁功能或功能已编码到应用程序。   
   
Apple 提供一层额外的采购安全，从而采购接收要单独验证由另一个服务器，可用于交付数字内容作为一部分购买之前验证请求 (如数字书籍或杂志）。   
   
 **内置产品**– 如本文档中的示例，作为与应用程序一起提供的功能的存在于所购买的产品。 应用内购买使用户能够访问的功能。
产品 Id 进行了硬编码。   
   
 **服务器传递产品**– 产品包含的远程服务器存储，直到成功事务导致要下载的内容的可下载内容。
示例可能包括或介绍书籍或杂志期刊。 产品 Id 通常源自外部服务器 （其中也托管产品内容）。 应用程序必须实现的录制完成事务后，因此，如果内容下载失败可能会重新尝试同时不会混淆用户可靠的方式。

### <a name="server-delivered-products"></a>服务器提供的产品

如需在购买过程中从远程服务器下载书籍和杂志 （或甚至游戏的级别），某些产品的内容。 这意味着另一台服务器需要存储和交付产品的内容，它购买后。

#### <a name="getting-prices-for-server-delivered-products"></a>获取服务器传递产品的价格

由于远程发送产品，因此，它还有可能不断地添加更多产品 （而不更新应用程序代码），如添加更多书籍或杂志的新问题。 以便应用程序可以发现这些新闻产品，并显示给用户，应存储的其他服务器并将其发送此信息。   
   
[![](transactions-and-verification-images/image38.png "获取服务器传递产品的价格")](transactions-and-verification-images/image38.png#lightbox)   
   
1. 产品信息必须存储在多个位置： 在服务器上和在 iTunes Connect 中。 此外，每个产品将具有与之关联的内容文件。 这些文件将成功购买后发送。   
   
2. 当用户想要购买产品时，该应用程序必须确定哪种产品有可用。 此信息可能会进行缓存，但应传递产品的主列表存储在远程服务器。   
   
3. 服务器返回的应用程序分析的产品 Id 的列表。   
   
4. 然后，应用程序确定哪些这些产品 Id 将发送到 StoreKit 检索价格和说明。   
   
5. StoreKit 将产品 Id 的列表发送到 Apple 的服务器。   
   
6. 使用有效的产品信息 （说明和当前价格） 进行响应的 iTunes 服务器。   
   
7. 应用程序的`SKProductsRequestDelegate`传递给用户显示的产品信息。

#### <a name="purchasing-server-delivered-products"></a>购买服务器交付产品

由于远程服务器需要验证内容的请求有效的某种方式 (即。 已支付的)，回执信息都会同时传递身份验证。 远程服务器将转发到 iTunes 验证该数据，以及如果成功，到应用程序在响应中包含的产品内容。   
   
 [![](transactions-and-verification-images/image39.png "购买服务器交付产品")](transactions-and-verification-images/image39.png#lightbox)   
   
1. 该应用将添加`SKPayment`到队列。 如果所需的用户将系统提示你输入其 Apple ID 和系统要求确认付款。   
   
2. StoreKit 将请求发送到服务器以进行处理。   
   
3. 在事务完成后，则服务器响应事务回执。   
   
4. `SKPaymentTransactionObserver`子类接收该回执并对其进行处理。 因为必须从服务器下载该产品，该应用程序启动对远程服务器的网络请求。   
   
5. 下载请求附带了由接收数据，以便在远程服务器可以验证它有权访问的内容。 应用程序的网络客户端在等待对此请求的响应。   
   
6. 当服务器收到对内容的请求时，它解析出接收数据，并将发送直接向 iTunes 服务器请求，以验证该回执是有效的事务。 服务器应使用一些逻辑来确定是否将请求发送到的生产或沙盒的 URL。 Apple 建议始终使用生产 URL 并切换到沙盒，如果你收到状态 21007 （沙盒回执发送到生产服务器）。 请参阅 Apple 的[回执验证编程指南](https://developer.apple.com/library/archive/releasenotes/General/ValidateAppStoreReceipt/Chapters/ValidateRemotely.html)的更多详细信息。
   
7. iTunes 将检查接收并返回零状态是否有效。   
   
8. 服务器等待 iTunes 的响应。 如果收到有效响应，代码应查找要向应用程序在响应中包含的关联的产品内容文件。   
  
9. 应用程序接收并分析该响应，将产品内容保存到设备的文件系统。   
   
10. 应用程序使该产品，然后调用 StoreKit 的`FinishTransaction`。 应用程序然后可以选择显示已购买的内容 （例如，购买的书籍或杂志的显示第一页）。

非常大的产品内容文件的备用实现可能涉及到将只需在步骤 #9 中存储事务回执，以便可以快速完成事务，并提供用于用户下载实际产品内容的用户界面在稍后的某个时间。 后续的下载请求可以将存储的回执以便访问所需的产品内容文件重新发送。

### <a name="writing-server-side-receipt-verification-code"></a>编写服务器端接收验证码

验证在服务器端代码接收可以通过简单 HTTP POST 请求/响应包含步骤 #5 到工作流关系图中的 #8。   
   
提取`SKPaymentTansaction.TransactionReceipt`应用中的属性。 这是需要发送到 iTunes 进行验证 （步骤 5） 的数据。

Base64 编码的事务接收数据 （无论是在步骤 #5 或 6）。

创建简单的 JSON 负载如下：

```csharp
{
   "receipt-data" : "(base-64 encoded receipt here)"
}
```

HTTP POST 到 JSON [ https://buy.itunes.apple.com/verifyReceipt ](https://buy.itunes.apple.com/verifyReceipt)用于生产或[ https://sandbox.itunes.apple.com/verifyReceipt ](https://sandbox.itunes.apple.com/verifyReceipt)进行测试。   
   
 JSON 响应将包含以下项：

```csharp
{
   "status" : 0,
   "receipt" : { (receipt repeated here) }
}
```

零状态指示有效回执。 你的服务器可以继续满足已购买的产品的内容。 回执项包含与相同的属性的 JSON 字典`SKPaymentTransaction`已接收到应用程序中，因此服务器代码可以查询此字典来检索信息，如 product_id 和购买数量的对象。

请参阅 Apple[回执验证编程指南](https://developer.apple.com/library/archive/releasenotes/General/ValidateAppStoreReceipt/Introduction.html)文档，了解其他信息。
