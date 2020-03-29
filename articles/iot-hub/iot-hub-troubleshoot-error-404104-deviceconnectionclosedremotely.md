---
title: Устранение неполадок Azure IoT концентратор ошибка 404104 DeviceConnectionClosedУдаленноудаленноудаленно
description: Понять, как исправить ошибку 404104 DeviceConnectionClosedУдаленноудаленно
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 686946b65b4b7540f404a291c87c5ad9c7b7a0ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960806"
---
# <a name="404104-deviceconnectionclosedremotely"></a>404104 DeviceConnectionClosedRemotely

В этой статье описаны причины и решения для **404104 DeviceConnectionClosedУдаленноудаленной ошибки.**

## <a name="symptoms"></a>Симптомы

### <a name="symptom-1"></a>Симптом 1

Устройства отключаются с регулярной просечестью (каждые 65 минут, например), и вы видите **404104 DeviceConnectionClosedRemotely** в диагностических журналах IoT Hub. Иногда вы также видите **401003 IoTHubНесанкционированное** и успешное событие подключения устройства менее чем через минуту.

### <a name="symptom-2"></a>Симптом 2

Устройства отключаются случайным образом, и вы видите **404104 DeviceConnectionClosedУдаленноудаленно удаленно** в диагностических журналах IoT Hub.

### <a name="symptom-3"></a>Симптом 3

Многие устройства отключаются сразу, вы видите падение в [метрика подключенных устройств,](iot-hub-metrics.md)и есть более **404104 DeviceConnectionClosedRemotely** и [500xxx Внутренние ошибки](iot-hub-troubleshoot-error-500xxx-internal-errors.md) в диагностических журналах, чем обычно.

## <a name="causes"></a>Причины

### <a name="cause-1"></a>Причина 1

[Срок действия токена SAS, используемого для подключения к Концентратору IoT,](iot-hub-devguide-security.md#security-tokens) истек, что приводит к отключению устройства. Соединение восстановлено при обновлении токена устройством. Например, [токен SAS истекает каждый час по умолчанию для C SDK,](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-authentication)что может привести к регулярным отключениям.

Чтобы узнать больше, [см.](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#cause-1)

### <a name="cause-2"></a>Причина 2

Некоторые возможности включают в себя:

- Устройство потеряло основное подключение к сети дольше, чем [МЗТТ, в](iot-hub-mqtt-support.md#default-keep-alive-timeout)результате чего был удален тайм-аут. Настройка МЗТТ может быть разной для одного устройства.

- Устройство отправило сбросить tCP/IP-уровень, но не `MQTT DISCONNECT`отправило уровень приложения. В основном, устройство резко закрыло основное соединение розетки. Иногда эта проблема вызвана ошибками в старых версиях SDK Azure IoT.

- Боковое приложение устройства сбой.

### <a name="cause-3"></a>Причина 3

Концентратор IoT может испытывать переходную проблему. Посмотреть [причину внутренней ошибки сервера IoT](iot-hub-troubleshoot-error-500xxx-internal-errors.md#cause)Hub.

## <a name="solutions"></a>Решения

### <a name="solution-1"></a>Решение 1

Смотрите [401003 IoTHubНесанкционированное решение 1](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#solution-1)

### <a name="solution-2"></a>Решение 2

- Убедитесь, что устройство имеет хорошее подключение к IoT концентратор, [тестируя соединение.](tutorial-connectivity.md) Если сеть ненадежна или непреклонна, мы не рекомендуем увеличивать значение «сохраняя в живых», поскольку это может привести к обнаружению (например, с помощью предупреждений Azure Monitor). 

- Используйте последние версии [IoT SDK.](iot-hub-devguide-sdks.md)

### <a name="solution-3"></a>Решение 3

Смотрите [решения внутренних ошибок сервера IoT Hub.](iot-hub-troubleshoot-error-500xxx-internal-errors.md#solution)

## <a name="next-steps"></a>Дальнейшие действия

Мы рекомендуем использовать SDK-устройства Azure IoT для надежного управления соединениями. Чтобы узнать больше, смотрите [Управление подключением и надежными сообщениями, используя SDK-концентратор устройства Azure IoT](iot-hub-reliability-features-in-sdks.md)