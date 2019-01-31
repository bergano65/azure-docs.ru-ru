---
title: Предварительно созданная сущность age
titleSuffix: Azure
description: В этой статье описана готовая сущность age в службе "Распознавание речи" (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 7644f2c4f4479ae234dc2d4b501be17455230bde
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55217855"
---
# <a name="age-prebuilt-entity-for-a-luis-app"></a>Предварительно созданная сущность age для приложения LUIS
Предварительно созданная сущность age фиксирует значение возраста как в цифровом выражении, так и в виде количества дней, недель, месяцев или лет. Так как эта сущность уже обучена, добавлять в назначения приложения примеры фраз, содержащие сущности age, не нужно. Сущность age поддерживается во [многих языках и региональных параметрах](luis-reference-prebuilt-entities.md). 

## <a name="types-of-age"></a>Типы сущностей age
Управление сущностью age выполняется из репозитория GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L3)

## <a name="resolution-for-prebuilt-age-entity"></a>Разрешение для предварительно созданной сущности age
В следующем примере показано разрешение сущности **builtin.age**.

```json
{
  "query": "A 90 day old utilities bill is quite late.",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.8236133
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.8236133
    }
  ],
  "entities": [
    {
      "entity": "90 day old",
      "type": "builtin.age",
      "startIndex": 2,
      "endIndex": 11,
      "resolution": {
        "unit": "Day",
        "value": "90"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Дополнительная информация

См. дополнительные сведения о сущностях [currency](luis-reference-prebuilt-currency.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md) и [dimension](luis-reference-prebuilt-dimension.md). 
