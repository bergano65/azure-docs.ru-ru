---
title: Выполнение пакетного прогнозирования на больших объемах данных
titleSuffix: Azure Machine Learning
description: Сведения о том, как асинхронно выполнять вывод больших объемов данных, используя ParallelRunStep в Машинном обучении Azure. Функция ParallelRunStep поддерживает возможности параллельной обработки. Она оптимизирована для сценариев вывода больших данных с высокой пропускной способностью без вмешательства пользователя.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: jmartens, larryfr
ms.author: tracych
author: tracychms
ms.date: 08/14/2020
ms.custom: Build2020, devx-track-python
ms.openlocfilehash: dddb332498f41437eba77d75c38218c58b8c8379
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88507120"
---
# <a name="run-batch-inference-on-large-amounts-of-data-by-using-azure-machine-learning"></a>Выполнение пакетного вывода больших объемов данных с помощью Машинного обучения Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье описывается, как параллельно выполнять модель Машинного обучения Azure для быстрой обработки больших объемов данных. 

Вывод больших наборов данных или сложных моделей может занимать много времени. Класс `ParallelRunStep` позволяет выполнять параллельную обработку, потенциально ускоряя получение результатов. Даже если одна оценка выполняется достаточно быстро, многие сценарии (обнаружение объектов, обработка видео, обработка естественного языка и т. д.) предполагают выполнение множества вычислений. 

С `ParallelRunStep` вы можете просто масштабировать пакетный вывод на крупных кластерах компьютеров. Такие кластеры могут обрабатывать терабайты структурированных или неструктурированных данных с повышенной производительностью и оптимизированными расходами.

В этой статье описаны следующие задачи:

> 1. Настройка ресурсов машинного обучения
> 1. Настройка входных и выходных данных пакетного вывода
> 1. Подготовка предварительно обученной модели классификации изображений на основе набора данных [MNIST](https://publicdataset.azurewebsites.net/dataDetail/mnist/) 
> 1.  Создание пользовательского скрипта вывода
> 1. Создание [конвейера машинного обучения](concept-ml-pipelines.md), содержащего функцию ParallelRunStep пакетный вывод, на тестовых изображениях MNIST 
> 1. Повторная отправка пакетного вывода с новыми входными данными и параметрами 
> 1. Просмотрите результаты.

## <a name="prerequisites"></a>Предварительные требования

* Если у вас еще нет подписки Azure, создайте бесплатную учетную запись, прежде чем начинать работу. Опробуйте [бесплатную или платную версию Машинного обучения Azure](https://aka.ms/AMLFree).

* Чтобы быстро начать работу, вы можете выполнить инструкции из [руководства по установке](tutorial-1st-experiment-sdk-setup.md), если у вас еще нет рабочей области Машинного обучения Azure. 

* Сведения о том, как управлять средой и зависимостями, см. в [этом практическом руководстве](how-to-configure-environment.md) по настройке локальной среды.

## <a name="set-up-machine-learning-resources"></a>Настройка ресурсов машинного обучения

Следующие действия позволяют настроить ресурсы машинного обучения, которые потребуются для запуска конвейера пакетного вывода:

- Подключение к рабочей области
- Создание или подключение существующего ресурса вычислений

### <a name="configure-workspace"></a>Настройка рабочей области

В существующей рабочей области создайте объект. `Workspace.from_config()` считывает файл config.json и загружает данные в объект с именем ws.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
```

> [!IMPORTANT]
> Этот фрагмент кода ищет конфигурацию рабочей области в текущем или родительском каталоге. Дополнительные сведения о создании рабочей области см. в статье [Создание рабочих областей машинного обучения Azure и управление ими](how-to-manage-workspace.md). Дополнительные сведения о сохранении конфигурации в файле см. в разделе [Создание файла конфигурации рабочей области](how-to-configure-environment.md#workspace).

### <a name="create-a-compute-target"></a>Создание целевого объекта вычислений

В Машинном обучении Azure *вычислительной средой* (или *целевым объектом вычислений*) считаются компьютеры или кластеры, которые выполняют вычислительные операции конвейера машинного обучения. Выполните следующий код, чтобы создать целевой объект [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) на основе ЦП.

```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpucluster")
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

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

## <a name="configure-inputs-and-output"></a>Настройка входных и выходных данных

### <a name="create-a-datastore-with-sample-images"></a>Создание хранилища данных с примерами изображений

Извлеките оценочный набор данных MNIST из общедоступного контейнера BLOB-объектов `sampledata`, который размещен в учетной записи с именем `pipelinedata`. Создайте хранилище данных с именем `mnist_datastore`, которое указывает на этот контейнер. В следующем вызове функции `register_azure_blob_container` значение `True` для флага `overwrite` означает, что любое ранее созданное хранилище данных с тем же именем будет перезаписано. 

Этот шаг можно изменить, указав ссылку на контейнер больших двоичных объектов, предоставив собственные значения для `datastore_name`, `container_name` и `account_name`.

```python
from azureml.core import Datastore
from azureml.core import Workspace

mnist_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="mnist_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata",
                      overwrite=True)
```

Теперь задайте хранилище данных по умолчанию в рабочей области в качестве выходного хранилища данных. Сюда будут сохраняться выходные данные вывода.

При создании рабочей области к ней по умолчанию подключаются [Файлы Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) и [хранилище BLOB-объектов](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) . Файлы Azure — это хранилище данных по умолчанию для рабочей области, но можно также использовать хранилище BLOB-объектов для хранения данных. Дополнительные сведения см. в статье [Выбор между большими двоичными объектами Azure, службой файлов Azure и дисками Azure](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

```python
def_data_store = ws.get_default_datastore()
```

### <a name="create-the-data-inputs"></a>Создание входных данных

Входные данные для пакетного вывода — это данные, которые необходимо секционировать для параллельной обработки. Конвейер пакетного вывода данных принимает входные данные через [`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py).

`Dataset` в Машинном обучении Azure предназначен для изучения, преобразования и администрирования данных. Существует два типа ключей: [`TabularDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) и [`FileDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py). В этом примере в качестве входных данных будет использоваться `FileDataset`. `FileDataset` предоставляет возможность загрузки или подключения файлов для вычислений. Создавая набор данных, вы одновременно создаете ссылку на расположение источника данных. Любые преобразования разбиения, примененные к такому набору данных, будут сохранены и в исходном наборе данных. Данные хранятся только в исходном расположении, а значит не потребуется лишних расходов на хранение.

Дополнительные сведения о наборах данных в Машинном обучении Azure см. в руководстве по [созданию наборов данных и получению доступа к ним (предварительная версия)](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets).

```python
from azureml.core.dataset import Dataset

path_on_datastore = mnist_blob.path('mnist/')
input_mnist_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)
```

Чтобы использовать динамические входные данные при запуске конвейера пакетного вывода, можно определить входные данные `Dataset` как [`PipelineParameter`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py). Входной набор данных можно указывать каждый раз при повторной отправке конвейера пакетного вывода.

```python
from azureml.data.dataset_consumption_config import DatasetConsumptionConfig
from azureml.pipeline.core import PipelineParameter

pipeline_param = PipelineParameter(name="mnist_param", default_value=input_mnist_ds)
input_mnist_ds_consumption = DatasetConsumptionConfig("minist_param_config", pipeline_param).as_mount()
```

### <a name="create-the-output"></a>Создание выходных данных

Объекты [`PipelineData`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) нужны для передачи промежуточных данных между шагами конвейера. В нашем примере они используются для выходных данных вывода.

```python
from azureml.pipeline.core import Pipeline, PipelineData

output_dir = PipelineData(name="inferences", datastore=def_data_store)
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

Затем зарегистрируйте модель в рабочей области, чтобы она была доступна для вычислительного ресурса.

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
>Следующий пример кода приведен в качестве примера, который включен в [пример записной книжки](https://aka.ms/batch-inference-notebooks). Вам следует создать собственный скрипт для своего сценария.

Этот скрипт *должен* содержать две функции.
- `init()`: Эта функция применяется для всех затратных или повторяющихся операций подготовки к последующему выводу. Например, в ней можно загружать модель в глобальный объект. Эта функция будет вызываться только один раз в начале процесса.
-  `run(mini_batch)`: Эта функция будет выполняться для каждого экземпляра `mini_batch`.
    -  `mini_batch`: `ParallelRunStep` вызывает метод run и передает ему в качестве аргумента список либо Pandas `DataFrame`. Каждая запись в mini_batch содержит одно из следующих значений: путь к файлу для входных данных в формате `FileDataset` или Pandas `DataFrame` для входных данных в формате `TabularDataset`.
    -  `response`: метод run() должен возвращать Pandas `DataFrame` или массив. Для append_row output_action эти возвращаемые элементы добавляются в общий выходной файл. Для summary_only содержимое элементов игнорируется. Для всех выходных действий каждый возвращаемый элемент обозначает один успешный запуск входного элемента во входном мини-пакете. Убедитесь в том, что в результат выполнения включено достаточно данных, чтобы сопоставить входные данные с результатом вывода. Выходные данные будут записаны в выходной файл, и для них не гарантируется правильный порядок. Для сопоставления со входными данными нужно использовать какой-либо ключ.

```python
%%writefile digit_identification.py
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

Если у вас есть другой файл или папка в том же каталоге, что и скрипт вывода, можно сослаться на него, найдя текущий рабочий каталог.

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

## <a name="build-and-run-the-pipeline-containing-parallelrunstep"></a>Сборка и запуск конвейера, содержащего ParallelRunStep

Теперь у вас есть все необходимое: входные данные, модель, выходные данные и скрипт вывода. Давайте создадим конвейер пакетного вывода, содержащий скрипт ParallelRunStep.

### <a name="prepare-the-environment"></a>Подготовка среды

Для начала укажите зависимости для скрипта. Это позволяет установить пакеты PIP, а также настроить среду.

Обязательно включайте **azureml-core** и **azureml-dataset-runtime[pandas, fuse]** в список пакетов pip. Если вы используете пользовательский образ Docker (user_managed_dependencies=True), также необходимо установить Conda.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

batch_conda_deps = CondaDependencies.create(pip_packages=["tensorflow==1.15.2", "pillow", 
                                                          "azureml-core", "azureml-dataset-runtime[pandas, fuse]"])

batch_env = Environment(name="batch_environment")
batch_env.python.conda_dependencies = batch_conda_deps
batch_env.docker.enabled = True
batch_env.docker.base_image = DEFAULT_GPU_IMAGE
```

### <a name="specify-the-parameters-using-parallelrunconfig"></a>Укажите параметры с помощью скрипта ParallelRunConfig

`ParallelRunConfig` — это основная конфигурация для экземпляра `ParallelRunStep` в конвейере Машинного обучения Azure. Он пригодится вам как оболочка скрипта для настройки необходимых параметров, включая перечисленные ниже записи:
- `entry_script`: Локальный путь к пользовательскому скрипту, который будет выполняться параллельно на нескольких узлах. Если присутствует `source_directory`, используйте относительный путь. В противном случае используйте любой путь, доступный на компьютере.
- `mini_batch_size`: Размер мини-пакета, который передается в одном вызове `run()`. (Необязательный параметр; по умолчанию заданы `10` файлов для `FileDataset` и `1MB` для `TabularDataset`.)
    - Для `FileDataset` здесь указывается количество файлов; минимальное допустимое значение — `1`. Несколько файлов можно объединить в один мини-пакет.
    - Для `TabularDataset` здесь указывается размер данных. Примеры допустимых значений: `1024`, `1024KB`, `10MB` и `1GB`. Мы рекомендуем использовать значение `1MB`. Мини-пакет из `TabularDataset` никогда не пересекает границы файлов. Предположим, что у вас есть CSV-файлы с разными размерами в пределах от 100 КБ до 10 МБ. Если задать `mini_batch_size = 1MB`, все файлы с размером меньше 1 МБ будут рассматриваться как один мини-пакет. Файлы с размером, превышающим 1 МБ, будут разбиты на несколько мини-пакетов.
- `error_threshold`: Количество ошибок записи для `TabularDataset` и сбоев чтения файлов для `FileDataset`, которые следует игнорировать во время обработки. Если общее количество ошибок для всего объема входных данных превысит это значение, задание будет прервано. Пороговое количество ошибок применяется к общему объему входных данных, а не к отдельному мини-пакету, которые передаются в метод `run()`. Используется диапазон `[-1, int.max]`. Часть `-1` указывает на то, что следует игнорировать все сбои во время обработки.
- `output_action`: Одно из следующих значений указывает, как будут организованы выходные данные.
    - `summary_only`: Выходные данные сохраняются в пользовательском скрипте. `ParallelRunStep` использует выходные данные только для вычисления порога ошибок.
    - `append_row`: Для всех входных данных в выходной папке будет создан только один файл, куда будут добавляться все выходные данные, разделенные пустой строкой.
- `append_row_file_name`: Чтобы настроить имя выходного файла для append_row output_action (необязательно; значение по умолчанию — `parallel_run_step.txt`).
- `source_directory`: Пути к папкам, которые содержат все файлы для выполнения в целевом объекте вычислений (необязательно).
- `compute_target`: Поддерживается только `AmlCompute`.
- `node_count`: Количество вычислительных узлов, которые будут использоваться для выполнения пользовательского скрипта.
- `process_count_per_node`: Количество процессов на каждом узле. Рекомендуется устанавливать в значение, равное количеству GPU или ЦП на одном узле (необязательно; значение по умолчанию — `1`).
- `environment`: Определение среды Python. Вы можете настроить использование существующей среды Python или временной среды. Также это определение может задавать необходимые зависимости приложения (необязательно).
- `logging_level`: Детализация журнала. Значения уровня детализации в порядке увеличения: `WARNING`, `INFO` и `DEBUG`. (необязательный параметр; по умолчанию используется значение `INFO`.)
- `run_invocation_timeout`: Время вызова метода `run()` в секундах. (необязательный параметр, значение по умолчанию — `60`)
- `run_max_try`: Максимальное число попыток `run()` для mini-batch. Сбой `run()` при возникновении исключения или если при достижении `run_invocation_timeout` ничего не возвращается (необязательно; значение по умолчанию — `3`). 

Можно указать `mini_batch_size`, `node_count`, `process_count_per_node`, `logging_level`, `run_invocation_timeout` и `run_max_try` как `PipelineParameter`, чтобы при повторной отправке запуска конвейера можно было точно настроить значения параметров. В этом примере используется `PipelineParameter` для `mini_batch_size` и `Process_count_per_node`, и эти значения будут изменены при повторной отправке позже. 

В этом примере предполагается, что вы используете скрипт `digit_identification.py`, рассмотренный ранее. Если вы используете собственный скрипт, измените параметры `source_directory` и `entry_script` соответствующим образом.

```python
from azureml.pipeline.core import PipelineParameter
from azureml.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    source_directory='.',
    entry_script="digit_identification.py",
    mini_batch_size=PipelineParameter(name="batch_size_param", default_value="5"),
    error_threshold=10,
    output_action="append_row",
    append_row_file_name="mnist_outputs.txt",
    environment=batch_env,
    compute_target=compute_target,
    process_count_per_node=PipelineParameter(name="process_count_param", default_value=2),
    node_count=2)
```

### <a name="create-the-parallelrunstep"></a>Создание ParallelRunStep

Создайте ParallelRunStep, используя скрипт, конфигурацию среды и параметры. Укажите целевой объект вычислений, который уже подключен к рабочей области, в качестве целевого объекта для выполнения скрипта вывода. Используйте `ParallelRunStep`, чтобы создать шаг конвейера пакетного вывода, который принимает все следующие параметры.
- `name`: Имя шага со следующими ограничениями на именование: уникальность, от 3 до 32 символов, соответствие регулярному выражению ^\[a-z\]([-a-z0-9]*[a-z0-9])?$.
- `parallel_run_config`: Объект `ParallelRunConfig`, как определено ранее.
- `inputs`: Один или несколько однотипных наборов данных Машинного обучения Azure, которые должны быть секционированы для параллельной обработки.
- `side_inputs`: Один или несколько эталонных данных или наборов данных, используемых в качестве дополнительных входных данных без необходимости секционирования.
- `output`: Объект `PipelineData`, который соответствует каталогу для выходных данных.
- `arguments`: Список аргументов, переданных в пользовательский скрипт. Используйте unknown_args, чтобы получить их в начальном сценарии (необязательно).
- `allow_reuse`: Указывает, должен ли шаг повторно использовать предыдущие результаты при запуске с теми же параметрами и входными данными. Если этот параметр имеет значение `False`, то во время выполнения конвейера для этого шага всегда будет создаваться новый запуск. (необязательный параметр; по умолчанию используется значение `True`.)

```python
from azureml.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="predict-digits-mnist",
    parallel_run_config=parallel_run_config,
    inputs=[input_mnist_ds_consumption],
    output=output_dir,
    allow_reuse=True
)
```
### <a name="create-and-run-the-pipeline"></a>создание и запуск конвейера

Теперь запустите конвейер. Сначала создайте объект [`Pipeline`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py), используя ссылку на вашу рабочую область и созданный шаг конвейера. Параметр `steps` является массивом шагов. В этом случае для пакетного вывода выполняется только один шаг. Чтобы создать конвейеры с несколькими шагами, разместите шаги по порядку в этом массиве.

Затем используйте функцию `Experiment.submit()`, чтобы передать конвейер для выполнения.

```python
from azureml.pipeline.core import Pipeline
from azureml.core.experiment import Experiment

pipeline = Pipeline(workspace=ws, steps=[parallelrun_step])
experiment = Experiment(ws, 'digit_identification')
pipeline_run = experiment.submit(pipeline)
```

## <a name="monitor-the-batch-inference-job"></a>Мониторинг задания пакетного вывода

Задание пакетного вывода может выполняться длительное время. В этом примере ход выполнения отслеживается с помощью мини-приложения Jupyter. Вы также можете отслеживать ход выполнения задания, используя следующие средства:

* Студии машинного обучения Azure. 
* консольные выходные данные из объекта [`PipelineRun`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.run.pipelinerun?view=azure-ml-py).

```python
from azureml.widgets import RunDetails
RunDetails(pipeline_run).show()

pipeline_run.wait_for_completion(show_output=True)
```

## <a name="resubmit-a-run-with-new-data-inputs-and-parameters"></a>Повторная отправка вывода с новыми входными данными и параметрами

Поскольку входные данные и несколько конфигураций настроены как `PipelineParameter`, можно повторно отправить пакетный вывод с другим входным набором данных и настроить его параметры, не создавая совершенно новый конвейер. Вы будете использовать то же хранилище данных, но использовать только один образ как входные данные.

```python
path_on_datastore = mnist_blob.path('mnist/0.png')
single_image_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)

pipeline_run_2 = experiment.submit(pipeline, 
                                   pipeline_parameters={"mnist_param": single_image_ds, 
                                                        "batch_size_param": "1",
                                                        "process_count_param": 1}
)

pipeline_run_2.wait_for_completion(show_output=True)
```
## <a name="view-the-results"></a>Просмотр результатов

Результаты приведенного выше запуска записываются в хранилище данных `DataStore`, указанное в объекте `PipelineData`, как выходные данные, которые в этом случае называются *выводами*. Результаты хранятся в контейнере больших двоичных объектов по умолчанию. Вы можете перейти к учетной записи хранения и просмотреть ее с помощью Обозревателя службы хранилища (путь к файлу: azureml-blobstore-*GUID*/azureml/*RunId*/*output_dir*).

Вы также можете скачать эти данные для просмотра результатов. Ниже приведен пример кода для просмотра первых 10 строк.

```python
import pandas as pd
import tempfile

batch_run = pipeline_run.find_step_run(parallelrun_step.name)[0]
batch_output = batch_run.get_output_data(output_dir.name)

target_dir = tempfile.mkdtemp()
batch_output.download(local_path=target_dir)
result_file = os.path.join(target_dir, batch_output.path_on_datastore, parallel_run_config.append_row_file_name)

df = pd.read_csv(result_file, delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
print("Prediction has ", df.shape[0], " rows")
df.head(10) 
```

## <a name="next-steps"></a>Дальнейшие действия

Чтобы наблюдать весь процесс, воспользуйтесь [записной книжкой пакетного вывода](https://aka.ms/batch-inference-notebooks). 

См. инструкции по [отладке и устранению неполадок с ParallelRunStep](how-to-debug-parallel-run-step.md).

См. инструкции по [отладке и устранению неполадок с конвейерами](how-to-debug-pipelines.md).

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

