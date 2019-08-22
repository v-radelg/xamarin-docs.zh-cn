---
title: Xamarin.iOS 中的 IPA 支持
description: 本文介绍如何创建可通过 Ad Hoc 分发用来部署应用程序的 IPA 文件，用于内部应用程序的测试或 In-House 分发。
ms.prod: xamarin
ms.assetid: D253C2DB-852E-6FC6-C9FD-574730B8DB19
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: c430d7771d443b80bec2a3e8a79565e2a7994a8b
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69887576"
---
# <a name="ipa-support-in-xamarinios"></a>Xamarin.iOS 中的 IPA 支持

本文介绍如何创建可通过 Ad Hoc 分发用来部署应用程序的 IPA 文件，用于内部应用程序的测试或内部分发。 

除了通过 iTunes App Store 发布应用程序进行出售，还可部署它用于以下用途：

- **Ad Hoc 测试** - iOS 应用程序最多可向 100 名用户（由特定 iOS 设备 UUID 标识）部署，用于 Alpha 和 Beta 测试目的。 请参阅[预配用于开发的 iOS 设备](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioning)文档，了解向 Apple 开发人员帐户添加测试 iOS 设备的相关详细信息，并请参阅 [Ad-Hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md) 指南，了解如何以此方法进行分发的相关详细信息。
- 内部/企业部署  - 可在公司内部部署 iOS 应用程序，这需要 Apple 开发人员企业计划的成员身份。 [内部分发](~/ios/deploy-test/app-distribution/in-house-distribution.md)指南中详细介绍了内部分发的相关详细信息。

在任一情况下，都必须创建 IPA 包（特殊类型的 zip 文件），并使用正确的分发配置文件进行数码签名。 本文介绍在 Mac 或 Windows 电脑上使用 iTunes 生成 IPA 包，并在 iOS 设备上进行安装的所需步骤。

<a name="iTunesMetadata" />

## <a name="the-itunesmetadataplist-file"></a>iTunesMetadata.plist 文件

在 iTunes Connect 中创建 iOS 应用程序（无论是用于在 iTunes App Store 中销售还是免费发布）时，开发人员可指定应用程序类型、子类型、版权声明、支持的 iOS 设备和必需设备功能等信息。

通过 Ad Hoc 分发或 In-House 分发传递的 iOS 应用程序需以某种方式支持此信息，使它在 iTunes 和用户设备上可见。 默认情况下，每次生成项目时都会创建小型 iTunesMetadata.plist 文件，并保存在项目目录中。

也可创建自定义 **iTunesMetadata.plist** 提供分发的其他信息。 若要了解有关此文件内容以及如何创建该文件的详细信息，请参阅 [iTunesMetadata.plist 内容](~/ios/deploy-test/app-distribution/itunesmetadata.md#iTunesMetadata_contents)和[创建 iTunesMetadata.plist 文件](~/ios/deploy-test/app-distribution/itunesmetadata.md#iTunesMetadata_creating)文档。

<a name="iTunesArtwork" />

## <a name="itunes-artwork"></a>iTunes 图稿

通过非 App Store 方式传递应用时，还需要包括在 iTunes 中用于表示应用程序的 512x512 和 1024x1024 图像。

若要指定 iTunes 图稿，请执行以下操作：

1. 在“解决方案资源管理器”  中，双击 **Info.plist** 文件，打开它进行编辑。
2. 滚动到编辑器的“iTunes 图稿”  分区。
3. 若有任何图像缺失，请单击编辑器中的缩略图，从“打开文件”  对话框中选择所需 iTunes 图稿的图像文件，然后单击“确定”  或“打开”  按钮。
4. 重复此步骤，直到已为应用程序指定所需的所有图像。

请参阅 [iTunes 图稿](~/ios/app-fundamentals/images-icons/app-icons.md)文档，了解详细信息。

<a name="createipa" />

## <a name="creating-an-ipa"></a>创建 IPA

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

创建 IPA 现已内置于新的发布工作流。 为此，请按照下面的指示存档应用、对其签名并保存 IPA。

在开始针对跨平台解决方案创建 IPA 之前，确保已选择 iOS 项目作为启动项目：

![](ipa-support-images/setasstartup.png "已选择 iOS 项目作为启动项目")

### <a name="build-your-archive"></a>生成存档

若要生成 IPA，需要创建应用程序发布版本的存档  。 该存档包含应用及其相关标识信息。


1. 在 Visual Studio for Mac 中选择“发布 | 设备”  配置： !

    ![](ipa-support-images/buildxs01new.png "选择“发布 | 设备”配置")

1. 在“生成”  菜单中，选择“存档以供发布”  ：

    ![](ipa-support-images/buildxs02new.png "选择“存档以供发布”")

1. 存档创建完成后，将显示“存档”  视图：

    ![](ipa-support-images/buildxs03new.png "将显示“存档”视图")


### <a name="sign-and-distribute-your-app"></a>签名和分发应用

每次生成用于存档的应用程序时，都会自动打开“存档视图”  ，显示所有已存档的项目（按解决方案分组）。 默认情况下，此视图只显示当前打开的解决方案。 若要查看拥有存档的所有解决方案，请单击“显示所有存档”  选项。

建议保留部署到客户（Ad-Hoc 或 In-House 部署）的存档，以便之后可代表生成的任何调试信息。

请注意，对于非 App Store 版本，**iTunesMetadata.plist** 文件和 iTunes 图稿集将自动包含在 IPA 中（如果在存档中找到）。

对应用进行签名并准备分发：


1. 选择“签名并分发...”  按钮，如下所示：

    ![](ipa-support-images/buildxs04new.png "选择“签名和分发...”")

1. 这将打开发布向导。 选择 **Ad-Hoc** 或“企业”  (In-House) 分发渠道来创建包：

    ![](ipa-support-images/distribute01.png "选择“临时”或“企业内部”分发")

1. 在“配置文件”屏幕上，选择签名标识和对应的配置文件，或使用其他标识重新签名：

    ![](ipa-support-images/distribute02.png "选择签名标识和相应的预配配置文件")

1. 验证包的详细信息，然后单击“发布”  ：

    ![](ipa-support-images/distribute03.png "验证包的详细信息")

1. 最后，将 IPA 保存到计算机：

    ![](ipa-support-images/distribute04.png "将 IPA 保存到计算机")


### <a name="building-via-the-command-line-on-mac"></a>（在 Mac 上）通过命令行生成

在某些情况下，如在 CI 环境中，可能需要通过命令行生成 IPA。 为此，请按照以下步骤进行操作：


1. 确保已选中“项目选项”>“iOS IPA 选项”>“包含 iTunes 图稿图像”  ，并且已选中“生成 Ad-Hoc/企业包 (IPA)”  ：

    ![](ipa-support-images/imagexs04.png "添加 iTunesArtwork 图像并选中“生成临时/企业包 IPA”")

    如果愿意，可转而在文本编辑器中编辑 **.csproj** 文件，并向将用于生成应用的配置的 `PropertyGroup` 手动添加两个对应属性：

    ```xml
    <BuildIpa>true</BuildIpa>
    <IpaIncludeArtwork>false</IpaIncludeArtwork>
    ```

1. 如果要包含可选的 **iTunesMetadata.plist** 文件，请单击“...”  选项，从列表选择它，然后单击“确定”  按钮：

     ![](ipa-support-images/imagexs03.png "从列表中选择 iTunesMetadata.plist")

1. 直接调用 msbuild  并在命令行上传递此属性：

    ```bash
    /Library/Frameworks/Mono.framework/Commands/msbuild YourSolution.sln /p:Configuration=Ad-Hoc /p:Platform=iPhone /p:BuildIpa=true
    ```

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

在创建并选择了预配配置文件、创建了可选的 iTunesMetadata.plist  文件，以及在 Visual Studio 中设置了 iTunes Artwork 后，便可以生成 IPA 以供分发。 接下来，需要配置项目。 请执行以下操作：

1. 在“解决方案资源管理器”  中，右键单击 Xamarin.iOS 项目名称，选择“属性”  将其打开进行编辑：

    ![](ipa-support-images/imagevs01.png "选择“属性”")

2. 选择“iOS IPA 选项”  ，然后从“配置”  下拉列表选择“Ad-Hoc”  ：

    ![](ipa-support-images/imagevs02.png "从“配置”下拉列表选择“临时”")

    > [!NOTE]
    > Ad-Hoc 配置可能不适用于较新的 Xamarin.iOS 项目。 如果不可用，请选择“发布”  配置。

3. 如果要包含可选 **iTunesMetadata.plist** 文件，请单击“...”  选项，从列表中选择它，然后单击“打开”  按钮：

    ![](ipa-support-images/imagevs03.png "从列表中选择 iTunesMetadata.plist")

4. 可选择指定 IPA 的**包名称**，若未指定，则将使用与 Xamarin.iOS 项目相同的名称。
5. 保存对“项目属性”所做的更改。
6. 如果可用，从“生成配置”  下拉列表选择“Ad Hoc”  。 否则，选择“发布”  ：

    ![](ipa-support-images/imagevs05.png "从“生成配置”下拉列表选择“临时”")

7. 生成项目以创建 IPA 包。
8. IPA 将在“Bin”>“iOS 设备”>“Ad Hoc”（或 Release）  文件夹中生成：

    ![](ipa-support-images/imagevs06.png "文件资源管理器中的 IPA")

-----

<a name="Customizing-the-IPA-Location" />

## <a name="customizing-the-ipa-location"></a>自定义 IPA 位置

已添加新的 **MSBuild** 属性 `IpaPackageDir`，使自定义 **.ipa** 文件输出位置更加轻松。 如果将 `IpaPackageDir` 设为自定义位置，则 **.ipa** 文件将放入该位置，而不是默认时间戳的子目录中。 创建依赖特定目录路径才能正常工作的自动生成，如用于持续集成 (CI) 生成的生成，此操作会很有帮助。

通过几种可能的方法使用新属性：

例如，若要将 **.ipa** 文件输出到旧的默认目录（正如在 Xamarin.iOS 9.6 和更低版本中），可以使用以下其中一种方法将 `IpaPackageDir` 属性设为 `$(OutputPath)`。 两种方法都与所有 Unified API Xamarin.iOS 内部版本兼容，包括 IDE 版本，以及使用 msbuild、xbuild 或 mdtool 的命令行版本    ：

- 第一个选项是，在 **MSBuild** 文件的 `<PropertyGroup>` 元素内，设置 `IpaPackageDir` 属性。 例如，可将以下 `<PropertyGroup>` 添加到 iOS 应用项目 **.csproj** 文件的底部（右 `</Project>` 标记前）：

    ```xml
    <PropertyGroup>
        <IpaPackageDir>$(OutputPath)</IpaPackageDir>
    </PropertyGroup>
    ```

- 更好的方法是将 `<IpaPackageDir>` 元素添加到现有 `<PropertyGroup>` 的底部，后者对应用于生成 **.ipa** 文件的配置。 此方法更好，因为会在 iOS IPA 选项项目属性页上使用计划设置针对将来的兼容性准备项目。 如果当前使用 `Release|iPhone` 配置生成 **.ipa** 文件，完整的已更新属性组可能类似如下所示：

    ```xml
    <PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Release|iPhone' ">
        <Optimize>true</Optimize>
        <OutputPath>bin\iPhone\Release</OutputPath>
        <ErrorReport>prompt</ErrorReport>
        <WarningLevel>4</WarningLevel>
        <ConsolePause>false</ConsolePause>
        <CodesignKey>iPhone Developer</CodesignKey>
        <MtouchUseSGen>true</MtouchUseSGen>
        <MtouchUseRefCounting>true</MtouchUseRefCounting>
        <MtouchFloat32>true</MtouchFloat32>
        <CodesignEntitlements>Entitlements.plist</CodesignEntitlements>
        <MtouchLink>SdkOnly</MtouchLink>
        <MtouchArch>;ARMv7, ARM64</MtouchArch>
        <MtouchHttpClientHandler>HttpClientHandler</MtouchHttpClientHandler>
        <MtouchTlsProvider>Default</MtouchTlsProvider>
        <PlatformTarget>x86&</PlatformTarget>
        <BuildIpa>true</BuildIpa>
        <IpaPackageDir>$(OutputPath</IpaPackageDir>
    </PropertyGroup>
    ```

另一种用于 msbuild 或 xbuild 命令行生成的方法是添加 `/p:` 参数来设置 `IpaPackageDir` 属性   。 请注意，在这种情况下，**msbuild** 不会展开传入到命令行的 `$()` 表达式，因此不能使用 `$(OutputPath)` 语法。 必须改为提供完整路径名称。 Mono 的 xbuild  命令确实会展开 `$()` 表达式，但仍建议使用完整路径名称，因为 xbuild  已因支持 msbuild 的[跨平台版本  ](https://www.mono-project.com/docs/about-mono/releases/5.0.0/#msbuild) 而被弃用。

使用此方法的完整示例可能与 Windows 上的以下示例类似：

```bash
msbuild /p:Configuration="Release" /p:Platform="iPhone" /p:ServerAddress="192.168.1.3" /p:ServerUser="macuser" /p:IpaPackageDir="%USERPROFILE%\Builds" /t:Build SingleViewIphone1.sln
```

或 Mac 上以下示例：

```bash
msbuild /p:Configuration="Release" /p:Platform="iPhone" /p:IpaPackageDir="$HOME/Builds" /t:Build SingleViewIphone1.sln
```

<a name="installipa" />

## <a name="installing-an-ipa-using-itunes"></a>使用 iTunes 安装 IPA

生成的 IPA 包可以传递给测试用户，以便在其 iOS 设备上安装或将其进行装运，用于企业部署。 无论选择哪种方法，最终用户都将在其 Mac 或 Windows 电脑上的 iTunes 应用程序中，通过双击 IPA 文件或将文件拖动到打开的 iTunes 窗口来安装包。

新的 iOS 应用程序将显示在“我的应用”  部分，在其中右键单击应用程序即可获取它的相关信息：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

 ![](ipa-support-images/installxs01.png "“我的应用”部分中的新 iOS 应用程序")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

 ![](ipa-support-images/installvs01.png "“我的应用”部分中的新 iOS 应用程序")

-----

用户现可将 iTunes 与设备同步，以安装新的 iOS 应用程序。

<a name="Summary" />

## <a name="summary"></a>总结

本文涵盖了为非 App Store 版本准备 Xamarin.iOS 应用程序所需的设置。 介绍了如何创建 IPA 包，以及如何在最终用户的 iOS 设备上安装生成的 iOS 应用程序，用于测试或 In-House 分发。


## <a name="related-links"></a>相关链接

- [App Store 分发](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)
- [在 iTunes Connect 中配置应用](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [发布到 App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)
- [内部分发](~/ios/deploy-test/app-distribution/in-house-distribution.md)
- [Ad Hoc 分发](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)
- [iTunesMetadata.plist 文件](~/ios/deploy-test/app-distribution/itunesmetadata.md)
- [疑难解答](~/ios/deploy-test/troubleshooting.md)
- [iTunes 图稿](~/ios/app-fundamentals/images-icons/app-icons.md#itunes)
- [开发和分发企业应用 (Apple)](https://help.apple.com/xcode/mac/current/#/devba5e7054d)
- [分发企业应用（WWDC 视频）](https://developer.apple.com/videos/play/wwdc2014/705/)
