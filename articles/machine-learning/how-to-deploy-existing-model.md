---
title: Использование и развертывание существующих моделей
titleSuffix: Azure Machine Learning
description: Узнайте, как можно использовать машинное обучение Azure с моделями, прошедшими обучение вне службы. Можно зарегистрировать модели, созданные за пределами машинного обучения Azure, а затем развернуть их в виде веб-службы или модуля Azure IoT Edge.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 03/17/2020
ms.openlocfilehash: 924bd2fdba2359e6f1108c39802ad3ce95ebdf07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472381"
---
# <a name="use-an-existing-model-with-azure-machine-learning"></a>Используйте существующую модель с помощью машинного обучения Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Узнайте, как использовать существующую модель машинного обучения с помощью Azure Machine Learning.

Если у вас есть модель машинного обучения, которая была подготовлена за пределами Машинного Обучения Azure, вы все равно можете использовать эту службу для развертывания модели в качестве веб-службы или устройства IoT Edge. 

> [!TIP]
> В этой статье содержится базовая информация о регистрации и развертывании существующей модели. После развертывания Azure Machine Learning обеспечивает мониторинг вашей модели. Он также позволяет хранить данные ввода, отправленные в развертывание, которые могут быть использованы для анализа дрейфа данных или обучения новым версиям модели.
>
> Для получения дополнительной информации о концепциях и терминах, используемых здесь, [см.](concept-model-management-and-deployment.md)
>
> Для получения общей информации о процессе развертывания см. [модели развертываний с помощью машинного обучения Azure.](how-to-deploy-and-where.md)

## <a name="prerequisites"></a>Предварительные требования

* Рабочая область машинного обучения Azure. Для получения дополнительной информации [см.](how-to-manage-workspace.md)

    > [!TIP]
    > Примеры Python в этой `ws` статье предполагают, что переменная установлена в рабочем пространстве машин Azure Machine Learning.
    >
    > Примеры CLI используют заполнитель `myworkspace` и `myresourcegroup`. Замените их названием рабочего пространства и группой ресурсов, содержащей его.

* [Azure машинного обучения SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).  

* [Расширение Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) и [машинного обучения CLI.](reference-azure-machine-learning-cli.md)

* Обученная модель. Модель должна быть упорна в один или несколько файлов в среде разработки.

    > [!NOTE]
    > Чтобы продемонстрировать регистрацию модели, подготовленной за пределами Azure Machine Learning, фрагменты кода в этой статье используют [https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis)модели, созданные проектом анализа настроений Паоло Рипамонти в Twitter: .

## <a name="register-the-models"></a>Регистрация модели (ы)

Регистрация модели позволяет хранить, регистрировать и отслеживать метаданные о моделях в рабочем пространстве. В следующих примерах Python `models` и CLI `model.h5` `model.w2v`каталог `encoder.pkl`содержит `tokenizer.pkl` , и, и файлы. Этот пример загружает `models` файлы, содержащиеся в `sentiment`каталоге, как новая регистрация модели под названием:

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning",
                       workspace = ws)
```

Для получения дополнительной [информации](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none-) см.

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

> [!TIP]
> Вы также можете `tags` `properties` установить объекты добавления и словаря в зарегистрированную модель. Эти значения могут быть использованы позже, чтобы помочь определить конкретную модель. Например, используемые рамки, параметры обучения и т.д.

Для получения дополнительной [информации](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register) см.


Для получения дополнительной информации о регистрации моделей в целом [см.](concept-model-management-and-deployment.md)

## <a name="define-inference-configuration"></a>Определение конфигурации выводов

Конфигурация выводов определяет среду, используемую для выполнения развернутой модели. Конфигурация выводов ссылается на следующие сущности, которые используются для запуска модели при развертывании:

* Скрипт входа. Этот файл `score.py`(названный) загружает модель при запуске развернутой службы. Он также отвечает за получение данных, передачу их в модель, а затем возвращение ответа.
* [Среда](how-to-use-environments.md)машинного обучения Azure . Среда определяет программные зависимости, необходимые для запуска модели и скрипта входа.

Ниже приводится следующий пример, как использовать SDK для создания среды, а затем использовать его с конфигурацией выводов:

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# Create the environment
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Define the packages needed by the model and scripts
conda_dep.add_conda_package("tensorflow")
conda_dep.add_conda_package("numpy")
conda_dep.add_conda_package("scikit-learn")
# You must list azureml-defaults as a pip dependency
conda_dep.add_pip_package("azureml-defaults")
conda_dep.add_pip_package("keras")
conda_dep.add_pip_package("gensim")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep

inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

Дополнительные сведения см. в следующих статьях:

+ [Как использовать среды](how-to-use-environments.md).
+ [Ссылка На выводConfig.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)


CLI загружает конфигурацию выводов из файла YAML:

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

С помощью CLI среда conda `myenv.yml` определяется в файле, на который ссылается конфигурация выводов. Следующим образом: содержимое этого файла:

```yaml
name: inference_environment
dependencies:
- python=3.6.2
- tensorflow
- numpy
- scikit-learn
- pip:
    - azureml-defaults
    - keras
    - gensim
```

Для получения дополнительной информации о [Deploy models with Azure Machine Learning](how-to-deploy-and-where.md)конфигурации выводов см.

### <a name="entry-script"></a>Сценарий входа

Сценарий входа имеет только `init()` две `run(data)`необходимые функции, и . Эти функции используются для инициализации службы при запуске и запуска модели с использованием данных запросов, переданных клиентом. Остальная часть скрипта обрабатывает загрузку и запуск модели(ы).

> [!IMPORTANT]
> Нет общего скрипта входа, который работает для всех моделей. Она всегда специфична для используемой модели. Он должен понимать, как загрузить модель, формат данных, который модель ожидает, и как набрать данные с помощью модели.

Следующий код Python является примером вступления скрипта ( ):`score.py`

```python
import os
import pickle
import json
import time
from keras.models import load_model
from keras.preprocessing.sequence import pad_sequences
from gensim.models.word2vec import Word2Vec

# SENTIMENT
POSITIVE = "POSITIVE"
NEGATIVE = "NEGATIVE"
NEUTRAL = "NEUTRAL"
SENTIMENT_THRESHOLDS = (0.4, 0.7)
SEQUENCE_LENGTH = 300

# Called when the deployed service starts
def init():
    global model
    global tokenizer
    global encoder
    global w2v_model

    # Get the path where the deployed model can be found.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), './models')
    # load models
    model = load_model(model_path + '/model.h5')
    w2v_model = Word2Vec.load(model_path + '/model.w2v')

    with open(model_path + '/tokenizer.pkl','rb') as handle:
        tokenizer = pickle.load(handle)

    with open(model_path + '/encoder.pkl','rb') as handle:
        encoder = pickle.load(handle)

# Handle requests to the service
def run(data):
    try:
        # Pick out the text property of the JSON request.
        # This expects a request in the form of {"text": "some text to score for sentiment"}
        data = json.loads(data)
        prediction = predict(data['text'])
        #Return prediction
        return prediction
    except Exception as e:
        error = str(e)
        return error

# Determine sentiment from score
def decode_sentiment(score, include_neutral=True):
    if include_neutral:
        label = NEUTRAL
        if score <= SENTIMENT_THRESHOLDS[0]:
            label = NEGATIVE
        elif score >= SENTIMENT_THRESHOLDS[1]:
            label = POSITIVE
        return label
    else:
        return NEGATIVE if score < 0.5 else POSITIVE

# Predict sentiment using the model
def predict(text, include_neutral=True):
    start_at = time.time()
    # Tokenize text
    x_test = pad_sequences(tokenizer.texts_to_sequences([text]), maxlen=SEQUENCE_LENGTH)
    # Predict
    score = model.predict([x_test])[0]
    # Decode sentiment
    label = decode_sentiment(score, include_neutral=include_neutral)

    return {"label": label, "score": float(score),
       "elapsed_time": time.time()-start_at}  
```

Для получения дополнительной информации о скриптах входа см. [Модели развертываний с помощью машинного обучения Azure.](how-to-deploy-and-where.md)

## <a name="define-deployment"></a>Определение развертывания

Пакет [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice?view=azure-ml-py) содержит классы, используемые для развертывания. Тип, который используется, определяет, где развертывается модель. Например, для развертывания в качестве веб-службы в службе Azure Kubernetes воспользуйтесь [AksWebService.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-) для создания конфигурации развертывания.

Следующий код Python определяет конфигурацию развертывания для локального развертывания. Эта конфигурация развертывает модель в качестве веб-службы на локальном компьютере.

> [!IMPORTANT]
> Локальное развертывание требует рабочей установки [Docker](https://www.docker.com/) на локальном компьютере:

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

Для получения дополнительной информации, см [LocalWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.localwebservice?view=azure-ml-py#deploy-configuration-port-none-) ссылка.

CLI загружает конфигурацию развертывания из файла YAML:

```YAML
{
    "computeType": "LOCAL"
}
```

Развертывание в другой вычислительной цели, такой как служба Azure Kubernetes в облаке Azure, так же просто, как изменение конфигурации развертывания. Для получения дополнительной [How and where to deploy models](how-to-deploy-and-where.md)информации см.

## <a name="deploy-the-model"></a>Развертывание модели

Следующий пример загружает информацию на зарегистрированную модель имени, `sentiment`а затем развертывает ее как службу с именем. `sentiment` Во время развертывания для создания и настройки среды службы используется конфигурация выводов и конфигурация развертывания:

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

Для получения дополнительной [информации](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) см.

Для развертывания модели из CLI используйте следующую команду. Эта команда развертывает версию 1`sentiment:1`зарегистрированной модели () с использованием конфигурации выводов и развертывания, хранящихся в `inferenceConfig.json` файлах: `deploymentConfig.json`

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

Для получения дополнительной [az ml model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) информации см.

Для получения дополнительной информации о развертывании [см.](how-to-deploy-and-where.md)

## <a name="request-response-consumption"></a>Потребление запроса и ответа

После развертывания отображается скоринг URI. Этот URI может быть использован клиентами для отправки запросов в службу. Следующим примером является основной клиент Python, который отправляет данные в службу и отображает ответ:

```python
import requests
import json

scoring_uri = 'scoring uri for your service'
headers = {'Content-Type':'application/json'}

test_data = json.dumps({'text': 'Today is a great day!'})

response = requests.post(scoring_uri, data=test_data, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

Для получения дополнительной информации о том, как использовать [развернутую](how-to-consume-web-service.md)службу, см.

## <a name="next-steps"></a>Дальнейшие действия

* [Мониторинг моделей машинного обучения Azure с помощью приложений Insights](how-to-enable-app-insights.md)
* [Сбор данных для моделей в рабочей среде](how-to-enable-data-collection.md)
* [Как и где развертывать модели](how-to-deploy-and-where.md)
* [Как создать клиент для развернутой модели](how-to-consume-web-service.md)
