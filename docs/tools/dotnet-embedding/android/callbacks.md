---
title: Android 上的回调
ms.prod: xamarin
ms.assetid: F3A7A4E6-41FE-4F12-949C-96090815C5D6
author: davidortinau
ms.author: daortin
ms.date: 11/14/2017
ms.openlocfilehash: 2d1d5b8985d132e5a5839e3cd23aaec32fc3815a
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76725111"
---
# <a name="callbacks-on-android"></a>Android 上的回调

从C#调用到 Java 是一个有*风险的业务*。 这就是说，从C#到 Java 的回调*模式*;但是，这比我们喜欢的复杂。

我们将介绍三个用于执行最适合 Java 的回调的选项：

- 추상 클래스
- 인터페이스
- 虚方法

## <a name="abstract-classes"></a>추상 클래스

这是最简单的回调路由，因此，如果你只是想要在最简单的形式使用回拨，则建议使用 `abstract`。

让我们从一个C#类开始，我们希望 Java 实现：

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

下面是执行此操作的详细信息：

- `[Register]` 在 Java 中生成良好的包名称--你将在不使用它的情况下获取自动生成的包名称。
- 子类化 `Java.Lang.Object` 向 .NET 嵌入发出的信号通过 Xamarin 的 Java 生成器运行类。
- 空构造函数：将从 Java 代码使用。
- `(IntPtr, JniHandleOwnership)` 构造函数： Xamarin Android 将使用它来创建C#等效的 Java 对象。
- `[Export]` 信号 Xamarin 向 Java 公开方法。 由于 Java 世界喜欢使用小写方法，因此还可以更改方法名称。

接下来，我们将C#创建一个方法来测试方案：

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

只要 `Java.Lang.Object`，`JavaCallbacks` 就可以是用于测试此情况的任何类。

现在，请在 .NET 程序集上运行 .NET 嵌入以生成 AAR。 有关详细信息，请参阅[入门指南](~/tools/dotnet-embedding/get-started/java/android.md)。

将 AAR 文件导入 Android Studio 后，让我们编写单元测试：

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

我们：

- 使用匿名类型在 Java 中实现 `AbstractClass`
- 确保实例从 Java 返回 `"Java"`
- 确保实例返回 `"Java"`C#
- 添加了 `throws Throwable`， C#因为构造函数当前标记有 `throws`

如果我们按原样运行此单元测试，则会失败并出现如下错误：

```csharp
System.NotSupportedException: Unable to find Invoker for type 'Android.AbstractClass'. Was it linked away?
```

此处缺少的是 `Invoker` 类型。 这是将调用转发C#到 Java 的 `AbstractClass` 的子类。 如果C# Java 对象进入世界并且等效C#类型是抽象的，则 Xamarin 会自动查找具有后缀的C#类型 `Invoker` 在代码中C#使用。

对于 Java 绑定项目，Xamarin 使用这种 `Invoker` 模式。

下面是 `AbstractClassInvoker`的实现：

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

这里有相当多的一点，我们：

- 添加了子类 `Invoker` 的类 `AbstractClass`
- 添加了 `class_ref` 以保存对C#类的子类的 Java 类的 JNI 引用
- 添加了 `id_gettext` 以保存对 Java `getText` 方法的 JNI 引用
- 包含 `(IntPtr, JniHandleOwnership)` 构造函数
- 已实现 `ThresholdType` 和 `ThresholdClass`，以要求 Xamarin 了解有关 `Invoker` 的详细信息
- `GetText` 需要在 Java `getText` 方法中查找适当的 JNI 签名并将其调用
- 只需清除对 `class_ref` 的引用 `Dispose`

添加此类并生成新的 AAR 后，我们的单元测试通过。 如您所见，这种回调模式并不*理想*，但可行。

有关 Java 互操作的详细信息，请参阅本主题中的精彩[Xamarin 文档](~/android/platform/java-integration/working-with-jni.md)。

## <a name="interfaces"></a>인터페이스

接口与抽象类大致相同，不同之处在于： Xamarin 不会为其生成 Java。 这是因为在 .NET 嵌入之前，Java 将实现一个C#接口。

假设有以下C#接口：

```csharp
[Register("mono.embeddinator.android.IJavaCallback")]
public interface IJavaCallback : IJavaObject
{
    [Export("send")]
    void Send(string text);
}
```

`IJavaObject` 信号发送到 .NET，这是一个 Xamarin Android 接口，否则这与 `abstract` 类完全相同。

由于 Xamarin 目前不会为此接口生成 Java 代码，因此请将以下 Java 添加到C#项目：

```java
package mono.embeddinator.android;

public interface IJavaCallback {
    void send(String text);
}
```

你可以在任何位置放置该文件，但请确保将其生成操作设置为 `AndroidJavaSource`。 这将向 .NET 嵌入发出信号，以便将其复制到适当的目录以编译到 AAR 文件中。

接下来，`Invoker` 实现将完全相同：

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

生成 AAR 文件后，在 Android Studio 可以编写以下传递单元测试：

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

## <a name="virtual-methods"></a>虚方法

在 Java 中替代 `virtual` 是可能的，但不是很好的体验。

假设您具有以下C#类：

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

如果你按以上 `abstract` 类示例操作，它将起作用，但这种情况除外： _Xamarin 不会查找 `Invoker`_ 。

若要解决此问题， C#请修改要 `abstract`的类：

```csharp
public abstract class VirtualClass : Java.Lang.Object
```

这并不理想，但这种情况可行。 Xamarin 会选取 `VirtualClassInvoker`，Java 可以在方法上使用 `@Override`。

## <a name="callbacks-in-the-future"></a>将来的回调

我们可以通过几种方式来改进这些方案：

1. 此 [PR](https://github.com/xamarin/java.interop/pull/170) C#上`throws Throwable`已修复构造函数。
1. 在 Xamarin 中创建 Java 生成器支持接口。
    - 这无需添加具有 `AndroidJavaSource`的生成操作的 Java 源文件。
1. 为 Xamarin 加载用于虚拟类的 `Invoker`。
    - 这无需在 `virtual` 示例 `abstract`中标记该类。
1. 自动生成用于 .NET 嵌入的 `Invoker` 类
    - 这会比较复杂，但可行。 对于 Java 绑定项目，Xamarin 已经执行了类似于此的操作。

这里有很多工作要做，但这些 .NET 嵌入功能的增强功能是可行的。

## <a name="further-reading"></a>추가 정보

- [Android 上的入门](~/tools/dotnet-embedding/get-started/java/android.md)
- [Android 初步研究](~/tools/dotnet-embedding/android/index.md)
- [.NET 嵌入限制](~/tools/dotnet-embedding/limitations.md)
- [错误代码和描述](~/tools/dotnet-embedding/errors.md)
