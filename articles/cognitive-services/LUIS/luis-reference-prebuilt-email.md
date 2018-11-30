---
title: Справочник по предварительно созданным сущностям email в службе LUIS в Azure | Документация Microsoft
titleSuffix: Azure
description: В этой статье описана готовая сущность email в службе "Распознавание речи" (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 6d05f62ad725a89b0a34b21b8a8d36bb8fa464b1
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52441017"
---
# <a name="email-entity"></a>Сущность email
При извлечении сущности email из фразы извлекается полный адрес электронной почты. Так как эта сущность уже обучена, добавлять в назначения приложения примеры фраз, содержащие адреса электронной почты, не нужно. Сущность email поддерживается только в `en-us` язычных и региональных параметрах. 

## <a name="resolution-for-prebuilt-email"></a>Разрешение для предварительно созданной сущности email
В следующем примере показано разрешение сущности **builtin.email**.

```JSON
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
      "endIndex": 55
    }
  ]
}
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о сущностях [number](luis-reference-prebuilt-number.md), [ordinal](luis-reference-prebuilt-ordinal.md) и [percentage](luis-reference-prebuilt-percentage.md). 