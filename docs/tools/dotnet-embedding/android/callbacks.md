---
title: 在 Android 上的回调
ms.prod: xamarin
ms.assetid: F3A7A4E6-41FE-4F12-949C-96090815C5D6
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: c6eaf4dd90b172053b4b87e3427cfe35213c6727
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61215873"
---
# <a name="callbacks-on-android"></a>在 Android 上的回调

调用从 Java 到C#有点*有风险的业务*。 也就是说没有*模式*回调从C#java; 但是，它是比我们想要更复杂。

我们将介绍用于执行最适用于 Java 的回调的三个选项：

- 抽象类
- 接口
- 虚拟方法

## <a name="abstract-classes"></a>抽象类

这是最简单的路由的回调，因此，我建议使用`abstract`如果只想要获取在最简单的窗体中工作的回调。

首先，C#我们想要实现的 Java 类：

```csharp
[Register("mono.embeddinator.android.AbstractClass")]
public abstract class AbstractClass : Java.Lang.Object
{
    public AbstractClass() { }

    public AbstractClass(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer) { }

    [Export("getText")]
    public abstract string GetText();
}
```

下面是用于实现此目的的详细信息：

- `[Register]` 生成一个很好的包名称在 Java-您将收到没有它自动生成的包名称。
- 子类化`Java.Lang.Object`.NET 嵌入到的信号，以运行通过 Xamarin.Android 的 Java 生成器的类。
- 空的构造函数： 是将想要从 Java 代码使用。
- `(IntPtr, JniHandleOwnership)` 构造函数： 是 Xamarin.Android 创建的用于C#的等效的 Java 对象。
- `[Export]` 发出信号 Xamarin.Android 公开到 Java 的方法。 我们还可以更改方法名称，因为在 Java 世界喜欢使用大小写的方法。

下一步让C#方法来测试该方案：

```csharp
[Register("mono.embeddinator.android.JavaCallbacks")]
public class JavaCallbacks : Java.Lang.Object
{
    [Export("abstractCallback")]
    public static string AbstractCallback(AbstractClass callback)
    {
        return callback.GetText();
    }
}
```
`JavaCallbacks` 可能是任何类来测试此操作，只要它是`Java.Lang.Object`。

现在，运行在.NET 程序集生成 AAR.NET 嵌入。 请参阅[入门指南](~/tools/dotnet-embedding/get-started/java/android.md)有关详细信息。

导入后 AAR 文件 Android Studio，让我们来编写单元测试：

```java
@Test
public void abstractCallback() throws Throwable {
    AbstractClass callback = new AbstractClass() {
        @Override
        public String getText() {
            return "Java";
        }
    };

    assertEquals("Java", callback.getText());
    assertEquals("Java", JavaCallbacks.abstractCallback(callback));
}
```
因此我们：

- 实现`AbstractClass`匿名类型使用 java
- 确保我们实例返回`"Java"`通过 Java
- 确保我们实例返回`"Java"`从C#
- 添加`throws Throwable`，因为C#与当前标记的构造函数 `throws`

如果我们运行此单元测试作为的是，它将如失败并出现错误：

```csharp
System.NotSupportedException: Unable to find Invoker for type 'Android.AbstractClass'. Was it linked away?
```

缺少的内容还有`Invoker`类型。 这是一个的子类`AbstractClass`，将转发C#对 Java 的调用。 如果 Java 对象会进入C#world 和等效的C#类型是抽象的则 Xamarin.Android 将自动查找C#类型的后缀`Invoker`中使用C#代码。

Xamarin.Android 使用此`Invoker`不仅限于 Java 绑定项目的模式。

下面是我们的实现`AbstractClassInvoker`:
```csharp
class AbstractClassInvoker : AbstractClass
{
    IntPtr class_ref, id_gettext;

    public AbstractClassInvoker(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer)
    {
        IntPtr lref = JNIEnv.GetObjectClass(Handle);
        class_ref = JNIEnv.NewGlobalRef(lref);
        JNIEnv.DeleteLocalRef(lref);
    }

    protected override Type ThresholdType
    {
        get { return typeof(AbstractClassInvoker); }
    }

    protected override IntPtr ThresholdClass
    {
        get { return class_ref; }
    }

    public override string GetText()
    {
        if (id_gettext == IntPtr.Zero)
            id_gettext = JNIEnv.GetMethodID(class_ref, "getText", "()Ljava/lang/String;");
        IntPtr lref = JNIEnv.CallObjectMethod(Handle, id_gettext);
        return GetObject<Java.Lang.String>(lref, JniHandleOwnership.TransferLocalRef)?.ToString();
    }

    protected override void Dispose(bool disposing)
    {
        if (class_ref != IntPtr.Zero)
            JNIEnv.DeleteGlobalRef(class_ref);
        class_ref = IntPtr.Zero;

        base.Dispose(disposing);
    }
}
```

没有较长操作，我们：

- 添加了一个带有后缀的类`Invoker`子类 `AbstractClass`
- 添加`class_ref`用于保存该子类的 Java 类的 JNI 引用我们C#类
- 添加`id_gettext`用于保存对 Java JNI 引用`getText`方法
- 包含`(IntPtr, JniHandleOwnership)`构造函数
- 实现`ThresholdType`和`ThresholdClass`作为一项要求适用于 Xamarin.Android 以了解详细信息 `Invoker`
- `GetText` 需要查找 Java`getText`使用适当的 JNI 签名的方法，并调用它
- `Dispose` 只需清除对引用 `class_ref`

在添加此类并生成新 AAR 之后, 我们单元测试通过。 您可以看到此模式的回调不是*理想*，但可行。

有关 Java 互操作的详细信息，请参阅出色[Xamarin.Android 文档](~/android/platform/java-integration/working-with-jni.md)有关此主题。

## <a name="interfaces"></a>接口

接口是类似于抽象类，除了一个详细信息：Xamarin.Android 不会为其生成 Java。 这是因为.NET 嵌入之前, 情况不是很多 Java 将在其中实施C#接口。

让我们假设我们有以下C#接口：

```csharp
[Register("mono.embeddinator.android.IJavaCallback")]
public interface IJavaCallback : IJavaObject
{
    [Export("send")]
    void Send(string text);
}
```

`IJavaObject` .NET 嵌入向发出信号，这是 Xamarin.Android 接口，但否则这就是完全相同`abstract`类。

因为 Xamarin.Android 将当前生成此接口的 Java 代码，添加以下 Java 到在C#项目：

```java
package mono.embeddinator.android;

public interface IJavaCallback {
    void send(String text);
}
```

您可以将该文件放在任意位置，但请确保将其生成操作设置为`AndroidJavaSource`。 这将指示.NET 嵌入将其复制到要获取编译到 AAR 文件的正确目录。

下一步，`Invoker`实现将完全相同：

```csharp
class IJavaCallbackInvoker : Java.Lang.Object, IJavaCallback
{
    IntPtr class_ref, id_send;

    public IJavaCallbackInvoker(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer)
    {
        IntPtr lref = JNIEnv.GetObjectClass(Handle);
        class_ref = JNIEnv.NewGlobalRef(lref);
        JNIEnv.DeleteLocalRef(lref);
    }

    protected override Type ThresholdType
    {
        get { return typeof(IJavaCallbackInvoker); }
    }

    protected override IntPtr ThresholdClass
    {
        get { return class_ref; }
    }

    public void Send(string text)
    {
        if (id_send == IntPtr.Zero)
            id_send = JNIEnv.GetMethodID(class_ref, "send", "(Ljava/lang/String;)V");
        JNIEnv.CallVoidMethod(Handle, id_send, new JValue(new Java.Lang.String(text)));
    }

    protected override void Dispose(bool disposing)
    {
        if (class_ref != IntPtr.Zero)
            JNIEnv.DeleteGlobalRef(class_ref);
        class_ref = IntPtr.Zero;

        base.Dispose(disposing);
    }
}
```

生成后 AAR 文件，我们可以编写下一个传递单位的 Android Studio 中的测试:

```java
class ConcreteCallback implements IJavaCallback {
    public String text;
    @Override
    public void send(String text) {
        this.text = text;
    }
}

@Test
public void interfaceCallback() {
    ConcreteCallback callback = new ConcreteCallback();
    JavaCallbacks.interfaceCallback(callback, "Java");
    assertEquals("Java", callback.text);
}
```

## <a name="virtual-methods"></a>虚拟方法

重写`virtual`在 Java 中是可行的但是不完美的体验。

我们假设您有以下C#类：

```csharp
[Register("mono.embeddinator.android.VirtualClass")]
public class VirtualClass : Java.Lang.Object
{
    public VirtualClass() { }

    public VirtualClass(IntPtr handle, JniHandleOwnership transfer) : base(handle, transfer) { }

    [Export("getText")]
    public virtual string GetText() { return "C#"; }
}
```

如果您遵循`abstract`类上面示例中，效果会除了一个详细信息：_不会查找 Xamarin.Android `Invoker`_ 。

若要解决此问题，请修改C#类就`abstract`:

```csharp
public abstract class VirtualClass : Java.Lang.Object
```

这并不理想，但获取此方案发挥作用。 Xamarin.Android 将选取`VirtualClassInvoker`，并可以使用 Java`@Override`方法上。

## <a name="callbacks-in-the-future"></a>在将来的回调

有几件事我们可以以改进了这些方案：

1. `throws Throwable` 在C#构造函数对此固定[PR](https://github.com/xamarin/java.interop/pull/170)。
1. 请在 Xamarin.Android 中的 Java 生成器支持的接口。
    - 这无需添加的生成操作的 Java 源文件`AndroidJavaSource`。
1. 请一种方法适用于 Xamarin.Android 加载`Invoker`虚拟类。
    - 这会需要将标记中的类中删除我们`virtual`示例`abstract`。
1. 生成`Invoker`自动类的.NET 嵌入
    - 这将会比较复杂，但可行。 Xamarin.Android 已经实现了为 Java 绑定项目类似于如下内容。

许多工作要做，但可使用.NET 嵌入到这些增强功能。

## <a name="further-reading"></a>其他阅读材料

* [Android 上开始使用](~/tools/dotnet-embedding/get-started/java/android.md)
* [Android 的初步研究](~/tools/dotnet-embedding/android/index.md)
* [.NET 嵌入限制](~/tools/dotnet-embedding/limitations.md)
* [参与到开放源代码项目](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md)
* [错误代码和说明](~/tools/dotnet-embedding/errors.md)
