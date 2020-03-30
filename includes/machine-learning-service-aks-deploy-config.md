---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: c71f35a06d904b45cb014d5199197220b57cf230
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79486061"
---
Записи в `deploymentconfig.json` карте документа по параметрам [для AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py). В следующей таблице описывается отображение между сущностями в документе JSON и параметрами метода:

| Лицо JSON | Параметр метода | Описание |
| ----- | ----- | ----- |
| `computeType` | Н/Д | Целевой объект вычисления. Для AKS значение должно `aks`быть . |
| `autoScaler` | Н/Д | Содержит элементы конфигурации для автомасштабирования. Посмотреть таблицу автоскалатора. |
| &emsp;&emsp;`autoscaleEnabled` | `autoscale_enabled` | Включить ли автоматическое масштабирование для веб-службы. `numReplicas`  = Если `0` `True`; ; в `False`противном случае, . |
| &emsp;&emsp;`minReplicas` | `autoscale_min_replicas` | Минимальное количество контейнеров для использования при автоматическом масштабировании этого веб-сервиса. По `1`умолчанию, . |
| &emsp;&emsp;`maxReplicas` | `autoscale_max_replicas` | Максимальное количество контейнеров для использования при автоматическом масштабировании этого веб-сервиса. По `10`умолчанию, . |
| &emsp;&emsp;`refreshPeriodInSeconds` | `autoscale_refresh_seconds` | Как часто автоскейл пытается масштабировать этот веб-сервис. По `1`умолчанию, . |
| &emsp;&emsp;`targetUtilization` | `autoscale_target_utilization` | Целевое использование (в процентах из 100), что автоскейл, что автоскейл, что автоскейл, которые автоскейла должны попытаться сохранить для этого веб-сервиса. По `70`умолчанию, . |
| `dataCollection` | Н/Д | Содержит элементы конфигурации для сбора данных. |
| &emsp;&emsp;`storageEnabled` | `collect_model_data` | Включить ли сбор данных модели для веб-службы. По `False`умолчанию, . |
| `authEnabled` | `auth_enabled` | Включить или не включить проверку подлинности ключей для веб-службы. И `tokenAuthEnabled` `authEnabled` так `True`и не может быть . По `True`умолчанию, . |
| `tokenAuthEnabled` | `token_auth_enabled` | Включить или не включить проверку подлинности токенов для веб-службы. И `tokenAuthEnabled` `authEnabled` так `True`и не может быть . По `False`умолчанию, . |
| `containerResourceRequirements` | Н/Д | Контейнер для процессора и объектов памяти. |
| &emsp;&emsp;`cpu` | `cpu_cores` | Количество ядер процессора для выделения для этого веб-сервиса. По умолчанию`0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | Объем памяти (в ГБ), чтобы выделить для этого веб-сервиса. По умолчанию`0.5` |
| `appInsightsEnabled` | `enable_app_insights` | Включить ли журнал Application Insights для веб-службы. По `False`умолчанию, . |
| `scoringTimeoutMs` | `scoring_timeout_ms` | Тайм-аут для обеспечения для скоринга звонки в веб-службу. По `60000`умолчанию, . |
| `maxConcurrentRequestsPerContainer` | `replica_max_concurrent_requests` | Максимальные одновременные запросы на узел для этого веб-сервиса. По `1`умолчанию, . |
| `maxQueueWaitMs` | `max_request_wait_time` | Максимальное время, в течение чем запрос будет находиться в очереди (в миллисекундах) до возврата ошибки 503. По `500`умолчанию, . |
| `numReplicas` | `num_replicas` | Количество контейнеров, выделяемых для этого веб-сервиса. Нет значения по умолчанию. Если этот параметр не установлен, автоскейлист включен по умолчанию. |
| `keys` | Н/Д | Содержит элементы конфигурации для ключей. |
| &emsp;&emsp;`primaryKey` | `primary_key` | Основной auth ключ для использования для этого Webservice |
| &emsp;&emsp;`secondaryKey` | `secondary_key` | Вторичный ключ auth, который можно использовать для этого Webservice |
| `gpuCores` | `gpu_cores` | Количество ядер графического процессора (реплика для контейнеров), которые можно выделить для этого Webservice. Значение по умолчанию: 1. Поддерживает только все значения числа. |
| `livenessProbeRequirements` | Н/Д | Содержит элементы конфигурации для требований зонда живости. |
| &emsp;&emsp;`periodSeconds` | `period_seconds` | Как часто (в секундах) выполнять зонд живости. По умолчанию до 10 секунд. Минимальное значение 1. |
| &emsp;&emsp;`initialDelaySeconds` | `initial_delay_seconds` | Количество секунд после запуска контейнера до начала зондов живости. По умолчанию до 310 |
| &emsp;&emsp;`timeoutSeconds` | `timeout_seconds` | Количество секунд, после которых зонд живости разключает. По умолчанию до 2 секунд. Минимальное значение 1 |
| &emsp;&emsp;`successThreshold` | `success_threshold` | Минимальные последовательные успехи для зонда живости, который будет считаться успешным после неудачи. По умолчанию равен 1. Минимальное значение 1. |
| &emsp;&emsp;`failureThreshold` | `failure_threshold` | Когда Pod начинается и зонд живости выходит из строя, Kubernetes будет пытаться failureThreshold раз, прежде чем сдаваться. По умолчанию до 3. Минимальное значение 1. |
| `namespace` | `namespace` | Пространство имен Kubernetes, в которое развернут веб-сервис. До 63 символов в нижнем регистре ('a'-''z', '0'-'9') и дефис ('-') символов. Первый и последний символы не могут быть дефисами. |

Следующий JSON является примером конфигурации развертывания для использования с CLI:

```json
{
    "computeType": "aks",
    "autoScaler":
    {
        "autoscaleEnabled": true,
        "minReplicas": 1,
        "maxReplicas": 3,
        "refreshPeriodInSeconds": 1,
        "targetUtilization": 70
    },
    "dataCollection":
    {
        "storageEnabled": true
    },
    "authEnabled": true,
    "containerResourceRequirements":
    {
        "cpu": 0.5,
        "memoryInGB": 1.0
    }
}
```
