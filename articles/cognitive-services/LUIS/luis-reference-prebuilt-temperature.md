---
title: Предварительно созданная сущность temperature
titleSuffix: Azure
description: В этой статье описана готовая сущность temperature в службе "Распознавание речи" (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 3629dc96fbb86236888014d9de9a7b7b3d86c298
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55867398"
---
# <a name="temperature-prebuilt-entity-for-a-luis-app"></a>Предварительно созданная сущность temperature для приложения LUIS
Сущность temperature извлекает различные типы температурных данных. Так как эта сущность уже обучена, добавлять в приложение примеры фраз, содержащие сущности temperature, не нужно. Сущность temperature поддерживается во [многих языках и региональных параметрах](luis-reference-prebuilt-entities.md). 

## <a name="types-of-temperature"></a>Типы сущностей temperature
Управление сущностью temperature выполняется из репозитория GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L819)

## <a name="resolution-for-prebuilt-temperature-entity"></a>Разрешение для предварительно созданной сущности temperature
В следующем примере показано разрешение сущности **builtin.temperature**.

```json
{
  "query": "set the temperature to 30 degrees",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.85310787
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.85310787
    }
  ],
  "entities": [
    {
      "entity": "30 degrees",
      "type": "builtin.temperature",
      "startIndex": 23,
      "endIndex": 32,
      "resolution": {
        "unit": "Degree",
        "value": "30"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о сущностях [percentage](luis-reference-prebuilt-percentage.md), [number](luis-reference-prebuilt-number.md) и [age](luis-reference-prebuilt-age.md). 
