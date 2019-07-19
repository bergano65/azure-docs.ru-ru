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
ms.date: 06/01/2019
ms.openlocfilehash: eeb1bc35e0438a7e99ea5ed8284f0c8611108da0
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326991"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>Развертывание модели глубокого обучения для вывода с помощью GPU

В этой статье рассказывается, как использовать службу Машинное обучение Azure для развертывания модели глубокого обучения Tensorflow с поддержкой GPU в качестве веб-службы.

Разверните модель в кластере Azure Kubernetes Service (AKS), чтобы в ней можно было выводить ссылку на GPU. Выработка или оценка модели — это этап, в котором развернутая модель используется для прогнозирования. Использование графических процессоров вместо процессоров обеспечивает преимущества производительности при параллелизуемыеных вычислениях.

Хотя в этом примере используется модель TensorFlow, вы можете применить следующие действия к любой платформе машинного обучения, которая поддерживает GPU, внося небольшие изменения в файл оценки и файл среды. 

Ниже перечислены действия, которые вы выполните в этой статье.

* Создание кластера AKS с поддержкой GPU
* Развертывание модели GPU Tensorflow
* Выдача примера запроса в развернутую модель

## <a name="prerequisites"></a>предварительные требования

* Рабочая область служб Машинное обучение Azure.
* Дистрибутив Python.
* Зарегистрированная Tensorflow сохраненная модель.
    * Дополнительные сведения о регистрации моделей см. в разделе [Развертывание моделей](../service/how-to-deploy-and-where.md#registermodel).

Вы можете пройти одну из этих инструкций, [как обучить модель TensorFlow](how-to-train-tensorflow.md)для выполнения необходимых условий.

## <a name="provision-an-aks-cluster-with-gpus"></a>Подготавливает кластер AKS с помощью GPU

В Azure предусмотрено множество различных параметров GPU. Для этого можно использовать любой из них. Полный список возможностей и затрат см. [в списке виртуальных машин серии N](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) .

Дополнительные сведения об использовании AKS со службой Машинное обучение Azure см. [в разделе Развертывание и размещение](../service/how-to-deploy-and-where.md#deploy-aks).

```Python
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
> При подготовке кластера AKS в Azure будет взиматься плата. Не забудьте удалить кластер AKS после завершения работы с ним.

## <a name="write-the-entry-script"></a>Написание сценария записи

Сохраните следующий код в рабочем каталоге как `score.py`. Этот файл оценивает изображения по мере их отправки в службу. Он загружает сохраненную модель TensorFlow, передает входной образ в сеанс TensorFlow в каждом запросе POST, а затем возвращает результирующие результаты. Другим платформам, для которых требуются другие платформы, необходимы разные файлы оценки.

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
## <a name="define-the-conda-environment"></a>Определение среды conda

Создайте файл среды conda с именем `myenv.yml` , чтобы указать зависимости для службы. Важно указать, что вы используете `tensorflow-gpu` для достижения ускоренной производительности.

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

## <a name="define-the-gpu-inferenceconfig-class"></a>Определение класса GPU Инференцеконфиг

`InferenceConfig` Создайте объект, который включает графические процессоры и обеспечивает установку CUDA вместе с образом DOCKER.

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

- [Класс Инференцеконфиг](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)
- [Класс Акссервицедеплойментконфигуратион](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)

## <a name="deploy-the-model"></a>Развертывание модели

Разверните модель в кластере AKS и подождите, пока она создаст службу.

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
> Машинное обучение Azure служба не развертывает модель с `InferenceConfig` объектом, который должен включать графический процессор в кластер без графического процессора.

Дополнительные сведения см. в разделе [класс Model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a name="issue-a-sample-query-to-your-model"></a>Выдача примера запроса в модель

Отправка тестового запроса в развернутую модель. При отправке изображения JPEG в модель она оценивает изображение. В следующем примере кода для загрузки изображений используется внешняя служебная функция. Соответствующий код можно найти по адресу PIR [TensorFlow Sample на сайте GitHub](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow/utils.py). 

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
> Чтобы сократить задержку и оптимизировать пропускную способность, убедитесь, что клиент находится в том же регионе Azure, что и конечная точка. В этом примере интерфейсы API создаются в регионе Azure "Восточная часть США".

## <a name="clean-up-the-resources"></a>Очистка ресурсов

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
