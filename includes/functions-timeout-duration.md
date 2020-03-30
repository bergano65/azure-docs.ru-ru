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
ms.openlocfilehash: eca2d3359614875e5bff0c9bb67f006f0a8cdba1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77198332"
---
## <a name="function-app-timeout-duration"></a><a name="timeout"></a>Длительность тайм-аута функционального приложения 

Продолжительность тайм-аута приложения функции `functionTimeout` определяется свойством в файле проекта [host.json.](../articles/azure-functions/functions-host-json.md#functiontimeout) В следующей таблице показаны значения по умолчанию и максимальные значения в минутах как для планов, так и для различных версий времени выполнения:

| План | Runtime Версия | Значение по умолчанию | Максимальная |
|------|---------|---------|---------|
| Потребление | 1.x | 5 | 10 |
| Потребление | 2.x | 5 | 10 |
| Потребление | 3.x | 5 | 10 |
| Premium | 1.x | 30 | Неограниченно |
| Premium | 2.x | 30 | Неограниченно |
| Premium | 3.x | 30 | Неограниченно |
| Служба приложений | 1.x | Неограниченно | Неограниченно |
| Служба приложений | 2.x | 30 | Неограниченно |
| Служба приложений | 3.x | 30 | Неограниченно |

> [!NOTE] 
> Независимо от настройки тайм-аута приложения, 230 секунд — это максимальное количество времени, которое функция HTTP-срабатывает, чтобы ответить на запрос. Это связано с выходом на [безделье по умолчанию из Баланса загрузки Azure.](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds) Для более длительного времени обработки рассмотрите возможность использования [шаблона асинаторов сроков действия и](../articles/azure-functions/durable/durable-functions-overview.md#async-http) [отложить фактическую работу и немедленно гоним ответ.](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions)
