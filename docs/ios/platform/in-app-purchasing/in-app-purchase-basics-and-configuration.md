---
title: 应用内购买基本知识和配置 Xamarin.iOS
description: 本文档介绍了在 Xamarin.iOS 中，讨论有关规则、 配置和 iTunes Connect 的相关信息的应用内购买。
ms.prod: xamarin
ms.assetid: 11FB7F02-41B3-2B34-5A4F-69F12897FE10
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 267dac5b6aec263f1d8b69d81f34f732118c1802
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57671976"
---
# <a name="in-app-purchase-basics-and-configuration-in-xamarinios"></a>应用内购买基本知识和配置 Xamarin.iOS

实现应用内购买需要应用程序以使用设备上的 StoreKit API。 StoreKit 管理与 Apple 的 iTunes 服务器以获取产品信息和执行事务的所有通信。 必须配置预配配置文件应用内购买并且必须在 iTunes Connect 中输入产品信息。

 [![](in-app-purchase-basics-and-configuration-images/image1.png "StoreKit 管理与 Apple 的所有通信，在此图中所示")](in-app-purchase-basics-and-configuration-images/image1.png#lightbox)

使用应用商店提供应用内购买，需要以下安装和配置：

-  **在 iTunes Connect** – 配置要销售的产品和设置沙盒用户帐户来测试购买。 您必须还提供你的银行和税务信息到 Apple 以便它们可以汇出收集代表你的资金。
-   **iOS 设置门户**– 创建捆绑包标识符并启用应用商店应用的访问权限。
-  **存储工具包**– 将代码添加到您的应用程序，用于显示产品、 购买产品和还原的事务。
-  **自定义代码**– 若要将跟踪通过客户的购买并提供已购买的服务或产品。 您可能还需要实现服务器端进程，以验证回执，如果您的产品包含的服务器 （例如书籍和杂志期刊） 从下载的内容。


有两个存储工具包"服务器环境":

-  **生产**– 具有真实的货币交易的事务。 仅可通过已提交并批准由 Apple 的应用程序访问。 应用内购买产品还必须将审核和批准之前它们可在生产环境。
-  **沙盒**– 你测试时。 产品位于以下位置 （审批过程仅适用于生产环境） 创建后立即。 在沙盒中的事务需要测试用户 (不是实际的 Apple Id) 来执行事务。

## <a name="in-app-purchase-rules"></a>应用内购买规则

不能接受其他形式的数字产品或服务的付款到你的应用，也不提一下或指用户从应用内。 这意味着您不能接受信用卡或 PayPal 应用内购买时最合适的付款机制。 购买应用外的数字产品的特殊情况，但对于使用在应用中，例如购买书籍在网站上所带来的特定于"登录"，并使用在应用中的"登录"，允许用户访问权限的已购买的书籍。
运行这种方式的应用程序不能说或链接到外部购买功能 – 开发人员必须进行通信，向其用户以其他方式 （也许是通过电子邮件市场营销或某些其他直接渠道） 此功能。

但是，由于不能使用应用内购买为物理商品上，，允许您的情况下使用备用的付款机制 （例如。 信用卡、 PayPal） 从应用中。

Apple 必须审批每个产品之前就上出售 – 名称、 说明和产品的屏幕截图是所必需的审阅。 产品评审时间是应用程序评论的一样。

不能为您的产品选择任何价格 – 你可能只选择价格层中每个国家/地区/货币的 Apple 支持具有特定值。 在不同的市场中，不能具有不同的价格层。

## <a name="configuration"></a>配置

在编写任何应用程序内购买代码之前必须执行一些设置工作在 iTunes Connect 中的 ( [itunesconnect.apple.com](http://itunesconnect.apple.com)) 和 iOS 设置门户 ( [developer.apple.com/iOS](https://developer.apple.com/iOS))。

在编写任何代码之前，应完成以下三个步骤：

-  **Apple 开发人员帐户**– 提交到 Apple 银行和税务信息。
-  **iOS 设置门户**– 请确保你的应用具有有效的应用程序 ID (不带星号是通配符 * 中它) 并启用了应用购买中。
-  **iTunes Connect 应用程序管理**– 将产品添加到你的应用程序。


### <a name="apple-developer-account"></a>Apple 开发人员帐户

构建和分发免费的应用程序需要在配置很少[iTunes Connect](https://itunesconnect.apple.com)，但要销售付费应用或应用内购买，你需要向 Apple 提供银行和税务信息。 单击**协议、 税款和银行**从主菜单如下所示：

 [![](in-app-purchase-basics-and-configuration-images/image2.png "协议、 税款和银行从主菜单上单击")](in-app-purchase-basics-and-configuration-images/image2.png#lightbox)

开发人员帐户应有**iOS 付费应用程序**协定实际上，此屏幕截图中所示：

 [![](in-app-purchase-basics-and-configuration-images/image3.png "你的开发人员帐户应具有付费应用程序协定有效的 iOS")](in-app-purchase-basics-and-configuration-images/image3.png#lightbox)

你将不能测试任何 StoreKit 功能，直到有**iOS 付费应用程序**协定 – 在代码中的 StoreKit 调用将会失败直到处理 Apple 你**协定、 税款和银行**信息。

### <a name="ios-provisioning-portal"></a>iOS 预配门户

新应用程序设置**应用 Id**一部分**iOS 设置门户**。 若要创建新的应用 ID，请转到[Member Center 的 iOS 设置门户](https://developer.apple.com/membercenter/index.action)，导航到**证书、 标识符和配置文件**部分中的门户，并单击**标识符**下*iOS 应用*。 然后，单击"+"的顶部右侧以生成新的应用程序 id。


用于创建新的窗体**应用 Id**

 如下所示：

 [![](in-app-purchase-basics-and-configuration-images/image4.png "用于创建新应用 Id 的窗体")](in-app-purchase-basics-and-configuration-images/image4.png#lightbox)

输入内容适合*说明*，因此可以轻松地识别此应用 ID 列表中。 有关*应用 ID 前缀*，选择团队 id。

#### <a name="bundle-identifierapp-id-suffix-format"></a>捆绑包标识符/应用 ID 后缀格式

可以使用您喜欢的任何字符串你**捆绑包标识符**（只要它是在你的帐户中唯一），但 Apple 建议遵循反向 DNS 格式，而不是使用任意字符串。 本文附带的示例应用程序使用 com.xamarin.storekit.testing 捆绑包标识符，但是同样有效使用标识符，例如 my_store_example （即使 Apple 不推荐这么做）。

> [!IMPORTANT]
> Apple 还允许通配符星号，若要添加到末尾**捆绑包标识符**，以便单个应用 ID 可用于多个应用程序，但是_通配符应用 Id 不能用于 AppPurchase_. 通配符捆绑包标识符可能 com.xamarin.* 示例

#### <a name="enabling-app-services"></a>启用应用服务

请注意，**应用内购买**将在服务列表中自动启用：

 [![](in-app-purchase-basics-and-configuration-images/image5.png "在服务列表中自动启用应用内购买")](in-app-purchase-basics-and-configuration-images/image5.png#lightbox)

#### <a name="provisioning-profiles"></a>预配配置文件

通常情况下，选择已设置为应用内购买的应用程序 ID 创建开发和生产预配配置文件。 请参阅[iOS 设备预配](~/ios/get-started/installation/device-provisioning/index.md)并[发布到应用商店](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)指南的详细信息。

## <a name="itunes-connect"></a>在 iTunes Connect

单击**我的应用**在 iTunes Connect 若要创建或编辑的 iOS 应用程序项中。 应用程序概述页如下所示：

 [![](in-app-purchase-basics-and-configuration-images/image6.png "应用程序概述页")](in-app-purchase-basics-and-configuration-images/image6.png#lightbox)

单击**应用内购买**创建或编辑你销售的产品。 此屏幕截图显示了示例应用已添加的多个产品：

 [![](in-app-purchase-basics-and-configuration-images/image7.png "具有多个产品已添加了示例应用程序")](in-app-purchase-basics-and-configuration-images/image7.png#lightbox)

添加新产品的过程具有两个步骤：

1.   选择产品类型：[![](in-app-purchase-basics-and-configuration-images/image8.png "选择产品类型")](in-app-purchase-basics-and-configuration-images/image8.png#lightbox) 
2.   输入产品的属性，包括产品 Id、 定价层和本地化的说明：[![](in-app-purchase-basics-and-configuration-images/image9.png "输入的产品属性")](in-app-purchase-basics-and-configuration-images/image9.png#lightbox)

每个应用内购买产品所需的字段如下所述：


### <a name="reference-name"></a>引用名称

引用名称不显示给用户;它是供内部使用，并仅出现在 iTunes Connect。

### <a name="product-id-format"></a>产品 ID 格式

产品标识符只能包含字母数字 (A-Z、 a-z、 0-9)、 下划线 (_) 和句点 （.） 字符。 虽然你标识符，可以使用任何字符串，Apple 建议的反向 DNS 格式。 例如，示例应用程序使用此捆绑包标识符：

 `com.xamarin.storekit.testing`

因此若要识别应用内购买产品的约定将如下所示：

```csharp
com.xamarin.storekit.testing.consume5credits
com.xamarin.storekit.testing.consume10credits
com.xamarin.storekit.testing.sepia
com.xamarin.storekit.testing.greyscale
```

不强制使用此命名约定，只需一个建议，帮助您管理您的产品。 此外，产品标识符都遵循相同的反向 DNS 约定，尽管*无关*到捆绑包标识符并不需要的相同字符串开头。 它仍可以有效地使用标识符如 photo_product_greyscale （即使 Apple 不推荐这么做）。

产品 ID 不会显示给用户，但它用于引用应用程序代码中的产品。

### <a name="product-type"></a>产品类型

有五种类型的应用内购买产品可提供：

1.  **可使用**– 的使用了，如游戏中播放机可以花费的货币。 如果用户不备份/还原或否则刷新其设备，可使用的事务不会不获取还原 （这会有效地将播放机再次重新一样的优势）。 应用程序代码必须确保提供可使用 item，一旦完成该事务。
1.  **非易耗型**– 用户拥有一次购买，例如数字杂志问题或游戏级别的产品。
1.  **自动续订订阅**– 只需像现实世界的杂志订阅，在订阅期结束 Apple 自动再次费用客户和永久或客户之前显式扩展订阅期，取消它。 这是 Newsstand 应用的首选的付款方式 （实际上，应用程序必须支持此付款方法将被批准为 Newsstand 分发）。
1.  **免费订阅**– 仅可以在 Newsstand 启用应用程序中提供，并允许其设备上的客户访问订阅内容。 免费订阅永不过期。
1.  **非续订订阅**– 应该用于销售的限时访问静态内容，例如照片存档到一个月的访问。


 *本文档目前包括仅前两个产品类型 （可使用和非易耗型）。*

 <a name="Price_Tiers" />

### <a name="price-tiers"></a>价格层

应用商店不允许您选择您的产品可任意价格 – Apple 提供了可供选择的固定的价格层。 价格固定的每个货币和 Apple 保留相对价格调整 （例如，在特定货币和美元之间的相对外汇速率持续更改） 的权利。

Apple 提供了价格矩阵来帮助你选择正确的层所需的货币/价格。 如下所示价格矩阵 (2012 年 8 月) 的一段摘录：

 [![](in-app-purchase-basics-and-configuration-images/image10.png "价格矩阵 2012 年 8 月的一段摘录")](in-app-purchase-basics-and-configuration-images/image10.png#lightbox)

在撰写本文时 (2013 年 6 月)，有从 USD 87 层 999.99 美元到 0.99。 定价矩阵显示的价格，你的客户将支付和也的量你将收到来自 Apple – 这是更小其 30%的费用以及本地的任何税款需收集 （请注意，在美国和加拿大的卖家接收 99 c p 70 c 的示例产品，而澳大利亚卖方获得仅 63 c 由于商品&amp;服务税征收的销售价格)。

可以在任意时间，包括在将来某个日期生效的计划的价格更改更新您的产品的定价。 此屏幕截图显示如何添加未来日期的价格变化 – 价格将暂时更改从第 1 层为第 3 层年 9 月的月内仅：

 [![](in-app-purchase-basics-and-configuration-images/image11.png "其中价格将暂时更改从第 1 层为第 3 层年 9 月的月内仅未来日期的价格变化")](in-app-purchase-basics-and-configuration-images/image11.png#lightbox)

### <a name="free-products-not-supported"></a>不支持的免费产品

尽管 Apple 提供了 Newsstand 应用特殊免费订阅选项，但不能为任何其他应用内购买类型设置为零 （免费） 的价格。 虽然您可以编辑 (即。 较低) 的促销价格，不能进行应用内购买免费，通过 iTunes Connect。

### <a name="localization"></a>本地化

在 iTunes Connect 中您可以输入任意数量的支持的语言不同的名称和说明文本。 每种语言可以添加/编辑中通过一个弹出窗口：

 [![](in-app-purchase-basics-and-configuration-images/image12.png "每种语言可以添加/编辑中通过一个弹出窗口")](in-app-purchase-basics-and-configuration-images/image12.png#lightbox)   
   
   
   
 应用程序中显示的产品信息，请将可供您可以通过 StoreKit 显示本地化的文本。 此外必须本地化的货币显示以显示正确的符号和十进制格式设置 – 文档后面部分中介绍了此格式设置。

### <a name="app-store-review"></a>App Store 审核

相同的应用 – 每个产品是由 Apple 前查看允许转上销售。 产品可能拒绝不适当内容中的名称或描述，或 Apple 可能会决定选择的错误的产品类型 （例如。 你已创建书籍或杂志问题但使用的可使用产品类型）。 产品评论可以需要长达应用审核。

第一次使用应用内购买启用 （无论它是一个新的应用，还是功能已添加到一个现有） 提交应用程序还必须选择某些产品来与它一起提交。 在 iTunes Connect 门户将提示你执行此操作，如以下屏幕截图中所示：

 [![](in-app-purchase-basics-and-configuration-images/image13.png "在 iTunes Connect 门户将提示你提交某些产品也")](in-app-purchase-basics-and-configuration-images/image13.png#lightbox)   
   
   
   
 应用程序和应用内购买将查看在一起，以便它们都获得批准在一次 （因此，该应用不会进入已批准的任何产品的商店 ！）。

第一个版本通过应用内购买功能已获得批准后，可以添加更多产品并将其提交供随时查看。 您还可以选择将提交与特定的应用内购买产品的新版本使用**版本的详细信息**页，因为在提示符处提供建议。

请参阅[应用商店查看准则](https://developer.apple.com/appstore/guidelines.html)有关详细信息。

 [第 2 部分-存储工具包概述和检索产品信息](~/ios/platform/in-app-purchasing/store-kit-overview-and-retreiving-product-information.md)
