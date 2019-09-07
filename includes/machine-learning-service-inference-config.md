---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/19/2019
ms.author: larryfr
ms.openlocfilehash: 055909d51fcd1228e8eb26189ba682e09aee6a1a
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390689"
---
Записи в `inferenceconfig.json` документе сопоставляются с параметрами класса [инференцеконфиг](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) . В следующей таблице описано сопоставление между сущностями в документе JSON и параметрами метода.

| Сущность JSON | Параметр метода | Описание |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Путь к локальному файлу, содержащему код для выполнения для образа. |
| `runtime` | `runtime` | Среда выполнения, используемая для образа. Текущие поддерживаемые среды выполнения: `spark-py` и `python`. |
| `condaFile` | `conda_file` | Необязательный элемент. Путь к локальному файлу, содержащему определение окружения Conda для использования в образе. |
| `extraDockerFileSteps` | `extra_docker_file_steps` | Необязательный элемент. Путь к локальному файлу, который содержит дополнительные шаги DOCKER для запуска при настройке образа. |
| `sourceDirectory` | `source_directory` | Необязательный элемент. Путь к папкам, содержащим все файлы для создания образа. |
| `enableGpu` | `enable_gpu` | Необязательный элемент. Включить ли поддержку GPU в образе. Образ GPU должен использоваться в службе Azure, например в экземплярах контейнеров Azure, Машинное обучение Azureных средах выполнения вычислений, виртуальных машинах Azure и службе Azure Kubernetes. Значение по умолчанию — false. |
| `baseImage` | `base_image` | Необязательный элемент. Пользовательский образ, используемый в качестве базового образа. Если базовый образ не указан, то образ будет основан на указанном параметре среды выполнения. |
| `baseImageRegistry` | `base_image_registry` | Необязательный элемент. Реестр образов, содержащий базовый образ. |
| `cudaVersion` | `cuda_version` | Необязательный элемент. Версия CUDA для установки образов, требующих поддержки GPU. Образ GPU должен использоваться в службе Azure, например в экземплярах контейнеров Azure, Машинное обучение Azureных средах выполнения вычислений, виртуальных машинах Azure и службе Azure Kubernetes. Поддерживаемые версии: 9,0, 9,1 и 10,0. Если `enable_gpu` задано значение, по умолчанию используется значение 9,1. |
| `description` | `description` | Описание изображения. |

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