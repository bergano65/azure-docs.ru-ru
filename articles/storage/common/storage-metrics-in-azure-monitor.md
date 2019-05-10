---
title: Метрики службы хранилища Azure в Azure Monitor | Документация Майкрософт
description: Узнайте о новых метриках, которые предлагает Azure Monitor.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 09/05/2017
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 8b091ecce98a626f18fe6547445d898b6710e1a5
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510546"
---
# <a name="azure-storage-metrics-in-azure-monitor"></a>Метрики службы хранилища Azure в Azure Monitor

С помощью метрик службы хранилища Azure вы можете анализировать тенденции использования, выполнять трассировку запросов и диагностику проблем учетной записи хранения.

Azure Monitor предоставляет унифицированный пользовательский интерфейс для мониторинга различных служб Azure. Дополнительные сведения см. в статье [Обзор мониторинга в Microsoft Azure](../../monitoring-and-diagnostics/monitoring-overview.md). Интеграция службы хранилища Azure с Azure Monitor осуществляется путем отправки данных метрик на платформу Azure Monitor.

## <a name="access-metrics"></a>Доступ к метрикам

Azure Monitor предоставляет несколько способов доступа к метрикам. Можно получить доступ к их из [портала Azure](https://portal.azure.com), интерфейсы API Azure Monitor (REST и .NET) и решения для анализа таких как концентраторы событий. Дополнительные сведения см. в статье [Обзор метрик в Microsoft Azure](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Метрики включены по умолчанию, и вы можете получить доступ к данным за последние 93 дня. Если необходимо хранить данные метрик в течение длительного периода времени, вы можете архивировать их в учетную запись хранения Azure. Для настройки используйте [параметры диагностики](../../azure-monitor/platform/diagnostic-logs-overview.md) в Azure Monitor.

### <a name="access-metrics-in-the-azure-portal"></a>Доступ к метрикам на портале Azure

На портале Azure можно отслеживать метрики в течение продолжительного периода времени. В примере ниже показано, как просмотреть **транзакции** на уровне учетной записи.

![снимок экрана доступа к метрикам на портале Azure](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal.png)

Для метрик с поддержкой измерений необходимо выполнить фильтрацию по нужному значению измерения. В примере ниже объясняется, как просмотреть **транзакции** на уровне учетной записи для определенной операции, выбрав значения для измерения **Имя API**.

![снимок экрана доступа к метрикам с поддержкой измерений на портале Azure](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal-with-dimension.png)

### <a name="access-metrics-with-the-rest-api"></a>Доступ к метрикам через REST API

Azure Monitor предоставляет [интерфейсы REST API](/rest/api/monitor/) для считывания определения и значений метрик. В этом разделе рассматривается, как считывать метрики хранилища. Идентификатор ресурса используется во всех интерфейсах REST API. Дополнительные сведения см. в статье, посвященной обзору идентификатора ресурса для служб в службе хранилища.

В примере ниже рассматривается, как использовать [ArmClient](https://github.com/projectkudu/ARMClient) в командной строке, чтобы упростить тестирование с помощью REST API.

#### <a name="list-account-level-metric-definition-with-the-rest-api"></a>Получение списка определений метрик на уровне учетной записи с помощью REST API

В примере ниже показано, как получить список определений метрик на уровне учетной записи.

```
# Login to Azure and enter your credentials when prompted.
> armclient login

> armclient GET /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions?api-version=2018-01-01

```

Если вы хотите получить список определений метрик для большого двоичного объекта, таблицы, файла или очереди, вам необходимо указать разные идентификаторы ресурсов для каждой службы с помощью REST API.

Ответ содержит определение метрики в формате JSON:

```Json
{
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions/UsedCapacity",
      "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}",
      "category": "Capacity",
      "name": {
        "value": "UsedCapacity",
        "localizedValue": "Used capacity"
      },
      "isDimensionRequired": false,
      "unit": "Bytes",
      "primaryAggregationType": "Average",
      "metricAvailabilities": [
        {
          "timeGrain": "PT1M",
          "retention": "P30D"
        },
        {
          "timeGrain": "PT1H",
          "retention": "P30D"
        }
      ]
    },
    ... next metric definition
  ]
}

```

#### <a name="read-account-level-metric-values-with-the-rest-api"></a>Считывание значений метрик на уровне учетной записи с помощью REST API

В примере ниже показано, как считать данные метрики на уровне учетной записи.

```
> armclient GET "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metrics?metricnames=Availability&api-version=2018-01-01&aggregation=Average&interval=PT1H"

```

Если вы хотите считать значения метрик для большого двоичного объекта, таблицы, файла или очереди, вам необходимо указать разные идентификаторы ресурсов для каждой службы с помощью REST API (см. пример выше).

Ответ ниже содержит значения метрик в формате JSON:

```Json
{
  "cost": 0,
  "timespan": "2017-09-07T17:27:41Z/2017-09-07T18:27:41Z",
  "interval": "PT1H",
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/Microsoft.Insights/metrics/Availability",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Availability",
        "localizedValue": "Availability"
      },
      "unit": "Percent",
      "timeseries": [
        {
          "metadatavalues": [],
          "data": [
            {
              "timeStamp": "2017-09-07T17:27:00Z",
              "average": 100.0
            }
          ]
        }
      ]
    }
  ]
}

```

### <a name="access-metrics-with-the-net-sdk"></a>Доступ к метрикам с помощью пакета SDK для .NET

Azure Monitor предоставляет [пакета SDK для .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) для считывания определения и значений метрик. [Пример кода](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) демонстрирует использование пакета SDK с различными параметрами. Необходимо использовать `0.18.0-preview` или более поздней версии для метрик хранилища. Идентификатор ресурса используется в пакете SDK для .NET. Дополнительные сведения см. в статье, посвященной обзору идентификатора ресурса для служб в службе хранилища.

В следующем примере показано, как использовать пакет SDK для .NET в Azure Monitor для считывания метрик хранилища.

#### <a name="list-account-level-metric-definition-with-the-net-sdk"></a>Получение списка определений метрик уровня учетной записи с помощью пакета SDK для .NET

В примере ниже показано, как получить список определений метрик на уровне учетной записи.

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        // Using metrics in Azure Monitor is currently free. However, if you use additional solutions ingesting metrics data, you may be billed by these solutions. For example, you are billed by Azure Storage if you archive metrics data to an Azure Storage account. Or you are billed by Operation Management Suite (OMS) if you stream metrics data to OMS for advanced analysis.
        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            //Enumrate metric definition:
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

Если вы хотите получить список определений метрик для большого двоичного объекта, таблицы, файла или очереди, вам необходимо указать разные идентификаторы ресурсов для каждой службы с помощью REST API.

#### <a name="read-metric-values-with-the-net-sdk"></a>Считывание значений метрик с помощью пакета SDK для .NET

В примере ниже показано, как считать данные `UsedCapacity` на уровне учетной записи.

```csharp
    public static async Task ReadStorageMetricValue()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

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

Если вы хотите считать значения метрик для большого двоичного объекта, таблицы, файла или очереди, вам необходимо указать разные идентификаторы ресурсов для каждой службы с помощью REST API (см. пример выше).

#### <a name="read-multi-dimensional-metric-values-with-the-net-sdk"></a>Считывание значений многомерных метрик с помощью пакета SDK для .NET

Для многомерных метрик необходимо определить фильтр по метаданным, если требуется считать данные метрик для конкретного значения измерения.

В примере ниже показано, как считать данные метрики в метрике, поддерживающей многомерные значения.

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

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

## <a name="understanding-resource-id-for-services-in-azure-storage"></a>Обзор идентификатора ресурса для служб в службе хранилища Azure

Идентификатор ресурса представляет собой уникальный идентификатор ресурса в Azure. При использовании REST API Azure Monitor для считывания определений метрик или их значений необходимо использовать идентификатор ресурса для ресурса, который вы планируете использовать. Шаблон идентификатора ресурса имеет следующий формат:

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

С помощью Azure Monitor хранилище предоставляет метрики на уровне учетной записи хранения и уровне службы. Например, вы можете извлечь метрики только хранилища BLOB-объектов. Каждый уровень имеет свой собственный идентификатор ресурса, который используется для получения метрик только этого уровня.

### <a name="resource-id-for-a-storage-account"></a>Идентификатор ресурса для учетной записи хранения

Ниже рассматривается формат указания идентификатора ресурса для учетной записи хранения.

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}
```

### <a name="resource-id-for-the-storage-services"></a>Идентификатор ресурса для служб хранилища

Ниже рассматривается формат указания идентификатора ресурса для каждой службы хранилища.

* Идентификатор ресурса службы BLOB-объектов
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default
```
* Идентификатор ресурса службы таблиц
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/tableServices/default
```
* Идентификатор ресурса службы очередей
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/queueServices/default
```
* Идентификатор ресурса службы файлов
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default
```

### <a name="resource-id-in-azure-monitor-rest-api"></a>Идентификатор ресурса в REST API Azure Monitor

Ниже приведен шаблон, используемый при вызове REST API Azure Monitor.

```
GET {resourceId}/providers/microsoft.insights/metrics?{parameters}
```

## <a name="capacity-metrics"></a>Метрики емкости
Значения метрик емкости отправляются в Azure Monitor каждый час. Значения обновляются каждый день. Интервал времени определяет интервал, за который представлены значения метрик. Поддерживаемый интервал времени для всех метрик емкости — один час (PT1H).

Служба хранилища Azure предоставляет следующие метрики емкости в Azure Monitor.

### <a name="account-level"></a>На уровне учетной записи

| Имя метрики | Описание |
| ------------------- | ----------------- |
| UsedCapacity | Объем хранилища, используемый учетной записью хранения. Для стандартных учетных записей хранения это общая емкость, используемая большим двоичным объектом, таблицей, файлом или очередью. Для учетных записей хранения уровня "Премиум" и учетных записей хранения больших двоичных объектов эта емкость сопоставима с емкостью метрики BlobCapacity. <br/><br/> Единица измерения. Байт <br/> Тип агрегирования. Средние <br/> Пример значения. 1024 |

### <a name="blob-storage"></a>Хранилище BLOB-объектов

| Имя метрики | Описание |
| ------------------- | ----------------- |
| BlobCapacity | Объем хранилища BLOB-объектов, используемый в учетной записи хранения. <br/><br/> Единица измерения. Байт <br/> Тип агрегирования. Средние <br/> Пример значения. 1024 <br/> Размеры: **BlobType**, и **BlobTier** ([определение](#metrics-dimensions)) |
| BlobCount    | Количество больших двоичных объектов, хранящихся в учетной записи хранения. <br/><br/> Единица измерения. Количество <br/> Тип агрегирования. Средние <br/> Пример значения. 1024 <br/> Размеры: **BlobType**, и **BlobTier** ([определение](#metrics-dimensions)) |
| ContainerCount    | Число контейнеров в учетной записи хранения. <br/><br/> Единица измерения. Количество <br/> Тип агрегирования. Средние <br/> Пример значения. 1024 |
| IndexCapacity     | Объем хранилища, используемый для индекса ADLS 2-го поколения (иерархического). <br/><br/> Единица измерения: Байт <br/> Тип агрегирования. Средние <br/> Пример значения. 1024 |

### <a name="table-storage"></a>Хранилище таблиц

| Имя метрики | Описание |
| ------------------- | ----------------- |
| TableCapacity | Объем хранилища таблиц, используемый учетной записью хранения. <br/><br/> Единица измерения. Байт <br/> Тип агрегирования. Средние <br/> Пример значения. 1024 |
| TableCount   | Количество таблиц в учетной записи хранения. <br/><br/> Единица измерения. Количество <br/> Тип агрегирования. Средние <br/> Пример значения. 1024 |
| TableEntityCount | Количество сущностей таблицы в учетной записи хранения. <br/><br/> Единица измерения. Количество <br/> Тип агрегирования. Средние <br/> Пример значения. 1024 |

### <a name="queue-storage"></a>Хранилище очередей

| Имя метрики | Описание |
| ------------------- | ----------------- |
| QueueCapacity | Объем хранилища очередей, используемый учетной записью хранения. <br/><br/> Единица измерения. Байт <br/> Тип агрегирования. Средние <br/> Пример значения. 1024 |
| QueueCount   | Количество очередей в учетной записи хранения. <br/><br/> Единица измерения. Количество <br/> Тип агрегирования. Средние <br/> Пример значения. 1024 |
| QueueMessageCount | Количество действительных сообщений очереди в учетной записи хранения. <br/><br/>Единица измерения. Количество <br/> Тип агрегирования. Средние <br/> Пример значения. 1024 |

### <a name="file-storage"></a>Хранилище файлов

| Имя метрики | Описание |
| ------------------- | ----------------- |
| FileCapacity | Объем хранилища файлов, используемый учетной записью хранения. <br/><br/> Единица измерения. Байт <br/> Тип агрегирования. Средние <br/> Пример значения. 1024 |
| FileCount   | Количество файлов в учетной записи хранения. <br/><br/> Единица измерения. Количество <br/> Тип агрегирования. Средние <br/> Пример значения. 1024 |
| FileShareCount | Количество общих папок в учетной записи хранения. <br/><br/> Единица измерения. Количество <br/> Тип агрегирования. Средние <br/> Пример значения. 1024 |

## <a name="transaction-metrics"></a>Метрики транзакций

Метрики транзакций создаются при каждом запросе к учетной записи хранения, отправляемом из службы хранилища Azure в Azure Monitor. Если учетная запись хранения неактивна, данные на основе метрик транзакций за этот период будут отсутствовать. Все метрики транзакций доступны на уровне учетной записи и службы (хранилище BLOB-объектов, хранилище таблиц, файлы Azure и хранилище очередей). Интервал времени определяет интервал, за который представлены значения метрик. Поддерживаемые интервалы времени для всех метрик транзакций — PT1H и PT1M.

Служба хранилища Azure предоставляет следующие метрики транзакций в Azure Monitor.

| Имя метрики | Описание |
| ------------------- | ----------------- |
| Транзакции | Количество запросов к службе хранения или указанной операции API. Это число включает успешные и неудачные запросы, а также запросы, вызвавшие ошибку. <br/><br/> Единица измерения. Количество <br/> Тип агрегирования: Всего <br/> Применимые измерения. ResponseType, GeoType, ApiName и Authentication ([определение](#metrics-dimensions))<br/> Пример значения. 1024 |
| Входящие | Объем входящих данных. Это количество включает входящий трафик из внешнего клиента в службу хранилища Azure, а также входящий трафик в пределах Azure. <br/><br/> Единица измерения. Байт <br/> Тип агрегирования: Всего <br/> Применимые измерения. GeoType, ApiName и Authentication ([определение](#metrics-dimensions)) <br/> Пример значения. 1024 |
| Исходящие | Объем исходящих данных. Это количество включает исходящий трафик из внешнего клиента в службу хранилища Azure, а также исходящий трафик в пределах Azure. В результате это количество не отражает исходящий трафик для тарификации. <br/><br/> Единица измерения. Байт <br/> Тип агрегирования: Всего <br/> Применимые измерения. GeoType, ApiName и Authentication ([определение](#metrics-dimensions)) <br/> Пример значения. 1024 |
| SuccessServerLatency | Среднее время, используемое для обработки успешного запроса службы хранилища Azure. Это значение не включает в себя сетевую задержку, указанную в метрике SuccessE2ELatency. <br/><br/> Единица измерения. мс <br/> Тип агрегирования. Средние <br/> Применимые измерения. GeoType, ApiName и Authentication ([определение](#metrics-dimensions)) <br/> Пример значения. 1024 |
| SuccessE2ELatency | Средняя совокупная задержка успешных запросов к службе хранения или указанной операции API. Это значение включает необходимое время обработки в пределах службы хранилища Azure для считывания запроса, отправки ответа и получения подтверждения ответа. <br/><br/> Единица измерения. мс <br/> Тип агрегирования. Средние <br/> Применимые измерения. GeoType, ApiName и Authentication ([определение](#metrics-dimensions)) <br/> Пример значения. 1024 |
| Доступность | Процент доступности службы хранения или указанной операции API. Доступность рассчитывается следующим образом: общее значение запросов для тарификации делится на количество применимых запросов, включая запросы, вызвавшие непредвиденные ошибки. Все непредвиденные ошибки приводят к снижению уровня доступности службы хранения или указанной операции API. <br/><br/> Единица измерения. Процент <br/> Тип агрегирования. Средние <br/> Применимые измерения. GeoType, ApiName и Authentication ([определение](#metrics-dimensions)) <br/> Пример значения. 99,99 |

## <a name="metrics-dimensions"></a>Измерения метрик

Служба хранилища Azure поддерживает следующие измерения метрик в Azure Monitor.

| Имя измерения | Описание |
| ------------------- | ----------------- |
| **BlobType** | Тип большого двоичного объекта только для метрик больших двоичных объектов. Поддерживаемые значения: **BlockBlob**, **PageBlob**, и **хранилище Azure Data Lake**. Расширенный большой двоичный объект включен в BlockBlob. |
| **BlobTier** | Хранилище Azure предоставляет доступ различных уровней, которые позволяют хранить данные объекта BLOB-объектов в наиболее экономичного. См. в разделе, см. в [уровень BLOB-объектов хранилища Azure](../blobs/storage-blob-storage-tiers.md). Поддерживаемые значения: <br/> <li>**"Горячий"**: "Горячий" уровень</li> <li>**"Холодный"**: "Холодном" уровне</li> <li>**Архивирование**. Архивный уровень</li> <li>**"Премиум"**: Уровень "премиум" для блочных BLOB-объектов</li> <li>**P4/P6/P10/P15/P20 ИЛИ P30/P40 ИЛИ P50/P60**: Типы уровня для уровня "премиум" страничный BLOB-объект</li> <li>**Стандартный**. Тип уровня стандартный страничный BLOB-объектов</li> <li>**Untiered**: Тип уровня для учетной записи хранения общего назначения версии 1</li> |
| **GeoType** | Транзакции из основного или дополнительного кластера. Допустимые значения: **основной** и **вторичного**. Применимо к геоизбыточному хранилищу с доступом только для чтения (RA-GRS) при считывании объектов из дополнительного клиента. |
| **ResponseType** | Тип ответа транзакции. Доступные значения: <br/><br/> <li>**ServerOtherError**. Все остальные ошибки на стороне сервера, за исключением описанных </li> <li>**ServerBusyError**. Запрос с проверкой подлинности, который вернул код состояния HTTP 503. </li> <li>**ServerTimeoutError**. Запрос с проверкой подлинности (время ожидания которого истекло), который вернул код состояния HTTP 500. Время ожидания истекло из-за ошибки на стороне сервера. </li> <li>**AuthorizationError**. Запрос с проверкой подлинности, завершившийся сбоем из-за несанкционированного доступа к данным или сбоя авторизации. </li> <li>**NetworkError**. Запрос с проверкой подлинности, завершившийся сбоем из-за ошибок сети. Чаще всего происходит, когда клиент преждевременно закрывает подключение до истечения времени ожидания. </li> <li>**ClientThrottlingError**. Ошибка регулирования на стороне клиента. </li> <li>**ClientTimeoutError**. Запрос с проверкой подлинности (время ожидания которого истекло), который вернул код состояния HTTP 500. Если для времени ожидания сети клиента или времени ожидания запроса задано меньшее значение, чем требовалось для службы хранения, это предусмотренное время ожидания. В противном случае оно будет отмечено как ServerTimeoutError. </li> <li>**ClientOtherError**. Все остальные ошибки на стороне клиента, за исключением описанных. </li> <li>**Выполнено**. Выполненный запрос</li> <li> **SuccessWithThrottling**: Успешный запрос при SMB-клиент получает регулироваться в случае с результатами первой, но завершается успешно после нескольких попыток.</li> |
| **ApiName** | Имя операции. Например: <br/> <li>**CreateContainer**</li> <li>**DeleteBlob**</li> <li>**GetBlob**</li> Для всех имен операций. Дополнительные сведения см. в этой [статье](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
| **Проверка подлинности** | Тип аутентификации, используемой в транзакциях. Доступные значения: <br/> <li>**AccountKey**: Транзакция проходит аутентификацию с помощью ключа учетной записи хранения.</li> <li>**SAS**: Транзакция проходит аутентификацию с помощью подписанных URL-адресов.</li> <li>**OAuth**: Транзакция проходит аутентификацию с помощью маркеров доступа OAuth.</li> <li>**Анонимные**: Транзакция запрашивается анонимно. Сюда не входят предварительные запросы.</li> <li>**AnonymousPreflight**: Транзакция является предварительным запросом.</li> |

Для метрик с поддержкой измерений необходимо указать значение измерения, чтобы увидеть соответствующие значения метрик. Например, если вы хотите просмотреть значения **Transactions** для поиска успешных ответов, необходимо выполнить фильтрацию по измерению **ResponseType** со значением **Success**. Если вы хотите просмотреть значения **BlobCount** для блочного BLOB-объекта, необходимо выполнить фильтрацию по измерению **BlobType** со значением **BlockBlob**.

## <a name="service-continuity-of-legacy-metrics"></a>Непрерывная работа службы метрик прежних версий

Устаревшие метрики доступны параллельно с управляемыми метриками Azure Monitor. Предусмотрена та же поддержка, пока в службе хранилища Azure обслуживаются устаревшие метрики.

## <a name="faq"></a>Часто задаваемые вопросы

**Поддерживается ли классическая учетная запись хранения в новых метриках?**

Нет, новые метрики Azure Monitor поддерживаются только в учетных записях хранения Azure Resource Manager. Если метрики требуется использовать в учетных записях службы хранения, необходимо выполнить миграцию в учетную запись хранения Azure Resource Manager. См. статью [Поддерживаемый платформой перенос ресурсов IaaS из классической модели в модель Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).

**Поддерживает ли служба хранилища Microsoft Azure метрики для управляемых и неуправляемых дисков?**

Нет, служба вычислений Azure поддерживает метрики на дисках. Дополнительные сведения см. в [этой статье](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/).

**Как сопоставить и перенести классические метрики, используя новые метрики?**

См. дополнительные сведения о [сопоставлении и переносе метрик службы хранилища Azure](./storage-metrics-migration.md).

## <a name="next-steps"></a>Дальнейшие действия

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
