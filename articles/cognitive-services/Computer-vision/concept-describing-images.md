---
title: Описание изображений. Компьютерное зрение
titleSuffix: Azure Cognitive Services
description: Понятия, связанные с функцией описания изображений API компьютерного зрения.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 5b920e4ce8df131b81a9ef6ce2d66c7082d8f5e4
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/18/2018
ms.locfileid: "53583414"
---
# <a name="describe-images-with-human-readable-language"></a>Описание изображений на понятном для пользователя языке

Алгоритмы Компьютерного зрения анализируют содержимое изображения. Анализ формирует основу для 'описания', отображаемого на понятном для пользователя языке в полных предложениях. В описании приведена сводка того, что найдено на изображении. Алгоритмы Компьютерного зрения создают различные описания в зависимости от визуальных признаков, определенных на изображении. Каждое описание оценивается и получает оценку достоверности. Затем возвращается список, упорядоченный от наибольшей оценки достоверности к наименьшей.

## <a name="image-description-example"></a>Пример описания изображения

В приведенном ниже ответе JSON показано, что именно возвращает Компьютерное зрение при описании примера изображения на основе его визуальных характеристик.

![Черно-белая фотография здания на Манхэттене](./Images/bw_buildings.png)

```json
{
    "description": {
        "tags": ["outdoor", "building", "photo", "city", "white", "black", "large", "sitting", "old", "water", "skyscraper", "many", "boat", "river", "group", "street", "people", "field", "tall", "bird", "standing"],
        "captions": [
            {
                "text": "a black and white photo of a city",
                "confidence": 0.95301952483304808
            },
            {
                "text": "a black and white photo of a large city",
                "confidence": 0.94085190563213816
            },
            {
                "text": "a large white building in a city",
                "confidence": 0.93108362931954824
            }
        ]
    },
    "requestId": "b20bfc83-fb25-4b8d-a3f8-b2a1f084b159",
    "metadata": {
        "height": 300,
        "width": 239,
        "format": "Jpeg"
    }
}
```

## <a name="next-steps"></a>Дополнительная информация

Сведения о концепциях [добавления тегов к изображениям](concept-tagging-images.md) и [классификации изображений](concept-categorizing-images.md).