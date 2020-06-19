---
title: Мониторинг службы хранилища Azure | Документация Майкрософт
description: Сведения об отслеживании производительности и доступности службы хранилища Azure.
author: normesta
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring
ms.openlocfilehash: 3ede22b5af942c3f0c0cd88d86b56a625c7656c0
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2020
ms.locfileid: "84267619"
---
# <a name="monitor-azure-storage"></a>Мониторинг службы хранилища Azure

При наличии критически важных приложений и бизнес-процессов, использующих ресурсы Azure, необходимо отслеживать эти ресурсы на предмет их доступности, производительности и функционирования. В этой статье описаны данные мониторинга, создаваемые службой хранилища Azure, и способы использования функций Azure Monitor для анализа оповещений об этих данных.

> [!NOTE]
> Журналы службы хранилища Azure в Azure Monitor предоставляются в общедоступной предварительной версии. Они также доступны для предварительного тестирования во всех регионах общедоступного облака. Чтобы зарегистрироваться для использования предварительной версии, см. [эту страницу](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u). Эта предварительная версия включает журналы для больших двоичных объектов (в том числе Azure Data Lake Storage 2-го поколения), файлов, очередей, таблиц, учетных записей хранения класса Premium общего назначения версии 1 и учетных записей хранения общего назначения версии 2. Классические учетные записи хранения не поддерживаются.

## <a name="monitor-overview"></a>Общие сведения о мониторинге

Страница **Обзор** на портале Azure для каждого ресурса хранилища содержит краткие сведения об использовании ресурсов, например запросы и данные почасового выставления счетов. Эти сведения полезны, но лишь небольшое количество данных мониторинга доступно. Некоторые из этих данных собираются автоматически. Они становятся доступными для анализа сразу после создания ресурса хранилища. Вы можете включить дополнительные типы сбора данных с помощью определенной конфигурации.

## <a name="what-is-azure-monitor"></a>Общие сведения об Azure Monitor
Служба хранилища Azure создает данные мониторинга с помощью [Azure Monitor](../../azure-monitor/overview.md), комплексной службы мониторинга стека в Azure. Azure Monitor предоставляет полный набор функций для мониторинга ресурсов Azure и ресурсов в других облаках, а также в локальной среде. 

Дополнительные сведения об Azure Monitor см. на странице [Мониторинг ресурсов Azure с помощью Azure Monitor](../../azure-monitor/insights/monitor-azure-resource.md). В этой статье описаны следующие темы:

- Общие сведения об Azure Monitor
- затраты, связанные с мониторингом;
- данные мониторинга, собираемые в Azure;
- настройка сбора данных;
- стандартные средства Azure для анализа данных мониторинга и оповещения.

В следующих разделах этой статьи описываются конкретные данные, собранные из службы хранилища Azure. В примерах показано, как настроить сбор данных и анализировать эти данные с помощью средств Azure.

## <a name="monitor-data-from-azure-storage"></a>Мониторинг данных из службы хранилища Azure

Служба хранилища Azure собирает те же данные мониторинга, что и другие ресурсы Azure, описанные в [этом разделе](../../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). Дополнительные сведения о журналах и метриках, созданных службой хранилища Azure, см. на [этой странице](monitor-storage-reference.md).

Метрики и журналы в Azure Monitor поддерживают только учетные записи хранения Azure Resource Manager. Azure Monitor не поддерживает классические учетные записи хранения. Если вы хотите использовать метрики или журналы в классической учетной записи хранения, необходимо выполнить миграцию в учетную запись хранения Azure Resource Manager. См. статью [Поддерживаемый платформой перенос ресурсов IaaS из классической модели в модель Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).

При необходимости вы можете продолжить использование классических метрик и журналов. На самом деле, классические метрики и журналы доступны параллельно с метриками и журналами в Azure Monitor. Предусмотрена та же поддержка, пока в службе хранилища Azure обслуживаются устаревшие метрики и журналы.

### <a name="logs-in-azure-monitor-preview"></a>Журналы в Azure Monitor (предварительная версия)

Записи журнала создаются только при получении запроса к конечной точке службы. Например, при обнаружении действия учетной записи хранения в конечной точке службы BLOB-объектов, но не в ее конечных точках служб таблиц или очередей, создаются журналы, которые относятся только к службе хранилища BLOB-объектов. Журналы службы хранилища Azure содержат подробные сведения об успешных и неудачных запросах к службе хранилища. Эта информация может использоваться для мониторинга отдельных запросов и диагностики неполадок в службе хранилища. Запросы вносятся в журнал наилучшим возможным образом.

#### <a name="log-authenticated-requests"></a>Ведение журналов запросов, прошедших аутентификацию

 Регистрируются запросы, прошедшие аутентификацию, следующих типов:

- Успешные запросы.
- Неудачные запросы, в том числе из-за ошибок, связанных с временем ожидания, регулированием, сетью, авторизацией и др.
- Запросы, в которых используется подписанный URL-адрес (SAS) или OAuth, в том числе неудачные и успешные запросы.
- Запросы к данным аналитики (классические данные журнала в контейнере **$logs** и данные метрик класса в таблицах **$metric**).

Запросы, выполненные самой службой хранилища, например создание или удаление журнала, не регистрируются. Полный список регистрируемых данных приведен на страницах об [операциях с протоколированием и сообщениях о состоянии службы хранилища](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) и [формате журналов службы хранилища](monitor-storage-reference.md).

#### <a name="log-anonymous-requests"></a>Ведение журналов анонимных запросов

 Регистрируются анонимные запросы следующих типов:

- Успешные запросы.
- ошибки сервера;
- Ошибки времени ожидания для клиента и сервера.
- Неудачные запросы GET с кодом ошибки 304 (не изменено).

Остальные неудачные анонимные запросы не регистрируются. Полный список регистрируемых данных приведен на страницах об [операциях с протоколированием и сообщениях о состоянии службы хранилища](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) и [формате журналов службы хранилища](monitor-storage-reference.md).

## <a name="configuration"></a>Конфигурация

Метрики платформы и журнал действий собираются автоматически, но необходимо создать параметр диагностики для сбора журналов ресурсов или их переадресации за пределы Azure Monitor. Сведения о создании параметра диагностики с помощью портала Azure, Azure CLI или PowerShell см. на странице [Создание параметра диагностики для сбора журналов и метрик платформы в Azure](../../azure-monitor/platform/diagnostic-settings.md).

При создании параметра диагностики выберите тип хранилища, для которого необходимо включить журналы, например большой двоичный объект, очередь, таблица или файл. При создании параметра диагностики на портале Azure можно выбрать ресурс из списка. Для PowerShell или Azure CLI необходимо использовать ИД ресурса типа хранилища. ИД ресурса можно найти на портале Azure, открыв страницу **свойств** учетной записи хранения.

Кроме того, необходимо указать категории операций, для которых требуется вести журнал. В приведенной ниже таблице перечислены категории для службы хранилища Azure.

| Категория | Описание |
|:---|:---|
| StorageRead | Операции чтения для больших двоичных объектов. |
| StorageWrite | Операции записи для больших двоичных объектов. |
| StorageDelete | Операции удаления для больших двоичных объектов. |

## <a name="analyze-metric-data"></a>Анализ данных метрик

Вы можете анализировать метрики для службы хранилища Azure с помощью метрик из других служб Azure, используя обозреватель метрик. Откройте обозреватель метрик, выбрав **Метрики** в меню **Azure Monitor**. Подробные сведения об использовании этого средства см. на странице [Начало работы с обозревателем метрик Azure](../../azure-monitor/platform/metrics-getting-started.md). 

В этом примере показано, как просмотреть **транзакции** на уровне учетной записи.

![Снимок экрана осуществления доступа к метрикам на портале Azure](./media/monitor-storage/access-metrics-portal.png)

Для метрик с поддержкой измерений можно выполнить фильтрацию по нужному значению измерения. В этом примере объясняется, как просмотреть **транзакции** на уровне учетной записи для определенной операции, выбрав значения для измерения **Имя API**.

![Снимок экрана осуществления доступа к метрикам с поддержкой измерений на портале Azure](./media/monitor-storage/access-metrics-portal-with-dimension.png)

Полный список измерений, поддерживаемых службой хранилища Azure, см. в разделе [Измерения метрик](monitor-storage-reference.md#metrics-dimensions).

Все метрики для службы хранилища Azure находятся в следующих пространствах имен:

- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/blobServices
- Microsoft.Storage/storageAccounts/fileServices
- Microsoft.Storage/storageAccounts/queueServices
- Microsoft.Storage/storageAccounts/tableServices

Список всех метрик поддержки Azure Monitor, включая службу хранилища Azure, см. в на странице [Поддерживаемые метрики Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported).


### <a name="access-metrics"></a>Доступ к метрикам

> [!TIP]
> Чтобы просмотреть примеры Azure CLI или .NET, выберите соответствующую вкладку ниже.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>Отображение определения метрики

Вы можете отобразить определение метрик учетной записи хранения или отдельной службы хранилища, например службы BLOB-объектов, файлов, таблиц или очередей. Используйте командлет [Get-AzMetricDefinition](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetricdefinition?view=azps-3.3.0).

В этом примере замените заполнитель `<resource-ID>` на ИД ресурса всей учетной записи хранения или ИД ресурса отдельной службы хранилища, например службы BLOB-объектов, файлов, таблиц или очередей. Эти ИД ресурса можно найти на странице **свойств** учетной записи хранения на портале Azure.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="read-metric-values"></a>Считывание значений метрик

Вы можете считать значения метрик на уровне учетной записи хранения или отдельной службы хранилища, например службы BLOB-объектов, файлов, таблиц или очередей. Используйте командлет [Get-AzMetric](https://docs.microsoft.com/powershell/module/Az.Monitor/Get-AzMetric?view=azps-3.3.0).

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>Отображение определения метрик на уровне учетной записи

Вы можете отобразить определение метрик учетной записи хранения или отдельной службы хранилища, например службы BLOB-объектов, файлов, таблиц или очередей. Используйте команду [az monitor metrics list-definitions](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list-definitions).
 
В этом примере замените заполнитель `<resource-ID>` на ИД ресурса всей учетной записи хранения или ИД ресурса отдельной службы хранилища, например службы BLOB-объектов, файлов, таблиц или очередей. Эти ИД ресурса можно найти на странице **свойств** учетной записи хранения на портале Azure.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Считывание значений метрик на уровне учетной записи

Вы можете считать значения метрик учетной записи хранения или отдельной службы хранилища, например службы BLOB-объектов, файлов, таблиц или очередей. Используйте команду [az monitor metrics list](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list).

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/dotnet)

Azure Monitor предоставляет [пакет SDK для .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) для считывания определения и значений метрик. [Пример кода](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) демонстрирует использование пакета SDK с различными параметрами. Необходимо использовать `0.18.0-preview` или более позднюю версию для метрик хранилища.
 
В этих примерах замените заполнитель `<resource-ID>` на ИД ресурса всей учетной записи хранения или ИД ресурса отдельной службы хранилища, например службы BLOB-объектов, файлов, таблиц или очередей. Эти ИД ресурса можно найти на странице **свойств** учетной записи хранения на портале Azure.

Замените переменную `<subscription-ID>` на ИД своей подписки. Инструкции по получению значений для `<tenant-ID>`, `<application-ID>` и `<AccessKey>` см. на странице [Создание приложения Azure Active Directory и субъекта-службы с доступом к ресурсам с помощью портала](https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/). 

#### <a name="list-the-account-level-metric-definition"></a>Отображение определения метрик на уровне учетной записи

В приведенном ниже примере показано, как получить список определений метрик на уровне учетной записи.

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

#### <a name="read-account-level-metric-values"></a>Считывание значений метрик на уровне учетной записи

В приведенном ниже примере показано, как считать данные `UsedCapacity` на уровне учетной записи.

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

#### <a name="read-multidimensional-metric-values"></a>Считывание значений многомерных метрик

Для многомерных метрик необходимо определить фильтры по метаданным, если требуется считать данные метрик для конкретных значений измерений.

В приведенном ниже примере показано, как считать данные в метрике, поддерживающей многомерные значения.

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

## <a name="analyze-log-data"></a>Анализ данных журналов

Доступ к журналам ресурсов можно получить с помощью большого двоичного объекта в учетной записи хранения, данных событий или запросов Log Analytic.

Подробные сведения о полях, которые отображаются в этих журналах, см. на странице со [справочными материалами по данным мониторинга службы хранилища Azure](monitor-storage-reference.md).

> [!NOTE]
> Журналы службы хранилища Azure в Azure Monitor предоставляются в общедоступной предварительной версии. Они также доступны для предварительного тестирования во всех регионах общедоступного облака. Чтобы зарегистрироваться для использования предварительной версии, см. [эту страницу](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u). Эта предварительная версия включает журналы для больших двоичных объектов (в том числе Azure Data Lake Storage 2-го поколения), файлов, очередей, таблиц, учетных записей хранения класса Premium общего назначения версии 1 и учетных записей хранения общего назначения версии 2. Классические учетные записи хранения не поддерживаются.

### <a name="access-logs-in-a-storage-account"></a>Получение доступа к журналам в учетной записи хранения

Журналы отображаются как большие двоичные объекты, хранящиеся в контейнере в целевой учетной записи хранения. Данные собираются и хранятся внутри одного большого двоичного объекта в качестве полезных данных JSON с разделителем-строкой. Имя большого двоичного объекта соответствует следующему соглашению об именовании.

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/blobServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Ниже приведен пример:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="access-logs-in-an-event-hub"></a>Получение доступа к журналам в концентраторе событий

Журналы, отправленные в концентратор событий, не сохраняются в виде файла, однако вы можете проверить, получил ли концентратор событий данные журнала. На портале Azure перейдите в концентратор событий и убедитесь, что количество **входящих сообщений** больше нуля. 

![Журналы аудита](media/monitor-storage/event-hub-log.png)

Вы можете получать доступ к данным журнала, отправляемым в концентратор событий, и считывать их, используя сведения о безопасности, а также средства мониторинга и управления событиями. Дополнительные сведения см. на [этой странице](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub).

### <a name="access-logs-in-a-log-analytics-workspace"></a>Получение доступа к журналам в рабочей области Log Analytics

Доступ к журналам, отправляемым в рабочую область Log Analytics, можно получить с помощью запросов журналов Azure Monitor.

Дополнительные сведения см. в статье [Начало работы с Log Analytics в Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

Данные хранятся в следующих таблицах:

| Таблица | Описание |
|:---|:---|
|StorageBlobLogs | Журналы со сведениями о действиях в хранилище BLOB-объектов. |
|StorageFileLogs | Журналы со сведениями о действиях в общих папках. |
|StorageQueueLogs | Журналы со сведениями о действиях в очередях.|
|StorageTableLogs| Журналы со сведениями о действиях в таблицах.|

### <a name="azure-storage-log-analytics-queries-in-azure-monitor"></a>Запросы Log Analytics службы хранилища Azure в Azure Monitor

Ниже приведены некоторые запросы, которые можно ввести в поле **Поиск по журналам**, чтобы выполнить мониторинг учетных записей хранения Azure. Эти запросы поддерживают [новый язык](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

Используйте следующие запросы для мониторинга учетных записей хранения Azure:

* Для отображения 10 наиболее распространенных ошибок за последние три дня.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by StatusText
    | top 10 by count_ desc
    ```
* Для отображения основных 10 операций, вызвавших наибольшее количество ошибок за последние три дня.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by OperationName
    | top 10 by count_ desc
    ```
* Для отображения основных 10 операций с наибольшей сквозной задержкой за последние три дня.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | top 10 by DurationMs desc
    | project TimeGenerated, OperationName, DurationMs, ServerLatencyMs, ClientLatencyMs = DurationMs - ServerLatencyMs
    ```
* Для отображения всех операций, вызвавших ошибки регулирования на стороне сервера за последние три дня.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText contains "ServerBusy"
    | project TimeGenerated, OperationName, StatusCode, StatusText
    ```
* Для отображения всех запросов с анонимным доступом за последние три дня.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and AuthenticationType == "Anonymous"
    | project TimeGenerated, OperationName, AuthenticationType, Uri
    ```
* Для создания круговой диаграммы операций, используемых за последние три дня.
    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | summarize count() by OperationName
    | sort by count_ desc 
    | render piechart
    ```
## <a name="faq"></a>ВОПРОСЫ И ОТВЕТЫ

**Поддерживает ли служба хранилища Microsoft Azure метрики для управляемых и неуправляемых дисков?**

Нет. Служба "Вычисления Azure" поддерживает метрики на дисках. Дополнительные сведения см. на странице [Дисковые метрики для управляемых и неуправляемых дисков](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/).

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о журналах и метриках, созданных службой хранилища Azure, см. на странице со [справочными материалами по данным мониторинга службы хранилища Azure](monitor-storage-reference.md).
- Подробные сведения о мониторинге ресурсов Azure см. на странице [Мониторинг ресурсов Azure с помощью Azure Monitor](../../azure-monitor/insights/monitor-azure-resource.md).
- Дополнительные сведения о миграции метрик см. на [этой странице](./storage-metrics-migration.md).
