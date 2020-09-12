---
title: Обучение с помощью azureml-DataSets
titleSuffix: Azure Machine Learning
description: Узнайте, как использовать наборы данных для обучения
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 1db62b77f3b9b1bcfc524a68b52c4aef5c16d851
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89648185"
---
# <a name="train-with-datasets-in-azure-machine-learning"></a>Обучение с наборами данных в Машинное обучение Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье вы узнаете, как работать с [машинное обучение Azure наборами данных](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py&preserve-view=true) в учебных экспериментах.  Наборы данных можно использовать в локальном или удаленном целевом объекте вычислений, не беспокоясь о строках подключения или путях к данным.

Машинное обучение Azure наборы данных обеспечивают простую интеграцию с Машинное обучение Azure обучающими продуктами, такими как [скриптрун](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrun?view=azure-ml-py&preserve-view=true), [оценщик](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py&preserve-view=true) [, а также](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive?view=azure-ml-py&preserve-view=true) [конвейеры и машинное обучение Azure](how-to-create-your-first-pipeline.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы создать и обучить наборы данных, вам потребуется:

* Подписка Azure. Если у вас еще нет подписки Azure, создайте бесплатную учетную запись, прежде чем начинать работу. Опробуйте [бесплатную или платную версию Машинного обучения Azure](https://aka.ms/AMLFree) уже сегодня.

* [Рабочая область машинное обучение Azure](how-to-manage-workspace.md).

* [Установленный пакет SDK для машинное обучение Azure для Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true), включающий пакет azureml-DataSets.

> [!Note]
> Некоторые классы наборов данных имеют зависимости от пакета [azureml-](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py&preserve-view=true) DataMarket. Для пользователей Linux эти классы поддерживаются только в следующих дистрибутивах: Red Hat Enterprise Linux, Ubuntu, Fedora и CentOS.

## <a name="access-and-explore-input-datasets"></a>Доступ к входным наборам данных и их изучение

Вы можете получить доступ к существующему Табулардатасет из обучающего скрипта эксперимента в рабочей области и загрузить этот набор данных в кадр Pandas для дальнейшего изучения в локальной среде.

В следующем коде [`get_context()`]() метод в [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&preserve-view=true) классе используется для доступа к существующему входному табулардатасет, `titanic` в скрипте обучения. Затем использует [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) метод для загрузки этого набора данных в кадр данных Pandas для дальнейшего изучения и подготовки перед обучением.

> [!Note]
> Если исходный источник данных содержит NaN, пустые строки или пустые значения, то при использовании to_pandas_dataframe () эти значения заменяются значением *null* . 

```Python
%%writefile $script_folder/train_titanic.py

from azureml.core import Dataset, Run

run = Run.get_context()
# get the input dataset by name
dataset = run.input_datasets['titanic']

# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

Если необходимо загрузить подготовленные данные в новый набор данных из Pandas в памяти, запишите данные в локальный файл, например в Parquet, и создайте новый набор данных из этого файла. Наборы данных можно также создавать из локальных файлов или путей в хранилищах данных. Дополнительные сведения о [создании наборов данных](how-to-create-register-datasets.md).

## <a name="use-datasets-directly-in-training-scripts"></a>Использование наборов данных непосредственно в сценариях обучения

Если структурированные данные еще не зарегистрированы в качестве набора данных, создайте Табулардатасет и используйте их непосредственно в обучающем скрипте для локального или удаленного эксперимента.

В этом примере вы создаете незарегистрированный [табулардатасет](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py&preserve-view=true) и используете его в качестве прямого ввода для вашего `estimator` объекта для обучения. Если вы хотите повторно использовать этот Табулардатасет с другими экспериментами в рабочей области, см. статью [как зарегистрировать наборы данных в рабочей области](how-to-create-register-datasets.md#register-datasets).

### <a name="create-a-tabulardataset"></a>Создание Табулардатасет

Следующий код создает незарегистрированный Табулардатасет из URL-адреса.  

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

Объекты Табулардатасет обеспечивают возможность загрузки данных в Табулардатасет в таблицу данных Pandas или Spark, чтобы вы могли работать с привычными библиотеками подготовки и обучения данных, не покидая записной книжки. Сведения о том, как использовать эту возможность, см. в разделе [доступ и изучение входных наборов](#access-and-explore-input-datasets)данных.

### <a name="configure-the-estimator"></a>Настройка средства оценки

Объект средства [оценки](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py&preserve-view=true) используется для отправки запуска эксперимента. Машинное обучение Azure содержит предварительно настроенные средства оценки для распространенных платформ машинного обучения, а также универсальный механизм оценки.

Этот код создает универсальный объект средства оценки, `est` который указывает

* Каталог скрипта для скриптов. Все файлы в этом каталоге передаются в узел кластера для выполнения.
* Сценарий обучения, *train_titanic. Корректировка*.
* Входной набор данных для обучения, `titanic_ds` . `as_named_input()` требуется, чтобы на входной набор данных можно было ссылаться по назначенному имени `titanic` в скрипте обучения. 
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

## <a name="mount-files-to-remote-compute-targets"></a>Подключение файлов к удаленным целевым объектам вычислений

Если у вас есть неструктурированные данные, создайте [филедатасет](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py&preserve-view=true) и либо подключите, либо Скачайте файлы данных, чтобы сделать их доступными для целей удаленного вычислений. Узнайте, когда использовать [подключение и скачивание](#mount-vs-download) для удаленных экспериментов. 

В следующем примере создается Филедатасет, а набор данных подключается к целевому объекту вычислений путем передачи его в качестве аргумента средства оценки для обучения. 

> [!Note]
> При использовании пользовательского базового образа DOCKER необходимо установить предохранитель через `apt-get install -y fuse` как зависимость для подключения набора данных к работе. Узнайте, как [создать пользовательский образ сборки](how-to-deploy-custom-docker-image.md#build-a-custom-base-image).

### <a name="create-a-filedataset"></a>Создание FileDataset

В следующем примере создается незарегистрированный Филедатасет из URL-адресов. Дополнительные сведения о [создании наборов данных](how-to-create-register-datasets.md) из других источников.

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

Рекомендуется передавать набор данных в качестве аргумента при подключении. Помимо передачи набора данных с помощью `inputs` параметра в средстве оценки, можно также передать набор данных с помощью `script_params` и получить путь к данным (точка подключения) в обучающем скрипте через аргументы. Таким образом вы сможете использовать один и тот же сценарий обучения для локальной отладки и удаленного обучения на любой облачной платформе.

Объект средства оценки [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py&preserve-view=true) используется для отправки экспериментов scikit-учиться. После отправки выполнения файлы данных, на которые ссылается `mnist` набор данных, будут подключены к целевому объекту вычислений. Узнайте больше о обучении с помощью средства [оценки SKlearn](how-to-train-scikit-learn.md).

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

В следующем коде показано, как получить данные в скрипте.

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

## <a name="mount-vs-download"></a>Подключение загрузки VS

Подключение или скачивание файлов любого формата поддерживается для наборов данных, созданных из хранилища BLOB-объектов Azure, файлов Azure Azure Data Lake Storage 1-го поколения, Azure Data Lake Storage 2-го поколения, базы данных SQL Azure и базы данных Azure для PostgreSQL. 

При **подключении** набора данных необходимо прикрепить файлы, на которые ссылается набор данных, к каталогу (точке подключения) и сделать его доступным в целевом объекте вычислений. Поддерживается подключение для вычислений на основе Linux, в том числе Машинное обучение Azure вычислений, виртуальных машин и HDInsight. 

При **скачивании** набора данных все файлы, на которые ссылается набор данных, будут скачаны в целевой объект вычислений. Загрузка поддерживается для всех типов вычислений. 

Если сценарий обрабатывает все файлы, на которые ссылается набор данных, а вычислительный диск может соответствовать полному набору данных, то скачивание рекомендуется, чтобы избежать издержек, связанных с потоковой передачей данных из служб хранилища. Если размер данных превышает размер диска вычислений, загрузка невозможна. В этом сценарии рекомендуется монтирование, так как только файлы данных, используемые скриптом, загружаются во время обработки.

Следующий код подключается `dataset` к каталогу Temp по адресу `mounted_path`

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

## <a name="access-datasets-in-your-script"></a>Доступ к наборам данных в скрипте

Зарегистрированные наборы данных доступны как локально, так и удаленно в таких кластерах, как Машинное обучение Azure вычислений. Чтобы получить доступ к зарегистрированному набору данных во всех экспериментах, используйте следующий код для доступа к рабочей области и зарегистрированному набору данных по имени. По умолчанию [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#&preserve-view=trueget-by-name-workspace--name--version--latest--) метод в `Dataset` классе возвращает последнюю версию набора данных, зарегистрированную в рабочей области.

```Python
%%writefile $script_folder/train.py

from azureml.core import Dataset, Run

run = Run.get_context()
workspace = run.experiment.workspace

dataset_name = 'titanic_ds'

# Get a dataset by name
titanic_ds = Dataset.get_by_name(workspace=workspace, name=dataset_name)

# Load a TabularDataset into pandas DataFrame
df = titanic_ds.to_pandas_dataframe()
```

## <a name="accessing-source-code-during-training"></a>Доступ к исходному коду во время обучения

Хранилище BLOB-объектов Azure имеет более высокую пропускную способность, чем общая папка Azure, и может масштабироваться до большого числа параллельно запущенных заданий. По этой причине рекомендуется настроить запуски для использования хранилища BLOB-объектов для передачи файлов исходного кода.

В следующем примере кода в конфигурации запуска указывается хранилище данных BLOB-объектов, которое будет использоваться для передачи исходного кода.

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="notebook-examples"></a>Примеры записных книжек

В этой статье демонстрируются и развертываются [записные книжки набора данных](https://aka.ms/dataset-tutorial) .

## <a name="next-steps"></a>Дальнейшие действия

* [Автоматическое обучение моделей машинного обучения](how-to-auto-train-remote.md) с помощью табулардатасетс.

* [Обучение моделей классификации изображений](https://aka.ms/filedataset-samplenotebook) с помощью филедатасетс.

* [Обучение с наборами данных с помощью конвейеров](how-to-create-your-first-pipeline.md).
