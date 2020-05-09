---
title: Мониторинг службы хранилища Azure | Документация Майкрософт
description: Узнайте, как отслеживать производительность и доступность службы хранилища Azure.
author: normesta
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: normesta
ms.reviewer: fryu
ms.openlocfilehash: 5564634471045838dae3344dc883b6fdc203711e
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82722925"
---
# <a name="monitoring-azure-storage"></a>Мониторинг службы хранилища Azure

При наличии критически важных приложений и бизнес-процессов, зависящих от ресурсов Azure, необходимо отслеживать эти ресурсы на предмет их доступности, производительности и работы. В этой статье описываются данные мониторинга, создаваемые службой хранилища Azure, а также использование функций Azure Monitor для анализа предупреждений об этих данных.

> [!NOTE]
> Журналы службы хранилища Azure в Azure Monitor находятся в общедоступной предварительной версии и доступны для предварительного тестирования во всех регионах общедоступного облака. Чтобы зарегистрироваться в предварительной версии, см. [эту страницу](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).  Эта предварительная версия включает журналы для больших двоичных объектов (включая Azure Data Lake Storage 2-го поколения), файлов, очередей, таблиц, учетных записей хранения класса Premium в учетных записях хранения общего назначения версии 1 и общего назначения v2. Классические учетные записи хранения не поддерживаются.

## <a name="monitor-overview"></a>Обзор монитора

На странице **Обзор** в портал Azure для каждого ресурса хранилища содержится краткое представление об использовании ресурсов, включая его запрос и почасовую оплату. Это полезная информация, но только небольшой объем доступных данных мониторинга. Некоторые из этих данных собираются автоматически и доступны для анализа сразу после создания ресурса хранилища. Можно включить дополнительные типы сбора данных с помощью некоторой конфигурации.

## <a name="what-is-azure-monitor"></a>Общие сведения об Azure Monitor
Служба хранилища Azure создает данные мониторинга с помощью [Azure Monitor](../../azure-monitor/overview.md) , которая представляет собой полную службу мониторинга стека в Azure, которая предоставляет полный набор функций для мониторинга ресурсов Azure в дополнение к ресурсам в других облаках и локальных средах. 

Начните с статьи [мониторинг ресурсов Azure с помощью Azure Monitor](../../azure-monitor/insights/monitor-azure-resource.md) , который описывает следующее:

- Общие сведения об Azure Monitor
- Затраты, связанные с мониторингом
- Данные мониторинга, собранные в Azure
- Настройка коллекции данных
- Стандартные средства в Azure для анализа и оповещения о данных мониторинга

Следующие разделы посвящены описанию данных, собираемых из службы хранилища Azure, и предоставлены примеры настройки сбора данных и анализа этих данных с помощью средств Azure.

## <a name="monitoring-data-from-azure-storage"></a>Мониторинг данных из службы хранилища Azure

Служба хранилища Azure собирает те же данные мониторинга, что и другие ресурсы Azure, которые описаны в разделе [мониторинг данных из ресурсов Azure](../../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). Подробный справочник по журналам и метрикам, созданным службой хранилища Azure, см. в [справочнике по данным мониторинга хранилища Azure](monitor-storage-reference.md) .

Метрики и журналы в Azure Monitor поддерживают только Azure Resource Manager учетные записи хранения. Azure Monitor не поддерживает классические учетные записи хранения. Если вы хотите использовать метрики или журналы в классической учетной записи хранения, необходимо перейти на Azure Resource Manager учетную запись хранения. См. статью [Поддерживаемый платформой перенос ресурсов IaaS из классической модели в модель Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).

При необходимости вы можете продолжать использовать классические метрики и журналы. Фактически, классические метрики и журналы доступны параллельно с метриками и журналами в Azure Monitor. Поддержка остается на месте до тех пор, пока служба хранилища Azure не завершит службу на основе устаревших метрик и журналов. 

### <a name="logs-in-azure-monitor-preview"></a>Журналы Azure Monitor (Предварительная версия)

Записи журнала создаются только при получении запроса к конечной точке службы. Например, если учетная запись хранения имеет действие в конечной точке большого двоичного объекта, но не находится в конечных точках таблицы или очереди, будут созданы только журналы, относящиеся к службе BLOB-объектов. Журналы службы хранилища Azure содержат подробные сведения об успешных и неудачных запросах к службе хранилища. Эта информация может использоваться для мониторинга отдельных запросов и диагностики неполадок в службе хранилища. Запросы вносятся в журнал наилучшим возможным образом.

#### <a name="logging-authenticated-requests"></a>Ведение журналов запросов, прошедших аутентификацию

 Регистрируются запросы, прошедшие аутентификацию, следующих типов:

- Успешные запросы.
- Неудачные запросы, в том числе из-за ошибок, связанных с временем ожидания, регулированием, сетью, авторизацией и др.
- Запросы, в которых используется подписанный URL-адрес (SAS) или OAuth, в том числе неудачные и успешные запросы.
- Запросы к данным аналитики (классические данные журнала в контейнере **$logs** и данные метрик класса в таблицах **$Metric** )

Запросы, выполняемые самой службой хранилища, например создание или удаление журнала, не регистрируются. Полный список записанных данных приведен в разделах « [регистрируемые в журнале операции и сообщения о состоянии](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) » и « [Формат журнала хранилища](monitor-storage-reference.md) ».

#### <a name="logging-anonymous-requests"></a>Ведение журналов анонимных запросов

 Регистрируются анонимные запросы следующих типов:

- Успешные запросы.
- ошибки сервера;
- Ошибки времени ожидания для клиента и сервера
- Неудачные запросы GET с кодом ошибки 304 (не изменено).

Остальные неудачные анонимные запросы не регистрируются. Полный список записанных данных приведен в разделах « [регистрируемые в журнале операции и сообщения о состоянии](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) » и « [Формат журнала хранилища](monitor-storage-reference.md) ».

## <a name="configuration"></a>Параметр Configuration

Метрики платформы и журнал действий собираются автоматически, но необходимо создать параметр диагностики для сбора журналов ресурсов или пересылки их за пределы Azure Monitor. Подробный процесс создания параметров диагностики с помощью портал Azure, CLI или PowerShell см. в статье [Создание параметров диагностики для сбора журналов и метрик платформы в Azure](../../azure-monitor/platform/diagnostic-settings.md) .

При создании параметра диагностики необходимо выбрать тип хранилища, для которого необходимо включить журналы (большой двоичный объект, очередь, таблица, файл). При создании параметра диагностики в портал Azure можно выбрать ресурс из списка. При использовании PowerShell или Azure CLI необходимо использовать идентификатор ресурса типа хранилища. Идентификатор ресурса можно найти в портал Azure, открыв страницу **свойств** учетной записи хранения.

Также необходимо указать категории операций, для которых будут собираются журналы. Категории для службы хранилища Azure перечислены в следующей таблице.

| Категория | Описание |
|:---|:---|
| сторажереад | Операции чтения с BLOB-объектами.  |
| сторажеврите | Операции записи с большими двоичными объектами. |
| сторажеделете | Операции удаления для больших двоичных объектов. |

## <a name="analyzing-metric-data"></a>Анализ данных метрик

Вы можете анализировать метрики для службы хранилища Azure с метриками из других служб Azure с помощью обозревателя метрик. Откройте обозреватель метрик, выбрав **метрики** в меню **Azure Monitor** . Дополнительные сведения об использовании этого средства см. в статье [Приступая к работе с Azure обозреватель метрик](../../azure-monitor/platform/metrics-getting-started.md) . 

В примере ниже показано, как просмотреть **транзакции** на уровне учетной записи.

![снимок экрана доступа к метрикам на портале Azure](./media/monitor-storage/access-metrics-portal.png)

Для метрик с поддержкой измерений необходимо выполнить фильтрацию по нужному значению измерения. В примере ниже объясняется, как просмотреть **транзакции** на уровне учетной записи для определенной операции, выбрав значения для измерения **Имя API**.

![снимок экрана доступа к метрикам с поддержкой измерений на портале Azure](./media/monitor-storage/access-metrics-portal-with-dimension.png)

Полный список измерений, поддерживаемых службой хранилища Azure, см. в разделе [измерения метрик](monitor-storage-reference.md#metrics-dimensions).

Все метрики для службы хранилища Azure находятся в следующих пространствах имен:

- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/blobServices
- Microsoft.Storage/storageAccounts/fileServices
- Microsoft.Storage/storageAccounts/queueServices
- Microsoft.Storage/storageAccounts/tableServices

Список всех метрик поддержки Azure Monitor (включая службу хранилища Azure) см. в статье [Azure Monitor поддерживаемые метрики](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported).


### <a name="access-metrics"></a>Доступ к метрикам

> [!TIP]
> Чтобы просмотреть примеры Azure CLI или .NET, перейдите на соответствующую вкладку ниже.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>Вывод определения метрики

Вы можете составить список определений метрик учетной записи хранения или отдельной службы хранилища, например службы BLOB-объектов, файлов, таблиц или очередей. Используйте командлет [Get-азметрикдефинитион](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetricdefinition?view=azps-3.3.0) .

В этом примере замените `<resource-ID>` ЗАполнитель идентификатором ресурса всей учетной записи хранения или идентификатором ресурса отдельной службы хранилища, например в качестве службы BLOB-объектов, файлов, таблиц или очередей. Эти идентификаторы ресурсов можно найти на страницах **свойств** вашей учетной записи хранения на портал Azure.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="read-metric-values"></a>Считывание значений метрик

Вы можете читать значения метрик на уровне учетной записи хранения или отдельную службу хранилища, например службу больших двоичных объектов, файлов, таблиц или очередей. Используйте командлет [Get-азметрик](https://docs.microsoft.com/powershell/module/Az.Monitor/Get-AzMetric?view=azps-3.3.0) .

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

#### <a name="list-account-level-metric-definition"></a>Список определений метрик уровня учетной записи

Вы можете составить список определений метрик учетной записи хранения или отдельной службы хранилища, например службы BLOB-объектов, файлов, таблиц или очередей. Используйте команду [AZ Monitor метрик List-Definitions](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list-definitions) .
 
В этом примере замените `<resource-ID>` ЗАполнитель идентификатором ресурса всей учетной записи хранения или идентификатором ресурса отдельной службы хранилища, например в качестве службы BLOB-объектов, файлов, таблиц или очередей. Эти идентификаторы ресурсов можно найти на страницах **свойств** вашей учетной записи хранения на портал Azure.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Чтение значений метрик на уровне учетной записи

Вы можете прочитать значения метрик вашей учетной записи хранения или отдельную службу хранилища, например службу BLOB-объектов, файл, таблицу или очередь. Используйте команду [AZ Monitor метрик List](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list) .

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/dotnet)

Azure Monitor предоставляет [пакет SDK для .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) для чтения определения и значений метрик. [Пример кода](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) демонстрирует использование пакета SDK с различными параметрами. Необходимо использовать `0.18.0-preview` или более поздней версии для метрик хранилища.
 
В этих примерах замените `<resource-ID>` ЗАполнитель идентификатором ресурса всей учетной записи хранения или идентификатором ресурса отдельной службы хранилища, например в качестве службы BLOB-объектов, файлов, таблиц или очередей. Эти идентификаторы ресурсов можно найти на страницах **свойств** вашей учетной записи хранения на портал Azure.

Замените `<subscription-ID>` ПЕРЕМЕНную идентификатором своей подписки.  Инструкции по получению значений `<tenant-ID>`для, `<application-ID>`и `<AccessKey>`см. в разделе [как использовать портал для создания приложения Azure AD и субъекта-службы, которые имеют доступ к ресурсам](https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/). 

#### <a name="list-account-level-metric-definition"></a>Список определений метрик уровня учетной записи

В следующих примерах показано, как вывести определение метрики на уровне учетной записи:

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";


        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            // Enumrate metric definition:
            //    Id
            //    ResourceId
            //    Name
            //    Unit
            //    MetricAvailabilities
            //    PrimaryAggregationType
            //    Dimensions
            //    IsDimensionRequired
        }
    }

```

#### <a name="read-account-level-metric-values"></a>Чтение значений метрик на уровне учетной записи

В примере ниже показано, как считать данные `UsedCapacity` на уровне учетной записи.

```csharp
    public static async Task ReadStorageMetricValue()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;

        Response = await readOnlyClient.Metrics.ListAsync(
            resourceUri: resourceId,
            timespan: timeSpan,
            interval: System.TimeSpan.FromHours(1),
            metricnames: "UsedCapacity",

            aggregation: "Average",
            resultType: ResultType.Data,
            cancellationToken: CancellationToken.None);

        foreach (var metric in Response.Value)
        {
            // Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

#### <a name="read-multi-dimensional-metric-values"></a>Чтение значений многомерных метрик

Для многомерных метрик необходимо определить фильтр по метаданным, если требуется считать данные метрик для конкретного значения измерения.

В примере ниже показано, как считать данные метрики в метрике, поддерживающей многомерные значения.

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
        var subscriptionId = "<subscription-ID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;
        // It's applicable to define meta data filter when a metric support dimension
        // More conditions can be added with the 'or' and 'and' operators, example: BlobType eq 'BlockBlob' or BlobType eq 'PageBlob'
        ODataQuery<MetadataValue> odataFilterMetrics = new ODataQuery<MetadataValue>(
            string.Format("BlobType eq '{0}'", "BlockBlob"));

        Response = readOnlyClient.Metrics.List(
                        resourceUri: resourceId,
                        timespan: timeSpan,
                        interval: System.TimeSpan.FromHours(1),
                        metricnames: "BlobCapacity",
                        odataQuery: odataFilterMetrics,
                        aggregation: "Average",
                        resultType: ResultType.Data);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

---

## <a name="analyzing-log-data"></a>Анализ данных журнала

Доступ к журналам ресурсов можно получить в виде большого двоичного объекта в учетной записи хранения, в виде данных события или с помощью запросов аналитики журнала.

Подробные справочные сведения о полях, которые отображаются в этих журналах, см. в [справочнике по данным мониторинга хранилища Azure](monitor-storage-reference.md) .

> [!NOTE]
> Журналы службы хранилища Azure в Azure Monitor находятся в общедоступной предварительной версии и доступны для предварительного тестирования во всех регионах общедоступного облака. Чтобы зарегистрироваться в предварительной версии, см. [эту страницу](https://www.microsoft.com).  Эта предварительная версия включает журналы для больших двоичных объектов (включая Azure Data Lake Storage 2-го поколения), файлов, очередей, таблиц, учетных записей хранения класса Premium в учетных записях хранения общего назначения версии 1 и общего назначения v2. Классические учетные записи хранения не поддерживаются.

### <a name="access-logs-in-a-storage-account"></a>Доступ к журналам в учетной записи хранения

Журналы отображаются как большие двоичные объекты, хранящиеся в контейнере в целевой учетной записи хранения. Данные собираются и хранятся внутри одного большого двоичного объекта как полезные данные JSON, разделенные строками. Имя BLOB-объекта соответствует описанному ниже соглашению об именовании.

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/blobServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Ниже приведен пример:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="access-logs-in-event-hub"></a>Доступ к журналам в концентраторе событий

Журналы, отправленные в концентратор событий, не сохраняются в виде файла, но можно проверить, что концентратор событий получил данные журнала. В портал Azure перейдите к концентратору событий и убедитесь, что число **входящих сообщений** больше нуля. 

![Журналы аудита](media/monitor-storage/event-hub-log.png)

Вы можете получать доступ к данным журнала, отправляемым в концентратор событий, и читать их, используя сведения о безопасности и средствах мониторинга и управления событиями. Дополнительные сведения см. [в разделе что можно делать с данными мониторинга, отправляемыми в мой концентратор событий?](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)

### <a name="access-logs-in-log-analytics-workspace"></a>Доступ к журналам в Log Analytics рабочей области

Доступ к журналам, отправляемым в рабочую область Log Analytics, можно получить с помощью Azure Monitor запросов журналов.

См. статью Начало [работы с log Analytics в Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

Данные хранятся в следующих таблицах.

| Таблица | Описание |
|:---|:---|
|сторажеблоблогс | Журналы, описывающие действия в хранилище BLOB-объектов. |
|сторажефилелогс | Журналы, описывающие действия в общих файловых ресурсах. |
|сторажекуеуелогс | Журналы, описывающие действия в очередях.|
|сторажетаблелогс| Журналы, описывающие действия в таблицах.|

### <a name="azure-storage-log-analytics-queries-in-azure-monitor"></a>Запросы Log Analytics службы хранилища Azure в Azure Monitor

Ниже приведены некоторые запросы, которые можно ввести в строку поиска по **журналам** , чтобы упростить мониторинг учетных записей хранения Azure. Эти запросы поддерживают [новый язык](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

Ниже приведены запросы, которые можно использовать для мониторинга учетных записей хранения Azure.

* Вывод списка из 10 наиболее распространенных ошибок за последние 3 дня.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by StatusText
    | top 10 by count_ desc
    ```
* Для перечисления первых 10 операций, вызывающих наибольшее количество ошибок за последние 3 дня.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by OperationName
    | top 10 by count_ desc
    ```
* Чтобы получить список первых 10 операций с наибольшей задержкой до конца за последние 3 дня.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | top 10 by DurationMs desc
    | project TimeGenerated, OperationName, DurationMs, ServerLatencyMs, ClientLatencyMs = DurationMs - ServerLatencyMs
    ```
* Перечисление всех операций, вызывающих ошибки регулирования на стороне сервера за последние 3 дня.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText contains "ServerBusy"
    | project TimeGenerated, OperationName, StatusCode, StatusText
    ```
* Перечисление всех запросов с анонимным доступом за последние 3 дня.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and AuthenticationType == "Anonymous"
    | project TimeGenerated, OperationName, AuthenticationType, Uri
    ```
* Создание круговой диаграммы операций, используемых за последние 3 дня.
    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | summarize count() by OperationName
    | sort by count_ desc 
    | render piechart
    ```
## <a name="faq"></a>ВОПРОСЫ И ОТВЕТЫ

**Поддерживает ли служба хранилища Microsoft Azure метрики для управляемых и неуправляемых дисков?**

Нет, служба вычислений Azure поддерживает метрики на дисках. Дополнительные сведения см. в [этой статье](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/).

## <a name="next-steps"></a>Дальнейшие действия

- [Справочник по данным мониторинга хранилища Azure](monitor-storage-reference.md) для справки по журналам и метрикам, созданным службой хранилища Azure.
- [Мониторинг ресурсов Azure с Azure Monitor](../../azure-monitor/insights/monitor-azure-resource.md) для получения дополнительных сведений о мониторинге ресурсов Azure.
- [Перенос метрик службы хранилища Azure](./storage-metrics-migration.md)

