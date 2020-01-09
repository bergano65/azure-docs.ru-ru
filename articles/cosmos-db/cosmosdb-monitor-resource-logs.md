---
title: Мониторинг Azure Cosmos DB данных с помощью параметров диагностики Azure
description: Узнайте, как использовать параметры диагностики Azure для наблюдения за производительностью и доступностью данных, хранящихся в Azure Cosmos DB
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: sngun
ms.openlocfilehash: 670797eb833b0a145a18e20c6bba711ca11609bc
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483288"
---
# <a name="monitor-azure-cosmos-db-data-by-using-diagnostic-settings-in-azure"></a>Мониторинг Azure Cosmos DB данных с помощью параметров диагностики в Azure

Параметры диагностики в Azure используются для получения журналов ресурсов. Журналы ресурсов Azure выдаются ресурсом и предоставляют обширные, часто встречающиеся данные о работе этого ресурса. Эти журналы фиксируются для каждого запроса, а также называются "журналами плоскости данных". Некоторые примеры операций с плоскостью данных включают удаление, вставку и readFeed. Содержимое этих журналов зависит от типа ресурса.

Метрики платформы и журналы действий собираются автоматически, в то время как необходимо создать параметр диагностики для сбора журналов ресурсов или пересылки их за пределы Azure Monitor. Вы можете включить параметр диагностики для учетных записей Azure Cosmos, выполнив следующие действия.

1. Войдите на [портал Azure](https://portal.azure.com).

1. Перейдите к своей учетной записи Azure Cosmos. Откройте панель **параметры диагностики** и выберите пункт Добавить параметр **диагностики** .

1. В области **параметры диагностики** заполните форму следующими сведениями: 

    * **Имя**. Введите имя для создаваемых журналов.

    * Вы можете хранить журналы для **архивации в учетную запись хранения**, передавать их в **концентратор событий** или **передавать log Analytics**

1. При создании параметра диагностики указывается, какую категорию журналов следует собираются. Ниже перечислены категории журналов, поддерживаемые Azure Cosmos DB, а также примеры журналов, собранные ими.

 * **DataPlaneRequests**. Выберите этот параметр, чтобы регистрировать запросы серверной части для всех API-интерфейсов, в том числе учетные записи SQL, Graph, MongoDB, Cassandra и API таблиц в Azure Cosmos DB. Ключевые свойства для заметок: `Requestcharge`, `statusCode`, `clientIPaddress`и `partitionID`.

    ```
    { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
    ```

* **MongoRequests**. Выберите этот параметр, чтобы регистрировать инициированные пользователем запросы из внешнего интерфейса для обслуживания запросов к API Azure Cosmos DB для MongoDB, этот тип журнала недоступен для других учетных записей API. Запросы MongoDB будут отображаться в MongoRequests, а также в DataPlaneRequests. Ключевые свойства для заметок: `Requestcharge`, `opCode`.

    ```
    { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
    ```

* **Куерирунтиместатистикс**: Выберите этот параметр, чтобы регистрировать выполненный текст запроса. Этот тип журнала доступен только для учетных записей API SQL.

    ```
    { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
    ```

* **Партитионкэйстатистикс**: Выберите этот параметр, чтобы регистрировать статистику ключей секций. В настоящее время он представлен с размером хранилища (КБ) ключей разделов. См. раздел [Устранение неполадок с помощью диагностических запросов Azure](#diagnostic-queries) этой статьи. Например, запросы, использующие "Партитионкэйстатистикс". Журнал создается для первых трех ключей разделов, которые занимают большую часть хранилища данных. Этот журнал содержит такие данные, как идентификатор подписки, имя региона, имя базы данных, имя коллекции, ключ секции и размер хранилища в КБ.

    ```
    { "time": "2019-10-11T02:33:24.2018744Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "PartitionKeyStatistics", "properties": {"subscriptionId": "<your_subscription_ID>","regionName": "West US 2","databaseName": "KustoQueryResults","collectionname": "CapacityMetrics","partitionkey": "["CapacityMetricsPartition.136"]","sizeKb": "2048270"}}
    ```

* **Партитионкэйруконсумптион**. Этот журнал содержит сводные данные о потреблении ключей секций за секунду. В настоящее время Azure Cosmos DB сообщает ключи секций только для учетных записей API SQL и для операций чтения, записи и хранимых процедур. другие API-интерфейсы и типы операций не поддерживаются. Для других API ключевой столбец секции в таблице журнала диагностики будет пустым. Этот журнал содержит такие данные, как идентификатор подписки, имя региона, имя базы данных, имя коллекции, ключ секции, тип операции и плата за запрос. См. раздел [Устранение неполадок с помощью диагностических запросов Azure](#diagnostic-queries) этой статьи. Например, запросы, использующие "Партитионкэйруконсумптион". 

* **Контролпланерекуестс**. Этот журнал содержит сведения о операциях плоскости управления, таких как создание учетной записи, добавление или удаление региона, обновление параметров репликации учетных записей и т. д. Этот тип журнала доступен для всех типов API, включающих SQL (Core), MongoDB, Gremlin, Cassandra, API таблиц.

* **Запросы**. Выберите этот параметр, чтобы получать данные метрик из Azure Cosmos DB в целевые объекты в параметре диагностики. Это те же данные, собираемые автоматически в метриках Azure. Собирайте данные метрик с помощью журналов ресурсов для одновременного анализа обоих видов данных и отправки данных метрик за пределы Azure Monitor.

Подробные сведения о создании параметров диагностики с помощью портал Azure, интерфейса командной строки или PowerShell см. в статье [Создание параметров диагностики для сбора журналов и метрик платформы в Azure](../azure-monitor/platform/diagnostic-settings.md) .


## <a id="diagnostic-queries"></a>Устранение неполадок с помощью диагностических запросов

1. Как получить плату за запросы для ресурсоемких запросов?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 10.0
   | project activityId_g, requestCharge_s
   | join kind= inner (
   AzureDiagnostics
   | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
   | project activityId_g, querytext_s
   ) on $left.activityId_g == $right.activityId_g
   | order by requestCharge_s desc
   | limit 100
   ```

1. Как узнать, какие операции занимает больше всего единиц запросов в секунду?

    ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(responseLength_s), max(requestLength_s), max(requestCharge_s), count = count() by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```
1. Как получить распределение для различных операций?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize count = count()  by OperationName, requestResourceType_s, bin(TimeGenerated, 1h) 
   ```

1. Какова максимальная пропускная способность, предоставляемая секцией?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(requestCharge_s) by bin(TimeGenerated, 1h), partitionId_g
   ```

1. Как получить сведения о ключах разделов, потребляемых единицами запросов/с, в секунду?

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s, TimeGenerated 
   | order by TimeGenerated asc 
   ```

1. Как получить плату за запрос для определенного ключа секции

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where parse_json(partitionKey_s)[0] == "2" 
   ```

1. Как получить ключи разделов с наибольшим количеством единиц запросов в секунду, потребляемых в определенный период? 

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where TimeGenerated >= datetime("11/26/2019, 11:20:00.000 PM") and TimeGenerated <= datetime("11/26/2019, 11:30:00.000 PM") 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s 
   | order by total desc
    ```

1. Как получить журналы для ключей разделов, размер хранения которых превышает 8 ГБ?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics"
   | where todouble(sizeKb_d) > 800000
   ```

1. Как получить статистику ключа секции для вычисления смещения между тремя верхними секциями для учетной записи базы данных?

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId 
    ```

## <a name="next-steps"></a>Дальнейшие действия

* [Azure Monitor для Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json)
* [Monitor and debug with metrics in Azure Cosmos DB](use-metrics.md) (Мониторинг и отладка с помощью метрик в Azure Cosmos DB)
