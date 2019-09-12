---
title: 编辑 Android 虚拟设备属性
description: 本文介绍了如何使用 Android Device Manager 编辑 Android 虚拟设备的配置文件属性。
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: 3E33C136-8042-4184-A40C-3200D8CD99CB
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/30/2018
ms.openlocfilehash: 7a4312120ce69493074f28fb3e202f437d3e69e8
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70758042"
---
# <a name="editing-android-virtual-device-properties"></a>编辑 Android 虚拟设备属性

本文介绍了如何使用 Android Device Manager 编辑 Android 虚拟设备的配置文件属性。 

::: zone pivot="windows"

## <a name="android-device-manager-on-windows"></a>Windows 上的 Android Device Manager

Android Device Manager 支持编辑单个 Android 虚拟设备配置文件属性  。 “新建设备”和“设备编辑”屏幕在第一列中列出了虚拟设备的属性，第二列中为每个属性相应的值（如此示例中所示）   ： 

[![“新建设备”屏幕示例](device-properties-images/win/01-new-device-editor-sml.png)](device-properties-images/win/01-new-device-editor.png#lightbox)

当选择某个属性时，有关该属性的详细描述会显示在右侧。 可以修改“硬件配置文件属性”和“AVD 属性”   。 硬件配置文件属性（如`hw.ramSize` 和 `hw.accelerometer`）描述仿真设备的物理特征。 这些特征包括屏幕大小、可用的 RAM 量以及是否具有加速计。 AVD 属性指定 AVD 在运行时的操作。 例如，可以配置 AVD 属性以指定 AVD 如何使用开发计算机的图形卡进行呈现。

可以使用以下准则来更改属性：

- 若要更改布尔属性，请单击布尔属性右侧的复选标记：

    ![更改布尔属性](device-properties-images/win/02-boolean-value.png)

- 若要更改枚举 (enumerated) 属性，请单击属性右侧的下拉箭头并选择一个新值  。

    ![更改枚举属性](device-properties-images/win/04-enum-value.png)

- 若要更改字符串或整数属性，请双击值列中的当前字符串或整数设置并输入一个新值。

    ![更改整数属性](device-properties-images/win/03-integer-value.png)

::: zone-end
::: zone pivot="macos"

## <a name="android-device-manager-on-macos"></a>macOS 上的 Android Device Manager

Android Device Manager 支持编辑单个 Android 虚拟设备配置文件属性  。 “新建设备”和“设备编辑”屏幕在第一列中列出了虚拟设备的属性，第二列中为每个属性相应的值（如此示例中所示）   ： 

[![“新建设备”屏幕示例](device-properties-images/mac/01-new-device-editor-sml.png)](device-properties-images/mac/01-new-device-editor.png#lightbox)

当选择某个属性时，有关该属性的详细描述会显示在右侧。 可以修改“硬件配置文件属性”和“AVD 属性”   。 硬件配置文件属性（如`hw.ramSize` 和 `hw.accelerometer`）描述仿真设备的物理特征。 这些特征包括屏幕大小、可用的 RAM 量以及是否具有加速计。 AVD 属性指定 AVD 在运行时的操作。 例如，可以配置 AVD 属性以指定 AVD 如何使用开发计算机的图形卡进行呈现。

可以使用以下准则来更改属性：

- 若要更改布尔属性，请单击布尔属性右侧的复选标记：

    ![更改布尔属性](device-properties-images/mac/02-boolean-value.png)

- 若要更改枚举 (enumerated) 属性，请单击属性右侧的下拉菜单并选择一个新值  。

    ![更改枚举属性](device-properties-images/mac/04-enum-value.png)

- 若要更改字符串或整数属性，请双击值列中的当前字符串或整数设置并输入一个新值。

    ![更改整数属性](device-properties-images/mac/03-integer-value.png)

::: zone-end

下表详细说明了“新建设备”和“设备编辑器”屏幕中列出的属性   ：

[!include[](~/android/includes/emulator-properties.md)]

有关这些属性的详细信息，请参阅[硬件配置文件属性](https://developer.android.com/studio/run/managing-avds.html#hpproperties)。
