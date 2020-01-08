---
title: Java 绑定元数据
description: C#Xamarin 中的代码通过绑定调用 Java 库，绑定是一种机制，用于提取 Java 本机接口（JNI）中指定的低级别的详细信息。 Xamarin 提供生成这些绑定的工具。 使用此工具，开发人员可以控制如何使用元数据创建绑定，这允许诸如修改命名空间和重命名成员的过程。 本文档讨论元数据的工作原理，概述元数据支持的属性，并说明如何通过修改此元数据解决绑定问题。
ms.prod: xamarin
ms.assetid: 27CB3C16-33F3-F580-E2C0-968005A7E02E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 25a5d79084f7caa78eec4011c047bd19a63ef748
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75487784"
---
# <a name="java-bindings-metadata"></a>Java 绑定元数据

_C#Xamarin 中的代码通过绑定调用 Java 库，绑定是一种机制，用于提取 Java 本机接口（JNI）中指定的低级别的详细信息。Xamarin 提供生成这些绑定的工具。使用此工具，开发人员可以控制如何使用元数据创建绑定，这允许诸如修改命名空间和重命名成员的过程。本文档讨论元数据的工作原理，概述元数据支持的属性，并说明如何通过修改此元数据解决绑定问题。_

## <a name="overview"></a>概述

Xamarin Android **Java 绑定库**尝试自动完成在绑定现有 Android 库所需的大部分工作，并提供了有时称为_绑定生成器_的工具的帮助。 绑定 Java 库时，Xamarin 将检查 Java 类并生成所有要绑定的包、类型和成员的列表。 Api 的此列表存储在一个 XML 文件，请参阅 **\{项目 directory}\obj\Release\api.xml** 有关 **发布** 生成而在 **\{项目directory}\obj\Debug\api.xml** 有关 **调试** 生成。

![在 obj/Debug 文件夹中的 api .xml 文件的位置](java-bindings-metadata-images/java-bindings-metadata-01.png)

绑定生成器将使用**api .xml**文件作为指导，以生成必要C#的包装类。 此 XML 文件的内容是 Google 的_Android 开源项目_格式的变体。
以下代码片段是**api**内容的示例：

```xml
<api>
    <package name="android">
        <class abstract="false" deprecated="not deprecated" extends="java.lang.Object"
            extends-generic-aware="java.lang.Object" 
            final="true" 
            name="Manifest" 
            static="false" 
            visibility="public">
            <constructor deprecated="not deprecated" final="false"
                name="Manifest" static="false" type="android.Manifest"
                visibility="public">
            </constructor>
        </class>
...
</api>
```

在此示例中， **api**在名为 `Manifest` 的 `android` 包中声明了一个类，该类用于扩展 `java.lang.Object`。

在许多情况下，需要人工协助使 Java API 感觉更多 ".NET （如"）或纠正阻止绑定程序集编译的问题。 例如，可能需要将 Java 包名称更改为 .NET 命名空间、重命名类或更改方法的返回类型。

不能通过直接修改**api**来实现这些更改。
而是在 "Java 绑定库" 模板提供的特殊 XML 文件中记录更改。 在编译 Xamarin. Android 绑定程序集时，这些映射文件在创建绑定程序集时受这些映射文件的影响

可以在项目的**转换**文件夹中找到这些 XML 映射文件：

- **MetaData** &ndash; 允许对最终 API 进行更改，如更改生成的绑定的命名空间。 

- **EnumFields** &ndash; 包含 Java `int` 常量和C# `enums` 之间的映射。 

- **EnumMethods** &ndash; 允许更改方法参数，并从 Java `int` 常数返回到C# `enums` 的类型。 

**元数据 .xml**文件是这些文件的最多导入，因为它允许对绑定进行一般用途的更改，例如：

- 重命名命名空间、类、方法或字段，使其遵循 .NET 约定。 

- 删除不需要的命名空间、类、方法或字段。 

- 将类移到不同的命名空间。 

- 添加其他支持类以使绑定的设计遵循 .NET framework 模式。 

让我们更详细地讨论**Metadata。**

## <a name="metadataxml-transform-file"></a>Metadata .xml 转换文件

正如我们已经了解到的，绑定生成器使用文件**Metadata .xml**来影响绑定程序集的创建。
元数据格式使用[XPath](https://www.w3.org/TR/xpath/)语法，与[GAPI 元数据](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)指南中所述的*GAPI 元数据*几乎相同。 此实现几乎是 XPath 1.0 的完整实现，因此支持1.0 标准中的项。 此文件是一种功能强大的基于 XPath 的机制，用于更改、添加、隐藏或移动 API 文件中的任何元素或属性。 元数据规范中的所有规则元素都包含一个路径属性，用于标识规则要应用到的节点。 规则按以下顺序应用：

- **添加节点 &ndash; 将**子节点追加到 path 特性指定的节点。
- **attr** &ndash; 设置 path 特性指定的元素的属性值。
- **删除-node** &ndash; 删除与指定 XPath 匹配的节点。

下面是**元数据 .xml**文件的示例：

```xml
<metadata>
    <!-- Normalize the namespace for .NET -->
    <attr path="/api/package[@name='com.evernote.android.job']" 
        name="managedName">Evernote.AndroidJob</attr>

    <!-- Don't  need these packages for the Xamarin binding/public API --> 
    <remove-node path="/api/package[@name='com.evernote.android.job.v14']" />
    <remove-node path="/api/package[@name='com.evernote.android.job.v21']" />

    <!-- Change a parameter name from the generic p0 to a more meaningful one. -->
    <attr path="/api/package[@name='com.evernote.android.job']/class[@name='JobManager']/method[@name='forceApi']/parameter[@name='p0']" 
        name="name">api</attr>
</metadata>
```

下面列出了 Java API 的一些常用 XPath 元素：

- `interface` &ndash; 用于查找 Java 接口。 例如，`/interface[@name='AuthListener']`

- 用于查找类 `class` &ndash;。 例如，`/class[@name='MapView']`

- 用于在 Java 类或接口上查找方法 `method` &ndash;。 例如，`/class[@name='MapView']/method[@name='setTitleSource']`

- `parameter` &ndash; 确定方法的参数。 例如，`/parameter[@name='p0']`

### <a name="adding-types"></a>添加类型

`add-node` 元素将通知 Xamarin 绑定项目将新的包装类添加到**api。** 例如，下面的代码片段将指示绑定生成器使用构造函数和单个字段创建类：

```xml
<add-node path="/api/package[@name='org.alljoyn.bus']">
    <class abstract="false" deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="true" visibility="public" extends="java.lang.Object">
        <constructor deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="false" type="org.alljoyn.bus.AuthListener.AuthRequest" visibility="public" />
        <field name="p0" type="org.alljoyn.bus.AuthListener.Credentials" />
    </class>
</add-node>
```

### <a name="removing-types"></a>删除类型

可能会指示 Xamarin 绑定生成器忽略 Java 类型，而不会绑定它。 这是通过将 `remove-node` XML 元素添加到**metadata .xml**文件来完成的：

```xml
<remove-node path="/api/package[@name='{package_name}']/class[@name='{name}']" />
```

### <a name="renaming-members"></a>重命名成员

由于 Xamarin 需要原始 Java 本机接口（JNI）名称，因此无法通过直接编辑**api**文件来重命名成员。 因此，不能更改 `//class/@name` 特性;如果是，则绑定将不起作用。

请考虑要重命名类型的情况，`android.Manifest`。
为实现此目的，我们可能会尝试直接编辑**api**并重命名类，如下所示：

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="name">NewName</attr>
```

这将导致绑定生成器为包装器类创建C#以下代码：

```csharp
[Register ("android/NewName")]
public class NewName : Java.Lang.Object { ... }
```

请注意，包装类已重命名为 `NewName`，而原始 Java 类型仍为 `Manifest`。 Xamarin Android 绑定类不再可能访问 `android.Manifest`上的任何方法;包装类绑定到不存在的 Java 类型。

若要正确更改已包装的类型（或方法）的托管名称，需要设置 `managedName` 属性，如以下示例中所示：

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="managedName">NewName</attr>
```

<a name="Renaming_EventArg_Wrapper_Classes" />

#### <a name="renaming-eventarg-wrapper-classes"></a>重命名 `EventArg` 包装类

当 Xamarin 绑定生成器标识_侦听器类型_的 `onXXX` setter 方法时，将生成一个C#事件和 `EventArgs` 子类，以支持基于 Java 的侦听器模式的 .net flavoured API。 例如，请考虑以下 Java 类和方法：

```xml
com.someapp.android.mpa.guidance.NavigationManager.on2DSignNextManuever(NextManueverListener listener);
```

Xamarin 会将前缀 `on` 从 setter 方法中删除，而改用 `2DSignNextManuever` 作为 `EventArgs` 子类的名称的基础。 子类的名称如下所示：

```csharp
NavigationManager.2DSignNextManueverEventArgs
```

这不是合法C#的类名。 若要更正此问题，绑定作者必须使用 `argsType` 属性，并为 `EventArgs` 子类C#提供有效名称：

```xml
<attr path="/api/package[@name='com.someapp.android.mpa.guidance']/
    interface[@name='NavigationManager.Listener']/
    method[@name='on2DSignNextManeuver']" 
    name="argsType">NavigationManager.TwoDSignNextManueverEventArgs</attr>
```

## <a name="supported-attributes"></a>支持的属性

以下各节介绍用于转换 Java Api 的一些属性。

### <a name="argstype"></a>argsType

此属性位于 setter 方法上，用于命名将为支持 Java 侦听器而生成的 `EventArg` 子类。 稍后在本指南的 "[重命名 EventArg 包装](#Renaming_EventArg_Wrapper_Classes)器" 一节中对此进行了详细介绍。

### <a name="eventname"></a>eventName

指定事件的名称。 如果为空，它将禁止生成事件。
标题[重命名 EventArg 包装类](#Renaming_EventArg_Wrapper_Classes)部分对此进行了详细介绍。

### <a name="managedname"></a>managedName

这用于更改包、类、方法或参数的名称。 例如，要将 Java 类的名称更改 `MyClass` 为 `NewClassName`：

```xml
<attr path="/api/package[@name='com.my.application']/class[@name='MyClass']" 
    name="managedName">NewClassName</attr>
```

下一个示例演示了用于将方法重命名为 `Java.Lang.Object.NewManagedName``java.lang.object.toString` 的 XPath 表达式：

```xml
<attr path="/api/package[@name='java.lang']/class[@name='Object']/method[@name='toString']" 
    name="managedName">NewMethodName</attr>
```

### <a name="managedtype"></a>managedType

`managedType` 用于更改方法的返回类型。 在某些情况下，绑定生成器将错误地推断 Java 方法的返回类型，这将导致编译时错误。 在这种情况下，一种可能的解决方案是更改方法的返回类型。

例如，绑定生成器认为 `de.neom.neoreadersdk.resolution.compareTo()` 的 Java 方法应返回 `int` 并采用 `Object` 作为参数，导致错误消息**错误 CS0535： "DE。Neom "不实现接口成员" Neoreadersdk （CompareTo （.Java.） "** 中的。 下面的代码片段演示如何将生成C#的方法的第一个参数类型从 `DE.Neom.Neoreadersdk.Resolution` 更改为 `Java.Lang.Object`： 

```xml
<attr path="/api/package[@name='de.neom.neoreadersdk']/
    class[@name='Resolution']/
    method[@name='compareTo' and count(parameter)=1 and
    parameter[1][@type='de.neom.neoreadersdk.Resolution']]/
    parameter[1]" name="managedType">Java.Lang.Object</attr> 
```

### <a name="managedreturn"></a>managedReturn

更改方法的返回类型。 这不会更改返回特性（因为更改返回特性会导致对 JNI 签名进行不兼容的更改）。 在下面的示例中，`append` 方法的返回类型从 "`SpannableStringBuilder`" 更改为 "`IAppendable` （回想C#一下不支持协变返回类型）：

```xml
<attr path="/api/package[@name='android.text']/
    class[@name='SpannableStringBuilder']/
    method[@name='append']" 
    name="managedReturn">Java.Lang.IAppendable</attr>
```

### <a name="obfuscated"></a>辨认

对 Java 库进行模糊处理的工具可能会影响 Xamarin 绑定生成器及其生成C#包装器类的功能。 模糊类的特征包括： 

- Class 名称中包含 **$** ，即 **$.class**
- 类名称完全泄露小写字符，即**类**

此代码片段举例说明如何生成 "未经过模糊处理" C#类型：

```xml
<attr path="/api/package[@name='{package_name}']/class[@name='{name}']" 
    name="obfuscated">false</attr>
```

### <a name="propertyname"></a>propertyName

此属性可用于更改托管属性的名称。

使用 `propertyName` 的一种特殊情况涉及到 Java 类只有一个 getter 方法用于字段的情况。 在这种情况下，绑定生成器将需要创建只写属性，这是不建议在 .NET 中使用的属性。 以下代码片段演示了如何通过将 `propertyName` 设置为空字符串来 "删除" .NET 属性：

```xml
<attr path="/api/package[@name='org.java_websocket.handshake']/class[@name='HandshakeImpl1Client']/method[@name='setResourceDescriptor' 
    and count(parameter)=1 
    and parameter[1][@type='java.lang.String']]" 
    name="propertyName"></attr>
<attr path="/api/package[@name='org.java_websocket.handshake']/class[@name='HandshakeImpl1Client']/method[@name='getResourceDescriptor' 
    and count(parameter)=0]" 
    name="propertyName"></attr>
```

请注意，资源库和 getter 方法仍将由绑定生成器创建。

### <a name="sender"></a>sender

当方法映射到事件时，指定方法的哪个参数应为 `sender` 参数。 该值可以是 `true` 或 `false`。 例如：

```xml
<attr path="/api/package[@name='android.app']/
    interface[@name='TimePickerDialog.OnTimeSetListener']/
    method[@name='onTimeSet']/
    parameter[@name='view']" 
    name="sender">true</ attr>
```

### <a name="visibility"></a>可见性

此属性用于更改类、方法或属性的可见性。 例如，可能有必要升级 `protected` Java 方法，使其对应C#的包装程序 `public`：

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method --> 
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

## <a name="enumfieldsxml-and-enummethodsxml"></a>EnumFields 和 EnumMethods

在某些情况下，Android 库使用整数常量表示传递到库的属性或方法的状态。 在许多情况下，将这些整数常量绑定到中C#的枚举很有用。 若要简化此映射，请使用绑定项目中的**EnumFields**和**EnumMethods**文件。 

### <a name="defining-an-enum-using-enumfieldsxml"></a>使用 EnumFields 定义枚举

**EnumFields**文件包含 Java `int` 常量和C# `enums`之间的映射。 让我们以下面的示例为例C# ，为一组 `int` 常量创建枚举： 

```xml 
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit">
    <field jni-name="UNIT_SECOND" clr-name="Second" value="0" />
    <field jni-name="UNIT_METER" clr-name="Meter" value="1" />
    <field jni-name="UNIT_MILIWATT_HOURS" clr-name="MilliwattHour" value="2" />
</mapping>
```

在这里，我们已将 Java 类 `SKRealReachSettings`，并C#在命名空间 `Skobbler.Ngx.Map.RealReach`中定义了一个名为 `SKMeasurementUnit` 的枚举。 `field` 条目定义 Java 常量的名称（示例 `UNIT_SECOND`）、枚举条目的名称（示例 `Second`）和两个实体（例如 `0`）表示的整数值。 

### <a name="defining-gettersetter-methods-using-enummethodsxml"></a>使用 EnumMethods 定义 Getter/Setter 方法

**EnumMethods**文件允许更改方法参数，并从 Java `int` 常数返回到C# `enums`的类型。 换句话说，它将C#枚举（在**EnumFields**文件中定义）的读取和写入映射到 Java `int` 常量 `get` 和 `set` 方法。

根据上面定义的 `SKRealReachSettings` 枚举，下面的**EnumMethods**文件将为此枚举定义 getter/setter：

```xml
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings">
    <method jni-name="getMeasurementUnit" parameter="return" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
    <method jni-name="setMeasurementUnit" parameter="measurementUnit" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
</mapping>
```

第一个 `method` 行将 Java `getMeasurementUnit` 方法的返回值映射到 `SKMeasurementUnit` 枚举。 第二个 `method` 行将 `setMeasurementUnit` 的第一个参数映射到相同的枚举。

完成所有这些更改后，可以使用 Xamarin 中的以下代码设置 `MeasurementUnit`： 

```csharp
realReachSettings.MeasurementUnit = SKMeasurementUnit.Second;
```

## <a name="summary"></a>摘要

本文讨论了如何使用元数据从*Google* *AOSP 格式*转换 API 定义。 在使用*Metadata*覆盖了可能的更改后，它将检查在重命名成员时遇到的限制，并显示受支持的 xml 特性的列表，描述每个特性的使用时间。

## <a name="related-links"></a>相关链接

- [使用 JNI](~/android/platform/java-integration/working-with-jni.md)
- [绑定 Java 库](~/android/platform/binding-java-library/index.md)
- [GAPI 元数据](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
