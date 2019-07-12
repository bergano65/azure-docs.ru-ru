---
title: Визуализация экспериментов с помощью TensorBoard
titleSuffix: Azure Machine Learning service
description: Запуск TensorBoard визуализировать историю запуска эксперимента и определения возможных областей гиперпараметров, настройки и повторного обучения.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: maxluk
ms.author: maxluk
ms.date: 06/28/2019
ms.openlocfilehash: fde2b6d1d298e89227951c376d584452fbff2679
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707049"
---
# <a name="visualize-experiment-runs-and-metrics-with-tensorboard-and-azure-machine-learning"></a>Визуализация экспериментов и метрик с помощью TensorBoard и машинного обучения Azure

В этой статье вы узнаете, как для представления нескольких запусков эксперимента и метрики с помощью TensorBoard [ `tensorboard` пакета](https://docs.microsoft.com/python/api/azureml-tensorboard/?view=azure-ml-py) основной машинного обучения Azure службы SDK. После нескольких запусков эксперимента проверен, может лучше настраивать и переобучение моделей машинного обучения.

[TensorBoard](https://www.tensorflow.org/tensorboard/r1/overview) — это набор веб-приложений для изучения и понимания структуры эксперимента и производительности.

Как запустить TensorBoard с эксперименты машинного обучения Azure зависит от типа эксперимента:
+ Если эксперимент в собственном коде выводит файлы журналов, которые могут использоваться TensorBoard, например PyTorch, Chainer и TensorFlow эксперименты, то вы можете [непосредственный запуск TensorBoard](#direct) журнал выполнения из эксперимента. 

+ Для экспериментов, которые не выводят изначально TensorBoard готовых к использованию файлов, таких как Scikit-learn или Azure экспериментов машинного обучения, используйте [ `export_to_tensorboard()` метод](#export) экспортировать журналы выполнения в виде журналов TensorBoard и запустить TensorBoard оттуда. 

## <a name="prerequisites"></a>предварительные требования

* Для запуска TensorBoard и Просмотр журналов выполнения эксперимент, эксперименты нужно было включено ведение журнала для отслеживания его метрики и производительность.  

* Код в этом практическом руководстве можно запустить одним из следующих средах: 

    * Machine Learning записные книжки Azure виртуальной Машины — нет файлы для загрузки или установки не требуется

        * Завершить [быстрого запуска облачной записной книжки](quickstart-run-cloud-notebook.md#create-notebook) создание специальных ноутбуков сервера предварительно загруженным с помощью пакета SDK и репозиторий с примером.

        * В папке samples на сервере записной книжки, найти два завершена и развернут записных книжек, перейдя к этому каталогу: **практические-в-использование azureml > обучения с помощью глубокого обучения**.
        * export-Run-History-to-Run-History.ipynb
        * tensorboard.ipynb

    * Сервер записной книжки Juptyer
      * Используйте [Создание рабочей области статьи](setup-create-workspace.md) для
          * [Установка пакета SDK для Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) с `tensorboard` дополнительный
          * Создание рабочей области и соответствующий файл конфигурации (config.json)
  
<a name="direct"></a>
## <a name="option-1-directly-view-run-history-in-tensorboard"></a>Вариант 1. Непосредственно в представлении журнала выполнения в TensorBoard

Этот параметр работает для экспериментов, что изначально выходные данные журналов готовых к использованию с TensorBoard, например PyTorch, Chainer и TensorFlow эксперименты. Если это не так эксперимента, используйте [ `export_to_tensorboard()` метод](#export) вместо этого.

В следующем примере кода используется [MNIST Демонстрация эксперимента](https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py) из репозитория TensorFlow в удаленном целевом объекте вычислений, вычислений машинного обучения Azure. Далее мы обучить модель с помощью пакета SDK для пользовательского [TensorFlow estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py), и запустите TensorBoard для этого эксперимента TensorFlow, то есть эксперимент, который изначально выводит файлы TensorBoard событий.

### <a name="set-experiment-name-and-create-project-folder"></a>Задайте имя эксперимента и создайте папку проекта

Здесь мы имя эксперимента и создайте его папку. 
 
```python
from os import path, makedirs
experiment_name = 'tensorboard-demo'

# experiment folder
exp_dir = './sample_projects/' + experiment_name

if not path.exists(exp_dir):
    makedirs(exp_dir)

```

### <a name="download-tensorflow-demo-experiment-code"></a>Скачайте TensorFlow демонстрационного кода в эксперимент

TensorFlow в репозиторий имеет Демонстрация MNIST с обширной TensorBoard инструментирования. Мы этого не сделать, а также потребуется изменить код в этой демонстрации для работы со службой машинного обучения Azure. В следующем коде Загрузите код MNIST и сохраните его в папке только что созданный эксперимент.

```python
import requests
import os

tf_code = requests.get("https://raw.githubusercontent.com/tensorflow/tensorflow/r1.8/tensorflow/examples/tutorials/mnist/mnist_with_summaries.py")
with open(os.path.join(exp_dir, "mnist_with_summaries.py"), "w") as file:
    file.write(tf_code.text)
```
В файле кода MNIST mnist_with_summaries.py, обратите внимание, что "строки" после этого вызова `tf.summary.scalar()`, `tf.summary.histogram()`, `tf.summary.FileWriter()` и т.д. Эти группы методов, журнала и тег ключевые метрики в журнал выполнения экспериментов. `tf.summary.FileWriter()` Особенно важно, так как он сериализует данные из журнала эксперимента метрики, позволяющий TensorBoard позволяет получить визуализации от них.

 ### <a name="configure-experiment"></a>Настройка эксперимента

Далее мы наш эксперимент и настройкой каталоги для журналов и данных. Эти журналы будет отправлен в службу артефакта TensorBoard обращается к более поздней версии.

>[!Note]
> В этом примере TensorFlow необходимо будет установить TensorFlow на локальном компьютере. Кроме того модуль TensorBoard (то есть входит в состав TensorFlow) должен быть доступен для ядра для записной книжки, как локальный компьютер — что работает TensorBoard.

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

### <a name="create-a-cluster-for-your-experiment"></a>Создание кластера для своего эксперимента
Как создать кластер AmlCompute для этого эксперимента, однако эксперимента можно создать в любой среде, и вы по-прежнему запуск TensorBoard от журнал выполнения эксперимента. 

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

### <a name="submit-run-with-tensorflow-estimator"></a>Отправить выполнения с механизмом оценки TensorFlow

Оценщик TensorFlow предоставляет простой способ запуска задание обучения TensorFlow в целевой объект вычислений. Он реализуется с помощью универсального [ `estimator` ](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) класс, который может использоваться для поддержки любой платформы. Дополнительные сведения об обучении моделей с помощью универсальный механизм оценки, см. в разделе [обучения моделей с машинным обучением Azure, с помощью механизма оценки](how-to-train-ml-models.md)

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

TensorBoard можно запустить во время выполнения или после ее завершения. Далее мы создаем экземпляр объекта TensorBoard `tb`, что принимает в журнал, загрузить в выполнения эксперимента `run`и затем запускает TensorBoard с `start()` метод. 
  
[TensorBoard конструктор](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py) принимает массив запусков, поэтому убедитесь, что и передать его в виде одного элемента массива.

```python
from azureml.tensorboard import Tensorboard

tb = Tensorboard([run])

# If successful, start() returns a string with the URI of the instance.
tb.start()

# After your job completes, be sure to stop() the streaming otherwise it will continue to run. 
tb.stop()
```

<a name="export"></a>

## <a name="option-2-export-history-as-log-to-view-in-tensorboard"></a>Вариант 2. Экспортировать журнал в виде журнала для просмотра в TensorBoard

Следующий код устанавливает пример эксперимента, начинает процесс ведения журнала, с помощью машинного обучения Azure, API-интерфейсы журнал выполнения и экспортирует журнал был выполнения в журналы готовых к использованию с TensorBoard для визуализации эксперимента. 

### <a name="set-up-experiment"></a>Настроить эксперимент

Следующий код устанавливает новый эксперимент и называет каталог выполнения `root_run`. 

```python
from azureml.core import Workspace, Experiment
import azuremml.core

# set experiment name and run name
ws = Workspace.from_config()
experiment_name = 'export-to-tensorboard'
exp = Experiment(ws, experiment_name)
root_run = exp.start_logging()
```

Здесь мы загружаем набор данных пима--Встроенные небольшой набор данных, входящий в состав scikit-Узнайте и разделить ее на тест и обучающих наборов.

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

### <a name="run-experiment-and-log-metrics"></a>Запустите эксперимент и журнала метрик

Для этого кода мы обучили модель линейной регрессии и журналов ключевые метрики, коэффициент альфа- `alpha` и среднеквадратическая погрешность `mse`, в журнал выполнения.

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

### <a name="export-runs-to-tensorboard"></a>Экспорт выполняется для TensorBoard

С помощью пакета SDK [export_to_tensorboard()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.export?view=azure-ml-py) метод, можно экспортировать журнал выполнения эксперимента машинного обучения Azure в TensorBoard журналы, чтобы их можно просматривать с помощью TensorBoard.  

В следующем коде, мы создадим папку `logdir` в наших текущий рабочий каталог. Эта папка является, где мы экспортируем нашего эксперимента, журнал выполнения и журналов из `root_run` и пометить работы как завершенные. 

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
 Можно также экспортировать одного определенного выполнения TensorBoard путем указания имени в сеансе  `export_to_tensorboard(run_name, logdir)`

Start и stop TensorBoard раза, экспортируются наш журнал выполнения для этого эксперимента, можно запустить TensorBoard с [start()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#start-start-browser-false-) метод. 

```Python
from azureml.tensorboard import Tensorboard

# The TensorBoard constructor takes an array of runs, so be sure and pass it in as a single-element array here
tb = Tensorboard([], local_root=logdir, port=6006)

# If successful, start() returns a string with the URI of the instance.
tb.start()
```

Когда все будет готово, не забудьте вызвать [stop()](https://docs.microsoft.com/python/api/azureml-tensorboard/azureml.tensorboard.tensorboard?view=azure-ml-py#stop--) метод TensorBoard объекта. В противном случае TensorBoard будет продолжать выполняться вплоть до завершения работы ядро записной книжки. 

```python
tb.stop()
```

## <a name="next-steps"></a>Следующие шаги

В этой инструкции, создается два экспериментов и узнали, как запуск TensorBoard по свои журналы выполнения для определения областей возможные настройки и повторного обучения. 

* Если вы удовлетворены результатами своей модели, перейдите к нашей [развертывание модели](how-to-deploy-and-where.md) статьи. 
* Дополнительные сведения о [настройки гиперпараметров](how-to-tune-hyperparameters.md). 
