---
title: Устранение неполадок Azure IoT концентратор ошибка 412002 DeviceMessageLockLost
description: Понять, как исправить ошибку 412002 DeviceMessageLockLost
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 66461b23432a3e8b7ae4ad1fdc078fba9ca05646
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960767"
---
# <a name="412002-devicemessagelocklost"></a>412002 DeviceMessageLockLost

В этой статье описаны причины и решения ошибок **412002 DeviceMessageLockLost.**

## <a name="symptoms"></a>Симптомы

При попытке отправить сообщение об облаке к устройству запрос завершается ошибкой **412002 DeviceMessageLockLost**.

## <a name="cause"></a>Причина

Когда устройство получает сообщение об облаке к устройству [`ReceiveAsync()`](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync?view=azure-dotnet)из очереди (например, с помощью) сообщение блокируется Концентратором IoT на время блокировки в одну минуту. Если устройство пытается завершить сообщение после истечения времени блокировки, IoT Концентратор бросает это исключение.

## <a name="solution"></a>Решение

Если IoT Концентратор не получает уведомление в течение одной минуты времени блокировки, он устанавливает сообщение обратно в состояние *Enqueued.* Устройство может попытаться получить сообщение снова. Чтобы предотвратить ошибку в будущем, реализуем логику стороны устройства, чтобы завершить сообщение в течение одной минуты после получения сообщения. Этот минутный тайм-аут не может быть изменен.