---
title: Мониторинг данных Azure Cosmos DB с помощью настроек Azure Diagnostic
description: Узнайте, как использовать настройки Azure Diagnostic для мониторинга производительности и доступности данных, хранящихся в DB Azure Cosmos
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: sngun
ms.openlocfilehash: f5a0b0f71a72ea76940450f73354fda230e09c5c
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521049"
---
# <a name="monitor-azure-cosmos-db-data-by-using-diagnostic-settings-in-azure"></a>Мониторинг данных DB Azure Cosmos с помощью диагностических настроек в Azure

Диагностические настройки в Azure используются для сбора журналов ресурсов. Журналы ресурсов Azure излучаются ресурсом и предоставляют богатые, частые данные о работе этого ресурса. Эти журналы фиксируются в запросе, и они также называются "журналы плоскости данных". Некоторые примеры операций плоскости данных включают удаление, вставку и чтениеFeed. Содержимое этих журналов зависит от типа ресурса.

Метрики платформы и журналы активности собираются автоматически, в то время как необходимо создать диагностическую настройку для сбора журналов ресурсов или их переадресации за пределы Azure Monitor. Можно включить диагностическую настройку для учетных записей Azure Cosmos, используя следующие действия:

1. Войти на [портал Azure](https://portal.azure.com).

1. Перейдите к своей учетной записи Azure Cosmos. Откройте панель **параметров диагностики,** а затем выберите **опцию настройки диагностики.**

1. В панели **параметров Диагностика** заполните форму следующими сведениями: 

    * **Имя**. Введите имя для создаваемых журналов.

    * Вы можете хранить журналы в **Архиве в учетную запись хранения,** **stream к концентратору событий** или отправить в Log **Analytics**

1. При создании диагностической настройки указывается, какую категорию журналов собирать. Категории журналов, поддерживаемых Azure Cosmos DB, перечислены ниже вместе с собранными ими образцами журналов:

 * **DataPlaneRequests**: Выберите эту опцию для входа в запросы на обратный конец для всех API, которые включают s'L, Graph, MongoDB, Cassandra и таблицы API учетных записей в Azure Cosmos DB. Ключевые свойства, `Requestcharge`чтобы `statusCode` `clientIPaddress`отметить: `partitionID`, , и .

    ```json
    { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
    ```

* **MongoRequests**: Выберите эту опцию для входа в запросы, инициированные пользователем, с переднего конца для обслуживания запросов в API API Azure Cosmos DB для MongoDB. Этот тип журнала недоступен для других учетных записей API. Ключевые свойства, `Requestcharge`чтобы `opCode`отметить: . При включании MongoRequests в журналы диагностики, обязательно выключите DataPlaneRequests. Вы увидите один журнал для каждого запроса, сделанного на API.

    ```json
    { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
    ```

* **CassandraRequests**: Выберите эту опцию для входа в запросы, инициированные пользователем, с переднего конца для обслуживания запросов в API API Azure Cosmos DB для Cassandra. Этот тип журнала недоступен для других учетных записей API. Ключевые свойства, `operationName`чтобы `requestCharge` `piiCommandText`отметить, являются , . При включании CassandraRequests в журналы диагностики, обязательно выключите DataPlaneRequests. Вы увидите один журнал для каждого запроса, сделанного на API.

   ```json
   { "time": "2020-03-30T23:55:10.9579593Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "CassandraRequests", "operationName": "QuerySelect", "properties": {"activityId": "6b33771c-baec-408a-b305-3127c17465b6","opCode": "<empty>","errorCode": "-1","duration": "0.311900","requestCharge": "1.589237","databaseName": "system","collectionName": "local","retryCount": "<empty>","authorizationTokenType": "PrimaryMasterKey","address": "104.42.195.92","piiCommandText": "{"request":"SELECT key from system.local"}","userAgent": """"}}
   ```

* **QueryRuntimeStatistics**: Выберите эту опцию для входа в выполненный текст запроса. Этот тип журнала доступен только для учетных записей API S'L.

    ```json
    { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
    ```

* **РазделКлючой статистики**: Выберите эту опцию для регистрации статистики ключей раздела. В настоящее время он представлен размером хранилища (КБ) ключей раздела. Ознакомьтесь [с проблемами устранения проблем, используя раздел Azure Diagnostic queries](#diagnostic-queries) в этой статье. Например, запросы, в которых используется "PartitionKeyStatistics". Бревно испускается против первых трех ключей раздела, которые занимают большую часть хранилища данных. Этот журнал содержит такие данные, как идентификатор подписки, имя региона, имя базы данных, имя сбора, ключ раздела и размер хранилища в КБ.

    ```json
    { "time": "2019-10-11T02:33:24.2018744Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "PartitionKeyStatistics", "properties": {"subscriptionId": "<your_subscription_ID>","regionName": "West US 2","databaseName": "KustoQueryResults","collectionname": "CapacityMetrics","partitionkey": "["CapacityMetricsPartition.136"]","sizeKb": "2048270"}}
    ```

* **PartitionKeyRUConsumption**: Этот журнал сообщает о агрегированном всекундном потреблении ключей раздела в секунду RU/s. В настоящее время Azure Cosmos DB сообщает ключи раздела только для учетных записей API S'L и для операций процедуры чтения/записи и хранения точек. другие AA и типы операций не поддерживаются. Для других AI столбец ключа раздела в таблице диагностического журнала будет пустым. Этот журнал содержит такие данные, как идентификатор подписки, имя региона, имя базы данных, имя сбора, ключ раздела, тип операции и плата за запрос. Ознакомьтесь [с проблемами устранения проблем, используя раздел Azure Diagnostic queries](#diagnostic-queries) в этой статье. Например, запросы, в которых используется "PartitionKeyRUConsumption". 

* **ControlPlaneRequests**: Этот журнал содержит сведения о операциях плоскости управления, таких как создание учетной записи, добавление или удаление региона, обновление настроек репликации учетной записи и т.д. Этот тип журнала доступен для всех типов API, которые включают S'L (Core), MongoDB, Gremlin, Cassandra, Table API.

* **Запросы**: Выберите эту опцию для сбора метрических данных из DB Azure Cosmos в пункты назначения в диагностической настройке. Это те же данные, автоматически собранные в Azure Metrics. Собирайте метрические данные с помощью журналов ресурсов для совместного анализа обоих видов данных и отправки метрических данных за пределы Azure Monitor.

Подробную информацию о том, как создать диагностическую настройку с помощью портала Azure, CLI или PowerShell, можно получить подробную информацию о том, как создать диагностические [настройки для сбора журналов и метрик платформы в статье Azure.](../azure-monitor/platform/diagnostic-settings.md)


## <a name="troubleshoot-issues-with-diagnostics-queries"></a><a id="diagnostic-queries"></a>Проблемы устранения проблем с запросами на диагностику

1. Как получить плату за запрос на дорогие запросы?

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

1. Как найти, какие операции принимают большинство RU /s?

    ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(responseLength_s), max(requestLength_s), max(requestCharge_s), count = count() by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```
1. Как получить дистрибутив для различных операций?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize count = count()  by OperationName, requestResourceType_s, bin(TimeGenerated, 1h) 
   ```

1. Какова максимальная пропускная выливка, потребляемая разделом?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(requestCharge_s) by bin(TimeGenerated, 1h), partitionId_g
   ```

1. Как получить информацию о ключах раздела RU/s потребления в секунду?

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s, TimeGenerated 
   | order by TimeGenerated asc 
   ```

1. Как получить плату за запрос на определенный ключ раздела

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where parse_json(partitionKey_s)[0] == "2" 
   ```

1. Как получить верхние клавиши перегородки с большинством RU/s, потребляемых в определенный период? 

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where TimeGenerated >= datetime("11/26/2019, 11:20:00.000 PM") and TimeGenerated <= datetime("11/26/2019, 11:30:00.000 PM") 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s 
   | order by total desc
    ```

1. Как получить журналы для ключей раздела, размер хранилища которых превышает 8 ГБ?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics"
   | where todouble(sizeKb_d) > 800000
   ```

1. Как получить статистику ключей раздела для оценки перекоса в трех верхних разделах для учетной записи базы данных?

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId 
    ```

## <a name="next-steps"></a>Дальнейшие действия

* [Azure Monitor для Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json)
* [Monitor and debug with metrics in Azure Cosmos DB](use-metrics.md) (Мониторинг и отладка с помощью метрик в Azure Cosmos DB)
