---
title: Развертывание моделей ml в приложениях функций Azure (предварительный просмотр)
titleSuffix: Azure Machine Learning
description: Узнайте, как использовать машинное обучение Azure для развертывания модели в приложении Azure Functions.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: vaidyas
ms.reviewer: larryfr
ms.date: 03/06/2020
ms.openlocfilehash: d03a3d482d147d3bc69354ee09dfe0b187610a09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927436"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-preview"></a>Развертывание модели машинного обучения в Функции Azure (предварительный просмотр)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Узнайте, как развернуть модель из машинного обучения Azure в качестве функционального приложения в Функциях Azure.

> [!IMPORTANT]
> Хотя функции Машинного обучения Azure и Azure, как правило, доступны, возможность упаковки модели из службы машинного обучения для функций находится в предварительном просмотре.

С помощью машинного обучения Azure можно создавать изображения Docker из обученных моделей машинного обучения. Теперь Azure Machine Learning имеет функцию предварительного просмотра для создания этих моделей машинного обучения в функциональных приложениях, которые могут быть [развернуты в Azure Functions.](https://docs.microsoft.com/azure/azure-functions/functions-deployment-technologies#docker-container)

## <a name="prerequisites"></a>Предварительные требования

* Рабочая область машинного обучения Azure. Для получения дополнительной информации смотрите [статью «Создание рабочей области».](how-to-manage-workspace.md)
* [Лазурный CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* Обученная модель машинного обучения, зарегистрированная в вашем рабочем пространстве. Если у вас нет модели, используйте [учебник классификации изображений: модель поезда](tutorial-train-models-with-aml.md) для обучения и регистрации.

    > [!IMPORTANT]
    > Фрагменты кода в этой статье предполагают, что вы установили следующие переменные:
    >
    > * `ws`- Рабочее пространство машинного обучения Azure.
    > * `model`- Зарегистрированная модель, которая будет развернута.
    > * `inference_config`- Конфигурация выводов для модели.
    >
    > Для получения дополнительной информации об установке этих переменных см. [Модели развертываний с помощью машинного обучения Azure.](how-to-deploy-and-where.md)

## <a name="prepare-for-deployment"></a>Подготовка к развертыванию

Перед развертыванием необходимо определить, что необходимо для запуска модели в виде веб-службы. В следующем списке описаны основные элементы, необходимые для развертывания:

* Сценарий __входа__. Этот скрипт принимает запросы, оценивает запрос с помощью модели и возвращает результаты.

    > [!IMPORTANT]
    > Сценарий входа специфичен для вашей модели; он должен понимать формат входящих данных запроса, формат данных, ожидаемых вашей моделью, и формат данных, возвращенных клиентам.
    >
    > Если данные запроса в формате, который не используется моделью, скрипт может превратить его в приемлемый формат. Он также может преобразовать ответ, прежде чем вернуться к нему клиенту.
    >
    > По умолчанию при упаковке для функций ввод рассматривается как текст. Если вы заинтересованы в потреблении необработанных байтов ввода (например, для триггеров Blob), вы должны использовать [AMLRequest для приема необработанных данных.](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where#binary-data)


* **Зависимости,** такие как скрипты-помощники или пакеты Python/Conda, необходимые для запуска сценария или модели входа

Эти сущности инкапсулированы в __конфигурацию выводов.__ В конфигурации зависимостей указываются скрипт входа и другие зависимости.

> [!IMPORTANT]
> При создании конфигурации выводов для использования с функциями Azure необходимо использовать объект [среды.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) Обратите внимание, что если вы определяете пользовательскую среду, необходимо добавить azureml-по умолчанию с версией >1,0,45 в качестве зависимости от пипсов. Этот пакет содержит функции, необходимые для размещения модели в качестве веб-службы. Следующий пример демонстрирует создание объекта среды и использование его с конфигурацией выводов:
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
>                                                            pip_packages=['azureml-defaults'])
> inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
> ```

Для получения дополнительной информации [Create and manage environments for training and deployment](how-to-use-environments.md)о средах см.

Для получения дополнительной информации о [Deploy models with Azure Machine Learning](how-to-deploy-and-where.md)конфигурации выводов см.

> [!IMPORTANT]
> При развертывании в функции не требуется создавать __конфигурацию развертывания.__

## <a name="install-the-sdk-preview-package-for-functions-support"></a>Установка пакета предварительного просмотра SDK для поддержки функций

Для создания пакетов для функций Azure необходимо установить пакет предварительного просмотра SDK.

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>Создание образа

Для создания изображения Docker, развернутого в Azure Functions, используйте [azureml.contrib.functions.package](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py) или конкретную функцию пакета для триггера, который вы заинтересованы в использовании. Следующий фрагмент кода демонстрирует, как создать новый пакет с триггером blob из модели и конфигурацией выводов:

> [!NOTE]
> Фрагмент кода предполагает, что `model` содержит зарегистрированную модель `inference_config` и содержит конфигурацию для среды выводов. Для получения дополнительной информации [см.](how-to-deploy-and-where.md)

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import BLOB_TRIGGER
blob = package(ws, [model], inference_config, functions_enabled=True, trigger=BLOB_TRIGGER, input_path="input/{blobname}.json", output_path="output/{blobname}_out.json")
blob.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(blob.location)
```

Когда `show_output=True`отображается выход процесса сборки Docker. После завершения процесса изображение было создано в реестре контейнеров Azure для рабочего пространства. После построения изображения отображается местоположение в реестре контейнеров Azure. Возвращается место в `<acrinstance>.azurecr.io/package@sha256:<hash>`формате .

> [!NOTE]
> Упаковка для функций в настоящее время поддерживает HTTP триггеры, Blob триггеры и триггеры шины службы. Для получения дополнительной информации [Azure Functions bindings](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob-trigger#blob-name-patterns)о триггерах см.

> [!IMPORTANT]
> Сохранить информацию о местоположении, так как она используется при развертывании изображения.

## <a name="deploy-image-as-a-web-app"></a>Развертывание изображения как веб-приложения

1. Используйте следующую команду, чтобы получить учетные данные входа в реестр контейнеров Azure, содержащий изображение. Заменить `<myacr>` значение, возвращенное ранее из: `package.location` 

    ```azurecli-interactive
    az acr credential show --name <myacr>
    ```

    Выход этой команды аналогичен следующему документу JSON:

    ```json
    {
    "passwords": [
        {
        "name": "password",
        "value": "Iv0lRZQ9762LUJrFiffo3P4sWgk4q+nW"
        },
        {
        "name": "password2",
        "value": "=pKCxHatX96jeoYBWZLsPR6opszr==mg"
        }
    ],
    "username": "myml08024f78fd10"
    }
    ```

    Сохранить значение для __имени пользователя__ и один из __паролей__.

1. Если у вас еще нет группы ресурсов или плана службы приложений для развертывания службы, следующие команды демонстрируют, как создать и то, и другое:

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku B1 --is-linux
    ```

    В этом примере используется базовый уровень _ценообразования Linux_ ()`--sku B1`

    > [!IMPORTANT]
    > Изображения, созданные Azure Machine Learning, используют `--is-linux` Linux, поэтому необходимо использовать параметр.

1. Создайте учетную запись для хранения данных для хранения веб-мест и получите строку соединения. Замените `<webjobStorage>` имя, которое вы хотите использовать.

    ```azurecli-interactive
    az storage account create --name <webjobStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <webJobStorage> --query connectionString --output tsv
    ```

1. Чтобы создать функциональное приложение, используйте следующую команду. Замените `<app-name>` имя, которое вы хотите использовать. `<acrinstance>` Заменить `<imagename>` и с значениями из вернулся `package.location` ранее. Заменить `<webjobStorage>` имя учетной записи хранилища с предыдущего шага:

    ```azurecli-interactive
    az functionapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename> --storage-account <webjobStorage>
    ```

    > [!IMPORTANT]
    > На этом этапе создано приложение функции. Однако, поскольку вы не предоставили строку соединения для триггера каплю или учетных данных в реестр контейнеров Azure, содержащий изображение, приложение функция не активна. В следующих шагах вы предоставляете строку соединения и информацию о проверке подлинности для реестра контейнеров. 

1. Создайте учетную запись для хранения для хранения триггера blob и получите строку соединения. Замените `<triggerStorage>` имя, которое вы хотите использовать.

    ```azurecli-interactive
    az storage account create --name <triggerStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactiv
    az storage account show-connection-string --resource-group myresourcegroup --name <triggerStorage> --query connectionString --output tsv
    ```
    Запишите эту строку соединения, чтобы обеспечить функциональное приложение. Мы будем использовать его позже, когда мы попросим`<triggerConnectionString>`

1. Создайте контейнеры для ввода и вывода в учетной записи хранилища. Заменить `<triggerConnectionString>` строку соединения, возвращенную ранее:

    ```azurecli-interactive
    az storage container create -n input --connection-string <triggerConnectionString>
    ```
    ```azurecli-interactive
    az storage container create -n output --connection-string <triggerConnectionString>
    ```

1. Чтобы связать строку соединения триггера с приложением функции, используйте следующую команду. Замените `<app-name>` название приложения функции. Заменить `<triggerConnectionString>` строку соединения, возвращенную ранее:

    ```azurecli-interactive
    az functionapp config appsettings set --name <app-name> --resource-group myresourcegroup --settings "TriggerConnectionString=<triggerConnectionString>"
    ```
1. Вам нужно будет получить тег, связанный с созданным контейнером, с помощью следующей команды. Заменить `<username>` имя пользователя, возвращенное ранее из реестра контейнеров:

    ```azurecli-interactive
    az acr repository show-tags --repository package --name <username> --output tsv
    ```
    Сохранить возвращенное значение, оно `imagetag` будет использоваться в качестве следующего шага.

1. Чтобы предоставить функции приложения с учетными данными, необходимыми для доступа к реестру контейнеров, используйте следующую команду. Замените `<app-name>` название приложения функции. `<acrinstance>` Заменить `<imagetag>` и с значениями из вызова CLI на предыдущем этапе. `<username>` Заменить `<password>` и с информацией о входе ACR, извлеченной ранее:

    ```azurecli-interactive
    az functionapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagetag> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
    ```

    Эта команда возвращает информацию, похожую на следующий документ JSON:

    ```json
    [
    {
        "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
        "slotSetting": false,
        "value": "false"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_URL",
        "slotSetting": false,
        "value": "https://myml08024f78fd10.azurecr.io"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_USERNAME",
        "slotSetting": false,
        "value": "myml08024f78fd10"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
        "slotSetting": false,
        "value": null
    },
    {
        "name": "DOCKER_CUSTOM_IMAGE_NAME",
        "value": "DOCKER|myml08024f78fd10.azurecr.io/package:20190827195524"
    }
    ]
    ```

На этом этапе приложение функции начинает загрузку изображения.

> [!IMPORTANT]
> Это может занять несколько минут, прежде чем изображение загрузилось. Вы можете отслеживать прогресс с помощью портала Azure.

## <a name="test-the-deployment"></a>тестирование развертывания

После загрузки изображения и доступности приложения используйте следующие шаги, чтобы запустить приложение:

1. Создайте текстовый файл, содержащий данные, которые ожидает score.py файл. Следующий пример будет работать с score.py, который ожидает массив из 10 чисел:

    ```json
    {"data": [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10], [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
    ```

    > [!IMPORTANT]
    > Формат данных зависит от того, что ожидает ваше score.py и модели.

2. Используйте следующую команду, чтобы загрузить этот файл в контейнер ввода в копилку хранилища триггеров, созданную ранее. Замените `<file>` имя файла, содержащего данные. Заменить `<triggerConnectionString>` строку соединения, возвращенную ранее. В этом `input` примере — имя вхотворного контейнера, созданного ранее. Если вы использовали другое имя, замените это значение:

    ```azurecli-interactive
    az storage blob upload --container-name input --file <file> --name <file> --connection-string <triggerConnectionString>
    ```

    Выход этой команды аналогичен следующему JSON:

    ```json
    {
    "etag": "\"0x8D7C21528E08844\"",
    "lastModified": "2020-03-06T21:27:23+00:00"
    }
    ```

3. Для просмотра вывода, произведенного функцией, используйте следующую команду для составления списка генерируемых выводных файлов. Заменить `<triggerConnectionString>` строку соединения, возвращенную ранее. В этом `output` примере — название выходного контейнера, созданного ранее. Если вы использовали другое имя, замените это значение:

    ```azurecli-interactive
    az storage blob list --container-name output --connection-string <triggerConnectionString> --query '[].name' --output tsv
    ```

    Выход этой команды аналогичен `sample_input_out.json`.

4. Чтобы загрузить файл и проверить содержимое, используйте следующую команду. Заменить `<file>` имя файла, возвращенное предыдущей командой. Заменить `<triggerConnectionString>` строку соединения, возвращенную ранее: 

    ```azurecli-interactive
    az storage blob download --container-name output --file <file> --name <file> --connection-string <triggerConnectionString>
    ```

    Как только команда завершается, откройте файл. Он содержит данные, возвращенные моделью.

Для получения дополнительной информации об использовании триггеров blob см. [Create a function triggered by Azure Blob storage](/azure/azure-functions/functions-create-storage-blob-triggered-function)

## <a name="next-steps"></a>Дальнейшие действия

* Научитесь настраивать приложение функций в документации [функций.](/azure/azure-functions/functions-create-function-linux-custom-image)
* Подробнее о том, как хранилище Blob запускает [привязки хранения Azure Blob.](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob)
* [Развертывание модели в службе приложений Azure.](how-to-deploy-app-service.md)
* [Использование модели Машинного обучения Azure, развернутой в качестве веб-службы](how-to-consume-web-service.md)
* [Справка по API](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py)
