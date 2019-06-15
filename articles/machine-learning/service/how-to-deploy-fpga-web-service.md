---
title: Развертывание моделей в ППВМ
titleSuffix: Azure Machine Learning service
description: Узнайте, как развернуть веб-службы с помощью модели, работающей на FPGA со службой машинного обучения Azure для вывода сверхнизкой задержкой.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: tedway
author: tedway
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 6cb9de60fe63c936da7340e6ec540a37163216f5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67074979"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning-service"></a>Развертывание модели как веб-службы в ППВМ с помощью Службы машинного обучения Azure

Вы можете развернуть модель как веб-службу на [поле программируемых Вентильные (FPGAs)](concept-accelerate-with-fpgas.md) с моделями Accelerated Azure Machine Learning оборудования. Использование FPGAs обеспечивает вывод сверхнизкой задержкой, даже с размером одного пакета. Определение или модель оценки, — это этап, где используется развернутой модели для прогнозирования, чаще всего для производственных данных.

Модели, доступные сегодня:
  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16
  - SSD-VGG

В этих регионах Azure доступны FPGAs.
  - Восточная часть США
  - Юго-Восточная Азия
  - Западная Европа
  - Западный регион США 2

> [!IMPORTANT]
> Чтобы оптимизировать пропускную способность и задержку, клиент отправляет данные в модели FPGA должны находиться в одном из регионов выше (то, что вы развернули модель).

## <a name="prerequisites"></a>Технические условия

- Подписка Azure.  Если нет, создайте бесплатную учетную запись перед началом работы. Опробуйте [бесплатную или платную версию Службы машинного обучения Azure](https://aka.ms/AMLFree).

- Квота FPGA.  Используйте Azure CLI, чтобы проверить, есть ли у вас квоты.
    ```shell
    az vm list-usage --location "eastus" -o table
    ```

    Другие расположения: ``southeastasia``, ``westeurope``, и ``westus2``.

    В столбце «Имя» найдите «Семейство PBS "стандартный" ЦП» и убедитесь, что у вас есть по крайней мере шесть виртуальных ЦП в разделе «CurrentValue.»

    Если у вас квоты, отправьте форму запроса [здесь](https://aka.ms/accelerateAI).

- Должны быть установлены рабочая область службы "Машинное обучение Azure" и пакет SDK Машинного обучения Azure для Python. Дополнительные сведения о получении этих необходимых компонентов см. в документе [Настройка среды разработки](how-to-configure-environment.md).
 
- Пакет SDK для Python для аппаратного ускорения моделей:

    ```shell
    pip install --upgrade azureml-accel-models
    ```

## <a name="sample-notebooks"></a>Примеры записных книжек

Для удобства [примеры записных книжек](https://aka.ms/aml-accel-models-notebooks) доступны в приведенном ниже примере и другие примеры.

## <a name="create-and-containerize-your-model"></a>Создание и упаковать модель

В этом документе будет описывается создание диаграммы TensorFlow для предварительной обработки входного изображения, вызвать его с помощью ResNet-50 на FPGA характеризатора и запустите средства через обучена на наборе данных ImageNet классификатора.

Следуйте инструкциям по выполнению следующих процедур:

* Определение модели TensorFlow
* Преобразование модели
* Развертывание модели
* Использовать развернутую модель.
* Удалить развернутые службы.

### <a name="load-azure-ml-workspace"></a>Загрузить рабочую область машинного Обучения Azure

Загрузите рабочую область машинного Обучения Azure.

```python
import os
import tensorflow as tf
 
from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')
```

### <a name="preprocess-image"></a>Предварительная обработка изображения

Входные данные веб-службы — изображения в формате JPEG.  Первым делом декодировать изображение JPEG и предварительно обработать его.  Изображения JPEG, рассматриваются как строки и результат tensors, которые будут входные данные в модель ResNet-50.

```python
# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.accel.models.utils as utils
tf.reset_default_graph()

in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="load-featurizer"></a>Загрузить featurizer

Инициализируйте модель и загрузите контрольную точку TensorFlow квантованной версии ResNet50 для использования в качестве характеризатора.  Вы можете заменить «QuantizedResnet50» в приведенном ниже фрагменте кода с путем импорта других глубоких нейронных сетей:

- QuantizedResnet152
- QuantizedVgg16
- Densenet121

```python
from azureml.accel.models import QuantizedResnet50
save_path = os.path.expanduser('~/models')
model_graph = QuantizedResnet50(save_path, is_frozen = True)
feature_tensor = model_graph.import_graph_def(image_tensors)
print(model_graph.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>Добавление классификатора

Этот классификатор обучен с помощью набора данных ImageNet.  Примеры для передачи обучения и обучения в настраиваемых весов доступны в наборе [примеры записных книжек](https://aka.ms/aml-notebooks).

```python
classifier_output = model_graph.get_default_classifier(feature_tensor)
print(classifier_output)
```

### <a name="save-the-model"></a>Сохранение модели

Теперь, когда были загружены препроцессор featurizer ResNet-50 и классификатор, сохраните как модель диаграммы и связанные переменные.

```python
model_name = "resnet50"
model_save_path = os.path.join(save_path, model_name)
print("Saving model in {}".format(model_save_path))

with tf.Session() as sess:
    model_graph.restore_weights(sess)
    tf.saved_model.simple_save(sess, model_save_path,
                                   inputs={'images': in_images},
                                   outputs={'output_alias': classifier_output})
```

### <a name="save-input-and-output-tensors"></a>Сохранить tensors ввода-вывода
Для преобразования модели и вывод необходимы tensors ввода-вывода, которые были созданы во время действия предварительной обработки и классификатора.

```python
input_tensors = in_images.name
output_tensors = classifier_output.name

print(input_tensors)
print(output_tensors)
```

> [!IMPORTANT]
> Сохраните входные данные и вывода tensors, так как они понадобятся для преобразования и вывод запросов модели.

Доступные модели и соответствующих классификатор по умолчанию вывода ниже, являются tensors, который является то, что используется для вывода при использовании классификатор по умолчанию.

+ Resnet50 QuantizedResnet50
  ```
  output_tensors = "classifier_1/resnet_v1_50/predictions/Softmax:0"
  ```
+ Resnet152, QuantizedResnet152
  ```
  output_tensors = "classifier/resnet_v1_152/predictions/Softmax:0"
  ```
+ Densenet121, QuantizedDensenet121
  ```
  output_tensors = "classifier/densenet121/predictions/Softmax:0"
  ```
+ Vgg16 QuantizedVgg16
  ```
  output_tensors = "classifier/vgg_16/fc8/squeezed:0"
  ```
+ SsdVgg QuantizedSsdVgg
  ```
  output_tensors = ['ssd_300_vgg/block4_box/Reshape_1:0', 'ssd_300_vgg/block7_box/Reshape_1:0', 'ssd_300_vgg/block8_box/Reshape_1:0', 'ssd_300_vgg/block9_box/Reshape_1:0', 'ssd_300_vgg/block10_box/Reshape_1:0', 'ssd_300_vgg/block11_box/Reshape_1:0', 'ssd_300_vgg/block4_box/Reshape:0', 'ssd_300_vgg/block7_box/Reshape:0', 'ssd_300_vgg/block8_box/Reshape:0', 'ssd_300_vgg/block9_box/Reshape:0', 'ssd_300_vgg/block10_box/Reshape:0', 'ssd_300_vgg/block11_box/Reshape:0']
  ```

### <a name="register-model"></a>Регистрация модели

[Зарегистрировать](./concept-model-management-and-deployment.md) созданной модели.  Добавляя теги и другие метаданные о модели можно хранить список ваших обученных моделей.

```python
from azureml.core.model import Model

registered_model = Model.register(workspace = ws,
                                  model_path = model_save_path,
                                  model_name = model_name)

print("Successfully registered: ", registered_model.name, registered_model.description, registered_model.version, sep = '\t')
```

Если вы уже зарегистрировали модели и загрузить его, могут получить его.

```python
from azureml.core.model import Model
model_name = "resnet50"
# By default, the latest version is retrieved. You can specify the version, i.e. version=1
registered_model = Model(ws, name="resnet50")
print(registered_model.name, registered_model.description, registered_model.version, sep = '\t')
```

### <a name="convert-model"></a>Преобразование модели

Преобразует граф TensorFlow в формате Open Exchange нейронной сети ([ONNX](https://onnx.ai/)).  Вам нужно будет предоставлять имена tensors входных и выходных, и эти имена будут использоваться вашим клиентом, при использовании веб-службы.

```python
from azureml.accel import AccelOnnxConverter

convert_request = AccelOnnxConverter.convert_tf_model(ws, registered_model, input_tensors, output_tensors)

# If it fails, you can run wait_for_completion again with show_output=True.
convert_request.wait_for_completion(show_output = False)

# If the above call succeeded, get the converted model
converted_model = convert_request.result
print("\nSuccessfully converted: ", converted_model.name, converted_model.url, converted_model.version, 
      converted_model.id, converted_model.created_time, '\n')
```

### <a name="create-docker-image"></a>Создание образа Docker

Преобразованный модели и всех зависимостей, добавляются в образ Docker.  Затем этот образ Docker можно развернуть и создании экземпляра.  Поддерживаемое развертывание цели включают AKS в облаке или на граничном устройстве, такие как [Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview).  Можно также добавить теги и описания для зарегистрированного образа Docker.

```python
from azureml.core.image import Image
from azureml.accel import AccelContainerImage

image_config = AccelContainerImage.image_configuration()
# Image name must be lowercase
image_name = "{}-image".format(model_name)

image = Image.create(name = image_name,
                     models = [converted_model],
                     image_config = image_config, 
                     workspace = ws)
image.wait_for_creation(show_output = False)
```

Список образов с использованием тега и получить подробные журналы для любой отладки.

```python
for i in Image.list(workspace = ws):
    print('{}(v.{} [{}]) stored at {} with build log {}'.format(i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
```

## <a name="model-deployment"></a>Развертывание модели

### <a name="deploy-to-the-cloud"></a>Развертывание в облаке

Для развертывания модели в качестве крупномасштабной рабочей веб-службы используется Служба Azure Kubernetes (AKS). Можно создать новый с помощью пакета SDK для Azure Machine Learning, CLI или портала Azure.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Specify the Standard_PB6s Azure VM
prov_config = AksCompute.provisioning_configuration(vm_size = "Standard_PB6s",
                                                    agent_count = 1)

aks_name = 'my-aks-cluster'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws, 
                                  name = aks_name, 
                                  provisioning_configuration = prov_config)
```

Развертывание AKS может занять около 15 минут.  Проверьте успешность развертывания.

```python
aks_target.wait_for_completion(show_output = True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

Развертывание контейнера в кластере AKS.
```python
from azureml.core.webservice import Webservice, AksWebservice

# For this deployment, set the web service configuration without enabling auto-scaling or authentication for testing
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                num_replicas=1,
                                                auth_enabled = False)

aks_service_name ='my-aks-service'

aks_service = Webservice.deploy_from_image(workspace = ws,
                                           name = aks_service_name,
                                           image = image,
                                           deployment_config = aks_config,
                                           deployment_target = aks_target)
aks_service.wait_for_deployment(show_output = True)
```

#### <a name="test-the-cloud-service"></a>Тестирование облачной службы
Образ Docker поддерживает gRPC и обслуживает TensorFlow, «прогнозирование» API.  Используйте пример клиента для вызова в образ Docker, чтобы получить прогнозы из модели.  Пример кода клиента доступен:
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

Если вы хотите использовать, обслуживающий TensorFlow, вы можете [загрузить образец клиента](https://www.tensorflow.org/serving/setup).

```python
# Using the grpc client in Azure ML Accelerated Models SDK package
from azureml.accel import PredictionClient

address = aks_service.scoring_uri
ssl_enabled = address.startswith("https")
address = address[address.find('/')+2:].strip('/')
port = 443 if ssl_enabled else 80

# Initialize AzureML Accelerated Models client
client = PredictionClient(address=address,
                          port=port,
                          use_ssl=ssl_enabled,
                          service_name=aks_service.name)
```

Так как этот классификатор была обучена на [ImageNet](http://www.image-net.org/) данных значение, сопоставить классы в удобное для восприятия метки.

```python
import requests
classes_entries = requests.get("https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()

# Score image with input and output tensor names
results = client.score_file(path="./snowleopardgaze.jpg", 
                             input_name=input_tensors, 
                             outputs=output_tensors)

# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
```

### <a name="clean-up-the-service"></a>Очистка службы
Удаление вашей веб-службы, изображения и модели (должна выполняться в указанном порядке, так как существуют зависимости).

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

## <a name="deploy-to-a-local-edge-server"></a>Развертывание на сервере локальные пограничные

Все [Azure Data Box пограничные устройства](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
) содержат FPGA для запуска модели.  Только одна модель может быть запущен на FPGA за один раз.  Чтобы запустить другую модель, просто разверните новый контейнер. Инструкции и примеры кода можно найти в [в этом примере Azure](https://github.com/Azure-Samples/aml-hardware-accelerated-models).

## <a name="secure-fpga-web-services"></a>Защита веб-служб FPGA

Сведения о защите веб-служб FPGA см. в статье [Использование SSL для защиты веб-служб, развернутых с помощью Службы машинного обучения Azure](how-to-secure-web-service.md).

## <a name="pbs-family-vms"></a>Семейство PBS виртуальных машин

Семейство PBS виртуальных машин Azure содержит Intel Arria 10 FPGAs.  Он отобразится как «Виртуальных ЦП семейства Standard PBS» при возврате выделения квоты Azure.  Виртуальная машина PB6 имеет шесть виртуальных ЦП и один FPGA и он будет автоматически подготовлен по машинного Обучения Azure как часть развертывания модели для FPGA.  Он используется только с помощью машинного Обучения Azure, и он не может выполняться произвольный bitstreams.  Например вы не сможете flash FPGA с bitstreams, чтобы выполнить шифрование, кодировки и т. д. 
