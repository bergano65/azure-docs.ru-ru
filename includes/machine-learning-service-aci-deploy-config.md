---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/19/2019
ms.author: larryfr
ms.openlocfilehash: fd9527714b87cc35fcfc61f4858cbacee7fa95f5
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348507"
---
Записи в `deploymentconfig.json` документе соответствуют параметрам для [аЦивебсервице. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration?view=azure-ml-py). В следующей таблице описывается сопоставление между сущностями в документе JSON и параметрами метода.

| Сущность JSON | Параметр метода | Описание |
| ----- | ----- | ----- |
| `computeType` | Н/Д | Целевой объект вычисления. Для ACI значение должно быть `ACI`. |
| `containerResourceRequirements` | Н/Д | Контейнер для сущностей ЦП и памяти. |
| &emsp;&emsp;`cpu` | `cpu_cores` | Количество ядер ЦП для выделения. Значения по умолчанию`0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | Объем памяти (в ГБ), выделяемой для данной веб-службы. Параметры`0.5` |
| `location` | `location` | Регион Azure для развертывания этой службы. Если не указано, будет использоваться расположение рабочей области. Дополнительные сведения о доступных регионах можно найти здесь: [ACI регионы](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) |
| `authEnabled` | `auth_enabled` | Следует ли включить проверку подлинности для этой WebService. По умолчанию имеет значение false |
| `sslEnabled` | `ssl_enabled` | Следует ли включить SSL для этой WebService. Значение по умолчанию — false. |
| `appInsightsEnabled` | `enable_app_insights` | Следует ли включить AppInsights для этой WebService. По умолчанию имеет значение false |
| `sslCertificate` | `ssl_cert_pem_file` | Файл сертификата, необходимый, если включен протокол SSL |
| `sslKey` | `ssl_key_pem_file` | Файл ключа, необходимый, если включен протокол SSL |
| `cname` | `ssl_cname` | Запись CNAME для, если включен протокол SSL |
| `dnsNameLabel` | `dns_name_label` | Метка DNS-имени для конечной точки оценки. Если не указано, для конечной точки оценки будет создана уникальная метка DNS-имени. |

Следующий код JSON представляет собой пример конфигурации развертывания для использования с CLI:

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