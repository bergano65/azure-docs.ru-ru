---
title: Руководство по использованию конвейеров Машинного обучения Azure для пакетной оценки
titleSuffix: Azure Machine Learning
description: Создайте конвейер Машинного обучения для выполнения пакетной оценки на основе модели классификации изображений. Контейнеры Машинного обучения оптимизируют ваш рабочий процесс за счет ускорения, мобильности и повторного использования, позволив вам сосредоточиться непосредственно на задачах обучения, а не на сложностях инфраструктуры.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 09/05/2019
ms.openlocfilehash: 15a11ba74262ec5a354f0cb3fe22c09167c8d5a6
ms.sourcegitcommit: d15b23e23328ce7502dd3d2846b49fd2d6d8209c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/16/2019
ms.locfileid: "71005392"
---
# <a name="use-azure-machine-learning-pipelines-for-batch-scoring"></a>Использование конвейеров Машинного обучения Azure для пакетной оценки

В рамках этого руководства вы будете использовать конвейеры Машинного обучения Azure для выполнения заданий пакетной оценки. В этой статье для классификации изображений без метки используется [Inception-V3](https://arxiv.org/abs/1512.00567), предварительно обученная модель сверточной нейронной сети TensorFlow. После создания и публикации конвейера необходимо настроить конечную точку REST, позволяющую активировать конвейер с любой библиотеки HTTP на любой платформе.

Контейнеры Машинного обучения оптимизируют ваш рабочий процесс за счет ускорения, мобильности и повторного использования, позволив вам сосредоточиться непосредственно на задачах обучения, а не на сложностях инфраструктуры. [Ознакомьтесь с дополнительными сведениями о конвейерах Машинного обучения](concept-ml-pipelines.md).

В этом руководстве описано, как выполнять такие задачи:

> [!div class="checklist"]
> * Настройка рабочей области и скачивание образцов данных.
> * Создание объектов данных для извлечения и вывода данных.
> * Скачивание, подготовка и регистрация модели в рабочей области.
> * Подготовка целевых объектов вычислений и создание сценария оценки.
> * Создание, запуск и публикация конвейера.
> * Включение конечной точки REST для конвейера.

Если у вас еще нет подписки Azure, создайте бесплатную учетную запись Azure, прежде чем начинать работу. Опробуйте [бесплатную или платную версию Машинного обучения Azure](https://aka.ms/AMLFree) уже сегодня.

## <a name="prerequisites"></a>Предварительные требования

* Выполните инструкции, приведенные в [первой части руководства по установке](tutorial-1st-experiment-sdk-setup.md), если у вас еще нет рабочей области Машинного обучения Azure или записной книжки виртуальной машины.
* Завершив установку, откройте записную книжку**tutorials/tutorial-pipeline-batch-scoring-classification.ipynb**, используя тот же сервер записной книжки.

Это руководство также доступно на сайте [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials), если вы хотите запустить его в собственной [локальной среде](how-to-configure-environment.md#local). Выполните команду `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-pipeline-steps pandas requests`, чтобы получить необходимые пакеты.

## <a name="configure-workspace-and-create-datastore"></a>Настройка рабочей области и создание хранилища данных

В имеющейся рабочей области Машинного обучения Azure создайте объект. 
+ Класс [Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) принимает сведения о подписке и ресурсах Azure. Он также создает облачный ресурс для мониторинга и отслеживания работы модели. 

+ `Workspace.from_config()` считывает файл **config.json** и загружает сведения о проверке подлинности в объект с именем `ws`. `ws` используется в остальном коде в этом руководстве.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

### <a name="create-a-datastore-for-sample-images"></a>Создание хранилища данных для образцов изображений

Извлеките образец общедоступных данных вычисления ImageNet из общедоступного контейнера BLOB-объектов `sampledata` в учетной записи `pipelinedata`. Выполнив вызов `register_azure_blob_container()`, вы предоставите доступ рабочей области `images_datastore` к данным. Затем в качестве выходного хранилища данных, используемого для оценки выходных данных в конвейере, укажите хранилище данных рабочей области по умолчанию.

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

При создании конвейера объекты `DataReference` используются для чтения данных из хранилищ данных рабочей области, а объекты `PipelineData` — для передачи промежуточных данных между шагами конвейера.

> [!Important]
> В примере пакетной оценки в этой статье используется только один шаг конвейера, но в вариантах использования с несколькими шагами стандартный поток будет включать:
>
> 1. Использование объектов `DataReference` в качестве **входных данных** для извлечения необработанных данных, выполнение определенных преобразований, а затем **вывод** объекта `PipelineData`.
>
> 2. Использование **выходного объекта** `PipelineData` предыдущего шага в качестве *входного объекта* для последующих шагов.

Для этого сценария создайте объекты `DataReference`, соответствующие каталогам хранилища данных для входных изображений и меток классификации (значения y-test). Кроме того, создайте объект `PipelineData` для выходных данных пакетной оценки.

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

Скачайте предварительно обученную модель TensorFlow, чтобы использовать ее для пакетной оценки в конвейере. Сначала создайте локальный каталог, в котором будет храниться модель, а затем скачайте и извлеките ее.

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

Теперь выполните регистрацию модели в рабочей области, что позволит быстро извлекать ее в процессе конвейера. В статической функции `register()` параметр `model_name` является ключом, который используется для определения расположения модели в пакете SDK.

```python
from azureml.core.model import Model
 
model = Model.register(model_path="models/inception_v3.ckpt",
                       model_name="inception",
                       tags={"pretrained": "inception"},
                       description="Imagenet trained tensorflow inception",
                       workspace=ws)
```

## <a name="create-and-attach-remote-compute-target"></a>Создание и вложение удаленного целевого объекта вычислений

Так как конвейеры Машинного обучения невозможно запустить локально, запустите их в ресурсах облака. Мы называем их удаленными целевыми объектами вычислений, которые представляют собой виртуальные вычислительные среды многократного использования, в которых можно запускать эксперименты и рабочие процессы Машинного обучения. Выполните приведенный ниже код, чтобы создать целевой объект [`AmlCompute`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) с поддержкой GPU, и вложите его в свою рабочую область. Дополнительные сведения о целевых объектах вычислений см. в [тематической статье](https://docs.microsoft.com/azure/machine-learning/service/concept-compute-target).


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

Для выполнения оценки необходимо создать сценарий пакетной оценки `batch_scoring.py` и записать его в текущий каталог. Сценарий принимает входные изображения, применяет модель классификации и выводит прогнозы в файл результатов.

Сценарий `batch_scoring.py` принимает приведенные ниже параметры, которые передаются из шага конвейера, который вы создадите позже:

- `--model_name` — имя используемой модели.
- `--label_dir` — каталог, содержащий файл `labels.txt`. 
- `--dataset_path` — каталог, содержащий входные изображения.
- `--output_dir` — каталог, в который сценарий выведет `results-label.txt`, запустив модель с данными.
- `--batch_size` — размер пакета, используемый при запуске модели.

Инфраструктура конвейеров использует класс `ArgumentParser` для передачи параметров в шаги конвейера. Например, в приведенном ниже коде первый аргумент `--model_name` получает идентификатор свойства `model_name`. В функции `main()` доступ к этому свойству осуществляется с помощью `Model.get_model_path(args.model_name)`.


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
> Конвейер в этом руководстве имеет только один шаг и записывает выходные данные в файл. Для конвейеров с несколькими шагами используете `ArgumentParser`, чтобы определить каталог для записи выходных данных для ввода на последующих этапах. Пример передачи данных между несколькими шагами конвейера с использованием конструктивного шаблона `ArgumentParser` см. в [записной книжке](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb).

## <a name="build-and-run-the-pipeline"></a>Создание и запуск конвейера

Перед запуском конвейера создайте объект, который определяет среду Python и зависимости, необходимые для вашего сценария `batch_scoring.py`. Основной зависимостью является TensorFlow, но кроме нее установите `azureml-defaults` для фоновых процессов из пакета SDK. Создайте объект `RunConfiguration` с помощью зависимостей, а также укажите поддержку Docker и Docker-GPU.

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

Определите пользовательский параметр для конвейера, чтобы управлять размером пакета. После публикации конвейера и получению к нему доступа через конечную точку REST извлекаются все настроенные параметры. Они могут быть указаны в полезных данных JSON при повторном запуске конвейера с помощью HTTP-запроса.

Создайте объект `PipelineParameter`, чтобы включить это поведение, и определите имя и значение по умолчанию.

```python
from azureml.pipeline.core.graph import PipelineParameter
batch_size_param = PipelineParameter(name="param_batch_size", default_value=20)
```

### <a name="create-the-pipeline-step"></a>Создание шага конвейера

Шаг конвейера — это объект, который инкапсулирует все необходимое для запуска конвейера, включая:

* параметры среды и зависимостей;
* вычислительный ресурс для запуска конвейера;
* входные и выходные данные и любые пользовательские параметры;
* ссылку на сценарий или логику пакета SDK для запуска во время выполнения шага.

Существует ряд классов, наследуемых от родительского класса [`PipelineStep`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py) для помощи в создании шага с использованием определенных платформ и стеков. В этой статье используется класс [`PythonScriptStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) для определения логики шага с помощью пользовательского сценария Python. Обратите внимание, если аргумент вашего сценария является вводом или выводом шага, его необходимо определить как **both** в массиве `arguments`, **as well as** в параметре `input` или `output` соответственно. 

Ссылка на объект в массиве `outputs` становится доступной в качестве **входных данных** для последующего шага конвейера в сценариях с несколькими шагами.

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

Теперь необходимо запустить конвейер. Сначала создайте объект `Pipeline` с ссылкой на вашу рабочую область и созданный шаг конвейера. Параметр `steps` является массивом шагов, и в данном случае для пакетной оценки существует лишь один шаг. Чтобы создать конвейеры с несколькими шагами, разместите шаги по порядку в этом массиве.

Затем используйте функцию `Experiment.submit()`, чтобы передать конвейер для выполнения. Кроме того, укажите пользовательский параметр `param_batch_size`. Функция `wait_for_completion` будет выводить журналы в процессе создания конвейера, благодаря чему вы сможете просматривать текущие сведения о ходе выполнения.

> [!IMPORTANT]
> Первый запуск конвейера длится примерно **15 минут**, так как необходимо загрузить все зависимости, создать образ Docker и подготовить или создать среду Python. Следующий запуск занимает значительно меньше времени, так как эти ресурсы будут использоваться повторно. Однако общее время запуска зависит от рабочей нагрузки ваших сценариев и процессов, выполняемых в каждом шаге конвейера.

```python
from azureml.core import Experiment
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline, pipeline_parameters={"param_batch_size": 20})
pipeline_run.wait_for_completion(show_output=True)
```

### <a name="download-and-review-output"></a>Скачивание и просмотр выходных данных

Запустите приведенный ниже код, чтобы загрузить выходной файл, созданный из сценария `batch_scoring.py`, а затем изучите результаты оценки.

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

## <a name="publish-and-run-from-rest-endpoint"></a>Публикация и запуск из конечной точки REST

Выполните приведенный ниже код, чтобы опубликовать конвейер в рабочей области. В своей рабочей области на портале вы можете просмотреть метаданные для конвейера, включая журнал выполнения и продолжительность. Вы также можете запустить конвейер вручную на портале.

Кроме того, публикация конвейера позволяет конечной точке REST повторно запускать конвейер из любой библиотеки HTTP на любой платформе.

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", description="Batch scoring using Inception v3 model", version="1.0")

published_pipeline
```

Чтобы запустить конвейер из конечной точки REST, вам сначала потребуется заголовок проверки подлинности типа OAuth2 Bearer. В этом примере для иллюстрации используется интерактивная проверка подлинности, но для большинства производственных сценариев, требующих автоматической или автономной проверки подлинности, необходимо использовать проверку подлинности службы-участника, как [описано в этой записной книжке](https://aka.ms/pl-restep-auth).

Проверка подлинности службы-участника включает создание **регистрации приложения** в **Azure Active Directory**, создание секрета клиента, а затем предоставление рабочей области машинного обучения **доступа на основе ролей** к субъекту службе. Затем используйте класс [`ServicePrincipalAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?view=azure-ml-py) для управления потоком проверки подлинности. 

`InteractiveLoginAuthentication` и `ServicePrincipalAuthentication` наследуются от `AbstractAuthentication`, и в обоих случаях для извлечения заголовка используется функция `get_authentication_header()`.

```python
from azureml.core.authentication import InteractiveLoginAuthentication

interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()
```

Получите URL-адрес REST из свойства `endpoint` объекта опубликованного конвейера. Кроме того, URL-адрес REST можно найти в рабочей области на портале. Создайте запрос HTTP POST к конечной точке, указав заголовок проверки подлинности. Кроме того, добавьте объект полезных данных JSON с именем эксперимента и параметром размера пакета. Напоминаем, что `param_batch_size` передается в ваш сценарий `batch_scoring.py`, потому что вы определили его как объект `PipelineParameter` в конфигурации шага.

Выполните запрос на активацию запуска. Получите доступ к ключу `Id` из словаря ответов, чтобы получить значение идентификатора запуска.

```python
import requests

rest_endpoint = published_pipeline.endpoint
response = requests.post(rest_endpoint, 
                         headers=auth_header, 
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"param_batch_size": 50}})
run_id = response.json()["Id"]
```

Используйте его для отслеживания состояния нового запуска. Этот запуск займет 10–15 минут, как и предыдущий запуск конвейера, поэтому, если вам не нужно выполнять другой запуск конвейера, вы можете пропустить просмотр полных результатов выполнения.

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.widgets import RunDetails

published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)
RunDetails(published_pipeline_run).show()
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Не выполняйте инструкции из этого раздела, если вы собираетесь изучать другие руководства по Машинному обучению Azure.

### <a name="stop-the-notebook-vm"></a>Остановка работы виртуальной машины записных книжек

Если вы использовали облачный сервер записных книжек, завершите работу неиспользуемой виртуальной машины, чтобы сократить расходы.

1. Выберите в своей рабочей области **Виртуальные машины записных книжек**.
1. Выберите пользователя из списка.
1. Выберите **Остановить**.
1. Когда вам снова понадобится использовать сервер, выберите **Запустить**.

### <a name="delete-everything"></a>Удаление всех ресурсов

Если вы не планируете использовать созданные ресурсы, удалите их, чтобы с вас не взималась плата.

1. На портале Azure выберите **Группы ресурсов** в левой части окна.
1. В списке выберите созданную группу ресурсов.
1. Выберите **Удалить группу ресурсов**.
1. Введите имя группы ресурсов. Теперь щелкните **Удалить**.

Вы также можете сохранить группу ресурсов, но удалить одну рабочую область. Отобразите свойства рабочей области и нажмите кнопку **Удалить**.

## <a name="next-steps"></a>Дополнительная информация

Изучив это руководство о конвейерах машинного обучения, вы выполнили следующие задачи:

> [!div class="checklist"]
> * Создание конвейера с зависимостями среды для запуска в удаленном ресурсе вычислений GPU.
> * Создание сценария оценки для выполнения пакетных прогнозирований с помощью предварительно обученной модели TensorFlow.
> * Публикация конвейера и включение его для запуска с конечной точки REST.

Дополнительные примеры создания конвейеров с помощью пакета SDK машинного обучения см. в [репозитории записных книжек](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines).
