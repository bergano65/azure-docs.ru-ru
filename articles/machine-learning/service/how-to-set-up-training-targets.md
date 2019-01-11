---
title: Целевые объекты вычислений для обучения моделей
titleSuffix: Azure Machine Learning service
description: Настройте среды обучения (целевые объекты вычислений) для обучения моделей машинного обучения. Вы можете с легкостью переключаться между средами обучения. Начните обучение на локальном компьютере. Если вам потребуется увеличить масштаб, переключитесь на облачный целевой объект вычислений.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: larryfr
manager: cgronlun
ms.service: machine-learning
ms.component: core
ms.topic: article
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 9037f6d7602f186bc30e55acbc050280bca134ee
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53794470"
---
# <a name="set-up-compute-targets-for-model-training"></a>Настройка целевых объектов вычислений для обучения моделей

С помощью Службы машинного обучения Azure можно обучать модель, используя разные вычислительные ресурсы. Эти ресурсы называются __целевыми объектами вычислений__ и могут быть локальными или облачными. В этой статье вы узнаете о поддерживаемых целевых объектах вычислений и способах их использования.

Целевой объект вычислений — это ресурс, на котором выполняется сценарий обучения или размещается модель, если она развертывается в качестве веб-службы. Для создания целевых объектов вычислений и управления ими можно использовать пакет SDK для Машинного обучения Azure, портал Azure или Azure CLI. Если у вас есть целевые объекты вычислений, созданные другой службой (например, кластером Azure HDInsight), вы можете подключить их к своей рабочей области Службы машинного обучения Azure.

Существуют три основные категории целевых объектов вычислений, которые поддерживает Машинное обучение Azure.

* __Локальные среды__. Ваш локальный компьютер или облачная виртуальная машина, используемая в качестве среды разработки и проведения экспериментов. 
* __Управляемые вычислительные среды__. Вычислительная среда Машинного обучения Azure — это вычислительное предложение, которое находится под управлением Службы машинного обучения Azure. Она позволяет легко создавать одно- или многоузловые вычислительные среды для обучения, тестирования и пакетного получения выводов.
* __Подключенные вычислительные среды__. Можно также использовать собственную облачную вычислительную среду Azure и подключить ее к Машинному обучению Azure. Дополнительные сведения о поддерживаемых типах вычислительных сред и способах их использования приведены в следующих разделах.


## <a name="supported-compute-targets"></a>Поддерживаемые целевые объекты вычислений

Служба машинного обучения Azure по-разному поддерживает различные целевые объекты вычислений. Жизненный цикл разработки обычной модели начинается с разработки и проведения экспериментов с небольшим объемом данных. На этом этапе мы советуем использовать локальную среду, например локальный компьютер или облачную виртуальную машину. По мере перехода к обучению на больших наборах данных или распределенному обучению используйте Вычислительную среду Машинного обучения Azure, чтобы создать одно- или многоузловой кластер, который автоматически масштабируется при каждой отправке запроса на запуск. Вы также можете подключить собственный вычислительный ресурс, хотя поддержка сценариев может отличаться. Подробнее об этом рассказывается в следующей таблице.

|Целевой объект вычисления| Ускорение GPU | Автоматическая регистрация.<br/> Настройка гиперпараметров | Автоматическая регистрация.</br> машинное обучение; | Поддержка конвейеров|
|----|:----:|:----:|:----:|:----:|
|[Локальный компьютер](#local)| Возможно | &nbsp; | ✓ | &nbsp; |
|[Вычислительная среда Машинного обучения Azure](#amlcompute)| ✓ | ✓ | ✓ | ✓ |
|[Удаленная виртуальная машина](#vm) | ✓ | ✓ | ✓ | ✓ |
|[Azure Databricks](#databricks)| &nbsp; | &nbsp; | ✓ | ✓[*](#pipeline-only) |
|[Аналитика озера данных Azure](#adla)| &nbsp; | &nbsp; | &nbsp; | ✓[*](#pipeline-only) |
|[Azure HDInsight](#hdinsight)| &nbsp; | &nbsp; | &nbsp; | ✓ |

> [!IMPORTANT]
> <a id="pipeline-only"></a>__*__ _Azure Databricks и Azure Data Lake Analytics можно использовать только в конвейере_.<br/>
> Дополнительные сведения о конвейерах см. в статье [Создание конвейеров машинного обучения с помощью Службы машинного обучения Azure](concept-ml-pipelines.md).
>
> В рабочей области нужно создать Вычислительную среду Машинного обучения Azure. Невозможно подключить к рабочей области существующие экземпляры.
>
> Прочие целевые объекты вычислений необходимо создать вне Машинного обучения Azure, а затем подключить их к рабочей области.
>
> Некоторые целевые объекты вычислений используют образы контейнеров Docker при обучении модели. Базовый образ GPU необходимо использовать только в службах Microsoft Azure. Для обучения модели используются следующие службы:
> * Вычислительная среда Машинного обучения Azure;
> * Служба Azure Kubernetes
> * Виртуальная машина для обработки и анализа данных (DSVM) под управлением Windows.

## <a name="workflow"></a>Рабочий процесс

Рабочий процесс для разработки и развертывания модели с помощью машинного обучения Azure включает следующие шаги:

1. Создание сценариев обучения машинного обучения на Python.
1. Создание и настройка целевого объекта вычислений либо подключение уже существующего объекта.
1. Отправка сценариев обучения в целевой объект вычислений.
1. Проверка результатов для подбора оптимальной модели.
1. Регистрация модели в реестре моделей.
1. Развертывание модели.

> [!NOTE]
> Сценарий обучения не привязывается к определенному целевому объекту вычислений. Обучение можно начать на локальном компьютере, а затем переключить целевые объекты вычислений, не переписывая весь сценарий.
> 
> Всякий раз, когда целевой объект вычислений связывается с рабочей областью путем создания управляемой вычислительной среды или подключения существующей вычислительной среды, указывайте имя этой среды. Имя должно содержать от двух до 16 знаков.

Для переключения с одного целевого объекта вычислений на другой необходимо создать [конфигурацию запуска](concept-azure-machine-learning-architecture.md#run-configuration). Конфигурация запуска определяет способ выполнения сценария в целевом объекте вычислений.

## <a name="training-scripts"></a>Сценарии обучения

При запуске обучения создается моментальный снимок каталога, содержащего сценарии обучения, который отправляется на целевой объект вычислений. Дополнительные сведения см. в разделе о [моментальных снимках](concept-azure-machine-learning-architecture.md#snapshot).

## <a id="local"></a>Локальный компьютер

В процессе локального обучения можно использовать для отправки операции обучения пакет SDK. Для обучения можно использовать среду, управляемую пользователем, или среду, управляемую системой.

### <a name="user-managed-environment"></a>Среда, управляемая пользователем

При использовании управляемой пользователем среды убедитесь, что все необходимые пакеты доступны в среде Python, где вы выполняете сценарий. Следующий фрагмент кода представляет собой пример настройки обучения для среды, управляемой пользователем:

```python
from azureml.core.runconfig import RunConfiguration

# Edit a run configuration property on the fly.
run_config_user_managed = RunConfiguration()

run_config_user_managed.environment.python.user_managed_dependencies = True

# Choose a specific Python environment by pointing to a Python path. For example:
# run_config.environment.python.interpreter_path = '/home/ninghai/miniconda3/envs/sdk2/bin/python'
```

  
### <a name="system-managed-environment"></a>Среда, управляемая системой

В средах, управляемых системой, зависимостями управляет Conda. Conda создает файл с именем **conda_dependencies.yml**, содержащий список зависимостей. После этого можно попросить систему создать новую среду Conda и выполнять сценарии в ней. Среды, управляемые системой, можно повторно использовать позднее при условии, что файл conda_dependencies.yml останется неизменным. 

Первоначальная настройка новой среды может потребовать несколько минут в зависимости от размера необходимых зависимостей. Следующий фрагмент кода демонстрирует создание среды, управляемой системой, которая зависит от scikit-learn:

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

run_config_system_managed = RunConfiguration()

run_config_system_managed.environment.python.user_managed_dependencies = False
run_config_system_managed.auto_prepare_environment = True

# Specify the conda dependencies with scikit-learn

run_config_system_managed.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
```

## <a id="amlcompute"></a>Вычислительная среда Машинного обучения Azure

Вычислительная среда Машинного обучения Azure — это управляемая вычислительная инфраструктура, которая позволяет пользователю легко создавать одно- и многоузловые вычислительные среды. Она создается в регионе вашей рабочей области и является ресурсом, который можно использовать совместно с другими пользователями в этой рабочей области. Вычислительная среда автоматически масштабируется при отправке задания, а также может быть размещена в виртуальной сети Azure. Она выполняется в контейнерной среде, упаковывая зависимости вашей модели в контейнер Docker.

Вычислительную среду Машинного обучения Azure можно использовать для распространения процесса обучения в кластере вычислительных узлов на основе ЦП или GPU, размещенном в облаке. Дополнительные сведения о размерах виртуальных машин, которые содержат GPU, см. в статье [Размеры виртуальных машин, оптимизированных для GPU](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

> [!NOTE]
> Вычислительная среда Машинного обучения Azure имеет ограничения по умолчанию, такие как количество ядер, которые могут быть выделены. Дополнительные сведения см. в статье [Управление квотами для ресурсов Azure и их запрашивание](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas).

Можно создать Вычислительную среду Машинного обучения Azure по запросу при запланированном запуске или создать ее в качестве постоянного ресурса.

### <a name="run-based-creation"></a>Создание при запуске

Вы можете создать Вычислительную среду Машинного обучения Azure в качестве целевого объекта вычислений во время выполнения. Вычислительная среда создается автоматически для вашего запуска, а ее масштаб увеличивается до числа узлов, определенного параметром **max_nodes** в конфигурации запуска. Эта среда автоматически удаляется после завершения выполнения запуска.

> [!IMPORTANT]
> Функция создания Вычислительной среды Машинного обучения Azure при запуске в настоящее время находится в предварительной версии. Не применяйте создание при запуске, если вы используете автоматическую настройку гиперпараметров или автоматическое машинное обучение. Чтобы использовать настройку гиперпараметров или автоматическое машинное обучение, создайте Вычислительную среду Машинного обучения Azure перед отправкой запроса на запуск.

```python
from azureml.core.compute import ComputeTarget, AmlCompute

# First, list the supported VM families for Azure Machine Learning Compute
AmlCompute.supported_vmsizes()

from azureml.core.runconfig import RunConfiguration

# Create a new runconfig object
run_config = RunConfiguration()

# Signal that you want to use AmlCompute to execute the script
run_config.target = "amlcompute"

# AmlCompute is created in the same region as your workspace
# Set the VM size for AmlCompute from the list of supported_vmsizes
run_config.amlcompute.vm_size = 'STANDARD_D2_V2'

```

### <a name="persistent-compute-basic"></a>Постоянная вычислительная среда: базовая;

Постоянную Вычислительную среду Машинного обучения Azure можно многократно использовать в заданиях. Ее можно использовать совместно с другими пользователями в рабочей области, и она сохраняется после выполнения заданий.

Чтобы создать постоянный ресурс Вычислительной среды Машинного обучения Azure, необходимо указать свойства **vm_size** и **max_nodes**. Затем Машинное обучение Azure использует интеллектуальные значения по умолчанию для остальных свойств. Число узлов вычислительной среды сокращается до нуля, когда она не используется, а при необходимости для выполнения заданий создаются выделенные виртуальные машины. 

* **vm_size**. Семейство виртуальных машин узлов, создаваемых Вычислительной средой Машинного обучения Azure.
* **max_nodes**. Максимальное количество узлов для автомасштабирования при запуске задания в Вычислительной среде Машинного обучения Azure.

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that the cluster doesn't already exist
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           max_nodes=4)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)

```

### <a name="persistent-compute-advanced"></a>Постоянная вычислительная среда: Расширенная

При создании Вычислительно среды Машинного обучения Azure можно также настроить несколько дополнительных свойств. Эти свойства позволяют создать постоянный кластер фиксированного размера, который может размещаться в существующей виртуальной сети Azure в вашей подписке.

Вместе со свойствами **vm_size** и **max_nodes** можно использовать следующие свойства:

* **min_nodes**. Минимальное количество узлов, до которого можно уменьшить масштаб при запуске задания в Вычислительной среде Машинного обучения Azure. Минимальным значением по умолчанию является ноль (0) узлов.
* **vm_priority**. Тип виртуальной машины, используемой при создании ресурса Вычислительной среды Машинного обучение Azure. Выберите между типом **dedicated** (выделенная; по умолчанию) и **lowpriority** (низкоприоритетная). Виртуальные машины с низким приоритетом используют избыточную емкость в Azure. Эти виртуальные машины дешевле, однако при их использовании запуски могут быть замещены.
* **idle_seconds_before_scaledown**. Время простоя после выполнения запуска и до автомасштабирования до количества **min_nodes**. Время простоя по умолчанию — 120 секунд.
* **vnet_resourcegroup_name**. Группа ресурсов __существующей__ виртуальной сети. В этой виртуальной сети создается Вычислительная среда Машинного обучения Azure.
* **vnet_name**. Имя виртуальной сети. Виртуальная сеть должна размещаться в одном регионе с вашей рабочей областью Машинного обучения Azure.
* **subnet_name**. Имя подсети в виртуальной сети. Ресурсам Вычислительной среды Машинного обучения Azure будут назначаться IP-адреса из этого диапазона подсети.

> [!TIP]
> При создании постоянного ресурса Вычислительной среды Машинного обучения Azure можно обновить такие свойства, как число **min_nodes** или **max_nodes**. Чтобы обновить свойство, вызовите функцию `update()` для него.

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that the cluster doesn't already exist 
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           vm_priority='lowpriority',
                                                           min_nodes=2,
                                                           max_nodes=4,
                                                           idle_seconds_before_scaledown='300',
                                                           vnet_resourcegroup_name='<my-resource-group>',
                                                           vnet_name='<my-vnet-name>',
                                                           subnet_name='<my-subnet-name>')
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)

```


## <a id="vm"></a>Удаленная виртуальная машина

Машинное обучение Azure также поддерживает использование собственных вычислительных ресурсов и подключение их к рабочей области. К такому типу ресурсов относится произвольная удаленная виртуальная машина, если она доступна из Службы машинного обучения Azure. Ресурсом может быть виртуальная машина Azure, удаленный или локальный сервер в вашей организации. В частности, указав IP-адрес и учетные данные (имя пользователя и пароль или ключ SSH), можно использовать любую доступную виртуальную машину для удаленного запуска.
Можно использовать системную среду Conda, существующую среду Python или контейнер Docker. При выполнении с использованием контейнера Docker на виртуальной машине должен быть запущен модуль Docker. Эта функция удаленной виртуальной машины особенно удобна в случаях, когда вам нужна облачная среда и среда для экспериментов, которая более гибкая, чем локальный компьютер.

> [!TIP]
> Используйте DSVM в качестве избранной виртуальной машины для этого сценария. Эта виртуальная машина представляет собой предварительно настроенную среду разработки с поддержкой обработки и анализа данных и искусственного интеллекта (ИИ) в Azure. Она предоставляет проверенный набор инструментов и платформ для всего жизненного цикла разработки решений машинного обучения. Дополнительных сведения об использовании DSVM со Службой машинного обучения Azure см. в статье [Настройка среды разработки для Машинного обучения Azure](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-environment#dsvm).

> [!WARNING]
> Машинное обучение Azure поддерживает только виртуальные машины под управлением Ubuntu. Поэтому создаваемая виртуальная или существующая виртуальная машина, которую вы выбираете, должна работать под управлением Ubuntu.

На следующих шагах пакет SDK используется, чтобы настроить DSVM как цель обучения:

1. Чтобы присоединить имеющуюся виртуальную машину как целевой объект вычислений, необходимо указать ее полное доменное имя, имя входа и пароль. В приведенном примере замените \<fqdn> на общедоступное полное доменное имя виртуальной машины или общедоступный IP-адрес. Замените \<username> и \<password> именем пользователя и паролем SSH для виртуальной машины.

    ```python
    from azureml.core.compute import RemoteCompute, ComputeTarget
    
    # Create the compute config
    attach_config = RemoteCompute.attach_configuration(address = "ipaddress",
                                                       ssh_port=22,
                                                       username='<username>',
                                                       password="<password>")

    # If you use SSH instead of a password, use this code:
    #                                                  ssh_port=22,
    #                                                  username='<username>',
    #                                                  password=None,
    #                                                  private_key_file="path-to-file",
    #                                                  private_key_passphrase="passphrase")

    # Attach the compute target
    compute = ComputeTarget.attach(ws, "attach-dsvm", attach_config)

    compute.wait_for_completion(show_output=True)
    ```

1. Создайте конфигурацию для целевого объекта вычислений DSVM. Для создания и настройки среды обучения на DSVM используются Docker и Conda.

    ```python
    from azureml.core.runconfig import RunConfiguration
    from azureml.core.conda_dependencies import CondaDependencies

    # Load into memory the cpu-dsvm.runconfig file created in the previous attach operation
    run_config = RunConfiguration(framework = "python")

    # Set the compute target to the Linux DSVM
    run_config.target = compute_target_name

    # Use Docker in the remote VM
    run_config.environment.docker.enabled = True

    # Use the CPU base image
    # To use GPU in DSVM, you must also use the GPU base Docker image "azureml.core.runconfig.DEFAULT_GPU_IMAGE"
    run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE
    print('Base Docker image is:', run_config.environment.docker.base_image)

    # Ask the system to provision a new conda environment based on the conda_dependencies.yml file
    run_config.environment.python.user_managed_dependencies = False

    # Prepare the Docker and conda environment automatically when they're used for the first time
    run_config.prepare_environment = True

    # Specify the CondaDependencies object
    run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])

    ```

## <a id="databricks"></a>Azure Databricks

Azure Databricks — это среда, которая лежит в основе Apache Spark в облаке Azure. Среду можно использовать как целевой объект вычислений при обучении моделей с помощью конвейера Машинного обучения Azure.

> [!IMPORTANT]
> Целевой объект вычислений Azure Databricks можно использовать только в конвейере машинного обучения.
>
> Перед тем как его использовать для обучения модели, необходимо создать рабочую область Azure Databricks. Чтобы создать этот ресурс, обратитесь к статье [Краткое руководство. Запуск задания Spark в Azure Databricks с помощью портала Azure](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal).

Чтобы вложить Azure Databricks в качестве целевого объекта вычислений, используйте пакет SDK машинного обучения Azure и укажите следующие сведения.

* __Имя Вычислительной среды__. Имя, которое нужно присвоить этому вычислительному ресурсу.
* __Рабочая область Databricks__. Имя рабочей области Azure Databricks.
* __Маркер доступа__. Маркер доступа, который используется для аутентификации в Azure Databricks. Чтобы создать маркер доступа, ознакомьтесь с документом о [проверке подлинности](https://docs.azuredatabricks.net/api/latest/authentication.html).

Следующий код демонстрирует, как вложить Azure Databricks в качестве целевого объекта вычислений.

```python
databricks_compute_name = os.environ.get("AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get("AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get("AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get("AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create the attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group = databricks_resource_group,
                                                           workspace_name = databricks_workspace_name,
                                                           access_token = databricks_access_token)
    databricks_compute = ComputeTarget.attach(
             ws,
             databricks_compute_name,
             attach_config
         )
    
    databricks_compute.wait_for_completion(True)
```

## <a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics — это платформа аналитики больших данных в облаке Azure. Ее можно использовать как целевой объект вычислений при обучении моделей с помощью конвейера Машинного обучения Azure.

> [!IMPORTANT]
> Целевой объект вычислений Azure Data Lake Analytics можно использовать только в конвейере машинного обучения.
>
> Перед использованием его для обучения модели необходимо создать учетную запись Azure Data Lake Analytics. Чтобы создать этот ресурс, обратитесь к статье [Начало работы с Azure Data Lake Analytics с помощью портала Azure](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal).

Чтобы вложить Azure Data Lake Analytics в качестве целевого объекта вычислений, используйте пакет SDK машинного обучения Azure и укажите следующие сведения.

* __Имя Вычислительной среды__. Имя, которое нужно присвоить этому вычислительному ресурсу.
* __Группа ресурсов.__ Группа ресурсов, содержащая учетную запись Data Lake Analytics.
* __Имя учетной записи__. Имя учетной записи Data Lake Analytics.

Следующий код демонстрирует, как вложить Data Lake Analytics в качестве целевого объекта вычислений.

```python
adla_compute_name = os.environ.get("AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get("AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get("AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    
    # Create the attach config
    attach_config = AdlaCompute.attach_configuration(resource_group = adla_resource_group,
                                                     account_name = adla_account_name)
    # Attach the ADLA
    adla_compute = ComputeTarget.attach(
             ws,
             adla_compute_name,
             attach_config
         )
    
    adla_compute.wait_for_completion(True)
```

> [!TIP]
> Конвейеры Машинного обучения Azure работают только с данными, размещенными в хранилище данных учетной записи Data Lake Analytics по умолчанию. Если необходимые данные не находятся в хранилище по умолчанию, используйте операцию [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py), чтобы скопировать данные перед обучением модели.

## <a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight — это популярная платформа для анализа больших данных. Она предоставляет Apache Spark, который можно использовать для обучения модели.

> [!IMPORTANT]
> Прежде чем использовать кластер HDInsight для обучения модели, его необходимо создать. Инструкции по созданию Spark в кластере HDInsight см. в статье [Краткое руководство. Создание кластера Apache Spark в HDInsight с помощью шаблона](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql).
>
> При создании кластера необходимо указать имя пользователя SSH и пароль. Запишите эти значения — они вам потребуются при использовании HDInsight в качестве целевого объекта вычислений.
>
> После создания кластера его полное доменное имя будет таким: \<имя кластера>.azurehdinsight.net, где \<имя кластера> — это имя, которое вы указали для кластера. Этот адрес полного доменного имени (или общедоступный IP-адрес кластера) нужен, чтобы использовать кластер в качестве целевого объекта вычислений.

Чтобы настроить HDInsight как целевой объект вычислений, необходимо указать полное доменное имя, а также имя пользователя и пароль для кластера HDInsight. В следующем примере пакет SDK используется для присоединения кластера к рабочей области. В приведенном примере замените \<fqdn> на общедоступное полное доменное имя кластера или общедоступный IP-адрес. Замените \<username> и \<password> именем пользователя и паролем SSH для кластера.

> [!NOTE]
> Чтобы узнать полное доменное имя для кластера, перейдите на портал Azure и выберите свой кластер HDInsight. Полное доменное имя отображается в записи __URL-адрес__ в разделе __Обзор__. Чтобы получить полное доменное имя, удалите префикс https:\// в самом начале записи.

![Получение полного доменного имени для кластера HDInsight на портале Azure](./media/how-to-set-up-training-targets/hdinsight-overview.png)

```python
from azureml.core.compute import HDInsightCompute, ComputeTarget

try:
    # Attach an HDInsight cluster as a compute target
    attach_config = HDInsightCompute.attach_configuration(address = "fqdn-or-ipaddress",
                                                          ssh_port = 22,
                                                          username = "username",
                                                          password = None, #if using ssh key
                                                          private_key_file = "path-to-key-file",
                                                          private_key_phrase = "key-phrase")
    compute = ComputeTarget.attach(ws, "myhdi", attach_config)
except UserErrorException as e:
    print("Caught = {}".format(e.message))
    print("Compute config already attached.")

# Configure the HDInsight run
# Load the runconfig object from the myhdi.runconfig file generated in the previous attach operation
run_config = RunConfiguration.load(project_object = project, run_config_name = 'myhdi')

# Ask the system to prepare the conda environment automatically when it's used for the first time
run_config.auto_prepare_environment = True
```

## <a name="submit-training-runs"></a>Отправка запусков на выполнение обучения

Существует два способа отправки запуска на выполнения обучения:

* с использованием объекта `ScriptRunConfig`;
* с использованием объекта `Pipeline`.

> [!IMPORTANT]
> Целевые объекты вычислений Azure Databricks и Azure Datalake Analytics могут использоваться только в конвейере.
>
> Локальный целевой объект вычислений не может использоваться в конвейере.

### <a name="scriptrunconfig-object"></a>Объект ScriptRunConfig

Шаблон кода для отправки запуска на выполнение обучения с использованием объекта `ScriptRunConfig` одинаковый для всех типов целевых объектов вычислений:

1. Создайте объект `ScriptRunConfig`, используя конфигурацию запуска для целевого объекта вычислений.
1. Выполните прогон.
1. Дождитесь завершения прогона.

В следующем примере используется конфигурация для локального целевого объекта вычислений, управляемого системой, который вы создали ранее:

```python
src = ScriptRunConfig(source_directory = script_folder, script = 'train.py', run_config = run_config_system_managed)
run = exp.submit(src)
run.wait_for_completion(show_output = True)
```


### <a name="pipeline-object"></a>Объект конвейера

Шаблон кода для отправки запуска на выполнение обучения с использованием объекта `Pipeline` одинаковый для всех типов целевых объектов вычислений:

1. Для ресурса вычисления добавьте шаг в объекте `Pipeline`.
1. С помощью конвейера отправьте запуск на выполнение.
1. Дождитесь завершения прогона.

В следующем примере используется созданный ранее целевой объект вычислений Azure Databricks.

```python
dbStep = DatabricksStep(
    name="databricksmodule",
    inputs=[step_1_input],
    outputs=[step_1_output],
    num_workers=1,
    notebook_path=notebook_path,
    notebook_params={'myparam': 'testparam'},
    run_name='demo run name',
    databricks_compute=databricks_compute,
    allow_reuse=False
)

# List of steps to run
steps = [dbStep]
pipeline = Pipeline(workspace=ws, steps=steps)
pipeline_run = Experiment(ws, 'Demo_experiment').submit(pipeline)
pipeline_run.wait_for_completion()
```

Дополнительные сведения о конвейерах машинного обучения см. в статье [Создание конвейеров машинного обучения с помощью Службы машинного обучения Azure](concept-ml-pipelines.md).

Ознакомьтесь с примерами записных книжек Jupyter, которые демонстрируют обучение модели с помощью конвейера, по адресу [https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline).

## <a name="access-computes-in-the-azure-portal"></a>Доступ к средам вычислений на портале Azure

Вы можете получить доступ к целевым объектам вычислений, связанным с вашей рабочей областью, на портале Azure. 

### <a name="view-compute-targets"></a>Просмотр целевых объектов вычислений

Чтобы просмотреть целевые объекты вычислений для своей рабочей области, сделайте следующее:

1. Перейдите на [портал Azure](https://portal.azure.com) и откройте свою рабочую область.
1. В разделе __Приложения__ выберите __Вычисления__.

    ![Просмотр целевых объектов вычислений](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)

### <a name="create-a-compute-target"></a>Создание целевого объекта вычислений

Выполните указанные выше шаги, чтобы просмотреть список целевых объектов вычислений. Затем выполните следующие действия, чтобы создать целевой объект вычислений.

1. Щелкните значок плюса (+), чтобы добавить целевой объект вычислений.

    ![Добавление целевого объекта вычислений](./media/how-to-set-up-training-targets/add-compute-target.png)

1. Введите имя для целевого объекта вычислений.
1. Выберите **Вычислительная среда Машинного обучения** в качестве типа вычислительной среды для среды __обучения__.

    > [!IMPORTANT]
    > Вычислительную среду Машинного обучения Azure можно создать только в качестве управляемого вычислительного ресурса для обучения.

1. Заполните форму. Введите значения для обязательных свойств, в частности, укажите **семейство виртуальных машин** и **максимальное количество узлов**, до которого можно развернуть вычислительный объект. 
1. Нажмите кнопку __Создать__.
1. Чтобы увидеть состояние операции создания, выберите целевой объект вычислений из списка.

    ![Выбор целевого объекта вычислений, чтобы просмотреть состояние операции создания](./media/how-to-set-up-training-targets/View_list.png)

1. Ниже приведены сведения о выбранном целевом объекте вычислений.

    ![Просмотр сведений о целевом объекте вычислений](./media/how-to-set-up-training-targets/compute-target-details.png)

Теперь вы можете отправить запуск на выполнение для целевых объектов вычислений, как было описано ранее.


### <a name="reuse-existing-compute-targets"></a>Повторное использование существующих целевых объектов вычислений

Выполните описанные ранее шаги, чтобы просмотреть список целевых объектов вычислений. Затем выполните следующие действия, чтобы создать целевой объект вычислений:

1. Щелкните значок плюса (+), чтобы добавить целевой объект вычислений.
1. Введите имя для целевого объекта вычислений.
1. Выберите тип вычислительной среды, который нужно подключить для __обучения__.

    > [!IMPORTANT]
    > Не все типы вычислительных сред можно подключить с помощью портала Azure.
    > Типы вычислительных сред, которые в настоящее время можно подключить для обучения:
    >
    > * Удаленная виртуальная машина.
    > * Azure Databricks
    > * Аналитика озера данных Azure
    > * Azure HDInsight

1. Заполните форму и укажите значения для обязательных свойств.

    > [!NOTE]
    > Корпорация Майкрософт рекомендует использовать ключи SSH, так как они безопаснее, чем пароли. Пароли подвержены атакам методом подбора. Ключи SSH задействуют криптографические подписи. Сведения о создании ключей SSH для использования на Виртуальных машинах Azure см. в следующих документах:
    >
    > * [Создание и использование ключей SSH в Linux или macOS](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Создание и использование ключей SSH в Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. Выберите __Подключить__.
1. Чтобы просмотреть состояние операции подключения, выберите целевой объект вычислений из списка.

Теперь вы можете отправить запуск на выполнение на этих целевых объектах вычислений, как было описано ранее.

## <a name="notebook-examples"></a>Примеры записных книжек

Ознакомьтесь с примерами записных книжек в следующих расположениях:

* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Дополнительная информация

* [Справка по пакету SDK для машинного обучения Azure](https://aka.ms/aml-sdk)
* [Руководство. Обучение модели](tutorial-train-models-with-aml.md)
* [Где следует развертывать модели](how-to-deploy-and-where.md)
* [Создание конвейеров машинного обучения с помощью службы машинного обучения Azure](concept-ml-pipelines.md)
