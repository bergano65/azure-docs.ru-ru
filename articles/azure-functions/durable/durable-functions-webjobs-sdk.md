---
title: How to run Durable Functions as WebJobs - Azure
description: Узнайте, как программировать и настраивать запуск устойчивых функций в веб-заданиях с помощью пакета SDK для веб-заданий.
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: d8dd0c86fbc520d0bd3ef6034891bd9871774b4a
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232743"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>How to run Durable Functions as WebJobs

By default, Durable Functions uses the Azure Functions runtime to host orchestrations. However, there may be certain scenarios where you need more control over the code that listens for events. This article shows you how to implement your orchestration using the WebJobs SDK. To see a more detailed comparison between Functions and WebJobs, see [Compare Functions and WebJobs](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

Расширения [Функций Azure](../functions-overview.md) и [устойчивых функций](durable-functions-overview.md) созданы на основе [пакета SDK для веб-заданий](../../app-service/webjobs-sdk-how-to.md). The job host in the WebJobs SDK is the runtime in Azure Functions. If you need to control behavior in ways not possible in Azure Functions, you can develop and run Durable Functions by using the WebJobs SDK yourself.

In version 3.x of the WebJobs SDK, the host is an implementation of `IHost`, and in version 2.x you use the `JobHost` object.

The chaining Durable Functions sample is available in a WebJobs SDK 2.x version: download or clone the [Durable Functions repository](https://github.com/azure/azure-functions-durable-extension/), and go to the *samples\\webjobssdk\\chaining* folder.

## <a name="prerequisites"></a>Технические условия

В этой статье предполагается, что вы знакомы с основами пакета SDK для веб-заданий, разработки библиотеки классов C# для Функций Azure и устойчивых функций. Ознакомиться с этими темами вам помогут следующие ресурсы:

* [Начало работы с пакетом SDK WebJobs](../../app-service/webjobs-sdk-get-started.md)
* [Создание первой функции с помощью Visual Studio](../functions-create-your-first-function-visual-studio.md)
* [Цепочки функций в устойчивых функциях — пример последовательности Hello](durable-functions-sequence.md)

Чтобы выполнить действия, описанные в этой статье, сделайте следующее:

* [Install Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/) with the **Azure development** workload.

  If you already have Visual Studio, but don't have that workload, add the workload by selecting **Tools** > **Get Tools and Features**.

  (Вместо этого можно использовать [Visual Studio Code](https://code.visualstudio.com/), но некоторые инструкции относятся к Visual Studio.)

* Установите и запустите [эмулятор хранилища Azure](../../storage/common/storage-use-emulator.md) версии 5.2 или выше. Альтернативой является добавление в файл *App.config* строки подключения к службе хранилища Azure.

## <a name="webjobs-sdk-versions"></a>Версии пакета SDK для веб-заданий

В этой статье объясняется, как разрабатывать проект пакета SDK для веб-заданий версии 2.x (эквивалентен Функциям Azure версии 1.x). Дополнительные сведения о версии 3.x см. в разделе [Пакет SDK для веб-заданий версии 3.x](#webjobs-sdk-3x) далее в этой статье.

## <a name="create-a-console-app"></a>Создание консольного приложения

To run Durable Functions as WebJobs, you must first create a console app. Проект пакета SDK для веб-заданий — это просто проект консольного приложения с установленными соответствующими пакетами NuGet.

In the Visual Studio **New Project** dialog box, select **Windows Classic Desktop** > **Console App (.NET Framework)** . В файле проекта `TargetFrameworkVersion` должна иметь значение `v4.6.1`.

Visual Studio also has a WebJob project template, which you can use by selecting **Cloud** > **Azure WebJob (.NET Framework)** . Этот шаблон устанавливает множество пакетов, некоторые из которых могут не потребоваться.

## <a name="install-nuget-packages"></a>Установка пакетов Nuget

Вам потребуются пакеты NuGet для пакета SDK для веб-заданий, основные привязки, платформа ведения журналов и расширение устойчивых задач. Here are **Package Manager Console** commands for those packages, with the latest stable version numbers as of the date this article was written:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.8.3
```

Вам также понадобятся регистраторы. The following commands install the Azure Application Insights provider and the `ConfigurationManager`. `ConfigurationManager` позволяет получить ключ инструментирования Application Insights из параметров приложения.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

Следующая команда устанавливает поставщик консоли:

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>Код JobHost

Having created the console app and installed the NuGet packages you need, you're ready to use Durable Functions. You do so by using JobHost code.

Чтобы использовать расширение устойчивых функций, вызовите `UseDurableTask` для объекта `JobHostConfiguration` в вашем методе `Main`:

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

Список свойств, которые можно задать в объекте `DurableTaskExtension`, см. в файле [host.json](../functions-host-json.md#durabletask).

Метод `Main` служит также местом для настройки регистраторов. The following example configures the console and Application Insights providers.

```cs
static void Main(string[] args)
{
    using (var loggerFactory = new LoggerFactory())
    {
        var config = new JobHostConfiguration();

        config.DashboardConnectionString = "";

        var instrumentationKey =
            ConfigurationManager.AppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"];

        config.LoggerFactory = loggerFactory
            .AddApplicationInsights(instrumentationKey, null)
            .AddConsole();

        config.UseTimers();
        config.UseDurableTask(new DurableTaskExtension
        {
            HubName = "MyTaskHub",
        });
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="functions"></a>Функции Azure

Durable Functions in the context of WebJobs differs somewhat from Durable Functions in the context of Azure Functions. It's important to be aware of the differences as you write your code.

Пакет SDK для веб-заданий не поддерживает следующие возможности Функций Azure:

* [Атрибут FunctionName](#functionname-attribute)
* [триггером HTTP](#http-trigger)
* [API управления HTTP устойчивых функций](#http-management-api)

### <a name="functionname-attribute"></a>Атрибут FunctionName

В проекте пакета SDK для веб-заданий имя метода функции является именем функции. Атрибут `FunctionName` используется только в Функциях Azure.

### <a name="http-trigger"></a>Триггер HTTP

В пакете SDK для веб-заданий нет триггера HTTP. Клиент оркестрации в примере проекта использует триггер таймера:

```cs
public static async Task CronJob(
    [TimerTrigger("0 */2 * * * *")] TimerInfo timer,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger logger)
{
  ...
}
```

### <a name="http-management-api"></a>API управления HTTP

Так как пакет SDK для веб-заданий не содержит ни одного триггера HTTP, в нем нет [API управления HTTP](durable-functions-http-api.md).

In a WebJobs SDK project, you can call methods on the orchestration client object, instead of by sending HTTP requests. Следующие методы соответствуют трем задачам, которые можно выполнить с помощью API управления HTTP.

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

The orchestration client function in the sample project starts the orchestrator function, and then goes into a loop that calls `GetStatusAsync` every 2 seconds:

```cs
string instanceId = await client.StartNewAsync(nameof(HelloSequence), input: null);
logger.LogInformation($"Started new instance with ID = {instanceId}.");

DurableOrchestrationStatus status;
while (true)
{
    status = await client.GetStatusAsync(instanceId);
    logger.LogInformation($"Status: {status.RuntimeStatus}, Last update: {status.LastUpdatedTime}.");

    if (status.RuntimeStatus == OrchestrationRuntimeStatus.Completed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Failed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Terminated)
    {
        break;
    }

    await Task.Delay(TimeSpan.FromSeconds(2));
}
```

## <a name="run-the-sample"></a>Запуск примера

You've got Durable Functions set up to run as a WebJob, and you now have an understanding of how this will differ from running Durable Functions as standalone Azure Functions. At this point, seeing it work in a sample might be helpful.

Этот раздел содержит общие сведения о способах запуска [примера проекта](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/webjobssdk/chaining). Подробные инструкции о том, как запустить проект пакета SDK для веб-заданий локально и развернуть его в веб-задание Azure, см. в разделе [Развертывание в качестве WebJob (веб-задания)](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob).

### <a name="run-locally"></a>Локальный запуск

1. Запустите эмулятор хранилища (см. раздел [Предварительные требования](#prerequisites)).

1. If you want to see logs in Application Insights when you run the project locally:

    а) Create an Application Insights resource, and use the **General** app type for it.

    б) Сохраните ключ инструментирования в файл *App.config*.

1. Запустите проект.

### <a name="run-in-azure"></a>Запуск в Azure

1. Создайте веб-приложение и учетную запись хранения.

1. In the web app, save the storage connection string in an app setting named `AzureWebJobsStorage`.

1. Create an Application Insights resource, and use the **General** app type for it.

1. Save the instrumentation key in an app setting named `APPINSIGHTS_INSTRUMENTATIONKEY`.

1. Разверните приложение в качестве веб-задания.

## <a name="webjobs-sdk-3x"></a>Пакет SDK для веб-заданий версии 3.x

This article explains how to develop a WebJobs SDK 2.x project. If you're developing a [WebJobs SDK 3.x](../../app-service/webjobs-sdk-get-started.md) project, this section helps you understand the differences.

The main change introduced is the use of .NET Core instead of .NET Framework. To create a WebJobs SDK 3.x project, the instructions are the same, with these exceptions:

1. Создайте консольное приложение .NET Core. In the Visual Studio **New Project** dialog box, select  **.NET Core** > **Console App (.NET Core)** . Файл проекта указывает, что `TargetFramework` является `netcoreapp2.x`.

1. Choose the release version WebJobs SDK 3.x of the following packages:

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Extensions.Storage`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. Set the storage connection string and the Application Insights instrumentation key in an *appsettings.json* file, by using the .NET Core configuration framework. Ниже приведен пример:

    ```json
        {
            "AzureWebJobsStorage": "<replace with storage connection string>",
            "APPINSIGHTS_INSTRUMENTATIONKEY": "<replace with Application Insights instrumentation key>"
        }
    ```

1. Change the `Main` method code to do this. Ниже приведен пример:

   ```cs
   static void Main(string[] args)
   {
        var hostBuilder = new HostBuilder()
            .ConfigureWebJobs(config =>
            {
                config.AddAzureStorageCoreServices();
                config.AddAzureStorage();
                config.AddTimers();
                config.AddDurableTask(options =>
                {
                    options.HubName = "MyTaskHub";
                    options.AzureStorageConnectionStringName = "AzureWebJobsStorage";
                });
            })
            .ConfigureLogging((context, logging) =>
            {
                logging.AddConsole();
                logging.AddApplicationInsights(config =>
                {
                    config.InstrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                });
            })
            .UseConsoleLifetime();

        var host = hostBuilder.Build();

        using (host)
        {
            host.Run();
        }
   }
   ```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о пакете SDK для веб-заданий см. в статье [Использование пакета SDK WebJobs для фоновой обработки управления событиями](../../app-service/webjobs-sdk-how-to.md).
