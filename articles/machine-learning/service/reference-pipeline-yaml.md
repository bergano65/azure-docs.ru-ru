---
title: YAML конвейера Машинное обучение
titleSuffix: Azure Machine Learning
description: Узнайте, как определить конвейер машинного обучения с помощью файла YAML. Определения конвейера YAML используются с расширением машинного обучения для Azure CLI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: sanpil
author: sanpil
ms.date: 10/15/2019
ms.openlocfilehash: a98a0e75c7a03baa663ccb4215e918a87bcc5df7
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72821779"
---
# <a name="define-machine-learning-pipelines-in-yaml"></a>Определение конвейеров машинного обучения в YAML

Узнайте, как определить конвейеры машинного обучения в [YAML](https://yaml.org/). При использовании расширения машинного обучения для Azure CLI многие команды, связанные с конвейером, предполагают файл YAML, определяющий конвейер.

В следующей таблице перечислены, что и сейчас не поддерживается при определении конвейера в YAML.

| Тип шага | Поддерживается? |
| ----- | :-----: |
| писонскриптстеп | ДА |
| адластеп | ДА |
| азуребатчстеп | ДА |
| датабрикксстеп | ДА |
| дататрансферстеп | ДА |
| аутомлстеп | Нет |
| хипердривестеп | Нет |
| модулестеп | Нет |
| мпистеп | Нет |
| естиматорстеп | Нет |

## <a name="pipeline-definition"></a>Определение конвейера

В определении конвейера используются следующие ключи, которые соответствуют классу [конвейеров](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py) :

| Ключ YAML | Описание |
| ----- | ----- |
| `name` | Описание конвейера. |
| `parameters` | Параметры для конвейера. |
| `data_reference` | Определяет, как и где следует предоставлять доступ к данным в запуске. |
| `default_compute` | Целевой объект вычислений по умолчанию, в котором выполняются все шаги в конвейере. |
| `steps` | Шаги, используемые в конвейере. |

Следующий YAML является примером определения конвейера:

## <a name="parameters"></a>Параметры

В разделе `parameters` используются следующие ключи, которые соответствуют классу [пипелинепараметер](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py) :

| Ключ YAML | Описание |
| ---- | ---- |
| `type` | Тип значения параметра. Допустимые типы: `string`, `int`, `float`, `bool`или `datapath`. |
| `default` | Значение по умолчанию. |

Каждый параметр имеет имя. Например, следующий фрагмент YAML определяет три параметра с именами `NumIterationsParameter`, `DataPathParameter`и `NodeCountParameter`:

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

## <a name="data-reference"></a>Справочник по данным

В разделе `data_references` используются следующие ключи, которые соответствуют [ссылке](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py)на объект.

| Ключ YAML | Описание |
| ----- | ----- |
| `datastore` | Хранилище данных для ссылки. |
| `path_on_datastore` | Относительный путь в резервном хранилище для ссылки на данные. |

Каждая ссылка на данные содержится в ключе. Например, следующий фрагмент YAML определяет ссылку на данные, хранимую в ключе с именем `employee_data`:

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

## <a name="steps"></a>Действия

Шаги определяют вычислительную среду, а также файлы для выполнения в среде. Определение YAML представляет следующие шаги:

| Ключ YAML | Описание |
| ----- | ----- |
| `adla_step` | Выполняет скрипт U-SQL с Azure Data Lake Analytics. Соответствует классу [адластеп](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.adlastep?view=azure-ml-py) . |
| `azurebatch_step` | Запускает задания с помощью пакетной службы Azure. Соответствует классу [азуребатчстеп](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep?view=azure-ml-py) . |
| `databricks_step` | Добавляет записную книжку, скрипт Python или JAR-файл. Соответствует классу [датабрикксстеп](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricksstep?view=azure-ml-py) . |
| `data_transfer_step` | Передает данные между вариантами хранения. Соответствует классу [дататрансферстеп](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py) . |
| `python_script_step` | Выполняет скрипт Python. Соответствует классу [писонскриптстеп](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) . |

### <a name="adla-step"></a>ADLA шаг

| Ключ YAML | Описание |
| ----- | ----- |
| `script_name` | Имя скрипта U-SQL (относительно `source_directory`). |
| `compute_target` | Целевой объект Azure Data Lake вычислений, используемый для этого шага. |
| `parameters` | [Параметры](#parameters) для конвейера. |
| `inputs` | Входными данными могут быть [инпутпортбиндинг](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [Reference](#data-reference), [портдатареференце](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [пипелинедата](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [датасетдефинитион](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)или [пипелинедатасет](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | Выходные данные могут быть либо [пипелинедата](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) , либо [аутпутпортбиндинг](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `source_directory` | Каталог, содержащий скрипт, сборки и т. д. |
| `priority` | Значение приоритета, используемое для текущего задания. |
| `params` | Словарь пар "имя-значение". |
| `degree_of_parallelism` | Степень параллелизма, используемая для этого задания. |
| `runtime_version` | Версия среды выполнения Data Lake Analytics Engine. |
| `allow_reuse` | Определяет, должен ли шаг повторно использовать предыдущие результаты при повторном выполнении с теми же параметрами. |

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
            runconfig: "yaml/default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            adla_step:
                name: "AdlaStep"
                script_name: "sample_script.usql"
                source_directory: "helloworld"
            inputs:
                employee_data:
                    source: employee_data
            outputs:
                OutputData:
                    destination: Output4
                    datastore: adftestadla
                    bind_mode: mount
```

### <a name="azure-batch-step"></a>Шаг пакетной службы Azure

| Ключ YAML | Описание |
| ----- | ----- |
| `compute_target` | Целевой объект вычислений пакетной службы Azure, который будет использоваться для этого шага. |
| `inputs` | Входными данными могут быть [инпутпортбиндинг](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [Reference](#data-reference), [портдатареференце](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [пипелинедата](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [датасетдефинитион](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)или [пипелинедатасет](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | Выходные данные могут быть либо [пипелинедата](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) , либо [аутпутпортбиндинг](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `source_directory` | Каталог, содержащий двоичные файлы модуля, исполняемый файл, сборки и т. д. |
| `executable` | Имя команды или исполняемого файла, которые будут запускаться в рамках этого задания. |
| `create_pool` | Логический флаг, указывающий, следует ли создать пул перед выполнением задания. |
| `delete_batch_job_after_finish` | Логический флаг, указывающий, следует ли удалить задание из учетной записи пакетной службы после ее завершения. |
| `delete_batch_pool_after_finish` | Логический флаг, указывающий, следует ли удалить пул после завершения задания. |
| `is_positive_exit_code_failure` | Логический флаг, указывающий, завершается ли задание ошибкой, если задача завершается с положительным кодом. |
| `vm_image_urn` | Если `create_pool` `True`, а виртуальная машина использует `VirtualMachineConfiguration`. |
| `pool_id` | Идентификатор пула, в котором будет выполняться задание. |
| `allow_reuse` | Определяет, должен ли шаг повторно использовать предыдущие результаты при повторном выполнении с теми же параметрами. |

В следующем примере содержится определение шага пакетной службы Azure.

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
            runconfig: "D:\\AzureMlCli\\cli_testing\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            azurebatch_step:
                name: "AzureBatchStep"
                pool_id: "MyPoolName"
                create_pool: true
                executable: "azurebatch.cmd"
                source_directory: "D:\\AzureMlCli\\cli_testing"
                allow_reuse: false
            inputs:
                input:
                    source: input
            outputs:
                output:
                    destination: output
                    datastore: workspaceblobstore
```

### <a name="databricks-step"></a>Шаг "кирпичы"

| Ключ YAML | Описание |
| ----- | ----- |
| `compute_target` | Целевой объект Azure Databricks вычислений, используемый для этого шага. |
| `inputs` | Входными данными могут быть [инпутпортбиндинг](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [Reference](#data-reference), [портдатареференце](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [пипелинедата](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [датасетдефинитион](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)или [пипелинедатасет](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | Выходные данные могут быть либо [пипелинедата](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) , либо [аутпутпортбиндинг](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `run_name` | Имя в модулях для этого запуска. |
| `source_directory` | Каталог, содержащий скрипт и другие файлы. |
| `num_workers` | Статическое число рабочих ролей для модуля данных, выполняющих кластер. |
| `runconfig` | Путь к файлу `.runconfig`. Этот файл является YAML представлением класса [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) . Дополнительные сведения о структуре этого файла см. в разделе [рунконфигсчема. JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Определяет, должен ли шаг повторно использовать предыдущие результаты при повторном выполнении с теми же параметрами. |

Следующий пример содержит шаг "кирпичы".

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
            runconfig: "D:\\AzureMlCli\\cli_testing\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            databricks_step:
                name: "Databrickstep"
                run_name: "DatabrickRun"
                python_script_name: "train-db-local.py"
                source_directory: "D:\\AzureMlCli\\cli_testing\\databricks_train"
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

### <a name="data-transfer-step"></a>Шаг пересылки данных

| Ключ YAML | Описание |
| ----- | ----- |
| `compute_target` | Целевой объект вычислений фабрики данных Azure, который будет использоваться для этого шага. |
| `source_data_reference` | Входное соединение, служащее источником операций по переносу данных. Поддерживаемые значения: [инпутпортбиндинг](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [Reference](#data-reference), [портдатареференце](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [пипелинедата](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [датасетдефинитион](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)или [пипелинедатасет](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `destination_data_reference` | Входное соединение, которое служит в качестве назначения для операций обмена данными. Поддерживаемые значения: [пипелинедата](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) и [аутпутпортбиндинг](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `allow_reuse` | Определяет, должен ли шаг повторно использовать предыдущие результаты при повторном выполнении с теми же параметрами. |

В следующем примере показан шаг перемещения данных.

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
            runconfig: "yaml/default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            data_transfer_step:
                name: "DataTransferStep"
                adla_compute_name: adftest
            source_data_reference:
                adls_test_data:
                    source: adls_test_data
            destination_data_reference:
                blob_test_data:
                    source: blob_test_data
```

### <a name="python-script-step"></a>Шаг скрипта Python

| Ключ YAML | Описание |
| ----- | ----- |
| `compute_target` | Целевой объект вычислений, который будет использоваться для этого шага. Целью вычислений может быть Машинное обучение Azure вычислительная виртуальная машина (например, виртуальная машина для обработки и анализа данных) или HDInsight. |
| `inputs` | Входными данными могут быть [инпутпортбиндинг](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py), [Reference](#data-reference), [портдатареференце](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py), [пипелинедата](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py), [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py), [датасетдефинитион](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)или [пипелинедатасет](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | Выходные данные могут быть либо [пипелинедата](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) , либо [аутпутпортбиндинг](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `script_name` | Имя скрипта Python (относительно `source_directory`). |
| `source_directory` | Каталог, содержащий скрипт, среду Conda и т. д. |
| `runconfig` | Путь к файлу `.runconfig`. Этот файл является YAML представлением класса [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) . Дополнительные сведения о структуре этого файла см. в разделе [runconfig. JSON](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Определяет, должен ли шаг повторно использовать предыдущие результаты при повторном выполнении с теми же параметрами. |

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
            runconfig: "yaml/default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            python_script_step:
                name: "PythonScriptStep"
                script_name: "train.py"
                allow_reuse: True
                source_directory: "helloworld"
            inputs:
                InputData:
                    source: DataReference1
            outputs:
                OutputData:
                    destination: Output4
                    datastore: workspaceblobstore
                    bind_mode: mount
```

## <a name="schedules"></a>Расписания

При определении расписания для конвейера это может быть либо хранилище данных, либо повторяющееся в зависимости от интервала времени. Ниже приведены ключи, используемые для определения расписания.

| Ключ YAML | Описание |
| ----- | ----- |
| `description` | Описание расписания. |
| `recurrence` | Содержит параметры повторения, если расписание повторяется. |
| `pipeline_parameters` | Все параметры, необходимые для конвейера. |
| `wait_for_provisioning` | Следует ли ожидать завершения подготовки расписания. |
| `wait_timeout` | Количество секунд ожидания до истечения времени ожидания. |
| `datastore_name` | Хранилище данных для отслеживания измененных или добавленных больших двоичных объектов. |
| `polling_interval` | Время в минутах между опросами измененных или добавленных больших двоичных объектов. Значение по умолчанию: 5 минут. Поддерживается только для расписаний хранилища данных. |
| `data_path_parameter_name` | Имя параметра конвейера пути к данным для задания с измененным путем к большому двоичному объекту. Поддерживается только для расписаний хранилища данных. |
| `continue_on_step_failure` | Следует ли продолжать выполнение других шагов в отправленном Пипелинерун, если шаг завершается ошибкой. Если он предоставлен, переопределит параметр `continue_on_step_failure` конвейера.
| `path_on_datastore` | Необязательный элемент. Путь в хранилище данных для отслеживания измененных или добавленных больших двоичных объектов. Путь находится под контейнером для хранилища данных, поэтому фактический путь, к которому мониторы расписаний, — контейнер/`path_on_datastore`. Если нет, отслеживается контейнер хранилища данных. Добавления и изменения, внесенные во вложенную папку `path_on_datastore`, не отслеживаются. Поддерживается только для расписаний хранилища данных. |

Следующий пример содержит определение расписания, запускаемого хранилищем данных:

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

При определении **повторяющегося расписания**используйте следующие ключи в разделе `recurrence`.

| Ключ YAML | Описание |
| ----- | ----- |
| `frequency` | Частота повторения расписания. Допустимые значения: `"Minute"`, `"Hour"`, `"Day"`, `"Week"`или `"Month"`. |
| `interval` | Частота срабатывания расписания. Целочисленное значение — это количество единиц времени, которое нужно ожидать до повторного запуска расписания. |
| `start_time` | Время начала расписания. Формат строки значения — `YYYY-MM-DDThh:mm:ss`. Если время начала не указано, первая рабочая нагрузка выполняется мгновенно, а будущие рабочие нагрузки выполняются на основе расписания. Если время начала в прошлом, то первая рабочая нагрузка будет выполняться при следующем расчете времени выполнения. |
| `time_zone` | Часовой пояс для времени начала. Если часовой пояс не указан, используется время в формате UTC. |
| `hours` | Если `frequency` имеет `"Day"` или `"Week"`, можно указать одно или несколько целых чисел от 0 до 23, разделенных запятыми, как часы дня, когда конвейер должен выполняться. Можно использовать только `time_of_day`, `hours` и `minutes`. |
| `minutes` | Если `frequency` имеет `"Day"` или `"Week"`, можно указать одно или несколько целых чисел от 0 до 59, разделенных запятыми, как минуты часа, когда должен выполняться конвейер. Можно использовать только `time_of_day`, `hours` и `minutes`. |
| `time_of_day` | Если `frequency` имеет `"Day"` или `"Week"`, можно указать время суток для выполнения расписания. Формат строки значения — `hh:mm`. Можно использовать только `time_of_day`, `hours` и `minutes`. |ззс
| `week_days` | Если `frequency` `"Week"`, можно указать один или несколько дней, разделенных запятыми, при запуске расписания. Допустимые значения: `"Monday"`, `"Tuesday"`, `"Wednesday"`, `"Thursday"`, `"Friday"`, `"Saturday"`и `"Sunday"`. |

Следующий пример содержит определение расписания повторения:

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

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как [использовать расширение CLI для машинное обучение Azure](reference-azure-machine-learning-cli.md).