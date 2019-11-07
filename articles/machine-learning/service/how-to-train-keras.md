---
title: Обучение нейронной сети для глубокого обучения с помощью keras
titleSuffix: Azure Machine Learning
description: Узнайте, как обучить и зарегистрировать модель классификации keras глубокой нейронной сети, работающую на TensorFlow с помощью Машинное обучение Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: 42a824863766b5d4d91d9eaa3dad1012f5019f63
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73584498"
---
# <a name="train-and-register-a-keras-classification-model-with-azure-machine-learning"></a>Обучение и регистрация модели классификации keras с помощью Машинное обучение Azure
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье показано, как обучить и зарегистрировать модель классификации keras, построенную на основе TensorFlow с помощью Машинное обучение Azure. Он использует популярный [набор данных MNIST](http://yann.lecun.com/exdb/mnist/) для классификации рукописных цифр с помощью глубокой нейронной сети (DNN), созданной с помощью [библиотеки keras Python](https://keras.io) , работающей поверх [TensorFlow](https://www.tensorflow.org/overview).

Keras — это высокоуровневый API нейронной сети, который может работать над другими популярными DNN платформами для упрощения разработки. С Машинное обучение Azure можно быстро масштабировать задания обучения с помощью эластичных облачных ресурсов. Вы также можете отвестись к обучающим запускам, моделям версий, развертыванию моделей и т. д.

Независимо от того, разрабатываете ли вы модель keras с нуля или используете существующую модель в облаке, Машинное обучение Azure может помочь в создании моделей, готовых для производства.

Сведения о различиях между машинным обучением и глубоким обучением см. в этой [статье](concept-deep-learning-vs-machine-learning.md) .

## <a name="prerequisites"></a>Предварительные требования

Запустите этот код в любой из этих сред:

 - Машинное обучение Azure виртуальной машины записной книжки — Загрузка или установка не требуется

     - Выполните инструкции из [учебника Настройка среды и рабочей области](tutorial-1st-experiment-sdk-setup.md) , чтобы создать выделенный сервер записной книжки, предварительно загруженный с помощью пакета SDK и примера репозитория.
    - В папке Samples на сервере записной книжки найдите готовую и развернутую записную книжку, перейдя к этому каталогу: **практические советы по использованию azureml > обучающие материалы с-глубокими обучениями > Train-i Parameter-Настройка-Deploy-with-keras** .

 - Собственный сервер Jupyter Notebook

    - [Установите пакет SDK для машинное обучение Azure](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Создайте файл конфигурации рабочей области](how-to-configure-environment.md#workspace).
    - [Скачайте примеры файлов скриптов](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras) `mnist-keras.py` и `utils.py`

    Вы также можете найти завершенную [Jupyter Notebook версию](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) этого руководства на странице примеров GitHub. Записная книжка включает в себя развернутые разделы, охватывающие интеллектуальные настройки, развертывание моделей и мини-приложения записных книжек.

## <a name="set-up-the-experiment"></a>Настройка эксперимента

В этом разделе выполняется настройка обучающего эксперимента путем загрузки требуемых пакетов Python, инициализации рабочей области, создания эксперимента и отправки обучающих данных и сценариев обучения.

### <a name="import-packages"></a>Импорт пакетов

Сначала импортируйте необходимые библиотеки Python.

```Python
import os
import azureml
from azureml.core import Experiment
from azureml.core import Workspace, Run
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Инициализация рабочей области

[Машинное обучение Azure Рабочая область](concept-workspace.md) — это ресурс верхнего уровня для службы. Он обеспечивает централизованное расположение для работы со всеми создаваемыми артефактами. В пакете SDK для Python можно получить доступ к артефактам рабочей области, создав объект [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) .

Создайте объект рабочей области из файла `config.json`, созданного в [разделе Предварительные требования](#prerequisites).

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>Создание эксперимента

Создайте эксперимент с именем "keras-mnist" в рабочей области.

```Python
exp = Experiment(workspace=ws, name='keras-mnist')
```

<a name="data-upload"></a>
### <a name="create-a-file-dataset"></a>Создание файлового набора данных

Объект `FileDataset` ссылается на один или несколько файлов в хранилище данных рабочей области или в общедоступных URL-адресах. Файлы могут иметь любой формат, и класс предоставляет возможность загрузки или подключения файлов к вычислению. Создавая `FileDataset`, вы создаете ссылку на расположение источника данных. Если к набору данных применены какие либо преобразования, они также будут сохранены в наборе данных. Данные остаются в существующем расположении, поэтому дополнительные затраты на хранение не взимается. Дополнительные сведения см. в [пошаговом](https://docs.microsoft.com/azure/machine-learning/service/how-to-create-register-datasets) руководствах по пакету `Dataset`.

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

Используйте метод `register()`, чтобы зарегистрировать набор данных в рабочей области, чтобы предоставить доступ к ним другим пользователям, повторно использовать их в различных экспериментах и называть их по имени в обучающем скрипте.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist dataset',
                           description='training and test dataset',
                           create_new_version=True)
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

Средство [оценки TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) предоставляет простой способ запуска заданий обучения TensorFlow в целевом объекте вычислений. Так как keras выполняется поверх TensorFlow, вы можете использовать Оценщик TensorFlow и импортировать библиотеку keras с помощью аргумента `pip_packages`.

Сначала получите данные из хранилища данных рабочей области с помощью класса `Dataset`.

```python
dataset = Dataset.get_by_name(ws, 'mnist dataset')

# list the files referenced by mnist dataset
dataset.to_path()
```

Оценщик TensorFlow реализуется с помощью универсального класса [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) , который можно использовать для поддержки любой платформы. Кроме того, создайте словарь `script_params`, содержащий настройки параметров DNN. Дополнительные сведения о учебных моделях с помощью универсального средства оценки см. в статье [обучение моделей с машинное обучение Azure помощью средства оценки](how-to-train-ml-models.md) .

```python
from azureml.train.dnn import TensorFlow

script_params = {
    '--data-folder': dataset.as_named_input('mnist').as_mount(),
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

- **Подготовка**. образ DOCKER создается в соответствии с оценкой TensorFlow. Образ отправляется в реестр контейнеров рабочей области и кэшируется для последующего выполнения. Журналы также передаются в журнал выполнения и могут быть просмотрены для отслеживания хода выполнения.

- **Масштабирование**. Кластер пытается выполнить масштабирование, если кластеру Batch AI требуется больше узлов для выполнения выполнения, чем в настоящее время доступно.

- **Выполняется**: все скрипты в папке Script передаются в целевой объект вычислений, хранилища данных подключаются или копируются, и выполняется entry_script. Выходные данные из STDOUT и папки/логс передаются в журнал выполнения и могут использоваться для наблюдения за выполнением.

- **Пост-обработка**. папка/Outputs для выполнения копируется в журнал выполнения.

## <a name="register-the-model"></a>Регистрация модели

После обучения модели DNN можно зарегистрировать ее в рабочей области. Регистрация модели позволяет хранить и отменять версии моделей в рабочей области, чтобы упростить [Управление моделями и их развертывание](concept-model-management-and-deployment.md).

```Python
model = run.register_model(model_name='keras-dnn-mnist', model_path='outputs/model')
```

Также можно скачать локальную копию модели. Это может быть полезно для выполнения дополнительной проверки модели локально. В скрипте обучения `mnist-keras.py`объект хранителя сохраняет модель в локальную папку (локальную для целевого объекта вычислений). Чтобы скачать копию из хранилища данных, можно использовать объект Run.

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

В этой статье вы обучили и зарегистрировали модель keras на Машинное обучение Azure. Чтобы узнать, как развернуть модель, перейдите к статье о развертывании модели.

> [!div class="nextstepaction"]
> [Как и где развертываются модели](how-to-deploy-and-where.md)
* [Отслеживание метрик выполнения во время обучения](how-to-track-experiments.md)
* [Настройка гиперпараметров](how-to-tune-hyperparameters.md)
* [Развертывание обученной модели](how-to-deploy-and-where.md)
* [Эталонная архитектура для распределенного обучения глубокого обучения в Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
