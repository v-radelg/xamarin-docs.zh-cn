---
title: Firebase 作业调度程序
description: 本指南讨论如何使用 Google 中的 Firebase 作业调度程序库计划后台工作。
ms.prod: xamarin
ms.assetid: 3DB9C7A3-D351-481D-90C5-BEC25D1B9910
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/05/2018
ms.openlocfilehash: 280fe11f935db0a364f3342b22bb9544cdda1e6d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73020236"
---
# <a name="firebase-job-dispatcher"></a>Firebase 作业调度程序

_本指南讨论如何使用 Google 中的 Firebase 作业调度程序库计划后台工作。_

## <a name="overview"></a>概述

使 Android 应用程序对用户进行响应的最佳方式之一是确保在后台执行复杂的或长时间运行的工作。 但重要的是，后台工作不会对用户对设备的体验产生负面影响。 

例如，后台作业可能每三或四分钟轮询一次网站，以查询特定数据集的更改。 这似乎是良性的，但会对电池寿命产生灾难性影响。 应用程序将重复唤醒设备，将 CPU 提升到较高的电源状态，启动无线电收发器，使网络请求，然后处理结果。 这种情况更糟，因为设备不会立即断电并返回到低功耗空闲状态。 计划不良的后台工作可能会意外地使设备处于一种不必要和过多电源要求的状态。 此看似合法的活动（轮询网站）会使设备在相对较短的时间内不可用。

Android 提供以下 Api，可帮助在后台执行工作，但其本身并不是用于智能作业计划的。 

- **[意向服务](~/android/app-fundamentals/services/creating-a-service/intent-services.md)** &ndash; 意向服务非常适合用于执行工作，不过，它们无法计划工作。
- **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager.html)** &ndash; 这些 api 只允许计划工作，但不能实际执行工作。 此外，AlarmManager 只允许基于时间的约束，这意味着在特定时间或经过一段时间后发出警报。 
- **[JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)** &ndash; JobSchedule 是一种很好的 API，适用于操作系统来安排作业。 不过，它仅适用于面向 API 级别21或更高版本的 Android 应用程序。 
- Android 应用 &ndash; 的 **[广播接收](~/android/app-fundamentals/broadcast-receivers.md)** 方可以设置广播接收器，以便在响应系统范围内的事件或意向时执行工作。 但是，广播接收器不提供对作业运行时间的任何控制。 同时，Android 操作系统中的更改将限制广播接收方的工作时间或它们可以响应的工作类型。 

有效地执行后台工作（有时称为_后台作业_或_作业_）有两个主要功能：

1. **智能地计划工作**&ndash; 重要的是，当应用程序在后台执行工作时，这一点非常重要。 理想情况下，应用程序不应要求运行作业。 相反，应用程序应指定在作业可以运行时必须满足的条件，然后计划在满足条件时运行的工作。 这允许 Android 智能地执行工作。 例如，可能会批处理网络请求同时运行全部，以充分利用网络所涉及的开销。
2. **封装工作**&ndash; 用于执行后台工作的代码应封装在独立于用户界面的离散组件中，如果无法完成某些任务的工作，则可以相对容易地重新安排这些工作在于.

Firebase 作业调度程序是 Google 中的一个库，提供 Fluent API 来简化计划后台工作。 它旨在取代 Google Cloud Manager。 Firebase 作业调度程序包含以下 Api：

- `Firebase.JobDispatcher.JobService` 是必须使用将在后台作业中运行的逻辑进行扩展的抽象类。
- 一个 `Firebase.JobDispatcher.JobTrigger` 声明作业应启动的时间。 这通常以时间窗口的形式表示，例如，在启动作业之前等待至少30秒，但在5分钟内运行作业。
- `Firebase.JobDispatcher.RetryStrategy` 包含了在作业无法正确执行时应执行的操作的相关信息。 重试策略指定尝试再次运行作业之前等待多长时间。 
- `Firebase.JobDispatcher.Constraint` 是一个可选值，用于描述在作业可以运行之前必须满足的条件，如设备处于不受计费的网络上或正在充电。
- `Firebase.JobDispatcher.Job` 是一种 API，该 API 将以前的 Api 统一到可由 `JobDispatcher`计划的工作单元。 `Job.Builder` 类用于实例化 `Job`。
- `Firebase.JobDispatcher.JobDispatcher` 使用前面的三个 Api 来计划操作系统的工作，并提供一种在必要时取消作业的方法。

若要计划使用 Firebase 作业调度程序，Xamarin Android 应用程序必须将代码封装在一个扩展 `JobService` 类的类型中。 `JobService` 具有三种可在作业的生存期内调用的生命周期方法：

- 此方法 &ndash; **`bool OnStartJob(IJobParameters parameters)`** 在此方法中将发生，并且应始终实现。 它在主线程上运行。 如果有剩余工作，则此方法将返回 `true`，如果工作完成，则返回 `false`。 
- **`bool OnStopJob(IJobParameters parameters)`** &ndash; 当作业出于某种原因而停止时调用。 如果应稍后重新计划作业，则它应返回 `true`。
- **`JobFinished(IJobParameters parameters, bool needsReschedule)`** &ndash; 在 `JobService` 完成任何异步工作时，将调用此方法。 

若要计划作业，应用程序将实例化一个 `JobDispatcher` 对象。 然后，使用 `Job.Builder` 创建 `Job` 对象，该对象将提供给将尝试运行作业的 `JobDispatcher`。

本指南将讨论如何向 Xamarin Android 应用程序添加 Firebase 作业调度程序，并使用它来计划后台工作。

## <a name="requirements"></a>要求

Firebase 作业调度程序需要 Android API 级别9或更高版本。 Firebase 作业调度程序库依赖于 Google Play Services 提供的某些组件;设备必须安装 Google Play Services。

## <a name="using-the-firebase-job-dispatcher-library-in-xamarinandroid"></a>在 Xamarin 中使用 Firebase 作业调度程序库

若要开始 Firebase 作业调度程序，请首先将[Firebase JobDispatcher NuGet 包](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher)添加到 Xamarin 项目。 在 NuGet 包管理器中搜索**JobDispatcher**包（仍处于预发行版）。

添加 Firebase 作业调度程序库后，创建一个 `JobService` 类，然后将其计划为使用 `FirebaseJobDispatcher`的实例运行。

### <a name="creating-a-jobservice"></a>创建 JobService

Firebase 作业调度程序库执行的所有工作都必须在一个扩展 `Firebase.JobDispatcher.JobService` 抽象类的类型中完成。 创建 `JobService` 非常类似于使用 Android framework 创建 `Service`： 

1. 扩展 `JobService` 类
2. 用 `ServiceAttribute`修饰子类。 尽管不是严格需要的，但建议显式设置 `Name` 参数以帮助调试 `JobService`。 
3. 添加 `IntentFilter` 以声明**androidmanifest.xml**中的 `JobService`。 这也有助于 Firebase 作业调度程序库查找并调用 `JobService`。

下面的代码示例是应用程序的最简单的 `JobService`，使用 TPL 异步执行一些工作：

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

在计划任何工作之前，需要创建一个 `Firebase.JobDispatcher.FirebaseJobDispatcher` 对象。 `FirebaseJobDispatcher` 负责计划 `JobService`。 以下代码片段是创建 `FirebaseJobDispatcher`实例的一种方法： 

 ```csharp
// This is the "Java" way to create a FirebaseJobDispatcher object
IDriver driver = new GooglePlayDriver(context);
FirebaseJobDispatcher dispatcher = new FirebaseJobDispatcher(driver);
```

在前面的代码片段中，`GooglePlayDriver` 为类，可帮助 `FirebaseJobDispatcher` 与设备上 Google Play Services 中的某些计划 Api 进行交互。 参数 `context` 为任意 Android `Context`，如活动。 目前，`GooglePlayDriver` 是 Firebase 作业调度程序库中唯一的 `IDriver` 实现。 

Firebase 作业调度程序的 Xamarin 绑定提供了一个扩展方法，用于从 `Context`创建 `FirebaseJobDispatcher`： 

```csharp
FirebaseJobDispatcher dispatcher = context.CreateJobDispatcher();
```

实例化 `FirebaseJobDispatcher` 后，便可以创建一个 `Job` 并在 `JobService` 类中运行该代码。 `Job` 由 `Job.Builder` 对象创建，将在下一节中讨论。

### <a name="creating-a-firebasejobdispatcherjob-with-the-jobbuilder"></a>使用作业创建 Firebase. JobDispatcher

`Firebase.JobDispatcher.Job` 类负责封装运行 `JobService`所需的元数据。 @ No__t_0_ 包含一些信息，例如在作业可以运行之前必须满足的任何约束（如果 `Job` 是定期执行）或将导致运行作业的任何触发器。  至少，`Job` 必须具有_标记_（标识作业到 `FirebaseJobDispatcher`的唯一字符串）和应运行的 `JobService` 的类型。 在运行作业时，Firebase 作业调度程序将实例化 `JobService`。  使用 `Firebase.JobDispatcher.Job.JobBuilder` 类的实例创建 `Job`。 

下面的代码片段是如何使用 Xamarin for Android 绑定创建 `Job` 的最简单示例：

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .Build();
```

`Job.Builder` 将对作业的输入值执行一些基本验证检查。 如果不可能 `Job.Builder` 创建 `Job`，则将引发异常。  `Job.Builder` 将创建具有以下默认值的 `Job`：

- `Job`的_生存期_（计划运行的时间长度）仅在设备重新启动时才会重新启动 &ndash; 设备重新启动后，`Job` 丢失。
- `Job` 不会重复 &ndash; 它将只运行一次。
- 将计划尽快运行 `Job`。
- `Job` 的默认重试策略是使用_指数回退_（有关详细信息，请参见 RetryStrategy 中的 "[设置" 一](#Setting_a_RetryStrategy)节）

### <a name="scheduling-a-job"></a>计划作业

创建 `Job`后，需要在运行之前通过 `FirebaseJobDispatcher` 计划。 计划 `Job`有两种方法：

```csharp
// This will throw an exception if there was a problem scheduling the job
dispatcher.MustSchedule(myJob);

// This method will not throw an exception; an integer result value is returned
int scheduleResult = dispatcher.Schedule(myJob);
```

`FirebaseJobDispatcher.Schedule` 返回的值将为以下整数值之一：

- 已成功计划 `FirebaseJobDispatcher.ScheduleResultSuccess` &ndash; `Job`。
- `FirebaseJobDispatcher.ScheduleResultUnknownError` &ndash; 发生了阻止 `Job` 计划的某些未知问题。
- `FirebaseJobDispatcher.ScheduleResultNoDriverAvailable` &ndash; 使用了无效的 `IDriver` 或 `IDriver` 不可用。 
- 不支持 `FirebaseJobDispatcher.ScheduleResultUnsupportedTrigger` &ndash; `Trigger`。
- `FirebaseJobDispatcher.ScheduleResultBadService` &ndash; 服务配置不正确或不可用。

### <a name="configuring-a-job"></a>配置作业

可以自定义作业。 如何自定义作业的示例包括：

- 将[参数传递给作业](#Passing_Parameters_to_a_Job)&ndash; `Job` 可能需要额外的值来执行其工作，例如，下载文件。
- [设置约束](#Setting_Constraints)&ndash; 在满足某些条件时，可能只需要运行作业。 例如，仅在设备充电时运行 `Job`。 
- [指定 `Job` 应何时运行](#Setting_Job_Triggers)&ndash; Firebase 作业调度程序允许应用程序指定作业应运行的时间。  
- [声明失败作业的重试策略](#Setting_a_RetryStrategy)&ndash;_重试策略_向 `FirebaseJobDispatcher` 提供有关无法完成的操作 `Jobs` 的指导。 

以下各节将详细讨论其中的每个主题。

<a name="Passing_Parameters_to_a_Job" />

#### <a name="passing-parameters-to-a-job"></a>将参数传递到作业

通过创建与 `Job.Builder.SetExtras` 方法一起传递的 `Bundle`，将参数传递给作业：

```csharp
Bundle jobParameters = new Bundle();
jobParameters.PutInt(FibonacciCalculatorJob.FibonacciPositionKey, 25);

Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetExtras(jobParameters)
                      .Build();

```

从 `OnStartJob` 方法上的 `IJobParameters.Extras` 属性访问 `Bundle`：

```csharp
public override bool OnStartJob(IJobParameters jobParameters)
{
    int position = jobParameters.Extras.GetInt(FibonacciPositionKey, DEFAULT_VALUE);
    
    // rest of code omitted
} 
```

<a name="Setting_Constraints" />

#### <a name="setting-constraints"></a>设置约束

约束有助于降低设备的成本或电池消耗。 `Firebase.JobDispatcher.Constraint` 类将这些约束定义为整数值：

- `Constraint.OnUnmeteredNetwork` &ndash; 仅在设备连接到无法进行的网络时运行作业。 这有助于防止用户产生数据收费。
- `Constraint.OnAnyNetwork` &ndash; 在设备连接到的任何网络上运行作业。 如果与 `Constraint.OnUnmeteredNetwork`一起指定，则此值优先。
- `Constraint.DeviceCharging` &ndash; 仅在设备充电时运行作业。

约束是用 `Job.Builder.SetConstraint` 方法设置的： 

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetConstraint(Constraint.DeviceCharging)
                      .Build();
```

<a name="Setting_Job_Triggers" />

`JobTrigger` 向操作系统提供有关何时应启动作业的指导。 `JobTrigger` 有一个_执行窗口_，该窗口定义 `Job` 应在何时运行的计划时间。 执行窗口有一个_开始窗口_值和一个_结束窗口_值。 "开始" 窗口是设备在运行作业之前应等待的秒数，而 "结束时间" 值是运行 `Job`之前要等待的最大秒数。 

可以使用 `Firebase.Jobdispatcher.Trigger.ExecutionWindow` 方法创建 `JobTrigger`。  例如 `Trigger.ExecutionWindow(15,60)` 表示作业应在计划时间15到60秒之间运行。 `Job.Builder.SetTrigger` 方法用于 

```csharp
JobTrigger myTrigger = Trigger.ExecutionWindow(15,60);
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetTrigger(myTrigger)
                      .Build();
```

作业的默认 `JobTrigger` 表示为 `Trigger.Now`的值，该值指定在计划后尽快运行作业。

<a name="Setting_a_RetryStrategy" />

#### <a name="setting-a-retrystrategy"></a>设置 RetryStrategy

`Firebase.JobDispatcher.RetryStrategy` 用于指定在尝试重新运行失败的作业之前设备应使用的延迟量。 `RetryStrategy` 具有一个策略，该_策略_定义将使用哪种时间基准算法来重新安排失败的作业，并指定一个执行窗口，该窗口指定应在其中安排作业的窗口。 此重新_计划窗口_由两个值定义。 第一个值是在重新安排作业之前等待的秒数（_初始回退_值），第二个数字是作业必须运行之前的最大秒数（_最大回退_值）。 

这两种类型的重试策略由以下 int 值标识：

- `RetryStrategy.RetryPolicyExponential` &ndash;_指数回退_策略会在每次发生故障后以指数方式增加初始回退值。 第一次作业失败时，库将等待初始时间间隔，在重新计划作业 &ndash; 示例30秒之前指定。 第二次作业失败时，库将等待至少60秒，然后再尝试运行作业。 第三次尝试失败后，库将等待120秒，依此类推。 Firebase 作业调度程序库的默认 `RetryStrategy` 由 `RetryStrategy.DefaultExponential` 对象表示。 它的初始回退为30秒，最大回退为3600秒。
- `RetryStrategy.RetryPolicyLinear` &ndash; 此策略是一种_线性回退_，应将作业重新安排为按设置的时间间隔运行（直到成功）。 线性回退最适用于必须尽快完成的工作，或者用于快速解决自身问题的问题。 Firebase 作业调度程序库定义了一个 `RetryStrategy.DefaultLinear`，其中至少有30秒，最长为3600秒。

使用 `FirebaseJobDispatcher.NewRetryStrategy` 方法可以定义自定义 `RetryStrategy`。 它采用三个参数：

1. `int policy` &ndash;_策略_是以前的 `RetryStrategy` 值、`RetryStrategy.RetryPolicyLinear`或 `RetryStrategy.RetryPolicyExponential`之一。
2. `int initialBackoffSeconds` &ndash;_初始回退_是在尝试再次运行作业之前需要延迟的时间（以秒为单位）。 此值的默认值为30秒。 
3. `int maximumBackoffSeconds` &ndash;_最大回退_值声明尝试再次运行作业之前延迟的最大秒数。 默认值为3600秒。 

```csharp
RetryStrategy retry = dispatcher.NewRetryStrategy(RetryStrategy.RetryPolicyLinear, initialBackoffSeconds, maximumBackoffSet);

// Create a Job and set the RetryStrategy via the Job.Builder
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetRetryStrategy(retry)
                      .Build();
```

### <a name="cancelling-a-job"></a>取消作业

可以取消已计划的所有作业，或者只是使用 `FirebaseJobDispatcher.CancelAll()` 方法或 `FirebaseJobDispatcher.Cancel(string)` 方法的单个作业：

```csharp
int cancelResult = dispatcher.CancelAll(); 

// to cancel a single job:

int cancelResult = dispatcher.Cancel("unique-tag-for-job");
```

任一方法都将返回一个整数值：

- 已成功取消作业 &ndash; `FirebaseJobDispatcher.CancelResultSuccess`。
- `FirebaseJobDispatcher.CancelResultUnknownError` &ndash; 错误阻止作业被取消。
- `FirebaseJobDispatcher.CancelResult.NoDriverAvailable` &ndash; `FirebaseJobDispatcher` 无法取消作业，因为没有可用的有效 `IDriver`。

## <a name="summary"></a>总结

本指南讨论了如何使用 Firebase 作业调度程序在后台智能地执行工作。 本指南讨论了如何封装要作为 `JobService` 执行的工作，以及如何使用 `FirebaseJobDispatcher` 来计划工作、使用 `JobTrigger` 指定条件以及如何使用 `RetryStrategy`处理失败。

## <a name="related-links"></a>相关链接

- [NuGet 上的 Firebase JobDispatcher](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher)
- [firebase-GitHub 上的调度程序](https://github.com/firebase/firebase-jobdispatcher-android)
- [Firebase. JobDispatcher 绑定](https://github.com/xamarin/XamarinComponents/tree/master/Android/FirebaseJobDispatcher)
- [智能作业-计划](https://developer.android.com/topic/performance/scheduling.html)
- [Android 电池和内存优化-Google i/o 2016 （视频）](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
