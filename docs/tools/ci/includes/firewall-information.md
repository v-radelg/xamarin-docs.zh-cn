---
ms.openlocfilehash: d46968f9c53314abe561e7f4871cfbf6e07b7002
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61047481"
---
## <a name="firewall-configuration"></a>防火墙配置

为了使测试，以将应用提交到 Xamarin Test Cloud，提交测试的计算机必须能够与测试云服务器进行通信。 必须将防火墙配置为允许传入和传出位于的服务器的网络流量**testcloud.xamarin.com**端口 80 和 443 上。 此终结点托管 dns 和 IP 地址可能会发生更改。 

在某些情况下，测试 （或运行测试的设备） 必须与受防火墙保护的 web 服务器通信。 在此方案中必须配置防火墙以允许来自以下 IP 地址的流量：

* **195.249.159.238**
* **195.249.159.239**
