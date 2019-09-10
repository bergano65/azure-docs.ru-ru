---
title: Краткое руководство. Использование Python для вызова API анализа текста
titleSuffix: Azure Cognitive Services
description: Получайте информацию и примеры кода, которые помогут вам приступить к работе с API Анализа текста в Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: aahi
ms.openlocfilehash: 669cd43b73bc66289a355f7fbf9c4498d8a7b99a
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70135025"
---
# <a name="quickstart-using-the-python-rest-api-to-call-the-text-analytics-cognitive-service"></a>Краткое руководство. Вызов API "Анализ текста" в Cognitive Services с помощью REST API Python 
<a name="HOLTop"></a>

В этом кратком руководстве содержатся сведения об анализе языка с помощью REST API "Анализ текста" и Python. В этой статье содержатся сведения о том, как [распознавать язык](#Detect), [анализировать тональность](#SentimentAnalysis), [извлекать ключевые фразы](#KeyPhraseExtraction) и [идентифицировать связанные сущности](#Entities).

Техническую документацию по API-интерфейсам см. в разделе [API definitions](//go.microsoft.com/fwlink/?LinkID=759346) (Определения API).

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

Создайте переменные для конечной точки ресурса Azure и ключа подписки. Получите эти значения из переменных среды TEXT_ANALYTICS_SUBSCRIPTION_KEY и TEXT_ANALYTICS_ENDPOINT. Если вы создали переменные среды после начала правки приложения, следует закрыть и повторно открыть редактор, интегрированную среду разработки или оболочку, которые использовались для доступа к этим переменным.
    
```python
import os

key_var_name = 'TEXT_ANALYTICS_SUBSCRIPTION_KEY'
if not key_var_name in os.environ:
    raise Exception('Please set/export the environment variable: {}'.format(key_var_name))
subscription_key = os.environ[key_var_name]

endpoint_var_name = 'TEXT_ANALYTICS_ENDPOINT'
if not endpoint_var_name in os.environ:
    raise Exception('Please set/export the environment variable: {}'.format(endpoint_var_name))
endpoint = os.environ[endpoint_var_name]
```

В приведенных ниже разделах описано, как вызвать каждую функцию API.

<a name="Detect"></a>

## <a name="detect-languages"></a>Распознавание языков

Добавьте объект `languages` к базовой конечной точке API "Анализ текста", чтобы создать URL-адрес распознавания языка. Например: `https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/languages`
    
```python
language_api_url = endpoint + "/text/analytics/v2.1/languages"
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
"documents":[
    {
        "detectedLanguages":[
        {
            "iso6391Name":"en",
            "name":"English",
            "score":1.0
        }
        ],
        "id":"1"
    },
    {
        "detectedLanguages":[
        {
            "iso6391Name":"es",
            "name":"Spanish",
            "score":1.0
        }
        ],
        "id":"2"
    },
    {
        "detectedLanguages":[
        {
            "iso6391Name":"zh_chs",
            "name":"Chinese_Simplified",
            "score":1.0
        }
        ],
        "id":"3"
    }
],
"errors":[]
}
```

<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Анализ тональности

Чтобы определить тональность (положительные или отрицательные эмоции) набора документов, добавьте объект `sentiment` к базовой конечной точке API "Анализ текста". Это позволит создать URL-адрес распознавания языка. Например: `https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/sentiment`
    
```python
sentiment_url = endpoint + "/text/analytics/v2.1/sentiment"
```

Как и в примере распознавания языка, создайте словарь с ключом `documents`, который содержит список документов. Каждый документ представляет собой кортеж, состоящий из `id`, `text` для анализа и `language` для текста. 

```python
documents = {"documents": [
    {"id": "1", "language": "en",
        "text": "I had a wonderful experience! The rooms were wonderful and the staff was helpful."},
    {"id": "2", "language": "en",
        "text": "I had a terrible time at the hotel. The staff was rude and the food was awful."},
    {"id": "3", "language": "es",
        "text": "Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos."},
    {"id": "4", "language": "es",
     "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."}
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
  "documents":[
    {
      "id":"1",
      "score":0.9708490371704102
    },
    {
      "id":"2",
      "score":0.0019068121910095215
    },
    {
      "id":"3",
      "score":0.7456425428390503
    },
    {
      "id":"4",
      "score":0.334433376789093
    }
  ],
  "errors":[]
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Извлечение ключевых фраз
 
Чтобы извлечь ключевые фразы из набора документов, добавьте объект `keyPhrases` к базовой конечной точке API "Анализ текста". Это позволит создать URL-адрес распознавания языка. Например: `https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases`
    
```python
keyphrase_url = endpoint + "/text/analytics/v2.1/keyphrases"
```

Здесь используется та же коллекция документов, что и в примере анализа тональности.

```python
documents = {"documents": [
    {"id": "1", "language": "en",
        "text": "I had a wonderful experience! The rooms were wonderful and the staff was helpful."},
    {"id": "2", "language": "en",
        "text": "I had a terrible time at the hotel. The staff was rude and the food was awful."},
    {"id": "3", "language": "es",
        "text": "Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos."},
    {"id": "4", "language": "es",
     "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."}
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
  "documents":[
    {
      "keyPhrases":[
        "wonderful experience",
        "staff",
        "rooms"
      ],
      "id":"1"
    },
    {
      "keyPhrases":[
        "food",
        "terrible time",
        "hotel",
        "staff"
      ],
      "id":"2"
    },
    {
      "keyPhrases":[
        "Monte Rainier",
        "caminos"
      ],
      "id":"3"
    },
    {
      "keyPhrases":[
        "carretera",
        "tráfico",
        "día"
      ],
      "id":"4"
    }
  ],
  "errors":[]
}
```

<a name="Entities"></a>

## <a name="identify-entities"></a>Определение сущностей

Чтобы определить известные сущности (люди, места, предметы) в текстовых документах, добавьте объект `entities` к базовой конечной точке API "Анализ текста". Это позволит создать URL-адрес распознавания языка. Например: `https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/entities`
    
```python
entities_url = endpoint + "/text/analytics/v2.1/entities"
```

Создайте коллекцию документов, как и в предыдущих примерах. 

```python
documents = {"documents": [
    {"id": "1", "text": "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."}
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
   "documents" : [
      {
         "id" : "1",
         "entities" : [
            {
               "name" : "Microsoft",
               "matches" : [
                  {
                     "wikipediaScore" : 0.49897989655674446,
                     "entityTypeScore" : 1.0,
                     "text" : "Microsoft",
                     "offset" : 0,
                     "length" : 9
                  }
               ],
               "wikipediaLanguage" : "en",
               "wikipediaId" : "Microsoft",
               "wikipediaUrl" : "https://en.wikipedia.org/wiki/Microsoft",
               "bingId" : "a093e9b9-90f5-a3d5-c4b8-5855e1b01f85",
               "type" : "Organization"
            },
            {
               "name" : "Bill Gates",
               "matches" : [
                  {
                     "wikipediaScore" : 0.58357497243368983,
                     "entityTypeScore" : 0.999847412109375,
                     "text" : "Bill Gates",
                     "offset" : 25,
                     "length" : 10
                  }
               ],
               "wikipediaLanguage" : "en",
               "wikipediaId" : "Bill Gates",
               "wikipediaUrl" : "https://en.wikipedia.org/wiki/Bill_Gates",
               "bingId" : "0d47c987-0042-5576-15e8-97af601614fa",
               "type" : "Person"
            },
            {
               "name" : "Paul Allen",
               "matches" : [
                  {
                     "wikipediaScore" : 0.52977533244176866,
                     "entityTypeScore" : 0.99884098768234253,
                     "text" : "Paul Allen",
                     "offset" : 40,
                     "length" : 10
                  }
               ],
               "wikipediaLanguage" : "en",
               "wikipediaId" : "Paul Allen",
               "wikipediaUrl" : "https://en.wikipedia.org/wiki/Paul_Allen",
               "bingId" : "df2c4376-9923-6a54-893f-2ee5a5badbc7",
               "type" : "Person"
            },
            {
               "name" : "April 4",
               "matches" : [
                  {
                     "wikipediaScore" : 0.37220990924571939,
                     "entityTypeScore" : 0.8,
                     "text" : "April 4",
                     "offset" : 54,
                     "length" : 7
                  }
               ],
               "wikipediaLanguage" : "en",
               "wikipediaId" : "April 4",
               "wikipediaUrl" : "https://en.wikipedia.org/wiki/April_4",
               "bingId" : "52535f87-235e-b513-54fe-c03e4233ac6e",
               "type" : "Other"
            },
            {
               "name" : "April 4, 1975",
               "matches" : [
                  {
                     "entityTypeScore" : 0.8,
                     "text" : "April 4, 1975",
                     "offset" : 54,
                     "length" : 13
                  }
               ],
               "type" : "DateTime",
               "subType" : "Date"
            },
            {
               "name" : "BASIC",
               "matches" : [
                  {
                     "wikipediaScore" : 0.35686239324548041,
                     "entityTypeScore" : 0.8,
                     "text" : "BASIC",
                     "offset" : 89,
                     "length" : 5
                  }
               ],
               "wikipediaLanguage" : "en",
               "wikipediaId" : "BASIC",
               "wikipediaUrl" : "https://en.wikipedia.org/wiki/BASIC",
               "bingId" : "5b16443d-501c-58f3-352e-611bbe75aa6e",
               "type" : "Other"
            },
            {
               "name" : "Altair 8800",
               "matches" : [
                  {
                     "wikipediaScore" : 0.868324676465041,
                     "entityTypeScore" : 0.8,
                     "text" : "Altair 8800",
                     "offset" : 116,
                     "length" : 11
                  }
               ],
               "wikipediaLanguage" : "en",
               "wikipediaId" : "Altair 8800",
               "wikipediaUrl" : "https://en.wikipedia.org/wiki/Altair_8800",
               "bingId" : "7216c654-3779-68a2-c7b7-12ff3dad5606",
               "type" : "Other"
            },
            {
               "name" : "Altair",
               "matches" : [
                  {
                     "entityTypeScore" : 0.52505272626876831,
                     "text" : "Altair",
                     "offset" : 116,
                     "length" : 6
                  }
               ],
               "type" : "Organization"
            },
            {
               "name" : "8800",
               "matches" : [
                  {
                     "entityTypeScore" : 0.8,
                     "text" : "8800",
                     "offset" : 123,
                     "length" : 4
                  }
               ],
               "type" : "Quantity",
               "subType" : "Number"
            }
         ]
      }
   ],
   "errors" : []
}
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Text Analytics with Power BI](../tutorials/tutorial-power-bi-key-phrases.md) (Анализ текста с использованием Power BI)

## <a name="see-also"></a>См. также 

 [Text Analytics overview](../overview.md) (Общие сведения об анализе текста)  
 [Часто задаваемые вопросы](../text-analytics-resource-faq.md)
