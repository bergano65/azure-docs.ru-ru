---
title: Предварительно созданные сущности dimension
titleSuffix: Azure
description: В этой статье описана готовая сущность dimension в службе "Распознавание речи" (LUIS).
services: cognitive-services
ms.custom: seodec18
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: e77cd2290981de62ba7fce7f04174cd0c5ec2af3
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/13/2019
ms.locfileid: "65072182"
---
# <a name="dimension-prebuilt-entity-for-a-luis-app"></a>Предварительно созданная сущность dimension для приложения LUIS
Предварительно созданная сущность dimension обнаруживает различные типы измерений, независимо от языка и региональных параметров LUIS. Так как эта сущность уже обучена, добавлять в назначения приложения примеры фраз, содержащие сущности dimension, не нужно. Сущность dimension поддерживается во [многих языках и региональных параметрах](luis-reference-prebuilt-entities.md). 

## <a name="types-of-dimension"></a>Типы сущностей dimension

Управление сущностью dimension выполняется из репозитория GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml)


## <a name="resolution-for-dimension-entity"></a>Разрешение для сущности dimension

### <a name="api-version-2x"></a>API версии 2.x

В следующем примере показано разрешение сущности **builtin.dimension**.

```json
{
  "query": "it takes more than 10 1/2 miles of cable and wire to hook it all up , and 23 computers.",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.762141049
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.762141049
    }
  ],
  "entities": [
    {
      "entity": "10 1/2 miles",
      "type": "builtin.dimension",
      "startIndex": 19,
      "endIndex": 30,
      "resolution": {
        "unit": "Mile",
        "value": "10.5"
      }
    }
  ]
}
```

### <a name="preview-api-version-3x"></a>Предварительная версия API 3.x

Приведенный ниже код JSON является с `verbose` параметру присвоить `false`:

```json
{
    "query": "it takes more than 10 1/2 miles of cable and wire to hook it all up , and 23 computers.",
    "prediction": {
        "normalizedQuery": "it takes more than 10 1/2 miles of cable and wire to hook it all up , and 23 computers.",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.400049
            }
        },
        "entities": {
            "dimension": [
                {
                    "number": 10.5,
                    "unit": "Mile"
                }
            ]
        }
    }
}
```

Приведенный ниже код JSON является с `verbose` параметру присвоить `true`:

```json
{
    "query": "it takes more than 10 1/2 miles of cable and wire to hook it all up , and 23 computers.",
    "prediction": {
        "normalizedQuery": "it takes more than 10 1/2 miles of cable and wire to hook it all up , and 23 computers.",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.400049
            }
        },
        "entities": {
            "dimension": [
                {
                    "number": 10.5,
                    "unit": "Mile"
                }
            ],
            "$instance": {
                "dimension": [
                    {
                        "type": "builtin.dimension",
                        "text": "10 1/2 miles",
                        "startIndex": 19,
                        "length": 12,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    }
                ]
            }
        }
    }
}
```

## <a name="next-steps"></a>Дальнейшие действия

См. дополнительные сведения о сущностях [email](luis-reference-prebuilt-email.md), [number](luis-reference-prebuilt-number.md) и [ordinal](luis-reference-prebuilt-ordinal.md). 
