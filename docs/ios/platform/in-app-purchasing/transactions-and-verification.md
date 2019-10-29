---
title: Xamarin 中的事务和验证
description: 本文档介绍如何允许在 Xamarin iOS 应用中还原过去的购买。 它还讨论了用于保护采购和服务器交付产品的方法。
ms.prod: xamarin
ms.assetid: 84EDD2B9-3FAA-B3C7-F5E8-C1E5645B7C77
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: d92de14dc42f7c20a1f25b6454623c7ad4441e8a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032289"
---
# <a name="transactions-and-verification-in-xamarinios"></a>Xamarin 中的事务和验证

## <a name="restoring-past-transactions"></a>还原过去的事务

如果你的应用程序支持可恢复的产品类型，则必须包含一些用户界面元素，以允许用户恢复这些购买。
此功能可让客户将产品添加到其他设备，或在擦除干净或删除并重新安装应用后，将产品恢复到同一设备。 以下产品类型是可恢复的：

- 不可耗用的产品
- 自动可续订订阅
- 免费订阅

还原过程应更新在设备上保留的记录以满足你的产品。 客户可在任何设备上随时选择还原。 还原过程会重新发送该用户以前的所有事务;然后，应用程序代码必须确定要对该信息执行的操作（例如，检查设备上是否已存在该信息的记录），如果没有，则创建采购记录并为用户启用该产品。

### <a name="implementing-restore"></a>执行还原

"用户界面**还原**" 按钮调用以下方法，该方法在 `SKPaymentQueue`上触发 RestoreCompletedTransactions。

```csharp
public void Restore()
{
   // theObserver will be notified of when the restored transactions start arriving <- AppStore
   SKPaymentQueue.DefaultQueue.RestoreCompletedTransactions();
}
```

StoreKit 会将还原请求异步发送到 Apple 的服务器。   
   
由于 `CustomPaymentObserver` 注册为事务观察程序，因此当 Apple 的服务器响应时，它将接收消息。 响应将包含此用户在该应用程序中执行的所有事务（跨其所有设备）。 代码循环遍历每个事务，检测还原状态并调用 `UpdatedTransactions` 方法来处理该状态，如下所示：

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

如果用户没有可恢复的产品，则不会调用 `UpdatedTransactions`。   
   
在示例中还原给定事务的最简单代码可能执行的操作与进行购买时相同，只不过 `OriginalTransaction` 属性用于访问产品 ID：

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

更复杂的实现可能会检查其他 `transaction.OriginalTransaction` 属性，如原始日期和接收号码。 此信息将对某些产品类型（如订阅）有用。

#### <a name="restore-completion"></a>还原完成

当还原过程完成（成功或失败）时，`CustomPaymentObserver` 有两个附加方法，StoreKit 将调用该方法，如下所示：

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

在此示例中，这些方法不执行任何操作，但实际的应用程序可能会选择向用户或其他一些功能实现消息。

## <a name="securing-purchases"></a>保护购买

本文档中的两个示例使用 `NSUserDefaults` 来跟踪购买：   
   
 **耗材**–信贷购买的 "平衡" 是一种简单的 `NSUserDefaults` 整数值，每次购买都会递增。   
   
 **非耗材**–每个照片筛选器购买都存储为 `NSUserDefaults`中的键值对。

使用 `NSUserDefaults` 可使示例代码简单简单，但不提供非常安全的解决方案，因为在技术上相投的用户可能会更新设置（绕过付款机制）。   
   
注意：实际应用程序应采用安全的机制来存储不受用户篡改限制的已购买内容。 这可能涉及加密和/或其他技术（包括远程服务器身份验证）。   
   
 该机制还应设计为利用 iOS、iTunes 和 iCloud 的内置备份和恢复功能。 这将确保在用户恢复备份后，其以前的购买将立即可用。   
   
有关更多 iOS 特定准则，请参阅 Apple 的安全编码指南。

## <a name="receipt-verification-and-server-delivered-products"></a>收据验证和服务器交付的产品

到目前为止，本文档中的示例仅包含与应用商店服务器直接通信的应用程序，以执行采购事务，这些事务可解锁已编码到应用程序中的功能或功能。   
   
Apple 通过允许其他服务器独立验证采购收据提供额外级别的购买安全性，这在作为购买的一部分交付数字内容之前验证请求（例如数字书籍或杂志）。   
   
 **内置产品**–如本文档中的示例，要购买的产品作为应用程序附带的功能存在。 应用内购买使用户能够访问功能。
产品 Id 进行硬编码。   
   
 **服务器交付的产品**-该产品包括存储在远程服务器上的可下载内容，直到成功的事务导致下载内容为止。
示例可能包括书籍或杂志问题。 产品 Id 通常源自外部服务器（产品内容也被托管）。 在事务完成时，应用程序必须实现一种可靠的记录方式，这样，如果内容下载失败，则可以重新尝试该操作，而不会给用户造成混淆。

### <a name="server-delivered-products"></a>服务器交付的产品

某些产品的内容（如书籍和杂志，甚至游戏级别）需要在购买过程中从远程服务器下载。 这意味着，在购买产品内容后需要额外的服务器来存储和交付产品内容。

#### <a name="getting-prices-for-server-delivered-products"></a>获取服务器交付产品的价格

由于产品是远程交付的，因此还可以添加更多产品（不更新应用程序代码），例如添加更多书籍或新的杂志问题。 为了使应用程序可以发现这些新闻产品并将其显示给用户，其他服务器应存储和传递此信息。   
   
[![](transactions-and-verification-images/image38.png "Getting Prices for Server-Delivered Products")](transactions-and-verification-images/image38.png#lightbox)   
   
1. 产品信息必须存储在多个位置：你的服务器上和 iTunes Connect。 此外，每个产品都具有与之关联的内容文件。 这些文件将在成功购买后交付。   
   
2. 当用户希望购买产品时，应用程序必须确定哪些产品可用。 此信息可以缓存，但应从存储主产品列表的远程服务器进行传递。   
   
3. 服务器返回要分析的应用程序的产品 Id 列表。   
   
4. 然后，应用程序确定要向 StoreKit 发送哪些产品 Id 以检索价格和说明。   
   
5. StoreKit 将产品 Id 列表发送到 Apple 的服务器。   
   
6. ITunes 服务器用有效的产品信息（说明和当前价格）进行响应。   
   
7. 应用程序的 `SKProductsRequestDelegate` 将向用户传递要显示的产品信息。

#### <a name="purchasing-server-delivered-products"></a>购买服务器交付的产品

由于远程服务器需要某种方式来验证内容请求是否有效（例如，已为支付了），因此会将回执信息传递到身份验证。 远程服务器会将该数据转发给 iTunes 进行验证，如果成功，则包括对应用程序的响应中的产品内容。   
   
 [![](transactions-and-verification-images/image39.png "Purchasing Server-Delivered Products")](transactions-and-verification-images/image39.png#lightbox)   
   
1. 应用程序将 `SKPayment` 添加到队列。 如果需要，将提示用户输入其 Apple ID，并要求确认付款。   
   
2. StoreKit 将请求发送到服务器进行处理。   
   
3. 事务完成后，服务器将使用事务回执进行响应。   
   
4. `SKPaymentTransactionObserver` 子类接收并处理接收。 由于产品必须从服务器下载，因此应用程序会向远程服务器发起网络请求。   
   
5. 下载请求附带收据数据，以便远程服务器可以验证它是否有权访问内容。 应用程序的网络客户端将等待对此请求的响应。   
   
6. 当服务器收到内容请求时，它会分析接收数据并将请求直接发送到 iTunes 服务器，以验证接收是否适用于有效的交易。 服务器应使用某些逻辑来确定是否将请求发送到生产或沙盒 URL。 如果接收状态21007（沙盒回执发送到生产服务器），Apple 建议始终使用生产 URL 并切换到沙盒。 有关更多详细信息，请参阅 Apple 的[收据验证编程指南](https://developer.apple.com/library/archive/releasenotes/General/ValidateAppStoreReceipt/Chapters/ValidateRemotely.html)。
   
7. iTunes 将检查回执并返回一个有效的状态。   
   
8. 服务器等待 iTunes 的响应。 如果它收到有效响应，则代码应查找要包括在应用程序响应中的关联产品内容文件。   
  
9. 应用程序接收并分析响应，并将产品内容保存到设备的文件系统。   
   
10. 应用程序启用产品，然后调用 StoreKit 的 `FinishTransaction`。 然后，应用程序可以选择显示购买的内容（例如，显示购买的书籍或杂志问题的第一页）。

非常大的产品内容文件的替代实现可能只需在步骤中存储事务收据 #9 以便可以快速完成事务，并为用户提供用户界面以下载实际产品内容稍后进行。 后续下载请求可以重新发送存储的收据以访问所需的产品内容文件。

### <a name="writing-server-side-receipt-verification-code"></a>正在编写服务器端回执验证代码

使用简单的 HTTP POST 请求/响应（包括工作流关系图中的 #8 #5 步骤）来验证服务器端代码中的回执。   
   
提取应用中的 `SKPaymentTansaction.TransactionReceipt` 属性。 这是需要发送到 iTunes 进行验证的数据（步骤 #5）。

Base64 编码事务接收数据（在步骤 #5 或 #6 中）。

创建一个简单的 JSON 有效负载，如下所示：

```csharp
{
   "receipt-data" : "(base-64 encoded receipt here)"
}
```

HTTP POST JSON 到[https://buy.itunes.apple.com/verifyReceipt](https://buy.itunes.apple.com/verifyReceipt)用于生产或[https://sandbox.itunes.apple.com/verifyReceipt](https://sandbox.itunes.apple.com/verifyReceipt)用于测试。   
   
 JSON 响应将包含以下项：

```csharp
{
   "status" : 0,
   "receipt" : { (receipt repeated here) }
}
```

状态为零表示有效回执。 你的服务器可以继续完成购买产品的内容。 接收密钥包含与应用收到的 `SKPaymentTransaction` 对象具有相同属性的 JSON 字典，因此服务器代码可以查询此字典以检索信息，如 product_id 和购买量。

有关其他信息，请参阅 Apple 的[收据验证编程指南](https://developer.apple.com/library/archive/releasenotes/General/ValidateAppStoreReceipt/Introduction.html)文档。
