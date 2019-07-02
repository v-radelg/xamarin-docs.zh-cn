---
ms.openlocfilehash: cef0b8f56639e7bc8571ab01b820dfd54b074472
ms.sourcegitcommit: a153623a69b5cb125f672df8007838afa32e9edf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67277126"
---
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

若要完成本教程，应使用 Visual Studio 2019（最新版本），且安装了“使用 .NET 的移动开发”工作负载  。 此外，还需要一个匹配的 Mac，用于在 iOS 上生成教程应用程序。 有关安装 Xamarin 平台的信息，请参阅[安装 Xamarin](~/get-started/installation/index.md)。 有关将 Visual Studio 2019 连接到 Mac 生成主机的信息，请参阅[通过“与 Mac 配对”进行 Xamarin.iOS 开发](~/ios/get-started/installation/windows/connecting-to-mac/index.md)。

1. 启动 Visual Studio，新建名为 WebServiceTutorial 的 Xamarin.Forms 空白应用  。 确保该应用使用 .NET Standard 作为共享代码机制。

    > [!IMPORTANT]
    > 本教程中的 C# 和 XAML 片段要求将解决方案命名为 WebServiceTutorial  。 使用不同的名称会导致：将本教程中的代码复制到解决方案中时出现生成错误。

    有关创建的 .NET Standard 库的详细信息，请参阅 [Xamarin.Forms 快速入门的深入探讨](~/get-started/first-app/index.md)中的 [Xamarin.Forms 应用程序剖析](~/get-started/first-app/index.md)。

1. 在“解决方案资源管理器”中，选择 WebServiceTutorial 项目，然后右键单击并选择“管理 NuGet 包…”    ：

    ![选中“添加 NuGet 包”菜单项的屏幕截图](../images/vs/add-nuget-packages.png "添加 NuGet 包菜单项")

1. 在“NuGet 包管理器”中，选择“浏览”选项卡，搜索“Newtonsoft.Json”NuGet 包，选择它，然后单击“安装”按钮将其添加到项目     ：

    ![NuGet 包管理器中的 Newtonsoft.Json NuGet 包的屏幕截图](../images/vs/add-package.png "Newtonsoft.Json NuGet 包")

    此包将用于将 JSON 反序列化合并到应用程序。

1. 生成解决方案，确保没有任何错误。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

若要完成本教程，应使用 Visual Studio for Mac（最新版），且安装了 iOS 和 Android 平台支持。 此外，还需要 Xcode（最新版）。 有关安装 Xamarin 平台的详细信息，请参阅[安装 Xamarin](~/get-started/installation/index.md)。

1. 启动 Visual Studio for Mac，新建名为 WebServiceTutorial 的 Xamarin.Forms 空白应用  。 确保该应用使用 .NET Standard 作为共享代码机制。

    > [!IMPORTANT]
    > 本教程中的 C# 和 XAML 片段要求将解决方案命名为 WebServiceTutorial  。 使用不同的名称会导致：将本教程中的代码复制到解决方案中时出现生成错误。

    有关创建的 .NET Standard 库的详细信息，请参阅 [Xamarin.Forms 快速入门的深入探讨](~/get-started/first-app/index.md)中的 [Xamarin.Forms 应用程序剖析](~/get-started/first-app/index.md)。

1. 在 Solution Pad 中，选择 WebServiceTutorial 项目，然后右键单击并选择“添加”>“添加 NuGet 包…”    ：

    ![选中“添加 NuGet 包”菜单项的屏幕截图](../images/vsmac/add-nuget-packages.png "添加 NuGet 包菜单项")

1. 在“添加包”窗口中，搜索“Newtonsoft.Json”NuGet 包，选择它，然后单击“添加包”按钮将其添加到项目    ：

    ![NuGet 包管理器中的 Newtonsoft.Json NuGet 包的屏幕截图](../images/vsmac/add-package.png "Newtonsoft.Json NuGet 包")

    此包将用于将 JSON 反序列化合并到应用程序。

1. 生成解决方案，确保没有任何错误。
