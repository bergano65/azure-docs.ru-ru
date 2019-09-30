---
title: Порядковый номер V2 предварительно созданная сущность — LUIS
titleSuffix: Azure Cognitive Services
description: В этой статье содержатся сведения о предварительно построенных сущностях с порядковым номером v2 в Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 48dcbd51190e747859f0172473c94b0caa296071
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677553"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>Порядковый номер V2 предварительно созданной сущности для приложения LUIS
Порядковый номер V2 расширяет [порядковый](luis-reference-prebuilt-ordinal.md) номер для предоставления относительных ссылок, таких как `next`, `last` и `previous`. Они не извлекаются с использованием порядкового номера предварительно построенной сущности.

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>Разрешение для предварительно построенной сущности с порядковым номером v2

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Ответ конечной точки прогнозирования v2](#tab/V2)

В следующем примере показано разрешение **встроенной сущности. ordinalV2** .

```json
{
    "query": "what is the second to last choice in the list",
    "topScoringIntent": {
        "intent": "None",
        "score": 0.823669851
    },
    "intents": [
        {
            "intent": "None",
            "score": 0.823669851
        }
    ],
    "entities": [
        {
            "entity": "the second to last",
            "type": "builtin.ordinalV2.relative",
            "startIndex": 8,
            "endIndex": 25,
            "resolution": {
                "offset": "-1",
                "relativeTo": "end"
            }
        }
    ]
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 прогнозирование ответа конечной точки](#tab/V3)

Следующий код JSON относится к параметру `verbose`, для которого задано значение `false`:

```json
{
    "query": "what is the second to last choice in the list",
    "prediction": {
        "normalizedQuery": "what is the second to last choice in the list",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "ordinalV2": [
                {
                    "offset": -1,
                    "relativeTo": "end"
                }
            ]
        }
    }
}
```

Следующий код JSON относится к параметру `verbose`, для которого задано значение `true`:

```json
{
    "query": "what is the second to last choice in the list",
    "prediction": {
        "normalizedQuery": "what is the second to last choice in the list",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.823669851
            }
        },
        "entities": {
            "ordinalV2": [
                {
                    "offset": -1,
                    "relativeTo": "end"
                }
            ],
            "$instance": {
                "ordinalV2": [
                    {
                        "type": "builtin.ordinalV2.relative",
                        "text": "the second to last",
                        "startIndex": 8,
                        "length": 18,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
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

Сведения о [процентах](luis-reference-prebuilt-percentage.md), [номере телефона](luis-reference-prebuilt-phonenumber.md)и [температуре](luis-reference-prebuilt-temperature.md) . 
