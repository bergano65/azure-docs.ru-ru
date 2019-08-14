---
title: Обучение нейронной сети для глубокого обучения с помощью keras
titleSuffix: Azure Machine Learning service
description: Узнайте, как обучить и зарегистрировать модель классификации keras Deep нейронной сети, работающую на TensorFlow с помощью службы Машинное обучение Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: e7646330d9d89d5257a991b5095b7b6814aa3ba9
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966816"
---
# <a name="train-and-register-a-keras-classification-model-with-azure-machine-learning-service"></a>Обучение и регистрация модели классификации keras с помощью службы Машинное обучение Azure

В этой статье показано, как обучить и зарегистрировать модель классификации keras, созданную на основе TensorFlow с помощью службы Машинное обучение Azure. Он использует популярный [набор данных MNIST](http://yann.lecun.com/exdb/mnist/) для классификации рукописных цифр с помощью глубокой нейронной сети (DNN), созданной с помощью [библиотеки keras Python](https://keras.io) , работающей поверх [TensorFlow](https://www.tensorflow.org/overview).

Keras — это высокоуровневый API нейронной сети, который может работать над другими популярными DNN платформами для упрощения разработки. Служба Машинное обучение Azure позволяет быстро масштабировать задания обучения с помощью эластичных облачных ресурсов. Вы также можете отвестись к обучающим запускам, моделям версий, развертыванию моделей и т. д.

Независимо от того, разрабатываете ли вы модель keras с нуля или используете существующую модель в облаке, Машинное обучение Azure служба может помочь вам создать готовые модели.

Сведения о различиях между машинным обучением и глубоким обучением см. в этой [статье](concept-deep-learning-vs-machine-learning.md) .

## <a name="prerequisites"></a>предварительные требования

Запустите этот код в любой из этих сред:

 - Машинное обучение Azure виртуальной машины записной книжки — Загрузка или установка не требуется

     - Пройдите [руководство по Настройте среду и рабочую](tutorial-1st-experiment-sdk-setup.md) область, чтобы создать выделенный сервер записной книжки, предварительно загруженный с помощью пакета SDK и примера репозитория.
    - В папке Samples на сервере записной книжки найдите готовую и развернутую записную книжку, перейдя к этому каталогу: **практические советы по использованию azureml > обучающие материалы с-глубокими обучениями > Train-i Parameter-Настройка-Deploy-with-keras** .

 - Собственный сервер Jupyter Notebook

    - [Установите пакет SDK для машинное обучение Azure](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Создайте файл конфигурации рабочей области](how-to-configure-environment.md#workspace).
    - [Загрузка примеров файлов скриптов](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras) `mnist-keras.py` и`utils.py`

    Вы также можете найти завершенную [Jupyter Notebook версию](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) этого руководства на странице примеров GitHub. Записная книжка включает в себя развернутые разделы, охватывающие интеллектуальные настройки, развертывание моделей и мини-приложения записных книжек.

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
```

### <a name="initialize-a-workspace"></a>Инициализация рабочей области

[Рабочая область службы машинное обучение Azure](concept-workspace.md) — это ресурс верхнего уровня для службы. Он обеспечивает централизованное расположение для работы со всеми создаваемыми артефактами. В пакете SDK для Python можно получить доступ к артефактам рабочей области, [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) создав объект.

Создайте объект рабочей области из `config.json` файла, созданного в [разделе Предварительные требования](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>Создание эксперимента

Создайте эксперимент и папку для хранения сценариев обучения. В этом примере Создайте эксперимент с именем «keras-mnist».

```Python
script_folder = './keras-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='keras-mnist')
```

### <a name="upload-dataset-and-scripts"></a>Отправка набора данных и скриптов

[Хранилище](how-to-access-data.md) данных — это место для хранения и доступа к данным путем подключения или копирования данных в целевой объект вычислений. Каждая Рабочая область предоставляет хранилище данных по умолчанию. Передайте данные и обучающие скрипты в хранилище данных, чтобы к ним можно было легко получить доступ во время обучения.

1. Скачайте набор данных MNIST локально.

    ```Python
    os.makedirs('./data/mnist', exist_ok=True)

    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz', filename = './data/mnist/train-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz', filename = './data/mnist/train-labels.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename = './data/mnist/test-images.gz')
    urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename = './data/mnist/test-labels.gz')
    ```

1. Отправьте набор данных MNIST в хранилище, заданное по умолчанию.

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/mnist', target_path='mnist', overwrite=True, show_progress=True)
    ```

1. Отправьте сценарий обучения keras, `keras_mnist.py`и вспомогательный файл,. `utils.py`

    ```Python
    shutil.copy('./keras_mnist.py', script_folder)
    shutil.copy('./utils.py', script_folder)
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

## <a name="create-a-tensorflow-estimator-and-import-keras"></a>Создание средства оценки TensorFlow и импорт keras

Средство [оценки TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) предоставляет простой способ запуска заданий обучения TensorFlow в целевом объекте вычислений. Так как keras выполняется поверх TensorFlow, вы можете использовать Оценщик TensorFlow и импортировать библиотеку keras с помощью `pip_packages` аргумента.

Оценщик TensorFlow реализуется через универсальный [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) класс, который можно использовать для поддержки любой платформы. Кроме того, создайте словарь `script_params` , содержащий настройки параметров DNN. Дополнительные сведения о учебных моделях с помощью универсального средства оценки см. в статье [обучение моделей с машинное обучение Azure помощью средства оценки](how-to-train-ml-models.md) .

```Python
script_params = {
    '--data-folder': ds.path('mnist').as_mount(),
    '--batch-size': 50,
    '--first-layer-neurons': 300,
    '--second-layer-neurons': 100,
    '--learning-rate': 0.001
}

est = TensorFlow(source_directory=script_folder,
                 entry_script='keras_mnist.py',
                 script_params=script_params,
                 compute_target=compute_target,
                 pip_packages=['keras', 'matplotlib'],
                 use_gpu=True)
```

## <a name="submit-a-run"></a>Отправка запуска

[Объект Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) предоставляет интерфейс для журнала выполнения во время выполнения задания и после его завершения.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

При выполнении выполнения он проходит следующие этапы.

- **Идет подготовка**: Образ DOCKER создается в соответствии с оценкой TensorFlow. Образ отправляется в реестр контейнеров рабочей области и кэшируется для последующего выполнения. Журналы также передаются в журнал выполнения и могут быть просмотрены для отслеживания хода выполнения.

- **Масштабирование**. Кластер пытается выполнить масштабирование, если кластеру Batch AI требуется больше узлов для выполнения выполнения, чем доступно в данный момент.

- **Running**. Все скрипты в папке скрипта передаются в целевой объект вычислений, хранилища данных подключаются или копируются, и выполняется entry_script. Выходные данные из STDOUT и папки/логс передаются в журнал выполнения и могут использоваться для наблюдения за выполнением.

- **Постобработка**. Папка./Outputs выполнения копируется в журнал выполнения.

## <a name="register-the-model"></a>Регистрация модели

После обучения модели DNN можно зарегистрировать ее в рабочей области. Регистрация модели позволяет хранить и отменять версии моделей в рабочей области, чтобы упростить [Управление моделями и их развертывание](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='keras-dnn-mnist', model_path='outputs/model')
```

Также можно скачать локальную копию модели. Это может быть полезно для выполнения дополнительной проверки модели локально. В скрипте `mnist-keras.py`обучения объект TensorFlow хранителя сохраняет модель в локальной папке (локальную для целевого объекта вычислений). Чтобы скачать копию из хранилища данных, можно использовать объект Run.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="next-steps"></a>Следующие шаги

В этой статье вы обучили и зарегистрировали модель keras в службе Машинное обучение Azure. Чтобы узнать, как развернуть модель, перейдите к статье о развертывании модели.

> [!div class="nextstepaction"]
> [Как и где развертываются модели](how-to-deploy-and-where.md)
* [Отслеживание метрик выполнения во время обучения](how-to-track-experiments.md)
* [Настройка гиперпараметров](how-to-tune-hyperparameters.md)
* [Развертывание обученной модели](how-to-deploy-and-where.md)
* [Эталонная архитектура для распределенного обучения глубокого обучения в Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
