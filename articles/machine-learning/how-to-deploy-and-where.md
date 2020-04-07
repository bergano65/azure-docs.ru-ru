---
title: Как и где развертывать модели
titleSuffix: Azure Machine Learning
description: Узнайте, как и где развертывать модели машинного обучения Azure, включая наборы контейнеров Azure, службу Azure Kubernetes, Azure IoT Edge и блоки для полевых программируемых ворот.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 02/27/2020
ms.custom: seoapril2019
ms.openlocfilehash: 3fe13dcb35e6985d160f52b7ee3f9da4accd7806
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756668"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Развертывание моделей с помощью Машинного обучения Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Узнайте, как развернуть модель машинного обучения в виде веб-службы в облаке Azure или на устройствах Azure IoT Edge.

Рабочий процесс аналогичен независимо от того, [где вы развертываете](#target) модель:

1. Регистрация модели.
1. подготовка к развертыванию (настройка ресурсов, потребления, целевого объекта вычислений);
1. развертывание модели в целевом объекте вычислений;
1. Проверьте развернутую модель, также называемую веб-службой.

Для получения дополнительной информации о концепциях, участвующих в рабочем процессе развертывания, [см. Управление, развертывание и мониторинг моделей с помощью машинного обучения Azure.](concept-model-management-and-deployment.md)

## <a name="prerequisites"></a>Предварительные требования

- Рабочая область машинного обучения Azure. Для получения дополнительной информации [см.](how-to-manage-workspace.md)

- Модель. Если у вас нет обученной модели, вы можете использовать файлы модели и зависимости, представленные в [этом учебнике.](https://aka.ms/azml-deploy-cloud)

- [Расширение Azure CLI для службы машинного обучения,](reference-azure-machine-learning-cli.md) [SDK для машинного обучения Azure для Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)или расширение кода Visual Studio [Azure Machine Learning.](tutorial-setup-vscode-extension.md)

## <a name="connect-to-your-workspace"></a>Подключение к рабочей области

Следующий код показывает, как подключиться к рабочему пространству Машинного обучения Azure, используя информацию, кэшированную в локальной среде разработки:

+ **Использование пакета SDK**

   ```python
   from azureml.core import Workspace
   ws = Workspace.from_config(path=".file-path/ws_config.json")
   ```

  Для получения дополнительной информации об использовании SDK для подключения к рабочему пространству можно ознакомиться на сайте [Azure Machine Learning SDK для](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) документации Python.

+ **Использование интерфейса командной строки**

   При использовании CLI `-w` используйте `--workspace-name` или параметр для указания рабочего пространства для команды.

+ **Использование Visual Studio Code**

   При использовании Visual Studio Code вы выбираете рабочее пространство с помощью графического интерфейса. Для получения дополнительной информации [см. Развертывание и управление моделями](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model) в документации по расширению кода Visual Studio.

## <a name="register-your-model"></a><a id="registermodel"></a>Регистрация модели

Зарегистрированная модель является логическим контейнером для одного или нескольких файлов, вкоторыеруюных в вашу модель. Например, если у вас есть модель, которая хранится в нескольких файлах, вы можете зарегистрировать их как единую модель в рабочем пространстве. После регистрации файлов можно загрузить или развернуть зарегистрированную модель и получить все файлы, которые вы зарегистрировали.

> [!TIP]
> При регистрации модели вы предоставляете путь либо облачного местоположения (от обучаемого запуска) или локального каталога. Этот путь только для того, чтобы найти файлы для загрузки в рамках процесса регистрации. Он не должен соответствовать пути, используемому в скрипте входа. Для получения дополнительной информации смотрите [файлы модели Locate в скрипте входа.](#load-model-files-in-your-entry-script)

Модели машинного обучения зарегистрированы в рабочем пространстве Azure Machine Learning. Модель может быть получена из Azure Machine Learning или откуда-то еще. При регистрации модели можно дополнительно предоставить метаданные о модели. `tags` Словари `properties` и словари, которые применяются к регистрации моделей, могут быть использованы для фильтрации моделей.

Следующие примеры демонстрируют, как зарегистрировать модель.

### <a name="register-a-model-from-an-experiment-run"></a>Регистрация модели из эксперимента

Фрагменты кода в этом разделе демонстрируют, как зарегистрировать модель из обучаемого запуска:

> [!IMPORTANT]
> Чтобы использовать эти фрагменты, необходимо предварительно выполнить обучаемый запуск, `Run` и вам необходимо иметь доступ к объекту (пример SDK) или значению идентификатора выполнения (пример CLI). Для получения дополнительной информации [Set up compute targets for model training](how-to-set-up-training-targets.md)о обучающие модели см.

+ **Использование пакета SDK**

  При использовании SDK для обучения модели можно получить объект [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) или объект [AutoMLRun](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) в зависимости от того, как вы обучали модель. Каждый объект может быть использован для регистрации модели, созданной в ходе эксперимента.

  + Регистрация модели `azureml.core.Run` с объекта:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    Параметр `model_path` относится к облачному расположению модели. В этом примере используется путь одного файла. Чтобы включить несколько файлов в `model_path` регистрацию модели, установите на путь папки, содержащей файлы. Для получения дополнительной информации смотрите документацию [Run.register_model.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-)

  + Регистрация модели `azureml.train.automl.run.AutoMLRun` с объекта:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'mnist'})

        print(run.model_id)
    ```

    В этом `metric` примере `iteration` не указаны и параметры, поэтому будет зарегистрирована итерация с лучшей первичной метрикой. Значение, `model_id` возвращенное из выполнения, используется вместо имени модели.

    Для получения дополнительной информации см [register_model.](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-)

+ **Использование интерфейса командной строки**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
  ```

  [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

  Параметр `--asset-path` относится к облачному расположению модели. В этом примере используется путь одного файла. Чтобы включить несколько файлов в `--asset-path` регистрацию модели, установите на путь папки, содержащей файлы.

+ **Использование Visual Studio Code**

  Регистрируйте модели с помощью любых файлов модели или папок с помощью расширения [Visual Studio Code.](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model)

### <a name="register-a-model-from-a-local-file"></a>Регистрация модели из локального файла

Можно зарегистрировать модель, предоставив локальный путь модели. Можно предоставить путь папки или одного файла. Этот метод можно использовать для регистрации моделей, обученных с помощью Azure Machine Learning, а затем скачиваемых. Вы также можете использовать этот метод для регистрации моделей, обученных за пределами машинного обучения Azure.

[!INCLUDE [trusted models](../../includes/machine-learning-service-trusted-model.md)]

+ **Использование SDK и ONNX**

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

  Чтобы включить несколько файлов в `model_path` регистрацию модели, установите на путь папки, содержащей файлы.

+ **Использование интерфейса командной строки**

  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  Чтобы включить несколько файлов в `-p` регистрацию модели, установите на путь папки, содержащей файлы.

**Оценка времени**: Приблизительно 10 секунд.

Для получения дополнительной информации смотрите документацию для [класса Model.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)

Для получения дополнительной информации о работе с [How to deploy an existing model](how-to-deploy-existing-model.md)моделями, обученными за пределами Машинного Обучения Azure, см.

<a name="target"></a>

## <a name="single-versus-multi-model-endpoints"></a>Одноместные и многомодельные конечные точки
Azure ML поддерживает развертывание одной или нескольких моделей за одной конечней точкой.

Мультимодельные конечные точки используют общий контейнер для размещения нескольких моделей. Это помогает сократить накладные расходы, улучшает использование и позволяет объединять модули в ансамбли. Модели, указанные в сценарии развертывания, монтируются и доступны на диске выступающего контейнера - их можно загрузить в память по требованию и оценка на основе конкретной модели, запрашиваемой во время подсчета очков.

На примере E2E, который показывает, как использовать несколько моделей за одной контейнерной конечной точкой, [см.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-multi-model)

## <a name="prepare-to-deploy"></a>Подготовка к развертыванию

Для развертывания модели в качестве службы необходимы следующие компоненты:

* **Определите среду выводов.** Эта среда инкапсулирует зависимости, необходимые для выполнения модели для выводов.
* **Определите скоринговый код**. Этот скрипт принимает запросы, оценивает запросы с помощью модели и возвращает результаты.
* **Определите конфигурацию выводов.** Конфигурация выводов определяет конфигурацию среды, скрипт входа и другие компоненты, необходимые для выполнения модели в качестве службы.

После того, как у вас есть необходимые компоненты, можно профилировать службу, которая будет создана в результате развертывания модели, чтобы понять ее требования к процессору и памяти.

### <a name="1-define-inference-environment"></a>1. Определить среду выводов

Конфигурация выводов описывает, как настроить веб-службу, содержащую модель. Он используется позже, при развертывании модели.

Конфигурация Inference использует среды машинного обучения Azure для определения программных зависимостей, необходимых для развертывания. Среды позволяют создавать, управлять и повторно использовать программные зависимости, необходимые для обучения и развертывания. Можно создать среду из пользовательских файлов зависимостей или использовать одну из кураторских сред машинного обучения Azure. Следующий YAML является примером файла зависимостей Conda для выводов. Обратите внимание, что необходимо указать лазурные по умолчанию с verion >1.0.45 в качестве зависимости от пипсов, поскольку он содержит функциональность, необходимую для размещения модели в качестве веб-сервиса. Если вы хотите использовать автоматическое поколение схемы, ваш `inference-schema` скрипт входа также должен импортировать пакеты.

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - scikit-learn=0.20.0
  - pip:
      # You must list azureml-defaults as a pip dependency
    - azureml-defaults>=1.0.45
    - inference-schema[numpy-support]
```

> [!IMPORTANT]
> Если ваша зависимость доступна как через Conda, так и через pip (от PyPi), корпорация Майкрософт рекомендует использовать версию Conda, так как пакеты Conda обычно поставляются с заранее построенными файлами, которые делают установку более надежной.
>
> Для получения дополнительной информации, см [Понимание Конда и Пип](https://www.anaconda.com/understanding-conda-and-pip/).
>
> Чтобы проверить, доступна ли ваша зависимость через `conda search <package-name>` Conda, используйте команду [https://anaconda.org/anaconda/repo](https://anaconda.org/anaconda/repo) [https://anaconda.org/conda-forge/repo](https://anaconda.org/conda-forge/repo)или используйте индексы пакетов и .

Можно использовать файл зависимостей для создания объекта среды и сохранения его в рабочем пространстве для использования в будущем:

```python
from azureml.core.environment import Environment
myenv = Environment.from_conda_specification(name = 'myenv',
                                             file_path = 'path-to-conda-specification-file'
myenv.register(workspace=ws)
```

### <a name="2-define-scoring-code"></a><a id="script"></a>2. Определить скоринговый код

Скрипт входа принимает данные, отправляемые в развернутую веб-службу, и передает их в модель. Затем он принимает ответ, возвращенный моделью, и возвращает его клиенту. *Скрипт специфичен для вашей модели.* Он должен понимать данные, которые модель ожидает и возвращает.

Скрипт содержит две функции, которые загружают и запускают модель:

* `init()`: Как правило, эта функция загружает модель в глобальный объект. Эта функция запускается только один раз, когда используется контейнер Docker для вашего веб-сервиса.

* `run(input_data)`: Эта функция использует модель для прогнозирования значения, основанного на входных данных. Ко входным и выходным данным для запуска обычно применяется формат JSON для сериализации и десериализации. Вы также можете работать с необработанными двоичными данными. Вы можете преобразовать данные, прежде чем отправлять их в модель или возвращать клиенту.

#### <a name="load-model-files-in-your-entry-script"></a>Загрузите файлы модели в скрипте входа

Существует два способа найти модели в скрипте входа:
* `AZUREML_MODEL_DIR`: Переменная среда, содержащая путь к местоположению модели.
* `Model.get_model_path`API, который возвращает путь в файл модели с помощью зарегистрированного имени модели.

##### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

AZUREML_MODEL_DIR — это переменная среды, созданная при развертывании службы. Эту переменную среды можно использовать для определения местоположения развернутой модели(ы).

В следующей таблице описывается значение AZUREML_MODEL_DIR в зависимости от количества развернутых моделей:

| Развертывание | Переменное значение среды |
| ----- | ----- |
| Единая модель | Путь к папке, содержащей модель. |
| Несколько моделей | Путь к папке, содержащей все модели. Модели расположены по имени и версии в этой папке (`$MODEL_NAME/$VERSION`) |

Во время регистрации и развертывания моделей модели помещаются в AZUREML_MODEL_DIR путь, а исходные имена файлов сохраняются.

Чтобы получить путь к файлу модели в скрипте входа, объедините переменную среды с искомым способом файла.

**Пример одной модели**
```python
# Example when the model is a file
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')

# Example when the model is a folder containing a file
file_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'my_model_folder', 'sklearn_regression_model.pkl')
```

**Пример нескольких моделей**
```python
# Example when the model is a file, and the deployment contains multiple models
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_model', '1', 'sklearn_regression_model.pkl')
```

##### <a name="get_model_path"></a>get_model_path

При регистрации модели укажите имя модели, используемое для управления моделью в реестре. Это имя используется методом [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) для получения пути файла модели или файлов в локальной файловой системе. Если вы регистрируете папку или набор файлов, этот API возвращает путь каталога, содержащий эти файлы.

Когда вы регистрируете модель, вы даете ей имя. Название соответствует месту размещения модели локально или во время развертывания службы.

#### <a name="optional-define-model-web-service-schema"></a>(Необязательно) Определите схему веб-сервиса

Чтобы автоматически создать схему для веб-сервиса, предоставьте образец ввода и/или вывода в конструкторе для одного из объектов определенного типа. Тип и образец используются для автоматического создания схемы. Затем Azure Machine Learning создает спецификацию [OpenAPI](https://swagger.io/docs/specification/about/) (Swagger) для веб-службы во время развертывания.

Эти типы в настоящее время поддерживаются:

* `pandas`
* `numpy`
* `pyspark`
* Стандартный объект Python

Чтобы использовать генерацию схемы, включите пакет с открытым исходным кодом `inference-schema` в файл зависимостей. Для получения дополнительной информации [https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema)об этом пакете см. Определите форматы входных и `input_sample` выходных выборок в переменных и `output_sample` переменных, которые представляют форматы запроса и ответа для веб-службы. Используйте эти образцы в декораторах `run()` функции ввода и вывода на функции. На следующем примере scikit-learn используется генерация схем.

##### <a name="example-entry-script"></a>Пример сценария входа

Следующий пример показывает, как принимать и возвращать данные JSON:

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

В следующем примере показано, как определить `<key: value>` входные данные как словарь с помощью DataFrame. Этот метод поддерживается для потребления развернутого веб-сервиса от Power BI. ([Узнайте больше о том, как потреблять веб-сервис от Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-integration).)

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

Для получения дополнительных примеров приведены следующие сценарии:

* [PyTorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch)
* [TensorFlow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow)
* [Keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* [AutoML](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features)
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)
* [Двоичные данные](#binary)
* [CORS](#cors)

### <a name="3-define-inference-configuration"></a><a id="script"></a>3. Определить конфигурацию выводов
    
Следующий пример демонстрирует загрузку среды из рабочего пространства, а затем использовать ее с конфигурацией выводов:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


myenv = Environment.get(workspace=ws, name='myenv', version='1')
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                   environment=myenv)
```

Для получения дополнительной информации [Create and manage environments for training and deployment](how-to-use-environments.md)о средах см.

Для получения дополнительной информации о [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) конфигурации выводов см.

Для получения информации об использовании пользовательского изображения Docker с конфигурацией выводов см. [Как развернуть модель с помощью пользовательского изображения Docker.](how-to-deploy-custom-docker-image.md)

#### <a name="cli-example-of-inferenceconfig"></a>CLI пример InferenceConfig

[!INCLUDE [inference config](../../includes/machine-learning-service-inference-config.md)]

Следующая команда демонстрирует, как развернуть модель с помощью CLI:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

В этом примере конфигурация определяет следующие параметры:

* Что модель требует Python.
* [Скрипт входа,](#script)который используется для обработки веб-запросов, отправленных в развернутую службу.
* Файл Conda, описывающий пакеты Python, необходимые для выводов.

Для получения информации об использовании пользовательского изображения Docker с конфигурацией выводов см. [Как развернуть модель с помощью пользовательского изображения Docker.](how-to-deploy-custom-docker-image.md)

### <a name="4-optional-profile-your-model-to-determine-resource-utilization"></a><a id="profilemodel"></a>4. (Необязательно) Профиль модели для определения использования ресурсов

После того как вы зарегистрировали модель и подготовили другие компоненты, необходимые для ее развертывания, можно определить процессор и память развернутой службы. Профилирование тестирует службу, которая запускает модель, и возвращает информацию, такую как использование процессора, использование памяти и задержка ответа. Он также предоставляет рекомендацию для процессора и памяти на основе использования ресурсов.

Для того, чтобы профилировать свою модель, вам потребуется:
* Зарегистрированная модель.
* Конфигурация выводов, основанная на скрипте входа и определении среды выводов.
* Единый табеобразный набор столбцов, где каждая строка содержит строку, представляющую данные запроса выборки.

> [!IMPORTANT]
> На данный момент мы поддерживаем только профилирование служб, которые ожидают, что данные запроса будут строкой, например: строка сериализованного json, текст, строка сериализованного изображения и т.д. Содержимое каждой строки набора данных (строки) будет помещено в тело запроса HTTP и отправлено в службу инкапсуляции модели для скоринга.

Ниже приведен пример того, как можно создать набор входных данных для профиля службы, которая ожидает, что данные входящего запроса будут содержать серийный json. В этом случае мы создали набор данных, основанный на ста экземплярах одного и того же содержимого данных запроса. В реальных сценариях мы предлагаем использовать большие наборы данных, содержащие различные входные данные, особенно если использование/поведение ресурсов модели зависит от ввода.

```python
import json
from azureml.core import Datastore
from azureml.core.dataset import Dataset
from azureml.data import dataset_type_definitions

input_json = {'data': [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
                       [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
# create a string that can be utf-8 encoded and
# put in the body of the request
serialized_input_json = json.dumps(input_json)
dataset_content = []
for i in range(100):
    dataset_content.append(serialized_input_json)
dataset_content = '\n'.join(dataset_content)
file_name = 'sample_request_data.txt'
f = open(file_name, 'w')
f.write(dataset_content)
f.close()

# upload the txt file created above to the Datastore and create a dataset from it
data_store = Datastore.get_default(ws)
data_store.upload_files(['./' + file_name], target_path='sample_request_data')
datastore_path = [(data_store, 'sample_request_data' +'/' + file_name)]
sample_request_data = Dataset.Tabular.from_delimited_files(
    datastore_path, separator='\n',
    infer_column_types=True,
    header=dataset_type_definitions.PromoteHeadersBehavior.NO_HEADERS)
sample_request_data = sample_request_data.register(workspace=ws,
                                                   name='sample_request_data',
                                                   create_new_version=True)
```

После того, как набор данных, содержащий данные запроса выборки, готов, создайте конфигурацию выводов. Конфигурация выводов основана на score.py и определении среды. Следующий пример показывает, как создать конфигурацию выводов и выполнить профилирование:

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.dataset import Dataset


model = Model(ws, id=model_id)
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                   environment=myenv)
input_dataset = Dataset.get_by_name(workspace=ws, name='sample_request_data')
profile = Model.profile(ws,
            'unique_name',
            [model],
            inference_config,
            input_dataset=input_dataset)

profile.wait_for_completion(True)

# see the result
details = profile.get_details()
```

Следующая команда демонстрирует, как профилировать модель с помощью CLI:

```azurecli-interactive
az ml model profile -g <resource-group-name> -w <workspace-name> --inference-config-file <path-to-inf-config.json> -m <model-id> --idi <input-dataset-id> -n <unique-name>
```

> [!TIP]
> Для сохранения информации, возвращаемой профилированием, используйте теги или свойства для модели. Использование тегов или свойств хранит данные с моделью в реестре моделей. Следующие примеры демонстрируют добавление `requestedCpu` нового `requestedMemoryInGb` тега, содержащего и информацию:
>
> ```python
> model.add_tags({'requestedCpu': details['requestedCpu'],
>                 'requestedMemoryInGb': details['requestedMemoryInGb']})
> ```
>
> ```azurecli-interactive
> az ml model profile -g <resource-group-name> -w <workspace-name> --i <model-id> --add-tag requestedCpu=1 --add-tag requestedMemoryInGb=0.5
> ```

## <a name="deploy-to-target"></a>Развертывание в цель

Развертывание использует конфигурацию развертывания конфигурации выводов для развертывания моделей. Процесс развертывания аналогичен независимо от цели вычислений. Развертывание в AKS немного отличается, потому что вы должны предоставить ссылку на кластер AKS.

### <a name="choose-a-compute-target"></a>Выбрать цель вычислений

Для развертывания веб-службы можно использовать следующие целевые показатели вычислений или вычислять ресурсы:

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

### <a name="define-your-deployment-configuration"></a>Определение конфигурации развертывания

Перед развертыванием модели необходимо определить конфигурацию развертывания. *Конфигурация развертывания специфична для вычислительной цели, в которую будет размещен веб-сервис.* Например, при развертывании модели локально необходимо указать порт, в котором служба принимает запросы. Конфигурация развертывания не является частью скрипта входа. Он используется для определения характеристик вычислительной цели, в которых будет размещена модель и скрипт входа.

Возможно, потребуется создать ресурс вычислений, если, например, у вас еще нет экземпляра службы Azure Kubernetes (AKS), связанного с рабочим пространством.

В следующей таблице приводится пример создания конфигурации развертывания для каждой вычислительной цели:

| Целевой объект вычисления | Пример конфигурации развертывания |
| ----- | ----- |
| Local | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Экземпляры контейнеров Azure | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Служба Azure Kubernetes | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Классы для локальных, Azure Контейнерных служб и веб-сервисов AKS могут быть импортированы из: `azureml.core.webservice`

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

### <a name="securing-deployments-with-tls"></a>Обеспечение развертывания с помощью TLS

Для получения дополнительной информации о том, как обеспечить развертывание веб-службы, [см.](how-to-secure-web-service.md#enable)

### <a name="local-deployment"></a><a id="local"></a>Локальное развертывание

Чтобы развернуть модель локально, необходимо установить Docker на локальной машине.

#### <a name="using-the-sdk"></a>Использование пакета SDK

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Для получения дополнительной информации смотрите документацию для [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)и [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

#### <a name="using-the-cli"></a>Использование интерфейса командной строки

Чтобы развернуть модель с помощью CLI, используйте следующую команду. Заменить `mymodel:1` имя и версию зарегистрированной модели:

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

Для получения дополнительной [az ml model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) информации см.

### <a name="understanding-service-state"></a>Понимание состояния службы

Во время развертывания модели можно увидеть изменение состояния службы во время полного развертывания.

В следующей таблице описаны различные состояния службы:

| Состояние Веб-сервиса | Описание | Окончательное состояние?
| ----- | ----- | ----- |
| Переход | Служба находится в процессе развертывания. | нет |
| Unhealthy; | Служба развернута, но в настоящее время недостижима.  | нет |
| Незасеченные | Служба не может быть развернута в настоящее время из-за нехватки ресурсов. | нет |
| Ошибка | Служба не удалось развернуть из-за ошибки или сбоя. | Да |
| Healthy | Услуга является работоспособной и конечная точка доступна. | Да |

### <a name="compute-instance-web-service-devtest"></a><a id="notebookvm"></a>Вычислить экземпляр веб-службы (dev/test)

[См. Развертывание модели для вычислений Azure Machine Learning экземпляра](how-to-deploy-local-container-notebook-vm.md)вычислений.

### <a name="azure-container-instances-devtest"></a><a id="aci"></a>Экземпляры контейнеров Azure (dev/test)

Смотрите [Развертывание в экземплярах контейнеров Azure.](how-to-deploy-azure-container-instance.md)

### <a name="azure-kubernetes-service-devtest-and-production"></a><a id="aks"></a>Служба Azure Kubernetes (dev/test and production)

Смотрите [Развертывание службы Azure Kubernetes.](how-to-deploy-azure-kubernetes-service.md)

### <a name="ab-testing-controlled-rollout"></a>A/B Тестирование (контролируемое развертывание)
Для получения дополнительной информации смотрите [контролируемое развертывание моделей ML.](how-to-deploy-azure-kubernetes-service.md#deploy-models-to-aks-using-controlled-rollout-preview)

## <a name="consume-web-services"></a>Использование веб-служб

Каждый развернутый веб-сервис предоставляет конечную точку REST, так что вы можете создавать клиентские приложения на любом языке программирования.
Если вы включили проверку подлинности на основе ключей для службы, необходимо предоставить ключ службы в качестве маркера в заголовке запроса.
Если вы включили проверку подлинности на основе токенов для службы, необходимо предоставить в качестве маркера для напредъявителя маркера на предъявителя в заголовке запроса маркер Для машинного обучения JSON (JWT). 

Основное отличие заключается в том, что **ключи статичны и могут быть регенерированы вручную,** а **токены должны обновляться по истечении срока действия.** Аут на основе ключей поддерживается для Azure Container Instance и Службы Azure Kubernetes, развернутых веб-сервисов, а аут на основе токенов доступен **только** для развертывания службы Azure Kubernetes. [Ознакомьтесь](how-to-setup-authentication.md#web-service-authentication) с информацией и конкретными образцами кода.

> [!TIP]
> Вы можете получить схему JSON после развертывания службы. Используйте [swagger_uri свойствизна](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri) из развернутого `service.swagger_uri`веб-сервиса (например), чтобы получить URI в файл Swagger локальной веб-службы.

### <a name="request-response-consumption"></a>Потребление запроса и ответа

Вот пример того, как вызвать службу в Python:
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

Для получения дополнительной информации [см. Создать клиентские приложения для потребления веб-сервисов.](how-to-consume-web-service.md)

### <a name="web-service-schema-openapi-specification"></a>Схема web-сервиса (спецификация OpenAPI)

Если вы использовали автоматическое поколение схемса с развертыванием, вы можете получить адрес спецификации OpenAPI для службы, используя [swagger_uri свойством.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri) (Например, `print(service.swagger_uri)`.) Используйте запрос GET или откройте URI в браузере, чтобы получить спецификацию.

Следующий документ JSON является примером схемы (спецификация OpenAPI), генерируемой для развертывания:

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

Для получения дополнительной [OpenAPI specification](https://swagger.io/specification/)информации см.

Для утилиты, которая может создавать клиентские библиотеки из спецификации, [см.](https://github.com/swagger-api/swagger-codegen)

### <a name="batch-inference"></a><a id="azuremlcompute"></a>Вывод о пакете
Цели Azure Machine Learning Compute создаются и управляются с помощью Azure Machine Learning. Они могут быть использованы для прогнозирования пакетов из конвейеров Azure Machine Learning.

Для пошагового поиска пакетного выводов с Azure Machine Learning Compute [см. Как запускать прогнозы пакетов.](tutorial-pipeline-batch-scoring-classification.md)

### <a name="iot-edge-inference"></a><a id="iotedge"></a>Вывод IoT Edge
Поддержка развертывания к краю находится в предварительном просмотре. Для получения дополнительной информации смотрите [развертывание машинного обучения ВС Уот см.](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning)


## <a name="update-web-services"></a><a id="update"></a>Обновление веб-сервисов

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="continuously-deploy-models"></a>Постоянное развертывание моделей

Вы можете постоянно развертывать модели с помощью расширения машинного обучения для [Azure DevOps.](https://azure.microsoft.com/services/devops/) Расширение машинного обучения для Azure DevOps можно использовать для запуска конвейера развертывания, когда новая модель машинного обучения зарегистрирована в рабочем пространстве Машинного обучения Azure.

1. Подпишитесь на [Azure Pipelines,](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops)что делает непрерывную интеграцию и доставку приложения на любую платформу или облако возможной. (Обратите внимание, что конвейеры Azure не совпадает с [конвейерами машинного обучения.)](concept-ml-pipelines.md#compare)

1. [Создайте проект Azure DevOps.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. Установите [расширение машинного обучения для трубопроводов Azure.](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList)

1. Используйте соединения служб для настройки основного подключения к рабочему пространству Машинного обучения Azure, чтобы вы могли получить доступ к артефактам. Перейдите к настройкам проекта, выберите **соединения службы,** а затем выберите **менеджер ресурсов Azure:**

    [![Выберите менеджер ресурсов Azure](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png)

1. В списке **уровня области области** выберите **AzureMLWorkspace,** а затем введите остальные значения:

    ![Выберите пространство AzureMLWork](./media/how-to-deploy-and-where/resource-manager-connection.png)

1. Чтобы постоянно развертывать модель машинного обучения с помощью Azure Pipelines под конвейерами, выберите **выпуск.** Добавьте новый артефакт, а затем выберите артефакт **модели AzureML** и соединение службы, созданное ранее. Выберите модель и версию для запуска развертывания:

    [![Выберите модель AzureML](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. Включить триггер модели на артефакте модели. При включании триггера каждый раз, когда указанная версия (т.е. новейшая версия) этой модели регистрируется в рабочем пространстве, срабатывает конвейер выпуска Azure DevOps.

    [![Включить триггер модели](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

Дополнительные примеры проектов и примеров см.

* [Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)

## <a name="download-a-model"></a>Скачать модель
Если вы хотите загрузить модель, чтобы использовать ее в собственной среде выполнения, вы можете сделать это со следующими командами SDK / CLI:

Sdk:
```python
model_path = Model(ws,'mymodel').download()
```

Интерфейс командной строки.
```azurecli-interactive
az ml model download --model-id mymodel:1 --target-dir model_folder
```

## <a name="preview-no-code-model-deployment"></a>(Предварительный просмотр) Развертывание модели без кода

Развертывание модели без кода в настоящее время находится в предварительном просмотре и поддерживает следующие платформы машинного обучения:

### <a name="tensorflow-savedmodel-format"></a>Формат Tensorflow SavedModel
Модели Tensorflow должны быть зарегистрированы в **формате SavedModel** для работы с развертыванием модели без кода.

Пожалуйста, ознакомьтесь с [этой ссылкой](https://www.tensorflow.org/guide/saved_model) для получения информации о том, как создать SavedModel.

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

Регистрация и развертывание модели ONNX поддерживается для любого графика выводов ONNX. В настоящее время не поддерживаются шаги по допроцессу и постпроцессу.

Вот пример того, как зарегистрировать и развернуть модель MNIST ONNX:

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

Если вы используете Pytorch, [экспорт моделей из PyTorch в ONNX](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) имеет подробную информацию о конверсии и ограничения. 

### <a name="scikit-learn-models"></a>Модели scikit-learn

Развертывание модели кода не поддерживается для всех встроенных типов scikit-learn.

Вот пример того, как зарегистрировать и развернуть модель sklearn без дополнительного кода:

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

ПРИМЕЧАНИЕ: Модели, поддерживающие predict_proba будут использовать этот метод по умолчанию. Чтобы переопределить это для использования предсказать вы можете изменить тело POST, как ниже:
```python
import json


input_payload = json.dumps({
    'data': [
        [ 0.03807591,  0.05068012,  0.06169621, 0.02187235, -0.0442235,
         -0.03482076, -0.04340085, -0.00259226, 0.01990842, -0.01764613]
    ],
    'method': 'predict'  # If you have a classification model, the default behavior is to run 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

ПРИМЕЧАНИЕ: Эти зависимости включены в заранее построенный контейнер выводов sklearn:

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

## <a name="package-models"></a>Модели пакетов

В некоторых случаях можно создать изображение Docker без развертывания модели (если, например, планируется [развертывание в службе приложений Azure).](how-to-deploy-app-service.md) Или вы можете скачать изображение и запустить его на локальной установке Docker. Возможно, вы даже захотите загрузить файлы, используемые для создания изображения, проверить их, изменить их и построить изображение вручную.

Модель упаковки позволяет делать эти вещи. Он упаковывает все ресурсы, необходимые для размещения модели в качестве веб-сервиса, и позволяет загружать либо полностью построенное изображение Docker, либо файлы, необходимые для его создания. Существует два способа использования модели упаковки:

**Скачать упакованную модель:** Загрузите изображение Docker, содержащее модель и другие файлы, необходимые для его размещения в качестве веб-службы.

**Создание Докерфайла:** Скачать Dockerfile, модель, сценарий входа и другие ресурсы, необходимые для создания изображения Docker. Затем можно проверить файлы или внести изменения, прежде чем построить изображение локально.

Оба пакета могут быть использованы для получения локального изображения Docker.

> [!TIP]
> Создание пакета похоже на развертывание модели. Вы используете зарегистрированную модель и конфигурацию выводов.

> [!IMPORTANT]
> Чтобы загрузить полностью построенное изображение или создать изображение локально, необходимо установить [Docker](https://www.docker.com) в среде разработки.

### <a name="download-a-packaged-model"></a>Скачать упакованную модель

Следующий пример создает изображение, которое зарегистрировано в реестре контейнеров Azure для рабочего пространства:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

После создания пакета можно использовать `package.pull()` изображение в локальной среде Docker. На выходе этой команды будет отображаться имя изображения. Пример: 

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. 

После загрузки модели используйте `docker images` команду для расписок локальных изображений:

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43 GB
```

Чтобы запустить локальный контейнер на основе этого изображения, используйте следующую команду, чтобы запустить названный контейнер из оболочки или командной строки. Замените `<imageid>` значение идентификатором изображения, возвращенным командой. `docker images`

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

Эта команда запускает последнюю версию изображения под названием. `myimage` Он отображает местный порт 6789 в порт в контейнере, на котором веб-сервис слушает (5001). Он также присваивает имя `mycontainer` контейнеру, что облегчает остановку контейнера. После запуска контейнера вы можете `http://localhost:6789/score`отправить запросы в .

### <a name="generate-a-dockerfile-and-dependencies"></a>Создание Dockerfile и зависимостей

В следующем примере показано, как загрузить Dockerfile, модель и другие ресурсы, необходимые для создания изображения локально. Параметр `generate_dockerfile=True` указывает на то, что вам нужны файлы, а не полностью построенное изображение.

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

Этот код загружает файлы, `imagefiles` необходимые для создания изображения в каталог. Dockerfile, включенный в сохраненные файлы, ссылается на базовое изображение, хранящееся в реестре контейнеров Azure. При создании изображения на локальной установке Docker необходимо использовать адрес, имя пользователя и пароль для проверки подлинности в реестре. Используйте следующие шаги для создания изображения с помощью локальной установки Docker:

1. Из сеанса оболочки или командной строки используйте следующую команду для проверки подлинности Docker с реестром контейнеров Azure. Заменить, `<address>` `<username>` `<password>` и со значениями, `package.get_container_registry()`извлеченными .

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. Чтобы создать изображение, используйте следующую команду. Заменить `<imagefiles>` путь каталога, `package.save()` где сохранены файлы.

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    Эта команда устанавливает имя `myimage`изображения.

Чтобы убедиться, что изображение `docker images` построено, используйте команду. Вы должны `myimage` увидеть изображение в списке:

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43 GB
myimage         latest              739f22498d64        3 minutes ago       1.43 GB
```

Чтобы запустить новый контейнер на основе этого изображения, используйте следующую команду:

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

Эта команда запускает последнюю версию изображения под названием. `myimage` Он отображает местный порт 6789 в порт в контейнере, на котором веб-сервис слушает (5001). Он также присваивает имя `mycontainer` контейнеру, что облегчает остановку контейнера. После запуска контейнера вы можете `http://localhost:6789/score`отправить запросы в .

### <a name="example-client-to-test-the-local-container"></a>Пример клиента для тестирования локального контейнера

Следующий код является примером клиента Python, который может быть использован с контейнером:

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

Например, клиенты на других языках программирования см. [Модели Потребления, развернутые в качестве веб-сервисов.](how-to-consume-web-service.md)

### <a name="stop-the-docker-container"></a>Остановить контейнер Docker

Чтобы остановить контейнер, используйте следующую команду из другой оболочки или командной строки:

```bash
docker kill mycontainer
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Для удаления развернутой веб-службы используйте `service.delete()`.
Чтобы удалить зарегистрированную модель, используйте `model.delete()`.

Для получения дополнительной информации смотрите документацию для [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) и [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

<a id="advanced-entry-script"></a>
## <a name="advanced-entry-script-authoring"></a>Расширенный автор сценария ввода

<a id="binary"></a>

### <a name="binary-data"></a>Двоичные данные

Если модель принимает двоичные данные, например `score.py` изображение, необходимо изменить файл, используемый для развертывания, для приема необработанных запросов HTTP. Чтобы принять необработанные `AMLRequest` данные, используйте `@rawhttp` класс в скрипте `run()` входа и добавьте декоратор в функцию.

Вот `score.py` пример, который принимает двоичные данные:

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
> Класс `AMLRequest` находится в пространстве имен `azureml.contrib`. Сущности в этом пространстве имен часто меняются по мере того, как мы работаем над улучшением службы. Все, что в этом пространстве имен следует рассматривать как предварительный просмотр, который не полностью поддерживается корпорацией Майкрософт.
>
> Если вам нужно протестировать это в локальной среде разработки, можно установить компоненты с помощью следующей команды:
>
> ```shell
> pip install azureml-contrib-services
> ```

Класс `AMLRequest` позволяет получить доступ только к необработанным размещенным данным в score.py нет компонента на стороне клиента. От клиента вы публикуете данные в обычном режиме. Например, следующий код Python считывает файл изображений и публикует данные:

```python
import requests
# Load image data
data = open('example.jpg', 'rb').read()
# Post raw data to scoring URI
res = request.post(url='<scoring-uri>', data=data, headers={'Content-Type': 'application/octet-stream'})
```

<a id="cors"></a>

### <a name="cross-origin-resource-sharing-cors"></a>Совместное использование ресурсов по перекрестному происхождению (CORS)

Совместное использование ресурсов совместного использования ресурсов совместного использования ресурсов для перекрестного происхождения — это способ разрешить запрашивать ресурсы на веб-странице из другого домена. CORS работает через заголовки HTTP, отправленные с запросом клиента и возвращенные с ответом службы. Для получения дополнительной информации о CORS и действительных заголовках, [см.](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing)

Чтобы настроить развертывание модели для поддержки `AMLResponse` CORS, используйте класс в скрипте входа. Этот класс позволяет настроить заголовки на объекте ответа.

Следующий пример `Access-Control-Allow-Origin` устанавливает заголовок для ответа от сценария входа:

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
> Класс `AMLResponse` находится в пространстве имен `azureml.contrib`. Сущности в этом пространстве имен часто меняются по мере того, как мы работаем над улучшением службы. Все, что в этом пространстве имен следует рассматривать как предварительный просмотр, который не полностью поддерживается корпорацией Майкрософт.
>
> Если вам нужно протестировать это в локальной среде разработки, можно установить компоненты с помощью следующей команды:
>
> ```shell
> pip install azureml-contrib-services
> ```


> [!WARNING]
> Azure Machine Learning будет направлять только запросы POST и GET в контейнеры, работающие с службой скоринга. Это может привести к ошибкам из-за браузеров, использующих запросы OPTIONS для предполетных запросов CORS.
> 

## <a name="next-steps"></a>Дальнейшие действия

* [Как развернуть модель с помощью пользовательского изображения Docker](how-to-deploy-custom-docker-image.md)
* [Устранение неполадок развертывания](how-to-troubleshoot-deployment.md)
* [Используйте TLS для обеспечения безопасности веб-службы через Машинное обучение Azure](how-to-secure-web-service.md)
* [Использование модели Машинного обучения Azure, развернутой в качестве веб-службы](how-to-consume-web-service.md)
* [Мониторинг моделей машинного обучения Azure с помощью приложений Insights](how-to-enable-app-insights.md)
* [Сбор данных для моделей в рабочей среде](how-to-enable-data-collection.md)
* [Создание оповещений и триггеров событий для развертывания моделей](how-to-use-event-grid.md)

