---
title: Сопоставление обогащенных полей входных данных, поддерживающих когнитивный поиск, с полями выходных данных в службе "Поиск Azure"
description: Извлечение и обогащение полей источников данных, а также сопоставление их с полями выходных данных в индекс поиска Azure.
manager: nitinme
author: luiscabrer
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: 0e422d2453fe12280da9e9b0b5dc7aa391f97b9f
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186407"
---
# <a name="how-to-map-enriched-fields-to-a-searchable-index"></a>Сопоставление обогащенных полей с индексом, поддерживающим поиск

В этой статье вы узнаете, как сопоставлять обогащенные поля входных данных с полями выходных данных в индексе, поддерживающем поиск. После [определения набора навыков](cognitive-search-defining-skillset.md) вы должны сопоставить поля выходных данных любого навыка, который напрямую вносит значения в заданное поле в ваш индекс поиска. Сопоставление полей является обязательным этапом для перемещения содержимого из обогащенных документов в индекс.


## <a name="use-outputfieldmappings"></a>Использование outputFieldMappings
Чтобы сопоставить поля, добавьте `outputFieldMappings` в определение индексатора, как показано ниже.

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```

Текст запроса имеет следующую структуру:

```json
{
    "name": "myIndexer",
    "dataSourceName": "myDataSource",
    "targetIndexName": "myIndex",
    "skillsetName": "myFirstSkillSet",
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction": {
                "name": "base64Encode"
            }
        }
    ],
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/content/organizations/*/description",
            "targetFieldName": "descriptions"
        },
        {
            "sourceFieldName": "/document/content/organizations",
            "targetFieldName": "orgNames"
        },
        {
            "sourceFieldName": "/document/content/sentiment",
            "targetFieldName": "sentiment"
        }
    ]
}
```
Для каждого сопоставления поля выходных данных, задайте имя обогащенного поля (sourceFieldName) и имя поля, как указано в индексе (targetFieldName).

Путь в sourceFieldName может представлять один элемент или несколько элементов. В приведенном выше примере ```/document/content/sentiment``` представляет одно числовое значение, а ```/document/content/organizations/*/description``` представляет несколько описаний организации. В случаях с несколькими элементами они преобразуются в массив, содержащий все элементы. Более конкретно, данные для примера ```/document/content/organizations/*/description``` в поле *описаний* будут выглядеть как плоский массив описаний до его индексации:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```
## <a name="next-steps"></a>Следующие шаги
После сопоставления обогащенных полей с полями, поддерживающими поиск, для каждого поля, поддерживающего поиск, можно задать атрибуты поля [в качестве части определения индекса](search-what-is-an-index.md).

Дополнительные сведения о сопоставлении полей см. в статье [Сопоставление полей в индексаторах Поиска Azure](search-indexer-field-mappings.md).
