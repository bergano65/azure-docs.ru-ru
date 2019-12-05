---
title: Выполнение пакетного прогнозирования на больших объемах данных
titleSuffix: Azure Machine Learning
description: Сведения о том, как асинхронно выполнять пакетный вывод больших объемов данных в Машинном обучении Azure. Функция пакетного вывода по умолчанию поддерживает возможности параллельной обработки. Она оптимизирована для сценариев вывода больших данных с высокой пропускной способностью без вмешательства пользователя.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: vaidyas
author: vaidya-s
ms.date: 11/04/2019
ms.custom: Ignite2019
ms.openlocfilehash: 62a2c3324df70c7ccdbbac273d314ff94cbb7b9a
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671561"
---
# <a name="run-batch-inference-on-large-amounts-of-data-by-using-azure-machine-learning"></a>Выполнение пакетного вывода больших объемов данных с помощью Машинного обучения Azure
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этом практическом руководстве описано, как в асинхронном и параллельном режиме выполнять пакетный вывод больших объемов данных с помощью Машинного обучения Azure. Описанные здесь возможности пакетного вывода предоставляются в режиме общедоступной предварительной версии. Вы сможете создавать выводы и эффективно обрабатывать данные, используя высокую пропускную способность сети. Возможности асинхронной обработки поддерживаются без дополнительной настройки.

Функция пакетного вывода позволяет очень легко масштабировать вывод с локальных компьютеров в крупные кластеры, которые позволяют обрабатывать терабайты рабочих данных, повышая производительность и оптимизируя затраты.

В этом практическом руководстве описано, как выполнять следующие задачи:

> * создание удаленного целевого вычислительного ресурса;
> * создание пользовательского скрипта вывода;
> * создание [конвейера машинного обучения](concept-ml-pipelines.md), который позволяет зарегистрировать модель классификации изображений, предварительно обученную по набору данных [MNIST](https://publicdataset.azurewebsites.net/dataDetail/mnist/); 
> * использование предварительно обученной модели для пакетного вывода изображений, которые размещены в учетной записи хранилища BLOB-объектов Azure. 

## <a name="prerequisites"></a>Предварительные требования

* Если у вас еще нет подписки Azure, создайте бесплатную учетную запись Azure, прежде чем начинать работу. Опробуйте [бесплатную или платную версию Машинного обучения Azure](https://aka.ms/AMLFree).

* Чтобы быстро начать работу, вы можете выполнить инструкции из [руководства по установке](tutorial-1st-experiment-sdk-setup.md), если у вас еще нет рабочей области Машинного обучения Azure или виртуальной машины записных книжек. 

* Сведения о том, как управлять средой и зависимостями, см. в [этом практическом руководстве](how-to-configure-environment.md) по настройке среды. Выполните `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-contrib-pipeline-steps` в своей среде, чтобы скачать необходимые зависимости.

## <a name="set-up-machine-learning-resources"></a>Настройка ресурсов машинного обучения

Следующие действия позволяют настроить ресурсы, которые потребуются для запуска конвейера пакетного вывода.

- Создайте хранилище данных, которое указывает на контейнер больших двоичных объектов с изображениями для вывода.
- Настройте ссылки на данные в качестве входных и выходных данных для шага конвейера пакетного вывода.
- Настройте вычислительный кластер для выполнения шага пакетного вывода.

### <a name="create-a-datastore-with-sample-images"></a>Создание хранилища данных с примерами изображений

Извлеките оценочный набор данных MNIST из общедоступного контейнера BLOB-объектов `sampledata`, который размещен в учетной записи с именем `pipelinedata`. Создайте хранилище данных с именем `mnist_datastore`, которое указывает на этот контейнер. В следующем вызове функции `register_azure_blob_container` значение `True` для флага `overwrite` означает, что любое ранее созданное хранилище данных с тем же именем будет перезаписано. 

Этот шаг можно изменить, указав ссылку на контейнер больших двоичных объектов, предоставив собственные значения для `datastore_name`, `container_name` и `account_name`.

```python
from azureml.core import Datastore
from azureml.core import Workspace

# Load workspace authorization details from config.json
ws = Workspace.from_config()

mnist_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="mnist_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata",
                      overwrite=True)
```

Теперь задайте хранилище данных по умолчанию в рабочей области в качестве выходного хранилища данных. Сюда будут сохраняться выходные данные вывода.

При создании рабочей области к ней по умолчанию подключаются [Файлы Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) и [хранилище BLOB-объектов](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) . Файлы Azure — это хранилище данных по умолчанию для рабочей области, но вы можете также использовать хранилище BLOB-объектов для хранения данных. Дополнительные сведения см. в статье [Выбор между большими двоичными объектами Azure, службой файлов Azure и дисками Azure](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

```python
def_data_store = ws.get_default_datastore()
```

### <a name="configure-data-inputs-and-outputs"></a>Настройка входных и выходных данных

Теперь вам нужно настроить входные и выходные данные, в том числе:

- Каталог, содержащий входные изображения.
- каталог, в котором хранится предварительно обученная модель;
- каталог, который содержит метки;
- каталог для выходных данных.

Класс `Dataset` в Машинном обучении Azure предназначен для изучения, преобразования и администрирования данных. Этот класс имеет два типа: `TabularDataset` и `FileDataset`. В нашем примере в качестве входных данных для шага конвейера пакетного вывода используется `FileDataset`. 

> [!NOTE] 
> Поддержка `FileDataset` для пакетного вывода в настоящее время ограничена хранилищем BLOB-объектов Azure. 

В пользовательском скрипте вывода вы также можете ссылаться на другие наборы данных. Например, можно обращаться из скрипта к меткам, чтобы добавлять их к изображениям, используя `Dataset.register` и `Dataset.get_by_name`.

Дополнительные сведения о наборах данных в Машинном обучении Azure см. в руководстве по [созданию наборов данных и получению доступа к ним (предварительная версия)](https://docs.microsoft.com/azure/machine-learning/service/how-to-create-register-datasets).

Объекты `PipelineData` нужны для передачи промежуточных данных между шагами конвейера. В нашем примере они используются для выходных данных вывода.

```python
from azureml.core.dataset import Dataset

mnist_ds_name = 'mnist_sample_data'

path_on_datastore = mnist_blob.path('mnist/')
input_mnist_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)
registered_mnist_ds = input_mnist_ds.register(ws, mnist_ds_name, create_new_version=True)
named_mnist_ds = registered_mnist_ds.as_named_input(mnist_ds_name)

output_dir = PipelineData(name="inferences", 
                          datastore=def_data_store, 
                          output_path_on_compute="mnist/results")
```

### <a name="set-up-a-compute-target"></a>Настройка целевой среды для вычислений

В Машинном обучении Azure *вычислительной средой* (или *целевым объектом вычислений*) считаются компьютеры или кластеры, которые выполняют вычислительные операции конвейера машинного обучения. Выполните следующий код, чтобы создать целевой объект [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) на основе ЦП.

```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpu-cluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size,
                                                                min_nodes = compute_min_nodes, 
                                                                max_nodes = compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

## <a name="prepare-the-model"></a>Подготовка модели

[Скачайте предварительно обученную модель классификации изображений](https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz) и извлеките ее в каталог `models`.

```python
import os
import tarfile
import urllib.request

model_dir = 'models'
if not os.path.isdir(model_dir):
    os.mkdir(model_dir)

url="https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz"
response = urllib.request.urlretrieve(url, "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall(model_dir)
```

Затем зарегистрируйте модель в рабочей области, чтобы она была доступна для удаленного вычислительного ресурса.

```python
from azureml.core.model import Model

# Register the downloaded model 
model = Model.register(model_path="models/",
                       model_name="mnist",
                       tags={'pretrained': "mnist"},
                       description="Mnist trained tensorflow model",
                       workspace=ws)
```

## <a name="write-your-inference-script"></a>Создание пользовательского скрипта вывода

>[!Warning]
>Следующий пример кода приведен в качестве примера, который включен в [пример записной книжки](https://aka.ms/batch-inference-notebooks). Вам следует создать собственный скрипт с учетом своего сценария.

Этот скрипт *должен* содержать две функции.
- `init()`: Эта функция применяется для всех затратных или повторяющихся операций подготовки к последующему выводу. Например, в ней можно загружать модель в глобальный объект.
-  `run(mini_batch)`: Эта функция будет выполняться для каждого экземпляра `mini_batch`.
    -  `mini_batch`: Пакетный вывод вызывает метод run и передает ему в качестве аргумента список либо Pandas DataFrame. Каждая запись в min_batch содержит одно из следующих значений: путь к файлу для входных данных в формате FileDataset и Pandas DataFrame для входных данных в формате TabularDataset.
    -  `response`: метод run() должен возвращать Pandas DataFrame или массив. Для append_row output_action эти возвращаемые элементы добавляются в общий выходной файл. Для summary_only содержимое элементов игнорируется. Для всех выходных действий каждый возвращаемый элемент обозначает один успешный вывод для входного элемента во входном мини-пакете. Пользователь должен убедиться в том, что в результат вывода включено достаточно данных, чтобы сопоставить входные данные для вывода. Выходные данные вывода будут записаны в выходной файл и для них не гарантируется правильный порядок. Для сопоставления со входными данными пользователю нужно использовать какой-либо ключ.

```python
# Snippets from a sample script.
# Refer to the accompanying digit_identification.py
# (https://aka.ms/batch-inference-notebooks)
# for the implementation script.

import os
import numpy as np
import tensorflow as tf
from PIL import Image
from azureml.core import Model


def init():
    global g_tf_sess

    # Pull down the model from the workspace
    model_path = Model.get_model_path("mnist")

    # Construct a graph to execute
    tf.reset_default_graph()
    saver = tf.train.import_meta_graph(os.path.join(model_path, 'mnist-tf.model.meta'))
    g_tf_sess = tf.Session()
    saver.restore(g_tf_sess, os.path.join(model_path, 'mnist-tf.model'))


def run(mini_batch):
    print(f'run method start: {__file__}, run({mini_batch})')
    resultList = []
    in_tensor = g_tf_sess.graph.get_tensor_by_name("network/X:0")
    output = g_tf_sess.graph.get_tensor_by_name("network/output/MatMul:0")

    for image in mini_batch:
        # Prepare each image
        data = Image.open(image)
        np_im = np.array(data).reshape((1, 784))
        # Perform inference
        inference_result = output.eval(feed_dict={in_tensor: np_im}, session=g_tf_sess)
        # Find the best probability, and add it to the result list
        best_result = np.argmax(inference_result)
        resultList.append("{}: {}".format(os.path.basename(image), best_result))

    return resultList
```

## <a name="build-and-run-the-batch-inference-pipeline"></a>Сборка и запуск конвейера пакетного вывода

Теперь все готово для сборки конвейера.

### <a name="prepare-the-run-environment"></a>Подготовка среды выполнения

Для начала укажите зависимости для скрипта. Этот объект потребуется позже при создании шага конвейера.

```python
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

batch_conda_deps = CondaDependencies.create(pip_packages=["tensorflow==1.13.1", "pillow"])

batch_env = Environment(name="batch_environment")
batch_env.python.conda_dependencies = batch_conda_deps
batch_env.docker.enabled = True
batch_env.docker.base_image = DEFAULT_GPU_IMAGE
batch_env.spark.precache_packages = False
```

### <a name="specify-the-parameters-for-your-batch-inference-pipeline-step"></a>Указание параметров для шага конвейера пакетного вывода

`ParallelRunConfig` назначается основной конфигурацией для вновь созданного экземпляра пакетного вывода `ParallelRunStep` в конвейере Машинного обучения Azure. Он пригодится вам как оболочка скрипта для настройки необходимых параметров, включая перечисленные ниже.
- `entry_script`: Локальный путь к пользовательскому скрипту, который будет выполняться параллельно на нескольких узлах. Если присутствует `source_directly`, используйте относительный путь. В противном случае используйте любой путь, доступный на компьютере.
- `mini_batch_size`: Размер мини-пакета, который передается в одном вызове `run()`. (Необязательный параметр; по умолчанию используется значение `1`.)
    - Для `FileDataset` здесь указывается количество файлов; минимальное допустимое значение — `1`. Несколько файлов можно объединить в один мини-пакет.
    - Для `TabularDataset` здесь указывается размер данных. Примеры допустимых значений: `1024`, `1024KB`, `10MB` и `1GB`. Мы рекомендуем использовать значение `1MB`. Обратите внимание, что мини-пакет из `TabularDataset` никогда не пересекает границы файлов. Предположим, что у вас есть CSV-файлы с разными размерами в пределах от 100 КБ до 10 МБ. Если задать `mini_batch_size = 1MB`, все файлы с размером меньше 1 МБ будут рассматриваться как один мини-пакет. Файлы с размером, превышающим 1 МБ, будут разбиты на несколько мини-пакетов.
- `error_threshold`: Количество ошибок записи для `TabularDataset` и сбоев чтения файлов для `FileDataset`, которые следует игнорировать во время обработки. Если общее количество ошибок для всего объема входных данных превысит это значение, задание будет остановлено. Пороговое количество ошибок применяется к общему объему входных данных, а не к отдельным мини-пакетам, которые передаются в метод `run()`. Используется диапазон `[-1, int.max]`. Часть `-1` указывает на то, что следует игнорировать все сбои во время обработки.
- `output_action`: Одно из следующих значений указывает, как будут организованы выходные данные.
    - `summary_only`: Выходные данные сохраняются в пользовательском скрипте. `ParallelRunStep` использует выходные данные только для вычисления порога ошибок.
    - `append_row`: Для всех входных файлов в выходной папке будет создан только один файл, куда будут добавляться все выходные данные, разделенные пустой строкой. Этому файлу присваивается имя parallel_run_step.txt.
- `source_directory`: Пути к папкам, которые содержат все файлы для выполнения в целевом объекте вычислений (необязательно).
- `compute_target`: Поддерживается только `AmlCompute`.
- `node_count`: Количество вычислительных узлов, которые будут использоваться для выполнения пользовательского скрипта.
- `process_count_per_node`: Количество процессов на каждом узле.
- `environment`: Определение среды Python. Вы можете настроить использование существующей среды Python или временной среды для указанного эксперимента. Также это определение может задавать необходимые зависимости приложения (необязательно).
- `logging_level`: Детализация журнала. Значения уровня детализации в порядке увеличения: `WARNING`, `INFO` и `DEBUG`. По умолчанию используется значение `INFO` (необязательно).
- `run_invocation_timeout`: Время вызова метода `run()` в секундах. По умолчанию используется значение `60`.

```python
from azureml.contrib.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    source_directory=scripts_folder,
    entry_script="digit_identification.py",
    mini_batch_size="5",
    error_threshold=10,
    output_action="append_row",
    environment=batch_env,
    compute_target=compute_target,
    node_count=4)
```

### <a name="create-the-pipeline-step"></a>Создание шага конвейера

Создайте шаг конвейера, используя сценарий, конфигурацию среды и параметры. Укажите целевой объект вычислений, который уже подключен к рабочей области, в качестве целевого объекта для выполнения скрипта. Используйте `ParallelRunStep`, чтобы создать шаг конвейера пакетного вывода, который принимает все следующие параметры.
- `name`: Имя шага со следующими ограничениями на именование: уникальность, от 3 до 32 символов, соответствие регулярному выражению ^\[a-z\]([-a-z0-9]*[a-z0-9])?$.
- `models`: Ноль или несколько имен моделей, которые уже зарегистрированы в реестре моделей Машинного обучения Azure.
- `parallel_run_config`: Объект `ParallelRunConfig`, как определено ранее.
- `inputs`: Один или несколько наборов данных Машинного обучения Azure с одним типом.
- `output`: Объект `PipelineData`, который соответствует каталогу для выходных данных.
- `arguments`: Список аргументов, переданных в пользовательский скрипт (необязательно).
- `allow_reuse`: Указывает, должен ли шаг повторно использовать предыдущие результаты при запуске с теми же параметрами и входными данными. Если этот параметр имеет значение `False`, то во время выполнения конвейера для этого шага всегда будет создаваться новый запуск. (Необязательный параметр; по умолчанию используется значение `True`.)

```python
from azureml.contrib.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="batch-mnist",
    models=[model],
    parallel_run_config=parallel_run_config,
    inputs=[named_mnist_ds],
    output=output_dir,
    arguments=[],
    allow_reuse=True
)
```

### <a name="run-the-pipeline"></a>Запуск конвейера

Теперь запустите конвейер. Прежде всего создайте объект `Pipeline`, используя ссылку на вашу рабочую область и созданный шаг конвейера. Параметр `steps` является массивом шагов. В этом случае для пакетной оценки выполняется только один шаг. Чтобы создать конвейеры с несколькими шагами, разместите шаги по порядку в этом массиве.

Затем используйте функцию `Experiment.submit()`, чтобы передать конвейер для выполнения.

```python
from azureml.pipeline.core import Pipeline
from azureml.core.experiment import Experiment

pipeline = Pipeline(workspace=ws, steps=[parallelrun_step])
pipeline_run = Experiment(ws, 'digit_identification').submit(pipeline)
```

## <a name="monitor-the-batch-inference-job"></a>Мониторинг задания пакетного вывода

Задание пакетного вывода может выполняться длительное время. В этом примере ход выполнения отслеживается с помощью мини-приложения Jupyter. Вы также можете управлять ходом выполнения задания, используя следующие средства:

* Студии машинного обучения Azure. 
* консольные выходные данные из объекта [`PipelineRun`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.run.pipelinerun?view=azure-ml-py).

```python
from azureml.widgets import RunDetails
RunDetails(pipeline_run).show()

pipeline_run.wait_for_completion(show_output=True)
```

## <a name="next-steps"></a>Дополнительная информация

Чтобы наблюдать весь процесс, воспользуйтесь [записной книжкой пакетного вывода](https://aka.ms/batch-inference-notebooks). 

См. инструкции по [отладке и устранению неполадок с конвейерами](how-to-debug-pipelines.md).

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

