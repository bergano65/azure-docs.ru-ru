---
title: Развертывание моделей в службе Kubernetes Azure
titleSuffix: Azure Machine Learning
description: Узнайте, как развернуть модели Машинное обучение Azure в качестве веб-службы с помощью службы Azure Kubernetes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 11/06/2019
ms.openlocfilehash: 054e0154d5b300df40840bb409879ff879a6a3b0
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123267"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Развертывание модели в кластере службы Azure Kubernetes
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Узнайте, как использовать Машинное обучение Azure для развертывания модели в качестве веб-службы в службе Azure Kubernetes Service (AKS). Служба Azure Kubernetes подходит для крупномасштабных производственных развертываний. Используйте службу Kubernetes Azure, если требуется одна или несколько из следующих возможностей:

- __Быстрое время отклика__.
- __Автоматическое масштабирование__ развернутой службы.
- Параметры __аппаратного ускорения__ , такие как многопроцессорные и программируемые массивы ШЛЮЗОВ (FPGA).

> [!IMPORTANT]
> Масштабирование кластера не предоставляется с помощью пакета SDK для Машинное обучение Azure. Дополнительные сведения о масштабировании узлов в кластере AKS см. [в разделе масштабирование числа узлов в кластере AKS](../../aks/scale-cluster.md).

При развертывании в службе Kubernetes Azure развертывание выполняется в кластере AKS, __подключенном к рабочей области__. Существует два способа подключения кластера AKS к рабочей области.

* Создайте кластер AKS с помощью пакета SDK Машинное обучение Azure, Машинное обучение CLI или [машинное обучение Azure Studio](https://ml.azure.com). Этот процесс автоматически подключает кластер к рабочей области.
* Подключите существующий кластер AKS к рабочей области Машинное обучение Azure. Кластер можно подключить с помощью Машинное обучение Azure пакета SDK, Машинное обучение интерфейса командной строки или Машинное обучение Azure Studio.

> [!IMPORTANT]
> Процесс создания или вложения является задачей, которая выполняется один раз. После подключения кластера AKS к рабочей области его можно использовать для развертываний. Вы можете отсоединить или удалить кластер AKS, если он больше не нужен. После детатчед или удаления вы больше не сможете развертывать в кластере.

## <a name="prerequisites"></a>предварительным требованиям

- Рабочая область машинного обучения Azure. Дополнительные сведения см. в статье [создание машинное обучение Azure рабочей области](how-to-manage-workspace.md).

- Модель машинного обучения, зарегистрированная в вашей рабочей области. Если у вас нет зарегистрированной модели, см. раздел [как и где развертывать модели](how-to-deploy-and-where.md).

- [Расширение Azure CLI для службы машинное обучение](reference-azure-machine-learning-cli.md), [машинное обучение Azure пакет SDK для Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)или [расширение машинное обучение Azure Visual Studio Code](how-to-vscode-tools.md).

- В фрагментах кода __Python__ в этой статье предполагается, что установлены следующие переменные:

    * `ws` — задайте свою рабочую область.
    * `model` — задайте свою зарегистрированную модель.
    * `inference_config` — Задайте конфигурацию вывода для модели.

    Дополнительные сведения об установке этих переменных см. в разделе [как и где развертываются модели](how-to-deploy-and-where.md).

- В фрагментах кода __CLI__ в этой статье предполагается, что вы создали `inferenceconfig.json` документ. Дополнительные сведения о создании этого документа см. в разделе [как и где развертываются модели](how-to-deploy-and-where.md).

## <a name="create-a-new-aks-cluster"></a>Создание нового кластера AKS

**Оценка времени**: приблизительно 20 минут.

Создание или присоединение кластера AKS выполняется один раз для рабочей области. Один кластер можно использовать для нескольких развертываний. При удалении кластера или группы ресурсов, содержащей этот кластер, необходимо создать новый кластер в следующий раз, когда потребуется выполнить развертывание. К рабочей области можно подключить несколько кластеров AKS.

> [!TIP]
> Если вы хотите защитить кластер AKS с помощью виртуальной сети Azure, сначала необходимо создать виртуальную сеть. Дополнительные сведения см. [в статье безопасное экспериментирование и вывод данных с помощью виртуальной сети Azure](how-to-enable-virtual-network.md#aksvnet).

Если вы хотите создать кластер AKS для __разработки__, __проверки__и __тестирования__ вместо рабочей среды, можно указать __назначение кластера__ для __тестирования разработки__.

> [!WARNING]
> Если задать `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`, созданный кластер не подходит для трафика уровня рабочей нагрузки и может увеличить время вывода. Кластеры для разработки и тестирования также не гарантируют отказоустойчивость. Для кластеров разработки и тестирования рекомендуется по крайней мере 2 виртуальных ЦП.

В следующих примерах показано, как создать новый кластер AKS с помощью пакета SDK и интерфейса командной строки.

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
> Для [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py), если вы выбираете пользовательские значения для `agent_count` и `vm_size`, а `cluster_purpose` не `DEV_TEST`, необходимо убедиться, что `agent_count` умноженные на `vm_size`, больше или равно 12 виртуальным ЦП. Например, если вы используете `vm_size` "Standard_D3_v2" с 4 виртуальными ЦП, то следует выбрать `agent_count` 3 или более.
>
> Пакет SDK для Машинное обучение Azure не обеспечивает поддержку масштабирования кластера AKS. Чтобы масштабировать узлы в кластере, используйте пользовательский интерфейс для кластера AKS в Машинное обучение Azure Studio. Можно изменить только число узлов, а не размер виртуальной машины кластера.

Дополнительные сведения о классах, методах и параметрах, используемых в этом примере, см. в следующих справочных документах:

* [Акскомпуте. Клустерпурпосе](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [Акскомпуте. provisioning_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget. Create](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget. wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#wait-for-completion-show-output-false-)

**Использование интерфейса командной строки**

```azurecli
az ml computetarget create aks -n myaks
```

Дополнительные сведения см. в разделе о [создании запроса AZ ML computetarget Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks) .

## <a name="attach-an-existing-aks-cluster"></a>Подключение существующего кластера AKS

**Оценка времени:** Приблизительно 5 минут.

Если у вас уже есть кластер AKS в подписке Azure и он ниже версии 1,14, его можно использовать для развертывания образа.

> [!TIP]
> Существующий кластер AKS может находиться в регионе Azure, отличном от Машинное обучение Azure рабочей области.
>
> Если вы хотите защитить кластер AKS с помощью виртуальной сети Azure, сначала необходимо создать виртуальную сеть. Дополнительные сведения см. [в статье безопасное экспериментирование и вывод данных с помощью виртуальной сети Azure](how-to-enable-virtual-network.md#aksvnet).

При присоединении кластера AKS к рабочей области можно определить, как будет использоваться кластер, задав параметр `cluster_purpose`.

Если не задать параметр `cluster_purpose` или задать `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`, то кластер должен иметь по крайней мере 12 виртуальных ЦП.

Если задать `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`, кластеру не требуется 12 виртуальных ЦП. Для разработки и тестирования рекомендуется по крайней мере 2 виртуальных ЦП. Однако кластер, настроенный для разработки и тестирования, не подходит для трафика уровня рабочей среды и может увеличить время вывода. Кластеры для разработки и тестирования также не гарантируют отказоустойчивость.

> [!WARNING]
> Не создавайте несколько одновременных вложений в одном кластере AKS из рабочей области. Например, можно подключить один кластер AKS к рабочей области, используя два разных имени. Каждое новое вложение приведет к нарушению предыдущих существующих вложений.
>
> Если требуется повторно подключить кластер AKS, например для изменения настроек SSL или другого кластера, необходимо сначала удалить существующее вложение с помощью [акскомпуте. Detach ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#detach--).

Дополнительные сведения о создании кластера AKS с помощью Azure CLI или портала см. в следующих статьях:

* [Создание кластера AKS (CLI)](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Создание кластера AKS (портал)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

В следующих примерах показано, как подключить существующий кластер AKS к рабочей области.

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

Дополнительные сведения о классах, методах и параметрах, используемых в этом примере, см. в следующих справочных документах:

* [Акскомпуте. attach_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [Акскомпуте. Клустерпурпосе](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [Акскомпуте. Attach](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#attach-workspace--name--attach-configuration-)

**Использование интерфейса командной строки**

Чтобы подключить существующий кластер с помощью интерфейса командной строки, необходимо получить идентификатор ресурса существующего кластера. Чтобы получить это значение, используйте следующую команду. Замените `myexistingcluster` именем кластера AKS. Замените `myresourcegroup` группой ресурсов, содержащей кластер:

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

Эта команда возвращает значение следующего вида:

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

Чтобы подключить существующий кластер к рабочей области, используйте следующую команду. Замените `aksresourceid` значением, возвращенным предыдущей командой. Замените `myresourcegroup` группой ресурсов, содержащей рабочую область. Замените `myworkspace` именем рабочей области.

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

Дополнительные сведения см. в справочнике по команде [AZ ML computetarget Attach AKS](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks) .

## <a name="deploy-to-aks"></a>Развертывание в AKS

Чтобы развернуть модель в службе Kubernetes Azure, создайте __конфигурацию развертывания__ , которая описывает требуемые ресурсы вычислений. Например, число ядер и память. Также необходима __Конфигурация вывода__, описывающая среду, необходимую для размещения модели и веб-службы. Дополнительные сведения о создании конфигурации вывода см. в разделе [как и где развертываются модели](how-to-deploy-and-where.md).

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

Дополнительные сведения о классах, методах и параметрах, используемых в этом примере, см. в следующих справочных документах:

* [акскомпуте](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute?view=azure-ml-py)
* [Аксвебсервице. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)
* [Модель. Развертывание](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [WebService. wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Использование интерфейса командной строки

Для развертывания с помощью интерфейса командной строки используйте следующую команду. Замените `myaks` именем целевого объекта вычислений AKS. Замените `mymodel:1` именем и версией зарегистрированной модели. Замените `myservice` именем, чтобы предоставить эту службу:

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-aks-deploy-config.md)]

Дополнительные сведения см. в справочнике по [развертыванию модели языка AZ ML](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) . 

### <a name="using-vs-code"></a>Использование VS Code

Сведения об использовании VS Code см. в разделе [развертывание в AKS с помощью расширения VS Code](how-to-vscode-tools.md#deploy-and-manage-models).

> [!IMPORTANT] 
> Для развертывания с помощью VS Code необходимо заранее создать кластер AKS или подключить его к рабочей области.

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>Развертывание моделей в AKS с помощью контролируемого развертывания (Предварительная версия)
Анализируйте и передвигайте версии модели в контролируемом виде с помощью конечных точек. Разверните до 6 версий за одну конечную точку и настройте% от оценки трафика для каждой развернутой версии. Вы можете включить Application Insights, чтобы просматривать операционные метрики конечных точек и развернутых версий.

### <a name="create-an-endpoint"></a>Создание конечной точки
Когда вы будете готовы к развертыванию моделей, создайте конечную точку оценки и разверните свою первую версию. На следующем шаге показано, как развернуть и создать конечную точку с помощью пакета SDK. Первое развертывание будет определено как версия по умолчанию, что означает, что неопределенный процент по всем версиям будет переходить к версии по умолчанию.  

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
                                                              enable_app_insights = true, 
                                                              tags = {'sckitlearn':'demo'},
                                                              decription = testing versions,
                                                              version_name = version_name,
                                                              traffic_percentile = 20)
 # deploy the model and endpoint
 endpoint = Model.deploy(ws, endpoint_name, [model], inference_config, endpoint_deployment_config, compute)
 ```

### <a name="update-and-add-versions-to-an-endpoint"></a>Обновление и добавление версий в конечную точку

Добавьте еще одну версию в конечную точку и настройте оценку трафика оценки до версии. Существует два типа версий: элемент управления и версия лечения. Для сравнения с одной версией элемента управления может использоваться несколько версий обработки. 

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

Обновите существующие версии или удалите их в конечной точке. Можно изменить тип по умолчанию для версии, тип элемента управления и значение интенсивности трафика. 
 
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

При развертывании в службе Kubernetes Azure проверка подлинности __на основе ключей__ включена по умолчанию. Также можно включить проверку подлинности __на основе маркеров__ . Для аутентификации на основе маркеров клиенты должны использовать учетную запись Azure Active Directory для запроса маркера проверки подлинности, который используется для выполнения запросов к развернутой службе.

Чтобы __Отключить__ проверку подлинности, задайте параметр `auth_enabled=False` при создании конфигурации развертывания. В следующем примере отключается проверка подлинности с помощью пакета SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

Сведения о проверке подлинности в клиентском приложении см. в разделе [использование машинное обучение Azureной модели, развернутой в качестве веб-службы](how-to-consume-web-service.md).

### <a name="authentication-with-keys"></a>Проверка подлинности с помощью ключей

Если включена проверка подлинности ключа, можно использовать метод `get_keys` для получения первичного и вторичного ключа проверки подлинности:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Если необходимо повторно создать ключ, используйте [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)

### <a name="authentication-with-tokens"></a>Аутентификация с помощью токенов

Чтобы включить проверку подлинности на маркере, задайте параметр `token_auth_enabled=True` при создании или обновлении развертывания. В следующем примере активируется проверка подлинности на основе маркеров с помощью пакета SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

Если включена проверка подлинности маркеров, можно использовать метод `get_token` для получения маркера JWT и срока действия маркера:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> После `refresh_by` времени маркера необходимо запросить новый токен.
>
> Корпорация Майкрософт настоятельно рекомендует создать рабочую область Машинное обучение Azure в том же регионе, что и кластер службы Azure Kubernetes. Для проверки подлинности с помощью маркера веб-служба выполнит вызов региона, в котором создана Рабочая область Машинное обучение Azure. Если регион рабочей области недоступен, вы не сможете получить маркер для веб-службы даже в том случае, если кластер находится в регионе, отличном от региона рабочей области. Это фактически приводит к недоступности проверки подлинности на основе маркеров, пока регион рабочей области не будет снова доступен. Кроме того, чем больше расстояние между регионом кластера и регионом рабочей области, тем дольше будет получена лексема.

## <a name="update-the-web-service"></a>Обновление веб-службы

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>Дополнительная информация

* [Безопасное экспериментирование и вывод в виртуальной сети](how-to-enable-virtual-network.md)
* [Развертывание модели с помощью пользовательского образа DOCKER](how-to-deploy-custom-docker-image.md)
* [Устранение неполадок развертывания](how-to-troubleshoot-deployment.md)
* [Защита веб-служб Машинного обучения Azure с помощью SSL](how-to-secure-web-service.md)
* [Использование модели Машинного обучения Azure, развернутой в качестве веб-службы](how-to-consume-web-service.md)
* [Мониторинг моделей машинного обучения в Azure с помощью Application Insights](how-to-enable-app-insights.md)
* [Сбор данных для моделей в рабочей среде](how-to-enable-data-collection.md)
