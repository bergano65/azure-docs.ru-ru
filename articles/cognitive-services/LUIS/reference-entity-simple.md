---
title: Простой тип сущности — LUIS
titleSuffix: Azure Cognitive Services
description: Простая сущность — это универсальная сущность, описывающая одно понятие и обучаемая на основе контекста машинного обучения. Так как простые сущности обычно называют названиями компаний, названиями продуктов или другими категориями имен, добавьте список фраз при использовании простой сущности для увеличения сигнала используемых имен.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: c1514b6cd512924a162a524d11e888055fa06514
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563200"
---
# <a name="simple-entity"></a>Простая сущность 

Простая сущность — это универсальная сущность, описывающая одно понятие и обучаемая на основе контекста машинного обучения. Так как простые сущности обычно являются именами, например названиями компаний, продуктов или другими категориями имен, добавьте [список фраз](luis-concept-feature.md) для выделения используемых имен. 

**Сущность хорошо подходит в следующих случаях:**

* У данных отсутствует согласованное форматирование, но они означают одно и то же. 

![простая сущность](./media/luis-concept-entities/simple-entity.png)

## <a name="example-json"></a>Пример JSON

`Bob Jones wants 3 meatball pho`

В предыдущем высказывании `Bob Jones` помечена как простая сущность `Customer`.

Данные, возвращаемые из конечной точки, содержат имя сущности, обнаруженный текст из высказывания, расположение обнаруженного текста, а также оценку:

```JSON
"entities": [
  {
  "entity": "bob jones",
  "type": "Customer",
  "startIndex": 0,
  "endIndex": 8,
  "score": 0.473899543
  }
]
```

|Объект данных|Имя сущности|Значение|
|--|--|--|
|Простая сущность|`Customer`|`bob jones`|

## <a name="next-steps"></a>Следующие шаги

В этом [руководстве](luis-quickstart-primary-and-secondary-data.md)вы извлечете сведения об имени задания по трудоустройству, полученные от компьютера, из utterance с помощью **простой сущности**. Чтобы увеличить точность извлечения, добавьте [список фраз](luis-concept-feature.md) для терминов, характерных для простой сущности.