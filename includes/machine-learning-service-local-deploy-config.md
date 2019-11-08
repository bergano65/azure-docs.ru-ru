---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 11/04/2019
ms.author: larryfr
ms.openlocfilehash: f35245aea0d7ba20561d1504b111e747ccbe5e0d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73800079"
---
Записи в документе `deploymentconfig.json` соответствуют параметрам для [локалвебсервице. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py). В следующей таблице описывается сопоставление между сущностями в документе JSON и параметрами метода.

| Сущность JSON | Параметр метода | Description (Описание) |
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
