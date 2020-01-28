---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 01/13/2019
ms.author: aahi
ms.openlocfilehash: 518e6d544547b808b278121bf6364dcd1590bd6f
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/20/2020
ms.locfileid: "76281194"
---
<a name="HOLTop"></a>

[Справочная документация](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-textanalytics/1.0.0b1/azure.ai.textanalytics.html) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics) | [Пакет (PiPy)](https://pypi.org/project/azure-ai-textanalytics/) | [Примеры](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

> [!NOTE]
> * В этом кратком руководстве используется версия `3.0-preview` клиентской библиотеки Анализа текста, которая включает в себя общедоступную предварительную версию улучшенных функций [анализа тональности](../../../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) и [распознавания именованных сущностей (NER)](../../../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features).
> * В коде, приведенном в этой статье, для простоты используются синхронные методы и незащищенное хранилище учетных данных. Для рабочих сценариев рекомендуется использовать пакетные асинхронные методы для повышения производительности и масштабируемости. Например, импорт клиента из пространства имен `azure.ai.textanalytics.aio` и вызов `analyze_sentiment()` вместо `analyze_sentiment()` из пространства имен `azure.ai.textanalytics`.

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Настройка

### <a name="create-a-text-analytics-azure-resource"></a>Создание ресурса для Анализа текста в Azure

[!INCLUDE [text-analytics-resource-creation](../resource-creation.md)]

### <a name="install-the-client-library"></a>Установка клиентской библиотеки

После установки Python вы можете установить клиентскую библиотеку с помощью следующей команды:

```console
pip install azure-ai-textanalytics
```

### <a name="create-a-new-python-application"></a>Создание приложения Python

Создайте файл Python и переменные для конечной точки ресурса Azure и ключа подписки.

[!INCLUDE [text-analytics-find-resource-information](../../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>Объектная модель

Клиент Анализа текста представляет собой объект `TextAnalyticsClient`, который выполняет проверку подлинности в Azure с использованием ключа. Этот клиент предоставляет несколько методов для анализа текста по пакетам. В этом кратком руководстве для быстрой отправки отдельных документов используется коллекция функций.

Во время пакетной обработки текст отправляется в API в формате списка `documents` объектов `dictionary`, содержащих комбинации атрибутов `id`, `text` и `language` в зависимости от используемого метода. Атрибут `text` содержит текст для анализа в источнике `language`, а `id` может иметь любое значение. При обработке отдельных документов требуются только входные данные `text`, как видно в примерах ниже.  

Объект в ответе — это список, содержащий аналитику по каждому документу. 

## <a name="code-examples"></a>Примеры кода

Эти фрагменты кода показывают, как выполнить следующие задачи с клиентской библиотекой Анализа текста для Python:

* [Анализ тональности](#sentiment-analysis) (общедоступная предварительная версия)
* [Пример. Как определить язык с помощью Анализа текста](#language-detection)
* [Распознавание именованных сущностей](#named-entity-recognition-public-preview) (общедоступная предварительная версия)
* [Распознавание именованных сущностей — персональные данные](#named-entity-recognition---personal-information-public-preview) (общедоступная предварительная версия)
* [Связывание сущностей](#entity-linking)
* [Пример. Как извлечь ключевые фразы с помощью Анализа текста](#key-phrase-extraction)

## <a name="sentiment-analysis"></a>Анализ мнений

> [!NOTE]
> В приведенном ниже коде используется функция анализа тональности версии 3, которая находится в общедоступной предварительной версии.

Создайте функцию с именем `sentiment_analysis_example()`, которая принимает конечную точку и ключ в качестве аргументов, а затем вызывает функцию `single_analyze_sentiment()`. Возвращаемый объект ответа будет содержать метку тональности и оценку всего входного документа, а также анализ тональности для каждого предложения.


```python
from azure.ai.textanalytics import single_analyze_sentiment

def sentiment_analysis_example(endpoint, key):

    document = "I had the best day of my life. I wish you were there with me."

    response = single_analyze_sentiment(endpoint=endpoint, key=key, input_text=document)
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.3f}; neutral={1:.3f}; negative={2:.3f} \n".format(
        response.document_scores.positive,
        response.document_scores.neutral,
        response.document_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("[Offset: {}, Length: {}]".format(sentence.offset, sentence.length))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.3f}\nNeutral={1:.3f}\nNegative={2:.3f}\n".format(
            sentence.sentence_scores.positive,
            sentence.sentence_scores.neutral,
            sentence.sentence_scores.negative,
        ))

            
sentiment_analysis_example(endpoint, key)
```

### <a name="output"></a>Выходные данные

```console
Document Sentiment: positive
Overall scores: positive=0.999; neutral=0.001; negative=0.000 

[Offset: 0, Length: 30]
Sentence 1 sentiment: positive
Sentence score:
positive=0.999
neutral=0.001
negative=0.000

[Offset: 31, Length: 30]
Sentence 2 sentiment: neutral
Sentence score:
positive=0.212
neutral=0.771
negative=0.017
```

## <a name="language-detection"></a>Определение языка

Создайте функцию с именем `language_detection_example()`, которая принимает конечную точку и ключ в качестве аргументов, а затем вызывает функцию `single_detect_languages()`. Возвращенный объект ответа будет содержать обнаруженный язык в `detected_languages`, если выполнение успешное, и значение `error` в противном случае.

> [!Tip]
> В некоторых случаях неоднозначность языков на основе входных данных может быть трудно устранить. Используйте параметр `country_hint`, чтобы указать двухбуквенный код страны. По умолчанию API использует US в качестве значения countryHint. Чтобы отменить это поведение, вы можете сбросить этот параметр, установив для этого значения пустую строку `country_hint : ""`. 

```python
from azure.ai.textanalytics import single_detect_language

def language_detection_example(endpoint, key):
    try:
        document = "Ce document est rédigé en Français."
        response = single_detect_language(endpoint=endpoint, key=key, input_text= document)
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(endpoint, key)
```


### <a name="output"></a>Выходные данные

```console
Language:  French
```

## <a name="named-entity-recognition-public-preview"></a>Распознавание именованных сущностей (общедоступная предварительная версия)

> [!NOTE]
> В приведенном ниже коде используется функция распознавания именованных сущностей версии 3, которая находится в общедоступной предварительной версии.

Создайте функцию с именем `entity_recognition_example`, которая принимает конечную точку и ключ в качестве аргументов, а затем вызывает функцию `single_recognize_entities()` и выполняет перебор результатов. Возвращенный объект ответа будет содержать список обнаруженных сущностей в `entity`, если выполнение успешное, и значение `error` в противном случае. Для каждой обнаруженной сущности выведите ее тип и подтип, если он существует.

```python
from azure.ai.textanalytics import single_recognize_entities

def entity_recognition_example(endpoint, key):

    try:
        document = "I had a wonderful trip to Seattle last week."
        result = single_recognize_entities(endpoint=endpoint, key=key, input_text= document)
        
        print("Named Entities:\n")
        for entity in result.entities:
                print("\tText: \t", entity.text, "\tType: \t", entity.type, "\tSubType: \t", entity.subtype,
                      "\n\tOffset: \t", entity.offset, "\tLength: \t", entity.offset, 
                      "\tConfidence Score: \t", round(entity.score, 3), "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(endpoint, key)
```

### <a name="output"></a>Выходные данные

```console
Named Entities:

    Text:    Seattle    Type:    Location   SubType:     None 
    Offset:      26     Length:      26     Confidence Score:    0.806 

    Text:    last week  Type:    DateTime   SubType:     DateRange 
    Offset:      34     Length:      34     Confidence Score:    0.8 
```

## <a name="named-entity-recognition---personal-information-public-preview"></a>Распознавание именованных сущностей — персональные данные (общедоступная предварительная версия)

> [!NOTE]
> Приведенный ниже код позволяет обнаружить личную информацию с помощью функции распознавания именованных сущностей версии 3, которая находится в общедоступной предварительной версии.

Создайте функцию с именем `entity_pii_example()`, которая принимает конечную точку и ключ в качестве аргументов, а затем вызывает функцию `single_recognize_pii_entities()` и получает результаты. Затем просмотрите все результаты и выведите сущности.

```python
from azure.ai.textanalytics import single_recognize_pii_entities

def entity_pii_example(endpoint, key):

        document = "Insurance policy for SSN on file 123-12-1234 is here by approved."


        result = single_recognize_pii_entities(endpoint=endpoint, key=key, input_text= document)
        
        print("Personally Identifiable Information Entities: ")
        for entity in result.entities:
            print("\tText: ",entity.text,"\tType: ", entity.type,"\tSub-Type: ", entity.subtype)
            print("\t\tOffset: ", entity.offset, "\tLength: ", entity.length, "\tScore: {0:.3f}".format(entity.score), "\n")
        
entity_pii_example(endpoint, key)
```

### <a name="output"></a>Выходные данные

```console
Personally Identifiable Information Entities: 
    Text:  123-12-1234  Type:  U.S. Social Security Number (SSN)    Sub-Type:  
        Offset:  33     Length:  11     Score: 0.850 
```

## <a name="entity-linking"></a>Связывание сущностей

Создайте функцию с именем `entity_linking_example()`, которая принимает конечную точку и ключ в качестве аргументов, а затем вызывает функцию `single_recognize_linked_entities()` и выполняет перебор результатов. Возвращенный объект ответа будет содержать список обнаруженных сущностей в `entities`, если выполнение успешное, и значение `error` в противном случае. Так как связанные сущности уникально идентифицируются, вхождения одной и той же сущности группируются в объекте `entity` как список объектов `match`.

```python
from azure.ai.textanalytics import single_recognize_linked_entities

def entity_linking_example(endpoint, key):

    try:
        document = """Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""
        result = single_recognize_linked_entities(endpoint=endpoint, key=key, input_text= document)

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tScore: {0:.3f}".format(match.score), "\tOffset: ", match.offset, 
                      "\tLength: {}\n".format(match.length))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(endpoint, key)
```

### <a name="output"></a>Выходные данные

```console
Linked Entities:

    Name:  Altair 8800  Id:  Altair 8800    Url:  https://en.wikipedia.org/wiki/Altair_8800 
    Data Source:  Wikipedia
    Matches:
        Text: Altair 8800
        Score: 0.777    Offset:  116    Length: 11

    Name:  Bill Gates   Id:  Bill Gates     Url:  https://en.wikipedia.org/wiki/Bill_Gates 
    Data Source:  Wikipedia
    Matches:
        Text: Bill Gates
        Score: 0.555    Offset:  25     Length: 10

        Text: Gates
        Score: 0.555    Offset:  161    Length: 5

    Name:  Paul Allen   Id:  Paul Allen     Url:  https://en.wikipedia.org/wiki/Paul_Allen 
    Data Source:  Wikipedia
    Matches:
        Text: Paul Allen
        Score: 0.533    Offset:  40     Length: 10

    Name:  Microsoft    Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft 
    Data Source:  Wikipedia
    Matches:
        Text: Microsoft
        Score: 0.469    Offset:  0  Length: 9

        Text: Microsoft
        Score: 0.469    Offset:  150    Length: 9

    Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4 
    Data Source:  Wikipedia
    Matches:
        Text: April 4
        Score: 0.248    Offset:  54     Length: 7

    Name:  BASIC    Id:  BASIC  Url:  https://en.wikipedia.org/wiki/BASIC 
    Data Source:  Wikipedia
    Matches:
        Text: BASIC
        Score: 0.281    Offset:  89     Length: 5
```

## <a name="key-phrase-extraction"></a>Извлечение ключевой фразы

Создайте функцию с именем `key_phrase_extraction_example()`, которая принимает конечную точку и ключ в качестве аргументов, а затем вызывает функцию `single_extract_key_phrases()`. При успешном выполнении результат будет содержать список обнаруженных ключевых фраз в `key_phrases`. В противном случае вернется `error`. Затем выведите все обнаруженные ключевые фразы.

```python
from azure.ai.textanalytics import single_extract_key_phrases

def key_phrase_extraction_example(endpoint, key):

    try:
        document = "My cat might need to see a veterinarian."

        response = single_extract_key_phrases(endpoint=endpoint, key=key, input_text= document)

        if not response.is_error:
            print("\tKey Phrases:")
            for phrase in response.key_phrases:
                print("\t\t", phrase)
        else:
            print(response.id, response.error)

    except Exception as err:
        print("Encountered exception. {}".format(err))
        
key_phrase_extraction_example(endpoint, key)
```


### <a name="output"></a>Выходные данные

```console
    Key Phrases:
         cat
         veterinarian
```