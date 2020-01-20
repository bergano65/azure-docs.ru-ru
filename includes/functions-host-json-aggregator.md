---
title: включить файл
description: включить файл
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 9c51ce726545d1c64d69c86c36fc69ea43c3b882
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279523"
---
Указывает, сколько вызовов функций обрабатывается при [расчете метрик для Application Insights](../articles/azure-functions/functions-monitoring.md#configure-the-aggregator). 

```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    }
}
```

|Свойство |По умолчанию  | Description |
|---------|---------|---------| 
|batchSize|1000|Максимальное количество запросов, которое необходимо обработать.| 
|flushTimeout|00:00:30|Максимальный период времени, который необходимо обработать.| 

Вызовы функций обрабатываются, когда достигается одно из этих двух ограничений.
