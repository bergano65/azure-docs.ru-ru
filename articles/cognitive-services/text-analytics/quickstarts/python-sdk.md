---
title: Краткое руководство. Вызов службы "Анализ текста" с помощью пакета SDK Python
titleSuffix: Azure Cognitive Services
description: Получайте информацию и примеры кода, которые помогут вам приступить к работе с API Анализа текста в Azure Cognitive Services.
services: cognitive-services
author: ctufts
manager: assafi
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 03/28/2019
ms.author: aahi
ms.openlocfilehash: 2e9e26243511972c7d05dc55c8e041d83efcbc7d
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546604"
---
# <a name="quickstart-call-the-text-analytics-service-using-the-python-sdk"></a>Краткое руководство. Вызов службы "Анализ текста" с помощью пакета SDK Python 
<a name="HOLTop"></a>

В этом кратком руководстве содержатся сведения об анализе языка с помощью пакета SDK Анализа текста для Python. Так как REST API Анализа текста совместим с большинством языков программирования, пакет SDK обеспечивает простой способ интеграции службы в ваши приложения без сериализации и десериализации JSON.


> [!Note]
> * Техническую документацию по API-интерфейсам см. в разделе [API definitions](//go.microsoft.com/fwlink/?LinkID=759346) (Определения API).
> * Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/language/text_analytics_samples.py).  

## <a name="prerequisites"></a>Предварительные требования

* [Python 3.x](https://www.python.org/)

* Служба "Анализ текста", [пакет SDK для Python](https://pypi.org/project/azure-cognitiveservices-language-textanalytics/). Пакет можно установить с помощью следующей строки:

    `pip install --upgrade azure-cognitiveservices-language-textanalytics`

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Также необходимо иметь [конечную точку и ключ доступа](../How-tos/text-analytics-how-to-access-key.md), созданные автоматически во время регистрации.

## <a name="create-a-new-python-application"></a>Создание приложения Python

Создайте приложения Python в любом удобном редакторе или интегрированной среде разработки. Затем добавьте следующие инструкции импорта в файл.

```python
from azure.cognitiveservices.language.textanalytics import TextAnalyticsClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="authenticate-your-credentials"></a>Аутентификация учетных данных

> [!Tip]
> Для безопасного развертывания секретов в рабочих системах рекомендуем использовать [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net).
>

После создания переменной для ключа подписки Анализа текста создайте экземпляр объекта `CognitiveServicesCredentials` с использованием этой переменной.

```python
subscription_key = "enter-your-key-here"
credentials = CognitiveServicesCredentials(subscription_key)
```

## <a name="create-a-text-analytics-client"></a>Создание клиента Анализа текста

Создайте новый объект `TextAnalyticsClient`, используя `credentials` и `text_analytics_url` в качестве параметров. Укажите правильный регион Azure для своей подписки на службу "Анализа текста" (например, `westcentralus`).

```
text_analytics_url = "https://westcentralus.api.cognitive.microsoft.com/"
text_analytics = TextAnalyticsClient(endpoint=text_analytics_url, credentials=credentials)
```

## <a name="sentiment-analysis"></a>Анализ мнений

Полезные данные этого API состоят из списка ключей `documents` — словарей, которые содержат параметр `id` и атрибут `text`. Атрибут `text` содержит текст для анализа, а параметр `id` может иметь любое значение. 

```python
documents = [
  {
    "id": "1", 
    "language": "en", 
    "text": "I had the best day of my life."
  },
  {
    "id": "2", 
    "language": "en", 
    "text": "This was a waste of my time. The speaker put me to sleep."
  },  
  {
    "id": "3", 
    "language": "es", 
    "text": "No tengo dinero ni nada que dar..."
  },  
  {
    "id": "4", 
    "language": "it", 
    "text": "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."
  }
]
```

Вызовите функцию `sentiment()` и получите результат. Затем выполните итерацию результатов и выведите идентификатор каждого документа и оценку тональности. Оценка, близкая к 0, указывает на негативную тональность, а оценка, близкая к 1, — на позитивную.

```python
response = text_analytics.sentiment(documents=documents)
for document in response.documents:
     print("Document Id: ", document.id, ", Sentiment Score: ", "{:.2f}".format(document.score))
```

### <a name="output"></a>Выходные данные

```console
Document Id:  1 , Sentiment Score:  0.87
Document Id:  2 , Sentiment Score:  0.11
Document Id:  3 , Sentiment Score:  0.44
Document Id:  4 , Sentiment Score:  1.00
```

## <a name="language-detection"></a>Определение языка

Создайте список словарей, каждый из которых содержит документ, который необходимо проанализировать. Атрибут `text` содержит текст для анализа, а параметр `id` может иметь любое значение. 

```python
documents = [
    { 
        'id': '1', 
        'text': 'This is a document written in English.' 
    },
    {
        'id': '2', 
        'text': 'Este es un document escrito en Español.' 
    },
    { 
        'id': '3', 
        'text': '这是一个用中文写的文件' 
    }
]
``` 

Используя созданный ранее клиент, вызовите `detect_language()` и получите результат. Затем выполните итерацию результатов и выведите идентификатор каждого документа и первый возвращенный язык.

```python
response = text_analytics.detect_language(documents=documents)
for document in response.documents:
    print("Document Id: ", document.id , ", Language: ", document.detected_languages[0].name)
```

### <a name="output"></a>Выходные данные

```console
Document Id:  1 , Language:  English
Document Id:  2 , Language:  Spanish
Document Id:  3 , Language:  Chinese_Simplified
```

## <a name="entity-recognition"></a>Распознавание сущностей

Создайте список словарей, содержащих документ, который необходимо проанализировать. Атрибут `text` содержит текст для анализа, а параметр `id` может иметь любое значение. 


```python
documents = [
    {
        "id": "1",
        "language": "en", 
        "text": "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."
    },
    {
        "id": "2",
        "language": "es", 
        "text": "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    }
]
```

Используя созданный ранее клиент, вызовите функцию `entities()` и получите результат. Затем выполните итерацию результатов и выведите идентификатор каждого документа и содержащиеся в нем сущности.

```python
response = text_analytics.entities(documents=documents)

for document in response.documents:
    print("Document Id: ", document.id)
    print("\tKey Entities:")
    for entity in document.entities:
        print("\t\t", "NAME: ",entity.name, "\tType: ", entity.type, "\tSub-type: ", entity.sub_type)
        for match in entity.matches:
            print("\t\t\tOffset: ", match.offset, "\tLength: ", match.length, "\tScore: ",
                  "{:.2f}".format(match.entity_type_score))
```


### <a name="output"></a>Выходные данные

```console
Document Id:  1
    Key Entities:
         NAME:  Microsoft   Type:  Organization     Sub-type:  None
            Offset:  0  Length:  9  Score:  1.00
         NAME:  Bill Gates  Type:  Person   Sub-type:  None
            Offset:  25     Length:  10     Score:  1.00
         NAME:  Paul Allen  Type:  Person   Sub-type:  None
            Offset:  40     Length:  10     Score:  1.00
         NAME:  April 4     Type:  Other    Sub-type:  None
            Offset:  54     Length:  7  Score:  0.80
         NAME:  April 4, 1975   Type:  DateTime     Sub-type:  Date
            Offset:  54     Length:  13     Score:  0.80
         NAME:  BASIC   Type:  Other    Sub-type:  None
            Offset:  89     Length:  5  Score:  0.80
         NAME:  Altair 8800     Type:  Other    Sub-type:  None
            Offset:  116    Length:  11     Score:  0.80
Document Id:  2
    Key Entities:
         NAME:  Microsoft   Type:  Organization     Sub-type:  None
            Offset:  21     Length:  9  Score:  1.00
         NAME:  Redmond (Washington)    Type:  Location     Sub-type:  None
            Offset:  60     Length:  7  Score:  0.99
         NAME:  21 kilómetros   Type:  Quantity     Sub-type:  Dimension
            Offset:  71     Length:  13     Score:  0.80
         NAME:  Seattle     Type:  Location     Sub-type:  None
            Offset:  88     Length:  7  Score:  1.00
```

## <a name="key-phrase-extraction"></a>Извлечение ключевой фразы

Создайте список словарей, содержащих документ, который необходимо проанализировать. Атрибут `text` содержит текст для анализа, а параметр `id` может иметь любое значение. 


```python
documents = [
    {
        "id": "1", 
        "language": "ja", 
        "text": "猫は幸せ"
    },
    {
        "id": "2", 
        "language": "de", 
        "text": "Fahrt nach Stuttgart und dann zum Hotel zu Fu."
    },
    {
        "id": "3", 
        "language": "en",
        "text": "My cat might need to see a veterinarian."
    },
    {
        "id": "4", 
        "language": "es", 
        "text": "A mi me encanta el fútbol!"
    }
]
```

Используя созданный ранее клиент, вызовите функцию `key_phrases()` и получите результат. Затем выполните итерацию результатов и выведите идентификатор каждого документа и содержащиеся в нем ключевые фразы.

```python
response = text_analytics.key_phrases(documents=documents)

for document in response.documents:
    print("Document Id: ", document.id)
    print("\tKey Phrases:")
    for phrase in document.key_phrases:
        print("\t\t",phrase)
```

### <a name="output"></a>Выходные данные

```console
Document Id:  1
    Phrases:
         幸せ
Document Id:  2
    Phrases:
         Stuttgart
         Hotel
         Fahrt
         Fu
Document Id:  3
    Phrases:
         cat
         veterinarian
Document Id:  4
    Phrases:
         fútbol
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Text Analytics with Power BI](../tutorials/tutorial-power-bi-key-phrases.md) (Анализ текста с использованием Power BI)

## <a name="see-also"></a>См. также

* [Что такое API "Анализ текста"?](../overview.md)
* [Примеры пользовательских сценариев](../text-analytics-user-scenarios.md)
* [Часто задаваемые вопросы](../text-analytics-resource-faq.md)
