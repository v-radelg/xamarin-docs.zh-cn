---
title: 将团队城市与 Xamarin 配合使用
description: 本指南将讨论使用 TeamCity 编译移动应用程序并将其提交到 Xamarin Test Cloud 所涉及的步骤。
ms.prod: xamarin
ms.assetid: AC2626CB-28A7-4808-B2A9-789D67899546
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 94bc775366d832e0994b8d3c74a45123ff56c13b
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76725303"
---
# <a name="using-team-city-with-xamarin"></a>将团队城市与 Xamarin 配合使用

_本指南将讨论使用 TeamCity 编译移动应用程序并将其提交到 Xamarin Test Cloud 所涉及的步骤。_

如[持续集成指南简介](~/tools/ci/intro-to-ci.md)中所述，持续集成（CI）是开发高质量移动应用程序时的一种非常有用的做法。 对于持续集成服务器软件，有许多可行的选项;本指南将重点介绍 JetBrains 中的[TeamCity](https://www.jetbrains.com/teamcity/) 。

TeamCity 安装有多个不同的排列。 下面是其中一些部分的列表：

- **Windows 服务**–在此方案中，当 Windows 作为 windows 服务启动时，TeamCity 启动。 它必须与 Mac 生成主机配对，才能编译任何 iOS 应用程序。

- **在 OS X 上启动守护**程序–从概念上讲，这与在上一步中描述的 Windows 服务运行非常类似。 默认情况下，生成将在根帐户下运行。

- **OS X 上的用户帐户**–可以在每次用户登录时启动的用户帐户下运行 TeamCity。

在前面的方案中，使用 OS X 上的用户帐户运行 TeamCity 是最简单且最容易的设置。

设置 TeamCity 涉及多个步骤：

- **安装 TeamCity** –本指南未介绍如何安装 TeamCity。 本指南假定 TeamCity 已安装，并且在用户帐户下运行。 有关[安装 TeamCity](https://confluence.jetbrains.com/display/TCD8/Installation)的说明，请参阅 JetBrains 的[TeamCity 8 文档](https://confluence.jetbrains.com/display/TCD8/TeamCity+Documentation)。

- **准备生成服务器**–此步骤涉及安装生成移动应用程序所需的软件、工具和证书，并准备好它们以进行分发。

- **创建生成脚本**–此步骤并不是必需的，但生成脚本可帮助你以无人参与方式生成应用程序。 使用生成脚本可帮助解决可能出现的生成问题，并提供一致的、可重复的方式来创建要分发的二进制文件，即使不会进行持续集成。

- **创建 TeamCity 项目**–在完成上述三个步骤之后，必须创建一个 TeamCity 项目，该项目将包含检索源代码、编译项目和将测试提交到 Xamarin Test Cloud 所需的所有元数据。

## <a name="requirements"></a>요구 사항

需要[App Center 测试](https://docs.microsoft.com/appcenter/test-cloud/)的经验。

需要熟悉 TeamCity 8.1。 TeamCity 的安装超出了本文档的范围。 假设 TeamCity 安装在 OS X Mavericks 上，并且在普通用户帐户下运行，而不是在根帐户下运行。

生成服务器应该是运行 OS X 的独立计算机，专用于持续集成。 理想情况下，生成服务器将不负责任何其他角色，如数据库服务器、web 服务器或开发人员工作站。

> [!IMPORTANT]
> 本指南不包括 Xamarin 的 "无外设" 安装。

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="preparing-the-build-server"></a>准备生成服务器

配置生成服务器的一个关键步骤是安装所有必需的工具、软件和证书以构建移动应用程序。 生成服务器必须能够编译移动解决方案并运行所有测试，这一点很重要。 若要最大程度地减少配置问题，应在托管 TeamCity 的同一用户帐户中安装软件和工具。 下面是需要执行的操作的列表：

1. **Visual Studio for Mac** –其中包括 Xamarin 和 xamarin。
2. **登录到 Xamarin 组件存储区**–这是一个可选步骤，并且仅当你的应用程序使用 Xamarin 组件存储中的组件时才需要。 此时主动登录到组件存储会阻止 TeamCity 生成尝试编译应用程序时出现的任何问题。
3. **Xcode** –对 iOS 应用程序进行编译和签名需要 Xcode。
4. **Xcode 命令行工具**–在[更新 Ruby with rbenv](https://github.com/calabash/calabash-ios/wiki)指南的 "安装" 部分的步骤1中进行了介绍。
5. **签名身份 & 预配配置文件**–通过 XCode 导入证书和预配配置文件。 有关更多详细信息，请参阅 Apple 有关[导出签名标识和预配配置文件](https://developer.apple.com/library/ios/recipes/xcode_help-accounts_preferences/articles/export_signing_assets.html)的指南。
6. **Android 密钥库**–将所需的 Android 密钥库复制到 TeamCity 用户有权访问的目录，即 `~/Documents/keystores/MyAndroidApp1`。
7. **Calabash** -如果你的应用程序具有使用 Calabash 编写的测试，则这是一个可选步骤。 有关详细信息，请参阅[安装 Calabash ON OS X Mavericks](https://github.com/calabash/calabash-ios/wiki)指南和[更新 Ruby with rbenv](https://github.com/calabash/calabash-ios/wiki)指南。

下图演示了这些组件的所有组件：

![](teamcity-images/image1.png "This diagram illustrates all of these components")

安装所有软件后，登录到该用户帐户，并确认所有软件都已正确安装并正常工作。 这应涉及到编译解决方案并将应用程序提交到 Test Cloud。 可以通过运行生成脚本来大大简化这一点，如下一节中所述。

## <a name="create-a-build-script"></a>创建生成脚本

尽管 TeamCity 完全可以处理编译和提交移动应用程序的各个方面，才能 Test Cloud 自身，但强烈建议创建生成脚本。 生成脚本具有以下优势：

1. **文档**–生成脚本是一种用于构建软件的方式的文档。 这消除了与部署应用程序相关的一些 "神奇"，并允许开发人员将精力集中在功能上。
1. **可重复性**–一个生成脚本可确保每次编译和部署应用程序时都以完全相同的方式进行，而不管工作是谁或什么。 这种可重复的一致性可消除由于错误执行的生成或人为错误而在中蔓延的任何问题或错误。
1. **版本控制**-可在源代码管理系统中包含生成脚本。 这意味着，如果发现错误或错误，可以跟踪、监视和更正对生成脚本所做的更改。
1. **准备环境**–生成脚本可包含用于安装任何所需的第三方依赖项的逻辑。 这将确保用适当的组件生成应用程序。

生成脚本可以很简单，如 Powershell 文件（在 Windows 上）或 bash 脚本（在 OS X 上）。 创建生成脚本时，有几种脚本语言选项可供选择：

- [**Rake**](https://github.com/jimweirich/rake) -这是基于 Ruby 构建项目的域特定语言（DSL）。 Rake 的优势在于普及和丰富的库生态系统。

- [**psake**](https://github.com/psake/psake) –这是用于构建软件的 Windows Powershell 库

- [**虚设**](https://fsharp.github.io/FAKE/)–这是一种基于F# DSL 的，如果需要，可以利用现有的 .net 库。

使用哪种脚本语言取决于您的首选项和要求。

> [!NOTE]
> 可以使用基于 XML 的生成系统（如 MSBuild 或 NAnt），但这缺乏专用于构建软件的 DSL 的表现力和可维护性。

### <a name="parameterizing-the-build-script"></a>参数化生成脚本

构建和测试软件的过程需要保密的信息。 特别是创建 APK 时，可能需要密钥存储的密码和/或密钥存储中的密钥别名。 同样，Test Cloud 需要一个对开发人员唯一的 API 密钥。 这些类型的值不应在生成脚本中进行硬编码。 相反，它们应作为变量传递到生成脚本。

不太敏感是一些值，例如 iOS 设备 ID 或 Android 设备 ID，用于标识 Test Cloud 应用于测试运行的设备。 这些不是需要保护的值，但它们可能会因生成而发生更改。

将这些类型的变量存储在生成脚本之外还可以更轻松地与开发人员一起共享组织内的生成脚本。 开发人员可以使用与生成服务器完全相同的脚本，但可以使用自己的密钥库和 API 密钥。

可以通过两种方法存储这些敏感值：

- **配置文件**–为了保护 Test Cloud API 密钥，此值不应签入到版本控制中。 可以为每台计算机创建该文件。 从此文件中读取值的方式取决于所使用的脚本语言。

- **环境变量**–可以在每台计算机上轻松地设置这些变量，并且独立于基础脚本语言共享。

每种选择都有各自的优点和缺点。 TeamCity 适用于环境变量，因此本指南将在创建生成脚本时建议使用此方法。

### <a name="build-steps"></a>生成步骤

生成脚本必须能够执行以下步骤：

- **编译应用程序**–这包括通过正确的预配配置文件对应用程序进行签名。

- 将**应用程序提交到 Xamarin Test Cloud** –这包括签名和 ZIP 将 APK 与适当的密钥存储进行对齐。

下面将更详细地介绍这两个步骤。

#### <a name="compiling-a-xamarinios-application"></a>编译 Xamarin iOS 应用程序

[!include[](~/tools/ci/includes/commandline-compile-of-xamarin-ios-ipa.md)]

#### <a name="compiling-a-xamarinandroid-application"></a>编译 Xamarin Android 应用程序

若要编译 Android 应用程序，请使用**xbuild** （或 Windows 上的**msbuild** ）：

```bash
/Library/Frameworks/Mono.framework/Commands/xbuild /t:SignAndroidPackage /p:Configuration=Release /path/to/android.csproj
```

请注意，若要编译 Xamarin Android 应用程序， **xbuild**将使用该项目，并且要生成 iOS 应用程序**xbuild**需要解决方案。

#### <a name="submitting-xamarinuitests-to-test-cloud"></a>正在将 Uitest 提交到 Test Cloud

Uitest 是使用 `test-cloud.exe` 应用程序提交的，如以下代码片段所示：

```bash
test-cloud.exe <path-to-apk-or-ipa-file> <test-cloud-team-api-key> --devices <device-selection-id> --assembly-dir <path-to-tests-containing-test-assemblies> --nunit-xml report.xml --user <email>
```

当测试运行时，测试结果将**以名为 NUnit 的 xml 文件**的形式返回。 TeamCity 会在生成日志中显示信息。

有关如何将 Uitest 提交到 Test Cloud 的详细信息，请参阅[准备 Xamarin Android 应用](/appcenter/test-cloud/preparing-for-upload/xamarin-android-uitest)或[准备 xamarin iOS 应用](/appcenter/test-cloud/preparing-for-upload/xamarin-ios-uitest)。

#### <a name="submitting-calabash-tests-to-test-cloud"></a>将 Calabash 测试提交到 Test Cloud

Calabash 测试是使用 `test-cloud` gem 提交的，如以下代码片段所示：

```bash
test-cloud submit /path/to/APK-or-IPA <test-cloud-team-api-key> --devices <device-id> --user <email>
```

若要将 Android 应用程序提交到 Test Cloud，必须先使用 calabash 将 APK 测试服务器重新生成：

```bash
$ calabash-android build </path/to/signed/APK>
$ test-cloud submit /path/to/APK <test-cloud-team-api-key> --devices <ANDROID_DEVICE_ID> --profile=android --config=config/cucumber.yml --pretty
```

有关提交 Calabash 测试的详细信息，请参阅有关[将 Calabash 测试提交到 Test Cloud](https://github.com/calabash/calabash-ios/wiki)的 Xamarin 指南。

## <a name="creating-a-teamcity-project"></a>创建 TeamCity 项目

安装 TeamCity 并 Visual Studio for Mac 可以生成项目后，就可以在 TeamCity 中创建项目以生成项目并将其提交到 Test Cloud。

1. 通过 web 浏览器登录到 TeamCity 开始。 导航到根项目：

    ![导航到根项目](teamcity-images/image2.png "导航到根项目")在根项目下，创建一个新的子项目：

    ![导航到根项目下面的根项目，创建一个新的子项目](teamcity-images/image3.png "导航到根项目下面的根项目，创建一个新的子项目")
2. 创建子项目后，添加新的生成配置：

    ![创建子项目后，添加新的生成配置](teamcity-images/image5.png "创建子项目后，添加新的生成配置")
3. 将 VCS 项目附加到生成配置。 此操作通过 "版本控制" 设置屏幕完成：

    ![此操作通过 "版本控制" 设置屏幕来完成](teamcity-images/image6.png "此操作通过 "版本控制" 设置屏幕来完成")

    如果未创建 VCS 项目，你可以选择从下面显示的 "新的 VCS 根" 页中创建一个项目：

    ![如果没有创建 VCS 项目，可以选择从新的 VCS 根页面创建一个](teamcity-images/image7.png "如果没有创建 VCS 项目，可以选择从新的 VCS 根页面创建一个")

    附加 VCS 根后，TeamCity 将签出该项目并尝试自动检测生成步骤。 如果熟悉 TeamCity，则可以选择检测到的生成步骤之一。 现在可以放心地忽略检测到的生成步骤。

4. 接下来，配置生成触发器。 这会在满足特定条件时（例如，当用户将代码提交到存储库时）对生成进行排队。 以下屏幕截图显示了如何添加生成触发器：

    ![此屏幕截图显示了如何添加生成触发器](teamcity-images/image8.png "此屏幕截图显示了如何添加生成触发器")可以在以下屏幕截图中查看配置生成触发器的示例：

    ![可在此屏幕截图中查看配置生成触发器的示例](teamcity-images/image9.png "可在此屏幕截图中查看配置生成触发器的示例")

5. 上一部分，参数化生成脚本，并建议将某些值存储为环境变量。 可以通过 "参数" 屏幕将这些变量添加到生成配置中。 如以下屏幕截图所示，为 Test Cloud API 密钥、iOS 设备 ID 和 Android 设备 ID 添加变量：

    ![为 Test Cloud API 密钥、iOS 设备 ID 和 Android 设备 ID 添加变量](teamcity-images/image11.png "为 Test Cloud API 密钥、iOS 设备 ID 和 Android 设备 ID 添加变量")

6. 最后一步是添加一个生成步骤，该步骤将调用生成脚本来编译应用程序，并将应用程序排入队列以便 Test Cloud。 以下屏幕截图是使用 Rakefile 生成应用程序的生成步骤示例：

    ![此屏幕截图是使用 Rakefile 生成应用程序的生成步骤示例](teamcity-images/image12.png "此屏幕截图是使用 Rakefile 生成应用程序的生成步骤示例")

7. 此时，生成配置已完成。 最好触发生成以确认已正确配置该项目。 实现此目的的一种好方法是提交对存储库的少量不重要的更改。 TeamCity 应检测到提交并启动生成。

8. 完成生成后，检查生成日志，并查看生成中是否存在需要注意的问题或警告。

## <a name="summary"></a>요약

本指南介绍了如何使用 TeamCity 生成 Xamarin 移动应用程序，然后将其提交到 Test Cloud。 我们讨论了如何创建生成脚本来自动执行生成过程。 生成脚本负责编译应用程序、提交到 Test Cloud 并等待结果

然后，我们介绍了如何在 TeamCity 中创建一个项目，该项目将在每次开发人员提交代码时对生成进行排队，并调用生成脚本。

## <a name="related-links"></a>관련 링크

- [正在准备 Xamarin Android 应用](/appcenter/test-cloud/preparing-for-upload/xamarin-android-uitest)
- [正在准备 Xamarin iOS 应用](/appcenter/test-cloud/preparing-for-upload/xamarin-ios-uitest)
- [安装和配置 TeamCity](https://confluence.jetbrains.com/display/TCD8/Installing+and+Configuring+the+TeamCity+Server)
