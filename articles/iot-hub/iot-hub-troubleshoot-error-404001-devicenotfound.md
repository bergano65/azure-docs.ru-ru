---
title: Устранение неполадок центра Интернета вещей Azure 404001 Девиценотфаунд
description: Сведения об исправлении ошибки 404001 Девиценотфаунд
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 15aa21c2ec2c11bb251f7208fd22c92ceb859d6d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "76960832"
---
# <a name="404001-devicenotfound"></a>404001 DeviceNotFound

В этой статье описываются причины и решения для ошибок **404001 девиценотфаунд** .

## <a name="symptoms"></a>Симптомы

При обмене данными между облаком и устройством (C2D), например сообщением C2D, двойника Update или прямым методом, операция завершается ошибкой с ошибкой **404001 девиценотфаунд**.

## <a name="cause"></a>Причина:

Не удалось выполнить операцию, так как центр Интернета вещей не может найти устройство. Устройство либо не зарегистрировано, либо отключено.

## <a name="solution"></a>Решение

Зарегистрируйте идентификатор устройства, который вы использовали, и повторите попытку.