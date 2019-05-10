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
ms.date: 05/06/2019
ms.custom: seodec18
ms.openlocfilehash: 0d5751ab96dc6b44229e2b18b832a570930058ca
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65442353"
---
# <a name="train-tensorflow-and-keras-models-with-azure-machine-learning-service"></a>Обучение модели TensorFlow и Keras со службой машинного обучения Azure

Вы можете легко запустить задания обучения TensorFlow вычисления Azure с помощью [ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) оценщика класса в пакете SDK для Azure Machine Learning. `TensorFlow` Оценщика направляет службы машинного обучения Azure для выполнения задания в контейнере с поддержкой TensorFlow для обучения глубокой нейронной сети (DNN).

`TensorFlow` Оценщика также предоставляет уровень абстракции по сравнению с выполнением, это означает, что можно легко настроить параметризованный запусков на различные целевые вычислительные узлы без изменения сценариев обучения.

## <a name="get-started"></a>Начало работы

Так как `TensorFlow` estimator класс похож по основанию [ `Estimator` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py), мы рекомендуем первого чтения [базовый механизм оценки практическом](how-to-train-ml-models.md) основные понятия, охватывающая.

Чтобы приступить к работе со службой машинного обучения Azure, [работы с кратким руководством](quickstart-run-cloud-notebook.md). После завершения вы получите [рабочей области машинного обучения Azure](concept-azure-machine-learning-architecture.md#workspace) и все наши [примеры записных книжек](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml) в том числе для обучения Dnn с TensorFlow и Keras.

## <a name="single-node-training"></a>Одноузловое обучение

Чтобы выполнить задание TensorFlow, следует создать [ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) объекта и отправьте его в качестве эксперимента.

Следующий код создает экземпляр оценщика TensorFlow и передает ее в качестве эксперимента. Сценарий обучения `train.py` будет запускаться с помощью параметров данного сценария. Задание будет выполняться по с поддержкой GPU [целевого объекта вычислений](how-to-set-up-training-targets.md)и scikit-Узнайте будет устанавливаться как зависимость для `train.py`.

```Python
from azureml.train.dnn import TensorFlow

# training script parameters passed as command-line arguments
script_params = {
    '--batch-size': 50,
    '--learning-rate': 0.01,
}

# TensorFlow constructor
tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train.py', # relative path to your TensorFlow job
                    conda_packages=['scikit-learn'],
                    use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

## <a name="distributed-training"></a>Распределенное обучение

[ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) Estimator также поддерживает распределенного обучения по кластерам ЦП и GPU. Вы можете легко запустить распределенных заданий TensorFlow и будет управлять служба машинного обучения Azure, инфраструктуры и оркестрации для вас.

Служба машинного обучения Azure поддерживает два метода распределенного обучения в TensorFlow.

* [На основе MPI](https://www.open-mpi.org/) распределенного обучения с помощью [Horovod](https://github.com/uber/horovod) framework
* Собственный [распределенных TensorFlow](https://www.tensorflow.org/deploy/distributed) с помощью параметра метода сервера

### <a name="horovod"></a>Horovod

[Horovod](https://github.com/uber/horovod) — фреймворк с открытым исходным кодом для распределенного обучения разработанный Uber. Он предлагает простой способ распределенных заданий GPU TensorFlow.

Следующий пример выполняет задание распределенного обучения с помощью Horovod с двумя рабочими ролями, распределенных между двумя узлами.

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py', # relative path to your TensorFlow job
                    node_count=2,
                    process_count_per_node=1,
                    distributed_backend='mpi', # specifies Horovod backend
                    use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

Horovod и его зависимости будет устанавливаться автоматически, поэтому его можно импортировать в скрипте обучения.

```Python
import tensorflow as tf
import horovod
```

### <a name="parameter-server"></a>Сервер параметров

Можно также запустить [собственное распределенное обучение TensorFlow](https://www.tensorflow.org/deploy/distributed), которое использует модель сервера параметров. В этом методе обучение проводится в кластере серверов параметров и рабочих ролей. Во время обучения рабочие роли вычисляют градиенты, а серверы параметров выполняют статистическую обработку градиентов.

Следующий пример выполняет задание распределенного обучения с помощью параметра метода сервера с четырьмя рабочими ролями, распределенных между двумя узлами.

```Python
from azureml.train.dnn import TensorFlow

# Tensorflow constructor
tf_est = TensorFlow(source_directory='./my-tf-proj',
                    script_params={},
                    compute_target=compute_target,
                    entry_script='train.py', # relative path to your TensorFlow job
                    node_count=2,
                    worker_count=2,
                    parameter_server_count=1,
                    distributed_backend='ps', # specifies parameter server backend
                    use_gpu=True)

# submit the TensorFlow job
run = exp.submit(tf_est)
```

#### <a name="note-on-tfconfig"></a>Примечания по `TF_CONFIG`

Вам также потребуется сетевых адресов и портов кластера для [ `tf.train.ClusterSpec` ](https://www.tensorflow.org/api_docs/python/tf/train/ClusterSpec), поэтому машинного обучения Azure задает `TF_CONFIG` переменной среды для вас.

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

## <a name="keras-support"></a>Поддержка Keras

[Keras](https://keras.io/) — это популярный высокоуровневые DNN Python API, поддерживающий TensorFlow и CNTK, Theano как серверных систем. Если вы используете TensorFlow в качестве серверной части, добавив Keras осуществляется простым включая `pip_package` параметра конструктора.

Следующий пример создает экземпляр [ `TensorFlow` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) механизм оценки и передает ее в качестве эксперимента. Калькулятор выполняет скрипт обучения Keras `keras_train.py`. Задание будет выполняться по с поддержкой gpu [целевого объекта вычислений](how-to-set-up-training-targets.md) с Keras устанавливается как зависимость с помощью pip.

```Python
from azureml.train.dnn import TensorFlow

keras_est = TensorFlow(source_directory='./my-keras-proj',
                       script_params=script_params,
                       compute_target=compute_target,
                       entry_script='keras_train.py', # relative path to your TensorFlow job
                       pip_packages=['keras'], # add keras through pip
                       use_gpu=True)
```

## <a name="export-to-onnx"></a>Экспорт в ONNX

Для получения выводов, оптимизированный с [среды выполнения ONNX](concept-onnx.md), можно преобразовать в формат ONNX обученной модели TensorFlow. См. [пример](https://github.com/onnx/tensorflow-onnx/blob/master/examples/call_coverter_via_python.py).

## <a name="examples"></a>Примеры

Рабочие образцы кода можно найти для одного узла или распределенных выполнений TensorFlow, с помощью различных платформ в [нашей странице GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning).

## <a name="next-steps"></a>Дальнейшие действия

* [Отслеживание метрик выполнения во время обучения](how-to-track-experiments.md)
* [Настройка гиперпараметров](how-to-tune-hyperparameters.md)
* [Развертывание обученной модели](how-to-deploy-and-where.md)
