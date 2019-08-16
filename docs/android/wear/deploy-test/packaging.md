---
title: 打包应用程序
ms.prod: xamarin
ms.assetid: E32DD855-78DD-46F8-B234-4EAC0756BDA2
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/02/2018
ms.openlocfilehash: bca29089a61ed1f3400458f4b102c61023f47247
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69522110"
---
# <a name="packaging-wear-apps"></a>打包应用程序

Android 磨损应用打包在 Google Play 上, 适用于分发的完整 Android 应用。 

## <a name="automatic-packaging"></a>自动打包

从 Xamarin Android 5.0 开始, 当你创建从掌上项项目到磨损项目的项目引用时, 你的损耗应用会自动打包为你的手持式应用中的资源。 你可以使用以下步骤来创建此关联: 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 如果你的应用程序还不在你的手持式解决方案中, 请右键单击 "解决方案" 节点, 然后选择 "**添加 > 添加现有项目 ...** "。

2. 导航到你的应用程序的 **.csproj**文件, 选择它, 然后单击 "**打开**"。 应用程序项目现在应在你的手持式解决方案中可见。

3. 右键单击 "**引用**" 节点, 然后选择 "**添加引用**"。

4. 在 "**引用管理器**" 对话框中, 启用你的磨损项目 (单击添加复选标记), 然后单击 **"确定"** 。

5. 更改磨损项目的包名称, 使其与掌上项目的包名称匹配 (包名称可在 "**属性 > Android 清单**" 下更改)。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 如果你的应用程序还不在你的手持式解决方案中, 请右键单击 "解决方案" 节点, 然后选择 "**添加 > 添加现有项目 ...** "。

2. 导航到你的应用程序的 **.csproj**文件, 选择它, 然后单击 "**打开**"。 应用程序项目现在应在你的手持式解决方案中可见。

3. 右键单击解决方案中的手持式项目节点, 然后单击 "**编辑引用 ...** "。

4. 在 "**编辑引用**" 对话框中, 启用你的磨损项目 (单击添加复选标记), 然后单击 **"确定"** 。

5. 更改你的磨损项目的包名称, 使其与掌上项项目的包名称匹配 (包名称可在**项目选项 > Android 应用程序**) 下进行更改。

-----


请注意, 如果磨损应用的包名称与手持应用的包名称不匹配, 则会出现**XA5211**错误。 例如:

```shell
Error XA5211: Embedded wear app package name differs from handheld 
app package name (com.companyname.mywearapp != com.companyname.myapp). (XA5211)
```

若要更正此错误, 请更改该应用程序的包名称, 使其与手持应用程序的包名称匹配。

单击 "**生成" >** "生成" 时, 该关联会触发将磨损项目自动打包到主要手持式 (电话) 项目中。 会自动生成应用程序并将其作为资源包含在手持式应用中。

应用程序项目生成的程序集不在手持式 (电话) 项目中用作程序集引用。 相反, 生成过程将执行以下操作:

- 验证包名称是否匹配。 

- 生成 XML, 并将其添加到手持式项目, 以将其与应用程序关联。 例如: 

    ```xml
    <!-- Handheld (Phone) Project.csproj -->
    <ProjectReference Include="..\MyWearApp\MyWearApp.csproj">
        <Project>{D80E1FEF-653B-448C-B2AA-609C74E88340}</Project>
        <Name>MyWearApp</Name>
        <IsAppExtension>True</IsAppExtension>
    </ProjectReference>
    ```

- 将磨损应用作为**原始**资源添加到手持项目。 


## <a name="manual-packaging"></a>手动打包

在版本5.0 之前, 可以在 Xamarin 中编写 Android 应用程序, 但必须遵循以下手动打包说明来分发应用程序: 

1. 确保你的可穿戴项目和手持 (电话) 项目具有相同的版本号和包名称。

2. 手动将可穿戴项目构建为**发布**版本。

3. 手动添加发布 **。** 从步骤 (2) APK 到手持式 (电话) 项目的**资源/原始**目录。

4. 手动在手持式项目中添加一个新的 XML 资源**资源/xml/wearable_app_desc** , 该项目引用步骤中的可穿戴**APK** (3):

    ```xml
    <wearableApp package="wearable.app.package.name">
        <versionCode>1</versionCode>
        <versionName>1.0</versionName>
        <rawPathResId>NAME_OF_APK_FROM_STEP_3</rawPathResId>
    </wearableApp>
    ```

5. 将`<meta-data />`元素手动添加到手持式项目的 `<application>` androidmanifest.xml 元素中, 该元素引用新的 xml 资源:

    ```xml
    <meta-data android:name="com.google.android.wearable.beta.app"
        android:resource="@xml/wearable_app_desc"/>
    ```

另请参阅 Android 开发人员网站的[手动 packging 说明](https://developer.android.com/training/wearables/apps/packaging.html#PackageManually)。

