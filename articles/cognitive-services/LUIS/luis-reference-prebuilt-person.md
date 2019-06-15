---
title: Предварительно созданная сущность PersonName
titleSuffix: Azure Cognitive Services
description: В этой статье описана предварительно созданная сущность PersonName в службе "Распознавание речи" (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 3b12c69b7c6710e774d50e631d2423fd72ce828a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65072176"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>Предварительно созданная сущность PersonName для приложения LUIS
Предварительно созданная сущность PersonName определяет имена людей. Так как эта сущность уже обучена, добавлять в намерения приложения примеры высказываний, содержащие сущности PersonName, не нужно. Сущность personName поддерживается для английского и китайского [языков и региональных параметров](luis-reference-prebuilt-entities.md).

## <a name="resolution-for-personname-entity"></a>Разрешение для сущности personName

### <a name="api-version-2x"></a>API версии 2.x

В следующем примере показано разрешение сущности **builtin.PersonName**.

```json
{
  "query": "Is Jill Jones in Cairo?",
  "topScoringIntent": {
    "intent": "WhereIsEmployee",
    "score": 0.762141049
  },
  "entities": [
    {
      "entity": "Jill Jones",
      "type": "builtin.personName",
      "startIndex": 3,
      "endIndex": 12
    }
  ]
}
```

### <a name="preview-api-version-3x"></a>Предварительная версия API 3.x

Приведенный ниже код JSON является с `verbose` параметру присвоить `false`:

```json
{
    "query": "Is Jill Jones in Cairo?",
    "prediction": {
        "normalizedQuery": "is jill jones in cairo?",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.6544678
            }
        },
        "entities": {
            "personName": [
                "Jill Jones"
            ]
        }
    }
}
```

Приведенный ниже код JSON является с `verbose` параметру присвоить `true`:

```json
{
    "query": "Is Jill Jones in Cairo?",
    "prediction": {
        "normalizedQuery": "is jill jones in cairo?",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.6544678
            }
        },
        "entities": {
            "personName": [
                "Jill Jones"
            ],
            "$instance": {
                "personName": [
                    {
                        "type": "builtin.personName",
                        "text": "Jill Jones",
                        "startIndex": 3,
                        "length": 10,
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
