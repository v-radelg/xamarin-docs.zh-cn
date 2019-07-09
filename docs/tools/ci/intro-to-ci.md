---
title: Xamarin 持续集成简介
description: 本文档介绍了与 Xamarin 持续集成。 它讨论了版本控制和各种持续集成环境。
ms.prod: xamarin
ms.assetid: C034200E-2947-4309-9DDD-80DAC505C43F
author: lobrien
ms.author: laobri
ms.date: 07/19/2017
ms.openlocfilehash: 48dd2e1f2f5dd9ffdad0b726c5066a9a968d2396
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650538"
---
# <a name="introduction-to-continuous-integration-with-xamarin"></a>Xamarin 持续集成简介

_持续集成是一种软件工程做法在其中自动的生成编译并 （可选） 测试应用程序代码中添加或更改项目的版本控制存储库中的开发人员时。本文将讨论持续集成的一般概念以及一些可用的选项用于持续集成与 Xamarin 项目。_

很常见的开发人员可以并行工作的软件项目。 在某些时候，就必须将所有这些集成到一个工作的并行流 o d e b 最终产品构成。 在软件开发的早期阶段，项目中，这是一个困难，而且有风险的过程结束时执行此集成。

持续集成 (CI) 通过将每个开发人员更改合并到连续地的通用代码基来避免这种复杂性，通常只要任何开发人员签入到项目的更改共享代码存储库。 每个签入触发自动的生成并运行自动的测试以验证新引入的代码没有破坏任何现有代码。  这样一来，CI 立即显示错误和问题，并确保所有团队成员都保持最新彼此的工作。 这会导致提供一致性和稳定的基本代码。

持续集成系统有两个主要部分：

- **版本控制**– 版本控制 (VC)，也称为源代码管理或源代码管理，将所有项目的代码合并到单个共享存储库，并向每个文件中保留每个更改的完整历史记录。 此存储库，通常称为*主线*或*master*分支中，包含将最终用于生成生产版本还是发行版本的应用的源代码。 有许多开放源代码和有关此任务中，通常情况下允许团队或个人分支的代码复制到辅助分支，即可以进行很多更改，或进行实验，而无需对主分支的风险的商业产品。 一旦辅助分支中的更改进行验证，然后可一起合并回主分支。
- **持续集成服务器**– 持续集成服务器是负责收集的所有项目的项目 （源代码、 图像、 视频、 数据库、 自动的测试等），编译应用程序中，并运行自动的测试。 同样，有许多开放源代码和商业 CI 服务器工具。

开发人员通常具有一个或多个分支的工作副本上他们的工作站，最初完成工作。 一组适当的工作完成后，所做的更改"签入"或者"已提交"到相应的分支，将其传播到其他开发人员的工作副本。 这是一个团队如何确保它们正在相同的代码。

同样，通过持续集成，提交更改的行为会导致 CI 服务器生成项目，并运行自动的测试以验证对源代码的正确性。 如果没有生成错误或测试失败，CI 服务器将通知相应的开发人员 （通过电子邮件、 IM、 Twitter、 Growl，等） 使他或她可以更正该问题。 （CI 服务器可以甚至拒绝提交如果发生故障，这称为"封闭签入"。）

下图说明了此过程：

[![](intro-to-ci-images/intro01-small.png "此图描述了此过程")](intro-to-ci-images/intro01.png#lightbox)

移动应用程序引入了用于持续集成独特的挑战。 应用可能需要如 GPS 或照相机才可在物理设备上的传感器。 此外，模拟器或仿真程序是仅可大概了解硬件和隐藏，也会掩盖问题。 最后，有必要测试移动应用程序以使您确信它是真正的客户准备好的真实硬件上。

[App Center Test](https://docs.microsoft.com/appcenter/test-cloud)通过测试应用程序直接在数百个物理设备上的解决了这个特定的问题。 开发人员编写允许进行功能强大的 UI 测试的自动的验收测试。 这些测试上传到 App Center 后, CI 服务器可以运行它们自动作为 CI 过程的一部分如以下关系图中所示：

[![](intro-to-ci-images/intro02-small.png "后这些测试上传到 App Center 时，CI 服务器可以运行这些自动作为 CI 过程的一部分，在此图中所示")](intro-to-ci-images/intro02.png#lightbox)

## <a name="components-of-continuous-integration"></a>持续集成的组件

没有设计为支持 CI 的商业和开放源代码工具的广泛生态系统。 本部分介绍几个最常见的。

### <a name="version-control"></a>版本控制

#### <a name="azure-devops-and-team-foundation-server"></a>Azure DevOps 和 Team Foundation Server

[Azure DevOps](https://azure.microsoft.com/services/devops/)并[Team Foundation Server](https://visualstudio.microsoft.com/tfs/) (TFS) 是 Microsoft 的协作工具，用于持续集成生成服务、 跟踪任务、 敏捷规划和报告工具和版本控制。 使用版本控制，Azure DevOps 和 TFS 可以处理与自己的系统 （Team Foundation 版本控制或 TFVC） 或托管在 GitHub 上的项目。

- Azure DevOps 提供通过云服务。 其主要优点是它不需要专用的硬件或基础结构，并可从任何位置通过 web 浏览器和流行的开发工具如 Visual Studio 中，使其具有吸引力的地理上分散的团队. 它是免费的 5 个开发人员团队或更少之后可以购买哪些额外的许可证以适应团队处于发展阶段。
- TFS 设计为在本地 Windows 服务器和通过本地网络或与该网络的 VPN 连接访问。 其主要优点是你完全控制生成服务器的配置和可以安装所需的任何其他软件或服务。 TFS 有适用于小型团队免费的入门级 Express 版本。

TFS 和 Azure DevOps 与 Visual Studio 紧密集成，并允许开发人员可以执行许多版本控制和便利的单个 IDE 中从 CI 任务。 Team Explorer Everywhere 适用于 Eclipse 插件 （见下文） 也是可用的。 Visual studio for Mac[的 TFVC 可用预览](/visualstudio/mac/tf-version-control/)。

[Azure DevOps 管道](https://docs.microsoft.com/azure/devops/pipelines/languages/xamarin/)直接支持 Xamarin 项目，在其中创建目标 （Android、 iOS 和 Windows） 您希望每个平台的生成定义。 每个生成定义需要相应的 Xamarin 许可证。 也可以连接本地，Xamarin 支持 TFS 生成服务器到 Azure DevOps 实现此目的。 通过此设置，要排队发送至 Azure DevOps 的生成将委派给本地服务器。 有关详细信息，请参阅[生成和发布代理](https://docs.microsoft.com/azure/devops/pipelines/agents/agents)。 或者，可以使用另一个生成工具，如 Jenkins 或团队城市。

Visual Studio、 Azure DevOps 和 Team Foundation Server，请参阅的所有应用程序生命周期管理 (ALM) 功能的详尽总结[适用于 Xamarin 应用的 DevOps](https://docs.microsoft.com/visualstudio/cross-platform/application-lifecycle-management-alm-with-xamarin-apps)。

#### <a name="team-explorer-everywhere"></a>Team Explorer Everywhere

[Team Explorer Everywhere](https://docs.microsoft.com/azure/devops/java/download-eclipse-plug-in/) Team Foundation Server 和 Azure DevOps 的强大功能引入到 Visual Studio 外部开发团队。 它允许开发人员能够适用于 OS X 和 Linux 从 Eclipse 或跨平台命令行客户端连接到在本地或云中的团队项目。 提供完整的 Team Explorer Everywhere 到版本控制 （包括 Git），访问工作项和生成的非 Windows 平台的功能。

#### <a name="git"></a>Git

[Git](http://git-scm.com)是一种受欢迎的开源版本控制解决方案，最初开发用于管理 Linux 内核的源代码。 它是一个非常快速、 灵活的系统，常用的各种规模的软件项目。 它可轻松扩展不佳的 Internet 访问的单个开发人员到遍布全球的大型团队。 Git 还使分支变得非常简单，这反过来可以鼓励开发风险降到最低的并行流。

Git 可以操作完全通过 web 浏览器，或[GUI 客户端](http://git-scm.com/downloads/guis)Linux、 Mac OSX 和 Windows 上运行。 它是免费的公共存储库;专用存储库需要[付费计划](https://github.com/pricing)。

当前版本的 Visual Studio 的 Windows 和 Mac 提供对 Git 的本机支持。 Microsoft 提供了[适用于 Git 的可下载扩展](http://visualstudiogallery.msdn.microsoft.com/abafc7d6-dcaa-40f4-8a5e-d6724bdb980c)较旧版本的 Visual Studio。 如上文所述，Azure DevOps 和 TFS 可以使用 Git 进行版本控制而不是 TFVC。

#### <a name="subversion"></a>Subversion

[Subversion](http://subversion.apache.org) (SVN) 是一直在使用 2000年以来一个受欢迎的开源版本控制系统。 SVN 在 OS X、 Windows、 FreeBSD、 Linux 和 Unix 的所有最新版本上运行。 Visual Studio for Mac 具有对 SVN 的本机支持。 有 SVN 支持引入 Visual Studio 的第三方扩展。

### <a name="continuous-integration-environments"></a>持续集成环境

设置持续集成环境意味着将与生成服务结合使用版本控制系统。  对于后者，两个最常见的是：

- [Azure 管道](https://docs.microsoft.com/azure/devops/pipelines/)是 Azure DevOps 和 TFS 的生成系统。 它与 Visual Studio 中，这样便于开发人员来触发它生成自动运行测试，并查看结果紧密集成。
- Jenkins 是插件的一个开放源代码 CI 服务器具有来支持所有类型的软件开发的丰富生态系统。 它运行在 Windows 和 Mac OS x。 Jenkins 未与任何特定的 IDE 集成。 相反，它的配置和管理通过 web 界面。 Jenkins CI 也很容易安装和配置从而吸引人于小型团队。

可以使用 TFS/Azure DevOps 本身，或可以与 TFS/Azure DevOps 或 Git 结合使用 Jenkins，如以下各节中所述。

#### <a name="azure-devops-and-team-foundation-server"></a>Azure DevOps 和 Team Foundation Server

如所述，Azure DevOps 和 Team Foundation Server 提供了这两个版本控制和生成服务。 生成服务始终需要 Xamarin 业务或企业版的许可证，每个目标平台。

使用 Azure DevOps，在创建每个目标平台的单独的生成定义，并输入那里相应的许可证。 一旦配置，Azure DevOps 将运行生成，并在云中测试。 请参阅[Azure 管道](https://docs.microsoft.com/azure/devops/pipelines/)的更多详细信息。

使用 Team Foundation Server，你配置生成计算机特定的目标平台的如下所示：

- **Android 和 Windows:** 安装 Visual Studio 和 Xamarin 工具 （适用于 Android 和 Windows 这两个），并使用 Xamarin 许可证配置。 还有必要移动到 TFS 在其中生成代理在服务器上的共享位置 Android SDK 可以找到它。 有关详细信息，请参阅[配置 TFVC](https://docs.microsoft.com/azure/devops/repos/tfvc/overview)。
- **iOS 和 Xamarin:** 在具有适当的许可的 Windows 服务器上安装 Visual Studio 和 Xamarin 工具。 然后安装 Visual Studio for Mac 的可访问网络的 Mac OS X 计算机上，将作为生成主机，并创建最终应用包 (IPA 适用于 iOS、 OS x 应用)。

下图说明了此拓扑：

[![](intro-to-ci-images/intro03-small.png "此图描述了此拓扑")](intro-to-ci-images/intro03.png#lightbox)

还有可能要链接到 Azure DevOps 项目的本地 TFS 服务器，以便 Azure DevOps 生成委托给本地服务器。 有关详细信息，请参阅[生成和发布代理](https://docs.microsoft.com/azure/devops/pipelines/agents/agents/)。

#### <a name="azure-devops-and-jenkins"></a>Azure DevOps 和 Jenkins

如果您使用 Jenkins 生成您的应用程序，可以在 Azure DevOps 或 Team Foundation Server 中存储你的代码并继续使用 Jenkins CI 生成的。 将代码推送到团队项目的 Git 存储库或在签入代码到 TFVC 时，可以触发 Jenkins 生成。 有关详细信息，请参阅[Jenkins 与 Azure DevOps](https://docs.microsoft.com/azure/devops/service-hooks/services/jenkins)。

[![](intro-to-ci-images/intro04-small.png "如果您使用 Jenkins 生成您的应用程序，可以 Azure DevOps 或 Team Foundation Server 中存储你的代码并继续使用 Jenkins CI 生成")](intro-to-ci-images/intro04.png#lightbox)

#### <a name="git-and-jenkins"></a>Git 和 Jenkins

OS X 基于可以完全是另一个常见的 CI 环境。 此方案涉及到使用 Git 进行源代码管理和 Jenkins 生成服务器。 这两种运行使用 Visual Studio 一台 Mac OS X 计算机上安装的 Mac。 这是非常类似于 Azure DevOps + Jenkins 环境上一节所述：

[![](intro-to-ci-images/intro05-small.png "这是非常类似于 Azure DevOps + 上一节所述的 Jenkins 环境")](intro-to-ci-images/intro05.png#lightbox)

> [!IMPORTANT]
> **Jenkins 是[不受 Microsoft](~/cross-platform/troubleshooting/questions/xamarin-jenkins.md)。**
