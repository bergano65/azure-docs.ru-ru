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
ms.openlocfilehash: 7bf95a2b49608ef1f031a3b443db92b42cdae624
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "80244721"
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

## <a name="use-the-api"></a>Использование API

Функция описания образа является частью API [анализа изображений](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) . Вы можете вызывать этот API с помощью собственного пакета SDK или с помощью вызовов REST. Включите `Description` в параметр запроса **висуалфеатурес** . Затем, когда вы получаете полный ответ JSON, просто Проанализируйте строку для содержимого `"description"` раздела.

* [Краткое руководство. Компьютерное зрение пакета SDK для .NET](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Краткое руководство. Анализ изображения (REST API)](./quickstarts/csharp-analyze.md)

## <a name="next-steps"></a>Дальнейшие действия

Изучите связанные понятия [маркировки изображений](concept-tagging-images.md) и [категоризации изображений](concept-categorizing-images.md).
