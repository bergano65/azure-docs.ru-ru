---
title: Предварительно созданная сущность географического уровня 2 (LUIS)
titleSuffix: Azure Cognitive Services
description: В этой статье описана предварительно созданная сущность GeographyV2 в службе "Распознавание речи" (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 04bc3019a55920351b0e91c87e63b8309d94e34c
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677610"
---
# <a name="geographyv2-prebuilt-entity-for-a-luis-app"></a>Предварительно созданная сущность GeographyV2 для приложения LUIS
Предварительно созданная сущность GeographyV2 определяет расположения. Так как эта сущность уже обучена, добавлять примеры высказываний, содержащие сущность GeographyV2, в намерения приложения не нужно. Сущность GeographyV2 поддерживает английский [язык и региональные параметры](luis-reference-prebuilt-entities.md).

## <a name="subtypes"></a>Подтипы
Географические расположения имеют подтипы.

|Subtype|Цель|
|--|--|
|`poi`|достопримечательность|
|`city`|название города|
|`countryRegion`|название страны или региона|
|`continent`|название континента|
|`state`|название области, республики, края, округа|


## <a name="resolution-for-geographyv2-entity"></a>Разрешение для сущности GeographyV2

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Ответ конечной точки прогнозирования v2](#tab/V2)

В следующем примере показано разрешение для сущности **builtin.geographyV2**.

```json
{
    "query": "Carol is visiting the sphinx in gizah egypt in africa before heading to texas",
    "topScoringIntent": {
        "intent": "None",
        "score": 0.8008023
    },
    "intents": [
        {
            "intent": "None",
            "score": 0.8008023
        }
    ],
    "entities": [
        {
            "entity": "the sphinx",
            "type": "builtin.geographyV2.poi",
            "startIndex": 18,
            "endIndex": 27
        },
        {
            "entity": "gizah",
            "type": "builtin.geographyV2.city",
            "startIndex": 32,
            "endIndex": 36
        },
        {
            "entity": "egypt",
            "type": "builtin.geographyV2.countryRegion",
            "startIndex": 38,
            "endIndex": 42
        },
        {
            "entity": "africa",
            "type": "builtin.geographyV2.continent",
            "startIndex": 47,
            "endIndex": 52
        },
        {
            "entity": "texas",
            "type": "builtin.geographyV2.state",
            "startIndex": 72,
            "endIndex": 76
        },
        {
            "entity": "carol",
            "type": "builtin.personName",
            "startIndex": 0,
            "endIndex": 4
        }
    ]
} 
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 прогнозирование ответа конечной точки](#tab/V3)

Следующий код JSON относится к параметру `verbose`, для которого задано значение `false`:

```json
{
    "query": "Carol is visiting the sphinx in gizah egypt in africa before heading to texas",
    "prediction": {
        "normalizedQuery": "carol is visiting the sphinx in gizah egypt in africa before heading to texas",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.5115521
            }
        },
        "entities": {
            "geographyV2": [
                "the sphinx",
                "gizah",
                "egypt",
                "africa",
                "texas"
            ]
        }
    }
}
```

Следующий код JSON относится к параметру `verbose`, для которого задано значение `true`:

```json
{
    "query": "Carol is visiting the sphinx in gizah egypt in africa before heading to texas",
    "prediction": {
        "normalizedQuery": "carol is visiting the sphinx in gizah egypt in africa before heading to texas",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.5115521
            }
        },
        "entities": {
            "geographyV2": [
                "the sphinx",
                "gizah",
                "egypt",
                "africa",
                "texas"
            ],
            "$instance": {
                "geographyV2": [
                    {
                        "type": "builtin.geographyV2",
                        "text": "the sphinx",
                        "startIndex": 18,
                        "length": 10,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    },
                    {
                        "type": "builtin.geographyV2",
                        "text": "gizah",
                        "startIndex": 32,
                        "length": 5,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    },
                    {
                        "type": "builtin.geographyV2",
                        "text": "egypt",
                        "startIndex": 38,
                        "length": 5,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    },
                    {
                        "type": "builtin.geographyV2",
                        "text": "africa",
                        "startIndex": 47,
                        "length": 6,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    },
                    {
                        "type": "builtin.geographyV2",
                        "text": "texas",
                        "startIndex": 72,
                        "length": 5,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    }
                ]
            }
        }
    }
}
```

* * * 

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о [конечной точке прогнозирования v3](luis-migration-api-v3.md).

См. дополнительные сведения о сущностях [email](luis-reference-prebuilt-email.md), [number](luis-reference-prebuilt-number.md) и [ordinal](luis-reference-prebuilt-ordinal.md). 
