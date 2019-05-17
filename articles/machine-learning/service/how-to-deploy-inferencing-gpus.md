---
title: Развертывание модели для выводов с графическим Процессором
titleSuffix: Azure Machine Learning service
description: Узнайте, как развернуть модель глубокого обучения в качестве веб-службы, который использует графический Процессор для выводов. В этой статье модели Tensorflow развертывается в кластере Kubernetes службы Azure. Кластер использует виртуальные Машины с поддержкой GPU для размещения веб-службы и запросы на оценку выводов.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 05/02/2019
ms.openlocfilehash: 7796e8dc07889c9816e4227f3b38904d91a24da3
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65595664"
---
# <a name="deploy-a-deep-learning-model-for-inferencing-with-gpu"></a>Развертывание модели глубокого обучения для выводов с графическим Процессором

Сведения об использовании GPU выводов для модели, развернутые как веб-службы машинного обучения. В этой статье вы узнаете, как использовать службы машинного обучения Azure для развертывания пример модели глубокого обучения Tensorflow. Модель развертывается в кластер Azure Kubernetes Service (AKS), использует виртуальные Машины с поддержкой GPU для размещения службы. При отправке запросов к службе, эта модель использует графический Процессор для выполнения выводов.

Графические процессоры предоставляют преимущества в производительности перед ЦП на высокий уровень параллелизма вычисления. Обучение и выводов для глубокого обучения модели (особенно для больших пакетов запросов) являются отличным использования для графических процессоров.  

В этом примере показано, как развернуть модель TensorFlow сохранены для машинного обучения Azure с:
* Создание кластера AKS с поддержкой GPU
* Развертывание модели с графическим Процессором Tensorflow

## <a name="prerequisites"></a>Технические условия

* Рабочая область службы машинного обучения Azure
* Python
* Зарегистрировать Tensorflow SavedModel. Дополнительные сведения о регистрации моделей см. в разделе [развертывание моделей](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#registermodel)

Эта статья основана на записной книжки Jupyter, [развертывание модели Tensorflow в AKS](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/production-deploy-to-aks-gpu/production-deploy-to-aks-gpu.ipynb), использующий TensorFlow сохранен моделирует и развертывает в кластере AKS. Тем не менее с небольшие изменения в файл оценки и файла среды он применим для любой платформы машинного обучения, который поддерживает графических процессоров.  

## <a name="provision-aks-cluster-with-gpus"></a>Подготовка кластера AKS с графическими процессорами
Azure имеет множество различных параметров GPU, которые можно использовать для определения. См. в разделе [список серии N](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) полный разбор возможностей и затраты. 

Дополнительные сведения об использовании AKS с помощью службы машинного обучения Azure, см. в разделе [способ развертывания и где статьи.](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#create-a-new-cluster)

```python
# Provision AKS cluster with GPU machine
prov_config = AksCompute.provisioning_configuration(vm_size="Standard_NC6")

# Create the cluster
aks_target = ComputeTarget.create(
    workspace=ws, name=aks_name, provisioning_configuration=prov_config
)

aks_target.wait_for_deployment()
```

> [!IMPORTANT]
> Azure будет выставлять вам счета, до тех пор, пока подготовки кластера AKS. Не забудьте удалить кластер AKS, после завершения его использования.


## <a name="write-entry-script"></a>Написать сценарий входа

Сохраните следующий в рабочий каталог как `score.py`. Этот файл будет использоваться для оценки изображений, так как они передаются в службу. Этот файл загружает TensorFlow, сохранить модель, а затем на каждой отправки запроса передает изображения к сеансу TensorFlow и возвращает результирующее оценки.
Другие платформы выводов потребует разные файлы оценки.

```python
import tensorflow as tf
import numpy as np
import ujson
from azureml.core.model import Model
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse

def init():
    global session
    global input_name
    global output_name
    
    session = tf.Session()

    model_path = Model.get_model_path('resnet50')
    model = tf.saved_model.loader.load(session, ['serve'], model_path)
    if len(model.signature_def['serving_default'].inputs) > 1:
        raise ValueError("This score.py only supports one input")
    input_name = [tensor.name for tensor in model.signature_def['serving_default'].inputs.values()][0]
    output_name = [tensor.name for tensor in model.signature_def['serving_default'].outputs.values()]
    

@rawhttp
def run(request):
    if request.method == 'POST':
        reqBody = request.get_data(False)
        resp = score(reqBody)
        return AMLResponse(resp, 200)
    if request.method == 'GET':
        respBody = str.encode("GET is not supported")
        return AMLResponse(respBody, 405)
    return AMLResponse("bad request", 500)

def score(data):
    result = session.run(output_name, {input_name: [data]})
    return ujson.dumps(result[1])

if __name__ == "__main__":
    init()
    with open("lynx.jpg", 'rb') as f: #load file for testing locally
        content = f.read()
        print(score(content))

```

## <a name="define-conda-environment"></a>Определите среду Conda
Создайте файл среды conda `myenv.yml` для определения зависимостей для вашей службы. Очень важно указать, что вы используете `tensorflow-gpu` для достижения улучшенную производительность.
```yaml
name: aml-accel-perf
channels:
  - defaults
dependencies:
  - tensorflow-gpu = 1.12
  - numpy
  - ujson
  - pip:
    - azureml-core
    - azureml-contrib-services
```

## <a name="define-gpu-inferenceconfig"></a>Определение GPU InferenceConfig

Создание [ `InferenceConfig` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) указывает, что при включении GPU. Это позволит гарантировать, что CUDA входящую в состав образа.

```python
from azureml.core.model import Model
from azureml.core.model import InferenceConfig

aks_service_name ='gpu-rn'
gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled = False, 
                                                    num_replicas = 3, 
                                                    cpu_cores=2, 
                                                    memory_gb=4)
model = Model(ws,"resnet50")

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml", 
                                   gpu_enabled=True)
```

Дополнительные сведения см. в разделе [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) и [AksServiceDeploymentConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py).
## <a name="deploy-the-model"></a>Развертывание модели

Развертывание модели в кластере AKS и дождитесь его, чтобы создать службу.

```python
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config, 
                           deployment_config=aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

> [!NOTE]
> Служба машинного обучения Azure не будет выполнять развертывание модели с `InferenceConfig` , ожидает, что GPU в кластере без GPU.

Дополнительные сведения см. в разделе [модели](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a name="issue-sample-query-to-deployed-model"></a>Образец запроса проблему для развертывания модели

Выполните образец запроса к развернутой модели. Эта модель будет оценивать любое изображение jpeg направлять на него в запросе post. 

```python
scoring_url = aks_service.scoring_uri
api_key = aks_service.get_key()(0)
IMAGEURL = "https://upload.wikimedia.org/wikipedia/commons/thumb/6/68/Lynx_lynx_poing.jpg/220px-Lynx_lynx_poing.jpg"

headers = {'Authorization':('Bearer '+ api_key)}
img_data = read_image_from(IMAGEURL).read()
r = requests.post(scoring_url, data = img_data, headers=headers)
```

> [!IMPORTANT]
> Чтобы оптимизировать задержку и пропускную способность, ваш клиент должен находиться в том же регионе Azure, что и конечная точка.  В настоящее время API создаются в регионе Azure "Восточная часть США".

## <a name="cleaning-up-the-resources"></a>Очистка ресурсов

Удалите ресурсы, Закончив с этой демонстрационной версией.

> [!IMPORTANT]
> Azure будет счета зависит от того, как долго кластер AKS развертывается. Убедитесь в том, что их можно очистить после всех возложенных на него.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Дальнейшие действия

* [Развернуть модель на основе FPGA](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-fpga-web-service)
* [Развернуть модель с помощью ONNX](https://docs.microsoft.com/azure/machine-learning/service/how-to-build-deploy-onnx#deploy)
* [Обучение модели Tensorflow DNN](https://docs.microsoft.com/azure/machine-learning/service/how-to-train-tensorflow)
