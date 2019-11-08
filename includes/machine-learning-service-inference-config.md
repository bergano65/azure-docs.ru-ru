---
author: larryfr
ms.service: machine-learning
ms.topic: include
ms.date: 11/06/2019
ms.author: larryfr
ms.openlocfilehash: ab31d45808a8c77c53b895643eec63952201d9e4
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73800078"
---
Записи в документе `inferenceconfig.json` соответствуют параметрам класса [инференцеконфиг](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) . В следующей таблице описано сопоставление между сущностями в документе JSON и параметрами метода.

| Сущность JSON | Параметр метода | Description (Описание) |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Путь к локальному файлу, содержащему код для выполнения для образа. |
| `runtime` | `runtime` | Среда выполнения, используемая для образа. Текущие поддерживаемые среды выполнения: `spark-py` и `python`. |
| `condaFile` | `conda_file` | необязательный параметр. Путь к локальному файлу, содержащему определение окружения Conda для использования в образе. |
| `extraDockerFileSteps` | `extra_docker_file_steps` | необязательный параметр. Путь к локальному файлу, который содержит дополнительные шаги DOCKER для запуска при настройке образа. |
| `sourceDirectory` | `source_directory` | необязательный параметр. Путь к папкам, содержащим все файлы для создания образа. |
| `enableGpu` | `enable_gpu` | необязательный параметр. Включить ли поддержку GPU в образе. Образ GPU должен использоваться в службе Azure, например в экземплярах контейнеров Azure, Машинное обучение Azureных средах выполнения вычислений, виртуальных машинах Azure и службе Azure Kubernetes. Значение по умолчанию — false. |
| `baseImage` | `base_image` | необязательный параметр. Пользовательский образ, используемый в качестве базового образа. Если базовый образ не указан, то образ будет основан на указанном параметре среды выполнения. |
| `baseImageRegistry` | `base_image_registry` | необязательный параметр. Реестр образов, содержащий базовый образ. |
| `cudaVersion` | `cuda_version` | необязательный параметр. Версия CUDA для установки образов, требующих поддержки GPU. Образ GPU должен использоваться в службе Azure, например в экземплярах контейнеров Azure, Машинное обучение Azureных средах выполнения вычислений, виртуальных машинах Azure и службе Azure Kubernetes. Поддерживаемые версии: 9,0, 9,1 и 10,0. Если задано значение `enable_gpu`, по умолчанию используется значение 9,1. |
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