---
title: Развертывание моделей с пользовательским изображением Docker
titleSuffix: Azure Machine Learning
description: Узнайте, как использовать пользовательское базовое изображение Docker при развертывании моделей машинного обучения Azure. В то время как Azure Machine Learning предоставляет базовое изображение по умолчанию для вас, вы также можете использовать свое собственное базовое изображение.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 03/16/2020
ms.openlocfilehash: 1f11d6667c22990b3cba2079959bec6f413d5951
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80296932"
---
# <a name="deploy-a-model-using-a-custom-docker-base-image"></a>Развертывание модели с помощью пользовательского базового изображения Docker
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Узнайте, как использовать пользовательское базовое изображение Docker при развертывании обученных моделей с помощью Azure Machine Learning.

При развертывании обученной модели на веб-службу или устройство IoT Edge создается пакет, содержащий веб-сервер для обработки входящих запросов.

Azure Machine Learning предоставляет базовое изображение Докера по умолчанию, так что вам не придется беспокоиться о его создании. Вы также можете использовать __среды__ машинного обучения Azure для выбора определенного базового изображения или пользовательский, который вы предоставляете.

Базовое изображение используется в качестве отправной точки при создании изображения для развертывания. Он обеспечивает базовую операционную систему и компоненты. Затем процесс развертывания добавляет дополнительные компоненты, такие как модель, среда conda и другие ресурсы, к изображению перед развертыванием.

Как правило, вы создаете пользовательское базовое изображение, когда требуется использовать Docker для управления зависимостями, более жесткого контроля над версиями компонентов или экономии времени во время развертывания. Например, можно стандартизировать определенную версию Python, Conda или другого компонента. Возможно, также необходимо установить программное обеспечение, требуемое вашей моделью, где процесс установки занимает много времени. Установка программного обеспечения при создании базового изображения означает, что вам не нужно устанавливать его для каждого развертывания.

> [!IMPORTANT]
> При развертывании модели нельзя переопределять основные компоненты, такие как веб-сервер или компоненты IoT Edge. Эти компоненты обеспечивают известную рабочую среду, которая тестируется и поддерживается корпорацией Майкрософт.

> [!WARNING]
> Корпорация Майкрософт может оказаться не в состоянии помочь устранению неполадок, вызванных пользовательским изображением. При возникновении проблем может быть предложено использовать изображение по умолчанию или одно из изображений, которые предоставляет корпорация Майкрософт, чтобы узнать, является ли проблема специфична для вашего изображения.

Этот документ разбит на два раздела:

* Создайте пользовательское базовое изображение: предоставляет информацию для админинов и DevOps о создании пользовательского изображения и настройке аутентификации в реестр контейнеров Azure с помощью Azure CLI и машинного обучения CLI.
* Развертывание модели с помощью пользовательского базового изображения: предоставляет информацию data Scientists и DevOps / ML Engineers об использовании пользовательских изображений при развертывании обученной модели от Python SDK или ML CLI.

## <a name="prerequisites"></a>Предварительные требования

* Рабочая группа по машинного обучению Azure. Для получения дополнительной информации смотрите [статью «Создание рабочей области».](how-to-manage-workspace.md)
* [Azure машинного обучения SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py). 
* [Лазурный CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* [Расширение CLI для машинного обучения Azure.](reference-azure-machine-learning-cli.md)
* [Реестр контейнеров Azure](/azure/container-registry) или другой реестр Docker, доступный в Интернете.
* Шаги в этом документе предполагают, что вы знакомы с созданием и использованием объекта __конфигурации выводов__ в рамках развертывания модели. Для получения дополнительной информации см. [Where to deploy and how](how-to-deploy-and-where.md#prepare-to-deploy)

## <a name="create-a-custom-base-image"></a>Создание пользовательского базового изображения

Информация в этом разделе предполагает, что для хранения изображений Докера используется реестр контейнеров Azure. Используйте следующий контрольный список при планировании создания пользовательских изображений для машинного обучения Azure:

* Будете ли вы использовать реестр контейнеров Azure, созданный для рабочего пространства машинного обучения Azure, или автономный реестр контейнеров Azure?

    При использовании изображений, хранящихся в __реестре контейнеров для рабочего пространства,__ вам не нужно authenticate в реестр. Аутентификация обрабатывается рабочим пространством.

    > [!WARNING]
    > Реестр контейнеров Azure для рабочего пространства __создается при первом обучении или развертывании модели__ с использованием рабочего пространства. Если вы создали новое рабочее пространство, но не обучили или не создали модель, для рабочего пространства не будет существовать реестр контейнеров Azure.

    Для получения информации о получении имени реестра контейнеров Azure для рабочего пространства можно ознакомиться в разделе [названия реестра контейнеров](#getname) Get registry в этой статье.

    При использовании изображений, хранящихся в __отдельном реестре контейнеров,__ необходимо настроить директор службы, который имеет по крайней мере доступ к чтению. Затем вы предоставляете основной идентификатор службы (имя пользователя) и пароль любому, кто использует изображения из реестра. Исключением является, если вы сделаете реестр контейнеров общедоступным.

    Для получения информации о создании частного реестра контейнеров Azure [см.](/azure/container-registry/container-registry-get-started-azure-cli)

    Для получения информации об использовании основ [Azure Container Registry authentication with service principals](/azure/container-registry/container-registry-auth-service-principal)услуг в реестре контейнеров Azure см.

* Реестр контейнеров Azure и информация об изображении: Предоставьте имя изображения любому, кто должен его использовать. Например, изображение `myimage`с именем, `myregistry`хранящимся `myregistry.azurecr.io/myimage` в реестре с именем, отсылается как при использовании изображения для развертывания модели

* Требования к изображениям: Azure Machine Learning поддерживает только изображения Docker, которые предоставляют следующее программное обеспечение:

    * Ubuntu 16.04 или больше.
    * Конда 4.5.» или больше.
    * Python 3.5.» или 3.6.

<a id="getname"></a>

### <a name="get-container-registry-information"></a>Получить информацию о реестре контейнеров

В этом разделе узнайте, как получить название реестра контейнеров Azure для рабочего пространства Azure Machine Learning.

> [!WARNING]
> Реестр контейнеров Azure для рабочего пространства __создается при первом обучении или развертывании модели__ с использованием рабочего пространства. Если вы создали новое рабочее пространство, но не обучили или не создали модель, для рабочего пространства не будет существовать реестр контейнеров Azure.

Если вы уже обучили или развернули модели с помощью Azure Machine Learning, для рабочего пространства был создан реестр контейнеров. Чтобы найти название этого реестра контейнеров, используйте следующие шаги:

1. Откройте новую оболочку или командно-запрос и используйте следующую команду для проверки подлинности подписки Azure:

    ```azurecli-interactive
    az login
    ```

    Следите за запросами, чтобы проверить подлинность подписки.

    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

2. Используйте следующую команду, чтобы перечислить реестр контейнеров для рабочего пространства. Замените `<myworkspace>` имя рабочего пространства Azure Machine Learning. Заменить `<resourcegroup>` группу ресурсов Azure, содержащую рабочее пространство:

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

    Эта команда возвращает следующую информацию:

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    Значение `<registry_name>` — это название реестра контейнеров Azure для рабочего пространства.

### <a name="build-a-custom-base-image"></a>Создание пользовательского базового изображения

Шаги в этом разделе пошаговые создания пользовательского изображения Docker в вашем реестре контейнеров Azure.

1. Создайте новый `Dockerfile`текстовый файл с именем используйте следующий текст в качестве содержимого:

    ```text
    FROM ubuntu:16.04

    ARG CONDA_VERSION=4.5.12
    ARG PYTHON_VERSION=3.6

    ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
    ENV PATH /opt/miniconda/bin:$PATH

    RUN apt-get update --fix-missing && \
        apt-get install -y wget bzip2 && \
        apt-get clean && \
        rm -rf /var/lib/apt/lists/*

    RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-x86_64.sh -O ~/miniconda.sh && \
        /bin/bash ~/miniconda.sh -b -p /opt/miniconda && \
        rm ~/miniconda.sh && \
        /opt/miniconda/bin/conda clean -tipsy

    RUN conda install -y conda=${CONDA_VERSION} python=${PYTHON_VERSION} && \
        conda clean -aqy && \
        rm -rf /opt/miniconda/pkgs && \
        find / -type d -name __pycache__ -prune -exec rm -rf {} \;
    ```

2. От оболочки или командно-запроса используйте следующее для проверки подлинности в реестре контейнеров Azure. Замените `<registry_name>` имя контейнерного реестра, в которое вы хотите сохранить изображение в:

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. Чтобы загрузить Dockerfile и построить его, используйте следующую команду. Замените `<registry_name>` название контейнерного реестра, в которое вы хотите сохранить изображение в:

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    > [!TIP]
    > В этом примере `:v1` к изображению применяется тег. Если тег не предусмотрен, `:latest` применяется тег.

    В процессе сборки информация передается обратно к командной строке. Если сборка успешно завершена, вы получаете сообщение, похожее на следующий текст:

    ```text
    Run ID: cda was successful after 2m56s
    ```

Для получения дополнительной информации о создании изображений с реестром контейнеров Azure [см.](https://docs.microsoft.com/azure/container-registry/container-registry-quickstart-task-cli)

Для получения дополнительной информации о загрузке существующих [Push your first image to a private Docker container registry](/azure/container-registry/container-registry-get-started-docker-cli)изображений в реестр контейнеров Azure см.

## <a name="use-a-custom-base-image"></a>Используйте пользовательское базовое изображение

Для использования пользовательского изображения требуется следующая информация:

* __Имя изображения__. Например, `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` это путь к базовому изображению Docker, предоставленному корпорацией Майкрософт.

    > [!IMPORTANT]
    > Для пользовательских изображений, которые вы создали, не забудьте включить любые теги, которые были использованы с изображением. Например, если изображение было создано с `:v1`помощью определенного тега, например. Если при создании изображения не использовался определенный `:latest` тег, был применен тег.

* Если изображение находится в __частном репозитории,__ вам нужна следующая информация:

    * __Адрес__реестра . Например, `myregistry.azureecr.io`.
    * Основное __имя пользователя__ службы и __пароль,__ считываедоступ к реестру.

    Если у вас нет этой информации, обратитесь к администратору реестра контейнеров Azure, который содержит ваше изображение.

### <a name="publicly-available-base-images"></a>Общедоступные базовые изображения

Корпорация Майкрософт предоставляет несколько докерных изображений в общедоступном репозитории, которые могут быть использованы с шагами в этом разделе:

| Изображение | Описание |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Базовое изображение для машинного обучения Azure |
| `mcr.microsoft.com/azureml/onnxruntime:latest` | Содержит ONNX Runtime для выводов процессора |
| `mcr.microsoft.com/azureml/onnxruntime:latest-cuda` | Содержит ONNX Runtime и CUDA для графического процессора |
| `mcr.microsoft.com/azureml/onnxruntime:latest-tensorrt` | Содержит ONNX Runtime и TensorRT для графического процессора |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-vadm ` | Содержит ONNX Runtime и OpenVINO для Intel<sup> </sup> Vision Ускоритель Дизайн на основе Movidius<sup>TM</sup> MyriadX VPUs |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-myriad` | Содержит ONNX Runtime и OpenVINO для Intel<sup> </sup> Movidius<sup>TM</sup> USB палочки |

Для получения дополнительной информации о базовых изображениях ONNX Runtime см. [раздел докерфайлon ONNX Runtime](https://github.com/microsoft/onnxruntime/blob/master/dockerfiles/README.md) в репо GitHub.

> [!TIP]
> Поскольку эти изображения находятся в открытом доступе, при их использовании не нужно предоставлять адрес, имя пользователя или пароль.

Для получения дополнительной [информации](https://github.com/Azure/AzureML-Containers)см.

> [!TIP]
>__Если ваша модель обучается на Azure Machine Learning Compute,__ используя __версию 1.0.22 или более__ Azure Machine Learning SDK, изображение создается во время обучения. Чтобы узнать название этого `run.properties["AzureML.DerivedImageName"]`изображения, используйте . Следующий пример показывает, как использовать это изображение:
>
> ```python
> # Use an image built during training with SDK 1.0.22 or greater
> image_config.base_image = run.properties["AzureML.DerivedImageName"]
> ```

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Используйте изображение с помощью SDK по изучению машин Azure

Чтобы использовать изображение, хранящееся в **реестре контейнеров Azure для рабочего пространства,** или **реестр контейнеров, который находится в открытом доступе,** установите следующие атрибуты [среды:](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)

+ `docker.enabled=True`
+ `docker.base_image`: Установить в реестр и путь к изображению.

```python
from azureml.core.environment import Environment
# Create the environment
myenv = Environment(name="myenv")
# Enable Docker and reference an image
myenv.docker.enabled = True
myenv.docker.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"
```

Чтобы использовать изображение из __частного реестра контейнеров,__ которое `docker.base_image_registry` не находится в рабочей области, необходимо указать адрес репозитория и имя пользователя и пароль:

```python
# Set the container registry information
myenv.docker.base_image_registry.address = "myregistry.azurecr.io"
myenv.docker.base_image_registry.username = "username"
myenv.docker.base_image_registry.password = "password"

myenv.inferencing_stack_version = "latest"  # This will install the inference specific apt packages.

# Define the packages needed by the model and scripts
from azureml.core.conda_dependencies import CondaDependencies
conda_dep = CondaDependencies()
# you must list azureml-defaults as a pip dependency
conda_dep.add_pip_package("azureml-defaults")
myenv.python.conda_dependencies=conda_dep
```

В качестве зависимости от пипсов необходимо добавить лазурные по умолчанию с версией >1,0,45 евро. Этот пакет содержит функции, необходимые для размещения модели в качестве веб-службы. Вы также должны установить inferencing_stack_version свойство на окружающую среду, чтобы "последний", это будет установить конкретные пакеты apt, необходимые веб-службы. 

После определения среды используйте ее с помощью объекта [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) для определения среды выводов, в которой будет работать модель и веб-служба.

```python
from azureml.core.model import InferenceConfig
# Use environment in InferenceConfig
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

На этом этапе можно продолжить развертывание. Например, следующий фрагмент кода будет развертывать веб-службу локально, используя конфигурацию выводов и пользовательское изображение:

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Для получения дополнительной информации о развертывании см. [Модели развертываний с помощью машинного обучения Azure.](how-to-deploy-and-where.md)

Для получения дополнительной информации о настройке среды Python [см.](how-to-use-environments.md) 

### <a name="use-an-image-with-the-machine-learning-cli"></a>Используйте изображение с помощью CLI машинного обучения

> [!IMPORTANT]
> В настоящее время CLI машинного обучения может использовать изображения из реестра контейнеров Azure для рабочего пространства или общедоступных репозиториев. Он не может использовать изображения из автономных частных реестров.

Перед развертыванием модели с помощью машинного обучения CLI создайте [среду,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) которая использует пользовательское изображение. Затем создайте файл конфигурации выводов, который ссылается на среду. Можно также определить среду непосредственно в файле конфигурации выводов. В следующем документе JSON показано, как ссылаться на изображение в общедоступном реестре контейнеров. В этом примере среда определяется в строке:

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda",
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

Этот файл используется `az ml model deploy` с командой. Параметр `--ic` используется для определения файла конфигурации выводов.

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

Для получения дополнительной информации о развертывании модели с использованием ML CLI см. [CLI extension for Azure Machine Learning](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment)

## <a name="next-steps"></a>Дальнейшие действия

* Подробнее о [том, где развертывать и как развернуть](how-to-deploy-and-where.md).
* Узнайте, как [обучить и развернуть модели машинного обучения с помощью Azure Pipelines.](/azure/devops/pipelines/targets/azure-machine-learning?view=azure-devops)
