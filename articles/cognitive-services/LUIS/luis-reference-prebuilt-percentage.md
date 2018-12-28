---
title: Предварительно созданная сущность percentage
titleSuffix: Azure
description: В этой статье описана готовая сущность percentage в службе "Распознавание речи" (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: 9b9faaae78cd1e3590291aef68db47f57f050f3d
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53165696"
---
# <a name="percentage-prebuilt-entity-for-a-luis-app"></a>Предварительно созданная сущность percentage для приложения LUIS
Процентное соотношение может выражаться в виде дроби (`3 1/2`) или в процентах (`2%`). Так как эта сущность уже обучена, добавлять в назначения приложения примеры фраз, содержащие сущности percentage, не нужно. Сущность percentage поддерживается во [многих языках и региональных параметрах](luis-reference-prebuilt-entities.md). 

## <a name="types-of-percentage"></a>Типы сущностей percentage
Управление сущностью percentage выполняется из репозитория GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L114)

## <a name="resolution-for-prebuilt-percentage-entity"></a>Разрешение для предварительно созданной сущности percentage
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

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о сущностях [порядковый номер](luis-reference-prebuilt-ordinal.md), [номер](luis-reference-prebuilt-number.md) и [температура](luis-reference-prebuilt-temperature.md). 