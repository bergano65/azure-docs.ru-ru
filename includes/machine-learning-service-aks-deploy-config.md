---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/26/2019
ms.author: larryfr
ms.openlocfilehash: d59482c04c4875cb6b17ceb405e9d3ab97a741d6
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565138"
---
Записи в `deploymentconfig.json` документе соответствуют параметрам для [аксвебсервице. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py). В следующей таблице описывается сопоставление между сущностями в документе JSON и параметрами метода.

| Сущность JSON | Параметр метода | Описание |
| ----- | ----- | ----- |
| `computeType` | Н/Д | Целевой объект вычисления. Для AKS значение должно быть `aks`. |
| `autoScaler` | Н/Д | Содержит элементы конфигурации для автомасштабирования. См. таблицу автомасштабирования. |
| &emsp;&emsp;`autoscaleEnabled` | `autoscale_enabled` | Включить ли автоматическое масштабирование для веб-службы. Значение `numReplicas`, если  = ,; в`False`противном случае. `True` `0` |
| &emsp;&emsp;`minReplicas` | `autoscale_min_replicas` | Минимальное число контейнеров, используемых при автомасштабировании этой веб-службы. По умолчанию, `1`. |
| &emsp;&emsp;`maxReplicas` | `autoscale_max_replicas` | Максимальное число контейнеров, используемых при автомасштабировании этой веб-службы. По умолчанию, `10`. |
| &emsp;&emsp;`refreshPeriodInSeconds` | `autoscale_refresh_seconds` | Частота, с которой Автомасштабирование пытается масштабировать эту веб-службу. По умолчанию, `1`. |
| &emsp;&emsp;`targetUtilization` | `autoscale_target_utilization` | Целевая Загрузка (в процентах от 100), которую должен поддерживать Автомасштабирование для этой веб-службы. По умолчанию, `70`. |
| `dataCollection` | Н/Д | Содержит элементы конфигурации для сбора данных. |
| &emsp;&emsp;`storageEnabled` | `collect_model_data` | Указывает, следует ли включить сбор данных модели для веб-службы. По умолчанию, `False`. |
| `authEnabled` | `auth_enabled` | Следует ли включить проверку подлинности для веб-службы. По умолчанию, `True`. |
| `containerResourceRequirements` | Н/Д | Контейнер для сущностей ЦП и памяти. |
| &emsp;&emsp;`cpu` | `cpu_cores` | Количество ядер ЦП, выделяемых для этой веб-службы. Значения по умолчанию`0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | Объем памяти (в ГБ), выделяемой для данной веб-службы. Параметры`0.5` |
| `appInsightsEnabled` | `enable_app_insights` | Следует ли включить ведение журнала Application Insights для веб-службы. По умолчанию, `False`. |
| `scoringTimeoutMs` | `scoring_timeout_ms` | Время ожидания принудительного выполнения для вызовов оценки веб-службы. По умолчанию, `60000`. |
| `maxConcurrentRequestsPerContainer` | `replica_max_concurrent_requests` | Максимальное число одновременных запросов на узел для этой веб-службы. По умолчанию, `1`. |
| `maxQueueWaitMs` | `max_request_wait_time` | Максимальное время, в течение которого запрос будет оставаться в очереди три (в миллисекундах), прежде чем будет возвращена ошибка 503. По умолчанию, `500`. |
| `numReplicas` | `num_replicas` | Число контейнеров, выделяемых для этой веб-службы. Значение по умолчанию отсутствует. Если этот параметр не задан, автомасштабирование включено по умолчанию. |
| `keys` | Н/Д | Содержит элементы конфигурации для ключей. |
| &emsp;&emsp;`primaryKey` | `primary_key` | Первичный ключ проверки подлинности, используемый для этой WebService |
| &emsp;&emsp;`secondaryKey` | `secondary_key` | Вторичный ключ проверки подлинности, используемый для этой WebService |
| `gpuCores` | `gpu_cores` | Число ядер GPU, выделяемых для этой WebService. Значение по умолчанию: 1. Поддерживаются только целые значения числа. |
| `livenessProbeRequirements` | Н/Д | Содержит элементы конфигурации для требований проверки на актуальность. |
| &emsp;&emsp;`periodSeconds` | `period_seconds` | Частота (в секундах) выполнения проверки на актуальность. По умолчанию — 10 секунд. Минимальное значение равно 1. |
| &emsp;&emsp;`initialDelaySeconds` | `initial_delay_seconds` | Время в секундах после запуска контейнера до инициации проверок в режиме реального времени. По умолчанию — 310 |
| &emsp;&emsp;`timeoutSeconds` | `timeout_seconds` | Число секунд, по истечении которых истекает время ожидания проверки актуальности. Значение по умолчанию — 2 секунды. Минимальное значение равно 1 |
| &emsp;&emsp;`successThreshold` | `success_threshold` | Минимальное число последовательных успешных попыток проверки актуальности, которое должно считаться успешным после сбоя. По умолчанию равен 1. Минимальное значение равно 1. |
| &emsp;&emsp;`failureThreshold` | `failure_threshold` | Когда модуль Pod запускается и проверка на актуальность завершается неудачно, Kubernetes попытается Фаилуресрешолд раз перед тем, как выдать. Значение по умолчанию — 3. Минимальное значение равно 1. |
| `namespace` | `namespace` | Пространство имен Kubernetes, в котором развернута WebService. До 63 строчных букв ("a-z", "0"-"9") и дефисов ("-"). Первый и последний символы не могут быть дефисами. |

Следующий код JSON представляет собой пример конфигурации развертывания для использования с CLI:

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
