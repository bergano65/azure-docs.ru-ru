---
title: Развертывание моделей в службе "экземпляры контейнеров Azure"
titleSuffix: Azure Machine Learning
description: Узнайте, как развернуть модели Машинное обучение Azure в качестве веб-службы с помощью службы "экземпляры контейнеров Azure".
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/08/2019
ms.openlocfilehash: 27988edaf9d6fe70288352b0ba45945e32976d4d
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71034648"
---
# <a name="deploy-a-model-to-azure-container-instances"></a>Развертывание модели в службе "экземпляры контейнеров Azure"

Узнайте, как использовать Машинное обучение Azure для развертывания модели в качестве веб-службы в службе "экземпляры контейнеров Azure" (ACI). Используйте службу "экземпляры контейнеров Azure", если выполняется одно из следующих условий.

- вам важно быстро выполнять развертывание и проверку модели. Создавать контейнеры ACI не нужно заранее. Они создаются в рамках процесса развертывания.
- вы тестируете модель, которая находится в стадии разработки. 

Сведения о доступности квот и регионов для ACI см. в статье о [квотах и доступности регионов для экземпляров контейнеров Azure](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) .

## <a name="prerequisites"></a>Предварительные требования

- Рабочая область машинного обучения Azure. Дополнительные сведения см. в статье [создание машинное обучение Azure рабочей области](how-to-manage-workspace.md).

- Модель машинного обучения, зарегистрированная в вашей рабочей области. Если у вас нет зарегистрированной модели, см. раздел [как и где развертывать модели](how-to-deploy-and-where.md).

- [Расширение Azure CLI для службы машинное обучение](reference-azure-machine-learning-cli.md), [машинное обучение Azure пакет SDK для Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)или [расширение машинное обучение Azure Visual Studio Code](how-to-vscode-tools.md).

- В фрагментах кода __Python__ в этой статье предполагается, что установлены следующие переменные:

    * `ws`— Укажите рабочую область.
    * `model`— Укажите вашу зарегистрированную модель.
    * `inference_config`— Задайте в качестве конфигурации вывода для модели.

    Дополнительные сведения об установке этих переменных см. в разделе [как и где развертываются модели](how-to-deploy-and-where.md).

- В фрагментах кода __CLI__ в этой статье предполагается, что вы `inferenceconfig.json` создали документ. Дополнительные сведения о создании этого документа см. в разделе [как и где развертываются модели](how-to-deploy-and-where.md).

## <a name="deploy-to-aci"></a>Развертывание в ACI

Чтобы развернуть модель в службе "экземпляры контейнеров Azure", создайте __конфигурацию развертывания__ , которая описывает требуемые ресурсы вычислений. Например, число ядер и память. Также необходима __Конфигурация вывода__, описывающая среду, необходимую для размещения модели и веб-службы. Дополнительные сведения о создании конфигурации вывода см. в разделе [как и где развертываются модели](how-to-deploy-and-where.md).

### <a name="using-the-sdk"></a>Использование пакета SDK

```python
from azureml.core.webservice import AciWebservice, Webservice
from azureml.core.model import Model

deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Дополнительные сведения о классах, методах и параметрах, используемых в этом примере, см. в следующих справочных документах:

* [АЦивебсервице. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-)
* [Модель. Развертывание](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-)
* [WebService. wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Использование интерфейса командной строки

Для развертывания с помощью интерфейса командной строки используйте следующую команду. Замените `mymodel:1` на имя и версию зарегистрированной модели. Замените `myservice` именем для предоставления этой службе:

```azurecli-interactive
az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-aci-deploy-config.md)]

Дополнительные сведения см. в справочнике по [развертыванию модели языка AZ ML](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) . 

## <a name="using-vs-code"></a>Использование VS Code

См. статью [Развертывание моделей с помощью VS Code](how-to-vscode-tools.md#deploy-and-manage-models).

> [!IMPORTANT]
> Вам не нужно создавать контейнер ACI для предварительного тестирования. Контейнеры ACI создаются по мере необходимости.

## <a name="update-the-web-service"></a>Обновление веб-службы

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>Следующие шаги

* [Развертывание модели с помощью пользовательского образа DOCKER](how-to-deploy-custom-docker-image.md)
* [Устранение неполадок развертывания](how-to-troubleshoot-deployment.md)
* [Защита веб-служб Машинного обучения Azure с помощью SSL](how-to-secure-web-service.md)
* [Использование модели Машинного обучения Azure, развернутой в качестве веб-службы](how-to-consume-web-service.md)
* [Мониторинг моделей машинного обучения в Azure с помощью Application Insights](how-to-enable-app-insights.md)
* [Сбор данных для моделей в рабочей среде](how-to-enable-data-collection.md)
