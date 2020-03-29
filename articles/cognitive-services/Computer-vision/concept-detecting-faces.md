---
title: Обнаружение лица - Компьютерное зрение
titleSuffix: Azure Cognitive Services
description: Изучите концепции, связанные с функцией распознавания лиц в API компьютерного зрения.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: d10e4c98d3e2f00e7139063c8263e65ffd015d7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244707"
---
# <a name="face-detection-with-computer-vision"></a>Определение лиц с помощью компьютерного зрения

Компьютерное зрение обнаруживает на изображении лица людей и создает прямоугольник для каждого из них, а также указывает пол и возраст. 

> [!NOTE]
> Такую функцию также предлагает служба [Распознавание лиц](/azure/cognitive-services/face/). Дополнительные сведения об альтернативе для анализа лица, включая идентификацию лица и определение позы. 

## <a name="face-detection-examples"></a>Примеры определения лиц

В следующем примере демонстрируется ответ в формате JSON, возвращаемый компьютерным зрением для изображения, на котором имеется одно лицо.

![Анализ изображения. Распознавание лица женщины на крыше](./Images/woman_roof_face.png)

```json
{
    "faces": [
        {
            "age": 23,
            "gender": "Female",
            "faceRectangle": {
                "top": 45,
                "left": 194,
                "width": 44,
                "height": 44
            }
        }
    ],
    "requestId": "8439ba87-de65-441b-a0f1-c85913157ecd",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Png"
    }
}
```

В следующем примере демонстрируется ответ в формате JSON, возвращаемый для изображения, на котором имеется несколько лиц.

![Анализ изображения. Распознавание лиц на семейном фото](./Images/family_photo_face.png)

```json
{
    "faces": [
        {
            "age": 11,
            "gender": "Male",
            "faceRectangle": {
                "top": 62,
                "left": 22,
                "width": 45,
                "height": 45
            }
        },
        {
            "age": 11,
            "gender": "Female",
            "faceRectangle": {
                "top": 127,
                "left": 240,
                "width": 42,
                "height": 42
            }
        },
        {
            "age": 37,
            "gender": "Female",
            "faceRectangle": {
                "top": 55,
                "left": 200,
                "width": 41,
                "height": 41
            }
        },
        {
            "age": 41,
            "gender": "Male",
            "faceRectangle": {
                "top": 45,
                "left": 103,
                "width": 39,
                "height": 39
            }
        }
    ],
    "requestId": "3a383cbe-1a05-4104-9ce7-1b5cf352b239",
    "metadata": {
        "height": 230,
        "width": 300,
        "format": "Png"
    }
}
```

## <a name="use-the-api"></a>Использование API

Функция обнаружения лиц является частью API [анализа изображений.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) Вы можете вызывать этот API с помощью собственного пакета SDK или с помощью вызовов REST. Включить `Faces` в параметр запроса **visualFeatures.** Затем, когда вы получите полный ответ JSON, просто разогнайте строку для содержимого раздела. `"faces"`

* [Быстрый запуск: Компьютерное зрение .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Быстрый запуск: Проанализируйте изображение (REST API)](./quickstarts/csharp-analyze.md)
