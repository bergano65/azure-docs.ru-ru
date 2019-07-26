---
title: Pattern. любой тип сущности
titleSuffix: Language Understanding - Azure Cognitive Services
description: Patterns.any — это местозаполнитель переменной длины, используемый только в высказывании шаблона для пометки начала и окончания сущности.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: f3e5bd068b5df99bdfabff0bfe18ccebff980fc6
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68480168"
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

```JSON
{
  "query": "where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?",
  "topScoringIntent": {
    "intent": "FindForm",
    "score": 0.999999464
  },
  "intents": [
    {
      "intent": "FindForm",
      "score": 0.999999464
    },
    {
      "intent": "GetEmployeeBenefits",
      "score": 4.883697E-06
    },
    {
      "intent": "None",
      "score": 1.02040713E-06
    },
    {
      "intent": "GetEmployeeOrgChart",
      "score": 9.278342E-07
    },
    {
      "intent": "MoveAssetsOrPeople",
      "score": 9.278342E-07
    }
  ],
  "entities": [
    {
      "entity": "understand your responsibilities as a member of the community",
      "type": "FormName",
      "startIndex": 18,
      "endIndex": 78,
      "role": ""
    }
  ]
}
```

## <a name="next-steps"></a>Следующие шаги

В этом [руководстве](luis-tutorial-pattern-any.md)используется **шаблон. Любая** сущность для извлечения данных из фразы продолжительностью, где фразы продолжительностью хорошо отформатированы и где конец данных можно легко путать с остальными словами utterance.