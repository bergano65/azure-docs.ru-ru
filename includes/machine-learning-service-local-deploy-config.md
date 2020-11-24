---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 9240f2616134055d6c97b9b85c264aa7635139cc
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95558310"
---
Записи в документе `deploymentconfig.json` соответствуют параметрам для [LocalWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py). В следующей таблице описано сопоставление сущностей в документе JSON и параметров метода:

| Сущность JSON | Параметр метода | Описание: |
| ----- | ----- | ----- |
| `computeType` | Н/Д | Целевой объект вычисления. Для локальных целевых объектов нужно задать значение `local`. |
| `port` | `port` | Локальный порт, на который будет предоставляться конечная точка HTTP службы. |

Код JSON — это пример конфигурации развертывания для использования с CLI:

```json
{
    "computeType": "local",
    "port": 32267
}
```