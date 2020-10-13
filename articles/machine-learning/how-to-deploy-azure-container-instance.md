---
title: Развертывание моделей в службе "экземпляры контейнеров Azure"
titleSuffix: Azure Machine Learning
description: Узнайте, как развернуть модели Машинное обучение Azure в качестве веб-службы с помощью службы "экземпляры контейнеров Azure".
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/12/2020
ms.openlocfilehash: fc20d59ca016e7c6be8dae9f50a84e4927449467
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91660740"
---
# <a name="deploy-a-model-to-azure-container-instances"></a>Развертывание веб-служб в Экземплярах контейнеров Azure.


Узнайте, как использовать Машинное обучение Azure для развертывания модели в качестве веб-службы в службе "экземпляры контейнеров Azure" (ACI). Используйте службу "экземпляры контейнеров Azure", если выполняется одно из следующих условий.

- вам важно быстро выполнять развертывание и проверку модели. Создавать контейнеры ACI не нужно заранее. Они создаются в рамках процесса развертывания.
- вы тестируете модель, которая находится в стадии разработки. 

Сведения о доступности квот и регионов для ACI см. в статье о [квотах и доступности регионов для экземпляров контейнеров Azure](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) .

> [!IMPORTANT]
> Перед развертыванием в веб-службе настоятельно рекомендуется выполнить отладку локально. Дополнительные сведения см. в статье [Локальная отладка](https://docs.microsoft.com/azure/machine-learning/how-to-troubleshoot-deployment#debug-locally) .
>
> Вы также можете ознакомиться со статьей о Машинном обучении Azure: [Развертывание в локальный Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local)

## <a name="prerequisites"></a>Предварительные требования

- Рабочая область машинного обучения Azure. Дополнительные сведения см. в статье [создание машинное обучение Azure рабочей области](how-to-manage-workspace.md).

- Модель машинного обучения, зарегистрированная в вашей рабочей области. Если у вас нет зарегистрированной модели, см. раздел [как и где развертывать модели](how-to-deploy-and-where.md).

- [Расширение Azure CLI для службы машинное обучение](reference-azure-machine-learning-cli.md), [машинное обучение Azure пакет SDK для Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)или [расширение машинное обучение Azure Visual Studio Code](tutorial-setup-vscode-extension.md).

- В фрагментах кода __Python__ в этой статье предполагается, что установлены следующие переменные:

    * `ws` — Укажите рабочую область.
    * `model` — Укажите вашу зарегистрированную модель.
    * `inference_config` — Задайте в качестве конфигурации вывода для модели.

    Дополнительные сведения об установке этих переменных см. в разделе [как и где развертываются модели](how-to-deploy-and-where.md).

- В фрагментах кода __CLI__ в этой статье предполагается, что вы создали `inferenceconfig.json` документ. Дополнительные сведения о создании этого документа см. в разделе [как и где развертываются модели](how-to-deploy-and-where.md).

## <a name="limitations"></a>Ограничения

* При использовании экземпляров контейнеров Azure в виртуальной сети виртуальная сеть должна находиться в той же группе ресурсов, что и Рабочая область Машинное обучение Azure.
* При использовании экземпляров контейнеров Azure в виртуальной сети реестр контейнеров Azure (запись контроля доступа) для рабочей области также не может находиться в виртуальной сети.

Дополнительные сведения см. [в статье как обеспечить безопасность при работе с виртуальными сетями](how-to-secure-inferencing-vnet.md#enable-azure-container-instances-aci).

## <a name="deploy-to-aci"></a>Развертывание в ACI

Чтобы развернуть модель в службе "экземпляры контейнеров Azure", создайте __конфигурацию развертывания__ , которая описывает требуемые ресурсы вычислений. Например, число ядер и память. Также необходима __Конфигурация вывода__, описывающая среду, необходимую для размещения модели и веб-службы. Дополнительные сведения о создании конфигурации вывода см. в разделе [как и где развертываются модели](how-to-deploy-and-where.md).

> [!NOTE]
> * ACI подходит только для небольших моделей, размер которых меньше 1 ГБ. 
> * Мы рекомендуем использовать AKS с одним узлом для разработки и тестирования больших моделей.
> * Число развертываемых моделей ограничено до 1 000 моделей на развертывание (для каждого контейнера). 

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

* [AciWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [Модель. Развертывание](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truewait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Использование интерфейса командной строки

Для развертывания с помощью интерфейса командной строки используйте следующую команду. Замените на `mymodel:1` имя и версию зарегистрированной модели. Замените `myservice` именем для предоставления этой службе:

```azurecli-interactive
az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

Дополнительные сведения см. в справочнике по [развертыванию модели языка AZ ML](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest&preserve-view=true#ext-azure-cli-ml-az-ml-model-deploy) . 

## <a name="using-vs-code"></a>Использование VS Code

См. статью [Развертывание моделей с помощью VS Code](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> Вам не нужно создавать контейнер ACI для предварительного тестирования. Контейнеры ACI создаются по мере необходимости.


## <a name="next-steps"></a>Дальнейшие шаги

* [Развертывание модели с помощью пользовательского образа DOCKER](how-to-deploy-custom-docker-image.md)
* [Устранение неполадок развертывания](how-to-troubleshoot-deployment.md)
* [Обновление веб-службы](how-to-deploy-update-web-service.md)
* [Использование TLS для защиты веб-службы с помощью Машинного обучения Azure](how-to-secure-web-service.md).
* [Использование модели Машинного обучения Azure, развернутой в качестве веб-службы](how-to-consume-web-service.md)
* [Мониторинг моделей Машинное обучение Azure с помощью Application Insights](how-to-enable-app-insights.md)
* [Сбор данных для моделей в рабочей среде](how-to-enable-data-collection.md)
