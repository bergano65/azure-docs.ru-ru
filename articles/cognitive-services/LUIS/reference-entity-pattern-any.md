---
title: Pattern.any тип сущности - LUIS
titleSuffix: Azure Cognitive Services
description: Patterns.any — это местозаполнитель переменной длины, используемый только в высказывании шаблона для пометки начала и окончания сущности.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 5164bf55ef8233cf34a470524da3bc852678d79a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75979164"
---
# <a name="patternany-entity"></a>Сущность Pattern.any

Patterns.any — это местозаполнитель переменной длины, используемый только в высказывании шаблона для пометки начала и окончания сущности.  

Сущности Pattern.Any необходимо помечать в примерах [шаблонов](luis-how-to-model-intent-pattern.md), а не в примерах намерений пользователя.

**Сущность оптимальна для данных в таких случаях.**

* Конечную часть сущности можно спутать с оставшимся текстом высказывания.

## <a name="usage"></a>Использование

В клиентском приложении для поиска книг по названию сущность pattern.any извлекает полное название. Высказывание шаблона, использующее сущность pattern.any для поиска книги, — `Was {BookTitle} written by an American this year[?]`.

В следующей таблице каждая строка имеет две версии высказывания. Верхняя тона, как LUIS первоначально видит высказывание. Непонятно, где начинается и заканчивается название книги. Нижнее высказывание использует сущность Pattern.any для обозначения начала и конца сущности.

|Высказывание с сущностью жирным шрифтом|
|--|
|`Was The Man Who Mistook His Wife for a Hat and Other Clinical Tales written by an American this year?`<br><br>Книга **The Man Who Mistook His Wife for a Hat and Other Clinical Tales (Человек, который принял жену за шляпу, и другие истории из врачебной практики)** была написана американским автором в этом году?|
|`Was Half Asleep in Frog Pajamas written by an American this year?`<br><br>Книга **Half Asleep in Frog Pajamas (Сонные глазки и пижама в лягушечку)** была написана американским автором в этом году?|
|`Was The Particular Sadness of Lemon Cake: A Novel written by an American this year?`<br><br>Была **особая печаль лимонного торта: Роман,** написанный американцем в этом году?|
|`Was There's A Wocket In My Pocket! written by an American this year?`<br><br>Был **ли вокет в моем кармане!** была написана американским автором этом году?|
||



## <a name="example-json"></a>Пример JSON

Обратите внимание на следующий запрос:

`where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

С встроенным названием формы для извлечения в качестве Pattern.any:

`Understand your responsibilities as a member of the community`

#### <a name="v2-prediction-endpoint-response"></a>[Отклик конечной точки прогнозирования V2](#tab/V2)

```JSON
"entities": [
  {
    "entity": "understand your responsibilities as a member of the community",
    "type": "FormName",
    "startIndex": 18,
    "endIndex": 78,
    "role": ""
  }
```


#### <a name="v3-prediction-endpoint-response"></a>[Отклик конечной точки прогнозирования V3](#tab/V3)

Это JSON, `verbose=false` если установлен в строке запроса:

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ]
}
```

Это JSON, `verbose=true` если установлен в строке запроса:

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ],
    "$instance": {
        "FormName": [
            {
                "type": "FormName",
                "text": "Understand your responsibilities as a member of the community",
                "startIndex": 18,
                "length": 61,
                "modelTypeId": 7,
                "modelType": "Pattern.Any Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

## <a name="next-steps"></a>Дальнейшие действия

В этом [учебнике](luis-tutorial-pattern.md)используйте **объект Pattern.any** для извлечения данных из высказываний, где высказывания хорошо отформатированы и где конец данных может быть легко спутан с оставшимися словами высказывания.
