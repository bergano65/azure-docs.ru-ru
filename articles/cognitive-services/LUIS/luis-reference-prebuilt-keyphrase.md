---
title: Кэйфрасе предварительно созданная сущность — LUIS
titleSuffix: Azure Cognitive Services
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
ms.openlocfilehash: d7ecb579305cb17525e220bc16be430c77d25080
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563418"
---
# <a name="keyphrase-prebuilt-entity-for-a-luis-app"></a>Предварительно созданная сущность keyPhrase для приложения LUIS
keyPhrase извлекает различные ключевые фразы из высказывания. Добавление в приложение примеров высказываний, содержащих keyPhrase, не требуется. Сущность keyPhrase поддерживается во [многих языках и региональных параметрах](luis-language-support.md#languages-supported) как часть функции [текстовой аналитики](../text-analytics/overview.md). 

## <a name="resolution-for-prebuilt-keyphrase-entity"></a>Разрешение для предварительно созданной сущности keyPhrase

### <a name="api-version-2x"></a>API версии 2. x

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

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о сущностях [percentage](luis-reference-prebuilt-percentage.md), [number](luis-reference-prebuilt-number.md) и [age](luis-reference-prebuilt-age.md).
