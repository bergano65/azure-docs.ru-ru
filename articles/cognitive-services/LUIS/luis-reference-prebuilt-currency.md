---
title: Справочник по предварительно созданным сущностям валюты в LUIS в Azure | Документация Майкрософт
titleSuffix: Azure
description: В этой статье описана готовая сущность currency в службе "Распознавание речи" (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: 6346ce4e3c9e51852a9a98904b797b6eb64970e4
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52334012"
---
# <a name="currency-entity"></a>Сущность валюты
Предварительно созданная сущность валюты обнаруживает валюту во многих деноминациях и странах, независимо от языка и региональных параметров LUIS. Так как эта сущность уже обучена, добавлять в назначение приложения примеры фраз, содержащие валюты, не нужно. Сущность валюты поддерживается во [многих языках и региональных параметрах](luis-reference-prebuilt-entities.md). 

## <a name="types-of-currency"></a>Типы валюты
Управление валютой выполняется из репозитория GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26)

## <a name="resolution-for-currency-entity"></a>Разрешение для сущности валюты
В следующем примере показано разрешение сущности **builtin.currency**.

```JSON
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

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о сущностях [datetimeV2](luis-reference-prebuilt-datetimev2.md), [измерение](luis-reference-prebuilt-dimension.md) и [электронная почта](luis-reference-prebuilt-email.md). 