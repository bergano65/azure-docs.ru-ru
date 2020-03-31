---
title: Поезд и развертывание модели TensorFlow
titleSuffix: Azure Machine Learning
description: Узнайте, как запускать обучающие сценарии TensorFlow в масштабе с помощью машинного обучения Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 08/20/2019
ms.custom: seodec18
ms.openlocfilehash: 2bbd81f3858aa78b9e0e2d610c0fdb0a67816c8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78228308"
---
# <a name="build-a-tensorflow-deep-learning-model-at-scale-with-azure-machine-learning"></a>Создайте модель глубокого обучения TensorFlow в масштабе с помощью машинного обучения Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье показано, как запускать обучающие сценарии [TensorFlow](https://www.tensorflow.org/overview) в масштабе с помощью класса [оценки TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) Azure Machine Learning. Этот пример обучает и регистрирует модель TensorFlow для классификации рукописных цифр с помощью глубокой нейронной сети (DNN).

Независимо от того, разрабатываете ли вы модель TensorFlow с нуля или приносите [существующую модель](how-to-deploy-existing-model.md) в облако, вы можете использовать Azure Machine Learning для масштабирования обучающих заданий с открытым исходным кодом для создания, развертывания, развертывания и мониторинга моделей производственного класса.

Узнайте больше о [глубоком обучении и машинном обучении.](concept-deep-learning-vs-machine-learning.md)

## <a name="prerequisites"></a>Предварительные требования

Выполнить этот код на любой из этих сред:

 - Экземпляр вычислений машинного обучения Azure - нет необходимости загружать или установку

     - Завершите [обучение: Настройка среды и рабочего пространства](tutorial-1st-experiment-sdk-setup.md) для создания специального ноутбука сервера предварительно загружены с SDK и образец репозитория.
    - В папке глубокого обучения образцов на блокноте на сервере ноутбука найдите завершенный и расширенный блокнот, перемещаясь по этому каталогу: **как-к-использованию-azureml > мл-рамки > тензорпотока > развертывание > поезд-гиперпараметр-настройка-развертывание-с тензорпотоком** папку. 
 
 - Собственный сервер Jupyter Notebook

    - [Установите SDK машинного обучения Azure.](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
    - [Создание файла конфигурации рабочего пространства.](how-to-configure-environment.md#workspace)
    - [Скачать пример файлов](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow) `mnist-tf.py` сценария и`utils.py`
     
    Вы также можете найти завершенную [версию Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) этого руководства на странице образцов GitHub. Ноутбук включает в себя расширенные разделы, охватывающие интеллектуальную настройку гиперпараментов, развертывание моделей и виджеты ноутбуков.

## <a name="set-up-the-experiment"></a>Настройка эксперимента

Этот раздел настраивает учебный эксперимент, загружая необходимые пакеты Python, инициируя рабочее пространство, создавая эксперимент и загружая обучающие данные и обучающие сценарии.

### <a name="import-packages"></a>Импорт пакетов

Во-первых, импортируйте необходимые библиотеки Python.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
from azureml.train.dnn import TensorFlow
```

### <a name="initialize-a-workspace"></a>Инициализация рабочего пространства

[Рабочее пространство Azure Machine Learning](concept-workspace.md) — это ресурс высшего уровня службы. Он предоставляет вам централизованное место для работы со всеми артефактами, которые вы создаете. В Python SDK можно получить доступ к артефактам рабочего пространства, создав [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) объект.

Создание объекта рабочей `config.json` области из файла, созданного в [разделе предпосылок.](#prerequisites)

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>Создание эксперимента по глубокому обучению

Создайте эксперимент и папку для хранения обучаемых скриптов. В этом примере создайте эксперимент под названием "tf-mnist".

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
```

### <a name="create-a-file-dataset"></a>Создание набора данных файлов

Объект `FileDataset` ссылается на один или несколько файлов в хранилище данных рабочей области или общедоступных URL-адресов. Это могут быть файлы любого формата, и с помощью этого класса вы сможете скачивать их или подключать к вычислительной среде. Создавая `FileDataset`, вы одновременно создаете ссылку на расположение источника данных. Любые преобразования, примененные к такому набору данных, будут сохранены и в исходном наборе данных. Данные хранятся только в исходном расположении, а значит не потребуется лишних расходов на хранение. Дополнительные сведения см. в соответствующем [практическом руководстве](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets) по пакету `Dataset`.

```python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
dataset = Dataset.File.from_files(path=web_paths)
```

Используйте `register()` метод для регистрации набора данных в рабочее пространство, чтобы они могли быть совместно с другими, повторно использоваться в различных экспериментах, и упоминается по имени в вашем сценарии обучения.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist dataset',
                           description='training and test dataset',
                           create_new_version=True)

# list the files referenced by dataset
dataset.to_path()
```

## <a name="create-a-compute-target"></a>Создание целевого объекта вычислений

Создайте цель вычислений для выполнения задания TensorFlow. В этом примере создайте кластер вычислений с поддержкой графического процессора Azure Machine Learning.

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

Для получения дополнительной информации о [what is a compute target](concept-compute-target.md) целевых показателях вычислений см.

## <a name="create-a-tensorflow-estimator"></a>Создание оценщика TensorFlow

[Оценщик TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) предоставляет простой способ запуска учебной работы TensorFlow на вычислительную цель.

Оценщик TensorFlow реализуется [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) через общий класс, который может быть использован для поддержки любых рамок. Для получения дополнительной информации о обучающих моделях с использованием общего оценщика см. [модели поездов с использованием машинного обучения Azure с использованием оценщика](how-to-train-ml-models.md)

Если ваш учебный скрипт нуждается в дополнительных пакетах pip или conda для запуска, вы `pip_packages` `conda_packages` можете установить пакеты на полученное изображение Docker, передавая их имена через аргументы.

```python
script_params = {
    '--data-folder': dataset.as_named_input('mnist').as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.01
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='tf_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 use_gpu=True,
                 pip_packages=['azureml-dataprep[pandas,fuse]'])
```

> [!TIP]
> Поддержка **Tensorflow 2.0** была добавлена в класс оценки Tensorflow. Дополнительные сведения см. в [записи блога](https://azure.microsoft.com/blog/tensorflow-2-0-on-azure-fine-tuning-bert-for-question-tagging/).

Для получения дополнительной информации о настройке среды Python [см.](how-to-use-environments.md) 

## <a name="submit-a-run"></a>Отправить пробег

[Объект Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) предоставляет интерфейс истории выполнения во время выполнения и после его завершения.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

При выполнении run он проходит следующие этапы:

- **Подготовка**: Изображение Докера создается в соответствии с оценкой TensorFlow. Изображение загружается в реестр контейнеров рабочего пространства и кэшируется для последующих запусков. Логи также передаются в историю выполнения и могут быть просмотрены для мониторинга прогресса.

- **Масштабирование**: Кластер пытается увеличить масштаб, если кластер ИИ пакетов требует больше узлов для выполнения выполнения, чем в настоящее время.

- **Запуск:** Все скрипты в папке скрипта загружаются в цель вычисления, хранилища данных монтируются или копируются, а entry_script выполняется. Выходы из stdout и папки ./logs передаются в историю выполнения и могут использоваться для мониторинга выполнения.

- **Постобработка**: Папка ./выходов запуска скопирована к истории выполнения.

## <a name="register-or-download-a-model"></a>Регистрация или загрузка модели

После того как вы обучили модель, вы можете зарегистрировать его в рабочее пространство. Регистрация моделей позволяет хранить и отстраивать модели в рабочем пространстве для упрощения [управления и развертывания моделей.](concept-model-management-and-deployment.md) При указании `model_framework` `model_framework_version`параметров, `resource_configuration`и развертывание модели без кода становится доступным. Это позволяет непосредственно развертывать модель в виде веб-сервиса `ResourceConfiguration` из зарегистрированной модели, и объект определяет вычислительный ресурс для веб-сервиса.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='tf-dnn-mnist', 
                           model_path='outputs/model',
                           model_framework=Model.Framework.TENSORFLOW,
                           model_framework_version='1.13.0',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

Вы также можете скачать локальную копию модели с помощью объекта Run. В скрипте `mnist-tf.py`обучения объект заставки TensorFlow сохраняет модель в локальной папке (локальной для цели вычисления). Вы можете использовать объект Run для загрузки копии.

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

Оценщик [`TensorFlow`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) также поддерживает распределенное обучение по кластерам процессоров и графических процессоров. Вы можете легко запустить распределенные задания TensorFlow, и Azure Machine Learning будет управлять оркестровкой для вас.

Служба машинного обучения Azure поддерживает два метода распределенного обучения в TensorFlow.

- [Распределенное](https://www.open-mpi.org/) обучение на базе MPI с использованием рамок [Хоровод](https://github.com/uber/horovod)
- Родные [распределенные TensorFlow](https://www.tensorflow.org/deploy/distributed) с помощью метода параметра сервера

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) — это система с открытым исходным кодом для распределенного обучения, разработанная Uber. Он предлагает простой путь к распределенной GPU TensorFlow рабочих мест.

Чтобы использовать Horovod, [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) укажите `distributed_training` объект для параметра в конструкторе TensorFlow. Этот параметр гарантирует, что библиотека Horovod установлена для использования в скрипте обучения.

```Python
from azureml.core.runconfig import MpiConfiguration
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
                      use_gpu=True,
                      pip_packages=['azureml-dataprep[pandas,fuse]'])
```

### <a name="parameter-server"></a>Сервер параметров

Можно также запустить [собственное распределенное обучение TensorFlow](https://www.tensorflow.org/deploy/distributed), которое использует модель сервера параметров. В этом методе обучение проводится в кластере серверов параметров и рабочих ролей. Во время обучения рабочие роли вычисляют градиенты, а серверы параметров выполняют статистическую обработку градиентов.

Чтобы использовать метод сервера [`TensorflowConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py) параметра, `distributed_training` укажите объект для параметра в конструкторе TensorFlow.

```Python
from azureml.train.dnn import TensorFlow

distributed_training = TensorflowConfiguration()
distributed_training.worker_count = 2

# Tensorflow constructor
tf_est= TensorFlow(source_directory=project_folder,
                      compute_target=compute_target,
                      script_params=script_params,
                      entry_script='script.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=distributed_training,
                      use_gpu=True,
                      pip_packages=['azureml-dataprep[pandas,fuse]'])

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="define-cluster-specifications-in-tf_config"></a>Определение спецификаций кластеров в "TF_CONFIG"

Вам также нужны сетевые адреса и [`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec)порты кластера для, поэтому Azure Machine Learning устанавливает переменную `TF_CONFIG` среды для вас.

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

Для API высокого уровня [`tf.estimator`](https://www.tensorflow.org/api_docs/python/tf/estimator) TensorFlow, TensorFlow `TF_CONFIG` разбирает переменную и создает спецификацию кластера для вас.

Для низкоуровневых базовых AI TensorFlow для `TF_CONFIG` обучения разберите переменную и создайте `tf.train.ClusterSpec` код обучения.

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

## <a name="deploy-a-tensorflow-model"></a>Развертывание модели TensorFlow

Только что зарегистрированная модель может быть развернута точно так же, как и любая другая зарегистрированная модель в Azure Machine Learning, независимо от того, какой оценщик вы использовали для обучения. Способ развертывания содержит раздел о регистрации моделей, но можно перейти непосредственно к [созданию вычислительной цели](how-to-deploy-and-where.md#choose-a-compute-target) для развертывания, так как у вас уже есть зарегистрированная модель.

## <a name="preview-no-code-model-deployment"></a>(Предварительный просмотр) Развертывание модели без кода

Вместо традиционного маршрута развертывания можно также использовать функцию развертывания без кода (предварительный просмотр) для Tensorflow. Зарегистрировав модель, как показано `model_framework` `model_framework_version`выше, `resource_configuration` с помощью , `deploy()` и параметры, вы можете просто использовать статическую функцию для развертывания модели.

```python
service = Model.deploy(ws, "tensorflow-web-service", [model])
```

Полное [способное](how-to-deploy-and-where.md) развертывание в Azure Machine Learning более подробно.

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы обучили и зарегистрировали модель TensorFlow и узнали о вариантах развертывания. Ознакомиться с другими статьями читайте в других статьях, чтобы узнать больше о машинном обучении Azure.

* [Отслеживание метрик выполнения во время обучения](how-to-track-experiments.md)
* [Настройка гиперпараметров](how-to-tune-hyperparameters.md)
* [Справочная архитектура для распределенного глубокого обучения в Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
