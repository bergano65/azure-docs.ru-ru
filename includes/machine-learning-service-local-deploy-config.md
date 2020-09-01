---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 60c754aeec3157abee02eb136dcf148c440d8da0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "79477278"
---
Записи в документе `deploymentconfig.json` соответствуют параметрам для [LocalWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py). В следующей таблице описано сопоставление сущностей в документе JSON и параметров метода:

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
