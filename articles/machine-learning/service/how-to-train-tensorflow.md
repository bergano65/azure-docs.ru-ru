---
title: Обучение и зарегистрируйте модели TensorFlow
titleSuffix: Azure Machine Learning service
description: В этой статье показано, как обучать и зарегистрируйте модели TensorFlow с помощью службы машинного обучения Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.date: 06/10/2019
ms.custom: seodec18
ms.openlocfilehash: e1f587785b06f10bab42a425b0910d4f25fddf9f
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165544"
---
# <a name="train-and-register-tensorflow-models-at-scale-with-azure-machine-learning-service"></a>Обучение и зарегистрируйте модели TensorFlow в масштабе с помощью службы машинного обучения Azure

В этой статье показано, как обучать и зарегистрируйте модели TensorFlow с помощью службы машинного обучения Azure. Она использует популярного [набора данных MNIST](http://yann.lecun.com/exdb/mnist/) для классификации рукописных цифр, с помощью глубокой нейронной сети, созданные с помощью [библиотеки TensorFlow Python](https://www.tensorflow.org/overview).

TensorFlow — это платформа вычислений открытым исходным кодом, обычно используется для создания глубоких нейронных сетей (DNN). С помощью службы машинного обучения Azure вы можете быстро выполнить развертывание заданий обучения с открытым исходным кодом, с помощью эластичных облачных вычислительных ресурсов. Вы также можете отслеживать свои учебных запусков, моделях версии развертывание моделей и многое другое.

При разработке модели TensorFlow из нуля, или вам нужно добавить существующую модель в облаке, службы машинного обучения Azure поможет вам создавать готовые модели.

## <a name="prerequisites"></a>Технические условия

Выполните этот код на любой из этих сред:

 - Machine Learning записные книжки Azure виртуальной Машины — нет файлы для загрузки или установки не требуется

     - Завершить [быстрого запуска облачной записной книжки](quickstart-run-cloud-notebook.md) создание специальных ноутбуков сервера предварительно загруженным с помощью пакета SDK и репозиторий с примером.
    - В папке samples на сервере записной книжки, найти записную книжку выполненной и развернутое, перейдя к этому каталогу: **практические-в-использование azureml > обучения с помощью глубокого обучения > train-hyperparameter-tune-deploy-with-tensorflow**папки. 
 
 - Собственный сервер Jupyter Notebook

     - [Установка Azure Machine Learning пакета SDK для Python](setup-create-workspace.md#sdk)
    - [Создайте файл конфигурации рабочей области](setup-create-workspace.md#write-a-configuration-file)
    - [Скачайте файлы образца скрипта](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow) `mnist-tf.py` и `utils.py`
     
    Вы также найдете завершенного [версии записной книжки Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) данного руководства на странице образцов GitHub. Записная книжка включает расширенный, освещая интеллектуальной настройки гиперпараметров, развертывание модели и записной книжки мини-приложения.

## <a name="set-up-the-experiment"></a>Настроить эксперимент

В этом разделе устанавливает обучающий эксперимент с помощью загрузки пакетов требуется python, инициализация рабочей области, создания эксперимента и отправки данных для обучения и скриптов обучения.

### <a name="import-packages"></a>Импорт пакетов

Во-первых импортируйте необходимые библиотеки Python.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Инициализация рабочей области

[Рабочей области машинного обучения Azure службы](concept-workspace.md) — это ресурс верхнего уровня для службы. Он предоставляет централизованное расположение для работы с все артефакты, созданные. В пакете SDK для Python, можно получить доступ к артефактам рабочей области путем создания [ `workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) объекта.

Создать объект рабочей области из `config.json` файла, созданного в [предварительных](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>Создание эксперимента

Создание эксперимента и папку для хранения сценариев обучения. В этом примере Создайте эксперимент, который называется «tf-mnist».

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
```

### <a name="upload-dataset-and-scripts"></a>Передача набора данных и сценариев

[Хранилище данных](how-to-access-data.md) — это место, где можно хранить данные и осуществляется подключаются или при копировании данных на целевой объект вычислений. Каждая рабочая область предоставляет хранилище данных по умолчанию. Передача данных и скриптов обучения в хранилище данных, чтобы они могут быть легко доступны во время обучения.

1. Скачайте набор данных MNIST локально.

    ```Python
    os.makedirs('./data/mnist', exist_ok=True)

    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz', filename = './data/mnist/train-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz', filename = './data/mnist/train-labels.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename = './data/mnist/test-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename = './data/mnist/test-labels.gz')
    ```

1. Передача набора данных MNIST в хранилище данных по умолчанию.

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/mnist', target_path='mnist', overwrite=True, show_progress=True)
    ```

1. Отправить скрипт обучения TensorFlow, `tf_mnist.py`и вспомогательный файл `utils.py`.

    ```Python
    shutil.copy('./tf_mnist.py', script_folder)
    shutil.copy('./utils.py', script_folder)
    ```

## <a name="create-a-compute-target"></a>Создание целевого объекта вычислений

Создание целевого объекта вычислений для задания TensorFlow под управлением. В этом примере создания вычислительного кластера с поддержкой GPU машинного обучения Azure.

```Python
cluster_name = "gpucluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

Дополнительные сведения о целевых объектах вычислений см. в разделе [Какова целевого объекта вычислений](concept-compute-target.md) статьи.

## <a name="create-a-tensorflow-estimator"></a>Создание оценки TensorFlow

[TensorFlow estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) предоставляет простой способ запуска задание обучения TensorFlow в целевой объект вычислений.

Оценщик TensorFlow реализуется с помощью универсального [ `estimator` ](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) класс, который может использоваться для поддержки любой платформы. Дополнительные сведения об обучении моделей с помощью универсальный механизм оценки, см. в разделе [обучения моделей с машинным обучением Azure, с помощью механизма оценки](how-to-train-ml-models.md)

Если сценарий обучения требуются дополнительные pip или conda пакеты для запуска, может иметь пакетами, устанавливаемыми на итоговый образ docker, передав их имена через `pip_packages` и `conda_packages` аргументы.

```Python
script_params = {
    '--data-folder': ws.get_default_datastore().as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.01
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='tf_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 use_gpu=True)
```

## <a name="submit-a-run"></a>Запуске

[Выполнения объекта](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) предоставляет интерфейс для журнала выполнения, пока выполняется задание, и после его завершения.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

Как выполняется запуска, он проходит через следующие этапы:

- **Подготовка**: В соответствии с оценщика TensorFlow создается образ docker. Изображение отправлены в реестр контейнеров в рабочей области и кэшируются для выполнения более поздней версии. Также будут отправлены в журнал выполнения и отслеживать ход выполнения можно просмотреть журналы.

- **Масштабирование**. Кластера пытается увеличить масштаб, если для кластера Batch AI требуется больше узлов для выполнения запуска, чем в настоящее время доступны.

- **Running**. Все сценарии в папке сценария передаются целевого объекта вычислений, хранилищ данных отправляются подключен или копируются, а также выполняется entry_script. Выходные данные stdout и. / Папка журналов будут отправлены в журнал выполнения и может использоваться для отслеживания выполнения.

- **Постобработка**. . / Outputs папки выполнения, копируются в журнал выполнения.

## <a name="register-or-download-a-model"></a>Зарегистрировать или загрузить модель

После обучения модели, можно зарегистрировать его в рабочую область. Регистрация модели позволяет хранилища и версию модели в рабочей области для упрощения [моделей управления и развертывания](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='tf-dnn-mnist', model_path='outputs/model')
```

Также можно загрузить локальную копию модели с помощью объекта выполнения. В сценарий обучения `mnist-tf.py`, объект заставки TensorFlow сохраняет модель в локальную папку (локальным для целевого объекта вычислений). Объект выполнения можно использовать для загрузки копии.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="distributed-training"></a>Распределенное обучение

[ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) Estimator также поддерживает распределенного обучения по кластерам ЦП и GPU. Вы можете легко запустить распределенных заданий TensorFlow и службы машинного обучения Azure будет администрировать оркестрацию для вас.

Служба машинного обучения Azure поддерживает два метода распределенного обучения в TensorFlow.

- [На основе MPI](https://www.open-mpi.org/) распределенного обучения с помощью [Horovod](https://github.com/uber/horovod) framework
- Собственный [распределенных TensorFlow](https://www.tensorflow.org/deploy/distributed) с помощью параметра метода сервера

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) — фреймворк с открытым исходным кодом для распределенного обучения разработанный Uber. Он предлагает простой способ распределенных заданий GPU TensorFlow.

Чтобы использовать Horovod, укажите [ `MpiConfiguration` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) для объекта `distributed_training` параметра конструктору объекта TensorFlow. Этот параметр гарантирует, что библиотека Horovod устанавливается для использования в скрипте обучения.

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),
                      framework_version='1.13',
                      use_gpu=True)
```

### <a name="parameter-server"></a>Сервер параметров

Можно также запустить [собственное распределенное обучение TensorFlow](https://www.tensorflow.org/deploy/distributed), которое использует модель сервера параметров. В этом методе обучение проводится в кластере серверов параметров и рабочих ролей. Во время обучения рабочие роли вычисляют градиенты, а серверы параметров выполняют статистическую обработку градиентов.

Чтобы использовать метод параметр сервера, укажите [ `TensorflowConfiguration` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py) для объекта `distributed_training` параметра конструктору объекта TensorFlow.

```Python
from azureml.train.dnn import TensorFlow

distributed_training = TensorflowConfiguration()
distributed_training.worker_count = 2

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=distributed_training,
                      use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="define-cluster-specifications-in-tfconfig"></a>Определить характеристики кластера в «TF_CONFIG»

Вам также потребуется сетевых адресов и портов кластера для [ `tf.train.ClusterSpec` ](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec), поэтому машинного обучения Azure задает `TF_CONFIG` переменной среды для вас.

Переменная среды `TF_CONFIG` представляет собой строку JSON. Ниже приведен пример переменной для сервера параметров.

```JSON
TF_CONFIG='{
    "cluster": {
        "ps": ["host0:2222", "host1:2222"],
        "worker": ["host2:2222", "host3:2222", "host4:2222"],
    },
    "task": {"type": "ps", "index": 0},
    "environment": "cloud"
}'
```

Для высокого уровня TensorFlow [ `tf.estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator) анализирует TensorFlow API, `TF_CONFIG` переменной и сборками, кластер по спецификациям для вас.

TensorFlow на нижнем уровне основными интерфейсами API для обучения, анализа `TF_CONFIG` переменной и построения `tf.train.ClusterSpec` в коде обучения.

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы обучена и зарегистрировали модели TensorFlow в службе машинного обучения Azure. Чтобы узнать, как развернуть модель, перейдите к статье развертывания нашей модели.

> [!div class="nextstepaction"]
> [Как и способа развертывания модели](how-to-deploy-and-where.md)
