---
title: Развертывание модели для вывода с графическим Процессором
titleSuffix: Azure Machine Learning service
description: Узнайте, как развернуть модель глубокого обучения в качестве веб-службы, который использует графический Процессор для вывода. В этой статье модели Tensorflow развертывается в кластере Kubernetes службы Azure. Кластер использует виртуальные Машины с поддержкой GPU для размещения веб-службы и оценка вывод запросов.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 05/02/2019
ms.openlocfilehash: ec71165553a1d65ff133d605bf94255100f74e6e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66388922"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>Развертывание модели глубокого обучения для вывода с графическим Процессором

Узнайте, как использовать GPU определение для модели, развернутые как веб-службы машинного обучения. Определение или модель оценки, — это этап, где используется развернутой модели для прогнозирования, чаще всего для производственных данных.

В этой статье объясняется, как использовать службы машинного обучения Azure для развертывания пример модели в кластер Azure Kubernetes Service (AKS) на с поддержкой GPU виртуальной машины (VM) глубокого обучения Tensorflow. При отправке запросов к службе, модель использует графический Процессор для выполнения рабочих нагрузок вывод.

Графические процессоры предоставляют преимущества в производительности перед ЦП на высокий уровень параллелизма вычисления. Отличная использования для виртуальных машин с поддержкой GPU включают в себя глубокого обучения модели обучения и вывода, особенно для больших пакетов запросов.

В этом примере показано, как развернуть TensorFlow, сохранить модель машинного обучения Azure. Вам необходимо сделать следующее:

* Создание кластера AKS с поддержкой GPU
* Развертывание модели Tensorflow GPU

## <a name="prerequisites"></a>Технические условия

* В рабочей области службы машинного обучения Azure
* Дистрибутив Python
* Зарегистрированный Tensorflow сохранить модель. Дополнительные сведения о регистрации модели, см. в разделе [развертывание моделей](../service/how-to-deploy-and-where.md#registermodel).

Эта статья основана на записную книжку Jupyter, [развертывание модели Tensorflow в AKS](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/production-deploy-to-aks-gpu/production-deploy-to-aks-gpu.ipynb). Записная книжка Jupyter использует TensorFlow, сохранения моделей и развертывает их в кластер AKS. Можно также применить записной книжки для машинного обучения. платформа с поддержкой графических процессоров путем внесения небольших изменений в файл оценки и файла среды.  

## <a name="provision-an-aks-cluster-with-gpus"></a>Подготовка кластера AKS с графическими процессорами

Azure имеет целый ряд разных возможностей GPU. Можно использовать любой из них для выводов. См. в разделе [список виртуальных машин серии N](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) полный разбор возможностей и затраты.

Дополнительные сведения об использовании AKS с помощью службы машинного обучения Azure, см. в разделе [развертывание и где](../service/how-to-deploy-and-where.md#deploy-aks).

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
> Azure будет выставлять вам счета, до тех пор, пока подготовки кластера AKS. Убедитесь в том, что удаление кластера AKS, когда закончите с ним.

## <a name="write-the-entry-script"></a>Написать сценарий входа

Сохраните следующий код в рабочий каталог как `score.py`. Этот файл оценки изображений, когда они отправляются в службу. Загружает модель TensorFlow сохранен, передает изображения к сеансу TensorFlow на каждый запрос POST и затем возвращает полученный оценки. Другие платформы выводов необходимы разные файлы оценки.

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

## <a name="define-the-conda-environment"></a>Определите среду conda

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

## <a name="define-the-gpu-inferenceconfig-class"></a>Определите класс GPU InferenceConfig

Создание `InferenceConfig` объект, который включает процессоры GPU и гарантирует, что CUDA установлен образа Docker.

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

## <a name="issue-a-sample-query-to-your-deployed-model"></a>Выполните пример запроса к развернутой модели

Отправка тестового запроса к развернутой модели. При отправке изображения в формате jpeg в модель, она оценивает изображение.

```python
scoring_url = aks_service.scoring_uri
api_key = aks_service.get_key()(0)
IMAGEURL = "https://upload.wikimedia.org/wikipedia/commons/thumb/6/68/Lynx_lynx_poing.jpg/220px-Lynx_lynx_poing.jpg"

headers = {'Authorization':('Bearer '+ api_key)}
img_data = read_image_from(IMAGEURL).read()
r = requests.post(scoring_url, data = img_data, headers=headers)
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
