---
title: Привязки центра Интернета вещей Azure для функций Azure
description: Узнайте, как использовать привязки центра Интернета вещей в функциях Azure.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: функции azure, функции, обработка событий, динамические вычисления, независимая архитектура
ms.service: azure-functions
ms.topic: reference
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: d8b749a294da379e99e61072ff7a3415c508d2ac
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097645"
---
# <a name="azure-iot-hub-bindings-for-azure-functions"></a>Привязки центра Интернета вещей Azure для функций Azure

В этой статье объясняется, как работать с привязками функций Azure для центра Интернета вещей. Поддержка центра Интернета вещей основана на привязке [концентраторов событий Azure](functions-bindings-event-hubs.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Пакеты – Функции 1.x

Для функций Azure версии 1. x привязки центра Интернета вещей предоставляются в пакете NuGet [Microsoft. Azure. веб-заданий. servicebus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) , версия 2. x. Исходный код для пакета находится в репозитории GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs).

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Пакеты — Функции 2.x

В службе "Функции" версии 2.x используйте пакет [Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs) версии 3.x. Исходный код для пакета находится в репозитории GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

> [!IMPORTANT]
> Хотя в следующих примерах кода используется API концентратора событий, заданный синтаксис применим к функциям центра Интернета вещей.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md)
