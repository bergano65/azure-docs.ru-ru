---
title: Устранение неполадок Azure IoT Hub 500xxx Внутренние ошибки
description: Понять, как исправить 500xxx Внутренние ошибки
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 7f3f5177e084693c45bed1088a4e1d091be100ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271048"
---
# <a name="500xxx-internal-errors"></a>Внутренние ошибки 500xxx

В этой статье описаны причины и решения для **500xxx внутренних ошибок**.

## <a name="symptoms"></a>Симптомы

Ваш запрос в IoT Концентратор не удается с ошибкой, которая начинается с 500 и / или какой-то "сервер ной ошибки". Некоторые возможности:

* **500001 ServerError**: IoT концентратор столкнулся с проблемой на стороне сервера.

* **500008 GenericTimeout**: IoT концентратор не смог выполнить запрос на подключение до отсутсвия времени.

* **ServiceUnavailable (без кода ошибки)**: Концентратор IoT столкнулся с внутренней ошибкой.

* **InternalServerError (без кода ошибки)**: IoT концентратор столкнулся с внутренней ошибкой.

## <a name="cause"></a>Причина

Существует ряд причин для ответа на ошибку 500xxx. Во всех случаях проблема, скорее всего, преходящая. Команда по работе с Центром Интернета вещей прикладывает все усилия, чтобы выполнять гарантии [Соглашения об уровне обслуживания](https://azure.microsoft.com/support/legal/sla/iot-hub/), но в работе небольших подмножеств узлов Центра Интернета вещей иногда могут возникать сбои. Если устройство пытается подключиться к узлу со сбоем, возникает эта ошибка.

## <a name="solution"></a>Решение

Чтобы уменьшить ошибки 500xxx, выдали повторную попытку с устройства. Чтобы [автоматически управлять повторами](./iot-hub-reliability-features-in-sdks.md#connection-and-retry), используйте последнюю версию [пакетов SDK для Azure IoT](./iot-hub-devguide-sdks.md). Рекомендации по исправлению временных ошибок и повторном подключении см. в [этой статье](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults).  Если проблема сохраняется, проверьте состояние [работоспособности ресурсов](./iot-hub-monitor-resource-health.md#use-azure-resource-health) и [Azure,](https://status.azure.com/) чтобы узнать, есть ли у Концентратора. Вы также можете использовать [функцию ручного отказа.](./tutorial-manual-failover.md) Если нет известных проблем и проблема продолжается, [обратитесь в службу поддержки](https://azure.microsoft.com/support/options/) для дальнейшего расследования.
