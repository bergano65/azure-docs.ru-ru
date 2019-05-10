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
ms.date: 05/02/2019
ms.custom: seoapril2019
ms.openlocfilehash: 45421a249642abf37c89aa33e2e8a1b4a9e5e497
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65507003"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Развертывание моделей с помощью Службы машинного обучения Azure

Узнайте, как развернуть модель машинного обучения как веб-службы в облаке Azure или на устройствах IoT Edge. Сведения в этом документе объясняется, как развертывание на целевые объекты вычисления в следующем:

| Целевой объект вычисления | Тип развертывания | Описание |
| ----- | ----- | ----- |
| [Локальный веб-службы](#local) | Тестирование и отладка | Такие ограниченные возможности для тестирования и устранения неполадок.
| [Служба Azure Kubernetes (AKS)](#aks) | Вывод в режиме реального времени | Подходит для крупномасштабных рабочих развертываний. Она обеспечивает автоматическое масштабирование и малое время отклика. |
| [Экземпляры контейнеров Azure (ACI)](#aci) | Тестирование | Подходит низкой масштабируемых, рабочих нагрузок на основе использования ресурсов ЦП. |
| [Вычислительная среда Машинного обучения Azure](how-to-run-batch-predictions.md) | (Предварительная версия) Определение пакета | Запустите пакетной оценки на бессерверных вычислений. Поддерживает нормальные и низкоприоритетные виртуальные машины. |
| [Edge Интернета вещей Azure](#iotedge) | (Предварительная версия) Модуль Интернета вещей | Развертывание и обслуживать моделей машинного Обучения на устройствах Интернета вещей. |

## <a name="deployment-workflow"></a>Рабочий процесс развертывания

Процесс развертывания модели аналогичен для всех целевых объектов вычислений:

1. Зарегистрируйте модели.
1. Развертывание модели.
1. Протестируйте развернутые модели.

Дополнительные сведения об основных понятиях, связанных с рабочим процессом развертывания, см. в статье [Администрирование, развертывание и мониторинг моделей с помощью службы "Машинное обучение Azure"](concept-model-management-and-deployment.md).

## <a name="prerequisites-for-deployment"></a>Необходимые условия для развертывания

- Модель. Если у вас нет обученной модели, можно использовать модель и файлы зависимостей, предоставляются в [учебником](https://aka.ms/azml-deploy-cloud).

- [Расширение Azure CLI для службы машинного обучения](reference-azure-machine-learning-cli.md), или [пакета SDK Azure Machine Learning Python](https://aka.ms/aml-sdk).

## <a id="registermodel"></a> Зарегистрировать модель машинного обучения

Реестр моделей позволяет хранить и упорядочивать обученные модели в облаке Azure. Модели зарегистрированы в рабочей области Службы машинного обучения Azure. Модель можно обучить с помощью машинного обучения Azure, или импортируются из модель, обученная в другом месте. Следующие примеры демонстрируют, как для регистрации модели из файла:

### <a name="register-a-model-from-an-experiment-run"></a>Регистрация модели из на выполнение эксперимента

**Пример, Scikit-Learn, с помощью интерфейса командной строки**
```azurecli-interactive
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment
```
**С помощью пакета SDK**
```python
model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
print(model.name, model.id, model.version, sep='\t')
```

### <a name="register-an-externally-created-model"></a>Зарегистрируйте модель, созданные извне
Вы можете зарегистрировать модель, созданные извне, предоставляя **локальный путь** в модель. Можно предоставить одного файла или папки.

**Пример ONNX с помощью пакета SDK для Python.**
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

**С помощью интерфейса командной строки**
```azurecli-interactive
az ml model register -n onnx_mnist -p mnist/model.onnx
```

**Оценка времени**. Примерно 10 секунд.

Дополнительные сведения см. в справочной документации по [классу Model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a name="how-to-deploy"></a>Развертывание

Чтобы развернуть веб-службы, необходимо создать определение конфигурации (`InferenceConfig`) и конфигурации развертывания. В файле конфигурации вывод указать скрипты и зависимости, необходимые для использования в модели. В файл конфигурации развертывания можно указать подробные сведения для обслуживания модели в целевой объект вычислений.


### <a id="script"></a> 1. Определите сценарий входа & зависимости

Сценарий входа получает данные, отправленные для развернутой веб-службы и передает его в модель. Затем он принимает ответ, возвращенный моделью, и возвращает его клиенту. **Сценарий относится только к модели**; его необходимо понимать, модель ожидает и возвращает данные.

Сценарий содержит две функции, которые загружают и запустить модель:

* `init()`: Обычно эта функция загружает модель в глобальный объект. Эта функция выполняется только один раз, когда запускается контейнер Docker для веб-службы.

* `run(input_data)`: Эта функция использует модель для прогнозирования значения на основе входных данных. Ко входным и выходным данным для запуска обычно применяется формат JSON для сериализации и десериализации. Вы также можете работать с необработанными двоичными данными. Вы можете преобразовать данные, прежде чем отправлять их в модель или возвращать клиенту.

#### <a name="optional-automatic-swagger-schema-generation"></a>(Необязательно) Автоматическое создание схемы Swagger

Чтобы автоматически создавать схему для веб-службы, обеспечения выборки входных данных и/или выходных данных в конструктор для одного из объектов определенного типа, тип и образец используются для автоматического создания схемы. Служба машинного обучения Azure создает [OpenAPI](https://swagger.io/docs/specification/about/) спецификации (Swagger) для веб-службы во время развертывания.

В настоящее время поддерживаются следующие типы:

* `pandas`
* `numpy`
* `pyspark`
* стандартный объект Python

Чтобы использовать Создание схем, включите `inference-schema` пакета в файле среды conda. В следующем примере используется `[numpy-support]` так, как тип параметра numpy использует сценарий входа: 

#### <a name="example-dependencies-file"></a>Пример файла зависимостей
Ниже приведен пример файла зависимостей Conda для вывода.
```python
name: project_environment
dependencies:
  - python=3.6.2
  - pip:
    - azureml-defaults
    - scikit-learn
    - inference-schema[numpy-support]
```

Если вы хотите использовать создание автоматического схемы, ваш сценарий входа **необходимо** импорта `inference-schema` пакетов. 

Определите входные и выходные форматы образец в `input_sample` и `output_sample` переменные, которые представляют параметры запроса и ответа для веб-службы. Используйте эти примеры во входных данных и вывода декораторы функция на `run()` функции. Scikit-Дополнительные сведения в следующем примере используется Создание схемы.

> [!TIP]
> После развертывания службы, используйте `swagger_uri` свойство для извлечения документа схемы JSON.

#### <a name="example-entry-script"></a>Пример сценария входа

Следующий пример демонстрирует принимают и возвращают данные JSON:

**Scikit-learn пример с созданием Swagger.**
```python
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

В этом примере конфигурации содержит следующие элементы:

* Каталог, содержащий ресурсы, необходимые для вывода
* Что эта модель требует Python
* [Сценарий входа](#script), который используется для обработки веб-запросы, отправляемые развернутой службы
* Файл conda, который описывает пакеты Python, необходимые для формирования выводов

Сведения о функциях InferenceConfig см. в разделе [расширенной конфигурации](#advanced-config) раздел.

### <a name="3-define-your-deployment-configuration"></a>3. Определение конфигурации развертывания

Перед развертыванием, необходимо определить конфигурацию развертывания. Конфигурация развертывания относится только к целевого объекта вычислений, где будет размещаться веб-службы. Например при развертывании локально необходимо указать порт, где служба принимает запросы.

Кроме того, может потребоваться создать вычислительный ресурс. Например если вы еще не службе Kubernetes Azure связаны с рабочей областью.

Ниже приведен пример создания конфигурации развертывания для каждого целевого объекта вычислений.

| Целевой объект вычисления | Пример конфигурации развертывания |
| ----- | ----- |
| Локальные | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Экземпляр контейнера Azure | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Служба Azure Kubernetes | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Ниже показано, как создать конфигурацию развертывания, а затем использовать его для развертывания веб-службы.

## <a name="where-to-deploy"></a>Расположение развертывания

### <a id="local"></a> Развертывание локально

В примерах этого раздела используется [deploy_from_image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-), который необходимо зарегистрировать модель и образ перед выполнением развертывания. Дополнительные сведения о методах развертывания см. в разделе [развертывание](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-) и [deploy_from_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-).

**Чтобы развернуть локально, необходимо иметь установленный на локальном компьютере Docker.**

**С помощью пакета SDK**

```python
deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

**С помощью интерфейса командной строки**

```azurecli-interactive
az ml model deploy -m sklearn_mnist:1 -ic inferenceconfig.json -dc deploymentconfig.json
```

### <a id="aci"></a> Развертывание в экземпляры контейнеров Azure (DEVTEST)

Экземпляры контейнеров Azure (ACI) можно использовать для развертывания моделей в качестве веб-службы, если выполняется одно или несколько из следующих условий:
- вам важно быстро выполнять развертывание и проверку модели.
- вы тестируете модель, которая находится в стадии разработки. 

Квоты и доступности по регионам для ACI см. в разделе [квоты и доступность по регионам для экземпляры контейнеров Azure](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) статьи.

**С помощью пакета SDK**

```python
deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

**С помощью интерфейса командной строки**

```azurecli-interactive
az ml model deploy -m sklearn_mnist:1 -n aciservice -ic inferenceconfig.json -dc deploymentconfig.json
```

Дополнительные сведения см. в справочной документации по классам [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) и [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

### <a id="aks"></a> Развертывание в службе Azure Kubernetes (рабочая СРЕДА)

Можно использовать существующий кластер AKS или создать его с помощью пакета SDK для службы "Машинное обучение Azure", CLI или портала Azure.


> [!IMPORTANT]
> Кластер AKS для рабочей области нужно создать только один раз. Один кластер можно использовать для нескольких развертываний.
> Если вы не создали или присоединенного AKS кластера go <a href="#create-attach-aks">здесь</a>.

#### Развертывание в AKS <a id="deploy-aks"></a>

Можно развернуть AKS с помощью Azure ML CLI:
```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n aksservice -ic inferenceconfig.json -dc deploymentconfig.json
```

Можно также использовать пакет SDK для Python:
```python
aks_target = AksCompute(ws,"myaks")
deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aksservice", [model], inference_config, deployment_config, aks_target)
service.wait_for_deployment(show_output = True)
print(service.state)
print(service.get_logs())
```

Дополнительные сведения о настройке развертывании AKS, в том числе Автомасштабирование, см. в разделе [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice) ссылки.

**Оценка времени:** Примерно через 5 минут.

#### Создать или присоединить кластера AKS <a id="create-attach-aks"></a>
Создание или присоединение кластера AKS **один процесс времени** для рабочей области. После кластера был связан с рабочей областью, его можно использовать для нескольких развертываний. 

При удалении кластера или содержащую его группу ресурсов, необходимо создать новый кластер в следующий раз необходимо выполнить развертывание.

##### <a name="create-a-new-aks-cluster"></a>Создание нового кластера AKS
Дополнительные сведения о параметр `autoscale_target_utilization`, `autoscale_max_replicas`, и `autoscale_min_replicas`, см. в разделе [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none-) ссылки.
Следующий пример демонстрирует создание нового кластера Azure Kubernetes Service:

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this)
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


> [!IMPORTANT]
> Если вы выберете пользовательские значения agent_count и vm_size для [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py), необходимо убедиться, что произведение agent_count и vm_size больше или равно 12 виртуальным ЦП. Например, если вы используете для vm_size значение "Standard_D3_v2", которое соответствует 4 виртуальным ЦП, для agent_count необходимо выбрать значение 3 или больше.

**Примерное время**: приблизительно 20 минут.

##### <a name="attach-an-existing-aks-cluster"></a>Присоединение существующего кластера AKS

Если вас уже есть кластер AKS в подписке Azure, и это версии 1.12. ## и имеет по крайней мере 12 виртуальных ЦП, его можно использовать для развертывания образа. Приведенный ниже показано, как подключить существующий 1.12 AKS. ## кластера к рабочей области:

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'mycluster'

# Attach the cluster to your workgroup
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'mycompute', attach_config)
```

## <a name="consume-web-services"></a>Использование веб-служб
Каждый развернутой веб-службы предоставляет REST API, чтобы вы могли создавать клиентские приложения в различных языках программирования. Если вы включили проверку подлинности для службы, необходимо предоставить ключ службы как токен в заголовке запроса.

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

## <a id="update"></a> Обновление веб-службы

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

## <a name="clean-up"></a>Очистка
Для удаления развернутой веб-службы используйте `service.delete()`.
Чтобы удалить зарегистрированную модель, используйте `model.delete()`.

Дополнительные сведения см. в справочной документации по [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--), и [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## Дополнительные параметры <a id="advanced-config"></a>

### <a id="customimage"></a> Использование пользовательского базового образа

На внутреннем уровне InferenceConfig создает образ Docker, содержащий модель и другие активы, необходимые службе. Если не указан, используется базовый образ по умолчанию.

При создании изображения для использования с вашей конфигурацией вывод, этот образ должен соответствовать следующим требованиям:

* Ubuntu 16.04 или более поздней версии.
* Conda 4.5. # или более поздней версии.
* Python 3.5. # или 3.6. #.

Чтобы использовать пользовательский образ, задайте `base_image` свойство конфигурации вывод на адрес образа. Следующий пример демонстрирует использование образа из обоих открытого и закрытого реестра контейнеров Azure:

```python
# use an image available in public Container Registry without authentication
inference_config.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"

# or, use an image available in a private Container Registry
inference_config.base_image = "myregistry.azurecr.io/mycustomimage:1.0"
inference_config.base_image_registry.address = "myregistry.azurecr.io"
inference_config.base_image_registry.username = "username"
inference_config.base_image_registry.password = "password"
```

Ниже URI являются для образов, предоставленных корпорацией Майкрософт и могут использоваться без указания значения имени и пароля пользователя:

* `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda`
* `mcr.microsoft.com/azureml/onnxruntime:v0.4.0`
* `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-cuda10.0-cudnn7`
* `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-tensorrt19.03`

Чтобы использовать эти образы, задайте `base_image` к URL-АДРЕСУ из приведенного выше списка. Задайте для параметра `base_image_registry.address` значение `mcr.microsoft.com`.

> [!IMPORTANT]
> Образы Майкрософт, которые используют CUDA или TensorRT необходимо использовать только в службах Microsoft Azure.

Дополнительные сведения о загрузке свои собственные образы в реестр контейнеров Azure, см. в разделе [отправка первого образа в частный реестр контейнеров Docker](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-docker-cli).

Если ваша модель обучается на вычислений машинного обучения Azure, с помощью __версии 1.0.22 или более поздней версии__ пакета SDK для обучения машины Azure, образ создается во время обучения. Следующий пример демонстрирует использование этого образа:

```python
# Use an image built during training with SDK 1.0.22 or greater
image_config.base_image = run.properties["AzureML.DerivedImageName"]
```

## <a name="other-inference-options"></a>Другие параметры вывода

### <a id="azuremlcompute"></a> Определение пакета
Целевые объекты вычислений для машинного обучения Azure создаются и управляются службой машинного обучения Azure. Они могут использоваться для прогноза пакетной службы на основе конвейеры машинного обучения Azure.

Пошаговое руководство, вывода пакетной службы с помощью вычислений машинного обучения Azure, в статье [как выполнить пакетные прогнозы](how-to-run-batch-predictions.md) статьи.

## <a id="iotedge"></a> Вывод в IoT Edge
Для поддержки развертывания на периферии доступна Предварительная версия. Дополнительные сведения см. в разделе [развертывание машинного обучения Azure, как модуль IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning) статьи.

## <a name="next-steps"></a>Дальнейшие действия
* [Устранение неполадок развертывания](how-to-troubleshoot-deployment.md)
* [Защита веб-служб Машинного обучения Azure с помощью SSL](how-to-secure-web-service.md)
* [Использование модели Машинного обучения Azure, развернутой в качестве веб-службы](how-to-consume-web-service.md)
* [Мониторинг моделей машинного обучения в Azure с помощью Application Insights](how-to-enable-app-insights.md)
* [Сбор данных для моделей в рабочей среде](how-to-enable-data-collection.md)
