---
title: Привязка концентратору Azure IoT для функций Azure
description: Научитесь писать сообщения в потоки концентратов Azure IoT с помощью функций Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: d4dbf43fb5684d829e581be29832e94ad46b2936
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277431"
---
# <a name="azure-iot-hub-output-binding-for-azure-functions"></a>Привязка концентратору Azure IoT для функций Azure

В этой статье объясняется, как работать с привязками вывода Azure Functions для IoT Hub. Поддержка концентратора IoT основана на [связывании концентраторов событий Azure.](functions-bindings-event-hubs.md)

Для получения информации о настройке и деталях конфигурации, [см.](functions-bindings-event-iot.md)

> [!IMPORTANT]
> В то время как следующие образцы кода используют API концентратора событий, данный синтаксис применим к функциям IoT Hub.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs-output.md)]

## <a name="next-steps"></a>Дальнейшие действия

- [Реагирование на события, отправленные в поток событийного центра событий (Trigger)](./functions-bindings-event-iot-trigger.md)
