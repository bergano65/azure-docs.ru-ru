---
title: Справочник по предварительно созданным сущностям email в службе LUIS в Azure | Документация Microsoft
titleSuffix: Azure
description: В этой статье описана готовая сущность email в службе "Распознавание речи" (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 4a48bb4a6e988d4352f957c6435a9c1bf0a3e5fb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60712747"
---
# <a name="email-prebuilt-entity-for-a-luis-app"></a>Предварительно созданная сущность email для приложения LUIS
При извлечении сущности email из фразы извлекается полный адрес электронной почты. Так как эта сущность уже обучена, добавлять в назначения приложения примеры фраз, содержащие адреса электронной почты, не нужно. Сущность email поддерживается только в `en-us` язычных и региональных параметрах. 

## <a name="resolution-for-prebuilt-email"></a>Разрешение для предварительно созданной сущности email
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

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о сущностях [number](luis-reference-prebuilt-number.md), [ordinal](luis-reference-prebuilt-ordinal.md) и [percentage](luis-reference-prebuilt-percentage.md). 
