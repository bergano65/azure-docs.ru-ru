---
title: Устранение неполадок Azure IoT концентратор ошибка 429001 ThrottlingException
description: Поймите, как исправить ошибку 429001 ThrottlingException
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 3095e398d7e5cfe59085144d5bb4e8dc33618064
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960702"
---
# <a name="429001-throttlingexception"></a>429001 ThrottlingException

В этой статье описаны причины и решения ошибок **429001 ThrottlingException.**

## <a name="symptoms"></a>Симптомы

Ваши запросы в IoT Концентратор не сдается с ошибкой **429001 ThrottlingException**.

## <a name="cause"></a>Причина

Для запрашиваемых операций были превышены [лимиты регулирования](./iot-hub-devguide-quotas-throttling.md) IoT Hub.

## <a name="solution"></a>Решение

Проверьте, если вы нажимаете на предел регулирования, сравнивая ваше *сообщение Телеметрии отправить попытки* метрики против пределов, указанных выше. Вы также можете проверить *количество метрики ошибок регулирования.* Для получения дополнительной информации об этих и других [IoT Hub metrics and how to use them](./iot-hub-metrics.md#iot-hub-metrics-and-how-to-use-them)метриках, доступных для IoT Hub, см.

IoT Концентратор возвращает 429 ThrottlingException только после того, как лимит был нарушен слишком долго. Это делается для того, чтобы ваши сообщения не отменялись, если ваш концентратор IoT получает взрыв трафика. В то же время Центр Интернета вещей обрабатывает сообщения на скорости регулирования операций, которая может быть низкой, если не обработан слишком большой объем трафика. См. о [формировании трафика Центра Интернета вещей](./iot-hub-devguide-quotas-throttling.md#traffic-shaping).

## <a name="next-steps"></a>Дальнейшие действия

Рассмотрите возможность [расширения концентратора IoT,](./iot-hub-scaling.md) если вы столкнулись с ограничениями квот или регулирования.