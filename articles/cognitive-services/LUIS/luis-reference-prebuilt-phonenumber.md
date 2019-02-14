---
title: Предварительно созданные сущности phonenumber
titleSuffix: Azure
description: В этой статье описана готовая сущность phonenumber в службе "Распознавание речи" (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: fa2163b37bda5beba7c36bfdff0778c4062c5546
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55863421"
---
# <a name="phonenumber-prebuilt-entity-for-a-luis-app"></a>Предварительно созданная сущность phonenumber для приложения LUIS
Сущность `phonenumber` извлекает различные номера телефонов, в том числе код страны. Так как эта сущность уже обучена, добавлять в приложение примеры фраз не нужно. Сущность `phonenumber` поддерживается только в языке `en-us`. 

## <a name="types-of-phonenumber"></a>Типы сущности phonenumber
Управление сущностью phonenumber выполняется из репозитория GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml).

## <a name="resolution-for-prebuilt-phonenumber-entity"></a>Разрешение для предварительно созданной сущности phonenumber
В следующем примере показано разрешение сущности **builtin.phonenumber**.

```json
{
  "query": "my mobile is 00 44 161 1234567",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.8448457
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.8448457
    }
  ],
  "entities": [
    {
      "entity": "00 44 161 1234567",
      "type": "builtin.phonenumber",
      "startIndex": 13,
      "endIndex": 29,
      "resolution": {
        "value": "00 44 161 1234567"
      }
    }
  ]
}
```


## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о сущностях [percentage](luis-reference-prebuilt-percentage.md), [number](luis-reference-prebuilt-number.md) и [temperature](luis-reference-prebuilt-temperature.md). 
