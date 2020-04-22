---
title: Устранение неполадок ВКЛ Azure IoT ошибка 504101 GatewayTimeout
description: Понять, как исправить ошибку 504101 GatewayTimeout
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: amqp
ms.openlocfilehash: 373acc30ed652a7f540e840dfad5eeeda65ca179
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759555"
---
# <a name="504101-gatewaytimeout"></a>504101 GatewayTimeout

В этой статье описаны причины и решения ошибок **504101 GatewayTimeout.**

## <a name="symptoms"></a>Симптомы

При попытке вызвать прямой метод от IoT Hub к устройству запрос завершается ошибкой с ошибкой **504101 GatewayTimeout.**

## <a name="cause"></a>Причина:

### <a name="cause-1"></a>Причина 1

IoT Концентратор столкнулся с ошибкой и не смог подтвердить, завершен ли прямой метод до отсеива.

### <a name="cause-2"></a>Причина 2

При использовании более ранней версии SDK Azure IoT C (<1.19.0) связь между устройством и концентратором IoT может быть удалена в бесшумно из-за ошибки.

## <a name="solution"></a>Решение

### <a name="solution-1"></a>Решение 1

Выдать повторную попытку.

### <a name="solution-2"></a>Решение 2

Обновление до последней версии SDK Azure IOT C.