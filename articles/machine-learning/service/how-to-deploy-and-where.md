---
title: Как и где развертываются модели
titleSuffix: Azure Machine Learning service
description: 'Сведения о том, как и где следует развертывать модели Службы машинного обучения Azure, в том числе: Экземпляры контейнеров Azure, Службу Azure Kubernetes, Azure IoT Edge и программируемые пользователем вентильные матрицы.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 08/06/2019
ms.custom: seoapril2019
ms.openlocfilehash: acb3717f0e71ca1e67f1ddec79a259935f6cc539
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69897688"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Развертывание моделей с помощью Службы машинного обучения Azure

Узнайте, как развернуть модель машинного обучения в качестве веб-службы в облаке Azure или IoT Edge устройств.

Рабочий процесс аналогичен, независимо от того, [где развертывается](#target) модель.

1. Регистрация модели.
1. Подготовка к развертыванию (указание ресурсов, использования, целевого объекта вычислений).
1. Разверните модель в целевом объекте вычислений.
1. Протестируйте развернутую модель, также называемую веб-службой.

Дополнительные сведения об основных понятиях, связанных с рабочим процессом развертывания, см. в статье [Администрирование, развертывание и мониторинг моделей с помощью службы "Машинное обучение Azure"](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>предварительные требования

- Рабочая область службы машинного обучения Azure. Дополнительные сведения см. в статье [Создание рабочей области службы машинное обучение Azure](how-to-manage-workspace.md).

- Модель. Если у вас нет обученной модели, можно использовать модель & файлов зависимостей, предоставленных в [этом руководстве](https://aka.ms/azml-deploy-cloud).

- [Расширение Azure CLI для службы машинное обучение](reference-azure-machine-learning-cli.md), [машинное обучение Azure пакет SDK для Python](https://aka.ms/aml-sdk)или [расширение машинное обучение Azure Visual Studio Code](how-to-vscode-tools.md).

## <a name="connect-to-your-workspace"></a>Подключение к рабочей области

В следующем коде показано, как подключиться к рабочей области службы Машинное обучение Azure с помощью информации, кэшированной в локальную среду разработки:

**Использование пакета SDK**

```python
from azureml.core import Workspace
ws = Workspace.from_config(path=".file-path/ws_config.json")
```

Дополнительные сведения об использовании пакета SDK для подключения к рабочей области см. в разделе [пакет SDK для машинное обучение Azure для Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace).

**Использование интерфейса командной строки**

При использовании интерфейса командной строки используйте `-w` параметр или `--workspace-name` , чтобы указать рабочую область для команды.

**Использование VS Code**

При использовании VS Code Рабочая область выбирается с помощью графического интерфейса. Дополнительные сведения см. в разделе [Развертывание моделей и управление ими](how-to-vscode-tools.md#deploy-and-manage-models) в документации по расширению VS Code.

## <a id="registermodel"></a>Регистрация модели

Зарегистрированная модель — это логический контейнер для одного или нескольких файлов, составляющих модель. Например, если имеется модель, которая хранится в нескольких файлах, можно зарегистрировать их как единую модель в рабочей области. После регистрации можно скачать или развернуть зарегистрированную модель и получить все зарегистрированные файлы.

> [!TIP]
> При регистрации модели вы предоставляете путь к облачному расположению (из обучающего запуска) или к локальному каталогу. Этот путь предназначен только для поиска файлов для отправки в процессе регистрации. Он не должен соответствовать пути, используемому в скрипте записи. Дополнительные сведения см. в разделе [что такое get_model_path](#what-is-get_model_path).

Модели машинного обучения регистрируются в рабочей области Машинное обучение Azure. Модель может быть взята из Машинное обучение Azure или может быть где-либо другим. В следующих примерах показано, как зарегистрировать модель.

### <a name="register-a-model-from-an-experiment-run"></a>Регистрация модели из запуска эксперимента

Фрагменты кода в этом разделе демонстрируют регистрацию модели из обучающего запуска:

> [!IMPORTANT]
> В этих фрагментах предполагается, что вы ранее выполнили обучающий запуск и получили `run` доступ к объекту (пример пакета SDK) или идентификатору запуска (пример с интерфейсом командной строки). Дополнительные сведения о моделях обучения см. в статье [Создание и использование целевых объектов вычислений для обучения модели](how-to-set-up-training-targets.md).

+ **Использование пакета SDK**

  ```python
  model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
  print(model.name, model.id, model.version, sep='\t')
  ```

  Объект `model_path` ссылается на облачное расположение модели. В этом примере используется путь к одному файлу. Чтобы включить несколько файлов в регистрацию модели, установите `model_path` в каталог, содержащий файлы.

+ **Использование интерфейса командной строки**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid
  ```

  [!INCLUDE [install extension](../../../includes/machine-learning-service-install-extension.md)]

  Объект `--asset-path` ссылается на облачное расположение модели. В этом примере используется путь к одному файлу. Чтобы включить несколько файлов в регистрацию модели, установите `--asset-path` в каталог, содержащий файлы.

+ **Использование VS Code**

  Зарегистрируйте модели с помощью любых файлов или папок модели с расширением [VS Code](how-to-vscode-tools.md#deploy-and-manage-models) .

### <a name="register-a-model-from-a-local-file"></a>Регистрация модели из локального файла

Можно зарегистрировать модель, указав **локальный путь** к модели. Можно указать либо папку, либо отдельный файл. Этот метод можно использовать для регистрации моделей, обученных в службе Машинное обучение Azure, а затем загрузки или моделей, обученных за пределами Машинное обучение Azure.

[!INCLUDE [trusted models](../../../includes/machine-learning-service-trusted-model.md)]

+ **Пример ONNX с пакетом SDK для Python:**

    ```python
    import os
    import urllib.request
    from azureml.core import Model
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

  Чтобы включить несколько файлов в регистрацию модели, установите `model_path` в каталог, содержащий файлы.

+ **Использование интерфейса командной строки**

  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  Чтобы включить несколько файлов в регистрацию модели, установите `-p` в каталог, содержащий файлы.

**Оценка времени**. Примерно 10 секунд.

Дополнительные сведения см. в справочной документации по [классу Model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

Дополнительные сведения о работе с моделями, обученными вне Машинное обучение Azure службы, см. [в разделе Развертывание существующей модели](how-to-deploy-existing-model.md).

<a name="target"></a>

## <a name="choose-a-compute-target"></a>Выбор целевого объекта вычислений

Для размещения развертывания веб-службы можно использовать следующие целевые объекты вычислений или ресурсы вычислений. 

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

## <a name="prepare-to-deploy"></a>Подготовка к развертыванию

Для развертывания модели требуется несколько моментов.

* __Скрипт записи__. Этот скрипт принимает запросы, оценивает запрос с помощью модели и возвращает результаты.

    > [!IMPORTANT]
    > Сценарий записи зависит от модели. Он должен понимать формат данных входящего запроса, формат данных, ожидаемых моделью, и формат данных, возвращаемых клиентам.
    >
    > Если данные запроса имеют формат, непригодный для использования в вашей модели, скрипт может преобразовать его в допустимый формат. Он также может преобразовать ответ перед возвратом клиенту.

    > [!IMPORTANT]
    > Пакет SDK для Машинное обучение Azure не предоставляет возможности развертывания веб-служб или IoT Edge для доступа к хранилищу данных или наборам. Если требуется развернутая модель для доступа к данным, хранящимся за пределами развертывания, например в учетной записи хранения Azure, необходимо разработать собственное решение с кодом, используя соответствующий пакет SDK. Например, [пакет SDK для службы хранилища Azure для Python](https://github.com/Azure/azure-storage-python).
    >
    > Другой альтернативой, который может работать в вашем сценарии, являются [пакетные прогнозы](how-to-run-batch-predictions.md), которые обеспечивают доступ к хранилищам данных при оценке.

* **Зависимости**, такие как вспомогательные скрипты или пакеты Python или Conda, необходимые для запуска сценария записи или модели

* __Конфигурация развертывания__ для целевого объекта вычислений, в котором развернута модель. Эта конфигурация описывает такие вещи, как память и требования к ЦП, необходимые для запуска модели.

Эти сущности инкапсулированы в __конфигурацию вывода__и __конфигурацию развертывания__. Конфигурация вывода ссылается на скрипт записи и другие зависимости. Эти конфигурации определяются программно при использовании пакета SDK и в виде файлов JSON при использовании CLI для выполнения развертывания.

### <a id="script"></a> 1. Определение скрипта записи & зависимостей

Сценарий записи получает данные, отправленные в развернутую веб-службу, и передает их в модель. Затем он принимает ответ, возвращенный моделью, и возвращает его клиенту. **Сценарий зависит от модели**. Он должен понимать данные, которые предположительно и возвращает модель.

Скрипт содержит две функции, которые загружают и запускают модель:

* `init()`. Обычно эта функция загружает модель в глобальный объект. Эта функция выполняется только один раз при запуске контейнера DOCKER для веб-службы.

* `run(input_data)`. Эта функция использует модель для прогнозирования значения на основе входных данных. Ко входным и выходным данным для запуска обычно применяется формат JSON для сериализации и десериализации. Вы также можете работать с необработанными двоичными данными. Вы можете преобразовать данные, прежде чем отправлять их в модель или возвращать клиенту.

#### <a name="what-is-get_model_path"></a>Что такое get_model_path?

При регистрации модели вы предоставляете имя модели, используемое для управления моделью в реестре. Это имя используется вместе с [моделью. Get _model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) для получения пути к файлам модели в локальной файловой системе. При регистрации папки или коллекции файлов этот API возвращает путь к каталогу, содержащему эти файлы.

При регистрации модели ей присваивается имя, которое соответствует месту размещения модели: локально или во время развертывания службы.

В приведенном ниже примере возвращается путь к одному файлу с именем `sklearn_mnist_model.pkl` (зарегистрированным с именем `sklearn_mnist`):

```python
model_path = Model.get_model_path('sklearn_mnist')
```

<a id="schema"></a>

#### <a name="optional-automatic-schema-generation"></a>Используемых Автоматическое создание схем

Чтобы автоматически создать схему для веб-службы, укажите образец входных и (или) выходных данных в конструкторе для одного из объектов определенного типа, а тип и образец используются для автоматического создания схемы. Затем служба Машинное обучение Azure создает спецификацию [OpenAPI](https://swagger.io/docs/specification/about/) (Swagger) для веб-службы во время развертывания.

В настоящее время поддерживаются следующие типы:

* `pandas`
* `numpy`
* `pyspark`
* Стандартный объект Python

Чтобы использовать формирование схемы, включите `inference-schema` пакет в файл среды conda.

##### <a name="example-dependencies-file"></a>Пример файла зависимостей

Следующий YAML является примером файла зависимостей Conda для вывода:

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - pip:
    - azureml-defaults
    - scikit-learn==0.20.0
    - inference-schema[numpy-support]
```

Если вы хотите использовать автоматическое создание схем, ваш сценарий записи **должен** импортировать `inference-schema` пакеты.

Определите форматы входных и выходных данных в `input_sample` переменных и `output_sample` , которые представляют форматы запросов и ответов для веб-службы. Используйте эти примеры в декораторах `run()` входных и выходных функций функции. В приведенном ниже примере scikit-учиться используется создание схем.

##### <a name="example-entry-script"></a>Пример скрипта записи

В следующем примере показано, как принимать и возвращать данные JSON.

```python
#example: scikit-learn and Swagger
import json
import numpy as np
from sklearn.externals import joblib
from sklearn.linear_model import Ridge
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    # note here "sklearn_regression_model.pkl" is the name of the model registered under
    # this is a different behavior than before when the code is run locally, even though the code is the same.
    model_path = Model.get_model_path('sklearn_regression_model.pkl')
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = np.array([[10, 9, 8, 7, 6, 5, 4, 3, 2, 1]])
output_sample = np.array([3726.995])


@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # you can return any datatype as long as it is JSON-serializable
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

В следующем примере показано, как определить входные данные в виде `<key: value>` словаря с помощью таблицы данных. Этот метод поддерживается для использования развернутой веб-службы из Power BI (дополнительные[сведения об использовании веб-службы из Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-integration)):

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
    # replace model_name with your actual model name, if needed
    model_path = Model.get_model_path('model_name')
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    # This is a decimal type sample. Use the data type that reflects this column in your data
    "input_name_1": 5.1,
    # This is a string type sample. Use the data type that reflects this column in your data
    "input_name_2": "value2",
    # This is a integer type sample. Use the data type that reflects this column in your data
    "input_name_3": 3
}])

# This is a integer type sample. Use the data type that reflects the expected result
output_sample = np.array([0])


@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # you can return any datatype as long as it is JSON-serializable
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Дополнительные примеры сценариев см. в следующих примерах.

* Pytorch[https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch)
* TensorFlow[https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow)
* Keras[https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* ONNX[https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)

<a id="binary"></a>

#### <a name="binary-data"></a>Двоичные данные

Если ваша модель принимает двоичные данные, например изображения, необходимо изменить файл `score.py`, используемый для развертывания, чтобы обеспечить прием необработанных HTTP-запросов. Чтобы принимать необработанные данные, `AMLRequest` используйте класс в скрипте записи и `@rawhttp` добавьте декоратор в `run()` функцию.

Ниже приведен пример `score.py` , который принимает двоичные данные:

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
        # For this example, just return the URL for GETs
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real world solution, you would load the data from reqBody
        # and send to the model. Then return the response.

        # For demonstration purposes, this example just returns the posted data as the response.
        return AMLResponse(reqBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> `AMLRequest` Класс находится`azureml.contrib` в пространстве имен. Это пространство имен часто меняется, так как мы работаем над улучшением службы. Поэтому все, что доступно в этом пространстве имен, считается предварительными версиями компонентов, поддержка которых корпорацией Майкрософт ограничена.
>
> Если необходимо протестировать это в локальной среде разработки, можно установить компоненты с помощью следующей команды:
>
> ```shell
> pip install azureml-contrib-services
> ```

<a id="cors"></a>

#### <a name="cross-origin-resource-sharing-cors"></a>Общий доступ к ресурсам между источниками (CORS)

Совместное использование ресурсов в разных источниках позволяет запрашивать ресурсы на веб-странице из другого домена. CORS работает на основе HTTP-заголовков, отправленных с помощью запроса клиента и возвращаемых с ответом службы. Дополнительные сведения о CORS и допустимых заголовках см. в разделе [общий доступ к ресурсам между источниками](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) в Википедии.

Чтобы настроить развертывание модели для поддержки CORS, используйте `AMLResponse` класс в скрипте записи. Этот класс позволяет задать заголовки для объекта ответа.

В следующем примере задается `Access-Control-Allow-Origin` заголовок для ответа из скрипта записи:

```python
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real world solution, you would load the data from reqBody
        # and send to the model. Then return the response.

        # For demonstration purposes, this example
        # adds a header and returns the request body
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> `AMLResponse` Класс находится`azureml.contrib` в пространстве имен. Это пространство имен часто меняется, так как мы работаем над улучшением службы. Поэтому все, что доступно в этом пространстве имен, считается предварительными версиями компонентов, поддержка которых корпорацией Майкрософт ограничена.
>
> Если необходимо протестировать это в локальной среде разработки, можно установить компоненты с помощью следующей команды:
>
> ```shell
> pip install azureml-contrib-services
> ```

### <a name="2-define-your-inferenceconfig"></a>2. Определение Инференцеконфиг

Конфигурация вывода описывает, как настроить модель для выполнения прогнозов. Эта конфигурация не является частью скрипта записи; Он ссылается на скрипт записи и используется для размещения всех ресурсов, необходимых для развертывания. Он используется позже при фактическом развертывании модели.

В следующем примере показано, как создать конфигурацию вывода. В этой конфигурации указывается среда выполнения, сценарий записи и (необязательно) файл среды conda:

```python
from azureml.core.model import InferenceConfig

inference_config = InferenceConfig(runtime="python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

Дополнительные сведения см. в справочнике по классу [инференцеконфиг](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) .

Сведения об использовании пользовательского образа DOCKER с конфигурацией вывода см. в статье [развертывание модели с помощью пользовательского образа DOCKER](how-to-deploy-custom-docker-image.md).

### <a name="cli-example-of-inferenceconfig"></a>Пример Инференцеконфиг в CLI

[!INCLUDE [inference config](../../../includes/machine-learning-service-inference-config.md)]

Следующая команда демонстрирует развертывание модели с помощью интерфейса командной строки.

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

В этом примере конфигурация содержит следующие элементы:

* Что для этой модели требуется Python
* [Скрипт записи](#script), используемый для обработки веб-запросов, отправляемых в развернутую службу
* Файл conda, описывающий пакеты Python, необходимые для вывода.

Сведения об использовании пользовательского образа DOCKER с конфигурацией вывода см. в статье [развертывание модели с помощью пользовательского образа DOCKER](how-to-deploy-custom-docker-image.md).

### <a name="3-define-your-deployment-configuration"></a>3. Определение конфигурации развертывания

Перед развертыванием необходимо определить конфигурацию развертывания. __Конфигурация развертывания зависит от целевого объекта вычислений, в котором будет размещена веб-служба__. Например, при локальном развертывании необходимо указать порт, на котором служба принимает запросы. Конфигурация развертывания не является частью скрипта записи. Он используется для определения характеристик целевого объекта вычислений, в котором будет размещаться скрипт модели и записи.

Также может потребоваться создать ресурс вычислений. Например, если у вас еще нет службы Azure Kubernetes, связанной с вашей рабочей областью.

В следующей таблице приведен пример создания конфигурации развертывания для каждого целевого объекта вычислений.

| Целевой объект вычисления | Пример конфигурации развертывания |
| ----- | ----- |
| Локальные | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Экземпляр контейнера Azure | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Служба Azure Kubernetes | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Каждый из этих классов для локальных веб-служб, ACI и AKS может быть импортирован из `azureml.core.webservice`:

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

> [!TIP]
> Перед развертыванием модели в качестве службы может потребоваться ее профилирование для определения оптимальных требований к ЦП и памяти. Модель можно профилировать с помощью пакета SDK или интерфейса командной строки. Дополнительные сведения см. в справочнике по профилю модели [()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#profile-workspace--profile-name--models--inference-config--input-data-) и [AZ ML](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile) .
>
> Результаты профилирования модели создаются в виде `Run` объекта. Дополнительные сведения см. в справочнике по классу [моделпрофиле](https://docs.microsoft.com/python/api/azureml-core/azureml.core.profile.modelprofile?view=azure-ml-py) .

## <a name="deploy-to-target"></a>Развертывание в целевом объекте

Развертывание использует конфигурацию развертывания конфигурации определения для развертывания моделей. Процесс развертывания аналогичен, независимо от целевого объекта вычислений. Развертывание в AKS немного отличается, так как необходимо предоставить ссылку на кластер AKS.

### <a id="local"></a>Локальное развертывание

Для локального развертывания на локальном компьютере должен быть установлен DOCKER.

#### <a name="using-the-sdk"></a>Использование пакета SDK

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Дополнительные сведения см. в справочной документации по [локалвебсервице](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), [model. deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-)и [WebService](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

#### <a name="using-the-cli"></a>Использование интерфейса командной строки

Для развертывания с помощью интерфейса командной строки используйте следующую команду. Замените `mymodel:1` на имя и версию зарегистрированной модели:

```azurecli-interactive
az ml model deploy -m mymodel:1 -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../../includes/machine-learning-service-local-deploy-config.md)]

Дополнительные сведения см. в справочнике по [развертыванию модели языка AZ ML](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) .

### <a id="notebookvm"></a>Веб-служба Нотебуквм (DEVTEST)

См. раздел [развертывание модели на виртуальных машинах записных книжек](how-to-deploy-local-container-notebook-vm.md).

### <a id="aci"></a>Экземпляры контейнеров Azure (DEVTEST)

См. раздел [развертывание в службе "экземпляры контейнеров Azure](how-to-deploy-azure-container-instance.md)".

### <a id="aks"></a>Служба Kubernetes Azure (DEVTEST & PRODUCTION)

См. раздел [Deploy to Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md).

## <a name="consume-web-services"></a>Использование веб-служб

Каждая развернутая веб-служба предоставляет REST API, что позволяет создавать клиентские приложения на различных языках программирования. Если для службы включен ключ проверки подлинности, необходимо предоставить ключ службы в качестве маркера в заголовке запроса.
Если вы включили проверку подлинности с помощью маркеров для службы, необходимо предоставить маркер JWT Машинное обучение Azure в качестве токена носителя в заголовке запроса.

> [!TIP]
> После развертывания службы можно получить документ JSON схемы. Используйте [свойство swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri) из развернутой веб-службы, например `service.swagger_uri`, чтобы получить универсальный код ресурса (URI) для файла Swagger локальной веб-службы.

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

Дополнительные сведения см. в статье [Использование модели Машинного обучения Azure, развернутой в качестве веб-службы](how-to-consume-web-service.md).

### <a name="web-service-schema-openapi-specification"></a>Схема веб-службы (спецификация OpenAPI)

Если вы использовали автоматическое создание схемы в развертывании, можно получить адрес спецификации OpenAPI для службы с помощью [Свойства swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri). Например, `print(service.swagger_uri)`. Чтобы получить спецификацию, используйте запрос GET (или откройте универсальный код ресурса (URI) в браузере).

Следующий документ JSON является примером схемы (спецификации OpenAPI), созданной для развертывания:

```json
{
    "swagger": "2.0",
    "info": {
        "title": "myservice",
        "description": "API specification for the Azure Machine Learning service myservice",
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

Дополнительные сведения о спецификации см. в [спецификации Open API](https://swagger.io/specification/).

Сведения о программе, которая может создавать клиентские библиотеки из спецификации, см. в разделе [Swagger-CodeGen](https://github.com/swagger-api/swagger-codegen).

### <a id="azuremlcompute"></a>Вывод пакета
Машинное обучение Azure целевые объекты вычислений создаются и управляются службой Машинное обучение Azure. Их можно использовать для прогнозирования пакетной службы из Машинное обучение Azure конвейеров.

Пошаговое руководство по выводу пакетов с помощью Машинное обучение Azure COMPUTE см. в статье [выполнение пакетных прогнозов](how-to-run-batch-predictions.md) .

### <a id="iotedge"></a>Вывод IoT Edge
Поддержка развертывания на границе доступна в предварительной версии. Дополнительные сведения см. в статье [развертывание машинное обучение Azure как модуля IOT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning) .


## <a id="update"></a>Обновление веб-служб

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="continuous-model-deployment"></a>Непрерывное развертывание модели 

Вы можете непрерывно развертывать модели с помощью расширения Машинное обучение для [Azure DevOps](https://azure.microsoft.com/services/devops/). С помощью расширения Машинное обучение для Azure DevOps можно активировать конвейер развертывания, когда новая модель машинного обучения регистрируется в рабочей области службы Машинное обучение Azure. 

1. Подпишитесь на [Azure pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops), что обеспечивает непрерывную интеграцию и доставку приложения на любую платформу или любое облако. Azure Pipelines [отличается от конвейеров машинного обучения](concept-ml-pipelines.md#compare). 

1. [Создайте проект Azure DevOps.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. Установка [расширения машинное обучение для Azure pipelines](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList) 

1. Используйте __подключения__ к службам, чтобы настроить подключение субъекта-службы к рабочей области службы машинное обучение Azure для доступа ко всем артефактам. Перейдите в раздел Параметры проекта, щелкните подключения службы и выберите Azure Resource Manager.

    [![Представление-служба — подключение](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png) 

1. Определите Азуремлворкспаце в качестве __уровня области__ и заполните последующие параметры.

    ![Просмотр-Azure-Resource-Manager](media/how-to-deploy-and-where/resource-manager-connection.png)

1. Далее, чтобы непрерывно развернуть модель машинного обучения с помощью Azure Pipelines, в разделе конвейеры выберите __выпуск__. Добавьте новый артефакт, выберите артефакт модели AzureML и подключение службы, созданное на предыдущем шаге. Выберите модель и версию для активации развертывания. 

    [![SELECT-Азуремлмодел-артефакт](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. Включите триггер модели для артефакта модели. При включении триггера каждый раз, когда указанная версия (например, в рабочей области регистрируется последняя версия этой модели. конвейер выпуска Azure DevOps запускается. 

    [![Enable-Model-Trigger](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

Дополнительные примеры проектов и примеры см. в следующем примере репозиториев:

* [https://github.com/Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [https://github.com/Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)

## <a name="clean-up-resources"></a>Очистка ресурсов
Для удаления развернутой веб-службы используйте `service.delete()`.
Чтобы удалить зарегистрированную модель, используйте `model.delete()`.

Дополнительные сведения см. в справочной документации по [WebService. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--)и [model. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## <a name="next-steps"></a>Следующие шаги
* [Развертывание модели с помощью пользовательского образа DOCKER](how-to-deploy-custom-docker-image.md)
* [Устранение неполадок развертывания](how-to-troubleshoot-deployment.md)
* [Защита веб-служб Машинного обучения Azure с помощью SSL](how-to-secure-web-service.md)
* [Использование модели Машинного обучения Azure, развернутой в качестве веб-службы](how-to-consume-web-service.md)
* [Мониторинг моделей машинного обучения в Azure с помощью Application Insights](how-to-enable-app-insights.md)
* [Сбор данных для моделей в рабочей среде](how-to-enable-data-collection.md)

