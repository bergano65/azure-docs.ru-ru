---
title: Перемещение данных в конвейерах машинного обучения
titleSuffix: Azure Machine Learning
description: Сведения о входных & выходных данных в конвейерах Машинное обучение Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: laobri
author: lobrien
ms.date: 07/20/2020
ms.custom: contperfq4, tracking-python
ms.openlocfilehash: 4c1a64b96e1548e0eeaadbccc5ff2ec3020f3ba2
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87030850"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>Перемещение данных в этапы конвейера машинного обучения и между ними (Python)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье представлен код для импорта, преобразования и перемещения данных между шагами в конвейере Машинное обучение Azure. Общие сведения о работе с данными в Машинное обучение Azure см. [в статье доступ к данным в службах хранилища Azure](how-to-access-data.md). Преимущества и структура конвейеров Машинное обучение Azure см. в разделе [что такое конвейеры машинное обучение Azure?](concept-ml-pipelines.md).

Эта статья покажет вам, как выполнить следующие действия:

- Использование `Dataset` объектов для уже существующих данных
- Доступ к данным в рамках шагов
- Разделение `Dataset` данных на подмножества, такие как подмножества обучения и проверки
- Создание `PipelineData` объектов для передачи данных на следующий этап конвейера
- Использование `PipelineData` объектов в качестве входных данных для шагов конвейера
- Создание новых `Dataset` объектов из `PipelineData` сохраняемых

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

Предпочтительный способ приема данных в конвейер — использование объекта [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) . `Dataset`объекты представляют постоянные данные, доступные во всей рабочей области.

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

## <a name="use-pipelinedata-for-intermediate-data"></a>Использовать `PipelineData` для промежуточных данных

Хотя `Dataset` объекты представляют постоянные данные, объекты [пипелинедата](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) используются для временных данных, выводимых шагами конвейера. Так как время существования `PipelineData` объекта длиннее, чем один шаг конвейера, оно определяется в скрипте определения конвейера. При создании `PipelineData` объекта необходимо указать имя и хранилище данных, в котором будут размещаться данные. Передайте `PipelineData` объекты в объект `PythonScriptStep` _both_ с помощью `arguments` `outputs` аргументов и.

```python
default_datastore = workspace.get_default_datastore()
dataprep_output = PipelineData("clean_data", datastore=default_datastore)

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    compute_target=cluster,
    arguments=["--output-path", dataprep_output]
    inputs=[Dataset.get_by_name(workspace, 'raw_data')],
    outputs=[dataprep_output]
)
```

Вы можете создать `PipelineData` объект с помощью режима доступа, который обеспечивает немедленную передачу. В этом случае при создании `PipelineData` задайте `upload_mode` для значение `"upload"` и используйте аргумент, чтобы `output_path_on_compute` указать путь, по которому будут записываться данные:

```python
PipelineData("clean_data", datastore=def_blob_store, output_mode="upload", output_path_on_compute="clean_data_output/")
```

### <a name="use-pipelinedata-as-outputs-of-a-training-step"></a>Использование `PipelineData` в качестве выходных данных для этапа обучения

В конвейере `PythonScriptStep` можно получить доступные выходные пути с помощью аргументов программы. Если этот шаг является первым и будет инициализировать выходные данные, необходимо создать каталог по указанному пути. Затем можно записать все файлы, которые должны содержаться в `PipelineData` .

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()

# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

Если вы создали `PipelineData` с `is_directory` аргументом, для которого задано значение `True` , достаточно просто выполнить `os.makedirs()` вызов, после чего вы сможете писать любые файлы, необходимые для пути. Дополнительные сведения см. в справочной документации по [пипелинедата](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) .

### <a name="read-pipelinedata-as-inputs-to-non-initial-steps"></a>Считать `PipelineData` входные данные неначальными шагами

После того, как начальный этап конвейера записывает некоторые данные в `PipelineData` путь и получает выходные данные этого начального шага, его можно использовать в качестве входных данных для последующего шага:

```python
step1_output_data = PipelineData("processed_data", datastore=def_blob_store, output_mode="upload")

step1 = PythonScriptStep(
    name="generate_data",
    script_name="step1.py",
    runconfig = aml_run_config,
    arguments = ["--output_path", step1_output_data],
    inputs=[],
    outputs=[step1_output_data]
)

step2 = PythonScriptStep(
    name="read_pipeline_data",
    script_name="step2.py",
    compute_target=compute,
    runconfig = aml_run_config,
    arguments = ["--pd", step1_output_data],
    inputs=[step1_output_data]
)

pipeline = Pipeline(workspace=ws, steps=[step1, step2])
```

`PipelineData`Входным значением является путь к предыдущему выходному файлу. Если, как показано ранее, первый шаг записал один файл, его использование может выглядеть следующим образом: 

```python
parser = argparse.ArgumentParser()
parser.add_argument('--pd', dest='pd', required=True)
args = parser.parse_args()

with open(args.pd) as f:
    print(f.read())
```

## <a name="convert-pipelinedata-objects-to-datasets"></a>Преобразовать `PipelineData` объекты в `Dataset` s

Если вы хотите сделать `PipelineData` доступной больше времени выполнения, используйте его `as_dataset()` функцию для преобразования в `Dataset` . Затем вы можете зарегистрировать `Dataset` , сделав его членом первого класса в вашей рабочей области. Так как у `PipelineData` объекта будет свой путь при каждом запуске конвейера, настоятельно рекомендуется задать значение `create_new_version` `True` при регистрации `Dataset` созданного из `PipelineData` объекта.

```python
step1_output_ds = step1_output_data.as_dataset()
step1_output_ds.register(name="processed_data", create_new_version=True)
```

## <a name="next-steps"></a>Дальнейшие действия

* [Создание набора данных Машинного обучения Azure](how-to-create-register-datasets.md)
* [Создание и запуск конвейеров машинного обучения с помощью пакета SDK для Машинное обучение Azure](how-to-create-your-first-pipeline.md)
