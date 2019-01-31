---
title: включение файла
description: включение файла
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: 46b588d6977aa6ffeb9d473e6bfe149cde7147ba
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55478762"
---
Файл с примерами высказываний **utterances.json** имеет определенный формат. 

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
