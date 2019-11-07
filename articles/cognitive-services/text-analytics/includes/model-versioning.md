---
title: управления версиями моделей;
titleSuffix: Azure Cognitive Services
description: Указание версий модели в конечных точках версии 3
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/25/2019
ms.author: aahi
ms.openlocfilehash: 5a06e26e5f1640024e343c714db3df134422115c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488652"
---
Версия 3 API анализа текста позволяет выбрать модель Анализа текста для данных. Используйте необязательный параметр `model-version` для выбора версии модели в запросах. Если этот параметр не указан, интерфейс API по умолчанию использует значение `latest`, то есть последнюю стабильную версию модели.

Доступные версии модели:
* `2019-10-01` (`latest`)

Каждый ответ от конечных точек версии 3 включает в себя поле `model-version`, в котором указывается используемая версия модели.

```json
{
    “documents”: […]
    “errors”: []
    “model-version”: “2019-10-01”
}
```
