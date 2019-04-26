---
title: 结合使用 Jenkins 和 Xamarin
description: 本文档介绍如何使用 Jenkins 实现与 Xamarin 应用程序的持续集成。 它介绍如何安装、 配置和使用 Jenkins。
ms.prod: xamarin
ms.assetid: 1E6825DF-1254-4FCB-B94D-ADD33D1B5309
author: lobrien
ms.author: laobri
ms.date: 03/23/2017
ms.openlocfilehash: 2e6a75fa3c4c63e8dea402c6761f8ef753908540
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61047485"
---
# <a name="using-jenkins-with-xamarin"></a>结合使用 Jenkins 和 Xamarin

_本指南说明如何设置 Jenkins 持续集成服务器，并自动执行编译使用 Xamarin 创建的移动应用程序。它介绍如何在 OS X 上安装 Jenkins，其进行配置，并将作业设置为编译 Xamarin.iOS 和 Xamarin.Android 应用程序时更改会提交到源代码管理系统。_

[与 Xamarin 持续集成简介](~/tools/ci/intro-to-ci.md)引入了有用的软件开发做法是提供早期警告的已损坏的或不兼容的代码的持续集成。 CI 允许开发人员解决问题和问题出现，并在部署的合适的状态中保留该软件。 本演练介绍如何结合使用这两个文档中的内容。

本指南演示如何在运行 OS X 的专用计算机上安装 Jenkins 并将其配置为在计算机启动时自动运行。 安装 Jenkins 后，我们将安装更多插件以支持 MS Build。 Jenkins 在现成支持 Git。 如果 TFS 用于源代码管理，还必须安装其他插件和命令行实用程序。

配置 Jenkins 并安装了任何必要的插件之后, 我们将创建一个或多个作业，以编译 Xamarin.Android 和 Xamarin.iOS 项目。 作业是一系列步骤和执行一些工作所需的元数据。 作业通常包括以下：

- **源代码管理 (SCM)** – 这是包含有关如何连接到源代码管理和要检索的文件信息的元数据条目的 Jenkins 配置文件中。
- **触发器**– 使用触发器来启动基于特定的操作，例如当开发人员提交到源代码存储库更改的作业。
- **构建说明**– 这是一个插件或将编译的源代码并生成可在移动设备安装的二进制文件的脚本。
- **可选的生成操作**– 这可能包括运行单元测试，签署代码的代码执行静态分析或启动另一个作业来执行其他生成相关的工作。
- **通知**– 作业可能会发送出某种类型的生成的状态通知。
- **安全**– 尽管是可选的强烈建议还启用 Jenkins 安全功能。

本指南将指导如何设置 Jenkins 服务器涵盖每个点。 它的结束时，我们应该充分了解如何设置和配置 Jenkins 以我们 Xamarin 移动项目中创建 IPA 和 APK。

## <a name="requirements"></a>要求

理想的生成服务器是专用于生成和可能测试应用程序的唯一目的的独立计算机。 在专用的计算机可确保项目可能需要其他角色 （例如，web 服务器） 的切勿弄脏生成。 例如，如果生成服务器还充当 web 服务器，web 服务器可能需要一些公用库的冲突版本。 由于此冲突的 web 服务器可能无法正常运行或 Jenkins 可能创建部署到用户时不起作用的生成。

Xamarin 移动应用的生成服务器设置非常类似的开发人员的工作站。 它具有用户帐户中的 Jenkins，Visual Studio for Mac，并将安装 Xamarin.iOS 和 Xamarin.Android。 必须也安装的所有代码签名证书时，预配配置文件和密钥存储。 *通常生成服务器的用户帐户是独立于开发人员帐户-请务必安装和配置所有软件、 密钥和证书时使用的生成服务器用户帐户登录。*

下图说明了所有这些元素的典型的 Jenkins 生成服务器上：

[![](jenkins-walkthrough-images/image1.png "此图描述了所有这些元素的典型的 Jenkins 生成服务器上")](jenkins-walkthrough-images/image1.png#lightbox)

iOS 应用程序可以仅生成并在运行 macOS 的计算机上进行签名。 Mac Mini 是合理的成本较低的选项，但能够运行 OS X 10.10 (Yosemite) 的任何计算机或更高版本就足够了。

如果 TFS 用于源代码管理，你将想要安装[Team Explorer Everywhere](https://docs.microsoft.com/azure/devops/java/download-eclipse-plug-in/)。 Team Explorer Everywhere 提供跨平台访问 TFS 在 macOS 中终端。

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="installing-jenkins"></a>安装 Jenkins

使用 Jenkins 的第一个任务是安装它。 有三种方法在 OS x： 上运行 Jenkins

- 作为守护程序，在后台运行。
- 在如 Tomcat、 Jetty 或 JBoss servlet 容器。
- 作为普通进程用户帐户下运行。

大多数传统的持续集成应用程序在后台运行，或者作为守护程序 (OS X 上或\*nix) 或 （在 Windows) 上的服务。 ： 必需的没有 GUI 交互并且可以轻松地执行安装程序的生成环境，这是适用于方案。 移动应用还需要密钥库和签名作为后台进程运行 Jenkins 时可能会访问有问题的证书。 由于这些问题，本文档重点介绍第三个方案 – Jenkins 用户帐户下运行生成服务器上。

Jenkins.App 是方便地安装 Jenkins。 这是一个 AppleScript 包装器，可简化的开始和停止的 Jenkins 服务器。 而不是在 bash shell 中运行，Jenkins 将作为运行，应用程序图标在停靠，如以下屏幕截图中所示：

[![](jenkins-walkthrough-images/image2.png "而不是运行 bash shell 中，Jenkins 将作为运行，应用程序中停靠、 图标，在此屏幕截图所示")](jenkins-walkthrough-images/image2.png#lightbox)

启动或停止 Jenkins 是启动或停止 Jenkins.App 一样简单。

若要安装 Jenkins.App，请从下面的屏幕截图中所示的项目的下载页下载最新版本：

[![](jenkins-walkthrough-images/image3.png "应用程序，请从项目的最新版本下载页上，此屏幕截图中所示的下载")](jenkins-walkthrough-images/image3.png#lightbox)

Zip 文件提取到`/Applications`上你的生成服务器和它就像任何其他 OS X 应用程序的启动文件夹。
第一次启动 Jenkins.App，它将显示一个对话框，通知它将下载 Jenkins:

[![](jenkins-walkthrough-images/image4.png "应用程序，它将显示一个对话框，通知它将下载 Jenkins")](jenkins-walkthrough-images/image4.png#lightbox)

Jenkins.App 完成下载后，它将显示另一个对话框，要求你是否你想要自定义 Jenkins 启动过程，如以下屏幕截图中所示：

[![](jenkins-walkthrough-images/image5.png "应用程序已经完成下载，它将显示另一个对话框，要求你是否你想要自定义 Jenkins 启动过程，如以下屏幕截图中所示")](jenkins-walkthrough-images/image5.png#lightbox)

自定义 Jenkins 是可选的不需要为 Jenkins 将适用于大多数情况下执行每次启动应用时-默认设置。

如果需要自定义 Jenkins，单击**更改默认值**按钮。 这将给你带来两个连续的对话框： 一个 Java 命令行参数的询问，另一个 Jenkins 命令行参数的要求。 以下两个屏幕截图显示这些两个对话框：

[![](jenkins-walkthrough-images/image6.png "此屏幕截图显示对话框")](jenkins-walkthrough-images/image6.png#lightbox)

[![](jenkins-walkthrough-images/image7.png "此屏幕截图显示对话框")](jenkins-walkthrough-images/image7.png#lightbox)

后运行 Jenkins，你可能想要将其设置为登录名项，以便它将开始每次中的用户登录到计算机。 您可以执行此操作通过右键单击停靠面板中的 Jenkins 图标，然后选择**选项...> 在登录时打开**，如以下屏幕截图中所示：

[![](jenkins-walkthrough-images/image8.png "此屏幕截图中所示，可以执行此 Jenkins 图标停靠面板中右键单击并选择登录名，在 OptionsOpen 操作")](jenkins-walkthrough-images/image8.png#lightbox)

这将导致 Jenkins.App 以自动启动每次用户登录，但不是在计算机启动注册。 它是可以指定 OS X 将在启动时使用自动使用的登录的用户帐户。 打开**系统首选项**，然后选择**用户和组**图标，在此屏幕截图所示：

[![](jenkins-walkthrough-images/image9.png "打开系统首选项，并选择用户组图标，如以下屏幕截图中所示")](jenkins-walkthrough-images/image9.png#lightbox)

单击**登录选项**按钮，并选择 OS X 将在启动时用于登录的帐户。

此时已安装 Jenkins。 但是，如果我们想要生成 Xamarin 移动应用程序，我们需要安装一些插件。

### <a name="installing-plugins"></a>安装插件

完成 Jenkins.App 安装程序后，它将启动 Jenkins 并启动 web 浏览器的 url http://localhost:8080，如以下屏幕截图中所示：

[![](jenkins-walkthrough-images/image10.png "8080，如以下屏幕截图中所示")](jenkins-walkthrough-images/image10.png#lightbox)

在此页中，选择**Jenkins > 管理 Jenkins > 管理插件**的菜单中的左上角，如下面的屏幕截图中所示：

[![](jenkins-walkthrough-images/image11.png "在此页中，从左上角的菜单中选择 Jenkins 管理的 Jenkins 管理插件")](jenkins-walkthrough-images/image11.png#lightbox)

这将显示**Jenkins 插件管理器**页。 如果单击可用选项卡上，您将看到一系列超过 600 个插件，可以下载并安装。 这是下面的屏幕截图中所示：

[![](jenkins-walkthrough-images/image12.png "如果单击可用选项卡上，你将看到一系列超过 600 个插件，可以下载并安装")](jenkins-walkthrough-images/image12.png#lightbox)

滚动浏览所有 600 插件查找几个可能比较繁琐且容易出错。 Jenkins 提供接口的右上角的筛选器搜索字段。 使用此筛选器字段搜索将简化查找和安装一个或所有以下插件：

- **Jenkins MSBuild 插件**– 此插件可以生成适用于 Mac 解决方案 (.sln) 和项目 (.csproj) Visual Studio 和 Visual Studio。
- **环境注入器插件**– 这是一个可选的但有用的插件，从而能够在作业中设置环境变量和生成级别。 例如，代码使用的密码登录该应用程序，它还提供对变量的额外保护。 有时缩写为*EnvInject 插件*。
- **Team Foundation Server 插件**– 这是一个可选的插件，只需如果使用 Team Foundation Server 或 Team Foundation Services 进行源代码管理。

没有任何额外的插件的情况下，Jenkins 支持 Git。

安装所有的插件之后, 你将想要重新启动 Jenkins 并配置每个插件的全局设置。 可以通过选择找到的全局设置插件**Jenkins > 管理 Jenkins > 配置系统**从左上角，如下面的屏幕截图中所示：

[![](jenkins-walkthrough-images/image13.png "可以通过选择 Jenkins 找到插件的全局设置 / 管理 Jenkins / 配置系统从左上分发角")](jenkins-walkthrough-images/image13.png#lightbox)

当选择此菜单选项时，您将进入**配置系统 [Jenkins]** 页。 此页包含部分配置 Jenkins 本身以及设置某些全局插件值。  下面的屏幕截图演示了此页的一个示例：

[![](jenkins-walkthrough-images/image14.png "此屏幕截图演示了此页的示例")](jenkins-walkthrough-images/image14.png#lightbox)

#### <a name="configuring-the-msbuild-plugin"></a>配置 MSBuild 插件

MSBuild 插件必须配置为使用 **/Library/Frameworks/Mono.framework/Commands/xbuild**编译 Visual Studio for Mac 解决方案和项目文件。 向下滚动**配置系统 [Jenkins]** 页上，直到**添加 MSBuild**按钮将出现，如下面的屏幕截图中所示：

 [![](jenkins-walkthrough-images/image15.png "配置系统 Jenkins 页面向下滚动，直到添加 MSBuild 按钮将出现")](jenkins-walkthrough-images/image15.png#lightbox)

单击此按钮，并填写**名称**并**路径**到**MSBuild**上显示的窗体的字段。 名称在**MSBuild**安装应该有意义的名称，同时**MSBuild 路径**应为路径`xbuild`，通常是 **/Library/框架 /Mono.framework/Commands/xbuild**。 我们通过单击保存或在页面底部的应用按钮保存所做的更改后，Jenkins 将能够使用`xbuild`来编译你的解决方案。

#### <a name="configuring-the-tfs-plugin"></a>配置 TFS 插件

本部分是如果你想要使用 TFS 进行源代码管理必需的。

为了使与 TFS 服务器，进行交互的 macOS 工作站[Team Explorer Everywhere](https://docs.microsoft.com/azure/devops/java/download-eclipse-plug-in/)必须安装在工作站上。 Team Explorer Everywhere 是跨平台命令行客户端包括用于访问 TFS 的 Microsoft 工具的一组。 Team Explorer Everywhere 可从 Microsoft 下载并安装三个步骤：

1. 将存档文件提取到用户帐户可以访问的目录。 例如，可能会将文件提取到 **~/tee**。
2. 配置要包含的文件夹，包含了在上述步骤中解压缩的文件的 shell 或系统路径。 例如，应用于对象的

    ```
    echo export PATH~/tee/:$PATH' >> ~/.bash_profile
    ```

3. 若要确认是否安装了 Team Explorer Everywhere，请打开终端会话中，并执行`tf`命令。 如果 tf 已正确配置，将在终端会话中看到以下输出：

    ```
    $ tf
    Team Explorer Everywhere Command Line Client (version 11.0.0.201306181526)

    Available commands and their options:
    ```

安装 TFS 的命令行客户端后，必须使用的完整路径配置 Jenkins`tf`命令行客户端。 向下滚动**配置系统 [Jenkins]** 页直到找到 Team Foundation Server 部分中，如以下屏幕截图中所示：

[![](jenkins-walkthrough-images/image17.png "配置系统 Jenkins 页面向下滚动，直到找到 Team Foundation Server 部分")](jenkins-walkthrough-images/image17.png#lightbox)

输入的完整路径`tf`命令，并单击**保存**按钮。

### <a name="configure-jenkins-security"></a>配置 Jenkins 安全性

首次安装，Jenkins 将具有安全性已禁用，因此可以为任何用户设置和运行任何类型的作业以匿名方式。 本部分介绍如何使用 Jenkins 用户数据库配置身份验证和授权配置安全性。

可以通过选择找到的安全设置**Jenkins > 管理 Jenkins > 配置全局安全性**，此屏幕截图中所示：

[![](jenkins-walkthrough-images/image18.png "可以通过选择 Jenkins 找到安全设置 / 管理 Jenkins / 配置全局安全性")](jenkins-walkthrough-images/image18.png#lightbox)

上**配置全局安全性**页上，选中**启用安全**复选框和**访问控制**窗体外观，类似于下面的屏幕截图：

[![](jenkins-walkthrough-images/image19.png "在配置全局安全性页上，选中启用安全复选框，并访问控制窗体外观，类似于以下屏幕截图")](jenkins-walkthrough-images/image19.png#lightbox)

切换的单选按钮**Jenkins 的用户数据库**中**安全领域部分**，并确保**允许用户注册**还检查，如中所示以下屏幕截图：

[![](jenkins-walkthrough-images/image20.png "切换在安全领域部分中，Jenkins 自己的用户数据库的单选按钮，并确保也选中允许用户注册")](jenkins-walkthrough-images/image20.png#lightbox)

最后，重新启动 Jenkins 并创建新的帐户。 创建的第一个帐户是根帐户，此帐户将自动提升为管理员。 导航回到**配置全局安全性**页上，并核对**矩阵基于安全**单选按钮。 应为根帐户授予完全访问权限，并匿名帐户应授予只读访问，如以下屏幕截图中所示：

[![](jenkins-walkthrough-images/image21.png "应为根帐户授予完全访问权限，并应为匿名帐户授予只读访问权限")](jenkins-walkthrough-images/image21.png#lightbox)

一旦保存这些设置并重新启动 Jenkins，将开启安全。

#### <a name="disabling-security"></a>禁用安全性

发生时忘记了的密码或 Jenkins 范围锁定，则可以通过执行以下步骤禁用安全性：

1. 停止 Jenkins。 如果使用 Jenkins.app，可以通过右键单击停靠面板中的 Jenkins.App 图标并从弹出菜单中选择退出来执行此操作：

    ![在停靠，并从弹出菜单中选择退出应用程序图标](jenkins-walkthrough-images/image19.png)

2. 打开文件 **~/.jenkins/config.xml**在文本编辑器中。
3. 更改的值`<usesecurity></usesecurity>`元素从`true`到`false`。
4. 删除`<authorizationstrategy></authorizationstrategy>`和`<securityrealm></securityrealm>`文件中的元素。
5. 重启 Jenkins。

## <a name="setting-up-a-job"></a>设置作业

在顶层，Jenkins 将组织构建软件转换所需的各种任务的所有*作业*。 为作业还指定了元数据与之关联，提供有关如何获取源代码、 生成的运行频率、 任何特殊的变量所需的构建，以及如何在生成失败时通知开发人员等生成的信息。

通过选择创建作业**Jenkins > 新作业**的菜单中的右上角，如以下屏幕截图中所示：

![](jenkins-walkthrough-images/image22.png "通过从右上角的菜单中选择新的 Jenkins 作业创建作业")

这将显示**新的作业 [Jenkins]** 页。 输入作业的名称，然后选择**生成自由格式的软件项目**单选按钮。 下面的屏幕截图显示了此示例：

![](jenkins-walkthrough-images/image23.png "输入作业的名称，然后选择生成自由格式的软件项目单选按钮")

单击**确定**按钮显示作业的配置页。 这应类似于以下屏幕截图：

![](jenkins-walkthrough-images/image24.png "这应类似于此屏幕截图")

Jenkins 将组织在位于以下路径的硬盘上的某个目录中的作业： **~/.jenkins/jobs/[JOB 名称]**

此文件夹包含的所有文件和项目特定于该作业，例如日志、 配置文件和需要进行编译的源代码。

一旦创建初始作业后，它必须与一个或多个以下配置：

- 必须指定源代码管理系统。
- 一个或多个*生成操作*必须添加到项目。 这些是步骤或生成应用程序所需的任务。
- 作业必须分配一个*生成触发器*– 一组指令通知频率 Jenkins 检索代码和生成的最终项目。

### <a name="configuring-source-code-control"></a>配置源代码管理

Jenkins does 的第一个任务是从源代码管理系统中检索的源代码。 Jenkins 支持许多流行的源代码代码管理系统立即可用。 本部分介绍两种受欢迎的系统，Git 和 Team Foundation Server。 以下部分更详细地讨论了每这些源代码管理系统。

#### <a name="using-git-for-source-code-control"></a>使用 Git 为源代码管理

如果使用 TFS 进行源代码管理[跳过](#using-tfs-for-source-code-management)此部分并转到下一部分使用的 TFS。

Jenkins 支持现成的 – 没有额外的插件所需的 Git。 若要使用 Git，请单击**Git**单选按钮和 Git 存储库中，输入的 URL，如以下屏幕截图中所示：

![](jenkins-walkthrough-images/image25.png "若要使用 Git，请单击 Git 单选按钮并输入 Git 存储库的 URL")

所做的更改保存后，Git 配置已完成。

#### <a name="using-tfs-for-source-code-management"></a>使用源代码管理 TFS

本部分仅适用于 TFS 用户。

单击**Team Foundation Server**单选按钮和 TFS 配置部分应显示，类似于以下屏幕截图中：

![](jenkins-walkthrough-images/image26.png "单击 Team Foundation Server 单选按钮和 TFS 配置部分应显示")

提供为 TFS 所需的信息。 以下屏幕截图显示完成的窗体的示例：

![](jenkins-walkthrough-images/image27.png "此屏幕截图显示完成的窗体的示例")

#### <a name="testing-the-source-code-control-configuration"></a>测试源代码控制配置

一旦配置了相应的源代码管理，请单击**保存**以保存所做的更改。 这将使你返回到作业，将类似于以下屏幕截图的主页上：

![](jenkins-walkthrough-images/image28.png "这将使你返回到作业，将类似于此屏幕截图的主页")

若要验证已正确配置源代码管理的最简单方法是手动触发的生成但不没有指定任何生成操作。 若要手动启动生成，该作业的主页已经有**立即生成**链接左侧，在菜单中，如下面的屏幕截图中所示：

![](jenkins-walkthrough-images/image29.png "若要手动启动生成，主页上的作业具有的立即生成链接上的左侧菜单中")

启动生成后，生成历史记录对话框中将显示一个闪烁的蓝色圆圈、 进度条、 内部版本号和生成开始，类似于下面的屏幕快照的时间：

![](jenkins-walkthrough-images/image30.png "启动生成后，生成历史记录对话框中显示一个闪烁的蓝色圆圈、 进度条、 内部版本号和生成开始的时间")

如果作业成功时，将显示一个蓝色圆。 如果作业失败时，将显示一个红色圆圈。

若要帮助进行故障排除可能作为生成的一部分出现的问题，Jenkins 将捕获所有作业的控制台输出。 若要查看控制台输出，请单击中的作业**生成历史记录**，然后在**控制台输出**左侧菜单中的链接。 以下屏幕截图显示**控制台输出**链接，以及一些成功的作业的输出：

![](jenkins-walkthrough-images/image31.png "此屏幕截图显示控制台输出链接，以及一些成功的作业的输出")

#### <a name="location-of-build-artifacts"></a>生成项目的位置

Jenkins 将检索到一个名为的特殊文件夹的完整源代码*工作区*。 可在文件夹上的以下位置找到此目录：

    ```
    ~/.jenkins/jobs/[JOB NAME]/workspace
    ```

工作区的路径将存储在名为一个环境变量`$WORKSPACE`。

可以通过导航到的登录页，一个作业，然后单击浏览在 Jenkins 中的工作区文件夹**工作区**左侧菜单中的链接。 以下屏幕截图显示了一个名为作业的工作区的示例**HelloWorld**:

![](jenkins-walkthrough-images/image32.png "此屏幕截图显示了一个名为 HelloWorld 作业的工作区的示例")

### <a name="build-triggers"></a>生成触发器

适用于启动在 Jenkins 中的生成多个不同的策略，这些参数称为*生成触发器*。 生成触发器可帮助决定何时启动作业，并生成项目的 Jenkins。 两个更常见的生成触发器是：

- **定期生成**– 此触发器使 Jenkins 以在指定的时间间隔，例如每隔两小时或在工作日的午夜启动作业。 生成将启动而不考虑是否在源代码存储库中进行了任何更改。
- **轮询 SCM** – 此触发器将轮询定期的源代码管理。 如果任何更改都已提交到源代码存储库，Jenkins 将启动新的生成。

轮询 SCM 是常用的触发器，因为当开发人员提交会导致生成中断的更改，它提供快速反馈。 这可用于一些最近提交的代码导致的问题，并允许开发人员解决该问题仍全新记住所做的更改时发出警报的团队。

定期生成通常用于创建应用程序的可分发给测试人员的版本。 例如，可能会定期生成安排为星期五晚上上，这样的 QA 团队成员可以测试与前一周的工作。

### <a name="compiling-a-xamarinios-applications"></a>编译 Xamarin.iOS 应用程序
Xamarin.iOS 项目可以在命令行中使用编译`xbuild`或`msbuild`。 Shell 命令将在运行 Jenkins 的用户帐户的上下文中执行。 非常重要的用户帐户有权访问预配配置文件，以便应用程序可以正确打包进行分发。 就可以将此 shell 命令添加到该作业配置页。

向下滚动到**生成**部分。 单击**添加生成步骤**按钮，然后选择**执行 shell**，如以下屏幕截图所示：

![](jenkins-walkthrough-images/image33.png "单击添加生成步骤按钮，然后选择执行 shell")

[!include[](~/tools/ci/includes/commandline-compile-of-xamarin-ios-ipa.md)]

### <a name="building-a-xamarinandroid-project"></a>生成 Xamarin.Android 项目

生成 Xamarin.Android 项目是在概念上非常类似于生成 Xamarin.iOS 项目。 若要从 Xamarin.Android 项目创建 APK，必须配置 Jenkins 以执行以下两个步骤：

- 编译使用 MSBuild 插件的项目
- 登录和 zip 对齐使用有效的 release 密钥存储 APK。

将在接下来的两部分更详细地介绍这两个步骤。

### <a name="creating-the-apk"></a>创建 APK

单击**添加生成步骤**按钮，然后选择**生成的 Visual Studio 项目或解决方案使用 MSBuild**，如以下屏幕截图中所示：

![](jenkins-walkthrough-images/image36.png "创建 APK 单击添加生成步骤按钮，并选择生成上的 Visual Studio 项目或解决方案使用 MSBuild")

一旦生成步骤添加到项目中，填写显示的窗体字段。 下面的屏幕截图是完成的窗体的一个示例：

![](jenkins-walkthrough-images/image37.png "一旦生成步骤添加到项目中，填写显示的窗体字段")

此生成步骤将执行`xbuild`中 **$WORKSPACE**文件夹。 MSBuild 生成文件设置为**Xamarin.Android.csproj**文件。 **命令行参数**指定目标的版本生成**PackageForAndroid**。 此步骤的产品将 APK 的以下位置：

    ```
    $WORKSPACE/[PROJECT NAME]/bin/Release
    ```

下面的屏幕截图显示了此 APK 的示例：

![](jenkins-walkthrough-images/image38.png "此屏幕截图显示了此 APK 的示例")

此 APK 不准备好进行部署，因为它没有签名使用专用密钥存储并必须对齐 zip。

#### <a name="signing-and-zipaligning-the-apk-for-release"></a>签名以及归档 APK 版本

签名以及归档 APK 是从技术上讲两个单独的任务执行的 Android SDK 中的两个单独的命令行工具。 但是，它是可以方便地在一个生成操作中执行它们。 有关签名以及归档 APK 的详细信息，请参阅有关准备 Android 应用程序版本的 Xamarin 的文档。

这两种命令要求可能不同项目到项目的命令行参数。 此外，密码时不应出现在控制台输出中生成正在运行的是其中一些命令行参数。 我们将在环境变量中存储其中一些命令行参数。 下表所述的环境变量所需的签名和/或 zip 对齐：

|环境变量|描述|
|--- |--- |
|KEYSTORE_FILE|这是用于对 apk 进行签名的密钥存储的路径|
|KEYSTORE_ALIAS|将使用对 APK 进行签名的密钥存储中的键。|
|INPUT_APK|创建的 APK `xbuild`。|
|SIGNED_APK|生成的已签名的 APK `jarsigner`。|
|FINAL_APK|这是 zip 对齐由生成的 APK `zipalign`。|
|STORE_PASS|这是用于访问的内容进行签名文件的密钥存储的密码。|

如所述的要求部分中，可以使用 EnvInject 插件在生成过程中设置这些环境变量。 作业均应具有新的生成步骤添加基于插入的环境变量，如下面的屏幕截图中所示：

![](jenkins-walkthrough-images/image39.png "作业均应具有新的生成步骤添加到基于插入的环境变量")

在中**内容属性**形成将显示的字段、 添加变量的环境，每行，一个采用以下格式：

    ```
    ENVIRONMENT_VARIABLE_NAME = value
    ```

下面的屏幕截图显示了所需的对 apk 进行签名的环境变量：

![](jenkins-walkthrough-images/image40.png "此屏幕截图显示了所需的对 apk 进行签名的环境变量")

请注意，某些环境变量的 APK 文件基于`WORKSPACE`环境变量。

最后一个环境变量是用于访问密钥存储的内容的密码： `STORE_PASS`。 密码是应被遮盖或日志文件中省略的敏感值。 EnvInject 插件可以配置为保护这些值，以便它们不显示在日志中。

紧靠**构建**作业配置的部分是**Build Environment**部分。 当**注入密码**切换复选框，才会出现某种形式的字段。 这些窗体字段用于捕获的名称和环境变量的值。 以下屏幕截图是添加的示例`STORE_PASS`环境变量：

![](jenkins-walkthrough-images/image41.png "这是屏幕截图添加 STOREPASS 环境变量的示例")

环境变量已初始化后下, 一步是添加生成步骤进行签名和压缩排列 APK。 立即生成步骤要插入的环境变量将是另一个后**执行 shell**将执行的命令生成`jarsigner`和`zipalign`。 每个命令将占用一行，如以下代码片段中所示：

    ```
    jarsigner -verbose -sigalg MD5withRSA -digestalg SHA1 -keystore $KEYSTORE_FILE -storepass $STORE_PASS -signedjar $SIGNED_APK $INPUT_APK $KEYSTORE_ALIAS
    zipalign -f -v 4 $SIGNED_APK $FINAL_APK
    ```

以下屏幕截图显示如何输入的示例`jarsigner`和`zipalign`到步骤的命令：

![](jenkins-walkthrough-images/image42.png "此屏幕截图显示了如何将输入步骤的 jarsigner 和 zipalign 命令的示例")

位置生成的所有操作后，它是很好的做法触发手动生成以验证一切正常工作。 如果生成失败，**控制台输出**应查看有关导致生成失败的原因的信息。

### <a name="submitting-tests-to-test-cloud"></a>提交到测试云测试

自动的测试可以提交到 Test Cloud 使用 shell 命令。 有关 Xamarin Test Cloud 中的测试运行设置的详细信息，请参阅本指南为使用[Xamarin.UITest](/appcenter/test-cloud/preparing-for-upload/uitest/)。

## <a name="summary"></a>总结

本指南中，我们引入了 Jenkins，作为在 macOS 上，在生成服务器并将其配置为编译和准备的版本的 Xamarin 移动应用程序。 我们以及多个插件以支持生成过程的 macOS 计算机上安装 Jenkins。 我们创建并配置将从 TFS 或 Git、 拉取代码，然后将该代码编译到发行就绪应用程序的作业。 我们还探讨了两种不同的方式来计划应运行作业。

## <a name="related-links"></a>相关链接

- [持续集成](~/tools/ci/index.md)
- [应用中心测试](https://docs.microsoft.com/appcenter/test-cloud/)
