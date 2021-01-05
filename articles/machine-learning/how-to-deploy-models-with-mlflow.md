---
title: Развертывание экспериментов машинного обучения с помощью Млфлов
titleSuffix: Azure Machine Learning
description: Настройте Млфлов с Машинное обучение Azure для развертывания моделей машинного обучения в качестве веб-службы.
services: machine-learning
author: shivp950
ms.author: shipatel
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 12/23/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 1159a6cfda6b877f04573c85fa437ce3bff81af1
ms.sourcegitcommit: 6cca6698e98e61c1eea2afea681442bd306487a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/24/2020
ms.locfileid: "97761803"
---
# <a name="deploy-mlflow-models-with-azure-machine-learning-preview"></a>Развертывание моделей Млфлов с помощью Машинное обучение Azure (Предварительная версия)

В этой статье описано, как развернуть модель Млфлов в качестве веб-службы Машинное обучение Azure, чтобы можно было использовать и применять к рабочим моделям возможности управления моделями Машинное обучение Azure и определения расхождения данных.

Машинное обучение Azure предлагает конфигурации развертывания для:
* Экземпляр контейнера Azure (ACI), который подходит для быстрого развертывания с поддержкой разработки и тестирования.
* Служба Kubernetes Azure (AKS), рекомендуемая для масштабируемых рабочих развертываний.

[MLflow](https://www.mlflow.org) — это библиотека с открытым кодом для управления жизненным циклом экспериментов машинного обучения. Его интеграция с Машинное обучение Azure позволяет расширить это управление за пределами этапа обучения модели на этапе развертывания производственной модели.

>[!NOTE]
> Как библиотека с открытым исходным кодом, Млфлов часто меняются. Таким образом, функции, предоставляемые через Машинное обучение Azure и интеграцию с Млфлов, следует рассматривать как предварительную версию, а не полностью поддерживать корпорацией Майкрософт.

На следующей схеме показано, что с помощью API развертывания Млфлов можно развернуть существующую модель Млфлов как веб-службу Машинное обучение Azure, несмотря на их платформы — PyTorch, Tensorflow, scikit-учиться, ONNX и т. д., а также управлять производственными моделями в рабочей области.

![ Развертывание моделей млфлов с помощью службы "машинное обучение Azure"](./media/how-to-use-mlflow/mlflow-diagram-deploy.png)

> [!TIP]
> Сведения в этом документе предназначены главным образом для специалистов по обработке и анализу данных и разработчиков, желающих развернуть свою модель Млфлов в конечной точке веб-службы Машинное обучение Azure. Если вы являетесь администратором, который заинтересован в наблюдении из Машинного обучения Azure за использованием ресурсов и событиями, такими как квоты, завершенные обучающие запуски или завершенные развертывания моделей, ознакомьтесь с разделом [Мониторинг Машинного обучения Azure](monitor-azure-machine-learning.md).

## <a name="prerequisites"></a>Предварительные требования

* [Настройте универсальный код ресурса (URI) отслеживания млфлов для подключения машинное обучение Azure](how-to-use-mlflow.md).
* Установите пакет `azureml-mlflow`. 
    * Этот пакет автоматически переносится в пакет `azureml-core` [SDK для машинное обучение Azure Python](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py), который обеспечивает подключение для млфлов к рабочей области.

## <a name="deploy-to-aci"></a>Развертывание в ACI

Чтобы развернуть модель Млфлов в веб-службе Машинное обучение Azure, необходимо настроить в модели [универсальный код ресурса (URI) отслеживания млфлов для подключения к машинное обучение Azure](how-to-use-mlflow.md). 

Настройте конфигурацию развертывания с помощью метода [deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.aciwebservice?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . Кроме того, можно добавить теги и описания, которые помогут отследить веб-службу.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Set the model path to the model folder created by your run
model_path = "model"

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Затем зарегистрируйте и разверните модель в одном шаге с помощью метода [развертывания](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) млфлов для машинное обучение Azure. 

```python
(webservice,model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='diabetes-model-1', 
                      deployment_config=aci_config, 
                      tags=None, mlflow_home=None, synchronous=True)

webservice.wait_for_deployment(show_output=True)
```

## <a name="deploy-to-aks"></a>Развертывание в AKS

Чтобы развернуть модель Млфлов в веб-службе Машинное обучение Azure, необходимо настроить в модели [универсальный код ресурса (URI) отслеживания млфлов для подключения к машинное обучение Azure](how-to-use-mlflow.md). 

Чтобы выполнить развертывание в AKS, сначала создайте кластер AKS. Создайте кластер AKS с помощью метода [ComputeTarget. Create ()](/python/api/azureml-core/azureml.core.computetarget?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-workspace--name--provisioning-configuration-) . Создание нового кластера может занять 20-25 минут.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (can also provide parameters to customize)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aks-mlflow'

# Create the cluster
aks_target = ComputeTarget.create(workspace=ws, 
                                  name=aks_name, 
                                  provisioning_configuration=prov_config)

aks_target.wait_for_completion(show_output = True)

print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```
Настройте конфигурацию развертывания с помощью метода [deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.aciwebservice?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . Кроме того, можно добавить теги и описания, которые помогут отследить веб-службу.

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True, compute_target_name='aks-mlflow')

```

Затем зарегистрируйте и разверните модель в одном шаге с помощью метода [развертывания](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) млфлов для машинное обучение Azure. 

```python

# Webservice creation using single command
from azureml.core.webservice import AksWebservice, Webservice

# set the model path 
model_path = "model"

(webservice, model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='my-aks', 
                      deployment_config=aks_config, 
                      tags=None, mlflow_home=None, synchronous=True)


webservice.wait_for_deployment()
```

Развертывание службы может занять несколько минут.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы не планируете использовать развернутую веб-службу, используйте `service.delete()` для ее удаления из записной книжки.  Дополнительные сведения см. в документации по [WebService. Delete ()](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truedelete--).

## <a name="example-notebooks"></a>Примеры записных книжек

В примере использования [MLflow с записными книжками Машинного обучения Azure](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow) демонстрируются и поясняются основные понятия, представленные в этой статье.

> [!NOTE]
> Основанный на сообществе репозиторий примеров с использованием млфлов можно найти по адресу https://github.com/Azure/azureml-examples .

## <a name="next-steps"></a>Дальнейшие действия

* [Управляйте своими моделями](concept-model-management-and-deployment.md).
* Отслеживайте рабочие модели для выявления [смещения данных](./how-to-enable-data-collection.md).
* [Track Azure Databricks выполняется с млфлов](how-to-use-mlflow-azure-databricks.md).
