---
title: StoreKit 概述和在 Xamarin.iOS 中的检索产品信息
description: 本文档提供 StoreKit 的概述。 它描述了与 StoreKit，测试 StoreKit 交互、 显示销售的产品、 处理无效的产品和显示本地化的价格一起使用的类。
ms.prod: xamarin
ms.assetid: FC21192E-6325-4389-C060-E92DBB5EBD87
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 0dcda2e4fd1ca7773668a0a6fdf46e01f2f0841d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61366910"
---
# <a name="storekit-overview-and-retrieving-product-info-in-xamarinios"></a>StoreKit 概述和在 Xamarin.iOS 中的检索产品信息

在下面的屏幕截图中显示的应用内购买的用户界面。
发生任何事务之前，应用程序必须检索产品的价格和显示的说明。 然后当用户按**购买**，应用程序发出请求对 StoreKit 负责管理确认对话框和 Apple ID 登录。 假定该事务随后成功，StoreKit 通知应用程序代码中，这必须将事务结果存储并提供用户有权访问其购买的产品。   

   
 [![](store-kit-overview-and-retreiving-product-information-images/image14.png "StoreKit 通知应用程序代码，必须将事务结果存储，并提供用户有权访问其购买的产品")](store-kit-overview-and-retreiving-product-information-images/image14.png#lightbox)

## <a name="classes"></a>类

实现应用内购买需要 StoreKit framework 中的以下类：   
   
 **SKProductsRequest** – 对 StoreKit 的已批准的产品销售 （应用商店） 的请求。 可以配置其产品 Id 号。

-   **SKProductsRequestDelegate** – 声明处理产品请求和响应的方法。 
-   **SKProductsResponse** – 从 StoreKit （应用商店） 发回给该委托。 包含 SKProducts 相匹配的产品 Id 与请求一起发送。 
-   **SKProduct** – 产品检索从 StoreKit （即已在 iTunes Connect 中进行配置）。 包含有关该产品，如产品 ID、 标题、 描述和价格信息。 
-   **SKPayment** – 使用产品 ID 创建并添加到要执行购买的付款队列。 
-   **SKPaymentQueue** – 已排队付款请求发送到 Apple。 通知是由于正在处理每个付款而触发的。 
-   **SKPaymentTransaction** – 表示已完成的事务 （采购请求已处理的应用商店和发回给你的应用程序通过 StoreKit）。 事务可能已采购、 还原或已失败。 
-   **SKPaymentTransactionObserver** – 响应由 StoreKit 付款队列生成的事件的自定义子类。 
-   **StoreKit 操作是异步的**– 后启动 SKProductRequest 或 SKPayment 添加到队列，控制权返回给你的代码。 从 Apple 的服务器接收数据时，StoreKit 将 SKProductsRequestDelegate 或 SKPaymentTransactionObserver 子类上调用方法。 


下图显示了各种 StoreKit 类 （抽象类必须在应用程序中实现） 之间的关系：   
   
   
   
 [![](store-kit-overview-and-retreiving-product-information-images/image15.png "必须在应用程序中实现各种 StoreKit 类抽象类之间的关系")](store-kit-overview-and-retreiving-product-information-images/image15.png#lightbox)   
   
   
   
 在本文档后面的更详细地介绍了这些类。

## <a name="testing"></a>正在测试

大多数 StoreKit 操作需要真实的设备进行测试。 检索产品信息 （例如。 价格&amp;说明) 会在模拟器而购买和还原操作将返回一个错误 (如 FailedTransaction 代码 = 5002 出现未知错误)。

注意:在 iOS 模拟器不运行 StoreKit。 当 iOS 模拟器中运行应用程序，StoreKit 将记录一个警告，如果你的应用程序尝试检索付款队列。 必须在实际设备上完成测试在存储区。   
   
   
   
 重要提示：不使用你在设置应用程序中的测试帐户登录。 可以使用登录到设置应用程序无法使用任何现有的 Apple ID 帐户，则系统会提示你必须等到*在您的应用内购买序列*登录使用测试 Apple id。   
   
   
   

如果您尝试登录到具有一个测试帐户的实际存储区，它将自动转换为实际的 Apple id。 该帐户将不再可用于测试。

若要测试 StoreKit 代码使用特殊的测试帐户 （在 iTunes Connect 中创建） 链接到测试存储区都必须注销的正则 iTunes 测试帐户和登录名。 若要注销当前帐户访问**设置 > iTunes 和 App Store**如下所示：

 [![](store-kit-overview-and-retreiving-product-information-images/image16.png "若要注销当前帐户，请访问设置 iTunes 和 App Store")](store-kit-overview-and-retreiving-product-information-images/image16.png#lightbox)
 
然后使用测试帐户登录*StoreKit 的请求中的应用内*:



若要创建在 iTunes Connect 中的测试用户，请单击**用户和角色**在主页上。

 [![](store-kit-overview-and-retreiving-product-information-images/image17.png "若要创建测试用户在 iTunes Connect 单击用户和角色主页上")](store-kit-overview-and-retreiving-product-information-images/image17.png#lightbox)

选择**沙盒测试人员**

 [![](store-kit-overview-and-retreiving-product-information-images/image18.png "选择沙盒测试人员")](store-kit-overview-and-retreiving-product-information-images/image18.png#lightbox)

将显示现有用户的列表。 您可以将新用户添加或删除现有记录。 在门户不 （目前） 可以查看或编辑现有测试用户，因此建议您保持良好的 （尤其是分配的密码） 创建每个测试用户的记录。 一旦你删除的测试用户的电子邮件地址不能为重新用于另一个测试帐户。  
   
 [![](store-kit-overview-and-retreiving-product-information-images/image19.png "显示的现有用户列表")](store-kit-overview-and-retreiving-product-information-images/image19.png#lightbox)   
   
 新的测试用户具有类似属性到实际的 Apple ID （例如名称、 密码、 机密问题和答案）。 记录在此处输入的所有详细信息。 **选择 iTunes 应用商店**字段将确定哪些货币和语言在应用内购买时将使用登录以该用户。

 [![](store-kit-overview-and-retreiving-product-information-images/image20.png "选择 iTunes 应用商店字段将确定用户的货币和语言，用于其应用内购买")](store-kit-overview-and-retreiving-product-information-images/image20.png#lightbox)

## <a name="retrieving-product-information"></a>检索产品信息

销售应用内购买产品的第一步显示它： 从应用商店为显示检索当前价格和描述。   
   
   
   
 无论哪种类型的产品应用销售 （可使用，非易耗型或类型），检索显示的产品信息的过程是订阅的相同的。 本文附带的 InAppPurchaseSample 代码包含一个名为项目*消费品*演示如何检索生产显示的信息。 它显示了如何：

-  创建的实现`SKProductsRequestDelegate`并实现`ReceivedResponse`抽象方法。 示例代码将其称为`InAppPurchaseManager`类。 
-  请与 StoreKit 以查看是否有权付款 (使用`SKPaymentQueue.CanMakePayments`)。 
-  实例化`SKProductsRequest`具有产品 Id 已在 iTunes Connect 中定义的。 这是在该示例的`InAppPurchaseManager.RequestProductData`方法。 
-  在调用 Start 方法`SKProductsRequest`。 这会触发对应用商店服务器的异步调用。 委托 ( `InAppPurchaseManager` ) 将调用后的结果。 
-  委托的 ( `InAppPurchaseManager` )`ReceivedResponse`方法会从应用商店 （产品价格和说明或无效的产品有关的消息） 返回的数据更新 UI。 

整体的交互如下所示 ( **StoreKit**已内置到 iOS 和**App Store**表示 Apple 的服务器):

 [![](store-kit-overview-and-retreiving-product-information-images/image21.png "检索产品信息图")](store-kit-overview-and-retreiving-product-information-images/image21.png#lightbox)

### <a name="displaying-product-information-example"></a>显示产品信息示例

[InAppPurchaseSample](https://developer.xamarin.com/samples/monotouch/StoreKit/) *消费品*示例代码演示如何检索产品信息。 该示例的主屏幕将显示从应用存储中检索这两种产品的信息：   
   
   
   
 [![](store-kit-overview-and-retreiving-product-information-images/image23.png "主屏幕将显示从应用商店中检索信息产品")](store-kit-overview-and-retreiving-product-information-images/image23.png#lightbox)   
   
   
   
 下面更详细地介绍示例代码检索并显示产品信息。


#### <a name="viewcontroller-methods"></a>ViewController 方法

`ConsumableViewController`类将管理其产品 Id 是在类中的硬编码的两个产品的价格的显示。

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

在类级别还应存在 NSObject 声明值将用于安装程序`NSNotificationCenter`观察者：

```csharp
NSObject priceObserver;
```

ViewWillAppear 方法中观察程序创建并分配使用默认通知中心：

```csharp
priceObserver = NSNotificationCenter.DefaultCenter.AddObserver (
  InAppPurchaseManager.InAppPurchaseManagerProductsFetchedNotification,
(notification) => {
   // display code goes here, to handle the response from the App Store
}
```

   
   
 在末尾`ViewWillAppear`方法中，调用`RequestProductData`方法来启动 StoreKit 请求。 一旦建立了此请求，StoreKit 将以异步方式与 Apple 的服务器，若要获取的信息，并将其返回到您的应用程序。 这通过实现`SKProductsRequestDelegate`子类 ( `InAppPurchaseManager`) 的下一节中所述。

```csharp
iap.RequestProductData(products);
```

若要显示的价格和说明的代码只需从 SKProduct 检索信息，并将其分配给 UIKit 控件 (请注意，我们将显示`LocalizedTitle`和`LocalizedDescription`– StoreKit 自动解析正确的文本和基于的价格用户的帐户设置）。 以下代码放入我们在上面创建的通知：

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

最后，`ViewWillDisappear`方法应确保删除观察程序：

```csharp
NSNotificationCenter.DefaultCenter.RemoveObserver (priceObserver);
```

#### <a name="skproductrequestdelegate-inapppurchasemanager-methods"></a>SKProductRequestDelegate (InAppPurchaseManager) 方法

`RequestProductData`方法调用时应用程序希望检索产品价格和其他信息。 它解析为正确的数据类型的产品 Id 的集合，然后创建`SKProductsRequest`包含该信息。 调用 Start 方法会导致对 Apple 的服务器所做的网络请求。 请求将以异步方式运行，并调用`ReceivedResponse`方法的委托时它已成功完成。

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

iOS 将自动将请求路由到应用商店，具体取决于该应用程序正在使用 – 哪些预配配置文件的沙盒或生产版本使请求在开发或测试您的应用程序时将有权访问每个产品在 iTunes Connect （甚至那些已提交或由 Apple 批准） 中配置。 在生产环境中你的应用程序时，StoreKit 请求只返回的信息**Approved**产品。   
   
   
   
 `ReceivedResponse` Apple 服务器做出响应数据后调用重写的方法。 由于这在后台调用，该代码应中分析出有效的数据并使用一条通知将发送到任何 ViewControllers 正在侦听的该通知的产品信息。 若要收集有效的产品信息并发送通知的代码如下所示：

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

尽管没有显示在图中，但是`RequestFailed`应还重写方法，以便在应用商店服务器不可访问 （或发生某些其他错误） 的情况下，可以向用户提供一些反馈意见。 示例代码只是将写入到控制台中，但实际的应用程序可能会选择到查询`error.Code`属性和实现自定义行为 （如向用户警报）。

```csharp
public override void RequestFailed (SKRequest request, NSError error)
{
   Console.WriteLine (" ** InAppPurchaseManager RequestFailed() " + error.LocalizedDescription);
}
```

此屏幕截图显示了示例应用程序后立即加载 （当没有产品信息可用时）：

 [![](store-kit-overview-and-retreiving-product-information-images/image24.png "示例应用程序后立即加载任何产品信息不可用")](store-kit-overview-and-retreiving-product-information-images/image24.png#lightbox)

## <a name="invalid-products"></a>无效的产品

`SKProductsRequest`还可能会返回无效的产品 Id 的列表。 无效的产品通常返回由于以下项之一：   
   
   
   
 **产品 ID 已正确**– 接受仅有效的产品 Id。   
   
 **产品尚未批准**– 测试时，应由返回的所有产品的销售清除`SKProductsRequest`; 不过，在生产环境中都返回已批准的产品。   
   
 **应用程序 ID 不是显式**– 通配符应用 Id （带星号） 不允许应用内购买。   
   
 **预配配置文件不正确**– 如果进行重新生成和生成应用时使用正确的预配配置文件，请务必在预配门户中 （例如，启用应用内购买），对应用程序配置更改。   
   
 **iOS 付费应用程序协定不是就地**– StoreKit 功能才会生效根本没有 Apple 开发人员帐户的有效的协定。   
   
 **二进制文件是否处于已拒绝状态**– StoreKit 功能将无法工作，如果处于已拒绝状态 （不管是通过 App Store 团队，还是由开发人员） 以前提交的二进制文件。

`ReceivedResponse`方法中的示例代码输出到控制台无效的产品：

```csharp
public override void ReceivedResponse (SKProductsRequest request, SKProductsResponse response)
{
   // code removed for clarity
   foreach (string invalidProductId in response.InvalidProducts) {
       Console.WriteLine("Invalid product id: " + invalidProductId );
   }
}
```

## <a name="displaying-localized-prices"></a>显示本地化的价格

价格层指定所有国际应用程序商店的每个产品的特定价格。 若要确保每个货币正确显示的价格，请使用以下扩展方法 (在中定义`SKProductExtension.cs`) 而不是每个价格属性`SKProduct`:

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

设置按钮的标题的代码使用扩展方法如下：

```csharp
string Buy = "Buy {0}"; // or a localizable string
buy5Button.SetTitle(String.Format(Buy, product.LocalizedPrice()), UIControlState.Normal);
```

使用两个不同的 iTunes 测试帐户 （一个用于美国应用商店），另一个用于日语存储会导致下面的屏幕截图：   
   
   
   
 [![](store-kit-overview-and-retreiving-product-information-images/image25.png "两个不同的 iTunes 测试帐户显示特定结果的语言")](store-kit-overview-and-retreiving-product-information-images/image25.png#lightbox)   
   
   
   
 请注意，在存储区会影响设备的语言设置会影响标签和其他本地化的内容时用于产品信息和价格货币语言。   
   
   
   
 回想一下，若要使用不同的存储测试帐户，必须**注销**中**设置 > iTunes 和 App Store** ，然后重新启动在使用不同的帐户登录到应用程序。 若要更改设备的语言，请转到**设置 > 常规 > 国际 > 语言**。

