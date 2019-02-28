---
title: Что происходит с Azure Batch AI | Документация Майкрософт
description: Сведения о том, что происходит с Azure Batch AI, и описание объекта вычислений, доступного в Службе машинного обучения Azure.
services: batch-ai
author: garyericson
ms.service: batch-ai
ms.topic: overview
ms.date: 2/14/2019
ms.author: garye
ms.openlocfilehash: fb1114e94c227ce6787532c6059186399d0f57f0
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961367"
---
# <a name="whats-happening-to-azure-batch-ai"></a>Что происходит с Azure Batch AI

**Служба Azure Batch AI прекращает работу в марте.** Возможности обучения и оценки службы Batch AI для масштабных развертываний теперь доступны в [Службе машинного обучения Azure](../machine-learning/service/overview-what-is-azure-ml.md), которая стала общедоступной 4 декабря 2018 года.

Помимо множества других возможностей машинного обучения, Служба машинного обучения Azure включает облачный управляемый целевой объект вычислений для обучения, развертывания и оценки моделей машинного обучения. Этот целевой объект вычислений называется [Вычислительная среда Машинного обучения Azure](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute). [Начните миграцию и перейдите к использованию уже сегодня](#migrate). Вы можете взаимодействовать со Службой машинного обучения Azure через [пакеты SDK для Python](../machine-learning/service/quickstart-create-workspace-with-python.md), интерфейс командной строки и [портал Azure](../machine-learning/service/quickstart-get-started.md).

## <a name="support-timeline"></a>Сроки поддержки

| Дата | Сведения о поддержке службы Batch AI |
| ---- |-----------------|
| &nbsp;14&nbsp;декабря 2018 г.| Вы можете использовать имеющиеся подписки Azure Batch AI как раньше. Однако создать **новые подписки** невозможно, а новые инвестиции не осуществляются.|
| &nbsp;31&nbsp;марта 2019 г. | После этой даты существующие подписки Batch AI больше не будут работать. |

## <a name="how-does-azure-machine-learning-service-compare"></a>Сравнение Службы машинного обучения Azure
Это облачная служба, которая используется для обучения, развертывания, автоматизации моделей машинного обучения и управления ими в предоставляемом облаком широком масштабе. Чтобы получить хорошее представление о Службе машинного обучения Azure, ознакомьтесь с [обзором](../machine-learning/service/overview-what-is-azure-ml.md).
 

Типовой жизненный цикл разработки модели включает фазы подготовки данных, обучения и экспериментирования, а также фазу развертывания. Этим комплексным циклом можно управлять с помощью конвейеров Машинного обучения.

![Схема процесса](./media/overview-what-happened-batch-ai/lifecycle.png)


[Узнайте о работе службы и ее основных понятиях](../machine-learning/service/concept-azure-machine-learning-architecture.md). Многие понятия в рабочем процессе обучения модели сходны с имеющимися понятиями в Batch AI. 

В частности, здесь показано, чем их следует считать:
 
|Служба Batch AI|  Служба "Машинное обучение Azure"|
|:--:|:---:|
|Рабочая область|Рабочая область|
|HDInsight|   Вычисления типа `AmlCompute`|
|Файловые серверы|Хранилища данных|
|Эксперименты|Эксперименты|
|Задания|Выполнения (позволяет выполнять вложенные запуски)|
 
Это другое представление той же таблицы, которая поможет при визуализации информации в дальнейшем:
 
**Иерархия Batch AI**
![Схема процесса](./media/overview-what-happened-batch-ai/batchai-heirarchy.png) 
 
**Иерархия Службы машинного обучения Azure**
![Схема процесса](./media/overview-what-happened-batch-ai/azure-machine-learning-service-heirarchy.png) 

### <a name="platform-capabilities"></a>"Возможности платформы"
Служба машинного обучения Azure предоставляет отличный набор новых возможностей, включая комплексный стек "обучение->развертывание", который можно использовать для развертывания искусственного интеллекта без необходимости управлять ресурсами Azure. В этой таблице сравниваются данные о поддержке функций в двух службах.

|Функция|Служба BatchAI|Служба "Машинное обучение Azure"|
|-------|:-------:|:-------:|
|Выбор размера виртуальной машины |ЦП и GPU    |ЦП и GPU. Кроме того, поддерживает FPGA для получения выводов|
|Кластер, готовый к использованию искусственного интеллекта (драйвера, Docker и т. д).|  Yes |Yes|
|Подготовка узла| Yes|    Нет |
|Выбор семейства ОС   |Частично    |Нет |
|Выделенные и низкоприоритетные виртуальные машины  |Yes    |Yes|
|Автомасштабирование   |Yes    |Да, по умолчанию|
|Время ожидания для автомасштабирования  |Нет  |Yes|
|SSH    |Yes|   Yes|
|Подключение на уровне кластера |Да (общие папки, большие двоичные объекты, NFS, настраиваемое)   |Да (подключение или скачивание в хранилище данных)|
|Распределенное обучение|  Yes |Yes|
|Режим выполнения задания|    Виртуальная машина или контейнер|    Контейнер|
|Настраиваемый образ контейнера|    Yes |Yes|
|Любой набор средств    |Yes    |Да (запускает скрипт Python)|
|Подготовка задания|    Yes |Пока нет|
|Подключение на уровне задания. |Да (общие папки, большие двоичные объекты, NFS, настраиваемое)   |Да (общие папки, большие двоичные объекты)|
|Мониторинг заданий     |с помощью GetJob|    с помощью журнала выполнения (более подробная информация, настраиваемая среда выполнения для отправки дополнительных метрик)|
|Получение журналов заданий и файлов или моделей |   с помощью ListFiles и API службы хранилища  |с помощью службы артефакта|
 |Поддержка Tensorboard   |Нет |    Yes|
|Квоты для уровня семейства виртуальных машин |Yes    |Да (с перенесением предыдущей емкости)|
 
В дополнение к приведенной выше таблице в Службе машинного обучения Azure существуют функции, которые обычно не поддерживаются в BatchAI.

|Функция|Служба BatchAI|Служба "Машинное обучение Azure"|
|-------|:-------:|:-------:|
|Подготовка среды    |Нет  |Да (Conda подготавливается и отправляется в ACR)|
|Настройка гиперпараметров  |Нет |    Yes|
|Управление моделями   |Нет  |Yes|
|Ввод в эксплуатацию и развертывание| Нет   |С помощью AKS и ACI|
|Подготовка данных   |Нет  |Yes|
|Целевые объекты вычислений    |Виртуальные машины Azure  |Локальные, BatchAI (как AmlCompute), DataBricks, HDInsight|
|Автоматическое машинное обучение |Нет |    Yes|
|Конвейеры  |Нет  |Yes|
|Пакетная оценка  |Yes    |Yes|
|Поддержка портала и CLI|    Yes |Yes|


### <a name="programming-interfaces"></a>"Программные интерфейсы"

В этой таблице представлены различные программные интерфейсы, доступные для каждой службы.
    
|Функция|Служба BatchAI|Служба "Машинное обучение Azure"|
|-------|:-------:|:-------:|
|SDK    |Java, C#, Python, Nodejs   |Python (выполнение на основе конфигурации и механизма оценки для стандартных платформ)|
|Интерфейс командной строки    |Yes    |Пока нет|
|Портал Azure   |Yes    |Да (кроме отправки заданий)|
|REST API   |Yes    |Да, но распределенных между микрослужбами|




## <a name="why-migrate"></a>Для чего нужна миграция

Обновление с предварительной версии службы BatchAI до общедоступной Службы машинного обучения Azure обеспечивает более оптимальные условия использования благодаря более простым в использовании понятиям, таким как механизмы оценки и хранилища данных. При этом также гарантируется Соглашение об уровне обслуживания службы Azure и поддержка пользователей на уровне общедоступной версии.

Служба машинного обучения Azure также вводит новые функции, такие как автоматическое машинное обучение, настройка гиперпараметров, конвейеры машинного обучения, которые полезны в большинстве крупномасштабных рабочих нагрузок искусственного интеллекта. Способность ввести в эксплуатацию обученную модель без перехода к отдельной службе позволяет реализовать полный цикл обработки и анализа данных, начиная с подготовки данных (с помощью пакета SDK для подготовки данных) вплоть до ввода в эксплуатацию и мониторинга модели.

<a name="migrate"></a>

## <a name="how-do-i-migrate"></a>Как выполнить миграцию

Прежде чем выполнять действия в этом руководстве по миграции, помогающие сопоставить команды в двух службах, рекомендуем потратить немного времени, чтобы ознакомиться со Службой машинного обучения Azure в [этой документации](../machine-learning/service/overview-what-is-azure-ml.md), включая [руководство по Python](../machine-learning/service/tutorial-train-models-with-aml.md).

Чтобы избежать нарушений в работе приложений и использовать преимущества новейших функций, выполните следующие действия до 31 марта 2019 г.:

1. Создайте рабочую область Службы машинного обучения Azure и начните работу:
    + [Краткое руководство с описанием использования Python](../machine-learning/service/quickstart-create-workspace-with-python.md)
    + [Краткое руководство с описанием использования портала Azure](../machine-learning/service/quickstart-get-started.md)

1. Настройте [Вычислительную среду Машинного обучения Azure](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute) для обучения моделей.

1. Обновите скрипты для использования Вычислительной среды Машинного обучения Azure.


### <a name="sdk-migration"></a>Миграция пакета SDK

Текущий пакет SDK поддерживается в Службе машинного обучения Azure в нескольких пакетах SDK для Python. Основной пакет SDK обновляется примерно раз в две недели, его можно установить из PyPi с помощью следующей команды:

```python
pip install --upgrade azureml-sdk[notebooks]
```

Настройте среду и установите пакет SDK, используя это [краткое руководство](../machine-learning/service/quickstart-create-workspace-with-python.md#install-the-sdk).

Открыв записную книжку jupyter с ядром, которое указывает на соответствующую среду conda, команды в двух службах будут сопоставляться следующим образом:


#### <a name="create-a-workspace"></a>Создание рабочей области
Понятие инициализации рабочей области с помощью файла configuration.json в BatchAI аналогичным образом сопоставляется с понятием использования файла конфигурации в Машинном обучении Azure.

Для **Batch AI** это выполняется следующим образом:

```python
sys.path.append('../../..')
import utilities as utils

cfg = utils.config.Configuration('../../configuration.json')
client = utils.config.create_batchai_client(cfg)

utils.config.create_resource_group(cfg)
_ = client.workspaces.create(cfg.resource_group, cfg.workspace, cfg.location).result()
```

В **Службе машинного обучения Azure** попробуйте следующее:

```python
from azureml.core.workspace import Workspace

ws = Workspace.from_config()
print('Workspace name: ' + ws.name, 
      'Azure region: ' + ws.location, 
      'Subscription id: ' + ws.subscription_id, 
      'Resource group: ' + ws.resource_group, sep = '\n')
```

Кроме того, вы также можете создать рабочую область непосредственно, указав такие параметры конфигурации:

```python
from azureml.core import Workspace
# Create the workspace using the specified parameters
ws = Workspace.create(name = workspace_name,
                      subscription_id = subscription_id,
                      resource_group = resource_group, 
                      location = workspace_region,
                      create_resource_group = True,
                      exist_ok = True)
ws.get_details()

# write the details of the workspace to a configuration file to the notebook library
ws.write_config()
```

Дополнительные сведения о классе в рабочей области AML см. в [справочной документации для пакета SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py).


#### <a name="create-a-compute-cluster"></a>Создание вычислительного кластера
Машинное обучение Azure поддерживает несколько целевых объектов вычислений, некоторыми из которых управляет служба и другие пользователи, которые можно подключить к рабочей области (например, кластер HDInsight или удаленную виртуальную машину). Дополнительные сведения о различных целевых объектах вычислений см. в [этой статье](../machine-learning/service/how-to-set-up-training-targets.md). Понятие создания кластеров вычислений BatchAI сопоставляется с понятием создания кластера AmlCompute в Машинном обучении Azure. При создании Amlcompute конфигурация вычислений принимается аналогично тому, как вы передаете параметры в BatchAI. Следует отметить, что автоматическое масштабирование по умолчанию включено в кластере AmlCompute, в то время как оно по умолчанию отключено в BatchAI.

Для **Batch AI** это выполняется следующим образом:

```python
nodes_count = 2
cluster_name = 'nc6'

parameters = models.ClusterCreateParameters(
    vm_size='STANDARD_NC6',
    scale_settings=models.ScaleSettings(
        manual=models.ManualScaleSettings(target_node_count=nodes_count)
    ),
    user_account_settings=models.UserAccountSettings(
        admin_user_name=cfg.admin,
        admin_user_password=cfg.admin_password or None,
        admin_user_ssh_public_key=cfg.admin_ssh_key or None,
    )
)
_ = client.clusters.create(cfg.resource_group, cfg.workspace, cluster_name, parameters).result()
```

В **Службе машинного обучения Azure** попробуйте:

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your CPU cluster
gpu_cluster_name = "nc6"

# Verify that cluster does not exist already
try:
    gpu_cluster = ComputeTarget(workspace=ws, name=gpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           vm_priority='lowpriority',
                                                           min_nodes=1,
                                                           max_nodes=2,
                                                           idle_seconds_before_scaledown='300',
                                                           vnet_resourcegroup_name='<my-resource-group>',
                                                           vnet_name='<my-vnet-name>',
                                                           subnet_name='<my-subnet-name>')
    gpu_cluster = ComputeTarget.create(ws, gpu_cluster_name, compute_config)

gpu_cluster.wait_for_completion(show_output=True)
```

Дополнительные сведения о классе AMLCompute см. в [справочной документации для пакета SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py). Обратите внимание, что в приведенной выше конфигурации только свойства vm_size и max_nodes являются обязательными, а остальные свойства, такие как виртуальные сети, предназначены только для расширенной установки кластера.


#### <a name="monitoring-status-of-your-cluster"></a>Мониторинг состояния кластера
Как можно будет увидеть ниже, это проще в службе "Машинное обучение Azure".

Для **Batch AI** это выполняется следующим образом:

```python
cluster = client.clusters.get(cfg.resource_group, cfg.workspace, cluster_name)
utils.cluster.print_cluster_status(cluster)
```

В **Службе машинного обучения Azure** попробуйте:

```python
gpu_cluster.get_status().serialize()
```

#### <a name="getting-reference-to-a-storage-account"></a>Получение ссылки на учетную запись хранения
Понятие хранилища данных, такого как большой двоичный объект, упрощено в службе "Машинное обучение Azure" с помощью объекта DataStore. По умолчанию рабочая область службы "Машинное обучение Azure" создает учетную запись хранения, однако вы можете также подключить собственное хранилище в ходе создания рабочей области. 

Для **Batch AI** это выполняется следующим образом:

```python
azure_blob_container_name = 'batchaisample'
blob_service = BlockBlobService(cfg.storage_account_name, cfg.storage_account_key)
blob_service.create_container(azure_blob_container_name, fail_on_exist=False)
```

В **Службе машинного обучения Azure** попробуйте:

```python
ds = ws.get_default_datastore()
print(ds.datastore_type, ds.account_name, ds.container_name)
```

Дополнительные сведения о регистрации дополнительных учетных записей хранения или получении ссылки на другое зарегистрированное хранилище данных см. в [документации по Службе машинного обучения Azure](../machine-learning/service/how-to-access-data.md#access).


#### <a name="downloading-and-uploading-data"></a>Отправка и скачивание файлов 
С помощью любой из этих служб вы можете легко отправить данные в учетную запись хранения с помощью ссылки на хранилище данных выше. В BatchAI мы также внедряем обучающий скрипт как часть общей папки, хотя вы увидите, что в случае со службой "Машинное обучение Azure" его можно указать при конфигурации задания.

Для **Batch AI** это выполняется следующим образом:

```python
mnist_dataset_directory = 'mnist_dataset'
utils.dataset.download_and_upload_mnist_dataset_to_blob(
    blob_service, azure_blob_container_name, mnist_dataset_directory)

script_directory = 'tensorflow_samples'
script_to_deploy = 'mnist_replica.py'

blob_service.create_blob_from_path(azure_blob_container_name,
                                   script_directory + '/' + script_to_deploy, 
                                   script_to_deploy)
```


В **Службе машинного обучения Azure** попробуйте:

```python
import os
import urllib
os.makedirs('./data', exist_ok=True)
download_url = 'https://s3.amazonaws.com/img-datasets/mnist.npz'
urllib.request.urlretrieve(download_url, filename='data/mnist.npz')

ds.upload(src_dir='data', target_path='mnist_dataset', overwrite=True, show_progress=True)

path_on_datastore = ' mnist_dataset/mnist.npz' ds_data = ds.path(path_on_datastore) print(ds_data)
```

#### <a name="create-an-experiment"></a>Создание эксперимента
Как упоминалось выше, в службе "Машинное обучение Azure" используется понятие эксперимента, аналогичное этому же понятию BatchAI. Каждый эксперимент затем выполняется отдельно, аналогично тому, как выполняются задания в BatchAI. В службе "Машинное обучение Azure" также разрешены иерархии при каждом родительском выполнении для отдельных дочерних выполнений.

Для **Batch AI** это выполняется следующим образом:

```python
experiment_name = 'tensorflow_experiment'
experiment = client.experiments.create(cfg.resource_group, cfg.workspace, experiment_name).result()
```

В **Службе машинного обучения Azure** попробуйте:

```python
from azureml.core import Experiment

experiment_name = 'tensorflow_experiment'
experiment = Experiment(ws, name=experiment_name)
```


#### <a name="submit-a-job"></a>Отправка задания
Создав эксперимент, вы можете запустить выполнение несколькими разными способами. В этом примере предпринимается попытка создания модели глубокого обучения с помощью TensorFlow, для этого будет использоваться механизм оценки службы "Машинное обучение Azure". [Механизм оценки](../machine-learning/service/how-to-train-ml-models.md) — это просто функция оболочки базовой конфигурации выполнения, упрощающая отправку выполнений, и в настоящее время поддерживается только для Pytorch и TensorFlow. Ознакомившись с понятием хранилища данных, вы также увидите, как легко указать пути подключения. 

Для **Batch AI** это выполняется следующим образом:

```python
azure_file_share = 'afs'
azure_blob = 'bfs'
args_fmt = '--job_name={0} --num_gpus=1 --train_steps 10000 --checkpoint_dir=$AZ_BATCHAI_OUTPUT_MODEL --log_dir=$AZ_BATCHAI_OUTPUT_TENSORBOARD --data_dir=$AZ_BATCHAI_INPUT_DATASET --ps_hosts=$AZ_BATCHAI_PS_HOSTS --worker_hosts=$AZ_BATCHAI_WORKER_HOSTS --task_index=$AZ_BATCHAI_TASK_INDEX'

parameters = models.JobCreateParameters(
     cluster=models.ResourceId(id=cluster.id),
     node_count=2,
     input_directories=[
        models.InputDirectory(
            id='SCRIPT',
            path='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}/{1}'.format(azure_blob, script_directory)),
        models.InputDirectory(
            id='DATASET',
            path='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}/{1}'.format(azure_blob, mnist_dataset_directory))],
     std_out_err_path_prefix='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}'.format(azure_file_share),
     output_directories=[
        models.OutputDirectory(
            id='MODEL',
            path_prefix='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}'.format(azure_file_share),
            path_suffix='Models'),
        models.OutputDirectory(
            id='TENSORBOARD',
            path_prefix='$AZ_BATCHAI_JOB_MOUNT_ROOT/{0}'.format(azure_file_share),
            path_suffix='Logs')
     ],
     mount_volumes=models.MountVolumes(
            azure_file_shares=[
                models.AzureFileShareReference(
                    account_name=cfg.storage_account_name,
                    credentials=models.AzureStorageCredentialsInfo(
                        account_key=cfg.storage_account_key),
                    azure_file_url='https://{0}.file.core.windows.net/{1}'.format(
                        cfg.storage_account_name, azure_file_share_name),
                    relative_mount_path=azure_file_share)
            ],
            azure_blob_file_systems=[
                models.AzureBlobFileSystemReference(
                    account_name=cfg.storage_account_name,
                    credentials=models.AzureStorageCredentialsInfo(
                        account_key=cfg.storage_account_key),
                    container_name=azure_blob_container_name,
                    relative_mount_path=azure_blob)
            ]
        ),
     container_settings=models.ContainerSettings(
         image_source_registry=models.ImageSourceRegistry(image='tensorflow/tensorflow:1.8.0-gpu')),
     tensor_flow_settings=models.TensorFlowSettings(
         parameter_server_count=1,
         worker_count=nodes_count,
         python_script_file_path='$AZ_BATCHAI_INPUT_SCRIPT/'+ script_to_deploy,
         master_command_line_args=args_fmt.format('worker'),
         worker_command_line_args=args_fmt.format('worker'),
         parameter_server_command_line_args=args_fmt.format('ps'),
     )
)
```

Отправка задания в BatchAI выполняется с помощью функции создания.

```python
job_name = datetime.utcnow().strftime('tf_%m_%d_%Y_%H%M%S')
job = client.jobs.create(cfg.resource_group, cfg.workspace, experiment_name, job_name, parameters).result()
print('Created Job {0} in Experiment {1}'.format(job.name, experiment.name))
```

Полные сведения для этого фрагмента кода для обучения (в том числе файл mnist_replica.py, который мы отправляли в общую папку выше) можно найти в [репозитории GitHub примера записной книжки BatchAI](https://github.com/Azure/BatchAI/tree/2238607d5a028a0c5e037168aefca7d7bb165d5c/recipes/TensorFlow/TensorFlow-GPU-Distributed).

В **Службе машинного обучения Azure** попробуйте:

```python
from azureml.train.dnn import TensorFlow

script_params={
    '--num_gpus': 1,
    '--train_steps': 500,
    '--input_data': ds_data.as_mount()

}

estimator = TensorFlow(source_directory=project_folder,
                       compute_target=gpu_cluster,
                       script_params=script_params,
                       entry_script='tf_mnist_replica.py',
                       node_count=2,
                       worker_count=2,
                       parameter_server_count=1,   
                       distributed_backend='ps',
                       use_gpu=True)
```

Полные сведения для этого фрагмента кода для обучения (в том числе файл mnist_replica.py) можно найти в [репозитории GitHub примера записной книжки службы "Машинное обучение Azure"](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/distributed-tensorflow-with-parameter-server). Само хранилище данных можно установить на отдельных узлах, или же данные для обучения можно скачать на сам узел. Дополнительные сведения о ссылках на хранилище данных механизма оценки см. в [документации службы "Машинное обучение Azure"](../machine-learning/service/how-to-access-data.md#access). 

Отправка выполнения в службе "Машинное обучение Azure" выполняется с помощью функции отправки.

```python
run = experiment.submit(estimator)
print(run)
```

Есть еще один способ указания параметров для выполнения — с помощью выполнения конфигурации. Это особенно полезно для определения пользовательской обучающей среды. Дополнительные сведения см. в этом [примере записной книжки AmlCompute](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-amlcompute/train-on-amlcompute.ipynb). 

#### <a name="monitor-your-run"></a>Мониторинг выполнения
После запуска выполнения вы можете или дождаться выполнения, или отслеживать его в службе "Машинное обучение Azure" с помощью мини-приложений Jupyter, которые можно вызывать непосредственно из кода. Вы также можете извлекать контекст из любого предыдущего выполнения путем различных экспериментов с рабочей областью и отдельных выполнений в рамках каждого эксперимента.

Для **Batch AI** это выполняется следующим образом:

```python
utils.job.wait_for_job_completion(client, cfg.resource_group, cfg.workspace, 
                                  experiment_name, job_name, cluster_name, 'stdouterr', 'stdout-wk-0.txt')

files = client.jobs.list_output_files(cfg.resource_group, cfg.workspace, experiment_name, job_name,
                                      models.JobsListOutputFilesOptions(outputdirectoryid='stdouterr')) 
for f in list(files):
    print(f.name, f.download_url or 'directory')
```


В **Службе машинного обучения Azure** попробуйте:

```python
run.wait_for_completion(show_output=True)

from azureml.widgets import RunDetails
RunDetails(run).show()
```

Ниже приведен моментальный снимок того, как мини-приложение будет загружать записную книжку для просмотра журналов в режиме реального времени. ![Диаграмма мониторинга мини-приложения](./media/overview-what-happened-batch-ai/monitor.png)



#### <a name="editing-a-cluster"></a>Редактирование в кластере
Кластер можно легко удалить. Кроме того, служба "Машинное обучение Azure" позволяет обновить кластер в рамках записной книжки, если вы хотите масштабировать ее на большее количество узлов или увеличить время ожидания простоя перед уменьшением масштаба кластера. Изменять размер кластера виртуальной машины запрещено, так как для этого в серверной части потребуется новое развертывание.

Для **Batch AI** это выполняется следующим образом:
```python
_ = client.clusters.delete(cfg.resource_group, cfg.workspace, cluster_name)
```

В **Службе машинного обучения Azure** попробуйте:

```python
gpu_cluster.delete()

gpu_cluster.update(min_nodes=2, max_nodes=4, idle_seconds_before_scaledown=600)
```

## <a name="support"></a>Поддержка

Прекращение поддержки BatchAI намечено на 31 марта, поэтому регистрация новых подписок в службе уже блокируется, если только они не добавлены в список разрешений благодаря запросам на исключение в службу поддержки.  Свяжитесь с нами на сайте [предварительной версии обучения Azure Batch AI](mailto:AzureBatchAITrainingPreview@service.microsoft.com), чтобы задать любые вопросы или чтобы оставить отзывы о миграции в Службу машинного обучения Azure.

Выпущена общедоступная версия Службы машинного обучения Azure. Это означает, что она поставляется с зафиксированным Соглашением об уровне обслуживания и различными планами поддержки на выбор.

Цены на использование инфраструктуры Azure через службу BatchAI или с помощью Службы машинного обучения Azure не должны различаться, так как мы взимаем плату за базовые вычисления в обоих случаях. Дополнительные сведения см. на странице [калькулятора цен](https://azure.microsoft.com/pricing/details/machine-learning-service/).

Сведения о региональной доступности двух служб см. на [портале Azure](https://azure.microsoft.com/global-infrastructure/services/?products=batch-ai,machine-learning-service&regions=all).


## <a name="next-steps"></a>Дополнительная информация

+ Ознакомьтесь со статьей [Что такое Служба машинного обучения Azure](../machine-learning/service/overview-what-is-azure-ml.md).

+ [Настройте целевой объект вычислений для обучения моделей с помощью Службы машинного обучения Azure](../machine-learning/service/how-to-set-up-training-targets.md).

+ Просмотрите [стратегию развития Azure](https://azure.microsoft.com/updates/), чтобы узнать о других обновлениях служб Azure.
