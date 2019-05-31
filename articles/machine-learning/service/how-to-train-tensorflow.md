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
ms.date: 05/28/2019
ms.custom: seodec18
ms.openlocfilehash: f3d675d0eac1255974995fd7717192ec6a21bac1
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66400227"
---
# <a name="use-azure-machine-learning-service-to-train-and-register-tensorflow-models"></a>Использовать службы машинного обучения Azure для обучения и зарегистрируйте модели TensorFlow

В этой статье показано, как обучать и зарегистрируйте модели TensorFlow с помощью службы машинного обучения Azure. Мы будем использовать популярный [набора данных MNIST](http://yann.lecun.com/exdb/mnist/) для классификации рукописных цифр, с помощью глубоких нейронных сетях на основе TensorFlow.

С помощью службы машинного обучения Azure вы сможете быстро масштабировать свои задания обучения с открытым исходным кодом, с помощью эластичных облачных вычислительных ресурсов. Также можно отслеживать в учебных запусков, версии модели, развертывания моделей и многое другое. При разработке модели TensorFlow из нуля, или вам нужно добавить существующую модель в облаке, службы машинного обучения Azure является ли здесь для построения моделей готовый к выпуску.

## <a name="prerequisites"></a>Технические условия

- установить пакет SDK для Машинного обучения Azure для Python.
- Необязательно: Создание файла конфигурации рабочей области
- Скачайте [образцы файлов скрипта](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow) `mnist-tf.py` и `utils.py`

Можно выполнить [руководство по установке пакета SDK для Python](setup-create-workspace.md#sdk) пошаговые инструкции по настройке среды. Образцы файлов обучения можно найти на наших [страница примеров GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow) с указанием расширенного записной книжки Juypter версии этого руководства.

## <a name="set-up-the-experiment"></a>Настроить эксперимент

### <a name="import-packages"></a>Импорт пакетов

Во-первых необходимо импортировать все необходимые библиотеки Python.

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

Объект рабочей области — это ресурс верхнего уровня для службы. Он предоставляет централизованное расположение для работы с все артефакты, созданные.

Если вы выполнили необязательный шаг в [предварительных](#prerequisites), можно использовать `Workspace.from_config()` быстро создать объект рабочей области от деталей, хранятся в файле конфигурации.

```Python
ws = Workspace.from_config()
```

Можно также созданием рабочей области явным образом.

```Python
ws = Workspace.create(name='<workspace-name>',
                      subscription_id='<azure-subscription-id>',
                      resource_group='<choose-a-resource-group>',
                      create_resource_group=True,
                      location='<select-location>' # For example: 'eastus2'
                      )
```

### <a name="create-an-experiment"></a>Создание эксперимента

Создание эксперимента и папку для хранения сценариев обучения. В этом примере создаете эксперимент, который называется «tf-mnist»

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
```

### <a name="upload-dataset-and-scripts"></a>Передача набора данных и сценариев

[Хранилище данных](how-to-access-data.md) — это место, где можно хранить данные и осуществляется подключаются или при копировании данных на целевой объект вычислений. Каждая рабочая область предоставляет хранилище данных по умолчанию. Мы отправите наших данных и скриптов обучения, чтобы они могут быть легко доступны во время обучения.

1. Загрузка набора данных MNIST локально

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

Создание целевого объекта вычислений для задания TensorFlow под управлением. В этом примере мы создадим кластер AmlCompute с поддержкой GPU. Список доступных учебных целевых объектов вычислений, см. в разделе [в этой статье](how-to-set-up-training-targets.md#compute-targets-for-training)

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

## <a name="create-a-tensorflow-estimator"></a>Создание оценки TensorFlow

[TensorFlow estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) предоставляет простой способ запуска задание обучения TensorFlow в целевой объект вычислений. Он автоматически предоставляет образ docker, который был установлен TensorFlow.

Можно включить дополнительные пакеты pip или conda в итоговый образ docker, передав их имена через `pip_packages` и `conda_packages` аргументы.

```Python
script_params = {
    '--data-folder': ws.get_default_datastore().as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.01
}

est = TensorFlow(source_directory=script_folder,
                 script_params=script_params,
                 compute_target=compute_target,
                 entry_script='tf_mnist.py',
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

- **Масштабирование**. Кластер будет предпринята попытка увеличить масштаб, если для кластера Batch AI требуется больше узлов для выполнения запуска, чем в настоящее время доступны.

- **Running**. Все сценарии в папке сценария передаются целевого объекта вычислений, хранилищ данных отправляются подключен или копируются, а также выполняется entry_script. Выходные данные stdout и. / Папка журналов будут отправлены в журнал выполнения и может использоваться для отслеживания выполнения.

- **Постобработка**. . / Outputs папки выполнения, копируются в журнал выполнения.

## <a name="register-or-download-a-model"></a>Зарегистрировать или загрузить модель

После обучения модели, можно зарегистрировать его в рабочую область. Регистрация модели позволяет хранилища и версию модели в рабочей области для упрощения [моделей управления и развертывания](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='tf-dnn-mnist', model_path='outputs/model')
```

Также можно загрузить локальную копию модели с помощью объекта выполнения. В сценарий обучения `mnist-tf.py`, объект заставки TensorFlow сохраняет модель в локальную папку (локальным для целевого объекта вычислений). Объект выполнения позволяет загрузить копию.

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

* [На основе MPI](https://www.open-mpi.org/) распределенного обучения с помощью [Horovod](https://github.com/uber/horovod) framework
* Собственный [распределенных TensorFlow](https://www.tensorflow.org/deploy/distributed) с помощью параметра метода сервера

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) — фреймворк с открытым исходным кодом для распределенного обучения разработанный Uber. Он предлагает простой способ распределенных заданий GPU TensorFlow.

Чтобы использовать Horovod, укажите `mpi` для `distributed_training` параметр в конструкторе оценщика TensorFlow. Horovod устанавливается для использования в скрипте обучения.

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_backend='mpi',
                      use_gpu=True)
```

### <a name="parameter-server"></a>Сервер параметров

Можно также запустить [собственное распределенное обучение TensorFlow](https://www.tensorflow.org/deploy/distributed), которое использует модель сервера параметров. В этом методе обучение проводится в кластере серверов параметров и рабочих ролей. Во время обучения рабочие роли вычисляют градиенты, а серверы параметров выполняют статистическую обработку градиентов.

Чтобы использовать метод параметр сервера, укажите `ps` для `distributed_training` параметр в конструкторе оценщика TensorFlow.

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
estimator= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_backend='ps',
                      use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="note-on-tfconfig"></a>Примечания по `TF_CONFIG`

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

Для высокого уровня TensorFlow [ `tf.estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator) проанализирует API, TensorFlow, это `TF_CONFIG` переменной и построения кластера по спецификациям для вас.

TensorFlow на нижнем уровне основными интерфейсами API для обучения, анализа `TF_CONFIG` переменной и построения `tf.train.ClusterSpec` в коде обучения. В [этом примере](https://aka.ms/aml-notebook-tf-ps) эти действия выполняются в **сценарии обучения** следующим образом:

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
