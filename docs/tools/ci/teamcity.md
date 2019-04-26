---
title: 通过 Xamarin 使用团队城市
description: 本指南将讨论结合使用 TeamCity 编译移动应用程序，然后将它们提交到 Xamarin Test Cloud 所涉及的步骤。
ms.prod: xamarin
ms.assetid: AC2626CB-28A7-4808-B2A9-789D67899546
author: lobrien
ms.author: laobri
ms.date: 03/23/2017
ms.openlocfilehash: a3cb79f33b64d933aa8ab4d3555479cc16238992
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61218888"
---
# <a name="using-team-city-with-xamarin"></a>通过 Xamarin 使用团队城市

_本指南将讨论结合使用 TeamCity 编译移动应用程序，然后将它们提交到 Xamarin Test Cloud 所涉及的步骤。_

如中所述[持续集成简介](~/tools/ci/intro-to-ci.md)指南中，开发高质量的移动应用程序时持续集成 (CI) 是很好的做法。 有许多可行选项的持续集成服务器软件;本指南将重点[TeamCity](http://www.jetbrains.com/teamcity/)来自 JetBrains。

有几个不同的 TeamCity 安装排列。 下面是其中一些的列表：

- **Windows 服务**– 在此方案中，TeamCity 开始运行后 Windows 作为 Windows 服务的启动时。 它必须与 Mac 生成主机来编译任何 iOS 应用程序配对。

- **启动在 OS X 上的守护程序**– 从概念上讲，这是非常类似于作为 Windows 服务在上一步中所述运行。 默认情况下将根帐户下运行生成。

- **在 OS X 上的用户帐户**– 可以在启动每次用户登录的用户帐户下运行 TeamCity。

前面的方案中，在 OS X 上的用户帐户下运行 TeamCity 是最简单且最简单到安装程序。

有几个步骤所涉及的设置 TeamCity:

- **安装 TeamCity** – 本指南中未提及的 TeamCity 安装。 本指南假定 TeamCity 已安装并在用户帐户下运行。 说明[安装 TeamCity](http://confluence.jetbrains.com/display/TCD8/Installation)中可以找到[TeamCity 8 文档](http://confluence.jetbrains.com/display/TCD8/TeamCity+Documentation)由 jetbrains 开发。

- **准备生成服务器**– 此步骤包括安装所需的软件工具，并为所需证书构建移动应用程序并准备用于分发。

- **创建生成脚本**– 此步骤不是必需的但生成脚本是如何构建无人参与的应用程序有用帮助。 使用生成脚本将帮助进行故障排除可能会出现并提供一致、 可重复方式创建用于分发，二进制文件，即使不实施持续集成的生成问题。

- **创建 TeamCity 项目**– 完成前面的三个步骤后，我们必须创建 TeamCity 项目将包含的所有元数据检索的源代码、 编译项目，并将测试提交到 Xamarin 测试云需要。

## <a name="requirements"></a>要求

经验[Xamarin Test Cloud](https://developer.xamarin.com/guides/testcloud)是必需的。

熟悉 TeamCity 8.1 是必需的。 TeamCity 安装不在本文档的范围。 假定 TeamCity 安装在 OS X Mavericks 上和普通用户帐户而不是根帐户下运行。

在生成服务器应运行 OS X，专用于持续集成的独立计算机。 理想情况下，生成服务器将不需要承担的任何其他角色，如数据库服务器、 web 服务器或开发人员工作站。

> [!IMPORTANT]
> 本指南不涉及 Xamarin 的"无外设"安装。

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="preparing-the-build-server"></a>准备生成服务器

配置生成服务器中的一个重要步骤是安装所有必要的工具、 软件和证书来构建移动应用程序。 非常重要，在生成服务器必须能够编译移动解决方案并运行任何测试。 为了尽量减少配置问题，软件和工具应安装在承载 TeamCity 的相同用户帐户。 下面是一系列的要求：

1. **Visual Studio for Mac** – 这包括 Xamarin.iOS 和 Xamarin.Android。
2. **登录到 Xamarin Component Store** – 这是一个可选步骤，才需要应用程序如果从 Xamarin 组件商店使用组件。 TeamCity 生成尝试编译该应用程序时，主动在这里登录到组件存储将阻止任何问题。
3. **Xcode** – Xcode 才能编译和符号的 iOS 应用程序。
4. **Xcode 命令行工具**– 这在步骤 1 的安装部分中所述[更新使用 rbenv 的 Ruby](https://developer.xamarin.com/guides/testcloud/calabash/updating-ruby-using-rbenv/)指南。
5. **签名标识和预配配置文件**-导入证书和预配配置文件通过 XCode。 请参阅 Apple 的指南[导出的签名标识和预配配置文件](https://developer.apple.com/library/ios/recipes/xcode_help-accounts_preferences/articles/export_signing_assets.html)的更多详细信息。
6. **Android 密钥库**– 将所需的 Android 密钥库复制到目录 TeamCity 用户即有权访问， `~/Documents/keystores/MyAndroidApp1`。
7. **Calabash** – 这是一个可选步骤，如果你的应用程序具有使用 Calabash 编写测试。 请参阅[OS X Mavericks 上安装 Calabash](https://developer.xamarin.com/guides/testcloud/calabash/osx-installation/)指南和[更新使用 rbenv 的 Ruby](https://developer.xamarin.com/guides/testcloud/calabash/updating-ruby-using-rbenv/)指南以获取详细信息。

下图说明了所有这些组件：

![](teamcity-images/image1.png "此图描述了所有这些组件")

一旦已安装的所有软件，登录到用户帐户和确认的所有软件已正确安装并且正常工作。 这应包括编译解决方案和应用程序提交到 Test Cloud。 这可以极大地简化通过运行生成脚本中下, 一节中所述。

## <a name="create-a-build-script"></a>创建生成脚本

虽然完全有可能把 TeamCity 来处理编译和提交到 Test Cloud 的移动应用程序本身的所有方面，但强烈建议创建的构建脚本。 生成脚本具有以下优点：

1. **文档**– 生成脚本作为窗体上构建软件的方式的文档。 这消除了一些"奇妙"的与部署应用程序相关联，并允许开发人员能够专注于功能。
1. **可重复性**– 生成脚本可确保在每次编译和部署，应用程序时它发生在完全相同的方式，而不考虑人员或内容执行的工作。 这种可重复一致性中删除任何问题或可能会悄悄引入许多由于错误地执行生成的错误或人为错误。
1. **版本控制**– 生成脚本可以包含在源代码管理系统。 这意味着，可以跟踪、 监视和更正如果找不到错误的数据对生成脚本的更改。
1. **准备环境**– 生成脚本可以包含用于安装任何所需的第三方依赖项的逻辑。 这将确保应用程序利用了正确的组件。

生成脚本可以作为 Powershell 文件 （在 Windows) 或 bash 脚本 （在 OS X) 一样简单。 在创建时生成脚本，有的脚本语言的多个选项：

- [**Rake** ](https://github.com/jimweirich/rake) – 这是域特定语言 (DSL)，用于构建基于 Ruby 的项目。 Rake 具有受欢迎程度的优点和丰富的库。

- [**psake** ](https://github.com/psake/psake) – 这是用于构建软件的 Windows Powershell 库

- [**虚设**](http://fsharp.github.io/FAKE/) – 这是 DSL 中基于F#从而可利用现有的.NET 库，如有必要。

所使用的脚本编写语言取决于您的首选项和要求。 [TaskyPro Calabash](https://github.com/xamarin/test-cloud-samples/tree/master/TaskyPro/TaskyPro-Calabash)示例中包含的使用 Rake 作为示例[生成脚本](https://github.com/xamarin/test-cloud-samples/blob/master/TaskyPro/TaskyPro-Calabash/Rakefile)。

> [!NOTE]
> 它是可以使用诸如 MSBuild 或 NAnt，但这些缺少之类的基于 XML 生成系统的表现力和可维护性的专用于构建软件的 DSL。

### <a name="parameterizing-the-build-script"></a>参数化生成脚本

构建和测试软件的过程要求应保密的信息。 特别是创建 APK 可能会要求提供密码来密钥存储和/或密钥存储中的密钥别名。 同样，测试云要求仅适用于开发人员的 API 密钥。 这些类型的值不应进行硬编码在生成脚本中。 而是它们应是作为变量传递给生成脚本。

敏感程度较低是如运行 iOS 的设备 ID 或标识的设备测试云应该用于测试的 Android 设备 ID 的值。 这些不是值需要受到保护，但它们可能会更改内部版本到内部版本。

将存储这些类型的外部生成脚本的变量还可以更轻松地与开发人员例如共享组织中的生成脚本。 开发人员可以为生成服务器中，使用完全相同的脚本，但可以使用其自己的密钥库和 API 密钥。

有两个可能的选项，用于存储这些敏感的值：

- **配置文件**– 若要保护此值不应签入版本控制测试云 API 密钥。 每台计算机，可以创建该文件。 如何从文件中读取值取决于所使用的脚本语言。

- **环境变量**-这些可以轻松地设置每台计算机和共享独立于基础的脚本语言。

有一些优点和缺点的每一个这些选择。 TeamCity 可很好地与环境变量，因此创建生成脚本时，本指南将建议此方法。

### <a name="build-steps"></a>生成步骤

生成脚本必须能够执行以下步骤：

- **编译该应用程序**– 这包括正确的预配配置文件与应用程序进行签名。

- **应用程序提交到 Xamarin Test Cloud** – 其中包括签名和 zip 排列 APK 使用适当的密钥存储。

将下面更详细地介绍这两个步骤。

#### <a name="compiling-a-xamarinios-application"></a>编译 Xamarin.iOS 应用程序

[!include[](~/tools/ci/includes/commandline-compile-of-xamarin-ios-ipa.md)]

#### <a name="compiling-a-xamarinandroid-application"></a>编译 Xamarin.Android 应用程序

若要编译的 Android 应用程序，请使用**xbuild** (或**msbuild**在 Windows 上):

```bash
/Library/Frameworks/Mono.framework/Commands/xbuild /t:SignAndroidPackage /p:Configuration=Release /path/to/android.csproj
```

请注意，若要编译 Xamarin Android 应用程序**xbuild**使用该项目，并且生成 iOS 应用程序**xbuild**要求解决方案。

#### <a name="submitting-xamarinuitests-to-test-cloud"></a>提交到测试云 Xamarin.UITests

使用提交 Uitest`test-cloud.exe`应用程序，如以下代码片段中所示：

```bash
test-cloud.exe <path-to-apk-or-ipa-file> <test-cloud-team-api-key> --devices <device-selection-id> --assembly-dir <path-to-tests-containing-test-assemblies> --nunit-xml report.xml --user <email>
```

运行测试时，将以名为 NUnit 样式 XML 文件的形式返回测试结果**report.xml**。 TeamCity 将生成日志中显示的信息。

有关如何将提交到 Test Cloud 的 Uitest 的详细信息，请查阅本指南上[上传准备 Xamarin.UITests](/appcenter/test-cloud/preparing-for-upload/uitest/)。

#### <a name="submitting-calabash-tests-to-test-cloud"></a>提交 Calabash 测试来测试云

使用 Calabash 测试提交`test-cloud`gem，如以下代码片段中所示：

```bash
test-cloud submit /path/to/APK-or-IPA <test-cloud-team-api-key> --devices <device-id> --user <email>
```
若要提交到 Test Cloud 的 Android 应用程序，它是需要首先重新生成使用 calabash android APK 测试服务器：

```bash
$ calabash-android build </path/to/signed/APK>
$ test-cloud submit /path/to/APK <test-cloud-team-api-key> --devices <ANDROID_DEVICE_ID> --profile=android --config=config/cucumber.yml --pretty
```
有关提交 Calabash 测试的详细信息，请参阅 Xamarin 的指南上[提交到 Test Cloud Calabash 测试](https://developer.xamarin.com/guides/testcloud/calabash/working-with/submitting-tests-to-xamarin-test-cloud/)。

## <a name="creating-a-teamcity-project"></a>创建 TeamCity 项目

一旦安装 TeamCity 和 Visual Studio for Mac 可生成项目，就可以在 TeamCity 生成项目并将其提交到 Test Cloud 中创建一个项目。

1. 通过登录到 TeamCity 通过 web 浏览器来启动。 导航到根项目：

    ![导航到根项目](teamcity-images/image2.png "导航到根项目")根项目下创建一个新的子项目：

    ![导航到根项目下面根项目，创建一个新的子项目](teamcity-images/image3.png "导航到根项目下面根项目，创建一个新的子项目")
2. 一旦创建子项目后，添加新的生成配置：

    ![一旦创建子项目后，添加新的生成配置](teamcity-images/image5.png "一旦创建子项目后，添加新的生成配置")
3. 将 VCS 项目附加到生成配置。 通过版本控制设置屏幕完成此操作：

    ![这是通过版本控制设置屏幕](teamcity-images/image6.png "这是通过版本控制设置屏幕")

    如果不没有创建任何 VCS 项目，您可以通过创建一个从新 VCS 根页如下所示：

    ![如果不没有创建任何 VCS 项目，则必须创建一个新 VCS 根页的选项](teamcity-images/image7.png "如果不没有创建任何 VCS 项目，则必须创建一个新 VCS 根页的选项")

    一旦附加 VCS 根，TeamCity 将签出该项目，然后重试自动检测到生成步骤。 如果您熟悉 TeamCity，则可以选择一个检测到的生成步骤。 它可以安全地暂时忽略检测到的生成步骤。

4. 接下来，配置生成的触发器。 这将生成进行排队时满足某些条件时，例如当用户提交到存储库的代码。 下面的屏幕截图显示了如何添加生成触发器：

    ![此屏幕截图显示如何添加生成触发器](teamcity-images/image8.png "此屏幕截图显示如何添加生成触发器")配置生成触发器的示例可以在以下屏幕截图中所示：

    ![配置生成触发器的示例所示此屏幕截图](teamcity-images/image9.png "配置生成触发器的示例所示此屏幕截图")

5. 上一部分中，参数化对生成的脚本，建议将某些值存储为环境变量。 可以将这些变量添加到通过参数屏幕的生成配置。 将变量添加测试云 API 密钥、 iOS 设备 ID 和 Android 的设备 ID，如下面的屏幕截图中所示：

    ![将变量添加测试云 API 密钥、 iOS 设备 ID 和 Android 的设备 ID](teamcity-images/image11.png "将变量添加测试云 API 密钥、 iOS 设备 ID 和 Android 的设备 ID")

6. 最后一步是添加将调用用于编译应用程序和排队到 Test Cloud 应用程序的生成脚本的生成步骤。 下面的屏幕截图是使用 Rakefile 构建的应用程序的生成步骤的示例：

    ![此屏幕截图是使用 Rakefile 构建的应用程序的生成步骤的示例](teamcity-images/image12.png "此屏幕截图是使用 Rakefile 构建的应用程序的生成步骤的示例")

7. 此时，已完成的生成配置。 它是一个好办法触发以确认已正确配置项目的生成。 执行此操作的好办法是将小型的无关紧要的更改提交到存储库。 TeamCity 应检测提交并启动生成。

8. 完成生成后，检查生成日志并查看是否有任何问题或需要关注与生成警告。

## <a name="summary"></a>总结

本指南介绍了如何使用 TeamCity 生成 Xamarin 移动应用程序，然后将它们提交到 Test Cloud。 我们讨论了创建生成脚本以自动执行生成过程。 生成脚本负责编译应用程序，将提交到 Test Cloud，并等待的结果

然后，我们介绍如何在 TeamCity，将生成排入队列每次在开发人员提交代码时，将调用生成脚本中创建一个项目。

## <a name="related-links"></a>相关链接

- [准备 Xamarin.UITests 以及上传](/appcenter/test-cloud/preparing-for-upload/uitest/)
- [安装和配置 TeamCity](http://confluence.jetbrains.com/display/TCD8/Installing+and+Configuring+the+TeamCity+Server)
