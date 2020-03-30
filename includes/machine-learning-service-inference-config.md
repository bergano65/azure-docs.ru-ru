---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/28/2020
ms.author: larryfr
ms.openlocfilehash: 5102e8f75da14c58e948e81aaa418539dd18869a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159423"
---
Записи в `inferenceconfig.json` карте документа по параметрам для класса [InferenceConfig.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) В следующей таблице описывается отображение между сущностями в документе JSON и параметры метода:

| Лицо JSON | Параметр метода | Описание |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Путь к локальному файлу, содержащему код для запуска изображения. |
| `sourceDirectory` | `source_directory` | Необязательный параметр. Путь к папкам, которые содержат все файлы для создания изображения, что делает его легким для доступа к любым файлам в этой папке или subfolder. Вы можете загрузить всю папку из локальной машины в качестве зависимостей для Webservice. Примечание: ваши entry_script, conda_file и extra_docker_file_steps пути являются относительными путями к source_directory пути. |
| `environment` | `environment` | Необязательный параметр.  [Среда](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)машинного обучения Azure .|

Можно включить полные спецификации [среды](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) машинного обучения Azure в файл конфигурации выводов. Если эта среда не существует в рабочей области, azure Machine Learning создаст ее. В противном случае, Azure Machine Learning при необходимости обновит среду. Следующий JSON является примером:

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

Можно также использовать существующую [среду](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) машинного обучения Azure в разделенных параметрах CLI и удалить ключ "окружающей среды" из файла конфигурации выводов. Используйте -e для названия среды и --ev для версии среды. Если вы не укажете --ev, последняя версия будет использоваться. Вот пример файла конфигурации выводов:

```json
{
    "entryScript": "score.py",
    "sourceDirectory": null
}
```

Следующая команда демонстрирует, как развернуть модель с помощью предыдущего файла конфигурации выводов (названный myInferenceConfig.json). 

Он также использует последнюю версию существующей [среды](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) машинного обучения Azure (названной AzureML-Minimal).

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic myInferenceConfig.json -e AzureML-Minimal --dc deploymentconfig.json
```
