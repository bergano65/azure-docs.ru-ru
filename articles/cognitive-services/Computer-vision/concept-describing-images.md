---
title: Описания изображений — Компьютерное зрение
titleSuffix: Azure Cognitive Services
description: Понятия, связанные с функцией описания изображений API компьютерного зрения.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: dcf61c642a9f8ad7aa68d72736ce8fdb0e9a6e3c
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945264"
---
# <a name="describe-images-with-human-readable-language"></a>Описание изображений на понятном для пользователя языке

API "Компьютерное зрение" позволяет анализировать изображения и создавать понятные пользователю предложения, описывающее содержимое изображений. Алгоритм фактически возвращает несколько описаний на основе различных визуальных функций, и каждому описанию предоставляется Оценка достоверности. Готовые выходные данные представлены в виде списка описаний, упорядоченных по оценке достоверности (от самой высокой до самой низкой).

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

## <a name="next-steps"></a>Следующие шаги

Сведения о концепциях [добавления тегов к изображениям](concept-tagging-images.md) и [классификации изображений](concept-categorizing-images.md).
