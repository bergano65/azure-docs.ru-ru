---
title: Поезд глубокого обучения Керас модели
titleSuffix: Azure Machine Learning
description: Узнайте, как обучить и зарегистрировать модель классификации глубокой нейронной сети Keras, работающая на TensorFlow с помощью Машинного обучения Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: peterlu
ms.date: 08/01/2019
ms.custom: seodec18
ms.openlocfilehash: ba7976d602412037578d0a324916718b2d515aac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269969"
---
# <a name="train-and-register-a-keras-classification-model-with-azure-machine-learning"></a>Обучить и зарегистрировать модель классификации Keras с помощью Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье показано, как обучать и регистрировать модель классификации Keras, построенную на TensorFlow с помощью машинного обучения Azure. Он использует популярный [набор данных MNIST](http://yann.lecun.com/exdb/mnist/) для классификации рукописных цифр с помощью глубокой нейронной сети (DNN), построенной с использованием [библиотеки Keras Python,](https://keras.io) работающего поверх [TensorFlow.](https://www.tensorflow.org/overview)

Keras — это API высокоуровневой нейронной сети, способный работать с другими популярными платформами DNN для упрощения разработки. С помощью машинного обучения Azure можно быстро масштабировать работу по обучению с использованием эластичных облачных вычислительных ресурсов. Вы также можете отслеживать обучающие пробеги, модели версий, развертывать модели и многое другое.

Независимо от того, разрабатываете ли вы модель Keras с нуля или приносите существующую модель в облако, Azure Machine Learning поможет вам создавать готовые к производству модели.

Ознакомьтесь с [концептуальной статьей](concept-deep-learning-vs-machine-learning.md) о различиях между машинным и глубоким обучением.

## <a name="prerequisites"></a>Предварительные требования

Выполнить этот код на любой из этих сред:

- Экземпляр вычислений машинного обучения Azure - нет необходимости загружать или установку

     - Завершите [обучение: Настройка среды и рабочего пространства](tutorial-1st-experiment-sdk-setup.md) для создания специального ноутбука сервера предварительно загружены с SDK и образец репозитория.
    - В папке образцов на блокноте-сервере найдите завершенный и расширенный блокнот, перепрыгивая в этот каталог: **как-к-использованию-azureml > обучение-с-глубоко-обучения > поезд-гиперпараметр-настройка-развертывание-с-керас** папку.

 - Собственный сервер Jupyter Notebook

    - [Установите SDK машинного обучения Azure.](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
    - [Создание файла конфигурации рабочего пространства.](how-to-configure-environment.md#workspace)
    - [Скачать пример файлов](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras) `mnist-keras.py` сценария и`utils.py`

    Вы также можете найти завершенную [версию Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) этого руководства на странице образцов GitHub. Ноутбук включает в себя расширенные разделы, охватывающие интеллектуальную настройку гиперпараментов, развертывание моделей и виджеты ноутбуков.

## <a name="set-up-the-experiment"></a>Настройка эксперимента

В этом разделе устанавливается учебный эксперимент путем загрузки необходимых пакетов питона, инициализации рабочего пространства, создания эксперимента и загрузки обучающих данных и обучающих скриптов.

### <a name="import-packages"></a>Импорт пакетов

Во-первых, импортируйте необходимые библиотеки Python.

```Python
import os
import azureml
from azureml.core import Experiment
from azureml.core import Workspace, Run
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Инициализация рабочего пространства

[Рабочее пространство Azure Machine Learning](concept-workspace.md) — это ресурс высшего уровня службы. Он предоставляет вам централизованное место для работы со всеми артефактами, которые вы создаете. В Python SDK можно получить доступ к артефактам рабочего пространства, создав [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) объект.

Создание объекта рабочей `config.json` области из файла, созданного в [разделе предпосылок.](#prerequisites)

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>Создание эксперимента

Создайте эксперимент под названием "keras-mnist" в вашем рабочем пространстве.

```Python
exp = Experiment(workspace=ws, name='keras-mnist')
```

<a name="data-upload"></a>
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

## <a name="create-a-tensorflow-estimator-and-import-keras"></a>Создание оценщика TensorFlow и импорта Keras

[Оценщик TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) предоставляет простой способ запуска рабочих мест обучения TensorFlow на вычислительной цели. Так как Keras работает на вершине TensorFlow, вы можете использовать оценщик `pip_packages` TensorFlow и импортировать библиотеку Keras с помощью аргумента.

Сначала получите данные из рабочего `Dataset` хранилища данных с помощью класса.

```python
dataset = Dataset.get_by_name(ws, 'mnist dataset')

# list the files referenced by mnist dataset
dataset.to_path()
```

Оценщик TensorFlow реализуется [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) через общий класс, который может быть использован для поддержки любых рамок. Кроме того, создайте `script_params` словарь, содержащий настройки гиперпарамета DNN. Для получения дополнительной информации о обучающих моделях с использованием общего оценщика см. [модели поездов с использованием машинного обучения Azure с использованием оценщика](how-to-train-ml-models.md)

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

## <a name="submit-a-run"></a>Отправить пробег

[Объект Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) предоставляет интерфейс истории выполнения во время выполнения и после его завершения.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

При выполнении run он проходит следующие этапы:

- **Подготовка**: Изображение докера создается в соответствии с оценкой TensorFlow. Изображение загружается в реестр контейнеров рабочего пространства и кэшируется для последующих запусков. Логи также передаются в историю выполнения и могут быть просмотрены для мониторинга прогресса.

- **Масштабирование**: Кластер пытается увеличить масштаб, если кластер ИИ пакетов требует больше узлов для выполнения выполнения, чем в настоящее время.

- **Запуск:** Все скрипты в папке скрипта загружаются в цель вычисления, хранилища данных монтируются или копируются, а entry_script выполняется. Выходы из stdout и папки ./logs передаются в историю выполнения и могут использоваться для мониторинга выполнения.

- **Постобработка**: Папка ./выходов запуска скопирована к истории выполнения.

## <a name="register-the-model"></a>Регистрация модели

После того как вы обучили модель DNN, вы можете зарегистрировать ее в своем рабочем пространстве. Регистрация моделей позволяет хранить и отстраивать модели в рабочем пространстве для упрощения [управления и развертывания моделей.](concept-model-management-and-deployment.md)

```Python
model = run.register_model(model_name='keras-dnn-mnist', model_path='outputs/model')
```

> [!TIP]
> Только что зарегистрированная модель развернута точно так же, как и любая другая зарегистрированная модель в Azure Machine Learning, независимо от того, какой оценщик вы использовали для обучения. Способ развертывания содержит раздел о регистрации моделей, но можно перейти непосредственно к [созданию вычислительной цели](how-to-deploy-and-where.md#choose-a-compute-target) для развертывания, так как у вас уже есть зарегистрированная модель.

Вы также можете скачать локальную копию модели. Это может быть полезно для выполнения дополнительной работы проверки модели локально. В скрипте `mnist-keras.py`обучения объект заставки TensorFlow сохраняет модель в локальной папке (локальной для цели вычисления). Вы можете использовать объект Run для загрузки копии из хранилища данных.

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

В этой статье вы обучали и регистрировали модель Keras по машинному обучению Azure. Чтобы узнать, как развернуть модель, перейти к нашей статье развертывания модели.

> [!div class="nextstepaction"]
> [Как и где развертывать модели](how-to-deploy-and-where.md)
* [Отслеживание метрик выполнения во время обучения](how-to-track-experiments.md)
* [Настройка гиперпараметров](how-to-tune-hyperparameters.md)
* [Развертывание обученной модели](how-to-deploy-and-where.md)
* [Справочная архитектура для распределенного глубокого обучения в Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
