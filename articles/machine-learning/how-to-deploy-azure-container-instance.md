---
title: Как развернуть модели в экземплярах контейнеров Azure
titleSuffix: Azure Machine Learning
description: Узнайте, как развернуть модели машинного обучения Azure в качестве веб-сервиса с помощью экземпляров контейнеров Azure Instances.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 12/27/2019
ms.openlocfilehash: d460112394d7c7b7d2da4e8af41c0085b67226ec
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475472"
---
# <a name="deploy-a-model-to-azure-container-instances"></a>Развертывание веб-служб в Экземплярах контейнеров Azure.
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Узнайте, как использовать Azure Machine Learning для развертывания модели в качестве веб-службы в экземплярах контейнеров Azure (ACI). Используйте экземпляры контейнеров Azure, если одно из следующих условий верно:

- вам важно быстро выполнять развертывание и проверку модели. Вам не нужно создавать контейнеры ACI заранее. Они создаются в рамках процесса развертывания.
- вы тестируете модель, которая находится в стадии разработки. 

Информацию о квотах и наличии aCI для ACI можно узнать в статье [«Квоты и доступность регионов» для статьи Azure Container Instances.](https://docs.microsoft.com/azure/container-instances/container-instances-quotas)

## <a name="prerequisites"></a>Предварительные требования

- Рабочая область машинного обучения Azure. Для получения дополнительной информации [см.](how-to-manage-workspace.md)

- Модель машинного обучения, зарегистрированная в вашем рабочем пространстве. Если у вас нет зарегистрированной модели, см., [как и где развертывать модели.](how-to-deploy-and-where.md)

- [Расширение Azure CLI для службы машинного обучения,](reference-azure-machine-learning-cli.md) [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)или [расширение кода Visual Studio Azure Machine Learning.](tutorial-setup-vscode-extension.md)

- Фрагменты кода __Python__ в этой статье предполагают, что установлены следующие переменные:

    * `ws`- Установите в рабочее пространство.
    * `model`- Установите на зарегистрированную модель.
    * `inference_config`- Установите конфигурацию выводов для модели.

    Для получения дополнительной информации об установке этих переменных см. [Как и где развертывать модели.](how-to-deploy-and-where.md)

- Фрагменты __CLI__ в этой статье предполагают, что `inferenceconfig.json` вы создали документ. Для получения дополнительной информации [How and where to deploy models](how-to-deploy-and-where.md)о создании этого документа см.

## <a name="deploy-to-aci"></a>Развертывание в ACI

Чтобы развернуть модель в экземплярах контейнеров Azure, создайте __конфигурацию развертывания,__ описывающая необходимые ресурсы вычислений. Например, количество ядер и память. Вам также нужна __конфигурация выводов,__ которая описывает среду, необходимую для размещения модели и веб-службы. Для получения дополнительной информации о создании конфигурации выводов см. [Как и где развертывать модели.](how-to-deploy-and-where.md)

### <a name="using-the-sdk"></a>Использование пакета SDK

```python
from azureml.core.webservice import AciWebservice, Webservice
from azureml.core.model import Model

deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Для получения дополнительной информации о классах, методах и параметрах, используемых в данном примере, см.

* [AciWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [Модель.развертывание](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Использование интерфейса командной строки

Для развертывания с помощью CLI используйте следующую команду. Заменить `mymodel:1` имя и версию зарегистрированной модели. Замените `myservice` имя, чтобы дать эту услугу:

```azurecli-interactive
az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

Для получения дополнительной [az ml model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) информации см. 

## <a name="using-vs-code"></a>Использование VS Code

Смотрите [развернуть модели с VS Code.](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model)

> [!IMPORTANT]
> Вам не нужно создавать контейнер ACI для тестирования заранее. Контейнеры ACI создаются по мере необходимости.

## <a name="update-the-web-service"></a>Обновление веб-службы

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>Следующие шаги

* [Как развернуть модель с помощью пользовательского изображения Docker](how-to-deploy-custom-docker-image.md)
* [Устранение неполадок развертывания](how-to-troubleshoot-deployment.md)
* [Используйте TLS для обеспечения безопасности веб-службы через Машинное обучение Azure](how-to-secure-web-service.md)
* [Использование модели Машинного обучения Azure, развернутой в качестве веб-службы](how-to-consume-web-service.md)
* [Мониторинг моделей машинного обучения Azure с помощью приложений Insights](how-to-enable-app-insights.md)
* [Сбор данных для моделей в рабочей среде](how-to-enable-data-collection.md)
