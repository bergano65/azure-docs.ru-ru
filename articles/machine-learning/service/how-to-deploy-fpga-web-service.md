---
title: Как развернуть модель как веб-службу в FPGA с помощью Машинного обучения Azure
description: Узнайте, как развернуть веб-службу с моделью, работающей на базе FPGA, с использованием Машинного обучения Azure.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 10/29/2018
ms.openlocfilehash: 3b2ff5bf4413c5d6a681aff66f0f338567f3b6a8
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50242744"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning"></a>Как развернуть модель как веб-службу в FPGA с помощью Машинного обучения Azure

Вы можете развернуть модель как веб-службу в [матрицах FPGA](concept-accelerate-with-fpgas.md).  При использовании FPGA обеспечивается сверхнизкая задержка даже в случае пакетов одного размера.   

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure. Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начать работу.

- Должны быть установлены рабочая область службы "Машинное обучение Azure" и пакет SDK Машинного обучения Azure для Python. Дополнительные сведения о получении этих необходимых компонентов см. в документе [Настройка среды разработки](how-to-configure-environment.md).
 
  - Ваша рабочая область должна находиться в регионе *Восточная часть США 2*.

  - Установите дополнительные компоненты.

    ```shell
    pip install --upgrade azureml-sdk[contrib]
    ```  

## <a name="create-and-deploy-your-model"></a>Создание и развертывание модели
Создайте конвейер для предварительной обработки входного изображения, определите для него признаки, используя модель ResNet 50 в FPGA, а затем пропустите признаки через классификатор, обученный с помощью набора данных ImageNet.

Следуйте инструкциям по выполнению следующих процедур:

* Определение конвейера модели
* Развертывание модели
* Использовать развернутую модель.
* Удалить развернутые службы.

> [!IMPORTANT]
> Чтобы оптимизировать задержку и пропускную способность, ваш клиент должен находиться в том же регионе Azure, что и конечная точка.  В настоящее время API создаются в регионе Azure "Восточная часть США".



### <a name="preprocess-image"></a>Предварительная обработка изображения
Первый этап конвейера — предварительная обработка изображений.

```python
import os
import tensorflow as tf

# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.contrib.brainwave.models.utils as utils
in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="add-featurizer"></a>Добавление характеризатора
Инициализируйте модель и загрузите контрольную точку TensorFlow квантованной версии ResNet50 для использования в качестве характеризатора.

```python
from azureml.contrib.brainwave.models import QuantizedResnet50, Resnet50
model_path = os.path.expanduser('~/models')
model = QuantizedResnet50(model_path, is_frozen = True)
feature_tensor = model.import_graph_def(image_tensors)
print(model.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>Добавление классификатора
Этот классификатор обучен с помощью набора данных ImageNet.

```python
classifier_input, classifier_output = Resnet50.get_default_classifier(feature_tensor, model_path)
```

### <a name="create-service-definition"></a>Создание определения службы
Определив предварительную обработку изображений, характеризатор и классификатор, который выполняется в службе, можно создать определение службы. Определение службы — это набор файлов, созданных на основе модели, которая развертывается в службе FPGA. Определение службы состоит из конвейера. Конвейер — это последовательность этапов, которые выполняются по порядку.  Поддерживаются этапы TensorFlow, Keras и BrainWave.  Этапы выполняются в службе по порядку: выходные данные каждого этапа используются как входные данные для следующего этапа.

Для создания этапа TensorFlow укажите сеанс, содержащий граф (в данном случае используется граф по умолчанию), и входные и выходные тензоры для этого этапа.  Эти данные используются для сохранения графа, чтобы его можно было выполнить в службе.

```python
from azureml.contrib.brainwave.pipeline import ModelDefinition, TensorflowStage, BrainWaveStage

save_path = os.path.expanduser('~/models/save')
model_def_path = os.path.join(save_path, 'service_def.zip')

model_def = ModelDefinition()
with tf.Session() as sess:
    model_def.pipeline.append(TensorflowStage(sess, in_images, image_tensors))
    model_def.pipeline.append(BrainWaveStage(sess, model))
    model_def.pipeline.append(TensorflowStage(sess, classifier_input, classifier_output))
    model_def.save(model_def_path)
    print(model_def_path)
```

### <a name="deploy-model"></a>Развертывание модели
Создайте службу на основе определения службы.  Ваша рабочая область должна находиться в регионе "Восточная часть США 2".

```python
from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')

from azureml.core.model import Model
model_name = "resnet-50-rtai"
registered_model = Model.register(ws, model_def_path, model_name)

from azureml.core.webservice import Webservice
from azureml.exceptions import WebserviceException
from azureml.contrib.brainwave import BrainwaveWebservice, BrainwaveImage
service_name = "imagenet-infer"
service = None
try:
    service = Webservice(ws, service_name)
except WebserviceException:
    image_config = BrainwaveImage.image_configuration()
    deployment_config = BrainwaveWebservice.deploy_configuration()
    service = Webservice.deploy_from_model(ws, service_name, [registered_model], image_config, deployment_config)
    service.wait_for_deployment(true)
```

### <a name="test-the-service"></a>Тестирование службы
Чтобы отправить изображение в API и проверить ответ, добавьте сопоставление из идентификатора выходного класса в имя класса ImageNet.

```python
import requests
classes_entries = requests.get("https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()
```

Вызовите службу и замените имя файла your-image.jpg ниже изображением с компьютера. 

```python
with open('your-image.jpg') as f:
    results = service.run(f)
# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
``` 

### <a name="clean-up-service"></a>Удаление службы
Удалите службу.

```python
service.delete()
    
registered_model.delete()
```

## <a name="secure-fpga-web-services"></a>Защита веб-служб FPGA

Модели машинного обучения Azure, выполняющиеся в FPGA, обеспечивают поддержку SSL и аутентификацию на основе ключей. Это позволит вам ограничивать доступ к вашей службе и защищать данные, предоставляемые клиентами. [Узнайте, как защитить веб-службы](how-to-secure-web-service.md).


## <a name="sample-notebook"></a>Пример записной книжки

Основные понятия из этой статьи демонстрируются в записной книжке [project-brainwave/project-brainwave-quickstart.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/project-brainwave/project-brainwave-quickstart.ipynb).

Получите эту записную книжку:

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
