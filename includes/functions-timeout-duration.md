---
title: включение файла
description: включение файла
services: functions
author: nzthiago
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2018
ms.author: nzthiago
ms.custom: include file
ms.openlocfilehash: fded43bb655cefda508b82eca94522730ab6da00
ms.sourcegitcommit: b5ff5abd7a82eaf3a1df883c4247e11cdfe38c19
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74941737"
---
## <a name="timeout"></a>Длительность ожидания приложения-функции 

Длительность времени ожидания для приложения-функции определяется свойством Функтионтимеаут в файле проекта [Host. JSON](../articles/azure-functions/functions-host-json.md#functiontimeout) . В следующей таблице показаны значения по умолчанию и максимальное значение в минутах для обоих планов и в обеих версиях среды выполнения:

| План | Версия среды выполнения | значение по умолчанию | Максимальная |
|------|---------|---------|---------|
| Потребление | 1.x | 5 | 10 |
| Потребление | 2.x | 5 | 10 |
| Потребление | 3.x | 5 | 10 |
| App Service | 1.x | Неограниченная | Неограниченная |
| App Service | 2.x | 30 | Неограниченная |
| App Service | 3.x | 30 | Неограниченная |

> [!NOTE] 
> Независимо от параметра времени ожидания приложения-функции 230 секунд — это максимальное время, которое функция, активируемая HTTP, может реагировать на запрос. Это связано с тем, что [время ожидания простоя по умолчанию Azure Load Balancer](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). Для более длительной обработки можно использовать [шаблон асинхронного устойчивые функции](../articles/azure-functions/durable/durable-functions-overview.md#async-http) или [отложить фактическую работу и вернуть немедленный ответ](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
