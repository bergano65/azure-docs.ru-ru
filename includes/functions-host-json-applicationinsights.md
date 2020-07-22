---
title: Включить имя файла
description: включить файл
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: a925076dfccd30c73febb2aadc8692667ea01525
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "76279474"
---
Управляет [функцией выборки в Application Insights](../articles/azure-functions/functions-monitoring.md#configure-sampling).

```json
{
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    }
}
```

|Свойство.  |По умолчанию | Описание |
|---------|---------|---------| 
|isEnabled|true|Включает или отключает выборку.| 
|maxTelemetryItemsPerSecond|5|Пороговое значение, при котором начинается выборка.| 
