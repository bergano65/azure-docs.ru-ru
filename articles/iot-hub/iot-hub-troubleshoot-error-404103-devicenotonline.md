---
title: Устранение неполадок Azure IoT концентратор ошибка 404103 DeviceNotOnline
description: Понять, как исправить ошибку 404103 DeviceNotOnline
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: e648428f924cfc33421c8591c41f7ac85b05a033
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960819"
---
# <a name="404103-devicenotonline"></a>404103 DeviceNotOnline

В этой статье описаны причины и решения ошибок **404103 DeviceNotOnline.**

## <a name="symptoms"></a>Симптомы

Прямой метод к устройству не удается с ошибкой **404103 DeviceNotOnline,** даже если устройство находится в сети. 

## <a name="cause"></a>Причина

Если вы знаете, что устройство находится в сети и по-прежнему получить ошибку, это вероятно, потому что прямой метод обратного вызова не зарегистрирован на устройстве.

## <a name="solution"></a>Решение

Чтобы настроить устройство правильно для прямого вызова вызова метода, [см.](iot-hub-devguide-direct-methods.md#handle-a-direct-method-on-a-device)