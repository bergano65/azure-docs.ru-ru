---
title: Развертывание модели для выводов с помощью графического процессора
titleSuffix: Azure Machine Learning
description: В этой статье выможете использовать Azure Machine Learning для развертывания модели глубокого обучения с поддержкой графического процессора Tensorflow в качестве веб-сервиса и запросов на выводы.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 03/05/2020
ms.openlocfilehash: b0fd537d1930e7c9d5f7a33f56ec5d00b1556562
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78398332"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>Развертывание модели глубокого обучения для выводов с Помощью Графического процессора
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье выможете использовать машинное обучение Azure для развертывания модели с поддержкой графического процессора в качестве веб-службы. Информация в этой статье основана на развертывании модели в службе Azure Kubernetes (AKS). Кластер AKS предоставляет ресурс GPU, который используется моделью для выводов.

Вывод, или оценка модели, — это этап, на котором развернутая модель используется для прогнозирования. Использование графических процессоров вместо процессоров дает преимущества производительности при высокопараллельных вычислениях.

> [!IMPORTANT]
> Для развертывания веб-служб вывод gPU поддерживается только в службе Azure Kubernetes. Для выводов с помощью __конвейера машинного обучения__графические процессоры поддерживаются только на Azure Machine Learning Compute. Для получения дополнительной информации об [Run batch predictions](how-to-use-parallel-run-step.md)использовании проводов ML см. 

> [!TIP]
> Хотя в фрагментах кода в этой статье используется модель TensorFlow, эту информацию можно применить к любой системе машинного обучения, поддерживающей графические процессоры.

> [!NOTE]
> Информация в этой статье основывается на информации, опубликованной в статье «Как развернуть» в статье [Службы Azure Kubernetes.](how-to-deploy-azure-kubernetes-service.md) В тех случаях, когда эта статья обычно охватывает развертывание в AKS, эта статья охватывает конкретные развертывания GPU.

## <a name="prerequisites"></a>Предварительные требования

* Рабочая область машинного обучения Azure. Для получения дополнительной информации [см.](how-to-manage-workspace.md)

* Среда разработки Python с установленным SDK Для машинного обучения Azure. Для получения дополнительной [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)информации см.  

* Зарегистрированная модель, используюва gPU.

    * Чтобы узнать, как зарегистрировать модели, [см.](how-to-deploy-and-where.md#registermodel)

    * Для создания и регистрации модели Tensorflow, [используемой](how-to-train-tensorflow.md)для создания этого документа, см.

* Общее понимание [того, как и где развертывать модели.](how-to-deploy-and-where.md)

## <a name="connect-to-your-workspace"></a>Подключение к рабочей области

Чтобы подключиться к существующему рабочему пространству, используйте следующий код:

> [!IMPORTANT]
> Этот фрагмент кода предполагает, что конфигурация рабочего пространства будет сохранена в текущем каталоге или его родительском. Для получения дополнительной информации о создании рабочего пространства [см.](how-to-manage-workspace.md)   Для получения дополнительной информации о сохранении конфигурации для файла [см.](how-to-configure-environment.md#workspace)

```python
from azureml.core import Workspace

# Connect to the workspace
ws = Workspace.from_config()
```

## <a name="create-a-kubernetes-cluster-with-gpus"></a>Создание кластера Kubernetes с графическими процессорами

Служба Azure Kubernetes предоставляет множество различных вариантов графического процессора. Вы можете использовать любой из них для моделирования выводов. [Ознакомьтесь со списком VMs-миков серии N](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) для полного разбивки возможностей и затрат.

Следующий код демонстрирует, как создать новый кластер AKS для рабочего пространства:

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
> Azure выставит вам счет до тех пор, пока существует кластер AKS. Убедитесь в том, чтобы удалить кластер AKS, когда вы закончите с ним.

Для получения дополнительной информации об использовании AKS с [помощью](how-to-deploy-azure-kubernetes-service.md)машинного обучения Azure см.

## <a name="write-the-entry-script"></a>Написать сценарий вступления

Скрипт входа получает данные, представленные в веб-сервис, передает их модели и возвращает результаты подсчета очков. Следующий скрипт загружает модель Tensorflow в запуск, а затем использует модель для оценки данных.

> [!TIP]
> Скрипт относится только к модели. Например, скрипт должен знать платформу для использования с моделью, форматами данных и т.д.

```python
import json
import numpy as np
import os
import tensorflow as tf

from azureml.core.model import Model


def init():
    global X, output, sess
    tf.reset_default_graph()
    model_root = os.getenv('AZUREML_MODEL_DIR')
    # the name of the folder in which to look for tensorflow model files
    tf_model_folder = 'model'
    saver = tf.train.import_meta_graph(
        os.path.join(model_root, tf_model_folder, 'mnist-tf.model.meta'))
    X = tf.get_default_graph().get_tensor_by_name("network/X:0")
    output = tf.get_default_graph().get_tensor_by_name("network/output/MatMul:0")

    sess = tf.Session()
    saver.restore(sess, os.path.join(model_root, tf_model_folder, 'mnist-tf.model'))


def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    out = output.eval(session=sess, feed_dict={X: data})
    y_hat = np.argmax(out, axis=1)
    return y_hat.tolist()
```

Этот файл `score.py`называется . Для получения дополнительной информации [How and where to deploy](how-to-deploy-and-where.md)о сценариях входа см.

## <a name="define-the-conda-environment"></a>Определите среду конды

Файл среды conda определяет зависимости для службы. Она включает в себя зависимости, требуемые как моделью, так и скриптом входа. Пожалуйста, обратите внимание, что вы должны указать лазурные по умолчанию с verion >1.0.45 в качестве зависимости от пипсов, потому что он содержит функциональность, необходимую для размещения модели в качестве веб-сервиса. Следующие YAML определяет среду для модели Tensorflow. Он `tensorflow-gpu`определяет, который будет использовать графический процессор, используемый в этом развертывании:

```yaml
name: project_environment
dependencies:
  # The python interpreter version.
  # Currently Azure ML only supports 3.5.2 and later.
- python=3.6.2

- pip:
  # You must list azureml-defaults as a pip dependency
  - azureml-defaults>=1.0.45
- numpy
- tensorflow-gpu=1.12
channels:
- conda-forge
```

В этом примере файл `myenv.yml`сохраняется как .

## <a name="define-the-deployment-configuration"></a>Определение конфигурации развертывания

Конфигурация развертывания определяет среду службы Azure Kubernetes, используемую для запуска веб-службы:

```python
from azureml.core.webservice import AksWebservice

gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                    num_replicas=3,
                                                    cpu_cores=2,
                                                    memory_gb=4)
```

Для получения дополнительной информации смотрите справочную документацию для [AksService.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-).

## <a name="define-the-inference-configuration"></a>Определение конфигурации выводов

Конфигурация выводов указывает на сценарий входа и объект среды, который использует докерное изображение с поддержкой графического процессора. Обратите внимание, что файл YAML, используемый для определения среды, должен содержать лазурные по умолчанию с версией >1,0,45 в качестве зависимости от пипсов, поскольку он содержит функциональность, необходимую для размещения модели в качестве веб-сервиса.

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment, DEFAULT_GPU_IMAGE

myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
myenv.docker.base_image = DEFAULT_GPU_IMAGE
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
```

Для получения дополнительной информации [Create and manage environments for training and deployment](how-to-use-environments.md)о средах см.
Для получения дополнительной информации смотрите справочную документацию для [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py).

## <a name="deploy-the-model"></a>Развертывание модели

Развернуть модель в кластер AKS и ждать, пока она создаст службу.

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
> Если `InferenceConfig` объект `enable_gpu=True`имеет, `deployment_target` то параметр должен ссылаться на кластер, который обеспечивает графический процессор. В противном случае развертывание завершится ошибкой.

Для получения дополнительной информации смотрите справочную документацию для [модели](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a name="issue-a-sample-query-to-your-service"></a>Выдать пример запроса в службу

Отправить тестовый запрос на развернутую модель. Когда вы отправляете изображение jpeg на модель, оно забивает изображение. Следующий пример кода загружает тестовые данные, а затем выбирает случайное тестовое изображение для отправки в службу.

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

Для получения дополнительной информации о создании клиентского приложения [см.](how-to-consume-web-service.md)

## <a name="clean-up-the-resources"></a>очищать ресурсы.

Если специально для этого примера был создан кластер AKS, удалите ресурсы после того, как вы закончите.

> [!IMPORTANT]
> Счета Azure основаны на том, как долго развертывается кластер AKS. Убедитесь в том, чтобы очистить его после того, как вы сделали с ним.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Дальнейшие действия

* [Развертывание модели на FPGA](how-to-deploy-fpga-web-service.md)
* [Развертывание модели с ONNX](concept-onnx.md#deploy-onnx-models-in-azure)
* [Поезд Tensorflow DNN Модели](how-to-train-tensorflow.md)
