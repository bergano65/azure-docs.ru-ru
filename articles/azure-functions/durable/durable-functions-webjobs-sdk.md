---
title: Как запустить долгосрочные функции как WebJobs - Azure
description: Узнайте, как программировать и настраивать запуск устойчивых функций в веб-заданиях с помощью пакета SDK для веб-заданий.
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: d8dd0c86fbc520d0bd3ef6034891bd9871774b4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232743"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>Как запустить долгосрочные функции как WebJobs

По умолчанию функции Durable functions используют время выполнения функций Azure для размещения оркестровок. Однако могут быть определенные сценарии, в которых требуется больший контроль над кодом, который слушает события. В этой статье показано, как реализовать оркестровку с помощью WebJobs SDK. Чтобы увидеть более подробное сравнение между функциями и WebJobs, [см. Сравнить функции и WebJobs](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

Расширения [Функций Azure](../functions-overview.md) и [устойчивых функций](durable-functions-overview.md) созданы на основе [пакета SDK для веб-заданий](../../app-service/webjobs-sdk-how-to.md). Ухост в SDK WebJobs — это время выполнения в функциях Azure. Если вам необходимо контролировать поведение способами, которые невозможны в Azure Functions, вы можете разрабатывать и запускать долгосрочные функции с помощью WebJobs SDK самостоятельно.

В версии 3.x WebJobs SDK, хост является реализацией, `IHost`и `JobHost` в версии 2.x вы используете объект.

Образец цепных функций имеется в версии WebJobs SDK 2.x: загрузите или клоните [репозиторий «Протерловые функции»](https://github.com/azure/azure-functions-durable-extension/)и перейдите в папку *для цепочки webjobssdk.\\\\*

## <a name="prerequisites"></a>Предварительные требования

В этой статье предполагается, что вы знакомы с основами пакета SDK для веб-заданий, разработки библиотеки классов C# для Функций Azure и устойчивых функций. Ознакомиться с этими темами вам помогут следующие ресурсы:

* [Начало работы с пакетом SDK WebJobs](../../app-service/webjobs-sdk-get-started.md)
* [Создание первой функции с помощью Visual Studio](../functions-create-your-first-function-visual-studio.md)
* [Устойчивые функции](durable-functions-sequence.md)

Чтобы выполнить действия, описанные в этой статье, сделайте следующее:

* [Установите Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/) с рабочей нагрузкой **разработки Azure.**

  Если у вас уже есть Visual Studio, но у вас нет такой рабочей нагрузки, добавьте рабочую нагрузку, выбрав **инструменты** > **Get Tools and Features.**

  (Вместо этого можно использовать [Visual Studio Code](https://code.visualstudio.com/), но некоторые инструкции относятся к Visual Studio.)

* Установите и запустите [эмулятор хранилища Azure](../../storage/common/storage-use-emulator.md) версии 5.2 или выше. Альтернативой является добавление в файл *App.config* строки подключения к службе хранилища Azure.

## <a name="webjobs-sdk-versions"></a>Версии пакета SDK для веб-заданий

В этой статье объясняется, как разрабатывать проект пакета SDK для веб-заданий версии 2.x (эквивалентен Функциям Azure версии 1.x). Дополнительные сведения о версии 3.x см. в разделе [Пакет SDK для веб-заданий версии 3.x](#webjobs-sdk-3x) далее в этой статье.

## <a name="create-a-console-app"></a>Создание консольного приложения

Для запуска функций длительного пользования как WebJobs необходимо сначала создать консольное приложение. Проект пакета SDK для веб-заданий — это просто проект консольного приложения с установленными соответствующими пакетами NuGet.

В диалоговом окне Visual Studio **New Project** выберите **Windows Classic Desktop** > **Console App (.NET Framework)**. В файле проекта `TargetFrameworkVersion` должна иметь значение `v4.6.1`.

Visual Studio также имеет шаблон проекта WebJob, который можно использовать, выбрав **Cloud** > **Azure WebJob (.NET Framework).** Этот шаблон устанавливает множество пакетов, некоторые из которых могут не потребоваться.

## <a name="install-nuget-packages"></a>Установка пакетов Nuget

Вам потребуются пакеты NuGet для пакета SDK для веб-заданий, основные привязки, платформа ведения журналов и расширение устойчивых задач. Вот команды **консоли менеджера пакетов** для этих пакетов, с последними стабильными номерами версии на дату, когда была написана эта статья:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.8.3
```

Вам также понадобятся регистраторы. Следующие команды устанавливают поставщик Azure Application `ConfigurationManager`Insights и . `ConfigurationManager` позволяет получить ключ инструментирования Application Insights из параметров приложения.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

Следующая команда устанавливает поставщик консоли:

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>Код JobHost

Создав приложение для консоли и установив необходимые пакеты NuGet, вы готовы использовать функции durable. Вы делаете это с помощью кода JobHost.

Чтобы использовать расширение устойчивых функций, вызовите `UseDurableTask` для объекта `JobHostConfiguration` в вашем методе `Main`:

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

Список свойств, которые можно задать в объекте `DurableTaskExtension`, см. в файле [host.json](../functions-host-json.md#durabletask).

Метод `Main` служит также местом для настройки регистраторов. Следующий пример настраивает консоль и приложения Исследования поставщиков.

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

## <a name="functions"></a>Функции

Долгосрочные функции в контексте WebJobs несколько отличаются от долгосрочных функций в контексте функций Azure. Важно быть в курсе различий при написании кода.

Пакет SDK для веб-заданий не поддерживает следующие возможности Функций Azure:

* [Атрибут FunctionName](#functionname-attribute)
* [Триггер HTTP](#http-trigger)
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

В проекте WebJobs SDK можно вызвать методы на объекте клиента оркестровки, а не путем отправки запросов HTTP. Следующие методы соответствуют трем задачам, которые можно выполнить с помощью API управления HTTP.

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

Функция оркестровки клиента в проекте образца запускает функцию оркестратора, а затем переходит в цикл, который вызывает `GetStatusAsync` каждые 2 секунды:

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

У вас есть функции,которые будут работать в качестве WebJob, и теперь у вас есть понимание того, чем это будет отличаться от выполнения функций длительного пользования в качестве автономных функций Azure. На этом этапе, видя его работу в образце может быть полезным.

Этот раздел содержит общие сведения о способах запуска [примера проекта](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/webjobssdk/chaining). Подробные инструкции о том, как запустить проект пакета SDK для веб-заданий локально и развернуть его в веб-задание Azure, см. в разделе [Развертывание в качестве WebJob (веб-задания)](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob).

### <a name="run-locally"></a>Локальный запуск

1. Запустите эмулятор хранилища (см. раздел [Предварительные требования](#prerequisites)).

1. Если вы хотите видеть журналы в Application Insights при локальном запуске проекта:

    а. Создайте ресурс Insights приложений и используйте для него **общий** тип приложения.

    b. Сохраните ключ инструментирования в файл *App.config*.

1. Запустите проект.

### <a name="run-in-azure"></a>Запуск в Azure

1. Создайте веб-приложение и учетную запись хранения.

1. В веб-приложении сохраните строку подключения `AzureWebJobsStorage`к хранилищу в настройке приложения под названием.

1. Создайте ресурс Insights приложений и используйте для него **общий** тип приложения.

1. Сохранить ключ приборов в настройке приложения под названием. `APPINSIGHTS_INSTRUMENTATIONKEY`

1. Разверните приложение в качестве веб-задания.

## <a name="webjobs-sdk-3x"></a>Пакет SDK для веб-заданий версии 3.x

В этой статье объясняется, как разработать проект WebJobs SDK 2.x. Если вы разрабатываете проект [WebJobs SDK 3.x,](../../app-service/webjobs-sdk-get-started.md) этот раздел поможет вам понять различия.

Основное изменение, внесенное, заключается в использовании .NET Core вместо рамочной системы .NET. Для создания проекта WebJobs SDK 3.x инструкции одинаковы, за этими исключениями:

1. Создайте консольное приложение .NET Core. В диалоговом окне Visual Studio **New Project** выберите **приложение .NET Core** > **Console App (.NET Core)**. Файл проекта указывает, что `TargetFramework` является `netcoreapp2.x`.

1. Выберите версию webJobs SDK 3.x из следующих пакетов:

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Extensions.Storage`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. Установите строку подключения к хранилищам и клавишу инструментария Application Insights в *файле appsettings.json* с помощью платформы конфигурации .NET Core. Ниже приведен пример:

    ```json
        {
            "AzureWebJobsStorage": "<replace with storage connection string>",
            "APPINSIGHTS_INSTRUMENTATIONKEY": "<replace with Application Insights instrumentation key>"
        }
    ```

1. Изменить `Main` код метода для этого. Ниже приведен пример:

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
