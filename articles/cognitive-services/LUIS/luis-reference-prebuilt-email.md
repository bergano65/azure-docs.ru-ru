---
title: Справочник по предварительно созданным сущностям email в службе LUIS в Azure | Документация Microsoft
titleSuffix: Azure
description: В этой статье описана готовая сущность email в службе "Распознавание речи" (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 1cb12bdc362955da907fb5a5ed64c2a1a43fdc32
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53140869"
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
      "endIndex": 55
    }
  ]
}
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о сущностях [number](luis-reference-prebuilt-number.md), [ordinal](luis-reference-prebuilt-ordinal.md) и [percentage](luis-reference-prebuilt-percentage.md). 