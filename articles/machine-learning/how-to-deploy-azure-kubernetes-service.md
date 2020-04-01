---
title: Как развернуть модели в службе Azure Kubernetes
titleSuffix: Azure Machine Learning
description: Узнайте, как развернуть модели машинного обучения Azure в качестве веб-сервиса с помощью службы Azure Kubernetes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 01/16/2020
ms.openlocfilehash: 792964f28ddb3fcb10932b8de9499a9c7027960f
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475382"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Развертывание модели в кластере службы Azure Kubernetes
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Узнайте, как использовать Машинное обучение Azure для развертывания модели в качестве веб-сервиса в службе Azure Kubernetes (AKS). Служба Azure Kubernetes хороша для крупномасштабных производственных развертываний. Используйте службу Azure Kubernetes, если вам нужна одна или несколько следующих возможностей:

- __Быстрое время отклика__.
- __Автоматическое размещение__ развернутой службы.
- __Варианты ускорения оборудования,__ такие как GPU и полевые наборы ворот (FPGA).

> [!IMPORTANT]
> Масштабирование кластеров не обеспечивается через SDK по изучению машин Azure. Для получения дополнительной информации о масштабировании узлов в кластере AKS [см. Масштабирование количества узлов в кластере AKS.](../aks/scale-cluster.md)

При развертывании в службе Azure Kubernetes развертывается кластер AKS, __подключенный к рабочему пространству.__ Существует два способа подключения кластера AKS к рабочему пространству:

* Создайте кластер AKS с помощью SDK-машинного обучения Azure, студии машинного обучения или [студии машинного обучения Azure.](https://ml.azure.com) Этот процесс автоматически соединяет кластер с рабочим пространством.
* Прикрепите существующий кластер AKS к рабочему пространству Azure Machine Learning. Кластер можно прикрепить с помощью SDK-машинного обучения Azure, CLI машинного обучения или студии машинного обучения Azure.

> [!IMPORTANT]
> Процесс создания или вложения — это задача одной задачи. После подключения кластера AKS к рабочему пространству его можно использовать для развертывания. Вы можете отсоединить или удалить кластер AKS, если он вам больше не нужен. После того, как вы отсоединитесь или удалены, вы больше не сможете развертывать в кластере.

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

## <a name="create-a-new-aks-cluster"></a>Создание нового кластера AKS

**Оценка времени**: Приблизительно 20 минут.

Создание или присоединение кластера AKS — это одноразовый процесс для рабочего пространства. Один кластер можно использовать для нескольких развертываний. При удалении кластера или группы ресурсов, содержащей его, необходимо создать новый кластер при следующем развертывании. К рабочему пространству можно прикрепить несколько кластеров AKS.

> [!TIP]
> Если вы хотите обезопасить кластер AKS с помощью виртуальной сети Azure, сначала необходимо создать виртуальную сеть. Для получения дополнительной информации [см. Безопасные эксперименты и выводы с Azure Virtual Network](how-to-enable-virtual-network.md#aksvnet).

Если требуется создать кластер AKS для __разработки,__ __проверки__и __тестирования__ вместо производства, можно указать __цель кластера__ для __тестирования.__

> [!WARNING]
> При установке `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`созданного кластера не подходит для трафика производственного уровня и может увеличиться время выводов. Кластеры Dev/test также не гарантируют отказоустойчивости. Мы рекомендуем по крайней мере 2 виртуальных процессора для кластеров dev/test.

Следующие примеры демонстрируют, как создать новый кластер AKS с использованием SDK и CLI:

**Использование пакета SDK**

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

> [!IMPORTANT]
> Для [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py), если вы выбираете пользовательские значения `agent_count` для и , `agent_count` и `vm_size` `cluster_purpose` `vm_size` нет, `DEV_TEST`то вы должны убедиться, умножается на больше, чем или равна 12 виртуальных процессоров. Например, если вы `vm_size` используете "Standard_D3_v2", который имеет 4 виртуальных `agent_count` процессора, то вы должны выбрать 3 или больше.
>
> SDK Azure Machine Learning SDK не обеспечивает поддержку масштабирования кластера AKS. Для масштабирования узлов в кластере используйте uI для кластера AKS в студии машинного обучения Azure. Можно изменить только количество узлов, а не размер VM кластера.

Для получения дополнительной информации о классах, методах и параметрах, используемых в данном примере, см.

* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute.provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget.create](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget.wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#wait-for-completion-show-output-false-)

**Использование интерфейса командной строки**

```azurecli
az ml computetarget create aks -n myaks
```

Для получения дополнительной [az ml computetarget create aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks) информации см.

## <a name="attach-an-existing-aks-cluster"></a>Прикрепите существующий кластер AKS

**Оценка времени:** Приблизительно 5 минут.

Если в подписке Azure уже есть кластер AKS, а версия 1.17 или ниже, его можно использовать для развертывания изображения.

> [!TIP]
> Существующий кластер AKS может находиться в регионе Azure, кроме рабочего пространства машинного обучения Azure.
>
> Если вы хотите обезопасить кластер AKS с помощью виртуальной сети Azure, сначала необходимо создать виртуальную сеть. Для получения дополнительной информации [см. Безопасные эксперименты и выводы с Azure Virtual Network](how-to-enable-virtual-network.md#aksvnet).

При подключении кластера AKS к рабочему пространству можно определить, `cluster_purpose` как использовать кластер, установив параметр.

Если вы не `cluster_purpose` устанавливаете параметр или не устанавливаете, `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`то кластер должен иметь по крайней мере 12 виртуальных процессоров.

Если вы `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`установите, то кластер не должен иметь 12 виртуальных процессоров. Мы рекомендуем по крайней мере 2 виртуальных процессора для dev/test. Однако кластер, настроенный для dev/test, не подходит для трафика производственного уровня и может увеличить время выводов. Кластеры Dev/test также не гарантируют отказоустойчивости.

> [!WARNING]
> Не создавайте несколько одновременных вложений в один и тот же кластер AKS из рабочего пространства. Например, присоединение одного кластера AKS к рабочему пространству с использованием двух разных имен. Каждое новое вложение нарушит предыдущее существующее вложение (ы).
>
> Если требуется повторно прикрепить кластер AKS, например, для изменения TLS или другой настройки конфигурации кластера, необходимо сначала удалить существующее вложение с помощью [AksCompute.detach()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#detach--)

Для получения дополнительной информации о создании кластера AKS с помощью Azure CLI или портала смотрите следующие статьи:

* [Создание кластера AKS (CLI)](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Создание кластера AKS (портал)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

Следующие примеры демонстрируют, как прикрепить существующий кластер AKS к рабочему пространству:

**Использование пакета SDK**

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'myexistingcluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'myaks', attach_config)
```

Для получения дополнительной информации о классах, методах и параметрах, используемых в данном примере, см.

* [AksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute.attach](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#attach-workspace--name--attach-configuration-)

**Использование интерфейса командной строки**

Чтобы прикрепить существующий кластер с помощью CLI, необходимо получить идентификатор ресурсов существующего кластера. Чтобы получить это значение, используйте следующую команду. Замените `myexistingcluster` название кластера AKS. Заменить `myresourcegroup` группу ресурсов, содержащую кластер:

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

Эта команда возвращает значение следующего вида:

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

Чтобы прикрепить существующий кластер к рабочему пространству, используйте следующую команду. Заменить `aksresourceid` значение, возвращенное предыдущей командой. Замените `myresourcegroup` группу ресурсов, содержащую рабочее пространство. Замените `myworkspace` имя рабочего пространства.

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

Для получения дополнительной [az ml computetarget attach aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks) информации см.

## <a name="deploy-to-aks"></a>Развертывание в AKS

Чтобы развернуть модель в службе Azure Kubernetes, создайте __конфигурацию развертывания,__ описывающая необходимые ресурсы вычислений. Например, количество ядер и память. Вам также нужна __конфигурация выводов,__ которая описывает среду, необходимую для размещения модели и веб-службы. Для получения дополнительной информации о создании конфигурации выводов см. [Как и где развертывать модели.](how-to-deploy-and-where.md)

### <a name="using-the-sdk"></a>Использование пакета SDK

```python
from azureml.core.webservice import AksWebservice, Webservice
from azureml.core.model import Model

aks_target = AksCompute(ws,"myaks")
# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.
deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config, aks_target)
service.wait_for_deployment(show_output = True)
print(service.state)
print(service.get_logs())
```

Для получения дополнительной информации о классах, методах и параметрах, используемых в данном примере, см.

* [АксКомпьютер](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute?view=azure-ml-py)
* [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)
* [Модель.развертывание](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Использование интерфейса командной строки

Для развертывания с помощью CLI используйте следующую команду. Замените `myaks` имя цели вычислений AKS. Заменить `mymodel:1` имя и версию зарегистрированной модели. Замените `myservice` имя, чтобы дать эту услугу:

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

Для получения дополнительной [az ml model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) информации см.

### <a name="using-vs-code"></a>Использование VS Code

Для получения информации об [deploy to AKS via the VS Code extension](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model)использовании VS Code см.

> [!IMPORTANT]
> Развертывание через VS Code требует заблаговременного создания или присоединения кластера AKS к рабочему пространству.

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>Развертывание моделей на AKS с помощью контролируемого развертывания (предварительный просмотр)
Анализируйте и продвигайте модельные версии контролируемым образом с помощью конечных точек. Развертывание до 6 версий за одной конечной точкой и настроить % трафика скоринга на каждую развернутую версию. Можно включить информацию о приложениях для просмотра операционных показателей конечных точек и развернутых версий.

### <a name="create-an-endpoint"></a>Создание конечной точки
Как только вы будете готовы развернуть свои модели, создайте конечную точку скоринга и разместите первую версию. На приведенном ниже шаге показано, как развернуть и создать конечную точку с помощью SDK. Первое развертывание будет определено как версия по умолчанию, что означает, что неопределенный процентиль трафика во всех версиях будет переходить в версию по умолчанию.  

```python
import azureml.core,
from azureml.core.webservice import AksEndpoint
from azureml.core.compute import AksCompute
from azureml.core.compute import ComputeTarget
# select a created compute
compute = ComputeTarget(ws, 'myaks')
namespace_name= endpointnamespace
# define the endpoint and version name
endpoint_name = "mynewendpoint",
version_name= "versiona",
# create the deployment config and define the scoring traffic percentile for the first deployment
endpoint_deployment_config = AksEndpoint.deploy_configuration(cpu_cores = 0.1, memory_gb = 0.2,
                                                              enable_app_insights = True,
                                                              tags = {'sckitlearn':'demo'},
                                                              description = "testing versions",
                                                              version_name = version_name,
                                                              traffic_percentile = 20)
 # deploy the model and endpoint
 endpoint = Model.deploy(ws, endpoint_name, [model], inference_config, endpoint_deployment_config, compute)
 ```

### <a name="update-and-add-versions-to-an-endpoint"></a>Обновление и добавление версий в конечную точку

Добавьте еще одну версию в конечную точку и настройте баллингный процентиль трафика, идущий в версию. Существует два типа версий: контроль и вариант лечения. Там может быть несколько версий лечения, чтобы помочь сравнить с одной версии управления.

 ```python
from azureml.core.webservice import AksEndpoint

# add another model deployment to the same endpoint as above
version_name_add = "versionb"
endpoint.create_version(version_name = version_name_add,
                        inference_config=inference_config,
                        models=[model],
                        tags = {'modelVersion':'b'},
                        description = "my second version",
                        traffic_percentile = 10)
```

Обновление существующих версий или удалить их в конечную точку. Можно изменить тип по умолчанию, тип управления и процентиль трафика.

 ```python
from azureml.core.webservice import AksEndpoint

# update the version's scoring traffic percentage and if it is a default or control type
endpoint.update_version(version_name=endpoint.versions["versionb"].name,
                        description="my second version update",
                        traffic_percentile=40,
                        is_default=True,
                        is_control_version_type=True)

# delete a version in an endpoint
endpoint.delete_version(version_name="versionb")

```


## <a name="web-service-authentication"></a>Проверка подлинности веб-службы

При развертывании в службе Azure Kubernetes аутентификация __на основе ключей__ включена по умолчанию. Вы также можете включить проверку подлинности __на основе токенов.__ Аутентификация на основе токенов требует от клиентов использовать учетную запись Azure Active Directory для запроса маркера аутентификации, который используется для запросов в развернутую службу.

Чтобы __отключить__ аутентификацию, установите `auth_enabled=False` параметр при создании конфигурации развертывания. Следующий пример отравливает аутентификацию с помощью SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

Для получения информации о проверке подлинности из клиентского приложения см. [Consume an Azure Machine Learning model deployed as a web service](how-to-consume-web-service.md)

### <a name="authentication-with-keys"></a>Аутентификация с ключами

Если ключ проверки подлинности включен, вы можете использовать `get_keys` метод для получения первичного и вторичного ключа проверки подлинности:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Если вам нужно регенерировать ключ, используйте[`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)

### <a name="authentication-with-tokens"></a>Аутентификация с токенами

Чтобы включить проверку подлинности `token_auth_enabled=True` маркеров, установите параметр при создании или обновлении развертывания. Следующий пример позволяет маркер аутентификации с помощью SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

Если аутентификация маркеров включена, можно использовать `get_token` метод для извлечения токена JWT и срока действия этого маркера:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> После `refresh_by` времени токена необходимо запросить новый токен.
>
> Корпорация Майкрософт настоятельно рекомендует создать рабочее пространство Для машин Azure Machine Learning в том же регионе, что и кластер службы Azure Kubernetes. Чтобы проверить подлинность с помощью маркера, веб-служба сделает вызов в область, в которой создается рабочее пространство Azure Machine Learning. Если область рабочего пространства недоступна, вы не сможете получить токен для веб-сервиса даже, если ваш кластер находится в другом регионе, чем ваше рабочее пространство. Это фактически приводит к тому, что аутентификация на основе токенов будет недоступна до тех пор, пока область рабочего пространства не будет снова доступна. Кроме того, чем больше расстояние между областью кластера и областью рабочего пространства, тем больше времени потребуется для получения маркера.

## <a name="update-the-web-service"></a>Обновление веб-службы

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>Следующие шаги

* [Безопасные эксперименты и выводы в виртуальной сети](how-to-enable-virtual-network.md)
* [Как развернуть модель с помощью пользовательского изображения Docker](how-to-deploy-custom-docker-image.md)
* [Устранение неполадок развертывания](how-to-troubleshoot-deployment.md)
* [Используйте TLS для обеспечения безопасности веб-службы через Машинное обучение Azure](how-to-secure-web-service.md)
* [Использование модели Машинного обучения Azure, развернутой в качестве веб-службы](how-to-consume-web-service.md)
* [Мониторинг моделей машинного обучения Azure с помощью приложений Insights](how-to-enable-app-insights.md)
* [Сбор данных для моделей в рабочей среде](how-to-enable-data-collection.md)
