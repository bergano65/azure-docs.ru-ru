---
title: включение файла
description: включение файла
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 3fd610512a87263eeb6599a24faef5ba23070823
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608343"
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

|Свойство  |Значение по умолчанию | Описание |
|---------|---------|---------| 
|isEnabled|true|Включает или отключает выборку.| 
|maxTelemetryItemsPerSecond|5|Пороговое значение, при котором начинается выборка.| 