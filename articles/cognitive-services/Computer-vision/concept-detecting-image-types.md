---
title: Определение типов изображений. Компьютерное зрение
titleSuffix: Azure Cognitive Services
description: Понятия, связанные с функцией определения типа изображений API компьютерного зрения.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 6cd7b2a8a70a315b05c0824a863803bbc6ffabb2
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55872141"
---
# <a name="detecting-image-types-with-computer-vision"></a>Определение типов изображений с помощью API компьютерного зрения

Компьютерное зрение может анализировать тип содержимого изображений и указывать, является ли изображение графическим или же это картинка, оценивая вероятность по шкале.

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

## <a name="next-steps"></a>Дополнительная информация

Сведения о концепциях [добавления тегов к изображениям](concept-tagging-images.md) и [классификации изображений](concept-categorizing-images.md).
