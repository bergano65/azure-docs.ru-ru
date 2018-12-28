---
title: Целевые объекты вычислений для обучения моделей
titleSuffix: Azure Machine Learning service
description: Настройте среды обучения (целевые объекты вычислений) для обучения моделей машинного обучения. Среды обучения можно легко переключать. Начните обучение на локальном компьютере, а затем, если вам потребуется увеличить масштаб, переключитесь на облачный целевой объект вычислений. Databricks
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
ms.openlocfilehash: 664d56daf3e70e2e5699d0c07331c466c60e06c5
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53338868"
---
# <a name="set-up-compute-targets-for-model-training"></a>Настройка целевых объектов вычислений для обучения моделей

С помощью Службы машинного обучения Azure можно обучать модель, используя разные вычислительные ресурсы. Эти ресурсы называются __целевыми объектами вычислений__ и могут быть локальными или облачными. Из этого документа вы узнаете о поддерживаемых целевых объектах вычислений и способах их использования.

Целевой объект вычислений — это ресурс, на котором выполняется сценарий обучения или размещается модель, если она развертывается в качестве веб-службы. Для создания целевых объектов вычислений и управления ими можно использовать пакет SDK для Машинного обучения Azure, портал Azure или Azure CLI. Если у вас есть целевые объекты вычислений, созданные другой службой (например, кластером HDInsight), вы можете подключить их к своей рабочей области Службы машинного обучения Azure.

Существуют три основные категории целевых объектов вычислений, которые поддерживает Машинное обучение Azure.

* __Локальные среды__. Ваш локальный компьютер или облачная виртуальная машина, используемая в качестве среды разработки и проведения экспериментов. 

* __Управляемые вычислительные среды__. Вычислительная среда Машинного обучения Azure — это вычислительное решение, которое находится под управлением Службы машинного обучения Azure. Она позволяет легко создавать одно- или многоузловые вычислительные среды для обучения, тестирования и пакетного получения выводов.

* __Подключенные вычислительные среды__. Можно также использовать собственную облачную вычислительную среду Azure и подключить ее к Машинному обучению Azure. Далее приведены дополнительные сведения о типах поддерживаемых вычислительных сред и способах их использования.


## <a name="supported-compute-targets"></a>Поддерживаемые целевые объекты вычислений

Служба машинного обучения Azure по-разному поддерживает различные целевые объекты вычислений. Жизненный цикл разработки обычной модели начинается с разработки и экспериментов с небольшим объемом данных. На этом этапе мы рекомендуем использовать локальную среду. Например, ваш локальный компьютер или облачную виртуальную машину. По мере перехода к обучению на больших наборах данных или распределенному обучению мы рекомендуем использовать Вычислительную среду Машинного обучения Azure, чтобы создать одно- или многоузловой кластер, который автоматически масштабируется при каждой отправке запроса на запуск. Можно также подключить собственный вычислительный ресурс, хотя не все сценарии могут поддерживаться. Подробнее об этом рассказывается ниже.

|Целевой объект вычисления| Ускорение GPU | Автоматическая настройка гиперпараметров | Автоматическое машинное обучение | Поддержка конвейеров|
|----|:----:|:----:|:----:|:----:|
|[Локальный компьютер](#local)| Возможно | &nbsp; | ✓ | &nbsp; |
|[Вычислительная среда Машинного обучения Azure](#amlcompute)| ✓ | ✓ | ✓ | ✓ |
|[Удаленная виртуальная машина](#vm) | ✓ | ✓ | ✓ | ✓ |
|[Azure Databricks](#databricks)| &nbsp; | &nbsp; | ✓ | ✓[*](#pipeline-only) |
|[Аналитика озера данных Azure](#adla)| &nbsp; | &nbsp; | &nbsp; | ✓[*](#pipeline-only) |
|[Azure HDInsight](#hdinsight)| &nbsp; | &nbsp; | &nbsp; | ✓ |

> [!IMPORTANT]
> <a id="pipeline-only"></a>__*__ Azure Databricks и Azure Data Lake Analytics можно использовать __только__ в конвейере. Дополнительные сведения о конвейерах см. в статье [Конвейеры и служба "Машинное обучение Azure"](concept-ml-pipelines.md).

> [!IMPORTANT]
> Вычислительная среда Машинного обучения Azure должна быть создана в рабочей области. Невозможно подключить к рабочей области существующие экземпляры.
>
> Прочие целевые объекты вычислений необходимо создать вне Машинного обучения Azure, а затем подключить их к рабочей области.

> [!NOTE]
> Некоторые целевые объекты вычислений используют образы контейнеров Docker при обучении модели. Базовый образ GPU необходимо использовать только в службах Microsoft Azure. Для обучения модели используются следующие службы:
>
> * Вычислительная среда Машинного обучения Azure;
> * Служба Azure Kubernetes;
> * Виртуальная машина для обработки и анализа данных.

## <a name="workflow"></a>Рабочий процесс

Рабочий процесс для разработки и развертывания модели с помощью машинного обучения Azure включает следующие шаги:

1. Создание сценариев обучения машинного обучения на Python.
1. Создание и настройка целевого объекта вычислений либо подключение уже существующего объекта.
1. Отправка сценариев обучения в целевой объект вычислений.
1. Проверка результатов для подбора оптимальной модели.
1. Регистрация модели в реестре моделей.
1. Развертывание модели.

> [!IMPORTANT]
> Сценарий обучения не привязывается к определенному целевому объекту вычислений. Обучение можно начать на локальном компьютере, а затем переключить целевые объекты вычислений, не переписывая весь сценарий.

> [!TIP]
> Всякий раз, когда целевой объект вычислений связывается с рабочей областью путем создания управляемой вычислительной среды или подключения существующей вычислительной среды, необходимо указать имя этой среды. Оно может содержать от 2 до 16 знаков.

Для переключения с одного целевого объекта вычислений на другой необходимо создать [конфигурацию запуска](concept-azure-machine-learning-architecture.md#run-configuration). Конфигурация запуска определяет способ выполнения сценария в целевом объекте вычислений.

## <a name="training-scripts"></a>Сценарии обучения

При запуске обучения создается моментальный снимок каталога, содержащего сценарии обучения, и отправляется на целевой объект вычислений. Дополнительные сведения см. в разделе о [моментальных снимках](concept-azure-machine-learning-architecture.md#snapshot).

## <a id="local"></a>Локальный компьютер

В процессе локального обучения можно использовать для отправки операции обучения пакет SDK. Для обучения можно использовать среду, управляемую пользователем, или среду, управляемую системой.

### <a name="user-managed-environment"></a>Среда, управляемая пользователем

В среде, управляемой пользователем, вы сами следите за тем, чтобы все необходимые пакеты были доступны в среде Python, которую вы выбрали для выполнения сценариев. Следующий фрагмент кода представляет собой пример настройки обучения для среды, управляемой пользователем:

```python
from azureml.core.runconfig import RunConfiguration

# Editing a run configuration property on-fly.
run_config_user_managed = RunConfiguration()

run_config_user_managed.environment.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
#run_config.environment.python.interpreter_path = '/home/ninghai/miniconda3/envs/sdk2/bin/python'
```

  
### <a name="system-managed-environment"></a>Среда, управляемая системой

В средах, управляемых системой, зависимостями управляет Conda. Conda создает файл с именем `conda_dependencies.yml`, содержащий список зависимостей. После этого можно попросить систему создать новую среду Conda и выполнять сценарии в ней. Среды, управляемые системой, можно повторно использовать позднее при условии, что файлы `conda_dependencies.yml` останутся неизменны. 

Первоначальная настройка новой среды может потребовать несколько минут в зависимости от размера необходимых зависимостей. Следующий фрагмент кода демонстрирует создание среды, управляемой системой, которая зависит от scikit-learn:

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

run_config_system_managed = RunConfiguration()

run_config_system_managed.environment.python.user_managed_dependencies = False
run_config_system_managed.auto_prepare_environment = True

# Specify conda dependencies with scikit-learn

run_config_system_managed.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
```

## <a id="amlcompute"></a>Вычислительная среда Машинного обучения Azure

Вычислительная среда Машинного обучения Azure — это управляемая вычислительная инфраструктура, которая позволяет пользователю легко создавать одно- и многоузловые вычислительные среды. Она создается __в регионе вашей рабочей области__ и является ресурсом, который можно использовать совместно с другими пользователями в этой рабочей области. Она автоматически масштабируется при отправке задания, а также может быть размещена в виртуальной сети Azure. Она выполняется в __контейнерной среде__, упаковывая зависимости вашей модели в контейнер Docker.

Вычислительную среду Машинного обучения Azure можно использовать для распространения процесса обучения в кластере вычислительных узлов на основе ЦП или GPU, размещенном в облаке. Дополнительные сведения о размерах виртуальных машин, которые содержат GPU, см. в документе [Размеры виртуальных машин, оптимизированных для GPU](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

> [!NOTE]
> Вычислительная среда Машинного обучения Azure имеет ограничения по умолчанию для таких параметров, как количество ядер, которые могут быть выделены. Дополнительные сведения см. в документе [Управление квотами для ресурсов Azure и их запрашивание](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-quotas).

Можно создать Вычислительную среду Машинного обучения Azure по запросу при запланированном запуске или создать ее в качестве постоянного ресурса.

### <a name="run-based-creation"></a>Создание при запуске

Вы можете создать Вычислительную среду Машинного обучения Azure в качестве целевого объекта вычислений во время выполнения. В этом случае вычислительная среда создается автоматически для вашего запуска, а ее масштаб увеличивается до числа узлов, определенного параметром max_nodes в конфигурации запуска. По завершении запуска эта среда __автоматически удаляется__.

> [!IMPORTANT]
> Функция создания Вычислительной среды Машинного обучения Azure при запуске в настоящее время находится на этапе предварительной версии. Не применяйте создание при запуске, если вы используете настройку гиперпараметров или автоматическое машинное обучение. Если необходимо использовать настройку гиперпараметров или автоматическое машинное обучение, создайте Вычислительную среду Машинного обучения Azure перед отправкой запроса на запуск.

```python
from azureml.core.compute import ComputeTarget, AmlCompute

#Let us first list the supported VM families for Azure Machine Learning Compute
AmlCompute.supported_vmsizes()

from azureml.core.runconfig import RunConfiguration

# create a new runconfig object
run_config = RunConfiguration()

# signal that you want to use AmlCompute to execute script.
run_config.target = "amlcompute"

# AmlCompute will be created in the same region as workspace. Set vm size for AmlCompute from the list returned above
run_config.amlcompute.vm_size = 'STANDARD_D2_V2'

```

### <a name="persistent-compute-basic"></a>Постоянная вычислительная среда (базовая)

Постоянную Вычислительную среду Машинного обучения Azure можно многократно использовать в нескольких заданиях. Ее можно использовать совместно с другими пользователями в рабочей области, и она сохраняется после выполнения заданий.

Чтобы создать постоянный ресурс Вычислительной среды Машинного обучения Azure, необходимо указать параметры `vm_size` и `max_nodes`. Затем Машинное обучение Azure использует интеллектуальные значения по умолчанию для остальных параметров.  Например, вычислительная среда настраивается таким образом, чтобы число ее узлов сокращалось до нуля, когда она не используется, а при необходимости для выполнения заданий создавались выделенные виртуальные машины. 

* **vm_size**. Семейство виртуальных машин узлов, создаваемых Вычислительной средой Машинного обучения Azure.
* **max_nodes**. Максимальное количество узлов для автомасштабирования при запуске задания в Вычислительной среде Машинного обучения Azure.

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           max_nodes=4)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)

```

### <a name="persistent-compute-advanced"></a>Постоянная вычислительная среда (расширенная)

При создании Вычислительно среды Машинного обучения Azure можно также настроить несколько дополнительных свойств.  Эти свойства позволяют создать постоянный кластер фиксированного размера, который может размещаться в существующей виртуальной сети Azure в вашей подписке.

В дополнение к `vm_size` и `max_nodes` можно использовать следующие свойства.

* **min_nodes**. Минимальное количество узлов (по умолчанию ноль узлов) для автомасштабирования при запуске задания в Вычислительной среде Машинного обучения Azure.
* **vm_priority**. При создании Вычислительной среды Машинного обучения Azure можно выбрать выделенные (по умолчанию) или низкоприоритетные виртуальные машины. Низкоприоритетные виртуальные машины используют избыточную емкость Azure. Они более дешевы, но создают риск замещения ваших заданий.
* **idle_seconds_before_scaledown**. Время простоя (по умолчанию 120 секунд) после завершения запуска, по истечении которого число узлов автоматически масштабируется до значения min_nodes.
* **vnet_resourcegroup_name**. Группа ресурсов __существующей__ виртуальной сети. В этой виртуальной сети создается Вычислительная среда Машинного обучения Azure.
* **vnet_name**. Имя виртуальной сети. Виртуальная сеть должна размещаться в одном регионе с вашей рабочей областью Машинного обучения Azure.
* **subnet_name**. Имя подсети в виртуальной сети. Ресурсам Вычислительной среды Машинного обучения Azure будут назначаться IP-адреса из этого диапазона подсети.

> [!TIP]
> При создании постоянного ресурса Вычислительной среды Машинного обучения Azure вы также можете обновить его свойства, такие как min_nodes или max_nodes. Просто вызовите функцию `update()` для них.

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
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

Машинное обучение Azure также поддерживает использование собственных вычислительных ресурсов и подключение их к рабочей области. К такому типу ресурсов относится произвольная удаленная виртуальная машина, если она доступна из Службы машинного обучения Azure. Это может быть виртуальная машина Azure, а также удаленный или локальный сервер в вашей организации. В частности, указав IP-адрес и учетные данные (имя пользователя и пароль или ключ SSH), можно использовать любую доступную виртуальную машину для удаленного запуска
Можно использовать системную среду Conda, существующую среду Python или контейнер Docker. Для выполнения с помощью контейнера Docker на виртуальной машине необходима подсистема Docker. Эта функция особенно удобна в случаях, когда необходима более гибкая облачная среда разработки и проведения экспериментов, чем локальный компьютер.

> [!TIP]
> Для таких случаев мы рекомендуем использовать Виртуальную машину для обработки и анализа данных в качестве виртуальной машины Azure. Это предварительно настроенная среда разработки с поддержкой обработки и анализа данных и искусственного интеллекта (ИИ) в Azure, предоставляющая проверенный набор инструментов и платформ для всего жизненного цикла разработки решений машинного обучения. Дополнительные сведения об использовании Виртуальной машины для обработки и анализа данных с Машинным обучением Azure см. в документе [Настройка среды разработки для Машинного обучения Azure](https://docs.microsoft.com/azure/machine-learning/service/how-to-configure-environment#dsvm).

> [!WARNING]
> Машинное обучение Azure поддерживает только виртуальные машины под управлением Ubuntu. При создании виртуальной машины или выборе уже существующей необходимо выбрать виртуальную машину под управлением Ubuntu.

При выполнении последующих действий SDK позволит настроить виртуальную машину для обработки и анализа данных как целевого объекта обучения:

1. Чтобы присоединить имеющуюся виртуальную машину как целевой объект вычислений, необходимо указать ее полное доменное имя, имя входа и пароль.  В приведенном примере замените ```<fqdn>``` на общедоступное полное доменное имя виртуальной машины или общедоступный IP-адрес. Замените ```<username>``` и ```<password>``` на имя пользователя SSH и пароль для виртуальной машины:

    ```python
    from azureml.core.compute import RemoteCompute, ComputeTarget
    
    # Create compute config.
    attach_config = RemoteCompute.attach_configuration(address = "ipaddress",
                                                       ssh_port=22,
                                                       username='<username>',
                                                       password="<password>")
    # If using SSH instead of a password, use this:
    #                                                  ssh_port=22,
    #                                                   username='<username>',
    #                                                   password=None,
    #                                                   private_key_file="path-to-file",
    #                                                   private_key_passphrase="passphrase")

    # Attach the compute
    compute = ComputeTarget.attach(ws, "attach-dsvm", attach_config)

    compute.wait_for_completion(show_output=True)
    ```

1. Создайте конфигурацию для целевого объекта вычислений DSVM. Для создания и настройки среды обучения на DSVM используются Docker и Conda.

    ```python
    from azureml.core.runconfig import RunConfiguration
    from azureml.core.conda_dependencies import CondaDependencies

    # Load the "cpu-dsvm.runconfig" file (created by the above attach operation) in memory
    run_config = RunConfiguration(framework = "python")

    # Set compute target to the Linux DSVM
    run_config.target = compute_target_name

    # Use Docker in the remote VM
    run_config.environment.docker.enabled = True

    # Use CPU base image
    # If you want to use GPU in DSVM, you must also use GPU base Docker image azureml.core.runconfig.DEFAULT_GPU_IMAGE
    run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE
    print('Base Docker image is:', run_config.environment.docker.base_image)

    # Ask system to provision a new one based on the conda_dependencies.yml file
    run_config.environment.python.user_managed_dependencies = False

    # Prepare the Docker and conda environment automatically when used the first time.
    run_config.prepare_environment = True

    # specify CondaDependencies obj
    run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])

    ```

## <a id="databricks"></a>Azure Databricks

Azure Databricks — это среда, которая лежит в основе Apache Spark в облаке Azure. Ее можно использовать как целевой объект вычислений при обучении моделей с помощью конвейера машинного обучения Azure.

> [!IMPORTANT]
> Целевой объект вычислений Azure Databricks можно использовать только в конвейере машинного обучения.
>
> Перед тем как его использовать для обучения модели, необходимо создать рабочую область Azure Databricks. Чтобы создать эти ресурсы, см. [Руководство. Автоматическое машинное обучение модели классификации в службе "Машинное обучение Azure"](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal).

Чтобы вложить Azure Databricks в качестве целевого объекта вычислений, используйте пакет SDK машинного обучения Azure и укажите следующие сведения.

* __Имя Вычислительной среды__. Имя, которое вы хотите присвоить этому вычислительному ресурсу.
* __Рабочая область Databricks__. Имя рабочей области Azure Databricks.
* __Маркер доступа__. Маркер доступа, который используется для аутентификации в Azure Databricks. Чтобы создать маркер доступа, см. документ [Проверка подлинности](https://docs.azuredatabricks.net/api/latest/authentication.html).

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

    # Create attach config
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

Azure Data Lake Analytics — это платформа аналитики больших данных в облаке Azure. Ее можно использовать как целевой объект вычислений при обучении моделей с помощью конвейера машинного обучения Azure.

> [!IMPORTANT]
> Целевой объект вычислений Azure Data Lake Analytics можно использовать только в конвейере машинного обучения.
>
> Перед использованием его для обучения модели необходимо создать учетную запись Azure Data Lake Analytics. Чтобы создать этот ресурс, см. статью [Начало работы с Azure Data Lake Analytics с помощью портала Azure](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal).

Чтобы вложить Azure Data Lake Analytics в качестве целевого объекта вычислений, используйте пакет SDK машинного обучения Azure и укажите следующие сведения.

* __Имя Вычислительной среды__. Имя, которое вы хотите присвоить этому вычислительному ресурсу.
* __Группа ресурсов__. Группа ресурсов, содержащая учетную запись Data Lake Analytics.
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
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group = adla_resource_group,
                                                     account_name = adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
             ws,
             adla_compute_name,
             attach_config
         )
    
    adla_compute.wait_for_completion(True)
```

> [!TIP]
> Конвейеры машинного обучения Azure работают только с хранимыми данными в хранилище данных учетной записи Data Lake Analytics по умолчанию. Если данные, с которыми нужно работать, находятся в хранилище не по умолчанию, то для копирования данных перед обучением можно использовать [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py).

## <a id="hdinsight"></a>Azure HDInsight 

HDInsight — это популярная платформа для анализа больших данных. Она предоставляет Apache Spark, который можно использовать для обучения модели.

> [!IMPORTANT]
> Прежде чем использовать кластер HDInsight для обучения модели, его необходимо создать. Инструкции по созданию Spark в кластере HDInsight см. в документе [Создание кластера Spark в HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql).
>
> При создании кластера необходимо указать имя пользователя SSH и пароль. Запишите эти значения — они вам потребуются при использовании HDInsight в качестве целевого объекта вычислений.
>
> Созданный кластер будет иметь полное доменное имя `<clustername>.azurehdinsight.net`, где `<clustername>` — это имя, которое вы указали для кластера. Этот адрес (или общедоступный IP-адрес кластера) нужно будет использовать как целевой объект вычислений.

Чтобы настроить HDInsight как целевой объект вычислений, необходимо указать полное доменное имя, а также имя входа и пароль для кластера HDInsight. В следующем примере пакет SDK используется для присоединения кластера к рабочей области. В приведенном примере замените `<fqdn>` на общедоступное полное доменное имя кластера или общедоступный IP-адрес. Замените `<username>` и `<password>` на имя пользователя SSH и пароль для кластера:

> [!NOTE]
> Чтобы узнать полное доменное имя для кластера, откройте портал Azure и выберите свой кластер HDInsight. В разделе __Обзор__ полное доменное имя будет частью записи __URL-адрес__. Просто удалите `https://` в начале значения.
>
> ![Снимок экрана с обзором кластера HDInsight и выделенной записью URL-адреса](./media/how-to-set-up-training-targets/hdinsight-overview.png)

```python
from azureml.core.compute import HDInsightCompute, ComputeTarget

try:
    # Attaches a HDInsight cluster as a compute target.
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

# Configure HDInsight run
# load the runconfig object from the "myhdi.runconfig" file generated by the attach operaton above.
run_config = RunConfiguration.load(project_object = project, run_config_name = 'myhdi')

# ask system to prepare the conda environment automatically when used for the first time
run_config.auto_prepare_environment = True
```

## <a name="submit-training-run"></a>Выполнение обучающего прогона

Существует два способа отправки запуска на выполнения обучения:

* отправка объекта `ScriptRunConfig`;
* отправка объекта `Pipeline`.

> [!IMPORTANT]
> Целевые объекты вычислений Azure Databricks и Azure Datalake Analytics могут использоваться только в конвейере.
> Локальный целевой объект вычислений не может использоваться в конвейере.

### <a name="submit-using-scriptrunconfig"></a>Отправка с помощью `ScriptRunConfig`

Шаблон кода для отправки запуска на выполнение обучения с помощью `ScriptRunConfig` будет одинаковым, независимо от целевого объекта вычислений.

* Создайте объект `ScriptRunConfig`, используя конфигурацию запуска для целевого объекта вычислений.
* Выполните прогон.
* Дождитесь завершения прогона.

В следующем примере используется конфигурация для локального целевого объекта вычислений, управляемого системой, который вы создали ранее в этом документе:

```python
src = ScriptRunConfig(source_directory = script_folder, script = 'train.py', run_config = run_config_system_managed)
run = exp.submit(src)
run.wait_for_completion(show_output = True)
```


### <a name="submit-using-a-pipeline"></a>Отправка с помощью конвейера

Шаблон кода для отправки запуска на выполнение обучения с помощью конвейера будет одинаковым, независимо от целевого объекта вычислений.

* Для ресурсов вычисления добавьте шаг в конвейере.
* С помощью конвейера отправьте запуск на выполнение.
* Дождитесь завершения прогона.

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
# list of steps to run
steps = [dbStep]
pipeline = Pipeline(workspace=ws, steps=steps)
pipeline_run = Experiment(ws, 'Demo_experiment').submit(pipeline)
pipeline_run.wait_for_completion()
```

Дополнительные сведения о конвейерах машинного обучения см. в статье [Конвейеры и служба "Машинное обучение Azure"](concept-ml-pipelines.md).

Например, Jupyter Notebook, которые демонстрируют обучение с помощью конвейера, см. [https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline).

## <a name="view-and-set-up-compute-using-the-azure-portal"></a>Просмотр и настройка вычислений с помощью портала Azure

Вы можете узнать, какие целевые объекты вычислений связаны с вашей рабочей областью, на портале Azure. Чтобы получить список этих объектов, выполните следующие действия:

1. Откройте [портал Azure](https://portal.azure.com) и перейдите к своей рабочей области.
2. Щелкните ссылку __Вычисление__ в разделе __Приложения__.

    ![Просмотр вкладки вычислений](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)

### <a name="create-a-compute-target"></a>Создание целевого объекта вычислений

Откройте список целевых объектов вычислений согласно приведенным выше инструкциям, а затем создайте целевой объект вычислений, выполнив следующие действия:

1. Щелкните значок __+__, чтобы добавить целевой объект вычислений.

    ![Добавить вычисление ](./media/how-to-set-up-training-targets/add-compute-target.png)

1. Введите имя для целевого объекта вычислений.
1. Выберите **Вычислительная среда Машинного обучения** в качестве типа вычислительной среды для среды __обучения__.

    > [!IMPORTANT]
    > Вычислительную среду Машинного обучения Azure можно создать только в качестве управляемой вычислительной среды для обучения.

1. Заполните необходимые поля формы. В частности, укажите семейство виртуальных машин и максимальное количество узлов для развертывания вычислительной среды. 
1. Нажмите кнопку __Создать__
1. Чтобы увидеть состояние операции создания, выберите целевой объект вычислений из списка.

    ![Просмотр списка вычислительных ресурсов](./media/how-to-set-up-training-targets/View_list.png)

1. Ниже приведены сведения о выбранном целевом объекте вычислений.

    ![Просмотр сведений](./media/how-to-set-up-training-targets/compute-target-details.png)

1. Теперь вы можете отправить запрос на запуск на этих целевых объектах, как описано выше.


### <a name="reuse-existing-compute-in-your-workspace"></a>Повторное использование существующих вычислений в рабочей области

Откройте список целевых объектов вычислений согласно приведенным выше инструкциям, а затем используйте существующий целевой объект вычислений еще раз, выполнив следующие действия:

1. Щелкните значок **+**, чтобы добавить целевой объект вычислений.
2. Введите имя для целевого объекта вычислений.
3. Выберите тип вычислительной среды для подключения к среде __обучения__.

    > [!IMPORTANT]
    > С помощью портала не можно вложить все типы вычислений.
    > Ниже приведен список типов, которые можно вложить для обучения.
    > 
    > * Удаленная виртуальная машина
    > * Databricks
    > * Data Lake Analytics
    > * HDInsight

1. Заполнение обязательных полей формы

    > [!NOTE]
    > Майкрософт рекомендует использовать ключи SSH, поскольку они безопаснее, чем пароли. Пароли уязвимы для атак методом подбора, в то время как ключи SSH задействуют криптографические подписи. Сведения о создании ключей SSH для использования на виртуальных машинах Azure см. в следующих документах:
    >
    > * [Создание и использование ключей SSH в Linux или macOS]( https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Создание и использование ключей SSH в Windows]( https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. Выберите "Подключить"
1. Чтобы увидеть состояние операции подключения, выберите целевой объект вычислений из списка.
1. Теперь вы можете отправить запрос на запуск на этих целевых объектах, как описано выше.

## <a name="examples"></a>Примеры
Ознакомьтесь с записными книжками в следующих расположениях:
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)

* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Дополнительная информация

* [Справка по пакету SDK для машинного обучения Azure](https://aka.ms/aml-sdk)
* [Руководство. Обучение модели](tutorial-train-models-with-aml.md)
* [Где следует развертывать модели](how-to-deploy-and-where.md)
* [Создание конвейеров машинного обучения с помощью службы машинного обучения Azure](concept-ml-pipelines.md)
