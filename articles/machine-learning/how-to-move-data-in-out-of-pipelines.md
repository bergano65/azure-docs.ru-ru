---
title: Перемещение данных в конвейерах машинного обучения
titleSuffix: Azure Machine Learning
description: Сведения о входных & выходных данных в конвейерах Машинное обучение Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 08/20/2020
ms.topic: conceptual
ms.custom: how-to, contperfq4, devx-track-python
ms.openlocfilehash: 1b6b5af2e6533c13165ae8253813a52b2c7ad261
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/23/2020
ms.locfileid: "88756968"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>Перемещение данных в этапы конвейера машинного обучения и между ними (Python)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье представлен код для импорта, преобразования и перемещения данных между шагами в конвейере Машинное обучение Azure. Общие сведения о работе с данными в Машинное обучение Azure см. [в статье доступ к данным в службах хранилища Azure](how-to-access-data.md). Преимущества и структура конвейеров Машинное обучение Azure см. в разделе [что такое конвейеры машинное обучение Azure?](concept-ml-pipelines.md).

Эта статья покажет вам, как выполнить следующие действия:

- Использование `Dataset` объектов для уже существующих данных
- Доступ к данным в рамках шагов
- Разделение `Dataset` данных на подмножества, такие как подмножества обучения и проверки
- Создание `OutputFileDatasetConfig` объектов для передачи данных на следующий этап конвейера
- Использование `OutputFileDatasetConfig` объектов в качестве входных данных для шагов конвейера
- Создание новых `Dataset` объектов из `OutputFileDatasetConfig` сохраняемых

> [!NOTE]
>`OutputFileDatasetConfig`Классы и `OutputTabularDatasetConfig` являются экспериментальными функциями предварительной версии и могут изменяться в любое время.
>
>Для получения дополнительной информации см. https://aka.ms/azuremlexperimental.

## <a name="prerequisites"></a>Предварительные требования

Что вам понадобится:

- Подписка Azure. Если у вас еще нет подписки Azure, создайте бесплатную учетную запись, прежде чем начинать работу. Попробуйте [бесплатную или платную версию Машинного обучения Azure](https://aka.ms/AMLFree).

- [Пакет SDK для Машинного обучения Azure для Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) или доступ к [Студии машинного обучения Azure](https://ml.azure.com/).

- Рабочая область машинного обучения Azure.
  
  [Создайте рабочую область Машинного обучения Azure](how-to-manage-workspace.md) или используйте существующую с помощью пакета SDK для Python. Импортируйте классы `Workspace` и `Datastore` и загрузите сведения о подписке из файла `config.json` с помощью функции `from_config()`. Эта функция ищет JSON-файл в текущем каталоге по умолчанию, но можно также указать параметр path, указывающий на файл с помощью `from_config(path="your/file/path")` .

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- Некоторые существовавшие ранее данные. В этой статье кратко показано использование [контейнера больших двоичных объектов Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview).

- Необязательно. существующий конвейер машинного обучения, например, описанный в статье [Создание и запуск конвейеров машинного обучения с помощью пакета SDK для машинное обучение Azure](how-to-create-your-first-pipeline.md).

## <a name="use-dataset-objects-for-pre-existing-data"></a>Использование `Dataset` объектов для уже существующих данных 

Предпочтительный способ приема данных в конвейер — использование объекта [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) . `Dataset` объекты представляют постоянные данные, доступные во всей рабочей области.

Существует множество способов создания и регистрации `Dataset` объектов. Табличные наборы данных предназначены для доступа к данным с разделителями в одном или нескольких файлах. Файловые наборы данных — это двоичные данные (например, изображения) или данные, которые вы будете анализировать. Самым простым программным способом создания `Dataset` объектов является использование существующих больших двоичных объектов в хранилище рабочей области или общедоступных URL-адресах.

```python
datastore = Datastore.get(workspace, 'training_data')
iris_dataset = Dataset.Tabular.from_delimited_files(DataPath(datastore, 'iris.csv'))

cats_dogs_dataset = Dataset.File.from_files(
    paths='https://download.microsoft.com/download/3/E/1/3E1C3F21-ECDB-4869-8368-6DEBA77B919F/kagglecatsanddogs_3367a.zip',
    archive_options=ArchiveOptions(archive_type=ArchiveType.ZIP, entry_glob='**/*.jpg')
)
```

Дополнительные параметры для создания наборов данных с разными параметрами и из разных источников, их регистрации и просмотра в пользовательском интерфейсе Машинное обучение Azure, понимание того, как размер данных взаимодействует с производительностью вычислений и их управление версиями, см. в разделе [Create машинное обучение Azure DataSets](how-to-create-register-datasets.md). 

### <a name="pass-datasets-to-your-script"></a>Передача наборов данных в скрипт

Чтобы передать путь к набору данных в скрипт, используйте `Dataset` `as_named_input()` метод объекта. Можно либо передать полученный `DatasetConsumptionConfig` объект в скрипт в качестве аргумента, либо, используя `inputs` аргумент для скрипта конвейера, извлечь набор данных с помощью `Run.get_context().input_datasets[]` .

После создания именованного входа можно выбрать его режим доступа: `as_mount()` или `as_download()` . Если сценарий обрабатывает все файлы в наборе данных, а диск в ресурсе вычислений достаточно велик для набора данных, то лучше использовать режим доступа для загрузки. Режим доступа для загрузки позволяет избежать издержек при потоковой передаче данных во время выполнения. Если скрипт обращается к подмножеству набора данных или слишком велик для вычислений, используйте режим подключения к подключению. Дополнительные сведения см. в статье [Установка и загрузка.](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets#mount-vs-download)

Чтобы передать набор данных на шаг конвейера, выполните следующие действия.

1. `TabularDataset.as_named_inputs()` `FileDataset.as_named_input()` Чтобы создать объект, используйте оператор или (No "в конце)" `DatasetConsumptionConfig`
1. Использование `as_mount()` или `as_download()` для установки режима доступа
1. Передайте наборы данных в этапы конвейера, используя либо `arguments` аргумент, либо `inputs`

В следующем фрагменте кода показан общий шаблон объединения этих шагов в `PythonScriptStep` конструкторе: 

```python

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[iris_dataset.as_named_inputs('iris').as_mount()]
)
```

Можно также использовать методы, такие как `random_split()` и, `take_sample()` чтобы создать несколько входов или уменьшить объем данных, передаваемых на шаг конвейера:

```python
seed = 42 # PRNG seed
smaller_dataset = iris_dataset.take_sample(0.1, seed=seed) # 10%
train, test = smaller_dataset.random_split(percentage=0.8, seed=seed)

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[train.as_named_inputs('train').as_download(), test.as_named_inputs('test').as_download()]
)
```

### <a name="access-datasets-within-your-script"></a>Доступ к наборам данных в скрипте

Именованные входные данные для скрипта шага конвейера доступны в виде словаря внутри `Run` объекта. Получите активный `Run` объект, используя `Run.get_context()` , а затем получите словарь именованных входов с помощью `input_datasets` . Если вы передали `DatasetConsumptionConfig` объект с помощью `arguments` аргумента, а не `inputs` аргумента, получите доступ к данным с помощью `ArgParser` кода. Оба метода показаны в следующем фрагменте кода.

```python
# In pipeline definition script:
# Code for demonstration only: It would be very confusing to split datasets between `arguments` and `inputs`
train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    arguments=['--training-folder', train.as_named_inputs('train').as_download()]
    inputs=[test.as_named_inputs('test').as_download()]
)

# In pipeline script
parser = argparse.ArgumentParser()
parser.add_argument('--training-folder', type=str, dest='train_folder', help='training data folder mounting point')
args = parser.parse_args()
training_data_folder = args.train_folder

testing_data_folder = Run.get_context().input_datasets['test']
```

Переданное значение будет представлять собой путь к файлам набора данных.

Кроме того, можно получить доступ к зарегистрированной `Dataset` напрямую. Так как зарегистрированные наборы данных являются постоянными и доступны для общего доступа в рабочей области, их можно получить напрямую:

```python
run = Run.get_context()
ws = run.experiment.workspace
ds = Dataset.get_by_name(workspace=ws, name='mnist_opendataset')
```

## <a name="use-outputfiledatasetconfig-for-intermediate-data"></a>Использовать `OutputFileDatasetConfig` для промежуточных данных

Хотя `Dataset` объекты представляют только постоянные данные, [`OutputFileDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py) можно использовать объекты для временных выходных данных этапов конвейера **и** постоянных выходных данных. 

 `OutputFileDatasetConfig` поведение объекта по умолчанию — запись в хранилище данных по умолчанию рабочей области. Передайте `OutputFileDatasetConfig` объекты в `PythonScriptStep` с помощью `arguments` параметра.

```python
from azureml.data import OutputFileDatasetConfig
dataprep_output = OutputFileDatasetConfig()
input_dataset = Dataset.get_by_name(workspace, 'raw_data')

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    compute_target=cluster,
    arguments=[input_dataset.as_named_input('raw_data').as_mount(), dataprep_output]
    )
```

Вы можете отправить содержимое `OutputFileDatasetConfig` объекта в конце выполнения. В этом случае используйте `as_upload()` функцию вместе с `OutputFileDatasetConfig` объектом и укажите, следует ли перезаписывать существующие файлы в назначении. 

```python
#get blob datastore already registered with the workspace
blob_store= ws.datastores['my_blob_store']
OutputFileDatasetConfig(name="clean_data", destination=blob_store).as_upload(overwrite=False)
```

### <a name="use-outputfiledatasetconfig-as-outputs-of-a-training-step"></a>Использование `OutputFileDatasetConfig` в качестве выходных данных для этапа обучения

В конвейере `PythonScriptStep` можно получить доступные выходные пути с помощью аргументов программы. Если этот шаг является первым и будет инициализировать выходные данные, необходимо создать каталог по указанному пути. Затем можно записать все файлы, которые должны содержаться в `OutputFileDatasetConfig` .

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()

# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

### <a name="read-outputfiledatasetconfig-as-inputs-to-non-initial-steps"></a>Считать `OutputFileDatasetConfig` входные данные неначальными шагами

После того, как начальный этап конвейера записывает некоторые данные в `OutputFileDatasetConfig` путь и получает выходные данные этого начального шага, его можно использовать в качестве входных данных для последующего шага. 

В следующем коде 

* `step1_output_data` Указывает, что выходные данные Писонскриптстеп `step1` записываются в хранилище данных ADLS поколения 2 `my_adlsgen2` в режиме доступа для передачи. Дополнительные сведения о [настройке разрешений роли](how-to-access-data.md#azure-data-lake-storage-generation-2) для записи данных в ADLS поколения 2. 

* После `step1` завершения, и выходные данные записываются в место назначения, указанное в параметре `step1_output_data` , шаг 2 готов к использованию в `step1_output_data` качестве входных данных. 

```python
# get adls gen 2 datastore already registered with the workspace
datastore = workspace.datastores['my_adlsgen2']
step1_output_data = OutputFileDatasetConfig(name="processed_data", destination=datastore).as_upload()

step1 = PythonScriptStep(
    name="generate_data",
    script_name="step1.py",
    runconfig = aml_run_config,
    arguments = ["--output_path", step1_output_data]
)

step2 = PythonScriptStep(
    name="read_pipeline_data",
    script_name="step2.py",
    compute_target=compute,
    runconfig = aml_run_config,
    arguments = ["--pd", step1_output_data.as_input]

)

pipeline = Pipeline(workspace=ws, steps=[step1, step2])
```

## <a name="register-outputfiledatasetconfig-objects-for-reuse"></a>Регистрация `OutputFileDatasetConfig` объектов для повторного использования

Если вы хотите, чтобы ваш `OutputFileDatasetConfig` ресурс стал доступным дольше вашего эксперимента, зарегистрируйте его в рабочей области для совместного использования и повторного использования в экспериментах.

```python
step1_output_ds = step1_output_data.register_on_complete(name='processed_data', 
                                                         description = 'files from step1`)
```

## <a name="next-steps"></a>Дальнейшие действия

* [Создание набора данных Машинного обучения Azure](how-to-create-register-datasets.md)
* [Создание и запуск конвейеров машинного обучения с помощью пакета SDK для Машинное обучение Azure](how-to-create-your-first-pipeline.md)
