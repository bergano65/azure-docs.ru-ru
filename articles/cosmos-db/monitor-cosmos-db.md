---
title: Мониторинг Azure Космос DB (ru) Документы Майкрософт
description: Узнайте, как контролировать производительность и доступность Azure Cosmos DB.
author: bwren
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: bwren
ms.custom: subject-monitoring
ms.openlocfilehash: db9e86706ecd4e5b2526e1d801dda45ed6b345c6
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887254"
---
# <a name="monitoring-azure-cosmos-db"></a>Мониторинг Azure Космос DB
При наличии критических приложений и бизнес-процессов, опирающихся на ресурсы Azure, необходимо отслеживать их доступность, производительность и работу. В этой статье описаны данные мониторинга, генерируемые базами данных Azure Cosmos, и то, как можно использовать функции Azure Monitor для анализа и оповещения об этих данных.

## <a name="what-is-azure-monitor"></a>Общие сведения об Azure Monitor
Azure Cosmos DB создает данные мониторинга с помощью [Azure Monitor,](../azure-monitor/overview.md) который является службой мониторинга полного стека в Azure, которая предоставляет полный набор функций для мониторинга ресурсов Azure в дополнение к ресурсам в других облаках и на месте. 

Если вы еще не знакомы с мониторингом служб Azure, начните со статьи [Мониторинг ресурсов Azure с Azure Monitor,](../azure-monitor/insights/monitor-azure-resource.md) в которой описаны следующие:

- Общие сведения об Azure Monitor
- Затраты, связанные с мониторингом
- Мониторинг данных, собранных в Azure
- Настройка коллекции данных
- Стандартные инструменты в Azure для анализа и оповещения о данных мониторинга

Следующие разделы основывается на этой статье, описывая конкретные данные, собранные из Azure Cosmos DB, и предоставляя примеры настройки сбора данных и анализа этих данных с помощью инструментов Azure.

## <a name="azure-monitor-for-cosmos-db-preview"></a>Azure монитор для космоса DB (Предварительный просмотр)
[Azure Monitor for Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md) основан на [особенностях трудовых книжек Azure Monitor](../azure-monitor/app/usage-workbooks.md) и использует те же данные мониторинга, собранные для Cosmos DB, описанные в разделах ниже. Используйте этот инструмент для просмотра общей производительности, сбоев, емкости и работоспособности всех ресурсов Azure Cosmos DB в едином интерактивном интерфейсе, а также использовать другие функции Azure Monitor для детального анализа и оповещения. 

![Azure монитор для Космос DB](media/monitor-cosmos-db/azure-monitor-cosmos-db.png)

## <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>Просмотр метрик уровня работы для Azure Cosmos DB

1. Войдите на [портал Azure](https://portal.azure.com/).

1. Выберите **монитор** из левой панели навигации и выберите **метрики.**

   ![Панель метрик в Azure Monitor](./media/monitor-cosmos-db/monitor-metrics-blade.png)

1. Из панели **метрик** > **Выберите ресурс** > выбрать требуемую **подписку**и **группу ресурсов.** Для **типа ресурса**выберите **dB-учетные записи Azure Cosmos,** выберите одну из существующих учетных записей Azure Cosmos и выберите **Apply.**

   ![Выберите учетную запись Cosmos DB для просмотра метрик](./media/monitor-cosmos-db/select-cosmosdb-account.png)

1. Далее вы можете выбрать метрику из списка доступных метрик. Вы можете выбрать метрики, специфичные для запроса единиц, хранения, задержки, доступности, Кассандры и других. Чтобы узнать подробно обо всех доступных метриках в этом списке, [см.](monitor-cosmos-db-reference.md) В этом примере давайте выберем **единицы запроса** и **Avg** в качестве значения агрегации.

   В дополнение к этим деталям, вы также можете выбрать **диапазон времени** и **детализацию времени** метрик. На макс, вы можете просмотреть метрики за последние 30 дней.  После нанесения фильтра на основе фильтра отображается диаграмма. Вы можете увидеть среднее количество единиц запроса, потребляемых в минуту за выбранный период.  

   ![Выберите метрику на портале Azure](./media/monitor-cosmos-db/metric-types.png)

### <a name="add-filters-to-metrics"></a>Добавление фильтров к метрикам

Вы также можете фильтровать метрики и диаграмму, отображаемую на определенном **CollectionName,** **DatabaseName,** **OperationType,** **Регион**, и **StatusCode**. Чтобы отфильтровать метрики, выберите **фильтр и** выберите требуемое свойство, такое как **OperationType,** и выберите значение, такое как **запрос.** Затем на графике отображается единицы запроса, потребляемые для операции запроса за выбранный период. Операции, выполняемые с помощью процедуры Stored, не регистрируются, поэтому они не доступны в метрике OperationType.

![Добавить фильтр для выбора детализации метрики](./media/monitor-cosmos-db/add-metrics-filter.png)

Вы можете сгруппировать метрики с помощью опции **расщепления Apply.** Например, можно сгруппировать единицы запроса на тип операции и просмотреть график для всех операций одновременно, как показано на следующем изображении:

![Добавить фильтр расщепления притязаний](./media/monitor-cosmos-db/apply-metrics-splitting.png)

## <a name="monitoring-data-collected-from-azure-cosmos-db"></a>Мониторинг данных, собранных с Azure Cosmos DB

Azure Cosmos DB собирает те же данные мониторинга, что и другие ресурсы Azure, описанные в [данных мониторинга с ресурсов Azure.](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data) Подробную ссылку на журналы и метрики, созданные Azure Cosmos DB, можно ознакомиться с [рекомендациями мониторинга данных Azure Cosmos DB.](monitor-cosmos-db-reference.md)

Страница **«Обзор»** на портале Azure для каждой базы данных Azure Cosmos содержит краткое представление об использовании базы данных, включая ее запрос и почасовое использование счетов. Это полезная информация, но лишь небольшой объем доступных данных мониторинга. Некоторые из этих данных собираются автоматически и доступны для анализа, как только вы создаете базу данных, в то время как вы можете включить дополнительный сбор данных с определенной конфигурацией.

![Страница "Обзор"](media/monitor-cosmos-db/overview-page.png)

## <a name="analyzing-metric-data"></a>Анализ метрических данных

Azure Cosmos DB предоставляет пользовательский опыт работы с метриками. См, как [Monitor и отладка Azure Cosmos DB, можно](cosmos-db-azure-monitor-metrics.md) узнать об использовании этого опыта и анализе различных сценариев DB Azure Cosmos.

Вы можете анализировать метрики для Azure Cosmos DB с помощью метрик других служб Azure с помощью исследователя метрик, **открывая метрики** из меню **Azure Monitor.** Подробную информацию об использовании этого инструмента можно узнать из-за использования этого инструмента с [помощью Azure Metrics Explorer.](../azure-monitor/platform/metrics-getting-started.md) Все метрики для Azure Cosmos DB находятся в пространстве имен **Космос DB стандартных метрик.** При добавлении фильтра в диаграмму можно использовать следующие измерения:

- CollectionName
- имя_базы_данных
- OperationType
- Регион
- StatusCode


## <a name="analyzing-log-data"></a>Анализ данных журнала
Данные в журналах Azure Monitor хранятся в таблицах, каждая таблица которой имеет свой собственный набор уникальных свойств. Azure Cosmos DB хранит данные в следующих таблицах.

| Таблица | Описание |
|:---|:---|
| AzureDiagnostics | Общая таблица, используемая несколькими службами для хранения журналов ресурсов. Логы ресурсов из Azure Cosmos DB можно идентифицировать с помощью. `MICROSOFT.DOCUMENTDB`   |
| AzureActivity    | Общая таблица, в ней хранятся все записи из журнала Activity. 


> [!IMPORTANT]
> При выборе **журналов** из меню Azure Cosmos DB аналитика журнала открывается с областью запроса, установленной в текущей базе данных Azure Cosmos. Это означает, что журнал запросов будет включать только данные из этого ресурса. Если требуется запустить запрос, включающий данные из других баз данных или данные из других служб Azure, выберите **журналы** из меню **Azure Monitor.** Подробнее о [возможности запроса и временном диапазоне в журнале Azure Monitor Analytics](../azure-monitor/log-query/scope.md) смотрите.

### <a name="azure-cosmos-db-log-analytics-queries-in-azure-monitor"></a>Запросы аналитики журналов Azure Cosmos DB в Azure Monitor

Вот некоторые запросы, которые можно ввести в панель **поиска журнала,** чтобы помочь вам контролировать контейнеры Azure Cosmos. Эти запросы поддерживают [новый язык](../log-analytics/log-analytics-log-search-upgrade.md).

Ниже приведены запросы, которые можно использовать для мониторинга баз данных Azure Cosmos.

* Для запроса всех журналов диагностики из Azure Cosmos DB за указанный период времени:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"

    ```

* Для запроса 10 последних событий журнала:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | limit 10
    ```

* Для запроса всех операций, сгруппированных по типу операции:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by OperationName
    ```

* Запрос для всех операций, сгруппированных по ресурсам:

    ```Kusto
    AzureActivity 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by Resource

    ```

* Для запроса всех действий пользователя, сгруппированных по ресурсу:

    ```Kusto
    AzureActivity 
    | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by Resource
    ```
* Чтобы получить все запросы больше, чем 100 RUs вместе с данными **dataPlaneRequests** и **queryRunTimeStatistics**.

    ```Kusto
    AzureDiagnostics
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 100.0
    | project activityId_g, requestCharge_s
    | join kind= inner (
           AzureDiagnostics
           | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
           | project activityId_g, querytext_s
    ) on $left.activityId_g == $right.activityId_g
    | order by requestCharge_s desc
    | limit 100
    ```

* Для запроса операций, которые выполнялись дольше 3 миллисекунд:

    ```Kusto
    AzureDiagnostics 
    | where toint(duration_s) > 3 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* Для запроса агента, выполняющего операции:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by OperationName, userAgent_s
    ```

* Для запроса времени выполнения длительных операций:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | project TimeGenerated , duration_s 
    | summarize count() by bin(TimeGenerated, 5s)
    | render timechart
    ```
    
* Чтобы получить статистику ключей раздела для оценки перекоса в верхних 3 разделах для учетной записи базы данных:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId 
    ```

## <a name="monitor-azure-cosmos-db-programmatically"></a>Программный мониторинг Azure Cosmos DB
Доступные на портале метрики уровня учетной записи, такие как данные об использовании хранилища учетной записи и общее число запросов, недоступны через API-интерфейсы SQL. Тем не менее, можно получить данные об использовании на уровне коллекции с помощью API SQL. Чтобы получить данных на уровне коллекции, выполните следующие действия:

* Чтобы использовать REST API, [выполните запрос GET к коллекции](https://msdn.microsoft.com/library/mt489073.aspx). Квота и данные об использовании коллекции возвращаются в заголовке ответа x-ms-resource-quota и x-ms-resource-usage headers.
* Чтобы использовать пакет SDK для .NET, воспользуйтесь методом [DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx), возвращающим объект [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx), который содержит ряд свойств использования, например **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage** и прочие.

Чтобы получить дополнительные метрики, используйте [пакет SDK для Azure Monitor](https://www.nuget.org/packages/Microsoft.Azure.Insights). Доступные определения метрик можно получить с помощью следующего вызова.

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08

В запросах для извлечения отдельных метрик используется следующий формат.

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z



## <a name="next-steps"></a>Дальнейшие действия

- Смотрите [ссылку мониторинга данных Azure Cosmos DB](monitor-cosmos-db-reference.md) для ссылки на журналы и метрики, созданные Azure Cosmos DB.
- Подробную информацию о мониторинге ресурсов Azure можно просмотреть [в ресурсах Azure с помощью Azure Monitor.](../azure-monitor/insights/monitor-azure-resource.md)
