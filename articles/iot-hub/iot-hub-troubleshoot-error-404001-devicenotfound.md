---
title: Устранение неполадок Azure IoT концентратор ошибка 404001 УстройствоNotFound
description: Понять, как исправить ошибку 404001 УстройствоNotFound
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 15aa21c2ec2c11bb251f7208fd22c92ceb859d6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960832"
---
# <a name="404001-devicenotfound"></a>404001 DeviceNotFound

В этой статье описаны причины и решения для **404001 DeviceNotFound** ошибок.

## <a name="symptoms"></a>Симптомы

Во время связи между облаками и устройствами (C2D), такой как C2D-сообщение, двойное обновление или прямой метод, операция завершается ошибкой с ошибкой **404001 DeviceNotFound.**

## <a name="cause"></a>Причина

Операция не удалась, так как устройство не может быть найдено Концентратором IoT. Устройство либо не зарегистрировано, либо отключено.

## <a name="solution"></a>Решение

Зарегистрируйте идентификатор устройства, который вы использовали, а затем повторите попытку.