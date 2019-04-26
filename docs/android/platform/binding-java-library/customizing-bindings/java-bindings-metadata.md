---
title: Java 绑定元数据
description: C#在 Xamarin.Android 中的代码是一种机制，用于提取指定了在 Java 本机接口 (JNI) 的低级别详细信息的绑定通过调用 Java 库。 Xamarin.Android 提供了一个工具，将生成这些绑定。 此工具可让开发人员控件如何通过使用元数据，这允许如修改命名空间和重命名成员的过程创建一个绑定。 本文档介绍元数据的工作原理，汇总了的属性的元数据支持，并说明如何通过修改此元数据来纠正绑定问题。
ms.prod: xamarin
ms.assetid: 27CB3C16-33F3-F580-E2C0-968005A7E02E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 06a7a3b00934b7a2f3eeb4fcfa6fc90071901ba0
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60955654"
---
# <a name="java-bindings-metadata"></a>Java 绑定元数据

_C#在 Xamarin.Android 中的代码是一种机制，用于提取指定了在 Java 本机接口 (JNI) 的低级别详细信息的绑定通过调用 Java 库。Xamarin.Android 提供了一个工具，将生成这些绑定。此工具可让开发人员控件如何通过使用元数据，这允许如修改命名空间和重命名成员的过程创建一个绑定。本文档介绍元数据的工作原理，汇总了的属性的元数据支持，并说明如何通过修改此元数据来纠正绑定问题。_


## <a name="overview"></a>概述

Xamarin.Android **Java 绑定库**尝试自动执行所需绑定有时称为的工具的帮助的现有 Android 库的工作大部分_绑定生成器_。 在绑定 Java 库时，Xamarin.Android 将检查的 Java 类，并生成所有包、 类型和成员的列表的绑定。 Api 的此列表存储在一个 XML 文件，请参阅 **\{项目 directory}\obj\Release\api.xml** 有关 **发布** 生成而在 **\{项目directory}\obj\Debug\api.xml** 有关 **调试** 生成。

![Obj/Debug 文件夹中的 api.xml 文件的位置](java-bindings-metadata-images/java-bindings-metadata-01.png)

将使用绑定生成器**api.xml**指导原则是用于生成所需的文件C#的包装类。 此 XML 文件的内容是 Google 的一种变体_Android Open Source Project_格式。
以下代码片段示范了的内容**api.xml**:

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

在此示例中， **api.xml**声明中的一个类`android`名为包`Manifest`扩展`java.lang.Object`。

在许多情况下，人工协助都需要使 Java API 感觉更多"等.NET"或以更正问题导致从编译的绑定程序集。 例如，它可能需要将 Java 包名称更改为.NET 命名空间、 重命名一个类，或更改一种方法的返回类型。

这些更改不通过修改**api.xml**直接。
相反，Java 绑定库模板提供的特殊 XML 文件中记录的更改。 在编译 Xamarin.Android 绑定程序集时，绑定生成器创建的绑定程序集时将受这些映射文件

这些 XML 映射文件，可以在下文**转换**项目文件夹：

-   **MetaData.xml** &ndash;允许更改不会对最终的 API，例如，更改所生成的绑定的命名空间。 

-   **EnumFields.xml** &ndash;包含 Java 之间的映射`int`常量和C# `enums` 。 

-   **EnumMethods.xml** &ndash;允许通过 Java 更改方法参数和返回类型`int`常量到C# `enums` 。 

**MetaData.xml**文件是最导入这些文件，因为它允许对等绑定的常规用途更改：

-   因此，它们遵循.NET 约定重命名命名空间、 类、 方法或字段。 

-   正在删除命名空间、 类、 方法或不需要的字段。 

-   将类移到不同的命名空间。 

-   添加其他支持类，以使绑定的设计遵循.NET framework 模式。 

允许继续讨论**Metadata.xml**中更多详细信息。


## <a name="metadataxml-transform-file"></a>Metadata.xml 转换文件

正如我们已经已经获知，该文件**Metadata.xml**绑定生成器用于影响绑定程序集的创建。
使用元数据格式[XPath](https://www.w3.org/TR/xpath/)语法并且几乎完全相同*GAPI 元数据*中所述[GAPI 元数据](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)指南。 此实现是几乎 XPath 1.0 的完整实现，因此支持标准 1.0 中的项。 此文件是强大的基于 XPath 机制来更改、 添加、 隐藏或移动 API 文件中的任何元素或属性。 所有元数据规范中的规则元素都包括一个路径属性来确定的规则是要应用的节点。 按以下顺序应用规则：

* **添加节点**&ndash;将子节点追加到指定的路径属性的节点。
* **attr** &ndash;设置路径属性指定的元素的属性的值。
* **删除节点**&ndash;匹配指定的 XPath 节点中删除。

以下是一种**Metadata.xml**文件：

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

下面列出了一些更常使用的 XPath 元素的 Java api:

-   `interface` &ndash; 用于查找 Java 接口。 例如`/interface[@name='AuthListener']`。

-   `class` &ndash; 用于查找一个类。 例如`/class[@name='MapView']`。

-   `method` &ndash; 用于查找上一个 Java 类或接口的方法。 例如`/class[@name='MapView']/method[@name='setTitleSource']`。

-   `parameter` &ndash; 标识一个方法的参数。 例如 `/parameter[@name='p0']`



### <a name="adding-types"></a>添加类型

`add-node`元素将告知 Xamarin.Android 绑定项目添加到新的包装器类**api.xml**。 例如，以下代码片段将指示要创建的类具有一个构造函数和单个字段的绑定生成器：

```xml
<add-node path="/api/package[@name='org.alljoyn.bus']">
    <class abstract="false" deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="true" visibility="public" extends="java.lang.Object">
        <constructor deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="false" type="org.alljoyn.bus.AuthListener.AuthRequest" visibility="public" />
        <field name="p0" type="org.alljoyn.bus.AuthListener.Credentials" />
    </class>
</add-node>
```


### <a name="removing-types"></a>删除类型

很可能会指示要忽略的 Java 类型并不将其绑定的 Xamarin.Android 绑定生成器。 这是通过添加`remove-node`XML 元素**metadata.xml**文件：

```xml
<remove-node path="/api/package[@name='{package_name}']/class[@name='{name}']" />
```

### <a name="renaming-members"></a>重命名成员

重命名成员不能通过直接编辑**api.xml**文件，因为 Xamarin.Android 需要原始 Java 本机接口 (JNI) 名称。 因此，`//class/@name`属性不能进行更改; 如果是，绑定将不起作用。

我们想要重命名类型，这种的情况，请考虑`android.Manifest`。
若要实现此目的，我们可能会尝试直接编辑**api.xml**和重命名一个类如下所示：

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="name">NewName</attr>
```

这将导致绑定生成器创建以下C#包装器类的代码：

```csharp
[Register ("android/NewName")]
public class NewName : Java.Lang.Object { ... }
```

请注意，包装器类已重命名为`NewName`，而原始 Java 类型仍为`Manifest`。 不再可以访问任意方法上的 Xamarin.Android 绑定类`android.Manifest`; 包装类绑定到不存在的 Java 类型。

若要正确更改托管包装类型 （或方法） 的名称，是需要设置`managedName`特性，如本示例中所示：

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="managedName">NewName</attr>
```

<a name="Renaming_EventArg_Wrapper_Classes" />

#### <a name="renaming-eventarg-wrapper-classes"></a>重命名`EventArg`包装类

Xamarin.Android 绑定生成器时标识`onXXX`setter 方法_侦听器类型_、C#事件和`EventArgs`将生成子类以支持.NET flavoured API 的基于 Java 的侦听器模式。 作为示例，请考虑下面的 Java 类和方法：

```xml
com.someapp.android.mpa.guidance.NavigationManager.on2DSignNextManuever(NextManueverListener listener);
```

Xamarin.Android 将删除该前缀`on`从资源库方法，改为使用`2DSignNextManuever`作为名称的基础`EventArgs`子类。 子类将被命名为类似于：

```csharp
NavigationManager.2DSignNextManueverEventArgs
```

这不是合法C#类名。 若要更正此问题，绑定作者必须使用`argsType`特性，并提供有效C#的名称`EventArgs`子类：
 
```xml
<attr path="/api/package[@name='com.someapp.android.mpa.guidance']/
    interface[@name='NavigationManager.Listener']/
    method[@name='on2DSignNextManeuver']" 
    name="argsType">NavigationManager.TwoDSignNextManueverEventArgs</attr>
```

 

## <a name="supported-attributes"></a>支持的属性

以下各节介绍了一些用于转换 Java Api 的属性。

### <a name="argstype"></a>argsType

此特性置于 setter 方法来命名`EventArg`将生成以支持 Java 侦听器的子类。 这更详细地下面部分中所述[重命名 EventArg 包装类](#Renaming_EventArg_Wrapper_Classes)稍后在本指南中。

### <a name="eventname"></a>eventName

指定事件的名称。 如果为空，它会抑制事件生成。
这部分标题中的更多详细信息中所述[重命名 EventArg 包装类](#Renaming_EventArg_Wrapper_Classes)。

### <a name="managedname"></a>managedName

这用于更改包、 类、 方法或参数的名称。 例如，若要更改的 Java 类的名称`MyClass`到`NewClassName`:

```xml
<attr path="/api/package[@name='com.my.application']/class[@name='MyClass']" 
    name="managedName">NewClassName</attr>
```

下一个示例说明了重命名该方法的 XPath 表达式`java.lang.object.toString`到`Java.Lang.Object.NewManagedName`:

```xml
<attr path="/api/package[@name='java.lang']/class[@name='Object']/method[@name='toString']" 
    name="managedName">NewMethodName</attr>
```

### <a name="managedtype"></a>managedType

`managedType` 用于更改一种方法的返回类型。 在某些情况下绑定生成器会错误地推断返回类型的 Java 方法，这将导致编译时错误。 在此情况下一个可能的解决方案是更改该方法的返回类型。

例如，绑定生成器认为的 Java 方法`de.neom.neoreadersdk.resolution.compareTo()`应返回`int`，这会导致错误消息**错误 CS0535:DE。Neom.Neoreadersdk.Resolution 不实现接口成员 Java.Lang.IComparable.CompareTo(Java.Lang.Object)**。 以下代码片段演示如何更改所生成的返回类型C#方法从`int`到`Java.Lang.Object`: 

```xml
<attr path="/api/package[@name='de.neom.neoreadersdk']/
    class[@name='Resolution']/
    method[@name='compareTo' and count(parameter)=1 and
    parameter[1][@type='de.neom.neoreadersdk.Resolution']]/
    parameter[1]"name="managedType">Java.Lang.Object</attr> 
```

### <a name="managedreturn"></a>managedReturn

更改方法的返回类型。 这不会更改返回值属性 （作为有所变化，返回属性可能会导致不兼容的更改到的 JNI 签名）。 在下面的示例中的返回类型的`append`方法更改从`SpannableStringBuilder`到`IAppendable`(回想一下，C#不支持协变返回类型):

```xml
<attr path="/api/package[@name='android.text']/
    class[@name='SpannableStringBuilder']/
    method[@name='append']" 
    name="managedReturn">Java.Lang.IAppendable</attr>
```

### <a name="obfuscated"></a>经过模糊处理

模糊处理 Java 库的工具可能会影响 Xamarin.Android 绑定生成器，并生成其功能与C#包装器类。 经过模糊处理类的特征包括： 

* Class 名称中包含 **$** ，即 **$.class** 
* 类名完全破坏的小写字符，即**a.class**

此代码片段示范了如何生成一个"未经过模糊处理"C#类型：

```xml
<attr path="/api/package[@name='{package_name}']/class[@name='{name}']" 
    name="obfuscated">false</attr>
```

### <a name="propertyname"></a>propertyName

此属性可以用于更改托管属性的名称。

使用的专用的化`propertyName`涉及这种情况，其中一个 Java 类具有仅一个字段的 getter 方法。 在这种情况下绑定生成器会想要创建一个只写属性，不建议在.NET 中的内容。 以下代码片段演示如何通过设置"删除"的.NET 属性`propertyName`为空字符串：

```xml
<attr path="/api/package[@name='org.java_websocket.handshake']/class[@name='HandshakeImpl1Client']/method[@name='setResourceDescriptor' 
    and count(parameter)=1 
    and parameter[1][@type='java.lang.String']]" 
    name="propertyName"></attr>
<attr path="/api/package[@name='org.java_websocket.handshake']/class[@name='HandshakeImpl1Client']/method[@name='getResourceDescriptor' 
    and count(parameter)=0]" 
    name="propertyName"></attr>
```

请注意仍由绑定生成器将创建的 setter 和 getter 方法。

### <a name="sender"></a>sender

指定一种方法的参数应为`sender`时该方法映射到一个事件参数。 值可以是`true`或`false`。 例如：

```xml
<attr path="/api/package[@name='android.app']/
    interface[@name='TimePickerDialog.OnTimeSetListener']/
    method[@name='onTimeSet']/
    parameter[@name='view']" 
    name="sender">true</ attr>
```

### <a name="visibility"></a>可见性

此属性用于类、 方法或属性的可见性更改。 例如，可能有必要将提升`protected`Java 方法，以便它的相应C#包装是否`public`:

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method --> 
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

## <a name="enumfieldsxml-and-enummethodsxml"></a>EnumFields.xml 和 EnumMethods.xml

有些情况下，Android 库使用整数常量表示传递到库的属性或方法的状态。 在许多情况下，最好先绑定到枚举中的这些整数常量C#。 若要简化此映射，请使用**EnumFields.xml**并**EnumMethods.xml**绑定项目中的文件。 

### <a name="defining-an-enum-using-enumfieldsxml"></a>定义使用 EnumFields.xml 枚举

**EnumFields.xml**文件包含 Java 之间的映射`int`常量和C# `enums`。 让我们看下面的示例对C#正在创建的一组用于枚举`int`常量： 

```xml 
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit">
    <field jni-name="UNIT_SECOND" clr-name="Second" value="0" />
    <field jni-name="UNIT_METER" clr-name="Meter" value="1" />
    <field jni-name="UNIT_MILIWATT_HOURS" clr-name="MilliwattHour" value="2" />
</mapping>
```

此处我们已采取的 Java 类`SKRealReachSettings`并定义C#称作`SKMeasurementUnit`命名空间中`Skobbler.Ngx.Map.RealReach`。 `field`条目定义的 Java 常量的名称 (示例`UNIT_SECOND`)，枚举项的名称 (示例`Second`)，并且这两个实体所表示的整数值 (示例`0`)。 

### <a name="defining-gettersetter-methods-using-enummethodsxml"></a>定义使用 EnumMethods.xml 的 Getter/Setter 方法

**EnumMethods.xml**文件允许从 Java 更改方法参数和返回类型`int`常量到C# `enums`。 换而言之，它将映射的读取和写入的C#枚举 (中定义**EnumFields.xml**文件) 到 Java`int`常量`get`并`set`方法。

给定`SKRealReachSettings`枚举定义更高版本，以下**EnumMethods.xml**文件会定义 getter/setter 此枚举：

```xml
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings">
    <method jni-name="getMeasurementUnit" parameter="return" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
    <method jni-name="setMeasurementUnit" parameter="measurementUnit" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
</mapping>
```

第一个`method`行映射 Java 的返回值`getMeasurementUnit`方法`SKMeasurementUnit`枚举。 第二个`method`行映射的第一个参数`setMeasurementUnit`到相同的枚举。

所有这些更改后，你可以使用下面的代码在 Xamarin.Android 中设置`MeasurementUnit`: 

```csharp
realReachSettings.MeasurementUnit = SKMeasurementUnit.Second;
```


## <a name="summary"></a>总结

本文讨论了如何 Xamarin.Android 使用元数据转换中的 API 定义*Google* *AOSP 格式*。 介绍可能的更改后使用*Metadata.xml*，它会检查重命名成员时遇到的限制和显示受支持的 XML 属性，描述应在何时使用每个属性的列表。



## <a name="related-links"></a>相关链接

- [使用 JNI](~/android/platform/java-integration/working-with-jni.md)
- [绑定 Java 库](~/android/platform/binding-java-library/index.md)
- [GAPI 元数据](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
