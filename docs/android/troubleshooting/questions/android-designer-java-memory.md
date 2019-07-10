---
title: 调整 Android Designer 的 Java 内存参数
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 62FAF21C-8090-4AF3-9D88-05A4CFCAFFDC
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/02/2018
ms.openlocfilehash: c42be49aff56453bb1bc3b3c732cad151ccd62e0
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67674459"
---
# <a name="adjusting-java-memory-parameters-for-the-android-designer"></a>调整 Android Designer 的 Java 内存参数

启动时使用的默认内存参数`java`处理有关 Android 设计器可能与某些系统配置不兼容。

从 Xamarin Studio 5.7.2.7 （和更高版本、 Visual Studio for Mac） 和 Visual Studio Tools for Xamarin 3.9.344，可以基于每个项目自定义这些设置。

## <a name="new-android-designer-properties-and-corresponding-java-options"></a>新的 Android 设计器属性和相应的 Java 选项

下面的属性名称对应于所指示的 java[命令行选项](http://docs.oracle.com/javase/7/docs/technotes/tools/windows/java.html)

- **AndroidDesignerJavaRendererMinMemory** -Xms

- **AndroidDesignerJavaRendererMaxMemory** -Xmx

- **AndroidDesignerJavaRendererPermSize** -XX:MaxPermSize


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  在 Visual Studio 中打开解决方案。

2.  在解决方案资源管理器中选择一个地的每个 Android 项目，然后单击[显示所有文件](https://docs.microsoft.com/previous-versions/visualstudio/visual-studio-2008/4afxey9h(v=vs.90))两次上每个项目。 你可以跳过项目不包含任何`.axml`布局文件。 此步骤将确保每个项目目录包含`.csproj.user`文件。

3.  退出 Visual Studio。

4.  找到`.csproj.user`为每个步骤 2 中的项目文件。

5.  编辑每个`.csproj.user`文本编辑器中的文件。

6.  添加 any 或 all 中的新 Android 设计器的内存属性`<PropertyGroup>`元素。 可以使用现有`<PropertyGroup>`或新建一个。 下面是一个完整示例`.csproj.user`文件，其中包含所有 3 个属性设置为其默认值：

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <Project ToolsVersion="12.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
       <PropertyGroup>
         <ProjectView>ProjectFiles</ProjectView>
       </PropertyGroup>
       <PropertyGroup>
         <AndroidDesignerJavaRendererMinMemory>128m</AndroidDesignerJavaRendererMinMemory>
         <AndroidDesignerJavaRendererMaxMemory>750m</AndroidDesignerJavaRendererMaxMemory>
         <AndroidDesignerJavaRendererPermSize>350m</AndroidDesignerJavaRendererPermSize>
       </PropertyGroup>
    </Project>
    ```

7.  保存并关闭所有更新后的`.csproj.user`文件。

8.  重新启动 Visual Studio 并重新打开你的解决方案。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1.  打开你的解决方案在 Visual Studio for Mac，以确保解决方案目录包含`.userprefs`文件。

2.  退出 Visual Studio for mac。

3.  找到`.userprefs`的解决方案目录中的文件。

4.  编辑`.userprefs`文本编辑器中的文件。

5.  找到具有以下格式的现有 XML 元素。 此元素名称的最后一部分将与你的项目的名称匹配："AndroidApplication1"在此示例中：

    ```xml
    <MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >
    ```

6.  如果`<MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >`元素不存在，在包含它的任意位置创建它`<Properties>`元素。 请确保"AndroidApplication1"替换为你的项目的名称。

7.  将任何或所有的新的 Android 设计器的内存属性添加为元素的属性。 下面是一个完整示例`.userprefs`文件，其中包含所有 3 个属性设置为其默认值：

    ```xml
    <Properties StartupItem="AndroidApplication1\AndroidApplication1.csproj">
      <MonoDevelop.Ide.Workspace ActiveConfiguration="Debug" PreferredExecutionTarget="Android.SelectDevice" />
      <MonoDevelop.Ide.Workbench />
      <MonoDevelop.Ide.DebuggingService.Breakpoints>
        <BreakpointStore />
      </MonoDevelop.Ide.DebuggingService.Breakpoints>
      <MonoDevelop.Ide.DebuggingService.PinnedWatches />
      <MonoDevelop.Ide.ItemProperties.AndroidApplication1 AndroidDesignerJavaRendererMinMemory="128m" AndroidDesignerJavaRendererMaxMemory="750m" AndroidDesignerJavaRendererPermSize="350m" />
    </Properties>
    ```

8.  重复步骤 5-7 为每个 Android 项目的解决方案中的包含任何`.axml`布局文件。 (即，添加一个`<MonoDevelop.Ide.ItemProperties.ProjectName>`元素为每个项目。)

9.  保存并关闭`.userprefs`文件。

10. 重启 Visual Studio for Mac，并重新打开你的解决方案。

-----

