---
title: Сетевая изоляция & конфиденциальность
titleSuffix: Azure Machine Learning
description: Используйте изолированную виртуальную сеть Azure с Машинное обучение Azure для обеспечения безопасности экспериментирования и обучения, а также заданий вывода и оценки.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 05/11/2020
ms.custom: contperfq4
ms.openlocfilehash: 5099cc2ce2228bcdbf49d3484e488e7373883ec0
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83119038"
---
# <a name="secure-your-machine-learning-lifecycles-with-private-virtual-networks"></a>Защита жизненных циклов машинного обучения с помощью частных виртуальных сетей
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье вы узнаете, как изолировать задания экспериментирования и обучения, а также задания определения и оценки в Машинное обучение Azure в виртуальной сети Azure. Вы также узнаете о некоторых *дополнительных параметрах безопасности*, сведениях, которые не нужны для основных или экспериментальных вариантов использования.

> [!WARNING]
> Если базовое хранилище находится в виртуальной сети, пользователи не смогут использовать веб-интерфейс Машинное обучение Azure Studio, в том числе:
> - Конструктор перетаскивания
> - Пользовательский интерфейс для автоматизированного машинного обучения
> - Пользовательский интерфейс для меток данных
> - Пользовательский интерфейс для наборов данных
> - Записные книжки
> 
> При попытке попытаться получить сообщение об ошибке следующего вида:`__Error: Unable to profile this dataset. This might be because your data is stored behind a virtual network or your data does not support profile.__`

## <a name="what-is-a-vnet"></a>Что такое виртуальная сеть?

**Виртуальная сеть** выступает в качестве границы безопасности, изолируя ресурсы Azure из общедоступного Интернета. Также вы можете подключить виртуальную сеть Azure к локальной сети. Присоединяясь к сетям, вы можете безопасно обучать модели и получать доступ к развернутым моделям для вывода.

Для обучения и развертывания моделей Машинное обучение Azure полагается на другие службы Azure для ресурсов вычислений, также известные как [целевые объекты вычислений](concept-compute-target.md). Целевые объекты можно создать в виртуальной сети. Например, можно использовать Машинное обучение Azure COMPUTE для обучения модели, а затем развернуть модель в службе Azure Kubernetes Service (AKS). 


## <a name="prerequisites"></a>Предварительные требования

+ [Рабочая область](how-to-manage-workspace.md)машинное обучение Azure.

+ Общие сведения о [службе виртуальной сети Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) и [IP-сети](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm).

+ Предварительно Существующая виртуальная сеть и подсеть для использования с ресурсами вычислений.

## <a name="private-endpoints"></a>Частные конечные точки

Вы также можете [включить частную ссылку Azure](how-to-configure-private-link.md) для подключения к рабочей области с помощью частной конечной точки. Частная конечная точка — это набор частных IP-адресов в виртуальной сети. [Узнайте, как настроить эту закрытую конечную точку.](how-to-configure-private-link.md)



> [!TIP]
> Вы можете объединить виртуальную сеть и частную связь, чтобы защитить взаимодействие между рабочей областью и другими ресурсами Azure. Однако для некоторых сочетаний требуется рабочая область Enterprise Edition. Используйте следующую таблицу, чтобы понять, в каких сценариях требуется выпуск Enterprise Edition:
>
> | Сценарий | Предприятие</br>edition | Основные</br>edition |
> | ----- |:-----:|:-----:| 
> | Нет виртуальной сети или закрытой ссылки | ✔ | ✔ |
> | Рабочая область без закрытой ссылки. Другие ресурсы (кроме реестра контейнеров Azure) в виртуальной сети | ✔ | ✔ |
> | Рабочая область без закрытой ссылки. Другие ресурсы с закрытой ссылкой | ✔ | |
> | Рабочая область с частной ссылкой. Другие ресурсы (кроме реестра контейнеров Azure) в виртуальной сети | ✔ | ✔ |
> | Рабочая область и другие ресурсы с закрытой ссылкой | ✔ | |
> | Рабочая область с частной ссылкой. Другие ресурсы без частной связи или виртуальной сети | ✔ | ✔ |
> | Реестр контейнеров Azure в виртуальной сети | ✔ | |
> | Управляемые клиентом ключи для рабочей области | ✔ | |
> 

> [!WARNING]
> Предварительная версия Машинное обучение Azure COMPUTE Instances не поддерживается в рабочей области, где включена частная ссылка.
> 
> Машинное обучение Azure не поддерживает использование службы Azure Kubernetes, для которой включена частная ссылка. Вместо этого вы можете использовать службу Azure Kubernetes в виртуальной сети. Дополнительные сведения см. [в статье Защита заданий экспериментирования машинного обучения Azure и задания вывода в виртуальной сети Azure](how-to-enable-virtual-network.md).


<a id="amlcompute"></a>

## <a name="compute-clusters--instances"></a><a name="compute-instance"></a>& экземпляры кластеров вычислений

Чтобы использовать [управляемый **целевой объект машинное обучение Azure вычислительных** ](concept-compute-target.md#azure-machine-learning-compute-managed) ресурсов или [машинное обучение Azure вычислительного **экземпляра** ](concept-compute-instance.md) в виртуальной сети, необходимо выполнить следующие требования к сети.

> [!div class="checklist"]
> * Виртуальная сеть должна находиться в той же подписке и регионе, что и Рабочая область Машинное обучение Azure.
> * Подсеть, указанная для вычислительного экземпляра или кластера, должна иметь достаточное число неназначенных IP-адресов для размещения целевых виртуальных машин. Если в подсети недостаточно неназначенных IP-адресов, то кластер будет частично распределен.
> * Убедитесь, что политики безопасности или блокировки в подписке или группе ресурсов виртуальной сети ограничивают разрешения для управления виртуальной сетью. Если вы планируете защитить виртуальную сеть путем ограниченного трафика, оставьте некоторые порты открытыми для службы вычислений. Дополнительные сведения см. в разделе [Требуемые порты](#mlcports).
> * Если вы планируете разместить несколько вычислений или кластеров в одной виртуальной сети, может потребоваться увеличить квоту для одного или нескольких ресурсов.
> * Если учетные записи хранения Azure для рабочей области также защищены в виртуальной сети, они должны находиться в той же виртуальной сети, что и Машинное обучение Azure вычислительного экземпляра или кластера. 

> [!TIP]
> Машинное обучение вычислительный экземпляр или кластер автоматически выделяет дополнительные сетевые ресурсы __в группе ресурсов, содержащей виртуальную сеть__. Для каждого вычислительного экземпляра или кластера служба выделяет следующие ресурсы:
> 
> * Одна группа безопасности сети
> * Один общедоступный IP-адрес
> * Один балансировщик нагрузки
> 
> В случае кластеров эти ресурсы удаляются (и создаются повторно) каждый раз, когда кластер масштабируется до 0 узлов, однако для экземпляра, на который хранятся ресурсы, экземпляр полностью удаляется (при остановке ресурсы не удаляются). 
> Эти ресурсы ограничены [квотами ресурсов](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) в подписке.


### <a name="required-ports"></a><a id="mlcports"></a>Требуемые порты

Сейчас Вычислительная среда Машинного обучения использует пакетную службу Azure для подготовки виртуальных машин в указанной виртуальной сети. Это означает, что подсеть должна разрешать входящие подключения из пакетной службы. Это взаимодействие можно использовать для планирования выполнения на узлах Вычислительная среда Машинного обучения, а также для взаимодействия с хранилищем Azure и другими ресурсами. Пакетная служба добавляет группы безопасности сети (группы безопасности сети) на уровне сетевых интерфейсов (NIC), подключенных к виртуальным машинам. Эти группы безопасности сети автоматически настраивают правила для входящего и исходящего трафика, чтобы разрешить следующий трафик:

- Входящий TCP-трафик на портах 29876 и 29877 из __тега службы__ __батчнодеманажемент__.

    ![Правило входящего трафика, использующее тег службы Батчнодеманажемент](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- Используемых Входящий TCP-трафик через порт 22 для разрешения удаленного доступа. Используйте этот порт только в том случае, если вы хотите подключиться по протоколу SSH на общедоступном IP-адресе.

- Исходящий трафик в виртуальную сеть на любом порту.

- Исходящий трафик в Интернет на любом порту.

- Для вычислительного экземпляра входящий TCP-трафик через порт 44224 из __тега службы__ __азуремачинелеарнинг__.

Соблюдайте осторожность, если вы изменяете или добавляете правила для входящего и исходящего трафика в группах безопасности сети, настроенных для пакетной службы. Если NSG блокирует обмен данными с вычисленными узлами, Служба вычислений устанавливает для них состояние "непригоден для использования".

Вам не нужно указывать группы безопасности сети на уровне подсети, так как пакетная служба Azure настраивает собственный группы безопасности сети. Однако если указанная подсеть связана с группы безопасности сети или брандмауэром, настройте правила безопасности для входящего и исходящего трафика, как упоминалось ранее.

Конфигурация правила NSG в портал Azure показана на следующих изображениях:

:::image type="content" source="./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png" alt-text="Правила NSG для входящего трафика для Вычислительная среда Машинного обучения" border="true":::



![Правила исходящего NSG для Вычислительная среда Машинного обучения](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="limit-outbound-connectivity-from-the-virtual-network"></a><a id="limiting-outbound-from-vnet"></a>Ограничение исходящего подключения из виртуальной сети

Если вы не хотите использовать правила исходящего трафика по умолчанию и вы хотите ограничить исходящий доступ к виртуальной сети, выполните следующие действия.

- Запрет исходящего подключения к Интернету с помощью правил NSG.

- Для __вычислительного экземпляра__ или __вычислительного кластера__Ограничьте исходящий трафик следующими элементами:
   - Служба хранилища Azure с помощью __тега службы__ __Storage. RegionName__. Где `{RegionName}` — имя региона Azure.
   - Реестр контейнеров Azure с помощью __тега службы__ __азуреконтаинеррегистри. RegionName__. Где `{RegionName}` — имя региона Azure.
   - Машинное обучение Azure с помощью __тега службы__ __азуремачинелеарнинг__
   - Azure Resource Manager с помощью __тега службы__ __AzureResourceManager__
   - Azure Active Directory с помощью __тега службы__ __AzureActiveDirectory__

Конфигурация правила NSG на портал Azure показана на следующем рисунке:

[![Правила исходящего NSG для Вычислительная среда Машинного обучения](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png#lightbox)

> [!NOTE]
> Если вы планируете использовать образы DOCKER по умолчанию, предоставляемые корпорацией Майкрософт, и включите управляемые пользователем зависимости, необходимо также использовать __тег службы__ __микрософтконтаинеррегистри. Region_Name__ (например, микрософтконтаинеррегистри. EastUS).
>
> Эта конфигурация необходима при наличии кода, аналогичного приведенным ниже, в рамках сценариев обучения.
>
> __Обучение RunConfig__
> ```python
> # create a new runconfig object
> run_config = RunConfiguration()
> 
> # configure Docker 
> run_config.environment.docker.enabled = True
> # For GPU, use DEFAULT_GPU_IMAGE
> run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE 
> run_config.environment.python.user_managed_dependencies = True
> ```
>
> __Обучение средства оценки__
> ```python
> est = Estimator(source_directory='.',
>                 script_params=script_params,
>                 compute_target='local',
>                 entry_script='dummy_train.py',
>                 user_managed=True)
> run = exp.submit(est)
> ```

### <a name="user-defined-routes-for-forced-tunneling"></a>Пользовательские маршруты для принудительного туннелирования

Если вы используете принудительное туннелирование с Вычислительная среда Машинного обучения, добавьте [определяемые пользователем маршруты (определяемые пользователем маршруты)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) в подсеть, содержащую ресурс вычислений.

* Установите UDR для каждого IP-адреса, используемого пакетной службой Azure в регионе, где находятся ваши ресурсы. Эти определяемые пользователем маршруты позволяют пакетной службе взаимодействовать с вычисленными узлами для планирования задач. Также добавьте IP-адрес для службы Машинное обучение Azure, где существуют ресурсы, так как это необходимо для доступа к экземплярам вычислений. Чтобы получить список IP-адресов пакетной службы и службы Машинное обучение Azure, используйте один из следующих методов.

    * Скачайте [диапазоны IP-адресов и теги службы Azure](https://www.microsoft.com/download/details.aspx?id=56519) и найдите в файле `BatchNodeManagement.<region>` и `AzureMachineLearning.<region>` , где `<region>` — регион Azure.

    * Для загрузки сведений используйте [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) . В следующем примере загружаются сведения об IP-адресе и отфильтровываются сведения для региона "Восточная часть США 2":

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
        ```

* Исходящий трафик в службу хранилища Azure не должен блокироваться локальным сетевым устройством. В частности, URL-адреса находятся в форме `<account>.table.core.windows.net` , `<account>.queue.core.windows.net` и `<account>.blob.core.windows.net` .

При добавлении определяемые пользователем маршруты определите маршрут для каждого соответствующего префикса IP-адреса пакета и задайте для параметра __тип следующего прыжка__ значение __Интернет__. На следующем рисунке показан пример UDR в портал Azure:

![Пример UDR для префикса адреса](./media/how-to-enable-virtual-network/user-defined-route.png)

Дополнительные сведения см. [в статье Создание пула пакетной службы Azure в виртуальной сети](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling).

### <a name="create-a-compute-cluster-in-a-virtual-network"></a>Создание кластера вычислений в виртуальной сети

Чтобы создать кластер Вычислительная среда Машинного обучения, выполните следующие действия.

1. Войдите в [машинное обучение Azure Studio](https://ml.azure.com/), а затем выберите подписку и рабочую область.

1. Слева выберите пункт __Вычисление__.

1. Выберите __обучающие кластеры__ в центре, а затем выберите __+__ .

1. В диалоговом окне __новый кластер обучения__ разверните раздел __Дополнительные параметры__ .

1. Чтобы настроить этот ресурс вычислений для использования виртуальной сети, выполните следующие действия в разделе __Настройка виртуальной сети__ .

    1. В раскрывающемся списке __Группа ресурсов__ выберите группу ресурсов, содержащую виртуальную сеть.
    1. В раскрывающемся списке __Виртуальная сеть__ выберите виртуальную сеть, содержащую подсеть.
    1. В раскрывающемся списке __подсеть__ выберите подсеть для использования.

   ![Параметры виртуальной сети для Вычислительная среда Машинного обучения](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

Вы также можете создать кластер Вычислительной среды Машинного обучения с помощью пакета SDK для Машинного обучения Azure. Следующий код создает новый кластер Вычислительной среды Машинного обучения в подсети `default`, размещенной в виртуальной сети с именем `mynetwork`:

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# The Azure virtual network name, subnet, and resource group
vnet_name = 'mynetwork'
subnet_name = 'default'
vnet_resourcegroup_name = 'mygroup'

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print("Found existing cpucluster")
except ComputeTargetException:
    print("Creating new cpucluster")

    # Specify the configuration for the new cluster
    compute_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                           min_nodes=0,
                                                           max_nodes=4,
                                                           vnet_resourcegroup_name=vnet_resourcegroup_name,
                                                           vnet_name=vnet_name,
                                                           subnet_name=subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

    # Wait for the cluster to be completed, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

По завершении процесса создания вы обучите модель с помощью кластера в эксперименте. Дополнительные сведения вы найдете в статье [Настройка целевых объектов вычислений для обучения моделей](how-to-set-up-training-targets.md).

## <a name="use-a-storage-account-for-your-workspace"></a>Использование учетной записи хранения для рабочей области

Чтобы использовать учетную запись хранения Azure для рабочей области в виртуальной сети, выполните следующие действия.

1. Создайте вычислительный ресурс (например, Машинное обучение вычислительного экземпляра или кластера) за виртуальной сетью или подключите вычислительный ресурс к рабочей области (например, кластер HDInsight, виртуальную машину или кластер службы Azure Kubernetes). Ресурс вычислений может быть для эксперимента или развертывания модели.

   Дополнительные сведения см. в разделах [использование машинное обучение вычислений](#amlcompute), [Использование виртуальной машины или кластера HDInsight](#vmorhdi)и [Использование Azure Kubernetes Service](#aksvnet) в этой статье.

1. В портал Azure перейдите к хранилищу, подключенному к рабочей области.

   [![Хранилище, подключенное к рабочей области Машинное обучение Azure](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. На странице Служба **хранилища Azure** выберите __брандмауэры и виртуальные сети__.

   ![Область "брандмауэры и виртуальные сети" на странице службы хранилища Azure в портал Azure](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. На странице __брандмауэры и виртуальные сети__ выполните следующие действия.
    - Выберите __Выбранные сети__.
    - В разделе __виртуальные сети__щелкните ссылку __Добавить существующую виртуальную сеть__ . Это действие добавляет виртуальную сеть, в которой размещается вычисление (см. шаг 1).

        > [!IMPORTANT]
        > Учетная запись хранения должна находиться в той же виртуальной сети и подсети, что и вычисленные экземпляры или кластеры, используемые для обучения или вывода.

    - Установите флажок __разрешить доверенным службам Майкрософт доступ к этой учетной записи хранения__ .

    > [!IMPORTANT]
    > При работе с пакетом SDK для Машинное обучение Azure среда разработки должна иметь возможность подключения к учетной записи хранения Azure. Если учетная запись хранения находится в виртуальной сети, брандмауэр должен разрешить доступ с IP-адреса среды разработки.
    >
    > Чтобы разрешить доступ к учетной записи хранения, посетите __брандмауэры и виртуальные сети__ для учетной записи хранения *из веб-браузера на клиенте разработки*. Затем используйте флажок __Добавить IP-адрес клиента__ , чтобы добавить IP-адрес клиента в __диапазон адресов__. Можно также использовать поле __диапазон адресов__ , чтобы вручную ввести IP-адрес среды разработки. После добавления IP-адреса клиента он может получить доступ к учетной записи хранения с помощью пакета SDK.

   [![Панель "брандмауэры и виртуальные сети" в портал Azure](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

> [!IMPORTANT]
> Вы можете разместить _учетную запись хранения по умолчанию_ для машинное обучение Azure или _учетные записи хранения, отличные от используемых по умолчанию_ , в виртуальной сети.
>
> Учетная запись хранения по умолчанию автоматически подготавливается при создании рабочей области.
>
> Для учетных записей хранения, отличных от по умолчанию, `storage_account` параметр в [ `Workspace.create()` функции](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) позволяет указать настраиваемую учетную запись хранения по идентификатору ресурса Azure.


<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Служба Azure Kubernetes

Чтобы добавить службу Azure Kubernetes Service (AKS) в виртуальную сеть в рабочую область, выполните следующие действия.

> [!IMPORTANT]
> Прежде чем приступить к описанной ниже процедуре, выполните предварительные требования в разделе [Настройка расширенных сетевых возможностей в Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/configure-azure-cni#prerequisites) и планирование IP-адресации для кластера.
>
> Экземпляр AKS и виртуальная сеть Azure должны находиться в одном регионе. Если вы защищаете учетные записи хранения Azure, используемые рабочей областью в виртуальной сети, они должны находиться в той же виртуальной сети, что и экземпляр AKS.

> [!WARNING]
> Машинное обучение Azure не поддерживает использование службы Azure Kubernetes, для которой включена частная ссылка.

1. Войдите в [машинное обучение Azure Studio](https://ml.azure.com/), а затем выберите подписку и рабочую область.

1. Слева выберите пункт __Вычисление__.

1. Выберите __кластеры вывода__ из центра, а затем выберите __+__ .

1. В диалоговом окне __новый кластер вывода__ выберите __Дополнительно__ в разделе __Конфигурация сети__.

1. Чтобы настроить этот ресурс вычислений для использования виртуальной сети, выполните следующие действия.

    1. В раскрывающемся списке __Группа ресурсов__ выберите группу ресурсов, содержащую виртуальную сеть.
    1. В раскрывающемся списке __Виртуальная сеть__ выберите виртуальную сеть, содержащую подсеть.
    1. В раскрывающемся списке __подсеть__ выберите подсеть.
    1. В поле __диапазон адресов службы Kubernetes__ введите диапазон адресов службы Kubernetes. Этот диапазон адресов использует диапазон IP-адресов нотации, определяющий междоменную маршрутизацию (CIDR), чтобы определить IP-адреса, доступные для кластера. Он не должен пересекаться с диапазонами IP-адресов подсети (например, 10.0.0.0/16).
    1. В поле __IP-адрес службы DNS Kubernetes__ введите IP-адрес службы DNS Kubernetes. Это IP-адрес, назначенный службе DNS Kubernetes. Он должен находиться в диапазоне адресов службы Kubernetes (например, 10.0.0.10).
    1. В поле __адрес моста DOCKER__ введите адрес моста DOCKER. Этот IP-адрес, назначенный мосту Docker. Он не должен находиться в диапазонах IP-адресов подсети или в диапазоне номеров служб Kubernetes (например, 172.17.0.1/16).

   ![Машинное обучение Azure: параметры Вычислительная среда Машинного обучения виртуальной сети](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Убедитесь, что в группе NSG, управляющей виртуальной сетью, включено правило безопасности входящего трафика для конечной точки оценки, чтобы ее можно было вызывать извне виртуальной сети.
   > [!IMPORTANT]
   > Сохраните правила по умолчанию для исходящего трафика для группы безопасности сети. Дополнительные сведения см. в описании стандартных правил безопасности в статье [о группах безопасности](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

   [![Правило безопасности для входящего трафика](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

Вы также можете использовать пакет SDK для Машинного обучения Azure, чтобы добавить в виртуальную сеть Службу Azure Kubernetes. Если у вас уже есть кластер AKS в виртуальной сети, подключите его к рабочей области, как описано в разделе [развертывание в AKS](how-to-deploy-and-where.md). Следующий код создает новый экземпляр AKS в `default` подсети виртуальной сети с именем `mynetwork` :

```python
from azureml.core.compute import ComputeTarget, AksCompute

# Create the compute configuration and set virtual network information
config = AksCompute.provisioning_configuration(location="eastus2")
config.vnet_resourcegroup_name = "mygroup"
config.vnet_name = "mynetwork"
config.subnet_name = "default"
config.service_cidr = "10.0.0.0/16"
config.dns_service_ip = "10.0.0.10"
config.docker_bridge_cidr = "172.17.0.1/16"

# Create the compute target
aks_target = ComputeTarget.create(workspace=ws,
                                  name="myaks",
                                  provisioning_configuration=config)
```

После завершения процесса создания можно выполнить вывод или оценку модели в кластере AKS за пределами виртуальной сети. Дополнительные сведения см. в статье [о развертывании в Службе Azure Kubernetes](how-to-deploy-and-where.md).

### <a name="use-private-ips-with-azure-kubernetes-service"></a>Использование частных IP-адресов со службой Kubernetes Azure

По умолчанию для развертываний AKS назначается общедоступный IP-адрес. При использовании AKS внутри виртуальной сети можно использовать вместо него частный IP-адрес. Частные IP-адреса доступны только из виртуальной сети или из Объединенных сетей.

Частный IP-адрес включается путем настройки AKS для использования _внутренней подсистемы балансировки нагрузки_. 

> [!IMPORTANT]
> Вы не можете включить частный IP-адрес при создании кластера службы Kubernetes Azure. Он должен быть включен в качестве обновления существующего кластера.

В следующем фрагменте кода показано, как **создать новый кластер AKS**, а затем обновить его для использования частного IP-или внутреннего балансировщика нагрузки:

```python
import azureml.core
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute import AksCompute, ComputeTarget

# Verify that cluster does not exist already
try:
    aks_target = AksCompute(workspace=ws, name=aks_cluster_name)
    print("Found existing aks cluster")

except:
    print("Creating new aks cluster")

    # Subnet to use for AKS
    subnet_name = "default"
    # Create AKS configuration
    prov_config = AksCompute.provisioning_configuration(location = "eastus2")
    # Set info for existing virtual network to create the cluster in
    prov_config.vnet_resourcegroup_name = "myvnetresourcegroup"
    prov_config.vnet_name = "myvnetname"
    prov_config.service_cidr = "10.0.0.0/16"
    prov_config.dns_service_ip = "10.0.0.10"
    prov_config.subnet_name = subnet_name
    prov_config.docker_bridge_cidr = "172.17.0.1/16"

    # Create compute target
    aks_target = ComputeTarget.create(workspace = ws, name = "myaks", provisioning_configuration = prov_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
    
    # Update AKS configuration to use an internal load balancer
    update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
    aks_target.update(update_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
```

__Azure CLI__

```azurecli-interactive
az rest --method put --uri https://management.azure.com"/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.ContainerService/managedClusters/<aks-resource-id>?api-version=2018-11-19 --body @body.json
```

Содержимое `body.json` файла, на который ссылается команда, аналогично следующему документу JSON:

```json
{ 
    "location": "<region>", 
    "properties": { 
        "resourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.ContainerService/managedClusters/<aks-resource-id>", 
        "computeType": "AKS", 
        "provisioningState": "Succeeded", 
        "properties": { 
            "loadBalancerType": "InternalLoadBalancer", 
            "agentCount": <agent-count>, 
            "agentVmSize": "vm-size", 
            "clusterFqdn": "<cluster-fqdn>" 
        } 
    } 
} 
```

> [!NOTE]
> В настоящее время подсистема балансировки нагрузки не может быть настроена при выполнении операции __присоединения__ к существующему кластеру. Необходимо сначала подключить кластер, а затем выполнить операцию обновления, чтобы изменить подсистему балансировки нагрузки.

Дополнительные сведения об использовании внутренней подсистемы балансировки нагрузки с AKS см. в статье [использование внутренней подсистемы балансировки нагрузки со службой Kubernetes Azure](/azure/aks/internal-lb).

## <a name="use-azure-container-instances-aci"></a>Использование службы "экземпляры контейнеров Azure" (ACI)

Экземпляры контейнеров Azure динамически создаются при развертывании модели. Чтобы Машинное обучение Azure создать ACI в виртуальной сети, необходимо включить __Делегирование подсети__ для подсети, используемой развертыванием.

Чтобы использовать ACI в виртуальной сети в рабочей области, выполните следующие действия.

1. Чтобы включить делегирование подсети в виртуальной сети, используйте сведения из статьи [Добавление или удаление делегирования подсети](../virtual-network/manage-subnet-delegation.md) . Делегирование можно включить при создании виртуальной сети или добавить в существующую сеть.

    > [!IMPORTANT]
    > При включении делегирования используйте в `Microsoft.ContainerInstance/containerGroups` качестве значения параметра __подсеть делегирования для службы__ .

2. Разверните модель с помощью [аЦивебсервице. deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-), используйте `vnet_name` Параметры и `subnet_name` . Задайте для этих параметров имя виртуальной сети и подсеть, в которой включено делегирование.

## <a name="azure-firewall"></a>Брандмауэр Azure

Сведения об использовании Машинное обучение Azure с брандмауэром Azure см. в статье [использование машинное обучение Azure рабочей области за брандмауэром Azure](how-to-access-azureml-behind-firewall.md).

## <a name="azure-container-registry"></a>Реестр контейнеров Azure

> [!IMPORTANT]
> Реестр контейнеров Azure (запись контроля доступа) можно разместить в виртуальной сети, однако необходимо выполнить следующие предварительные требования.
>
> * Рабочая область Машинное обучение Azure должна быть Enterprise Edition. Сведения об обновлении см. [в статье обновление до выпуска Enterprise Edition](how-to-manage-workspace.md#upgrade).
> * Реестр контейнеров Azure должен иметь версию Premium. Дополнительные сведения об обновлении см. в разделе [изменение номеров SKU](/azure/container-registry/container-registry-skus#changing-skus).
> * Реестр контейнеров Azure должен находиться в той же виртуальной сети и подсети, что и учетная запись хранения, и целевые объекты вычислений, используемые для обучения или вывода.
> * Рабочая область Машинное обучение Azure должна содержать [машинное обучение Azure вычислений кластера](how-to-set-up-training-targets.md#amlcompute).
>
>     Если запись контроля доступа находится за виртуальной сетью, Машинное обучение Azure не может использовать ее для непосредственного создания образов DOCKER. Вместо этого для построения образов используется кластер вычислений.

1. Чтобы найти имя реестра контейнеров Azure для рабочей области, используйте один из следующих методов.

    __Портал Azure__

    В разделе Обзор рабочей области значение __реестра__ ссылается на реестр контейнеров Azure.

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="Реестр контейнеров Azure для рабочей области" border="true":::

    __Azure CLI__

    Если вы [установили расширение машинное обучение для Azure CLI](reference-azure-machine-learning-cli.md), можно использовать `az ml workspace show` команду, чтобы отобразить сведения о рабочей области.

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    Эта команда возвращает значение следующего вида: `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"`. Последняя часть строки — это имя реестра контейнеров Azure для рабочей области.

1. Чтобы ограничить доступ к виртуальной сети, выполните действия, описанные в [разделе Настройка доступа к сети для реестра](../container-registry/container-registry-vnet.md#configure-network-access-for-registry). При добавлении виртуальной сети выберите виртуальную сеть и подсеть для ресурсов Машинное обучение Azure.

1. Используйте пакет SDK для Машинное обучение Azure Python, чтобы настроить в расчете кластеры для создания образов DOCKER. В следующем фрагменте кода показано, как это сделать:

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    ```

    > [!IMPORTANT]
    > Учетная запись хранения, кластер вычислений и реестр контейнеров Azure должны находиться в одной подсети виртуальной сети.
    
    Дополнительные сведения см. в справочнике по методу [Update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-) .

1. Необходимо применить следующий шаблон Azure Resource Manager. Этот шаблон позволяет рабочей области взаимодействовать с записью контроля доступа.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "keyVaultArmId": {
        "type": "string"
        },
        "workspaceName": {
        "type": "string"
        },
        "containerRegistryArmId": {
        "type": "string"
        },
        "applicationInsightsArmId": {
        "type": "string"
        },
        "storageAccountArmId": {
        "type": "string"
        },
        "location": {
        "type": "string"
        }
    },
    "resources": [
        {
        "type": "Microsoft.MachineLearningServices/workspaces",
        "apiVersion": "2019-11-01",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "sku": {
            "tier": "enterprise",
            "name": "enterprise"
        },
        "properties": {
            "sharedPrivateLinkResources":
    [{"Name":"Acr","Properties":{"PrivateLinkResourceId":"[concat(parameters('containerRegistryArmId'), '/privateLinkResources/registry')]","GroupId":"registry","RequestMessage":"Approve","Status":"Pending"}}],
            "keyVault": "[parameters('keyVaultArmId')]",
            "containerRegistry": "[parameters('containerRegistryArmId')]",
            "applicationInsights": "[parameters('applicationInsightsArmId')]",
            "storageAccount": "[parameters('storageAccountArmId')]"
        }
        }
    ]
    }
    ```
    
## <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

Azure Data Lake Storage Gen 2 — это набор возможностей для аналитики больших данных, построенных в хранилище BLOB-объектов Azure. Он может использоваться для хранения данных, используемых для обучения моделей с Машинное обучение Azure. 

Чтобы использовать Data Lake Storage Gen 2 в виртуальной сети Машинное обучение Azure рабочей области, выполните следующие действия.

1. Создайте учетную запись Azure Data Lake Storage Gen 2. Дополнительные сведения см. [в статье Создание учетной записи хранения Azure Data Lake Storage 2-го поколения](../storage/blobs/data-lake-storage-quickstart-create-account.md).

1. Выполните шаги 2-4 в предыдущем разделе, чтобы разместить учетную запись в виртуальной сети, [используя учетную запись хранения для рабочей области](#use-a-storage-account-for-your-workspace).

При использовании Машинное обучение Azure с Data Lake Storage Gen 2 в виртуальной сети используйте следующие рекомендации.

* Если __для создания набора данных используется пакет SDK__, а система, в которой выполняется код, отсутствует __в виртуальной сети__, используйте `validate=False` параметр. Этот параметр пропускает проверку, которая завершается ошибкой, если система не находится в той же виртуальной сети, что и учетная запись хранения. Дополнительные сведения см. в описании метода [from_files ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) .

* При использовании Машинное обучение Azure вычислительного экземпляра или вычислительного кластера для обучения модели с помощью набора данных она должна находиться в той же виртуальной сети, что и учетная запись хранения.

## <a name="key-vault-instance"></a>Экземпляр хранилища ключей 

Экземпляр хранилища ключей, связанный с рабочей областью, используется Машинное обучение Azure для хранения следующих учетных данных:
* Строка подключения связанной учетной записи хранения
* Пароли для экземпляров репозитория контейнеров Azure
* Строки подключения к хранилищам данных

Чтобы использовать Машинное обучение Azure возможности экспериментов с Azure Key Vault за виртуальной сетью, выполните следующие действия.

1. Перейдите в хранилище ключей, связанное с рабочей областью.

   [![Хранилище ключей, связанное с рабочей областью Машинное обучение Azure](./media/how-to-enable-virtual-network/workspace-key-vault.png)](./media/how-to-enable-virtual-network/workspace-key-vault.png#lightbox)

1. На странице **Key Vault** в левой области выберите __брандмауэры и виртуальные сети__.

   ![Раздел "брандмауэры и виртуальные сети" в области Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks.png)

1. На странице __брандмауэры и виртуальные сети__ выполните следующие действия.
    - В разделе __Разрешить доступ из__ щелкните __Выбранные сети__.
    - В разделе __виртуальные сети__выберите __Добавить существующие виртуальные сети__ , чтобы добавить виртуальную сеть, в которой находится вычисление экспериментов.
    - В разделе __разрешить доверенным службам Майкрософт обходить этот брандмауэр__выберите __Да__.

   [![Раздел "брандмауэры и виртуальные сети" в области Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)


## <a name="azure-databricks"></a>Azure Databricks

Чтобы использовать Azure Databricks в виртуальной сети с рабочей областью, должны выполняться следующие требования.

> [!div class="checklist"]
> * Виртуальная сеть должна находиться в той же подписке и регионе, что и Рабочая область Машинное обучение Azure.
> * Если учетные записи хранения Azure для рабочей области также защищены в виртуальной сети, они должны находиться в той же виртуальной сети, что и кластер Azure Databricks.
> * В дополнение к __модулям "данные — частные__ " и " __кирпичы" — общедоступные__ подсети, используемые Azure Databricks, также требуется подсеть __по умолчанию__ , созданная для виртуальной сети.

Конкретные сведения об использовании Azure Databricks с виртуальной сетью см. в статье [развертывание Azure Databricks в виртуальной сети Azure](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html).

<a id="vmorhdi"></a>

## <a name="virtual-machine-or-hdinsight-cluster"></a>Виртуальная машина или кластер HDInsight

> [!IMPORTANT]
> Машинное обучение Azure поддерживает только виртуальные машины под управлением Ubuntu.

Чтобы использовать виртуальную машину или кластер Azure HDInsight в виртуальной сети с рабочей областью, выполните следующие действия.

1. Создайте виртуальную машину или кластер HDInsight с помощью портал Azure или Azure CLI и вставьте кластер в виртуальную сеть Azure. См. сведения в следующих статьях:
    * [Создание и администрирование виртуальных сетей Azure для виртуальных машин Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [Расширение возможностей HDInsight с помощью виртуальной сети Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network).

1. Чтобы разрешить Машинное обучение Azure взаимодействовать с портом SSH на виртуальной машине или в кластере, настройте запись источника для группы безопасности сети. Обычно для SSH используется порт 22. Чтобы разрешить трафик из этого источника, выполните следующие действия.

    * В раскрывающемся списке __источник__ выберите __тег службы__.

    * В раскрывающемся списке __тег исходной службы__ выберите __азуремачинелеарнинг__.

    * В раскрывающемся списке __диапазоны исходных портов__ выберите __*__ .

    * В раскрывающемся списке __назначение__ выберите __любой__.

    * В раскрывающемся списке __диапазоны портов назначения выберите значение__ __22__.

    * В разделе __протокол__выберите __любой__.

    * В разделе __действие__выберите __Разрешить__.

   ![Правила для входящих подключений для экспериментов на виртуальной машине или в кластере HDInsight в виртуальной сети](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

    Примите правила исходящего трафика по умолчанию для группы безопасности сети. Дополнительные сведения см. в описании стандартных правил безопасности в статье [о группах безопасности](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

    Если вы не хотите использовать правила исходящего трафика по умолчанию и вы хотите ограничить исходящий доступ к виртуальной сети, см. раздел [ограничение исходящего подключения в виртуальной сети](#limiting-outbound-from-vnet) .

1. Подключите виртуальную машину или кластер HDInsight к рабочей области Машинное обучение Azure. Дополнительные сведения вы найдете в статье [Настройка целевых объектов вычислений для обучения моделей](how-to-set-up-training-targets.md).


## <a name="next-steps"></a>Дальнейшие действия

* [Настройка сред обучения](how-to-set-up-training-targets.md)
* [Настройка частных конечных точек](how-to-configure-private-link.md)
* [Где следует развертывать модели](how-to-deploy-and-where.md)
* [Использование TLS для защиты веб-службы с помощью Машинное обучение Azure](how-to-secure-web-service.md)
