---
title: Обучение с помощью azureml-DataSets
titleSuffix: Azure Machine Learning
description: Узнайте, как использовать наборы данных для обучения
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 09/25/2019
ms.openlocfilehash: 12c08a95cd43f9f988c98711141b1b1f879e8b3a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489374"
---
# <a name="train-with-datasets-preview-in-azure-machine-learning"></a>Обучение с наборами данных (Предварительная версия) в Машинное обучение Azure
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье вы узнаете о двух способах использования [наборов данных машинное обучение Azure](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) в ходе удаленного обучения экспериментов, не беспокоясь о строках подключения и путях к данным.

- Вариант 1. при наличии структурированных данных создайте Табулардатасет и используйте их непосредственно в обучающем скрипте.

- Вариант 2. при наличии неструктурированных данных создайте Филедатасет и подключите или Скачайте файлы на удаленное вычисление для обучения.

Машинное обучение Azure наборы данных обеспечивают простую интеграцию с Машинное обучение Azure обучающими [продуктами, такими](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive?view=azure-ml-py)как [скриптрун](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrun?view=azure-ml-py), [оценщик](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) и подсистему.

## <a name="prerequisites"></a>Технические условия

Чтобы создать и обучить наборы данных, вам потребуется:

* Подписка Azure. Если у вас еще нет подписки Azure, создайте бесплатную учетную запись Azure, прежде чем начинать работу. Опробуйте [бесплатную или платную версию машинное обучение Azure](https://aka.ms/AMLFree) уже сегодня.

* [Рабочая область машинное обучение Azure](how-to-manage-workspace.md).

* [Установленный пакет SDK для машинное обучение Azure для Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), включающий пакет azureml-DataSets.

> [!Note]
> Некоторые классы набора данных (Предварительная версия) имеют зависимости от пакета [azureml-](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) DataMarket. Для пользователей Linux эти классы поддерживаются только в следующих дистрибутивах: Red Hat Enterprise Linux, Ubuntu, Fedora и CentOS.

## <a name="option-1-use-datasets-directly-in-training-scripts"></a>Вариант 1. Использование наборов данных непосредственно в сценариях обучения

В этом примере вы создаете [табулардатасет](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) и используете его в качестве прямого ввода для объекта `estimator` для обучения. 

### <a name="create-a-tabulardataset"></a>Создание Табулардатасет

Следующий код создает незарегистрированный Табулардатасет из URL-адреса. Наборы данных можно также создавать из локальных файлов или путей в хранилищах данных. Дополнительные сведения о [создании наборов данных](https://aka.ms/azureml/howto/createdatasets).

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

### <a name="access-the-input-dataset-in-your-training-script"></a>Доступ к входному набору данных в скрипте обучения

Объекты Табулардатасет предоставляют возможность загружать данные в таблицу данных Pandas или Spark, чтобы вы могли работать с привычными библиотеками подготовки и обучения данных. Чтобы использовать эту возможность, можно передать Табулардатасет в качестве входных данных в обучающей конфигурации, а затем извлечь его в скрипте.

Для этого необходимо получить доступ к входному набору данных через объект [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) в скрипте обучения и использовать метод [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#to-pandas-dataframe--) . 

```Python
%%writefile $script_folder/train_titanic.py

from azureml.core import Dataset, Run

run = Run.get_context()
# get the input dataset by name
dataset = run.input_datasets['titanic_ds']
# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

### <a name="configure-the-estimator"></a>Настройка средства оценки

Объект средства [оценки](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) используется для отправки запуска эксперимента. Машинное обучение Azure содержит предварительно настроенные средства оценки для распространенных платформ машинного обучения, а также универсальный механизм оценки.

Этот код создает универсальный объект средства оценки, `est`, который указывает

* Каталог скрипта для скриптов. Все файлы в этом каталоге передаются в узел кластера для выполнения.
* Обучающий сценарий *train_titanic. Корректировка*.
* Входной набор данных для обучения, `titanic`.
* Целевой объект вычислений для эксперимента.
* Определение окружения для эксперимента.

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

## <a name="option-2--mount-files-to-a-remote-compute-target"></a>Вариант 2. подключение файлов к удаленному целевому объекту вычислений

Если вы хотите сделать файлы данных доступными в целевом объекте вычислений для обучения, используйте [филедатасет](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) для подключения или загрузки файлов, на которые они ссылаются.

При подключении файловой системы вы подключаете эту файловую систему к каталогу (точке подключения) и сделаете ее доступной для целевого объекта вычислений. Подключение выполняется мгновенно, поскольку файлы загружаются только во время обработки. Подключение поддерживается и рекомендуется для вычислений на основе Linux, включая Машинное обучение Azure вычислений, виртуальных машин и HDInsight. Для вычислений, отличных от Linux, поддерживается только скачивание.  

>[!WARNING]
> Если размер данных превышает предел хранилища целевого объекта вычислений, загрузка завершится ошибкой.

### <a name="create-a-filedataset"></a>Создание Филедатасет

В следующем примере создается незарегистрированный Филедатасет из URL-адресов. Дополнительные сведения о [создании наборов данных](https://aka.ms/azureml/howto/createdatasets) из других источников.

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

### <a name="configure-the-estimator"></a>Настройка средства оценки

Вместо передачи набора данных с помощью параметра `inputs` в средстве оценки можно также передать набор данных с помощью `script_params` и получить путь к данным (точку подключения) в обучающем скрипте через аргументы. Таким образом вы сможете получить доступ к данным и использовать существующий обучающий сценарий.

Объект средства оценки [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) используется для отправки экспериментов scikit-учиться. Узнайте больше о обучении с помощью средства [оценки SKlearn](how-to-train-scikit-learn.md).

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

### <a name="retrieve-the-data-in-your-training-script"></a>Получение данных в скрипте обучения

После отправки выполнения файлы данных, на которые ссылается набор данных `mnist`, будут подключены к целевому объекту вычислений. В следующем коде показано, как получить данные в скрипте.

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

В этой статье демонстрируются и развертываются [записные книжки набора данных](https://aka.ms/dataset-tutorial) . 

## <a name="next-steps"></a>Дальнейшие действия

* [Автоматическое обучение моделей машинного обучения](how-to-auto-train-remote.md) с помощью табулардатасетс.

* [Обучение моделей классификации изображений](https://aka.ms/filedataset-samplenotebook) с помощью филедатасетс.

