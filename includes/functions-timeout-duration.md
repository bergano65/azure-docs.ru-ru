---
title: включить файл
description: включить файл
services: functions
author: nzthiago
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2018
ms.author: nzthiago
ms.custom: include file
ms.openlocfilehash: 3501ff3f92ae045019df2766bbcf7fc2c3fec5b0
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75768930"
---
## <a name="timeout"></a>Длительность ожидания приложения-функции 

Длительность времени ожидания для приложения-функции определяется свойством `functionTimeout` в файле проекта [Host. JSON](../articles/azure-functions/functions-host-json.md#functiontimeout) . В следующей таблице показаны значения по умолчанию и максимальное значение в минутах для обоих планов и различных версий среды выполнения.

| План | Версия среды выполнения | По умолчанию | Максимальная |
|------|---------|---------|---------|
| Потребление | 1.x | 5 | 10 |
| Потребление | 2.x | 5 | 10 |
| Потребление | 3.x | 5 | 10 |
| App Service | 1.x | Неограниченная | Неограниченная |
| App Service | 2.x | 30 | Неограниченная |
| App Service | 3.x | 30 | Неограниченная |

> [!NOTE] 
> Независимо от параметра времени ожидания приложения-функции 230 секунд — это максимальное время, которое функция, активируемая HTTP, может реагировать на запрос. Это связано с тем, что [время ожидания простоя по умолчанию Azure Load Balancer](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). Для более длительной обработки можно использовать [шаблон асинхронного устойчивые функции](../articles/azure-functions/durable/durable-functions-overview.md#async-http) или [отложить фактическую работу и вернуть немедленный ответ](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
