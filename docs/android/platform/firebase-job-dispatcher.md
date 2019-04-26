---
title: Firebase 作业调度程序
description: 本指南讨论如何使用Google的Firebase作业调度程序库安排后台工作。
ms.prod: xamarin
ms.assetid: 3DB9C7A3-D351-481D-90C5-BEC25D1B9910
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/05/2018
ms.openlocfilehash: 91bafbbdaee805ad128766bf0a770cb711597a85
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61023345"
---
# <a name="firebase-job-dispatcher"></a>Firebase 作业调度程序

_本指南讨论如何使用Google的Firebase作业调度程序库安排后台工作。_


## <a name="overview"></a>概述

若要使 Android 应用程序保持响应状态向用户的最佳方法之一是确保复杂或长时间运行的工作在后台执行。 但是，务必与设备后台工作将带来负面影响的用户体验。 

例如，后台作业可能分钟轮询一次网站每三个或四个查询的特定的数据集的更改。 这看起来良性的的但它会上的电池使用寿命带来灾难性影响。 应用程序将重复唤醒设备，将 CPU 提升到更高版本的电源状态、 接通电源的无线电收发器，使网络请求，然后处理结果。 它获取更糟的是，因为该设备将不会立即关闭电源，并返回到低能耗空闲状态。 效果不佳计划的后台工作可能会无意中保护设备的不必要和过多电源要求的状态中。 此一项看似无害的活动 （轮询网站） 将导致设备相对较短的时间内不可用。

Android 提供了以下 Api，以帮助在后台执行工作，但本身不足够的智能作业计划。 

* **[意向服务](~/android/app-fundamentals/services/creating-a-service/intent-services.md)** &ndash;意向服务非常适合用于执行工作，但它们不提供任何方法来计划工作。
* **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager.html)**  &ndash;这些 Api 仅允许以进行计划，但不提供任何方法来实际执行的工作的工作。 此外，AlarmManager 仅允许基于时间的限制，这意味着在某个时间或经过一段时间后会引发警报。 
* **[JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)**  &ndash; JobSchedule 是一个很好的 API，适用于计划作业的操作系统。 但是，它是仅适用于 Android 应用程序的目标 API 级别 21 或更高版本。 
* **[广播接收器](~/android/app-fundamentals/broadcast-receivers.md)** &ndash; Android 应用程序可以设置广播的接收器来执行工作以响应系统级事件或意向。 但是，广播的接收器不提供任何控制应何时运行该作业。 此外限制 Android 操作系统中的更改将广播的接收器将起作用，或类型的响应的工作。 

有两个有效地执行后台工作的关键功能 (有时称为_后台作业_或_作业_):

1. **以智能方式调度工作**&ndash;非常重要的应用程序在后台的工作执行该操作时它会作为一名良好公民。 理想情况下，应用程序不应要求，在运行作业。 相反，应用程序应指定当作业可以运行，然后安排时满足条件时运行该工作必须满足的条件。 这使 Android 可智能地执行工作。 例如，网络请求可能会进行批处理运行全部在同一时间，以便与网络相关的开销最大值使用。
2. **封装工作**&ndash;来执行后台工作的代码应封装在可运行独立于用户界面并将可以很轻松地重新计划如果无法完成工作的离散组件由于某种原因。

Firebase 作业调度程序是从 Google 提供了一个 fluent API 来简化计划的后台工作的库。 它旨在替换为 Google 云管理器。 Firebase 作业调度程序包括以下 Api:

* 一个`Firebase.JobDispatcher.JobService`是一个抽象类，必须使用将在后台作业中运行的逻辑扩展。
* 一个`Firebase.JobDispatcher.JobTrigger`声明时应启动的作业。 这通常都表示为窗口的时间，例如，等待至少 30 秒，然后启动作业，但在 5 分钟内运行作业。
* 一个`Firebase.JobDispatcher.RetryStrategy`包含有关作业无法正确执行时应完成的操作的信息。 重试策略指定尝试重新运行该作业之前等待多长时间。 
* 一个`Firebase.JobDispatcher.Constraint`是一个可选值，描述一个条件，必须满足才能运行该作业，例如设备已打开 unmetered 网络或计费。
* `Firebase.JobDispatcher.Job`是一个 API，统一了中的工作单元，可以通过计划到以前的 Api `JobDispatcher`。 `Job.Builder`类用于实例化`Job`。
* 一个`Firebase.JobDispatcher.JobDispatcher`使用以前的三个 Api 来计划随操作系统一起工作，并提供一种方法可以取消作业，如有必要。

若要计划使用 Firebase 作业调度程序的工作，Xamarin.Android 应用程序必须封装扩展的类型中的代码`JobService`类。 `JobService` 有三个作业的生存期内可以调用的生命周期方法：

* **`bool OnStartJob(IJobParameters parameters)`** &ndash; 此方法是在其中工作将发生，并且应始终实现。 它在主线程上运行。 此方法将返回`true`剩余，如果没有工作或`false`如果完成工作。 
* **`bool OnStopJob(IJobParameters parameters)`** &ndash; 这称为时出于某种原因停止作业。 它应返回`true`如果应供稍后重新安排作业。
* **`JobFinished(IJobParameters parameters, bool needsReschedule)`** &ndash; 将调用此方法`JobService`完成任何异步工作。 

若要安排作业计划，应用程序将实例化`JobDispatcher`对象。 然后，`Job.Builder`用于创建`Job`对象，它提供给`JobDispatcher`以尝试，并计划要运行的作业。

本指南介绍了如何将 Firebase 作业调度程序添加到 Xamarin.Android 应用程序并使用它来计划后台工作。

## <a name="requirements"></a>要求

Firebase 作业调度程序需要 Android API 级别 9 或更高版本。 Firebase 作业调度程序库依赖于某些组件提供的 Google Play Services;设备必须已安装的 Google Play Services。

## <a name="using-the-firebase-job-dispatcher-library-in-xamarinandroid"></a>使用在 Xamarin.Android 中的 Firebase 作业调度程序库

若要开始使用 Firebase 作业调度程序，首先添加[Xamarin.Firebase.JobDispatcher NuGet 包](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher)到 Xamarin.Android 项目。 搜索 NuGet 包管理器**Xamarin.Firebase.JobDispatcher**包 （其中仍处于预发布）。

添加后 Firebase 作业调度程序库，创建`JobService`类，然后安排其运行的实例`FirebaseJobDispatcher`。


### <a name="creating-a-jobservice"></a>创建 JobService

Firebase 作业调度程序库执行的所有工作必须都进行扩展的类型中`Firebase.JobDispatcher.JobService`抽象类。 创建`JobService`非常类似于创建`Service`Android framework: 

1. 扩展`JobService`类
2. 修饰子类的`ServiceAttribute`。 虽然不是严格要求，但建议显式设置`Name`参数，以帮助进行调试`JobService`。 
3. 添加`IntentFilter`声明`JobService`中**AndroidManifest.xml**。 这将帮助您查找和调用的 Firebase 作业调度程序库`JobService`。

下面的代码是最简单的示例`JobService`应用程序，使用 TPL 执行一些操作，以异步方式：

```csharp
[Service(Name = "com.xamarin.fjdtestapp.DemoJob")]
[IntentFilter(new[] {FirebaseJobServiceIntent.Action})]
public class DemoJob : JobService
{
    static readonly string TAG = "X:DemoService";

    public override bool OnStartJob(IJobParameters jobParameters)
    {
        Task.Run(() =>
        {
            // Work is happening asynchronously (code omitted)
                       
        });

        // Return true because of the asynchronous work
        return true;  
    }

    public override bool OnStopJob(IJobParameters jobParameters)
    {
        Log.Debug(TAG, "DemoJob::OnStartJob");
        // nothing to do.
        return false;
    }
}
```

### <a name="creating-a-firebasejobdispatcher"></a>创建 FirebaseJobDispatcher

可以安排任何工作之前，有必要创建`Firebase.JobDispatcher.FirebaseJobDispatcher`对象。 `FirebaseJobDispatcher`负责计划`JobService`。 下面的代码段是一种方法创建的实例`FirebaseJobDispatcher`: 
 
 ```csharp
// This is the "Java" way to create a FirebaseJobDispatcher object
IDriver driver = new GooglePlayDriver(context);
FirebaseJobDispatcher dispatcher = new FirebaseJobDispatcher(driver);
```

在上一代码片段中，`GooglePlayDriver`是类，可帮助`FirebaseJobDispatcher`与 Google Play Services 中的计划 Api 的一些设备上进行交互。 将参数`context`是任何 Android `Context`，例如活动。 目前`GooglePlayDriver`是唯一`IDriver`Firebase 作业调度程序库中的实现。 

Firebase 作业调度程序的 Xamarin.Android 绑定提供了一个扩展方法来创建`FirebaseJobDispatcher`从`Context`: 

```csharp
FirebaseJobDispatcher dispatcher = context.CreateJobDispatcher();
```

一次`FirebaseJobDispatcher`已被实例化，则可以创建`Job`运行中的代码和`JobService`类。 `Job`创建的`Job.Builder`对象，并将在下一节中讨论。

### <a name="creating-a-firebasejobdispatcherjob-with-the-jobbuilder"></a>使用 Job.Builder 创建 Firebase.JobDispatcher.Job

`Firebase.JobDispatcher.Job`类负责封装元数据运行所需`JobService`。 一个`Job`包含信息如之前必须满足作业可以运行，如果任何约束`Job`重复进行的或将使作业在运行任何触发器。  作为最低限度`Job`必须具有_标记_(标识到作业的唯一字符串`FirebaseJobDispatcher`) 和类型的`JobService`应运行的。 Firebase 作业调度程序将实例化`JobService`时的时间来运行作业。  一个`Job`使用的实例创建`Firebase.JobDispatcher.Job.JobBuilder`类。 

下面的代码段是如何创建的最简单的示例`Job`使用 Xamarin.Android 绑定：

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .Build();
```

`Job.Builder`作业将执行一些基本验证检查，对输入值。 如果它不可能，将引发异常`Job.Builder`若要创建`Job`。  `Job.Builder`将创建`Job`使用以下默认值：

* 一个`Job`的_生存期_（多长时间它将安排运行） 是仅在设备重启之前&ndash;设备重启后`Job`都将丢失。
* 一个`Job`不重复进行&ndash;它将只运行一次。
* 一个`Job`将按计划运行越早越好。
* 默认重试策略`Job`是使用_指数退避算法_(在部分下面更详细地讨论[设置 RetryStrategy](#Setting_a_RetryStrategy))

### <a name="scheduling-a-job"></a>计划的作业

在创建后`Job`，它需要与计划`FirebaseJobDispatcher`在运行之前。 有两种方法来计划`Job`:

```csharp
// This will throw an exception if there was a problem scheduling the job
dispatcher.MustSchedule(myJob);

// This method will not throw an exception; an integer result value is returned
int scheduleResult = dispatcher.Schedule(myJob);
```

返回的值`FirebaseJobDispatcher.Schedule`将是以下整数值之一：

* `FirebaseJobDispatcher.ScheduleResultSuccess` &ndash; `Job`已成功安排。
* `FirebaseJobDispatcher.ScheduleResultUnknownError` &ndash; 出现某种未知的错误导致`Job`进行调度。
* `FirebaseJobDispatcher.ScheduleResultNoDriverAvailable` &ndash; 无效`IDriver`已使用或`IDriver`已由于某种原因不可用。 
* `FirebaseJobDispatcher.ScheduleResultUnsupportedTrigger` &ndash; `Trigger`不受支持。
* `FirebaseJobDispatcher.ScheduleResultBadService` &ndash; 该服务配置不正确或不可用。
 
### <a name="configuring-a-job"></a>配置作业

它是可以自定义作业。 作业可以自定义的示例包括：

* [将参数传递到作业](#Passing_Parameters_to_a_Job) &ndash; A`Job`可能需要其他值以执行其工作，例如下载文件。
* [设置约束](#Setting_Constraints)&ndash;可能有必要时满足某些条件时才运行作业。 例如，仅运行`Job`设备正在充电时。 
* [指定何时`Job`应运行](#Setting_Job_Triggers) &ndash; Firebase 作业调度程序允许应用程序指定运行作业的时间。  
* [声明失败的作业的重试策略](#Setting_a_RetryStrategy) &ndash; A_重试策略_提供的指导来`FirebaseJobDispatcher`上要执行的操作与`Jobs`，无法完成。 

每个主题将讨论更以下各节中。

<a name="Passing_Parameters_to_a_Job" />

#### <a name="passing-parameters-to-a-job"></a>将参数传递到作业

通过创建参数传递到作业`Bundle`连同传递`Job.Builder.SetExtras`方法：

```csharp
Bundle jobParameters = new Bundle();
jobParameters.PutInt(FibonacciCalculatorJob.FibonacciPositionKey, 25);

Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetExtras(jobParameters)
                      .Build();

```

`Bundle`从访问`IJobParameters.Extras`属性上的`OnStartJob`方法：

```csharp
public override bool OnStartJob(IJobParameters jobParameters)
{
    int position = jobParameters.Extras.GetInt(FibonacciPositionKey, DEFAULT_VALUE);
    
    // rest of code omitted
} 
```

<a name="Setting_Constraints" />

#### <a name="setting-constraints"></a>设置约束

约束可以帮助减少在设备上的成本或电池消耗。 `Firebase.JobDispatcher.Constraint`类将这些约束定义为整数值：

* `Constraint.OnUnmeteredNetwork` &ndash; 在设备连接到 unmetered 网络时，仅运行该作业。 这可用于防止用户不会产生数据费用。
* `Constraint.OnAnyNetwork` &ndash; 设备连接到任何网络上运行作业。 如果指定与`Constraint.OnUnmeteredNetwork`，此值将优先。
* `Constraint.DeviceCharging` &ndash; 仅当设备正在充电时，请运行该作业。

使用设置约束`Job.Builder.SetConstraint`方法： 

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetConstraint(Constraint.DeviceCharging)
                      .Build();
```

<a name="Setting_Job_Triggers" />

`JobTrigger`指导有关作业应该启动操作系统。 一个`JobTrigger`已_执行窗口_定义计划的时间，何时`Job`应运行。 执行窗口已_启动窗口_值和一个_结束时段_值。 开始时段是设备应在运行作业之前等待的秒数和结束窗口值是在运行前等待的秒的最大数`Job`。 

一个`JobTrigger`可用于创建`Firebase.Jobdispatcher.Trigger.ExecutionWindow`方法。  例如`Trigger.ExecutionWindow(15,60)`意味着，作业应运行在计划运行时从 15 到 60 秒之间。 `Job.Builder.SetTrigger`方法是使用 

```csharp
JobTrigger myTrigger = Trigger.ExecutionWindow(15,60);
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetTrigger(myTrigger)
                      .Build();
```

默认值`JobTrigger`的值表示作业`Trigger.Now`，它指定安排后尽快运行作业...

<a name="Setting_a_RetryStrategy" />

#### <a name="setting-a-retrystrategy"></a>设置 RetryStrategy

`Firebase.JobDispatcher.RetryStrategy`用来指定多少设备延迟应使用之前尝试重新运行失败的作业。 一个`RetryStrategy`已_策略_，用于定义哪些时间基准算法将用于重新计划失败的作业，并指定应将作业安排在其中一个时段执行窗口。 这_重新计划窗口_由两个值定义。 第一个值是在重新安排作业之前等待的秒数 (_初始退避算法_值)，并且第二个数字的最大之前运行该作业必须等待的秒数 (_最大回退_值)。 
 
由这些整数值标识两种类型的重试策略：

* `RetryStrategy.RetryPolicyExponential` &ndash; _指数退避算法_策略将初始回退值指数级增长后增加每个失败。 第一次的作业失败，库将等待重新安排作业之前指定的 （_i） 间隔&ndash;示例 30 秒。 第二次作业失败时，库将等待至少 60 秒，然后再尝试运行该作业。 第三个尝试失败后，库将等待 120 秒，依此类推。 默认值`RetryStrategy`Firebase 作业调度程序库由`RetryStrategy.DefaultExponential`对象。 它有 30 秒初始退避算法和 3600 秒的最大回退。
* `RetryStrategy.RetryPolicyLinear` &ndash; 此策略是_线性退让_（直到成功），应重新安排作业，若要在运行设置的时间间隔。 线性退避算法最适合必须尽可能快地完成的工作或快速将自身解决的问题。 Firebase 作业调度程序库定义`RetryStrategy.DefaultLinear`具有至少 30 秒重新计划窗口和高达 3600 秒。

可以定义一个自定义`RetryStrategy`与`FirebaseJobDispatcher.NewRetryStrategy`方法。 它采用三个参数：

1. `int policy` &ndash; _策略_是上一个之一`RetryStrategy`值， `RetryStrategy.RetryPolicyLinear`，或`RetryStrategy.RetryPolicyExponential`。
2. `int initialBackoffSeconds` &ndash; _初始退避算法_延迟，以秒为单位，尝试重新运行该作业之前需。 此默认值为 30 秒。 
3. `int maximumBackoffSeconds` &ndash; _最大回退_值声明的最大尝试重新运行该作业之前的延迟秒数。 默认值为 3600 秒。 

```csharp
RetryStrategy retry = dispatcher.NewRetryStrategy(RetryStrategy.RetryPolicyLinear, initialBackoffSeconds, maximumBackoffSet);

// Create a Job and set the RetryStrategy via the Job.Builder
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetRetryStrategy(retry)
                      .Build();
```

### <a name="cancelling-a-job"></a>正在取消作业

它是可以取消所有作业的计划，或只是单个作业使用`FirebaseJobDispatcher.CancelAll()`方法或`FirebaseJobDispatcher.Cancel(string)`方法：

```csharp
int cancelResult = dispatcher.CancelAll(); 

// to cancel a single job:

int cancelResult = dispatcher.Cancel("unique-tag-for-job");
```

这两种方法将返回一个整数值：

* `FirebaseJobDispatcher.CancelResultSuccess` &ndash; 已成功取消该作业。
* `FirebaseJobDispatcher.CancelResultUnknownError` &ndash; 错误阻止，正在取消作业。
* `FirebaseJobDispatcher.CancelResult.NoDriverAvailable` &ndash; `FirebaseJobDispatcher`无法取消该作业，因为没有有效`IDriver`可用。

## <a name="summary"></a>总结

本指南介绍如何使用 Firebase 作业调度程序以智能方式在后台执行工作。 它介绍了如何封装为执行的工作`JobService`以及如何使用`FirebaseJobDispatcher`安排该工作，指定与条件`JobTrigger`方式与处理故障和`RetryStrategy`。


## <a name="related-links"></a>相关链接

- [NuGet 上 Xamarin.Firebase.JobDispatcher](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher)
- [firebase 作业-dispatcher GitHub 上](https://github.com/firebase/firebase-jobdispatcher-android)
- [Xamarin.Firebase.JobDispatcher 绑定](https://github.com/xamarin/XamarinComponents/tree/master/Android/FirebaseJobDispatcher)
- [智能的作业计划](https://developer.android.com/topic/performance/scheduling.html)
- [Android 的电池和内存优化-Google I/O 2016 （视频）](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
