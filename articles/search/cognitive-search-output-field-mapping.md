---
title: Преобразование входных данных в поля вывода
titleSuffix: Azure Cognitive Search
description: Извлеките и дополните поля исходных данных и сопоставьте их с полями вывода в индексе Azure Когнитивный поиск.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d2d5e717154d16cc5579c1495aff9c1eebf54b17
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132373"
---
# <a name="how-to-map-ai-enriched-fields-to-a-searchable-index"></a>Как сопоставлять ИСКУССТВЕНные поля с индексом с возможностью поиска

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
## <a name="next-steps"></a>Дополнительная информация
После сопоставления обогащенных полей с полями, поддерживающими поиск, для каждого поля, поддерживающего поиск, можно задать атрибуты поля [в качестве части определения индекса](search-what-is-an-index.md).

Дополнительные сведения о сопоставлении полей см. [в разделе сопоставления полей в Azure когнитивный Поиск индексаторы](search-indexer-field-mappings.md).
