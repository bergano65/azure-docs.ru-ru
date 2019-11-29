---
title: Создание, запуск и отслеживание конвейеров машинного обучения
titleSuffix: Azure Machine Learning
description: Создание и запуск конвейера машинного обучения с помощью пакета SDK Машинного обучения Azure для Python. Используйте конвейеры машинного обучения для создания рабочих процессов и управления ими, которые объединяют фазы машинного обучения (ML). Эти этапы включают подготовку данных, обучение модели, развертывание модели и выведение/оценка.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 11/12/2019
ms.custom: seodec18
ms.openlocfilehash: 329fa301917fec368b0e76ab970d8ece72aa66c5
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561408"
---
# <a name="create-and-run-machine-learning-pipelines-with-azure-machine-learning-sdk"></a>Создание и запуск конвейеров машинного обучения с помощью пакета SDK для Машинное обучение Azure
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье вы узнаете, как создать, опубликовать, запустить и отслеживать [конвейер машинного обучения](concept-ml-pipelines.md) с помощью [пакета SDK для машинного обучения Azure](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).  Используйте **конвейеры машинного обучения** , чтобы создать рабочий процесс, объединяющий различные фазы машинного обучения, а затем опубликовать этот конвейер в машинное обучение Azure рабочей области, чтобы получить доступ к ним позже или поделиться с другими пользователями.  Конвейеры машинного обучения идеально подходят для сценариев пакетной оценки с использованием различных вычислений, повторного использования действий вместо их перезапуска, а также совместного использования рабочих процессов машинного обучения с другими пользователями.

Хотя можно использовать другой тип конвейера, называемый [конвейером Azure](https://docs.microsoft.com/azure/devops/pipelines/targets/azure-machine-learning?context=azure%2Fmachine-learning%2Fservice%2Fcontext%2Fml-context&view=azure-devops&tabs=yaml) для автоматизации задач ML в CI/CD, этот тип конвейера никогда не хранится в рабочей области. [Сравните эти разные конвейеры](concept-ml-pipelines.md#which-azure-pipeline-technology-should-i-use).

Каждый этап конвейера МАШИНного обучения, например подготовка данных и обучение модели, может включать один или несколько шагов.

Создаваемые конвейеры машинного обучения видимы для членов [рабочей области](how-to-manage-workspace.md)машинное обучение Azure. 

Конвейеры машинного обучения используют удаленные целевые объекты вычислений для вычислений и хранения промежуточных и окончательных данных, связанных с этим конвейером. Они могут читать и записывать данные в поддерживаемые расположения [хранилища Azure](https://docs.microsoft.com/azure/storage/) и из них.

Если у вас еще нет подписки Azure, создайте бесплатную учетную запись, прежде чем начинать работу. Попробуйте [бесплатную или платную версию машинное обучение Azure](https://aka.ms/AMLFree).

## <a name="prerequisites"></a>Технические условия

* Создайте [рабочую область Машинного обучения Azure](how-to-manage-workspace.md) для хранения всех ресурсов конвейера.

* [Настройте среду разработки](how-to-configure-environment.md) , чтобы установить пакет sdk для машинное обучение Azure, или используйте [виртуальную машину машинное обучение Azure Notebook](concept-azure-machine-learning-architecture.md#compute-instance) с уже установленным пакетом SDK.

Начните с подключения рабочей области:

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```


## <a name="set-up-machine-learning-resources"></a>Настройка ресурсов машинного обучения

Создайте ресурсы, необходимые для запуска конвейера машинного обучения.

* Настройте хранилище данных для хранения данных, необходимых для выполнения шагов конвейера.

* Настройте объект `DataReference`, чтобы указать данные, которые хранятся или доступны в хранилище данных.

* Настройте [целевые объекты вычислений](concept-azure-machine-learning-architecture.md#compute-targets) для выполнения шагов вашего конвейера.

### <a name="set-up-a-datastore"></a>Настройка хранилища данных

Хранилище данных содержит данные, которые использует конвейер. У каждой рабочей области есть хранилище данных по умолчанию. Вы можете зарегистрировать дополнительные хранилища данных. 

При создании рабочей области [файлы Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) и [хранилище BLOB-объектов Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) присоединяются к рабочей области. Для подключения к хранилищу BLOB-объектов Azure регистрируется хранилище данных по умолчанию. Дополнительные сведения см. в статье [Выбор между большими двоичными объектами Azure, службой файлов Azure и дисками Azure](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks). 

```python
# Default datastore 
def_data_store = ws.get_default_datastore()

# Get the blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")

# Get file storage associated with the workspace
def_file_store = Datastore(ws, "workspacefilestore")

```

Передайте файлы данных или каталоги в хранилище данных, чтобы они были доступны из конвейеров. В этом примере используется хранилище BLOB-объектов в качестве хранилища данных:

```python
def_blob_store.upload_files(
    ["./data/20news.pkl"],
    target_path="20newsgroups",
    overwrite=True)
```

Конвейер состоит из одного или нескольких шагов. Шаг — это единица вычислений, выполняемая на целевом объекте вычислений. Шаги могут использовать источники данных и создавать "промежуточные" данные. Шаг может создать данные, такие как модель, каталог с моделью и зависимыми файлами или временные данные. Эти данные становятся доступными для последующих шагов в конвейере.

### <a name="configure-data-reference"></a>Настройка ссылки на данные

Вы только что создали источник данных, который можно указать в конвейере в качестве входных данных шага. Источник данных в конвейере представлен объектом [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference). Объект `DataReference` указывает на данные, которые хранятся или доступны в хранилище данных.

```python
from azureml.data.data_reference import DataReference

blob_input_data = DataReference(
    datastore=def_blob_store,
    data_reference_name="test_data",
    path_on_datastore="20newsgroups/20news.pkl")
```

Промежуточные данные (или выходные данные шага) представляет объект [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py). `output_data1` создается как выходные данные шага и используется в качестве входных данных одного шага или нескольких последующих шагов. `PipelineData` представляет зависимость данных между шагами и неявно определяет порядок выполнения шагов в конвейере. Этот объект будет использоваться позже при создании шагов конвейера.

```python
from azureml.pipeline.core import PipelineData

output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

### <a name="configure-data-using-datasets"></a>Настроить данные с помощью наборов данных

Если в файле или наборе файлов хранятся табличные данные, [табулардатасет](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) является эффективной альтернативой `DataReference`. `TabularDataset` объекты поддерживают управление версиями, различия и сводную статистику. `TabularDataset`ы обрабатываются отложенным образом (например, генераторы Python), и их можно подделать подмножествами путем разбиения или фильтрации. Класс `FileDataset` предоставляет аналогичные вычисляемые данные с отложенной назначением, представляющие один или несколько файлов. 

`TabularDataset` создается с помощью таких методов, как [from_delimited_files](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none-).

```python
from azureml.data import TabularDataset

iris_tabular_dataset = Dataset.Tabular.from_delimited_files([(def_blob_store, 'train-dataset/tabular/iris.csv')])
```

 `FileDataset` создается с помощью [from_files](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-).

 Дополнительные сведения о работе с наборами данных см. в статье [добавление & регистрация наборов данных](how-to-create-register-datasets.md) или [этого примера записной книжки](https://aka.ms/tabulardataset-samplenotebook).

## <a name="set-up-compute-target"></a>Настройка целевого объекта для вычислений

В Машинное обучение Azure термин __вычисления__ (или __целевое значение вычислений__) относится к компьютерам или кластерам, которые выполняют вычислительные шаги в конвейере машинного обучения.   См. раздел [Настройка целевых объектов вычислений для обучения моделей](how-to-set-up-training-targets.md), чтобы получить полный список целевых объектов вычислений и научиться создавать и присоединять их к своей рабочей области.  Процесс создания и присоединения целевого объекта вычислений аналогичен независимо от того, тренируете ли вы модель или выполняете шаг конвейера. После того как вы создадите и присоедините свой целевой объект вычислений, используйте объект `ComputeTarget` на [шаге конвейера](#steps).

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

### <a id="databricks"></a>Azure Databricks

Azure Databricks — это среда, которая лежит в основе Apache Spark в облаке Azure. Ее можно использовать как целевой объект вычислений с помощью конвейера Машинного обучения Azure.

Прежде чем ее использовать, создайте рабочую область Azure Databricks. Чтобы создать ресурс рабочей области, см. статью [Запуск задания Spark в Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) документе.

Введите следующие данные, чтобы вложить Azure Databricks в качестве целевого объекта вычислений.

* __Имя для вычислений для кирпичей__. имя, которое вы хотите назначить этому ресурсу вычислений.
* __Имя рабочей области кирпичей__данных: имя рабочей области Azure Databricks.
* __Маркер доступа к модулям__. маркер доступа используется для проверки подлинности в Azure Databricks. Чтобы создать маркер доступа, см. документ [Проверка подлинности](https://docs.azuredatabricks.net/dev-tools/api/latest/authentication.html).

В следующем примере кода показано, как присоединить Azure Databricks в качестве целевого объекта вычислений с Машинное обучение Azureным пакетом SDK (__Рабочая область "кирпичы" должна находиться в той же подписке, что и Рабочая область AML__):

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

Более подробный пример см. в [примере записной книжки](https://aka.ms/pl-databricks) на сайте GitHub.

### <a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics — это платформа аналитики больших данных в облаке Azure. Ее можно использовать как целевой объект вычислений с помощью конвейера Машинного обучения Azure.

Прежде чем ее использовать, создайте учетную запись Azure Data Lake Analytics. Чтобы создать этот ресурс, см. статью [Начало работы с Azure Data Lake Analytics с помощью портала Azure](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal).

Чтобы вложить Azure Data Lake Analytics в качестве целевого объекта вычислений, используйте пакет SDK машинного обучения Azure и укажите следующие сведения.

* __Имя вычисления__. Имя, которое вы хотите назначить как вычислительный ресурс.
* __Группа ресурсов__. Группа ресурсов, содержащая учетную запись Data Lake Analytics.
* __Имя учетной записи__. имя учетной записи Data Lake Analytics.

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

Более подробный пример см. в [примере записной книжки](https://aka.ms/pl-adla) на сайте GitHub.

> [!TIP]
> Конвейеры машинного обучения Azure работают только с хранимыми данными в хранилище данных учетной записи Data Lake Analytics по умолчанию. Если данные, с которыми нужно работать, находятся в хранилище не по умолчанию, то для копирования данных перед обучением можно использовать [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py).

## <a id="steps"></a>Создание шагов конвейера

После создания и присоединения целевого объекта вычисления к вашей рабочей области все готово для определения шага конвейера. В пакете SDK для Машинного обучения Azure доступно много встроенных шагов. Наиболее простым из этих шагов является [писонскриптстеп](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py), который запускает скрипт Python в указанном целевом объекте вычислений:

```python
from azureml.pipeline.steps import PythonScriptStep

trainStep = PythonScriptStep(
    script_name="train.py",
    arguments=["--input", blob_input_data, "--output", output_data1],
    inputs=[blob_input_data],
    outputs=[output_data1],
    compute_target=compute_target,
    source_directory=project_folder
)
```

Повторное использование предыдущих результатов (`allow_reuse`) является ключом при использовании конвейеров в среде совместной работы, поскольку удаление ненужных повторов обеспечивает гибкость. Используется по умолчанию, когда script_name, входные и параметры шага остаются неизменными. При повторном использовании выходных данных шага задание не отправляется в вычисление, а результаты предыдущего запуска немедленно становятся доступными для выполнения следующего шага. Если `allow_reuse` имеет значение false, то во время выполнения конвейера для этого шага всегда будет создаваться новый запуск. 

После определения шагов следует создать конвейер, добавив в него некоторые созданные шаги (или все).

> [!NOTE]
> Файл или данные не передаются в Машинное обучение Azure при определении шагов или при построении конвейера.

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

Чтобы использовать ЕИС `TabularDataset` или `FileDataset` в конвейере, необходимо преобразовать его в объект [датасетконсумптионконфиг](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_consumption_config.datasetconsumptionconfig?view=azure-ml-py) , вызвав [as_named_input (Name)](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#as-named-input-name-). Этот объект `DatasetConsumptionConfig` передается в качестве одного из `inputs` на шаг конвейера. 

```python
dataset_consuming_step = PythonScriptStep(
    script_name="iris_train.py",
    inputs=[iris_tabular_dataset.as_named_input("iris_data")],
    compute_target=compute_target,
    source_directory=project_folder
)
```

Затем вы получаете набор данных в конвейере с помощью словаря [Run. input_datasets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#input-datasets) .

```python
# iris_train.py
from azureml.core import Run, Dataset

run_context = Run.get_context()
iris_dataset = run_context.input_datasets['iris_data']
dataframe = iris_dataset.to_pandas_dataframe()
```

Дополнительные сведения см. в справочнике по [классам пакета и конвейера](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) [Azure-Pipeline-этапах](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) .

## <a name="submit-the-pipeline"></a>Отправка конвейера

При отправке конвейера Машинное обучение Azure проверяет зависимости для каждого шага и загружает моментальный снимок указанного исходного каталога. Если исходный каталог не указан, передается текущий локальный каталог. Моментальный снимок также хранится в рамках эксперимента в рабочей области.

> [!IMPORTANT]
> Чтобы предотвратить включение файлов в моментальный снимок, создайте файл [. gitignore](https://git-scm.com/docs/gitignore) или `.amlignore` в каталоге и добавьте в него файлы. Файл `.amlignore` использует тот же синтаксис и шаблоны, что и файл [gitignore](https://git-scm.com/docs/gitignore) . Если оба файла существуют, приоритет имеет файл `.amlignore`.
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
* Скачивает образ DOCKER для каждого шага в целевом объекте вычислений из реестра контейнеров.
* Подключает хранилище данных, если на шаге указан объект `DataReference`. Если подключение не поддерживается, то данные копируются на целевой объект вычислений.
* Выполняет шаг на целевом объекте вычислений, указанном в определении этого шага. 
* Создает артефакты (журналы, stdout и stderr, метрики и выходные данные), указанные в шаге. Затем эти артефакты передаются в хранилище данных пользователя по умолчанию для хранения.

![Схема запуска эксперимента в виде конвейера](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

Дополнительные сведения см. в справочнике по [классу эксперимента](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) .

### <a name="view-results-of-a-pipeline"></a>Просмотр результатов конвейера

Просмотрите список всех конвейеров и сведения о его выполнении в студии:

1. Войдите в [Студию машинного обучения Azure](https://ml.azure.com).

1. [Просмотрите рабочую область](how-to-manage-workspace.md#view).

1. В левой части выберите **конвейеры** , чтобы просмотреть все запуски конвейера.
 ![Список конвейеров машинного обучения](./media/how-to-create-your-first-pipeline/pipelines.png)
 
1. Выберите конкретный конвейер, чтобы просмотреть результаты его запуска.

## <a name="git-tracking-and-integration"></a>Отслеживание и интеграция Git

При запуске обучающего запуска, в котором исходный каталог является локальным репозиторием Git, сведения о репозитории хранятся в журнале выполнения. Дополнительные сведения см. в статье [Интеграция Git для машинное обучение Azure](concept-train-model-git-integration.md).

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
     target=compute_target,
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

Чтобы вызвать выполнение предыдущего конвейера, требуется маркер заголовка проверки подлинности Azure Active Directory, как описано в справочнике по [классам азуреклиаусентикатион](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py) , или получите дополнительные сведения о [проверке подлинности в машинное обучение Azure](https://aka.ms/pl-restep-auth) записной книжке.

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

## <a name="create-a-versioned-pipeline-endpoint"></a>Создание конечной точки конвейера с версией
Вы можете создать конечную точку конвейера с несколькими опубликованными конвейерами. Это можно использовать как опубликованный конвейер, но предоставляет фиксированную конечную точку RESTFUL при итерации и обновлении конвейеров машинного обучения.

```python
from azureml.pipeline.core import PipelineEndpoint

published_pipeline = PublishedPipeline.get(workspace="ws", name="My_Published_Pipeline")
pipeline_endpoint = PipelineEndpoint.publish(workspace=ws, name="PipelineEndpointTest",
                                            pipeline=published_pipeline, description="Test description Notebook")
```

### <a name="submit-a-job-to-a-pipeline-endpoint"></a>Отправка задания в конечную точку конвейера
Вы можете отправить задание в версию по умолчанию конечной точки конвейера:
```python
pipeline_endpoint_by_name = PipelineEndpoint.get(workspace=ws, name="PipelineEndpointTest")
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment")
print(run_id)
```
Вы также можете отправить задание в определенную версию:
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

1. [Просмотрите рабочую область](how-to-manage-workspace.md#view).

1. В левой части выберите **конечные точки**.

1. В верхней части окна выберите **конечные точки конвейера**.
 ![список опубликованных конвейеров машинного обучения](./media/how-to-create-your-first-pipeline/pipeline-endpoints.png)

1. Выберите конкретный конвейер для выполнения, использования или просмотра результатов предыдущих запусков конечной точки конвейера.


### <a name="disable-a-published-pipeline"></a>Отключение опубликованного конвейера

Чтобы скрыть конвейер из списка опубликованных конвейеров, отключите его либо в студии, либо в пакете SDK:

```
# Get the pipeline by using its ID from Azure Machine Learning studio
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

Его можно снова включить с `p.enable()`. Дополнительные сведения см. в разделе Справочник по [классам публишедпипелине](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline?view=azure-ml-py) .


## <a name="caching--reuse"></a>Повторное использование кэширования &  

Чтобы оптимизировать и настроить поведение конвейеров, можно выполнить несколько действий по кэшированию и повторному использованию. Например, можно выбрать один из следующих способов:
+ **Отключите повторное использование по умолчанию выходных данных этапа выполнения** , задав `allow_reuse=False` во время [определения шага](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py). Повторное использование является ключом при использовании конвейеров в среде совместной работы, поскольку удаление ненужных запусков обеспечивает гибкость. Однако можно отказаться от повторного использования.
+ **Расширение хэширования за пределы скрипта**для включения абсолютного или относительного пути к source_directory другим файлам и каталогам с помощью `hash_paths=['<file or directory']` 
+ **Принудительное повторное создание выходных данных для всех шагов в запуске** с `pipeline_run = exp.submit(pipeline, regenerate_outputs=False)`

По умолчанию `allow_reuse` для шагов включено, и только основной файл скрипта хэшируется. Таким образом, если скрипт для данного шага остается неизменным (`script_name`, входные и параметры), то выходные данные предыдущего шага используются повторно, задание не отправляется в вычисление, а результаты предыдущего запуска сразу же становятся доступными для следующего шага.  

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
- См. справочную справку по пакету [azureml-конвейеры-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) и пакету [azureml-конвейеры-этапов](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) .
- Советы по отладке и устранению неполадок конвейеров см. [в этой статье](how-to-debug-pipelines.md) .

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
