---
title: Входные и выходные данные из проводов ML
titleSuffix: Azure Machine Learning
description: Подготовка, потребление и генерация данных в конвейерах Машинного обучения Azure
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 04/01/2020
ms.openlocfilehash: 3dd1a82bf7fad1f201f5c0f52af944ef44a3fdf9
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879769"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>Перемещение данных в и между шагами конвейера ML (Python)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Данные имеют центральное значение для конвейеров машинного обучения. В этой статье содержится код для импорта, преобразования и перемещения данных между шагами в конвейере Машинного обучения Azure. Для получения обзора работы данных в машинном обучении Azure можно в [службах хранения данных Azure.](how-to-access-data.md) О преимуществах и структуре конвейеров Azure Machine Learning читайте в разделе [«Что такое конвейеры машинного обучения Azure?».](concept-ml-pipelines.md)

Эта статья покажет вам, как выполнить следующие действия:

- Использование `Dataset` объектов для уже существующих данных
- Доступ к данным в рамках ваших шагов
- Разделение `Dataset` данных на подмножества, такие как подмножества обучения и проверки
- Создание `PipelineData` объектов для передачи данных на следующий этап конвейера
- Используйте `PipelineData` объекты в качестве ввода для шагов конвейера
- Создание `Dataset` новых `PipelineData` объектов из желающих сохраниться

## <a name="prerequisites"></a>Предварительные требования

Что вам понадобится:

- Подписка Azure. Если у вас еще нет подписки Azure, создайте бесплатную учетную запись, прежде чем начинать работу. Попробуйте [бесплатную или платную версию машинного обучения Azure.](https://aka.ms/AMLFree)

- [SDK Для Python,](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)или доступ к [студии машинного обучения Azure.](https://ml.azure.com/)

- Рабочая область машинного обучения Azure.
  
  Либо [создайте рабочее пространство Для машин Azure,](how-to-manage-workspace.md) либо используйте существующее через Python SDK. Импортируйте `Workspace` `Datastore` и класс, и загружайте информацию о подписке из файла `config.json` с помощью функции. `from_config()` Эта функция ищет файл JSON в текущем каталоге по умолчанию, но можно также `from_config(path="your/file/path")`указать параметр пути, чтобы указать на файл с помощью.

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- Некоторые уже существующие данные. В этой статье кратко показано использование [контейнера Azure blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview).

- Дополнительный: Существующий конвейер машинного обучения, описанный в [проекте «Создание и запуск конвейеров машинного обучения» с помощью SDK Azure Machine Learning.](how-to-create-your-first-pipeline.md)

## <a name="use-dataset-objects-for-pre-existing-data"></a>Использование `Dataset` объектов для уже существующих данных 

Предпочтительным способом поглока данных в конвейер является использование объекта [Dataset.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) `Dataset`объекты представляют постоянные данные, доступные на протяжении всего рабочего пространства.

Существует множество способов создания `Dataset` и регистрации объектов. Табулярные наборы данных для делимитированных данных доступны в одном или нескольких файлах. Наборы данных файлов относятся к двоичным данным (например, изображениям) или данным, которые вы будете анализировать. Простейшие программные `Dataset` способы создания объектов — использование существующих капли в рабочем пространстве или общедоступных URL-адресах:

```python
datastore = Datastore.get(workspace, 'training_data')
iris_dataset = Dataset.Tabular.from_delimited_files(DataPath(datastore, 'iris.csv'))

cats_dogs_dataset = Dataset.File.from_files(
    paths='https://download.microsoft.com/download/3/E/1/3E1C3F21-ECDB-4869-8368-6DEBA77B919F/kagglecatsanddogs_3367a.zip',
    archive_options=ArchiveOptions(archive_type=ArchiveType.ZIP, entry_glob='**/*.jpg')
)
```

Дополнительные возможности по созданию наборов данных с различными опциями и из различных источников, их регистрации и рецензирования в испуге Azure Machine Learning, понимании того, как размер данных взаимодействует с вычислительной мощностью, и их версии [см.](how-to-create-register-datasets.md) 

### <a name="pass-datasets-to-your-script"></a>Передайте наборы данных скрипту

Чтобы передать путь набора данных скрипту, `Dataset` используйте `as_named_input()` метод объекта. Полученный `DatasetConsumptionConfig` объект можно передать в качестве аргумента или, `inputs` используя аргумент для скрипта конвейера, `Run.get_context().input_datasets[]`можно получить набор данных с помощью.

После того как вы создали именный вход, вы `as_mount()` `as_download()`можете выбрать его режим доступа: или . Если ваш скрипт обрабатывает все файлы в наборе данных, а диск на вычислительном ресурсе достаточно велик для набора данных, то режим доступа к загрузке является лучшим выбором. Режим доступа к загрузке позволит избежать накладных расходов на потоковую передачу данных во время выполнения. Если скрипт получает доступ к подмножею набора данных или он слишком велик для вычислений, используйте режим доступа к креплению. Для получения дополнительной информации, прочитайте [Маунт против Скачать](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets#mount-vs-download)

Чтобы передать набор данных на этап конвейера:

1. Использование `TabularDataset.as_named_inputs()` `FileDataset.as_named_input()` или (без 's' в `DatasetConsumptionConfig` конце) для создания объекта
1. Использование `as_mount()` `as_download()` или настройка режима доступа
1. Передайте наборы данных на `arguments` этапы `inputs` конвейера, используя аргумент или аргумент

Следующий фрагмент показывает общую схему объединения `PythonScriptStep` этих шагов в конструкторе: 

```python

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[iris_dataset.as_named_inputs('iris').as_mount()]
)
```

Можно также использовать такие `random_split()` методы, как создание `take_sample()` нескольких входных данных или уменьшение объема данных, передаваемых на этап конвейера:

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

Именованные входы в сценарий шага конвейера доступны как словарь в объекте. `Run` Извлеките `Run` активный `Run.get_context()` объект с помощью, а затем `input_datasets`извлеките словарь именных входов с помощью. Если вы `DatasetConsumptionConfig` прошли объект `arguments` с помощью аргумента, а не `inputs` аргумент, доступ к данным с помощью `ArgParser` кода. Оба метода продемонстрированы в следующем фрагменте.

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

Пройденое значение будет путем к файлу набора данных(ы).

Также можно получить доступ к `Dataset` зарегистрированным напрямую. Поскольку зарегистрированные наборы данных являются постоянными и общими для рабочей области, их можно получить непосредственно:

```python
run = Run.get_context()
ws = run.experiment.workspace
ds = Dataset.get_by_name(workspace=ws, name='mnist_opendataset')
```

## <a name="use-pipelinedata-for-intermediate-data"></a>Использование `PipelineData` промежуточных данных

В `Dataset` то время как объекты представляют постоянные данные, объекты [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) используются для временных данных, выводимых из этапов конвейера. Поскольку срок службы `PipelineData` объекта больше одного шага конвейера, вы определяете его в скрипте определения конвейера. При создании `PipelineData` объекта необходимо укажите имя и хранилище данных, в котором будут храниться данные. Передайте `PipelineData` объект (ы) `PythonScriptStep` с `arguments` помощью `outputs` _как_ аргументов, так и аргументов:

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

Вы можете создать `PipelineData` объект с помощью режима доступа, который обеспечивает немедленную загрузку. В этом случае при `PipelineData`создании, `upload_mode` установить `"upload"` и `output_path_on_compute` использовать аргумент, чтобы указать путь, на котором вы будете писать данные:

```python
PipelineData("clean_data", datastore=def_blob_store, output_mode="upload", output_path_on_compute="clean_data_output/")
```

### <a name="use-pipelinedata-as-outputs-of-a-training-step"></a>Использование `PipelineData` в качестве выходов учебного шага

В рамках конвейера `PythonScriptStep`можно получить доступные пути вывода, используя аргументы программы. Если этот шаг является первым и будет инициализировать данные вывода, необходимо создать каталог на указанном пути. Затем вы можете написать все файлы, которые вы хотите содержать в `PipelineData`.

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()

# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

Если вы `PipelineData` создали `is_directory` свой с `True`аргументом, установленным на, было бы достаточно, чтобы просто выполнить `os.makedirs()` вызов, а затем вы будете свободны, чтобы написать все файлы, которые вы хотели на пути. Для получения более подробной информации смотрите справочную документацию [PipelineData.](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)

### <a name="read-pipelinedata-as-inputs-to-non-initial-steps"></a>Читать `PipelineData` как входные данные на неначальные шаги

После того, как начальный `PipelineData` шаг конвейера записывает некоторые данные на путь, и он становится выходом этого начального шага, он может быть использован в качестве ввода на более позднем этапе:

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

Значение `PipelineData` ввода — это путь к предыдущему выходу. Если, как показано ранее, первый шаг написал один файл, потребление может выглядеть следующим образом: 

```python
parser = argparse.ArgumentParser()
parser.add_argument('--pd', dest='pd', required=True)
args = parser.parse_args()

with open(args.pd) as f:
    print(f.read())
```

## <a name="convert-pipelinedata-objects-to-datasets"></a>`PipelineData` Преобразование `Dataset`объектов в s

Если вы хотите сделать `PipelineData` ваш доступен дольше, чем `as_dataset()` продолжительность выполнения, `Dataset`используйте его функцию, чтобы преобразовать его в . Затем вы можете `Dataset`зарегистрировать, что делает его первоклассным гражданином в вашем рабочем пространстве. Поскольку `PipelineData` ваш объект будет иметь другой путь каждый раз, когда `create_new_version` конвейер `True` работает, `Dataset` настоятельно рекомендуется установить при регистрации созданного объекта. `PipelineData`

```python
step1_output_ds = step1_output_data.as_dataset()
step1_output_ds.register(name="processed_data", create_new_version=True)
```

## <a name="next-steps"></a>Дальнейшие действия

* [Создание набора данных машинного обучения Azure](how-to-create-register-datasets.md)
* [Создание и запуск конвейеров машинного обучения с помощью SDK машинного обучения Azure](how-to-create-your-first-pipeline.md)
