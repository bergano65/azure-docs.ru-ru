---
title: Шаблон. любой тип сущности — LUIS
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
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979164"
---
# <a name="patternany-entity"></a>Сущность Pattern.any

Patterns.any — это местозаполнитель переменной длины, используемый только в высказывании шаблона для пометки начала и окончания сущности.  

Сущности Pattern.Any необходимо помечать в примерах [шаблонов](luis-how-to-model-intent-pattern.md), а не в примерах намерений пользователя.

**Сущность хорошо подходит в следующих случаях:**

* Конечную часть сущности можно спутать с оставшимся текстом высказывания.

## <a name="usage"></a>Использование

В клиентском приложении для поиска книг по названию сущность pattern.any извлекает полное название. Высказывание шаблона, использующее сущность pattern.any для поиска книги, — `Was {BookTitle} written by an American this year[?]`.

В следующей таблице каждая строка имеет две версии высказывания. Верхняя utterance — это то, как LUIS изначально видит utterance. Не ясно, где начинается и заканчивается заголовок книги. В нижней utterance используется шаблон. любая сущность, помечающая начало и конец сущности.

|Utterance с сущностью, выделенной жирным шрифтом|
|--|
|`Was The Man Who Mistook His Wife for a Hat and Other Clinical Tales written by an American this year?`<br><br>Книга **The Man Who Mistook His Wife for a Hat and Other Clinical Tales (Человек, который принял жену за шляпу, и другие истории из врачебной практики)** была написана американским автором в этом году?|
|`Was Half Asleep in Frog Pajamas written by an American this year?`<br><br>Книга **Half Asleep in Frog Pajamas (Сонные глазки и пижама в лягушечку)** была написана американским автором в этом году?|
|`Was The Particular Sadness of Lemon Cake: A Novel written by an American this year?`<br><br>Был ли **определенный грусть Лемон торт: романский** , написанный в США в этом году?|
|`Was There's A Wocket In My Pocket! written by an American this year?`<br><br>Книга **There's A Wocket In My Pocket! (Кто живет в моем кармане?)** была написана американским автором этом году?|
||



## <a name="example-json"></a>Пример JSON

Обратите внимание на следующий запрос:

`where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

С именем внедренной формы для извлечения в виде шаблона. any:

`Understand your responsibilities as a member of the community`

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Отклик конечной точки прогнозирования V2](#tab/V2)

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


#### <a name="v3-prediction-endpoint-responsetabv3"></a>[Отклик конечной точки прогнозирования V3](#tab/V3)

Это JSON, если в строке запроса задано `verbose=false`:

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ]
}
```

Это JSON, если в строке запроса задано `verbose=true`:

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

В этом [руководстве](luis-tutorial-pattern.md)используется **шаблон. Любая** сущность для извлечения данных из фразы продолжительностью, где фразы продолжительностью хорошо отформатированы и где конец данных можно легко путать с остальными словами utterance.
