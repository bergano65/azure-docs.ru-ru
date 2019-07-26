---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 07/19/2019
ms.author: larryfr
ms.openlocfilehash: 03b40b1f334816f08d7e169f8ff78bdb7c06c4de
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348489"
---
Записи в `inferenceconfig.json` документе сопоставляются с параметрами класса [инференцеконфиг](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) . В следующей таблице описано сопоставление между сущностями в документе JSON и параметрами метода.

| Сущность JSON | Параметр метода | Описание |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Путь к локальному файлу, содержащему код для выполнения образа. |
| `runtime` | `runtime` | Среда выполнения, используемая для образа. Текущие поддерживаемые среды выполнения: Spark-корректировка и Python. |
| `condaFile` | `conda_file` | Необязательный элемент. Путь к локальному файлу, содержащему определение среды conda для использования в образе. |
| `extraDockerFileSteps` | `extra_docker_file_steps` | Необязательный элемент. Путь к локальному файлу, содержащему дополнительные шаги DOCKER для запуска при настройке образа. |
| `sourceDirectory` | `source_directory` | Необязательный элемент. Путь к папкам, содержащим все файлы для создания образа. |
| `enableGpu` | `enable_gpu` | Необязательный элемент. Включить ли поддержку GPU в образе. Образ GPU должен использоваться в службе Microsoft Azure. Например, службы "экземпляры контейнеров Azure", "Машинное обучение Azure вычислений", "виртуальные машины Azure" и "служба Kubernetes Azure". Значение по умолчанию — false. |
| `baseImage` | `base_image` | Необязательный. Пользовательский образ, используемый в качестве базового образа. Если базовый образ не задан, то базовый образ будет использоваться на основе заданного параметра среды выполнения. |
| `baseImageRegistry` | `base_image_registry` | Необязательный элемент. Реестр образов, содержащий базовый образ. |
| `cudaVersion` | `cuda_version` | Необязательный элемент. Версия CUDA для установки образов, требующих поддержки GPU. Образ GPU должен использоваться в службах Microsoft Azure. Например, службы "экземпляры контейнеров Azure", "Машинное обучение Azure вычислений", "виртуальные машины Azure" и "служба Kubernetes Azure". Поддерживаемые версии: 9,0, 9,1 и 10,0. Если задано значение "enable_gpu", по умолчанию используется значение "9,1". |
| `description` | `description` |  Описание для этого образа. |

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