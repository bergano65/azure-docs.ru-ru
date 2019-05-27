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
ms.openlocfilehash: ffb29fc76313e8870b52cb0a63936da7853ea6ce
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66131345"
---
## <a name="timeout"></a>Время ожидания функции приложения 

Продолжительность времени ожидания приложения-функции определяется по свойству functionTimeout в [host.json](../articles/azure-functions/functions-host-json.md#functiontimeout) файл проекта. Следующая таблица показывает значения по умолчанию и максимальное в минутах для обоих планов и в обеих версиях среды выполнения:

| План | Версия среды выполнения | значение по умолчанию | Максимум |
|------|---------|---------|---------|
| Потребление | 1.x | 5 | 10 |
| Потребление | 2.x | 5 | 10 |
| Служба приложений | 1.x | Не ограничено | Не ограничено |
| Служба приложений | 2.x | 30 | Не ограничено |

> [!NOTE] 
> Независимо от значения времени ожидания приложения функции, 230 секунд — это максимальный объем времени, который может принимать функция, активируемая HTTP для ответа на запрос. Это из-за [по умолчанию время ожидания простоя балансировщика нагрузки Azure](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). Для более длительное время обработки, рассмотрите возможность использования [асинхронный шаблон устойчивых функций](../articles/azure-functions/durable/durable-functions-concepts.md#async-http) или [отложить фактическую работу и сразу возвратить ответ](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
