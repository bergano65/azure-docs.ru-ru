---
title: Визуализация экспериментов с помощью TensorBoard
titleSuffix: Azure Machine Learning
description: Запуск TensorBoard для визуализации истории запуска эксперимента и выявления потенциальных областей для настройки и переподготовки гиперпарапланов.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: maxluk
ms.author: maxluk
ms.date: 02/27/2020
ms.openlocfilehash: b6b7e47acdbc5bd059e17e512731bd09c8580798
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78195385"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>Визуализируйте запуски экспериментов и метрик с помощью TensorBoard и машинного обучения Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье вы узнаете, как просматривать запуски и метрики в TensorBoard с помощью [ `tensorboard` пакета](https://docs.microsoft.com/python/api/azureml-tensorboard/?view=azure-ml-py) в главном SDK по изучению машин Azure. После проверки эксперимента можно лучше настроить и переобучить модели машинного обучения.

[TensorBoard](https://www.tensorflow.org/tensorboard/r1/overview) — это набор веб-приложений для проверки и понимания структуры и производительности эксперимента.

Как вы запускаете TensorBoard с azure machine Learning эксперименты зависит от типа эксперимента:
+ Если ваш эксперимент роднища выводит файлы журнала, которые расходуются Наивным Бордоном, такими как эксперименты PyTorch, Chainer и TensorFlow, то вы можете [запустить TensorBoard непосредственно](#direct) из истории запуска эксперимента. 

+ Для экспериментов, которые не выпускают расходные файлы TensorBoard, такие как Scikit-learn или Эксперименты по машинному обучению Azure, используйте [ `export_to_tensorboard()` метод](#export) для экспорта историй запуска в качестве журналов TensorBoard и запуска оттуда TensorBoard. 

> [!TIP]
> Информация в этом документе в первую очередь предназначена для ученых и разработчиков данных, которые хотят контролировать процесс обучения модели. Если вы являетесь администратором, заинтересованным в мониторинге использования ресурсов и событий из машинного обучения [Monitoring Azure Machine Learning](monitor-azure-machine-learning.md)Azure, таких как квоты, завершенные обучающие заезды или завершенные развертывания моделей, см.

## <a name="prerequisites"></a>Предварительные требования

* Для запуска TensorBoard и просмотра историй запуска эксперимента ваши эксперименты должны иметь ранее включенную систему регистрации для отслеживания метрик и производительности.  

* Код в этом документе может быть запущен в любой из следующих сред: 

    * Экземпляр вычислений машинного обучения Azure - нет необходимости загружать или установку

        * Завершите [обучение: Настройка среды и рабочего пространства](tutorial-1st-experiment-sdk-setup.md) для создания специального ноутбука сервера предварительно загружены с SDK и образец репозитория.

        * В папке образцов на ноутбуке-сервере найдите два завершенных и расширенных ноутбука, перемещаясь по этим каталогам:
            * **как-к-использованию-azureml > обучение-с-глубоко-обучения > экспорт-бег-истории-к-тензорборд > экспорт-бег-истории-к-tensorboard.ipynb**

            * **как использовать-azureml > трек-и-монитор-эксперименты > tensorboard.ipynb**

    * Ваш собственный ноутбук Juptyer сервер
       * [Установка SDK машинного обучения Azure](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) с `tensorboard` дополнительным
        * [Создайте рабочее пространство для машинного обучения Azure.](how-to-manage-workspace.md)  
        * [Создание файла конфигурации рабочего пространства.](how-to-configure-environment.md#workspace)
  
<a name="direct"></a>

## <a name="option-1-directly-view-run-history-in-tensorboard"></a>Вариант 1: Непосредственно просмотреть историю выполнения в TensorBoard

Эта опция работает для экспериментов, которые по-найму выводит файлы журнала, расходуемые Наборту, такие как Эксперименты PyTorch, Chainer и TensorFlow. Если это не так, метод, вместо этого используйте [ `export_to_tensorboard()` метод.](#export)

В следующем примере код использует [демо-эксперимент MNIST](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py) из репозитория TensorFlow в удаленной вычислительной цели Azure Machine Learning Compute. Далее мы обучаем нашу модель с пользовательским [оценщиком TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)sDK, а затем начинаем TensorBoard против этого эксперимента TensorFlow, то есть эксперимента, который роднища выводит файлы событий TensorBoard.

### <a name="set-experiment-name-and-create-project-folder"></a>Установите имя эксперимента и создайте папку проекта

Здесь мы назовем эксперимент и создаем его папку. 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>Скачать код демонстрационных экспериментов TensorFlow

Репозиторий TensorFlow имеет демо-версию MNIST с обширными инструментами TensorBoard. Мы не и не нуждаемся в изменении кода этой демонстрации для работы с Azure Machine Learning. В следующем коде мы загружаем код MNIST и сохраняем его в нашей недавно созданной папке эксперимента.

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
На протяжении mNIST код файл, mnist_with_summaries.py, обратите `tf.summary.scalar()` `tf.summary.histogram()`внимание, что Есть линии, которые называют , и `tf.summary.FileWriter()` т.д. Эти методы группируют, регистрируют и помечают ключевые метрики экспериментов в историю выполнения. Это `tf.summary.FileWriter()` особенно важно, поскольку он сериализирует данные из зарегистрированных метрик эксперимента, что позволяет TensorBoard генерировать визуализации от них.

 ### <a name="configure-experiment"></a>Настройка эксперимента

В следующем мы настраиваем наш эксперимент и настраиваем каталоги для журналов и данных. Эти журналы будут загружены в службу Artifact, к которой позже будет доступ К TensorBoard.

>[!Note]
> Для этого примера TensorFlow вам нужно будет установить TensorFlow на локальной машине. Кроме того, модуль TensorBoard (т.е. тот, который входит в состав TensorFlow) должен быть доступен для ядра этого ноутбука, так как локальная машина является тем, что работает Внештатный борт.

```Python
import azureml.core
from azureml.core import Workspace
from azureml.core import Experiment

ws = Workspace.from_config()

# create directories for experiment logs and dataset
logs_dir = os.path.join(os.curdir, "logs")
data_dir = os.path.abspath(os.path.join(os.curdir, "mnist_data"))

if not path.exists(data_dir):
    makedirs(data_dir)

os.environ["TEST_TMPDIR"] = data_dir

# Writing logs to ./logs results in their being uploaded to Artifact Service,
# and thus, made accessible to our TensorBoard instance.
arguments_list = ["--log_dir", logs_dir]

# Create an experiment
exp = Experiment(ws, experiment_name)
```

### <a name="create-a-cluster-for-your-experiment"></a>Создание кластера для эксперимента
Мы создаем кластер AmlCompute для этого эксперимента, однако ваши эксперименты могут быть созданы в любой среде, и вы все еще можете запустить TensorBoard против истории эксперимента. 

```Python
from azureml.core.compute import ComputeTarget, AmlCompute

cluster_name = "cpucluster"

cts = ws.compute_targets
found = False
if cluster_name in cts and cts[cluster_name].type == 'AmlCompute':
   found = True
   print('Found existing compute target.')
   compute_target = cts[cluster_name]
if not found:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

compute_target.wait_for_completion(show_output=True, min_node_count=None)

# use get_status() to get a detailed status for the current cluster. 
# print(compute_target.get_status().serialize())
```

### <a name="submit-run-with-tensorflow-estimator"></a>Отправить запустить с оценкой TensorFlow

Оценщик TensorFlow предоставляет простой способ запуска учебной работы TensorFlow на вычислительную цель. Он реализован через общий [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) класс, который может быть использован для поддержки любых рамок. Для получения дополнительной информации о обучающих моделях с использованием общего оценщика см. [модели поездов с использованием машинного обучения Azure с использованием оценщика](how-to-train-ml-models.md)

```Python
from azureml.train.dnn import TensorFlow
script_params = {"--log_dir": "./logs"}

tf_estimator = TensorFlow(source_directory=exp_dir,
                          compute_target=compute_target,
                          entry_script='mnist_with_summaries.py',
                          script_params=script_params)

run = exp.submit(tf_estimator)
```

### <a name="launch-tensorboard"></a>Запуск TensorBoard

Вы можете запустить TensorBoard во время выполнения или после его завершения. В следующем, мы создаем экземпляр объекта `tb`TensorBoard, который принимает историю запуска эксперимента, загруженную в `run`, а затем запускаем TensorBoard с помощью метода. `start()` 
  
[Конструктор TensorBoard](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py) принимает массив трасс, так что будьте уверены, и передать его в качестве одного элемента массива.

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

>[!Note]
 В то время как этот пример используется TensorFlow, TensorBoard может быть использован так же легко с PyTorch или Chainer моделей. TensorFlow должен быть доступен на машине под управлением TensorBoard, но не является необходимым на машине, выполняя pyTorch или Chainer вычислений. 


<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>Вариант 2: История экспорта как журнал для просмотра в TensorBoard

Следующий код настраивает пример эксперимента, начинает процесс регистрации с помощью ApIs-истории истории машинного обучения Azure Machine Learning и экспортирует историю запуска эксперимента в журналы, расходуемые TensorBoard для визуализации. 

### <a name="set-up-experiment"></a>Настройка эксперимента

Следующий код настраивает новый эксперимент и `root_run`называет каталог выполнения. 

```python
from azureml.core import Workspace, Experiment
import azuremml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

Здесь мы загружаем набор данных диабета - встроенный небольшой набор данных, который поставляется с scikit-учиться, и разделить его на тест и учебные наборы.

```Python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
X, y = load_diabetes(return_X_y=True)
columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
x_train, x_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=0)
data = {
    "train":{"x":x_train, "y":y_train},        
    "test":{"x":x_test, "y":y_test}
}
```

### <a name="run-experiment-and-log-metrics"></a>Запуск метрик экспериментов и журналов

Для этого кода мы обучаем линейную модель регрессии и `alpha`регистрируем ключевые `mse`метрики, альфа-коэффициент и среднее ошибка в квадрате, в истории запуска.

```Python
from tqdm import tqdm
alphas = [.1, .2, .3, .4, .5, .6 , .7]
# try a bunch of alpha values in a Linear Regression (aka Ridge regression) mode
for alpha in tqdm(alphas):
  # create child runs and fit lines for the resulting models
  with root_run.child_run("alpha" + str(alpha)) as run
 
   reg = Ridge(alpha=alpha)
   reg.fit(data["train"]["x"], data["train"]["y"])    
 
   preds = reg.predict(data["test"]["x"])
   mse = mean_squared_error(preds, data["test"]["y"])
   # End train and eval

# log alpha, mean_squared_error and feature names in run history
   root_run.log("alpha", alpha)
   root_run.log("mse", mse)
```

### <a name="export-runs-to-tensorboard"></a>Экспорт работает в TensorBoard

С помощью [метода export_to_tensorboard()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.export?view=azure-ml-py) SDK мы можем экспортировать историю выполнения нашего эксперимента машинного обучения Azure в журналы TensorBoard, чтобы просмотреть их через TensorBoard.  

В следующем коде мы создаем папку `logdir` в текущем рабочем каталоге. Эта папка, где мы будем экспортировать наш `root_run` эксперимент запустить историю и журналы из, а затем пометить, что запустить как завершено. 

```Python
from azureml.tensorboard.export import export_to_tensorboard
import os

logdir = 'exportedTBlogs'
log_path = os.path.join(os.getcwd(), logdir)
try:
    os.stat(log_path)
except os.error:
    os.mkdir(log_path)
print(logdir)

# export run history for the project
export_to_tensorboard(root_run, logdir)

root_run.complete()
```

>[!Note]
 Вы также можете экспортировать определенный пробег в TensorBoard, указав название запуска`export_to_tensorboard(run_name, logdir)`

### <a name="start-and-stop-tensorboard"></a>Запуск и остановка TensorBoard
После того, как наша история запуска для этого эксперимента экспортируется, мы можем запустить TensorBoard с [помощью метода запуска()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#start-start-browser-false-) 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

Когда вы закончите, убедитесь, что вызов [остановки ()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#stop--) метод объекта TensorBoard. В противном случае TensorBoard будет продолжать работать до тех пор, пока вы не выключите ядро ноутбука. 

```python
tb.stop()
```

## <a name="next-steps"></a>Дальнейшие действия

В этом как-к вам, создал два эксперимента и узнал, как запустить TensorBoard против их запустить истории для выявления областей для потенциальной настройки и переподготовки. 

* Если вы удовлетворены своей моделью, перейдьте к нашей [статье Как развернуть модель.](how-to-deploy-and-where.md) 
* Подробнее о [настройке гиперпараметра.](how-to-tune-hyperparameters.md) 
