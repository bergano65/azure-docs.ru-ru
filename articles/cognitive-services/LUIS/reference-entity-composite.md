---
title: Составной объект типа-LUIS
titleSuffix: Azure Cognitive Services
description: Составная сущность состоит из других сущностей, например готовых сущностей, простых, регулярных выражений и сущностей списка. Эти отдельные сущности формируют единую сущность.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: ea258275cf954bc6e06da03324c2ae93de0e7fde
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563237"
---
# <a name="composite-entity"></a>Составная сущность 

Составная сущность состоит из других сущностей, например готовых сущностей, простых, регулярных выражений и сущностей списка. Эти отдельные сущности формируют единую сущность. 

**Эта сущность хорошо подходит, когда данные:**

* данные связаны между собой; 
* Связаны между собой в контексте фразы.
* для них используются различные типы сущностей;
* должны быть сгруппированы и обработаны клиентским приложением в качестве единицы информации;
* имеют различные высказывания пользователей, которые требуют машинного обучения.

![составная сущность](./media/luis-concept-entities/composite-entity.png)

## <a name="example-json"></a>Пример JSON

Рассмотрим составную сущность предварительно `number` построенного и `Location::ToLocation` со следующими utterance:

`book 2 tickets to paris`

Обратите внимание, что между `2` (числом) и `paris` ToLocation находятся слова, которые не входят в какие-либо сущности. Зеленая линия подчеркивания, используемая в помеченном высказывании на веб-сайте [LUIS](luis-reference-regions.md), указывает на составную сущность.

![Составная сущность](./media/luis-concept-data-extraction/composite-entity.png)

Составные сущности возвращаются в массиве `compositeEntities`, а все сущности в рамках составной возвращаются в массиве `entities`:

```JSON

"entities": [
    {
    "entity": "2 tickets to cairo",
    "type": "ticketInfo",
    "startIndex": 0,
    "endIndex": 17,
    "score": 0.67200166
    },
    {
    "entity": "2",
    "type": "builtin.number",
    "startIndex": 0,
    "endIndex": 0,
    "resolution": {
        "subtype": "integer",
        "value": "2"
    }
    },
    {
    "entity": "cairo",
    "type": "builtin.geographyV2",
    "startIndex": 13,
    "endIndex": 17
    }
],
"compositeEntities": [
    {
    "parentType": "ticketInfo",
    "value": "2 tickets to cairo",
    "children": [
        {
        "type": "builtin.geographyV2",
        "value": "cairo"
        },
        {
        "type": "builtin.number",
        "value": "2"
        }
    ]
    }
]
```    

|Объект данных|Имя сущности|Значение|
|--|--|--|
|Предварительно созданная сущность — число|"builtin.number"|"2"|
|Предварительно созданная сущность — GeographyV2|"Location::ToLocation"|"paris"|

## <a name="next-steps"></a>Следующие шаги

В этом [руководстве](luis-tutorial-composite-entity.md)добавляется **составная сущность** для объединения извлеченных данных различных типов в одну содержащую сущность. Клиентское приложение может легко извлекать связанные данные разных типов путем их объединения.
