---
title: Мониторинг файлов Azure | Документация Майкрософт
description: Узнайте, как отслеживать производительность и доступность файлов Azure. Мониторинг данных файлов Azure, изучение конфигурации и анализ данных метрик и журналов.
author: normesta
services: storage
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 10/02/2020
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: 4b2f819edd875130c57d487536691b4588dcc71f
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91772674"
---
# <a name="monitoring-azure-files"></a>Мониторинг файлов Azure

При наличии критически важных приложений и бизнес-процессов, использующих ресурсы Azure, необходимо отслеживать эти ресурсы на предмет их доступности, производительности и функционирования. В этой статье описываются данные мониторинга, создаваемые службой файлов Azure, и способы использования функций Azure Monitor для анализа оповещений об этих данных.

> [!NOTE]
> Журналы службы хранилища Azure в Azure Monitor предоставляются в общедоступной предварительной версии. Они также доступны для предварительного тестирования во всех регионах общедоступного облака. Чтобы зарегистрироваться для использования предварительной версии, см. [эту страницу](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u). Эта предварительная версия включает журналы для больших двоичных объектов (в том числе Azure Data Lake Storage 2-го поколения), файлов, очередей и таблиц. Эта функция доступна для всех учетных записей хранения, созданных с помощью модели развертывания Azure Resource Manager. См. раздел [Общие сведения об учетной записи хранения](../common/storage-account-overview.md).

## <a name="monitor-overview"></a>Общие сведения о мониторинге

На странице **Обзор** в портал Azure для каждого ресурса службы файлов Azure содержатся краткие сведения об использовании ресурсов, таких как запросы и почасовая оплата. Эти сведения полезны, но лишь небольшое количество данных мониторинга доступно. Некоторые из этих данных собираются автоматически и доступны для анализа сразу после создания ресурса. Вы можете включить дополнительные типы сбора данных с помощью определенной конфигурации.

## <a name="what-is-azure-monitor"></a>Общие сведения об Azure Monitor
Служба "файлы Azure" создает данные мониторинга с помощью [Azure Monitor](../../azure-monitor/overview.md), которая представляет собой полную службу мониторинга стека в Azure. Azure Monitor предоставляет полный набор функций для мониторинга ресурсов Azure и ресурсов в других облаках, а также в локальной среде. 

Начните с статьи [мониторинг ресурсов Azure с помощью Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource), который описывает следующее:

- Общие сведения об Azure Monitor
- затраты, связанные с мониторингом;
- данные мониторинга, собираемые в Azure;
- настройка сбора данных;
- стандартные средства Azure для анализа данных мониторинга и оповещения.

Следующие разделы посвящены этой статье с помощью описания конкретных данных, собранных из службы файлов Azure. В примерах показано, как настроить сбор данных и анализировать эти данные с помощью средств Azure.

## <a name="monitoring-data"></a>Данные мониторинга

Служба файлов Azure собирает те же данные мониторинга, что и другие ресурсы Azure, которые описаны в разделе [мониторинг данных из ресурсов Azure](../../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). 

Подробные сведения о метриках и журналах, созданных службой файлов Azure, см. в [справочнике по данным мониторинга файлов Azure](storage-files-monitoring-reference.md) .

Метрики и журналы в Azure Monitor поддерживают только учетные записи хранения Azure Resource Manager. Azure Monitor не поддерживает классические учетные записи хранения. Если вы хотите использовать метрики или журналы в классической учетной записи хранения, необходимо выполнить миграцию в учетную запись хранения Azure Resource Manager. См. статью [Поддерживаемый платформой перенос ресурсов IaaS из классической модели в модель Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).

## <a name="collection-and-routing"></a>Сбор и маршрутизация

Метрики платформы и журнал действий собираются автоматически, но их можно направить в другие расположения с помощью параметра диагностики. Необходимо создать параметр диагностики для сбор журналов ресурсов. 

> [!NOTE]
> Журналы службы хранилища Azure в Azure Monitor предоставляются в общедоступной предварительной версии. Они также доступны для предварительного тестирования во всех регионах общедоступного облака. Чтобы зарегистрироваться для использования предварительной версии, см. [эту страницу](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u). Эта предварительная версия включает журналы для больших двоичных объектов (в том числе Azure Data Lake Storage 2-го поколения), файлов, очередей, таблиц, учетных записей хранения класса Premium общего назначения версии 1 и учетных записей хранения общего назначения версии 2. Классические учетные записи хранения не поддерживаются.

Сведения о создании параметра диагностики с помощью портал Azure, Azure CLI или PowerShell см. в статье [Создание параметров диагностики для сбора журналов и метрик платформы в Azure](../../azure-monitor/platform/diagnostic-settings.md). 

Чтобы просмотреть шаблон Azure Resource Manager, который создает параметр диагностики, см. раздел [параметр диагностики для службы хранилища Azure](https://docs.microsoft.com/azure/azure-monitor/samples/resource-manager-diagnostic-settings#diagnostic-setting-for-azure-storage).

При создании параметра диагностики выберите тип хранилища, для которого необходимо включить журналы, например большой двоичный объект, очередь, таблица или файл. Для службы "файлы Azure" выберите **файл**. 

При создании параметра диагностики на портале Azure можно выбрать ресурс из списка. При использовании PowerShell или Azure CLI необходимо использовать идентификатор ресурса конечной точки службы файлов Azure. ИД ресурса можно найти на портале Azure, открыв страницу **свойств** учетной записи хранения.

Также необходимо указать одну из следующих категорий операций, для которых требуется вести журнал. 

| Категория | Описание |
|:---|:---|
| StorageRead | Операции чтения с объектами. |
| StorageWrite | Операции записи в объекты. |
| StorageDelete | Удаление операций с объектами. |

Чтобы получить список регистрируемых операций SMB и RESTFUL, см. раздел [операции с зарегистрированными хранилищами и сообщения о состоянии](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) и [Справочник по данным мониторинга службы файлов Azure](storage-files-monitoring-reference.md).

## <a name="analyzing-metrics"></a>Анализ метрик

Вы можете анализировать метрики для службы хранилища Azure с помощью метрик из других служб Azure, используя обозреватель метрик. Откройте обозреватель метрик, выбрав **Метрики** в меню **Azure Monitor**. Подробные сведения об использовании этого средства см. на странице [Начало работы с обозревателем метрик Azure](../../azure-monitor/platform/metrics-getting-started.md). 

Для метрик с поддержкой измерений можно выполнить фильтрацию по нужному значению измерения.  Полный список измерений, поддерживаемых службой хранилища Azure, см. в разделе [Измерения метрик](storage-files-monitoring-reference.md#metrics-dimensions). Метрики для службы файлов Azure находятся в следующих пространствах имен: 

- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/fileServices

Список всех метрик поддержки Azure Monitor, включая службы файлов Azure, см. в статье [Azure Monitor поддерживаемые метрики](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccountsfileservices).

### <a name="accessing-metrics"></a>Доступ к метрикам

> [!TIP]
> Чтобы просмотреть примеры Azure CLI или .NET, выберите соответствующую вкладку ниже.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>Отображение определения метрики

Вы можете составить список определений метрик вашей учетной записи хранения или службы файлов Azure. Используйте командлет [Get-AzMetricDefinition](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetricdefinition).

В этом примере замените `<resource-ID>` заполнитель идентификатором ресурса всей учетной записи хранения или идентификатором ресурса службы файлов Azure.  Эти ИД ресурса можно найти на странице **свойств** учетной записи хранения на портале Azure.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="reading-metric-values"></a>Считывание значений метрик

Вы можете читать значения метрик на уровне учетной записи хранения или службы файлов Azure. Используйте командлет [Get-AzMetric](https://docs.microsoft.com/powershell/module/Az.Monitor/Get-AzMetric).

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>Отображение определения метрик на уровне учетной записи

Вы можете составить список определений метрик вашей учетной записи хранения или службы файлов Azure. Используйте команду [az monitor metrics list-definitions](https://docs.microsoft.com/cli/azure/monitor/metrics#az-monitor-metrics-list-definitions).
 
В этом примере замените `<resource-ID>` заполнитель идентификатором ресурса всей учетной записи хранения или идентификатором ресурса службы файлов Azure. Эти ИД ресурса можно найти на странице **свойств** учетной записи хранения на портале Azure.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>Считывание значений метрик на уровне учетной записи

Вы можете прочитать значения метрик вашей учетной записи хранения или службы файлов Azure. Используйте команду [az monitor metrics list](https://docs.microsoft.com/cli/azure/monitor/metrics#az-monitor-metrics-list).

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/dotnet)

Azure Monitor предоставляет [пакет SDK для .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) для считывания определения и значений метрик. [Пример кода](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) демонстрирует использование пакета SDK с различными параметрами. Необходимо использовать `0.18.0-preview` или более позднюю версию для метрик хранилища.
 
В этих примерах замените `<resource-ID>` заполнитель идентификатором ресурса всей учетной записи хранения или службой файлов Azure. Эти ИД ресурса можно найти на странице **свойств** учетной записи хранения на портале Azure.

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

#### <a name="reading-account-level-metric-values"></a>Чтение значений метрик уровня учетной записи

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

#### <a name="reading-multidimensional-metric-values"></a>Считывание значений многомерных метрик

Для многомерных метрик необходимо определить фильтры по метаданным, если требуется считать данные метрик для конкретных значений измерений.

В приведенном ниже примере показано, как считать данные в метрике, поддерживающей многомерные значения.

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for Azure Files
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default";
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

## <a name="analyzing-logs"></a>анализ журналов;

Доступ к журналам ресурсов можно получить с помощью большого двоичного объекта в учетной записи хранения, данных событий или запросов Log Analytic.

Чтобы получить список регистрируемых операций SMB и RESTFUL, см. раздел [операции с зарегистрированными хранилищами и сообщения о состоянии](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) и [Справочник по данным мониторинга службы файлов Azure](storage-files-monitoring-reference.md).

> [!NOTE]
> Журналы службы хранилища Azure в Azure Monitor предоставляются в общедоступной предварительной версии. Они также доступны для предварительного тестирования во всех регионах общедоступного облака. Чтобы зарегистрироваться для использования предварительной версии, см. [эту страницу](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u). Эта предварительная версия включает журналы для больших двоичных объектов (в том числе Azure Data Lake Storage 2-го поколения), файлов, очередей, таблиц, учетных записей хранения класса Premium общего назначения версии 1 и учетных записей хранения общего назначения версии 2. Классические учетные записи хранения не поддерживаются.

Записи журнала создаются только при получении запроса к конечной точке службы. Например, если учетная запись хранения имеет действие в конечной точке файла, но не находится в конечных точках таблицы или очереди, то создаются только те журналы, которые относятся к файловой службе. Журналы службы хранилища Azure содержат подробные сведения об успешных и неудачных запросах к службе хранилища. Эта информация может использоваться для мониторинга отдельных запросов и диагностики неполадок в службе хранилища. Запросы вносятся в журнал наилучшим возможным образом.

### <a name="log-authenticated-requests"></a>Ведение журналов запросов, прошедших аутентификацию

 Регистрируются запросы, прошедшие аутентификацию, следующих типов:

- Успешные запросы.
- Неудачные запросы, в том числе из-за ошибок, связанных с временем ожидания, регулированием, сетью, авторизацией и др.
- Запросы, в которых используется подписанный URL-адрес (SAS) или OAuth, в том числе неудачные и успешные запросы.
- Запросы к данным аналитики (классические данные журнала в контейнере **$logs** и данные метрик класса в таблицах **$metric**).

Запросы, выполняемые самой службой файлов Azure, например создание или удаление журнала, не регистрируются. Полный список заносимых запросов SMB и RESTFUL см. в разделе операции с [зарегистрированным хранилищем и сообщения о состоянии](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) и [Справочник по данным мониторинга службы файлов Azure](storage-files-monitoring-reference.md).

### <a name="log-anonymous-requests"></a>Ведение журналов анонимных запросов

 Регистрируются анонимные запросы следующих типов:

- Успешные запросы.
- Ошибки сервера.
- Ошибки времени ожидания для клиента и сервера.
- Неудачные запросы GET с кодом ошибки 304 (не изменено).

Остальные неудачные анонимные запросы не регистрируются. Полный список заносимых запросов SMB и RESTFUL см. в разделе операции с [зарегистрированным хранилищем и сообщения о состоянии](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) и [Справочник по данным мониторинга службы файлов Azure](storage-files-monitoring-reference.md).

### <a name="accessing-logs-in-a-storage-account"></a>Доступ к журналам в учетной записи хранения

Журналы отображаются как большие двоичные объекты, хранящиеся в контейнере в целевой учетной записи хранения. Данные собираются и хранятся внутри одного большого двоичного объекта в качестве полезных данных JSON с разделителем-строкой. Имя большого двоичного объекта соответствует следующему соглашению об именовании.

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/fileServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

Ниже приведен пример:

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/fileServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="accessing-logs-in-an-event-hub"></a>Доступ к журналам в концентраторе событий

Журналы, отправленные в концентратор событий, не сохраняются в виде файла, однако вы можете проверить, получил ли концентратор событий данные журнала. На портале Azure перейдите в концентратор событий и убедитесь, что количество **входящих сообщений** больше нуля. 

![Журналы аудита](media/storage-files-monitoring/event-hub-log.png)

Вы можете получать доступ к данным журнала, отправляемым в концентратор событий, и считывать их, используя сведения о безопасности, а также средства мониторинга и управления событиями. Дополнительные сведения см. на [этой странице](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub).

### <a name="accessing-logs-in-a-log-analytics-workspace"></a>Доступ к журналам в рабочей области Log Analytics

Доступ к журналам, отправляемым в рабочую область Log Analytics, можно получить с помощью запросов журналов Azure Monitor. Данные хранятся в таблице **сторажефилелогс** . 

Дополнительные сведения см. в статье [Начало работы с Log Analytics в Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

#### <a name="sample-kusto-queries"></a>Примеры запросов Kusto

Ниже приведены некоторые запросы, которые можно ввести на панели **поиска журналов** , чтобы упростить мониторинг файлов Azure. Эти запросы поддерживают [новый язык](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

> [!IMPORTANT]
> При выборе **журналов** в меню группы ресурсов учетной записи хранения log Analytics открывается с областью запроса, заданной для текущей группы ресурсов. Это означает, что запросы журнала будут содержать только данные из этой группы ресурсов. Если требуется выполнить запрос, который включает данные из других ресурсов или данных из других служб Azure, выберите **журналы** в меню **Azure Monitor** . Подробные сведения см. в статье [Область запросов журнала и временной диапазон в Azure Monitor Log Analytics](/azure/azure-monitor/log-query/scope/).

Используйте эти запросы для мониторинга файловых ресурсов Azure:

- Просмотр ошибок SMB за последнюю неделю

```Kusto
StorageFileLogs
| where Protocol == "SMB" and TimeGenerated >= ago(7d) and StatusCode contains "-"
| sort by StatusCode
```
- Создание круговой диаграммы операций SMB за последнюю неделю

```Kusto
StorageFileLogs
| where Protocol == "SMB" and TimeGenerated >= ago(7d) 
| summarize count() by OperationName
| sort by count_ desc
| render piechart
```

- Просмотр ошибок RESTFUL за последнюю неделю

```Kusto
StorageFileLogs
| where Protocol == "HTTPS" and TimeGenerated >= ago(7d) and StatusText !contains "Success"
| sort by StatusText asc
```

- Создание круговой диаграммы операций RESTFUL за последнюю неделю

```Kusto
StorageFileLogs
| where Protocol == "HTTPS" and TimeGenerated >= ago(7d) 
| summarize count() by OperationName
| sort by count_ desc
| render piechart
```

Список имен и описаний столбцов для службы файлов Azure см. в разделе [сторажефилелогс](https://docs.microsoft.com/azure/azure-monitor/reference/tables/storagefilelogs).

Дополнительные сведения о написании запросов см. в разделе [учебник. Начало работы с log Analytics запросами](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

## <a name="alerts"></a>видны узлы

Azure Monitor оповещения заблаговременно уведомляют Вас при обнаружении важных условий в данных мониторинга. Они позволяют выявление и устранение проблем в системе до того, как ваши клиенты заметят их. Вы можете настроить оповещения для [метрик](/azure/azure-monitor/platform/alerts-metric-overview), [журналов](/azure/azure-monitor/platform/alerts-unified-log)и [журнала действий](/azure/azure-monitor/platform/activity-log-alerts). 

В следующей таблице перечислены некоторые примеры сценариев для мониторинга и правильная метрика, используемая для оповещения.

| Сценарий | Метрика, используемая для оповещения |
|-|-|
| Файловый ресурс регулируется. | Метрика: транзакции<br>Имя измерения: тип ответа <br>Имя измерения: общая папка (только для файлового ресурса Premium) |
| Размер общей папки составляет 80% емкости. | Метрика: емкость файла<br>Имя измерения: общая папка (только для файлового ресурса Premium) |
| Исходящий файловый ресурс превысил 500 гиб в один день. | Метрика: исходящий трафик<br>Имя измерения: общая папка (только для файлового ресурса Premium) |

### <a name="how-to-create-alerts-for-azure-files"></a>Создание оповещений для службы файлов Azure

1. Перейдите к своей **учетной записи хранения** в **портал Azure**. 

2. Щелкните **оповещения** , а затем — **+ новое правило генерации оповещений**.

3. Щелкните **изменить ресурс**, выберите **тип файлового ресурса** и нажмите кнопку **Готово**. 

4. Щелкните **Выбрать условие** и укажите следующие сведения для оповещения. 

    - **Метрика**
    - **Имя измерения**
    - **Логика оповещений**

5. Щелкните **выбрать группу действий** и добавьте группу действий (электронная почта, SMS и т. д.) в предупреждение либо выбрав существующую группу действий, либо создав новую группу действий.

6. Введите сведения о **предупреждении** , такие как имя, **Описание**и **серьезность** **правила оповещения**.

7. Щелкните **создать правило генерации оповещений** , чтобы создать оповещение.

> [!NOTE]  
> Если вы создаете предупреждение и оно слишком шумным, измените пороговое значение и логику оповещений.

### <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>Создание оповещения в случае регулирования общей папки

1. Перейдите к своей **учетной записи хранения** в **портал Azure**.
2. В разделе **мониторинг** щелкните **оповещения**, а затем — **+ новое правило генерации оповещений**.
3. Щелкните **изменить ресурс**, выберите **тип файлового ресурса** для учетной записи хранения и нажмите кнопку **Готово**. Например, если имя учетной записи хранения — `contoso` , выберите `contoso/file` ресурс.
4. Нажмите кнопку **Выбрать условие** , чтобы добавить условие.
5. Вы увидите список сигналов, поддерживаемых для учетной записи хранения, и выберите метрику **транзакции** .
6. В колонке **Настройка логики сигнала** щелкните раскрывающийся список **имя измерения** и выберите **тип ответа**.
7. Щелкните раскрывающийся список **значения измерения** и выберите **СУКЦЕССВИССРОТТЛИНГ** (для SMB) или **ClientThrottlingError** (для остальных).

   > [!NOTE]
   > Если значение измерения Сукцессвиссроттлинг или ClientThrottlingError отсутствует в списке, это означает, что ресурс не был отрегулирован. Чтобы добавить значение измерения, щелкните **Добавить пользовательское значение** рядом с раскрывающимся списком **значения измерений** , **введите сукцессвиссроттлинг** или **ClientThrottlingError**, нажмите кнопку **ОК** , а затем повторите шаг #7.

8. Щелкните раскрывающийся список **имя измерения** и выберите **файловый ресурс**.
9. Щелкните раскрывающийся список **значения измерений** и выберите Общие папки, по которым необходимо создать оповещение.

   > [!NOTE]
   > Если общий файловый ресурс является стандартным общим ресурсом, выберите **все текущие и будущие значения**. В раскрывающемся списке значения измерений не будут перечислены общие папки, так как метрики для общего ресурса недоступны для стандартных файловых ресурсов. Предупреждения регулирования для стандартных файловых ресурсов будут активированы, если какая-либо общая папка в учетной записи хранения регулируется, а предупреждение не определит, какой файловый ресурс был отрегулирован. Так как метрики для общего ресурса недоступны для стандартных файловых ресурсов, рекомендуется использовать одну общую папку для каждой учетной записи хранения.

10. Определите **Параметры оповещения** (пороговое значение, оператор, гранулярность агрегирования и частоту оценки) и нажмите кнопку **Готово**.

    > [!TIP]
    > При использовании статического порога диаграмма метрик может помочь определить разумное пороговое значение, если общая папка в данный момент регулируется. Если используется динамическое пороговое значение, на диаграмме метрики будут отображаться рассчитанные пороговые значения на основе последних данных.

11. Щелкните **выбрать группу действий** , чтобы добавить **группу действий** (электронную почту, SMS и т. д.) в предупреждение, выбрав существующую группу действий или создав новую группу действий.
12. Заполните **сведения о предупреждении** , такие как **имя правила оповещения**, * * описание и **серьезность**.
13. Щелкните **создать правило генерации оповещений** , чтобы создать оповещение.

### <a name="how-to-create-an-alert-if-the-azure-file-share-size-is-80-of-capacity"></a>Как создать оповещение, если размер общей папки Azure составляет 80% емкости

1. Перейдите к своей **учетной записи хранения** в **портал Azure**.
2. В разделе **мониторинг** щелкните **оповещения** , а затем — **+ новое правило генерации оповещений**.
3. Щелкните **изменить ресурс**, выберите **тип файлового ресурса** для учетной записи хранения и нажмите кнопку **Готово**. Например, если имя учетной записи хранения — `contoso` , выберите `contoso/file` ресурс.
4. Нажмите кнопку **Выбрать условие** , чтобы добавить условие.
5. Вы увидите список сигналов, поддерживаемых для учетной записи хранения, и выберите метрику **емкости файлов** .
6. В колонке **Настройка логики сигнала** щелкните раскрывающийся список **имя измерения** и выберите **файловый ресурс**.
7. Щелкните раскрывающийся список **значения измерений** и выберите Общие папки, по которым необходимо создать оповещение.

   > [!NOTE]
   > Если общий файловый ресурс является стандартным общим ресурсом, выберите **все текущие и будущие значения**. В раскрывающемся списке значения измерений не будут перечислены общие папки, так как метрики для общего ресурса недоступны для стандартных файловых ресурсов. Оповещения для стандартных файловых ресурсов основаны на всех общих файловых ресурсах в учетной записи хранения. Так как метрики для общего ресурса недоступны для стандартных файловых ресурсов, рекомендуется использовать одну общую папку для каждой учетной записи хранения.

8. Введите **пороговое значение** в байтах. Например, если файловый ресурс имеет размер 100 Тиб и вы хотите получать предупреждение, если размер общей папки составляет 80% емкости, то пороговое значение в байтах — 87960930222080.
9. Определите остальные **Параметры оповещения** (гранулярность статистической обработки и частоту оценки) и нажмите кнопку **Готово**.
10. Щелкните выбрать группу действий, чтобы добавить группу действий (электронную почту, SMS и т. д.) в предупреждение, выбрав существующую группу действий или создав новую группу действий.
11. Заполните **сведения о предупреждении** , такие как **имя правила оповещения**, * * описание и **серьезность**.
12. Щелкните **создать правило генерации оповещений** , чтобы создать оповещение.

### <a name="how-to-create-an-alert-if-the-azure-file-share-egress-has-exceeded-500-gib-in-a-day"></a>Как создать оповещение, если исходящий файловый ресурс Azure превысил 500 гиб в день

1. Перейдите к своей **учетной записи хранения** в **портал Azure**.
2. В разделе Мониторинг щелкните **оповещения** , а затем — **+ новое правило генерации оповещений**.
3. Щелкните **изменить ресурс**, выберите **тип файлового ресурса** для учетной записи хранения и нажмите кнопку **Готово**. Например, если имя учетной записи хранения — Contoso, выберите ресурс Contoso/File.
4. Нажмите кнопку **Выбрать условие** , чтобы добавить условие.
5. Вы увидите список сигналов, поддерживаемых для учетной записи хранения, и выберите метрику исходящего **трафика** .
6. В колонке **Настройка логики сигнала** щелкните раскрывающийся список **имя измерения** и выберите **файловый ресурс**.
7. Щелкните раскрывающийся список **значения измерений** и выберите Общие папки, по которым необходимо создать оповещение.

   > [!NOTE]
   > Если общий файловый ресурс является стандартным общим ресурсом, выберите **все текущие и будущие значения**. В раскрывающемся списке значения измерений не будут перечислены общие папки, так как метрики для общего ресурса недоступны для стандартных файловых ресурсов. Оповещения для стандартных файловых ресурсов основаны на всех общих файловых ресурсах в учетной записи хранения. Так как метрики для общего ресурса недоступны для стандартных файловых ресурсов, рекомендуется использовать одну общую папку для каждой учетной записи хранения.

8. Введите значение порога **536870912000** байт. 
9. Щелкните раскрывающийся список **детализации статистической обработки** и выберите **24 часа**.
10. Выберите **частоту оценки** и **нажмите кнопку Готово**.
11. Щелкните **выбрать группу действий** , чтобы добавить **группу действий** (электронную почту, SMS и т. д.) в предупреждение, выбрав существующую группу действий или создав новую группу действий.
12. Заполните **сведения о предупреждении** , такие как **имя правила оповещения**, * * описание и **серьезность**.
13. Щелкните **создать правило генерации оповещений** , чтобы создать оповещение.

## <a name="next-steps"></a>Дальнейшие действия

- [Справочник по данным мониторинга файлов Azure](storage-files-monitoring.md)
- [Мониторинг ресурсов Azure с помощью Azure Monitor](../../azure-monitor/insights/monitor-azure-resource.md)
- [Миграция метрик службы хранилища Azure](../common/storage-metrics-migration.md)
- [Планирование развертывания службы файлов Azure](https://docs.microsoft.com/azure/storage/files/storage-files-planning)
- [Как развернуть службу файлов Azure](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)
- [Устранение неполадок в работе службы "Файлы Azure" в Windows](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems)
- [Устранение неполадок в работе службы "Файлы Azure" в Linux](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-linux-file-connection-problems)
