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
ms.openlocfilehash: 1edf723aa885ff18d2ce2dda4d71b67700a98a5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79497493"
---
# <a name="403004-devicemaximumqueuedepthexceeded"></a>403004 DeviceMaximumQueueDepthExceeded

В этой статье описаны причины и решения для **403004 DeviceMaximumRu.Ru.**

## <a name="symptoms"></a>Симптомы

При попытке отправить сообщение об облаке к устройству запрос завершается с ошибкой **403004** или **DeviceMaximum'Uuu.Ue.**

## <a name="cause"></a>Причина

Основная причина заключается в том, что количество сообщений, закрытых для устройства, превышает [предел очереди (50).](./iot-hub-devguide-quotas-throttling.md#other-limits)

Наиболее вероятной причиной, по которой вы столкнулись с этим пределом, является то, что вы используете HTTPS для получения сообщения, что приводит к непрерывному использованию `ReceiveAsync`опроса, в результате чего IoT Концентр регулирует запрос.

## <a name="solution"></a>Решение

Схема сообщений, передаваемых из облака на устройство с помощью HTTPS, может использоваться на периодически подключаемых устройствах, которые редко проверяют наличие новых сообщений (реже, чем раз в 25 минут). Чтобы уменьшить вероятность попадания в предел очереди, переключитесь на AM-P или МЗТТ для сообщений об облаке к устройству.

Кроме того, увеличьте логику стороны устройства, чтобы завершить, отклонить или быстро отказаться от сообщений в очереди, сократить время для жизни или рассмотреть возможность отправки меньшего количества сообщений. См. о [сроке жизни сообщений, отправляемых из облака на устройство](./iot-hub-devguide-messages-c2d.md#message-expiration-time-to-live).

Наконец, рассмотрите возможность использования API очереди [очистки](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/purgecommandqueue) для периодических очистки ожидающих сообщений до достижения лимита.