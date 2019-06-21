---
title: Обучение и зарегистрировать Keras моделях, запущенных на TensorFlow
titleSuffix: Azure Machine Learning service
description: В этой статье показано, как обучать и зарегистрируйте модели Keras, работающей на TensorFlow с помощью службы машинного обучения Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.date: 06/07/2019
ms.custom: seodec18
ms.openlocfilehash: e070b80f86cb6c8b1d9e7575e19022b5cb08f340
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165556"
---
# <a name="train-and-register-keras-models-at-scale-with-azure-machine-learning-service"></a>Обучение и зарегистрировать Keras модели в масштабе с помощью службы машинного обучения Azure

В этой статье показано, как обучать и зарегистрировать Keras модель, построенная на TensorFlow с помощью службы машинного обучения Azure. Она использует популярного [набора данных MNIST](http://yann.lecun.com/exdb/mnist/) для классификации рукописных цифр, с помощью глубокой нейронной сети (DNN), созданные с помощью [библиотеки Python для Keras](https://keras.io) под управлением на основе [TensorFlow](https://www.tensorflow.org/overview) .

Keras — это высокоуровневый API, могут работать с верхней части других популярных платформ DNN для упрощения разработки нейронной сети. С помощью службы машинного обучения Azure вы можете быстро выполнить развертывание заданий обучения, с помощью эластичных облачных вычислительных ресурсов. Вы также можете отслеживать свои учебных запусков, моделях версии развертывание моделей и многое другое.

При разработке модели Keras из нуля, или вам нужно добавить существующую модель в облаке, службы машинного обучения Azure поможет вам создавать готовые модели.

## <a name="prerequisites"></a>Технические условия

Выполните этот код на любой из этих сред:

 - Machine Learning записные книжки Azure виртуальной Машины — нет файлы для загрузки или установки не требуется

     - Завершить [быстрого запуска облачной записной книжки](quickstart-run-cloud-notebook.md) создание специальных ноутбуков сервера предварительно загруженным с помощью пакета SDK и репозиторий с примером.
    - В папке samples на сервере записной книжки, найти записную книжку выполненной и развернутое, перейдя к этому каталогу: **практические-в-использование azureml > обучения с помощью глубокого обучения > train-hyperparameter-tune-deploy-with-keras** папка. 
 
 - Собственный сервер Jupyter Notebook

     - [Установка Azure Machine Learning пакета SDK для Python](setup-create-workspace.md#sdk)
    - [Создайте файл конфигурации рабочей области](setup-create-workspace.md#write-a-configuration-file)
    - [Скачайте файлы образца скрипта](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras) `mnist-keras.py` и `utils.py`
     
    Вы также найдете завершенного [версии записной книжки Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) данного руководства на странице образцов GitHub. Записная книжка включает расширенный, освещая интеллектуальной настройки гиперпараметров, развертывание модели и записной книжки мини-приложения.

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

Создание эксперимента и папку для хранения сценариев обучения. В этом примере Создайте эксперимент, который называется «keras-mnist».

```Python
script_folder = './keras-mnist'
os.makedirs(script_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='keras-mnist')
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

1. Отправить скрипт обучения Keras, `keras_mnist.py`и вспомогательный файл `utils.py`.

    ```Python
    shutil.copy('./keras_mnist.py', script_folder)
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

## <a name="create-a-tensorflow-estimator-and-import-keras"></a>Создание оценки TensorFlow и импорт Keras

[TensorFlow estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) предоставляет простой способ запуска заданий обучения TensorFlow на целевой объект вычислений. Поскольку Keras работает поверх TensorFlow, можно использовать калькулятор TensorFlow и импортируйте библиотеку Keras с помощью `pip_packages` аргумент.

Оценщик TensorFlow реализуется с помощью универсального [ `estimator` ](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) класс, который может использоваться для поддержки любой платформы. Дополнительные сведения об обучении моделей с помощью универсальный механизм оценки, см. в разделе [обучения моделей с машинным обучением Azure, с помощью механизма оценки](how-to-train-ml-models.md)

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

## <a name="register-the-model"></a>Регистрация модели

После обучения модели, можно зарегистрировать его в рабочую область. Регистрация модели позволяет хранилища и версию модели в рабочей области для упрощения [моделей управления и развертывания](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='keras-dnn-mnist', model_path='outputs/model')
```

Также можно загрузить локальную копию модели. Это может быть полезно для дополнительных моделей проверки задачи выполнялись локально. В скрипт обучения `mnist-keras.py`, объект заставки TensorFlow сохраняет модель в локальную папку (локальным для целевого объекта вычислений). Объект выполнения можно использовать для загрузки копии из хранилища данных.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы обучена и зарегистрировали модели Keras службе машинного обучения Azure. Чтобы узнать, как развернуть модель, перейдите к статье развертывания нашей модели.

> [!div class="nextstepaction"]
> [Как и способа развертывания модели](how-to-deploy-and-where.md)
