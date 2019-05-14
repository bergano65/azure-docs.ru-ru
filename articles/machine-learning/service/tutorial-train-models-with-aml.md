---
title: Руководство по классификации изображений. Обучение моделей
titleSuffix: Azure Machine Learning service
description: Узнайте, как использовать Службу машинного обучения Azure для обучения модели классификации изображений с помощью scikit-learn в записной книжке Jupyter на Python. Это руководство представляет собой первую часть серии, состоящей из двух частей.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 05/08/2019
ms.custom: seodec18
ms.openlocfilehash: 097fb3422ce3868d9ef499ad6c92c8b7fa12e852
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65442061"
---
# <a name="tutorial-train-image-classification-models-with-mnist-data-and-scikit-learn-using-azure-machine-learning"></a>Руководство по Обучение моделей классификации изображений с помощью данных MNIST и scikit-learn в Службе машинного обучения Azure

В этом руководстве необходимо обучить модель машинного обучения на удаленных вычислительных ресурсах. Вы будете использовать рабочий процесс обучения и развертывания для службы "Машинное обучение Azure" (предварительная версия) в Jupyter Notebook для Python.  Затем можно использовать записную книжку как шаблон для обучения собственной модели машинного обучения со своими данными. Это руководство представляет собой **первую часть серии, состоящей из двух частей**.  

Это руководство обучает простую логистическую регрессию по набору данных [MNIST](http://yann.lecun.com/exdb/mnist/) с помощью библиотеки [scikit-learn](https://scikit-learn.org) и Службы машинного обучения Azure. MNIST — это популярный набор данных, состоящий из 70 000 изображений в оттенках серого. Каждое изображение содержит рукописную цифру размером 28 x 28 пикселей, то есть числа от нуля до девяти. Целью является создание многоклассового классификатора для идентификации цифры, которую отображает указанное изображение. 

Узнайте, как выполнять следующие действия:

> [!div class="checklist"]
> * Настройка среды разработки.
> * Подключение к данным и их проверка.
> * Обучайте простую логистическую регрессию на удаленном кластере.
> * Проверка результатов обучения и регистрация наилучшей модели.

Во [второй части этого руководства](tutorial-deploy-models-with-aml.md) описано, как выбрать и развернуть модель. 

Если у вас еще нет подписки Azure, создайте бесплатную учетную запись Azure, прежде чем начинать работу. Опробуйте [бесплатную или платную версию Службы машинного обучения Azure](https://aka.ms/AMLFree).

>[!NOTE]
> Код в этой статье протестирован с помощью пакета SDK для Машинного обучения Azure версии 1.0.8.

## <a name="prerequisites"></a>Предварительные требования

Перейдите к разделу [Настройка среды разработки](#start), чтобы ознакомиться с шагами записной книжки, или используйте приведенные ниже инструкции, чтобы получить записную книжку и запустить ее в службе "Записные книжки Azure" или на собственном сервере записных книжек.  Чтобы запустить записную книжку, вам потребуется:

* Сервер записных книжек Python 3.6, на котором установлены следующие компоненты:
    * пакет SDK для Машинного обучения Azure для Python;
    * `matplotlib` и `scikit-learn`
* Пример записной книжки и файл **utils.py**
* Рабочая область машинного обучения.
* Файл конфигурации для рабочей области в том же каталоге, что и записная книжка.

Получите все необходимые компоненты, перейдя к нужному из разделов, щелкнув соответствующую ссылку ниже.
 
* Использование [облачного сервера записных книжек в рабочей области](#azure) 
* Использование [собственного сервера записных книжек](#server)

### <a name="azure"></a>Использование облачного сервера записных книжек в рабочей области

С помощью облачного сервера записной книжки можно легко начать работу. [Пакет SDK Машинного обучения Azure для Python](https://aka.ms/aml-sdk) будет установлен и настроен, как только вы создадите этот облачный ресурс.

[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]

* После запуска веб-страницы записной книжки откройте записную книжку **tutorials/img-classification-part1-training.ipynb**.


### <a name="server"></a>Использование собственного сервера записных книжек Jupyter

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

 Выполнив указанные действия, запустите записную книжку **tutorials/img-classification-part1-training.ipynb** из клонированного каталога.

## <a name="start"></a>Настройка среды разработки

Все настройки для работы по разработке можно сделать в записной книжке Python. Настройка включает следующие действия:

* Импорт пакетов Python.
* Подключение к рабочей области, что позволяет локальному компьютеру обмениваться данными с удаленными ресурсами.
* Создание эксперимента, чтобы отслеживать все запуски.
* Создание удаленного целевого объекта вычислений для использования в обучении.

### <a name="import-packages"></a>Импорт пакетов

Импортируйте пакеты Python, которые необходимы вам в этом сеансе. Также отобразите версию пакета SDK Службы машинного обучения Azure.

```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt

import azureml.core
from azureml.core import Workspace

# check core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="connect-to-a-workspace"></a>Подключение к рабочей области

В существующей рабочей области создайте объект. `Workspace.from_config()` считывает файл **config.json** и загружает данные в объект с именем `ws`.

```python
# load workspace configuration from the config.json file in the current folder.
ws = Workspace.from_config()
print(ws.name, ws.location, ws.resource_group, ws.location, sep = '\t')
```

### <a name="create-an-experiment"></a>Создание эксперимента

Чтобы отслеживать сведения о выполнении в рабочей области, создайте эксперимент. Рабочая область может содержать несколько экспериментов. 

```python
experiment_name = 'sklearn-mnist'

from azureml.core import Experiment
exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="create-or-attach-an-existing-compute-resource"></a>Создание или подключение существующего ресурса вычислений

Использование Вычислительной среды Машинного обучения Azure (управляемой службы) позволяет специалистам по обработке и анализу данных обучать модели машинного обучения в кластерах виртуальных машин Azure. Часто используются виртуальные машины с поддержкой GPU. В этом руководстве описано, как создать Вычислительную среду Машинного обучения Azure в качестве среды обучения. Приведенный ниже код создаст вычислительные кластеры, если они еще не существуют в вашей рабочей области.

 **Создание вычислительной среды занимает около пяти минут.** Если вычислительная среда уже находится в рабочей области, код сразу применяет ее, пропуская процесс создания.


```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
import os

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpucluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size,
                                                                min_nodes = compute_min_nodes, 
                                                                max_nodes = compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

Теперь у вас есть необходимые пакеты и вычислительные ресурсы для обучения модели в облаке. 

## <a name="explore-data"></a>Изучение данных

Прежде чем начинать обучение модели, необходимо понимать, какие данные используются для ее обучения. Также следует скопировать данные в облако. Оттуда они будут доступны для облачной среды обучения. В этом разделе описано, как выполнять следующие действия:

* Скачивание набора данных MNIST.
* Отображение примеров изображений.
* Отправка данных в облако.

### <a name="download-the-mnist-dataset"></a>Скачивание набора данных MNIST

Скачайте набор данных MNIST и сохраните файлы в локальном каталоге `data`. Загрузите изображения и метки для обучения и тестирования.


```python
import urllib.request
import os

data_folder = os.path.join(os.getcwd(), 'data')
os.makedirs(data_folder, exist_ok = True)

urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz', filename=os.path.join(data_folder, 'train-images.gz'))
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz', filename=os.path.join(data_folder, 'train-labels.gz'))
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename=os.path.join(data_folder, 'test-images.gz'))
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename=os.path.join(data_folder, 'test-labels.gz'))
```
Вы увидите примерно такие выходные данные: ```('./data/test-labels.gz', <http.client.HTTPMessage at 0x7f40864c77b8>)```.

### <a name="display-some-sample-images"></a>Отображение некоторых примеров изображений

Загрузите сжатые файлы в массивы `numpy`. Затем с помощью `matplotlib` постройте график 30 случайных изображений из набора данных с подписями над ними. На этом шаге потребуется функция `load_data`, которая содержится в файле `util.py`. Этот файл находится в папке примера. Разместите его в той же папке, где находится эта записная книжка. Функция `load_data` выполняет анализ сжатых файлов, преобразовывая их в массивы numpy.



```python
# make sure utils.py is in the same directory as this code
from utils import load_data

# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the model converge faster.
X_train = load_data(os.path.join(data_folder, 'train-images.gz'), False) / 255.0
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_train = load_data(os.path.join(data_folder, 'train-labels.gz'), True).reshape(-1)
y_test = load_data(os.path.join(data_folder, 'test-labels.gz'), True).reshape(-1)

# now let's show some randomly chosen images from the traininng set.
count = 0
sample_size = 30
plt.figure(figsize = (16, 6))
for i in np.random.permutation(X_train.shape[0])[:sample_size]:
    count = count + 1
    plt.subplot(1, sample_size, count)
    plt.axhline('')
    plt.axvline('')
    plt.text(x=10, y=-10, s=y_train[i], fontsize=18)
    plt.imshow(X_train[i].reshape(28, 28), cmap=plt.cm.Greys)
plt.show()
```

Случайный пример изображений приведен ниже.

![Случайный пример изображений](./media/tutorial-train-models-with-aml/digits.png)

Теперь у вас есть представление о том, как выглядят эти изображения, и прогноз ожидаемого результата.

### <a name="upload-data-to-the-cloud"></a>Отправка данных в облако

Теперь сделайте данные доступными удаленно, отправив их с локального компьютера в Azure. Так они будут доступны для удаленного обучения Хранилище данных — это удобная конструкция для отправки и скачивания файлов, связанная с рабочей областью. Вы можете взаимодействовать с ним из удаленных целевых объектов вычислений. В качестве базового хранилища используется учетная запись хранилища BLOB-объектов Azure.

MNIST-файлы передаются в каталог с именем `mnist`, размещенный в корневом каталоге хранилища данных.

```python
ds = ws.get_default_datastore()
print(ds.datastore_type, ds.account_name, ds.container_name)

ds.upload(src_dir=data_folder, target_path='mnist', overwrite=True, show_progress=True)
```
Теперь у вас есть все необходимое для начала обучения модели. 


## <a name="train-on-a-remote-cluster"></a>Обучение на удаленном кластере

Для выполнения этой задачи отправьте задание в кластер удаленного обучения, который мы настроили ранее.  Чтобы отправить задание, нужно:
* создать каталог;
* Создание сценария обучения
* Создание объекта оценщика
* отправить задание. 

### <a name="create-a-directory"></a>создать каталог;

Создайте каталог для доставки необходимого кода с компьютера на удаленный ресурс.

```python
import os
script_folder  = os.path.join(os.getcwd(), "sklearn-mnist")
os.makedirs(script_folder, exist_ok=True)
```

### <a name="create-a-training-script"></a>Создание сценария обучения

Чтобы отправить задание в кластер, необходимо сначала создать сценарий обучения. Для создания сценария обучения выполните следующий код, который называется `train.py`, в каталоге, который был только что создан.

```python
%%writefile $script_folder/train.py

import argparse
import os
import numpy as np

from sklearn.linear_model import LogisticRegression
from sklearn.externals import joblib

from azureml.core import Run
from utils import load_data

# let user feed in 2 parameters, the location of the data files (from datastore), and the regularization rate of the logistic regression model
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# load train and test set into numpy arrays
# note we scale the pixel intensity values to 0-1 (by dividing it with 255.0) so the model can converge faster.
X_train = load_data(os.path.join(data_folder, 'train-images.gz'), False) / 255.0
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_train = load_data(os.path.join(data_folder, 'train-labels.gz'), True).reshape(-1)
y_test = load_data(os.path.join(data_folder, 'test-labels.gz'), True).reshape(-1)
print(X_train.shape, y_train.shape, X_test.shape, y_test.shape, sep = '\n')

# get hold of the current run
run = Run.get_context()

print('Train a logistic regression model with regularization rate of', args.reg)
clf = LogisticRegression(C=1.0/args.reg, random_state=42)
clf.fit(X_train, y_train)

print('Predict the test set')
y_hat = clf.predict(X_test)

# calculate accuracy on the prediction
acc = np.average(y_hat == y_test)
print('Accuracy is', acc)

run.log('regularization rate', np.float(args.reg))
run.log('accuracy', np.float(acc))

os.makedirs('outputs', exist_ok=True)
# note file saved in the outputs folder is automatically uploaded into experiment record
joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')
```

Обратите внимание на то, как сценарий получает данные и сохраняет модели.

+ Скрипт обучения считывает аргумент, чтобы найти каталог с данными. При отправке задания позже вы указываете хранилище данных для этого аргумента: ```parser.add_argument('--data-folder', type=str, dest='data_folder', help='data directory mounting point')```.

+ Скрипт обучения сохраняет модель в каталог с именем **outputs**. Все записи в этом каталоге автоматически передаются в рабочую область. Вы будете обращаться к модели из этого каталога далее в этом руководстве. `joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')`

+ Для правильной загрузки набора данных учебный скрипт требует файл `utils.py`. Следующий код копирует `utils.py` в `script_folder`, чтобы получить доступ к файлу вместе со сценарием обучения на удаленном ресурсе.

  ```python
  import shutil
  shutil.copy('utils.py', script_folder)
  ```


### <a name="create-an-estimator"></a>Создание оценщика

Объект оценщика используется для отправки потокового выполнения. Создайте оценщик, выполнив следующий код, который определяет следующие элементы:

* Имя оценщика — `est`.
* Выберите каталог, который содержит скрипт. Все файлы в этом каталоге передаются в узел кластера для выполнения. 
* Целевой объект вычисления. В этом примере используется созданный вычислительный кластер Службы машинного обучения Azure.
* Имя скрипта обучения — **train.py**.
* Параметры, требуемые от скрипта обучения. 
* Необходимые для обучения пакеты Python.

В этом руководстве целевой средой является AMLCompute. Все файлы в папке скриптов передаются для выполнения в узлы кластера. Параметр **data_folder** настраивается на использование хранилища данных `ds.path('mnist').as_mount()`.

```python
from azureml.train.estimator import Estimator

script_params = {
    '--data-folder': ds.path('mnist').as_mount(),
    '--regularization': 0.8
}

est = Estimator(source_directory=script_folder,
                script_params=script_params,
                compute_target=compute_target,
                entry_script='train.py',
                conda_packages=['scikit-learn'])
```


### <a name="submit-the-job-to-the-cluster"></a>Отправка задания в кластер

Запустите эксперимент, отправив объект оценщика.

```python
run = exp.submit(config=est)
run
```

Так как вызов является асинхронным, сразу после запуска задания он возвращает состояние **Preparing** (Подготовка) или **Running** (Выполнение).

## <a name="monitor-a-remote-run"></a>Мониторинг удаленного выполнения

Обычно первый запуск занимает **около 10 минут**. Но для последующих запусков используется тот же образ, если зависимости скрипта не изменяются. Благодаря этому запуск контейнера будет намного быстрее.

Что происходит, пока вы ожидаете завершения?

- **Создание образа**. Создается образ Docker, который соответствует среде Python, указанной в оценщике. Изображение загружается в рабочую область. Создание и отправка изображений занимает **около пяти минут**. 

  Этот этап выполняется один раз для каждой среды Python, а для последующих запусков контейнер помещается в кэш. Во время создания образа журналы будут отправлены в журнал выполнения. С помощью этих журналов вы можете отслеживать ход создания образа.

- **Масштабирование**. Если для выполнения на удаленном кластере требуется больше узлов, чем доступно в текущий момент, дополнительные узлы добавляются автоматически. Масштабирование обычно занимает **около пяти минут.**

- **Running**. На этом этапе необходимые скрипты и файлы отправляются на целевой объект вычислений. Затем подключаются или копируются хранилища данных. После этого выполняется **entry_script**. Пока выполняется задание, стандартный выход **stdout** и каталог **./logs** направляются в журнал выполнения. С помощью этих журналов вы можете отслеживать ход создания образа.

- **Постобработка**. Каталог **./outputs** завершенного выполнения копируется в журнал выполнения в рабочей области, чтобы вы могли обращаться к этим результатам.


Ход выполнения запущенного задания можно контролировать несколькими способами. В этом руководстве используются мини-приложение Jupyter и метод `wait_for_completion`. 

### <a name="jupyter-widget"></a>Мини-приложение Jupyter

Отслеживайте ход выполнения с помощью мини-приложения Jupyter. Как и отправка выполнения, мини-приложение работает асинхронно и в реальном времени предоставляет обновления каждые 10–15 секунд, пока не завершит задание.


```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

Этот снимок демонстрирует окно мини-приложение в конце процесса обучения.

![Мини-приложение записной книжки](./media/tutorial-train-models-with-aml/widget.png)

Если необходимо отменить выполнение, вы можете выполнить [эти инструкции](https://aka.ms/aml-docs-cancel-run).

### <a name="get-log-results-upon-completion"></a>Получение результатов записи по завершении

Обучение и мониторинг модели происходит в фоновом режиме. Прежде, чем выполнять другой код, дождитесь завершения обучения модели. `wait_for_completion` позволяет узнать, когда завершится обучение модели. 


```python
run.wait_for_completion(show_output=False) # specify True for a verbose log
```

### <a name="display-run-results"></a>Отображение результатов потокового выполнения

Теперь у вас есть модель, которую обучили на удаленном кластере. Извлеките точность модели, выполнив такую команду.

```python
print(run.get_metrics())
```
В выходных данных показано, что точность удаленной модели — 0,9204.

`{'regularization rate': 0.8, 'accuracy': 0.9204}`

В следующем руководстве эта модель рассматривается подробнее.

## <a name="register-model"></a>Регистрация модели

На последнем шаге скрипт обучения записал файл `outputs/sklearn_mnist_model.pkl` в каталог с именем `outputs` на виртуальной машине кластера, где выполнялось задание. Все содержимое специального каталога `outputs` автоматически отправляется в рабочую область. Это содержимое отображается в записи о выполнении эксперимента в рабочей области. Теперь файл модели также доступен в рабочей области.

Вы увидите файлы, связанные с этим выполнением.

```python
print(run.get_file_names())
```

Зарегистрируйте модель в рабочей области, чтобы вы или другие участники смогли позже запросить, проверить и развернуть ее.

```python
# register model 
model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
print(model.name, model.id, model.version, sep = '\t')
```

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Вычислительный кластер Машинного обучения Azure можно удалить. Но для него включено автомасштабирование с минимальным размером, равным нулю. Это означает, что этот ресурс не создает дополнительных расходов, когда он не используется.


```python
# optionally, delete the Azure Machine Learning Compute cluster
compute_target.delete()
```

## <a name="next-steps"></a>Дополнительная информация

В рамках этого руководства по Службе машинного обучения Azure вы выполнили с помощью Python следующие задачи:

> [!div class="checklist"]
> * Настройка среды разработки.
> * Подключение к данным и их проверка.
> * Обучение нескольких моделей на удаленном кластере с помощью популярной библиотеки машинного обучения scikit-learn.
> * Проверка сведений об обучении и регистрация наилучшей модели.

Теперь вы готовы развернуть зарегистрированную модель с помощью инструкций, представленных в следующей части серии руководств.

> [!div class="nextstepaction"]
> [Руководство 2. Развертывание моделей](tutorial-deploy-models-with-aml.md)
