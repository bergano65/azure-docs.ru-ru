---
title: Расширение CLI
titleSuffix: Azure Machine Learning
description: Узнайте о расширении интерфейса командной строки для службы Машинного обучения Azure. Azure CLI — это кроссплатформенная программа командной строки, которая позволяет работать с ресурсами в облаке Azure. Расширение машинного обучения позволяет работать с Azure Machine Learning. ML CLI создает и управляет ресурсами, такими как рабочее пространство, хранилища данных, наборы данных, конвейеры, модели и развертывания.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 16f9080487af95e7de5c5f8c91fd5c8d356b7bde
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81618062"
---
# <a name="use-the-cli-extension-for-azure-machine-learning"></a>Используйте расширение CLI для машинного обучения Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

CLI для Машинного обучения Azure является расширением кроссплатформенного интерфейса командной строки для платформы Azure ([Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)). Это расширение предоставляет команды для работы с Машинным обучением Azure. Это позволяет автоматизировать свою деятельность машинного обучения. В следующем списке приведены некоторые примеры действий, которые можно сделать с расширением CLI:

+ выполнять эксперименты для создания моделей машинного обучения;

+ регистрировать модели машинного обучения для использования клиентами;

+ упаковывать, развертывать и отслеживать жизненный цикл моделей машинного обучения.

Интерфейс командной строки не является заменой пакета SDK для Машинного обучения Azure. Это дополнительный инструмент, который оптимизирован для обработки высоко параметризированных задач, которые хорошо подходят для автоматизации.

## <a name="prerequisites"></a>Предварительные требования

* Для использования интерфейса командной строки необходима подписка Azure. Если у вас еще нет подписки Azure, создайте бесплатную учетную запись, прежде чем начинать работу. Опробуйте [бесплатную или платную версию Машинного обучения Azure](https://aka.ms/AMLFree) уже сегодня.

* Для использования команд CLI в этом документе из **локальной среды**необходим [Azure CLI.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

    Если вы используете [облачную оболочку Azure,](https://azure.microsoft.com//features/cloud-shell/)то доступ к CLI осуществляется через браузер и живет в облаке.

## <a name="full-reference-docs"></a>Полные справочные документы

Найдите [полные справочные документы для расширения Azure-cli-ml Azure CLI.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/?view=azure-cli-latest)

## <a name="connect-the-cli-to-your-azure-subscription"></a>Подключение интерфейса командной строки к своей подписке Azure

> [!IMPORTANT]
> Если вы используете оболочку облака Azure, вы можете пропустить этот раздел. Облачная оболочка автоматически проверяет подлинность с помощью учетной записи, вскакиваемых в подписку Azure.

Существует несколько способов проверки подлинности подписки Azure с CLI. Наиболее основным является интерактивная аутентификативная проверка с помощью браузера. Чтобы проверить подлинность в интерактивном режиме, откройте командную строку или терминал и используйте следующую команду:

```azurecli-interactive
az login
```

Если CLI сможет запустить браузер по умолчанию, он откроет в браузере страницу входа. В противном случае вам нужно открыть браузер и следовать инструкциям по командной строке. Инструкции включают просмотр [https://aka.ms/devicelogin](https://aka.ms/devicelogin) и ввод кода авторизации.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

Для других методов проверки подлинности, [перепишитесь с Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

## <a name="install-the-extension"></a>Установка расширения

Чтобы установить расширение интерфейса командной строки Машинного обучения, выполните следующую команду:

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> Пример файлов, которые можно использовать с командами ниже, можно найти [здесь.](https://aka.ms/azml-deploy-cloud)

При появлении запроса выберите `y`, чтобы установить расширение.

Чтобы убедиться, что расширение установлено, используйте следующую команду для отображения списка подкоманд Машинного обучения:

```azurecli-interactive
az ml -h
```

## <a name="update-the-extension"></a>Обновление расширения

Для обновления расширения Машинного обучения CLI используйте следующую команду:

```azurecli-interactive
az extension update -n azure-cli-ml
```


## <a name="remove-the-extension"></a>Удаление расширения

Чтобы удалить расширение интерфейса командной строки, используйте следующую команду:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Управление ресурсами

Следующие команды демонстрируют использование интерфейса командной строки для управления ресурсами, используемыми службой "Машинное обучение Azure".

+ Если у вас его еще нет, создайте группу ресурсов:

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Создание рабочего пространства для машинНого обучения Azure:

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    > [!TIP]
    > Эта команда создает базовое рабочее пространство издания. Для создания рабочего пространства `--sku enterprise` предприятия используйте коммутатор с командой. `az ml workspace create` Для получения дополнительной информации о изданиях машинного обучения Azure [смотрите, что такое машинное обучение Azure.](overview-what-is-azure-ml.md#sku)

    Для получения дополнительной информации, см [Az мл рабочее пространство создать](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-create).

+ Прикрепите конфигурацию рабочего пространства к папке, чтобы обеспечить контекстную осведомленность CLI.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Эта команда `.azureml` создает субдиректорию, которая содержит примеры файлов среды runconfig и conda. Он также `config.json` содержит файл, который используется для связи с вашим рабочим пространством машинного обучения Azure.

    Для получения дополнительной информации, см [Az мл папку прикрепить](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).

+ Прикрепите контейнер Azure blob в качестве хранилища данных.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    Для получения дополнительной информации, см [Az ml Datastore прикрепить-blob](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-attach-blob).

+ Загрузка файлов в хранилище данных.

    ```azurecli-interactive
    az ml datastore upload  -n datastorename -p sourcepath
    ```

    Для получения дополнительной информации, см [Az ml Datastore загрузить](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-upload).

+ Прикрепите кластер AKS в качестве вычислительной цели.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    Для получения дополнительной [az ml computetarget attach aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks) информации см.

+ Создайте новую цель AMLcompute.

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```

    Для получения дополнительной [az ml computetarget create amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute)информации см.

## <a name="run-experiments"></a><a id="experiments"></a>Запуск экспериментов

* Запустите выполнение эксперимента. При использовании этой команды укажите имя файла runconfig (текст до \*.runconfig, если вы смотрите на файловую систему) по параметру -c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > Команда `az ml folder attach` создает `.azureml` субдиректор, который содержит два примера файлов runconfig. 
    >
    > Если у вас есть скрипт Python, который создает объект конфигурации запуска программно, вы можете использовать [RunConfig.save()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) для сохранения его в качестве файла runconfig.
    >
    > Полную схему runconfig можно найти в этом [файле JSON.](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json) Схема самодокументируется через `description` ключ каждого объекта. Кроме того, есть перечисления возможных значений и фрагмент шаблона в конце.

    Для получения дополнительной [az ml run submit-script](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script)информации см.

* Посмотреть список экспериментов:

    ```azurecli-interactive
    az ml experiment list
    ```

    Для получения дополнительной информации, см [Az мл список экспериментов](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

## <a name="dataset-management"></a>Управление набором данных

Следующие команды демонстрируют, как работать с наборами данных в Azure Machine Learning:

+ Зарегистрируйте набор данных:

    ```azurecli-interactive
    az ml dataset register -f mydataset.json
    ```

    Для получения информации о формате файла JSON, `az ml dataset register --show-template`используемого для определения набора данных, используйте .

    Для получения дополнительной [az ml dataset register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)информации см.

+ Архивируйте активный или универсированный набор данных:

    ```azurecli-interactive
    az ml dataset archive -n dataset-name
    ```

    Для получения дополнительной [az ml dataset archive](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)информации см.

+ Осквернить набор данных:

    ```azurecli-interactive
    az ml dataset deprecate -d replacement-dataset-id -n dataset-to-deprecate
    ```

    Для получения дополнительной информации, см [Az ml набор данных deprecate](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Перечислите все наборы данных в рабочей области:

    ```azurecli-interactive
    az ml dataset list
    ```

    Для получения дополнительной [az ml dataset list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)информации см.

+ Получите подробную информацию о наборе данных:

    ```azurecli-interactive
    az ml dataset show -n dataset-name
    ```

    Для получения дополнительной информации, см [Az ml набор данных показывают](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive).

+ Повторно активируйте архивированный или изунебренный набор данных:

    ```azurecli-interactive
    az ml dataset reactivate -n dataset-name
    ```

    Для получения дополнительной [az ml dataset reactivate](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)информации см.

+ Не регистрируйте набор данных:

    ```azurecli-interactive
    az ml dataset unregister -n dataset-name
    ```

    Для получения дополнительной [az ml dataset unregister](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)информации см.

## <a name="environment-management"></a>Управление окружающей средой

Следующие команды демонстрируют, как создавать, регистрировать и перечислять [среды](how-to-configure-environment.md) машинного обучения Azure для рабочего пространства:

+ Создание файлов строительных лесов для среды:

    ```azurecli-interactive
    az ml environment scaffold -n myenv -d myenvdirectory
    ```

    Для получения дополнительной информации, см [Az мл окружающей среды эшафот](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-scaffold).

+ Регистрация среды:

    ```azurecli-interactive
    az ml environment register -d myenvdirectory
    ```

    Для получения дополнительной [az ml environment register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-register)информации см.

+ Список зарегистрированных сред:

    ```azurecli-interactive
    az ml environment list
    ```

    Для получения дополнительной [az ml environment list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-list)информации см.

+ Скачать зарегистрированную среду:

    ```azurecli-interactive
    az ml environment download -n myenv -d downloaddirectory
    ```

    Для получения дополнительной информации [см.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-download)

### <a name="environment-configuration-schema"></a>Схема конфигурации среды

Если вы `az ml environment scaffold` использовали команду, она `azureml_environment.json` генерирует файл шаблона, который может быть изменен и использован для создания пользовательских конфигураций среды с CLI. Объект верхнего уровня свободно [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) отображает класс в Python SDK. 

```json
{
    "name": "testenv",
    "version": null,
    "environmentVariables": {
        "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
    },
    "python": {
        "userManagedDependencies": false,
        "interpreterPath": "python",
        "condaDependenciesFile": null,
        "baseCondaEnvironment": null
    },
    "docker": {
        "enabled": false,
        "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
        "baseDockerfile": null,
        "sharedVolumes": true,
        "shmSize": "2g",
        "arguments": [],
        "baseImageRegistry": {
            "address": null,
            "username": null,
            "password": null
        }
    },
    "spark": {
        "repositories": [],
        "packages": [],
        "precachePackages": true
    },
    "databricks": {
        "mavenLibraries": [],
        "pypiLibraries": [],
        "rcranLibraries": [],
        "jarLibraries": [],
        "eggLibraries": []
    },
    "inferencingStackVersion": null
}
```

В следующей таблице подробно описано каждое поле верхнего уровня в файле JSON, его тип и описание. Если тип объекта связан с классом из Python SDK, между каждым полем JSON и общедоступным переменным именем в классе Python существует сыпучий матч 1:1. В некоторых случаях поле может отображаться на аргументе конструктора, а не на переменную класса. Например, `environmentVariables` поле карт `environment_variables` для переменной в [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) классе.

| Поле JSON | Тип | Описание |
|---|---|---|
| `name` | `string` | Имя среды. Не начинайте имя с **Microsoft** или **AzureML**. |
| `version` | `string` | Версия окружающей среды. |
| `environmentVariables` | `{string: string}` | Хэш-карта переменных имен и значений среды. |
| `python` | [`PythonSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py) | Объект, определяющий среду Python и интерпретатор для использования в ресурсе целевых вычислений. |
| `docker` | [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) | Определяет настройки для настройки изображения Docker, построенного в соответствии со спецификациями среды. |
| `spark` | [`SparkSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.sparksection?view=azure-ml-py) | Раздел настраивает настройки Spark. Он используется только тогда, когда фреймворк установлен в PySpark. |
| `databricks` | [`DatabricksSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.databricks.databrickssection?view=azure-ml-py) | Настраивает библиотечные зависимости Databricks. |
| `inferencingStackVersion` | `string` | Определяет версию стека выводов, добавленную к изображению. Чтобы избежать добавления стека выводов, `null`покинуйте это поле. Действительное значение: "последнее". |

## <a name="ml-pipeline-management"></a>Управление конвейером ML

Следующие команды демонстрируют, как работать с конвейерами машинного обучения:

+ Создание конвейера машинного обучения:

    ```azurecli-interactive
    az ml pipeline create -n mypipeline -y mypipeline.yml
    ```

    Для получения дополнительной [az ml pipeline create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create)информации см.

    Для получения дополнительной информации о файле конвейера YAML [см.](reference-pipeline-yaml.md)

+ Запуск конвейера:

    ```azurecli-interactive
    az ml run submit-pipeline -n myexperiment -y mypipeline.yml
    ```

    Для получения дополнительной информации, см [Az ml запустить представить-конвейер](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-pipeline).

    Для получения дополнительной информации о файле конвейера YAML [см.](reference-pipeline-yaml.md)

+ Расписание конвейера:

    ```azurecli-interactive
    az ml pipeline create-schedule -n myschedule -e myexpereiment -i mypipelineid -y myschedule.yml
    ```

    Для получения дополнительной [az ml pipeline create-schedule](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create-schedule)информации см.

    Для получения дополнительной информации о расписании работы файла YAML [см.](reference-pipeline-yaml.md#schedules)

## <a name="model-registration-profiling-deployment"></a>Регистрация моделей, профилирование, развертывание

Следующие команды демонстрируют регистрацию обученной модели, а затем ее развертывание в качестве рабочей службы:

+ Зарегистрируйте модель с помощью Машинного обучения Azure.

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    Для получения дополнительной [az ml model register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register)информации см.

+ **ВНЕВЕХ** Профиль модели, чтобы получить оптимальные значения процессора и памяти для развертывания.
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    Для получения дополнительной [az ml model profile](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile)информации см.

+ Развертывание модели в AKS
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    Для получения дополнительной информации о схеме файла конфигурации выводов [см.](#inferenceconfig)
    
    Для получения дополнительной информации о схеме [Deployment configuration schema](#deploymentconfig)конфигурации развертывания см.

    Для получения дополнительной [az ml model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)информации см.

<a id="inferenceconfig"></a>

## <a name="inference-configuration-schema"></a>Схема конфигурации выводов

[!INCLUDE [inferenceconfig](../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## <a name="deployment-configuration-schema"></a>Схема конфигурации развертывания

### <a name="local-deployment-configuration-schema"></a>Схема конфигурации локального развертывания

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-local-deploy-config.md)]

### <a name="azure-container-instance-deployment-configuration-schema"></a>Схема настройки развертывания тарносных экземпляров Azure Container 

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

### <a name="azure-kubernetes-service-deployment-configuration-schema"></a>Схема конфигурации развертывания службы Azure Kubernetes

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

## <a name="next-steps"></a>Дальнейшие действия

* [Ссылка команды для расширения машинного обучения CLI.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest)

* [Обучение и развертывание моделей машинного обучения с помощью Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning)
