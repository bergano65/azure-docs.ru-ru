---
title: Развертывание модели для вывода с графическим Процессором
titleSuffix: Azure Machine Learning service
description: В этой статье объясняется, как использовать службы машинного обучения Azure для развертывания Tensorflow с поддержкой GPU для глубокого обучения модели в качестве web service.service и оценка вывод запросов.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 06/01/2019
ms.openlocfilehash: 8086d059913cc61bff0bca31681368bea6d76777
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543800"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>Развертывание модели глубокого обучения для вывода с графическим Процессором

В этой статье объясняется, как использовать службы машинного обучения Azure для развертывания Tensorflow с поддержкой GPU для глубокого обучения модели как веб-службы.

Разверните свою модель в кластер Azure Kubernetes Service (AKS) для выполнения с поддержкой GPU выводов. Выводов или оценки модели, — это этап, где используется развернутой модели для прогнозирования. Использование GPU вместо преимущества производительности ЦП предложения на высокий уровень параллелизма вычисления.

Несмотря на то, что этот образец использует модель TensorFlow, можно применить следующие действия для машинного обучения. платформа с поддержкой графических процессоров путем внесения небольших изменений в файл оценки и файла среды. 

Ниже перечислены действия, которые вы выполните в этой статье.

* Создание кластера AKS с поддержкой GPU
* Развертывание модели Tensorflow GPU
* Выполните пример запроса к развернутой модели

## <a name="prerequisites"></a>Технические условия

* В рабочей области машинного обучения Azure services.
* Дистрибутив Python.
* Зарегистрированный Tensorflow сохранить модель.
    * Дополнительные сведения о регистрации модели, см. в разделе [развертывание моделей](../service/how-to-deploy-and-where.md#registermodel).

Можно выполнить первой части этого руководства по серии, [Практическое обучение модели TensorFlow](how-to-train-tensorflow.md), для выполнения необходимых условий.

## <a name="provision-an-aks-cluster-with-gpus"></a>Подготовка кластера AKS с графическими процессорами

Azure имеет целый ряд разных возможностей GPU. Можно использовать любой из них для выводов. См. в разделе [список виртуальных машин серии N](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) полный разбор возможностей и затраты.

Дополнительные сведения об использовании AKS с помощью службы машинного обучения Azure, см. в разделе [развертывание и где](../service/how-to-deploy-and-where.md#deploy-aks).

```Python
# Choose a name for your cluster
aks_name = "aks-gpu"

# Check to see if the cluster already exists
try:
    compute_target = ComputeTarget(workspace=ws, name=aks_name)
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
> Azure будет выставлять вам счета, до тех пор, пока подготовки кластера AKS. Убедитесь в том, что удаление кластера AKS, когда закончите с ним.

## <a name="write-the-entry-script"></a>Написать сценарий входа

Сохраните следующий код в рабочий каталог как `score.py`. Этот файл оценки изображений, когда они отправляются в службу. Загружает модель TensorFlow сохранен, передает изображения к сеансу TensorFlow на каждый запрос POST и затем возвращает полученный оценки. Другие платформы выводов необходимы разные файлы оценки.

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
    saver = tf.train.import_meta_graph(os.path.join(model_root, 'mnist-tf.model.meta'))
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
## <a name="define-the-conda-environment"></a>Определите среду conda

Создайте файл среды conda `myenv.yml` для определения зависимостей для вашей службы. Очень важно указать, что вы используете `tensorflow-gpu` для достижения улучшенную производительность.

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

## <a name="define-the-gpu-inferenceconfig-class"></a>Определите класс GPU InferenceConfig

Создание `InferenceConfig` объект, который включает процессоры GPU и гарантирует, что CUDA установлен образа Docker.

```python
from azureml.core.model import Model
from azureml.core.model import InferenceConfig

aks_service_name ='aks-dnn-mnist'
gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled = False, 
                                                    num_replicas = 3, 
                                                    cpu_cores=2, 
                                                    memory_gb=4)
model = Model(ws,"tf-dnn-mnist")

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml", 
                                   enable_gpu=True)
```

Дополнительные сведения можно найти в разделе

- [Класс InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)
- [Класс AksServiceDeploymentConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)

## <a name="deploy-the-model"></a>Развертывание модели

Развертывание модели в кластере AKS и дождитесь его, чтобы создать службу.

```python
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config, 
                           deployment_config=gpu_aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

> [!NOTE]
> Служба машинного обучения Azure не развертывания модели с `InferenceConfig` объект, который ожидает, что GPU, чтобы включить в кластер, не имеет графического Процессора.

Дополнительные сведения см. в разделе [класс модели](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a name="issue-a-sample-query-to-your-model"></a>Выполним пример запрос к модели

Отправка тестового запроса к развернутой модели. При отправке изображения в формате jpeg в модель, она оценивает изображение. В следующем образце кода использует функции внешняя программа для загрузки изображений. Соответствующий код можно найти в pir [TensorFlow пример на GitHub](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow/utils.py). 

```python
# Used to test your webservice
from utils import load_data 

# Load test data from model training
X_test = load_data('./data/mnist/test-images.gz', False) / 255.0
y_test = load_data('./data/mnist/test-labels.gz', True).reshape(-1)

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

api_key = aks_service.get_keys()[0]
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)

print("POST to url", aks_service.scoring_uri)
#print("input data:", input_data)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

> [!IMPORTANT]
> Чтобы свести к минимуму задержки и оптимизации пропускной способности, убедитесь, что ваш клиент находится в том же регионе Azure как конечной точки. В этом примере API-интерфейсы создаются в регионе Azure восточная часть США.

## <a name="clean-up-the-resources"></a>Очистка ресурсов

Удаление ресурсов после завершения работы с примером.

> [!IMPORTANT]
> Azure выставляет вам на основании как долго кластер AKS развертывается. Убедитесь в том, что их можно очистить после всех возложенных на него.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Дальнейшие действия

* [Развернуть модель на основе FPGA](../service/how-to-deploy-fpga-web-service.md)
* [Развернуть модель с помощью ONNX](../service/concept-onnx.md#deploy-onnx-models-in-azure)
* [Обучение модели Tensorflow DNN](../service/how-to-train-tensorflow.md)
