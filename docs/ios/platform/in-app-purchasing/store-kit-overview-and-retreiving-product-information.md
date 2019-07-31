---
title: StoreKit 概述和检索 Xamarin 中的产品信息
description: 本文档提供 StoreKit 的概述。 它介绍了用于 StoreKit、测试 StoreKit 交互、显示产品以供销售、处理无效产品以及显示本地化价格的类。
ms.prod: xamarin
ms.assetid: FC21192E-6325-4389-C060-E92DBB5EBD87
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 4eb115889b65819e969b8024fc9fbcdc02b566fb
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68648210"
---
# <a name="storekit-overview-and-retrieving-product-info-in-xamarinios"></a>StoreKit 概述和检索 Xamarin 中的产品信息

下面的屏幕截图中显示了应用内购买的用户界面。
在发生任何交易之前, 应用程序必须检索产品的价格以及显示的说明。 然后, 当用户按 "**购买**" 时, 应用程序向 StoreKit 发出请求, 该请求管理确认对话框和 Apple ID 登录。 假设事务成功, StoreKit 将通知应用程序代码, 该代码必须存储事务结果, 并向用户提供对其购买的访问权限。   

   
 [![](store-kit-overview-and-retreiving-product-information-images/image14.png "StoreKit 通知应用程序代码, 该代码必须存储事务结果并向用户提供对其购买的访问权限")](store-kit-overview-and-retreiving-product-information-images/image14.png#lightbox)

## <a name="classes"></a>类

实现应用内购买需要 StoreKit 框架中的以下类:   
   
 **SKProductsRequest** –对已批准产品进行 StoreKit 的请求 (应用商店)。 可以使用多个产品 Id 进行配置。

-   **SKProductsRequestDelegate** –声明处理产品请求和响应的方法。 
-   **SKProductsResponse** -从 StoreKit (应用商店) 发送回委托。 包含与请求发送的产品 Id 相匹配的 SKProducts。 
-   **SKProduct** -从 StoreKit 检索的产品 (已在 iTunes Connect 中配置)。 包含有关产品的信息, 如产品 ID、标题、描述和价格。 
-   **SKPayment** –使用产品 ID 创建并添加到付款队列, 用于执行购买。 
-   **SKPaymentQueue** –要发送到 Apple 的排队支付请求数。 由于每个正在处理的付款都会触发通知。 
-   **SKPaymentTransaction** –表示已完成的事务 (已由应用商店处理并通过 StoreKit 发送回你的应用程序的采购请求)。 可以购买、还原或失败该事务。 
-   **SKPaymentTransactionObserver** –自定义子类, 用于响应 StoreKit 付款队列生成的事件。 
-   **StoreKit 操作是异步**的–启动 SKProductRequest 后, 将向队列中添加 SKPayment 后, 控件会返回到代码。 StoreKit 在从 Apple 服务器接收数据时, 将调用 SKProductsRequestDelegate 或 SKPaymentTransactionObserver 子类上的方法。 


下图显示了各种 StoreKit 类之间的关系 (必须在应用程序中实现抽象类):   
   
   
   
 [![](store-kit-overview-and-retreiving-product-information-images/image15.png "各种 StoreKit 类之间的关系必须在应用程序中实现")](store-kit-overview-and-retreiving-product-information-images/image15.png#lightbox)   
   
   
   
 本文档稍后将详细介绍这些类。

## <a name="testing"></a>正在测试

大多数 StoreKit 操作需要使用实际设备进行测试。 检索产品信息 (即 价格&amp;描述) 将在模拟器中运行, 但采购和还原操作将返回错误 (如 FailedTransaction 代码 = 5002 发生未知错误)。

注意:StoreKit 不在 iOS 模拟器中运行。 在 iOS 模拟器中运行应用程序时, 如果你的应用程序尝试检索付款队列, StoreKit 将记录一个警告。 必须在实际设备上测试应用商店。   
   
   
   
 重要提示：不要在 "设置" 应用程序中用测试帐户登录。 你可以使用 "设置" 应用程序来注销任何现有 Apple ID 帐户, 然后必须等待*在应用内购买顺序*中出现提示, 才能使用测试 Apple id 进行登录。   
   
   
   

如果尝试使用测试帐户登录到实际存储, 则它会自动转换为实际的 Apple ID。 该帐户将无法再用于测试。

若要测试 StoreKit 代码, 必须注销常规 iTunes 测试帐户, 并使用链接到测试存储的特殊测试帐户 (在 iTunes Connect 中创建) 登录。 若要注销当前帐户, 请访问 **> iTunes 和 App Store 的设置**, 如下所示:

 [![](store-kit-overview-and-retreiving-product-information-images/image16.png "若要从当前帐户注销, 请访问设置 iTunes 和 App Store")](store-kit-overview-and-retreiving-product-information-images/image16.png#lightbox)
 
然后, 在*你的应用中通过 StoreKit 请求时*使用测试帐户登录:



若要在 iTunes Connect 中创建测试用户, 请单击主页上的 "**用户和角色**"。

 [![](store-kit-overview-and-retreiving-product-information-images/image17.png "若要在 iTunes Connect 中创建测试用户, 请单击主页上的 \"用户和角色\"")](store-kit-overview-and-retreiving-product-information-images/image17.png#lightbox)

选择**沙盒测试人员**

 [![](store-kit-overview-and-retreiving-product-information-images/image18.png "选择沙盒测试人员")](store-kit-overview-and-retreiving-product-information-images/image18.png#lightbox)

随即显示现有用户的列表。 您可以添加新用户或删除现有记录。 门户不是 (目前) 允许你查看或编辑现有测试用户, 因此建议你记录每个已创建的测试用户的良好记录 (特别是所分配的密码)。 删除测试用户后, 电子邮件地址不能再用于另一个测试帐户。  
   
 [![](store-kit-overview-and-retreiving-product-information-images/image19.png "将显示现有用户的列表")](store-kit-overview-and-retreiving-product-information-images/image19.png#lightbox)   
   
 新的测试用户与真实的 Apple ID (如名称、密码、机密问题和答案) 具有相似的属性。 记录在此处输入的所有详细信息。 "**选择 ITunes 商店**" 字段将确定应用中购买的应用在以该用户身份登录时将使用的货币和语言。

 [![](store-kit-overview-and-retreiving-product-information-images/image20.png "\"选择 iTunes 商店\" 字段将确定用户在应用内购买时的货币和语言")](store-kit-overview-and-retreiving-product-information-images/image20.png#lightbox)

## <a name="retrieving-product-information"></a>检索产品信息

销售应用内购买产品的第一步是显示它: 从应用商店检索当前价格和说明以供显示。   
   
   
   
 无论应用销售哪种类型的产品 (可使用、不可用或一种类型的订阅), 检索产品信息以显示的过程都是相同的。 本文附带的 InAppPurchaseSample 代码包含一个名为*耗材*的项目, 该项目演示如何检索要显示的生产信息。 它演示了如何执行以下操作:

-  创建的`SKProductsRequestDelegate`实现并`ReceivedResponse`实现抽象方法。 示例代码将`InAppPurchaseManager`此类称为。 
-  请检查 StoreKit 以查看是否允许付款 (使用`SKPaymentQueue.CanMakePayments` )。 
-  `SKProductsRequest`使用 iTunes Connect 中定义的产品 id 实例化。 这是在示例的`InAppPurchaseManager.RequestProductData`方法中完成的。 
-  对调用 Start 方法`SKProductsRequest` 。 这会触发对 App Store 服务器的异步调用。 委托 ( `InAppPurchaseManager` ) 将被调用, 并返回结果。 
-  委托的 ( `InAppPurchaseManager` ) `ReceivedResponse`方法将 UI 与从应用商店返回的数据 (产品价格 & 说明或有关无效产品的消息) 进行更新。 

总体交互类似于此 ( **StoreKit**内置于 IOS,**应用商店**表示 Apple 的服务器):

 [![](store-kit-overview-and-retreiving-product-information-images/image21.png "检索产品信息图")](store-kit-overview-and-retreiving-product-information-images/image21.png#lightbox)

### <a name="displaying-product-information-example"></a>显示产品信息示例

[InAppPurchaseSample](https://docs.microsoft.com/samples/xamarin/ios-samples/storekit) *耗材*示例代码演示了如何检索产品信息。 该示例的主屏幕显示从应用商店检索到的两个产品的信息:   
   
   
   
 [![](store-kit-overview-and-retreiving-product-information-images/image23.png "主屏幕显示从应用商店检索到的信息产品")](store-kit-overview-and-retreiving-product-information-images/image23.png#lightbox)   
   
   
   
 下面更详细地介绍了用于检索和显示产品信息的示例代码。


#### <a name="viewcontroller-methods"></a>ViewController 方法

`ConsumableViewController`类将管理两个产品的价格的显示, 这些产品的产品 id 在类中硬编码。

```csharp
public static string Buy5ProductId = "com.xamarin.storekit.testing.consume5credits",
   Buy10ProductId = "com.xamarin.storekit.testing.consume10credits";
List<string> products;
InAppPurchaseManager iap;
public ConsumableViewController () : base()
{
   // two products for sale on this page
   products = new List<string>() {Buy5ProductId, Buy10ProductId};
   iap = new InAppPurchaseManager();
}
```

在类级别, 还应将用于设置`NSNotificationCenter`观察程序的 NSObject 声明为:

```csharp
NSObject priceObserver;
```

在 ViewWillAppear 方法中, 使用默认通知中心创建并分配观察者:

```csharp
priceObserver = NSNotificationCenter.DefaultCenter.AddObserver (
  InAppPurchaseManager.InAppPurchaseManagerProductsFetchedNotification,
(notification) => {
   // display code goes here, to handle the response from the App Store
}
```

   
   
 在`ViewWillAppear`方法的末尾, `RequestProductData`调用方法以启动 StoreKit 请求。 发出此请求后, StoreKit 将与 Apple 的服务器异步联系以获取信息, 并将其反馈给你的应用程序。 这是由`SKProductsRequestDelegate`下一部分中所`InAppPurchaseManager`述的子类 () 实现的。

```csharp
iap.RequestProductData(products);
```

显示价格和说明的代码只是从 SKProduct 检索信息, 并将其分配给 UIKit 控件 (请注意, 我们会显示`LocalizedTitle`和`LocalizedDescription` – StoreKit 自动解析正确的文本和价格用户的帐户设置)。 下面的代码属于前面创建的通知:

```csharp
priceObserver = NSNotificationCenter.DefaultCenter.AddObserver (
  InAppPurchaseManager.InAppPurchaseManagerProductsFetchedNotification,
(notification) => {
   // display code goes here, to handle the response from the App Store
   var info = notification.UserInfo;
   if (info.ContainsKey(NSBuy5ProductId)) {
       var product = (SKProduct) info.ObjectForKey(NSBuy5ProductId);
       buy5Button.Enabled = true;
       buy5Title.Text = product.LocalizedTitle;
       buy5Description.Text = product.LocalizedDescription;
       buy5Button.SetTitle("Buy " + product.Price, UIControlState.Normal); // price display should be localized
   }
}
```

最后, 此`ViewWillDisappear`方法应确保删除观察程序:

```csharp
NSNotificationCenter.DefaultCenter.RemoveObserver (priceObserver);
```

#### <a name="skproductrequestdelegate-inapppurchasemanager-methods"></a>SKProductRequestDelegate (InAppPurchaseManager) 方法

当应用程序希望检索产品价格和其他信息时, 将调用方法。`RequestProductData` 它将产品 id 的集合分析为正确的数据类型, 然后使用`SKProductsRequest`该信息创建一个。 调用 Start 方法会导致向 Apple 的服务器发出网络请求。 请求将异步运行, 并在成功`ReceivedResponse`完成后调用委托的方法。

```csharp
public void RequestProductData (List<string> productIds)
{
   var array = new NSString[productIds.Count];
   for (var i = 0; i < productIds.Count; i++) {
       array[i] = new NSString(productIds[i]);
   }
   NSSet productIdentifiers = NSSet.MakeNSObjectSet<NSString>(array);
   productsRequest = new SKProductsRequest(productIdentifiers);
   productsRequest.Delegate = this; // for SKProductsRequestDelegate.ReceivedResponse
   productsRequest.Start();
}
```

iOS 会根据应用程序运行时使用的预配配置文件自动将请求路由到应用商店的 "沙盒" 或 "生产" 版本-因此, 在开发或测试应用时, 请求将有权访问每个产品在 iTunes Connect 中配置 (甚至是尚未通过 Apple 提交或批准的配置)。 当应用程序在生产环境中时, StoreKit 请求将仅返回**已批准**产品的信息。   
   
   
   
 在`ReceivedResponse` Apple 的服务器响应数据后, 将调用重写的方法。 由于这是在后台调用的, 因此, 代码应分析出有效数据, 并使用通知将产品信息发送到针对该通知 "侦听" 的任何 ViewControllers。 下面显示了用于收集有效产品信息并发送通知的代码:

```csharp
public override void ReceivedResponse (SKProductsRequest request, SKProductsResponse response)
{
   SKProduct[] products = response.Products;
   NSDictionary userInfo = null;
   if (products.Length > 0) {
       NSObject[] productIdsArray = new NSObject[response.Products.Length];
       NSObject[] productsArray = new NSObject[response.Products.Length];
       for (int i = 0; i < response.Products.Length; i++) {
           productIdsArray[i] = new NSString(response.Products[i].ProductIdentifier);
           productsArray[i] = response.Products[i];
       }
       userInfo = NSDictionary.FromObjectsAndKeys (productsArray, productIdsArray);
   }
   NSNotificationCenter.DefaultCenter.PostNotificationName (InAppPurchaseManagerProductsFetchedNotification, this, userInfo);
}
```

虽然未在关系图中显示, `RequestFailed`但也应重写方法, 以便在无法访问应用商店服务器 (或发生其他错误) 时向用户提供一些反馈。 示例代码只是写入控制台, 但真实的应用程序可能会选择查询`error.Code`属性并实现自定义行为 (例如, 向用户发出警报)。

```csharp
public override void RequestFailed (SKRequest request, NSError error)
{
   Console.WriteLine (" ** InAppPurchaseManager RequestFailed() " + error.LocalizedDescription);
}
```

此屏幕截图显示了加载后的示例应用程序 (没有可用的产品信息时):

 [![](store-kit-overview-and-retreiving-product-information-images/image24.png "在未提供产品信息时立即加载示例应用")](store-kit-overview-and-retreiving-product-information-images/image24.png#lightbox)

## <a name="invalid-products"></a>无效产品

还`SKProductsRequest`可能返回无效产品 id 的列表。 通常由于下列原因之一而返回无效的产品:   
   
   
   
 **产品 ID 已错误键入**–只接受有效的产品 id。   
   
 **产品尚未获得批准**–在测试过程中, 所有已清除的产品都应由`SKProductsRequest`返回, 但在生产中, 只返回已批准的产品。   
   
 **应用 ID 不是显式的**–通配符应用 id (带有星号) 不允许应用内购买。   
   
 **预配配置文件不正确**–如果在预配门户中对应用程序配置进行更改 (例如启用应用内购买), 请记住在生成应用时重新生成并使用正确的预配配置文件。   
   
 **IOS 付费应用程序协定不到位**–除非有适用于 Apple 开发人员帐户的有效合同, 否则 StoreKit 功能将不起作用。   
   
 **二进制文件处于已拒绝状态**–如果以前提交的二进制文件处于拒绝状态 (由应用商店团队或开发人员提供), 则 StoreKit 功能将不起作用。

示例`ReceivedResponse`代码中的方法将无效产品输出到控制台:

```csharp
public override void ReceivedResponse (SKProductsRequest request, SKProductsResponse response)
{
   // code removed for clarity
   foreach (string invalidProductId in response.InvalidProducts) {
       Console.WriteLine("Invalid product id: " + invalidProductId );
   }
}
```

## <a name="displaying-localized-prices"></a>显示本地化价格

价格层为所有国际应用商店中的每个产品指定特定价格。 为了确保为每种货币正确显示价格, 请使用以下扩展方法 (在中`SKProductExtension.cs`定义) 而不是每个`SKProduct`的价格属性:

```csharp
public static class SKProductExtension {
   public static string LocalizedPrice (this SKProduct product)
   {
       var formatter = new NSNumberFormatter ();
       formatter.FormatterBehavior = NSNumberFormatterBehavior.Version_10_4;  
       formatter.NumberStyle = NSNumberFormatterStyle.Currency;
       formatter.Locale = product.PriceLocale;
       var formattedString = formatter.StringFromNumber(product.Price);
       return formattedString;
   }
}
```

设置按钮标题的代码使用如下所示的扩展方法:

```csharp
string Buy = "Buy {0}"; // or a localizable string
buy5Button.SetTitle(String.Format(Buy, product.LocalizedPrice()), UIControlState.Normal);
```

使用两个不同的 iTunes 测试帐户 (一个用于美国商店, 一个用于日本商店) 会生成以下屏幕截图:   
   
   
   
 [![](store-kit-overview-and-retreiving-product-information-images/image25.png "显示语言特定结果的两个不同的 iTunes 测试帐户")](store-kit-overview-and-retreiving-product-information-images/image25.png#lightbox)   
   
   
   
 请注意, 存储会影响用于产品信息和价格的语言, 而设备的语言设置会影响标签和其他本地化内容。   
   
   
   
 请记住, 若要使用其他存储测试帐户, 必须在**iTunes 和 App store > 设置**中**注销**, 然后重新启动应用程序以使用其他帐户登录。 若要更改设备的语言, 请转到 "**设置" > 常规 > 国际 > 语言**。

