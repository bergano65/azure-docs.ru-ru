---
title: Что такое FPGA - как развернуть
titleSuffix: Azure Machine Learning
description: Узнайте, как развернуть веб-службу с моделью, работающая на FPGA с Azure Machine Learning, для получения сверхнизкой задержки.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 8cb6cf49e302122849dc2402bcff008e72e15608
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472364"
---
# <a name="what-are-field-programmable-gate-arrays-fpga-and-how-to-deploy"></a>Что такое полевые наборы ворот (FPGA) и как развертывать
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье приводится введение в полевые программируемые массивы ворот (FPGA) и показано, как развертывать модели с помощью Azure Machine Learning в Azure FPGA.

ППВМ содержат массив программируемых логических блоков и иерархию настраиваемых взаимоподключений. Взаимоподключения позволяют настраивать эти блоки различными способами после производства. В отличие от других микросхем, ППВМ обеспечивают сочетание программируемости и производительности.

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGA против процессора, GPU и ASIC

На схеме и в таблице ниже показано сравнение ППВМ и других процессоров.

![Диаграмма сравнения FPGA машинного обучения Azure](./media/how-to-deploy-fpga-web-service/azure-machine-learning-fpga-comparison.png)

|Процессор||Описание|
|---|:-------:|------|
|Микросхемы приложений|ASIC|Пользовательские микросхемы, такие как процессор TensorFlow (TPU) от Google, предоставляют повышенную эффективность. Их нельзя настраивать повторно в случае изменения требований.|
|Программируемые пользователем вентильные матрицы|FPGA|ППВМ (например те, что доступны в Azure) обеспечивают производительность, близкую к микросхемам ASIC. Они также гибкие и поддерживают перенастройку для реализации новой логики.|
|Графические процессоры|GPU|Широко применяются для вычислений с использованием ИИ. Они обеспечивают возможности параллельной обработки, что позволяет выполнять отрисовку изображений быстрее, чем ЦП.|
|Центральные процессоры|ЦП|Процессоры общего назначения, производительность которых не подходит для обработки графики и видео.|

FPGA на Azure основаны на устройствах Intel FPGA, которые специалисты и разработчики данных используют для ускорения вычислений ИИ в режиме реального времени. Эта архитектура с поддержкой ППВМ доступна в Azure и обеспечивает производительность, гибкость и масштабирование.

FPGA позволяют достичь низкой задержки для запросов выводов в реальном времени (или оценки модели). Асинхронные запросы (пакетная обработка) не нужны. Пакетная обработка может повысить задержку, так как необходимо обработать больше данных. Реализация нейронных процессоров не требует пакетирования; поэтому задержка может быть во много раз ниже, по сравнению с процессорами процессоров и процессоров GPU.

### <a name="reconfigurable-power"></a>Настраиваемое питание
ППВМ можно перенастроить для различных типов моделей машинного обучения. Такая гибкость упрощает ускорение приложений на основе наиболее оптимальной точности числовых значений и используемой модели памяти. Так как ППВМ перенастраиваются, они могут поддерживать актуальные требования быстро меняющихся алгоритмов ИИ.

## <a name="whats-supported-on-azure"></a>Поддерживаемые в Azure функции
Microsoft Azure – крупнейшая в мире облачная инвестиция в FPGA. Используя эту аппаратную архитектуру с поддержкой FPGA, обученные нейронные сети работают быстро и с меньшей задержкой. Azure может параллелизировать предварительно обученные глубокие нейронные сети (DNN) через FPGA, чтобы масштабировать свой сервис. DNN можно предварительно обучить как глубокий характеризатор для переноса обучения или настроить с использованием обновленных весовых коэффициентов.

FPGA на поддержке Azure:

+ Сценарии классификации и распознавания изображений.
+ Развертывание TensorFlow.
+ Оборудование FPGA Intel.

Эти модели DNN в настоящее время доступны:
  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16
  - SSD-VGG

FPGA доступны в этих регионах Azure:
  - Восточная часть США
  - Юго-Восточная Азия
  - Западная Европа
  - западная часть США 2

> [!IMPORTANT]
> Чтобы оптимизировать задержку и пропускную связь, клиент, отправляющий данные в модель FPGA, должен находиться в одном из вышеперечисленных регионов (тот, в который вы развернули модель).

Семейство **VMs семейства Azure** содержит Intel Arria 10 FPGA. Он будет отображаться как "Стандартный PBS Family vCPUs", когда вы проверяете распределение квот Azure. PB6 VM имеет шесть vCPUs и один FPGA, и он будет автоматически подготовлен Azure ML в рамках развертывания модели для FPGA. Он используется только с Azure ML, и он не может запускать произвольные битстримы. Например, вы не сможете мигать FPGA с битстримами, чтобы сделать шифрование, кодирование и т.д.

### <a name="scenarios-and-applications"></a>Сценарии и приложения

Azure FPGA интегрированы с машинным обучением Azure. Корпорация Майкрософт использует ППВМ для оценки DNN, ранжирования поиска Bing и ускорения работы программно определяемой сети (SDN), чтобы уменьшить задержку, одновременно освобождая ЦП для других задач.

В следующих сценариях используются FPGA:
+ [Автоматизированная система оптического осмотра](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)

+ [Отображение земельного покрова](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)

## <a name="example-deploy-models-on-fpgas"></a>Пример: Развертывание моделей на FPGA

Можно развернуть модель в качестве веб-сервиса на FPGA с помощью ускоренных моделей машинного обучения Azure. Использование FPGAs обеспечивает сверхнизкую задержку, даже с одним размером партии. Вывод, или оценка модели, — это этап, на котором развернутая модель используется для прогнозирования, чаще всего на производственных данных.

### <a name="prerequisites"></a>Предварительные требования

- Подписка Azure.  Если у вас еще нет подписки Azure, создайте бесплатную учетную запись, прежде чем начать работу. Опробуйте [бесплатную или платную версию Машинного обучения Azure](https://aka.ms/AMLFree) уже сегодня.

- Квота FPGA. Используйте Azure CLI, чтобы проверить, есть ли у вас квота:

    ```azurecli-interactive
    az vm list-usage --location "eastus" -o table --query "[?localName=='Standard PBS Family vCPUs']"
    ```

    > [!TIP]
    > Другие возможные ``southeastasia`` ``westeurope``места, ``westus2``и .

    Команда возвращает текст, похожий на следующий:

    ```text
    CurrentValue    Limit    LocalName
    --------------  -------  -------------------------
    0               6        Standard PBS Family vCPUs
    ```

    Убедитесь, что у вас есть по крайней мере 6 vCPUs под __CurrentValue__.

    Если у вас нет квоты, [https://aka.ms/accelerateAI](https://aka.ms/accelerateAI)а затем отправить запрос на .

- Должны быть установлены рабочая область машинного обучения Azure и пакет SDK машинного обучения Azure для Python. Для получения дополнительной информации [см.](how-to-manage-workspace.md)
 
- Python SDK для аппаратно-ускоренных моделей:

    ```bash
    pip install --upgrade azureml-accel-models
    ```


## <a name="1-create-and-containerize-models"></a>1. Создание и таретаризация моделей

В этом документе будет описано, как создать график TensorFlow для предварительного обработки входном изображения, сделать его феатуризатором с помощью ResNet 50 на FPGA, а затем запустить функции через классификатор, обученный на наборе данных ImageNet.

Следуйте инструкциям по выполнению следующих процедур:

* Определите модель TensorFlow
* Преобразование модели
* Развертывание модели
* Использовать развернутую модель.
* Удалить развернутые службы.

Используйте [пакет SDK Машинного обучения Azure для Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py), чтобы создать определение службы. Определение службы представляет собой файл, описывающий конвейер графов (входящие данные, характеризатор и классификатор) на основе TensorFlow. Команда развертывания автоматически выполняет сжатие определения и графов в ZIP-файл и передает этот файл в хранилище BLOB-объектов Azure. DNN уже развернутдля для запуска на FPGA.

### <a name="load-azure-machine-learning-workspace"></a>Загрузка рабочего пространства машинного обучения Azure

Загрузите рабочее пространство машинного обучения Azure.

```python
import os
import tensorflow as tf

from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep='\n')
```

### <a name="preprocess-image"></a>Предварительная обработка изображения

Вход в веб-сервис — это изображение JPEG.  Первым шагом является расшифровка изображения JPEG и его предварительное оформление.  Изображения JPEG рассматриваются как строки, и в результате будут входить в модель ResNet 50.

```python
# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.accel.models.utils as utils
tf.reset_default_graph()

in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="load-featurizer"></a>Нагрузка феатурайзер

Инициализируйте модель и загрузите контрольную точку TensorFlow квантованной версии ResNet50 для использования в качестве характеризатора.  Вы можете заменить "КвантидResnet50" в фрагменте кода ниже, импортируя другие глубокие нейронные сети:

- КвантидРеснет152
- Квантидвгг16
- Плотнеет121

```python
from azureml.accel.models import QuantizedResnet50
save_path = os.path.expanduser('~/models')
model_graph = QuantizedResnet50(save_path, is_frozen=True)
feature_tensor = model_graph.import_graph_def(image_tensors)
print(model_graph.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>Добавление классификатора

Этот классификатор обучен с помощью набора данных ImageNet.  Примеры для передачи обучения и обучения настраиваемые веса доступны в наборе [образцов ноутбуков.](https://aka.ms/aml-notebooks)

```python
classifier_output = model_graph.get_default_classifier(feature_tensor)
print(classifier_output)
```

### <a name="save-the-model"></a>Сохранение модели

Теперь, когда препроцессор, ResNet 50 featurizer и классификатор были загружены, сохраните график и связанные с ними переменные в качестве модели.

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

### <a name="save-input-and-output-tensors"></a>Сохранение ввода и выходных тензоров
Для преобразования и вывода модели потребуются вводные и выходные тензоры, созданные во время этапов предварительной обработки и классификации.

```python
input_tensors = in_images.name
output_tensors = classifier_output.name

print(input_tensors)
print(output_tensors)
```

> [!IMPORTANT]
> Сохранить вхотворные и выходные тензоры, потому что они понадобятся для конверсии модели и запросов вывода.

Доступные модели и соответствующие десятки вывода классификатора по умолчанию приведены ниже, что и будет использовано для вывода, если вы используете классификатор по умолчанию.

+ Resnet50, КвантидРеснет50
  ```python
  output_tensors = "classifier_1/resnet_v1_50/predictions/Softmax:0"
  ```
+ Resnet152, КвантидРеснет152
  ```python
  output_tensors = "classifier/resnet_v1_152/predictions/Softmax:0"
  ```
+ Densenet121, Квантидденсенет121
  ```python
  output_tensors = "classifier/densenet121/predictions/Softmax:0"
  ```
+ Vgg16, КвантидВгг16
  ```python
  output_tensors = "classifier/vgg_16/fc8/squeezed:0"
  ```
+ SsdVgg, КвантидСсдВгг
  ```python
  output_tensors = ['ssd_300_vgg/block4_box/Reshape_1:0', 'ssd_300_vgg/block7_box/Reshape_1:0', 'ssd_300_vgg/block8_box/Reshape_1:0', 'ssd_300_vgg/block9_box/Reshape_1:0', 'ssd_300_vgg/block10_box/Reshape_1:0', 'ssd_300_vgg/block11_box/Reshape_1:0', 'ssd_300_vgg/block4_box/Reshape:0', 'ssd_300_vgg/block7_box/Reshape:0', 'ssd_300_vgg/block8_box/Reshape:0', 'ssd_300_vgg/block9_box/Reshape:0', 'ssd_300_vgg/block10_box/Reshape:0', 'ssd_300_vgg/block11_box/Reshape:0']
  ```

### <a name="register-model"></a>Регистрация модели

[Зарегистрируйте](concept-model-management-and-deployment.md) модель, используя SDK с помощью файла «ЗИП» в хранилище Azure Blob. Добавление тегов и других метаданных о модели поможет вам отслеживать обученные модели.

```python
from azureml.core.model import Model

registered_model = Model.register(workspace=ws,
                                  model_path=model_save_path,
                                  model_name=model_name)

print("Successfully registered: ", registered_model.name,
      registered_model.description, registered_model.version, sep='\t')
```

Если вы уже зарегистрировали модель и хотите загрузить ее, вы можете получить ее.

```python
from azureml.core.model import Model
model_name = "resnet50"
# By default, the latest version is retrieved. You can specify the version, i.e. version=1
registered_model = Model(ws, name="resnet50")
print(registered_model.name, registered_model.description,
      registered_model.version, sep='\t')
```

### <a name="convert-model"></a>Преобразование модели

Преобразуйте график TensorFlow в формат Open Neural Network Exchange[(ONNX).](https://onnx.ai/)  Вам нужно будет узнать имена ввода и вывода тензоров, и эти имена будут использоваться вашим клиентом, когда вы потребляете веб-службы.

```python
from azureml.accel import AccelOnnxConverter

convert_request = AccelOnnxConverter.convert_tf_model(
    ws, registered_model, input_tensors, output_tensors)

# If it fails, you can run wait_for_completion again with show_output=True.
convert_request.wait_for_completion(show_output=False)

# If the above call succeeded, get the converted model
converted_model = convert_request.result
print("\nSuccessfully converted: ", converted_model.name, converted_model.url, converted_model.version,
      converted_model.id, converted_model.created_time, '\n')
```

### <a name="create-docker-image"></a>Создание образа Docker

Преобразованная модель и все зависимости добавляются к изображению Docker.  Это изображение Docker может быть развернуто и мгновенно.  Поддерживаемые цели развертывания включают AKS в облаке или устройство края, такие как [Azure Data Box Edge.](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview)  Вы также можете добавить теги и описания для зарегистрированного изображения Docker.

```python
from azureml.core.image import Image
from azureml.accel import AccelContainerImage

image_config = AccelContainerImage.image_configuration()
# Image name must be lowercase
image_name = "{}-image".format(model_name)

image = Image.create(name=image_name,
                     models=[converted_model],
                     image_config=image_config,
                     workspace=ws)
image.wait_for_creation(show_output=False)
```

Перечислите изображения по тегу и получите подробные журналы для любой отладки.

```python
for i in Image.list(workspace=ws):
    print('{}(v.{} [{}]) stored at {} with build log {}'.format(
        i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
```

## <a name="2-deploy-to-cloud-or-edge"></a>2. Развертывание в облако или край

### <a name="deploy-to-the-cloud"></a>Развертывание в облаке

Для развертывания модели в качестве крупномасштабной рабочей веб-службы используется Служба Azure Kubernetes (AKS). Создать новую можно с помощью студии Машинного обучения Azure, CLI или [Azure Machine Learning.](https://ml.azure.com)

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Specify the Standard_PB6s Azure VM and location. Values for location may be "eastus", "southeastasia", "westeurope", or "westus2". If no value is specified, the default is "eastus".
prov_config = AksCompute.provisioning_configuration(vm_size = "Standard_PB6s",
                                                    agent_count = 1,
                                                    location = "eastus")

aks_name = 'my-aks-cluster'
# Create the cluster
aks_target = ComputeTarget.create(workspace=ws,
                                  name=aks_name,
                                  provisioning_configuration=prov_config)
```

Развертывание AKS может занять около 15 минут.  Проверьте, удалось ли развертывание.

```python
aks_target.wait_for_completion(show_output=True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

Развертывание контейнера в кластер AKS.
```python
from azureml.core.webservice import Webservice, AksWebservice

# For this deployment, set the web service configuration without enabling auto-scaling or authentication for testing
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                num_replicas=1,
                                                auth_enabled=False)

aks_service_name = 'my-aks-service'

aks_service = Webservice.deploy_from_image(workspace=ws,
                                           name=aks_service_name,
                                           image=image,
                                           deployment_config=aks_config,
                                           deployment_target=aks_target)
aks_service.wait_for_deployment(show_output=True)
```

#### <a name="test-the-cloud-service"></a>Протестировать облачный сервис
Изображение Docker поддерживает gRPC и ТенсорПоток, обслуживающий API.  Используйте пример клиента, чтобы вызвать изображение Docker, чтобы получить прогнозы от модели.  Пример клиентского кода доступен:
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C #](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

Если вы хотите использовать Обслуживание TensorFlow, вы можете [скачать пример клиента.](https://www.tensorflow.org/serving/setup)

```python
# Using the grpc client in Azure ML Accelerated Models SDK package
from azureml.accel import PredictionClient

address = aks_service.scoring_uri
ssl_enabled = address.startswith("https")
address = address[address.find('/')+2:].strip('/')
port = 443 if ssl_enabled else 80

# Initialize Azure ML Accelerated Models client
client = PredictionClient(address=address,
                          port=port,
                          use_ssl=ssl_enabled,
                          service_name=aks_service.name)
```

Поскольку этот классификатор был обучен набору данных [ImageNet,](http://www.image-net.org/) отображайте классы на метки, читаемые человеком.

```python
import requests
classes_entries = requests.get(
    "https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()

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
Удалите веб-службу, изображение и модель (должно быть сделано в этом порядке, так как есть зависимости).

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

### <a name="deploy-to-a-local-edge-server"></a>Развертывание на локальном сервере края

Все [устройства Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
) содержат FPGA для запуска модели.  Только одна модель может работать на FPGA в одно время.  Чтобы запустить другую модель, просто развернуть новый контейнер. Инструкции и пример кода можно найти в [этом примере Azure.](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

## <a name="secure-fpga-web-services"></a>Защита веб-служб FPGA

Чтобы обезопасить веб-сервисы FPGA, смотрите документ [Безопасные веб-службы.](how-to-secure-web-service.md)

## <a name="next-steps"></a>Дальнейшие действия

Проверьте эти ноутбуки, видео и блоги:

+ Несколько [образцов тетрадей](https://aka.ms/aml-accel-models-notebooks)

+ [Гипермасштабное оборудование: ML в масштабе на вершине Azure и FPGA: Build 2018 (видео)](https://channel9.msdn.com/events/Build/2018/BRK3202)

+ [Внутренние параметры настраиваемого облака на основе FPGA Microsoft (видео)](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [Домашняя страница проекта Project Brainwave для использования искусственного интеллекта в режиме реального времени](https://www.microsoft.com/research/project/project-brainwave/)
