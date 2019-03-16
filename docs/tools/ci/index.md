---
title: Xamarin 持续集成简介
description: 本文档所链接到指南描述与 Xamarin 持续集成。 链接的内容提供了持续集成的概述，并讨论了应用中心的生成、 TeamCity 和 Jenkins。
ms.prod: xamarin
ms.assetid: 99484E96-DC69-4697-8BBB-1B44C5CBB5ED
author: lobrien
ms.author: laobri
ms.date: 10/23/2018
---

# <a name="continuous-integration-with-xamarin"></a>与 Xamarin 持续集成

> [!Video https://youtube.com/embed/wXgnh2Q7Uv8]

## <a name="introduction-to-continuous-integrationtoolsciintro-to-cimd"></a>[持续集成简介](~/tools/ci/intro-to-ci.md)

本节介绍了持续集成所涉及的不同组件及其关系。 它概述了以下特定部分中讨论的持续集成环境。

## <a name="devops-with-xamarintoolscidevopsmd"></a>[DevOps与Xamarin](~/tools/ci/devops.md)

本节介绍了 Azure 和 Visual Studio 中哪些 DevOps 功能可以与 Xamarin 项目一起使用。

## <a name="working-with-continuous-integration-environments"></a>使用持续集成环境

### <a name="build-xamarin-apps-with-azure-pipelineshttpsdocsmicrosoftcomazuredevopspipelineslanguagesxamarin"></a>[使用 Azure 管道构建 Xamarin 应用程序](https://docs.microsoft.com/azure/devops/pipelines/languages/xamarin/)

使用 Azure 管道自动构建面向 Android 和 iOS 的 Xamarin 应用。

### <a name="build-xamarin-apps-using-app-centerhttpsdocsmicrosoftcomappcenterbuildxamarin"></a>[使用 App Center 构建 Xamarin 应用](https://docs.microsoft.com/appcenter/build/xamarin/)

可以直接在 GitHub、 Azure DevOps 或 Bitbucket 上使用 App Center 构建 Xamarin.iOS 和 Xamarin.Android 的解决方案。

### <a name="build-xamarin-apps-with-teamcitytoolsciteamcitymd"></a>[使用 TeamCity 构建 Xamarin 应用](~/tools/ci/teamcity.md)

本指南介绍使用 TeamCity 编译移动应用，然后将它们提交到 App Center Test 所涉及的步骤。

### <a name="build-xamarin-apps-with-jenkinstoolscijenkins-walkthroughmd"></a>[使用 Jenkins 构建 Xamarin 应用](~/tools/ci/jenkins-walkthrough.md)

本指南说明了如何将 Jenkins 设置为持续集成服务器，并自动编译用 Xamarin 创建的移动应用程序。 它描述了如何在 OS X 上安装Jenkins、配置它，并设置作业以在新的更改提交到版本控制系统时编译 Xamarin.iOS 和 Xamarin.Android 应用程序。
