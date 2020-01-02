---
title: Развертывание моделей ML в приложениях функций Azure (Предварительная версия)
titleSuffix: Azure Machine Learning
description: Узнайте, как использовать Машинное обучение Azure для развертывания модели в приложении "функции Azure".
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: vaidyas
ms.reviewer: larryfr
ms.date: 11/22/2019
ms.openlocfilehash: d35f0bbc057c97ed4f8b6429c0573fa6bcf3742f
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74807380"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-preview"></a>Развертывание модели машинного обучения в функциях Azure (Предварительная версия)
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Узнайте, как развернуть модель из Машинное обучение Azure как приложения-функции в службе "функции Azure".

> [!IMPORTANT]
> Хотя функции Машинное обучение Azure и Azure общедоступны, возможность упаковать модель из службы Машинное обучение для функций доступна в предварительной версии.

С помощью Машинное обучение Azure можно создавать образы DOCKER из обученных моделей машинного обучения. Машинное обучение Azure теперь имеет функции предварительной версии для создания этих моделей машинного обучения в приложениях функций, которые можно [развернуть в функциях Azure](https://docs.microsoft.com/azure/azure-functions/functions-deployment-technologies#docker-container).

## <a name="prerequisites"></a>Технические условия

* Рабочая область машинного обучения Azure. Дополнительные сведения см. в статье [Создание рабочей области](how-to-manage-workspace.md) .
* [Интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* Обученная модель машинного обучения, зарегистрированная в вашей рабочей области. Если модель отсутствует, используйте [учебник по классификации образов: обучение модели](tutorial-train-models-with-aml.md) для обучения и регистрации.

    > [!IMPORTANT]
    > В фрагментах кода в этой статье предполагается, что вы установили следующие переменные:
    >
    > * `ws` — ваша рабочая область Машинное обучение Azure.
    > * `model` — Зарегистрированная модель, которая будет развернута.
    > * `inference_config` — конфигурация вывода для модели.
    >
    > Дополнительные сведения об установке этих переменных см. в разделе [Развертывание моделей с помощью машинное обучение Azure](how-to-deploy-and-where.md).

## <a name="prepare-for-deployment"></a>Подготовка к развертыванию

Перед развертыванием необходимо определить, что нужно для запуска модели в качестве веб-службы. В следующем списке описаны основные элементы, необходимые для развертывания.

* __Скрипт записи__. Этот скрипт принимает запросы, оценивает запрос с помощью модели и возвращает результаты.

    > [!IMPORTANT]
    > Сценарий записи зависит от модели. Он должен понимать формат данных входящего запроса, формат данных, ожидаемых моделью, и формат данных, возвращаемых клиентам.
    >
    > Если данные запроса имеют формат, непригодный для использования в вашей модели, скрипт может преобразовать его в допустимый формат. Он также может преобразовать ответ перед возвратом клиенту.
    >
    > По умолчанию при упаковке для функций входные данные обрабатываются как текст. Если вы заинтересованы в использовании необработанных байт входных данных (например, для триггеров BLOB-объектов), следует использовать [амлрекуест, чтобы принимать необработанные данные](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#binary-data).


* **Зависимости**, такие как вспомогательные скрипты или пакеты Python или Conda, необходимые для запуска сценария записи или модели

Эти сущности инкапсулированы в __конфигурацию вывода__. Конфигурация вывода ссылается на скрипт записи и другие зависимости.

> [!IMPORTANT]
> При создании конфигурации вывода для использования с функциями Azure необходимо использовать объект [среды](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) . В следующем примере демонстрируется создание объекта среды и его использование с конфигурацией вывода:
>
> ```python
> from azureml.core import Environment
> from azureml.core.environment import CondaDependencies
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
> ```

Дополнительные сведения о средах см. в статье [создание сред для обучения и развертывания и управление ими](how-to-use-environments.md).

Дополнительные сведения о конфигурации вывода см. в разделе [Развертывание моделей с помощью машинное обучение Azure](how-to-deploy-and-where.md).

> [!IMPORTANT]
> При развертывании в функциях не нужно создавать __конфигурацию развертывания__.

## <a name="install-the-sdk-preview-package-for-functions-support"></a>Установка пакета SDK для поддержки функций

Чтобы создать пакеты для функций Azure, необходимо установить пакет SDK предварительной версии.

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>Создание образа

Чтобы создать образ DOCKER, развернутый в функциях Azure, используйте [azureml. от участников сообщества. functions. Package](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py) или конкретную функцию пакета для триггера, который вы хотите использовать. В следующем фрагменте кода показано, как создать новый пакет с триггером большого двоичного объекта из модели и конфигурации вывода:

> [!NOTE]
> В фрагменте кода предполагается, что `model` содержит зарегистрированную модель и `inference_config` содержит конфигурацию для среды вывода. Дополнительные сведения см. в разделе [Развертывание моделей с помощью машинное обучение Azure](how-to-deploy-and-where.md).

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import BLOB_TRIGGER
blob = package(ws, [model], inference_config, functions_enabled=True, trigger=BLOB_TRIGGER, input_path="input/{blobname}.json", output_path="output/{blobname}_out.json")
blob.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(blob.location)
```

При `show_output=True`отображаются выходные данные процесса сборки DOCKER. После завершения процесса образ будет создан в реестре контейнеров Azure для вашей рабочей области. После построения образа в реестре контейнеров Azure отобразится расположение. Возвращаемое расположение имеет формат `<acrinstance>.azurecr.io/package@sha256:<hash>`.

> [!NOTE]
> Упаковка для функций в настоящее время поддерживает триггеры HTTP, триггеры больших двоичных объектов и триггеры служебной шины. Дополнительные сведения о триггерах см. в статье [привязки функций Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob?tabs=csharp#trigger---blob-name-patterns).

> [!IMPORTANT]
> Сохраните сведения о расположении, так как они используются при развертывании образа.

## <a name="deploy-image-as-a-web-app"></a>Развертывание образа как веб-приложения

1. Используйте следующую команду, чтобы получить учетные данные входа для реестра контейнеров Azure, содержащего образ. Замените `<acrinstance>` значением, которое было возвращено из `package.location`: 

    ```azurecli-interactive
    az acr credential show --name <myacr>
    ```

    Выходные данные этой команды похожи на следующий документ JSON:

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

    Сохраните значение __username__ и один из __паролей__.

1. Если у вас еще нет группы ресурсов или плана службы приложений для развертывания службы, в следующих командах показано, как создать оба:

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku EP1 --is-linux
    ```

    В этом примере используется ценовая категория _Linux Premium_ (`--sku EP1`).

    > [!IMPORTANT]
    > Образы, созданные Машинное обучение Azure используют Linux, поэтому необходимо использовать параметр `--is-linux`.

1. Чтобы создать приложение функции, используйте следующую команду. Замените `<app-name>` именем, которое вы хотите использовать. Замените `<acrinstance>` и `<imagename>` значениями, полученными `package.location` ранее:

    ```azurecli-interactive
    az storage account create --name 
    az functionapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename>
    ```

    > [!IMPORTANT]
    > На этом этапе приложение функции создано. Однако, так как вы не указали строку подключения для триггера большого двоичного объекта или учетных данных в реестре контейнеров Azure, который содержит образ, приложение-функция неактивно. На следующих шагах вы укажите строку подключения и сведения для проверки подлинности для реестра контейнеров. 

1. Создайте учетную запись хранения, которая будет использоваться в качестве триггера, и получите ее строку подключения.

    ```azurecli-interactive
    az storage account create --name triggerStorage --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name triggerStorage --query connectionString --output tsv
    ```
    Запишите эту строку подключения, чтобы предоставить приложению функции. Он будет использоваться позже при запросе `<triggerConnectionString>`

1. Создайте контейнеры для входных и выходных данных в учетной записи хранения. 

    ```azurecli-interactive
    az storage container create -n input --connection-string <triggerConnectionString>
    ```
    ```azurecli-interactive
    az storage container create -n output --connection-string <triggerConnectionString>
    ```

1. Необходимо получить тег, связанный с созданным контейнером, с помощью следующей команды:

    ```azurecli-interactive
    az acr repository show-tags --repository package --name <username> --output tsv
    ```
    Последний показанный тег будет `imagetag` ниже.

1. Чтобы предоставить приложению функции учетные данные, необходимые для доступа к реестру контейнеров, используйте следующую команду. Замените `<app-name>` именем, которое вы хотите использовать. Замените `<acrinstance>` и `<imagetag>` значениями из вызова AZ CLI на предыдущем шаге. Замените `<username>` и `<password>` полученными ранее сведениями об имени для входа записи контроля доступа:

    ```azurecli-interactive
    az functionapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagetag> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
    ```

    Эта команда возвращает сведения, аналогичные следующему документу JSON:

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

На этом этапе приложение функции начинает загрузку образа.

> [!IMPORTANT]
> Загрузка образа может занять несколько минут. Ход выполнения можно отслеживать с помощью портала Azure.

## <a name="next-steps"></a>Дальнейшие действия

* Научитесь настраивать приложение функций в документации по [функциям](/azure/azure-functions/functions-create-function-linux-custom-image) .
* Дополнительные сведения о службе хранилища BLOB-объектов см. в статье триггеры [хранилища BLOB-объектов Azure](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob).
* [Разверните модель в службе приложений Azure](how-to-deploy-app-service.md).
* [Использование модели Машинного обучения Azure, развернутой в качестве веб-службы](how-to-consume-web-service.md)
* [Справочник по API](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py)