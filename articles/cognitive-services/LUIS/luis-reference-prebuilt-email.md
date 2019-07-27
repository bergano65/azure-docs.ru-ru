---
title: Справочник по электронной почте для предварительно созданных сущностей LUIS
titleSuffix: Azure Cognitive Services
description: В этой статье описана готовая сущность email в службе "Распознавание речи" (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 9c9c7b373f820dd23c70a67a1de8545935a1d93c
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560250"
---
# <a name="email-prebuilt-entity-for-a-luis-app"></a>Предварительно созданная сущность email для приложения LUIS
При извлечении сущности email из фразы извлекается полный адрес электронной почты. Так как эта сущность уже обучена, добавлять в назначения приложения примеры фраз, содержащие адреса электронной почты, не нужно. Сущность email поддерживается только в `en-us` язычных и региональных параметрах. 

## <a name="resolution-for-prebuilt-email"></a>Разрешение для предварительно созданной сущности email

### <a name="api-version-2x"></a>API версии 2. x

В следующем примере показано разрешение сущности **builtin.email**.

```json
{
  "query": "please send the information to patti.owens@microsoft.com",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.811592042
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.811592042
    }
  ],
  "entities": [
    {
      "entity": "patti.owens@microsoft.com",
      "type": "builtin.email",
      "startIndex": 31,
      "endIndex": 55,
      "resolution": {
        "value": "patti.owens@microsoft.com"
      }
    }
  ]
}
```

### <a name="preview-api-version-3x"></a>Предварительная версия API версии 3. x

Следующий код JSON имеет `verbose` параметр со `false`значением:

```json
{
    "query": "please send the information to patti.owens@microsoft.com",
    "prediction": {
        "normalizedQuery": "please send the information to patti.owens@microsoft.com",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.5023781
            }
        },
        "entities": {
            "email": [
                "patti.owens@microsoft.com"
            ]
        }
    }
}
```


Следующий код JSON имеет `verbose` параметр со `true`значением:

```json
{
    "query": "please send the information to patti.owens@microsoft.com",
    "prediction": {
        "normalizedQuery": "please send the information to patti.owens@microsoft.com",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.5023781
            }
        },
        "entities": {
            "email": [
                "patti.owens@microsoft.com"
            ],
            "$instance": {
                "email": [
                    {
                        "type": "builtin.email",
                        "text": "patti.owens@microsoft.com",
                        "startIndex": 31,
                        "length": 25,
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

Дополнительные сведения о сущностях [number](luis-reference-prebuilt-number.md), [ordinal](luis-reference-prebuilt-ordinal.md) и [percentage](luis-reference-prebuilt-percentage.md). 
