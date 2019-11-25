---
title: Azure Cosmos DB monitoring data reference | Microsoft Docs
description: Log and metrics reference for monitoring data from Azure Cosmos DB.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 11/11/2019
ms.author: bwren
ms.custom: subject-monitoring
ms.subservice: logs
ms.openlocfilehash: d131523e3031f55a818bb1919f39119bf073cb75
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456532"
---
# <a name="azure-cosmos-db-monitoring-data-reference"></a>Azure Cosmos DB monitoring data reference
This article provides a reference of log and metric data collected to analyze the performance and availability of Azure Cosmos DB. See [Monitoring Cosmos DB](monitor-cosmos-db.md) for details on collecting and analyzing monitoring data for Azure Cosmos DB.


## <a name="resource-logs"></a>Журналы ресурсов
The following table lists the properties for Azure Cosmos DB resource logs when they're collected in Azure Monitor Logs or Azure Storage. In Azure Monitor Logs, they're collected in the **AzureDiagnostics** table with a **ResourceProvider** value of *MICROSOFT.DOCUMENTDB*. 

| Поле или свойство службы хранилища Azure | Azure Monitor Logs property | Описание |
| --- | --- | --- |
| **time** | **TimeGenerated** | Дата и время (UTC) выполнения операции. |
| **resourceId** | **Ресурс** | Учетная запись Azure Cosmos DB, для которой включены журналы.|
| **category** | **Категория** | For Azure Cosmos DB logs, **DataPlaneRequests**, **MongoRequests**, **QueryRuntimeStatistics**, **PartitionKeyStatistics**, **PartitionKeyRUConsumption**, **ControlPlaneRequests** are the available log types. |
| **operationName** | **OperationName** | Имя операции. В качестве значения можно использовать любую из следующих операций: Create, Update, Read, ReadFeed, Delete, Replace, Execute, SqlQuery, Query, JSQuery, Head, HeadFeed или Upsert.   |
| **properties** | Н/Д | Содержимое этого поля описано в строках, приведенных ниже. |
| **activityId** | **activityId_g** | Уникальный идентификатор GUID регистрируемой операции. |
| **userAgent** | **userAgent_s** | Строка, которая указывает агент пользователя клиента, выполняющий запрос. Формат: {имя_агента_пользователя} / {версия}.|
| **requestResourceType** | **requestResourceType_s** | Тип доступного ресурса. В качестве значения можно использовать любой из следующих типов ресурсов: Database, Container, Document, Attachment, User, Permission, StoredProcedure, Trigger, UserDefinedFunction или Offer. |
| **statusCode** | **statusCode_s** | Состояние ответа операции. |
| **requestResourceId** | **ResourceId** | Идентификатор ресурса, который относится к запросу. В зависимости от выполняемой операции значение может указывать на databaseRid, collectionRid или documentRid.|
| **clientIpAddress** | **clientIpAddress_s** | IP-адрес клиента. |
| **requestCharge** | **requestCharge_s** | Количество ЕЗ, которые используются при операции. |
| **collectionRid** | **collectionId_s** | Уникальный идентификатор коллекции.|
| **duration** | **duration_s** | The duration of the operation, in milliseconds. |
| **requestLength** | **requestLength_s** | Длина запроса в байтах. |
| **responseLength** | **responseLength_s** | Длина ответа в байтах.|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Это свойство должно быть заполнено, если [маркеры ресурсов](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) используются для проверки подлинности. Значение указывает на идентификатор ресурса пользователя. |

For a list of all Azure Monitor log categories and links to associated schemas, see [Azure Monitor Logs categories and schemas](../azure-monitor/platform/diagnostic-logs-schema.md). 

## <a name="metrics"></a>Метрики
The following tables list the platform metrics collected for Azure CosmOS DB. All metrics are stored in the namespace **Cosmos DB standard metrics**.

For a list of all Azure Monitor support metrics (including CosmosDB), see [Azure Monitor supported metrics](../azure-monitor/platform/metrics-supported.md). 

#### <a name="request-metrics"></a>Метрики запросов
            
|Metric (Metric Display Name)|Unit (Aggregation Type) |Описание|Измерения| Степени детализации времени| Сопоставление метрик прежних версий | Использование |
|---|---|---|---| ---| ---| ---|
| TotalRequests (Total Requests) | Count (Count) | Количество выполненных запросов| DatabaseName, CollectionName, Region, StatusCode| Все | TotalRequests, HTTP 2xx, HTTP 3xx, HTTP 400, HTTP 401, внутренняя ошибка сервера, служба недоступна, отрегулированные запросы, среднее число запросов в секунду | Used to monitor requests per status code, container at a minute granularity. Чтобы получить среднее число запросов в секунду, используйте агрегат Count в минуту и разделите на 60. |
| MetadataRequests (Metadata Requests) |Count (Count) | Количество запросов метаданных. Azure Cosmos DB maintains system metadata container for each account, that allows you to enumerate collections, databases, etc., and their configurations, free of charge. | DatabaseName, CollectionName, Region, StatusCode| Все| |Позволяет отслеживать регулирование из-за запросов метаданных.|
| MongoRequests (Mongo Requests) | Count (Count) | Количество выполненных запросов Mongo | DatabaseName, CollectionName, Region, CommandName, ErrorCode| Все |Частота запросов на запрос Mongo, частота запросов на обновление Mongo, частота запросов на удаление Mongo, частота запросов на вставку Mongo, частота запросов на количество Mongo| Позволяет отслеживать ошибки запроса Mongo, использование каждого типа команды. |

#### <a name="request-unit-metrics"></a>Метрики единиц запроса

|Metric (Metric Display Name)|Unit (Aggregation Type)|Описание|Измерения| Степени детализации времени| Сопоставление метрик прежних версий | Использование |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (Mongo Request Charge) | Count (Total) |Использованные единицы запросов Mongo| DatabaseName, CollectionName, Region, CommandName, ErrorCode| Все |Расходы запросов на запрос Mongo, расходы запросов на обновление Mongo, расходы запросов на удаление Mongo, расходы запросов на вставку Mongo, расходы запросов на количество Mongo| Позволяет отслеживать единицы запросов ресурсов Mongo в минуту.|
| TotalRequestUnits (Total Request Units)| Count (Total) | Использованные единицы запросов| DatabaseName, CollectionName, Region, StatusCode |Все| TotalRequestUnits| Позволяет отслеживать использование общего количества единиц запросов со степенью детализации до минуты. Чтобы получить среднее число единиц запросов, потребленных в секунду, используйте общий объем агрегата в минуту и разделите на 60.|
| ProvisionedThroughput (Provisioned Throughput)| Count (Maximum) |Provisioned throughput at container granularity| DatabaseName, ContainerName| 5M| | Used to monitor provisioned throughput per container.|

#### <a name="storage-metrics"></a>Метрики хранения

|Metric (Metric Display Name)|Unit (Aggregation Type)|Описание|Измерения| Степени детализации времени| Сопоставление метрик прежних версий | Использование |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (Available Storage) |Bytes (Total) | Total available storage reported at 5-minutes granularity per region| DatabaseName, CollectionName, Region| 5M| Доступная служба хранилища| Позволяет отслеживать доступную емкость хранилища (применимо только для фиксированных коллекций хранилища). Минимальная степень детализации должна составлять 5 минут.| 
| DataUsage (Data Usage) |Bytes (Total) |Total data usage reported at 5-minutes granularity per region| DatabaseName, CollectionName, Region| 5M |Размер данных | Used to monitor total data usage at container and region, minimum granularity should be 5 minutes.|
| IndexUsage (Index Usage) | Bytes (Total) |Total Index usage reported at 5-minutes granularity per region| DatabaseName, CollectionName, Region| 5M| Размер индексов| Used to monitor total data usage at container and region, minimum granularity should be 5 minutes. |
| DocumentQuota (Document Quota) | Bytes (Total) | Total storage quota reported at 5-minutes granularity per region.| DatabaseName, CollectionName, Region| 5M |Объем хранилища| Used to monitor total quota at container and region, minimum granularity should be 5 minutes.|
| DocumentCount (Document Count) | Count (Total) |Total document count reported at 5-minutes granularity per region| DatabaseName, CollectionName, Region| 5M |Число документов|Used to monitor document count at container and region, minimum granularity should be 5 minutes.|

#### <a name="latency-metrics"></a>Метрики задержки

|Metric (Metric Display Name)|Unit (Aggregation Type)|Описание|Измерения| Степени детализации времени| Использование |
|---|---|---|---| ---| ---|
| ReplicationLatency (Replication Latency)| MilliSeconds (Minimum, Maximum, Average) | Задержка репликации P99 между исходными и целевыми регионами для геореплицируемой учетной записи| SourceRegion, TargetRegion| Все | Позволяет отслеживать задержку репликации P99 между любыми двумя регионами для геореплицируемой учетной записи. |


#### <a name="availability-metrics"></a>Метрики доступности

|Metric (Metric Display Name) |Unit (Aggregation Type)|Описание| Степени детализации времени| Сопоставление метрик прежних версий | Использование |
|---|---|---|---| ---| ---|
| ServiceAvailability (Service Availability)| Percent (Minimum, Maximum) | Доступность запросов учетной записи со степенью детализации до одного часа| 1 ч | Доступность службы | Represents the percent of total passed requests. Запрос считается не удавшимся из-за ошибки системы, если код состояния — 410, 500 или 503. Позволяет отслеживать доступность учетной записи со степенью детализации до одного часа. |


#### <a name="cassandra-api-metrics"></a>Метрики по API Cassandra

|Metric (Metric Display Name)|Unit (Aggregation Type)|Описание|Измерения| Степени детализации времени| Использование |
|---|---|---|---| ---| ---|
| CassandraRequests (Cassandra Requests) | Count (Count) | Количество сделанных запросов API Cassandra| DatabaseName, CollectionName, ErrorCode, Region, OperationType, ResourceType| Все| Позволяет отслеживать запросы Cassandra со степенью детализации до минуты. Чтобы получить среднее число запросов в секунду, используйте агрегат Count в минуту и разделите на 60.|
| CassandraRequestCharges (Cassandra Request Charges) | Count (Sum, Min, Max, Avg) | Единицы запросов, потребленные запросами API Cassandra| DatabaseName, CollectionName, Region, OperationType, ResourceType| Все| Позволяет отслеживать единицы запросов, используемые в минуту учетной записью API Cassandra.|
| CassandraConnectionClosures (Cassandra Connection Closures) |Count (Count) |Число закрытых подключений Cassandra| ClosureReason, Region| Все | Позволяет отслеживать возможность подключения между клиентами и API Cassandra для Azure Cosmos DB.|

## <a name="see-also"></a>См. также

- See [Monitoring Azure Cosmos DB](monitor-cosmos-db.md) for a description of monitoring Azure Cosmos DB.
- See [Monitoring Azure resources with Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md) for details on monitoring Azure resources.
