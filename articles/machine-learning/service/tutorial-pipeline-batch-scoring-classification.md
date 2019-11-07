---
title: Руководство по конвейерам Машинного обучения для пакетной оценки
titleSuffix: Azure Machine Learning
description: Создайте конвейер машинного обучения для выполнения пакетной оценки в модели классификации изображений в Машинном обучении Azure. Контейнеры машинного обучения оптимизируют ваш рабочий процесс за счет ускорения, мобильности и повторного использования, позволив вам сосредоточиться непосредственно на задачах обучения, а не на сложностях инфраструктуры.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 11/04/2019
ms.openlocfilehash: f693a80726c9185bbd75d5fb99eb7e5f3ccad987
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493499"
---
# <a name="build--use-an-azure-machine-learning-pipeline-for-batch-scoring"></a>Создание и использование конвейеров Машинного обучения Azure для пакетной оценки

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

В рамках этого руководства вы будете использовать конвейер Машинного обучения Azure для выполнения заданий пакетной оценки. В этой статье для классификации изображений без метки используется [Inception-V3](https://arxiv.org/abs/1512.00567), предварительно обученная модель сверточной нейронной сети Tensorflow. После создания и публикации конвейера необходимо настроить конечную точку REST, позволяющую активировать конвейер с любой библиотеки HTTP на любой платформе.

Контейнеры машинного обучения оптимизируют ваш рабочий процесс за счет ускорения, мобильности и повторного использования, позволив вам сосредоточиться непосредственно на задачах обучения, а не на сложностях инфраструктуры. [Дополнительные сведения о конвейерах машинного обучения](concept-ml-pipelines.md).

В этом руководстве выполняются следующие задачи:

> [!div class="checklist"]
> * Настройка рабочей области и скачивание образцов данных.
> * Создание объектов данных для извлечения и вывода данных.
> * Скачивание, подготовка и регистрация модели в рабочей области
> * Подготовка целевых объектов вычислений и создание сценария оценки.
> * Создание, запуск и публикация конвейера.
> * Включение конечной точки REST для конвейера.

Если у вас еще нет подписки Azure, создайте бесплатную учетную запись Azure, прежде чем начинать работу. Опробуйте [бесплатную или платную версию Машинного обучения Azure](https://aka.ms/AMLFree) уже сегодня.

## <a name="prerequisites"></a>Предварительные требования

* Если у вас еще нет рабочей области Машинного обучения Azure или виртуальной машины записной книжки, выполните инструкции, приведенные в [первой части руководства по установке](tutorial-1st-experiment-sdk-setup.md).
* Завершив установку, используйте тот же сервер записной книжки, чтобы открыть записную книжку *tutorials/tutorial-pipeline-batch-scoring-classification.ipynb*.

Если вы хотите открыть руководство по установке в собственной [локальной среде](how-to-configure-environment.md#local), доступ к нему можно получить на [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials). Выполните команду `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-pipeline-steps pandas requests`, чтобы получить необходимые пакеты.

## <a name="configure-workspace-and-create-a-datastore"></a>Настройка рабочей области и создание хранилища данных

В имеющейся рабочей области Машинного обучения Azure создайте объект.

- Класс [workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) принимает сведения о подписке и ресурсах Azure. Он также создает облачный ресурс, который можно использовать для мониторинга и отслеживания работы модели. 
- `Workspace.from_config()` считывает файл `config.json` и загружает сведения о проверке подлинности в объект с именем `ws`. Объект `ws` используется в коде в рамках этого руководства.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

### <a name="create-a-datastore-for-sample-images"></a>Создание хранилища данных для образцов изображений

В учетной записи `pipelinedata` извлеките образец общедоступных данных вычисления ImageNet из общедоступного контейнера больших двоичных объектов `sampledata`. Выполните вызов `register_azure_blob_container()`, чтобы предоставить рабочей области `images_datastore` доступ к данным. Затем задайте хранилище данных рабочей области по умолчанию в качестве выходного. Используйте выходное хранилище данных для оценки выходных данных в конвейере.

```python
from azureml.core.datastore import Datastore

batchscore_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="images_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata", 
                      overwrite=True)

def_data_store = ws.get_default_datastore()
```

## <a name="create-data-objects"></a>Создание объектов данных

При создании конвейера объект `DataReference` считывает данные из хранилища данных рабочей области. Объект `PipelineData` передает промежуточные данные между шагами конвейера.

> [!Important]
> Пример оценки пакетной службы в этом руководстве использует только один шаг конвейера. В вариантах использования с несколькими шагами типичный поток будет включать следующие шаги:
>
> 1. Использование объектов `DataReference` в качестве *входных данных* для извлечения необработанных данных, выполнение определенных преобразований, а затем *вывод* объекта `PipelineData`.
>
> 2. Использование *выходного объекта* `PipelineData` в предыдущем шаге в качестве *входного объекта*. Повторение для последующих шагов.

В этом сценарии создайте объекты `DataReference`, соответствующие каталогам хранилища данных для входных изображений и меток классификации (значения y-test). Кроме того, создайте объект `PipelineData` для выходных данных пакетной оценки.

```python
from azureml.data.data_reference import DataReference
from azureml.pipeline.core import PipelineData

input_images = DataReference(datastore=batchscore_blob, 
                             data_reference_name="input_images",
                             path_on_datastore="batchscoring/images",
                             mode="download"
                            )

label_dir = DataReference(datastore=batchscore_blob, 
                          data_reference_name="input_labels",
                          path_on_datastore="batchscoring/labels",
                          mode="download"                          
                         )

output_dir = PipelineData(name="scores", 
                          datastore=def_data_store, 
                          output_path_on_compute="batchscoring/results")
```

## <a name="download-and-register-the-model"></a>Скачивание и регистрация модели

Скачайте предварительно обученную модель Tensorflow, чтобы использовать ее для пакетной оценки в конвейере. Сначала создайте локальный каталог, в котором будет храниться модель. Затем скачайте и извлеките модель.

```python
import os
import tarfile
import urllib.request

if not os.path.isdir("models"):
    os.mkdir("models")
    
response = urllib.request.urlretrieve("http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz", "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall("models")
```

Выполните регистрацию модели в рабочей области, что позволит быстро извлекать ее в процессе конвейера. В статической функции `register()` параметр `model_name` является ключом, который используется для определения расположения модели в пакете SDK.

```python
from azureml.core.model import Model
 
model = Model.register(model_path="models/inception_v3.ckpt",
                       model_name="inception",
                       tags={"pretrained": "inception"},
                       description="Imagenet trained tensorflow inception",
                       workspace=ws)
```

## <a name="create-and-attach-the-remote-compute-target"></a>Создание и вложение удаленного целевого объекта вычислений

Конвейеры машинного обучения невозможно запустить локально, поэтому их можно запускать в облачных ресурсах или *удаленных целевых объектах вычислений*. Удаленный целевой объект вычислений — это повторно используемая виртуальная среда вычислений, в которой выполняются эксперименты и рабочие процессы машинного обучения. 

Выполните приведенный ниже код, чтобы создать целевой объект [`AmlCompute`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) с поддержкой GPU, а затем вложите его в свою рабочую область. Дополнительные сведения о целевых объектах вычислений см. в [тематической статье](https://docs.microsoft.com/azure/machine-learning/service/concept-compute-target).


```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.exceptions import ComputeTargetException
compute_name = "gpu-cluster"

# checks to see if compute target already exists in workspace, else create it
try:
    compute_target = ComputeTarget(workspace=ws, name=compute_name)
except ComputeTargetException:
    config = AmlCompute.provisioning_configuration(vm_size="STANDARD_NC6",
                                                   vm_priority="lowpriority", 
                                                   min_nodes=0, 
                                                   max_nodes=1)

    compute_target = ComputeTarget.create(workspace=ws, name=compute_name, provisioning_configuration=config)
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

## <a name="write-a-scoring-script"></a>Написание сценария оценки

Для оценки необходимо создать скрипт пакетной оценки под названием `batch_scoring.py`, а затем записать его в текущий каталог. Скрипт принимает входные изображения, применяет модель классификации, а затем выводит прогнозы в файл результатов.

Скрипт `batch_scoring.py` принимает приведенные ниже параметры, передаваемые из шага конвейера, который вы создадите позже:

- `--model_name`: Имя используемой модели.
- `--label_dir`: Каталог, содержащий файл `labels.txt`.
- `--dataset_path`: Каталог, содержащий входные изображения.
- `--output_dir`: Выходной каталог для файла `results-label.txt` после того, как скрипт запустит модель по данным.
- `--batch_size`: Размер пакета, используемый при запуске модели.

Инфраструктура конвейеров использует класс `ArgumentParser` для передачи параметров в шаги конвейера. Например, в следующем коде первый аргумент `--model_name` получает идентификатор свойства `model_name`. В функции `main()` для доступа к этому свойству используется `Model.get_model_path(args.model_name)`.


```python
%%writefile batch_scoring.py

import os
import argparse
import datetime
import time
import tensorflow as tf
from math import ceil
import numpy as np
import shutil
from tensorflow.contrib.slim.python.slim.nets import inception_v3
from azureml.core.model import Model

slim = tf.contrib.slim

parser = argparse.ArgumentParser(description="Start a tensorflow model serving")
parser.add_argument('--model_name', dest="model_name", required=True)
parser.add_argument('--label_dir', dest="label_dir", required=True)
parser.add_argument('--dataset_path', dest="dataset_path", required=True)
parser.add_argument('--output_dir', dest="output_dir", required=True)
parser.add_argument('--batch_size', dest="batch_size", type=int, required=True)

args = parser.parse_args()

image_size = 299
num_channel = 3

# create output directory if it does not exist
os.makedirs(args.output_dir, exist_ok=True)


def get_class_label_dict(label_file):
    label = []
    proto_as_ascii_lines = tf.gfile.GFile(label_file).readlines()
    for l in proto_as_ascii_lines:
        label.append(l.rstrip())
    return label


class DataIterator:
    def __init__(self, data_dir):
        self.file_paths = []
        image_list = os.listdir(data_dir)
        self.file_paths = [data_dir + '/' + file_name.rstrip() for file_name in image_list]
        self.labels = [1 for file_name in self.file_paths]

    @property
    def size(self):
        return len(self.labels)

    def input_pipeline(self, batch_size):
        images_tensor = tf.convert_to_tensor(self.file_paths, dtype=tf.string)
        labels_tensor = tf.convert_to_tensor(self.labels, dtype=tf.int64)
        input_queue = tf.train.slice_input_producer([images_tensor, labels_tensor], shuffle=False)
        labels = input_queue[1]
        images_content = tf.read_file(input_queue[0])

        image_reader = tf.image.decode_jpeg(images_content, channels=num_channel, name="jpeg_reader")
        float_caster = tf.cast(image_reader, tf.float32)
        new_size = tf.constant([image_size, image_size], dtype=tf.int32)
        images = tf.image.resize_images(float_caster, new_size)
        images = tf.divide(tf.subtract(images, [0]), [255])

        image_batch, label_batch = tf.train.batch([images, labels], batch_size=batch_size, capacity=5 * batch_size)
        return image_batch


def main(_):
    label_file_name = os.path.join(args.label_dir, "labels.txt")
    label_dict = get_class_label_dict(label_file_name)
    classes_num = len(label_dict)
    test_feeder = DataIterator(data_dir=args.dataset_path)
    total_size = len(test_feeder.labels)
    count = 0

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
        with open(out_filename, "w") as result_file:
            i = 0
            while count < total_size and not coord.should_stop():
                test_images_batch = sess.run(test_images)
                file_names_batch = test_feeder.file_paths[i * args.batch_size:
                                                          min(test_feeder.size, (i + 1) * args.batch_size)]
                results = sess.run(probabilities, feed_dict={input_images: test_images_batch})
                new_add = min(args.batch_size, total_size - count)
                count += new_add
                i += 1
                for j in range(new_add):
                    result_file.write(os.path.basename(file_names_batch[j]) + ": " + label_dict[results[j]] + "\n")
                result_file.flush()
            coord.request_stop()
            coord.join(threads)

        shutil.copy(out_filename, "./outputs/")

if __name__ == "__main__":
    tf.app.run()

```

> [!TIP]
> Конвейер в этом руководстве содержит только один шаг и записывает выходные данные в файл. Для многошаговых конвейеров также используется `ArgumentParser`, чтобы определить каталог для записи выходных данных для последующего выполнения действий. Пример передачи данных между несколькими шагами конвейера с использованием конструктивного шаблона `ArgumentParser` см. в [записной книжке](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb).

## <a name="build-and-run-the-pipeline"></a>Создание и запуск конвейера

Перед запуском конвейера создайте объект, который определяет среду Python и создает зависимости, необходимые скрипту `batch_scoring.py`. Основной зависимостью является Tensorflow, но кроме нее установите `azureml-defaults` для фоновых процессов из пакета SDK. Создайте объект `RunConfiguration` с помощью зависимостей. Кроме того, следует указать поддержку Docker и Docker-GPU.

```python
from azureml.core.runconfig import DEFAULT_GPU_IMAGE
from azureml.core.runconfig import CondaDependencies, RunConfiguration

cd = CondaDependencies.create(pip_packages=["tensorflow-gpu==1.13.1", "azureml-defaults"])

amlcompute_run_config = RunConfiguration(conda_dependencies=cd)
amlcompute_run_config.environment.docker.enabled = True
amlcompute_run_config.environment.docker.base_image = DEFAULT_GPU_IMAGE
amlcompute_run_config.environment.spark.precache_packages = False
```

### <a name="parameterize-the-pipeline"></a>Параметризация конвейера

Определите пользовательский параметр для конвейера, чтобы управлять размером пакета. Когда конвейер публикуется и предоставляется через конечную точку REST, также предоставляются все настроенные параметры. При повторном запуске конвейера через HTTP-запрос можно указать пользовательские параметры в полезных данных JSON.

Создайте объект `PipelineParameter`, чтобы включить это поведение и определить имя и значение по умолчанию.

```python
from azureml.pipeline.core.graph import PipelineParameter
batch_size_param = PipelineParameter(name="param_batch_size", default_value=20)
```

### <a name="create-the-pipeline-step"></a>Создание шага конвейера

Шаг конвейера — это объект, который инкапсулирует все необходимое для запуска конвейера, включая:

* параметры среды и зависимостей;
* вычислительный ресурс для запуска конвейера;
* входные и выходные данные и любые пользовательские параметры;
* ссылку на скрипт или логику пакета SDK для запуска во время выполнения шага.

Несколько классов наследуют от родительского класса [`PipelineStep`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py). Для создания шага можно выбрать классы для использования конкретных платформ или стеков. В этом примере используется класс [`PythonScriptStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) для определения логики шага с помощью пользовательского скрипта Python. Если аргумент вашего скрипта является вводом или выводом шага, его необходимо определить *в* массиве `arguments` *и* в параметре `input` или `output` соответственно. 

Ссылка на объект в массиве `outputs` становится доступной в качестве *входных данных* для последующего шага конвейера в сценариях с несколькими шагами.

```python
from azureml.pipeline.steps import PythonScriptStep

batch_score_step = PythonScriptStep(
    name="batch_scoring",
    script_name="batch_scoring.py",
    arguments=["--dataset_path", input_images, 
               "--model_name", "inception",
               "--label_dir", label_dir, 
               "--output_dir", output_dir, 
               "--batch_size", batch_size_param],
    compute_target=compute_target,
    inputs=[input_images, label_dir],
    outputs=[output_dir],
    runconfig=amlcompute_run_config
)
```

Список всех классов для разных типов шагов см. в [этой статье](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py).

### <a name="run-the-pipeline"></a>Запуск конвейера

Теперь запустите конвейер. Сначала создайте объект `Pipeline` с помощью ссылки на вашу рабочую область и созданный шаг конвейера. Параметр `steps` является массивом шагов. В этом случае для пакетной оценки выполняется только один шаг. Чтобы создать конвейеры с несколькими шагами, разместите шаги по порядку в этом массиве.

Затем используйте функцию `Experiment.submit()`, чтобы передать конвейер для выполнения. Кроме того, укажите пользовательский параметр `param_batch_size`. Функция `wait_for_completion` выводит журналы во время процесса сборки конвейера. В журналах можно просмотреть текущий ход выполнения.

> [!IMPORTANT]
> Первый запуск конвейера занимает примерно *15 минут*. Необходимо скачать все зависимости, создать образ Docker и подготовить и создать среду Python. Повторный запуск конвейера занимает значительно меньше времени, так как эти ресурсы используются повторно, а не создаются. Однако общее время запуска для конвейера зависит от рабочей нагрузки ваших скриптов и процессов, выполняемых в каждом шаге конвейера.

```python
from azureml.core import Experiment
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline, pipeline_parameters={"param_batch_size": 20})
pipeline_run.wait_for_completion(show_output=True)
```

### <a name="download-and-review-output"></a>Скачивание и просмотр выходных данных

Запустите приведенный ниже код, чтобы скачать выходной файл, созданный из скрипта `batch_scoring.py`. Затем изучите результаты оценки.

```python
import pandas as pd

step_run = list(pipeline_run.get_children())[0]
step_run.download_file("./outputs/result-labels.txt")

df = pd.read_csv("result-labels.txt", delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
df.head(10)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Имя файла</th>
      <th>Прогнозирование</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>ILSVRC2012_val_00000102.JPEG</td>
      <td>Родезийский риджбек</td>
    </tr>
    <tr>
      <td>1</td>
      <td>ILSVRC2012_val_00000103.JPEG</td>
      <td>Штатив</td>
    </tr>
    <tr>
      <td>2</td>
      <td>ILSVRC2012_val_00000104.JPEG</td>
      <td>Клавиатура пишущего устройства</td>
    </tr>
    <tr>
      <td>3</td>
      <td>ILSVRC2012_val_00000105.JPEG</td>
      <td>Шелковистый терьер</td>
    </tr>
    <tr>
      <td>4\.</td>
      <td>ILSVRC2012_val_00000106.JPEG</td>
      <td>Виндзорский галстук</td>
    </tr>
    <tr>
      <td>5</td>
      <td>ILSVRC2012_val_00000107.JPEG</td>
      <td>Сенокосец</td>
    </tr>
    <tr>
      <td>6</td>
      <td>ILSVRC2012_val_00000108.JPEG</td>
      <td>Скрипка</td>
    </tr>
    <tr>
      <td>7</td>
      <td>ILSVRC2012_val_00000109.JPEG</td>
      <td>Громкоговоритель</td>
    </tr>
    <tr>
      <td>8</td>
      <td>ILSVRC2012_val_00000110.JPEG</td>
      <td>Фартук</td>
    </tr>
    <tr>
      <td>9</td>
      <td>ILSVRC2012_val_00000111.JPEG</td>
      <td>Американский омар</td>
    </tr>
  </tbody>
</table>
</div>

## <a name="publish-and-run-from-a-rest-endpoint"></a>Публикация и запуск из конечной точки REST

Выполните приведенный ниже код, чтобы опубликовать конвейер в рабочей области. В своей рабочей области студии машинного обучения Azure вы можете просмотреть метаданные для конвейера, включая журнал выполнения и продолжительность. Вы также можете запустить в студии конвейер вручную.

Публикация конвейера позволяет конечной точке REST повторно запускать конвейер из любой библиотеки HTTP на любой платформе.

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", description="Batch scoring using Inception v3 model", version="1.0")

published_pipeline
```

Чтобы запустить конвейер из конечной точки REST, вам потребуется заголовок проверки подлинности типа OAuth2 Bearer. В следующем примере для иллюстрации используется интерактивная проверка подлинности, но для большинства производственных сценариев, требующих автоматической или автономной проверки подлинности, необходимо использовать проверку подлинности субъекта-службы, как [описано в этой записной книжке](https://aka.ms/pl-restep-auth).

Проверка подлинности субъекта-службы включает в себя создание *регистрации приложения* в *Azure Active Directory*. Сначала необходимо создать секрет клиента, а затем предоставить субъекту-службе *доступ роли* к вашей рабочей области машинного обучения. Используйте класс [`ServicePrincipalAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?view=azure-ml-py) для управления потоком проверки подлинности. 

`InteractiveLoginAuthentication` и `ServicePrincipalAuthentication` наследуются от `AbstractAuthentication`. В обоих случаях используйте функцию `get_authentication_header()` для извлечения заголовка:

```python
from azureml.core.authentication import InteractiveLoginAuthentication

interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()
```

Получите URL-адрес REST из свойства `endpoint` объекта опубликованного конвейера. Кроме того, URL-адрес REST можно найти в рабочей области в студии машинного обучения Azure. 

Создайте запрос HTTP POST к конечной точке. Укажите заголовок проверки подлинности в запросе. Добавьте объект полезных данных JSON с именем эксперимента и параметром размера пакета. Как уже говорилось ранее в руководстве, `param_batch_size` передается в ваш скрипт `batch_scoring.py`, потому что вы определили его как объект `PipelineParameter` в конфигурации шага.

Выполните запрос на активацию запуска. Включите код для доступа к ключу `Id` из словаря ответов, чтобы получить значение идентификатора запуска.

```python
import requests

rest_endpoint = published_pipeline.endpoint
response = requests.post(rest_endpoint, 
                         headers=auth_header, 
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"param_batch_size": 50}})
run_id = response.json()["Id"]
```

Используйте его для отслеживания состояния нового запуска. Для завершения нового запуска требуется еще 10–15 минут. 

Новый запуск будет выглядеть аналогично конвейеру, который вы запустили ранее в этом руководстве. Вы можете не просматривать все выходные данные.

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.widgets import RunDetails

published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)
RunDetails(published_pipeline_run).show()
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Не выполняйте инструкции из этого раздела, если вы собираетесь изучать другие руководства по Машинному обучению Azure.

### <a name="stop-the-compute-instance"></a>Остановка экземпляра для вычислений

[!INCLUDE [aml-stop-server](../../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Удаление всех ресурсов

Если вы не планируете использовать созданные ресурсы, удалите их, чтобы с вас не взималась плата.

1. На портале Azure в меню слева выберите **Группы ресурсов**.
1. В списке групп ресурсов выберите созданную группу ресурсов.
1. Выберите **Удалить группу ресурсов**.
1. Введите имя группы ресурсов. Затем нажмите кнопку **Удалить**.

Вы также можете сохранить группу ресурсов, но удалить одну рабочую область. Отобразите свойства рабочей области и нажмите кнопку **Удалить**.

## <a name="next-steps"></a>Дополнительная информация

Изучив это руководство о конвейерах машинного обучения, вы выполнили следующие задачи:

> [!div class="checklist"]
> * Создание конвейера с зависимостями среды для запуска в удаленном ресурсе вычислений GPU.
> * Создание скрипта оценки для выполнения пакетных прогнозирований с помощью предварительно обученной модели Tensorflow.
> * Публикация конвейера и его включение для запуска с конечной точки REST.

Дополнительные примеры создания конвейеров с помощью пакета SDK для машинного обучения см. в [репозитории записных книжек](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines).
