---
title: Привязки центра Интернета вещей Azure для функций Azure
description: Узнайте, как использовать привязки центра Интернета вещей в функциях Azure.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: функции azure, функции, обработка событий, динамические вычисления, независимая архитектура
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: e39a7b2c29ee541a9a104d681d25335496e5625b
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67480601"
---
# <a name="azure-iot-hub-bindings-for-azure-functions"></a>Привязки центра Интернета вещей Azure для функций Azure

В этой статье объясняется, как работать с привязками "функции Azure" для центра Интернета вещей. Центр Интернета вещей с расчетом [привязки концентраторов событий Azure](functions-bindings-event-hubs.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Пакеты – Функции 1.x

Для функций Azure версии 1.x, центр Интернета вещей привязки доступны в [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) пакет NuGet версии 2.x. Исходный код для пакета находится в репозитории GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs).

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Пакеты — Функции 2.x

В службе "Функции" версии 2.x используйте пакет [Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs) версии 3.x. Исходный код для пакета находится в репозитории GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

> [!IMPORTANT]
> Хотя в следующих примерах кода используется API концентратора событий, данный синтаксис применяется для функций центра Интернета вещей.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md)
