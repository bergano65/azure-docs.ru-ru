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
ms.openlocfilehash: b74a93e15d533bf9b15797e6371a25230f7a08f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960676"
---
# <a name="504101-gatewaytimeout"></a>504101 GatewayTimeout

В этой статье описаны причины и решения ошибок **504101 GatewayTimeout.**

## <a name="symptoms"></a>Симптомы

При попытке вызвать прямой метод от IoT Hub к устройству запрос завершается ошибкой с ошибкой **504101 GatewayTimeout.**

## <a name="cause"></a>Причина

### <a name="cause-1"></a>Причина 1

IoT Концентратор столкнулся с ошибкой и не смог подтвердить, завершен ли прямой метод до отсеива.

### <a name="cause-2"></a>Причина 2

При использовании более ранней версии SDK Azure IoT C (<1.19.0) связь между устройством и концентратором IoT может быть удалена в бесшумно из-за ошибки.

## <a name="solution"></a>Решение

### <a name="solution-1"></a>Решение 1

Выдать повторную попытку.

### <a name="solution-2"></a>Решение 2

Обновление до последней версии SDK Azure IOT C.