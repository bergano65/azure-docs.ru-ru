---
title: Создание обучающих & развертывание вычислений (Python)
titleSuffix: Azure Machine Learning
description: Использование пакета SDK для Машинное обучение Azure Python для создания ресурсов для обучения и развертывания (целевые объекты вычислений) для машинного обучения
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 07/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: cc7ca9d217e405b0b39779cf256edcf0669afd6b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91302440"
---
# <a name="create-compute-targets-for-model-training-and-deployment-with-python-sdk"></a>Создание целевых объектов вычислений для обучения и развертывания моделей с помощью пакета SDK для Python

В этой статье описано, как создавать целевые объекты вычислений и управлять ими с помощью пакета SDK для Машинное обучение Azure Python. Вы также можете создавать целевые объекты вычислений и управлять ими с помощью:
* [Машинное обучение Azure Studio](how-to-create-attach-compute-studio.md), 
* [Расширение CLI](reference-azure-machine-learning-cli.md#resource-management) для машинное обучение Azure
* [Расширение VS Code](how-to-manage-resources-vscode.md#compute-clusters) для машинное обучение Azure.


## <a name="prerequisites"></a>Предварительные требования

* Если у вас еще нет подписки Azure, создайте бесплатную учетную запись, прежде чем начинать работу. Опробуйте [бесплатную или платную версию машинное обучение Azure](https://aka.ms/AMLFree) уже сегодня
* [Пакет SDK для машинное обучение Azure для Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)
* [Рабочая область машинное обучение Azure](how-to-manage-workspace.md)

## <a name="limitations"></a>Ограничения

* **Не создавайте несколько одновременных вложений для одного и того же вычислений** из рабочей области. Например, можно подключить один кластер службы Azure Kubernetes к рабочей области, используя два разных имени. Каждое новое вложение приведет к нарушению предыдущих существующих вложений.

    Если необходимо повторно подключить целевой объект вычислений, например для изменения конфигурации TLS или другого кластера, необходимо сначала удалить существующее вложение.

* Некоторые сценарии, перечисленные в этом документе, помечены как __Предварительная версия__. Функции предварительной версии предоставляются без соглашения об уровне обслуживания и не рекомендуются для рабочих нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="whats-a-compute-target"></a>Что такое целевой объект вычислений?

С помощью решения "Машинное обучение Azure" вы можете обучать модель, используя разные вычислительные ресурсы или среды, которые вместе называются [__целевыми объектами вычислений__](concept-azure-machine-learning-architecture.md#compute-targets). Они могут быть локальными или облачными. Например, это может быть Вычислительная среда Машинного обучения Azure, Azure HDInsight или удаленная виртуальная машина.  Можно также создать целевые объекты вычислений для развертывания моделей, как описано в статье [Развертывание моделей с помощью Службы машинного обучения Azure](how-to-deploy-and-where.md).

## <a name="compute-targets-for-training"></a>Целевые объекты вычислений для обучения

Машинное обучение Azure по-разному поддерживает разные целевые объекты вычислений. Жизненный цикл разработки обычной модели начинается с разработки и экспериментов с небольшим объемом данных. На этом этапе мы рекомендуем использовать локальную среду. Например, ваш локальный компьютер или облачную виртуальную машину. По мере перехода к обучению на больших наборах данных или при выполнении распределенного обучения мы рекомендуем использовать Вычислительную среду Машинного обучения Azure, чтобы создать одно- или многоузловой кластер, который автоматически масштабируется при каждой отправке запроса на запуск. Можно также подключить собственный вычислительный ресурс, хотя не все сценарии могут поддерживаться. Подробнее об этом рассказывается ниже.

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

> [!NOTE]
> Машинное обучение Azure COMPUTE Clusters можно создать как постоянный ресурс или динамически создать при запросе запуска. Созданные при запуске целевые объекты вычислений будут удалены после завершения обучающего запуска, поэтому объекты вычислений, созданные таким способом, невозможно использовать повторно.

Чтобы настроить эти целевые объекты вычислений, используйте следующие разделы.

* [Локальный компьютер](#local)
* [Вычислительный кластер Машинного обучения Azure](#amlcompute)
* [Вычислительная операция Машинного обучения Azure](#instance)
* [Удаленные виртуальные машины](#vm)
* [Azure HDInsight](#hdinsight)

## <a name="compute-targets-for-inference"></a>Целевые объекты вычислений для вывода

При выполнении вывода Машинное обучение Azure создает контейнер DOCKER, в котором размещается модель и связанные ресурсы, необходимые для ее использования. Затем этот контейнер используется в одном из следующих сценариев развертывания:

* В качестве __веб-службы__ , используемой для вывода в режиме реального времени. Развертывания веб-служб используют одну из следующих целевых объектов вычислений:

    * [Локальный компьютер](#local)
    * [Вычислительная операция Машинного обучения Azure](#instance)
    * [Экземпляры контейнеров Azure](#aci);
    * [Службы Azure Kubernetes](how-to-create-attach-kubernetes.md);
    * Функции Azure (Предварительная версия). Развертывание в функции Azure зависит только от Машинное обучение Azure для создания контейнера DOCKER. После этого они развертываются с помощью функций Azure. Дополнительные сведения см. [в статье Развертывание модели машинного обучения в функциях Azure (Предварительная версия)](how-to-deploy-functions.md).

* В качестве конечной точки __вывода пакета__ , которая используется для периодической обработки пакетов данных. В выводе пакетов используется [кластер машинное обучение Azure COMPUTE](#amlcompute).

* На __устройство IOT__ (Предварительная версия). Развертывание на устройстве IoT зависит только от Машинное обучение Azure для создания контейнера DOCKER. После этого оно развертывается с помощью Azure IoT Edge. Дополнительные сведения см. в статье [развертывание в виде модуля IOT EDGE (Предварительная версия)](/azure/iot-edge/tutorial-deploy-machine-learning).

## <a name="local-computer"></a><a id="local"></a>Локальный компьютер

При использовании локального компьютера для **обучения**нет необходимости создавать целевой объект вычислений.  Просто [отправьте обучающий запуск](how-to-set-up-training-targets.md) с локального компьютера.

При использовании локального компьютера для **вывода**необходимо установить DOCKER. Чтобы выполнить развертывание, используйте [локалвебсервице. deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py&preserve-view=true#deploy-configuration-port-none-) , чтобы определить порт, который будет использовать веб-служба. Затем используйте нормальный процесс развертывания, как описано в разделе [Развертывание моделей с помощью машинное обучение Azure](how-to-deploy-and-where.md).

## <a name="azure-machine-learning-compute-cluster"></a><a id="amlcompute"></a>Кластер Машинное обучение Azure COMPUTE

Кластер Машинное обучение Azure COMPUTE — это управляемая инфраструктура вычислений, которая позволяет легко создавать одно или несколько вычислений. Она создается в регионе вашей рабочей области и является ресурсом, который можно использовать совместно с другими пользователями в этой рабочей области. Вычислительная среда автоматически масштабируется при отправке задания, а также может быть размещена в виртуальной сети Azure. Она выполняется в контейнерной среде, упаковывая зависимости вашей модели в [контейнер Docker](https://www.docker.com/why-docker).

Машинное обучение Azure вычисления можно использовать для распределения процесса обучения или выполнения пакетного вывода в кластере вычислительных узлов ЦП или GPU в облаке. Дополнительные сведения о размерах виртуальных машин, которые содержат GPU, см. в статье [Размеры виртуальных машин, оптимизированных для GPU](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu). 

Вычислительная среда Машинного обучения Azure имеет ограничения по умолчанию, такие как количество ядер, которые могут быть выделены. Дополнительные сведения см. в статье [Управление квотами для ресурсов Azure и их запрашивание](how-to-manage-quotas.md).

> [!TIP]
> Кластеры обычно масштабируются до 100 узлов при наличии достаточной квоты для требуемого числа ядер. По умолчанию кластеры настроены с поддержкой взаимодействия между их узлами, например для поддержки заданий MPI. Однако вы можете масштабировать кластеры до тысяч узлов, просто [вызывая запрос в службу поддержки](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)и запрашивая разрешение на получение списка подписки, рабочей области или конкретного кластера для отключения взаимодействия между узлами. 

Вычислительную среду Машинного обучения Azure можно многократно использовать при различных запусках. Результаты вычислений могут использоваться совместно с другими пользователями в рабочей области. Они сохраняются для последующих запусков, а число узлов в них автоматически масштабируется в зависимости от числа отправленных запросов на запуск и заданного для кластера значения max_nodes. С помощью параметра min_nodes можно контролировать минимально доступное количество узлов.

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

1. **Создание и присоединение**. Чтобы создать постоянный ресурс Вычислительной среды машинного обучения Azure в Python, необходимо указать свойства **vm_size** и **max_nodes**. Затем Машинное обучение Azure использует интеллектуальные значения по умолчанию для остальных свойств. Когда вычислительная среда не используется, она автоматически масштабируется до нуля узлов.   Выделенные виртуальные машины создаются для выполнения заданий по мере необходимости.
    
    * **vm_size**. Семейство виртуальных машин узлов, создаваемых Вычислительной средой Машинного обучения Azure.
    * **max_nodes**. Максимальное количество узлов для автомасштабирования при запуске задания в Вычислительной среде Машинного обучения Azure.
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   При создании Вычислительной среды машинного обучения Azure вы можете также настроить несколько дополнительных свойств. Эти свойства позволяют создать постоянный кластер фиксированного размера, который может размещаться в существующей виртуальной сети Azure в вашей подписке.  Дополнительные сведения см. в разделе о [классе AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py&preserve-view=true).

    Или вы можете создать и присоединить постоянный ресурс Вычислительной среды Машинного обучения в [Студии машинного обучения Azure](how-to-create-attach-compute-studio.md#portal-create).

Теперь, когда вы присоединяете вычисления, следующим шагом является [Отправка обучающего запуска](how-to-set-up-training-targets.md) или [выполнения вывода пакета](how-to-use-parallel-run-step.md).

 ### <a name="lower-your-compute-cluster-cost"></a><a id="low-pri-vm"></a> Снижение стоимости вычислений в кластере

Вы также можете использовать [виртуальные машины с низким приоритетом](concept-plan-manage-cost.md#low-pri-vm) для выполнения некоторых или всех рабочих нагрузок. Эти виртуальные машины не обеспечены гарантиями доступности и могут быть замещены при использовании. Замещенное задание перезапускается, а не возобновляется. 

Используйте любой из этих способов, чтобы указать виртуальную машину с низким приоритетом:
    
* В студии при создании виртуальной машины выберите **низкий приоритет** .
    
* В пакете SDK для Python задайте `vm_priority` атрибут в конфигурации подготовки.  
    
    ```python
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                vm_priority='lowpriority',
                                                                max_nodes=4)
    ```
    
* С помощью интерфейса командной строки установите `vm-priority` :
    
    ```azurecli-interactive
    az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
    ```

 ### <a name="set-up-managed-identity"></a><a id="managed-identity"></a> Настройка управляемого удостоверения

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

Настройте управляемое удостоверение в конфигурации подготовки:  
    
* Управляемое удостоверение, назначенное системой:
    ```python
    # configure cluster with a system-assigned managed identity
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=5,
                                                            identity_type="SystemAssigned",
                                                            )
    ```

* Управляемое удостоверение, назначаемое пользователем: 

    ```python
    # configure cluster with a user-assigned managed identity
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=5,
                                                            identity_type="UserAssigned",
                                                            identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])

    cpu_cluster_name = "cpu-cluster"
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    ```

Добавление управляемого удостоверения в существующий кластер вычислений:  
    
* Управляемое удостоверение, назначаемое системой:

    ```python
    # add a system-assigned managed identity
    cpu_cluster.add_identity(identity_type="SystemAssigned")
    ````

* Управляемое удостоверение, назначаемое пользователем:

    ```python
    # add a user-assigned managed identity
    cpu_cluster.add_identity(identity_type="UserAssigned", 
                                identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
    ```

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

#### <a name="managed-identity-usage"></a>Использование управляемого удостоверения

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]


## <a name="azure-machine-learning-compute-instance"></a><a id="instance"></a>Вычислительная операция Машинного обучения Azure

[Машинное обучение Azure вычислительный экземпляр](concept-compute-instance.md) — это управляемая вычислительная инфраструктура, которая позволяет легко создавать одну виртуальную машину. Вычислительные ресурсы создаются в регионе рабочей области, но в отличие от вычислительного кластера, экземпляр нельзя использовать совместно с другими пользователями в рабочей области. Кроме того, экземпляр не масштабируется автоматически.  Необходимо закрыть ресурс, чтобы предотвратить постоянную плату.

Вычислительный экземпляр может параллельно запускать несколько заданий и иметь очередь заданий. 

Экземпляры вычислений могут безопасно выполнять задания в [среде виртуальной сети](how-to-enable-virtual-network.md#compute-instance), не требуя, чтобы предприятия открывали порты SSH. Задание выполняется в контейнерной среде и упаковывает зависимости модели в контейнер DOCKER. 

1. **Создание и присоединение**. 
    
    ```python
    import datetime
    import time
    
    from azureml.core.compute import ComputeTarget, ComputeInstance
    from azureml.core.compute_target import ComputeTargetException
    
    # Choose a name for your instance
    # Compute instance name should be unique across the azure region
    compute_name = "ci{}".format(ws._workspace_id)[:10]
    
    # Verify that instance does not exist already
    try:
        instance = ComputeInstance(workspace=ws, name=compute_name)
        print('Found existing instance, use it.')
    except ComputeTargetException:
        compute_config = ComputeInstance.provisioning_configuration(
            vm_size='STANDARD_D3_V2',
            ssh_public_access=False,
            # vnet_resourcegroup_name='<my-resource-group>',
            # vnet_name='<my-vnet-name>',
            # subnet_name='default',
            # admin_user_ssh_public_key='<my-sshkey>'
        )
        instance = ComputeInstance.create(ws, compute_name, compute_config)
        instance.wait_for_completion(show_output=True)
    ```

Теперь, когда вы присоединяете вычисления и настроили свой запуск, следующим шагом является [Отправка обучающего запуска](how-to-set-up-training-targets.md) или [развертывание модели для вывода](how-to-deploy-local-container-notebook-vm.md).

## <a name="azure-container-instance"></a><a id="aci"></a>Экземпляр контейнера Azure

Службы "экземпляры контейнеров Azure" (ACI) создаются динамически при развертывании модели. Вы не можете создать или присоединить ACI к рабочей области любым другим способом. Дополнительные сведения см. в статье [развертывание модели в службе "экземпляры контейнеров Azure](how-to-deploy-azure-container-instance.md)".

## <a name="azure-kubernetes-service"></a>Служба Azure Kubernetes

Служба Azure Kubernetes Service (AKS) позволяет использовать различные параметры конфигурации при использовании с Машинное обучение Azure. Дополнительные сведения см. [в статье Создание и подключение службы Kubernetes Azure](how-to-create-attach-kubernetes.md).

## <a name="remote-virtual-machines"></a><a id="vm"></a>Удаленные виртуальные машины

Машинное обучение Azure также поддерживает использование собственных вычислительных ресурсов и подключение их к рабочей области. К такому типу ресурсов относится произвольная удаленная виртуальная машина, если она доступна из Машинного обучения Azure. Ресурсом может быть виртуальная машина Azure, удаленный или локальный сервер в вашей организации. В частности, указав IP-адрес и учетные данные (имя пользователя и пароль или ключ SSH), можно использовать любую доступную виртуальную машину для удаленного запуска.

Можно использовать системную среду Conda, существующую среду Python или контейнер Docker. Для выполнения контейнера Docker на виртуальной машине должен быть запущен модуль Docker. Эта функция особенно удобна в случаях, когда необходима более гибкая облачная среда разработки и проведения экспериментов, чем локальный компьютер.

Используйте виртуальную машину Azure для обработки и анализа данных (DSVM) в качестве выбранной виртуальной машины Azure для этого сценария. Эта виртуальная машина является предварительно настроенной обработкой и анализом данных и средой разработки искусственного интеллекта в Azure. Виртуальная машина предлагает выбор средств и платформ для разработки машинного обучения в течение всего жизненного цикла. Дополнительных сведения об использовании DSVM со Службой машинного обучения Azure см. в статье [Настройка среды разработки для Машинного обучения Azure](https://docs.microsoft.com/azure/machine-learning/how-to-configure-environment#dsvm).

1. **Создание**. Создайте DSVM перед ее использованием для обучения модели. Подробные сведения о создании этого ресурса см. в разделе [Подготовка виртуальной машины Linux (Ubuntu) для обработки и анализа данных](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro).

    > [!WARNING]
    > Машинное обучение Azure поддерживает только виртуальные машины под управлением **Ubuntu**. Поэтому создаваемая виртуальная или существующая виртуальная машина, которую вы выбираете, должна работать под управлением Ubuntu.
    > 
    > Для Машинное обучение Azure также требуется, чтобы виртуальная машина соимела __общедоступный IP-адрес__.

1. **Присоединение**. Чтобы присоединить имеющуюся виртуальную машину как целевой объект вычислений, необходимо указать для нее ИД ресурса, имя входа и пароль. Идентификатор ресурса виртуальной машины можно создать с помощью идентификатора подписки, имени группы ресурсов и имени виртуальной машины, используя следующий строковый формат: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Compute/virtualMachines/<vm_name>`

 
   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   
   attach_config = RemoteCompute.attach_configuration(resource_id='<resource_id>',
                                                   ssh_port=22,
                                                   username='<username>',
                                                   password="<password>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   Кроме того, можно присоединить DSVM к рабочей области, [используя Студию машинного обучения Azure](how-to-create-attach-compute-studio.md#attached-compute).

    > [!WARNING]
    > Не создавайте несколько одновременных вложений для одного и того же DSVM из рабочей области. Каждое новое вложение приведет к нарушению предыдущих существующих вложений.

1. **Настройка**. Создайте конфигурацию запуска для целевого объекта вычислений DSVM. Для создания и настройки среды обучения на DSVM используются Docker и Conda.

   ```python
   from azureml.core import ScriptRunConfig
   from azureml.core.environment import Environment
   from azureml.core.conda_dependencies import CondaDependencies
   
   # Create environment
   myenv = Environment(name="myenv")
   
   # Specify the conda dependencies
   myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
   
   # If no base image is explicitly specified the default CPU image "azureml.core.runconfig.DEFAULT_CPU_IMAGE" will be used
   # To use GPU in DSVM, you should specify the default GPU base Docker image or another GPU-enabled image:
   # myenv.docker.enabled = True
   # myenv.docker.base_image = azureml.core.runconfig.DEFAULT_GPU_IMAGE
   
   # Configure the run configuration with the Linux DSVM as the compute target and the environment defined above
   src = ScriptRunConfig(source_directory=".", script="train.py", compute_target=compute, environment=myenv) 
   ```

Теперь, после подключения вычислительной среды и настройки запуска, следующий шаг — [отправить запрос на запуск обучения](how-to-set-up-training-targets.md).

## <a name="azure-hdinsight"></a><a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight — это популярная платформа для анализа больших данных. Она предоставляет Apache Spark, который можно использовать для обучения модели.

1. **Создание**.  Прежде чем использовать кластер HDInsight для обучения модели, создайте его. Инструкции по созданию Spark в кластере HDInsight см. в статье [Краткое руководство. Создание кластера Apache Spark в HDInsight с помощью шаблона](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql). 

    > [!WARNING]
    > Для Машинное обучение Azure требуется, чтобы кластер HDInsight имел __общедоступный IP-адрес__.

    При создании кластера необходимо указать имя пользователя SSH и пароль. Запишите эти значения — они вам потребуются при использовании HDInsight в качестве целевого объекта вычислений.
    
    После создания кластера подключитесь к нему с помощью имени узла \<clustername> -SSH.azurehdinsight.NET, где \<clustername> — это имя, указанное для кластера. 

1. **Присоединение**. Чтобы присоединить кластер HDInsight в качестве целевого объекта вычислений, необходимо указать ИД ресурса, имя пользователя и пароль для кластера HDInsight. Идентификатор ресурса кластера HDInsight можно создать с помощью идентификатора подписки, имени группы ресурсов и имени кластера HDInsight, используя следующий строковый формат: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.HDInsight/clusters/<cluster_name>`

    ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase

    attach_config = HDInsightCompute.attach_configuration(resource_id='<resource_id>',
                                                          ssh_port=22, 
                                                          username='<ssh-username>', 
                                                          password='<ssh-pwd>')
    hdi_compute = ComputeTarget.attach(workspace=ws, 
                                       name='myhdi', 
                                       attach_configuration=attach_config)

   except ComputeTargetException as e:
    print("Caught = {}".format(e.message))

   hdi_compute.wait_for_completion(show_output=True)
   ```

   Кроме того, можно присоединить кластер HDInsight к рабочей области, [используя Студию машинного обучения Azure](how-to-create-attach-compute-studio.md#attached-compute).

    > [!WARNING]
    > Не создавайте несколько одновременных вложений в одном HDInsight из рабочей области. Каждое новое вложение приведет к нарушению предыдущих существующих вложений.

1. **Настройка**. Создайте конфигурацию запуска для целевого объекта вычислений HDI. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Теперь, после подключения вычислительной среды и настройки запуска, следующий шаг — [отправить запрос на запуск обучения](how-to-set-up-training-targets.md).

## <a name="azure-batch"></a><a id="azbatch"></a>Пакетная служба Azure 

Пакетная служба Azure используется для эффективного запуска приложений для крупномасштабных параллельных и высокопроизводительных вычислений (HPC) в облаке. В конвейере Машинного обучения Azure для отправки заданий в пул виртуальных машин пакетной службы Azure можно использовать AzureBatchStep.

Чтобы присоединить пакетную службу Azure в качестве целевого объекта вычислений, используйте пакет SDK Машинного обучения Azure и укажите следующие сведения.

-    **Azure Batch compute name** (Имя пакетного вычисления пакетной службы Azure). Понятное имя, используемое для вычислений в рабочей области.
-    **Имя учетной записи для пакетной службы Azure**. Имя учетной записи пакетной службы Azure.
-    **Группа ресурсов**. Группа ресурсов, в которой содержится учетная запись пакетной службы Azure.

Следующий код демонстрирует, как присоединить пакетную службу в качестве целевого объекта вычислений.

```python
from azureml.core.compute import ComputeTarget, BatchCompute
from azureml.exceptions import ComputeTargetException

# Name to associate with new compute in workspace
batch_compute_name = 'mybatchcompute'

# Batch account details needed to attach as compute to workspace
batch_account_name = "<batch_account_name>"  # Name of the Batch account
# Name of the resource group which contains this account
batch_resource_group = "<batch_resource_group>"

try:
    # check if the compute is already attached
    batch_compute = BatchCompute(ws, batch_compute_name)
except ComputeTargetException:
    print('Attaching Batch compute...')
    provisioning_config = BatchCompute.attach_configuration(
        resource_group=batch_resource_group, account_name=batch_account_name)
    batch_compute = ComputeTarget.attach(
        ws, batch_compute_name, provisioning_config)
    batch_compute.wait_for_completion()
    print("Provisioning state:{}".format(batch_compute.provisioning_state))
    print("Provisioning errors:{}".format(batch_compute.provisioning_errors))

print("Using Batch compute:{}".format(batch_compute.cluster_resource_id))
```

> [!WARNING]
> Не создавайте несколько одновременных вложений в один пакет Azure из рабочей области. Каждое новое вложение приведет к нарушению предыдущих существующих вложений.

### <a name="azure-databricks"></a><a id="databricks"></a>Azure Databricks

Azure Databricks — это среда, которая лежит в основе Apache Spark в облаке Azure. Ее можно использовать как целевой объект вычислений с помощью конвейера Машинного обучения Azure.

Прежде чем ее использовать, создайте рабочую область Azure Databricks. Чтобы создать ресурс рабочей области, см. статью [Запуск задания Spark в Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) документе.

Введите следующие данные, чтобы вложить Azure Databricks в качестве целевого объекта вычислений.

* __Имя для вычислений для кирпичей__. имя, которое вы хотите назначить этому ресурсу вычислений.
* __Имя рабочей области кирпичей__данных: имя рабочей области Azure Databricks.
* __Маркер доступа к модулям__. маркер доступа используется для проверки подлинности в Azure Databricks. Чтобы создать маркер доступа, см. документ [Проверка подлинности](https://docs.azuredatabricks.net/dev-tools/api/latest/authentication.html).

В следующем примере кода показано, как присоединить Azure Databricks в качестве целевого объекта вычислений с Машинное обучение Azureным пакетом SDK (__Рабочая область "кирпичы" должна находиться в той же подписке, что и Рабочая область AML__):

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

Более подробный пример см. в [примере записной книжки](https://aka.ms/pl-databricks) на сайте GitHub.

> [!WARNING]
> Не создавайте несколько одновременных вложений для одного и того же Azure Databricks из рабочей области. Каждое новое вложение приведет к нарушению предыдущих существующих вложений.

### <a name="azure-data-lake-analytics"></a><a id="adla"></a>Аналитика озера данных Azure

Azure Data Lake Analytics — это платформа аналитики больших данных в облаке Azure. Ее можно использовать как целевой объект вычислений с помощью конвейера Машинного обучения Azure.

Прежде чем ее использовать, создайте учетную запись Azure Data Lake Analytics. Чтобы создать этот ресурс, см. статью [Начало работы с Azure Data Lake Analytics с помощью портала Azure](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal).

Чтобы вложить Azure Data Lake Analytics в качестве целевого объекта вычислений, используйте пакет SDK машинного обучения Azure и укажите следующие сведения.

* __Имя вычисления__. Имя, которое вы хотите назначить как вычислительный ресурс.
* __Группа ресурсов__. Группа ресурсов, содержащая учетную запись Data Lake Analytics.
* __Имя учетной записи__. имя учетной записи Data Lake Analytics.

Следующий код демонстрирует, как вложить Data Lake Analytics в качестве целевого объекта вычислений.

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

Более подробный пример см. в [примере записной книжки](https://aka.ms/pl-adla) на сайте GitHub.

> [!WARNING]
> Не создавайте несколько одновременных вложений для одного и того же ADLA из рабочей области. Каждое новое вложение приведет к нарушению предыдущих существующих вложений.

> [!TIP]
> Конвейеры машинного обучения Azure работают только с хранимыми данными в хранилище данных учетной записи Data Lake Analytics по умолчанию. Если данные, с которыми необходимо работать, находятся в хранилище, отличном от хранилища по умолчанию, можно использовать [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py&preserve-view=true) для копирования данных перед обучением.

## <a name="notebook-examples"></a>Примеры записных книжек

Посмотрите примеры обучения различных целевых объектов вычислений в следующих записных книжках.
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Дальнейшие действия

* Используйте ресурс вычислений для [настройки и отправки обучающего запуска](how-to-set-up-training-targets.md).
* [Руководство. по обучению модели классификации изображений с помощью Службы машинного обучения Azure](tutorial-train-models-with-aml.md). В нем используется управляемый целевой объект вычислений для обучения модели.
* Узнайте, как [эффективно настроить гиперпараметры](how-to-tune-hyperparameters.md) для создания улучшенных моделей.
* После обучения модели узнайте о [способах и расположениях развертывания моделей](how-to-deploy-and-where.md).
* [Использование Машинного обучения Azure с виртуальными сетями Microsoft Azure](how-to-enable-virtual-network.md)
