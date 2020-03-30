---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 4f13c171c5fafb13875f5f87d4eb3d6013f0ff30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79485952"
---
Записи в `deploymentconfig.json` карте документа по параметрам [aciWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration?view=azure-ml-py). В следующей таблице описывается отображение между сущностями в документе JSON и параметрами метода:

| Лицо JSON | Параметр метода | Описание |
| ----- | ----- | ----- |
| `computeType` | Н/Д | Целевой объект вычисления. Для ACI значение должно `ACI`быть . |
| `containerResourceRequirements` | Н/Д | Контейнер для процессора и объектов памяти. |
| &emsp;&emsp;`cpu` | `cpu_cores` | Количество ядер процессора для выделения. По умолчанию`0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | Объем памяти (в ГБ), чтобы выделить для этого веб-сервиса. По умолчанию`0.5` |
| `location` | `location` | Область Azure для развертывания этой Web-службы. Если не указано местоположение рабочего пространства будет использоваться. Более подробную информацию о доступных регионах можно найти здесь: [Регионы ACI](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) |
| `authEnabled` | `auth_enabled` | Следует ли включить auth для этого Webservice. По умолчанию ложные |
| `sslEnabled` | `ssl_enabled` | Включить ли SSL для этого Webservice. Значение по умолчанию — False. |
| `appInsightsEnabled` | `enable_app_insights` | Включить ли AppInsights для этого Веб-сервиса. По умолчанию ложные |
| `sslCertificate` | `ssl_cert_pem_file` | Файл сертификата необходим для включения SSL |
| `sslKey` | `ssl_key_pem_file` | Ключевой файл необходим для включения SSL |
| `cname` | `ssl_cname` | Загвоздка для включения SSL |
| `dnsNameLabel` | `dns_name_label` | Dns имя этикетки для скоринга конечная точка. Если не указано, для скоринговой точки будет создана уникальная метка имени dns. |

Следующий JSON является примером конфигурации развертывания для использования с CLI:

```json
{
    "computeType": "aci",
    "containerResourceRequirements":
    {
        "cpu": 0.5,
        "memoryInGB": 1.0
    },
    "authEnabled": true,
    "sslEnabled": false,
    "appInsightsEnabled": false
}
```