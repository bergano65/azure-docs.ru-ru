---
title: Конвейер машинного обучения YAML
titleSuffix: Azure Machine Learning
description: Узнайте, как определить конвейер машинного обучения с помощью файла YAML. Определения конвейера YAML используются с расширением машинного обучения для Azure CLI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: sanpil
author: sanpil
ms.date: 11/11/2019
ms.openlocfilehash: 40e6d7f3d9c28708c5adec26ddc3c0463e75adc0
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81529711"
---
# <a name="define-machine-learning-pipelines-in-yaml"></a>Определение конвейеров машинного обучения в YAML

Узнайте, как определить конвейеры машинного обучения в [YAML.](https://yaml.org/) При использовании расширения машинного обучения для Azure CLI многие команды, связанные с конвейером, ожидают файл YAML, определяющий конвейер.

В следующей таблице перечислены данные, которые поддерживаются и не поддерживаются при определении конвейера в YAML:

| Тип шага | Поддержка |
| ----- | :-----: |
| PythonScriptStep | Да |
| AdlaStep | Да |
| AzureBatchStep | Да |
| DatabricksStep | Да |
| DataTransferStep | Да |
| AutoMLStep | нет |
| HyperDriveStep | нет |
| ModuleStep | Да |
| MPIStep | нет |
| EstimatorStep | нет |

## <a name="pipeline-definition"></a>Определение конвейера

Определение трубопровода использует следующие клавиши, которые соответствуют классу [трубопроводов:](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py)

| Ключ YAML | Описание |
| ----- | ----- |
| `name` | Описание трубопровода. |
| `parameters` | Параметр (ы) к конвейеру. |
| `data_reference` | Определяет, как и где данные должны быть доступны в перспективе. |
| `default_compute` | Цель вычислений по умолчанию, где работают все этапы конвейера. |
| `steps` | Шаги, используемые в конвейере. |

## <a name="parameters"></a>Параметры

В `parameters` разделе используются следующие клавиши, которые соответствуют классу [PipelineParameter:](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py)

| Ключ YAML | Описание |
| ---- | ---- |
| `type` | Тип значения параметра. Действительные `string` `int`типы, `bool`, `datapath` `float`, или . |
| `default` | Значение по умолчанию. |

Каждый параметр назван. Например, следующий фрагмент YAML определяет три `NumIterationsParameter`параметра, `DataPathParameter`названные, и: `NodeCountParameter`

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        NumIterationsParameter:
            type: int
            default: 40
        DataPathParameter:
            type: datapath
            default:
                datastore: workspaceblobstore
                path_on_datastore: sample2.txt
        NodeCountParameter:
            type: int
            default: 4
```

## <a name="data-reference"></a>Описание данных

В `data_references` разделе используются следующие клавиши, которые соответствуют [DataReference:](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py)

| Ключ YAML | Описание |
| ----- | ----- |
| `datastore` | Хранилище данных для ссылки. |
| `path_on_datastore` | Относительный путь в резервном хранилище для ссылки данных. |

Каждая ссылка данных содержится в ключе. Например, следующий фрагмент YAML определяет ссылку на данные, хранящуюся в ключе с именем: `employee_data`

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        employee_data:
            datastore: adftestadla
            path_on_datastore: "adla_sample/sample_input.csv"
```

## <a name="steps"></a>Шаги

Шаги определяют вычислительную среду, а также файлы для запуска в среде. Чтобы определить тип шага, используйте `type` ключ:

| Тип шага | Описание |
| ----- | ----- |
| `AdlaStep` | Запускает скрипт U-S'L с помощью аналитики Azure Data Lake. Соответствует классу [AdlaStep.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.adlastep?view=azure-ml-py) |
| `AzureBatchStep` | Выполняет задания с помощью Azure Batch. Соответствует классу [AzureBatchStep.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep?view=azure-ml-py) |
| `DatabricsStep` | Добавляет блокнот Databricks, скрипт Python или JAR. Соответствует классу [DatabricksStep.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricksstep?view=azure-ml-py) |
| `DataTransferStep` | Передача данных между вариантами хранения. Соответствует классу [DataTransferStep.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py) |
| `PythonScriptStep` | Запускает сценарий Python. Соответствует классу [PythonScriptStep.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) |

### <a name="adla-step"></a>Шаг ADLA

| Ключ YAML | Описание |
| ----- | ----- |
| `script_name` | Название сценария U-S'L (относительно `source_directory`сценария ). |
| `compute_target` | Цель Azure Data Lake для использования в этом шаге. |
| `parameters` | [Параметры](#parameters) конвейера. |
| `inputs` | Входные данные могут быть [ВводПортобязательным,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py) [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Набор данных](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [Определение данных](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py), или [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | Выходы могут быть либо [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) или [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `source_directory` | Каталог, содержащий сценарий, сборки и т.д. |
| `priority` | Приоритетное значение для использования для текущего задания. |
| `params` | Словарь пар именной ценности. |
| `degree_of_parallelism` | Степень параллелизма, чтобы использовать для этой работы. |
| `runtime_version` | Версия времени выполнения движка Data Lake Analytics. |
| `allow_reuse` | Определяет, следует ли повторно использовать предыдущие результаты при повторном запуске с теми же настройками. |

Следующий пример содержит определение шага ADLA:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        employee_data:
            datastore: adftestadla
            path_on_datastore: "adla_sample/sample_input.csv"
    default_compute: adlacomp
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "AdlaStep"
            name: "MyAdlaStep"
            script_name: "sample_script.usql"
            source_directory: "D:\\scripts\\Adla"
            inputs:
                employee_data:
                    source: employee_data
            outputs:
                OutputData:
                    destination: Output4
                    datastore: adftestadla
                    bind_mode: mount
```

### <a name="azure-batch-step"></a>Шаг пакета Azure

| Ключ YAML | Описание |
| ----- | ----- |
| `compute_target` | Цель вычислений Azure Batch для использования для этого шага. |
| `inputs` | Входные данные могут быть [ВводПортобязательным,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py) [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Набор данных](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [Определение данных](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py), или [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | Выходы могут быть либо [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) или [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `source_directory` | Каталог, содержащий модульные бинарные файлы, исполняемые, сборки и т.д. |
| `executable` | Имя команды/исполняемой, которая будет работать как часть этой работы. |
| `create_pool` | Флаг Boolean, чтобы указать, следует ли создать пул перед запуском задания. |
| `delete_batch_job_after_finish` | Флаг Boolean указывает, следует ли удалить задание из учетной записи Batch после его завершения. |
| `delete_batch_pool_after_finish` | Флаг Boolean, чтобы указать, следует ли удалить пул после завершения задания. |
| `is_positive_exit_code_failure` | Флаг Boolean, чтобы указать, если задание не выполняется, если задача выходит с положительным кодом. |
| `vm_image_urn` | Если `create_pool` `True`есть, и `VirtualMachineConfiguration`VM использует . |
| `pool_id` | Идентификатор пула, в котором будет работать задание. |
| `allow_reuse` | Определяет, следует ли повторно использовать предыдущие результаты при повторном запуске с теми же настройками. |

Следующий пример содержит определение шага пакета Azure:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        input:
            datastore: workspaceblobstore
            path_on_datastore: "input.txt"
    default_compute: testbatch
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "AzureBatchStep"
            name: "MyAzureBatchStep"
            pool_id: "MyPoolName"
            create_pool: true
            executable: "azurebatch.cmd"
            source_directory: "D:\\scripts\\AureBatch"
            allow_reuse: false
            inputs:
                input:
                    source: input
            outputs:
                output:
                    destination: output
                    datastore: workspaceblobstore
```

### <a name="databricks-step"></a>Шаг Databricks

| Ключ YAML | Описание |
| ----- | ----- |
| `compute_target` | Цель Azure Databricks вычисляет для использования для этого шага. |
| `inputs` | Входные данные могут быть [ВводПортобязательным,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py) [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Набор данных](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [Определение данных](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py), или [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | Выходы могут быть либо [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) или [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `run_name` | Имя в Databricks для этого запуска. |
| `source_directory` | Каталог, содержащий скрипт и другие файлы. |
| `num_workers` | Статическое число работников для кластера Databricks. |
| `runconfig` | Путь к `.runconfig` файлу. Этот файл представляет собой представление YAML класса [RunConfiguration.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) Для получения дополнительной информации о структуре этого файла, [см.](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json) |
| `allow_reuse` | Определяет, следует ли повторно использовать предыдущие результаты при повторном запуске с теми же настройками. |

Следующий пример содержит шаг Databricks:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        adls_test_data:
            datastore: adftestadla
            path_on_datastore: "testdata"
        blob_test_data:
            datastore: workspaceblobstore
            path_on_datastore: "dbtest"
    default_compute: mydatabricks
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "DatabricksStep"
            name: "MyDatabrickStep"
            run_name: "DatabricksRun"
            python_script_name: "train-db-local.py"
            source_directory: "D:\\scripts\\Databricks"
            num_workers: 1
            allow_reuse: true
            inputs:
                blob_test_data:
                    source: blob_test_data
            outputs:
                OutputData:
                    destination: Output4
                    datastore: workspaceblobstore
                    bind_mode: mount
```

### <a name="data-transfer-step"></a>Шаг передачи данных

| Ключ YAML | Описание |
| ----- | ----- |
| `compute_target` | Вычислительная цель Azure Data Factory для использования для этого шага. |
| `source_data_reference` | Входное соединение, которое служит источником операций по передаче данных. Поддерживаемые значения: [Вводпорта,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py) [DataReference,](#data-reference) [PortDataReference,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py) [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Набор данных,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) [Определение данных](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py), или [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `destination_data_reference` | Входное соединение, которое служит пунктом назначения операций по передаче данных. Поддерживаемые значения [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) и [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `allow_reuse` | Определяет, следует ли повторно использовать предыдущие результаты при повторном запуске с теми же настройками. |

Следующий пример содержит шаг передачи данных:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        adls_test_data:
            datastore: adftestadla
            path_on_datastore: "testdata"
        blob_test_data:
            datastore: workspaceblobstore
            path_on_datastore: "testdata"
    default_compute: adftest
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "DataTransferStep"
            name: "MyDataTransferStep"
            adla_compute_name: adftest
            source_data_reference:
                adls_test_data:
                    source: adls_test_data
            destination_data_reference:
                blob_test_data:
                    source: blob_test_data
```

### <a name="python-script-step"></a>Шаг сценария Python

| Ключ YAML | Описание |
| ----- | ----- |
| `inputs` | Входные данные могут быть [ВводПортобязательным,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py) [DataReference](#data-reference), [PortDataReference](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [Набор данных](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [Определение данных](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py), или [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | Выходы могут быть либо [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) или [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `script_name` | Название сценария Python (относительно `source_directory`). |
| `source_directory` | Каталог, содержащий сценарий, среду Conda и т.д. |
| `runconfig` | Путь к `.runconfig` файлу. Этот файл представляет собой представление YAML класса [RunConfiguration.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) Для получения дополнительной информации о структуре этого файла, [см.](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json) |
| `allow_reuse` | Определяет, следует ли повторно использовать предыдущие результаты при повторном запуске с теми же настройками. |

Следующий пример содержит шаг сценария Python:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        DataReference1:
            datastore: workspaceblobstore
            path_on_datastore: testfolder/sample.txt
    default_compute: cpu-cluster
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "PythonScriptStep"
            name: "MyPythonScriptStep"
            script_name: "train.py"
            allow_reuse: True
            source_directory: "D:\\scripts\\PythonScript"
            inputs:
                InputData:
                    source: DataReference1
            outputs:
                OutputData:
                    destination: Output4
                    datastore: workspaceblobstore
                    bind_mode: mount
```

### <a name="pipeline-with-multiple-steps"></a>Трубопровод с несколькими шагами 

| Ключ YAML | Описание |
| ----- | ----- |
| `steps` | Последовательность одного или нескольких определений PipelineStep. Обратите внимание, что `destination` один `outputs` шаг стал `inputs` ключом к .| 

```yaml
pipeline:
    name: SamplePipelineFromYAML
    description: Sample multistep YAML pipeline
    data_references:
        TitanicDS:
            dataset_name: 'titanic_ds'
            bind_mode: download
    default_compute: cpu-cluster
    steps:
        Dataprep:
            type: "PythonScriptStep"
            name: "DataPrep Step"
            compute: cpu-cluster
            runconfig: ".\\default_runconfig.yml"
            script_name: "prep.py"
            arguments:
            - '--train_path'
            - output:train_path
            - '--test_path'
            - output:test_path
            allow_reuse: True
            inputs:
                titanic_ds:
                    source: TitanicDS
                    bind_mode: download
            outputs:
                train_path:
                    destination: train_csv
                    datastore: workspaceblobstore
                test_path:
                    destination: test_csv
        Training:
            type: "PythonScriptStep"
            name: "Training Step"
            compute: cpu-cluster
            runconfig: ".\\default_runconfig.yml"
            script_name: "train.py"
            arguments:
            - "--train_path"
            - input:train_path
            - "--test_path"
            - input:test_path
            inputs:
                train_path:
                    source: train_csv
                    bind_mode: download
                test_path:
                    source: test_csv
                    bind_mode: download

```

## <a name="schedules"></a>Расписания

При определении расписания конвейера он может быть либо срабатывает в хранилище данных, либо повторяется на основе интервала времени. Ниже приведены ключи, используемые для определения расписания:

| Ключ YAML | Описание |
| ----- | ----- |
| `description` | Описание расписания. |
| `recurrence` | Содержит параметры повторения, если расписание повторяется. |
| `pipeline_parameters` | Любые параметры, которые требуются конвейеру. |
| `wait_for_provisioning` | Стоит ли ждать подготовки графика для завершения. |
| `wait_timeout` | Количество секунд, чтобы ждать до времени. |
| `datastore_name` | Хранилище данных для мониторинга модифицированных/добавленных капли. |
| `polling_interval` | Как долго, в минутах, между опросом для модифицированных / добавленных капли. Значение по умолчанию: 5 минут. Поддерживается только для расписаний хранилища данных. |
| `data_path_parameter_name` | Имя параметра конвейера пути передачи данных для установки с измененным траекторией blob. Поддерживается только для расписаний хранилища данных. |
| `continue_on_step_failure` | Продолжать ли выполнение других шагов в представленном PipelineRun в случае сбоя шага. Если это предусмотрено, `continue_on_step_failure` переопределяет настройку конвейера.
| `path_on_datastore` | Необязательный параметр. Путь в хранилище данных для мониторинга модифицированных/добавленных капли. Путь находится под контейнером для хранилища данных, поэтому фактическим`path_on_datastore`путем, который отслеживает расписание, является контейнер/ . Если этого нет, контейнер хранилища данных контролируется. Дополнения/модификации, внесенные в субфолдере, `path_on_datastore` не контролируются. Поддерживается только для расписаний хранилища данных. |

Следующий пример содержит определение для графика, инициированного хранилищем данных:

```yaml
Schedule: 
      description: "Test create with datastore" 
      recurrence: ~ 
      pipeline_parameters: {} 
      wait_for_provisioning: True 
      wait_timeout: 3600 
      datastore_name: "workspaceblobstore" 
      polling_interval: 5 
      data_path_parameter_name: "input_data" 
      continue_on_step_failure: None 
      path_on_datastore: "file/path" 
```

При **определении повторяюющегося расписания**используйте следующие клавиши под: `recurrence`

| Ключ YAML | Описание |
| ----- | ----- |
| `frequency` | Как часто повторяется расписание. `"Minute"`Действительные значения, `"Hour"` `"Day"` `"Week"`, , `"Month"`или . |
| `interval` | Как часто сражает расписание. Значение целых числа — это количество единиц времени, чтобы ждать, пока расписание снова не загорится. |
| `start_time` | Время начала расписания. Строка формата значения `YYYY-MM-DDThh:mm:ss`. Если время начала не предусмотрено, первая рабочая нагрузка заражается мгновенно, а будущие рабочие нагрузки — в зависимости от расписания. Если время начала в прошлом, первая рабочая нагрузка запущена в следующем расчетном времени выполнения. |
| `time_zone` | Часовой пояс на время начала. Если часовой пояс не предусмотрен, используется UTC. |
| `hours` | Если `frequency` `"Day"` есть `"Week"`или , вы можете указать один или несколько целых числа от 0 до 23, разделенных запятыми, как часы дня, когда трубопровод должен работать. Только `time_of_day` `hours` или `minutes` и может быть использован. |
| `minutes` | Если `frequency` `"Day"` есть `"Week"`или , вы можете указать один или несколько целых числа от 0 до 59, разделенных запятыми, как минуты часа, когда трубопровод должен работать. Только `time_of_day` `hours` или `minutes` и может быть использован. |
| `time_of_day` | Если `frequency` `"Day"` есть `"Week"`или, вы можете указать время суток для выполнения расписания. Строка формата значения `hh:mm`. Только `time_of_day` `hours` или `minutes` и может быть использован. |
| `week_days` | Если `frequency` `"Week"`это так, то можно указать один или несколько дней, разделенных запятыми, когда расписание должно быть запущено. Действительные `"Monday"`значения, `"Tuesday"` `"Wednesday"`, `"Thursday"` `"Friday"`, `"Saturday"`, `"Sunday"`, и . |

Следующий пример содержит определение для повторяющихся графиков:

```yaml
Schedule: 
    description: "Test create with recurrence" 
    recurrence: 
        frequency: Week # Can be "Minute", "Hour", "Day", "Week", or "Month". 
        interval: 1 # how often fires 
        start_time: 2019-06-07T10:50:00 
        time_zone: UTC 
        hours: 
        - 1 
        minutes: 
        - 0 
        time_of_day: null 
        week_days: 
        - Friday 
    pipeline_parameters: 
        'a': 1 
    wait_for_provisioning: True 
    wait_timeout: 3600 
    datastore_name: ~ 
    polling_interval: ~ 
    data_path_parameter_name: ~ 
    continue_on_step_failure: None 
    path_on_datastore: ~ 
```

## <a name="next-steps"></a>Следующие шаги

Узнайте, как [использовать расширение CLI для машинного обучения Azure.](reference-azure-machine-learning-cli.md)
