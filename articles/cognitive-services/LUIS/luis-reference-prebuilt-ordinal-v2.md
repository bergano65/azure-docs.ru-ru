---
title: Порядковый номер версии 2 предварительно созданных сущностей
titleSuffix: Language Understanding - Azure Cognitive Services
description: Эта статья содержит порядковый номер версии 2 сведения о предварительно созданных сущностей в Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 06/25/2019
ms.author: diberry
ms.openlocfilehash: 862b962f5642e01d7ed8250f49d51a6132447083
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67486143"
---
# <a name="ordinal-v2-prebuilt-entity-for-a-luis-app"></a>Порядковый номер версии 2 предварительно созданных сущностей для приложения LUIS
Порядковый номер версии 2 расширяет [порядковый номер](luis-reference-prebuilt-ordinal.md) предоставляет относительных ссылок, такие как `next`, `last`, и `previous`. Они не извлекаются с помощью порядкового номера предварительно созданные сущности.

## <a name="resolution-for-prebuilt-ordinal-v2-entity"></a>Разрешение для предварительно созданных сущностей порядковый номер версии 2

### <a name="api-version-2x"></a>API версии 2.x

В следующем примере показано разрешение **builtin.ordinalV2** сущности.

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

### <a name="preview-api-version-3x"></a>Предварительная версия API 3.x

Приведенный ниже код JSON является с `verbose` параметру присвоить `false`:

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

Приведенный ниже код JSON является с `verbose` параметру присвоить `true`:

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

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о [процент](luis-reference-prebuilt-percentage.md), [номер телефона](luis-reference-prebuilt-phonenumber.md), и [температуры](luis-reference-prebuilt-temperature.md) сущностей. 
