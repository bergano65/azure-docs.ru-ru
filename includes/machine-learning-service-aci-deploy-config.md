---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 10/06/2019
ms.author: larryfr
ms.openlocfilehash: b3c1ad48da0ef28c73e3af5d1e30414534f4e94d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73800080"
---
Записи в документе `deploymentconfig.json` соответствуют параметрам для [аЦивебсервице. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration?view=azure-ml-py). В следующей таблице описывается сопоставление между сущностями в документе JSON и параметрами метода.

| Сущность JSON | Параметр метода | Description (Описание) |
| ----- | ----- | ----- |
| `computeType` | Нет данных | Целевой объект вычисления. Для ACI значение должно быть `ACI`. |
| `containerResourceRequirements` | Нет данных | Контейнер для сущностей ЦП и памяти. |
| &emsp;&emsp;`cpu` | `cpu_cores` | Количество ядер ЦП для выделения. Значения по умолчанию, `0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | Объем памяти (в ГБ), выделяемой для данной веб-службы. По умолчанию, `0.5` |
| `location` | `location` | Регион Azure для развертывания этой службы. Если не указано, будет использоваться расположение рабочей области. Дополнительные сведения о доступных регионах можно найти здесь: [ACI regions](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) . |
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