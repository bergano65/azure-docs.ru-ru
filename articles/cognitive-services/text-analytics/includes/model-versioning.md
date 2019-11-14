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
ms.openlocfilehash: 29850cb9cb40eae0829b5d8c2b58b5f9518f18d5
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74021171"
---
Версия 3 API анализа текста позволяет выбрать модель Анализа текста для данных. Используйте необязательный параметр `model-version` для выбора версии модели в запросах. Если этот параметр не указан, интерфейс API по умолчанию использует значение `latest`, то есть последнюю стабильную версию модели.

Доступные версии модели:
* `2019-10-01` (`latest`)

Каждый ответ от конечных точек версии 3 включает в себя поле `model-version`, в котором указывается используемая версия модели.

```json
{
    "documents": […]
    "errors": []
    "model-version": "2019-10-01"
}
```
