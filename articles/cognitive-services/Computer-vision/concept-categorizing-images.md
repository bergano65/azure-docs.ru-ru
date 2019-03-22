---
title: Классификация изображений. Компьютерное зрение
titleSuffix: Azure Cognitive Services
description: Понятия, связанные с функцией классификации изображений API компьютерного зрения.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 07fdaa22532f48cc39b6c524d85fdfe625f8b80c
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337145"
---
# <a name="categorize-images"></a>Классификация изображений

В дополнение к тегам и описанию, компьютерное зрение возвращает основанные на таксономии категории, обнаруженные в изображении. В отличие от тегов, категории организованы в иерархии родительского или дочернего наследования, и их меньше (86, в отличие от тысяч тегов). Все категории имен указаны на английском языке. Классификация может быть выполнена сама по себе или вместе с более новой моделью тегов.

## <a name="the-86-category-concept"></a>Концепция из 86 категорий

Компьютерное зрение можно разделить изображения широко или в частности, с помощью списка 86 категорий на следующей схеме. Полную таксономию в формате текста см. в статье [86-Categories Taxonomy](category-taxonomy.md) (Таксономия 86 категорий).

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

| Образ — | Категория |
|-------|----------|
| ![Четыре человека позируют как семья](./Images/family_photo.png) | people_group |
| ![Щенок, сидящий на лужайке](./Images/cute_dog.png) | animal_dog |
| ![Человек, стоящий на скале на закате](./Images/mountain_vista.png) | outdoor_mountain |
| ![Гора булочек на столе](./Images/bread.png) | food_bread |

## <a name="next-steps"></a>Дальнейшие действия

Сведения о концепциях [добавления тегов к изображениям](concept-tagging-images.md) и [описания изображений](concept-describing-images.md).
