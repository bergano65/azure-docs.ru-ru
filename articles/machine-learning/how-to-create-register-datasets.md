---
title: Создание Машинное обучение Azure наборов данных для доступа к данным
titleSuffix: Azure Machine Learning
description: Узнайте, как создать Машинное обучение Azure наборы данных для доступа к данным для запуска экспериментов машинного обучения.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 02/10/2020
ms.openlocfilehash: 23984bdbcfc649c2bfe04a08787bc10149a1ed91
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82231899"
---
# <a name="create-azure-machine-learning-datasets"></a>Создание наборов данных Машинное обучение Azure

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Из этой статьи вы узнаете, как создать Машинное обучение Azure наборы данных для доступа к данным в локальных и удаленных экспериментах. Чтобы узнать, где находятся наборы данных в общем рабочем процессе доступа к данным Машинное обучение Azure, ознакомьтесь со статьей [безопасный доступ к](concept-data.md#data-workflow) данным.

С помощью Машинное обучение Azure наборов данных можно:

* Сохранение одной копии данных в хранилище, на которые ссылаются наборы данных.

* Беспроблемный доступ к данным во время обучения модели, не беспокоясь о строках подключения или путях к данным.

* Совместное использование данных и совместная работа с другими пользователями.

## <a name="prerequisites"></a>Предварительные условия
' Для создания наборов данных и работы с ними требуется:

* Подписка Azure. Если у вас еще нет подписки Azure, создайте бесплатную учетную запись, прежде чем начать работу. Попробуйте [бесплатную или платную версию машинное обучение Azure](https://aka.ms/AMLFree).

* [Рабочая область машинное обучение Azure](how-to-manage-workspace.md).

* [Установленный пакет SDK для машинное обучение Azure для Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), включающий пакет azureml-DataSets.

> [!NOTE]
> Некоторые классы наборов данных имеют зависимости от пакета [azureml-](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) DataMarket. Для пользователей Linux эти классы поддерживаются только в следующих дистрибутивах: Red Hat Enterprise Linux, Ubuntu, Fedora и CentOS.

## <a name="compute-size-guidance"></a>Руководство по размеру вычислений

При создании набора данных проверьте вычислительную мощность и размер данных в памяти. Размер данных в хранилище не совпадает с размером данных в кадре. Например, данные в CSV-файлах могут расширяться до 10 раз в кадре данных, поэтому файл CSV размером 1 ГБ может стать 10 ГБ в кадре данных. 

Основной фактор — это то, насколько большой набор данных находится в памяти, т. е. в виде фрейма. Рекомендуется, чтобы размер вычислительных ресурсов и вычислительная мощность содержали двукратный объем ОЗУ. Поэтому, если ваша таблица данных — 10 ГБ, требуется целевой объект для вычислений с объемом ОЗУ 20 + GB, чтобы кадр данных мог помещаться в памяти и обрабатываться. Если данные сжаты, их можно расширить. 20 ГБ относительно разреженных данных, хранящихся в сжатом формате Parquet, можно увеличить до ~ 800 ГБ в памяти. Поскольку файлы Parquet хранят данные в формате столбцов, если требуется только половина столбцов, необходимо загрузить в память ~ 400 ГБ.
 
Если вы используете Pandas, нет необходимости иметь более 1 виртуальных ЦП, поскольку все они будут использоваться. Можно легко параллельно использовать многие виртуальных ЦП на одном Машинное обучение Azure вычислительных экземплярах или узлах с помощью Модин и Даск/Ray, а также при необходимости масштабировать в крупном кластере, `import pandas as pd` просто `import modin.pandas as pd`изменив на. 
 
Если вы не можете получить достаточно большое виртуальное место для данных, у вас есть два варианта: использовать такую платформу, как Spark или Даск, для выполнения обработки данных "нехватка памяти", т. е. Таблица данных загружается в секцию ОЗУ по секциям и обрабатываются с окончательным результатом сбора в конце. Если это слишком большое значение, Spark или Даск позволяет масштабировать кластер, который по-прежнему можно использовать в интерактивном режиме. 

## <a name="dataset-types"></a>Типы наборов данных

Существует два типа наборов данных в зависимости от того, как пользователи их используют в обучении:

* [Табулардатасет](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) представляет данные в табличном формате путем синтаксического анализа указанного файла или списка файлов. Это дает возможность материализовать данные в кадр данных Pandas или Spark. Вы можете создать `TabularDataset` объект из файлов. csv,. TSV,. Parquet,. JSON и из результатов SQL-запроса. Полный список см. в разделе [класс табулардатасетфактори](https://aka.ms/tabulardataset-api-reference).

* Класс [филедатасет](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) ссылается на один или несколько файлов в хранилищах данных или общедоступных URL-адресах. Этот метод позволяет скачать или подключить файлы к вычислению в качестве объекта Филедатасет. Файлы могут иметь любой формат, что позволяет использовать более широкий спектр сценариев машинного обучения, включая глубокое обучение. 

## <a name="create-datasets"></a>Создание наборов данных

Создавая набор данных, вы создаете ссылку на расположение источника данных вместе с копией его метаданных. Поскольку данные остаются в существующем расположении, дополнительные затраты на хранение не взимается. Вы можете создать оба `TabularDataset` набора `FileDataset` данных и с помощью пакета SDK для Python или машинное обучение Azure Studio по https://ml.azure.comадресу.

Чтобы данные были доступны по Машинное обучение Azure, необходимо создать наборы данных по путям в [хранилищах данных Azure](how-to-access-data.md) или общедоступных веб-URL. 

### <a name="use-the-sdk"></a>Использование пакета SDK

Чтобы создать наборы данных из [хранилища данных Azure](how-to-access-data.md) с помощью пакета SDK для Python, выполните следующие действия.

1. Убедитесь, что вы `contributor` или `owner` имеете доступ к зарегистрированному хранилищу данных Azure.

2. Создайте набор данных, ссылаясь на пути в хранилище данных.

> [!Note]
> Набор данных можно создать из нескольких путей в нескольких хранилищах. Нет жесткого ограничения на количество файлов или объем данных, из которых можно создать набор данных. Однако для каждого пути данных в службу хранилища будут отправлены несколько запросов, чтобы проверить, указывает ли она на файл или папку. Эта дополнительная нагрузка может привести к снижению производительности или сбою. Набор данных, ссылающийся на одну папку с 1000 файлами в, считается ссылкой на один путь к данным. Для оптимальной производительности рекомендуется создавать наборы данных, ссылающиеся на менее 100 путей в хранилищах.

#### <a name="create-a-tabulardataset"></a>Создание Табулардатасет

Используйте [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false-) метод `TabularDatasetFactory` класса для чтения файлов в формате CSV или TSV и для создания незарегистрированного табулардатасет. Если выполняется чтение из нескольких файлов, результаты будут объединены в одно табличное представление. 

Следующий код возвращает существующую рабочую область рабочей области и требуемое хранилище данных по имени. Затем передает хранилище данных и расположения файлов в `path` параметр для создания нового табулардатасет,. `weather_ds`

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 file paths in datastore
datastore_paths = [(datastore, 'weather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]

weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```

По умолчанию при создании Табулардатасет типы данных столбца выводятся автоматически. Если выводимые типы не соответствуют ожиданиям, можно указать типы столбцов с помощью следующего кода. Параметр `infer_column_type` применим только для наборов данных, созданных из файлов с разделителями. Кроме того, вы можете [узнать больше о поддерживаемых типах данных](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.datatype?view=azure-ml-py).

> [!IMPORTANT] 
> Если хранилище находится за виртуальной сетью или брандмауэром, поддерживается только создание набора данных с помощью пакета SDK. Чтобы создать набор данных, обязательно включите параметры `validate=False` и `infer_column_types=False` в `from_delimited_files()` метод. Это пропускает начальную проверку и обеспечивает возможность создания набора данных из этих защищенных файлов. 

```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| |пассенжерид|Survived|пкласс|Имя|Пол|Возраст|сибсп|парч|Билет|Плата|кабин|Предпринимались
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|False|3|Браунд, Mr. О'мэлли Owen Харрис|Мужской|22,0|1|0|A/5 21171|7,2500||S
1|2|True|1|Кумингс, Mrs. Джон Кирилл (Флоренция Бриггс TH...|Женский|38,0|1|0|PC 17599|71,2833|C85|В
2|3|True|3|Хеиккинен, промах. лаина|Женский|26,0|0|0|СТОН/O2. 3101282|7,9250||S

Чтобы создать набор данных из таблицы данных Pandas в памяти, запишите данные в локальный файл, например в CSV-файл, и создайте набор данных из этого файла. Этот рабочий процесс демонстрируется в следующем коде.

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

Для чтения [`from_sql_query()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none--query-timeout-30-) из базы данных `TabularDatasetFactory` SQL Azure используйте метод класса:

```Python

from azureml.core import Dataset, Datastore

# create tabular dataset from a SQL database in datastore
sql_datastore = Datastore.get(workspace, 'mssql')
sql_ds = Dataset.Tabular.from_sql_query((sql_datastore, 'SELECT * FROM my_table'))
```

В Табулардатасетс можно указать отметку времени из столбца данных или из любого места, где хранятся данные шаблона пути, чтобы включить признаки временных рядов. Эта спецификация обеспечивает простую и эффективную фильтрацию по времени.

Используйте [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) метод в`TabularDataset` классе, чтобы указать столбец временной метки и включить фильтрацию по времени. Дополнительные сведения см. [в статье Демонстрация API, связанной с табличным временным набором, с данными о погоде NOAA](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb).

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

Используйте [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) метод `FileDatasetFactory` класса для загрузки файлов в любом формате и создания незарегистрированного филедатасет. Если хранилище находится за виртуальной сетью или брандмауэром, задайте параметр `validate =False` в `from_files()` методе. Это обходит этап начальной проверки и гарантирует, что вы сможете создать набор данных на основе этих защищенных файлов.

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
В следующих шагах и анимации показано, как создать набор данных в Машинное обучение Azure Studio https://ml.azure.com.

![Создание набора данных с помощью пользовательского интерфейса](./media/how-to-create-register-datasets/create-dataset-ui.gif)

Чтобы создать набор данных в студии, сделайте следующее:
1. Войдите в https://ml.azure.com.
1. Выберите **наборы данных** в разделе **ресурсы** на левой панели. 
1. Выберите **создать набор данных** , чтобы выбрать источник набора данных. Этот источник может представлять собой локальные файлы, хранилище данных или общедоступные URL-адреса.
1. Выберите **табличный** или **файл** для типа набора данных.
1. Нажмите кнопку **Далее** , чтобы открыть форму **хранилище данных и выбор файлов** . В этой форме можно выбрать место сохранения набора данных после создания, а также выбрать, какие файлы данных следует использовать для набора данных. 
1. Нажмите кнопку **Далее** , чтобы заполнить **Параметры, а также формы предварительного просмотра** и **схемы** . они являются интеллектуально заполненными на основе типа файлов. Вы можете дополнительно настроить набор данных перед созданием этих форм. 
1. Нажмите кнопку **Далее** , чтобы проверить форму **Подтверждение сведений** . Проверьте параметры и создайте для набора данных необязательный профиль. Подробная информация [о профилировании данных](how-to-use-automated-ml-for-ml-models.md#profile). 
1. Выберите **создать** , чтобы завершить создание набора данных.

## <a name="register-datasets"></a>Регистрация наборов данных

Чтобы завершить процесс создания, зарегистрируйте свои наборы данных в рабочей области. Используйте [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--create-new-version-false-) метод для регистрации наборов данных в рабочей области, чтобы поделиться ими с другими пользователями и использовать их в разных экспериментах в рабочей области:

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

> [!Note]
> Наборы данных, созданные с помощью Машинное обучение Azure Studio, автоматически регистрируются в рабочей области.

## <a name="create-datasets-with-azure-open-datasets"></a>Создание наборов данных с помощью открытых наборов данных Azure

[Открытые наборы данных Azure](https://azure.microsoft.com/services/open-datasets/) — это проверенные общедоступные наборы данных, которые можно использовать для добавления функций конкретных сценариев в решения машинного обучения для создания более точных моделей. Наборы данных включают открытые данные о погоде, численности населения, праздниках, общественной безопасности и расположениях, которые помогают вам обучать модели машинного обучения и обогащать прогностические решения. Открытые наборы данных находятся в облаке на Microsoft Azure и включены как в пакет SDK, так и в пользовательский интерфейс рабочей области.

### <a name="use-the-sdk"></a>Использование пакета SDK

Чтобы создать наборы данных с открытыми наборами данных Azure из пакета SDK, убедитесь, что пакет установлен с помощью `pip install azureml-opendatasets`. Каждый дискретный набор данных представлен своим собственным классом в пакете SDK, а некоторые классы доступны как `TabularDataset`, `FileDataset`или оба. Полный список классов см. в [справочной документации](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py) .

Некоторые классы можно извлечь как `TabularDataset` или `FileDataset`, что позволяет напрямую управлять файлами и/или скачивать их. Другие классы могут получить набор данных **только** с помощью одной функции `get_tabular_dataset()` или `get_file_dataset()` . В следующем образце кода приведено несколько примеров этих типов классов.

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

При регистрации набора данных, созданного из открытых наборов данных, данные не загружаются немедленно, но доступ к ним будет осуществляться позже при запросе (например, во время обучения) из центрального хранилища.

### <a name="use-the-ui"></a>Использование пользовательского интерфейса

Наборы данных можно также создавать из открытых классов наборов данных через пользовательский интерфейс. В рабочей области выберите вкладку **наборы данных** в разделе **активы**. В раскрывающемся меню **CREATE DataSet (создание набора данных** ) выберите пункт **из открытых наборов**.

![Открытие набора данных с помощью пользовательского интерфейса](./media/how-to-create-register-datasets/open-datasets-1.png)

Выберите набор данных, выбрав его плитку. (Можно выполнить фильтрацию с помощью панели поиска.) Нажмите кнопку **Далее**.

![Выбор набора данных](./media/how-to-create-register-datasets/open-datasets-2.png)

Выберите имя, под которым будет зарегистрирован набор данных, и при необходимости отфильтруйте данные с помощью доступных фильтров. В этом случае для набора данных "общедоступные праздники" вы фильтруете период времени в один год и код страны только в США. Нажмите кнопку **создания**.

![Установка параметров набора данных и создание набора данных](./media/how-to-create-register-datasets/open-datasets-3.png)

Набор данных теперь доступен в рабочей области в разделе **наборы данных**. Его можно использовать точно так же, как и другие созданные наборы данных.

## <a name="version-datasets"></a>Наборы данных версий

Вы можете зарегистрировать новый набор данных с тем же именем, создав новую версию. Версия набора данных — это способ закладки состояния данных, чтобы можно было применить определенную версию набора данных для эксперимента или будущего воспроизведения. Дополнительные сведения о [версиях наборов данных](how-to-version-track-datasets.md).
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

Зарегистрированные наборы данных доступны как локально, так и удаленно в таких кластерах, как Машинное обучение Azure вычислений. Чтобы получить доступ к зарегистрированному набору данных во всех экспериментах, используйте следующий код для доступа к рабочей области и зарегистрированному набору данных по имени. По умолчанию [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) метод в `Dataset` классе возвращает последнюю версию набора данных, зарегистрированную в рабочей области.

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

## <a name="next-steps"></a>Следующие шаги

* Узнайте, [как обучаться с помощью наборов данных](how-to-train-with-datasets.md).
* Используйте автоматическое машинное обучение для [обучения с табулардатасетс](https://aka.ms/automl-dataset).
* Дополнительные примеры обучения наборов данных см. в [примерах записных книжек](https://aka.ms/dataset-tutorial).
