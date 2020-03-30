---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 60c754aeec3157abee02eb136dcf148c440d8da0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477278"
---
Записи в `deploymentconfig.json` карте документа по параметрам [LocalWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py). В следующей таблице описывается отображение между сущностями в документе JSON и параметрами метода:

| Лицо JSON | Параметр метода | Описание |
| ----- | ----- | ----- |
| `computeType` | Н/Д | Целевой объект вычисления. Для локальных целей `local`значение должно быть . |
| `port` | `port` | Местный порт, на котором можно выставить конечную точку HTTP службы. |

Этот JSON является примером конфигурации развертывания для использования с CLI:

```json
{
    "computeType": "local",
    "port": 32267
}
```
