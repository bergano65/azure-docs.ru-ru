---
title: Развертывание моделей ML в службе Kubernetes
titleSuffix: Azure Machine Learning
description: Узнайте, как развернуть модели Машинное обучение Azure в качестве веб-службы с помощью службы Azure Kubernetes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/23/2020
ms.openlocfilehash: 03477fa46aaec04c0563ed38b085605dce5b87a1
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/22/2020
ms.locfileid: "88751741"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Развертывание модели в кластере службы Azure Kubernetes
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Узнайте, как использовать Машинное обучение Azure для развертывания модели в качестве веб-службы в службе Azure Kubernetes Service (AKS). Служба Azure Kubernetes подходит для крупномасштабных производственных развертываний. Используйте службу Kubernetes Azure, если требуется одна или несколько из следующих возможностей:

- __Быстрое время отклика__.
- __Автоматическое масштабирование__ развернутой службы.
- Параметры __аппаратного ускорения__ , такие как многопроцессорные и программируемые массивы ШЛЮЗОВ (FPGA).

> [!IMPORTANT]
> Масштабирование кластера не предоставляется с помощью пакета SDK для Машинное обучение Azure. Дополнительные сведения о масштабировании узлов в кластере AKS см. в разделе. 
- [Ручное масштабирование числа узлов в кластере AKS](../aks/scale-cluster.md)
- [Настройка автомасштабирования кластера в AKS](../aks/cluster-autoscaler.md)

При развертывании в службе Kubernetes Azure развертывание выполняется в кластере AKS, __подключенном к рабочей области__. Существует два способа подключения кластера AKS к рабочей области.

* Создайте кластер AKS с помощью пакета SDK Машинное обучение Azure, Машинное обучение CLI или [машинное обучение Azure Studio](https://ml.azure.com). Этот процесс автоматически подключает кластер к рабочей области.
* Подключите существующий кластер AKS к рабочей области Машинное обучение Azure. Кластер можно подключить с помощью Машинное обучение Azure пакета SDK, Машинное обучение интерфейса командной строки или Машинное обучение Azure Studio.

Кластер AKS и Рабочая область AML могут находиться в разных группах ресурсов.

> [!IMPORTANT]
> Процесс создания или вложения является задачей, которая выполняется один раз. После подключения кластера AKS к рабочей области его можно использовать для развертываний. Вы можете отсоединить или удалить кластер AKS, если он больше не нужен. После отсоединения или удаления развертывание в кластере будет невозможно.

> [!IMPORTANT]
> Рекомендуется выполнять отладку локально перед развертыванием в веб-службе. Дополнительные сведения см. в разделе [Локальная отладка](https://docs.microsoft.com/azure/machine-learning/how-to-troubleshoot-deployment#debug-locally) .
>
> Вы также можете ознакомиться со статьей о Машинном обучении Azure: [Развертывание в локальный Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local)

## <a name="prerequisites"></a>Предварительные требования

- Рабочая область машинного обучения Azure. Дополнительные сведения см. в статье [создание машинное обучение Azure рабочей области](how-to-manage-workspace.md).

- Модель машинного обучения, зарегистрированная в вашей рабочей области. Если у вас нет зарегистрированной модели, см. раздел [как и где развертывать модели](how-to-deploy-and-where.md).

- [Расширение Azure CLI для службы машинное обучение](reference-azure-machine-learning-cli.md), [машинное обучение Azure пакет SDK для Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)или [расширение машинное обучение Azure Visual Studio Code](tutorial-setup-vscode-extension.md).

- В фрагментах кода __Python__ в этой статье предполагается, что установлены следующие переменные:

    * `ws` — Укажите рабочую область.
    * `model` — Укажите вашу зарегистрированную модель.
    * `inference_config` — Задайте в качестве конфигурации вывода для модели.

    Дополнительные сведения об установке этих переменных см. в разделе [как и где развертываются модели](how-to-deploy-and-where.md).

- В фрагментах кода __CLI__ в этой статье предполагается, что вы создали `inferenceconfig.json` документ. Дополнительные сведения о создании этого документа см. в разделе [как и где развертываются модели](how-to-deploy-and-where.md).

- Если вам требуется Load Balancer (цен. категория "Стандартный") (SLB), развернутый в кластере вместо базового Load Balancer (БЛБ), создайте кластер на портале AKS, CLI или SDK, а затем подключите его к рабочей области AML.

- Если у вас есть политика Azure, которая ограничит создание общедоступного IP-адреса, создание кластера AKS завершится сбоем. Для AKS требуется общедоступный IP-адрес для [исходящего трафика](https://docs.microsoft.com/azure/aks/limit-egress-traffic). В этой статье также приводятся рекомендации по блокированию исходящего трафика из кластера через общедоступный IP-адрес, за исключением нескольких полных доменных имен. Существует два способа включения общедоступного IP-адреса:
  - Кластер может использовать общедоступный IP-адрес, созданный по умолчанию с помощью БЛБ или SLB, или
  - Кластер можно создать без общедоступного IP-адреса, а затем настроить общедоступный IP-адрес брандмауэром с определяемым пользователем маршрутом, как описано [здесь](https://docs.microsoft.com/azure/aks/egress-outboundtype) . 
  
  Плоскость управления AML не взаимодействуют с этим общедоступным IP-адресом. Он обращается к плоскости управления AKS для развертываний. 

- Если вы подключаете кластер AKS с [разрешенным диапазоном IP-адресов для доступа к серверу API](https://docs.microsoft.com/azure/aks/api-server-authorized-ip-ranges), включите диапазоны IP-адресов плоскости AML управления для кластера AKS. Плоскость управления AML развертывается в парных регионах и развертывает в кластере AKS. Без доступа к серверу API не удается развернуть модули. Используйте [диапазоны IP-адресов](https://www.microsoft.com/en-us/download/confirmation.aspx?id=56519) для [парных регионов]( https://docs.microsoft.com/azure/best-practices-availability-paired-regions) при включении диапазонов IP-адресов в кластере AKS.


  Диапазоны IP-адресов аусроизед работают только с Load Balancer (цен. категория "Стандартный").
 
 - Имя вычислений должно быть уникальным в пределах рабочей области
   - Имя является обязательным и должно иметь длину от 3 до 24 символов.
   - Допустимыми символами являются буквы верхнего и нижнего регистра, цифры и символ-знак.
   - Имя должно начинаться с буквы
   - Имя должно быть уникальным среди всех существующих вычислений в регионе Azure. Вы увидите оповещение, если выбранное имя не является уникальным
   
 - Если вы хотите развернуть модели на узлах GPU или узлах FPGA (или на любом конкретном номере SKU), необходимо создать кластер с конкретным номером SKU. Создание пула вторичных узлов в существующем кластере и развертывание моделей в пуле вторичных узлов не поддерживаются.

## <a name="create-a-new-aks-cluster"></a>Создание нового кластера AKS

**Оценка времени**: приблизительно 10 минут.

Создание или присоединение кластера AKS выполняется один раз для рабочей области. Один кластер можно использовать для нескольких развертываний. При удалении кластера или группы ресурсов, содержащей этот кластер, необходимо создать новый кластер в следующий раз, когда потребуется выполнить развертывание. К рабочей области можно подключить несколько кластеров AKS.
 
Машинное обучение Azure теперь поддерживает использование службы Azure Kubernetes, для которой включена частная ссылка.
Чтобы создать частный кластер AKS, следуйте инструкциям [здесь](https://docs.microsoft.com/azure/aks/private-clusters)

> [!TIP]
> Если вы хотите защитить кластер AKS с помощью виртуальной сети Azure, сначала необходимо создать виртуальную сеть. Дополнительные сведения см. [в статье безопасное экспериментирование и вывод данных с помощью виртуальной сети Azure](how-to-enable-virtual-network.md#aksvnet).

Если вы хотите создать кластер AKS для __разработки__, __проверки__и __тестирования__ вместо рабочей среды, можно указать __назначение кластера__ для __тестирования разработки__.

> [!WARNING]
> Если задать `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` , созданный кластер не подходит для трафика уровня рабочей среды и может увеличить время вывода. Кластеры для разработки и тестирования также не гарантируют отказоустойчивость. Для кластеров разработки и тестирования рекомендуется по крайней мере 2 виртуальных ЦП.

В следующих примерах показано, как создать новый кластер AKS с помощью пакета SDK и интерфейса командной строки.

**Использование пакета SDK**

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()
# Example configuration to use an existing virtual network
# prov_config.vnet_name = "mynetwork"
# prov_config.vnet_resourcegroup_name = "mygroup"
# prov_config.subnet_name = "default"
# prov_config.service_cidr = "10.0.0.0/16"
# prov_config.dns_service_ip = "10.0.0.10"
# prov_config.docker_bridge_cidr = "172.17.0.1/16"

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

> [!IMPORTANT]
> Для [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py) , если вы выбираете пользовательские значения для `agent_count` и, а не, то необходимо убедиться, `vm_size` `cluster_purpose` `DEV_TEST` что `agent_count` умножение на `vm_size` больше или равно 12 виртуальным ЦП. Например, если вы используете `vm_size` "Standard_D3_v2" с 4 виртуальными процессорами, то следует выбрать не `agent_count` более 3.
>
> Пакет SDK для Машинное обучение Azure не обеспечивает поддержку масштабирования кластера AKS. Чтобы масштабировать узлы в кластере, используйте пользовательский интерфейс для кластера AKS в Машинное обучение Azure Studio. Можно изменить только число узлов, а не размер виртуальной машины кластера.

Дополнительные сведения о классах, методах и параметрах, используемых в этом примере, см. в следующих справочных документах:

* [Акскомпуте. Клустерпурпосе](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [Акскомпуте. provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget. Create](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget. wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#wait-for-completion-show-output-false-)

**Использование интерфейса командной строки**

```azurecli
az ml computetarget create aks -n myaks
```

Дополнительные сведения см. в справке по команде [AZ ML computetarget Create AKS](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks) .

## <a name="attach-an-existing-aks-cluster"></a>Подключение существующего кластера AKS

**Оценка времени:** Приблизительно 5 минут.

Если у вас уже есть кластер AKS в подписке Azure и он имеет версию 1,17 или более раннюю, его можно использовать для развертывания образа.

> [!TIP]
> Существующий кластер AKS может находиться в регионе Azure, отличном от Машинное обучение Azure рабочей области.
>
> Если вы хотите защитить кластер AKS с помощью виртуальной сети Azure, сначала необходимо создать виртуальную сеть. Дополнительные сведения см. [в статье безопасное экспериментирование и вывод данных с помощью виртуальной сети Azure](how-to-enable-virtual-network.md#aksvnet).

При присоединении кластера AKS к рабочей области можно определить, как будет использоваться кластер, задав `cluster_purpose` параметр.

Если не задать `cluster_purpose` параметр или задать `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD` , кластер должен иметь по крайней мере 12 виртуальных ЦП.

Если задано значение `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` , кластеру не требуется 12 виртуальных ЦП. Для разработки и тестирования рекомендуется по крайней мере 2 виртуальных ЦП. Однако кластер, настроенный для разработки и тестирования, не подходит для трафика уровня рабочей среды и может увеличить время вывода. Кластеры для разработки и тестирования также не гарантируют отказоустойчивость.

> [!WARNING]
> Не создавайте несколько одновременных вложений в одном кластере AKS из рабочей области. Например, можно подключить один кластер AKS к рабочей области, используя два разных имени. Каждое новое вложение приведет к нарушению предыдущих существующих вложений.
>
> Если требуется повторно подключить кластер AKS, например для изменения настроек TLS или другого кластера, необходимо сначала удалить существующее вложение с помощью [акскомпуте. Detach ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#detach--).

Дополнительные сведения о создании кластера AKS с помощью Azure CLI или портала см. в следующих статьях:

* [Создание кластера AKS с помощью CLI](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Создание кластера AKS (портал)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)
* [Создание кластера AKS (шаблон ARM в шаблонах быстрого запуска Azure)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aks-azml-targetcompute)

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

# Wait for the attach process to complete
aks_target.wait_for_completion(show_output = True)
```

Дополнительные сведения о классах, методах и параметрах, используемых в этом примере, см. в следующих справочных документах:

* [Акскомпуте. attach_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [Акскомпуте. Клустерпурпосе](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [Акскомпуте. Attach](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#attach-workspace--name--attach-configuration-)

**Использование интерфейса командной строки**

Чтобы подключить существующий кластер с помощью интерфейса командной строки, необходимо получить идентификатор ресурса существующего кластера. Чтобы получить это значение, используйте следующую команду. Замените `myexistingcluster` именем кластера AKS. Замените на `myresourcegroup` группу ресурсов, содержащую кластер:

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

Эта команда возвращает значение следующего вида:

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

Чтобы подключить существующий кластер к рабочей области, используйте следующую команду. Замените на `aksresourceid` значение, возвращенное предыдущей командой. Замените `myresourcegroup` группой ресурсов, содержащей рабочую область. Замените `myworkspace` именем рабочей области.

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

Для развертывания с помощью интерфейса командной строки используйте следующую команду. Замените на `myaks` имя целевого объекта вычислений AKS. Замените на `mymodel:1` имя и версию зарегистрированной модели. Замените `myservice` именем для предоставления этой службе:

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

Дополнительные сведения см. в справочнике по [развертыванию модели языка AZ ML](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) .

### <a name="using-vs-code"></a>Использование VS Code

Сведения об использовании VS Code см. в разделе [развертывание в AKS с помощью расширения VS Code](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> Для развертывания с помощью VS Code необходимо заранее создать кластер AKS или подключить его к рабочей области.

### <a name="understand-the-deployment-processes"></a>Общие сведения о процессах развертывания

Слово «Deployment» используется как в Kubernetes, так и в Машинное обучение Azure. В этих двух контекстах значение "Deployment" имеет разные значения. В Kubernetes `Deployment` — конкретная сущность, указанная с помощью декларативного файла YAML. Kubernetes `Deployment` имеет определенный жизненный цикл и конкретные связи с другими сущностями Kubernetes, такими как `Pods` и `ReplicaSets` . Вы можете узнать о Kubernetes из документации и видеороликов о том [, что такое Kubernetes?](https://aka.ms/k8slearning).

В Машинное обучение Azure "развертывание" используется в более общем смысле предоставления доступа и очистки ресурсов проекта. Ниже приведены действия, которые Машинное обучение Azure рассматривает часть развертывания.

1. Переключить файлы в папку проекта, игнорируя их, указанные в файле. амлигноре или. gitignore
1. Масштабирование кластера вычислений (относится к Kubernetes)
1. Сборка или скачивание dockerfile на расчетный узел (относится к Kubernetes)
    1. Система вычисляет хэш: 
        - Базовый образ 
        - Пользовательские шаги DOCKER (см. раздел [развертывание модели с помощью пользовательского базового образа DOCKER](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-custom-docker-image)).
        - Conda Definition YAML (см. раздел [создание & использование программных сред в машинное обучение Azure](https://docs.microsoft.com/azure/machine-learning/how-to-use-environments))
    1. Система использует этот хэш в качестве ключа при поиске в рабочей области реестра контейнеров Azure (запись контроля доступа).
    1. Если он не найден, он ищет соответствие в глобальной записи контроля доступа.
    1. Если он не найден, система создает новый образ (который будет кэшироваться и зарегистрирован в записи контроля доступа рабочей области).
1. Скачивание ZIP-файла проекта во временное хранилище на кластерном узле
1. Распаковка файла проекта
1. Выполненный узел вычислений `python <entry script> <arguments>`
1. Сохранение журналов, файлов моделей и других файлов, записанных в `./outputs` учетную запись хранения, связанную с рабочей областью
1. Уменьшение масштаба вычислений, включая удаление временного хранилища (относится к Kubernetes)

При использовании AKS масштабирование с увеличением и уменьшением масштаба осуществляется с помощью Kubernetes, используя dockerfile, созданное или найденное как описано выше. 

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>Развертывание моделей в AKS с помощью контролируемого развертывания (Предварительная версия)

Анализируйте и передвигайте версии модели в контролируемом виде с помощью конечных точек. Можно развернуть до шести версий за одну конечную точку. Конечные точки предоставляют следующие возможности.

* Настройте __процент трафика оценки, отправляемого в каждую конечную точку__. Например, маршрут 20% трафика к конечной точке "Test" и 80% в "Production".

    > [!NOTE]
    > Если не учитывать 100% трафика, оставшийся процент направляется к версии конечной точки __по умолчанию__ . Например, если настроить конечную точку "Test" для получения 10% трафика, а "произ." — 30%, оставшиеся 60% будут отправлены в конечную точку по умолчанию.
    >
    > Первая созданная версия конечной точки автоматически настраивается по умолчанию. Это можно изменить, задав параметр `is_default=True` при создании или обновлении версии конечной точки.
     
* Разметка версии конечной точки как __управления__ или __обработки__. Например, текущая версия рабочей конечной точки может быть элементом управления, а потенциальные новые модели развертываются как версии лечения. После оценки производительности версий лечения, если одна из них выполняет текущий элемент управления, она может быть перенесена в новый рабочий или элемент управления.

    > [!NOTE]
    > У вас может быть только __один__ элемент управления. Можно использовать несколько использование.

Вы можете включить Application Insights, чтобы просматривать операционные метрики конечных точек и развернутых версий.

### <a name="create-an-endpoint"></a>Создание конечной точки
Когда вы будете готовы к развертыванию моделей, создайте конечную точку оценки и разверните свою первую версию. В следующем примере показано, как развернуть и создать конечную точку с помощью пакета SDK. Первое развертывание будет определено как версия по умолчанию. Это означает, что неопределенный процент по всем версиям будет переходить к версии по умолчанию.  

> [!TIP]
> В следующем примере конфигурация устанавливает начальную версию конечной точки, чтобы обрабатывалась 20% трафика. Так как это первая конечная точка, она также является версией по умолчанию. А так как у нас нет других версий для других 80% трафика, он также направляется по умолчанию. До тех пор, пока не будут развернуты другие версии, которые принимают процент трафика, он получает 100% трафика.

```python
import azureml.core,
from azureml.core.webservice import AksEndpoint
from azureml.core.compute import AksCompute
from azureml.core.compute import ComputeTarget
# select a created compute
compute = ComputeTarget(ws, 'myaks')
namespace_name= endpointnamespace
# define the endpoint and version name
endpoint_name = "mynewendpoint"
version_name= "versiona"
# create the deployment config and define the scoring traffic percentile for the first deployment
endpoint_deployment_config = AksEndpoint.deploy_configuration(cpu_cores = 0.1, memory_gb = 0.2,
                                                              enable_app_insights = True,
                                                              tags = {'sckitlearn':'demo'},
                                                              description = "testing versions",
                                                              version_name = version_name,
                                                              traffic_percentile = 20)
 # deploy the model and endpoint
 endpoint = Model.deploy(ws, endpoint_name, [model], inference_config, endpoint_deployment_config, compute)
 # Wait for he process to complete
 endpoint.wait_for_deployment(True)
 ```

### <a name="update-and-add-versions-to-an-endpoint"></a>Обновление и добавление версий в конечную точку

Добавьте еще одну версию в конечную точку и настройте оценку трафика оценки до версии. Существует два типа версий: элемент управления и версия лечения. Для сравнения с одной версией элемента управления может существовать несколько версий лечения.

> [!TIP]
> Вторая версия, созданная следующим фрагментом кода, принимает 10% трафика. Первая версия настраивается на 20%, поэтому для конкретных версий настраивается только 30% трафика. Остальные 70% отправляются в первую версию конечной точки, так как она также является версией по умолчанию.

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
endpoint.wait_for_deployment(True)
```

Обновите существующие версии или удалите их в конечной точке. Можно изменить тип по умолчанию для версии, тип элемента управления и значение интенсивности трафика. В следующем примере вторая версия увеличивает трафик на 40% и теперь является значением по умолчанию.

> [!TIP]
> После выполнения следующего фрагмента кода вторая версия будет по умолчанию. Теперь она настроена на 40%, а исходная версия по-прежнему настроена на 20%. Это означает, что 40% трафика не учитывается конфигурациями версий. Остаточный трафик будет направляться во вторую версию, так как теперь он по умолчанию. Он эффективно получает 80% трафика.

 ```python
from azureml.core.webservice import AksEndpoint

# update the version's scoring traffic percentage and if it is a default or control type
endpoint.update_version(version_name=endpoint.versions["versionb"].name,
                        description="my second version update",
                        traffic_percentile=40,
                        is_default=True,
                        is_control_version_type=True)
# Wait for the process to complete before deleting
endpoint.wait_for_deployment(true)
# delete a version in an endpoint
endpoint.delete_version(version_name="versionb")

```


## <a name="web-service-authentication"></a>Проверка подлинности веб-службы

При развертывании в службе Kubernetes Azure проверка подлинности __на основе ключей__ включена по умолчанию. Также можно включить проверку подлинности __на основе маркеров__ . Для аутентификации на основе маркеров клиенты должны использовать учетную запись Azure Active Directory для запроса маркера проверки подлинности, который используется для выполнения запросов к развернутой службе.

Чтобы __Отключить__ проверку подлинности, задайте `auth_enabled=False` параметр при создании конфигурации развертывания. В следующем примере отключается проверка подлинности с помощью пакета SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

Сведения о проверке подлинности в клиентском приложении см. в разделе [использование машинное обучение Azureной модели, развернутой в качестве веб-службы](how-to-consume-web-service.md).

### <a name="authentication-with-keys"></a>Проверка подлинности с помощью ключей

Если включена проверка подлинности ключа, можно использовать `get_keys` метод для получения первичного и вторичного ключа проверки подлинности:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Если необходимо повторно создать ключ, используйте [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)

### <a name="authentication-with-tokens"></a>Аутентификация с помощью токенов

Чтобы включить проверку подлинности на маркере, задайте `token_auth_enabled=True` параметр при создании или обновлении развертывания. В следующем примере активируется проверка подлинности на основе маркеров с помощью пакета SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

Если включена проверка подлинности токенов, можно использовать `get_token` метод для получения маркера JWT и срока действия маркера:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> После времени маркера потребуется запросить новый токен `refresh_by` .
>
> Корпорация Майкрософт настоятельно рекомендует создать рабочую область Машинное обучение Azure в том же регионе, что и кластер службы Azure Kubernetes. Для проверки подлинности с помощью маркера веб-служба выполняет вызов в регион, в котором создана рабочая область Машинного обучения Azure. Если регион рабочей области недоступен, вы не сможете получить маркер для веб-службы даже в том случае, если кластер находится в регионе, отличном от региона рабочей области. Это фактически приводит к недоступности проверки подлинности на основе маркеров, пока регион рабочей области не будет снова доступен. Кроме того, чем больше расстояние между регионом кластера и регионом рабочей области, тем дольше будет получена лексема.
>
> Чтобы получить маркер, необходимо использовать пакет SDK для Машинное обучение Azure или команду [AZ ML Service Get-Access-Token](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-get-access-token) .

## <a name="next-steps"></a>Дальнейшие действия

* [Безопасное экспериментирование и вывод в виртуальной сети](how-to-enable-virtual-network.md)
* [Развертывание модели с помощью пользовательского образа DOCKER](how-to-deploy-custom-docker-image.md)
* [Устранение неполадок развертывания](how-to-troubleshoot-deployment.md)
* [Обновление веб-службы](how-to-deploy-update-web-service.md)
* [Использование TLS для защиты веб-службы с помощью Машинного обучения Azure](how-to-secure-web-service.md).
* [Использование модели Машинного обучения Azure, развернутой в качестве веб-службы](how-to-consume-web-service.md)
* [Мониторинг моделей Машинное обучение Azure с помощью Application Insights](how-to-enable-app-insights.md)
* [Сбор данных для моделей в рабочей среде](how-to-enable-data-collection.md)
