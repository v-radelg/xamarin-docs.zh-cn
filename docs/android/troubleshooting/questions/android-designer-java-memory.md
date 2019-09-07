---
title: 调整 Android Designer 的 Java 内存参数
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 62FAF21C-8090-4AF3-9D88-05A4CFCAFFDC
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/02/2018
ms.openlocfilehash: 4a3f3849725f0d3b8e8bc8d43c1cd3f87f044616
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70761051"
---
# <a name="adjusting-java-memory-parameters-for-the-android-designer"></a>调整 Android Designer 的 Java 内存参数

为 Android 设计器启动`java`进程时使用的默认内存参数可能与某些系统配置不兼容。

从 Xamarin Studio 5.7.2.7 （及更高版本、Visual Studio for Mac）以及针对 Xamarin 3.9.344 的 Visual Studio Tools 开始，可以基于每个项目自定义这些设置。

## <a name="new-android-designer-properties-and-corresponding-java-options"></a>新的 Android 设计器属性和对应的 Java 选项

以下属性名称对应于指定的 java[命令行选项](http://docs.oracle.com/javase/7/docs/technotes/tools/windows/java.html)

- **AndroidDesignerJavaRendererMinMemory** -Xms

- **AndroidDesignerJavaRendererMaxMemory** -Xmx

- **AndroidDesignerJavaRendererPermSize** -XX:MaxPermSize

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 在 Visual Studio 中打开解决方案。

2. 在解决方案资源管理器中逐个选择每个 Android 项目，然后单击每个项目上的 "[显示所有文件](https://docs.microsoft.com/previous-versions/visualstudio/visual-studio-2008/4afxey9h(v=vs.90))" 两次。 可以跳过不包含任何`.axml`布局文件的项目。 此步骤将确保每个项目目录都包含`.csproj.user`一个文件。

3. 退出 Visual Studio。

4. 找到步骤2中每个项目的文件。`.csproj.user`

5. 在文本`.csproj.user`编辑器中编辑每个文件。

6. 在`<PropertyGroup>`元素中添加任何或所有新的 Android 设计器内存属性。 您可以使用现有`<PropertyGroup>`的或创建一个新的。 下面是一个完整的`.csproj.user`示例文件，其中包含所有3个属性都设置为其默认值：

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

7. 保存并关闭所有已更新`.csproj.user`的文件。

8. 重新启动 Visual Studio 并重新打开解决方案。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 在 Visual Studio for Mac 中打开解决方案，以确保解决方案目录包含`.userprefs`文件。

2. 退出 Visual Studio for Mac。

3. 在解决方案`.userprefs`目录中找到该文件。

4. 在文本编辑器中编辑文件。`.userprefs`

5. 找到具有以下格式的现有 XML 元素。 此元素名称的最后部分将匹配项目的名称：本示例中的 "AndroidApplication1"：

    ```xml
    <MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >
    ```

6. 如果该`<MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >`元素不存在，则在该封闭`<Properties>`元素中的任意位置创建它。 务必将 "AndroidApplication1" 替换为项目的名称。

7. 添加任何或所有新的 Android 设计器内存属性作为元素的特性。 下面是一个完整的`.userprefs`示例文件，其中包含所有3个属性都设置为其默认值：

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

8. 对于包含任何`.axml`布局文件的解决方案中的每个 Android 项目，重复步骤5-7。 （也就是说，为每个`<MonoDevelop.Ide.ItemProperties.ProjectName>`项目添加一个元素。）

9. 保存并关闭该`.userprefs`文件。

10. 重新启动 Visual Studio for Mac 并重新打开解决方案。

-----
