---
ms.openlocfilehash: b39fb6157c247416c85846d4d8e63e6e1b7c6028
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60956833"
---
|Property|说明|选项|
|--- |--- |--- |
|`abi.type`|ABI 类型 &ndash; 指定仿真设备的 ABI（应用程序二进制接口）类型。 x86 选项适用于通常被称为“x86”或“IA-32”的指令集。 x86_64 选项适用于 64 位 x86 指令集。 armeabi-v7a 选项适用于具有 v7-a ARM 扩展的 ARM 指令集。 arm64-v8a 选项适用于支持 AArch64 的 ARM 指令集。|x86、x86_64、armeabi-v7a、arm64-v8a|
|`disk.cachePartition`|缓存分区 &ndash; 确定仿真设备是否将在设备上使用 /cache 分区。 /cache 分区（最初为空）是 Android 存储经常访问的数据和应用组件的位置。 如果设置为“否”，仿真器将不使用 /cache 分区，且其他的 `disk.cache` 设置将被忽略。|yes、no|
|`disk.cachePartition.path`|缓存分区路径 &ndash; 在开发计算机上指定一个缓存分区映像文件。 仿真器将此文件用于 /cache 分区。 输入绝对路径或相对于仿真器数据目录的路径。 如果未设置，仿真器将在开发计算机上创建一个名为 cache.img 的空临时文件。 如果文件不存在，它则会被创建为一个空文件。 如果 `disk.cachePartition` 设置为“否”，则忽略此选项。||
|`disk.cachePartition.size`|缓存分区大小 &ndash; 缓存分区文件的大小（以字节为单位）。 通常不需要设置此选项，除非应用将下载非常大的文件，这些文件大于 66 MB 的默认缓存大小。 如果 `disk.cachePartition` 设置为“否”，则忽略此选项。 如果此值为整数，它则以字节为单位指定大小。 还可以通过将 K、M 或 G 追加到值中来以 KB、MB 或 GB 为单位指定大小。 最小大小为 9 M；最大为 1023 G。||
|`disk.dataPartition.initPath`|数据分区的初始路径 &ndash; 指定数据分区的初始内容。 擦除用户数据后，仿真器将指定文件的内容复制到用户数据（默认情况下为 userdata-qemu.img），而不是使用 userdata.img 作为初始版本。||
|`disk.dataPartition.path`|数据分区的路径 &ndash; 指定用户数据分区文件。 若要配置永久性用户数据文件，请在开发计算机上输入文件名和路径。 如果文件不存在，仿真器将从默认文件 userdata.img 创建一个映像，将它存储在由 `disk.dataPartition.path` 指定的文件名中，并在仿真器关闭时将用户数据永久保存到其中。 如果不指定路径，默认文件名为 userdata-qemu.img。 使用特殊值 <temp> 会使仿真器创建并使用一个临时文件。 如果 `disk.dataPartition.initPath` 已设置，会再启动时将其内容复制到 `disk.dataPartition.path` 文件。 注意，此选项不能留空。||
|`disk.dataPartition.size`|数据分区大小 &ndash; 指定用户数据分区的大小（以字节为单位）。 如果此值为整数，它则以字节为单位指定大小。 还可以通过将 K、M 或 G 追加到值中来以 KB、MB 或 GB 为单位指定大小。 最小大小为 9 M；最大为 1023 G。||
|`disk.ramdisk.path`|Ramdisk 路径 &ndash; 到启动分区 (ramdisk) 映像的路径。 ramdisk 映像是装载系统映像之前由内核加载的系统映像的子集。 ramdisk 映像通常包含启动时的二进制文件和初始化脚本。 如果未指定此选项，仿真器系统目录中的默认值为 ramdisk.img。||
|`disk.snapStorage.path`|快照存储路径 &ndash; 到存储所有快照的快照存储文件的路径。 在执行期间进行的所有快照将被保存到此文件。 只有被保存到此文件的快照在仿真器运行期间才能被还原。 如果未指定此选项，仿真器数据目录中的默认值为 snapshots.img。||
|`disk.systemPartition.initPath`|系统分区初始路径 &ndash; 到系统映像文件的只读副本的路径；具体来说，是指包含系统二进制文件以及与 API 级别和任何变体相对应的数据的分区。 如果未指定此选项，仿真器系统目录中的默认值为 system.img。||
|`disk.systemPartition.path`|系统分区路径 &ndash; 到读/写系统分区映像的路径。 如果未设置此路径，则将创建一个临时文件并从 `disk.systemPartition.initPath` 指定的文件内容初始化此文件。||
|`disk.systemPartition.size`|系统分区大小 &ndash; 系统分区的理想大小（以字节为单位）。 如果实际的系统分区映像大于此设置，则会忽略此大小；否则，它指定系统分区文件可以增长到的最大大小。 如果此值为整数，它则以字节为单位指定大小。 还可以通过将 K、M 或 G 追加到值中来以 KB、MB 或 GB 为单位指定大小。 最小大小为 9 M；最大为 1023 G。||
|`hw.accelerometer`|加速计 &ndash; 确定仿真设备是否包含一个加速度传感器。 加速计帮助设备确定方向（用于自动旋转）。 加速计报告设备沿着三个传感器轴的加速情况。|yes、no|
|`hw.audioInput`|音频录制支持 &ndash; 确定仿真设备是否可以录制音频。|yes、no|
|`hw.audioOutput`|音频播放支持 &ndash; 确定仿真设备是否可以播放音频。|yes、no|
|`hw.battery`|电池支持 &ndash; 确定仿真设备是否可以通过电池运行。|yes、no|
|`hw.camera`|照相机支持 &ndash; 确定仿真设备是否具有照相机。|yes、no|
|`hw.camera.back`|后置照相机 &ndash; 配置后置照相机（镜头背朝用户的面部）。 如果在开发计算机上使用网络摄像头以模拟仿真设备上的后置照相机，必须将此值设置为 webcamn，其中 _n_ 选择的是网络摄像头（如果只有一个网络摄像头，则选择 webcam0）。 如果设置为“emulated”，仿真器将在软件中模拟照相机。 若要禁用后置照相机，请将此值设置为“none”。 如果启用后置照相机，请确保也要启用 `hw.camera`。|emulated、none、webcam0|
|`hw.camera.front`|前置照相机 &ndash; 配置前置照相机（镜头面向用户）。 如果在开发计算机上使用网络摄像头以模拟仿真设备上的前置照相机，必须将此值设置为 webcamn，其中 n 选择的是网络摄像头（如果只有一个网络摄像头，则选择 webcam0）。 如果设置为“emulated”，仿真器将在软件中模拟照相机。 若要禁用前置照相机，请将此值设置为“none”。 如果启用前置照相机，请确保也要启用 `hw.camera`。|emulated、none、webcam0|
|`hw.camera.maxHorizontalPixels`|最大的照相机水平像素 &ndash; 配置仿真设备照相机的最大水平分辨率（以像素为单位）。||
|`hw.camera.maxVerticalPixels`|最大的照相机垂直像素 &ndash; 配置仿真设备照相机的最大垂直分辨率（以像素为单位）。||
|`hw.cpu.arch`|CPU 体系结构 &ndash; 要由虚拟设备仿真的 CPU 体系结构。 如果使用 Intel HAXM 以进行硬件加速，请为 32 位 CPU 选择 x86。 为 64 位 HAXM 加速的设备选择 x86_64。 （请务必在 SDK 管理器中安装相应的 Intel x86 系统映像：例如，Intel x86 Atom 或 Intel x86 Atom_64。）若要模拟 ARM CPU，请为 32 位 ARM CPU 选择 arm 或者为 64 位选择 arm64。 请记住，基于 ARM 的虚拟设备运行速度要比基于 x86 的那些虚拟设备慢得多，因为硬件加速不适用于 ARM。|x86、x86_64、arm、arm64|
|`hw.cpu.model`|CPU 型号 &ndash; 通常不会设置此值（如果未显式设置此值，它将被设置为派生自 `hw.cpu.arch` 的一个值）。 但是，可以将它设置为特定于仿真器的字符串以作实验性使用。||
|`hw.dPad`|DPad 密钥 &ndash; 确定仿真设备是否支持方向键 (DPad) 密钥。 一个 DPad 通常具有四个密钥以指示方向控件。|yes、no|
|`hw.gps`|GPS 支持 &ndash; 确定仿真设备是否具有 GPS（全球定位系统）接收器。|yes、no|
|`hw.gpu.enabled`|GPU 仿真 &ndash; 确定仿真设备是否支持 GPU 仿真。 启用后，GPU 仿真会使用 Open GL for Embedded Systems 以在屏幕上呈现 2D 和 3D 图形，并且关联的 GPU 仿真模式设置会确定 GPU 仿真的实现方式。|yes、no|
|`hw.gpu.mode`|GPU 仿真模式 &ndash; 确定仿真器实现 GPU 仿真的方式。 如果选择“auto”，仿真器将根据开发计算机设置选择硬件加速和软件加速。 如果选择“host”，仿真器将使用开发计算机的图形处理器执行 GPU 仿真以进行更快的呈现。 如果 GPU 与仿真器不兼容并且系统为 Windows，则可以尝试选择“angle”，而不是“host”。 “angle”模式使用 DirectX 以提供与“host”模式类似的性能。 如果选择“mesa”，模拟器将使用 Mesa 3D 软件库来呈现图形。 如果通过开发计算机的图形处理器进行呈现存在问题，请选择“mesa”。 可以使用“swiftshader”模式在软件中呈现图形，不过性能与使用计算机的 CPU 相比稍有降低。 “off”选项（禁用图形硬件仿真）是已弃用的一个选项，使用此选项后可能无法正确呈现某些项，因此不推荐此选项。|auto、host、mesa、angle、swiftshader、off|
|`hw.gsmModem`|GSM 调制解调器支持 &ndash; 确定仿真设备是否包含支持 GSM（全球移动通信系统）电话无线电系统的调制解调器。|yes、no|
|`hw.initialOrientation`|初始屏幕方向 &ndash; 配置仿真设备上屏幕的初始方向（纵向或横向模式）。 在纵向模式下，屏幕的高度大于宽度。 在横向模式下，屏幕的宽度大于高度。 如果设备配置文件中都支持纵向和横向模式，则可以在运行仿真设备时更改方向。|portrait、landscape|
|`hw.keyboard`|键盘支持 &ndash; 确定仿真设备是否支持全键盘。|yes、no|
|`hw.keyboard.charmap`|键盘字符映射名称 &ndash; 此设备的硬件字符映射的名称。 注意：此应始终为默认值 **qwerty2**，除非相应地修改了系统映像。 此名称会在启动时发送到内核。 使用不正确的名称将导致虚拟设备不可用。||
|`hw.keyboard.lid`|键盘盖支持 &ndash; 在启用键盘支持的情况下，此设置确定是否可以关闭/隐藏或打开/显示全键盘。 如果“hw.keyboard”设置为“false”，则将忽略此设置。 注意：如果仿真设备面向 API 级别 12 或更高版本，默认值则为“false”。|yes、no|
|`hw.lcd.backlight`|LCD 背光 &ndash; 确定 LCD 背光是否由仿真设备模拟。|yes、no|
|`hw.lcd.density`|LCD 密度 &ndash; 仿真 LCD 显示的密度，以与密度无关的像素或 dp（dp 是一个虚拟像素单位）为单位测量。 当设置为 160 dp 时，每个 dp 将对应一个物理像素。 在运行时，Android 使用此值选择和缩放适当的资源/资产以进行正确的显示呈现。|120、160、240、213、320|
|`hw.lcd.depth`|LCD 颜色深度 &ndash; 保留位图以驱动 LCD 显示的仿真帧缓冲区的颜色位深度。 此值可以为 16 位（65,536 种可能的颜色）或 32 位（16,777,216 种颜色和透明度）。 尽管 32 位设置使仿真器运行较为缓慢，但颜色准确度更高。|16, 32|
|`hw.lcd.height`|LCD 像素高度 &ndash; 构成仿真 LCD 显示的垂直维度的像素数量。||
|`hw.lcd.width`|LCD 像素宽度 &ndash; 构成仿真 LCD 显示的水平维度的像素数量。||
|`hw.mainKeys`|硬件返回/主页键 &ndash; 确定仿真设备是否支持硬件“返回”和“主页”导航按钮。 如果仅在软件中实现按钮，可以将此值设置为“yes”。 如果将 `hw.mainKeys` 设置为“yes”，仿真器将不会在屏幕上显示导航按钮，但你可以使用仿真器侧面板来“按”这些按钮。|yes、no|
|`hw.ramSize`|设备 RAM 大小 &ndash; 仿真器上的物理 RAM 量（以 MB 为单位）。 默认值将根据屏幕大小或外观版本来计算。 尽管增加大小可以提供更快的仿真器操作，但这将耗费开发计算机中更多的资源。||
|`hw.screen`|触控屏类型 &ndash; 定义仿真器上的屏幕类型。 多点触控屏可以在触控界面上跟踪两根或更多的手指。 触控屏仅可以检测单个手指触控事件。 无触控屏不会检测触控事件。|touch、multi-touch、no-touch|
|`hw.sdCard`|SDCard 支持 &ndash; 确定仿真设备是否支持插入和移除虚拟 SD（数字安全）卡。 仿真器使用存储在开发计算机上可装载的磁盘映像来模拟真实 SD 卡设备的分区（请参阅 hw.sdCard.path）。|yes、no|
|`sdcard.size`|SDCard 大小 &ndash; 指定虚拟 SD 卡文件的大小（以字节为单位），此文件位于由设备上可用的 `hw.sdCard.path` 指定的位置 。 如果此值为整数，它则以字节为单位指定大小。 还可以通过将 K、M 或 G 追加到值中来以 KB、MB 或 GB 为单位指定大小。 最小大小为 9 M；最大为 1023 G。||
|`hw.sdCard.path`|SDCard 映像路径 &ndash; 指定开发计算机上 SD 卡分区映像文件的文件名和路径。 例如，可以在 Windows 上将此路径设置为 C:\sd\sdcard.img。||
|`hw.sensors.magnetic_field`|磁场传感器 &ndash; 确定仿真设备是否支持磁场传感器。 磁场传感器（也被称为磁力计）报告沿三个传感器轴测量的环境磁场。 为需要访问指南针读数的应用启用此设置。 例如，导航应用可能需要此传感器以检测用户面朝的方向。|yes、no|
|`hw.sensors.orientation`|方向传感器 &ndash; 确定仿真设备是否提供方向传感器值。 方向传感器测量某个设备围绕三个物理坐标轴（x、y、z）旋转的度数。 注意，自 Android 2.2（API 级别 8）起，方向传感器已被弃用。|yes、no|
|`hw.sensors.proximity`|邻近感应传感器 &ndash; 确定仿真设备是否支持邻近感应传感器。 此传感器测量某个物体相对于设备的视图屏幕的邻近度。 此传感器通常用于确定话筒是否正在向上靠近一个人的耳朵。|yes、no|
|`hw.sensors.temperature`|温度传感器 &ndash; 确定仿真设备是否支持温度传感器。 此传感器以摄氏度 (&deg;C) 为单位测量设备的温度。|yes、no|
|`hw.touchScreen`|触控屏支持 &ndash; 确定仿真设备是否支持触控屏。 触控屏用于在屏幕上直接操作对象。|yes、no|
|`hw.trackBall`|轨迹球支持 &ndash; 确定仿真设备是否支持轨迹球。|yes、no|
|`hw.useext4`|EXT4 文件系统支持 &ndash; 确定仿真设备是否将 Linux EXT4 文件系统用于分区。 因为文件系统类型是自动检测的，因此，此选项已被弃用并忽略。|否|
|`kernel.newDeviceNaming`|内核新设备命名 &ndash; 用于指定内核是否需要新的设备命名方案。 这通常与 Linux 3.10 内核和更高版本配合使用。 如果设置为“autodetect”，仿真器将自动检测内核是否需要新的设备命名方案。|autodetect、yes、no|
|`kernel.parameters`|内核参数 &ndash; 指定 Linux 内核启动参数的字符串。 默认情况下，此设置将留空。||
|`kernel.path`|内核路径 &ndash; 指定 Linux 内核的路径。 如果未指定此路径，仿真器会在仿真器系统目录中查找 kernel-ranchu。||
|`kernel.supportsYaffs2`|YAFFS2 分区支持 **–** &ndash; 确定内核是否支持 YAFFS2 (Yet Another Flash File System 2) 分区。 通常情况下，这仅适用于 Linux 3.10 之前的内核。 如果设置为“autodetect”，仿真器将自动检测内核是否可以装载 YAFFS2 文件系统。|autodetect、yes、no|
|`skin.name`|外观名称 &ndash; Android 仿真器外观的名称。 外观是定义仿真显示的视觉对象和控件元素的文件集合，描述了 AVD 的窗口在开发计算机上的外观。 外观描述屏幕大小、按钮和整体设计，但不会影响应用的操作。||
|`skin.path`|外观路径 &ndash; 包含在 skin.name 中指定的仿真器外观文件的目录的路径。此目录包含 hardware.ini 布局文件以及外观显示元素的映像文件。||
|`skin.dynamic`|外观动态 &ndash; 外观是否为动态的。 如果仿真器是基于指定的宽度和高度构造给定大小的外观，仿真器外观则为动态外观。|否|

