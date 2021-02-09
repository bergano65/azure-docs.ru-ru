---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 02/05/2021
ms.openlocfilehash: b06b91e972fd07543cf02105360cb0400ef6b0f1
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831560"
---
Используйте ответы времени выполнения агента IoT Edge, чтобы устранить ошибки, связанные с вычислением. Ниже перечислены возможные ответы:

* 200 – OK
* 400 — конфигурация развертывания имеет неправильный формат или недопустима.
* 417-у устройства нет набора конфигураций развертывания.
* 412 — версия схемы в конфигурации развертывания недопустима.
* 406 — устройство IoT Edge работает в автономном режиме или не отправляет отчеты о состоянии.
* 500 — в среде выполнения IoT Edge произошла ошибка.

Дополнительные сведения см. в разделе [агент IOT Edge](/azure/iot-edge/iot-edge-runtime?view=iotedge-2018-06&preserve-view=true#iot-edge-agent).

Следующая ошибка связана со службой IoT Edge на Azure Stack пограничных Pro.<!--/ Data Box Gateway--> возможностями, доступными на любом устройстве.

### <a name="compute-modules-have-unknown-status-and-cant-be-used"></a>Модули вычислений имеют неизвестное состояние и не могут использоваться

#### <a name="error-description"></a>Описание ошибки

Все модули на устройстве отображают неизвестное состояние и не могут использоваться. Неизвестное состояние сохраняется при перезагрузке.<!--Original Support ticket relates to trying to deploy a container app on a Hub. Based on the work item, I assume the error description should not be that specific, and that the error applies to Azure Stack Edge Devices, which is the focus of this troubleshooting.-->

#### <a name="suggested-solution"></a>Предлагаемое решение

Удалите IoT Edgeную службу, а затем повторно разверните модули. Дополнительные сведения см. в разделе [Remove IOT Edge Service](../articles/databox-online/azure-stack-edge-j-series-manage-compute.md#remove-iot-edge-service).
