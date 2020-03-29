---
title: Содержимое теги - Компьютерное зрение
titleSuffix: Azure Cognitive Services
description: Понятия, связанные с функцией задания тегов для изображений API компьютерного зрения.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 887f6d9163f800ecbe8a5ab9a0715fd171f1d3d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244517"
---
# <a name="applying-content-tags-to-images"></a>Применение тегов содержимого к изображениям

API компьютерного зрения возвращает теги на основе тысячи распознаваемых объектов, живых существ, пейзажей и действий. В случаях, когда теги могут быть неоднозначными или необщеизвестными, в ответ API входят "подсказки", помогающие разъяснить смысл тега на основе известного параметра. Теги не упорядочены по таксономии и не существуют в иерархиях наследования. Коллекция тегов содержимого формирует основу для "описания" изображения, отображаемого на понятном для пользователя языке и отформатированного в полные предложения. Обратите внимание, что на данный момент английский является единственным поддерживаемым языком для описание изображения.

После загрузки изображения или указав его URL-адрес, алгоритмы компьютерного зрения выводят теги, в зависимости от объектов, живых существ и действий, указанных на изображении. Добавление тегов не ограничивается основным предметом, например человеком на переднем плане, но также включает параметр (внутри помещения или снаружи), мебель, инструменты, растения, животных, аксессуары, гаджеты и т. д.

## <a name="image-tagging-example"></a>Пример добавления тегов к изображению

В следующем ответе JSON показано, что именно возвращает компьютерное зрение при отметке визуальных характеристик, обнаруженных в примере изображения.

![Синий дом и передний двор](./Images/house_yard.png).

```json
{
    "tags": [
        {
            "name": "grass",
            "confidence": 0.9999995231628418
        },
        {
            "name": "outdoor",
            "confidence": 0.99992108345031738
        },
        {
            "name": "house",
            "confidence": 0.99685388803482056
        },
        {
            "name": "sky",
            "confidence": 0.99532157182693481
        },
        {
            "name": "building",
            "confidence": 0.99436837434768677
        },
        {
            "name": "tree",
            "confidence": 0.98880356550216675
        },
        {
            "name": "lawn",
            "confidence": 0.788884699344635
        },
        {
            "name": "green",
            "confidence": 0.71250593662261963
        },
        {
            "name": "residential",
            "confidence": 0.70859086513519287
        },
        {
            "name": "grassy",
            "confidence": 0.46624681353569031
        }
    ],
    "requestId": "06f39352-e445-42dc-96fb-0a1288ad9cf1",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="use-the-api"></a>Использование API

Функция пометки является частью API [анализа изображений.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) Вы можете вызывать этот API с помощью собственного пакета SDK или с помощью вызовов REST. Включить `Tags` в параметр запроса **visualFeatures.** Затем, когда вы получите полный ответ JSON, просто разогнайте строку для содержимого раздела. `"tags"`

* [Быстрый запуск: Компьютерное зрение .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Быстрый запуск: Проанализируйте изображение (REST API)](./quickstarts/csharp-analyze.md)

## <a name="next-steps"></a>Дальнейшие действия

Изучите связанные с ними концепции [классификации изображений](concept-categorizing-images.md) и [описания изображений.](concept-describing-images.md)
