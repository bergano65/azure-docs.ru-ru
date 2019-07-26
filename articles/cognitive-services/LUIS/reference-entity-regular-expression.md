---
title: Тип сущности регулярного выражения
titleSuffix: Language Understanding - Azure Cognitive Services
description: Регулярное выражение является оптимальным вариантом при использовании необработанного текста высказывания. Оно не учитывает регистр и игнорирует региональный алфавит.  Сопоставление регулярных выражений применяется на уровне символов, а не на уровне маркеров, после внесения изменений при проверке орфографии.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: dfffea26ccd8dfcecea2c6a2e07234f3ab27f72b
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68480234"
---
# <a name="regular-expression-entity"></a>Сущность регулярного выражения 

Сущность регулярного выражения извлекает сущность на основе предоставленного шаблона регулярного выражения.

Регулярное выражение является оптимальным вариантом при использовании необработанного текста высказывания. Оно не учитывает регистр и игнорирует региональный алфавит.  Сопоставление регулярных выражений применяется на уровне символов, а не на уровне маркеров, после внесения изменений при проверке орфографии. Если регулярное выражение слишком сложное, например в нем используется много квадратных скобок, вы не сможете добавить выражение в модель. Использует часть, но не всю библиотеку [.NET Regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions) . 

**Сущность хорошо подходит в следующих случаях:**

* Данные согласованно отформатированы с любыми также согласованными вариантами.
* У регулярного выражения может быть не более двух уровней вложенности. 

![Сущность регулярного выражения](./media/luis-concept-entities/regex-entity.png)

## <a name="usage-considerations"></a>Рекомендации по использованию

Регулярные выражения могут соответствовать большему значению, чем предполагается. Примером этого является числовое совпадение слов, например `one` и `two`. Примером является следующее регулярное выражение, которое соответствует числу `one` вместе с другими числами:

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
``` 

Это выражение регулярного выражения также соответствует любым словам, которые заканчиваются этими числами, например `phone`. Чтобы устранить подобные проблемы, убедитесь, что регулярное выражение учитывает границы слов. Регулярное выражение для использования границ слов в этом примере используется в следующем регулярном выражении:

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>Пример JSON

При использовании `kb[0-9]{6}`в качестве определения сущности регулярного выражения следующий ответ JSON представляет собой пример utterance с возвращаемыми сущностями регулярных выражений для запроса `When was kb123456 published?`:

```JSON
{
  "query": "when was kb123456 published?",
  "topScoringIntent": {
    "intent": "FindKBArticle",
    "score": 0.933641255
  },
  "intents": [
    {
      "intent": "FindKBArticle",
      "score": 0.933641255
    },
    {
      "intent": "None",
      "score": 0.04397359
    }
  ],
  "entities": [
    {
      "entity": "kb123456",
      "type": "KB number",
      "startIndex": 9,
      "endIndex": 16
    }
  ]
}
```

## <a name="next-steps"></a>Следующие шаги

В этом [руководстве](luis-quickstart-intents-regex-entity.md)вы создадите приложение для извлечения согласованных данных из utterance с помощью сущности регулярного **выражения** .