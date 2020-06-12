---
title: Мониторинг Azure Cosmos DB | Документация Майкрософт
description: Узнайте, как отслеживать производительность и доступность Azure Cosmos DB.
author: bwren
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: bwren
ms.custom: subject-monitoring
ms.openlocfilehash: 5056762dab18ae23980c7d3b3ebfbb3c3014fa56
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/22/2020
ms.locfileid: "83798712"
---
# <a name="monitoring-azure-cosmos-db"></a>Мониторинг Azure Cosmos DB

При наличии критически важных приложений и бизнес-процессов, использующих ресурсы Azure, необходимо отслеживать эти ресурсы на предмет их доступности, производительности и работы. В этой статье описаны данные мониторинга, созданные в базах данных Azure Cosmos, а также анализ таких данных и оповещений о них с помощью функций Azure Monitor.

## <a name="what-is-azure-monitor"></a>Общие сведения об Azure Monitor
Azure Cosmos DB создает данные мониторинга с помощью [Azure Monitor](../azure-monitor/overview.md) — комплексной службы мониторинга в Azure, которая предоставляет полный набор функций для отслеживания ресурсов Azure в дополнение к ресурсам в других облаках и локальных средах.

Если вы еще не знакомы с мониторингом служб Azure, начните со статьи [Мониторинг ресурсов Azure с помощью Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md), в которой описывается следующее:

- Общие сведения об Azure Monitor
- затраты, связанные с мониторингом;
- данные мониторинга, собираемые в Azure;
- настройка сбора данных;
- стандартные средства Azure для анализа данных мониторинга и оповещения.

Следующие разделы являются продолжением этой статьи и посвящены описанию данных, собираемых в Azure Cosmos DB. Кроме того, в ней приводятся примеры настройки сбора и анализа данных с помощью средств Azure.

## <a name="azure-monitor-for-azure-cosmos-db"></a>Azure Monitor для Azure Cosmos DB

Компонент Azure Monitor для Azure Cosmos DB основан на [функции книг Azure Monitor](../azure-monitor/platform/workbooks-overview.md) и использует собранные для Cosmos DB данные мониторинга, которые описываются в следующих разделах. Используйте Azure Monitor для получения общего представления о производительности, сбоях, емкости и работоспособности всех ваших ресурсов Azure Cosmos DB в едином интерактивном интерфейсе. Кроме того, в Azure Monitor есть функции для подробного анализа и оповещений. Дополнительные сведения см. в статье [Изучение возможностей Azure Monitor для Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md).

> [!NOTE]
> При создании контейнеров следите за тем, чтобы не создать два контейнера с одинаковыми именами, но в разных регистрах. Причина в том, что некоторые компоненты платформы Azure не учитывают регистр. Это может привести к путанице или конфликтам в данных телеметрии и действиях с контейнерами с такими именами.

## <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>Просмотр метрик на уровне операций для Azure Cosmos DB

1. Войдите на [портал Azure](https://portal.azure.com/).

1. На панели навигации слева выберите пункт **Монитор**, а затем выберите **Метрики**.

   ![Область метрик в Azure Monitor](./media/monitor-cosmos-db/monitor-metrics-blade.png)

1. В области **Метрики** щелкните **Выбрать ресурс** и выберите требуемые **подписку** и **группу ресурсов**. В поле **Тип ресурса** выберите **Учетные записи Azure Cosmos DB**, выберите одну из существующих учетных записей Azure Cosmos и нажмите кнопку **Применить**.

   ![Выбор учетной записи Cosmos DB для просмотра метрик](./media/monitor-cosmos-db/select-cosmosdb-account.png)

1. Далее можно выбрать метрику из списка доступных метрик. Вы можете выбрать метрики, связанные с единицами запросов, хранилищем, задержкой, доступностью, Cassandra и т. д. Подробные сведения о всех доступных метриках в списке см. в статье с [перечнем метрик по категориям](monitor-cosmos-db-reference.md). В этом примере выберем **Единицы запроса** и **Среднее** в качестве значения агрегата.

   Помимо этих сведений, можно также выбрать **диапазон времени** и **степень детализации времени** для метрик. Вы можете просмотреть метрики максимум за последние 30 дней.  После применения фильтра отображается диаграмма на его основе. На ней можно увидеть среднее количество единиц запросов, употреблявшихся в минуту за выбранный период.  

   ![Выбор метрики на портале Azure](./media/monitor-cosmos-db/metric-types.png)

### <a name="add-filters-to-metrics"></a>Добавление фильтров к метрикам

Можно также отфильтровать метрики и отображаемую диаграмму по определенному значению **CollectionName**, **DatabaseName**, **OperationType**, **Region** или **StatusCode**. Чтобы отфильтровать метрики, выберите команду **Добавить фильтр**, а затем выберите требуемое свойство, например **OperationType**, и значение, например **Запрос**. После этого на графе отобразятся единицы запросов, использованные для операции запроса за выбранный период. Операции, выполняемые с помощью хранимой процедуры, не регистрируются в журнале, поэтому они недоступны в метрике OperationType.

![Добавление фильтра для выбора степени детализации метрики](./media/monitor-cosmos-db/add-metrics-filter.png)

Метрики можно группировать с помощью параметра **Применить разделение**. Например, можно сгруппировать единицы запросов по типу операции и просмотреть граф для всех операций сразу, как показано на следующем рисунке:

![Добавление фильтра "Применить разделение"](./media/monitor-cosmos-db/apply-metrics-splitting.png)

## <a name="monitoring-data-collected-from-azure-cosmos-db"></a>Данные мониторинга, собираемые из Azure Cosmos DB

Azure Cosmos DB собирает данные мониторинга тех же типов, что и другие ресурсы Azure, которые описаны в разделе [Мониторинг ресурсов Azure с помощью Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). Подробную справку по журналам и метрикам, создаваемым Azure Cosmos DB, см. в [справочнике по данным мониторинга Azure Cosmos DB](monitor-cosmos-db-reference.md).

На странице **Обзор** на портале Azure приводится сводка использования каждой базы данных Azure Cosmos, включая количество запросов и стоимость в час. Это полезная информация, которая, однако, представляет собой лишь небольшую часть доступных данных мониторинга. Некоторые из этих данных собираются автоматически и доступны для анализа сразу же после создания базы данных, но для сбора дополнительных данных требуется настройка.

![Страница "Обзор"](media/monitor-cosmos-db/overview-page.png)

## <a name="analyzing-metric-data"></a>Анализ данных метрик

Azure Cosmos DB предоставляет пользовательский интерфейс для работы с метриками. Подробные сведения об использовании этого интерфейса и анализе различных сценариев Azure Cosmos DB см. в статье о [мониторинге и отладке метрик Azure Cosmos DB в Azure Monitor](cosmos-db-azure-monitor-metrics.md).

Вы можете анализировать метрики Azure Cosmos DB вместе с метриками из других служб Azure с помощью обозревателя метрик. Для этого выберите пункт **Метрики** в меню **Azure Monitor**. Подробные сведения об использовании этого средства см. в статье [Начало работы с обозревателем метрик Azure](../azure-monitor/platform/metrics-getting-started.md). Все метрики Azure Cosmos DB находятся в пространстве имен **Стандартные метрики Cosmos DB**. При добавлении фильтра к диаграмме можно использовать следующие измерения с метриками.

- CollectionName
- имя_базы_данных
- OperationType
- Регион
- StatusCode

## <a name="analyzing-log-data"></a>Анализ данных журналов
Данные в журналах Azure Monitor хранятся в таблицах, каждая из которых имеет собственный набор уникальных свойств. Azure Cosmos DB сохраняет данные в указанных ниже таблицах.

| Таблица | Описание |
|:---|:---|
| AzureDiagnostics | Общая таблица, используемая несколькими службами для хранения журналов ресурсов. Журналы ресурсов из Azure Cosmos DB можно определить по `MICROSOFT.DOCUMENTDB`.   |
| AzureActivity    | Общая таблица, в которой хранятся все записи из журнала действий. 


> [!IMPORTANT]
> Если выбрать в меню Azure Cosmos DB пункт **Журналы**, откроется Log Analytics с текущей базой данных Azure Cosmos в качестве области запроса. Это означает, что запросы к журналам будут содержать данные только из этого ресурса. Если требуется выполнить запрос, включающий данные из других баз данных или служб Azure, выберите в меню **Azure Monitor** пункт **Журналы**. Подробные сведения см. в статье [Область запросов журнала и временной диапазон в Azure Monitor Log Analytics](../azure-monitor/log-query/scope.md).

### <a name="azure-cosmos-db-log-analytics-queries-in-azure-monitor"></a>Запросы Log Analytics для Azure Cosmos DB в Azure Monitor

Ниже приведены некоторые запросы, которые можно ввести в поле **поиска по журналам**, чтобы выполнить мониторинг контейнеров Azure Cosmos. Эти запросы поддерживают [новый язык](../log-analytics/log-analytics-log-search-upgrade.md).

Ниже приведены запросы, которые можно использовать для мониторинга баз данных Azure Cosmos.

* Для запроса всех журналов диагностики из Azure Cosmos DB за указанный период времени:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests"

    ```

* Для запроса всех операций, сгруппированных по ресурсу:

    ```Kusto
    AzureActivity 
    | where ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests" 
    | summarize count() by Resource

    ```

* Для запроса всех действий пользователя, сгруппированных по ресурсу:

    ```Kusto
    AzureActivity 
    | where Caller == "test@company.com" and ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests" 
    | summarize count() by Resource
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

- Справку по журналам и метрикам, создаваемым Azure Cosmos DB, см. в [справочнике по данным мониторинга Azure Cosmos DB](monitor-cosmos-db-reference.md).
- Подробные сведения о мониторинге ресурсов Azure см. в статье [Мониторинг ресурсов Azure с помощью Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md).
