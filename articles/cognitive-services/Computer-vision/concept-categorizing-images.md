---
title: Классификация изображений. Компьютерное зрение
titleSuffix: Azure Cognitive Services
description: Понятия, связанные с классификацией изображений с помощью API компьютерного зрения.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: 7062d98d40c15f4e9e873038fc12fc1b104c996d
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52333485"
---
# <a name="categorizing-images"></a>Классификация изображений

В дополнение к добавлению тегов и описаний, компьютерное зрение возвращает основанные на таксономии категории, определенные в предыдущих версиях. Эти категории организованы в виде таксономии с иерархиями родительских и дочерних наследований. Все категории указаны на английском языке. Они могут использоваться отдельно или с новыми моделями добавления тегов.

## <a name="the-86-category-concept"></a>Концепция из 86 категорий

На основе списка из 86 понятий, показанного на схеме ниже, изображение можно классифицировать в диапазоне от общего к более конкретному. Полную таксономию в формате текста см. в статье [86-Categories Taxonomy](category-taxonomy.md) (Таксономия 86 категорий).

![Сгруппированный список всех категорий в классификации категорий](./Images/analyze_categories-v2.png)

## <a name="image-categorization-examples"></a>Примеры классификации изображений

В приведенном ниже ответе JSON показано, что именно возвращает компьютерное зрение при классификации примера изображения на основе его визуальных характеристик.

![Женщина на крыше](./Images/woman_roof.png)

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
| ![Семейное фото](./Images/family_photo.png) | people_group |
| ![Милая собака](./Images/cute_dog.png) | animal_dog |
| ![Гора](./Images/mountain_vista.png) | outdoor_mountain |
| ![Анализ изображения. Хлеб](./Images/bread.png) | food_bread |

## <a name="next-steps"></a>Дополнительная информация

Сведения о концепциях [добавления тегов к изображениям](concept-tagging-images.md) и [описания изображений](concept-describing-images.md).