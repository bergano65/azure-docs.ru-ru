---
title: Обнаружение объектов — Компьютерное зрение
titleSuffix: Azure Cognitive Services
description: Понятия, связанные с определением объектов с помощью API компьютерного зрения.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: pafarley
ms.openlocfilehash: 7f0f86e783edb55bc3193df073c92c9523a90176
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52976018"
---
# <a name="object-detection"></a>Обнаружение объектов

Обнаружение объекта похоже на [добавление тегов](concept-tagging-images.md), но в этом случае API возвращает координаты (в пикселях) прямоугольника, ограничивающего каждый найденный объект. Например, если на изображении есть собака, кот и человек, операция определения отобразит список этих объектов с их координатами на изображении. Эту функциональность можно использовать, чтобы обрабатывать отношения между объектами на изображении. Также она позволяет определить, присутствуют ли на изображении несколько экземпляров одного тега.

API обнаружения применяет теги на основе объектов или живых существ, найденных на изображении. Обратите внимание, что на этом этапе отсутствует формальная связь между таксономией, которая используется для добавления тегов и той, которая используется для обнаружения объектов. На концептуальном уровне API обнаружения находит только объекты и живые существа, тогда как API тегов умеет добавлять контекстные термины, как например indoor (в помещении), которые невозможно локализовать ограничивающим прямоугольником.

## <a name="object-detection-example"></a>Пример обнаружения объектов

В следующем ответе JSON демонстрируется ответ службы "Компьютерное зрение" при обнаружении объектов на тестовом изображении.

![Женщина с устройством Microsoft Surface на кухне](./Images/windows-kitchen.jpg)

```json
{
   "objects":[
      {
         "rectangle":{
            "x":730,
            "y":66,
            "w":135,
            "h":85
         },
         "object":"kitchen appliance",
         "confidence":0.501
      },
      {
         "rectangle":{
            "x":523,
            "y":377,
            "w":185,
            "h":46
         },
         "object":"computer keyboard",
         "confidence":0.51
      },
      {
         "rectangle":{
            "x":471,
            "y":218,
            "w":289,
            "h":226
         },
         "object":"Laptop",
         "confidence":0.85,
         "parent":{
            "object":"computer",
            "confidence":0.851
         }
      },
      {
         "rectangle":{
            "x":654,
            "y":0,
            "w":584,
            "h":473
         },
         "object":"person",
         "confidence":0.855
      }
   ],
   "requestId":"a7fde8fd-cc18-4f5f-99d3-897dcd07b308",
   "metadata":{
      "width":1260,
      "height":473,
      "format":"Jpeg"
   }
}
```

## <a name="next-steps"></a>Дополнительная информация

Сведения о концепциях [классификации изображений](concept-categorizing-images.md) и [описания изображений](concept-describing-images.md).