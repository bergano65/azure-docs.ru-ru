---
title: Визуализация экспериментов с помощью TensorBoard
titleSuffix: Azure Machine Learning
description: Запустите TensorBoard для визуализации журналов запуска экспериментов и выявления потенциальных областей для настройки и повторного обучения параметров.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: maxluk
ms.author: maxluk
ms.date: 11/08/2019
ms.openlocfilehash: fc8159b3deba373948f513cb11540695362ecaf1
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954566"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>Визуализируйте запуски и метрики экспериментов с помощью TensorBoard и Машинное обучение Azure
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Из этой статьи вы узнаете, как просматривать запуски и метрики экспериментов в TensorBoard с помощью [пакета `tensorboard`](https://docs.microsoft.com/python/api/azureml-tensorboard/?view=azure-ml-py) в основном машинное обучение Azure SDK. Проверив запуски экспериментов, вы сможете лучше настраивать и переучить модели машинного обучения.

[TensorBoard](https://www.tensorflow.org/tensorboard/r1/overview) — это набор веб-приложений для изучения и понимания структуры и производительности экспериментов.

Способ запуска TensorBoard с Машинное обучение Azure экспериментами зависит от типа эксперимента.
+ Если ваш эксперимент самостоятельно выводит файлы журналов, которые могут использоваться TensorBoard, например PyTorch, Chain и TensorFlow, вы можете [запустить TensorBoard непосредственно](#direct) из журнала выполнения эксперимента. 

+ Для экспериментов, которые не выводят нестандартно TensorBoard файлы, например Scikit-учиться или Машинное обучение Azure, используйте [метод `export_to_tensorboard()`](#export) для экспорта журналов выполнения в качестве TensorBoard и запуска TensorBoard из него. 

> [!TIP]
> Сведения в этом документе предназначены главным образом для специалистов по обработке и анализу данных и разработчиков, желающих отслеживать процесс обучения модели. Если вы являетесь администратором, который заинтересован в наблюдении за использованием ресурсов и событиями из машинного обучения Azure, таких как квоты, завершенные обучающие запуски или завершенные развертывания моделей, см. раздел [мониторинг машинное обучение Azure](monitor-azure-machine-learning.md).

## <a name="prerequisites"></a>предварительным требованиям

* Чтобы запустить TensorBoard и просмотреть журналы запуска экспериментов, в экспериментах необходимо включить ранее ведение журнала для контроля метрик и производительности.  

* Код в этом документе можно запустить в любой из следующих сред: 

    * Машинное обучение Azure виртуальной машины записной книжки — Загрузка или установка не требуется

        * Выполните инструкции из [учебника Настройка среды и рабочей области](tutorial-1st-experiment-sdk-setup.md) , чтобы создать выделенный сервер записной книжки, предварительно загруженный с помощью пакета SDK и примера репозитория.

        * В папке Samples на сервере записной книжки найдите две готовые и расширенные записные книжки, перейдя к следующим каталогам:
            * **практические рекомендации по использованию azureml > Training-with глубокое обучение > Export-Run-History-to-tensorboard > Export-Run-History-to-tensorboard. ipynb**

            * **как использовать > отслеживания и мониторинга с помощью azureml > tensorboard. ipynb**

    * Ваш собственный сервер записных книжек Жуптер
       * [Установка пакета SDK для машинное обучение Azure](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) с `tensorboard` дополнительными
        * [Создайте рабочую область Машинного обучения Azure](how-to-manage-workspace.md).  
        * [Создайте файл конфигурации рабочей области](how-to-configure-environment.md#workspace).
  
<a name="direct"></a>

## <a name="option-1-directly-view-run-history-in-tensorboard"></a>Вариант 1. Просмотр журнала выполнения непосредственно в TensorBoard

Этот параметр работает для экспериментов, которые изначально выводят файлы журналов, используемые TensorBoard, такие как PyTorch, формирователь и эксперименты TensorFlow. Если это не так в эксперименте, используйте вместо этого [метод `export_to_tensorboard()`](#export) .

В следующем примере кода используется [демонстрационный эксперимент MNIST](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py) из репозитория TensorFlow в удаленном целевом объекте вычислений, машинное обучение Azure вычислений. Затем мы обучили модель с помощью настраиваемого средства [оценки TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)пакета SDK, а затем начнем TensorBoard с этого TensorFlow эксперимента, то есть эксперимента, который изначально выводит файлы событий TensorBoard.

### <a name="set-experiment-name-and-create-project-folder"></a>Задание имени эксперимента и папки создания проекта

Здесь мы наименимся экспериментом и создадим его папку. 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>Скачать демонстрационный код эксперимента TensorFlow

В репозитории TensorFlow есть демонстрационная версия MNIST с расширенными инструментами TensorBoard. Для работы с Машинное обучение Azure не нужно вносить изменения в код этой демонстрационной версии. В следующем коде мы скачиваем код MNIST и сохраняем его в созданной папке эксперимента.

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
В файле кода MNIST mnist_with_summaries. Корректировка Обратите внимание, что существуют строки, вызывающие `tf.summary.scalar()`, `tf.summary.histogram()``tf.summary.FileWriter()` и т. д. Эти методы группируют, заменяют и помечают ключевые метрики экспериментов в журнале выполнения. `tf.summary.FileWriter()` особенно важно, так как он сериализует данные из зарегистрированных вами метрик эксперимента, что позволяет TensorBoard создавать визуализации.

 ### <a name="configure-experiment"></a>Настройка эксперимента

Ниже мы настроим наш эксперимент и настроили каталоги для журналов и данных. Эти журналы будут отправлены в службу артефактов, которая TensorBoard к ним доступ позже.

>[!Note]
> В этом примере TensorFlow необходимо установить TensorFlow на локальном компьютере. Кроме того, модуль TensorBoard (то есть тот, который входит в состав TensorFlow) должен быть доступен для ядра этой записной книжки, так как на локальном компьютере выполняется TensorBoard.

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
Мы создадим кластер Амлкомпуте для этого эксперимента, но эксперименты можно создавать в любой среде, и вы по-прежнему можете запускать TensorBoard в журнале выполнения экспериментов. 

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

### <a name="submit-run-with-tensorflow-estimator"></a>Отправка выполнения с помощью средства оценки TensorFlow

Средство оценки TensorFlow предоставляет простой способ запуска задания обучения TensorFlow на целевом объекте вычислений. Он реализуется с помощью универсального класса [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) , который можно использовать для поддержки любой платформы. Дополнительные сведения о учебных моделях с помощью универсального средства оценки см. в статье [обучение моделей с машинное обучение Azure помощью средства оценки](how-to-train-ml-models.md) .

```Python
from azureml.train.dnn import TensorFlow
script_params = {"--log_dir": "./logs"}

tf_estimator = TensorFlow(source_directory=exp_dir,
                          compute_target=compute_target,
                          entry_script='mnist_with_summaries.py',
                          script_params=script_params)

run = exp.submit(tf_estimator)
```

### <a name="launch-tensorboard"></a>Запустить TensorBoard

Вы можете запустить TensorBoard во время выполнения или после завершения. В следующем примере создается экземпляр объекта TensorBoard, `tb`, который принимает журнал запуска эксперимента, загруженный в `run`, а затем запускает TensorBoard с помощью метода `start()`. 
  
[Конструктор TensorBoard](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py) принимает массив запусков, поэтому убедитесь, что он передается в виде одноэлементного массива.

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>Вариант 2. Экспорт журнала в качестве журнала для просмотра в TensorBoard

Следующий код настраивает пример эксперимента, начинает процесс ведения журнала с помощью Машинное обучение Azure интерфейсов API журнала выполнения и экспортирует журнал запуска эксперимента в журналы, которые можно использовать в TensorBoard для визуализации. 

### <a name="set-up-experiment"></a>Настройка эксперимента

Следующий код настраивает новый эксперимент и называет каталог Run `root_run`. 

```python
from azureml.core import Workspace, Experiment
import azuremml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

Здесь мы загружаем набор данных диабета — встроенный небольшой набор данных, который поставляется вместе с scikit-учиться, и разбивает его на наборы тестов и обучения.

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

### <a name="run-experiment-and-log-metrics"></a>Запуск эксперимента и запись метрик журнала

Для этого кода мы обучить модель линейной регрессии и метрики ключа журнала, альфа-коэффициент, `alpha`и среднее значение ошибки в квадрате, `mse`в журнале выполнения.

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

### <a name="export-runs-to-tensorboard"></a>Запуски экспорта в TensorBoard

С помощью метода [export_to_tensorboard ()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.export?view=azure-ml-py) пакета SDK можно экспортировать журнал выполнения эксперимента машинного обучения Azure в журналы tensorboard, чтобы мы могли просматривать их через tensorboard.  

В следующем коде мы создадим папку `logdir` в текущем рабочем каталоге. В этой папке мы экспортируем историю выполнения эксперимента и журналы из `root_run`, а затем помечайте выполнение как завершенное. 

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
 Вы также можете экспортировать определенный запуск в TensorBoard, указав имя запуска `export_to_tensorboard(run_name, logdir)`

### <a name="start-and-stop-tensorboard"></a>Запуск и завершение TensorBoard
После экспорта журнала выполнения для этого эксперимента можно запустить TensorBoard с помощью метода [Start ()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#start-start-browser-false-) . 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

Когда все будет готово, обязательно вызовите метод [останавливаюте ()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#stop--) объекта TensorBoard. В противном случае TensorBoard будет продолжать работать, пока не завершится работа ядра записной книжки. 

```python
tb.stop()
```

## <a name="next-steps"></a>Дополнительная информация

В этом пошаговом окне вы создали два эксперимента и узнали, как запустить TensorBoard в своих журналах выполнения, чтобы найти области для возможной настройки и повторного обучения. 

* Если вы удовлетворены вашей моделью, перейдите к статье [развертывание модели](how-to-deploy-and-where.md) . 
* Дополнительные сведения о [настройке параметров](how-to-tune-hyperparameters.md). 
