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
ms.openlocfilehash: 2239387ffff4c30e1183721a528e666199316bed
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695092"
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
|`Was The Particular Sadness of Lemon Cake: A Novel written by an American this year?`<br><br>Роман **The Particular Sadness of Lemon Cake (Особая печаль лимонного торта)**  был написан американским автором в этом году?|
|`Was There's A Wocket In My Pocket! written by an American this year?`<br><br>Книга **There's A Wocket In My Pocket! (Кто живет в моем кармане?)** была написана американским автором этом году?|
||



## <a name="example-json"></a>Пример JSON

Рассмотрим следующий запрос:

`where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

С именем внедренной формы для извлечения в виде шаблона. any:

`Understand your responsibilities as a member of the community`

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Ответ конечной точки прогнозирования v2](#tab/V2)

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


#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 прогнозирование ответа конечной точки](#tab/V3)

Это JSON, если `verbose=false` задан в строке запроса:

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ]
}
```

Это JSON, если `verbose=true` задан в строке запроса:

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

## <a name="next-steps"></a>Следующие шаги

В этом [руководстве](luis-tutorial-pattern-any.md)используется **шаблон. Любая** сущность для извлечения данных из фразы продолжительностью, где фразы продолжительностью хорошо отформатированы и где конец данных можно легко путать с остальными словами utterance.