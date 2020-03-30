---
title: Вход карты в поля вывода
titleSuffix: Azure Cognitive Search
description: Извлекайте и обогащайте поля исходных данных и отображайте карты к полям вывода в индексе когнитивного поиска Azure.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f0537af684632a08a39e3e681900d62238365073
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74280967"
---
# <a name="how-to-map-ai-enriched-fields-to-a-searchable-index"></a>Как сопоставить поля, обогащенные ИИ, в индекс поиска

В этой статье вы узнаете, как сопоставлять обогащенные поля входных данных с полями выходных данных в индексе, поддерживающем поиск. После [определения набора навыков](cognitive-search-defining-skillset.md) вы должны сопоставить поля выходных данных любого навыка, который напрямую вносит значения в заданное поле в ваш индекс поиска. 

Картирование полей вывода требуется для перемещения содержимого из обогащенных документов в индекс.  Обогащенный документ действительно является деревом информации, и даже если в индексе есть поддержка сложных типов, иногда может потребоваться превратить информацию из обогащенного дерева в более простой тип (например, массив строк). Отображение полей вывода позволяет выполнять преобразования формы данных путем уплощения информации.

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

Для каждого картографии поля вывода установите местоположение данных в обогащенном дереве документа (sourceFieldName) и название поля, укоторозначаемое в индексе (targetFieldName).

## <a name="flattening-information-from-complex-types"></a>Информация о разглашении из сложных типов 

Путь в sourceFieldName может представлять один элемент или несколько элементов. В приведенном выше примере ```/document/content/sentiment``` представляет одно числовое значение, а ```/document/content/organizations/*/description``` представляет несколько описаний организации. 

В случаях с несколькими элементами они преобразуются в массив, содержащий все элементы. 

Более конкретно, данные для примера ```/document/content/organizations/*/description``` в поле *описаний* будут выглядеть как плоский массив описаний до его индексации:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```

Это важный принцип, поэтому мы привезём другой пример. Представьте, что у вас есть множество сложных типов как часть дерева обогащения. Допустим, есть член, называемый customEntities, который имеет множество сложных типов, как описано ниже.

```json
"document/customEntities": 
[
    {
        "name": "heart failure",
        "matches": [
            {
                "text": "heart failure",
                "offset": 10,
                "length": 12,
                "matchDistance": 0.0
            }
        ]
    },
    {
        "name": "morquio",
        "matches": [
            {
                "text": "morquio",
                "offset": 25,
                "length": 7,
                "matchDistance": 0.0
            }
        ]
    }
    //...
]
```

Предположим, что ваш индекс имеет поле под названием "болезни" коллекции типов (Edm.String), где вы хотели бы сохранить каждое из имен сущностей. 

Это можно сделать легко,\*используя символ «« следующим образом:

```json
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/customEntities/*/name",
            "targetFieldName": "diseases"
        }
    ]
```

Эта операция просто "утихнет" каждое из имен элементов пользовательских сущностей в единый массив строк, как это:

```json
  "diseases" : ["heart failure","morquio"]
```

## <a name="next-steps"></a>Дальнейшие действия
После сопоставления обогащенных полей с полями, поддерживающими поиск, для каждого поля, поддерживающего поиск, можно задать атрибуты поля [в качестве части определения индекса](search-what-is-an-index.md).

Для получения дополнительной информации о картографии полей смотрите [картографы полей в индексах когнитивного поиска Azure.](search-indexer-field-mappings.md)
