---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/28/2020
ms.author: larryfr
ms.openlocfilehash: 6970732f53ffa99849e20f279c8bdf4160c30a0a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845146"
---
Записи в документе `inferenceconfig.json` соответствуют параметрам класса [инференцеконфиг](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) . В следующей таблице описано сопоставление между сущностями в документе JSON и параметрами метода.

| Сущность JSON | Параметр метода | Description |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Путь к локальному файлу, содержащему код для выполнения для образа. |
| `runtime` | `runtime` | Необязательный параметр. Среда выполнения, используемая для образа. Поддерживаемые среды выполнения: `spark-py` и `python`. Если параметр `environment` установлен, эта запись игнорируется. |
| `condaFile` | `conda_file` | Необязательный параметр. Путь к локальному файлу, содержащему определение окружения Conda для использования в образе.  Если параметр `environment` установлен, эта запись игнорируется. |
| `extraDockerFileSteps` | `extra_docker_file_steps` | Необязательный параметр. Путь к локальному файлу, который содержит дополнительные шаги DOCKER для запуска при настройке образа.  Если параметр `environment` установлен, эта запись игнорируется.|
| `sourceDirectory` | `source_directory` | Необязательный параметр. Путь к папкам, содержащим все файлы для создания образа, что упрощает доступ к любым файлам в этой папке или вложенной папке. Вы можете отправить всю папку с локального компьютера в качестве зависимостей для веб – службы. Примечание. entry_script, conda_file и пути extra_docker_file_steps являются относительными путями к source_directory пути. |
| `enableGpu` | `enable_gpu` | Необязательный параметр. Включить ли поддержку GPU в образе. Образ GPU должен использоваться в службе Azure, например в экземплярах контейнеров Azure. Например, Машинное обучение Azure вычислений, виртуальных машин Azure и службы Kubernetes Azure. Значение по умолчанию — False. Если параметр `environment` установлен, эта запись игнорируется.|
| `baseImage` | `base_image` | Необязательный параметр. Пользовательский образ, используемый в качестве базового образа. Если базовый образ не указан, то образ будет основан на указанном параметре среды выполнения. Если параметр `environment` установлен, эта запись игнорируется. |
| `baseImageRegistry` | `base_image_registry` | Необязательный параметр. Реестр образов, содержащий базовый образ. Если параметр `environment` установлен, эта запись игнорируется.|
| `cudaVersion` | `cuda_version` | Необязательный параметр. Версия CUDA для установки образов, требующих поддержки GPU. Образ GPU должен использоваться в службе Azure. Например, службы "экземпляры контейнеров Azure", "Машинное обучение Azure вычислений", "виртуальные машины Azure" и "служба Kubernetes Azure". Поддерживаемые версии: 9,0, 9,1 и 10,0. Если задано значение `enable_gpu`, по умолчанию используется значение 9,1. Если параметр `environment` установлен, эта запись игнорируется. |
| `description` | `description` | Описание изображения. Если параметр `environment` установлен, эта запись игнорируется.  |
| `environment` | `environment` | Необязательный параметр.  [Среда](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)машинное обучение Azure.|

Следующий код JSON — это пример конфигурации вывода для использования с CLI:

```json
{
    "entryScript": "score.py",
    "runtime": "python",
    "condaFile": "myenv.yml",
    "extraDockerfileSteps": null,
    "sourceDirectory": null,
    "enableGpu": false,
    "baseImage": null,
    "baseImageRegistry": null
}
```

В файл конфигурации вывода можно включить полные спецификации Машинное обучение Azureной [среды](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) . Если эта среда не существует в рабочей области, Машинное обучение Azure создаст ее. В противном случае при необходимости Машинное обучение Azure обновит среду. Пример JSON приведен ниже.

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
            "enabled": false,
            "sharedVolumes": true,
            "shmSize": null
        },
        "environmentVariables": {
            "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
        },
        "name": "my-deploy-env",
        "python": {
            "baseCondaEnvironment": null,
            "condaDependencies": {
                "channels": [
                    "conda-forge"
                ],
                "dependencies": [
                    "python=3.6.2",
                    {
                        "pip": [
                            "azureml-defaults",
                            "azureml-telemetry",
                            "scikit-learn",
                            "inference-schema[numpy-support]"
                        ]
                    }
                ],
                "name": "project_environment"
            },
            "condaDependenciesFile": null,
            "interpreterPath": "python",
            "userManagedDependencies": false
        },
        "version": "1"
    }
}
```

Можно также использовать существующую [среду](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) машинное обучение Azure в разделе параметров CLI и удалить ключ "среда" из файла конфигурации вывода. Используйте параметр-e для имени среды и--EV для версии среды. Если не указать--EV, будет использоваться последняя версия. Ниже приведен пример файла конфигурации вывода.

```json
{
    "entryScript": "score.py",
    "sourceDirectory": null
}
```

Следующая команда демонстрирует развертывание модели с помощью предыдущего файла конфигурации вывода (с именем Минференцеконфиг. JSON). 

Он также использует последнюю версию существующей [среды](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) машинное обучение Azure (с именем AzureML-минимум).

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic myInferenceConfig.json -e AzureML-Minimal --dc deploymentconfig.json
```
