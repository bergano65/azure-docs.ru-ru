---
title: Привязка концентратора Azure IoT для функций Azure
description: Научитесь использовать триггер и связывание Концентратора IoT в функциях Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 1c25543b16c3486a8f6a445427346382faaaa09a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586141"
---
# <a name="azure-iot-hub-bindings-for-azure-functions"></a>Привязка концентратора Azure IoT для функций Azure

В этом наборе статей объясняется, как работать с привязками функций Azure для Концентратора IoT. Поддержка концентратора IoT основана на [связывании концентраторов событий Azure.](functions-bindings-event-hubs.md)

> [!IMPORTANT]
> В то время как следующие образцы кода используют API концентратора событий, данный синтаксис применим к функциям IoT Hub.

| Действие | Тип |
|--------|------|
| Реагировать на события, отправленные в поток событий концентратора IoT. | [Триггер](./functions-bindings-event-iot-trigger.md) |
| Запись событий в поток событий IoT | [Выходная привязка](./functions-bindings-event-iot-output.md) |

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>Дальнейшие действия

- [Реагирование на события, отправленные в поток событийного центра событий (Trigger)](./functions-bindings-event-iot-trigger.md)
- [Запись событий в поток событий (связка вывода)](./functions-bindings-event-iot-output.md)
