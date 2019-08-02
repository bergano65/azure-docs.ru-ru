---
title: Развертывание модели для вывода с помощью GPU
titleSuffix: Azure Machine Learning service
description: В этой статье объясняется, как использовать службу Машинное обучение Azure для развертывания модели глубокого обучения Tensorflow с поддержкой GPU в качестве веб-службы. запросы на вывод служб и оценки.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 07/24/2019
ms.openlocfilehash: 06194537a0c0cce3a52510e6f426a9c2904387b2
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68694339"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>Развертывание модели глубокого обучения для вывода с помощью GPU

В этой статье объясняется, как использовать службу Машинное обучение Azure для развертывания модели с поддержкой GPU в качестве веб-службы. Сведения в этой статье основаны на развертывании модели в службе Azure Kubernetes Service (AKS). Кластер AKS предоставляет ресурс GPU, который используется моделью для вывода.

Вывод или оценка модели — это этап, в котором для создания прогнозов используется развернутая модель. Использование графических процессоров вместо ЦП обеспечивает преимущества производительности при параллелизуемыеных вычислениях.

> [!TIP]
> Хотя фрагменты кода в этой статье Уси модель TensorFlow, эту информацию можно применить к любой платформе машинного обучения, которая поддерживает GPU.

> [!NOTE]
> Сведения в этой статье основаны на сведениях в статье [развертывание в службе Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md) . Когда эта статья обычно охватывает развертывание в AKS, в этой статье рассматривается развертывание GPU.

## <a name="prerequisites"></a>Предварительные требования

* Рабочая область службы машинного обучения Azure. Дополнительные сведения см. в статье [Создание рабочей области службы машинное обучение Azure](setup-create-workspace.md).

* Среда разработки Python с установленным пакетом SDK для Машинное обучение Azure. Дополнительные сведения см. в разделе о [пакете Python SDK](setup-create-workspace.md#sdk) статьи Создание рабочей области.

* Зарегистрированная модель, использующая GPU.

    * Дополнительные сведения о регистрации моделей см. в разделе [Развертывание моделей](../service/how-to-deploy-and-where.md#registermodel).

    * Сведения о создании и регистрации модели Tensorflow, используемой для создания этого документа, см. в разделе [обучение модели Tensorflow](how-to-train-tensorflow.md).

* Общее представление [о том, как и где развертываются модели](how-to-deploy-and-where.md).

## <a name="connect-to-your-workspace"></a>Подключение к рабочей области

Чтобы подключиться к существующей рабочей области, используйте следующий код:

> [!IMPORTANT]
> Этот фрагмент кода ждет, что конфигурация рабочей области будет сохранена в текущем или родительском каталоге. Дополнительные сведения о создании рабочей области и сохранении конфигурации в файле см. в разделе [Создание рабочей области службы машинное обучение Azure](setup-create-workspace.md).

```python
from azureml.core import Workspace

# Connect to the workspace
ws = Workspace.from_config()
```

## <a name="create-a-kubernetes-cluster-with-gpus"></a>Создание кластера Kubernetes с помощью GPU

Служба Azure Kubernetes предоставляет множество различных параметров GPU. Для определения модели можно использовать любой из них. Полный список возможностей и затрат см. [в списке виртуальных машин серии N](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) .

В следующем коде показано, как создать новый кластер AKS для рабочей области.

```python
from azureml.core.compute import ComputeTarget, AksCompute
from azureml.exceptions import ComputeTargetException

# Choose a name for your cluster
aks_name = "aks-gpu"

# Check to see if the cluster already exists
try:
    aks_target = ComputeTarget(workspace=ws, name=aks_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    # Provision AKS cluster with GPU machine
    prov_config = AksCompute.provisioning_configuration(vm_size="Standard_NC6")

    # Create the cluster
    aks_target = ComputeTarget.create(
        workspace=ws, name=aks_name, provisioning_configuration=prov_config
    )

    aks_target.wait_for_completion(show_output=True)
```

> [!IMPORTANT]
> В Azure будет взиматься плата, если существует кластер AKS. Не забудьте удалить кластер AKS после завершения работы с ним.

Дополнительные сведения об использовании AKS со службой Машинное обучение Azure см. в статье [развертывание в службе Kubernetes Azure](how-to-deploy-azure-kubernetes-service.md).

## <a name="write-the-entry-script"></a>Написание сценария записи

Сценарий записи получает данные, отправленные в веб-службу, передает их в модель и возвращает результаты оценки. Следующий скрипт загружает модель Tensorflow при запуске, а затем использует модель для оценки данных.

> [!TIP]
> Сценарий записи зависит от модели. Например, сценарий должен быть уверен, что платформа будет использоваться с моделью, форматами данных и т. д.

```python
import json
import numpy as np
import os
import tensorflow as tf

from azureml.core.model import Model


def init():
    global X, output, sess
    tf.reset_default_graph()
    model_root = Model.get_model_path('tf-dnn-mnist')
    saver = tf.train.import_meta_graph(
        os.path.join(model_root, 'mnist-tf.model.meta'))
    X = tf.get_default_graph().get_tensor_by_name("network/X:0")
    output = tf.get_default_graph().get_tensor_by_name("network/output/MatMul:0")

    sess = tf.Session()
    saver.restore(sess, os.path.join(model_root, 'mnist-tf.model'))


def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    out = output.eval(session=sess, feed_dict={X: data})
    y_hat = np.argmax(out, axis=1)
    return y_hat.tolist()
```

Этот файл называется `score.py`. Дополнительные сведения о сценариях входа см. [в разделе как и где развертывать](how-to-deploy-and-where.md).

## <a name="define-the-conda-environment"></a>Определение среды conda

Файл среды conda определяет зависимости для службы. Он включает зависимости, необходимые как для модели, так и для скрипта записи. Следующая YAML определяет среду для модели Tensorflow. Он указывает `tensorflow-gpu`, что будет использовать GPU, используемый в этом развертывании:

```yaml
name: project_environment
dependencies:
  # The python interpreter version.
  # Currently Azure ML only supports 3.5.2 and later.
- python=3.6.2

- pip:
  - azureml-defaults==1.0.43.*
- numpy
- tensorflow-gpu=1.12
channels:
- conda-forge
```

В этом примере файл сохраняется как `myenv.yml`.

## <a name="define-the-deployment-configuration"></a>Определение конфигурации развертывания

Конфигурация развертывания определяет среду службы Azure Kubernetes, используемую для запуска веб-службы:

```python
from azureml.core.webservice import AksWebservice

gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                    num_replicas=3,
                                                    cpu_cores=2,
                                                    memory_gb=4)
```

Дополнительные сведения см. в справочной документации по [акссервице. deploy_configuration](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none-).

## <a name="define-the-inference-configuration"></a>Определение конфигурации вывода

Конфигурация вывода указывает на сценарий записи и файл среды conda. Он также включает поддержку GPU, которая устанавливает CUDA в образе DOCKER, созданном для веб-службы:

```python
from azureml.core.model import InferenceConfig

inference_config = InferenceConfig(runtime="python",
                                   entry_script="score.py",
                                   conda_file="myenv.yml",
                                   enable_gpu=True)
```

Дополнительные сведения см. в справочной документации по [инференцеконфиг](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py).

## <a name="deploy-the-model"></a>Развертывание модели

Разверните модель в кластере AKS и подождите, пока она создаст службу.

```python
from azureml.core.model import Model

# Name of the web service that is deployed
aks_service_name = 'aks-dnn-mnist'
# Get the registerd model
model = Model(ws, "tf-dnn-mnist")
# Deploy the model
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=gpu_aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output=True)
print(aks_service.state)
```

> [!NOTE]
> Если объект `InferenceConfig` имеет `enable_gpu=True`значение, `deployment_target` параметр должен ссылаться на кластер, который предоставляет GPU. В противном случае развертывание завершится ошибкой.

Дополнительные сведения см. в справочной документации по [модели](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a name="issue-a-sample-query-to-your-service"></a>Выдача примера запроса в службу

Отправка тестового запроса в развернутую модель. При отправке изображения JPEG в модель она оценивает изображение. Следующий пример кода скачивает тестовые данные, а затем выбирает случайное тестовое изображение для отправки в службу. 

```python
# Used to test your webservice
import os
import urllib
import gzip
import numpy as np
import struct
import requests

# load compressed MNIST gz files and return numpy arrays
def load_data(filename, label=False):
    with gzip.open(filename) as gz:
        struct.unpack('I', gz.read(4))
        n_items = struct.unpack('>I', gz.read(4))
        if not label:
            n_rows = struct.unpack('>I', gz.read(4))[0]
            n_cols = struct.unpack('>I', gz.read(4))[0]
            res = np.frombuffer(gz.read(n_items[0] * n_rows * n_cols), dtype=np.uint8)
            res = res.reshape(n_items[0], n_rows * n_cols)
        else:
            res = np.frombuffer(gz.read(n_items[0]), dtype=np.uint8)
            res = res.reshape(n_items[0], 1)
    return res

# one-hot encode a 1-D array
def one_hot_encode(array, num_of_classes):
    return np.eye(num_of_classes)[array.reshape(-1)]

# Download test data
os.makedirs('./data/mnist', exist_ok=True)
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename='./data/mnist/test-images.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename='./data/mnist/test-labels.gz')

# Load test data from model training
X_test = load_data('./data/mnist/test-images.gz', False) / 255.0
y_test = load_data('./data/mnist/test-labels.gz', True).reshape(-1)

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

api_key = aks_service.get_keys()[0]
headers = {'Content-Type': 'application/json',
           'Authorization': ('Bearer ' + api_key)}
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)

print("POST to url", aks_service.scoring_uri)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

Дополнительные сведения о создании клиентского приложения см. в разделе [Создание клиента для использования развернутой веб-службы](how-to-consume-web-service.md).

## <a name="clean-up-the-resources"></a>очищать ресурсы.

Если вы создали кластер AKS специально для этого примера, удалите ресурсы после завершения работы.

> [!IMPORTANT]
> Счета Azure выставляются в зависимости от того, как долго развертывается кластер AKS. Не забудьте очистить ее после завершения.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Следующие шаги

* [Развертывание модели на FPGA](../service/how-to-deploy-fpga-web-service.md)
* [Развертывание модели с помощью ONNX](../service/concept-onnx.md#deploy-onnx-models-in-azure)
* [Обучение моделей Tensorflow DNN](../service/how-to-train-tensorflow.md)
