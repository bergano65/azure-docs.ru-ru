---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 97f0412141f15ad0a72c02b92cfcf089b61db0cf
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88120356"
---
## <a name="prerequisites"></a>Предварительные требования

- Рабочая область машинного обучения Azure. Дополнительные сведения см. в статье [создание машинное обучение Azure рабочей области](../articles/machine-learning/how-to-manage-workspace.md).
- Модель. Если у вас нет обученной модели, можно использовать модель и файлы зависимостей, предоставленные в [этом руководстве](https://aka.ms/azml-deploy-cloud).
- [Пакет средств разработки программного обеспечения (SDK) для Python (машинное обучение Azure)](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)


## <a name="connect-to-your-workspace"></a>Подключение к рабочей области

```python
from azureml.core import Workspace
ws = Workspace.from_config(path=".file-path/ws_config.json")
```

Дополнительные сведения об использовании пакета SDK для подключения к рабочей области см. в документации по [машинное обучение Azure SDK для Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) .

## <a name="register-your-model"></a><a id="registermodel"></a>Регистрация модели

Зарегистрированная модель — это логический контейнер для одного или нескольких файлов, составляющих эту модель. Например, если имеется модель, которая хранится в нескольких файлах, можно зарегистрировать их как единую модель в рабочей области. После регистрации файлов можно скачать или развернуть зарегистрированную модель и получить все зарегистрированные файлы.

> [!TIP]
> При регистрации модели вы предоставляете путь к облачному расположению (из обучающего запуска) или к локальному каталогу. Этот путь предназначен только для поиска файлов для отправки в ходе процесса регистрации. Он не должен соответствовать пути, используемому в скрипте записи. Дополнительные сведения см. [в разделе Поиск файлов модели в скрипте записи](../articles/machine-learning/how-to-deploy-advanced-entry-script.md#load-registered-models).

Модели машинного обучения регистрируются в рабочей области Машинное обучение Azure. Модель может поступать из Машинное обучение Azure или в другом месте. При регистрации модели можно дополнительно предоставить метаданные о модели. `tags` `properties` Словари и, применяемые к регистрации модели, затем можно использовать для фильтрации моделей.

В следующих примерах показано, как зарегистрировать модель.

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Регистрация модели из запуска обучения Azure ML

  При использовании пакета SDK для обучения модели можно получить либо объект [запуска](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) , либо объект [аутомлрун](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) в зависимости от того, как была обучена модель. Каждый объект можно использовать для регистрации модели, созданной с помощью запуска эксперимента.

  + Зарегистрировать модель из `azureml.core.Run` объекта:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    `model_path`Параметр ссылается на расположение в облаке модели. В этом примере используется путь к одному файлу. Чтобы включить несколько файлов в регистрацию модели, задайте `model_path` путь к папке, содержащей файлы. Дополнительные сведения см. в документации по [Run. register_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) .

  + Зарегистрировать модель из `azureml.train.automl.run.AutoMLRun` объекта:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'mnist'})

        print(run.model_id)
    ```

    В этом примере `metric` `iteration` Параметры и не указаны, поэтому будет зарегистрирована итерация с лучшей основной метрикой. `model_id`Значение, возвращаемое при выполнении, используется вместо имени модели.

    Дополнительные сведения см. в документации по [аутомлрун. register_model](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) .


### <a name="register-a-model-from-a-local-file"></a>Регистрация модели из локального файла

Вы можете зарегистрировать модель, указав локальный путь к модели. Можно указать путь либо к папке, либо к одному файлу. С помощью этого метода можно регистрировать модели, обученные Машинное обучение Azure, а затем загружать их. Этот метод также можно использовать для регистрации моделей, обученных за пределами Машинное обучение Azure.

[!INCLUDE [trusted models](machine-learning-service-trusted-model.md)]

+ **Использование пакета SDK и ONNX**

    ```python
    import os
    import urllib.request
    from azureml.core.model import Model
    # Download model
    onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
    urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
    os.system('tar xvzf mnist.tar.gz')
    # Register model
    model = Model.register(workspace = ws,
                            model_path ="mnist/model.onnx",
                            model_name = "onnx_mnist",
                            tags = {"onnx": "demo"},
                            description = "MNIST image classification CNN from ONNX Model Zoo",)
    ```

  Чтобы включить несколько файлов в регистрацию модели, задайте `model_path` путь к папке, содержащей файлы.

Дополнительные сведения см. в документации по [классу Model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

Дополнительные сведения о работе с моделями, обученными вне Машинное обучение Azure, см. [в разделе Развертывание существующей модели](../articles/machine-learning/how-to-deploy-existing-model.md).

## <a name="define-an-entry-script"></a>Определение скрипта записи

[!INCLUDE [write entry script](machine-learning-entry-script.md)]

## <a name="define-an-inferenceconfig"></a>Определение Инференцеконфиг

Конфигурация вывода описывает, как настроить веб-службу, содержащую модель. Он используется позже при развертывании модели.

Конфигурация вывода использует среды Машинное обучение Azure для определения зависимостей программного обеспечения, необходимых для развертывания. Среды позволяют создавать, администрировать и повторно использовать зависимости программного обеспечения, необходимые для обучения и развертывания. Среду можно создать из пользовательских файлов зависимостей или с помощью одной из проверенных Машинное обучение Azure сред. Следующий YAML является примером файла зависимостей Conda для вывода. Обратите внимание, что необходимо указать значения azureml-Default с версии >= 1.0.45 в качестве зависимости PIP, так как она содержит функции, необходимые для размещения модели в качестве веб-службы. Если вы хотите использовать автоматическое создание схем, сценарий записи также должен импортировать `inference-schema` пакеты.

```YAML

name: project_environment
dependencies:
    - python=3.6.2
    - scikit-learn=0.20.0
    - pip:
        # You must list azureml-defaults as a pip dependency
    - azureml-defaults>=1.0.45
    - inference-schema[numpy-support]
```

> [!IMPORTANT]
> Если зависимость доступна с помощью Conda и PIP (из PyPi), корпорация Майкрософт рекомендует использовать версию Conda, так как Conda пакеты обычно поставляются с предварительно созданными двоичными файлами, которые делают установку более надежной.
>
> Дополнительные сведения см. в разделе [Основные сведения о Conda и PIP](https://www.anaconda.com/understanding-conda-and-pip/).
>
> Чтобы проверить, доступна ли зависимость через Conda, используйте `conda search <package-name>` команду или используйте индексы пакетов по адресу [https://anaconda.org/anaconda/repo](https://anaconda.org/anaconda/repo) и [https://anaconda.org/conda-forge/repo](https://anaconda.org/conda-forge/repo) .

Файл зависимостей можно использовать для создания объекта среды и сохранения его в рабочей области для будущего использования:

```python
from azureml.core.environment import Environment
myenv = Environment.from_conda_specification(name = 'myenv',
                                                file_path = 'path-to-conda-specification-file'
myenv.register(workspace=ws)
```

Подробное описание использования и настройки сред Python с помощью Машинное обучение Azure см. [в разделе создание & использование программных сред в машинное обучение Azure](../articles/machine-learning/how-to-use-environments.md)

Сведения об использовании пользовательского образа DOCKER с конфигурацией вывода см. в статье [развертывание модели с помощью пользовательского образа DOCKER](../articles/machine-learning/how-to-deploy-custom-docker-image.md).


В следующем примере демонстрируется загрузка среды из рабочей области и ее использование с конфигурацией вывода:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


myenv = Environment.get(workspace=ws, name='myenv', version='1')
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                    environment=myenv)
```

Дополнительные сведения о средах см. в статье [создание сред для обучения и развертывания и управление ими](../articles/machine-learning/how-to-use-environments.md).

Дополнительные сведения о конфигурации вывода см. в документации по классу [инференцеконфиг](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) .

## <a name="choose-a-compute-target"></a>Выбор целевого объекта вычислений

[!INCLUDE [aml-compute-target-deploy](aml-compute-target-deploy.md)]

## <a name="define-a-deploymentconfiguration"></a>Определение Деплойментконфигуратион

Перед развертыванием модели необходимо определить конфигурацию развертывания. *Конфигурация развертывания зависит от целевого объекта вычислений, в котором будет размещена веб-служба.* Например, при локальном развертывании модели необходимо указать порт, на котором служба принимает запросы. Конфигурация развертывания не является частью скрипта записи. Он используется для определения характеристик целевого объекта вычислений, в котором будет размещаться скрипт модели и входа.

Вам также может потребоваться создать вычислительный ресурс, если, например, у вас еще нет экземпляра Azure Kubernetes Service (AKS), связанного с вашей рабочей областью.

В следующей таблице приведен пример создания конфигурации развертывания для каждого целевого объекта вычислений.

| Целевой объект вычисления | Пример конфигурации развертывания |
| ----- | ----- |
| Локальная | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Экземпляры контейнеров Azure | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Служба Azure Kubernetes | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Классы для локальных объектов, экземпляров контейнеров Azure и веб-служб AKS можно импортировать из `azureml.core.webservice` :

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

## <a name="deploy-your-model"></a>Развертывание модели

Теперь вы готовы к развертыванию модели. В приведенном ниже примере демонстрируется локальное развертывание. Синтаксис будет зависеть от целевого объекта вычислений, выбранного на предыдущем шаге.

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Дополнительные сведения см. в документации по [локалвебсервице](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), [model. deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)и [WebService](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

## <a name="delete-resources"></a>Удаление ресурсов

Для удаления развернутой веб-службы используйте `service.delete()`.
Чтобы удалить зарегистрированную модель, используйте `model.delete()`.

Дополнительные сведения см. в документации по [WebService. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) и [model. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).