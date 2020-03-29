---
title: Обнаружение типа изображения - Компьютерное зрение
titleSuffix: Azure Cognitive Services
description: Понятия, связанные с функцией определения типа изображений API компьютерного зрения.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4e6c2db5333962d7ae43534998ffc1c48b0dba45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244568"
---
# <a name="detecting-image-types-with-computer-vision"></a>Определение типов изображений с помощью API компьютерного зрения

С помощью [API-анализа изображений](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) Computer Vision может анализировать тип содержимого изображений, указывая, является ли изображение искусством клипа или рисунком линии.

## <a name="detecting-clip-art"></a>Определение картинки

Компьютерное зрение анализирует изображение и оценивает вероятность того, что оно является картинкой, по шкале от 0 до 3, как показано в таблице ниже.

| Значение | Значение |
|-------|---------|
| 0 | не картинка |
| 1 | Неоднозначно |
| 2 | Обычная картинка |
| 3 | Хорошая картинка |

### <a name="clip-art-detection-examples"></a>Примеры определения картинок

В приведенных ниже ответах JSON показано, что именно возвращает компьютерное зрение при оценке вероятности того, что примеры изображений являются картинками.

![Картинка с ломтиком сыра](./Images/cheese_clipart.png)

```json
{
    "imageType": {
        "clipArtType": 3,
        "lineDrawingType": 0
    },
    "requestId": "88c48d8c-80f3-449f-878f-6947f3b35a27",
    "metadata": {
        "height": 225,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![Синий дом и передний двор](./Images/house_yard.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "a9c8490a-2740-4e04-923b-e8f4830d0e47",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="detecting-line-drawings"></a>Определение графических изображений

Компьютерное зрение анализирует изображение и возвращает логическое значение, указывающее, является ли оно графическим.

### <a name="line-drawing-detection-examples"></a>Примеры определения графических изображений

В приведенных ниже ответах JSON показано, что именно возвращает компьютерное зрение при определении того, являются ли примеры изображений графическими.

![Графическое изображение льва](./Images/lion_drawing.png)

```json
{
    "imageType": {
        "clipArtType": 2,
        "lineDrawingType": 1
    },
    "requestId": "6442dc22-476a-41c4-aa3d-9ceb15172f01",
    "metadata": {
        "height": 268,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![Белый цветок на зеленом фоне](./Images/flower.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "98437d65-1b05-4ab7-b439-7098b5dfdcbf",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="use-the-api"></a>Использование API

Функция обнаружения типа изображения является частью API [анализа изображений.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) Вы можете вызывать этот API с помощью собственного пакета SDK или с помощью вызовов REST. Включить `ImageType` в параметр запроса **visualFeatures.** Затем, когда вы получите полный ответ JSON, просто разогнайте строку для содержимого раздела. `"imageType"`

* [Быстрый запуск: Компьютерное зрение .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Быстрый запуск: Проанализируйте изображение (REST API)](./quickstarts/csharp-analyze.md)
