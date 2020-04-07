---
title: Создание наборов машинного обучения Azure для доступа к данным
titleSuffix: Azure Machine Learning
description: Узнайте, как создать наборы данных машинного обучения Azure для доступа к данным для запуска эксперимента по машинного обучению.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 02/10/2020
ms.openlocfilehash: feaa0c22ec98d170a65e5c9bee119ba3904a95cf
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673728"
---
# <a name="create-azure-machine-learning-datasets"></a>Создание наборов машинного обучения Azure

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье вы узнаете, как создать наборы данных Машинного обучения Azure для доступа к данным для локальных или удаленных экспериментов.

С наборами данных машинного обучения Azure вы можете:

* Храните в хранилище единую копию данных, на которые ссылаются наборы данных.

* Бесшовный доступ к данным во время обучения модели, не беспокоясь о строках соединения или путях передачи данных.

* Делитесь данными и сотрудничайте с другими пользователями.

## <a name="prerequisites"></a>Предварительные требования
Для создания и работы с наборами данных необходимо:

* Подписка Azure. Если у вас еще нет подписки Azure, создайте бесплатную учетную запись, прежде чем начать работу. Попробуйте [бесплатную или платную версию машинного обучения Azure.](https://aka.ms/AMLFree)

* [Рабочее пространство машинного обучения Azure](how-to-manage-workspace.md).

* [Установлен SDK Для Python,](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)который включает пакет наборов лазурных данных.

> [!NOTE]
> Некоторые классы наборов данных имеют зависимости от пакета [azureml-dataprep.](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) Для пользователей Linux эти классы поддерживаются только в следующих дистрибутивах: Red Hat Enterprise Linux, Ubuntu, Fedora и CentOS.

## <a name="compute-size-guidance"></a>Руководство по размеру вычисления

При создании набора данных проанализируйте вычислительную вычислительную мощность и размер данных в памяти. Размер данных в хранилище не совпадает с размером данных в кадре данных. Например, данные в файлах CSV могут расширяться до 10 x в кадре данных, поэтому 1 ГБ файла CSV может стать 10 ГБ в кадре данных. 

Основным фактором является то, насколько велик набор данных в памяти, т.е. в качестве кадра данных. Мы рекомендуем ваш размер вычисления и вычислительной мощности содержат 2x размер оперативной памяти. Так что, если ваш dataframe 10GB, вы хотите вычислительной цели с 20 "ГБ оперативной памяти, чтобы гарантировать, что dataframe может удобно вписаться в память и быть обработаны. Если ваши данные сжаты, они могут расширяться дальше; 20 ГБ относительно редких данных, хранящихся в формате сжатого паркета, могут увеличиться до 800 ГБ памяти. Так как файлы Parquet хранят данные в формате columnar, если вам нужна только половина столбцов, то вам нужно только загрузить 400 ГБ в памяти.
 
Если вы используете панды, нет никаких причин, чтобы иметь более 1 vCPU, поскольку это все, что он будет использовать. Вы можете легко параллелизировать с многими vCPUs на одном Экземпляре машинного обучения Azure compute/node через Modin и Dask/Ray, и масштабировать сяврань к большому кластеру, если это необходимо, просто изменяя `import pandas as pd` к `import modin.pandas as pd`. 
 
Если вы не можете получить достаточно большой виртуальный для данных, у вас есть два варианта: использовать фреймворк, как Spark или Dask для выполнения обработки данных "из памяти", т.е. dataframe загружается в перевалу раздела по разделу и обрабатывается, с конечным результатом собираются в конце. Если это слишком медленно, Spark или Dask позволяют масштабироваться в кластер, который все еще может быть использован в интерактивном режиме. 

## <a name="dataset-types"></a>Типы наборов данных

Существует два типа наборов данных, основанных на том, как пользователи потребляют их в обучении:

* [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) представляет данные в табулярном формате путем анализа предоставленного файла или списка файлов. Это дает вам возможность материализовать данные в Pandas или Spark DataFrame. Вы можете `TabularDataset` создать объект из файлов запроса .csv, .tsv, .parquet, .jsonl и из результатов запроса S'L. Полный список можно оперетрать в [классе TabularDatasetFactory.](https://aka.ms/tabulardataset-api-reference)

* Класс [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) ссылается на отдельные или несколько файлов в ваших хранилищах данных или общедоступных URL-адресах. С помощью этого метода можно загрузить или смонтировать файлы для вычисления в качестве объекта FileDataset. Файлы могут быть в любом формате, что позволяет более широкий спектр сценариев машинного обучения, включая глубокое обучение.

Чтобы узнать больше о предстоящих [Dataset API change notice](https://aka.ms/tabular-dataset)изменениях API, см.

## <a name="create-datasets"></a>Создание наборов данных

Создавая набор данных, вы создаете ссылку на местоположение источника данных, а также копию его метаданных. Поскольку данные остаются в существующем местоположении, вы не несете никаких дополнительных затрат на хранение. Вы можете `TabularDataset` создавать `FileDataset` как наборы данных, так https://ml.azure.comи наборы данных с помощью Python SDK или в.

Для того чтобы данные были доступны с помощью Azure Machine Learning, наборы данных должны создаваться из путей в [хранилищах данных Azure](how-to-access-data.md) или общедоступных веб-URL-адресах. 

### <a name="use-the-sdk"></a>Использование SDK

Для создания наборов данных из [хранилища данных Azure](how-to-access-data.md) с помощью Python SDK:

1. Убедитесь, `contributor` что `owner` у вас есть или доступ к зарегистрированной лазурной лавке данных.

2. Создание набора данных путем ссылки на пути в хранилище данных.
> [!Note]
> Можно создать набор данных из нескольких путей в нескольких хранилищах данных. Существует никаких жестких ограничений на количество файлов или размер данных, которые можно создать набор данных из. Однако для каждого пути данных несколько запросов будут отправлены в службу хранения, чтобы проверить, указывает ли он на файл или папку. Это накладные расходы могут привести к деградации производительности или сбою. Набор данных, отображающий одну папку с 1000 файлами внутри, считается ссылкой на один путь данных. Мы рекомендуем создать набор данных, ссылающийся менее чем на 100 путей в хранилищах данных для оптимальной производительности.

#### <a name="create-a-tabulardataset"></a>Создание TabularDataset

Используйте [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false-) метод `TabularDatasetFactory` в классе для чтения файлов в формате .csv или .tsv, а также для создания незарегистрированного TabularDataset. Если вы читаете из нескольких файлов, результаты будут агрегированы в одно табликовое представление. 

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 paths in datastore
datastore_paths = [(datastore, 'weather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]
weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```

По умолчанию при создании TabularDataset типы данных столбцов выводится автоматически. Если выведенные типы не соответствуют вашим ожиданиям, можно указать типы столбцов, используя следующий код. Параметр `infer_column_type` применим только для наборов данных, созданных из делимитированных файлов. Вы также можете [узнать больше о поддерживаемых типах данных.](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.datatype?view=azure-ml-py)

> [!IMPORTANT] 
> Если ваше хранилище находится за виртуальной сетью или брандмауэром, поддерживается только создание набора данных через SDK. Чтобы создать набор данных, обязательно включите параметры `validate=False` и `infer_column_types=False` в свой `from_delimited_files()` метод. Это обходит начальную проверку проверки и гарантирует, что вы можете создать набор данных из этих безопасных файлов. 

```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| |ПассажирИд|Survived|Pclass|name|Секс|Возраст|СибСп|Парч|Билет|Плата|Кабина|Начал
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|False|3|Браунд, мистер Оуэн Харрис|Мужской|22.0|1|0|A/5 21171|7.2500||S
1|2|True|1|Cumings, миссис Джон Брэдли (Флоренция Бриггс Th...|Женский|38.0|1|0|PC 17599|71.2833|C85|C
2|3|True|3|Хейккинен, мисс. Лайна|Женский|26.0|0|0|СТОН/О2. 3101282|7.9250||S


Чтобы создать набор данных из фрейма данных панд в памяти, напишите данные в локальный файл, например csv, и создайте набор данных из этого файла. Следующий код демонстрирует этот рабочий процесс.

```python
local_path = 'data/prepared.csv'
dataframe.to_csv(local_path)
upload the local file to a datastore on the cloud
# azureml-core of version 1.0.72 or higher is required
# azureml-dataprep[pandas] of version 1.1.34 or higher is required
from azureml.core import Workspace, Dataset

subscription_id = 'xxxxxxxxxxxxxxxxxxxxx'
resource_group = 'xxxxxx'
workspace_name = 'xxxxxxxxxxxxxxxx'

workspace = Workspace(subscription_id, resource_group, workspace_name)

# get the datastore to upload prepared data
datastore = workspace.get_default_datastore()

# upload the local file from src_dir to the target_path in datastore
datastore.upload(src_dir='data', target_path='data')
# create a dataset referencing the cloud location
dataset = Dataset.Tabular.from_delimited_files(datastore.path('data/prepared.csv'))
```

Используйте [`from_sql_query()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none--query-timeout-30-) метод `TabularDatasetFactory` в классе для чтения из базы данных Azure S'L:

```Python

from azureml.core import Dataset, Datastore

# create tabular dataset from a SQL database in datastore
sql_datastore = Datastore.get(workspace, 'mssql')
sql_ds = Dataset.Tabular.from_sql_query((sql_datastore, 'SELECT * FROM my_table'))
```

В TabularDatasets можно указать отметку времени из столбца в данных или от того, где хранятся данные шаблона пути, чтобы включить черту временных рядов. Эта спецификация позволяет легко и эффективно фильтровать по времени.

Используйте [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) метод`TabularDataset` в классе, чтобы указать столбец отметки времени и включить фильтрацию по времени. Для получения дополнительной [Tabular time series-related API demo with NOAA weather data](https://aka.ms/azureml-tsd-notebook)информации см.

```Python
# create a TabularDataset with time series trait
datastore_paths = [(datastore, 'weather/*/*/*/data.parquet')]

# get a coarse timestamp column from the path pattern
dataset = Dataset.Tabular.from_parquet_files(path=datastore_path, partition_format='weather/{coarse_time:yyy/MM/dd}/data.parquet')

# set coarse timestamp to the virtual column created, and fine grain timestamp from a column in the data
dataset = dataset.with_timestamp_columns(fine_grain_timestamp='datetime', coarse_grain_timestamp='coarse_time')

# filter with time-series-trait-specific methods
data_slice = dataset.time_before(datetime(2019, 1, 1))
data_slice = dataset.time_after(datetime(2019, 1, 1))
data_slice = dataset.time_between(datetime(2019, 1, 1), datetime(2019, 2, 1))
data_slice = dataset.time_recent(timedelta(weeks=1, days=1))
```

#### <a name="create-a-filedataset"></a>Создание FileDataset

Используйте [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) метод `FileDatasetFactory` в классе для загрузки файлов в любом формате и создания незарегистрированного FileDataset. Если ваше хранилище находится за виртуальной сетью `validate =False` или `from_files()` брандмауэром, установите параметр в методе. Это обходит начальный шаг проверки и гарантирует, что вы можете создать набор данных из этих безопасных файлов.

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```

#### <a name="on-the-web"></a>В Интернете 
Следующие шаги и анимация показывают, как создать набор https://ml.azure.comданных в студии Машинного Обучения Azure.

![Создание набора данных с помощью uI](./media/how-to-create-register-datasets/create-dataset-ui.gif)

Для создания набора данных в студии:
1. Вопиюте в https://ml.azure.com.
1. Выберите **наборы данных** в разделе **Активов** левой панели. 
1. Выберите **создать набор данных,** чтобы выбрать источник набора данных. Этот источник может быть локальным файлами, хранилищем данных или общедоступными URL-адресами.
1. Выберите **tabular** или **файл** для типа набора данных.
1. Выберите **далее,** чтобы открыть форму **Datastore и выбора файлов.** В этой форме вы выбираете, где хранить набор данных после создания, а также выбираете, какие файлы данных использовать для набора данных. 
1. Выберите **Далее,** чтобы заполнить **настройки и предварительный просмотр** и формы **схемы;** они разумно заселены на основе типа файла, и вы можете дополнительно настроить набор данных до создания на этих формах. 
1. Выберите **далее,** чтобы просмотреть форму **подтверждения деталей.** Проверьте свой выбор и создайте дополнительный профиль данных для набора данных. Подробная информация [о профилировании данных](how-to-use-automated-ml-for-ml-models.md#profile). 
1. Выберите **Создать** для завершения создания набора данных.

## <a name="register-datasets"></a>Регистрация наборов данных

Чтобы завершить процесс создания, зарегистрируйте наборы данных в рабочем пространстве. Используйте [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--create-new-version-false-) метод для регистрации наборов данных в рабочем пространстве, чтобы поделиться ими с другими пользователями и повторно использовать их в различных экспериментах:

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

> [!Note]
> Наборы данных, созданные через студию Машинного Обучения Azure, автоматически регистрируются в рабочем пространстве.

## <a name="create-datasets-with-azure-open-datasets"></a>Создание наборов данных с помощью открытых наборов данных Azure

[Открытые наборы данных Azure](https://azure.microsoft.com/services/open-datasets/) — это проверенные общедоступные наборы данных, которые можно использовать для добавления функций конкретных сценариев в решения машинного обучения для создания более точных моделей. Наборы данных включают открытые данные о погоде, численности населения, праздниках, общественной безопасности и расположениях, которые помогают вам обучать модели машинного обучения и обогащать прогностические решения. Открытые наборы данных находятся в облаке Microsoft Azure и включены как в SDK, так и в uI рабочего пространства.

### <a name="use-the-sdk"></a>Использование SDK

Чтобы создать наборы данных с открытыми наборами данных Azure из `pip install azureml-opendatasets`SDK, убедитесь, что вы установили пакет с помощью Каждый дискретный набор данных представлен своим классом в SDK, и `TabularDataset` `FileDataset`некоторые классы доступны как , или оба. Ознакомьтесь с [справочной документацией](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py) для полного списка классов.

Вы можете получить определенные `TabularDataset` классы как или `FileDataset`, что позволяет манипулировать и / или скачать файлы напрямую. Другие классы могут **only** получить набор `get_tabular_dataset()` данных только с помощью одной из функций или `get_file_dataset()` функций. В следующем примере кода показано несколько примеров этих типов классов.

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

При регистрации набора данных, созданного из открытых наборов данных, данные не загружаются сразу, но данные будут доступны позже по запросу (во время обучения, например) из центрального места хранения.

### <a name="use-the-ui"></a>Использование uI

Можно также создавать наборы данных из классов открытых наборов данных через uI. В рабочем пространстве выберите вкладку **Datasets** под **активами.** В меню выпадения **наборов данных** выберите **из открытых наборов данных.**

![Открытый набор данных с uI](./media/how-to-create-register-datasets/open-datasets-1.png)

Выберите набор данных, выбрав его плитку. (У вас есть возможность фильтрации с помощью панели поиска.) Выберите **далее**.

![Выберите набор данных](./media/how-to-create-register-datasets/open-datasets-2.png)

Выберите имя, под которым можно зарегистрировать набор данных, и дополнительно отфильтруйте данные с помощью доступных фильтров. В этом случае для набора данных о праздничных днях вы фильтруете период времени до одного года и код страны только в США. Нажмите кнопку **создания**.

![Установите парамы набора данных и создайте набор данных](./media/how-to-create-register-datasets/open-datasets-3.png)

Теперь набор данных доступен в рабочей области под **наборами данных.** Вы можете использовать его так же, как и другие созданные вами наборы данных.

## <a name="version-datasets"></a>Наборы данных версии

Вы можете зарегистрировать новый набор данных под тем же названием, создав новую версию. Версия набора данных — это способ закладки состояния ваших данных, чтобы можно было применить определенную версию набора данных для экспериментов или будущего воспроизведения. Подробнее о [версиях набора данных.](how-to-version-track-datasets.md)
```Python
# create a TabularDataset from Titanic training data
web_paths = ['https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
             'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv']
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```

## <a name="access-datasets-in-your-script"></a>Доступ к наборам данных в скрипте

Зарегистрированные наборы данных доступны как локально, так и удаленно на вычислительных кластерах, таких как Azure Machine Learning compute. Чтобы получить доступ к зарегистрированному набору данных в рамках экспериментов, используйте следующий код для доступа к рабочему пространству и зарегистрированному набору данных по имени. По умолчанию [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) метод `Dataset` в классе возвращает последнюю версию набора данных, зарегистрированную в рабочем пространстве.

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

## <a name="next-steps"></a>Дальнейшие действия

* [Узнайте, как тренироваться с наборами данных.](how-to-train-with-datasets.md)
* Используйте автоматизированное машинное обучение для [обучения с TabularDatasets.](https://aka.ms/automl-dataset)
* Для получения дополнительных примеров [sample notebooks](https://aka.ms/dataset-tutorial)обучения набору данных см.
