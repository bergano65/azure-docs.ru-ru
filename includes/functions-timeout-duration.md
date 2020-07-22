---
title: Включить имя файла
description: включить файл
services: functions
author: nzthiago
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2018
ms.author: nzthiago
ms.custom: include file
ms.openlocfilehash: eca2d3359614875e5bff0c9bb67f006f0a8cdba1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "77198332"
---
## <a name="function-app-timeout-duration"></a><a name="timeout"></a>Длительность ожидания приложения-функции 

Длительность времени ожидания для приложения-функции определяется `functionTimeout` свойством в [host.js](../articles/azure-functions/functions-host-json.md#functiontimeout) файла проекта. В следующей таблице показаны значения по умолчанию и максимальное значение в минутах для обоих планов и различных версий среды выполнения.

| План | Версия среды выполнения | Значение по умолчанию | Максимальная |
|------|---------|---------|---------|
| Потребление | 1.x | 5 | 10 |
| Потребление | 2.x | 5 | 10 |
| Потребление | 3.x | 5 | 10 |
| Premium | 1.x | 30 | Без ограничений |
| Premium | 2.x | 30 | Без ограничений |
| Premium | 3.x | 30 | Без ограничений |
| Служба приложений | 1.x | Без ограничений | Без ограничений |
| Служба приложений | 2.x | 30 | Без ограничений |
| Служба приложений | 3.x | 30 | Без ограничений |

> [!NOTE] 
> Независимо от параметра времени ожидания приложения-функции 230 секунд — это максимальное время, которое функция, активируемая HTTP, может реагировать на запрос. Это связано с тем, что [время ожидания простоя по умолчанию Azure Load Balancer](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). Для более длительной обработки можно использовать [шаблон асинхронного устойчивые функции](../articles/azure-functions/durable/durable-functions-overview.md#async-http) или [отложить фактическую работу и вернуть немедленный ответ](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
