---
title: Обновление REST API версий
titleSuffix: Azure Cognitive Search
description: Ознакомьтесь с различиями в версиях API и Узнайте, какие действия необходимы для переноса существующего кода в последнюю версию REST API службы Когнитивный поиск Azure.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: edb45eebc2c4eacc2f30d13988943f097a7190fa
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112170"
---
# <a name="upgrade-to-the-latest-azure-cognitive-search-service-rest-api-version"></a>Обновление до последней версии REST API службы Когнитивный поиск Azure

Если вы используете предыдущую версию [REST API поиска](https://docs.microsoft.com/rest/api/searchservice/), эта статья поможет вам обновить приложение для использования новейшей общедоступной версии API, 2019-05-06.

Версия 2019-05-06 REST API содержит некоторые изменения из более ранних версий. Эти отличия в основном обратно совместимы, поэтому изменение кода потребует минимальных усилий в зависимости от версии, которая использовался ранее. В [статье описываются](#UpgradeSteps) изменения кода, необходимые для использования новых функций.

> [!NOTE]
> Экземпляр службы Когнитивный поиск Azure поддерживает ряд версий REST API, включая более ранние. Вы можете продолжать использовать эти версии API, но мы рекомендуем перенести код в самую новую версию, чтобы иметь возможность получить доступ к новым возможностям.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2019-05-06"></a>Новые возможности версии 2019-05-06
Версия 2019-05-06 является самым новым общедоступным выпуском REST API. Функции, переведенные в общедоступное состояние в этой версии API, включают:

* [Автозаполнение](index-add-suggesters.md) — это функция typeahead, которая завершает частично заданное условие ввода.

* [Сложные типы](search-howto-complex-data-types.md) обеспечивают встроенную поддержку структурированных данных объекта в индексе поиска.

* [Режимы синтаксического анализа жсонлинес](search-howto-index-json-blobs.md), часть индексирования больших двоичных объектов Azure, создают один документ поиска для каждой сущности JSON, разделенной символом новой строки.

* [Обогащение искусственного интеллекта](cognitive-search-concept-intro.md) обеспечивает индексирование, которое использует механизмы ОБОГАЩЕНия искусственного интеллекта Cognitive Services.

Несколько выпусков функций предварительной версии совпадают с общедоступным обновлением. Список новых функций предварительной версии см. в статье [Поиск по API-версии 2019-05-06-Preview](search-api-preview.md).

## <a name="breaking-changes"></a>Критические изменения

Существующий код, содержащий следующие функциональные возможности, будет прерываться на API-Version = 2019-05-06.

### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Индексатор для Azure Cosmos DB-DataSource теперь — "Type": "cosmosdb"

Если используется [индексатор Cosmos DB](search-howto-index-cosmosdb.md ), необходимо изменить `"type": "documentdb"` на `"type": "cosmosdb"`.

### <a name="indexer-execution-result-errors-no-longer-have-status"></a>Ошибки результатов выполнения индексатора больше не имеют состояния

Структура ошибок для выполнения индексатора ранее имела элемент `status`. Этот элемент был удален, так как он не предоставляет полезные сведения.

### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>API источника данных индексатора больше не возвращает строки подключения

Начиная с версий API 2019-05-06 и 2019-05-06-Preview, API источника данных больше не возвращает строки подключения в ответ на любые операции RESTFUL. В предыдущих версиях API для источников данных, созданных с помощью POST, Когнитивный поиск Azure вернул **201** , а затем ответ OData, содержащий строку подключения в виде обычного текста.

### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>Теперь недоступно распознавание распознавания именованных сущностей

Если вы вызовите в коде навык [распознавания сущности Name](cognitive-search-skill-named-entity-recognition.md) , вызов завершится ошибкой. Функция замены — это [Распознавание сущностей](cognitive-search-skill-entity-recognition.md). Вы можете заменить ссылку на навык без других изменений. Сигнатура API одинакова для обеих версий. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Действия по обновлению
Если вы обновляете предыдущую версию общедоступной версии 2017-11-11 или 2016-09-01, вам, возможно, не придется вносить какие-либо изменения в код, кроме изменения номера версии. Изменение кода может потребоваться только в следующих случаях:

* Код завершается ошибкой, если в ответе API возвращаются нераспознанные свойства. По умолчанию приложение должно игнорировать свойства, которые не распознаются.

* Код сохраняет запросы API и пытается повторно отправить их в новую версию API. Например, это может произойти, если приложение сохраняет маркеры продолжения, возвращенные API поиска (см. сведения об `@search.nextPageParameters` в [справочнике по API поиска](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)).

Если одна из этих ситуаций относится к вам, может потребоваться изменить код соответствующим образом. В противном случае никакие изменения не требуются, если вы не хотите использовать [новые функции](#WhatsNew) версии 2019-05-06.

Если вы выполняете обновление с версии API предварительного просмотра, приведенные выше действия также применимы, но необходимо помнить, что некоторые функции предварительной версии недоступны в версия 2019-05-06:

* ["Больше нравится" запросы](search-more-like-this.md)
* [Индексирование BLOB-объектов CSV](search-howto-index-csv-blobs.md)
* [Поддержка MongoDB API для индексаторов Cosmos DB](search-howto-index-cosmosdb.md)

Если в коде используются эти функции, вы не сможете выполнить обновление до версии API 2019-05-06 без удаления их использования.

> [!IMPORTANT]
> Предварительные версии API предназначены для тестирования и ознакомления. Они не должны использоваться в рабочей среде.
> 

### <a name="upgrading-complex-types"></a>Обновление сложных типов

Если в коде используются сложные типы с предыдущими версиями API предварительной версии 2017-11-11-Preview или 2016-09-01-Preview, то необходимо помнить о новых и измененных ограничениях в версии 2019-05-06.

+ Ограничения глубины вложенных полей и количества сложных коллекций на индекс были сокращены. Если вы создали индексы, превышающие эти ограничения с помощью предварительных версий API, любая попытка обновить или повторно создать их с помощью API версии 2019-05-06 завершится ошибкой. Если это применимо к вам, необходимо будет переконструировать схему, чтобы она соответствовала новым ограничениям, а затем перестроить индекс.

+ В API-версии 2019-05-06 имеется новое ограничение на количество элементов сложных коллекций на документ. Если вы создали индексы с документами, превышающими эти ограничения с помощью предварительных версий API, любая попытка переиндексации этих данных с помощью API-версии 2019-05-06 завершится ошибкой. Если это применимо к вам, необходимо уменьшить количество элементов комплексной коллекции на документ перед повторным индексированием данных.

Дополнительные сведения см. в статье [ограничения службы для когнитивный Поиск Azure](search-limits-quotas-capacity.md).

### <a name="how-to-upgrade-an-old-complex-type-structure"></a>Обновление старой структуры сложного типа

Если в коде используются сложные типы с одной из предыдущих версий API предварительной версии, возможно, используется формат определения индекса, который выглядит следующим образом:

```json
{
  "name": "hotels",  
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.microsoft" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.microsoft" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true, "analyzer": "tagsAnalyzer" },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true },
    { "name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType" },
    { "name": "Address/StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
    { "name": "Address/City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)" }, 
    { "name": "Rooms/Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene" },
    { "name": "Rooms/Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "Rooms/Type", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
    { "name": "Rooms/BedOptions", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "Rooms/SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Rooms/Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "facetable": true, "analyzer": "tagsAnalyzer" }
  ]
}  
```

Более новый древовидный формат для определения полей индекса был представлен в API версии 2017-11-11-Preview. В новом формате каждое сложное поле содержит коллекцию Fields, в которой определены его вложенные поля. В API версии 2019-05-06 этот новый формат используется исключительно, и попытка создать или обновить индекс с использованием старого формата завершится ошибкой. При наличии индексов, созданных с использованием старого формата, необходимо использовать API версии 2017-11-11-Preview, чтобы обновить их до нового формата, прежде чем можно будет управлять ими с помощью API версии 2019-05-06.

Вы можете обновить "неструктурированные" индексы до нового формата, выполнив следующие действия с помощью API версии 2017-11-11-Preview:

1. Выполните запрос GET для получения индекса. Если он уже есть в новом формате, то все готово.

2. Преобразование индекса из плоского формата в новый формат. Вам потребуется написать код для этого, так как на момент написания этой статьи доступ к образцу кода невозможен.

3. Выполните запрос на размещение, чтобы обновить индекс до нового формата. Не изменяйте другие сведения об индексе, такие как возможность поиска и фильтрацию полей, поскольку это запрещено API-интерфейсом обновления индекса.

> [!NOTE]
> Нельзя управлять индексами, созданными со старым форматом "плоский", из портал Azure. Обновите индексы с "плоского" представления до "дерева" в самом раннем удобства.

## <a name="next-steps"></a>Дополнительная информация

Ознакомьтесь с справочной документацией по поиску REST API. Если вы столкнулись с проблемами, попросите помощь по [StackOverflow](https://stackoverflow.com/) или [обратитесь в службу поддержки](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Справочник по службе поиска REST API](https://docs.microsoft.com/rest/api/searchservice/)

