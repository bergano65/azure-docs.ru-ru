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
ms.openlocfilehash: 189683a9e98f161ce537284cc7b0349c94be2bf0
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57410916"
---
## <a name="timeout"></a>Время ожидания функции приложения 

Продолжительность времени ожидания приложения-функции определяется по свойству functionTimeout в [host.json](../articles/azure-functions/functions-host-json.md#functiontimeout) файл проекта. В следующей таблице показаны по умолчанию и максимальное значения для обоих планов, так и в обеих версиях среды выполнения:

| План | Версия среды выполнения | значение по умолчанию | Максимальная |
|------|---------|---------|---------|
| Потребление | 1.x | 5 | 10 |
| Потребление | 2.x | 5 | 10 |
| Служба приложений | 1.x | Без ограничений | Без ограничений |
| Служба приложений | 2.x | 30 | Без ограничений |

> [!NOTE] 
> Независимо от значения времени ожидания приложения функции, 230 секунд — это максимальный объем времени, который может принимать функция, активируемая HTTP для ответа на запрос. Это из-за [по умолчанию время ожидания простоя балансировщика нагрузки Azure](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds). Для более длительное время обработки, рассмотрите возможность использования [асинхронный шаблон устойчивых функций](../articles/azure-functions/durable/durable-functions-concepts.md#async-http) или [отложить фактическую работу и сразу возвратить ответ](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions).
