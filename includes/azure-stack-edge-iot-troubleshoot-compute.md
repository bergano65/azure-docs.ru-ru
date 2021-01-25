---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 01/21/2021
ms.openlocfilehash: 3defa62c55bb5ab042ade816f611ea45b39a0117
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/25/2021
ms.locfileid: "98761554"
---
Используйте ответы времени выполнения агента IoT Edge, чтобы устранить ошибки, связанные с вычислением. Ниже перечислены возможные ответы:

* 200 – OK
* 400 — конфигурация развертывания имеет неправильный формат или недопустима.
* 417-у устройства нет набора конфигураций развертывания.
* 412 — версия схемы в конфигурации развертывания недопустима.
* 406 — устройство IoT Edge работает в автономном режиме или не отправляет отчеты о состоянии.
* 500 — в среде выполнения IoT Edge произошла ошибка.

Дополнительные сведения см. в разделе [агент IOT Edge](/azure/iot-edge/iot-edge-runtime?view=iotedge-2018-06&preserve-view=true#iot-edge-agent).