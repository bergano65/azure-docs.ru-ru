---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 11/06/2019
ms.author: larryfr
ms.openlocfilehash: 52689c585e148c18d16ad627570414a8de444fea
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926974"
---
Записи в документе `inferenceconfig.json` соответствуют параметрам класса [инференцеконфиг](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) . В следующей таблице описано сопоставление между сущностями в документе JSON и параметрами метода.

| Сущность JSON | Параметр метода | Описание |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Путь к локальному файлу, содержащему код для выполнения для образа. |
| `runtime` | `runtime` | Необязательный элемент. Среда выполнения, используемая для образа. Текущие поддерживаемые среды выполнения: `spark-py` и `python`. Если параметр `environment` установлен, он игнорируется. |
| `condaFile` | `conda_file` | Необязательный элемент. Путь к локальному файлу, содержащему определение окружения Conda для использования в образе.  Если параметр `environment` установлен, он игнорируется. |
| `extraDockerFileSteps` | `extra_docker_file_steps` | Необязательный элемент. Путь к локальному файлу, который содержит дополнительные шаги DOCKER для запуска при настройке образа.  Если параметр `environment` установлен, он игнорируется.|
| `sourceDirectory` | `source_directory` | Необязательный элемент. Путь к папкам, содержащим все файлы для создания образа. |
| `enableGpu` | `enable_gpu` | Необязательный элемент. Включить ли поддержку GPU в образе. Образ GPU должен использоваться в службе Azure, например в экземплярах контейнеров Azure, Машинное обучение Azureных средах выполнения вычислений, виртуальных машинах Azure и службе Azure Kubernetes. Значение по умолчанию равно False. Если параметр `environment` установлен, он игнорируется.|
| `baseImage` | `base_image` | Необязательный элемент. Пользовательский образ, используемый в качестве базового образа. Если базовый образ не указан, то образ будет основан на указанном параметре среды выполнения. Если параметр `environment` установлен, он игнорируется. |
| `baseImageRegistry` | `base_image_registry` | Необязательный элемент. Реестр образов, содержащий базовый образ. Если параметр `environment` установлен, он игнорируется.|
| `cudaVersion` | `cuda_version` | Необязательный элемент. Версия CUDA для установки образов, требующих поддержки GPU. Образ GPU должен использоваться в службе Azure, например в экземплярах контейнеров Azure, Машинное обучение Azureных средах выполнения вычислений, виртуальных машинах Azure и службе Azure Kubernetes. Поддерживаемые версии: 9,0, 9,1 и 10,0. Если задано значение `enable_gpu`, по умолчанию используется значение 9,1. Если параметр `environment` установлен, он игнорируется. |
| `description` | `description` | Описание изображения. Если параметр `environment` установлен, он игнорируется.  |
| `environment` | `environment` | Необязательный элемент.  [Среда](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)машинное обучение Azure.|

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

Следующий JSON — это пример конфигурации определения, которая использует существующую [среду](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) машинное обучение Azure с определенной версией для использования с CLI:

```json
{
    "entryScript": "score.py",
    "environment":{
        "name": "myenv",
        "version": "1"
    },
    "condaFile": "myenv.yml",
    "sourceDirectory": null
}
```

Следующий код JSON — это пример конфигурации определения, которая использует существующую [среду](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) машинное обучение Azure с последней версией для использования с CLI:

```json
{
    "entryScript": "score.py",
    "environment":{
        "name": "myenv",
        "version": null
    },
    "condaFile": "myenv.yml",
    "sourceDirectory": null
}
```
