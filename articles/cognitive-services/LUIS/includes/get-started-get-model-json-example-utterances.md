---
title: включить файл
description: включить файл
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.date: 02/14/2020
ms.subservice: language-understanding
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: e879afdbd4c34e9d74405644de86421fb2cbab46
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2020
ms.locfileid: "77279685"
---
Примеры речевых фрагментов соответствуют определенному формату.

Поле `text` содержит текст примера высказывания. Поле `intentName` должно соответствовать имени существующего намерения в приложении LUIS. Поле `entityLabels` является обязательным. Чтобы не помечать все сущности, укажите пустой массив.

Если массив entityLabels не пуст, `startCharIndex` и `endCharIndex` должны пометить сущность, указанную в поле `entityName`. Индекс отсчитывается с нуля. Это означает, что 6 в примере выше обозначает заглавную букву S слова Seattle, а не пробел перед этой буквой. Если метка будет начинаться или заканчиваться пробелом в тексте, вызов API для добавления высказываний завершится ошибкой.

```JSON
[
  {
    "text": "go to Seattle today",
    "intentName": "BookFlight",
    "entityLabels": [
      {
        "entityName": "Location::LocationTo",
        "startCharIndex": 6,
        "endCharIndex": 12
      }
    ]
  },
  {
    "text": "purple dogs are difficult to work with",
    "intentName": "BookFlight",
    "entityLabels": []
  }
]
```
