---
title: Обучение и развертывание модели TensorFlow
titleSuffix: Azure Machine Learning
description: Узнайте, как запускать скрипты обучения TensorFlow в масштабе с помощью Машинное обучение Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: maxluk
author: maxluk
ms.date: 08/20/2019
ms.custom: seodec18
ms.openlocfilehash: 679e44a8949f283c0e01c47ca3e602ae6fc0eacf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84433796"
---
# <a name="build-a-tensorflow-deep-learning-model-at-scale-with-azure-machine-learning"></a>Создание модели глубокого обучения TensorFlow в масштабе с помощью Машинное обучение Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье показано, как выполнять сценарии обучения [TensorFlow](https://www.tensorflow.org/overview) в масштабе с помощью класса средства [оценки TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) для машинное обучение Azure. В этом примере производится обучение и регистрация модели TensorFlow для классификации рукописных цифр с помощью глубокой нейронной сети (DNN).

Независимо от того, разрабатываете ли вы модель TensorFlow с нуля или используете [существующую модель](how-to-deploy-existing-model.md) в облаке, вы можете использовать машинное обучение Azure для масштабирования заданий обучения с открытым исходным кодом для создания, развертывания, контроля версий и мониторинга моделей производственного уровня.

Дополнительные сведения о [глубоком обучении и машинном обучении](concept-deep-learning-vs-machine-learning.md).

## <a name="prerequisites"></a>Предварительные условия

Запустите этот код в любой из этих сред:

 - Вычислительная операция Машинного обучения Azure — загрузка или установка не требуется

     - Пройдите [руководство по настройке среды и рабочей области](tutorial-1st-experiment-sdk-setup.md), чтобы создать выделенный сервер записных книжек, предварительно загруженный с пакетом SDK и репозиторием с примером.
    - В папке примеры глубокого обучения на сервере записной книжки найдите готовую и развернутую записную книжку, перейдя в этот каталог: **практические советы по использованию azureml > машинного обучения ML-frameworks > tensorflow > deployment > поезд-i Parameter-Настройка-Deploy-with-tensorflow** . 
 
 - Собственный сервер Jupyter Notebook

    - [Установите пакет SDK для машинное обучение Azure](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Создайте файл конфигурации рабочей области](how-to-configure-environment.md#workspace).
    - [Загрузка примеров файлов скриптов](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow) `mnist-tf.py` перетаскивани`utils.py`
     
    Вы также можете найти завершенную [Jupyter Notebook версию](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/deployment/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) этого руководства на странице примеров GitHub. Записная книжка включает в себя развернутые разделы, охватывающие интеллектуальные настройки, развертывание моделей и мини-приложения записных книжек.

## <a name="set-up-the-experiment"></a>Настройка эксперимента

В этом разделе выполняется настройка обучающего эксперимента путем загрузки требуемых пакетов Python, инициализации рабочей области, создания эксперимента и отправки обучающих данных и сценариев обучения.

### <a name="import-packages"></a>Импорт пакетов

Сначала импортируйте необходимые библиотеки Python.

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

### <a name="initialize-a-workspace"></a>Инициализация рабочей области

[Машинное обучение Azure Рабочая область](concept-workspace.md) — это ресурс верхнего уровня для службы. Она предоставляет централизованное расположение для работы со всеми создаваемыми артефактами. В пакете SDK для Python можно получить доступ к артефактам рабочей области, создав [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) объект.

Создайте объект рабочей области из `config.json` файла, созданного в [разделе Предварительные требования](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-a-deep-learning-experiment"></a>Создание эксперимента глубокого обучения

Создайте эксперимент и папку для хранения сценариев обучения. В этом примере Создайте эксперимент с именем "TF-mnist".

```Python
script_folder = './tf-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='tf-mnist')
```

### <a name="create-a-file-dataset"></a>Создание файлового набора данных

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

Используйте `register()` метод, чтобы зарегистрировать набор данных в рабочей области, чтобы предоставить общий доступ другим пользователям, повторно использовать их в различных экспериментах и называть их по имени в обучающем скрипте.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist dataset',
                           description='training and test dataset',
                           create_new_version=True)

# list the files referenced by dataset
dataset.to_path()
```

## <a name="create-a-compute-target"></a>Создание целевого объекта вычислений

Создайте целевой объект вычислений для выполнения задания TensorFlow. В этом примере создайте кластерный Машинное обучение Azure вычислений с поддержкой GPU.

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

Дополнительные сведения о целевых объектах вычислений см. в статье [что такое целевые показатели вычислений](concept-compute-target.md) .

## <a name="create-a-tensorflow-estimator"></a>Создание средства оценки TensorFlow

Средство [оценки TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) предоставляет простой способ запуска задания обучения TensorFlow на целевом объекте вычислений.

Оценщик TensorFlow реализуется через универсальный [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) класс, который можно использовать для поддержки любой платформы. Дополнительные сведения об обучении моделей с помощью универсального оценщика см. в разделе [Обучение моделей с помощью оценщика Машинного обучения Azure](how-to-train-ml-models.md).

Если для выполнения сценария обучения требуются дополнительные пакеты PIP или conda, можно установить пакеты в полученном образе DOCKER, передав их имена с помощью `pip_packages` `conda_packages` аргументов и.

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
> В класс средства оценки Tensorflow добавлена поддержка **Tensorflow 2,0** . Дополнительные сведения см. в [записи блога](https://azure.microsoft.com/blog/tensorflow-2-0-on-azure-fine-tuning-bert-for-question-tagging/).

Дополнительные сведения о настройке среды Python см. на [этой странице](how-to-use-environments.md). 

## <a name="submit-a-run"></a>Отправка запроса на выполнение

[Объект Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) предоставляет интерфейс для журнала выполнения во время выполнения задания и после его завершения.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

При выполнении выполнения он проходит следующие этапы.

- **Подготовка**. образ DOCKER создается в соответствии с оценкой TensorFlow. Образ отправляется в реестр контейнеров рабочей области и кэшируется для последующего выполнения. Журналы также передаются в журнал выполнения и могут быть просмотрены для отслеживания хода выполнения.

- **Масштабирование**. Кластер пытается выполнить масштабирование, если кластеру Batch AI требуется больше узлов для выполнения выполнения, чем в настоящее время доступно.

- **Выполняется**: все скрипты в папке Script передаются в целевой объект вычислений, хранилища данных подключаются или копируются, а entry_script выполняется. Выходные данные из STDOUT и папки/логс передаются в журнал выполнения и могут использоваться для наблюдения за выполнением.

- **Пост-обработка**. папка/Outputs для выполнения копируется в журнал выполнения.

## <a name="register-or-download-a-model"></a>Регистрация или скачивание модели

После обучения модели ее можно зарегистрировать в рабочей области. Регистрация модели позволяет хранить и редактировать версии моделей в рабочей области для упрощения [развертывания и управления моделями](concept-model-management-and-deployment.md). При указании параметров `model_framework` , `model_framework_version` и `resource_configuration` , развертывание модели без кода станет доступным. Это позволяет напрямую развернуть модель в качестве веб-службы из зарегистрированной модели, а `ResourceConfiguration` объект определяет ресурс вычислений для веб-службы.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='tf-dnn-mnist', 
                           model_path='outputs/model',
                           model_framework=Model.Framework.TENSORFLOW,
                           model_framework_version='1.13.0',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

Вы также можете скачать локальную копию модели с помощью объекта Run. В сценарии обучения `mnist-tf.py` объект TensorFlow хранителя сохраняет модель в локальной папке (локальную для целевого объекта вычислений). Для скачивания копии можно использовать объект Run.

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

[`TensorFlow`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)Оценщик также поддерживает распределенное обучение между кластерами ЦП и GPU. Вы можете легко выполнять распределенные задания TensorFlow, а Машинное обучение Azure будет управлять согласованием.

Служба машинного обучения Azure поддерживает два метода распределенного обучения в TensorFlow.

- Распределенное обучение [на основе MPI](https://www.open-mpi.org/) с использованием платформы [хоровод](https://github.com/uber/horovod)
- Машинный [распределенный TensorFlow](https://www.tensorflow.org/deploy/distributed) с использованием метода сервера параметров

### <a name="horovod"></a>Horovod

[Хоровод](https://github.com/uber/horovod) — это платформа с открытым исходным кодом для распределенного обучения, разработанного Uber. Он предоставляет простой путь к распределенным заданиям TensorFlow GPU.

Чтобы использовать хоровод, укажите [`MpiConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) объект для `distributed_training` параметра в конструкторе TensorFlow. Этот параметр обеспечивает установку библиотеки хоровод для использования в обучающем скрипте.

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

Чтобы использовать метод сервера параметров, укажите [`TensorflowConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?view=azure-ml-py) объект для `distributed_training` параметра в конструкторе TensorFlow.

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

#### <a name="define-cluster-specifications-in-tf_config"></a>Определение спецификаций кластера в "TF_CONFIG"

Также требуются сетевые адреса и порты кластера для [`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec) , поэтому машинное обучение Azure задает `TF_CONFIG` переменную среды.

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

Для API высокого уровня TensorFlow [`tf.estimator`](https://www.tensorflow.org/api_docs/python/tf/estimator) TensorFlow анализирует `TF_CONFIG` переменную и создает спецификацию кластера.

Для обучения с помощью API-интерфейсов TensorFlow нижних уровней можно проанализировать `TF_CONFIG` переменную и создать `tf.train.ClusterSpec` в коде для обучения.

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

Только что зарегистрированная модель может быть развернута точно так же, как и любая другая Зарегистрированная модель в Машинное обучение Azure, независимо от того, какой механизм оценки использовался для обучения. Руководство по развертыванию содержит раздел, посвященный регистрации моделей, но можно сразу перейти к [созданию целевого объекта вычислений](how-to-deploy-and-where.md#choose-a-compute-target) для развертывания, так как у вас уже есть Зарегистрированная модель.

## <a name="preview-no-code-model-deployment"></a>Образца Развертывание модели без кода

Вместо традиционного маршрута развертывания можно также использовать функцию развертывания без кода (Предварительная версия) для Tensorflow. Зарегистрировав модель, как показано выше с помощью `model_framework` параметров, `model_framework_version` и `resource_configuration` , можно просто использовать `deploy()` статическую функцию для развертывания модели.

```python
service = Model.deploy(ws, "tensorflow-web-service", [model])
```

Полное [Описание процесса](how-to-deploy-and-where.md) развертывания в машинное обучение Azure более подробно.

## <a name="next-steps"></a>Дальнейшие шаги

В этой статье вы обучили и зарегистрировали модель TensorFlow и узнали о вариантах развертывания. Дополнительные сведения о Машинное обучение Azure см. в других статьях.

* [Отслеживание метрик выполнения во время обучения](how-to-track-experiments.md)
* [Настройка гиперпараметров](how-to-tune-hyperparameters.md)
* [Эталонная архитектура для распределенного обучения глубокого обучения в Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
