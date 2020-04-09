---
title: Создание, запуск и отслеживание конвейеров машинного обучения
titleSuffix: Azure Machine Learning
description: Создание и запуск конвейера машинного обучения с помощью пакета SDK Машинного обучения Azure для Python. Используйте провода ML для создания и управления рабочими процессами, которые сшивают этапы машинного обучения (ML). Эти этапы включают подготовку данных, обучение модели, развертывание моделей и выводы/оценки.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 12/05/2019
ms.custom: seodec18
ms.openlocfilehash: d175a2cea685585da3767acdb0ab77a99c541d09
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80873877"
---
# <a name="create-and-run-machine-learning-pipelines-with-azure-machine-learning-sdk"></a>Создание и запуск конвейеров машинного обучения с помощью SDK машинного обучения Azure

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье вы узнаете, как создать, опубликовать, запустить и отслеживать [конвейер машинного обучения](concept-ml-pipelines.md) с помощью [пакета SDK для машинного обучения Azure](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).  Используйте **конвейеры ML** для создания рабочего процесса, который сшивает различные фазы ML, а затем опубликуйте этот конвейер в рабочее пространство Azure Machine Learning, чтобы получить доступ позже или поделиться с другими.  Конвейеры ML идеально подходят для сценариев пакетного скоринга, используя различные вычисления, повторно использовать шаги вместо их повторного выполнения, а также совместного использования рабочих процессов ML с другими.

В то время как вы можете использовать другой тип конвейера, называемый [Azure Pipeline](https://docs.microsoft.com/azure/devops/pipelines/targets/azure-machine-learning?context=azure%2Fmachine-learning%2Fservice%2Fcontext%2Fml-context&view=azure-devops&tabs=yaml) для автоматизации задач CI/CD ML, этот тип конвейера никогда не хранится в вашем рабочем пространстве. [Сравните эти различные трубопроводы.](concept-ml-pipelines.md#which-azure-pipeline-technology-should-i-use)

Каждый этап конвейера ML, например подготовка данных и обучение модели, может включать один или несколько этапов.

Создаваемые конвейеры ML видны членам [рабочего пространства](how-to-manage-workspace.md)Azure Machine Learning. 

Провода ML используют удаленные вычислительные цели для вычислений и хранения промежуточных и конечных данных, связанных с этим конвейером. Они могут читать и записывать данные в и из поддерживаемых мест [хранения Azure.](https://docs.microsoft.com/azure/storage/)

Если у вас еще нет подписки Azure, создайте бесплатную учетную запись, прежде чем начинать работу. Попробуйте [бесплатную или платную версию машинного обучения Azure.](https://aka.ms/AMLFree)

## <a name="prerequisites"></a>Предварительные требования

* Создайте [рабочую область Машинного обучения Azure](how-to-manage-workspace.md) для хранения всех ресурсов конвейера.

* [Настройте среду разработки](how-to-configure-environment.md) для установки SDK для машинного обучения Azure или используйте [экземпляр вычислений для машин Azure Machine Learning (предварительный просмотр)](concept-compute-instance.md) с уже установленным SDK.

Начните с крепления рабочего пространства:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```

## <a name="set-up-machine-learning-resources"></a>Настройка ресурсов машинного обучения

Создание ресурсов, необходимых для запуска конвейера ML:

* Настройте хранилище данных для хранения данных, необходимых для выполнения шагов конвейера.

* Наверстуна объект, `Dataset` чтобы указать на постоянные данные, которые живут или доступны в хранилище данных. Наверстойобъект `PipelineData` для временных данных, передаваемых между шагами конвейера. 

* Настройте [целевые объекты вычислений](concept-azure-machine-learning-architecture.md#compute-targets) для выполнения шагов вашего конвейера.

### <a name="set-up-a-datastore"></a>Настройка хранилища данных

Хранилище данных содержит данные, которые использует конвейер. У каждой рабочей области есть хранилище данных по умолчанию. Вы можете зарегистрировать дополнительные хранилища данных. 

При создании рабочего пространства к рабочему пространству прикрепляются [файлы Azure и](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) [хранилище Azure Blob.](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) Для подключения к хранилищу Azure Blob зарегистрирован хранилище данных по умолчанию. Дополнительные сведения см. в статье [Выбор между большими двоичными объектами Azure, службой файлов Azure и дисками Azure](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks). 

```python
# Default datastore 
def_data_store = ws.get_default_datastore()

# Get the blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")

# Get file storage associated with the workspace
def_file_store = Datastore(ws, "workspacefilestore")

```

Передайте файлы данных или каталоги в хранилище данных, чтобы они были доступны из конвейеров. Этот пример использует хранилище Blob в качестве хранилища данных:

```python
def_blob_store.upload_files(
    ["./data/20news.pkl"],
    target_path="20newsgroups",
    overwrite=True)
```

Конвейер состоит из одного или нескольких шагов. Шаг — это единица вычислений, выполняемая на целевом объекте вычислений. Шаги могут потреблять источники данных и создавать "промежуточные" данные. Шаг может создать данные, такие как модель, каталог с моделью и зависимыми файлами или временные данные. Эти данные становятся доступными для последующих шагов в конвейере.

Чтобы узнать больше о подключении конвейера к данным, смотрите статьи [Как получить доступ к данным](how-to-access-data.md) и как зарегистрировать [наборы данных.](how-to-create-register-datasets.md) 

### <a name="configure-data-using-dataset-and-pipelinedata-objects"></a>Настройка данных с использованием `Dataset` и `PipelineData` объектов

Вы только что создали источник данных, который можно указать в конвейере в качестве входных данных шага. Предпочтительным способом передачи данных конвейеру является объект [набора данных.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.Dataset) Объект `Dataset` указывает на данные, которые живут или доступны в хранилище данных или в Веб-УРЛ. Класс `Dataset` является абстрактным, так что `FileDataset` вы создадите экземпляр либо (ссылаясь на один или несколько файлов) `TabularDataset` или, который создан из одного или нескольких файлов с делимитированных столбцов данных.

`Dataset`объекты поддерживают версии, диффы и сводные статистические данные. `Dataset`s лениво оцениваются (например, генераторы Python), и это эффективно подмножество их путем расщепления или фильтрации. 

Вы создаете `Dataset` такие методы, как [from_file](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) или [from_delimited_files.](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false-)

```python
from azureml.core import Dataset

iris_tabular_dataset = Dataset.Tabular.from_delimited_files([(def_blob_store, 'train-dataset/tabular/iris.csv')])
```

Промежуточные данные (или выходные данные шага) представляет объект [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py). `output_data1` создается как выходные данные шага и используется в качестве входных данных одного шага или нескольких последующих шагов. `PipelineData` представляет зависимость данных между шагами и неявно определяет порядок выполнения шагов в конвейере. Этот объект будет использоваться позже при создании шагов конвейера.

```python
from azureml.pipeline.core import PipelineData

output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

Более подробную информацию и примерный код для работы с наборами данных и данными конвейера можно найти в [перемещении данных в этапы конвейера ML (Python).](how-to-move-data-in-out-of-pipelines.md)

## <a name="set-up-a-compute-target"></a>Настройка целевой среды для вычислений

В машинном обучении Azure __вычислительной средой__ (или __целевым объектом вычислений__) считаются компьютеры или кластеры, которые выполняют вычислительные операции конвейера машинного обучения.   См. раздел [Настройка целевых объектов вычислений для обучения моделей](how-to-set-up-training-targets.md), чтобы получить полный список целевых объектов вычислений и научиться создавать и присоединять их к своей рабочей области.  Процесс создания и присоединения целевого объекта вычислений аналогичен независимо от того, тренируете ли вы модель или выполняете шаг конвейера. После того как вы создадите и присоедините свой целевой объект вычислений, используйте объект `ComputeTarget` на [шаге конвейера](#steps).

> [!IMPORTANT]
> Выполнение операций управления на целевых объектах вычислений не поддерживается внутри удаленных заданий. Так как конвейеры машинного обучения передаются в качестве удаленного задания, не используйте операции управления на целевых объектах вычислений внутри конвейера.

Ниже приведены примеры создания и присоединения целевых объектов вычислений для таких служб.

* Вычислительная среда Машинного обучения Azure;
* Azure Databricks 
* Аналитика озера данных Azure

### <a name="azure-machine-learning-compute"></a>Вычислительная среда Машинного обучения Azure

Для выполнения шагов конвейера можно создать вычислительную среду Машинного обучения Azure.

```python
from azureml.core.compute import ComputeTarget, AmlCompute

compute_name = "aml-compute"
vm_size = "STANDARD_NC6"
if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target: ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size=vm_size,  # STANDARD_NC6 is GPU-enabled
                                                                min_nodes=0,
                                                                max_nodes=4)
    # create the compute target
    compute_target = ComputeTarget.create(
        ws, compute_name, provisioning_config)

    # Can poll for a minimum number of nodes and for a specific timeout.
    # If no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # For a more detailed view of current cluster status, use the 'status' property
    print(compute_target.status.serialize())
```

### <a name="azure-databricks"></a><a id="databricks"></a>Azure Databricks

Azure Databricks — это среда, которая лежит в основе Apache Spark в облаке Azure. Ее можно использовать как целевой объект вычислений с помощью конвейера Машинного обучения Azure.

Прежде чем ее использовать, создайте рабочую область Azure Databricks. Для создания ресурса рабочего [Run a Spark job on Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) пространства см.

Введите следующие данные, чтобы вложить Azure Databricks в качестве целевого объекта вычислений.

* __Databricks вычислить имя__: имя, которое вы хотите назначить этому вычислительному ресурсу.
* __Имя рабочего пространства Databricks__: Название рабочего пространства Azure Databricks.
* __Доступ к токену Databricks:__ токен доступа, используемый для проверки подлинности для Azure Databricks. Чтобы создать маркер доступа, см. документ [Проверка подлинности](https://docs.azuredatabricks.net/dev-tools/api/latest/authentication.html).

Следующий код демонстрирует, как прикрепить Azure Databricks в качестве вычислительной цели с Помощью SDK По спомощьи Azure Machine Learning __(рабочее пространство Databricks должно присутствовать в__той же подписке, что и рабочее пространство AML):

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

Для более подробного примера смотрите [пример ноутбука](https://aka.ms/pl-databricks) на GitHub.

### <a name="azure-data-lake-analytics"></a><a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics — это платформа аналитики больших данных в облаке Azure. Ее можно использовать как целевой объект вычислений с помощью конвейера Машинного обучения Azure.

Прежде чем ее использовать, создайте учетную запись Azure Data Lake Analytics. Чтобы создать этот ресурс, см. статью [Начало работы с Azure Data Lake Analytics с помощью портала Azure](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal).

Чтобы вложить Azure Data Lake Analytics в качестве целевого объекта вычислений, используйте пакет SDK машинного обучения Azure и укажите следующие сведения.

* __Имя вычисления__. Имя, которое вы хотите назначить как вычислительный ресурс.
* __Группа ресурсов__: Группа ресурсов, содержащая учетную запись Data Lake Analytics.
* __Имя учетной записи__: Имя учетной записи Data Lake Analytics.

Следующий код демонстрирует, как вложить Data Lake Analytics в качестве целевого объекта вычислений.

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

Для более подробного примера смотрите [пример ноутбука](https://aka.ms/pl-adla) на GitHub.

> [!TIP]
> Конвейеры машинного обучения Azure работают только с хранимыми данными в хранилище данных учетной записи Data Lake Analytics по умолчанию. Если данные, с которые необходимо работать, хранятся в [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) магазине без дефолта, можно скопировать данные перед тренировкой.

## <a name="construct-your-pipeline-steps"></a><a id="steps"></a>Создание шагов конвейера

После создания и присоединения целевого объекта вычисления к вашей рабочей области все готово для определения шага конвейера. В пакете SDK для Машинного обучения Azure доступно много встроенных шагов. Наиболее основным из этих шагов является [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py), который запускает сценарий Python в указанной цели вычислений:

```python
from azureml.pipeline.steps import PythonScriptStep

ds_input = my_dataset.as_named_input('input1')

trainStep = PythonScriptStep(
    script_name="train.py",
    arguments=["--input", ds_input.as_download(), "--output", output_data1],
    inputs=[ds_input],
    outputs=[output_data1],
    compute_target=compute_target,
    source_directory=project_folder,
    allow_reuse=True
)
```

Повторное использование предыдущих результатов ()`allow_reuse`является ключевым при использовании конвейеров в совместной среде, так как устранение ненужных повторов обеспечивает гибкость. Повторное использование — это поведение по умолчанию, когда script_name, входы и параметры шага остаются прежними. При повторном использовании вывода шага задание не передается вычислению, вместо этого результаты предыдущего запуска сразу же доступны для выполнения следующего шага. Если `allow_reuse` настроен на ложную, для этого шага во время выполнения конвейера всегда будет создан новый запуск. 

После определения шагов следует создать конвейер, добавив в него некоторые созданные шаги (или все).

> [!NOTE]
> При определении этапов или построении конвейера файл или данные не загружаются в Azure Machine Learning.

```python
# list of steps to run
compareModels = [trainStep, extractStep, compareStep]

from azureml.pipeline.core import Pipeline

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compareModels])
```

В следующем примере используется созданный ранее целевой объект вычислений Azure Databricks. 

```python
from azureml.pipeline.steps import DatabricksStep

dbStep = DatabricksStep(
    name="databricksmodule",
    inputs=[step_1_input],
    outputs=[step_1_output],
    num_workers=1,
    notebook_path=notebook_path,
    notebook_params={'myparam': 'testparam'},
    run_name='demo run name',
    compute_target=databricks_compute,
    allow_reuse=False
)
# List of steps to run
steps = [dbStep]

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=steps)
```

### <a name="use-a-dataset"></a>Использование набора данных 

Наборы данных, созданные из хранилища Azure Blob, Файлов Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, базы данных Azure S'L и базы данных Azure для PostgreS'L, могут использоваться в качестве ввода для любого шага конвейера. За исключением записи вывода в [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py) или [DatabricksStep,](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py)выходные данные[(PipelineData)](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)могут быть записаны только в хранилища данных обмена файлами Azure Blob и Azure File.

```python
dataset_consuming_step = PythonScriptStep(
    script_name="iris_train.py",
    inputs=[iris_tabular_dataset.as_named_input("iris_data")],
    compute_target=compute_target,
    source_directory=project_folder
)
```

Затем вы получаете набор данных в конвейере с помощью словаря [Run.input_datasets.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#input-datasets)

```python
# iris_train.py
from azureml.core import Run, Dataset

run_context = Run.get_context()
iris_dataset = run_context.input_datasets['iris_data']
dataframe = iris_dataset.to_pandas_dataframe()
```

Линия `Run.get_context()` стоит выделить. Эта функция извлекает `Run` представление текущего экспериментального запуска. В приведенной выше примере мы используем его для получения зарегистрированного набора данных. Другим распространенным использованием `Run` объекта является получение как самого эксперимента, так и рабочего пространства, в котором находится эксперимент: 

```python
# Within a PythonScriptStep

ws = Run.get_context().experiment.workspace
```

Для получения более подробной информации, включая альтернативные способы передачи и доступа к данным, [см. Перемещение данных в и между шагами конвейера ML (Python)](how-to-move-data-in-out-of-pipelines.md).

## <a name="submit-the-pipeline"></a>Отправка конвейера

При отправке конвейера Azure Machine Learning проверяет зависимости для каждого шага и загружает моментальный снимок указанного исходного каталога. Если исходный каталог не указан, передается текущий локальный каталог. Снимок также сохраняется в рамках эксперимента в рабочей области.

> [!IMPORTANT]
> Чтобы предотвратить включение файлов в моментальный снимок, создайте в каталоге [файл .gitignore](https://git-scm.com/docs/gitignore) или `.amlignore` файл и добавьте файлы в него. Файл `.amlignore` использует тот же синтаксис и шаблоны, что и файл [.gitignore.](https://git-scm.com/docs/gitignore) Если оба файла `.amlignore` существуют, файл имеет приоритет.
>
> Дополнительные сведения см. в разделе о [моментальных снимках](concept-azure-machine-learning-architecture.md#snapshots).

```python
from azureml.core import Experiment

# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
pipeline_run1.wait_for_completion()
```

При первом запуске конвейера служба машинного обучения Azure выполняет следующие действия.

* Скачивает моментальный снимок проекта из хранилища BLOB-объектов, связанного с рабочей областью, на целевой объект вычислений.
* Создает образ Docker, соответствующий каждому шагу в конвейере.
* Загружает изображение Docker для каждого шага в цель вычислений из реестра контейнеров.
* Настраивает доступ `Dataset` `PipelineData` к объектам и объектам. Для `as_mount()` режима доступа, FUSE используется для обеспечения виртуального доступа. Если горе не поддерживается или если `as_download()`пользователь указал доступ как, данные вместо этого копируются в цель вычисления.
* Выполняет шаг на целевом объекте вычислений, указанном в определении этого шага. 
* Создает артефакты (журналы, stdout и stderr, метрики и выходные данные), указанные в шаге. Затем эти артефакты загружаются и хранятся в хранилище данных пользователя по умолчанию.

![Схема запуска эксперимента в виде конвейера](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

Для получения дополнительной [информации](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) см.

### <a name="view-results-of-a-pipeline"></a>Просмотр результатов конвейера

Смотрите список всех ваших конвейеров и их данные о пробеге в студии:

1. Войдите в [Студию машинного обучения Azure](https://ml.azure.com).

1. [Просмотр рабочего пространства](how-to-manage-workspace.md#view).

1. Слева выберите **Трубопроводы,** чтобы увидеть все запуски конвейера.
 ![Список конвейеров машинного обучения](./media/how-to-create-your-first-pipeline/pipelines.png)
 
1. Выберите конкретный конвейер, чтобы просмотреть результаты его запуска.

## <a name="git-tracking-and-integration"></a>Отслеживание и интеграция Git

При запуске обучаемого запуска, в котором исходный каталог является локальным репозиторием Git, информация о репозитории хранится в истории выполнения. Для получения дополнительной информации см. [интеграция Git для машинного обучения Azure.](concept-train-model-git-integration.md)

## <a name="publish-a-pipeline"></a>Публикация конвейера

Вы можете опубликовать конвейера для последующего запуска с другими входными данными. Чтобы конечная точка REST опубликованного конвейера могла принимать параметры, перед публикацией конвейер нужно параметризовать.

1. Чтобы создать параметр конвейера, используйте объект [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) со значением по умолчанию.

   ```python
   from azureml.pipeline.core.graph import PipelineParameter
   
   pipeline_param = PipelineParameter(
     name="pipeline_arg",
     default_value=10)
   ```

2. Добавьте этот объект `PipelineParameter` в качестве параметра в любой из шагов в конвейере следующим образом.

   ```python
   compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],
     compute_target=compute_target,
     source_directory=project_folder)
   ```

3. Опубликуйте этот конвейер, который будет принимать параметр при вызове.

   ```python
   published_pipeline1 = pipeline_run1.publish_pipeline(
        name="My_Published_Pipeline",
        description="My Published Pipeline Description",
        version="1.0")
   ```

### <a name="run-a-published-pipeline"></a>Запустите опубликованный конвейер.

Все опубликованные конвейеры используют конечную точку REST. Она вызывает запуск конвейера из внешних систем, таких как клиенты не на платформе Python. Эта конечная точка обеспечивает управляемую повторяемость для сценариев пакетной оценки и переобучения.

Для того чтобы вызвать запуск предыдущего конвейера, необходимо создать маркер заголовка заголовок проверки подлинности Azure Active Directory, описанный в [ссылке класса AzureCliAuthentication,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py) или получить более подробную информацию [в блокноте Azure Machine Learning.](https://aka.ms/pl-restep-auth)

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

## <a name="create-a-versioned-pipeline-endpoint"></a>Создание конечная точка конвейера версий

Можно создать конечную точку трубопровода с несколькими опубликованными трубопроводами позади нее. Это может быть использовано как опубликованный конвейер, но дает вам фиксированную конечную точку REST, как вы итерировать и обновлять ваши провода ML.

```python
from azureml.pipeline.core import PipelineEndpoint

published_pipeline = PublishedPipeline.get(workspace="ws", name="My_Published_Pipeline")
pipeline_endpoint = PipelineEndpoint.publish(workspace=ws, name="PipelineEndpointTest",
                                            pipeline=published_pipeline, description="Test description Notebook")
```

### <a name="submit-a-job-to-a-pipeline-endpoint"></a>Отправить задание в конечную точку конвейера

Задание можно отправить в версию конечной точки конвейера по умолчанию:

```python
pipeline_endpoint_by_name = PipelineEndpoint.get(workspace=ws, name="PipelineEndpointTest")
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment")
print(run_id)
```

Вы также можете отправить задание в конкретную версию:

```python
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment", pipeline_version="0")
print(run_id)
```

То же самое можно сделать с помощью REST API:

```python
rest_endpoint = pipeline_endpoint_by_name.endpoint
response = requests.post(rest_endpoint, 
                         headers=aad_token, 
                         json={"ExperimentName": "PipelineEndpointExperiment",
                               "RunSource": "API",
                               "ParameterAssignments": {"1": "united", "2":"city"}})
```

### <a name="use-published-pipelines-in-the-studio"></a>Использование опубликованных конвейеров в студии

Вы также можете запустить опубликованный конвейер из студии:

1. Войдите в [Студию машинного обучения Azure](https://ml.azure.com).

1. [Просмотр рабочего пространства](how-to-manage-workspace.md#view).

1. Слева выберите **конечные точки.**

1. Сверху выберите **конечные точки трубопровода.**
 ![список опубликованных конвейеров машинного обучения](./media/how-to-create-your-first-pipeline/pipeline-endpoints.png)

1. Выберите конкретный конвейер для запуска, потребления или просмотрите результаты предыдущих запусков конечной точки конвейера.

### <a name="disable-a-published-pipeline"></a>Отключить опубликованный конвейер

Чтобы скрыть конвейер из списка опубликованных трубопроводов, вы откажете его, либо в студии, либо из SDK:

```python
# Get the pipeline by using its ID from Azure Machine Learning studio
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

Вы можете включить `p.enable()`его снова с . Для получения дополнительной [PublishedPipeline class](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline?view=azure-ml-py) информации см.

## <a name="caching--reuse"></a>Повторное использование кэшинга &  

Для оптимизации и настройки поведения конвейеров можно сделать несколько вещей, которые можно использовать и повторно использовать. Например, можно выбрать:
+ **Выключите повторное использование вывода шага,** установив `allow_reuse=False` его во время [определения шага.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) Повторное использование является ключевым при использовании конвейеров в среде совместной работы, так как устранение ненужных запусков обеспечивает гибкость. Тем не менее, вы можете отказаться от повторного использования.
+ **Регенерация вывода силы для всех шагов в беге** с`pipeline_run = exp.submit(pipeline, regenerate_outputs=False)`

По умолчанию `allow_reuse` для шагов `source_directory` включено и указанное в определении шага хэшируется. Таким образом, если скрипт для данного шага остается неизменным (,`script_name`входы` source_directory` и параметры), и ничего другого в измененном, выход предыдущего запуска шага используется повторно, задание не представлено вычислению, и результаты предыдущего запуска немедленно доступны для следующего шага вместо этого.

```python
step = PythonScriptStep(name="Hello World",
                        script_name="hello_world.py",
                        compute_target=aml_compute,
                        source_directory=source_directory,
                        allow_reuse=False,
                        hash_paths=['hello_world.ipynb'])
```

## <a name="next-steps"></a>Дальнейшие действия

- Используйте [эти записные книжки Jupyter на сайте GitHub](https://aka.ms/aml-pipeline-readme), чтобы подробнее изучить конвейеры машинного обучения.
- Ознакомьтесь с справочной справкой SDK для пакета [azureml-pipelines-core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) и пакета [шагов лазурных конвейеров.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)
- [Ознакомьтесь](how-to-debug-pipelines.md) с советами по отладке и устранению неполадок.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]
