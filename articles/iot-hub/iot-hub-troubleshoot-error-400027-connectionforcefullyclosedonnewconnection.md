---
title: Устранение неполадок Azure IoT концентратор ошибка 400027 ПодключениеForceforceclosedonNewConnection
description: Понять, как исправить ошибку 400027 ConnectionForcefullyClosedOnNewConnection
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: f4949816f516c6a6b60cfda0602f458256370d40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960533"
---
# <a name="400027-connectionforcefullyclosedonnewconnection"></a>400027 ConnectionForcefullyClosedOnNewConnection

В этой статье описаны причины и решения для **400027 ConnectionForcefullyClosedOnNewConnection** ошибок.

## <a name="symptoms"></a>Симптомы

Операция с двумя устройствами к облаку (например, свойства чтения или патча), или прямой вызов метода не сдались с кодом ошибки **400027.**

## <a name="cause"></a>Причина

Другой клиент создал новое соединение с IoT Hub, используя те же учетные данные, поэтому IoT Hub закрыл предыдущее соединение. IoT Концентратор не позволяет более чем одному клиенту подключаться, используя один и тот же набор учетных данных.

## <a name="solution"></a>Решение

Убедитесь, что каждый клиент подключается к IoT Hub, используя свою собственную идентификацию.