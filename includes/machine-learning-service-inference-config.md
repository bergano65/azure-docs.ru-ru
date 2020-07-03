---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/28/2020
ms.author: larryfr
ms.openlocfilehash: 5102e8f75da14c58e948e81aaa418539dd18869a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80159423"
---
Записи в `inferenceconfig.json` документе сопоставляются с параметрами класса [инференцеконфиг](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) . В следующей таблице описано сопоставление между сущностями в документе JSON и параметрами метода.

| Сущность JSON | Параметр метода | Описание |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | Путь к локальному файлу, содержащему код для выполнения для образа. |
| `sourceDirectory` | `source_directory` | Необязательный параметр. Путь к папкам, содержащим все файлы для создания образа, что упрощает доступ к любым файлам в этой папке или вложенной папке. Вы можете отправить всю папку с локального компьютера в качестве зависимостей для веб – службы. Примечание. entry_script, conda_file и пути extra_docker_file_steps являются относительными путями к source_directory пути. |
| `environment` | `environment` | Необязательный параметр.  [Среда](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)машинное обучение Azure.|

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
