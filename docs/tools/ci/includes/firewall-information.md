---
ms.openlocfilehash: d46968f9c53314abe561e7f4871cfbf6e07b7002
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "61047481"
---
## <a name="firewall-configuration"></a>防火墙配置

为了将测试提交到 Xamarin Test Cloud，提交测试的计算机必须能够与 Test Cloud 服务器通信。 必须将防火墙配置为允许进出端口80和443上**testcloud.xamarin.com**的服务器的网络流量。 此终结点由 DNS 管理，IP 地址可能会更改。 

在某些情况下，测试（或运行测试的设备）必须与受防火墙保护的 web 服务器进行通信。 在这种情况下，必须将防火墙配置为允许来自以下 IP 地址的流量：

* **195.249.159.238**
* **195.249.159.239**
