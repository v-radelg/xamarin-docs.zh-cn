---
title: 使用 Android Device Manager 管理虚拟设备
description: 本文介绍如何使用 Android Device Manager 创建和配置对物理 Android 设备进行仿真的 Android 虚拟设备 (AVD)。 可以使用这些虚拟设备运行和测试应用，而不需要依赖物理设备。
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: ECB327F3-FF1C-45CC-9FA6-9C11032BD5EF
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.custom: video
ms.date: 01/22/2019
ms.openlocfilehash: 64e637648b03dff81468f2d7ad072c7acc50868b
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021152"
---
# <a name="managing-virtual-devices-with-the-android-device-manager"></a>使用 Android Device Manager 管理虚拟设备

本文介绍如何使用 Android Device Manager 创建和配置对物理 Android 设备进行仿真的 Android 虚拟设备 (AVD) _。你可以使用这些虚拟设备运行和测试应用，而不需要依赖物理设备。_

验证硬件加速已启用后（如[通过硬件加速提高仿真器性能](~/android/get-started/installation/android-emulator/hardware-acceleration.md)中所述），下一步是使用 Android Device Manager（也称为 Xamarin Android Device Manager）创建用于测试和调试应用的虚拟设备   。

::: zone pivot="windows"

## <a name="android-device-manager-on-windows"></a>Windows 上的 Android Device Manager

本文介绍了如何使用 Android Device Manager 创建、复制、自定义和启动 Android 虚拟设备。

[![“设备”选项卡中 Android Device Manager 的屏幕截图](device-manager-images/win/01-devices-dialog-sml.png)](device-manager-images/win/01-devices-dialog.png#lightbox)

使用 Android Device Manager 创建和配置在 [Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md) 中运行的 Android 虚拟设备 (AVD)  。
每台 AVD 是模拟物理 Android 设备的仿真器配置。 这样可以在模拟不同物理 Android 设备的多种配置中运行和测试应用。

## <a name="requirements"></a>要求

若要使用 Android Device Manager，需要具备以下各项：

- Visual Studio 2019 Community、Professional 或 Enterprise。

- 或者需要 Visual Studio 2017 版本 15.8 或更高版本。 支持 Visual Studio Community、Professional 和 Enterprise 版本。

- Visual Studio Tools for Xamarin 版本 4.9 或更高版本。

- 必须安装 Android SDK（请参阅[设置用于 Xamarin.Android 的 Android SDK](~/android/get-started/installation/android-sdk.md)）。
  请务必在默认位置安装 Android SDK（如果尚未安装的话）：C:\\Program Files (x86)\\Android\\android-sdk。 

- 必须（通过 [Android SDK 管理器](~/android/get-started/installation/android-sdk.md)）安装以下包： 
  - Android SDK Tools 版本 26.1.1 或更高版本 
  - Android SDK 平台工具 27.0.1 或更高版本 
  - Android SDK 生成工具 27.0.3 或更高版本  
  - Android Emulator 27.2.7 或更高版本  。 

  这些包应显示为“已安装”状态，如下面的屏幕截图所示  ：

  [![安装 Android SDK Tools](device-manager-images/win/02-sdk-tools-sml.png)](device-manager-images/win/02-sdk-tools.png#lightbox)

## <a name="launching-the-device-manager"></a>启动设备管理器

通过单击“工具”>“Android”>“Android Device Manager”，从“工具”菜单启动 Android Device Manager   ：

[![从工具菜单启动 Device Manager](device-manager-images/win/03-tools-menu-sml.png)](device-manager-images/win/03-tools-menu.png#lightbox)

如果在启动时看到以下错误对话框，请参阅[故障排除](#troubleshooting)部分以查找解决方法：

![“Android SDK 实例错误”对话框](device-manager-images/win/04-sdk-error.png)

## <a name="main-screen"></a>主屏幕

首次启动 Android 设备管理器时，它会展现一个显示所有当前已配置的虚拟设备的屏幕。 对于每台虚拟设备，将显示“名称”、“OS”（Android 版）、“处理器”、“内存”大小以及屏幕“分辨率”      ：

[![已安装设备及其参数的列表](device-manager-images/win/05-installed-list-sml.png)](device-manager-images/win/05-installed-list.png#lightbox)

选择列表中的设备时，“启动”按钮出现在右侧  。 可以单击“启动”按钮以通过此虚拟设备启动仿真器  ：

[![设备的“启动”按钮图像](device-manager-images/win/06-start-button-sml.png)](device-manager-images/win/06-start-button.png#lightbox)

通过所选虚拟设备启动仿真器后，“启动”按钮将更改为可用于终止运行仿真器的“停止”按钮   ：

[![正在运行的设备的“停止”按钮](device-manager-images/win/07-stop-button-sml.png)](device-manager-images/win/07-stop-button.png#lightbox)

### <a name="new-device"></a>新设备

若要创建新设备，请单击“新建”按钮（位于屏幕的右上方区域）  ：

[![用于创建新设备的“新建”按钮](device-manager-images/win/08-new-button-sml.png)](device-manager-images/win/08-new-button.png#lightbox)

单击“新建”以启动“新建设备”屏幕   ：

[![设备管理器的“新建设备”屏幕](device-manager-images/win/09-new-device-editor-sml.png)](device-manager-images/win/09-new-device-editor.png#lightbox)

若要在“新建设备”中配置新设备，请使用以下步骤  ：

1. 为设备提供新名称。 在下面的示例中，新设备名为“Pixel_API_27”  ：

   [![为新设备命名](device-manager-images/win/10-device-name-sml.png)](device-manager-images/win/10-device-name.png#lightbox)

2. 通过单击“基本设备”下拉菜单选择要仿真的物理设备  ：

   [![选择要仿真的物理设备](device-manager-images/win/11-device-menu-sml.png)](device-manager-images/win/11-device-menu.png#lightbox)

3. 单击“处理器”下拉菜单以选择适用于此虚拟设备的处理器类型  。 选择“x86”可提供最佳性能，因为它使仿真器能够充分利用[硬件加速](~/android/get-started/installation/android-emulator/hardware-acceleration.md)  。
   x86_64 选项也将使用硬件加速，但运行速度略慢于 x86（x86_64 通常用于测试 64 位应用）    ：

   [![选择处理器类型](device-manager-images/win/12-processor-type-menu-sml.png)](device-manager-images/win/12-processor-type-menu.png#lightbox)

4. 单击“OS”下拉菜单可选择 Android 版本（API 级别）  。 例如，选择“Oreo 8.1 - API 27”以创建 API 级别为 27 的虚拟设备  ：

   [![选择 Android 版本](device-manager-images/win/13-android-version-w158-sml.png)](device-manager-images/win/13-android-version-w158.png#lightbox)

   如果选择尚未安装的 Android API 级别，Device Manager 将在屏幕底部显示“将下载新设备”消息 &ndash; 它将在创建新的虚拟设备时，下载并安装必要的文件  ：

   ![将下载新的设备映像](device-manager-images/win/14-automatic-download-w158.png)

5. 如果希望虚拟设备中包含 Google Play Services API，请启用“Google API”选项  。 若要包含 Google Play 商店应用，请启用“Google Play 商店”选项  ：

   [![选择 Google Play Services 和 Google Play 商店](device-manager-images/win/15-google-play-services-sml.png)](device-manager-images/win/15-google-play-services.png#lightbox)

   请注意，Google Play 商店图片仅适用于某些基本设备类型，例如 Pixel、Pixel 2、Nexus 5 和 Nexus 5X。

6. 编辑需要修改的任何属性。 若要对属性进行更改，请参阅[编辑 Android 虚拟设备属性](~/android/get-started/installation/android-emulator/device-properties.md)。

7. 添加需要显式设置的任何其他属性。 尽管“新建设备”屏幕只列出了最常修改的属性，但你可以单击“添加属性”下拉菜单（位于底部）以添加其他的属性   ：

   [![“添加属性”下拉菜单](device-manager-images/win/16-add-property-menu-sml.png)](device-manager-images/win/16-add-property-menu.png#lightbox)

    还可以在属性列表顶部选择“自定义...”，定义自定义属性  。

8. 单击“创建”按钮（位于右下角）以创建新设备  ：

   [![“创建”按钮](device-manager-images/win/17-create-button-sml.png)](device-manager-images/win/17-create-button.png#lightbox)

9. 可能会出现“许可证接受”屏幕  。 如果同意许可条款，请单击“接受”  ：

   [![“许可证接受”屏幕](device-manager-images/win/18-license-acceptance-sml.png)](device-manager-images/win/18-license-acceptance.png#lightbox)

10. 在设备创建期间，Android Device Manager 将新设备添加到已安装虚拟设备列表中，同时显示“正在创建”进度指示器  ：

    [![创建进度指示器](device-manager-images/win/19-creating-the-device-sml.png)](device-manager-images/win/19-creating-the-device.png#lightbox)

11. 创建过程完成后，新设备会显示在已安装虚拟设备的列表中，并且会显示可以启动的“启动”按钮  ：

    [![可以启动的新创建的设备](device-manager-images/win/20-created-device-sml.png)](device-manager-images/win/20-created-device.png#lightbox)

### <a name="edit-device"></a>编辑设备

若要编辑现有的虚拟设备，请选择设备并单击“编辑”按钮（位于屏幕的右上方）  ：

[![用于修改设备的“编辑”按钮](device-manager-images/win/21-edit-button-sml.png)](device-manager-images/win/21-edit-button.png#lightbox)

单击“编辑”为所选的虚拟设备启动“设备编辑器”  ：

[![“设备编辑器”屏幕](device-manager-images/win/22-device-editor-sml.png)](device-manager-images/win/22-device-editor.png#lightbox)

“设备编辑器”屏幕在“属性”列下列出了虚拟设备的属性，在“值”列中为每个属性列出相应的值    。 当选择某个属性时，有关该属性的详细描述会显示在右侧。

若要更改属性，请在“值”列中编辑其值  。
例如，在下面的屏幕截图中，`hw.lcd.density` 属性正从“480”更改为“240”   ：

[![设备编辑示例](device-manager-images/win/23-device-editing-sml.png)](device-manager-images/win/23-device-editing.png#lightbox)

在进行必要的配置更改后，单击“保存”按钮  。
有关更改虚拟设备属性的详细信息，请参阅[编辑 Android 虚拟设备属性](~/android/get-started/installation/android-emulator/device-properties.md)。

### <a name="additional-options"></a>附加选项

右上方的“其他选项”(&hellip;) 下拉菜单中提供适用于设备的其他选项  ：

[![附加选项菜单的位置](device-manager-images/win/24-overflow-menu-sml.png)](device-manager-images/win/24-overflow-menu.png#lightbox)

附加选项菜单中包含以下项：

- **复制和编辑** &ndash; 复制当前所选的设备，并会在“新建设备”屏幕中使用不同的唯一名称打开  。 例如，选择“Pixel_API_27”并单击“复制和编辑”可将计数器追加到名称中   ：

  [![“复制和编辑”屏幕](device-manager-images/win/25-dupe-and-edit-sml.png)](device-manager-images/win/25-dupe-and-edit.png#lightbox)

- **在资源管理器中展现** &ndash; 在包含用于虚拟设备的文件的文件夹中打开“Windows 资源管理器”窗口。 例如，选择“Pixel_API_27”并单击“在资源管理器中展现”以打开一个窗口，如下所示   ：

  [![单击“在资源管理器中展现”的结果](device-manager-images/win/26-reveal-in-explorer-sml.png)](device-manager-images/win/26-reveal-in-explorer.png#lightbox)

- **恢复出厂设置**  &ndash; 将所选设备重置为其默认设置，擦除用户在此设备运行时对其内部状态进行的任何更改（这也会擦除当前[快速启动](~/android/deploy-test/debugging/debug-on-emulator.md#quick-boot)快照（如果有））。 此更改不会影响在创建和编辑期间对虚拟设备做出的修改。 将出现提醒此重置无法被撤消的一个对话框。 单击“恢复出厂设置”，确认重置  ：

  ![“恢复出厂设置”对话框](device-manager-images/win/27-factory-reset.png)

- **删除** &ndash; 永久删除所选的虚拟设备。 将出现提醒删除设备无法被撤消的一个对话框。 如果确定要删除设备，请单击“删除”  。

  ![“删除设备”对话框](device-manager-images/win/28-delete-device-w158.png)

::: zone-end
::: zone pivot="macos"

## <a name="android-device-manager-on-macos"></a>macOS 上的 Android Device Manager

本文介绍了如何使用 Android Device Manager 创建、复制、自定义和启动 Android 虚拟设备。

[![“设备”选项卡中 Android Device Manager 的屏幕截图](device-manager-images/mac/01-devices-dialog-sml.png)](device-manager-images/mac/01-devices-dialog.png#lightbox)

> [!NOTE]
> 本指南仅适用于 Visual Studio for Mac。
Xamarin Studio 与 Android Device Manager 不兼容。

使用 Android Device Manager 创建和配置在 [Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md) 中运行的 Android 虚拟设备 (AVD)  。
每台 AVD 是模拟物理 Android 设备的仿真器配置。 这样可以在模拟不同物理 Android 设备的多种配置中运行和测试应用。

## <a name="requirements"></a>要求

若要使用 Android Device Manager，需要具备以下各项：

- Visual Studio for Mac 7.6 或更高版本。

- 必须安装 Android SDK（请参阅[设置用于 Xamarin.Android 的 Android SDK](~/android/get-started/installation/android-sdk.md)）。

- 必须（通过 [Android SDK 管理器](~/android/get-started/installation/android-sdk.md)）安装以下包： 
  - SDK 工具 26.1.1 版或更高版本 
  - Android SDK 平台工具 28.0.1 或更高版本  
  - Android SDK 生成工具 26.0.3 或更高版本 

  这些包应显示为“已安装”状态，如下面的屏幕截图所示  ：

  [![安装 Android SDK Tools](device-manager-images/mac/02-sdk-tools-sml.png)](device-manager-images/mac/02-sdk-tools.png#lightbox)

## <a name="launching-the-device-manager"></a>启动设备管理器

通过单击“工具”>“Device Manager”启动 Android Device Manager  ：

[![从工具菜单启动 Device Manager](device-manager-images/mac/03-tools-menu-sml.png)](device-manager-images/mac/03-tools-menu.png#lightbox)

如果在启动时看到以下错误对话框，请参阅[故障排除](#troubleshooting)部分以查找解决方法：

![“Android SDK 实例错误”对话框](device-manager-images/mac/04-sdk-instance-error.png)

## <a name="main-screen"></a>主屏幕

首次启动 Android 设备管理器时，它会展现一个显示所有当前已配置的虚拟设备的屏幕。 对于每台虚拟设备，将显示“名称”、“OS”（Android 版）、“处理器”、“内存”大小以及屏幕“分辨率”      ：

[![已安装设备及其参数的列表](device-manager-images/mac/05-devices-list-sml.png)](device-manager-images/mac/05-devices-list.png#lightbox)

选择列表中的设备时，“播放”按钮出现在右侧  。 可以单击“播放”按钮以通过此虚拟设备启动仿真器  ：

[![设备映像的“播放”按钮](device-manager-images/mac/06-start-button-sml.png)](device-manager-images/mac/06-start-button.png#lightbox)

通过所选虚拟设备启动仿真器后，“播放”按钮将更改为可用于终止运行仿真器的“停止”按钮   ：

[![正在运行的设备的“停止”按钮](device-manager-images/mac/07-stop-button-sml.png)](device-manager-images/mac/07-stop-button.png#lightbox)

停止仿真器时，可能会收到一个提示，询问是否要保存当前状态以便下一次快速启动：

![保存“快速启动”对话框的当前状态](device-manager-images/mac/08-save-for-quick-boot-m76.png)

保存当前状态会使再次启动虚拟设备时仿真器的启动速度更快。 有关“快速启动”的详细信息，请参阅[快速启动](~/android/deploy-test/debugging/debug-on-emulator.md#quick-boot)。

### <a name="new-device"></a>新设备

若要创建新设备，请单击“新建设备”按钮（位于屏幕的左上方区域）  ：

[![用于创建新设备的“新建”按钮](device-manager-images/mac/09-new-button-sml.png)](device-manager-images/mac/09-new-button.png#lightbox)

单击“新建设备”以启动“新建设备”屏幕   ：

[![设备管理器的“新建设备”屏幕](device-manager-images/mac/10-new-device-editor-sml.png)](device-manager-images/mac/10-new-device-editor.png#lightbox)

使用以下步骤在“新建设备”屏幕中配置新设备  ：

1. 为设备提供新名称。 在下面的示例中，新设备名为“Pixel_API_27”  ：

   [![为新设备命名](device-manager-images/mac/11-device-name-m76-sml.png)](device-manager-images/mac/11-device-name-m76.png#lightbox)

2. 通过单击“基本设备”下拉菜单选择要仿真的物理设备  ：

   [![选择要仿真的物理设备](device-manager-images/mac/12-device-menu-m76-sml.png)](device-manager-images/mac/12-device-menu-m76.png#lightbox)

3. 单击“处理器”下拉菜单以选择适用于此虚拟设备的处理器类型  。 选择“x86”可提供最佳性能，因为它使仿真器能够充分利用[硬件加速](~/android/get-started/installation/android-emulator/hardware-acceleration.md)  。
   x86_64 选项也将使用硬件加速，但运行速度略慢于 x86（x86_64 通常用于测试 64 位应用）    ：

   [![选择处理器类型](device-manager-images/mac/13-processor-type-menu-m76-sml.png)](device-manager-images/mac/13-processor-type-menu-m76.png#lightbox)

4. 单击“OS”下拉菜单可选择 Android 版本（API 级别）  。 例如，选择“Oreo 8.1 - API 27”以创建 API 级别为 27 的虚拟设备  ：

   [![选择 Android 版本](device-manager-images/mac/14-android-screenshot-m76-sml.png)](device-manager-images/mac/14-android-screenshot-m76.png#lightbox)

   如果选择尚未安装的 Android API 级别，Device Manager 将在屏幕底部显示“将下载新设备”消息 &ndash; 它将在创建新的虚拟设备时，下载并安装必要的文件  ：

   ![将下载新的设备映像](device-manager-images/mac/15-automatic-download-m76.png)

5. 如果希望虚拟设备中包含 Google Play Services API，请启用“Google API”选项  。 若要包含 Google Play 商店应用，请启用“Google Play 商店”选项  ：

   [![选择 Google Play Services 和 Google Play 商店](device-manager-images/mac/16-google-play-services-m76-sml.png)](device-manager-images/mac/16-google-play-services-m76.png#lightbox)

   请注意，Google Play 商店图片仅适用于某些基本设备类型，例如 Pixel、Pixel 2、Nexus 5 和 Nexus 5X。

6. 编辑需要修改的任何属性。 若要对属性进行更改，请参阅[编辑 Android 虚拟设备属性](~/android/get-started/installation/android-emulator/device-properties.md)。

7. 添加需要显式设置的任何其他属性。 尽管“新建设备”屏幕只列出了最常修改的属性，但你可以单击“添加属性”下拉菜单（位于底部）以添加其他的属性   ：

   [![“添加属性”下拉菜单](device-manager-images/mac/17-add-property-menu-m76-sml.png)](device-manager-images/mac/17-add-property-menu-m76.png#lightbox)

   还可以在该属性列表顶部单击“自定义...”，定义自定义属性  。

8. 单击“创建”按钮（位于右下角）以创建新设备  ：

   ![“创建”按钮](device-manager-images/mac/18-create-button-m76.png)

9. 在设备创建期间，Android Device Manager 将新设备添加到已安装虚拟设备列表中，同时显示“正在创建”进度指示器  ：

   [![创建进度指示器](device-manager-images/mac/19-creating-the-device-m76-sml.png)](device-manager-images/mac/19-creating-the-device-m76.png#lightbox)

10. 创建过程完成后，新设备会显示在已安装虚拟设备的列表中，并且会显示可以启动的“启动”按钮  ：

    [![可以启动的新创建的设备](device-manager-images/mac/20-created-device-m76-sml.png)](device-manager-images/mac/20-created-device-m76.png#lightbox)

### <a name="edit-device"></a>编辑设备

若要编辑现有的虚拟设备，请选择“其他选项”下拉菜单（齿轮图标），然后选择“编辑”   ：

[![用于修改新设备的“编辑”菜单选项](device-manager-images/mac/21-edit-button-m76-sml.png)](device-manager-images/mac/21-edit-button-m76.png#lightbox)

单击“编辑”为所选的虚拟设备启动“设备编辑器”  ：

[![“设备编辑器”屏幕](device-manager-images/mac/22-device-editor-sml.png)](device-manager-images/mac/22-device-editor.png#lightbox)

“设备编辑器”屏幕在“属性”列下列出了虚拟设备的属性，在“值”列中为每个属性列出相应的值    。 当选择某个属性时，有关该属性的详细描述会显示在右侧。

若要更改属性，请在“值”列中编辑其值  。
例如，在下面的屏幕截图中，`hw.lcd.density` 属性正从“480”更改为“240”   ：

[![设备编辑示例](device-manager-images/mac/23-device-editing-sml.png)](device-manager-images/mac/23-device-editing.png#lightbox)

在进行必要的配置更改后，单击“保存”按钮  。
有关更改虚拟设备属性的详细信息，请参阅[编辑 Android 虚拟设备属性](~/android/get-started/installation/android-emulator/device-properties.md)。

### <a name="additional-options"></a>附加选项

“播放”按钮左侧的下拉菜单中提供适用于设备的附加选项  ：

[![附加选项菜单的位置](device-manager-images/mac/24-overflow-menu-sml.png)](device-manager-images/mac/24-overflow-menu.png#lightbox)

附加选项菜单中包含以下项：

- **编辑** &ndash; 在设备编辑器中打开当前所选设备，如之前所述。

- **复制和编辑** &ndash; 复制当前所选的设备，并会在“新建设备”屏幕中使用不同的唯一名称打开  。 例如，选择“Pixel 2 API 28”并单击“复制和编辑”可将计数器追加到名称中   ：

  [![“复制和编辑”屏幕](device-manager-images/mac/25-dupe-and-edit-sml.png)](device-manager-images/mac/25-dupe-and-edit.png#lightbox)

- **在查找器中展现** &ndash; 在包含用于虚拟设备的文件的文件夹中打开 macOS Finder 窗口。 例如，选择“Pixel 2 API 28”并单击“在查找器中展现”以打开一个窗口，如下所示   ：

  [![单击“在查找器中展现”的结果](device-manager-images/mac/26-reveal-in-finder-sml.png)](device-manager-images/mac/26-reveal-in-finder.png#lightbox)

- **恢复出厂设置**  &ndash; 将所选设备重置为其默认设置，擦除用户在此设备运行时对其内部状态进行的任何更改（这也会擦除当前[快速启动](~/android/deploy-test/debugging/debug-on-emulator.md#quick-boot)快照（如果有））。 此更改不会影响在创建和编辑期间对虚拟设备做出的修改。 将出现提醒此重置无法被撤消的一个对话框。 单击“恢复出厂设置”以确认重置  。

  ![“恢复出厂设置”对话框](device-manager-images/mac/27-factory-reset-m76.png)

- **删除** &ndash; 永久删除所选的虚拟设备。 将出现提醒删除设备无法被撤消的一个对话框。 如果确定要删除设备，请单击“删除”  。

  ![“删除设备”对话框](device-manager-images/mac/28-delete-device-m76.png)

-----

<a name="troubleshooting" />

## <a name="troubleshooting"></a>疑难解答

以下各节介绍如何诊断和避开使用 Android Device Manager 配置虚拟设备时可能发生的问题。

<!-- markdownlint-disable MD001 -->

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

### <a name="android-sdk-in-non-standard-location"></a>Android SDK 位于非标准位置

通常情况下，Android SDK 安装在以下位置：

C:\\Program Files (x86)\\Android\\android-sdk 

如果 SDK 未安装在此位置，在启动 Android Device Manager 时可能会遇到此错误：

![Android SDK 实例错误](device-manager-images/win/29-sdk-error.png)

若要解决此问题，请使用以下步骤：

1. 从 Windows 桌面依次转到 C:\\Users\\username  \\AppData\\Roaming\\XamarinDeviceManager  ：

   ![Android Device Manager 日志文件位置](device-manager-images/win/30-log-files.png)

2. 双击以打开某个日志文件，并找到“配置文件路径”  。 例如:

   [![日志文件中的“配置文件路径”](device-manager-images/win/31-config-file-path-sml.png)](device-manager-images/win/31-config-file-path.png#lightbox)

3. 导航到此位置，然后双击“user.config”以将其打开  。

4. 在“user.config”中，找到 `<UserSettings>` 元素，并向它添加一个“AndroidSdkPath”特性   。 将此特性设置为 Android SDK 在开发计算机上的安装位置路径，然后保存文件。 例如，如果 Android SDK 安装在 C:\\Programs\\Android\\SDK，`<UserSettings>` 将如下所示  ：

   ```xml
   <UserSettings SdkLibLastWriteTimeUtcTicks="636409365200000000" AndroidSdkPath="C:ProgramsAndroidSDK" />
   ```

对 user.config 进行更改后，应该能够启动 Android Device Manager  。

### <a name="wrong-version-of-android-sdk-tools"></a>错误版本的 Android SDK Tools

如果未安装 Android SDK 工具 26.1.1 或更高版本，在启动时可能会看到以下错误对话框：

![“Android SDK 实例错误”对话框](device-manager-images/win/32-sdk-instance-error.png)

如果看到此错误对话框，请单击“打开 SDK 管理器”以打开 Android SDK 管理器  。 在 Android SDK 管理器中，单击“工具”选项卡并安装以下包  ：

- Android SDK Tools 26.1.1 或更高版本 
- Android SDK 平台工具 27.0.1 或更高版本 
- Android SDK 生成工具 27.0.3 或更高版本 

### <a name="snapshot-disables-wifi-on-android-oreo"></a>快照禁用 Android Oreo 上的 WiFi

如果 AVD 配置为使用模拟 Wi-Fi 访问的 Android Oreo，那么在快照之后重启 AVD 可能会导致 Wi-Fi 访问被禁用。

若要解决此问题：

1. 在 Android Device Manager 中选择 AVD。

2. 在其他选项菜单中，单击“在资源管理器中展现”  。

3. 导航到“快照”>“default_boot”  。

4. 删除 snapshot.pb  文件：

   ![snapshot.pb 文件的位置](device-manager-images/win/33-delete-snapshot.png)

5. 重启 AVD。

进行这些更改后，AVD 将在允许 Wi-Fi 重新工作的状态下重新启动。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

### <a name="wrong-version-of-android-sdk-tools"></a>错误版本的 Android SDK Tools

如果未安装 Android SDK 工具 26.1.1 或更高版本，在启动时可能会看到以下错误对话框：

![“Android SDK 实例错误”对话框](device-manager-images/mac/29-sdk-instance-error.png)

如果看到此错误对话框，请单击“确定”以打开 Android SDK 管理器  。 在 Android SDK 管理器中，单击“工具”选项卡并安装以下包  ：

- Android SDK Tools 26.1.1 或更高版本 
- Android SDK 平台工具 28.0.1 或更高版本 
- Android SDK 生成工具 26.0.3 或更高版本 

### <a name="snapshot-disables-wifi-on-android-oreo"></a>快照禁用 Android Oreo 上的 WiFi

如果 AVD 配置为使用模拟 Wi-Fi 访问的 Android Oreo，那么在快照之后重启 AVD 可能会导致 Wi-Fi 访问被禁用。

若要解决此问题：

1. 在 Android Device Manager 中选择 AVD。

2. 在其他选项菜单中，单击“在查找器中展现”  。

3. 导航到“快照”>“default_boot”  。

4. 删除 snapshot.pb  文件：

   [![Snapshot.pb 文件的位置](device-manager-images/mac/30-delete-snapshot-sml.png)](device-manager-images/mac/30-delete-snapshot.png#lightbox)

5. 重启 AVD。

进行这些更改后，AVD 将在允许 Wi-Fi 重新工作的状态下重新启动。

-----

### <a name="generating-a-bug-report"></a>生成 Bug 报表

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

如果发现 Android Device Manager 出现问题，但无法使用上述疑难解答提示解决，请右键单击标题栏并选择“生成 Bug 报告”，提交该 bug 报告  ：

[![提交 bug 报告的菜单项位置](device-manager-images/win/34-bug-report-sml.png)](device-manager-images/win/34-bug-report.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

如果发现 Android Device Manager 出现问题，但无法使用上述疑难解答提示解决，请单击“帮助”>“报告问题”，提交该 Bug 报告  ：

[![提交 bug 报告的菜单项位置](device-manager-images/mac/31-bug-report-sml.png)](device-manager-images/mac/31-bug-report.png#lightbox)

::: zone-end

## <a name="summary"></a>总结

本指南介绍 Visual Studio Tools for Xamarin 和 Visual Studio for Mac 中提供的 Android Device Manager。 其中介绍了启动和停止 Android 仿真器、选择要运行的 Android 虚拟设备 (AVD)、创建新的虚拟设备以及如何编辑虚拟设备等基本功能。 还介绍了如何编辑配置文件硬件属性以便进一步进行自定义并提供了常见问题的故障排除技巧。

## <a name="related-links"></a>相关链接

- [对 Android SDK 工具的更改](~/android/troubleshooting/sdk-cli-tooling-changes.md)
- [在 Android Emulator 上调试](~/android/deploy-test/debugging/debug-on-emulator.md)
- [SDK Tools 发行说明 (Google)](https://developer.android.com/studio/releases/sdk-tools)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)

## <a name="related-video"></a>相关视频

> [!Video https://channel9.msdn.com/Shows/XamarinShow/How-to-Create-and-Manage-Your-Own-Android-Emulators/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
