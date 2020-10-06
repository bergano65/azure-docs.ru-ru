---
title: Краткое руководство по использованию Python для вызова API анализа текста
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве показано, как с помощью Python получить информацию и примеры кода для начала работы с API "Анализ текста" в Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/20/2020
ms.author: aahi
ms.custom: devx-track-python
ms.openlocfilehash: 0ef870b6b2d3b88b13c16c8c2acbfcee7ed551c1
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90527247"
---
# <a name="quickstart-using-the-python-rest-api-to-call-the-text-analytics-cognitive-service"></a>Вызов API "Анализ текста" в Cognitive Services с помощью REST API Python 
<a name="HOLTop"></a>

В этом кратком руководстве содержатся сведения об анализе языка с помощью REST API "Анализ текста" и Python. В этой статье содержатся сведения о том, как [распознавать язык](#Detect), [анализировать тональность](#SentimentAnalysis), [извлекать ключевые фразы](#KeyPhraseExtraction) и [идентифицировать связанные сущности](#Entities).

[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

## <a name="prerequisites"></a>Предварительные требования

* [Python 3.x](https://python.org)

* Библиотека запросов Python
    
    Вы можете установить библиотеку с помощью следующей команды:

    ```console
    pip install --upgrade requests
    ```

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]


## <a name="create-a-new-python-application"></a>Создание приложения Python

Создайте приложения Python в любом удобном редакторе или интегрированной среде разработки. Добавьте приведенные ниже операторы import в файл.

```python
import requests
# pprint is used to format the JSON response
from pprint import pprint
```

Создайте переменные для конечной точки ресурса Azure и ключа подписки.
    
```python
import os

subscription_key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```

В приведенных ниже разделах описано, как вызвать каждую функцию API.

<a name="Detect"></a>

## <a name="detect-languages"></a>Распознавание языков

Добавьте объект `/text/analytics/v3.0/languages` к базовой конечной точке API "Анализ текста", чтобы создать URL-адрес распознавания языка. Например: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/languages`
    
```python
language_api_url = endpoint + "/text/analytics/v3.0/languages"
```

Полезные данные этого API состоят из списка ключей `documents` —кортежей, которые содержат параметр `id` и атрибут `text`. Атрибут `text` содержит текст для анализа, а параметр `id` может иметь любое значение. 

```python
documents = {"documents": [
    {"id": "1", "text": "This is a document written in English."},
    {"id": "2", "text": "Este es un document escrito en Español."},
    {"id": "3", "text": "这是一个用中文写的文件"}
]}
```

С помощью библиотеки запросов отправьте документы в API. Добавьте ключ подписки к заголовку `Ocp-Apim-Subscription-Key` и отправьте запрос с помощью команды `requests.post()`. 

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(language_api_url, headers=headers, json=documents)
languages = response.json()
pprint(languages)
```

### <a name="output"></a>Выходные данные

```json
{
    "documents": [
        {
            "id": "1",
            "detectedLanguage": {
                "name": "English",
                "iso6391Name": "en",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "2",
            "detectedLanguage": {
                "name": "Spanish",
                "iso6391Name": "es",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "3",
            "detectedLanguage": {
                "name": "Chinese_Simplified",
                "iso6391Name": "zh_chs",
                "confidenceScore": 1.0
            },
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Анализ тональности

Чтобы определить тональность (положительные или отрицательные эмоции) набора документов, добавьте объект `/text/analytics/v3.0/sentiment` к базовой конечной точке API "Анализ текста". Это позволит создать URL-адрес распознавания языка. Например: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment`
    
```python
sentiment_url = endpoint + "/text/analytics/v3.0/sentiment"
```

Как и в примере распознавания языка, создайте словарь с ключом `documents`, который содержит список документов. Каждый документ представляет собой кортеж, состоящий из `id`, `text` для анализа и `language` для текста. 

```python
documents = {"documents": [
    {"id": "1", "language": "en",
        "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
    {"id": "2", "language": "es",
        "text": "Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico."}
]}
```

С помощью библиотеки запросов отправьте документы в API. Добавьте ключ подписки к заголовку `Ocp-Apim-Subscription-Key` и отправьте запрос с помощью команды `requests.post()`. 

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(sentiment_url, headers=headers, json=documents)
sentiments = response.json()
pprint(sentiments)
```

### <a name="output"></a>Выходные данные

Оценка тональности для документа имеет значение в диапазоне от 0.0 до 1.0, где большее число обозначает более положительную тональность.

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 102,
                    "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."
                }
            ],
            "warnings": []
        },
        {
            "id": "2",
            "sentiment": "negative",
            "confidenceScores": {
                "positive": 0.02,
                "neutral": 0.05,
                "negative": 0.93
            },
            "sentences": [
                {
                    "sentiment": "negative",
                    "confidenceScores": {
                        "positive": 0.02,
                        "neutral": 0.05,
                        "negative": 0.93
                    },
                    "offset": 0,
                    "length": 92,
                    "text": "Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico."
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Извлечение ключевых фраз
 
Чтобы извлечь ключевые фразы из набора документов, добавьте объект `/text/analytics/v3.0/keyPhrases` к базовой конечной точке API "Анализ текста". Это позволит создать URL-адрес распознавания языка. Например: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`
    
```python
keyphrase_url = endpoint + "/text/analytics/v3.0/keyphrases"
```

Здесь используется та же коллекция документов, что и в примере анализа тональности.

```python
documents = {"documents": [
    {"id": "1", "language": "en",
        "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
    {"id": "2", "language": "es",
        "text": "Si usted quiere comunicarse con Carlos, usted debe de llamarlo a su telefono movil. Carlos es muy responsable, pero necesita recibir una notificacion si hay algun problema."},
    {"id": "3", "language": "en",
        "text": "The Grand Hotel is a new hotel in the center of Seattle. It earned 5 stars in my review, and has the classiest decor I've ever seen."}
]}
```

С помощью библиотеки запросов отправьте документы в API. Добавьте ключ подписки к заголовку `Ocp-Apim-Subscription-Key` и отправьте запрос с помощью команды `requests.post()`. 

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(keyphrase_url, headers=headers, json=documents)
key_phrases = response.json()
pprint(key_phrases)
```

### <a name="output"></a>Выходные данные

```json
{
    "documents": [
        {
            "id": "1",
            "keyPhrases": [
                "HDR resolution",
                "new XBox",
                "clean look"
            ],
            "warnings": []
        },
        {
            "id": "2",
            "keyPhrases": [
                "Carlos",
                "notificacion",
                "algun problema",
                "telefono movil"
            ],
            "warnings": []
        },
        {
            "id": "3",
            "keyPhrases": [
                "new hotel",
                "Grand Hotel",
                "review",
                "center of Seattle",
                "classiest decor",
                "stars"
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

<a name="Entities"></a>

## <a name="identify-entities"></a>Определение сущностей

Чтобы определить известные сущности (люди, места, предметы) в текстовых документах, добавьте объект `/text/analytics/v3.0/entities/recognition/general` к базовой конечной точке API "Анализ текста". Это позволит создать URL-адрес распознавания языка. Например: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`
    
```python
entities_url = endpoint + "/text/analytics/v3.0/entities/recognition/general"
```

Создайте коллекцию документов, как и в предыдущих примерах. 

```python
documents = {"documents": [
    {"id": "1", "text": "Microsoft is an It company."}
]}
```

С помощью библиотеки запросов отправьте документы в API. Добавьте ключ подписки к заголовку `Ocp-Apim-Subscription-Key` и отправьте запрос с помощью команды `requests.post()`.

```python
headers = {"Ocp-Apim-Subscription-Key": subscription_key}
response = requests.post(entities_url, headers=headers, json=documents)
entities = response.json()
pprint(entities)
```

### <a name="output"></a>Выходные данные

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "text": "Microsoft",
                    "category": "Organization",
                    "offset": 0,
                    "length": 9,
                    "confidenceScore": 0.86
                },
                {
                    "text": "IT",
                    "category": "Skill",
                    "offset": 16,
                    "length": 2,
                    "confidenceScore": 0.8
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Text Analytics with Power BI](../tutorials/tutorial-power-bi-key-phrases.md) (Анализ текста с использованием Power BI)

## <a name="see-also"></a>См. также раздел 

 [Text Analytics overview](../overview.md) (Общие сведения об анализе текста)  
 [Часто задаваемые вопросы](../text-analytics-resource-faq.md)
