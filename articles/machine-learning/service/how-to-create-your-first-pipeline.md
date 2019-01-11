---
title: Создание, запуск и отслеживание конвейеров машинного обучения
titleSuffix: Azure Machine Learning service
description: Создание и запуск конвейера машинного обучения с помощью пакета SDK Машинного обучения Azure для Python. Конвейеры используются для создания рабочих процессов, которые совмещают этапы машинного обучения, и управления ими. Эти этапы включают в себя подготовку данных, обучение модели, развертывание модели и вывод.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 6c6472b824eefdd1954f3645c69090d1fb5455de
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/22/2018
ms.locfileid: "53754464"
---
# <a name="create-and-run-a-machine-learning-pipeline-by-using-azure-machine-learning-sdk"></a>Создание и запуск конвейера машинного обучения с помощью пакета SDK для машинного обучения Azure

В этой статье вы узнаете, как создать, опубликовать, запустить и отслеживать [конвейер машинного обучения](concept-ml-pipelines.md) с помощью [пакета SDK для машинного обучения Azure](https://aka.ms/aml-sdk).  Эти конвейеры используются для создания рабочих процессов, объединяющих различные этапы машинного обучения. Каждый этап конвейера, например подготовка данных и обучение модели, может содержать один или несколько шагов.

Созданные вами конвейеры видны участникам вашей [рабочей области](how-to-manage-workspace.md) в Службе машинного обучения Azure. 

Конвейер использует удаленные целевые объекты вычислений для вычисления и хранения промежуточных и конечных данных, связанных с данным конвейером. Конвейеры могут выполнять чтение и запись данных в поддерживаемых расположениях [службы хранилища Azure](https://docs.microsoft.com/azure/storage/).

>[!Note]
>Если у вас еще нет подписки Azure, создайте бесплатную учетную запись Azure, прежде чем начинать работу. Опробуйте [бесплатную или платную версию службы машинного обучения Azure](http://aka.ms/AMLFree).

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

### <a name="set-up-compute"></a>Настройка вычислительной среды

В машинном обучении Azure *вычислительной средой* (или *целевым объектом вычислений*) считаются компьютеры или кластеры, которые выполняют вычислительные операции конвейера машинного обучения. Например, для выполнения шагов конвейера можно создать вычислительную среду машинного обучения Azure.

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

## <a name="construct-your-pipeline-steps"></a>Создание шагов конвейера

Теперь все готово для определения шага конвейера. В пакете SDK для Машинного обучения Azure доступно много встроенных шагов. Наиболее простым из них является `PythonScriptStep`. Он выполняет сценарий Python на указанном целевом объекте вычислений.

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

## <a name="submit-the-pipeline"></a>Отправка конвейера

При отправке конвейера служба машинного обучения Azure проверяет зависимости для каждого шага и отправляет моментальный снимок указанного исходного каталога. Если исходный каталог не указан, передается текущий локальный каталог.

```python
# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
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
