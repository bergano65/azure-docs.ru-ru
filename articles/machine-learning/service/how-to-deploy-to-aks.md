---
title: Развертывание моделей в Kubernetes из службы машинного обучения Azure | Документы Майкрософт
description: Узнайте, как развернуть модель из службы машинного обучения Azure в службе Azure Kubernetes. Модель развертывается как веб-служба. Служба Azure Kubernetes подходит для крупномасштабных рабочих нагрузок.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: raymondl
author: raymondlaghaeian
manager: cgronlun
ms.reviewer: larryfr
ms.date: 09/24/2018
ms.openlocfilehash: d9328b293d38114d319d79e38b91b1b67e410d94
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/29/2018
ms.locfileid: "52581845"
---
# <a name="how-to-deploy-models-from-azure-machine-learning-service-to-azure-kubernetes-service"></a>Развертывание моделей из службы машинного обучения Azure в службе Azure Kubernetes

Для крупномасштабных рабочих сценариев можно развернуть модель в службе Azure Kubernetes (AKS). Машинное обучение Azure может использовать существующий кластер AKS или новый кластер, созданный в процессе развертывания. Модель развертывается в ASK как веб-служба.

Развертывание в AKS обеспечивает автоматическое масштабирование, ведение журналов, сбор данных модели и короткое время отклика веб-службы.

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure. Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://aka.ms/AMLfree), прежде чем начать работу.

- Должны быть установлены: рабочая область службы "Машинное обучение Azure", локальный каталог со сценариями и пакет SDK Машинного обучения Azure для Python. Дополнительные сведения о получении этих необходимых компонентов см. в документе [Настройка среды разработки](how-to-configure-environment.md).

- Обученная модель машинного обучения. Если у вас ее нет, обратитесь к [руководству по обучению модели классификации изображений](tutorial-train-models-with-aml.md).

## <a name="initialize-the-workspace"></a>Инициализация рабочей области

Чтобы инициализировать рабочую область, загрузите файл `config.json`, содержащий сведения о вашей рабочей области.

```python
from azureml.coreazureml import Workspace

# Load existing workspace from the config file info.
ws = Workspace.from_config()
```

## <a name="register-the-model"></a>Регистрация модели

Чтобы зарегистрировать существующую модель, укажите путь к этой модели, ее описание и теги.

```python
from azureml.core.model import Model

model = Model.register(model_path = "model.pkl", # this points to a local file
                        model_name = "best_model", # this is the name the model is registered as
                        tags = {"data": "diabetes", "type": "regression"},
                        description = "Ridge regression model to predict diabetes",
                        workspace = ws)
```

## <a name="create-a-docker-image"></a>Создайте образ Docker.

Служба Azure Kubernetes использует образы Docker. Для создания образа выполните следующие действия:

1. Чтобы настроить образ, необходимо создать сценарий оценки и файл среды. Пример создания сценария и файла среды см. в следующих разделах примера классификации изображений:

    * [Создание сценария оценки (score.py)](tutorial-deploy-models-with-aml.md#create-scoring-script)

        > [!IMPORTANT]
        > Скрипт оценки получает данные, передаваемые из клиентов, и передает их в модель для оценки. Задокументируйте структуру данных, планируемую для скрипта и модели. Такой документ упрощает работу при создании клиента для веб-службы.

    * [Создание файла среды (myenv.yml)](tutorial-deploy-models-with-aml.md#create-environment-file) 

   В следующем примере эти файлы используются для настройки образа:

    ```python
    from azureml.core.image import ContainerImage

    # Image configuration
    image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                        runtime = "python",
                                                        conda_file = "myenv.yml",
                                                        description = "Image with ridge regression model",
                                                        tags = {"data": "diabetes", "type": "regression"}
                                                        )
    ```

1. Создайте образ с помощью конфигурации модели и образа. Выполнение этой операции займет около 5 минут:

    ```python
    image = ContainerImage.create(name = "myimage1",
                                    # this is the model object
                                    models = [model],
                                    image_config = image_config,
                                    workspace = ws)

    # Wait for the create process to complete
    image.wait_for_creation(show_output = True)
    ```

## <a name="create-the-aks-cluster"></a>Создание кластера AKS

В следующем фрагменте кода показано, как создать кластер AKS. Эта процедура занимает около 20 минут:

> [!IMPORTANT]
> Кластер AKS для рабочей области нужно создать только один раз. Созданный кластер можно использовать для различных развертываний. Если вы удаляете кластер или группу ресурсов, в которую он входит, во время следующего развертывания нужно будет создать новый кластер.


```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aml-aks-1' 
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws, 
                                    name = aks_name, 
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

### <a name="attach-existing-aks-cluster-optional"></a>Присоединение существующего кластера AKS (необязательно)

Если в вашей подписке Azure уже есть кластер AKS, его можно использовать для развертывания вашего образа. В следующем фрагменте кода показано, как присоединить кластер к рабочей области. 

> [!IMPORTANT]
> Поддерживается только AKS версии 1.11.3.

```python
# Get the resource id from https://porta..azure.com -> Find your resource group -> click on the Kubernetes service -> Properties
resource_id = '/subscriptions/<your subscription id>/resourcegroups/<your resource group>/providers/Microsoft.ContainerService/managedClusters/<your aks service name>'

# Set to the name of the cluster
cluster_name='my-existing-aks' 

# Attatch the cluster to your workgroup
aks_target = AksCompute.attach(workspace=ws, name=cluster_name, resource_id=resource_id)

# Wait for the operation to complete
aks_target.wait_for_completion(True)
```

## <a name="deploy-your-web-service"></a>Развертывание веб-службы

В следующем фрагменте кода показано, как развернуть образ в кластер AKS:

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set configuration and service name
aks_config = AksWebservice.deploy_configuration()
aks_service_name ='aks-service-1'
# Deploy from image
aks_service = Webservice.deploy_from_image(workspace = ws, 
                                            name = aks_service_name,
                                            image = image,
                                            deployment_config = aks_config,
                                            deployment_target = aks_target)
# Wait for the deployment to complete
aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

> [!TIP]
> Если во время развертывания возникают ошибки, используйте `aks_service.get_logs()` для просмотра журналов службы AKS. Информация в этих журналах может указывать на причину ошибки.

## <a name="test-the-web-service"></a>Тестирование веб-службы

Для тестирования веб-службы используйте `aks_service.run()`. В следующем фрагменте кода показано, как передать данные службе и отобразить прогноз:

```python
import json

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

prediction = aks_service.run(input_data = test_sample)
print(prediction)
```

## <a name="cleanup"></a>Очистка

Чтобы удалить службу, образ и модель, используйте следующий фрагмент кода:

```python
aks_service.delete()
image.delete()
model.delete()
```

## <a name="next-steps"></a>Дополнительная информация

Узнайте, как [использовать модель машинного обучения, развернутую в виде веб-службы](how-to-consume-web-service.md).