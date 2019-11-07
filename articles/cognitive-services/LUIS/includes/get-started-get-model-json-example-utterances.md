---
title: включение файла
description: включение файла
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 10/19/2019
ms.author: diberry
ms.openlocfilehash: ba400beb13d0bd4b29eee0c60617ec7ad128dd77
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505830"
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
