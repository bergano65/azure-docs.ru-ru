---
title: Устранение проблем Azure IoT концентратор ошибка 403002 IoTHubквотаПревышес
description: Понять, как исправить ошибку 403002 IoTHubквотаПревышед
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 8312c3267e826088f34b3bffe1520703eec00bdc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961118"
---
# <a name="403002-iothubquotaexceeded"></a>403002 IoTHubQuotaExceeded

В этой статье описаны причины и решения для **403002 IoTHub'кваквовы ошибок.**

## <a name="symptoms"></a>Симптомы

Все запросы в IoT концентратор не сбой с ошибкой **403002 IoTHub квотапревышение**. На портале Azure список концентраторных устройств IoT не загружается.

## <a name="cause"></a>Причина

Перевыполнена суточная квота сообщений для концентратора IoT. 

## <a name="solution"></a>Решение

[Обновление или увеличение количества единиц на концентраторе IoT](iot-hub-upgrade.md) или ждать следующего дня UTC для ежедневной квоты для обновления.

## <a name="next-steps"></a>Дальнейшие действия

* Чтобы понять, как операции учитываются по квотам, таким как двойные запросы и прямые методы, [см.](iot-hub-devguide-pricing.md#charges-per-operation)
* Для настройки мониторинга для ежедневного использования квот нависли оповещения с метрическим *общим числом используемых сообщений.* Для пошаговых инструкций смотрите [настройку метрик и оповещений с помощью Концентратора IoT](tutorial-use-metrics-and-diags.md#set-up-metrics)