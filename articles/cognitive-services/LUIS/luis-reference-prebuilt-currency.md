---
title: Предварительно построенная сущность Currency-LUIS
titleSuffix: Azure Cognitive Services
description: В этой статье описана готовая сущность currency в службе "Распознавание речи" (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 867e8f830542aab712b2bfe32f05dd9469c0da49
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932535"
---
# <a name="currency-prebuilt-entity-for-a-luis-app"></a>Предварительно созданная сущность currency для приложения LUIS
Предварительно построенная сущность Currency обнаруживает валюту во многих деноминатионс и странах и регионах независимо от языка и региональных параметров приложения LUIS. Так как эта сущность уже обучена, добавлять в назначение приложения примеры фраз, содержащие валюты, не нужно. Сущность валюты поддерживается во [многих языках и региональных параметрах](luis-reference-prebuilt-entities.md). 

## <a name="types-of-currency"></a>Типы валюты
Управление валютой выполняется из репозитория GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26)

## <a name="resolution-for-currency-entity"></a>Разрешение для сущности валюты

### <a name="api-version-2x"></a>API версии 2. x

В следующем примере показано разрешение сущности **builtin.currency**.

```json
{
  "query": "search for items under $10.99",
  "topScoringIntent": {
    "intent": "SearchForItems",
    "score": 0.926173568
  },
  "intents": [
    {
      "intent": "SearchForItems",
      "score": 0.926173568
    },
    {
      "intent": "None",
      "score": 0.07376878
    }
  ],
  "entities": [
    {
      "entity": "$10.99",
      "type": "builtin.currency",
      "startIndex": 23,
      "endIndex": 28,
      "resolution": {
        "unit": "Dollar",
        "value": "10.99"
      }
    }
  ]
}
```



### <a name="preview-api-version-3x"></a>Предварительная версия API версии 3. x

Следующий код JSON имеет `verbose` параметр со `false`значением:

```json
{
    "query": "search for items under $10.99",
    "prediction": {
        "normalizedQuery": "search for items under $10.99",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.605889857
            }
        },
        "entities": {
            "money": [
                {
                    "number": 10.99,
                    "unit": "Dollar"
                }
            ]
        }
    }
}
```

Следующий код JSON имеет `verbose` параметр со `true`значением:

```json
{
    "query": "search for items under $10.99",
    "prediction": {
        "normalizedQuery": "search for items under $10.99",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.605889857
            }
        },
        "entities": {
            "money": [
                {
                    "number": 10.99,
                    "unit": "Dollar"
                }
            ],
            "$instance": {
                "money": [
                    {
                        "type": "builtin.currency",
                        "text": "$10.99",
                        "startIndex": 23,
                        "length": 6,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    }
                ]
            }
        }
    }
}
```

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о сущностях [datetimeV2](luis-reference-prebuilt-datetimev2.md), [измерение](luis-reference-prebuilt-dimension.md) и [электронная почта](luis-reference-prebuilt-email.md). 
