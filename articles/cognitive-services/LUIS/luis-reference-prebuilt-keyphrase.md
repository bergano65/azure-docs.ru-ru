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
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 988609f411ad405b0f1dc244b23fb6db446136a2
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2019
ms.locfileid: "65072006"
---
# <a name="keyphrase-prebuilt-entity-for-a-luis-app"></a>Предварительно созданная сущность keyPhrase для приложения LUIS
keyPhrase извлекает различные ключевые фразы из высказывания. Добавление в приложение примеров высказываний, содержащих keyPhrase, не требуется. Сущность keyPhrase поддерживается во [многих языках и региональных параметрах](luis-language-support.md#languages-supported) как часть функции [текстовой аналитики](../text-analytics/overview.md). 

## <a name="resolution-for-prebuilt-keyphrase-entity"></a>Разрешение для предварительно созданной сущности keyPhrase

### <a name="api-version-2x"></a>API версии 2.x

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
