---
title: Развертывание моделей ML в службе приложений Azure (Предварительная версия)
titleSuffix: Azure Machine Learning service
description: Узнайте, как использовать службу Машинное обучение Azure для развертывания модели в веб-приложении в службе приложений Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/27/2019
ms.openlocfilehash: 20a90a70c66310f6838b41a40aa945308bf338d4
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147902"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Развертывание модели машинного обучения в службе приложений Azure (Предварительная версия)

Узнайте, как развернуть модель из службы Машинное обучение Azure в качестве веб-приложения в службе приложений Azure.

> [!IMPORTANT]
> Хотя служба Машинное обучение Azure и служба приложений Azure являются общедоступными, возможность развертывания модели из службы Машинное обучение в службе приложений доступна в предварительной версии.

С помощью службы Машинное обучение Azure можно создавать образы DOCKER из обученных моделей машинного обучения. Этот образ содержит веб-службу, которая получает данные, отправляет ее в модель, а затем возвращает ответ. Службу приложений Azure можно использовать для развертывания образа и предоставляет следующие возможности.

* Расширенная [Проверка подлинности](/azure/app-service/configure-authentication-provider-aad) для усиления безопасности. Методы проверки подлинности включают как Azure Active Directory, так и многофакторную проверку подлинности.
* Автоматическое [масштабирование](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json) без необходимости повторного развертывания.
* [Поддержка SSL](/azure/app-service/app-service-web-ssl-cert-load) для безопасной связи между клиентами и службой.

Дополнительные сведения о функциях, предоставляемых службой приложений Azure, см. в статье [Обзор службы приложений](/azure/app-service/overview).

> [!IMPORTANT]
> Если требуется возможность регистрировать данные оценки, используемые в развернутой модели, или результаты оценки, следует развернуть в службе Azure Kubernetes. Дополнительные сведения см. [в разделе Получение данных в рабочих моделях](how-to-enable-data-collection.md).

## <a name="prerequisites"></a>Предварительные требования

* Рабочая область службы машинного обучения Azure. Дополнительные сведения см. в статье [Создание рабочей области](how-to-manage-workspace.md) .
* [Интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* Обученная модель машинного обучения, зарегистрированная в вашей рабочей области. Если модель отсутствует, используйте [учебник по классификации образов: обучение модели](tutorial-train-models-with-aml.md) для обучения и регистрации.

    > [!IMPORTANT]
    > В фрагментах кода в этой статье предполагается, что вы установили следующие переменные:
    >
    > * `ws`— Рабочая область Машинное обучение Azure.
    > * `model`— Зарегистрированная модель, которая будет развернута.
    > * `inference_config`— Конфигурация вывода для модели.
    >
    > Дополнительные сведения об установке этих переменных см. в разделе [Развертывание моделей с помощью службы машинное обучение Azure](how-to-deploy-and-where.md).

## <a name="prepare-for-deployment"></a>Подготовка к развертыванию

Перед развертыванием необходимо определить, что нужно для запуска модели в качестве веб-службы. В следующем списке описаны основные элементы, необходимые для развертывания.

* __Скрипт записи__. Этот скрипт принимает запросы, оценивает запрос с помощью модели и возвращает результаты.

    > [!IMPORTANT]
    > Сценарий записи зависит от модели. Он должен понимать формат данных входящего запроса, формат данных, ожидаемых моделью, и формат данных, возвращаемых клиентам.
    >
    > Если данные запроса имеют формат, непригодный для использования в вашей модели, скрипт может преобразовать его в допустимый формат. Он также может преобразовать ответ перед возвратом клиенту.

    > [!IMPORTANT]
    > Пакет SDK для Машинное обучение Azure не предоставляет веб-службе способ доступа к хранилищу данных или наборам. Если требуется развернутая модель для доступа к данным, хранящимся за пределами развертывания, например в учетной записи хранения Azure, необходимо разработать собственное решение с кодом, используя соответствующий пакет SDK. Например, [пакет SDK для службы хранилища Azure для Python](https://github.com/Azure/azure-storage-python).
    >
    > Другой альтернативой, который может работать в вашем сценарии, являются [пакетные прогнозы](how-to-run-batch-predictions.md), которые обеспечивают доступ к хранилищам данных при оценке.

    Дополнительные сведения о сценариях входа см. в разделе [Развертывание моделей с помощью службы машинное обучение Azure](how-to-deploy-and-where.md).

* **Зависимости**, такие как вспомогательные скрипты или пакеты Python или Conda, необходимые для запуска сценария записи или модели

Эти сущности инкапсулированы в __конфигурацию вывода__. Конфигурация вывода ссылается на скрипт записи и другие зависимости.

> [!IMPORTANT]
> При создании конфигурации вывода для использования со службой приложений Azure необходимо использовать объект [среды](https://docs.microsoft.com//python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) . В следующем примере демонстрируется создание объекта среды и его использование с конфигурацией вывода:
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

Дополнительные сведения о конфигурации вывода см. в разделе [Развертывание моделей со службой машинное обучение Azure](how-to-deploy-and-where.md).

> [!IMPORTANT]
> При развертывании в службе приложений Azure не требуется создавать __конфигурацию развертывания__.

## <a name="create-the-image"></a>Создание образа

Чтобы создать образ DOCKER, развернутый в службе приложений Azure, используйте [модель. Package](https://docs.microsoft.com//python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config--generate-dockerfile-false-). В следующем фрагменте кода показано, как создать новый образ из модели и конфигурации вывода:

> [!NOTE]
> В фрагменте кода предполагается, что `model` содержит зарегистрированную модель `inference_config` и содержит конфигурацию для среды вывода. Дополнительные сведения см. [в статье Развертывание моделей со службой машинное обучение Azure](how-to-deploy-and-where.md).

```python
from azureml.core import Model

package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(package.location)
```

Если `show_output=True`задано значение, выводятся выходные данные процесса сборки DOCKER. После завершения процесса образ будет создан в реестре контейнеров Azure для вашей рабочей области. После построения образа в реестре контейнеров Azure отобразится расположение. Возвращаемое расположение имеет формат `<acrinstance>.azurecr.io/package:<imagename>`. Например, `myml08024f78fd10.azurecr.io/package:20190827151241`.

> [!IMPORTANT]
> Сохраните сведения о расположении, так как они используются при развертывании образа.

## <a name="deploy-image-as-a-web-app"></a>Развертывание образа как веб-приложения

1. Используйте следующую команду, чтобы получить учетные данные входа для реестра контейнеров Azure, содержащего образ. Замените `<acrinstance>` на значение TH e, возвращенное `package.location`ранее из: 

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
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku B1 --is-linux
    ```

    В этом примере используется __Базовая__ ценовая категория (`--sku B1`).

    > [!IMPORTANT]
    > Образы, созданные машинное обучение Azureной службой, используют Linux, поэтому необходимо использовать `--is-linux` параметр.

1. Чтобы создать веб-приложение, используйте следующую команду. Замените `<app-name>` на имя, которое вы хотите использовать. Замените `<acrinstance>` `package.location` и `<imagename>` значениями, полученными ранее:

    ```azurecli-interactive
    az webapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename>
    ```

    Эта команда возвращает сведения, аналогичные следующему документу JSON:

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
    > На этом этапе веб-приложение создано. Однако так как вы не указали учетные данные в реестре контейнеров Azure, который содержит образ, веб-приложение неактивно. На следующем шаге вы укажите сведения для проверки подлинности для реестра контейнеров.

1. Чтобы предоставить веб-приложению учетные данные, необходимые для доступа к реестру контейнеров, используйте следующую команду. Замените `<app-name>` на имя, которое вы хотите использовать. Замените `<acrinstance>` `package.location` и `<imagename>` значениями, полученными ранее. Замените `<username>` и`<password>` на сведения об имени входа записи контроля доступа, полученные ранее:

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagename> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
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

На этом этапе веб-приложение начнет загрузку образа.

> [!IMPORTANT]
> Загрузка образа может занять несколько минут. Чтобы отслеживать ход выполнения, используйте следующую команду:
>
> ```azurecli-interactive
> az webapp log tail --name <app-name> --resource-group myresourcegroup
> ```
>
> После загрузки образа и активации сайта в журнале отобразится сообщение о том `Container <container name> for site <app-name> initialized successfully and is ready to serve requests`, что это так.

После развертывания образа имя узла можно найти с помощью следующей команды:

```azurecli-interactive
az webapp show --name <app-name> --resource-group myresourcegroup
```

Эта команда возвращает сведения, аналогичные следующему имени узла — `<app-name>.azurewebsites.net`. Используйте это значение как часть __базового URL-адреса__ службы.

## <a name="use-the-web-app"></a>Использование веб-приложения

Веб-служба, которая передает запросы модели, находится в `{baseurl}/score`папке. Например, `https://<app-name>.azurewebsites.net/score`. В следующем примере кода Python показано, как отправить данные в URL-адрес и отобразить ответ:

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

## <a name="next-steps"></a>Следующие шаги

* Узнайте, как настроить веб-приложение в [службе приложений](/azure/app-service/containers/) в документации по Linux.
* Дополнительные сведения о масштабировании см. в статье [Приступая к работе с автомасштабированием в Azure](/azure/azure-monitor/platform/autoscale-get-started?toc=%2fazure%2fapp-service%2ftoc.json).
* [Используйте SSL-сертификат в службе приложений Azure](/azure/app-service/app-service-web-ssl-cert-load).
* [Настройте приложение службы приложений для использования Azure Active Directory входа](/azure/app-service/configure-authentication-provider-aad).
* [Использование модели Машинного обучения Azure, развернутой в качестве веб-службы](how-to-consume-web-service.md)
