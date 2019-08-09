---
title: Использование и развертывание существующих моделей
titleSuffix: Azure Machine Learning service
description: Узнайте, как можно использовать службу Машинное обучение Azure с моделями, которые прошли обучение за пределами службы. Можно зарегистрировать модели, созданные вне Машинное обучение Azure службы, а затем развернуть их в виде веб-службы или Azure IoT Edge модуля.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/19/2019
ms.openlocfilehash: cbbfd5f7beb7270bf55e952c818b4802d9d9ecab
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68847988"
---
# <a name="use-an-existing-model-with-azure-machine-learning-service"></a>Использование существующей модели со службой Машинное обучение Azure

Узнайте, как использовать существующую модель машинного обучения со службой Машинное обучение Azure.

Если у вас есть модель машинного обучения, обученная за пределами службы Машинное обучение Azure, вы по-прежнему можете использовать эту службу для развертывания модели в качестве веб-службы или устройства IoT Edge. 

> [!TIP]
> В этой статье приводятся основные сведения о регистрации и развертывании существующей модели. После развертывания Машинное обучение Azure служба обеспечивает мониторинг модели. Он также позволяет хранить входные данные, отправленные в развертывание, которые можно использовать для анализа смещения данных или обучения новых версий модели.
>
> Дополнительные сведения об основных понятиях и терминах, используемых здесь, см. в разделе [Управление моделями машинного обучения, их развертывание и мониторинг](concept-model-management-and-deployment.md).
>
> Общие сведения о процессе развертывания см. в разделе [Развертывание моделей с помощью службы машинное обучение Azure](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>предварительные требования

* Рабочая область службы машинного обучения Azure. Дополнительные сведения см. [в разделе Создание рабочей области](how-to-manage-workspace.md).

    > [!TIP]
    > В примерах Python в этой статье предполагается `ws` , что для переменной задана рабочая область машинное обучение Azure службы.
    >
    > В примерах интерфейса командной строки используется `myworkspace` заполнитель и `myresourcegroup`. Замените их именем рабочей области и группой ресурсов, содержащей ее.

* [Пакет SDK для машинное обучение Azure](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).  

* [Расширение CLI](reference-azure-machine-learning-cli.md) [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) и машинное обучение.

* Обученная модель. Модель должна быть сохранена в одном или нескольких файлах в среде разработки.

    > [!NOTE]
    > Чтобы продемонстрировать регистрацию модели, обученной за пределами Машинное обучение Azure службы, в примерах фрагментов кода в этой статье используются модели, созданные проектом [https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis)Паулу Рипамонти в Twitter тональности Analysis.

## <a name="register-the-models"></a>Регистрация моделей

Регистрация модели позволяет хранить, отменять и записывать метаданные о моделях в рабочей области. В следующих `models` примерах Python и CLI каталог `model.w2v` `model.h5`содержит файлы, `encoder.pkl`, и `tokenizer.pkl` . В этом примере перегружаются файлы, содержащиеся `models` в каталоге, в качестве новой регистрации `sentiment`модели с именем:

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning service",
                       workspace = ws)
```

Дополнительные сведения см. в справочнике по [model. Register ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none-) .

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

Дополнительные сведения см. в справочнике по [регистру AZ ML Model](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register) .


Дополнительные сведения о регистрации модели в целом см. в разделе [Управление моделями машинного обучения, их развертывание и мониторинг](concept-model-management-and-deployment.md).


## <a name="define-inference-configuration"></a>Определение конфигурации вывода

Конфигурация вывода определяет среду, используемую для запуска развернутой модели. Конфигурация вывода ссылается на следующие файлы, которые используются для запуска модели при ее развертывании:

* Среда выполнения. Единственным допустимым значением для выполнения в настоящее время является Python.
* Скрипт записи. Этот файл (с `score.py`именем) загружает модель при запуске развернутой службы. Он также отвечает за получение данных, передачу их в модель и возврат ответа.
* Файл среды conda. Этот файл определяет пакеты Python, необходимые для запуска скрипта модели и входа. 

В следующем примере показана базовая конфигурация вывода с помощью пакета SDK для Python:

```python
from azureml.core.model import InferenceConfig

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml")
```

Дополнительные сведения см. в справочнике по [инференцеконфиг](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) .

CLI загружает конфигурацию вывода из файла YAML:

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

Дополнительные сведения о конфигурации вывода см. в разделе [deploy Models with машинное обучение Azure Service](how-to-deploy-and-where.md).

### <a name="entry-script"></a>Сценарий записи

Сценарий записи содержит только две обязательные функции `init()` и. `run(data)` Эти функции используются для инициализации службы при запуске и запуска модели с использованием данных запроса, передаваемых клиентом. Оставшаяся часть скрипта обрабатывает загрузку и запуск моделей.

> [!IMPORTANT]
> Нет универсального скрипта записи, который работает для всех моделей. Он всегда зависит от используемой модели. Он должен иметь представление о загрузке модели, формате данных, предполагаемом для модели, и способе оценки данных с помощью модели.

Следующий код Python — пример скрипта записи (`score.py`):

```python
import pickle
import json
import time
from keras.models import load_model
from keras.preprocessing.sequence import pad_sequences
from gensim.models.word2vec import Word2Vec
from azureml.core.model import Model

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

    # Get the path where the model(s) registered as the name 'sentiment' can be found.
    model_path = Model.get_model_path('sentiment')
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

Дополнительные сведения о сценариях ввода см. в разделе [Развертывание моделей с помощью службы машинное обучение Azure](how-to-deploy-and-where.md).

### <a name="conda-environment"></a>Среда Conda

В следующем YAML описывается среда conda, необходимая для запуска скрипта модели и записи.

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
```

Дополнительные сведения см. в разделе [Развертывание моделей с помощью службы машинное обучение Azure](how-to-deploy-and-where.md).

## <a name="define-deployment"></a>Определение развертывания

Пакет [WebService](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice?view=azure-ml-py) содержит классы, используемые для развертывания. Используемый класс определяет, где развертывается модель. Например, для развертывания в качестве веб-службы в службе Kubernetes Azure используйте [аксвебсервице. deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none-) , чтобы создать конфигурацию развертывания.

В следующем коде Python определяется конфигурация развертывания для локального развертывания. Эта конфигурация развертывает модель как веб-службу на локальном компьютере.

> [!IMPORTANT]
> Для локального развертывания требуется Рабочая установка [DOCKER](https://www.docker.com/) на локальном компьютере:

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

Дополнительные сведения см. в справочнике по [локалвебсервице. deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.localwebservice?view=azure-ml-py#deploy-configuration-port-none-) .

CLI загружает конфигурацию развертывания из файла YAML:

```YAML
{
    "computeType": "LOCAL"
}
```

Развертывание в другом целевом объекте вычислений, например в службе Azure Kubernetes в облаке Azure, так же просто, как и изменение конфигурации развертывания. Дополнительные сведения см. [в разделе как и где развертываются модели](how-to-deploy-and-where.md).

## <a name="deploy-the-model"></a>Развертывание модели

Следующий пример загружает сведения о зарегистрированной модели с `sentiment`именем, а затем развертывает ее как службу с именем `sentiment`. Во время развертывания для создания и настройки среды службы используются конфигурация определения и конфигурация развертывания.

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

Дополнительные сведения см. в справочнике по [модели. deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-) .

Чтобы развернуть модель из интерфейса командной строки, используйте следующую команду. Эта команда развертывает версию 1 зарегистрированной модели (`sentiment:1`) с помощью вывода и конфигурации развертывания, хранящейся `inferenceConfig.json` в файлах и `deploymentConfig.json` .

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

Дополнительные сведения см. в справочнике по [развертыванию модели языка AZ ML](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) .

Дополнительные сведения о развертывании см. в статье [как и где развертывать модели](how-to-deploy-and-where.md).

## <a name="request-response-consumption"></a>Использование запросов и ответов

После развертывания отображается URI оценки. Этот универсальный код ресурса (URI) может использоваться клиентами для отправки запросов в службу. Ниже приведен пример базового клиента Python, который отправляет данные в службу и отображает ответ:

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

Дополнительные сведения о том, как использовать развернутую службу, см. в разделе [Создание клиента](how-to-consume-web-service.md).

## <a name="next-steps"></a>Следующие шаги

* [Мониторинг моделей машинного обучения в Azure с помощью Application Insights](how-to-enable-app-insights.md)
* [Сбор данных для моделей в рабочей среде](how-to-enable-data-collection.md)
* [Как и где развертываются модели](how-to-deploy-and-where.md)
* [Создание клиента для развернутой модели](how-to-consume-web-service.md)
