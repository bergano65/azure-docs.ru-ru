---
title: Предварительно созданная сущность keyPhrase
titleSuffix: Azure
description: В этой статье содержатся сведения о предварительно созданных сущностях keyPhrase в Интеллектуальной службе распознавания речи (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 5ef7ccb58533161d8397ad42e70de1999908dc36
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61473269"
---
# <a name="keyphrase-prebuilt-entity-for-a-luis-app"></a>Предварительно созданная сущность keyPhrase для приложения LUIS
keyPhrase извлекает различные ключевые фразы из высказывания. Добавление в приложение примеров высказываний, содержащих keyPhrase, не требуется. Сущность keyPhrase поддерживается во [многих языках и региональных параметрах](luis-language-support.md#languages-supported) как часть функции [текстовой аналитики](../text-analytics/overview.md). 

## <a name="resolution-for-prebuilt-keyphrase-entity"></a>Разрешение для предварительно созданной сущности keyPhrase
В следующем примере показано разрешение для сущности **builtin.keyPhrase**.

```json
{
  "query": "where is the educational requirements form for the development and engineering group",
  "topScoringIntent": {
    "intent": "GetJobInformation",
    "score": 0.182757929
  },
  "entities": [
    {
      "entity": "development",
      "type": "builtin.keyPhrase",
      "startIndex": 51,
      "endIndex": 61
    },
    {
      "entity": "educational requirements",
      "type": "builtin.keyPhrase",
      "startIndex": 13,
      "endIndex": 36
    }
  ]
}
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о сущностях [percentage](luis-reference-prebuilt-percentage.md), [number](luis-reference-prebuilt-number.md) и [age](luis-reference-prebuilt-age.md).
