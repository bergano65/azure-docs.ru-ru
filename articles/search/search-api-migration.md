---
title: Обновление версий REST API
titleSuffix: Azure Cognitive Search
description: Просмотрите различия в версиях API и узнайте, какие действия необходимы для переноса существующего кода в новейшую версию Rest API службы cognitive Search Azure.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: edb45eebc2c4eacc2f30d13988943f097a7190fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112170"
---
# <a name="upgrade-to-the-latest-azure-cognitive-search-service-rest-api-version"></a>Обновление до последней версии Azure Cognitive Search REST API

Если вы используете предыдущую версию [Search REST API,](https://docs.microsoft.com/rest/api/searchservice/)эта статья поможет вам обновить приложение, чтобы использовать новейшую общедоступную версию API, 2019-05-06.

Версия 2019-05-06 REST API содержит некоторые изменения от предыдущих версий. Эти отличия в основном обратно совместимы, поэтому изменение кода потребует минимальных усилий в зависимости от версии, которая использовался ранее. [Шаги по обновлению](#UpgradeSteps) определяют изменения кода, необходимые для использования новых функций.

> [!NOTE]
> Экземпляр службы когнитивного поиска Azure поддерживает ряд версий REST API, включая предыдущие версии. Вы можете продолжать использовать эти версии API, но мы рекомендуем перейти код на новейшую версию, чтобы вы могли получить доступ к новым возможностям.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2019-05-06"></a>Что нового в версии 2019-05-06
Версия 2019-05-06 является новейшим общедоступным выпуском REST API. Функции, которые перешли к общедоступному статусу в этой версии API, включают:

* [Автозаполнение](index-add-suggesters.md) — это функция typeahead, которая завершает частично указанный термин ввода.

* [Сложные типы](search-howto-complex-data-types.md) обеспечивают родную поддержку структурированных данных объектов в индексе поиска.

* [Режимы разбора JsonLines,](search-howto-index-json-blobs.md)часть индексирования Azure Blob, создают один поисковый документ на объект JSON, который разделен новой строкой.

* [Обогащение ИИ](cognitive-search-concept-intro.md) обеспечивает индексацию, которая использует двигатели обогащения ИИ Cognitive Services.

Несколько выпусков функций предварительного просмотра совпадают с этим общедоступным обновлением. Чтобы ознакомиться со списком новых функций предварительного просмотра, [см.](search-api-preview.md)

## <a name="breaking-changes"></a>Критические изменения

Существующий код, содержащий следующие функциональные возможности, будет ломаться на api-версии-2019-05-06.

### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Индексер для Azure Cosmos DB - источник данных теперь "тип": "космос"

Если вы используете [индексатор Cosmos DB,](search-howto-index-cosmosdb.md )вы должны измениться `"type": "documentdb"` на `"type": "cosmosdb"`.

### <a name="indexer-execution-result-errors-no-longer-have-status"></a>Ошибки результатов выполнения индекса больше не имеют статуса

Структура ошибки для выполнения индекса ранее имела `status` элемент. Этот элемент был удален, поскольку он не предоставляет полезной информации.

### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>API источника данных индекса больше не возвращает строки соединения

Из версий API 2019-05-06 и 2019-05-06-Preview onwards, API источника данных больше не возвращает строки соединения в ответ на любую операцию REST. В предыдущих версиях API для источников данных, созданных с помощью POST, Azure Cognitive Search вернул **201,** а затем ответ OData, который содержал строку соединения в простом тексте.

### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>Именованные образования Распознавание когнитивных навыков в настоящее время прекращено

Если вы называете [навыкраспознавания имен](cognitive-search-skill-named-entity-recognition.md) в коде, вызов не сдастся. Функциональность замены — [распознавание сущности.](cognitive-search-skill-entity-recognition.md) Вы должны быть в состоянии заменить навык ссылку без каких-либо других изменений. Подпись API одинакова для обеих версий. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Действия по обновлению
Если вы переходите из предыдущей версии GA, 2017-11-11 или 2016-09-01, вам, вероятно, не придется вносить какие-либо изменения в свой код, кроме как изменить номер версии. Изменение кода может потребоваться только в следующих случаях:

* Код завершается ошибкой, если в ответе API возвращаются нераспознанные свойства. По умолчанию приложение должно игнорировать свойства, которые не распознаются.

* Код сохраняет запросы API и пытается повторно отправить их в новую версию API. Например, это может произойти, если приложение сохраняет маркеры продолжения, возвращенные API поиска (см. сведения об `@search.nextPageParameters` в [справочнике по API поиска](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)).

Если одна из этих ситуаций относится к вам, может потребоваться изменить код соответствующим образом. В противном случае, никаких изменений не должно быть необходимым, если вы не хотите, чтобы начать использовать [новые функции](#WhatsNew) версии 2019-05-06.

Если вы обновляете от версии API предварительного просмотра, выше, также применяется, но вы также должны знать, что некоторые функции предварительного просмотра не доступны в версии 2019-05-06:

* [Запросы "Скорее всего".](search-more-like-this.md)
* [Индексирование больших двоичных объектов в формате CSV](search-howto-index-csv-blobs.md)
* [Поддержка MongoDB API для индексов Cosmos DB](search-howto-index-cosmosdb.md)

Если ваш код использует эти функции, вы не сможете перейти на версию API 2019-05-06, не удалив их использование.

> [!IMPORTANT]
> Предварительные версии API предназначены для тестирования и ознакомления. Они не должны использоваться в рабочей среде.
> 

### <a name="upgrading-complex-types"></a>Модернизация сложных типов

Если ваш код использует сложные типы со старыми версиями API предварительного просмотра 2017-11-11-Preview или 2016-09-01-Preview, в версии 2019-05-06 есть некоторые новые и измененные ограничения, о которых необходимо знать:

+ Снижены ограничения на глубину подполей и количество сложных коллекций на индекс. Если вы создали индексы, превышаюющие эти пределы, используя api-версии предварительного просмотра, любая попытка обновить или воссоздать их с помощью версии API 2019-05-06 не увенчается неудачей. Если это относится к вам, вам нужно будет перестроить схему, чтобы вписаться в новые пределы, а затем восстановить свой индекс.

+ Существует новый лимит в апи-версии 2019-05-06 по количеству элементов сложных коллекций на документ. Если вы создали индексы с документами, превышаютми эти пределы, используя апи-версии предварительного просмотра, любая попытка переиндексировать эти данные с помощью api-версии 2019-05-06 не увенчается неудачей. Если это относится к вам, вам необходимо уменьшить количество сложных элементов сбора в документе до переиндексации данных.

Для получения дополнительной [информации](search-limits-quotas-capacity.md)см.

### <a name="how-to-upgrade-an-old-complex-type-structure"></a>Как обновить старую структуру сложного типа

Если ваш код использует сложные типы с одной из старых версий API предварительного просмотра, вы можете использовать формат определения индекса, который выглядит следующим образом:

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

В версии API 2017-11-11-Preview был представлен новый древесный формат для определения полей индекса. В новом формате каждое сложное поле имеет коллекцию полей, где определяются его подполя. В версии API 2019-05-06 этот новый формат используется исключительно, и попытка создать или обновить индекс с использованием старого формата не увенчается провалом. Если у вас есть индексы, созданные с использованием старого формата, вам нужно будет использовать версию API 2017-11-11-Preview, чтобы обновить их до нового формата, прежде чем ими можно будет управлять с помощью версии API 2019-05-06.

Вы можете обновить "плоские" индексы до нового формата со следующими шагами с помощью версии API 2017-11-11-Preview:

1. Выполните запрос GET для получения индекса. Если он уже в новом формате, вы сделали.

2. Переведите индекс из «плоского» формата в новый. Для этого вам придется написать код, так как на момент написания этой статьи не было образца кода.

3. Выполните запрос PUT для обновления индекса до нового формата. Убедитесь в том, чтобы не изменять другие детали индекса, такие как доступность поиска/фильтруемость полей, так как это не допускается API индекса обновления.

> [!NOTE]
> Управлять индексами, созданными в старом «плоском» формате с портала Azure, невозможно. Пожалуйста, обновите свои индексы от "плоского" представления до "деревя" представления при первой необходимости.

## <a name="next-steps"></a>Дальнейшие действия

Просмотрите справочную документацию Search REST API. Если вы столкнулись с проблемами, обратитесь к нам за помощью по [StackOverflow](https://stackoverflow.com/) или [обратитесь в службу поддержки.](https://azure.microsoft.com/support/community/?product=search)

> [!div class="nextstepaction"]
> [Поисковая служба REST API Справка](https://docs.microsoft.com/rest/api/searchservice/)

