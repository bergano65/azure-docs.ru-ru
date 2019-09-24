---
title: Создание наборов данных для доступа к данным с помощью azureml-DataSets
titleSuffix: Azure Machine Learning
description: Узнайте, как создавать наборы данных из различных источников и регистрировать наборы данных в рабочей области.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 08/22/2019
ms.openlocfilehash: d2b9e53fc6c58f0477e252c751e25a99bdbfba42
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200100"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>Создание и доступ к наборам данных (Предварительная версия) в Машинное обучение Azure

В этой статье вы узнаете, как создавать Машинное обучение Azure наборы данных (Предварительная версия) и как получать доступ к данным из локальных и удаленных экспериментов.

С помощью Машинное обучение Azure наборов данных можно: 

* **Хранить одну копию данных в хранилище** , на которое ссылаются наборы DataSet. 

* Вы **сможете легко получить доступ к данным во время обучения модели** , не беспокоясь о строках подключения или путях к данным.

* **Совместное использование данных & совместно** с другими пользователями.

## <a name="prerequisites"></a>Предварительные требования

Для создания наборов данных и работы с ними требуется:

* Подписка Azure. Если у вас еще нет подписки Azure, создайте бесплатную учетную запись Azure, прежде чем начинать работу. Опробуйте [бесплатную или платную версию Машинного обучения Azure](https://aka.ms/AMLFree) уже сегодня.

* [Рабочая область машинное обучение Azure](how-to-manage-workspace.md)

* [Установленный пакет SDK для машинное обучение Azure для Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), включающий пакет azureml-DataSets.

> [!Note]
> Некоторые классы набора данных (Предварительная версия) имеют зависимости от пакета [azureml-](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) DataMarket. Для пользователей Linux эти классы поддерживаются только в следующих дистрибутивах:  Red Hat Enterprise Linux, Ubuntu, Fedora и CentOS.

## <a name="dataset-types"></a>Типы наборов данных

Наборы данных делятся на два типа в зависимости от того, как пользователи их используют в обучении. 

* [Табулардатасет](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) представляет данные в табличном формате путем синтаксического анализа указанного файла или списка файлов. Это дает возможность материализовать данные в кадр данных Pandas или Spark. `TabularDataset` Объект может быть создан из файлов CSV, TSV, Parquet, результатов SQL-запросов и т. д. Полный список см. в нашей [документации](https://aka.ms/tabulardataset-api-reference).

* [Филедатасет](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) ссылается на один или несколько файлов в хранилищах данных или общедоступных URL-адресах. Это дает возможность скачивать файлы или подключать их к вашему вычислению. Файлы могут иметь любой формат, что позволяет использовать более широкий спектр сценариев машинного обучения, включая глубокое обучение.

Дополнительные сведения о предстоящих изменениях API см. [здесь](https://aka.ms/tabular-dataset).

## <a name="create-datasets"></a>Создание наборов данных 

Создавая набор данных, вы создаете ссылку на расположение источника данных вместе с копией его метаданных. Данные остаются в существующем расположении, поэтому дополнительные затраты на хранение не взимается.

Чтобы данные были доступны по Машинное обучение Azure, необходимо создать наборы данных по путям в [хранилищах данных Azure](how-to-access-data.md) или общедоступных веб-URL.

Чтобы создать наборы данных из [хранилища данных Azure](how-to-access-data.md), сделайте следующее:

* Убедитесь, что `contributor` вы `owner` или имеете доступ к зарегистрированному хранилищу данных Azure.

* Создайте набор данных, обратившись к пути в хранилище данных.

```Python
from azureml.core.workspace import Workspace
from azureml.core.datastore import Datastore
from azureml.core.dataset import Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()

# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)
```

### <a name="create-tabulardatasets"></a>Создание Табулардатасетс

Табулардатасетс можно создать с помощью пакета SDK или целевой страницы рабочей области (Предварительная версия). Отметка времени может быть указана из столбца в данных, или данные шаблона пути хранятся в, чтобы включить приtimeseries, что позволяет легко и эффективно выполнять фильтрацию по времени. 

#### <a name="using-the-sdk"></a>Использование пакета SDK 

[`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header--promoteheadersbehavior-all-files-have-same-headers--3---partition-format-none-) Используйте`TabularDatasetFactory` метод класса для чтения файлов в формате CSV или TSV и создания незарегистрированного табулардатасет. При чтении из нескольких файлов результаты будут объединены в одно табличное представление.

```Python
# create a TabularDataset from multiple paths in datastore
datastore_paths = [
                  (datastore, 'weather/2018/11.csv'),
                  (datastore, 'weather/2018/12.csv'),
                  (datastore, 'weather/2019/*.csv')
                 ]
weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)

# create a TabularDataset from a delimited file behind a public web url
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| |пассенжерид|Оставшихся|пкласс|Название|Пол|Время существования|сибсп|парч|Службу|FARE|кабин|Предпринимались
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|0|3|Браунд, Mr. О'мэлли Owen Харрис|Мужской|22,0|1|0|A/5 21171|7,2500||с.
1|2|1|1|Кумингс, Mrs. Джон Кирилл (Флоренция Бриггс TH...|Женский|38,0|1|0|PC 17599|71,2833|C85|К
2|3|1|3|Хеиккинен, промах. лаина|Женский|26,0|0|0|СТОН/O2. 3101282|7,9250||с.

[`from_sql_query()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none-) Используйте`TabularDatasetFactory` метод класса для чтения из базы данных SQL Azure.

```Python

from azureml.core import Dataset, Datastore

# create tabular dataset from a SQL database in datastore
sql_datastore = Datastore.get(workspace, 'mssql')
sql_ds = Dataset.Tabular.from_sql_query((sql_datastore, 'SELECT * FROM my_table'))
```
Используйте метод для `TabularDataset` класса, чтобы обеспечить простую и эффективную фильтрацию по времени. [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-fine-grain-timestamp--coarse-grain-timestamp-none--validate-false-) Дополнительные примеры и подробные сведения можно найти [здесь](http://aka.ms/azureml-tsd-notebook). 

```Python
# create a TabularDataset with timeseries trait
datastore_paths = [(datastore, 'weather/*/*/*/data.parquet')]

# get a coarse timestamp column from the path pattern
dataset = Dataset.Tabular.from_parquet_files(path=datastore_path, partition_format='weather/{coarse_time:yyy/MM/dd}/data.parquet')

# set coarse timestamp to the virtual column created, and fine grain timestamp from a column in the data
dataset = dataset.with_timestamp_columns(fine_grain_timestamp='datetime', coarse_grain_timestamp='coarse_time')

# filter with timeseries trait specific methods 
data_slice = dataset.time_before(datetime(2019, 1, 1))
data_slice = dataset.time_after(datetime(2019, 1, 1))
data_slice = dataset.time_between(datetime(2019, 1, 1), datetime(2019, 2, 1)) 
data_slice = dataset.time_recent(timedelta(weeks=1, days=1))                  
```

#### <a name="using-the-workspace-landing-page"></a>Использование целевой страницы рабочей области 

Войдите на [целевую страницу рабочей области](https://ml.azure.com) , чтобы создать набор данных через веб-интерфейс. В настоящее время Целевая страница рабочей области поддерживает только создание Табулардатасетс.

Следующая анимация показывает, как создать набор данных на целевой странице рабочей области. 

Сначала выберите **наборы данных** в разделе **активы** в левой области. Затем выберите **+ создать набор данных** , чтобы выбрать источник набора данных. Это может быть локальный файл, хранилище данных или общедоступные URL-адреса. **Параметры, предварительный просмотр** и формы **схемы** являются интеллектуальным заполнением на основе типа файла. Нажмите кнопку **Далее** , чтобы проверить их или настроить набор данных перед созданием. Нажмите кнопку **Готово** , чтобы завершить создание набора данных. 

![Создание набора данных с помощью пользовательского интерфейса](media/how-to-create-register-datasets/create-dataset-ui.gif)

### <a name="create-filedatasets"></a>Создание Филедатасетс

[`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) Используйте`FileDatasetFactory` метод класса для загрузки файлов в любом формате и создания незарегистрированного филедатасет.

```Python
# create a FileDataset from multiple paths in datastore
datastore_paths = [
                  (datastore, 'animals/dog/1.jpg'),
                  (datastore, 'animals/dog/2.jpg'),
                  (datastore, 'animals/dog/*.jpg')
                 ]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = [
            'https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
            'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz'
           ]          
mnist_ds = Dataset.File.from_files(path=web_paths)
```

## <a name="register-datasets"></a>Регистрация наборов данных

Чтобы завершить процесс создания, зарегистрируйте наборы данных в рабочей области:

[`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-) Используйте метод для регистрации наборов данных в рабочей области, чтобы их можно было использовать совместно с другими пользователями в различных экспериментах.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data')
```

>[!Note]
> Наборы данных, созданные с помощью целевой страницы рабочей области, автоматически регистрируются в рабочей области. 

## <a name="version-datasets"></a>Наборы данных версий

Вы можете зарегистрировать новый набор данных с тем же именем, создав новую версию. Версия набора данных — это способ закладки состояния данных, чтобы можно было применить определенную версию набора данных для эксперимента или будущего воспроизведения. Типичные сценарии, которые следует учитывать при управлении версиями: 
* Когда новые данные доступны для повторного обучения.
* При применении различных подходов к подготовке данных или проектированию компонентов.

```Python
# create a TabularDataset from Titanic training data
web_paths = [
            'https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
            'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv'
           ]          
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```


## <a name="access-datasets-in-your-script"></a>Доступ к наборам данных в скрипте

Зарегистрированные наборы данных доступны локально и удаленно в таких кластерах, как Машинное обучение Azure вычислений. Для доступа к зарегистрированному набору данных во всех экспериментах используйте следующий код, чтобы получить рабочую область и зарегистрированный набор данных по имени. [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) Метод`Dataset` класса по умолчанию возвращает последнюю версию набора данных, зарегистрированную в рабочей области.

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

* Обучение [работе с наборами данных](how-to-train-with-datasets.md)
* Используйте автоматическое машинное обучение для [обучения с табулардатасетс](https://aka.ms/automl-dataset).
* Дополнительные примеры обучения с наборами данных см. в [примерах записных книжек](https://aka.ms/dataset-tutorial).
