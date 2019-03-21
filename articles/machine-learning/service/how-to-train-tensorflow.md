---
title: Обучение моделей с помощью TensorFlow & Keras
titleSuffix: Azure Machine Learning service
description: Узнайте, как выполнять одним узлом и распределенного обучения TensorFlow и Keras моделей с помощью совместном TensorFlow и Keras
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.reviewer: sgilley
ms.date: 02/21/2019
ms.custom: seodec18
ms.openlocfilehash: b41098907f801f7dae839a470249834b02c8d519
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57338558"
---
# <a name="train-tensorflow-and-keras-models-with-azure-machine-learning-service"></a>Обучение модели TensorFlow и Keras со службой машинного обучения Azure

Для глубокого обучения нейронных сетей (DNN) с помощью TensorFlow служба "Машинное обучение Azure" предоставляет пользовательский класс `TensorFlow` средства оценки `Estimator`. Пакет Azure SDK [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) механизм оценки (не для быть объединена с [ `tf.estimator.Estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator/Estimator) класса) вы сможете легко отправлять задания обучения TensorFlow для одного узла или распределенных запусков в Azure вычислительные ресурсы.

## <a name="single-node-training"></a>Одноузловое обучение
Обучение с помощью средства оценки `TensorFlow` похоже на использование [базового средства оценки`Estimator`](how-to-train-ml-models.md), поэтому сначала прочтите статью с практическим руководством и изучите изложенные понятия.
  
Чтобы выполнить задание TensorFlow, следует создать объект `TensorFlow`. У вас уже должен быть создан объект `compute_target` [целевого вычислительного ресурса](how-to-set-up-training-targets.md#amlcompute).

```Python
from azureml.train.dnn import TensorFlow

script_params = {
    '--batch-size': 50,
    '--learning-rate': 0.01,
}

tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train.py',
                    conda_packages=['scikit-learn'], # in case you need scikit-learn in train.py
                    use_gpu=True)
```

Укажем следующие параметры в конструкторе TensorFlow.

Параметр | ОПИСАНИЕ
--|--
`source_directory` | Локальный каталог, который содержит весь код, необходимый для задания обучения. Эта папка копируется с локального компьютера на удаленный вычислительный ресурс.
`script_params` | Словарь, указывающий аргументы командной строки для сценария обучения `entry_script` в виде пар <аргумент командной строки, значение>.
`compute_target` | Удаленный целевой объект вычислений, на котором будет выполняться сценарий обучения. В нашем случае это кластер Вычислительной среды Машинного обучения Azure ([AmlCompute](how-to-set-up-training-targets.md#amlcompute)).
`entry_script` | Путь к файлу (относительно `source_directory`) сценария обучения, который будет выполняться на удаленном вычислительном ресурсе. В этой папке должны быть расположены этот файл и дополнительные файлы, от которых он зависит.
`conda_packages` | Необходимый для сценария обучения список пакетов Python, которые нужно установить с помощью conda. В этом случае сценарий обучения использует `sklearn` для загрузки данных, поэтому необходимо указать этот пакет для установки.  Параметр `pip_packages` конструктора можно использовать для всех необходимых пакетов pip.
`use_gpu` | Присвойте этому флагу значение `True`, чтобы использовать GPU для обучения. По умолчанию равен `False`.

Так как вы работаете со средством оценки TensorFlow, контейнер, используемый для обучения, по умолчанию будет содержать пакет TensorFlow и связанные зависимости, необходимые для обучения в ЦП и GPU.

Затем отправьте задание TensorFlow:
```Python
run = exp.submit(tf_est)
```

## <a name="keras-support"></a>Поддержка Keras
[Keras](https://keras.io/) представляет собой популярный высокоуровневый DNN Python API, поддерживающий TensorFlow, CNTK или Theano как серверных систем. При использовании TensorFlow в качестве серверной части, можно легко использовать калькулятор TensFlow для обучения модели Keras. Ниже приведен пример оценщика TensorFlow с Keras, добавить к ним:

```Python
from azureml.train.dnn import TensorFlow

keras_est = TensorFlow(source_directory='./my-keras-proj',
                       script_params=script_params,
                       compute_target=compute_target,
                       entry_script='keras_train.py',
                       pip_packages=['keras'], # just add keras through pip
                       use_gpu=True)
```
Указанный выше конструктор оценщика TensorFlow указывает службе машинного обучения Azure для установки Keras с помощью pip в среду выполнения. И `keras_train.py` можно импортировать API Keras для обучения модели Keras. Полный пример, изучите [эту записную книжку Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb).

## <a name="distributed-training"></a>Распределенное обучение
Средство оценки TensorFlow также позволяет обучать модели в кластерах ЦП и GPU виртуальных машин Azure. Распределенное обучение TensorFlow проводится с помощью нескольких вызовов API, при этом служба машинного обучения Azure в фоновом режиме будет управлять инфраструктурой и функциями оркестрации, необходимыми для выполнения этих рабочих нагрузок.

Служба машинного обучения Azure поддерживает два метода распределенного обучения в TensorFlow.
* Распределенное обучение на основе MPI с использованием платформы [Horovod](https://github.com/uber/horovod).
* Собственное [распределенное обучение TensorFlow](https://www.tensorflow.org/deploy/distributed) с использованием метода сервера параметров.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) — это поддерживающая алгоритм ring-allreduce платформа на основе открытого исходного кода для распределенного обучения, разработанная Uber.

Чтобы запустить распределенное обучение TensorFlow с помощью платформы Horovod, создайте объект TensorFlow следующим образом:

```Python
from azureml.train.dnn import TensorFlow

tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py',
                    node_count=2,
                    process_count_per_node=1,
                    distributed_backend='mpi',
                    use_gpu=True)
```

В приведенном выше коде показаны следующие новые параметры в конструкторе TensorFlow.

Параметр | ОПИСАНИЕ | значение по умолчанию
--|--|--
`node_count` | Количество узлов, которые будут использоваться для задания обучения. | `1`
`process_count_per_node` | Количество процессов (или рабочих ролей), запускаемых на каждом узле.|`1`
`distributed_backend` | Серверная часть для запуска распределенного обучения, предлагаемая средством оценки с помощью MPI. Если вы хотите выполнить обучение параллельного или распределенного (например, `node_count`> 1 или `process_count_per_node`> 1 или оба) MPI (и Horovod), установите `distributed_backend='mpi'`. Служба "Машинное обучение Azure" использует реализацию MPI [Open MPI](https://www.open-mpi.org/). | `None`

В приведенном выше примере будет выполняться распределенное обучение с двумя рабочими ролями — по одной рабочей роли для каждого узла.

Horovod и его зависимости будут установлены для, поэтому его можно импортировать в скрипте обучения `train.py` следующим образом:

```Python
import tensorflow as tf
import horovod
```

И, наконец, отправьте задание TensorFlow:
```Python
run = exp.submit(tf_est)
```

### <a name="parameter-server"></a>Сервер параметров
Можно также запустить [собственное распределенное обучение TensorFlow](https://www.tensorflow.org/deploy/distributed), которое использует модель сервера параметров. В этом методе обучение проводится в кластере серверов параметров и рабочих ролей. Во время обучения рабочие роли вычисляют градиенты, а серверы параметров выполняют статистическую обработку градиентов.

Создайте объект TensorFlow:

```Python
from azureml.train.dnn import TensorFlow

tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py',
                    node_count=2,
                    worker_count=2,
                    parameter_server_count=1,
                    distributed_backend='ps',
                    use_gpu=True)
```

Обратите внимание на следующие параметры в конструкторе TensorFlow в приведенном выше коде.

Параметр | ОПИСАНИЕ | значение по умолчанию
--|--|--
`worker_count` | Количество рабочих ролей. | `1`
`parameter_server_count` | Количество серверов параметров. | `1`
`distributed_backend` | Серверная часть, которая будет использоваться для распределенного обучения. Чтобы провести распределенное обучение с помощью сервера параметров, задайте значение `distributed_backend='ps'`. | `None`

#### <a name="note-on-tfconfig"></a>Примечания по `TF_CONFIG`
Вам также потребуются сетевые адреса и порты кластера для [`tf.train.ClusterSpec`](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec), поэтому служба машинного обучения Azure автоматически задает переменную среды `TF_CONFIG`.

Переменная среды `TF_CONFIG` представляет собой строку JSON. Ниже приведен пример переменной для сервера параметров.
```
TF_CONFIG='{
    "cluster": {
        "ps": ["host0:2222", "host1:2222"],
        "worker": ["host2:2222", "host3:2222", "host4:2222"],
    },
    "task": {"type": "ps", "index": 0},
    "environment": "cloud"
}'
```

Если вы используете TensorFlow на высоком уровне [ `tf.estimator` ](https://www.tensorflow.org/api_docs/python/tf/estimator) проанализирует API, TensorFlow, это `TF_CONFIG` переменной и построения кластера по спецификациям для вас. 

Если для обучения вы используете API более низкого уровня, вам необходимо самостоятельно проанализировать переменную`TF_CONFIG` и создать `tf.train.ClusterSpec` в коде обучения. В [этом примере](https://aka.ms/aml-notebook-tf-ps) эти действия выполняются в **сценарии обучения** следующим образом:

```Python
import os, json
import tensorflow as tf

tf_config = os.environ.get('TF_CONFIG')
if not tf_config or tf_config == "":
    raise ValueError("TF_CONFIG not found.")
tf_config_json = json.loads(tf_config)
cluster_spec = tf.train.ClusterSpec(cluster)

```

Завершив написание сценария обучения и создание объекта TensorFlow, отправьте задание обучения:
```Python
run = exp.submit(tf_est)
```

## <a name="examples"></a>Примеры

Изучите различные [записные книжки в распределенного глубокого обучения на сайте Github](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Дальнейшие действия
* [Отслеживание метрик выполнения во время обучения](how-to-track-experiments.md)
* [Настройка гиперпараметров](how-to-tune-hyperparameters.md)
* [Развертывание обученной модели](how-to-deploy-and-where.md)
