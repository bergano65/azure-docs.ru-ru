---
title: Развертывание моделей ml в службу приложений Azure (предварительный просмотр)
titleSuffix: Azure Machine Learning
description: Узнайте, как использовать машинное обучение Azure для развертывания модели в Web-приложении в службе приложений Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/27/2019
ms.openlocfilehash: 3e6cfde20d9f4d56af836e06b0c9a84010dea47b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282823"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Развертывание модели машинного обучения в службе приложений Azure (предварительный просмотр)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Узнайте, как развернуть модель из машинного обучения Azure в качестве веб-приложения в службе приложений Azure.

> [!IMPORTANT]
> Хотя как Azure Machine Learning, так и служба приложений Azure, как правило, доступны, возможность развертывания модели из службы машинного обучения в службу приложений находится в предварительном просмотре.

С помощью машинного обучения Azure можно создавать изображения Docker из обученных моделей машинного обучения. Это изображение содержит веб-службу, которая получает данные, отправляет их в модель, а затем возвращает ответ. Служба приложений Azure может использоваться для развертывания изображения и предоставляет следующие функции:

* Расширенная [аутентификация](/azure/app-service/configure-authentication-provider-aad) для повышения безопасности. Методы аутентификации включают как Azure Active Directory, так и многофакторную auth.
* [Autoscale](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json) без необходимости передислокации.
* [Поддержка TLS](/azure/app-service/configure-ssl-certificate-in-code) для безопасной связи между клиентами и службой.

Для получения дополнительной информации об особенностях, предоставляемых Службой приложений Azure, см. [App Service overview](/azure/app-service/overview)

> [!IMPORTANT]
> Если вам нужна возможность регистрации данных скоринга, используемых с развернутой моделью, или результатов скоринга, следует вместо этого развернуть в службе Azure Kubernetes. Для получения дополнительной информации смотрите [сбор данных о ваших производственных моделях.](how-to-enable-data-collection.md)

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

    > [!IMPORTANT]
    > SDK Azure Machine Learning SDK не предоставляет доступ к веб-службе в хранилище данных или наборах данных. Если развернутая модель необходима для доступа к данным, хранящимся за пределами развертывания, например в учетной записи Хранилища Azure, необходимо разработать решение пользовательского кода с помощью соответствующего SDK. Например, [SDK хранения Azure для Python](https://github.com/Azure/azure-storage-python).
    >
    > Другой альтернативой, которая может работать для вашего сценария, являются [пакетные прогнозы,](how-to-use-parallel-run-step.md)которые предоставляют доступ к хранилищам данных при подсчете очков.

    Для получения дополнительной информации о скриптах входа см. [Модели развертываний с помощью машинного обучения Azure.](how-to-deploy-and-where.md)

* **Зависимости,** такие как скрипты-помощники или пакеты Python/Conda, необходимые для запуска сценария или модели входа

Эти сущности инкапсулированы в __конфигурацию выводов.__ В конфигурации зависимостей указываются скрипт входа и другие зависимости.

> [!IMPORTANT]
> При создании конфигурации выводов для использования в Службе приложений Azure необходимо использовать объект [среды.](https://docs.microsoft.com//python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) Обратите внимание, что если вы определяете пользовательскую среду, необходимо добавить azureml-по умолчанию с версией >1,0,45 в качестве зависимости от пипсов. Этот пакет содержит функции, необходимые для размещения модели в качестве веб-службы. Следующий пример демонстрирует создание объекта среды и использование его с конфигурацией выводов:
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
> from azureml.core.model import InferenceConfig
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
> При развертывании в службе приложений Azure не требуется создавать __конфигурацию развертывания.__

## <a name="create-the-image"></a>Создание образа

Для создания изображения Docker, развернутого в службе приложений Azure, используйте [Model.package](https://docs.microsoft.com//python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config-none--generate-dockerfile-false-). Следующий фрагмент кода демонстрирует, как построить новое изображение из модели и конфигурации выводов:

> [!NOTE]
> Фрагмент кода предполагает, что `model` содержит зарегистрированную модель `inference_config` и содержит конфигурацию для среды выводов. Для получения дополнительной информации [см.](how-to-deploy-and-where.md)

```python
from azureml.core import Model

package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(package.location)
```

Когда `show_output=True`отображается выход процесса сборки Docker. После завершения процесса изображение было создано в реестре контейнеров Azure для рабочего пространства. После построения изображения отображается местоположение в реестре контейнеров Azure. Возвращается место в `<acrinstance>.azurecr.io/package:<imagename>`формате . Например, `myml08024f78fd10.azurecr.io/package:20190827151241`.

> [!IMPORTANT]
> Сохранить информацию о местоположении, так как она используется при развертывании изображения.

## <a name="deploy-image-as-a-web-app"></a>Развертывание изображения как веб-приложения

1. Используйте следующую команду, чтобы получить учетные данные входа в реестр контейнеров Azure, содержащий изображение. Заменить `<acrinstance>` с й e `package.location`значение вернулся ранее из :

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

    В этом примере используется`--sku B1` __базовый__ уровень ценообразования ()

    > [!IMPORTANT]
    > Изображения, созданные Azure Machine Learning, используют `--is-linux` Linux, поэтому необходимо использовать параметр.

1. Для создания веб-приложения используйте следующую команду. Замените `<app-name>` имя, которое вы хотите использовать. `<acrinstance>` Заменить `<imagename>` и с значениями из вернулся `package.location` ранее:

    ```azurecli-interactive
    az webapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename>
    ```

    Эта команда возвращает информацию, похожую на следующий документ JSON:

    ```json
    {
    "adminSiteName": null,
    "appServicePlanName": "myplanname",
    "geoRegion": "West Europe",
    "hostingEnvironmentProfile": null,
    "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myplanname",
    "kind": "linux",
    "location": "West Europe",
    "maximumNumberOfWorkers": 1,
    "name": "myplanname",
    < JSON data removed for brevity. >
    "targetWorkerSizeId": 0,
    "type": "Microsoft.Web/serverfarms",
    "workerTierName": null
    }
    ```

    > [!IMPORTANT]
    > На этом этапе было создано веб-приложение. Однако, поскольку вы не предоставили учетные данные в реестр контейнеров Azure, содержащий изображение, веб-приложение не является активным. На следующем этапе вы предоставляете информацию о проверке подлинности для реестра контейнеров.

1. Чтобы предоставить веб-приложению учетные данные, необходимые для доступа к реестру контейнеров, используйте следующую команду. Замените `<app-name>` имя, которое вы хотите использовать. `<acrinstance>` Заменить `<imagename>` и с значениями из вернулся `package.location` ранее. `<username>` Заменить `<password>` и с информацией о входе ACR, извлеченной ранее:

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagename> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
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

На этом этапе веб-приложение начинает загрузку изображения.

> [!IMPORTANT]
> Это может занять несколько минут, прежде чем изображение загрузилось. Для мониторинга прогресса используйте следующую команду:
>
> ```azurecli-interactive
> az webapp log tail --name <app-name> --resource-group myresourcegroup
> ```
>
> После загрузки изображения и активности сайта журнал отображает сообщение, в `Container <container name> for site <app-name> initialized successfully and is ready to serve requests`которое указывается.

После развертывания изображения можно найти имя хоста, используя следующую команду:

```azurecli-interactive
az webapp show --name <app-name> --resource-group myresourcegroup
```

Эта команда возвращает информацию, похожую на следующее имя хоста - `<app-name>.azurewebsites.net`. Используйте это значение как часть __базового URL__ для службы.

## <a name="use-the-web-app"></a>Использование веб-приложения

Веб-сервис, который передает запросы `{baseurl}/score`модели, находится по адресу . Например, `https://<app-name>.azurewebsites.net/score`. Следующий код Python демонстрирует, как отправлять данные на URL и отображать ответ:

```python
import requests
import json

scoring_uri = "https://mywebapp.azurewebsites.net/score"

headers = {'Content-Type':'application/json'}

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

## <a name="next-steps"></a>Дальнейшие действия

* Научитесь настраивать web-приложение в [Службе приложений на](/azure/app-service/containers/) документации Linux.
* Узнайте больше о масштабировании в [Get started с Autoscale в Azure](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json).
* [Используйте сертификат TLS/SSL в службе приложений Azure.](/azure/app-service/configure-ssl-certificate-in-code)
* [Настройте приложение Службы приложений, чтобы использовать систему регистрации Azure Active Directory.](/azure/app-service/configure-authentication-provider-aad)
* [Использование модели Машинного обучения Azure, развернутой в качестве веб-службы](how-to-consume-web-service.md)
