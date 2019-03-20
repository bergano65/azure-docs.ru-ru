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
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 7b748c507d5c848cc83a8a0c55cb7b05903bc542
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56958538"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>Предварительно созданная сущность PersonName для приложения LUIS
Предварительно созданная сущность PersonName определяет имена людей. Так как эта сущность уже обучена, добавлять в намерения приложения примеры высказываний, содержащие сущности PersonName, не нужно. Сущность personName поддерживается для английского и китайского [языков и региональных параметров](luis-reference-prebuilt-entities.md).

## <a name="resolution-for-personname-entity"></a>Разрешение для сущности personName
В следующем примере показано разрешение сущности **builtin.PersonName**.

```json
{
  "query": "Is Jill Jones in Cairo?",
  "topScoringIntent": {
    "intent": "WhereIsEmployee",
    "score": 0.762141049
  }
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

## <a name="next-steps"></a>Дальнейшие действия

См. дополнительные сведения о сущностях [email](luis-reference-prebuilt-email.md), [number](luis-reference-prebuilt-number.md) и [ordinal](luis-reference-prebuilt-ordinal.md). 
