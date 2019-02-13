---
title: Создание, запуск и отслеживание конвейеров машинного обучения
titleSuffix: Azure Machine Learning service
description: Создание и запуск конвейера машинного обучения с помощью пакета SDK Машинного обучения Azure для Python. Конвейеры используются для создания рабочих процессов, которые совмещают этапы машинного обучения, и управления ими. Эти этапы включают в себя подготовку данных, обучение модели, развертывание модели и вывод.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 01/08/2019
ms.custom: seodec18
ms.openlocfilehash: 3d1e0f591e5ce9f56e79ffecf72599ed1d894dc9
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55746206"
---
# <a name="create-and-run-a-machine-learning-pipeline-by-using-azure-machine-learning-sdk"></a>Создание и запуск конвейера машинного обучения с помощью пакета SDK для машинного обучения Azure

В этой статье вы узнаете, как создать, опубликовать, запустить и отслеживать [конвейер машинного обучения](concept-ml-pipelines.md) с помощью [пакета SDK для машинного обучения Azure](https://aka.ms/aml-sdk).  Эти конвейеры используются для создания рабочих процессов, объединяющих различные этапы машинного обучения. Каждый этап конвейера, например подготовка данных и обучение модели, может содержать один или несколько шагов.

Созданные вами конвейеры видны участникам вашей [рабочей области](how-to-manage-workspace.md) в Службе машинного обучения Azure. 

Конвейер использует удаленные целевые объекты вычислений для вычисления и хранения промежуточных и конечных данных, связанных с данным конвейером. Конвейеры могут выполнять чтение и запись данных в поддерживаемых расположениях [службы хранилища Azure](https://docs.microsoft.com/azure/storage/).

Если у вас еще нет подписки Azure, создайте бесплатную учетную запись Azure, прежде чем начинать работу. Опробуйте [бесплатную или платную версию службы машинного обучения Azure](http://aka.ms/AMLFree).

## <a name="prerequisites"></a>Предварительные требования

* [Настройте среду разработки](how-to-configure-environment.md) для установки пакета SDK для Машинного обучения Azure.

* Создайте [рабочую область Машинного обучения Azure](how-to-configure-environment.md#workspace) для хранения всех ресурсов конвейера. 

 ```python
 ws = Workspace.create(
     name = '<workspace-name>',
     subscription_id = '<subscription-id>',
     resource_group = '<resource-group>',
     location = '<workspace_region>',
     exist_ok = True)
 ```

## <a name="set-up-machine-learning-resources"></a>Настройка ресурсов машинного обучения

Создайте ресурсы, необходимые для запуска конвейера.

* Настройте хранилище данных для хранения данных, необходимых для выполнения шагов конвейера.

* Настройте объект `DataReference`, чтобы указать данные, которые хранятся или доступны в хранилище данных.

* Настройте [целевые объекты вычислений](concept-azure-machine-learning-architecture.md#compute-target) для выполнения шагов вашего конвейера.

### <a name="set-up-a-datastore"></a>Настройка хранилища данных
Хранилище данных содержит данные, которые использует конвейер. У каждой рабочей области есть хранилище данных по умолчанию. Вы можете зарегистрировать дополнительные хранилища данных. 

При создании рабочей области к ней по умолчанию подключаются [служба файлов Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) и [хранилище BLOB-объектов Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). Файлы Azure — это хранилище данных по умолчанию для рабочей области, но можно также использовать хранилище BLOB-объектов для хранения данных. Дополнительные сведения см. в статье [Выбор между большими двоичными объектами Azure, службой файлов Azure и дисками Azure](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks). 

```python
# Default datastore (Azure file storage)
def_data_store = ws.get_default_datastore() 

# The above call is equivalent to this 
def_data_store = Datastore(ws, "workspacefilestore")

# Get blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")
```

Передайте файлы данных или каталоги в хранилище данных, чтобы они были доступны из конвейеров. В этом примере используется хранилище BLOB-объектов.

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
blob_input_data = DataReference(
    datastore=def_blob_store,
    data_reference_name="test_data",
    path_on_datastore="20newsgroups/20news.pkl")
```

Промежуточные данные (или выходные данные шага) представляет объект [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py). `output_data1` создается как выходные данные шага и используется в качестве входных данных одного шага или нескольких последующих шагов. `PipelineData` представляет зависимость данных между шагами и неявно определяет порядок выполнения шагов в конвейере.

```python
output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

## <a name="set-up-compute-target"></a>Настройка целевого объекта для вычислений

В машинном обучении Azure __вычислительной средой__ (или __целевым объектом вычислений__) считаются компьютеры или кластеры, которые выполняют вычислительные операции конвейера машинного обучения.   См. раздел [Настройка целевых объектов вычислений для обучения моделей](how-to-set-up-training-targets.md), чтобы получить полный список целевых объектов вычислений и научиться создавать и присоединять их к своей рабочей области.  Процесс создания и присоединения целевого объекта вычислений аналогичен независимо от того, тренируете ли вы модель или выполняете шаг конвейера. После того как вы создадите и присоедините свой целевой объект вычислений, используйте объект `ComputeTarget` на [шаге конвейера](#steps).

> [!IMPORTANT]
> Выполнение операций управления на целевых объектах вычислений не поддерживается внутри удаленных заданий. Так как конвейеры машинного обучения передаются в качестве удаленного задания, не используйте операции управления на целевых объектах вычислений внутри конвейера.

Ниже приведены примеры создания и присоединения целевых объектов вычислений для таких служб.

* Вычислительная среда Машинного обучения Azure;
* Azure Databricks 
* Аналитика озера данных Azure

### <a name="azure-machine-learning-compute"></a>Вычислительная среда Машинного обучения Azure;

Для выполнения шагов конвейера можно создать вычислительную среду Машинного обучения Azure.

```python
compute_name = "aml-compute"
 if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target: ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size, # NC6 is GPU-enabled
                                                                min_nodes = 1, 
                                                                max_nodes = 4)
     # create the compute target
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # Can poll for a minimum number of nodes and for a specific timeout. 
    # If no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current cluster status, use the 'status' property    
    print(compute_target.status.serialize())
```

### <a id="databricks"></a>Azure Databricks

Azure Databricks — это среда, которая лежит в основе Apache Spark в облаке Azure. Ее можно использовать как целевой объект вычислений с помощью конвейера Машинного обучения Azure.

Прежде чем ее использовать, создайте рабочую область Azure Databricks. Чтобы создать эти ресурсы, см. [Руководство. Автоматическое машинное обучение модели классификации в службе "Машинное обучение Azure"](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal).

Введите следующие данные, чтобы вложить Azure Databricks в качестве целевого объекта вычислений.

* __Вычислительная среда Databricks__. Имя, которое вы хотите присвоить этому вычислительному ресурсу.
* __Рабочая область Databricks__. Имя рабочей области Azure Databricks.
* __Маркер доступа Databricks__. Маркер доступа, который используется для аутентификации в Azure Databricks. Чтобы создать маркер доступа, см. документ [Проверка подлинности](https://docs.azuredatabricks.net/api/latest/authentication.html).

Следующий код демонстрирует, как вложить Azure Databricks в качестве целевого объекта вычислений с использованием пакета SDK для Машинного обучения Azure.

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get("AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get("AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get("AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get("AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group = databricks_resource_group,
                                                           workspace_name = databricks_workspace_name,
                                                           access_token = databricks_access_token)
    databricks_compute = ComputeTarget.attach(
             ws,
             databricks_compute_name,
             attach_config
         )
    
    databricks_compute.wait_for_completion(True)
```
### <a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics — это платформа аналитики больших данных в облаке Azure. Ее можно использовать как целевой объект вычислений с помощью конвейера Машинного обучения Azure.

Прежде чем ее использовать, создайте учетную запись Azure Data Lake Analytics. Чтобы создать этот ресурс, см. статью [Начало работы с Azure Data Lake Analytics с помощью портала Azure](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal).

Чтобы вложить Azure Data Lake Analytics в качестве целевого объекта вычислений, используйте пакет SDK машинного обучения Azure и укажите следующие сведения.

* __Имя Вычислительной среды__. Имя, которое вы хотите присвоить этому вычислительному ресурсу.
* __Группа ресурсов__. Группа ресурсов, содержащая учетную запись Data Lake Analytics.
* __Имя учетной записи__. Имя учетной записи Data Lake Analytics.

Следующий код демонстрирует, как вложить Data Lake Analytics в качестве целевого объекта вычислений.

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get("AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get("AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get("AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group = adla_resource_group,
                                                     account_name = adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
             ws,
             adla_compute_name,
             attach_config
         )
    
    adla_compute.wait_for_completion(True)
```

> [!TIP]
> Конвейеры машинного обучения Azure работают только с хранимыми данными в хранилище данных учетной записи Data Lake Analytics по умолчанию. Если данные, с которыми нужно работать, находятся в хранилище не по умолчанию, то для копирования данных перед обучением можно использовать [`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py).

## <a id="steps"></a>Создание шагов конвейера

После создания и присоединения целевого объекта вычисления к вашей рабочей области все готово для определения шага конвейера. В пакете SDK для Машинного обучения Azure доступно много встроенных шагов. Наиболее простым из них является [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py). Он выполняет сценарий Python на указанном целевом объекте вычислений.

```python
trainStep = PythonScriptStep(
    script_name="train.py",
    arguments=["--input", blob_input_data, "--output", processed_data1],
    inputs=[blob_input_data],
    outputs=[processed_data1],
    compute_target=compute_target,
    source_directory=project_folder
)
```

После определения шагов следует создать конвейер, добавив в него некоторые созданные шаги (или все).

>[!NOTE]
>При определении шагов и создании конвейера никакие файлы или данные в службу машинного обучения Azure не передаются.

```python
# list of steps to run
compareModels = [trainStep, extractStep, compareStep]

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compareModels])
```

В следующем примере используется созданный ранее целевой объект вычислений Azure Databricks. 

```python
dbStep = DatabricksStep(
    name="databricksmodule",
    inputs=[step_1_input],
    outputs=[step_1_output],
    num_workers=1,
    notebook_path=notebook_path,
    notebook_params={'myparam': 'testparam'},
    run_name='demo run name',
    databricks_compute=databricks_compute,
    allow_reuse=False
)
# List of steps to run
steps = [dbStep]

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=steps)
```

## <a name="submit-the-pipeline"></a>Отправка конвейера

При отправке конвейера служба машинного обучения Azure проверяет зависимости для каждого шага и отправляет моментальный снимок указанного исходного каталога. Если исходный каталог не указан, передается текущий локальный каталог.


```python
# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
pipeline_run1.wait_for_completion()
```

При первом запуске конвейера служба машинного обучения Azure выполняет следующие действия.

* Скачивает моментальный снимок проекта из хранилища BLOB-объектов, связанного с рабочей областью, на целевой объект вычислений.
* Создает образ Docker, соответствующий каждому шагу в конвейере.
* Скачивает образ Docker для каждого шага на целевой объект вычислений из реестра контейнеров.
* Если в шаге указан объект `DataReference`, подключает хранилище данных. Если подключение не поддерживается, то данные копируются на целевой объект вычислений.
* Выполняет шаг на целевом объекте вычислений, указанном в определении этого шага. 
* Создает артефакты (журналы, stdout и stderr, метрики и выходные данные), указанные в шаге. Затем эти артефакты передаются в хранилище данных пользователя по умолчанию для хранения.

![Схема запуска эксперимента в виде конвейера](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

## <a name="publish-a-pipeline"></a>Публикация конвейера

Вы можете опубликовать конвейера для последующего запуска с другими входными данными. Чтобы конечная точка REST опубликованного конвейера могла принимать параметры, перед публикацией конвейер нужно параметризовать. 

1. Чтобы создать параметр конвейера, используйте объект [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) со значением по умолчанию.

 ```python
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
   published_pipeline1 = pipeline1.publish(
       name="My_Published_Pipeline", 
       description="My Published Pipeline Description")
   ```

## <a name="run-a-published-pipeline"></a>Запустите опубликованный конвейер.

Все опубликованные конвейеры используют конечную точку REST. Она вызывает запуск конвейера из внешних систем, таких как клиенты не на платформе Python. Эта конечная точка обеспечивает управляемую повторяемость для сценариев пакетной оценки и переобучения.

Чтобы вызвать запуск предыдущего конвейера, требуется маркер заголовка аутентификации Azure Active Directory, как описывается в [классе AzureCliAuthentication](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py).

```python
response = requests.post(published_pipeline1.endpoint, 
    headers=aad_token, 
    json={"ExperimentName": "My_Pipeline",
        "ParameterAssignments": {"pipeline_arg": 20}})
```
## <a name="view-results"></a>Просмотр результатов

Просмотрите список всех конвейеров и подробные сведения об их запуске.
1. Войдите на [портале Azure](https://portal.azure.com/).  

1. [Просмотрите рабочую область](how-to-manage-workspace.md#view-a-workspace), чтобы найти список конвейеров.
 ![Список конвейеров машинного обучения](./media/how-to-create-your-first-pipeline/list_of_pipelines.png)
 
1. Выберите конкретный конвейер, чтобы просмотреть результаты его запуска.

## <a name="next-steps"></a>Дополнительная информация
- Используйте [эти записные книжки Jupyter на сайте GitHub](https://aka.ms/aml-pipeline-readme), чтобы подробнее изучить конвейеры машинного обучения.
- Прочитайте справочные материалы по пакету SDK для пакетов [azureml-pipelines-core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) и [azureml-pipelines-steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py).

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
