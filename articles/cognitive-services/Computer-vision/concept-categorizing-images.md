---
title: Категоризация изображений - Компьютерное зрение
titleSuffix: Azure Cognitive Services
description: Понятия, связанные с функцией классификации изображений API компьютерного зрения.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 477349f1addf71a30e8ecb179266d8eac5510887
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244756"
---
# <a name="categorize-images-by-subject-matter"></a>Категоризировать изображения по тематике

В дополнение к тегам и описанию, компьютерное зрение возвращает основанные на таксономии категории, обнаруженные в изображении. В отличие от тегов, категории организованы в иерархии родительского или дочернего наследования, и их меньше (86, в отличие от тысяч тегов). Все категории имен указаны на английском языке. Классификация может быть выполнена сама по себе или вместе с более новой моделью тегов.

## <a name="the-86-category-concept"></a>Концепция из 86 категорий

Компьютерное зрение может классифицировать изображение широко или конкретно, используя список из 86 категорий в следующей диаграмме. Полную таксономию в формате текста см. в статье [86-Categories Taxonomy](category-taxonomy.md) (Таксономия 86 категорий).

![Сгруппированный список всех категорий в классификации категорий](./Images/analyze_categories-v2.png)

## <a name="image-categorization-examples"></a>Примеры классификации изображений

В приведенном ниже ответе JSON показано, что именно возвращает компьютерное зрение при классификации примера изображения на основе его визуальных характеристик.

![Женщина на крыше жилого дома](./Images/woman_roof.png)

```json
{
    "categories": [
        {
            "name": "people_",
            "score": 0.81640625
        }
    ],
    "requestId": "bae7f76a-1cc7-4479-8d29-48a694974705",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

В приведенной ниже таблице показаны типичный набор изображений и категория, возвращаемая компьютерным зрением для каждого из них.

| Изображение | Категория |
|-------|----------|
| ![Четыре человека позируют как семья](./Images/family_photo.png) | people_group |
| ![Щенок, сидящий на лужайке](./Images/cute_dog.png) | animal_dog |
| ![Человек, стоящий на скале на закате](./Images/mountain_vista.png) | outdoor_mountain |
| ![Гора булочек на столе](./Images/bread.png) | food_bread |

## <a name="use-the-api"></a>Использование API

Функция категоризации является частью API [анализа изображений.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) Вы можете вызывать этот API с помощью собственного пакета SDK или с помощью вызовов REST. Включить `Categories` в параметр запроса **visualFeatures.** Затем, когда вы получите полный ответ JSON, просто разогнайте строку для содержимого раздела. `"categories"`

* [Быстрый запуск: Компьютерное зрение .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Быстрый запуск: Проанализируйте изображение (REST API)](./quickstarts/csharp-analyze.md)

## <a name="next-steps"></a>Дальнейшие действия

Изучите связанные с ними концепции [пометки изображений](concept-tagging-images.md) и [описания изображений.](concept-describing-images.md)
