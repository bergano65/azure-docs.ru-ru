---
title: Как использовать существующую модель
titleSuffix: Azure Machine Learning service
description: Узнайте, как можно использовать службы машинного обучения Azure с моделями, которые прошли обучение за пределами службы. Можно зарегистрировать моделей, созданных за пределами службы машинного обучения Azure и затем развернуть их как веб-службы или модуля Azure IoT Edge.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/19/2019
ms.openlocfilehash: 332129c9847c317369d5631c3af584da9430e9dd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453688"
---
# <a name="how-to-use-an-existing-model-with-azure-machine-learning-service"></a>Как использовать существующую модель со службой машинного обучения Azure

Узнайте, как использовать существующие модели машинного обучения в службе машинного обучения Azure.

Если у вас есть машинного обучения модели, которая была обучена за пределами службы машинного обучения Azure, можно по-прежнему использовать службу развертывание модели как веб-службу или на устройстве IoT Edge. 

> [!TIP]
> В этой статье приведены базовые сведения о регистрации и развертывания существующей модели. После развертывания службы машинного обучения Azure обеспечивает мониторинг для модели. Он также позволяет сохранять входные данные, отправляемые развертывания, который может использоваться для данных о смещении анализа или обучение новых версий модели.
>
> Дополнительные сведения о концепции и термины, используемые здесь, см. в разделе [управление, развертывание и отслеживание моделей машинного обучения](concept-model-management-and-deployment.md).
>
> Общие сведения о процессе развертывания см. в разделе [развертывание моделей с помощью службы машинного обучения Azure](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Технические условия

* Рабочая область службы машинного обучения Azure. Дополнительные сведения см. в разделе [создать рабочую область](setup-create-workspace.md).

    > [!TIP]
    > Примеры Python в этой статье предполагается, что `ws` переменной присваивается рабочая область службы машинного обучения Azure.
    >
    > В примерах интерфейса командной строки используется заполнитель из `myworkspace` и `myresourcegroup`. Замените их имя рабочей области и содержащую его группу ресурсов.

* Машинного обучения Azure SDK. Дополнительные сведения см. в разделе пакет SDK для Python [создать рабочую область](setup-create-workspace.md#sdk).

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) и [Machine Learning CLI расширение](reference-azure-machine-learning-cli.md).

* Обученная модель. Модель необходимо сохранить один или несколько файлов в среде разработки.

    > [!NOTE]
    > Чтобы продемонстрировать, регистрации модели обучения за пределами службы машинного обучения Azure, фрагменты кода пример в этой статье используйте модели, созданные проектом анализ мнений пользователей Twitter Паоло Ripamonti: [ https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis ](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis).

## <a name="register-the-models"></a>Регистрация в модели

Регистрация модели позволяет сохранять, версии и отслеживать метаданные о модели в рабочей области. В следующих примерах Python и интерфейса командной строки `models` каталог содержит `model.h5`, `model.w2v`, `encoder.pkl`, и `tokenizer.pkl` файлы. В этом примере передает файлы, содержащиеся в `models` каталог как новая модель с именем `sentiment`:

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning service",
                       workspace = ws)
```

Дополнительные сведения см. в разделе [Model.register()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none-) ссылки.

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

Дополнительные сведения см. в разделе [az ml модели register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register) ссылки.


Дополнительные сведения о регистрации модели в целом, см. в разделе [управление, развертывание и отслеживание моделей машинного обучения](concept-model-management-and-deployment.md).


## <a name="define-inference-configuration"></a>Определение конфигурации вывод

Определение конфигурации определяет среду, используемую для запуска развернутой модели. Определение конфигурации ссылается на следующие файлы, которые используются для запуска модели, при развертывании:

* Среда выполнения. В настоящее время единственным допустимым значением для среды выполнения является Python.
* Сценарий входа. Этот файл (с именем `score.py`) загружает модель, при запуске развернутой службы. Он также отвечает за получение данных, передав его в модель и последующего возврата ответа.
* Файл среды conda. Этот файл определяет пакеты Python, необходимые для запуска скрипта модели и запись. 

В следующем примере показано основные определения конфигурации, с помощью пакета SDK для Python:

```python
from azureml.core.model import InferenceConfig

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml")
```

Дополнительные сведения см. в разделе [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) ссылки.

Интерфейс командной строки загружает определение конфигурации из файла YAML:

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

Дополнительные сведения о конфигурации вывод, см. в разделе [развертывание моделей с помощью службы машинного обучения Azure](how-to-deploy-and-where.md).

### <a name="entry-script"></a>Сценарий входа

Сценарий входа имеет только два требуемые функции `init()` и `run(data)`. Эти функции используются для инициализации службы во время запуска и выполнения модели, используя данные запроса, передаваемые в клиент. Остальная часть сценария обрабатывает Загрузка и запуск в модели.

> [!IMPORTANT]
> Не существует универсального входа сценарий, который работает для всех моделей. Всегда относится только к модели, которая используется. Необходимо понять, как загрузить модель, формат данных, чтобы модель ожидала поступления и как для оценки с помощью модели данных.

В следующем коде Python представлен пример сценария входа (`score.py`):

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

Дополнительные сведения о сценарии входа, см. в разделе [развертывание моделей с помощью службы машинного обучения Azure](how-to-deploy-and-where.md).

### <a name="conda-environment"></a>Среда Conda

Следующий yaml-ФАЙЛ описывает среду conda, необходимые для запуска скрипта модели и запись:

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

Дополнительные сведения см. в разделе [развертывание моделей с помощью службы машинного обучения Azure](how-to-deploy-and-where.md).

## <a name="define-deployment"></a>Определение развертывания

[Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice?view=azure-ml-py) пакет содержит классы, используемые для развертывания. Класс, который используется определяет, где развернута модель. Например, для развертывания веб-службы в службе Azure Kubernetes, используется [AksWebService.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none-) для создания конфигурации развертывания.

В следующем коде Python определяется конфигурация развертывания для локального развертывания. Эта конфигурация развертывает модель как веб-службы на локальном компьютере.

> [!IMPORTANT]
> Локальное развертывание требует рабочую установку [Docker](https://www.docker.com/) на локальном компьютере:

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

Дополнительные сведения см. в разделе [LocalWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.localwebservice?view=azure-ml-py#deploy-configuration-port-none-) ссылки.

Интерфейс командной строки загружает конфигурацию развертывания из файла YAML:

```YAML
{
    "computeType": "LOCAL"
}
```

Развертывание на разных объектах вычислений, например в службе Azure Kubernetes в облаке Azure, так же просто, как изменение конфигурации развертывания. Дополнительные сведения см. в разделе [способ и место развертывания моделей](how-to-deploy-and-where.md).

## <a name="deploy-the-model"></a>Развертывание модели

В следующем примере загружаются сведения о зарегистрированных модели с именем `sentiment`, а затем развернет его как служба, имеющая `sentiment`. Во время развертывания определение конфигурации и конфигурация развертывания используются для создания и настройки среды службы:

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

Дополнительные сведения см. в разделе [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-) ссылки.

Развертывание модели с помощью CLI, используйте следующую команду. Эта команда выполняет развертывание первой версии зарегистрированной модели (`sentiment:1`) с помощью определения и развертывания конфигурации, хранимой в `inferenceConfig.json` и `deploymentConfig.json` файлы:

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

Дополнительные сведения см. в разделе [развертывание модели машинного обучения az](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) ссылки.

Дополнительные сведения о развертывании см. в разделе [способ и место развертывания моделей](how-to-deploy-and-where.md).

## <a name="request-response-consumption"></a>Запрос ответ потребления

После развертывания отображается оценки URI. Этот URI может использоваться клиентами для отправки запросов к службе. Ниже приведен базовый клиент Python, который отправляет данные в службу и выводит ответ:

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

Дополнительные сведения о том, как использовать службу, развернутую см. в разделе [создать клиент](how-to-consume-web-service.md).

## <a name="next-steps"></a>Дальнейшие действия

* [Мониторинг моделей машинного обучения в Azure с помощью Application Insights](how-to-enable-app-insights.md)
* [Сбор данных для моделей в рабочей среде](how-to-enable-data-collection.md)
* [Как и способа развертывания модели](how-to-deploy-and-where.md)
* [Как создать клиент для развернутой модели](how-to-consume-web-service.md)