---
ms.openlocfilehash: aee7c7eb494af76bb450038453c19ee1ed83f2d3
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61388776"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 启动 Visual Studio，新建名为 WebServiceTutorial 的 Xamarin.Forms 空白应用。 确保该应用使用 .NET Standard 作为共享代码机制。

    > [!IMPORTANT]
    > 本教程中的 C# 和 XAML 片段要求将解决方案命名为 WebServiceTutorial。 使用不同的名称会导致：将本教程中的代码复制到解决方案中时出现生成错误。

    有关创建的 .NET Standard 库的详细信息，请参阅 [Xamarin.Forms 快速入门的深入探讨](~/get-started/first-app/index.md)中的 [Xamarin.Forms 应用程序剖析](~/get-started/first-app/index.md)。

1. 在“解决方案资源管理器”中，选择 WebServiceTutorial 项目，然后右键单击并选择“管理 NuGet 包…”：

    ![选中“添加 NuGet 包”菜单项的屏幕截图](../images/vs/add-nuget-packages.png "添加 NuGet 包菜单项")

1. 在“NuGet 包管理器”中，选择“浏览”选项卡，搜索“Newtonsoft.Json”NuGet 包，选择它，然后单击“安装”按钮将其添加到项目：

    ![NuGet 包管理器中的 Newtonsoft.Json NuGet 包的屏幕截图](../images/vs/add-package.png "Newtonsoft.Json NuGet 包")

    此包将用于将 JSON 反序列化合并到应用程序。

1. 生成解决方案，确保没有任何错误。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 启动 Visual Studio for Mac，新建名为 WebServiceTutorial 的 Xamarin.Forms 空白应用。 确保该应用使用 .NET Standard 作为共享代码机制。

    > [!IMPORTANT]
    > 本教程中的 C# 和 XAML 片段要求将解决方案命名为 WebServiceTutorial。 使用不同的名称会导致：将本教程中的代码复制到解决方案中时出现生成错误。

    有关创建的 .NET Standard 库的详细信息，请参阅 [Xamarin.Forms 快速入门的深入探讨](~/get-started/first-app/index.md)中的 [Xamarin.Forms 应用程序剖析](~/get-started/first-app/index.md)。

1. 在 Solution Pad 中，选择 WebServiceTutorial 项目，然后右键单击并选择“添加”>“添加 NuGet 包…”：

    ![选中“添加 NuGet 包”菜单项的屏幕截图](../images/vsmac/add-nuget-packages.png "添加 NuGet 包菜单项")

1. 在“添加包”窗口中，搜索“Newtonsoft.Json”NuGet 包，选择它，然后单击“添加包”按钮将其添加到项目：

    ![NuGet 包管理器中的 Newtonsoft.Json NuGet 包的屏幕截图](../images/vsmac/add-package.png "Newtonsoft.Json NuGet 包")

    此包将用于将 JSON 反序列化合并到应用程序。

1. 生成解决方案，确保没有任何错误。
