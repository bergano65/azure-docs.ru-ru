---
title: Начало работы с пакетом SDK WebJobs в Azure
description: Знакомство с пакетом SDK WebJobs для фоновой обработки на основе событий. Получение доступа к данным в службах Azure и службах сторонних разработчиков.
services: app-service\web, storage
documentationcenter: .net
author: ggailey777
manager: jeconnoc
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: b824c99a015cfa2c1d1c75e2a1257eff482e8dd6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58087964"
---
# <a name="get-started-with-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Использование пакета SDK для Веб-заданий Azure для фоновой обработки на основе событий

В этой статье показано, как с помощью Visual Studio 2017 создайте проект пакета SDK для веб-заданий Azure, запустить его локально и затем развернуть его в [службе приложений Azure](overview.md). Проекта, созданного — это консольное приложение .NET Core, использующий версии 3.x, пакета SDK веб-заданий. Если вы заинтересованы в версии 2.x, которая использует .NET Framework, см. в разделе [разработка и развертывание веб-заданий с помощью Visual Studio: службе приложений Azure](webjobs-dotnet-deploy-vs.md).

Дополнительные сведения о работе с пакетом SDK веб-заданий, см. в разделе [как использовать пакет SDK веб-заданий Azure для управляемых событиями фоновой обработки](webjobs-sdk-how-to.md).

## <a name="prerequisites"></a>Технические условия

* [Установите Visual Studio 2017](/visualstudio/install/) с рабочей нагрузкой **разработки в Azure**. Если у вас уже установлена среда Visual Studio, но нет компонентов для разработки приложений, добавьте их, выбрав **Tools > Get Tools and Features** (Инструменты > Добавить инструменты и компоненты).

* Необходимо иметь [учетной записи Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) для публикации веб-заданий SDK проекта в Azure.

## <a name="create-a-project"></a>Создание проекта

1. В Visual Studio выберите **Файл > Создать > Проект**.

2. Выберите **.NET Core > консольное приложение (.NET Core)**.

3. Назовите проект *WebJobsSDKSample*, а затем нажмите кнопку **OK**.

   ![Диалоговое окно "Новый проект"](./media/webjobs-sdk-get-started/new-project.png)

## <a name="webjobs-nuget-packages"></a>Пакеты NuGet веб-заданий

1. Установите последние стабильные версии 3.x следующих пакетов NuGet:

   * `Microsoft.Azure.WebJobs`
   * `Microsoft.Azure.WebJobs.Extensions`

     Вот **консоль диспетчера пакетов** команд для версии 3.0.4:

     ```powershell
     Install-Package Microsoft.Azure.WebJobs -version 3.0.4
     Install-Package Microsoft.Azure.WebJobs.Extensions -version 3.0.1
     ```

## <a name="create-the-host"></a>Создание узла

Узел представляет контейнер среды выполнения для функций, который ожидает передачи данных для триггеров и вызовы функций. Ниже приведены инструкции по созданию узла, который реализует [ `IHost` ](/dotnet/api/microsoft.extensions.hosting.ihost), который является универсальный узел в ASP.NET Core.

1. В *Program.cs* добавьте инструкцию `using`:

    ```cs
    using Microsoft.Extensions.Hosting;
    ```

1. Замените метод `Main` следующим кодом:

    ```cs
    static void Main(string[] args)
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        var host = builder.Build();
        using (host)
        {
            host.Run();
        }
    }
    ```

В ASP.NET Core конфигурации узла настраиваются путем вызова методов в экземпляре [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder). Дополнительные сведения можно найти в статье [Универсальный узел .NET](/aspnet/core/fundamentals/host/generic-host). Метод расширения `ConfigureWebJobs` инициализирует узел веб-заданий. В `ConfigureWebJobs`, можно инициализировать специальные расширения веб-заданий и задать свойства этих расширений.  

## <a name="enable-console-logging"></a>Включение журналирования консоли

В этом разделе вы Настройка ведения журнала консоли, использующего [платформу ведения журналов ASP.NET Core](/aspnet/core/fundamentals/logging).

1. Установите последнюю стабильную версию следующих пакетов NuGet:

   * `Microsoft.Extensions.Logging` — платформа ведения журналов;
   * `Microsoft.Extensions.Logging.Console` -Поставщик console, который отправляет журналы в консоль.

   Ниже приведены команды **консоли диспетчера пакетов** для версии 2.2.0:

   ```powershell
   Install-Package Microsoft.Extensions.Logging -version 2.2.0
   ```

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version 2.2.0
   ```

1. В *Program.cs* добавьте инструкцию `using`:

   ```cs
   using Microsoft.Extensions.Logging;
   ```

1. Вызовите метод [`ConfigureLogging`](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) в [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder). Метод [`AddConsole`](/dotnet/api/microsoft.extensions.logging.consoleloggerextensions.addconsole) добавляет в конфигурацию ведение журнала консоли.

    ```cs
    builder.ConfigureLogging((context, b) =>
    {
        b.AddConsole();
    });
    ```

    Метод `Main` теперь выглядит следующим образом:

    ```cs
    static void Main(string[] args)
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        builder.ConfigureLogging((context, b) =>
                {
                    b.AddConsole();
                });
        var host = builder.Build();
        using (host)
        {
            host.Run();
        }
    }
    ```

    Это обновление выполняет следующие функции:

    * Отключает [ведение журнала панели мониторинга](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs). Панель мониторинга — это устаревшее средство мониторинга, поэтому ведение журнала панели мониторинга не рекомендуется в высокопроизводительных сценариях.
    * Добавляет поставщика консоли с [фильтрацией](webjobs-sdk-how-to.md#log-filtering) по умолчанию.

Теперь можно добавить функцию, которая активируется с помощью сообщений, поступающих в [очередь службы хранилища Azure](../azure-functions/functions-bindings-storage-queue.md).

## <a name="install-the-storage-binding-extension"></a>Установка расширения привязки Службы хранилища

Начиная с версии 3.x, необходимо явно установить расширение привязки хранилища, необходимо с помощью пакета SDK веб-заданий. В предыдущих версиях привязки хранилища были включены в пакет SDK.

1. Установите последнюю стабильную версию пакета NuGet [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage), версию 3.х. 

    Вот **консоль диспетчера пакетов** команду для версии 3.0.3:

    ```powershell
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version 3.0.3
    ```

2. В методе расширения `ConfigureWebJobs` вызовите метод `AddAzureStorage` в экземпляре [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder), чтобы инициализировать расширение службы хранилища. На этом этапе метод `ConfigureWebJobs` выглядит следующим образом:

    ```cs
    builder.ConfigureWebJobs(b =>
                    {
                        b.AddAzureStorageCoreServices();
                        b.AddAzureStorage();
                    });
    ```

## <a name="create-a-function"></a>Создание функции

1. Щелкните проект правой кнопкой мыши **добавить** > **новый элемент...** , выберите **класс**, имя нового C# файл класса *Functions.cs*и выберите **добавить**.

1. В сценарии Functions.cs замените созданный шаблон следующим кодом:

   ```cs
   using Microsoft.Azure.WebJobs;
   using Microsoft.Extensions.Logging;

   namespace WebJobsSDKSample
   {
       public class Functions
       {
           public static void ProcessQueueMessage([QueueTrigger("queue")] string message, ILogger logger)
           {
               logger.LogInformation(message);
           }
       }
   }
   ```

   Атрибут `QueueTrigger` вызывает в среде выполнения эту функцию при записи нового сообщения в очередь службы хранилища Azure с именем `queue`. Содержимое сообщения в очереди передается коду метода в параметре `message`. Тело метода реализуется при обработке данных триггера. В этом примере код просто записывает сообщение в журнал.

   Параметр `message` не должен быть строкой. Также можно выполнить привязку к объекту JSON, массиву байтов или [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage). [См. "Использование триггера очереди"](../azure-functions/functions-bindings-storage-queue.md#trigger---usage). Каждый тип привязки (например, очереди, BLOB-объекты или таблицы) обладает своим набором типов параметров привязки.

## <a name="create-a-storage-account"></a>Создание учетной записи хранения

Эмулятор службы хранилища Azure, работающий локально, не обладает всеми возможностями, необходимыми пакету SDK WebJobs. Поэтому в этом разделе вы создать учетную запись хранилища в Azure и настроить проект для его использования. Если у вас уже есть учетную запись хранения, перейдите к шагу 6.

1. В Visual Studio откройте **обозреватель сервера** и войдите в Azure. Щелкните правой кнопкой мыши узел **Azure** и выберите команду **Connect to Microsoft Azure Subscription** (Подключение к подписке Microsoft Azure).

   ![Вход в Azure](./media/webjobs-sdk-get-started/sign-in.png)

1. В узле **Azure** **обозревателя сервера** щелкните правой кнопкой мыши элемент **Хранилище** и выберите **Создать учетную запись хранения**.

   ![Создание меню учетной записи хранения](./media/webjobs-sdk-get-started/create-storage-account-menu.png)

1. В диалоговом окне **Создание учетной записи хранения** введите уникальное имя учетной записи хранения.

1. Выберите тот же **Регион**, в котором вы создали приложение службы приложений, или ближайший к вам регион.

1. Нажмите кнопку **Создать**.

   ![Создание учетной записи хранения](./media/webjobs-sdk-get-started/create-storage-account.png)

1. В узле **Хранилище** **обозревателя серверов** выберите новую учетную запись хранения. В окне **Свойства** выберите многоточие (**...**) справа от поля со значением **Строка подключения**.

   ![Многоточие в строке подключения](./media/webjobs-sdk-get-started/conn-string-ellipsis.png)

1. Скопируйте строку подключения и сохраните это значение где-либо, откуда впоследствии его можно будет легко скопировать еще раз.

   ![Копирование строки подключения](./media/webjobs-sdk-get-started/copy-key.png)

## <a name="configure-storage-to-run-locally"></a>Настройка хранилища для локального запуска

Пакет SDK веб-заданий ищет строку подключения в параметрах приложения в Azure. При локальном запуске он ищет это значение в локальном файле конфигурации или среди переменных среды.

1. Щелкните проект правой кнопкой мыши **добавить** > **новый элемент...** , выберите **файл конфигурации JavaScript JSON**, назовите новый файл *appsettings.json* файла и выберите **добавить**. 

1. Добавьте в новый файл `AzureWebJobsStorage` поле, как показано в следующем примере:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}"
    }
    ```

1. Замените значение *{storage connection string}* на строку подключения, скопированную ранее.

1. Выберите *appsettings.json* файл в обозревателе решений и в **свойства** окне **Копировать в выходной каталог** для **копировать, если новее**.

Позже вы добавите тот же параметр приложения строки подключения в приложение в службе приложений Azure.

## <a name="test-locally"></a>Локальное тестирование

В этом разделе проект создается и запускается локально, а кроме того, посредством создания очереди сообщений запускается триггер вызова функции.

1. Нажмите клавишу **Ctrl + F5** для запуска проекта.

   Консоль показывает, что среда выполнения обнаружила функцию и ожидает сообщений в очереди для ее активации. На узле версии 3.x отобразятся следующие сообщения:

   ```console
    info: Microsoft.Azure.WebJobs.Hosting.JobHostService[0]
          Starting JobHost
    info: Host.Startup[0]
          Found the following functions:
          WebJobsSDKSample.Functions.ProcessQueueMessage

    info: Host.Startup[0]
          Job host started
    Application started. Press Ctrl+C to shut down.
    Hosting environment: Development
    Content root path: C:\WebJobsSDKSample\WebJobsSDKSample\bin\Debug\netcoreapp2.1\
   ```

1. Закройте окно консоли.

1. В Visual Studio в **обозревателе сервера** разверните узел новой учетной записи хранения и щелкните правой кнопкой мыши элемент **Очереди**.

1. Выберите **Создать очередь**.

1. Введите *queue* в качестве имени очереди, а затем нажмите кнопку **OK**.

   ![Создание очереди](./media/webjobs-sdk-get-started/create-queue.png)

1. Щелкните правой кнопкой мыши узел новой очереди, а затем выберите команду **Просмотреть очередь**.

1. Выберите значок **Добавить сообщение**.

   ![Создание очереди](./media/webjobs-sdk-get-started/create-queue-message.png)

1. В диалоговом окне **Добавить сообщение** введите *Здравствуй, мир!* в поле **Текст сообщения**, а затем нажмите кнопку **OK**. Теперь есть сообщение в очереди.

   ![Создание очереди](./media/webjobs-sdk-get-started/hello-world-text.png)

1. Запустите проект снова.

   Поскольку в функции `ProcessQueueMessage` использовался атрибут `QueueTrigger`, среда выполнения пакета SDK WebJobs прослушивает запускаемые сообщения в очереди. Она находит новое сообщение в очереди с именем *queue* и вызывает функцию.

   Из-за [экспоненциально растущего интервала опроса очереди](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm) процесс поиска сообщения и вызова функции может занимать до 2 минут. Время отклика можно сократить путем запуска в [режиме разработки](webjobs-sdk-how-to.md#host-development-settings).

   В консоли отобразятся следующие сообщения:

   ```console
    info: Function.ProcessQueueMessage[0]
          Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=2c319369-d381-43f3-aedf-ff538a4209b8)
    info: Function.ProcessQueueMessage[0]
          Trigger Details: MessageId: b00a86dc-298d-4cd2-811f-98ec39545539, DequeueCount: 1, InsertionTime: 1/18/2019 3:28:51 AM +00:00
    info: Function.ProcessQueueMessage.User[0]
          Hello World!
    info: Function.ProcessQueueMessage[0]
          Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=2c319369-d381-43f3-aedf-ff538a4209b8)
   ```

1. Закройте окно консоли. 

1. Вернитесь в окно очереди и обновить его. Сообщение отсутствует, так как он был обработан текущая функция выполняется локально. 

## <a name="add-application-insights-logging"></a>Добавление журнала Application Insights

При выполнении проекта в Azure вам не удастся отследить выполнение функции в консоли. В качестве решения для мониторинга рекомендуется использовать [Application Insights](../azure-monitor/app/app-insights-overview.md). Дополнительные сведения см. в разделе [Мониторинг функций Azure](../azure-functions/functions-monitoring.md).

В этом разделе рассматриваются следующие задания по настройке ведения журнала Application Insights перед развертыванием в Azure:

* Убедитесь, что у вас есть приложение службы приложений Azure и экземпляр Application Insights, с которым будет вестись работа.
* Настройте приложение службы приложений Azure для использования экземпляра Application Insights и учетной записи хранения, созданной ранее.
* Настройте проект ведения журнала Application Insights.

### <a name="create-app-service-app-and-application-insights-instance"></a>Создание приложения службы приложений Azure и экземпляра Application Insights

1. Если у вас еще нет приложения службы приложений, которое можно использовать, [создайте его](app-service-web-get-started-dotnet-framework.md). При создании приложения можно также создать подключенный ресурс Application Insights. После этого параметр `APPINSIGHTS_INSTRUMENTATIONKEY` будет настроен в вашем приложении.

1. Если у вас еще нет доступного ресурса Application Insights, [создайте его](../azure-monitor/app/create-new-resource.md ). Установите для параметра **Тип приложения** значение **Общий** и пропустите разделы, следующие за разделом **Copy the instrumentation key** (Копировать ключ инструментирования).

1. При наличии ресурса Application Insights, который вы собираетесь использовать, [скопируйте ключ инструментирования](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key).

### <a name="configure-app-settings"></a>Настройка параметров приложения 

1. В Visual Studio в **обозревателе сервера** разверните узел **Служба приложений** в разделе **Azure**.

1. Разверните группу ресурсов с приложением службы приложений, а затем выберите это приложение службы приложений.

1. Выберите **Просмотр параметров**.

1. В поле **Строки подключения** добавьте следующую запись.

   |ИМЯ  |Строка подключения  |Тип базы данных|
   |---------|---------|------|
   |AzureWebJobsStorage | {строка подключения к службе хранилища, скопированная ранее}|Пользовательская|

1. Если поле **Параметры приложения** не содержит ключа инструментирования Application Insights, добавьте один, скопированный ранее. (Ключ инструментирования уже может быть там, в зависимости от способа создания приложения службы приложений).

   |ИМЯ  |Значение  |
   |---------|---------|
   |APPINSIGHTS_INSTRUMENTATIONKEY | {instrumentation key} |

1. Замените строку *{instrumentation key}* ключом инструментирования с используемого ресурса Application Insights.

1. Щелкните **Сохранить**.

1. Добавьте подключение Application Insights в проект, чтобы запустить его локально. В файле *appsettings.json* добавьте поле `APPINSIGHTS_INSTRUMENTATIONKEY`, как показано в следующем примере:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}",
        "APPINSIGHTS_INSTRUMENTATIONKEY": "{instrumentation key}"
    }
    ```

    Замените строку *{instrumentation key}* ключом инструментирования с используемого ресурса Application Insights.

1. Сохраните изменения.

### <a name="add-application-insights-logging-provider"></a>Добавление поставщика журнала для Application Insights

Чтобы воспользоваться преимуществами ведения журнала [Application Insights](../azure-monitor/app/app-insights-overview.md), обновите код ведения журнала для следующих целей:

* Добавление поставщика журнала Application Insights с [фильтрацией](webjobs-sdk-how-to.md#log-filtering) по умолчанию; при локальном запуске все сведения и журналы более высокого уровня перемещаются одновременно в Application Insights и в консоль.
* Помещение объекта `LoggerFactory` в блок `using`, чтобы активировать освобождение вывода журнала при выходе узла.

1. Установите последнюю стабильную версию 3.x пакета NuGet для поставщика журнала Application Insights: `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`.

   Ниже приведена команда **консоли диспетчера пакетов** для версии 3.0.2:

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version 3.0.2
   ```

1. Откройте файл *Program.cs* и замените код в методе `Main` следующим кодом:

    ```cs
    static void Main(string[] args)
    {
        var builder = new HostBuilder();
        builder.UseEnvironment(EnvironmentName.Development);
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                    b.AddAzureStorage();
                });
        builder.ConfigureLogging((context, b) =>
                {
                    b.AddConsole();

                    // If the key exists in settings, use it to enable Application Insights.
                    string instrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                    if (!string.IsNullOrEmpty(instrumentationKey))
                    {
                        b.AddApplicationInsights(o => o.InstrumentationKey = instrumentationKey);
                    }
                });
        var host = builder.Build();
        using (host)
        {
            host.Run();
        }
    }
    ```

    Это добавляет поставщик Application Insights для ведения журнала, с помощью ключа, которые вы ранее добавили в параметрах приложения.

## <a name="test-application-insights-logging"></a>Тестирование ведения журнала Application Insights

В этом разделе выполняется повторный локальный запуск для проверки того, что данные журналов теперь передаются в Application Insights и в консоль.

1. В Visual Studio в **обозревателе сервера** создайте сообщение в очереди точно так же, как это было сделано [ранее](#trigger-the-function-in-azure), но в качестве текста сообщения введите *Hello App Insights!* на этот раз.

1. Запустите проект.

   Пакет SDK WebJobs обрабатывает сообщения в очереди, и вы можете просматривать журналы в окне консоли.

1. Закройте окно консоли.

1. Вернитесь к ресурсу Application Insights на [портале Azure](https://portal.azure.com/).

1. Выберите **Поиск**.

   ![Выбор команды "Поиск"](./media/webjobs-sdk-get-started/select-search.png)

1. Если вы не видите сообщения *Hello App Insights!*, в течение нескольких минут периодически нажимайте кнопку **Обновить**. (Журналы не отображаются немедленно потому, что клиенту Application Insights требуется некоторое время на освобождение обрабатываемых журналов.)

   ![Журналы в Application Insights](./media/webjobs-sdk-get-started/logs-in-ai.png)

1. Закройте окно консоли.

## <a name="deploy-as-a-webjob"></a>Развертывание в Azure

Во время развертывания создание экземпляра службы приложений, в котором будут выполняться функции. При публикации консольное приложение .NET Core в службе приложений в Azure, он автоматически получает Запуск от имени веб-задания. Дополнительные сведения о публикации, см. в разделе [разработка и развертывание веб-заданий с помощью Visual Studio](webjobs-dotnet-deploy-vs.md).

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)]

## <a name="trigger-the-function-in-azure"></a>Активация функции в Azure

1. Убедитесь, что вы не работаете локально (закройте окно консоли, если оно еще открыто). В противном случае локальный экземпляр может сначала начать обрабатывать созданные вами сообщения в очереди.

1. На странице **Очередь** в Visual Studio добавьте сообщение в очередь, как и раньше.

1. Обновите страницу **Очередь**, после чего новое сообщение исчезнет, так как оно было обработано с помощью функции, работающей в Azure.

   > [!TIP]
   > При тестировании в Azure используйте [режим разработки](webjobs-sdk-how-to.md#host-development-settings), чтобы убедиться, что функция триггера очереди вызывается сразу же, и избежать задержки из-за [экспоненциально растущего интервала опроса очереди](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm).

### <a name="view-logs-in-application-insights"></a>Просмотр журналов Application Insights

1. Вернитесь к ресурсу Application Insights на [портале Azure](https://portal.azure.com/).

1. Выберите **Поиск**.

1. Если вы не видите сообщение *Hello Azure!*, в течение нескольких минут периодически нажимайте кнопку **Обновить**.

   С помощью функции, работающей в веб-задании, можно просмотреть журналы и сообщение *Hello Azure!*, введенное в предыдущем разделе.

## <a name="add-an-input-binding"></a>Добавление привязки входных данных

Привязка входных данных упрощает код чтения данных. В этом примере сообщением в очереди будет имя большого двоичного объекта, используемое при поиске и чтении большого двоичного объекта в службе хранилища Azure.

1. В сценарии *Functions.cs* замените метод `ProcessQueueMessage` следующим кодом:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
   }
   ```

   В этом коде `queueTrigger` — это [выражение привязки](../azure-functions/functions-bindings-expressions-patterns.md), что означает, что его значение определяется во время выполнения.  Во время выполнения оно содержит сообщение в очереди.

1. Добавьте `using`:

   ```cs
   using System.IO;
   ```

1. Создайте контейнер BLOB-объектов в учетной записи хранения.

   a. В Visual Studio в **обозревателе сервера** разверните узел своей учетной записи хранения, щелкните правой кнопкой мыши кнопку **Большие двоичные объекты** и выберите команду **Создать контейнер BLOB-объектов**.

   2. В диалоговом окне **Создать контейнер BLOB-объектов** для имени контейнера введите *container*, а затем нажмите кнопку **OK**.

1. Отправьте файл *Program.cs* в контейнер больших двоичных объектов. (Этот файл используется здесь для примера; вы можете передать любой текстовый файл и создавать сообщение в очереди с его именем.)

   a. В **обозревателе сервера** дважды щелкните узел контейнера, который вы создали.

   2. В окне **Контейнер** нажмите кнопку **Отправить**.

   ![Кнопка отправки BLOB-объекта](./media/webjobs-sdk-get-started/blob-upload-button.png)

   c. Найдите и выберите файл *Program.cs*, а затем нажмите кнопку **OK**.

1. Создайте сообщение в ранее созданной очереди с *Program.cs* в качестве текста сообщения.

   ![Сообщение в очереди на основе сценария Program.cs](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

1. Локальный запуск проекта.

   Сообщение в очереди активирует функцию, которая затем считывает большой двоичный объект и записывает в журнал его размер. В консоли отобразятся следующие сообщения:

   ```console
   Found the following functions:
   ConsoleApp1.Functions.ProcessQueueMessage
   Job host started
   Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   Blob name:Program.cs
   Size: 532 bytes
   Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   ```

## <a name="add-an-output-binding"></a>Добавление выходной привязки

Привязки выходных данных упрощают код записи данных. Данный пример проще предыдущего, так как вместо регистрации размера большого двоичного объекта в журнале была сделана запись его копии. Привязки хранилища BLOB-объектов включены в пакет расширений службы хранилища Azure, установленный ранее.

1. Замените метод `ProcessQueueMessage` следующим кодом:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       [Blob("container/copy-{queueTrigger}", FileAccess.Write)] Stream outputBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
       myBlob.CopyTo(outputBlob);
   }
   ```

1. Создайте другое сообщение в очереди с использованием *Program.cs* в качестве текста сообщения.

1. Локальный запуск проекта.

   Сообщение в очереди активирует функцию, которая затем считывает большой двоичный объект, регистрирует в журнале его размер и создает новый большой двоичный объект. В консоли отобразится аналогичное сообщение, но если перейти в окно контейнера большого двоичного объекта и нажать кнопку **Обновить**, вы увидите новый BLOB-объект с именем *copy-Program.cs*.

## <a name="republish-the-updates-to-azure"></a>Повторно публиковать обновления для Azure

1. Щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите пункт **Опубликовать**.

1. В **публикации** диалоговое окно, убедитесь, что выбран текущий профиль и выберите **публикации**. Результаты публикации, подробно изложены в **вывода** окна.
 
1. Проверьте функции в Azure, снова отправить файлы в контейнер больших двоичных объектов и добавления сообщения в очередь, представляющее имя отправленного файла. Вы увидите сообщение удаляется из очереди и копию файла, созданного в контейнер больших двоичных объектов. 

## <a name="next-steps"></a>Дальнейшие действия

В этой статье показано, как создать, запустить и развернуть проект веб-заданий SDK 3.x.

> [!div class="nextstepaction"]
> [Подробнее о пакете SDK веб-заданий](webjobs-sdk-how-to.md)
