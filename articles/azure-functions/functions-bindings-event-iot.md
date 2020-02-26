---
title: Привязки центра Интернета вещей Azure для функций Azure
description: Узнайте, как использовать триггер и привязку центра Интернета вещей в функциях Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 1c25543b16c3486a8f6a445427346382faaaa09a
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77586141"
---
# <a name="azure-iot-hub-bindings-for-azure-functions"></a>Привязки центра Интернета вещей Azure для функций Azure

В этом наборе статей объясняется, как работать с привязками функций Azure для центра Интернета вещей. Поддержка центра Интернета вещей основана на [привязке концентраторов событий Azure](functions-bindings-event-hubs.md).

> [!IMPORTANT]
> Хотя в следующих примерах кода используется API концентратора событий, заданный синтаксис применим к функциям центра Интернета вещей.

| Действие | Тип |
|--------|------|
| Реагирование на события, отправляемые в поток событий центра Интернета вещей. | [Триггер](./functions-bindings-event-iot-trigger.md) |
| Запись событий в поток событий Интернета вещей | [Выходная привязка](./functions-bindings-event-iot-output.md) |

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>Следующие шаги

- [Реагирование на события, отправленные в поток событий концентратора событий (триггер)](./functions-bindings-event-iot-trigger.md)
- [Запись событий в поток событий (Выходная привязка)](./functions-bindings-event-iot-output.md)
