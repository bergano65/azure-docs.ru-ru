---
title: Устранение неполадок Azure IoT концентратор ошибка 403004 DeviceMaximumQueue.
description: Понять, как исправить ошибку 403004 DeviceMaximumQueueУраУинг
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 5cc8bae0f0245f5c4b45ca0cd446582b04788c21
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758758"
---
# <a name="403004-devicemaximumqueuedepthexceeded"></a>403004 DeviceMaximumQueueDepthExceeded

В этой статье описаны причины и решения для **403004 DeviceMaximumRu.Ru.**

## <a name="symptoms"></a>Симптомы

При попытке отправить сообщение об облаке к устройству запрос завершается с ошибкой **403004** или **DeviceMaximum'Uuu.Ue.**

## <a name="cause"></a>Причина:

Основная причина заключается в том, что количество сообщений, закрытых для устройства, превышает [предел очереди (50).](./iot-hub-devguide-quotas-throttling.md#other-limits)

Наиболее вероятной причиной, по которой вы столкнулись с этим пределом, является то, что вы используете HTTPS для получения сообщения, что приводит к непрерывному использованию `ReceiveAsync`опроса, в результате чего IoT Концентр регулирует запрос.

## <a name="solution"></a>Решение

Схема сообщений, передаваемых из облака на устройство с помощью HTTPS, может использоваться на периодически подключаемых устройствах, которые редко проверяют наличие новых сообщений (реже, чем раз в 25 минут). Чтобы уменьшить вероятность попадания в предел очереди, переключитесь на AM-P или МЗТТ для сообщений об облаке к устройству.

Кроме того, увеличьте логику стороны устройства, чтобы завершить, отклонить или быстро отказаться от сообщений в очереди, сократить время для жизни или рассмотреть возможность отправки меньшего количества сообщений. См. о [сроке жизни сообщений, отправляемых из облака на устройство](./iot-hub-devguide-messages-c2d.md#message-expiration-time-to-live).

Наконец, рассмотрите возможность использования API очереди [очистки](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/purgecommandqueue) для периодических очистки ожидающих сообщений до достижения лимита.