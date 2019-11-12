---
title: Как и где развертываются модели
titleSuffix: Azure Machine Learning
description: Узнайте, как и где развертываются модели Машинное обучение Azure, включая экземпляры контейнеров Azure, службу Azure Kubernetes, Azure IoT Edge и программируемые для полей массивы шлюзов.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 09/13/2019
ms.custom: seoapril2019
ms.openlocfilehash: 4a0736267ca00b67f35abc7cf263e7cf19543d81
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73932125"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Развертывание моделей с помощью Машинное обучение Azure
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Узнайте, как развернуть модель машинного обучения в качестве веб-службы в облаке Azure или на Azure IoT Edge устройствах.

Рабочий процесс аналогичен независимо от [того, где развертывается](#target) модель:

1. Регистрация модели.
1. Подготовка к развертыванию. (Укажите активы, использование, целевой объект вычислений.)
1. Разверните модель в целевом объекте вычислений.
1. Протестируйте развернутую модель, также называемую веб-службой.

Дополнительные сведения об основных понятиях, связанных с рабочим процессом развертывания, см. в разделе [Управление моделями, их развертывание и мониторинг с помощью машинное обучение Azure](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>предварительным требованиям

- Рабочая область машинного обучения Azure. Дополнительные сведения см. в статье [создание машинное обучение Azure рабочей области](how-to-manage-workspace.md).

- Модель. Если у вас нет обученной модели, можно использовать модель и файлы зависимостей, предоставленные в [этом руководстве](https://aka.ms/azml-deploy-cloud).

- [Расширение Azure CLI для службы машинное обучение](reference-azure-machine-learning-cli.md), [машинное обучение Azure SDK для Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)или [расширение машинное обучение Azure Visual Studio Code](how-to-vscode-tools.md).

## <a name="connect-to-your-workspace"></a>Подключение к рабочей области

В следующем коде показано, как подключиться к рабочей области Машинное обучение Azure, используя сведения, кэшированные в локальной среде разработки:

+ **Использование пакета SDK**

   ```python
   from azureml.core import Workspace
   ws = Workspace.from_config(path=".file-path/ws_config.json")
   ```

  Дополнительные сведения об использовании пакета SDK для подключения к рабочей области см. в документации по [машинное обучение Azure SDK для Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) .

+ **Использование интерфейса командной строки**

   При использовании интерфейса командной строки используйте параметр `-w` или `--workspace-name`, чтобы указать рабочую область для команды.

+ **Использование VS Code**

   При использовании VS Code рабочую область можно выбрать с помощью графического интерфейса. Дополнительные сведения см. в разделе [Развертывание моделей и управление ими](how-to-vscode-tools.md#deploy-and-manage-models) в документации по расширению VS Code.

## <a id="registermodel"></a>Регистрация модели

Зарегистрированная модель — это логический контейнер для одного или нескольких файлов, составляющих модель. Например, если имеется модель, которая хранится в нескольких файлах, можно зарегистрировать их как единую модель в рабочей области. После регистрации файлов можно скачать или развернуть зарегистрированную модель и получить все зарегистрированные файлы.

> [!TIP]
> При регистрации модели вы предоставляете путь к облачному расположению (из обучающего запуска) или к локальному каталогу. Этот путь предназначен только для поиска файлов для отправки в ходе процесса регистрации. Он не должен соответствовать пути, используемому в скрипте записи. Дополнительные сведения см. [в разделе Поиск файлов модели в скрипте записи](#locate-model-files-in-your-entry-script).

Модели машинного обучения регистрируются в рабочей области Машинное обучение Azure. Модель может поступать из Машинное обучение Azure или в другом месте. В следующих примерах показано, как зарегистрировать модель.

### <a name="register-a-model-from-an-experiment-run"></a>Регистрация модели из запуска эксперимента

В фрагментах кода в этом разделе показано, как зарегистрировать модель из обучающего запуска:

> [!IMPORTANT]
> Чтобы использовать эти фрагменты кода, необходимо предварительно выполнить обучение и получить доступ к объекту `Run` (пример пакета SDK) или значению идентификатора запуска (пример с интерфейсом командной строки). Дополнительные сведения о моделях обучения см. в разделе [Настройка целевых объектов вычислений для обучения модели](how-to-set-up-training-targets.md).

+ **Использование пакета SDK**

  При использовании пакета SDK для обучения модели можно получить либо объект [запуска](https://review.docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&branch=master) , либо объект [аутомлрун](https://review.docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.run.automlrun?view=azure-ml-py&branch=master) в зависимости от того, как была обучена модель. Каждый объект можно использовать для регистрации модели, созданной с помощью запуска эксперимента.

  + Регистрация модели из объекта `azureml.core.Run`:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    Параметр `model_path` ссылается на расположение в облаке модели. В этом примере используется путь к одному файлу. Чтобы включить несколько файлов в регистрацию модели, задайте `model_path` пути к папке, содержащей файлы. Дополнительные сведения см. в документации по [Run. register_model](https://review.docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&branch=master#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none----kwargs-) .

  + Регистрация модели из объекта `azureml.train.automl.run.AutoMLRun`:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description)

        print(run.model_id)
    ```

    В этом примере параметры `metric` и `iteration` не указаны, поэтому будет зарегистрирована итерация с лучшей основной метрикой. Значение `model_id`, возвращаемое при выполнении, используется вместо имени модели.

    Дополнительные сведения см. в документации по [аутомлрун. register_model](https://review.docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.run.automlrun?view=azure-ml-py&branch=master#register-model-description-none--tags-none--iteration-none--metric-none-) .

+ **Использование интерфейса командной строки**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid
  ```

  [!INCLUDE [install extension](../../../includes/machine-learning-service-install-extension.md)]

  Параметр `--asset-path` ссылается на расположение в облаке модели. В этом примере используется путь к одному файлу. Чтобы включить несколько файлов в регистрацию модели, задайте `--asset-path` пути к папке, содержащей файлы.

+ **Использование VS Code**

  Регистрация моделей с помощью любых файлов или папок модели с помощью расширения [VS Code](how-to-vscode-tools.md#deploy-and-manage-models) .

### <a name="register-a-model-from-a-local-file"></a>Регистрация модели из локального файла

Вы можете зарегистрировать модель, указав локальный путь к модели. Можно указать путь либо к папке, либо к одному файлу. С помощью этого метода можно регистрировать модели, обученные Машинное обучение Azure, а затем загружать их. Этот метод также можно использовать для регистрации моделей, обученных за пределами Машинное обучение Azure.

[!INCLUDE [trusted models](../../../includes/machine-learning-service-trusted-model.md)]

+ **Использование пакета SDK и ONNX**

    ```python
    import os
    import urllib.request
    from azureml.core.model import Model
    # Download model
    onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
    urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
    os.system('tar xvzf mnist.tar.gz')
    # Register model
    model = Model.register(workspace = ws,
                            model_path ="mnist/model.onnx",
                            model_name = "onnx_mnist",
                            tags = {"onnx": "demo"},
                            description = "MNIST image classification CNN from ONNX Model Zoo",)
    ```

  Чтобы включить несколько файлов в регистрацию модели, задайте `model_path` пути к папке, содержащей файлы.

+ **Использование интерфейса командной строки**

  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  Чтобы включить несколько файлов в регистрацию модели, задайте `-p` пути к папке, содержащей файлы.

**Оценка времени**: приблизительно 10 секунд.

Дополнительные сведения см. в документации по [классу Model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

Дополнительные сведения о работе с моделями, обученными вне Машинное обучение Azure, см. [в разделе Развертывание существующей модели](how-to-deploy-existing-model.md).

<a name="target"></a>

## <a name="choose-a-compute-target"></a>Выбор целевого объекта вычислений

Для размещения развертывания веб-службы можно использовать следующие целевые объекты вычислений или ресурсы вычислений:

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

## <a name="prepare-to-deploy"></a>Подготовка к развертыванию

Для развертывания модели вам потребуются следующие элементы:

* **Скрипт записи**. Этот скрипт принимает запросы, оценивает запросы с помощью модели и возвращает результаты.

    > [!IMPORTANT]
    > * Сценарий записи зависит от модели. Он должен понимать формат данных входящего запроса, формат данных, ожидаемых моделью, и формат данных, возвращаемых клиентам.
    >
    >   Если данные запроса имеют формат, непригодный для использования в вашей модели, скрипт может преобразовать его в допустимый формат. Он также может преобразовать ответ перед его возвратом клиенту.
    >
    > * Пакет SDK для Машинное обучение Azure не предоставляет веб-службам и IoT Edgeным развертываниям доступ к хранилищу данных или наборам DataSet. Если развернутой модели требуется доступ к данным, хранящимся за пределами развертывания, например данных в учетной записи хранения Azure, необходимо разработать собственное решение с кодом, используя соответствующий пакет SDK. Например, [пакет SDK для службы хранилища Azure для Python](https://github.com/Azure/azure-storage-python).
    >
    >   Альтернативой, который может работать в вашем сценарии, является [Прогнозирование пакетной обработки](how-to-run-batch-predictions.md), предоставляющее доступ к хранилищам данных во время оценки.

* **Зависимости**, такие как вспомогательные скрипты или пакеты Python или Conda, необходимые для запуска сценария записи или модели.

* **Конфигурация развертывания** для целевого объекта вычислений, в котором развернута модель. Эта конфигурация описывает такие вещи, как память и требования к ЦП, необходимые для запуска модели.

Эти элементы инкапсулированы в *конфигурацию вывода* и *конфигурацию развертывания*. Конфигурация вывода ссылается на скрипт записи и другие зависимости. Эти конфигурации определяются программно при использовании пакета SDK для выполнения развертывания. Они определяются в файлах JSON при использовании интерфейса командной строки.

### <a id="script"></a>1. определение скрипта записи и зависимостей

Сценарий записи получает данные, отправленные в развернутую веб-службу, и передает их в модель. Затем он принимает ответ, возвращенный моделью, и возвращает его клиенту. *Сценарий зависит от модели*. Он должен понимать данные, которые предположительно и возвращает модель.

Скрипт содержит две функции, которые загружают и запускают модель:

* `init()`. как правило, эта функция загружает модель в глобальный объект. Эта функция выполняется только один раз при запуске контейнера DOCKER для веб-службы.

* `run(input_data)`: Эта функция использует модель для прогнозирования значения на основе входных данных. Входы и выходы для выполнения обычно используют JSON для сериализации и десериализации. Вы также можете работать с необработанными двоичными данными. Данные можно преобразовать перед отправкой в модель или перед их возвратом клиенту.

#### <a name="locate-model-files-in-your-entry-script"></a>Обнаружение файлов модели в скрипте записи

Существует два способа определения модели в скрипте записи:
* `AZUREML_MODEL_DIR`: переменная среды, содержащая путь к расположению модели.
* `Model.get_model_path`: API, который возвращает путь к файлу модели, используя имя зарегистрированной модели.

##### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

AZUREML_MODEL_DIR — это переменная среды, созданная во время развертывания службы. Эту переменную среды можно использовать для поиска расположения развернутых моделей.

В следующей таблице описано значение AZUREML_MODEL_DIR в зависимости от числа развернутых моделей:

| Развертывание | Значение переменной среды |
| ----- | ----- |
| Одна модель | Путь к папке, содержащей модель. |
| Несколько моделей | Путь к папке, содержащей все модели. Модели расположены по имени и версии в этой папке (`$MODEL_NAME/$VERSION`) |

Чтобы получить путь к файлу в модели, объедините переменную среды с искомым именем файла.
Имена файлов модели сохраняются во время регистрации и развертывания. 

**Пример одной модели**
```python
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')
```

**Пример с несколькими моделями**
```python
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_model/1/sklearn_regression_model.pkl')
```

##### <a name="get_model_path"></a>get_model_path

При регистрации модели вы предоставляете имя модели, используемое для управления моделью в реестре. Это имя используется с методом [model. get_model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) для получения пути к файлу модели или файлам в локальной файловой системе. При регистрации папки или коллекции файлов этот API возвращает путь к каталогу, содержащему эти файлы.

При регистрации модели ей присваивается имя. Имя соответствует месту размещения модели: локально или во время развертывания службы.

> [!IMPORTANT]
> Если для обучения модели использовалось автоматическое машинное обучение, в качестве имени модели используется значение `model_id`. Пример регистрации и развертывания модели, обученной в автоматизированном машинном обучении, см. в статье [Azure/мачинелеарнингнотебукс](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-with-deployment) на сайте GitHub.

В следующем примере возвращается путь к одному файлу с именем `sklearn_mnist_model.pkl` (который был зарегистрирован с именем `sklearn_mnist`):

```python
model_path = Model.get_model_path('sklearn_mnist')
```

<a id="schema"></a>

#### <a name="optional-automatic-schema-generation"></a>Используемых Автоматическое создание схем

Чтобы автоматически создать схему для веб-службы, укажите образец входных и (или) выходных данных в конструкторе для одного из объектов определенного типа. Тип и образец используются для автоматического создания схемы. После этого Машинное обучение Azure создает спецификацию [OpenAPI](https://swagger.io/docs/specification/about/) (Swagger) для веб-службы во время развертывания.

В настоящее время поддерживаются следующие типы:

* `pandas`
* `numpy`
* `pyspark`
* Стандартный объект Python

Чтобы использовать формирование схемы, включите пакет `inference-schema` в файл среды Conda. Дополнительные сведения об этом пакете см. в разделе [https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema).

##### <a name="example-dependencies-file"></a>Пример файла зависимостей

Следующий YAML является примером файла зависимостей Conda для вывода:

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - scikit-learn=0.20.0
  - pip:
    - azureml-defaults
    - inference-schema[numpy-support]
```

> [!IMPORTANT]
> Если зависимость доступна с помощью Conda и PIP (из PyPi), корпорация Майкрософт рекомендует использовать версию Conda, так как Conda пакеты обычно поставляются с предварительно созданными двоичными файлами, которые делают установку более надежной.
>
> Дополнительные сведения см. в разделе [Основные сведения о Conda и PIP](https://www.anaconda.com/understanding-conda-and-pip/).
>
> Чтобы проверить, доступна ли зависимость через Conda, используйте команду `conda search <package-name>` или используйте индексы пакетов в [https://anaconda.org/anaconda/repo](https://anaconda.org/anaconda/repo) и [https://anaconda.org/conda-forge/repo](https://anaconda.org/conda-forge/repo).

Если вы хотите использовать автоматическое создание схем, сценарий записи должен импортировать `inference-schema` пакеты.

Определите форматы образца входных и выходных данных в переменных `input_sample` и `output_sample`, которые представляют форматы запросов и ответов для веб-службы. Используйте эти примеры в декораторах входных и выходных функций в функции `run()`. В следующем примере scikit-учиться используется создание схемы.

##### <a name="example-entry-script"></a>Пример скрипта записи

В следующем примере показано, как принимать и возвращать данные JSON.

```python
#Example: scikit-learn and Swagger
import json
import numpy as np
import os
from sklearn.externals import joblib
from sklearn.linear_model import Ridge

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment. Join this path with the filename of the model file.
    # It holds the path to the directory that contains the deployed model (./azureml-models/$MODEL_NAME/$VERSION).
    # If there are multiple models, this value is the path to the directory containing all deployed models (./azureml-models).
    # Alternatively: model_path = Model.get_model_path('sklearn_mnist')
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    # Deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = np.array([[10, 9, 8, 7, 6, 5, 4, 3, 2, 1]])
output_sample = np.array([3726.995])


@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

В следующем примере показано, как определить входные данные как словарь `<key: value>` с помощью таблицы данных. Этот метод поддерживается для использования развернутой веб-службы из Power BI. (Дополнительные[сведения об использовании веб-службы из Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-integration).)

```python
import json
import pickle
import numpy as np
import pandas as pd
import azureml.train.automl
from sklearn.externals import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # Replace filename if needed.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'model_file.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    # This is a decimal type sample. Use the data type that reflects this column in your data.
    "input_name_1": 5.1,
    # This is a string type sample. Use the data type that reflects this column in your data.
    "input_name_2": "value2",
    # This is an integer type sample. Use the data type that reflects this column in your data.
    "input_name_3": 3
}])

# This is an integer type sample. Use the data type that reflects the expected result.
output_sample = np.array([0])


@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Дополнительные примеры см. в следующих скриптах:

* [PyTorch](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch)
* [TensorFlow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow);
* [Keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras);
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)

<a id="binary"></a>

#### <a name="binary-data"></a>Двоичные данные

Если модель принимает двоичные данные, например изображение, необходимо изменить файл `score.py`, используемый для развертывания, чтобы принимать необработанные HTTP-запросы. Чтобы принимать необработанные данные, используйте класс `AMLRequest` в скрипте записи и добавьте `@rawhttp` декоратора в функцию `run()`.

Ниже приведен пример `score.py`, который принимает двоичные данные:

```python
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse


def init():
    print("This is init()")


@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example just returns the posted data as the response.
        return AMLResponse(reqBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> Класс `AMLRequest` находится в пространстве имен `azureml.contrib`. Сущности в этом пространстве имен часто изменяются, так как мы работаем над улучшением службы. Все, что есть в этом пространстве имен, следует рассматривать как предварительную версию, которая не полностью поддерживается корпорацией Майкрософт.
>
> Если необходимо протестировать это в локальной среде разработки, можно установить компоненты с помощью следующей команды:
>
> ```shell
> pip install azureml-contrib-services
> ```

<a id="cors"></a>

#### <a name="cross-origin-resource-sharing-cors"></a>Общий доступ к ресурсам между источниками (CORS)

Совместное использование ресурсов в разных источниках позволяет запрашивать ресурсы на веб-странице из другого домена. CORS работает через заголовки HTTP, отправленные с клиентским запросом и возвращаемые с ответом службы. Дополнительные сведения о CORS и допустимых заголовках см. в разделе [общий доступ к ресурсам между источниками](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) в Википедии.

Чтобы настроить развертывание модели для поддержки CORS, используйте класс `AMLResponse` в скрипте записи. Этот класс позволяет задать заголовки для объекта ответа.

В следующем примере задается заголовок `Access-Control-Allow-Origin` для ответа из скрипта записи:

```python
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example
        # adds a header and returns the request body.
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> Класс `AMLResponse` находится в пространстве имен `azureml.contrib`. Сущности в этом пространстве имен часто изменяются, так как мы работаем над улучшением службы. Все, что есть в этом пространстве имен, следует рассматривать как предварительную версию, которая не полностью поддерживается корпорацией Майкрософт.
>
> Если необходимо протестировать это в локальной среде разработки, можно установить компоненты с помощью следующей команды:
>
> ```shell
> pip install azureml-contrib-services
> ```

### <a name="2-define-your-inferenceconfig"></a>2. Определение Инференцеконфиг

Конфигурация вывода описывает, как настроить модель для выполнения прогнозов. Эта конфигурация не является частью сценария записи. Он ссылается на скрипт записи и используется для размещения всех ресурсов, необходимых для развертывания. Он используется позже при развертывании модели.

Конфигурация вывода может использовать Машинное обучение Azure среды для определения зависимостей программного обеспечения, необходимых для развертывания. Среды позволяют создавать, администрировать и повторно использовать зависимости программного обеспечения, необходимые для обучения и развертывания. В следующем примере демонстрируется загрузка среды из рабочей области и ее использование с конфигурацией вывода:

```python
from azureml.core import Environment
from azureml.core.model import InferenceConfig

deploy_env = Environment.get(workspace=ws,name="myenv",version="1")
inference_config = InferenceConfig(entry_script="x/y/score.py",
                                   environment=deploy_env)
```

Дополнительные сведения о средах см. в статье [создание сред для обучения и развертывания и управление ими](how-to-use-environments.md).

Можно также указать зависимости напрямую, не используя среду. В следующем примере показано, как создать конфигурацию вывода, которая загружает зависимости программного обеспечения из файла Conda:

```python
from azureml.core.model import InferenceConfig

inference_config = InferenceConfig(runtime="python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

Дополнительные сведения см. в документации по классу [инференцеконфиг](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) .

Сведения об использовании пользовательского образа DOCKER с конфигурацией вывода см. в статье [развертывание модели с помощью пользовательского образа DOCKER](how-to-deploy-custom-docker-image.md).

### <a name="cli-example-of-inferenceconfig"></a>Пример Инференцеконфиг в CLI

[!INCLUDE [inference config](../../../includes/machine-learning-service-inference-config.md)]

Следующая команда демонстрирует развертывание модели с помощью интерфейса командной строки.

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

В этом примере конфигурация задает следующие параметры.

* Для модели требуется Python.
* [Скрипт записи](#script), используемый для обработки веб-запросов, отправляемых в развернутую службу.
* Файл Conda, описывающий пакеты Python, необходимые для вывода.

Сведения об использовании пользовательского образа DOCKER с конфигурацией вывода см. в статье [развертывание модели с помощью пользовательского образа DOCKER](how-to-deploy-custom-docker-image.md).

### <a name="3-define-your-deployment-configuration"></a>3. Определение конфигурации развертывания

Перед развертыванием модели необходимо определить конфигурацию развертывания. *Конфигурация развертывания зависит от целевого объекта вычислений, в котором будет размещена веб-служба.* Например, при локальном развертывании модели необходимо указать порт, на котором служба принимает запросы. Конфигурация развертывания не является частью скрипта записи. Он используется для определения характеристик целевого объекта вычислений, в котором будет размещаться скрипт модели и входа.

Вам также может потребоваться создать вычислительный ресурс, если, например, у вас еще нет экземпляра Azure Kubernetes Service (AKS), связанного с вашей рабочей областью.

В следующей таблице приведен пример создания конфигурации развертывания для каждого целевого объекта вычислений.

| Целевой объект вычисления | Пример конфигурации развертывания |
| ----- | ----- |
| Local | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Экземпляры контейнеров Azure | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Служба Azure Kubernetes | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Классы для локальных объектов, экземпляров контейнеров Azure и веб-служб AKS можно импортировать из `azureml.core.webservice`:

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

#### <a name="profiling"></a>Профилирование

Перед развертыванием модели в качестве службы может потребоваться ее профилирование для определения оптимальных требований к ЦП и памяти. Для профилирования модели можно использовать либо пакет SDK, либо интерфейс командной строки. В следующих примерах показано, как профилировать модель с помощью пакета SDK.

> [!IMPORTANT]
> При использовании профилирования предоставленная конфигурация вывода не может ссылаться на среду Машинное обучение Azure. Вместо этого определите зависимости программного обеспечения с помощью параметра `conda_file` объекта `InferenceConfig`.

```python
import json
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10]
]})

profile = Model.profile(ws, "profilemymodel", [model], inference_config, test_data)
profile.wait_for_profiling(True)
profiling_results = profile.get_results()
print(profiling_results)
```

Этот код отображает результат, аналогичный приведенному ниже:

```python
{'cpu': 1.0, 'memoryInGB': 0.5}
```

Результаты профилирования модели создаются как объект `Run`.

Дополнительные сведения об использовании профилирования из CLI см. в разделе [AZ ML Model Profile](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile).

Дополнительные сведения см. в следующих документах:

* [моделпрофиле](https://docs.microsoft.com/python/api/azureml-core/azureml.core.profile.modelprofile?view=azure-ml-py)
* [профиль ()](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#profile-workspace--profile-name--models--inference-config--input-data-)
* [Схема файла конфигурации вывода](reference-azure-machine-learning-cli.md#inference-configuration-schema)

## <a name="deploy-to-target"></a>Развертывание в целевом объекте

Развертывание использует конфигурацию развертывания конфигурации определения для развертывания моделей. Процесс развертывания аналогичен, независимо от целевого объекта вычислений. Развертывание в AKS немного отличается, так как необходимо предоставить ссылку на кластер AKS.

### <a id="local"></a>Локальное развертывание

Чтобы развернуть модель локально, на локальном компьютере должен быть установлен DOCKER.

#### <a name="using-the-sdk"></a>Использование пакета SDK

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Дополнительные сведения см. в документации по [локалвебсервице](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), [model. deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-)и [WebService](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

#### <a name="using-the-cli"></a>Использование интерфейса командной строки

Чтобы развернуть модель с помощью интерфейса командной строки, используйте следующую команду. Замените `mymodel:1` именем и версией зарегистрированной модели:

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../../includes/machine-learning-service-local-deploy-config.md)]

Дополнительные сведения см. в документации по [AZ ml для развертывания модели](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) .

### <a id="notebookvm"></a>Веб-служба виртуальной машины записных книжек (разработка и тестирование)

См. статью [развертывание модели для машинное обучение Azure виртуальной машины записной книжки](how-to-deploy-local-container-notebook-vm.md).

### <a id="aci"></a>Экземпляры контейнеров Azure (разработка и тестирование)

См. раздел [развертывание в службе "экземпляры контейнеров Azure](how-to-deploy-azure-container-instance.md)".

### <a id="aks"></a>Служба Kubernetes Azure (разработка, тестирование и Рабочая среда)

См. раздел [Deploy to Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md).

## <a name="consume-web-services"></a>Использование веб-служб

Каждая развернутая веб-служба предоставляет REST API, что позволяет создавать клиентские приложения на различных языках программирования.
Если для службы включен ключ проверки подлинности, необходимо предоставить ключ службы в качестве маркера в заголовке запроса.
Если вы включили проверку подлинности маркеров для службы, необходимо предоставить маркер JWT Машинное обучение Azure в качестве токена носителя в заголовке запроса.

> [!TIP]
> После развертывания службы можно получить документ JSON схемы. Используйте [свойство swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri) из развернутой веб-службы (например, `service.swagger_uri`), чтобы получить универсальный код ресурса (URI) для файла Swagger локальной веб-службы.

### <a name="request-response-consumption"></a>Использование запросов и ответов

Ниже приведен пример вызова службы в Python:
```python
import requests
import json

headers = {'Content-Type': 'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]
elif service.token_auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_token()[0]

print(headers)

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

response = requests.post(
    service.scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

Дополнительные сведения см. [в статье Создание клиентских приложений для использования веб-служб](how-to-consume-web-service.md).

### <a name="web-service-schema-openapi-specification"></a>Схема веб-службы (спецификация OpenAPI)

Если вы использовали автоматическое создание схем в развертывании, можно получить адрес спецификации OpenAPI для службы с помощью [свойства swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri). (Например, `print(service.swagger_uri)`.) Используйте запрос GET или откройте универсальный код ресурса (URI) в браузере, чтобы получить спецификацию.

Следующий документ JSON является примером схемы (спецификации OpenAPI), созданной для развертывания:

```json
{
    "swagger": "2.0",
    "info": {
        "title": "myservice",
        "description": "API specification for Azure Machine Learning myservice",
        "version": "1.0"
    },
    "schemes": [
        "https"
    ],
    "consumes": [
        "application/json"
    ],
    "produces": [
        "application/json"
    ],
    "securityDefinitions": {
        "Bearer": {
            "type": "apiKey",
            "name": "Authorization",
            "in": "header",
            "description": "For example: Bearer abc123"
        }
    },
    "paths": {
        "/": {
            "get": {
                "operationId": "ServiceHealthCheck",
                "description": "Simple health check endpoint to ensure the service is up at any given point.",
                "responses": {
                    "200": {
                        "description": "If service is up and running, this response will be returned with the content 'Healthy'",
                        "schema": {
                            "type": "string"
                        },
                        "examples": {
                            "application/json": "Healthy"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        },
        "/score": {
            "post": {
                "operationId": "RunMLService",
                "description": "Run web service's model and get the prediction output",
                "security": [
                    {
                        "Bearer": []
                    }
                ],
                "parameters": [
                    {
                        "name": "serviceInputPayload",
                        "in": "body",
                        "description": "The input payload for executing the real-time machine learning service.",
                        "schema": {
                            "$ref": "#/definitions/ServiceInput"
                        }
                    }
                ],
                "responses": {
                    "200": {
                        "description": "The service processed the input correctly and provided a result prediction, if applicable.",
                        "schema": {
                            "$ref": "#/definitions/ServiceOutput"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        }
    },
    "definitions": {
        "ServiceInput": {
            "type": "object",
            "properties": {
                "data": {
                    "type": "array",
                    "items": {
                        "type": "array",
                        "items": {
                            "type": "integer",
                            "format": "int64"
                        }
                    }
                }
            },
            "example": {
                "data": [
                    [ 10, 9, 8, 7, 6, 5, 4, 3, 2, 1 ]
                ]
            }
        },
        "ServiceOutput": {
            "type": "array",
            "items": {
                "type": "number",
                "format": "double"
            },
            "example": [
                3726.995
            ]
        },
        "ErrorResponse": {
            "type": "object",
            "properties": {
                "status_code": {
                    "type": "integer",
                    "format": "int32"
                },
                "message": {
                    "type": "string"
                }
            }
        }
    }
}
```

Дополнительные сведения см. в статье [Спецификация OpenAPI](https://swagger.io/specification/).

Сведения о программе, которая может создавать клиентские библиотеки из спецификации, см. в разделе [Swagger-CodeGen](https://github.com/swagger-api/swagger-codegen).

### <a id="azuremlcompute"></a>Вывод пакета
Машинное обучение Azure целевые объекты вычислений создаются и управляются с помощью Машинное обучение Azure. Их можно использовать для прогнозирования пакетной службы из Машинное обучение Azure конвейеров.

Пошаговое руководство по выводу пакетов с помощью Машинное обучение Azure COMPUTE см. [в разделе Выполнение пакетных прогнозов](tutorial-pipeline-batch-scoring-classification.md).

### <a id="iotedge"></a>Вывод IoT Edge
Поддержка развертывания на границе доступна в предварительной версии. Дополнительные сведения см. в разделе [развертывание машинное обучение Azure как модуля IOT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning).


## <a id="update"></a>Обновление веб-служб

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="continuously-deploy-models"></a>Непрерывное развертывание моделей

Вы можете непрерывно развертывать модели с помощью расширения Машинное обучение для [Azure DevOps](https://azure.microsoft.com/services/devops/). Вы можете использовать расширение Машинное обучение для Azure DevOps, чтобы активировать конвейер развертывания при регистрации новой модели машинного обучения в Машинное обучение Azure рабочей области.

1. Подпишитесь на [Azure pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops), что обеспечивает непрерывную интеграцию и доставку приложения на любую платформу или облако. (Обратите внимание, что Azure Pipelines не так же, как [машинное обучение конвейеры](concept-ml-pipelines.md#compare)).

1. [Создайте проект Azure DevOps.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. Установите [расширение машинное обучение для Azure pipelines](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList).

1. Используйте подключения к службам, чтобы настроить подключение субъекта-службы к рабочей области Машинное обучение Azure, чтобы получить доступ к артефактам. Последовательно выберите пункты Параметры проекта, **подключения к службе**и **Azure Resource Manager**:

    [![выберите Azure Resource Manager](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png)

1. В списке **область уровня области** выберите **азуремлворкспаце**и введите остальные значения:

    ![Выбор Азуремлворкспаце](media/how-to-deploy-and-where/resource-manager-connection.png)

1. Чтобы непрерывно развернуть модель машинного обучения с помощью Azure Pipelines, в разделе конвейеры выберите **выпуск**. Добавьте новый артефакт, а затем выберите артефакт **модели AzureML** и созданное ранее подключение службы. Выберите модель и версию для активации развертывания:

    [![выбрать модель AzureML](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. Включите триггер модели для артефакта модели. При включении триггера каждый раз, когда указанная версия (то есть самая последняя версия) в этой модели регистрируется в рабочей области, инициируется конвейер выпуска Azure DevOps.

    [![включения триггера модели](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

Дополнительные примеры проектов и примеры см. в этих примерах репозиториев в GitHub:

* [Microsoft/Млопс](https://github.com/Microsoft/MLOps)
* [Microsoft/Млопсписон](https://github.com/microsoft/MLOpsPython)

## <a name="download-a-model"></a>Загрузка модели
Если вы хотите загрузить модель для использования в собственной среде выполнения, это можно сделать с помощью следующих команд SDK/CLI:

Tool
```python
model_path = Model(ws,'mymodel').download()
```

CLI:
```azurecli-interactive
az ml model download --model-id mymodel:1 --target-dir model_folder
```

## <a name="preview-no-code-model-deployment"></a>Образца Развертывание модели без кода

Развертывание модели без кода сейчас находится на этапе предварительной версии и поддерживает следующие платформы машинного обучения:

### <a name="tensorflow-savedmodel-format"></a>Формат Саведмодел Tensorflow

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='flowers',                        # Name of the registered model in your workspace.
                       model_path='./flowers_model',                # Local Tensorflow SavedModel folder to upload and register as a model.
                       model_framework=Model.Framework.TENSORFLOW,  # Framework used to create the model.
                       model_framework_version='1.14.0',            # Version of Tensorflow used to create the model.
                       description='Flowers model')

service_name = 'tensorflow-flower-service'
service = Model.deploy(ws, service_name, [model])
```

### <a name="onnx-models"></a>Модели ONNX

Регистрация и развертывание модели ONNX поддерживаются для любого графа вывода ONNX. Этапы предварительной обработки и выполнения процессов в настоящее время не поддерживаются.

Ниже приведен пример регистрации и развертывания модели MNIST ONNX.

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='mnist-sample',                  # Name of the registered model in your workspace.
                       model_path='mnist-model.onnx',              # Local ONNX model to upload and register as a model.
                       model_framework=Model.Framework.ONNX ,      # Framework used to create the model.
                       model_framework_version='1.3',              # Version of ONNX used to create the model.
                       description='Onnx MNIST model')

service_name = 'onnx-mnist-service'
service = Model.deploy(ws, service_name, [model])
```

### <a name="scikit-learn-models"></a>Scikit — изучение моделей

Не поддерживается развертывание модели кода для всех встроенных типов моделей scikit-учиться.

Ниже приведен пример регистрации и развертывания модели sklearn без дополнительного кода.

```python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version='0.19.1',             # Version of scikit-learn used to create the model.
                       resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})
                       
service_name = 'my-sklearn-service'
service = Model.deploy(ws, service_name, [model])
```

Примечание. Эти зависимости включены в готовый контейнер вывода sklearn:

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

## <a name="package-models"></a>Модели пакетов

В некоторых случаях может потребоваться создать образ DOCKER без развертывания модели (например, если планируется [развертывание в службе приложений Azure](how-to-deploy-app-service.md)). Также можно загрузить образ и запустить его в локальной установке DOCKER. Может даже потребоваться загрузить файлы, используемые для создания образа, проверить их, изменить и создать образ вручную.

Упаковка модели позволяет выполнять эти задачи. Он упаковывает все ресурсы, необходимые для размещения модели в качестве веб-службы, и позволяет скачать полностью созданный образ DOCKER или файлы, необходимые для его создания. Существует два способа использования упаковки модели.

**Скачайте упакованную модель:** Скачайте образ DOCKER, содержащий модель и другие файлы, необходимые для размещения его в качестве веб-службы.

**Создайте Dockerfile:** Скачайте Dockerfile, модель, сценарий входа и другие ресурсы, необходимые для создания образа DOCKER. Затем можно проверить файлы или внести изменения, прежде чем создавать образ локально.

Оба пакета можно использовать для получения образа локального DOCKER.

> [!TIP]
> Создание пакета аналогично развертыванию модели. Вы используете зарегистрированную модель и конфигурацию вывода.

> [!IMPORTANT]
> Чтобы загрузить полностью собранный образ или создать образ локально, необходимо установить [DOCKER](https://www.docker.com) в среде разработки.

### <a name="download-a-packaged-model"></a>Загрузка упакованной модели

В следующем примере создается образ, который регистрируется в реестре контейнеров Azure для вашей рабочей области:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

После создания пакета можно использовать `package.pull()` для извлечения образа в локальную среду DOCKER. Выходные данные этой команды будут отображать имя изображения. Например, 

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. 

После загрузки модели используйте команду `docker images`, чтобы вывести список локальных образов:

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43GB
```

Чтобы запустить локальный контейнер на основе этого образа, используйте следующую команду для запуска именованного контейнера из оболочки или командной строки. Замените `<imageid>` значение ИДЕНТИФИКАТОРом образа, возвращенным командой `docker images`.

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

Эта команда запускает последнюю версию образа с именем `myimage`. Он сопоставляет локальный порт 6789 с портом в контейнере, прослушиваемом веб-службой (5001). Он также назначает имя `mycontainer` контейнеру, что делает контейнер более легким для его завершения. После запуска контейнера можно отправлять запросы в `http://localhost:6789/score`.

### <a name="generate-a-dockerfile-and-dependencies"></a>Создание Dockerfile и зависимостей

В следующем примере показано, как загрузить Dockerfile, модель и другие ресурсы, необходимые для создания образа локально. Параметр `generate_dockerfile=True` указывает, что требуется, чтобы файлы, а не полностью построенные образы.

```python
package = Model.package(ws, [model], inference_config, generate_dockerfile=True)
package.wait_for_creation(show_output=True)
# Download the package.
package.save("./imagefiles")
# Get the Azure container registry that the model/Dockerfile uses.
acr=package.get_container_registry()
print("Address:", acr.address)
print("Username:", acr.username)
print("Password:", acr.password)
```

Этот код скачивает файлы, необходимые для сборки образа, в каталог `imagefiles`. Dockerfile, включаемые в сохраненные файлы, ссылаются на базовый образ, хранящийся в реестре контейнеров Azure. При создании образа в локальной установке DOCKER необходимо использовать адрес, имя пользователя и пароль для проверки подлинности в реестре. Выполните следующие действия, чтобы создать образ с помощью локальной установки docker:

1. Чтобы проверить подлинность DOCKER с помощью реестра контейнеров Azure, из оболочки или сеанса командной строки используйте следующую команду. Замените `<address>`, `<username>`и `<password>` значениями, полученными с помощью `package.get_container_registry()`.

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. Чтобы создать образ, используйте следующую команду. Замените `<imagefiles>` на путь к каталогу, в котором `package.save()` сохранить файлы.

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    Эта команда задает имя образа для `myimage`.

Чтобы убедиться, что образ построен, используйте команду `docker images`. Вы должны увидеть изображение `myimage` в списке:

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43GB
myimage         latest              739f22498d64        3 minutes ago       1.43GB
```

Чтобы запустить новый контейнер на основе этого образа, используйте следующую команду:

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

Эта команда запускает последнюю версию образа с именем `myimage`. Он сопоставляет локальный порт 6789 с портом в контейнере, прослушиваемом веб-службой (5001). Он также назначает имя `mycontainer` контейнеру, что делает контейнер более легким для его завершения. После запуска контейнера можно отправлять запросы в `http://localhost:6789/score`.

### <a name="example-client-to-test-the-local-container"></a>Пример клиента для тестирования локального контейнера

Следующий код является примером клиента Python, который можно использовать с контейнером:

```python
import requests
import json

# URL for the web service.
scoring_uri = 'http://localhost:6789/score'

# Two sets of data to score, so we get two results back.
data = {"data":
        [
            [ 1,2,3,4,5,6,7,8,9,10 ],
            [ 10,9,8,7,6,5,4,3,2,1 ]
        ]
        }
# Convert to JSON string.
input_data = json.dumps(data)

# Set the content type.
headers = {'Content-Type': 'application/json'}

# Make the request and display the response.
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

Примеры клиентов на других языках программирования см. в разделе [Использование моделей, развернутых в качестве веб-служб](how-to-consume-web-service.md).

### <a name="stop-the-docker-container"></a>Останавливает контейнер DOCKER

Чтобы прерывать контейнер, используйте следующую команду из другой оболочки или командной строки:

```bash
docker kill mycontainer
```

## <a name="preview-no-code-model-deployment"></a>Образца Развертывание модели без кода

Развертывание модели без кода сейчас находится на этапе предварительной версии и поддерживает следующие платформы машинного обучения:

### <a name="tensorflow-savedmodel-format"></a>Формат Саведмодел Tensorflow

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='flowers',                        # Name of the registered model in your workspace.
                       model_path='./flowers_model',                # Local Tensorflow SavedModel folder to upload and register as a model.
                       model_framework=Model.Framework.TENSORFLOW,  # Framework used to create the model.
                       model_framework_version='1.14.0',            # Version of Tensorflow used to create the model.
                       description='Flowers model')

service_name = 'tensorflow-flower-service'
service = Model.deploy(ws, service_name, [model])
```

### <a name="onnx-models"></a>Модели ONNX

Регистрация и развертывание модели ONNX поддерживаются для любого графа вывода ONNX. Этапы предварительной обработки и выполнения процессов в настоящее время не поддерживаются.

Ниже приведен пример регистрации и развертывания модели MNIST ONNX.

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='mnist-sample',                  # Name of the registered model in your workspace.
                       model_path='mnist-model.onnx',              # Local ONNX model to upload and register as a model.
                       model_framework=Model.Framework.ONNX ,      # Framework used to create the model.
                       model_framework_version='1.3',              # Version of ONNX used to create the model.
                       description='Onnx MNIST model')

service_name = 'onnx-mnist-service'
service = Model.deploy(ws, service_name, [model])
```

### <a name="scikit-learn-models"></a>Scikit — изучение моделей

Не поддерживается развертывание модели кода для всех встроенных типов моделей scikit-учиться.

Ниже приведен пример регистрации и развертывания модели sklearn без дополнительного кода.

```python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version='0.19.1',             # Version of scikit-learn used to create the model.
                       resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})
                       
service_name = 'my-sklearn-service'
service = Model.deploy(ws, service_name, [model])
```

Примечание. Эти зависимости включены в готовый контейнер вывода sklearn:

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Для удаления развернутой веб-службы используйте `service.delete()`.
Чтобы удалить зарегистрированную модель, используйте `model.delete()`.

Дополнительные сведения см. в документации по [WebService. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) и [model. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## <a name="next-steps"></a>Дополнительная информация

* [Развертывание модели с помощью пользовательского образа DOCKER](how-to-deploy-custom-docker-image.md)
* [Устранение неполадок развертывания](how-to-troubleshoot-deployment.md)
* [Защита веб-служб Машинного обучения Azure с помощью SSL](how-to-secure-web-service.md)
* [Использование Машинное обучение Azureной модели, развернутой в качестве веб-службы](how-to-consume-web-service.md)
* [Мониторинг моделей машинного обучения в Azure с помощью Application Insights](how-to-enable-app-insights.md)
* [Сбор данных для моделей в рабочей среде](how-to-enable-data-collection.md)

