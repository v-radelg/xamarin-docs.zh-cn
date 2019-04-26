---
title: 打包 Wear 应用
ms.prod: xamarin
ms.assetid: E32DD855-78DD-46F8-B234-4EAC0756BDA2
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/02/2018
ms.openlocfilehash: 585c276b327a9092bdd13fa633307477017558c5
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61276783"
---
# <a name="packaging-wear-apps"></a>打包 Wear 应用

Android Wear 应用都打包在一起的完整的 Android 应用的 Google Play 上的分发。 

## <a name="automatic-packaging"></a>自动打包

从 Xamarin Android 5.0 开始，Wear 应用自动打包为掌上电脑应用中资源时您创建从手持项目穿戴设备项目到项目引用。 可以使用以下步骤来创建此关联： 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 如果 Wear 应用尚未手持式解决方案的一部分，右键单击解决方案节点，然后选择**添加 > 添加现有项目...**.

2. 导航到 **.csproj**文件的穿戴设备应用，选择它，然后单击**打开**。 Wear 应用程序项目现在应可以看到在手持设备的解决方案中。

3. 右键单击**引用**节点，然后选择**添加引用**。

4. 在中**引用管理器**对话框中，启用穿戴设备项目 （单击以添加一个复选标记），然后单击**确定**。

5. 更改包名称为穿戴设备项目，使其匹配手持项目的包名称 (包名称可以更改下**属性 > Android 清单**)。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 如果 Wear 应用尚未手持式解决方案的一部分，右键单击解决方案节点，然后选择**添加 > 添加现有项目...**.

2. 导航到 **.csproj**文件的穿戴设备应用，选择它，然后单击**打开**。 Wear 应用程序项目现在应可以看到在手持设备的解决方案中。

3. 右键单击解决方案，单击中的手持项目节点**编辑引用...**.

4. 在中**编辑引用**对话框中，启用穿戴设备项目 （单击以添加一个复选标记），然后单击**确定**。

5. 更改包名称为穿戴设备项目，使其匹配手持项目的包名称 (包名称可以更改下**项目选项 > Android 应用程序**)。

-----


请注意，您将获得**XA5211**如果 Wear 应用的包名称与手持应用的包名称不匹配错误。 例如：

```shell
Error XA5211: Embedded wear app package name differs from handheld 
app package name (com.companyname.mywearapp != com.companyname.myapp). (XA5211)
```

若要更正此错误，请更改 Wear 应用的包名称，使其匹配掌上电脑应用的包名称。

当您单击**生成 > 生成所有**，这种关联触发穿戴设备项目自动打包到主 Handheld (Phone) 项目。 Wear 应用自动生成并包含为掌上电脑应用中的资源。

Wear 应用程序项目生成的程序集不用作 Handheld (Phone) 项目中的程序集引用。 相反，生成过程将执行以下操作：

-   验证包名匹配。 

-   生成的 XML 并将其添加到手持项目，以将它与 Wear 应用相关联。 例如： 

    ```xml
    <!-- Handheld (Phone) Project.csproj -->
    <ProjectReference Include="..\MyWearApp\MyWearApp.csproj">
        <Project>{D80E1FEF-653B-448C-B2AA-609C74E88340}</Project>
        <Name>MyWearApp</Name>
        <IsAppExtension>True</IsAppExtension>
    </ProjectReference>
    ```

-   将作为 Wear 应用添加**原始**到手持项目的资源。 


## <a name="manual-packaging"></a>手动打包

可以之前的版本 5.0 中，在 Xamarin.Android 中编写 Android Wear 应用程序，但是，必须执行这些手动打包说明将应用分发： 

1. 请确保你的可穿戴项目和 Handheld (Phone) 项目具有相同的版本数和包名称。

2. 手动构建该项目可穿戴视为**版本**生成。

3. 手动添加发布 **。APK**从步骤 (2) 到**资源/原始**Handheld (Phone) 项目的目录。

4. 手动添加新的 XML 资源**Resources/xml/wearable_app_desc.xml**在手持设备项目中，是指可穿戴设备**APK**步骤 (3) 中：

    ```xml
    <wearableApp package="wearable.app.package.name">
        <versionCode>1</versionCode>
        <versionName>1.0</versionName>
        <rawPathResId>NAME_OF_APK_FROM_STEP_3</rawPathResId>
    </wearableApp>
    ```

5. 手动添加`<meta-data />`到手持项目的元素**AndroidManifest.xml** `<application>`指的是新的 XML 资源的元素：

    ```xml
    <meta-data android:name="com.google.android.wearable.beta.app"
        android:resource="@xml/wearable_app_desc"/>
    ```

另请参阅 Android 开发人员站点[手动 packging 说明](https://developer.android.com/training/wearables/apps/packaging.html#PackageManually)。

