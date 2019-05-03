---
title: Обновление REST API службы "Поиск Azure" до последней версии — служба "Поиск Azure"
description: Просмотрите различия в версиях API и узнайте, какие действия необходимы для переноса имеющегося кода в последнюю версию REST API службы "Поиск Azure".
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.openlocfilehash: 286d8bbc01b5916e842c196aed5a49ef1c76bc3c
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025199"
---
# <a name="upgrade-to-the-latest-azure-search-service-rest-api-version"></a>Обновление REST API службы "Поиск Azure" до последней версии
Если вы используете предыдущую версию [API REST службы поиска Azure](https://docs.microsoft.com/rest/api/searchservice/), эта статья поможет вам обновить приложение для использования последней общедоступной версии API, 2019-05-06.

Версия 2019-05-06-интерфейса REST API содержит некоторые отличия от более ранних версий. Эти отличия в основном обратно совместимы, поэтому изменение кода потребует минимальных усилий в зависимости от версии, которая использовался ранее. [Действия по обновлению](#UpgradeSteps) перечислены изменения кода, необходимые для использования новых функций.

> [!NOTE]
> Экземпляр службы поиска Azure поддерживает API REST диапазон версий, включая более ранних версий. Вы можете использовать эти версии API, но мы рекомендуем, переносе кода до последней версии, можно получить доступ к новой возможности.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2019-05-06"></a>Новые возможности в версии 2019-05-06
Версия 2019-05-06 является последней общедоступной версии API REST службы поиска Azure. Функции, которые перешли на общедоступной состояние в этой версии API:

* [Автозаполнение](index-add-suggesters.md) typeahead возможность завершения ввода частично определенное условие.

* [Сложные типы](search-howto-complex-data-types.md) обеспечивает встроенную поддержку структурированный объект данных в индекс службы поиска Azure.

* [Синтаксический анализ режимов JsonLines](search-howto-index-json-blobs.md), которое входит BLOB-объектов Azure индексирования, создает один документ поиска по сущности JSON, который отделяется символом новой строки.

* [Когнитивный поиск](cognitive-search-concept-intro.md) обеспечивает индексирование, использующего модуль искусственного Интеллекта обогащение из Cognitive Services.

Несколько предварительных выпусков компонентов совпадают с этого обновления общедоступной. Чтобы просмотреть список новых возможностей предварительной версии, см. в разделе [версия api REST поиска 2019-05-06-Preview](search-api-preview.md).

## <a name="breaking-changes"></a>Критические изменения

Существующий код, содержащий следующие функциональные возможности произойдет останов на api-version = 2019-05-06.

### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Индексатор для Azure Cosmos DB — источник данных является теперь «type»: «cosmosdb»

Если вы используете [индексатора Cosmos DB](search-howto-index-cosmosdb.md ), необходимо изменить `"type": "documentdb"` для `"type": "cosmosdb"`.

### <a name="indexer-execution-result-errors-no-longer-have-status"></a>Ошибки результат выполнения индексатора больше не имеют состояние

Ошибка структуры для выполнения индексатора ранее было `status` элемент. Этот элемент был удален, так как он был не предоставляет полезной информации.

### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>Источник данных индексатора API больше не возвращает строки подключения

API версии 2019-05-06 и 2019-05-06-Preview и более поздних версий, источника данных, API больше не возвращает из строки подключения в ответе любую операцию REST. В предыдущих версиях API для источников данных, созданных с помощью запроса POST, поиск Azure **201** следуют ответ OData, который содержит строку подключения в виде обычного текста.

### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>С именем распознавание сущностей, теперь не поддерживается когнитивные навыки

При вызове метода [распознавание сущностей имя](cognitive-search-skill-named-entity-recognition.md) навыков в коде, вызов завершится ошибкой. Функциональные возможности замены [распознавание сущностей](cognitive-search-skill-entity-recognition.md). Можно для замены навыков ссылки с других изменений нет. Подпись API является одинаковым для обеих версий. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Действия по обновлению
При обновлении с предыдущей версии общедоступной версии, 2017-11-11 или 2016-09-01, возможно, не придется изменять в коде, отличное от номер версии изменяется. Изменение кода может потребоваться только в следующих случаях:

* Код завершается ошибкой, если в ответе API возвращаются нераспознанные свойства. По умолчанию приложение должно игнорировать свойства, которые не распознаются.

* Код сохраняет запросы API и пытается повторно отправить их в новую версию API. Например, это может произойти, если приложение сохраняет маркеры продолжения, возвращенные API поиска (см. сведения об `@search.nextPageParameters` в [справочнике по API поиска](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)).

Если одна из этих ситуаций относится к вам, может потребоваться изменить код соответствующим образом. В противном случае не понадобится вносить изменения только если вы хотите начать использовать [новые функции](#WhatsNew) версии 2019-05-06.

При обновлении от предварительной версии API, оно также применяется, но также необходимо иметь в виду, что некоторые возможности предварительной версии недоступны в версии 2019-05-06:

* [«Более мне нравится это» запросы](search-more-like-this.md) продолжать будет доступно только для предварительного просмотра.

Код использует эти функции, вы не сможете обновить до версии 2019-05-06 API не отключите их.

> [!IMPORTANT]
> Предварительные версии API предназначены для тестирования и ознакомления. Они не должны использоваться в рабочей среде.
> 

### <a name="upgrading-complex-types"></a>Обновление сложных типов

Если код использует сложные типы с более старыми версиями API предварительной версии 2017-11-11-Preview или 2016-09-01-Preview, существуют некоторые новые и измененные ограничения в версии 2019-05-06 из которых вы должны учитывать:

+ Ограничения на глубину вложенных полей и количество сложных коллекций на индекс снизилась. Если вы создали индексы, превышающие эти ограничения при использовании версий api предварительной версии, любая попытка изменить или повторно создать их с помощью API версии 2019-05-06 завершится ошибкой. Если это применимо к вам, измените схему, чтобы соблюдалось новых ограничений, а затем перестройте индекс будет необходимо.

+ В api-version 2019-05-06 на количество элементов сложных коллекций в документе имеется новое ограничение. Если вы создали индексы с документами, превышение этих ограничений, с помощью версии api предварительной версии, любая попытка повторной индексации данных с помощью api-version 2019-05-06 завершится ошибкой. Если это применимо к вам, необходимо уменьшить количество элементов коллекции сложных на документ до повторного индексирования данных.

Дополнительные сведения см. в разделе [ограничениях службы для службы поиска Azure](search-limits-quotas-capacity.md).

### <a name="how-to-upgrade-an-old-complex-type-structure"></a>Как обновить старую структуру сложного типа

Если ваш код использует сложные типы с одним из старых версий API предварительной версии, вы можете использовать, формат определения индекса, который выглядит следующим образом:

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

Более новый формат дерева для определения полей индекса появилась в версии 2017-11-11-предварительной версии API. В новом формате каждое поле сложных имеет коллекцию полей которой определены его вложенные поля. В API версии 2019-05-06 этот новый формат используется в монопольном режиме, и попытка создания или обновления индекса с использованием старого формата не удастся. Если у вас есть индексы, созданные с использованием старого формата, необходимо использовать API версии 2017-11-11-предварительной версии для их обновления до нового формата, прежде чем ими можно управлять с помощью API версии 2019-05-06.

Вы можете обновить «плоский» индексы в новый формат, выполнив следующие действия с помощью API версии 2017-11-11-Предварительная версия:

1. Выполните запрос GET для извлечения индекса. Если он уже в новом формате, все готово.

2. Перевод индекс из «плоский» формата в новый формат. Вам придется написать код для этого, так как доступно без образца кода во время написания этой статьи.

3. Выполните запрос PUT для обновления индекса в новый формат. Убедитесь в том, что не позволяют менять другие сведения индекса, например возможность поиска/filterability полей, так как это не допускается в API для обновления индекса.

> [!NOTE]
> Это не позволяет использовать несколько индексов, созданных с помощью старого формата «плоский» на портале Azure. Выполните обновление индексов в представлении «плоский» в представление «дерево», как можно скорее.

## <a name="next-steps"></a>Дальнейшие действия

Просмотрите в справочной документации API REST службы поиска Azure. Если у вас возникли проблемы, обратиться за помощью на [StackOverflow](https://stackoverflow.com/) или [обратитесь в службу поддержки](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Справочник по API REST службы поиска](https://docs.microsoft.com/rest/api/searchservice/)

