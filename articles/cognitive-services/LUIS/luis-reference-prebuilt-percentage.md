---
title: Процент предварительно построенной сущности — LUIS
titleSuffix: Azure Cognitive Services
description: В этой статье описана готовая сущность percentage в службе "Распознавание речи" (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 0005d679819f7516e1ded6294030cd42813e085f
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677533"
---
# <a name="percentage-prebuilt-entity-for-a-luis-app"></a>Предварительно созданная сущность percentage для приложения LUIS
Процентное соотношение может выражаться в виде дроби (`3 1/2`) или в процентах (`2%`). Так как эта сущность уже обучена, добавлять в назначения приложения примеры фраз, содержащие сущности percentage, не нужно. Сущность percentage поддерживается во [многих языках и региональных параметрах](luis-reference-prebuilt-entities.md). 

## <a name="types-of-percentage"></a>Типы сущностей percentage
Управление сущностью percentage выполняется из репозитория GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114)

## <a name="resolution-for-prebuilt-percentage-entity"></a>Разрешение для предварительно созданной сущности percentage

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Ответ конечной точки прогнозирования v2](#tab/V2)

В следующем примере показано разрешение сущности **builtin.percentage**.

```json
{
  "query": "set a trigger when my stock goes up 2%",
  "topScoringIntent": {
    "intent": "SetTrigger",
    "score": 0.971157849
  },
  "intents": [
    {
      "intent": "SetTrigger",
      "score": 0.971157849
    }
  ],
  "entities": [
    {
      "entity": "2%",
      "type": "builtin.percentage",
      "startIndex": 36,
      "endIndex": 37,
      "resolution": {
        "value": "2%"
      }
    }
  ]
}
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 прогнозирование ответа конечной точки](#tab/V3)

Следующий код JSON относится к параметру `verbose`, для которого задано значение `false`:

```json
{
    "query": "set a trigger when my stock goes up 2%",
    "prediction": {
        "normalizedQuery": "set a trigger when my stock goes up 2%",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.541765451
            }
        },
        "entities": {
            "percentage": [
                2
            ]
        }
    }
}
```

Следующий код JSON относится к параметру `verbose`, для которого задано значение `true`:

```json
{
    "query": "set a trigger when my stock goes up 2%",
    "prediction": {
        "normalizedQuery": "set a trigger when my stock goes up 2%",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.541765451
            }
        },
        "entities": {
            "percentage": [
                2
            ],
            "$instance": {
                "percentage": [
                    {
                        "type": "builtin.percentage",
                        "text": "2%",
                        "startIndex": 36,
                        "length": 2,
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

Дополнительные сведения о сущностях [порядковый номер](luis-reference-prebuilt-ordinal.md), [номер](luis-reference-prebuilt-number.md) и [температура](luis-reference-prebuilt-temperature.md). 
