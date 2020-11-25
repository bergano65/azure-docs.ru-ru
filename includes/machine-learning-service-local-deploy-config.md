---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 9240f2616134055d6c97b9b85c264aa7635139cc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027978"
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