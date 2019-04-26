---
title: 购买易耗型产品在 Xamarin.iOS
description: 本文档介绍在 Xamarin.iOS 易耗型产品。 易耗型产品是一次性部分功能，如游戏中货币。
ms.prod: xamarin
ms.assetid: E0CB4A0F-C3FA-3933-58A7-13246971D677
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: b55465a700974e0ce5ceb8893d96311d920e04ae
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61366497"
---
# <a name="purchasing-consumable-products-in-xamarinios"></a>购买易耗型产品在 Xamarin.iOS

易耗型产品是最易于实现，因为没有任何 restore 的要求。 它们可用于产品，如游戏中货币或一次性项功能。 用户可以重新再次购买易耗型产品转移，转移。

## <a name="built-in-product-delivery"></a>内置的产品交付

本文档附带的示例代码演示了内置产品 – 产品 Id 进行了硬编码到应用程序，因为它们紧密耦合到解除锁定该功能还款之后的代码。 采购流程可以如下可视化：   
   
[![采购流程可视化](purchasing-consumable-products-images/image26.png)](purchasing-consumable-products-images/image26.png#lightbox)     
   
 基本工作流是：   
   
 1. 该应用将添加`SKPayment`到队列。 如果所需的用户将系统提示你输入其 Apple ID 和系统要求确认付款。   
   
 2. StoreKit 将请求发送到服务器以进行处理。   
   
 3. 在事务完成后，则服务器响应事务回执。   
   
 4. `SKPaymentTransactionObserver`子类接收该回执并对其进行处理。   
   
 5. 应用程序使产品 (通过更新`NSUserDefaults`或其他机制)，然后调用 StoreKit 的`FinishTransaction`。

还有另一种类型的工作流 – *Server-Delivered 产品*–，它是文档的更高版本中所述 (请参阅部分*回执验证和 Server-Delivered 产品*)。

## <a name="consumable-products-example"></a>可使用的产品示例

[InAppPurchaseSample 代码](https://developer.xamarin.com/samples/monotouch/StoreKit/)包含一个名为项目*消费品*的实现的基本中的游戏 currency （称为"调整的信用额度"）。 此示例演示如何实现这两种应用内购买产品，以允许用户购买如许多"monkey 信用额度"根据需要 – 实际的应用程序也会花费它们的某种方式 ！   
   
   
   
 应用程序显示在这些屏幕截图-每次购买将更多"monkey 信用额度"添加到用户的平衡：   
   
   
   
 [![每次购买的用户余额添加更多的 monkey 信用额度](purchasing-consumable-products-images/image27.png)](purchasing-consumable-products-images/image27.png#lightbox)   
   
   
   
 自定义类，StoreKit 和应用商店之间的交互如下所示：   
   
   
   
 [![StoreKit 和应用商店的自定义类之间的交互](purchasing-consumable-products-images/image28.png)](purchasing-consumable-products-images/image28.png#lightbox)

&nbsp;

### <a name="viewcontroller-methods"></a>ViewController 方法

除了属性和所需以检索产品信息的方法中，视图控制器还需要其他通知观察者来侦听与购买相关通知。 这些只是`NSObjects`，将注册并从中移除`ViewWillAppear`和`ViewWillDisappear`分别。

```csharp
NSObject succeededObserver, failedObserver;
```

构造函数还将创建`SKProductsRequestDelegate`子类 ( `InAppPurchaseManager`)，进而创建并注册`SKPaymentTransactionObserver`( `CustomPaymentObserver`)。   
   
   
   
 处理的应用内购买事务的第一部分是处理按下按钮时用户想要购买某件事情，如以下代码示例应用程序中所示：

```csharp
buy5Button.TouchUpInside += (sender, e) => {
   iap.PurchaseProduct (Buy5ProductId);
};
buy10Button.TouchUpInside += (sender, e) => {
   iap.PurchaseProduct (Buy10ProductId);
};
```

   
   
 用户界面的第二部分处理通知，则事务成功，在这种情况下通过更新所显示的余额：

```csharp
priceObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerTransactionSucceededNotification,
(notification) => {
   balanceLabel.Text = CreditManager.Balance() + " monkey credits";
});
```

用户界面的最后一部分显示一条消息，如果出于某种原因取消事务。 在示例代码中的消息只需写入输出窗口：

```csharp
failedObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerTransactionFailedNotification,
(notification) => {
   Console.WriteLine ("Transaction Failed");
});
```

除了这些视图控制器上的方法，可使用产品购买事务也需要代码上`SKProductsRequestDelegate`和`SKPaymentTransactionObserver`。

### <a name="inapppurchasemanager-methods"></a>InAppPurchaseManager 方法

本示例代码实现的一些购买 InAppPurchaseManager 类上的相关的方法包括`PurchaseProduct`方法，可创建`SKPayment`实例，并将其添加到队列中等待处理：

```csharp
public void PurchaseProduct(string appStoreProductId)
{
   SKPayment payment = SKPayment.PaymentWithProduct (appStoreProductId);
   SKPaymentQueue.DefaultQueue.AddPayment (payment);
}
```

向队列添加付款是一个异步操作。 应用程序重新获得控制，而 StoreKit 处理事务，并将其发送到 Apple 的服务器。 它现在是该 iOS 将验证用户登录到应用商店，并且如果需要，则会提示她的 Apple ID 和密码。   
   
   
   
 假定用户已成功向 App Store 进行身份验证并同意对该事务，`SKPaymentTransactionObserver`将收到 StoreKit 的响应，且调用以下方法以完成该事务并完成它。

```csharp
public void CompleteTransaction (SKPaymentTransaction transaction)
{
   var productId = transaction.Payment.ProductIdentifier;
   // Register the purchase, so it is remembered for next time
   PhotoFilterManager.Purchase(productId);
   FinishTransaction(transaction, true);
}
```

最后一步是确保，通知 StoreKit 已成功完成事务，通过调用`FinishTransaction`:

```csharp
public void FinishTransaction(SKPaymentTransaction transaction, bool wasSuccessful)
{
   // remove the transaction from the payment queue.
   SKPaymentQueue.DefaultQueue.FinishTransaction(transaction);  // THIS IS IMPORTANT - LET'S APPLE KNOW WE'RE DONE !!!!
   using (var pool = new NSAutoreleasePool()) {
       NSDictionary userInfo = NSDictionary.FromObjectsAndKeys(new NSObject[] {transaction},new NSObject[] {new NSString("transaction")});
       if (wasSuccessful) {
           // send out a notification that we've finished the transaction
           NSNotificationCenter.DefaultCenter.PostNotificationName (InAppPurchaseManagerTransactionSucceededNotification, this, userInfo);
       } else {
           // send out a notification for the failed transaction
           NSNotificationCenter.DefaultCenter.PostNotificationName (InAppPurchaseManagerTransactionFailedNotification, this, userInfo);
       }
   }
}
```

一旦产品交付，`SKPaymentQueue.DefaultQueue.FinishTransaction`必须调用以从付款队列中删除该事务。

### <a name="skpaymenttransactionobserver-custompaymentobserver-methods"></a>SKPaymentTransactionObserver (CustomPaymentObserver) 方法

StoreKit 调用`UpdatedTransactions`方法时它接收来自 Apple 的服务器的响应并将传递一个数组`SKPaymentTransaction`为你的代码检查的对象。 该方法循环访问每个事务，并执行不同的功能 （如此处所示），可根据事务状态：

```csharp
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
           default:
               break;
       }
   }
}
```

`CompleteTransaction`方法已在本部分前面介绍 – 它将保存到的采购详细信息`NSUserDefaults`、 完成与 StoreKit 的事务，并最后通知用户界面更新。

### <a name="purchasing-multiple-products"></a>购买多个产品

若要购买多个产品在应用程序中，它会比较有利，使用`SKMutablePayment`类，然后设置数量字段：

```csharp
public void PurchaseProduct(string appStoreProductId)
{
   SKMutablePayment payment = SKMutablePayment.PaymentWithProduct (appStoreProductId);
   payment.Quantity = 4; // hardcoded as an example
   SKPaymentQueue.DefaultQueue.AddPayment (payment);
}
```

处理完成的事务的代码也必须查询正确完成购买的数量属性：

```csharp
public void CompleteTransaction (SKPaymentTransaction transaction)
{
   var productId = transaction.Payment.ProductIdentifier;
   var qty = transaction.Payment.Quantity;
   if (productId == ConsumableViewController.Buy5ProductId)
       CreditManager.Add(5 * qty);
   else if (productId == ConsumableViewController.Buy10ProductId)
       CreditManager.Add(10 * qty);
   else
       Console.WriteLine ("Shouldn't happen, there are only two products");
   FinishTransaction(transaction, true);
}
```

当用户购买了多个数量时，StoreKit 确认警报将反映出数量、 的单位价格和总价需付费，如以下屏幕截图中所示：

[![确认购买](purchasing-consumable-products-images/image30.png)](purchasing-consumable-products-images/image30.png#lightbox)

## <a name="handling-network-outages"></a>处理网络中断

应用内购买需要有效的网络连接为 StoreKit 与 Apple 的服务器进行通信。 如果网络连接不可用，然后应用内购买将不可用。

### <a name="product-requests"></a>请求产品

如果网络不可用时制定`SKProductRequest`，则`RequestFailed`方法`SKProductsRequestDelegate`子类 ( `InAppPurchaseManager`) 将调用，如下所示：

```csharp
public override void RequestFailed (SKRequest request, NSError error)
{
   using (var pool = new NSAutoreleasePool()) {
       NSDictionary userInfo = NSDictionary.FromObjectsAndKeys(new NSObject[] {error},new NSObject[] {new NSString("error")});
       // send out a notification for the failed transaction
       NSNotificationCenter.DefaultCenter.PostNotificationName (InAppPurchaseManagerRequestFailedNotification, this, userInfo);
   }
}
```

ViewController 然后侦听通知，并显示一条消息中的购买按钮：

```csharp
requestObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerRequestFailedNotification,
(notification) => {
   Console.WriteLine ("Request Failed");
   buy5Button.SetTitle ("Network down?", UIControlState.Disabled);
   buy10Button.SetTitle ("Network down?", UIControlState.Disabled);
});
```

因为网络连接可能是暂时的移动设备上，应用程序可能想要监视网络状态使用系统的配置框架，然后重试时网络连接不可用。 请参阅 Apple 的或，使用它。

### <a name="purchase-transactions"></a>购买交易

StoreKit 付款队列将存储并转发采购请求如果可能，因此没有网络中断的影响固定具体取决于网络在购买过程中失败时。   
   
   
   
 如果在事务期间会出现错误`SKPaymentTransactionObserver`子类 ( `CustomPaymentObserver`) 将具有`UpdatedTransactions`方法调用和`SKPaymentTransaction`类将处于失败状态。

```csharp
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
           default:
               break;
       }
   }
}
```

`FailedTransaction`方法检测到的错误是否因用户取消操作，而为如下所示：

```csharp
public void FailedTransaction (SKPaymentTransaction transaction)
{
   //SKErrorPaymentCancelled == 2
   if (transaction.Error.Code == 2) // user cancelled
       Console.WriteLine("User CANCELLED FailedTransaction Code=" + transaction.Error.Code + " " + transaction.Error.LocalizedDescription);
   else // error!
       Console.WriteLine("FailedTransaction Code=" + transaction.Error.Code + " " + transaction.Error.LocalizedDescription);
   FinishTransaction(transaction,false);
}
```

即使某个事务失败，`FinishTransaction`必须调用方法来从付款队列中删除事务：

```csharp
SKPaymentQueue.DefaultQueue.FinishTransaction(transaction);
```

示例代码并发送通知，以便将 ViewController 可以显示一条消息。 应用程序不应显示其他消息如果用户已取消该事务。 可能会出现其他错误代码包括：

```csharp
FailedTransaction Code=0 Cannot connect to iTunes Store
FailedTransaction Code=5002 An unknown error has occurred
FailedTransaction Code=5020 Forget Your Password?
Applications may detect and respond to specific error codes, or handle them in the same way.
```

## <a name="handling-restrictions"></a>处理限制

**设置 > 常规 > 限制**的 iOS 功能允许用户锁定其设备的某些功能。   
   
   
   
 您可以查询是否允许用户通过应用内购买`SKPaymentQueue.CanMakePayments`方法。 如果此方法返回 false 则用户无法访问应用内购买。 如果尝试进行购买，StoreKit 自动将向用户显示一条错误消息。 通过检查此值您的应用程序可以改为隐藏购买按钮，或采取其他措施来帮助用户。   
   
   
   
 在中`InAppPurchaseManager.cs`文件`CanMakePayments`方法包装 StoreKit 函数如下：

```csharp
public bool CanMakePayments()
{
   return SKPaymentQueue.CanMakePayments;
}
```

若要测试此方法，请使用**限制**iOS 若要禁用的功能**应用内购买**:   
   
   
   
 [![使用 iOS 的限制功能禁用应用内购买](purchasing-consumable-products-images/image31.png)](purchasing-consumable-products-images/image31.png#lightbox)   
   
   
   
 此示例代码`ConsumableViewController`应对`CanMakePayments`通过显示返回 false **AppStore 禁用**上禁用的按钮的文本。

```csharp
// only if we can make payments, request the prices
if (iap.CanMakePayments()) {
   // now go get prices, if we don't have them already
   if (!pricesLoaded)
       iap.RequestProductData(products); // async request via StoreKit -> App Store
} else {
   // can't make payments (purchases turned off in Settings?)
   // the buttons are disabled by default, and only enabled when prices are retrieved
   buy5Button.SetTitle ("AppStore disabled", UIControlState.Disabled);
   buy10Button.SetTitle ("AppStore disabled", UIControlState.Disabled);
}
```

应用程序看起来像在以下情况**应用内购买**功能是受限制 – 购买按钮处于禁用状态。   
   
   
   
 [![功能是应用内购买限制的购买按钮处于禁用状态时，应用程序如下所示](purchasing-consumable-products-images/image32.png)](purchasing-consumable-products-images/image32.png#lightbox)   
   
   
   

产品信息仍可以请求`CanMakePayments`为 false，因此应用程序仍可以检索和显示的价格。 这意味着，如果我们删除了`CanMakePayments`检查从代码购买按钮仍处于活动状态，但尝试进行购买时用户将看到一条消息，**不允许应用内购买**（由 StoreKit 生成当付款队列访问时）：   
   
   
   
 [![不允许应用内购买](purchasing-consumable-products-images/image33.png)](purchasing-consumable-products-images/image33.png#lightbox)   
   
   
   
 真实的应用程序可能采用不同的方法来处理此限制，如完全隐藏该按钮，并且可能产品/服务比 StoreKit 自动显示的警报的更多详细的消息。

