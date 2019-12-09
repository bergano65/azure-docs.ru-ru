---
title: Привязки Центров событий Azure для службы "Функции Azure"
description: Узнайте, как использовать привязки Центров событий Azure в Функциях Azure.
author: craigshoemaker
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.topic: reference
ms.date: 11/08/2017
ms.author: cshoe
ms.openlocfilehash: 998ba122d6582a86122bbe65ff47bad1ea24a900
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74925536"
---
# <a name="azure-event-hubs-bindings-for-azure-functions"></a>Привязки Центров событий Azure для службы "Функции Azure"

Здесь объясняется, как работать с привязками [Центров событий Azure](../event-hubs/event-hubs-what-is-event-hubs.md) для службы "Функции Azure". Функции Azure поддерживают привязки триггера и выходные привязки для Центров событий Azure.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Пакеты – Функции 1.x

Привязки Центров событий службы "Функции Azure" версии 1.x доступны в пакете NuGet [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) версии 2.x.
Исходный код для пакета находится в репозитории GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs).


[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Packages — функции 2. x и более поздних версий

Для функций 2. x и более поздних версий используйте пакет [Microsoft. Azure. веб-задания. Extensions. EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs) версии 3. x.
Исходный код для пакета находится в репозитории GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md)
