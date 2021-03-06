---
title: Выходные данные Azure Stream Analytics в Azure Cosmos DB
description: Из этой статьи вы узнаете, как с помощью Azure Stream Analytics сохранять выходные данные в Azure Cosmos DB в формате JSON, что позволяет архивировать данные и уменьшать задержки запросов в отношении неструктурированных данных JSON.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/11/2019
ms.custom: seodec18
ms.openlocfilehash: cf6b94418516f681bf6c782fe02f3434faa5374e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426277"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Выходные данные Azure Stream Analytics в Azure Cosmos DB  
Azure Stream Analytics могут ориентироваться [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) для выходных данных JSON, что позволяет архивировать данные и запросы с низкой задержкой для неструктурированных данных JSON. В этом документе представлены некоторые рекомендации по реализации данной конфигурации.

Если вы не знакомы с Azure Cosmos DB, ознакомьтесь с [документацией по Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) , чтобы приступить к работе. 

> [!Note]
> В настоящее время Stream Analytics поддерживает подключение к Azure Cosmos DB только через *API SQL*.
> Другие API Azure Cosmos DB в данный момент не поддерживаются. Если указать Stream Analytics Azure Cosmos DB учетных записей, созданных с помощью других API-интерфейсов, данные могут быть неправильно сохранены. 

## <a name="basics-of-azure-cosmos-db-as-an-output-target"></a>Основные сведения о Azure Cosmos DB в качестве целевого объекта вывода
Azure Cosmos DB выходные данные в Stream Analytics позволяет записывать результаты обработки потока в виде выходных данных JSON в контейнеры Azure Cosmos DB. 

Stream Analytics не создает контейнеры в базе данных. Вместо этого необходимо создать их заранее. После этого можно контролировать стоимость выставления счетов за Azure Cosmos DB контейнеры. Вы также можете настроить производительность, согласованность и емкость контейнеров непосредственно с помощью [Azure Cosmos DB API](https://msdn.microsoft.com/library/azure/dn781481.aspx).

> [!Note]
> Необходимо добавить 0.0.0.0 в список разрешенных IP-адресов брандмауэра Azure Cosmos DB.

В следующих разделах подробно описаны некоторые параметры контейнера для Azure Cosmos DB.

## <a name="tuning-consistency-availability-and-latency"></a>Согласованность, доступность и задержка настройки
В соответствии с требованиями приложения Azure Cosmos DB позволяет точно настраивать базу данных и контейнеры и принимать компромиссы между согласованностью, доступностью, задержкой и пропускной способностью. 

В зависимости от того, какие уровни согласованности чтения потребуются вашему сценарию для чтения и записи задержки, вы можете выбирать уровень согласованности в своей учетной записи базы данных. Можно повысить пропускную способность, увеличив количество единиц запроса (RUs) в контейнере. 

Кроме того, по умолчанию Azure Cosmos DB позволяет выполнять синхронное индексирование для каждой операции CRUD в контейнере. Это еще один полезный параметр для управления производительностью записи и чтения в Azure Cosmos DB. 

Дополнительные сведения см. в статье [изменение уровней согласованности базы данных и запросов](../cosmos-db/consistency-levels.md) .

## <a name="upserts-from-stream-analytics"></a>Вставка и обновление Upsert в Stream Analytics
Stream Analytics интеграция с Azure Cosmos DB позволяет вставлять или обновлять записи в контейнере на основе заданного столбца **идентификатора документа** . Это также называется *Upsert*.

Stream Analytics использует подход оптимистичного Upsert. Обновления происходят только в случае, если вставка завершается ошибкой с конфликтом ИДЕНТИФИКАТОРов документов. 

С уровнем совместимости 1,0 Stream Analytics выполняет это обновление как операцию исправления, поэтому оно обеспечивает частичные обновления документа. Stream Analytics добавляет новые свойства или заменяет существующее свойство постепенно. Однако изменения в значениях свойств массива в документе JSON приводят к перезаписи всего массива. То есть массив не объединяется. 

В 1,2 поведение Upsert изменяется для вставки или замены документа. В следующем разделе об уровне совместимости 1,2 описывается это поведение.

Если входящий документ JSON имеет существующее поле ID, это поле автоматически используется в качестве столбца **идентификаторов документа** в Azure Cosmos DB. Все последующие операции записи обрабатываются таким образом, что приводит к одной из следующих ситуаций:

- Уникальные идентификаторы — Вставка.
- Дубликаты идентификаторов и **идентификаторов документов** , присвоенные **идентификатору** Upsert.
- После первого документа не заданы дублирующиеся идентификаторы и **идентификатор документа** .

Если необходимо сохранить *все* документы, включая те, которые имеют дублирующийся идентификатор, переименуйте поле идентификатора в запросе (с помощью ключевого слова **as** ). Позвольте Azure Cosmos DB создать поле идентификатора или заменить его значением другого столбца (с помощью ключевого слова **as** или с помощью параметра **идентификатора документа** ).

## <a name="data-partitioning-in-azure-cosmos-db"></a>Секционирование данных в Azure Cosmos DB
Azure Cosmos DB автоматически масштабирует секции в зависимости от рабочей нагрузки. Поэтому мы рекомендуем использовать [неограниченные](../cosmos-db/partition-data.md) контейнеры в качестве подхода к секционированию данных. Когда Stream Analytics записывает данные в неограниченные контейнеры, он использует столько же параллельных модулей записи, сколько предыдущий шаг запроса или схема секционирования ввода.

> [!NOTE]
> Azure Stream Analytics поддерживает только неограниченные контейнеры с ключами секций на верхнем уровне. Например, `/region` поддерживается. Вложенные ключи разделов (например, `/region/name`) не поддерживаются. 

В зависимости от выбранного ключа раздела может появиться следующее _предупреждение_:

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

Важно выбрать свойство ключа секции, имеющее несколько уникальных значений, которое позволяет равномерно распределить рабочую нагрузку по этим значениям. В качестве естественного артефакта секционирования запросы, затрагивающие один и тот же ключ секции, ограничиваются максимальной пропускной способностью одной секции. 

Размер хранилища для документов, принадлежащих одному ключу раздела, ограничен 10 ГБ. Идеальный ключ секции — это тот, который часто встречается как фильтр в запросах и имеет достаточно элементов для обеспечения масштабируемости решения.

Ключ секции также является границей для транзакций в хранимых процедурах и триггерах для Azure Cosmos DB. Следует выбрать ключ секции, чтобы документы, которые выполняются в транзакциях, совместно используют одно и то же значение ключа секции. В статье [секционирование в Azure Cosmos DB](../cosmos-db/partitioning-overview.md) содержатся дополнительные сведения о выборе ключа секции.

Для фиксированных Azure Cosmos DB контейнеров Stream Analytics не позволяет увеличивать или уменьшать масштаб по мере заполнения. Они имеют верхний предел в 10 ГБ и 10 000 единиц запросов в секунду. Чтобы перенести данные из фиксированного контейнера в неограниченный контейнер (например, по крайней мере 1 000 единицы запросов в секунду и ключ секции), используйте [средство переноса данных](../cosmos-db/import-data.md) или [библиотеку веб-канала изменений](../cosmos-db/change-feed.md).

Возможность записи в несколько фиксированных контейнеров является устаревшей. Мы не рекомендуем использовать его для масштабирования задания Stream Analytics.

## <a name="improved-throughput-with-compatibility-level-12"></a>Улучшенная пропускная способность с уровнем совместимости 1,2
С уровнем совместимости 1,2 Stream Analytics поддерживает собственную интеграцию для выполнения операции записи в Azure Cosmos DB. Такая интеграция позволяет эффективно вести запись в Azure Cosmos DB, обеспечивая максимальную пропускную способность и эффективную обработку запросов регулирования. 

Улучшенный механизм записи доступен на новом уровне совместимости из-за разницы в Upsert поведении. С уровнями до 1,2 Upsert поведение заключается в вставке или объединении документа. В 1,2 поведение Upsert изменяется для вставки или замены документа.

С уровнями до 1,2 Stream Analytics использует пользовательскую хранимую процедуру для упаковки Upsert документов по ключу секции в Azure Cosmos DB. В этом случае пакет записывается как транзакция. Даже если одна запись достигает временной ошибки (регулирование), необходимо повторить весь пакет. Это делает сценарии с даже разумным регулированием относительно медленным.

В следующем примере показаны два идентичных задания Stream Analytics, которые считываются из одного и того же ввода концентраторов событий Azure. Оба Stream Analytics задания [полностью секционированы](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) с транзитным запросом и записываются в идентичные Azure Cosmos DB контейнеры. Метрики слева находятся в задании с уровнем совместимости 1,0. Метрики справа настраиваются с 1,2. Ключ секции Azure Cosmos DB контейнера — это уникальный идентификатор GUID, который поступает из события ввода.

![Сравнение метрик Stream Analytics](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

Частота входящих событий в концентраторах событий в два раза выше, чем Azure Cosmos DB контейнеров (20 000 RUs), настроенных для выполнения, поэтому регулирование ожидается в Azure Cosmos DB. Однако задание с 1,2 постоянно записывается с более высокой пропускной способностью (события вывода в минуту), а также с меньшим средним использованием SU%. В вашей среде это различие будет зависеть от нескольких других факторов. Эти факторы включают в себя выбор формата событий, входного события или размера сообщения, ключей секций и запроса.

![Сравнение метрик Azure Cosmos DB](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

В 1,2 Stream Analytics более интеллектуальные в использовании 100 процента доступной пропускной способности в Azure Cosmos DB с очень малой повторной отправкой от регулирования или ограничения частоты. Это обеспечивает более эффективное взаимодействие с другими рабочими нагрузками, такими как запросы, выполняющиеся в контейнере. Если вы хотите узнать, как Stream Analytics масштабируется с Azure Cosmos DB в качестве приемника для 1 000 10 000 сообщений в секунду, попробуйте использовать [Этот пример проекта Azure](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb).

Пропускная способность выходных данных Azure Cosmos DB идентична 1,0 и 1,1. Поскольку 1,2 в настоящее время не является значением по умолчанию, можно [задать уровень совместимости](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level) для Stream Analytics задания с помощью портала или с помощью [задания создания Stream Analytics REST API вызов](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job). *Настоятельно рекомендуется* использовать уровень совместимости 1,2 в Stream Analytics с Azure Cosmos DB.

## <a name="azure-cosmos-db-settings-for-json-output"></a>Параметры Azure Cosmos DB для выходных данных JSON

При использовании Azure Cosmos DB в качестве выходных данных в Stream Analytics для получения сведений выводится следующий запрос.

![Поля сведений для потока вывода Azure Cosmos DB](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|Поле           | Description|
|-------------   | -------------|
|Псевдоним выходных данных    | Псевдоним для ссылки на эти выходные данные в запросе Stream Analytics.|
|Subscription    | Подписка Azure.|
|Идентификатор учетной записи      | Имя или универсальный код ресурса (URI) конечной точки учетной записи Azure Cosmos DB.|
|Ключ учетной записи     | Общедоступный ключ доступа к учетной записи Azure Cosmos DB.|
|База данных        | Имя базы данных Azure Cosmos DB.|
|Имя контейнера | Имя контейнера, например `MyContainer`. Должен существовать один контейнер с именем `MyContainer`.  |
|Идентификатор документа     | Необязательный параметр. Имя столбца в выходных событиях используется как уникальный ключ, на котором должны основываться операции вставки или обновления. Если оставить его пустым, будут вставлены все события без параметров обновления.|

После настройки выходных данных Azure Cosmos DB их можно использовать в запросе в качестве целевого объекта [инструкции into](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics). При использовании вывода Azure Cosmos DB таким образом, [ключ секции необходимо задать явным образом](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#partitions-in-sources-and-sinks). 

Выходная запись должна содержать столбец с учетом регистра, названный после ключа секции в Azure Cosmos DB. Для достижения большей параллелизации оператору может потребоваться [Предложение PARTITION BY](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) , использующее тот же столбец.

Вот пример запроса:

```SQL
    SELECT TollBoothId, PartitionId
    INTO CosmosDBOutput
    FROM Input1 PARTITION BY PartitionId
``` 

## <a name="error-handling-and-retries"></a>Обработка ошибок и повторные попытки

Если временная ошибка, недоступность службы или регулирование происходит, когда Stream Analytics отправляет события в Azure Cosmos DB, Stream Analytics пытается завершить операцию неограниченно долго. Но это не попытается выполнить следующие сбои:

- Несанкционированный (код ошибки HTTP 401)
- NotFound (код ошибки HTTP 404)
- Запрещено (код ошибки HTTP 403)
- BadRequest (код ошибки HTTP 400)
