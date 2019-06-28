---
title: Как и способа развертывания модели
titleSuffix: Azure Machine Learning service
description: 'Сведения о том, как и где следует развертывать модели Службы машинного обучения Azure, в том числе: Экземпляры контейнеров Azure, Службу Azure Kubernetes, Azure IoT Edge и программируемые пользователем вентильные матрицы.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 05/31/2019
ms.custom: seoapril2019
ms.openlocfilehash: b5a08b9b998f8d0b30091af016af564e836d4651
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/22/2019
ms.locfileid: "67331654"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Развертывание моделей с помощью Службы машинного обучения Azure

Узнайте, как развернуть модель машинного обучения как веб-службы в облаке Azure или на устройствах IoT Edge. 

Рабочий процесс аналогичен вне зависимости от [где развернуть](#target) модели:

1. Регистрация модели.
1. Подготовка к развертыванию (Указание целевого объекта вычислений активы, использования,)
1. Развертывание модели для целевого объекта вычислений.
1. Тестирование развернутого модели, также называемый веб-службы.

Дополнительные сведения об основных понятиях, связанных с рабочим процессом развертывания, см. в статье [Администрирование, развертывание и мониторинг моделей с помощью службы "Машинное обучение Azure"](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Технические условия

- Модель. Если у вас нет обученной модели, можно использовать модель и файлы зависимостей, предоставляются в [учебником](https://aka.ms/azml-deploy-cloud).

- [Расширение Azure CLI для службы машинного обучения](reference-azure-machine-learning-cli.md), [пакета SDK Azure Machine Learning Python](https://aka.ms/aml-sdk), или [расширения Azure Machine Learning Visual Studio Code](how-to-vscode-tools.md).

## <a id="registermodel"></a> Регистрация модели

Зарегистрированной модели логический контейнер для одного или нескольких файлов, которые входят в модель. Например если у вас есть модель, которая хранится в нескольких файлах, можно зарегистрировать их как одну модель в рабочей области. После регистрации можно затем загрузке или развертывании зарегистрированной модели и получать все файлы, которые были зарегистрированы.

Модели машинного обучения, регистрируются в рабочей области машинного обучения Azure. Модели могут поступать из машинного обучения Azure или поступать из другого места. Следующие примеры демонстрируют, как для регистрации модели из файла:

### <a name="register-a-model-from-an-experiment-run"></a>Регистрация модели из на выполнение эксперимента

+ **Пример Scikit-Learn, с помощью пакета SDK**
  ```python
  model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
  print(model.name, model.id, model.version, sep='\t')
  ```

  > [!TIP]
  > Чтобы включить несколько файлов в регистрации модели, присвойте `model_path` в каталог, содержащий файлы.

+ **С помощью интерфейса командной строки**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment
  ```

  > [!TIP]
  > Чтобы включить несколько файлов в регистрации модели, присвойте `--asset-path` в каталог, содержащий файлы.

+ **С помощью VS Code**

  Регистрация моделей с помощью любой модели файлы или папки с [VS Code](how-to-vscode-tools.md#deploy-and-manage-models) расширения.

### <a name="register-an-externally-created-model"></a>Зарегистрируйте модель, созданные извне

[!INCLUDE [trusted models](../../../includes/machine-learning-service-trusted-model.md)]

Вы можете зарегистрировать модель, созданные извне, предоставляя **локальный путь** в модель. Можно предоставить одного файла или папки.

+ **Пример ONNX с помощью пакета SDK для Python.**
  ```python
  onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
  urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
  !tar xvzf mnist.tar.gz
  
  model = Model.register(workspace = ws,
                         model_path ="mnist/model.onnx",
                         model_name = "onnx_mnist",
                         tags = {"onnx": "demo"},
                         description = "MNIST image classification CNN from ONNX Model Zoo",)
  ```

  > [!TIP]
  > Чтобы включить несколько файлов в регистрации модели, присвойте `model_path` в каталог, содержащий файлы.

+ **С помощью интерфейса командной строки**
  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  > [!TIP]
  > Чтобы включить несколько файлов в регистрации модели, присвойте `-p` в каталог, содержащий файлы.

**Оценка времени**. Примерно 10 секунд.

Дополнительные сведения см. в справочной документации по [классу Model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

<a name="target"></a>

## <a name="choose-a-compute-target"></a>Выберите целевой объект вычислений

Следующие целевые объекты, вычислений или вычислительных ресурсов, можно использовать для размещения вашего развертывания веб-службы. 

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

## <a name="prepare-to-deploy"></a>Подготовка к развертыванию

Чтобы развернуть веб-службы, необходимо создать определение конфигурации (`InferenceConfig`) и конфигурации развертывания. Определение или модель оценки, — это этап, где используется развернутой модели для прогнозирования, чаще всего для производственных данных. В файле конфигурации вывод указать скрипты и зависимости, необходимые для использования в модели. В файл конфигурации развертывания можно указать подробные сведения для обслуживания модели в целевой объект вычислений.


### <a id="script"></a> 1. Определите сценарий входа & зависимости

Сценарий входа получает данные, отправленные для развернутой веб-службы и передает его в модель. Затем он принимает ответ, возвращенный моделью, и возвращает его клиенту. **Сценарий относится только к модели**; его необходимо понимать, модель ожидает и возвращает данные.

Сценарий содержит две функции, которые загружают и запустить модель:

* `init()`: Обычно эта функция загружает модель в глобальный объект. Эта функция выполняется только один раз, когда запускается контейнер Docker для веб-службы.

* `run(input_data)`: Эта функция использует модель для прогнозирования значения на основе входных данных. Ко входным и выходным данным для запуска обычно применяется формат JSON для сериализации и десериализации. Вы также можете работать с необработанными двоичными данными. Вы можете преобразовать данные, прежде чем отправлять их в модель или возвращать клиенту.

#### <a name="what-is-getmodelpath"></a>Что такое get_model_path?

При регистрации модели, можно предоставить имя модели, используемые для управления моделью в реестре. Используйте это имя с [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) для получения пути файлов модели в локальной файловой системе. Если зарегистрировать папку или коллекцию файлов, этот API возвращает путь к каталогу, содержащему эти файлы.

При регистрации модель, задается имя, которое соответствует место размещения модели, либо локально, либо во время развертывания службы.

Пример ниже возвращает путь в единственном файле `sklearn_mnist_model.pkl` (который был зарегистрирован с именем `sklearn_mnist`):

```python
model_path = Model.get_model_path('sklearn_mnist')
``` 

#### <a name="optional-automatic-swagger-schema-generation"></a>(Необязательно) Автоматическое создание схемы Swagger

Чтобы автоматически создавать схему для веб-службы, обеспечения выборки входных данных и/или выходных данных в конструктор для одного из объектов определенного типа, тип и образец используются для автоматического создания схемы. Служба машинного обучения Azure создает [OpenAPI](https://swagger.io/docs/specification/about/) спецификации (Swagger) для веб-службы во время развертывания.

В настоящее время поддерживаются следующие типы:

* `pandas`
* `numpy`
* `pyspark`
* стандартный объект Python

Чтобы использовать Создание схем, включите `inference-schema` пакета в файле среды conda. В следующем примере используется `[numpy-support]` так, как тип параметра numpy использует сценарий входа: 

#### <a name="example-dependencies-file"></a>Пример файла зависимостей
Следующий yaml-ФАЙЛ является примером файл зависимостей Conda для вывода.

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - pip:
    - azureml-defaults
    - scikit-learn==0.20.0
    - inference-schema[numpy-support]
```

Если вы хотите использовать создание автоматического схемы, ваш сценарий входа **необходимо** импорта `inference-schema` пакетов. 

Определите входные и выходные форматы образец в `input_sample` и `output_sample` переменные, которые представляют параметры запроса и ответа для веб-службы. Используйте эти примеры во входных данных и вывода декораторы функция на `run()` функции. Scikit-Дополнительные сведения в следующем примере используется Создание схемы.

> [!TIP]
> После развертывания службы, используйте `swagger_uri` свойство для извлечения документа схемы JSON.

#### <a name="example-entry-script"></a>Пример сценария входа

Следующий пример демонстрирует принимают и возвращают данные JSON:

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

input_sample = np.array([[10,9,8,7,6,5,4,3,2,1]])
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

#### <a name="example-script-with-dictionary-input-support-consumption-from-power-bi"></a>Пример сценария с входными данными словаря (поддержка потребление из Power BI)

В следующем примере демонстрируется определение входных данных как < ключ: значение > словарь, используя кадр данных. Этот метод поддерживается для использования развернутой веб-службы из Power BI ([Дополнительные сведения о том, как использовать веб-службу из Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-integration)):

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
    model_path = Model.get_model_path('model_name')   # replace model_name with your actual model name, if needed
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)

input_sample = pd.DataFrame(data=[{
              "input_name_1": 5.1,         # This is a decimal type sample. Use the data type that reflects this column in your data
              "input_name_2": "value2",    # This is a string type sample. Use the data type that reflects this column in your data
              "input_name_3": 3            # This is a integer type sample. Use the data type that reflects this column in your data
            }])

output_sample = np.array([0])              # This is a integer type sample. Use the data type that reflects the expected result

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
Дополнительные примеры скриптов см. в следующих примерах:

* Pytorch: [https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch)
* TensorFlow: [https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow)
* Keras: [https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* ONNX: [https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)
* Оценку двоичных данных: [Как использовать веб-службу](how-to-consume-web-service.md)

### <a name="2-define-your-inferenceconfig"></a>2. Определение вашего InferenceConfig

Определение конфигурации описывается, как настроить модель для прогнозирования. Следующий пример демонстрирует создание конфигурации вывод:

```python
inference_config = InferenceConfig(source_directory="C:/abc",
                                   runtime= "python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

### <a name="cli-example-of-inferenceconfig"></a>Пример CLI InferenceConfig
```JSON
{
   "entryScript": "x/y/score.py",
   "runtime": "python",
   "condaFile": "env/myenv.yml",
   "sourceDirectory":"C:/abc",
}
```
```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

В этом примере конфигурации содержит следующие элементы:

* Каталог, содержащий ресурсы, необходимые для вывода
* Что эта модель требует Python
* [Сценарий входа](#script), который используется для обработки веб-запросы, отправляемые развернутой службы
* Файл conda, который описывает пакеты Python, необходимые для вывода

Сведения о функциях InferenceConfig см. в разделе [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) класса ссылки.

Сведения об использовании пользовательского образа Docker с конфигурацией вывод, см. в разделе [развертывание модели с помощью пользовательского образа Docker](how-to-deploy-custom-docker-image.md).

### <a name="3-define-your-deployment-configuration"></a>3. Определение конфигурации развертывания

Перед развертыванием, необходимо определить конфигурацию развертывания. Конфигурация развертывания относится только к целевого объекта вычислений, где будет размещаться веб-службы. Например при развертывании локально необходимо указать порт, где служба принимает запросы.

Кроме того, может потребоваться создать вычислительный ресурс. Например если вы еще не службе Kubernetes Azure связаны с рабочей областью.

Ниже приведен пример создания конфигурации развертывания для каждого целевого объекта вычислений.

| Целевой объект вычисления | Пример конфигурации развертывания |
| ----- | ----- |
| Local | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Экземпляр контейнера Azure | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Служба Azure Kubernetes | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Ниже показано, как создать конфигурацию развертывания, а затем использовать его для развертывания веб-службы.

### <a name="optional-profile-your-model"></a>Необязательно: Профилирование модели
Перед развертыванием модели в качестве службы, может потребоваться его для определения оптимальной ЦП и требования к памяти.

Вы можно сделать профиль модели с помощью пакета SDK или интерфейса командной строки.

Дополнительные сведения можно извлечь документации пакета SDK: https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#profile-workspace--profile-name--models--inference-config--input-data-

Модель результатов профилирования передаются в рамках объекта Run.
Особенности схемы профиль модели можно найти здесь: https://docs.microsoft.com/python/api/azureml-core/azureml.core.profile.modelprofile?view=azure-ml-py

## <a name="deploy-to-target"></a>Развертывание в целевой объект

### <a id="local"></a> Локальное развертывание

Чтобы развернуть локально, необходимо иметь **установленный Docker** на локальном компьютере.

+ **С помощью пакета SDK**

  ```python
  deployment_config = LocalWebservice.deploy_configuration(port=8890)
  service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  ```

+ **С помощью интерфейса командной строки**

  ```azurecli-interactive
  az ml model deploy -m sklearn_mnist:1 -ic inferenceconfig.json -dc deploymentconfig.json
  ```

### <a id="aci"></a> Экземпляры контейнеров Azure (DEVTEST)

Экземпляры контейнеров Azure (ACI) можно использовать для развертывания моделей в качестве веб-службы, если выполняется одно или несколько из следующих условий:
- вам важно быстро выполнять развертывание и проверку модели.
- вы тестируете модель, которая находится в стадии разработки. 

Квоты и доступности по регионам для ACI см. в разделе [квоты и доступность по регионам для экземпляры контейнеров Azure](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) статьи.

+ **С помощью пакета SDK**

  ```python
  deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
  service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  ```

+ **С помощью интерфейса командной строки**

  ```azurecli-interactive
  az ml model deploy -m sklearn_mnist:1 -n aciservice -ic inferenceconfig.json -dc deploymentconfig.json
  ```


+ **С помощью VS Code**

  Чтобы [развертывания моделей с помощью VS Code](how-to-vscode-tools.md#deploy-and-manage-models) не требуется для создания контейнера ACI для заранее, тестирования, из-за создания контейнеров ACI в режиме реального времени.

Дополнительные сведения см. в справочной документации по классам [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) и [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

### <a id="aks"></a>Служба Azure Kubernetes (DEVTEST & производства)

Можно использовать существующий кластер AKS или создать его с помощью пакета SDK для службы "Машинное обучение Azure", CLI или портала Azure.

<a id="deploy-aks"></a>

При наличии подключенных кластера AKS, можно развернуть его. Если вы еще не создали или подключен кластер AKS, выполните процедуру <a href="#create-attach-aks">создать кластер AKS</a>.

+ **С помощью пакета SDK**

  ```python
  aks_target = AksCompute(ws,"myaks")
  # If deploying to a cluster configured for dev/test, ensure that it was created with enough
  # cores and memory to handle this deployment configuration. Note that memory is also used by
  # things such as dependencies and AML components.
  deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
  service = Model.deploy(ws, "aksservice", [model], inference_config, deployment_config, aks_target)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  print(service.get_logs())
  ```

+ **С помощью интерфейса командной строки**

  ```azurecli-interactive
  az ml model deploy -ct myaks -m mymodel:1 -n aksservice -ic inferenceconfig.json -dc deploymentconfig.json
  ```

+ **С помощью VS Code**

  Вы также можете [развертывание AKS с помощью расширения VS Code](how-to-vscode-tools.md#deploy-and-manage-models), но она понадобится для настройки кластеров AKS заранее.

Дополнительные сведения о развертывании AKS и автоматического масштабирования в [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice) ссылки.

#### Создание нового кластера AKS<a id="create-attach-aks"></a>
**Оценка времени**. приблизительно 20 минут.

Создание или присоединение кластера AKS находится один раз обработать для рабочей области. Один кластер можно использовать для нескольких развертываний. При удалении кластера или содержащую его группу ресурсов, необходимо создать новый кластер в следующий раз необходимо выполнить развертывание. Может иметь несколько кластеров AKS, подключенный к рабочей области.

Если вы хотите создать кластер службы контейнеров AZURE для разработки, проверки и тестирования, задайте `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` при использовании [ `provisioning_configuration()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py). Кластера, созданного с помощью этого параметра будет иметь только один узел.

> [!IMPORTANT]
> Параметр `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` создает кластер AKS, который не подходит для обработки трафика в рабочей среде. Время вывода может быть дольше, чем для кластера, созданного для рабочей среды. Для кластеров разработки и тестирования также не гарантируется устойчивость к сбоям.
>
> Мы рекомендуем, кластеры, созданные для разработки и тестирования использовать по крайней мере два виртуальных ЦП.

Следующий пример демонстрирует создание нового кластера Azure Kubernetes Service:

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksComputee.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

Дополнительные сведения о создании кластера AKS за пределами пакета SDK для Azure Machine Learning см. в разделе со следующими статьями:
* [Создание кластера AKS](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Создание кластера AKS (портал)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

Дополнительные сведения о `cluster_purpose` параметр, см. в разделе [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py) ссылки.

> [!IMPORTANT]
> Если вы выберете пользовательские значения agent_count и vm_size для [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py), необходимо убедиться, что произведение agent_count и vm_size больше или равно 12 виртуальным ЦП. Например, если вы используете для vm_size значение "Standard_D3_v2", которое соответствует 4 виртуальным ЦП, для agent_count необходимо выбрать значение 3 или больше.
>
> Пакета SDK для Azure Machine Learning не поддерживает масштабирование кластера AKS. Чтобы масштабировать узлы в кластере, используйте пользовательский Интерфейс для кластера AKS на портале Azure. Можно только изменить число узлов, а не размер виртуальной Машины кластера.

#### <a name="attach-an-existing-aks-cluster"></a>Присоединение существующего кластера AKS
**Оценка времени:** Примерно через 5 минут.

Если вас уже есть кластер AKS в подписке Azure, и это версии 1.12. ##, его можно использовать для развертывания образа.

> [!WARNING]
> При присоединении кластера AKS в рабочую область, можно определить, как кластер будет использовать, задав `cluster_purpose` параметра.
>
> Если вы не установите `cluster_purpose` параметра или набора `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`, а затем кластер должен иметь по крайней мере 12 виртуальных процессоров, доступных.
>
> Если задать `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`, а затем кластера не требуется 12 виртуальных ЦП. Тем не менее кластер, настроенный для разработки и тестирования не подойти для трафика на уровне рабочей среде и может увеличить время вывода.

Приведенный ниже показано, как подключить существующий 1.12 AKS. ## кластера к рабочей области:

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'mycluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'mycompute', attach_config)
```

Дополнительные сведения о `attack_configuration()`, см. в разделе [AksCompute.attach_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-) ссылки.

Дополнительные сведения о `cluster_purpose` параметр, см. в разделе [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py) ссылки.

## <a name="consume-web-services"></a>Использование веб-служб

Каждый развернутой веб-службы предоставляет REST API, чтобы вы могли создавать клиентские приложения в различных языках программирования. Если вы включили проверку подлинности для службы, необходимо предоставить ключ службы как токен в заголовке запроса.

### <a name="request-response-consumption"></a>Запрос ответ потребления

Вот пример того, как вызвать службу на языке Python:
```python
import requests
import json

headers = {'Content-Type':'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]

print(headers)
    
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(service.scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

Дополнительные сведения см. в статье [Использование модели Машинного обучения Azure, развернутой в качестве веб-службы](how-to-consume-web-service.md).


### <a id="azuremlcompute"></a> Определение пакета
Целевые объекты вычислений для машинного обучения Azure создаются и управляются службой машинного обучения Azure. Они могут использоваться для прогноза пакетной службы на основе конвейеры машинного обучения Azure.

Пошаговое руководство, вывода пакетной службы с помощью вычислений машинного обучения Azure, в статье [как выполнить пакетные прогнозы](how-to-run-batch-predictions.md) статьи.

### <a id="iotedge"></a> Вывод IoT Edge
Для поддержки развертывания на периферии доступна Предварительная версия. Дополнительные сведения см. в разделе [развертывание машинного обучения Azure, как модуль IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning) статьи.


## <a id="update"></a> Обновите веб-службы

При создании новой модели, необходимо вручную обновить каждую службу, вы хотите использовать новую модель. Чтобы обновить веб-службу, используйте метод `update`. Следующий код демонстрирует способы обновления веб-службы для использования новой модели:

```python
from azureml.core.webservice import Webservice
from azureml.core.model import Model

# register new model
new_model = Model.register(model_path = "outputs/sklearn_mnist_model.pkl",
                       model_name = "sklearn_mnist",
                       tags = {"key": "0.1"},
                       description = "test",
                       workspace = ws)

service_name = 'myservice'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)

# Update to new model(s)
service.update(models = [new_model])
print(service.state)
print(service.get_logs())
```

## <a name="clean-up-resources"></a>Очистка ресурсов
Для удаления развернутой веб-службы используйте `service.delete()`.
Чтобы удалить зарегистрированную модель, используйте `model.delete()`.

Дополнительные сведения см. в справочной документации по [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--), и [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## <a name="next-steps"></a>Дальнейшие действия
* [Развертывание модели с помощью пользовательского образа Docker](how-to-deploy-custom-docker-image.md)
* [Устранение неполадок развертывания](how-to-troubleshoot-deployment.md)
* [Защита веб-служб Машинного обучения Azure с помощью SSL](how-to-secure-web-service.md)
* [Использование модели Машинного обучения Azure, развернутой в качестве веб-службы](how-to-consume-web-service.md)
* [Мониторинг моделей машинного обучения в Azure с помощью Application Insights](how-to-enable-app-insights.md)
* [Сбор данных для моделей в рабочей среде](how-to-enable-data-collection.md)

