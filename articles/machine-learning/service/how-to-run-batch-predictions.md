---
title: Выполнение пакетного прогнозирования на больших объемах данных
titleSuffix: Azure Machine Learning service
description: Узнайте, как асинхронно выполнять пакетное прогнозирование на больших объемах данных с помощью Службы машинного обучения Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens, garye
ms.author: jordane
author: jpe316
ms.date: 07/12/2019
ms.openlocfilehash: 689ee003e0923a65d3ca3f2d13c1a2d05c299dbd
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358725"
---
# <a name="run-batch-predictions-on-large-data-sets-with-azure-machine-learning-service"></a>Пакетное прогнозирование на больших наборах данных с помощью Службы машинного обучения Azure

Из этой статьи вы узнаете, как асинхронно создавать прогнозы для больших объемов данных с помощью службы Машинное обучение Azure.

Пакетное прогнозирование (или пакетная оценка) обеспечивает экономичное получение выводов с непревзойденной пропускной способностью для асинхронных приложений. Конвейеры пакетного прогнозирования могут масштабировать свои ресурсы, чтобы получать выводы на основе терабайтов рабочих данных. Пакетное прогнозирование оптимизировано для обеспечения высокой пропускной способности и получения мгновенных прогнозов для большой коллекции данных.

>[!TIP]
> Если вашей системе требуется обработка с низкой задержкой (для быстрой обработки отдельных документов или небольшого набора документов), используйте [оценку в реальном времени](how-to-consume-web-service.md) вместо пакетного прогнозирования.

На следующих шагах вы создадите [конвейер машинного обучения](concept-ml-pipelines.md) для регистрации предварительно обученной модели компьютерной концепции ([порождение — v3](https://arxiv.org/abs/1512.00567)). Затем вы используете предварительно обученную модель для пакетной оценки образов, доступных в учетной записи хранилища BLOB-объектов Azure. Для оценки будут использоваться изображения без меток из набора данных [ImageNet](http://image-net.org/).

## <a name="prerequisites"></a>предварительные требования

- Если у вас еще нет подписки Azure, создайте бесплатную учетную запись Azure, прежде чем начинать работу. Опробуйте [бесплатную или платную версию Службы машинного обучения Azure](https://aka.ms/AMLFree).

- Настройте среду разработки для установки пакета SDK для Машинного обучения Azure. Для получения дополнительных сведений см. раздел [Настройка среды разработки для Машинного обучения Azure](how-to-configure-environment.md).

- Создайте рабочую область Машинного обучения Azure, в которой будут храниться все ресурсы конвейера. Можно использовать приведенный ниже код. Чтобы ознакомиться с дополнительными возможностями, прочитайте раздел [Создание файла конфигурации рабочей области](how-to-configure-environment.md#workspace).

  ```python
  from azureml.core import Workspace
  ws = Workspace.create(name = '<workspace-name>',
                        subscription_id = '<subscription-id>',
                        resource_group = '<resource-group>',
                        location = '<workspace_region>',
                        exist_ok = True
                        )
  ```

## <a name="set-up-machine-learning-resources"></a>Настройка ресурсов машинного обучения

На следующих шагах настраиваются ресурсы, необходимые для запуска конвейера.

- Получение доступа к хранилищу данных, содержащему предварительно обученную модель, входные метки и изображения для оценки (все это уже настроено для вас).
- Настройка хранилища данных для хранения выходных данных.
- Настройка объектов `DataReference` для указания на данные в описанных выше хранилищах данных.
- Настройка вычислительных компьютеров или кластеров, на которых будут выполняться действия конвейера.

### <a name="access-the-datastores"></a>Доступ к хранилищам данных

Сначала следует получить доступ к хранилищу данных, содержащему модели, метки и изображения.

Используйте общедоступный контейнер больших двоичных объектов с именем *SampleData*в учетной записи *пипелинедата* , которая содержит образы из набора оценки ImageNet. Имя хранилища данных для этого общедоступного — *images_datastore*. Зарегистрируйте это хранилище данных в своей рабочей области.

```python
from azureml.core import Datastore

account_name = "pipelinedata"
datastore_name = "images_datastore"
container_name = "sampledata"

batchscore_blob = Datastore.register_azure_blob_container(ws,
                                                          datastore_name=datastore_name,
                                                          container_name=container_name,
                                                          account_name=account_name,
                                                          overwrite=True)
```

Затем настройте хранилище данных по умолчанию для выходных данных.

При создании рабочей области к ней по умолчанию подключаются [Файлы Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) и [хранилище BLOB-объектов](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) . Файлы Azure — это хранилище данных по умолчанию для рабочей области, но вы можете также использовать хранилище BLOB-объектов для хранения данных. Дополнительные сведения см. в статье [Выбор между большими двоичными объектами Azure, службой файлов Azure и дисками Azure](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

```python
def_data_store = ws.get_default_datastore()
```

### <a name="configure-data-references"></a>Настройка ссылок на данные

Теперь добавьте в конвейер ссылки на данные в качестве входных данных шагов.

Источник данных в конвейере представлен объектом [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference) . Объект `DataReference` указывает на данные, которые хранятся или доступны в хранилище данных. Объекты `DataReference` требуются для каталога, используемого для входных изображений, каталога, в котором хранится предварительно обученная модель, каталога для меток и каталога выходных данных.

```python
from azureml.data.data_reference import DataReference

input_images = DataReference(datastore=batchscore_blob,
                             data_reference_name="input_images",
                             path_on_datastore="batchscoring/images",
                             mode="download")

model_dir = DataReference(datastore=batchscore_blob,
                          data_reference_name="input_model",
                          path_on_datastore="batchscoring/models",
                          mode="download")

label_dir = DataReference(datastore=batchscore_blob,
                          data_reference_name="input_labels",
                          path_on_datastore="batchscoring/labels",
                          mode="download")

output_dir = PipelineData(name="scores",
                          datastore=def_data_store,
                          output_path_on_compute="batchscoring/results")
```

### <a name="set-up-compute-target"></a>Настройка целевого объекта для вычислений

В Машинном обучении Azure *вычислительной средой* (или *целевым объектом вычислений*) считаются компьютеры или кластеры, которые выполняют вычислительные операции конвейера машинного обучения. Например, вы можете создать `Azure Machine Learning compute`.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget

compute_name = "gpucluster"
compute_min_nodes = 0
compute_max_nodes = 4
vm_size = "STANDARD_NC6"

if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target. just use it. ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(
        vm_size=vm_size,  # NC6 is GPU-enabled
        vm_priority='lowpriority',  # optional
        min_nodes=compute_min_nodes,
        max_nodes=compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws,
                                          compute_name,
                                          provisioning_config)

    compute_target.wait_for_completion(
        show_output=True,
        min_node_count=None,
        timeout_in_minutes=20)
```

## <a name="prepare-the-model"></a>Подготовка модели

Прежде чем использовать предварительно обученную модель, ее необходимо скачать и зарегистрировать в рабочей области.

### <a name="download-the-pretrained-model"></a>Скачивание предварительно обученной модели

Скачайте предварительно обученную модель компьютерного зрения (InceptionV3) отсюда: <http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz>. Извлеките содержимое во вложенную папку `models`.

```python
import os
import tarfile
import urllib.request

model_dir = 'models'
if not os.path.isdir(model_dir):
    os.mkdir(model_dir)

url = "http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz"
response = urllib.request.urlretrieve(url, "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall(model_dir)
```

### <a name="register-the-model"></a>Регистрация модели

Вот как можно зарегистрировать модель:

```python
import shutil
from azureml.core.model import Model

# register downloaded model
model = Model.register(
    model_path="models/inception_v3.ckpt",
    model_name="inception",  # This is the name of the registered model
    tags={'pretrained': "inception"},
    description="Imagenet trained tensorflow inception",
    workspace=ws)
```

## <a name="write-your-scoring-script"></a>Создание сценария оценки

>[!Warning]
>Приведенный ниже код является только примером содержимого файла [batch_score.py](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/pipeline-batch-scoring/batch_scoring.py), используемого [примером записной книжки](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/pipeline-batch-scoring/pipeline-batch-scoring.ipynb). Вам потребуется создать собственный сценарий оценки, подходящий для вашей ситуации.

Сценарий `batch_score.py` принимает входные изображения из папки  *dataset_path* и обученные модели из папки  *model_dir*, а файл результатов *results-label.txt*  сохраняет в папку  *output_dir*.

```python
# Snippets from a sample scoring script
# Refer to the accompanying batch-scoring Notebook
# https://github.com/Azure/MachineLearningNotebooks/blob/master/pipeline/pipeline-batch-scoring.ipynb
# for the implementation script

# Get labels
def get_class_label_dict(label_file):
  label = []
  proto_as_ascii_lines = tf.gfile.GFile(label_file).readlines()
  for l in proto_as_ascii_lines:
    label.append(l.rstrip())
  return label

class DataIterator:
  # Definition of the DataIterator here

def main(_):
    # Refer to batch-scoring Notebook for implementation.
    label_file_name = os.path.join(args.label_dir, "labels.txt")
    label_dict = get_class_label_dict(label_file_name)
    classes_num = len(label_dict)
    test_feeder = DataIterator(data_dir=args.dataset_path)
    total_size = len(test_feeder.labels)

    # get model from model registry
    model_path = Model.get_model_path(args.model_name)
    with tf.Session() as sess:
        test_images = test_feeder.input_pipeline(batch_size=args.batch_size)
        with slim.arg_scope(inception_v3.inception_v3_arg_scope()):
            input_images = tf.placeholder(tf.float32, [args.batch_size, image_size, image_size, num_channel])
            logits, _ = inception_v3.inception_v3(input_images,
                                                        num_classes=classes_num,
                                                        is_training=False)
            probabilities = tf.argmax(logits, 1)

        sess.run(tf.global_variables_initializer())
        sess.run(tf.local_variables_initializer())
        coord = tf.train.Coordinator()
        threads = tf.train.start_queue_runners(sess=sess, coord=coord)
        saver = tf.train.Saver()
        saver.restore(sess, model_path)
        out_filename = os.path.join(args.output_dir, "result-labels.txt")

        # copy the file to artifacts
        shutil.copy(out_filename, "./outputs/")
```

## <a name="build-and-run-the-batch-scoring-pipeline"></a>Сборка и запуск конвейера пакетной оценки

### <a name="prepare-the-run-environment"></a>Подготовка среды выполнения

Укажите зависимости Conda для своего сценария. Этот объект потребуется позже, когда будет создаваться шаг конвейера.

```python
from azureml.core.runconfig import DEFAULT_GPU_IMAGE
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

cd = CondaDependencies.create(
    pip_packages=["tensorflow-gpu==1.10.0", "azureml-defaults"])

# Runconfig
amlcompute_run_config = RunConfiguration(conda_dependencies=cd)
amlcompute_run_config.environment.docker.enabled = True
amlcompute_run_config.environment.docker.gpu_support = True
amlcompute_run_config.environment.docker.base_image = DEFAULT_GPU_IMAGE
amlcompute_run_config.environment.spark.precache_packages = False
```

### <a name="specify-the-parameter-for-your-pipeline"></a>Указание параметра для конвейера

Создайте параметр конвейера, используя объект [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) со значением по умолчанию.

```python
from azureml.pipeline.core.graph import PipelineParameter
batch_size_param = PipelineParameter(
    name="param_batch_size",
    default_value=20)
```

### <a name="create-the-pipeline-step"></a>Создание шага конвейера

Создайте шаг конвейера, используя сценарий, конфигурацию среды и параметры. Укажите целевой объект вычислений, уже подключенный к вашей рабочей области, в качестве цели выполнения сценария. Используйте [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py), чтобы создать шаг конвейера.

```python
from azureml.pipeline.steps import PythonScriptStep
inception_model_name = "inception_v3.ckpt"

batch_score_step = PythonScriptStep(
    name="batch_scoring",
    script_name="batch_score.py",
    arguments=["--dataset_path", input_images,
               "--model_name", "inception",
               "--label_dir", label_dir,
               "--output_dir", output_dir,
               "--batch_size", batch_size_param],
    compute_target=compute_target,
    inputs=[input_images, label_dir],
    outputs=[output_dir],
    runconfig=amlcompute_run_config,
    source_directory=scripts_folder
```

### <a name="run-the-pipeline"></a>Запуск конвейера

Теперь запустите конвейер и изучите полученные выходные данные. Выходные данные содержат оценку, соответствующую каждому входному изображению.

```python
import pandas as pd
from azureml.pipeline.core import Pipeline

# Run the pipeline
pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(
    pipeline, pipeline_params={"param_batch_size": 20})

# Wait for the run to finish (this might take several minutes)
pipeline_run.wait_for_completion(show_output=True)

# Download and review the output
step_run = list(pipeline_run.get_children())[0]
step_run.download_file("./outputs/result-labels.txt")

df = pd.read_csv("result-labels.txt", delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
df.head()
```

## <a name="publish-the-pipeline"></a>Публикация конвейера

Когда результат запуска конвейера вас устроит, опубликуйте этот конвейер, чтобы позже вы могли запустить его с другими входными значениями. При публикации конвейера вы получаете конечную точку REST. Эта конечная точка принимает вызов запуска конвейера с набором параметров, которые вы уже добавили с помощью объекта [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py).

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring",
    description="Batch scoring using Inception v3 model",
    version="1.0")
```

## <a name="rerun-the-pipeline-by-using-the-rest-endpoint"></a>Повторный запуск конвейера с помощью конечной точки REST

Чтобы повторно запустить конвейер, потребуется маркер заголовка аутентификации Azure Active Directory, как описывается в [классе AzureCliAuthentication](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py).

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.pipeline.core import PublishedPipeline

rest_endpoint = published_pipeline.endpoint
# specify batch size when running the pipeline
response = requests.post(rest_endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"param_batch_size": 50}})

# Monitor the run
published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)

RunDetails(published_pipeline_run).show()
```

## <a name="next-steps"></a>Следующие шаги

Чтобы увидеть, как это работает от начала до конца, опробуйте записную книжку пакетной оценки на сайте [GitHub](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines).

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

