---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 11/04/2019
ms.author: larryfr
ms.openlocfilehash: c288ea4a2c62999f27b3f07c39df40dae7ab1426
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926945"
---
Записи в документе `deploymentconfig.json` соответствуют параметрам для [локалвебсервице. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py). В следующей таблице описывается сопоставление между сущностями в документе JSON и параметрами метода.

| Сущность JSON | Параметр метода | Описание |
| ----- | ----- | ----- |
| `computeType` | Нет данных | Целевой объект вычисления. Для локальных целевых объектов значение должно быть `local`. |
| `port` | `port` | Локальный порт, на который будет предоставляться конечная точка HTTP службы. |

Этот формат JSON является примером конфигурации развертывания для использования с CLI:

```json
{
    "computeType": "local",
    "port": 32267
}
```
