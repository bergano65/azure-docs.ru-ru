---
title: Поезд с лазурными данными
titleSuffix: Azure Machine Learning
description: Узнайте, как использовать наборы данных в обучении
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 03/09/2020
ms.openlocfilehash: 401383f2d483836bf725051810d78167869f7b22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283502"
---
# <a name="train-with-datasets-in-azure-machine-learning"></a>Поезд с наборами данных в машинном обучении Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье вы узнаете два способа использования [наборов данных Машинного обучения Azure](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) в ходе обучения удаленному эксперименту, не беспокоясь о строках соединения или путях передачи данных.

- Вариант 1: Если у вас есть структурированные данные, создайте TabularDataset и используйте его непосредственно в скрипте обучения.

- Вариант 2: Если у вас есть неструктурированные данные, создайте набор файлов и смонтируйте или загрузите файлы на удаленные вычисления для обучения.

Наборы данных по машинного обучению Azure обеспечивают беспрепятственную интеграцию с обучающими продуктами Обучения машинного обучения Azure, такими как [ScriptRun,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrun?view=azure-ml-py) [Estimator,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) [HyperDrive](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive?view=azure-ml-py) и [Azure Machine Learning pipelines.](how-to-create-your-first-pipeline.md)

## <a name="prerequisites"></a>Предварительные требования

Для создания и обучения с наборами данных необходимо:

* Подписка Azure. Если у вас еще нет подписки Azure, создайте бесплатную учетную запись, прежде чем начинать работу. Опробуйте [бесплатную или платную версию Машинного обучения Azure](https://aka.ms/AMLFree) уже сегодня.

* [Рабочее пространство машинного обучения Azure](how-to-manage-workspace.md).

* [Установлен SDK Для Python,](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)который включает пакет наборов лазурных данных.

> [!Note]
> Некоторые классы наборов данных имеют зависимости от пакета [azureml-dataprep.](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) Для пользователей Linux эти классы поддерживаются только в следующих дистрибутивах: Red Hat Enterprise Linux, Ubuntu, Fedora и CentOS.

## <a name="option-1-use-datasets-directly-in-training-scripts"></a>Вариант 1: Используйте наборы данных непосредственно в обучающие скрипты

В этом примере вы создаете [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) и используете `estimator` его в качестве прямого ввода в объект для обучения. 

### <a name="create-a-tabulardataset"></a>Создание TabularDataset

Следующий код создает незарегистрированный TabularDataset из веб-адреса. Можно также создавать наборы данных из локальных файлов или путей в хранилищах данных. Подробнее о [том, как создавать наборы данных.](https://aka.ms/azureml/howto/createdatasets)

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

### <a name="access-the-input-dataset-in-your-training-script"></a>Доступ к набору входных данных в скрипте обучения

Объекты TabularDataset предоставляют возможность загрузки данных в панды или искры DataFrame, так что вы можете работать со знакомыми библиотеками подготовки данных и обучения. Чтобы использовать эту возможность, вы можете передать TabularDataset в качестве ввода в вашей конфигурации обучения, а затем получить его в скрипте.

Для этого получите доступ к набору входных данных через [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) объект в скрипте обучения и воспользуйтесь методом. [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) 

```Python
%%writefile $script_folder/train_titanic.py

from azureml.core import Dataset, Run

run = Run.get_context()
# get the input dataset by name
dataset = run.input_datasets['titanic']
# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

### <a name="configure-the-estimator"></a>Настройка оценщика

Объект [оценщика](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) используется для отправки запуска эксперимента. Azure Machine Learning имеет предварительно настроенные оценщики для общих инфраструктур машинного обучения, а также общий оценщик.

Этот код создает общий `est`объект оценщика, который определяет

* Каталог сценариев для скриптов. Все файлы в этом каталоге передаются в узел кластера для выполнения.
* Сценарий обучения, *train_titanic.py*.
* Набор входных данных `titanic`для обучения, . `as_named_input()`требуется, чтобы набор входных данных можно было ссылаться на назначенное имя в скрипте обучения. 
* Цель вычислений для эксперимента.
* Определение среды для эксперимента.

```Python
est = Estimator(source_directory=script_folder,
                entry_script='train_titanic.py',
                # pass dataset object as an input with name 'titanic'
                inputs=[titanic_ds.as_named_input('titanic')],
                compute_target=compute_target,
                environment_definition= conda_env)

# Submit the estimator as part of your experiment run
experiment_run = experiment.submit(est)
experiment_run.wait_for_completion(show_output=True)
```


## <a name="option-2--mount-files-to-a-remote-compute-target"></a>Вариант 2: Монтировать файлы к цели удаленного вычисления

Если вы хотите сделать файлы данных доступными для обучения в вычислительной цели, используйте [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) для установки или загрузки переданных им файлов.

### <a name="mount-vs-download"></a>Гора против Скачать

Монтаж или загрузка файлов любого формата поддерживаются для наборов данных, созданных из хранилищ Azure Blob, файлов Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, базы данных Azure S'L и базы данных Azure для PostgreS'L. 

При установке набора данных вы прикрепляете файлы, на которые ссылается набор данных, к каталогу (точке крепления) и делает ею доступ к вычислительной цели. Монтаж поддерживается для linux-компьютеров, включая Azure Machine Learning Compute, виртуальные машины и HDInsight. При загрузке набора данных все файлы, на которые ссылается набор данных, будут загружены в вычислительную цель. Загрузка поддерживается для всех типов вычислений. 

Если скрипт обрабатывает все файлы, на которые ссылается набор данных, и ваш вычислительный диск может соответствовать вашему полному набору данных, загрузка рекомендуется избегать накладных расходов на потоковые данные из служб хранения данных. Если размер данных превышает размер вычислительного диска, загрузка невозможна. Для этого сценария мы рекомендуем монтирование, так как во время обработки загружаются только файлы данных, используемые скриптом.

Следующий код `dataset` крепится к временному каталогу в`mounted_path`

```python
import tempfile
mounted_path = tempfile.mkdtemp()

# mount dataset onto the mounted_path of a Linux-based compute
mount_context = dataset.mount(mounted_path)

mount_context.start()

import os
print(os.listdir(mounted_path))
print (mounted_path)
```

### <a name="create-a-filedataset"></a>Создание FileDataset

Следующий пример создает незарегистрированный Набор файлов из веб-URL-. Узнайте больше о [том, как создавать наборы данных](https://aka.ms/azureml/howto/createdatasets) из других источников.

```Python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
mnist_ds = Dataset.File.from_files(path = web_paths)
```

### <a name="configure-the-estimator"></a>Настройка оценщика

Помимо прохождения набора `inputs` данных по параметру в оценщике, вы также можете пройти через набор данных `script_params` и получить путь данных (точка монтажа) в скрипте обучения с помощью аргументов. Таким образом, вы можете сохранить ваш сценарий обучения независимо от лазурного сдк. Другими словами, вы сможете использовать тот же сценарий обучения для локальной отладки и удаленного обучения на любой облачной платформе.

Объект оценщика [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) используется для отправки пробега для scikit-экспериментов. Узнайте больше о тренировках с [оценщиком SKlearn.](how-to-train-scikit-learn.md)

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    # mount the dataset on the remote compute and pass the mounted path as an argument to the training script
    '--data-folder': mnist_ds.as_named_input('mnist').as_mount(),
    '--regularization': 0.5
}

est = SKLearn(source_directory=script_folder,
              script_params=script_params,
              compute_target=compute_target,
              environment_definition=env,
              entry_script='train_mnist.py')

# Run the experiment
run = experiment.submit(est)
run.wait_for_completion(show_output=True)
```

### <a name="retrieve-the-data-in-your-training-script"></a>Извлечение данных в скрипте обучения

После отправки запуска файлы данных, переданные набором `mnist` данных, будут установлены в цель вычисления. Следующий код показывает, как получить данные в скрипте.

```Python
%%writefile $script_folder/train_mnist.py

import argparse
import os
import numpy as np
import glob

from utils import load_data

# retrieve the 2 arguments configured through script_params in estimator
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# get the file paths on the compute
X_train_path = glob.glob(os.path.join(data_folder, '**/train-images-idx3-ubyte.gz'), recursive=True)[0]
X_test_path = glob.glob(os.path.join(data_folder, '**/t10k-images-idx3-ubyte.gz'), recursive=True)[0]
y_train_path = glob.glob(os.path.join(data_folder, '**/train-labels-idx1-ubyte.gz'), recursive=True)[0]
y_test = glob.glob(os.path.join(data_folder, '**/t10k-labels-idx1-ubyte.gz'), recursive=True)[0]

# load train and test set into numpy arrays
X_train = load_data(X_train_path, False) / 255.0
X_test = load_data(X_test_path, False) / 255.0
y_train = load_data(y_train_path, True).reshape(-1)
y_test = load_data(y_test, True).reshape(-1)
```

## <a name="notebook-examples"></a>Примеры записных книжек

[Ноутбуки набора данных](https://aka.ms/dataset-tutorial) демонстрируют и расширяют концепции в этой статье.

## <a name="next-steps"></a>Дальнейшие действия

* [Модели машинного обучения автопоезда](how-to-auto-train-remote.md) с TabularDatasets

* [Обучить модели классификации изображений](https://aka.ms/filedataset-samplenotebook) с помощью наборов файлов

* [Поезд с наборами данных с помощью трубопроводов](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb)
