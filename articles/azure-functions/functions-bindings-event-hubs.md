---
title: Привязки Центров событий Azure для службы "Функции Azure"
description: Узнайте, как использовать привязки Центров событий Azure в Функциях Azure.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: функции azure, функции, обработка событий, динамические вычисления, независимая архитектура
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/08/2017
ms.author: cshoe
ms.openlocfilehash: aa808bf333b35ce46a40a2fc8da7a2c8e78ac1aa
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67480622"
---
# <a name="azure-event-hubs-bindings-for-azure-functions"></a>Привязки Центров событий Azure для службы "Функции Azure"

Здесь объясняется, как работать с привязками [Центров событий Azure](../event-hubs/event-hubs-what-is-event-hubs.md) для службы "Функции Azure". Функции Azure поддерживают привязки триггера и выходные привязки для Центров событий Azure.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Пакеты – Функции 1.x

Привязки Центров событий службы "Функции Azure" версии 1.x доступны в пакете NuGet [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) версии 2.x.
Исходный код для пакета находится в репозитории GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs).


[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Пакеты — Функции 2.x

В службе "Функции" версии 2.x используйте пакет [Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs) версии 3.x.
Исходный код для пакета находится в репозитории GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md)
