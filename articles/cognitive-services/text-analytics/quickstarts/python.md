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
ms.date: 04/16/2019
ms.author: aahi
ms.openlocfilehash: 69eb3789586233b824da1ef6a9c338b07281f324
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60001394"
---
# <a name="quickstart-using-python-to-call-the-text-analytics-cognitive-service"></a>Краткое руководство. Использование Python для вызова API анализа текста Cognitive Services 
<a name="HOLTop"></a>

В этом пошаговом руководстве содержатся сведения о [распознавании языка](#Detect), [анализе тональности](#SentimentAnalysis) и [извлечении ключевых фраз](#KeyPhraseExtraction) с использованием [API анализа текста](//go.microsoft.com/fwlink/?LinkID=759711) для Python.

Вы можете запустить этот пример из командной строки или как записную книжку Jupyter в [MyBinder](https://mybinder.org), щелкнув эмблему запуска Binder:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=TextAnalytics.ipynb)

### <a name="command-line"></a>Команда

Вам может потребоваться обновить [IPython](https://ipython.org/install.html), ядро для Jupyter:
```bash
pip install --upgrade IPython
```

Вам может потребоваться обновить библиотеку [запросов](http://docs.python-requests.org/en/master/):
```bash
pip install requests
```

Техническую документацию по API-интерфейсам см. в разделе [API definitions](//go.microsoft.com/fwlink/?LinkID=759346) (Определения API).

## <a name="prerequisites"></a>Предварительные требования

* [!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

* [Конечная точка и ключ доступа](../How-tos/text-analytics-how-to-access-key.md), созданный автоматически во время регистрации.

* Следующие операции импорта, ключ подписки и `text_analytics_base_url` используются для всех кратких руководств ниже. Добавьте операции импорта.

    ```python
    import requests
    # pprint is pretty print (formats the JSON)
    from pprint import pprint
    from IPython.display import HTML
    ```
    
    Добавьте эти строки, затем замените `subscription_key` действительным ключом подписки, полученным ранее.
    
    ```python
    subscription_key = '<ADD KEY HERE>'
    assert subscription_key
    ```
    
    Далее добавьте эту строку, затем убедитесь, что регион в `text_analytics_base_url` соответствует тому, который вы указали при настройке службы. Если вы используете ключ бесплатной пробной версии, ничего изменять не нужно.
    
    ```python
    text_analytics_base_url = "https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/"
    ```

<a name="Detect"></a>

## <a name="detect-languages"></a>Распознавание языков

API распознавания языка определяет язык текстового документа, используя [метод распознавания языка](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7). Конечная точка API распознавания языка доступна через следующий URL-адрес:

```python
language_api_url = text_analytics_base_url + "languages"
print(language_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/languages


Полезные данные этого API состоят из списка объектов `documents`, каждый из которых содержит `id` и атрибут `text`. Атрибут `text` содержит текст для анализа. 

Замените словарь `documents` любым другим текстом для распознавания языка.

```python
documents = { 'documents': [
    { 'id': '1', 'text': 'This is a document written in English.' },
    { 'id': '2', 'text': 'Este es un document escrito en Español.' },
    { 'id': '3', 'text': '这是一个用中文写的文件' }
]}
```

Следующие несколько строк кода обращаются к API распознавания языка с помощью библиотеки `requests` для Python, чтобы определить язык текста в этих документах.

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(language_api_url, headers=headers, json=documents)
languages = response.json()
pprint(languages)
```

В следующих строках кода отображаются данные JSON в виде HTML-таблицы.

```python
table = []
for document in languages["documents"]:
    text  = next(filter(lambda d: d["id"] == document["id"], documents["documents"]))["text"]
    langs = ", ".join(["{0}({1})".format(lang["name"], lang["score"]) for lang in document["detectedLanguages"]])
    table.append("<tr><td>{0}</td><td>{1}</td>".format(text, langs))
HTML("<table><tr><th>Text</th><th>Detected languages(scores)</th></tr>{0}</table>".format("\n".join(table)))
```

Успешный ответ JSON:

```json
    {'documents': [{'detectedLanguages': [{'iso6391Name': 'en',
                                           'name': 'English',
                                           'score': 1.0}],
                    'id': '1'},
                   {'detectedLanguages': [{'iso6391Name': 'es',
                                           'name': 'Spanish',
                                           'score': 1.0}],
                    'id': '2'},
                   {'detectedLanguages': [{'iso6391Name': 'zh_chs',
                                           'name': 'Chinese_Simplified',
                                           'score': 1.0}],
                    'id': '3'}],
     'errors': []}
```

<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Анализ тональности

API анализа тональности определяет тональность (диапазон между положительным и отрицательным) набора текстовых записей с помощью [метода определения тональности](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9). В следующем примере выполняется оценка двух документов — на английском и на испанском языках.

Конечная точка службы определения тональности доступна в вашем регионе через следующий URL-адрес:

```python
sentiment_api_url = text_analytics_base_url + "sentiment"
print(sentiment_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/sentiment

Как и в примере распознавания языка, этой службе передается словарь с ключом `documents`, который содержит список документов. Каждый документ представляет собой кортеж, состоящий из `id`, `text` для анализа и `language` для текста. Вы можете использовать API распознавания языка из предыдущего раздела, чтобы заполнить это поле.

```python
documents = {'documents' : [
  {'id': '1', 'language': 'en', 'text': 'I had a wonderful experience! The rooms were wonderful and the staff was helpful.'},
  {'id': '2', 'language': 'en', 'text': 'I had a terrible time at the hotel. The staff was rude and the food was awful.'},  
  {'id': '3', 'language': 'es', 'text': 'Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos.'},  
  {'id': '4', 'language': 'es', 'text': 'La carretera estaba atascada. Había mucho tráfico el día de ayer.'}
]}
```

Теперь можно использовать API анализа тональности для определения тональности этих документов.

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(sentiment_api_url, headers=headers, json=documents)
sentiments = response.json()
pprint(sentiments)
```

Успешный ответ JSON:

```json
{'documents': [{'id': '1', 'score': 0.7673527002334595},
                {'id': '2', 'score': 0.18574094772338867},
                {'id': '3', 'score': 0.5}],
    'errors': []}
```

Оценка тональности для документа имеет значение в диапазоне от 0.0 до 1.0, где большее число обозначает более положительную тональность.

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Извлечение ключевых фраз

API извлечения ключевых фраз извлекает ключевые фразы из текстового документа с помощью [метода ключевых фраз](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6). В этом разделе пошагового руководства показано, как извлекаются ключевые фразы из документов на английском и испанском языках.

Конечная точка службы для извлечения ключевых фраз доступна через следующий URL-адрес:

```python
key_phrase_api_url = text_analytics_base_url + "keyPhrases"
print(key_phrase_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases

Здесь используется та же коллекция документов, что и для анализа тональности.

```python
documents = {'documents' : [
  {'id': '1', 'language': 'en', 'text': 'I had a wonderful experience! The rooms were wonderful and the staff was helpful.'},
  {'id': '2', 'language': 'en', 'text': 'I had a terrible time at the hotel. The staff was rude and the food was awful.'},  
  {'id': '3', 'language': 'es', 'text': 'Los caminos que llevan hasta Monte Rainier son espectaculares y hermosos.'},  
  {'id': '4', 'language': 'es', 'text': 'La carretera estaba atascada. Había mucho tráfico el día de ayer.'}
]}
```

Объект JSON можно преобразовать в HTML-таблицу для просмотра, используя следующие строки кода:

```python
table = []
for document in key_phrases["documents"]:
    text    = next(filter(lambda d: d["id"] == document["id"], documents["documents"]))["text"]    
    phrases = ",".join(document["keyPhrases"])
    table.append("<tr><td>{0}</td><td>{1}</td>".format(text, phrases))
HTML("<table><tr><th>Text</th><th>Key phrases</th></tr>{0}</table>".format("\n".join(table)))
```

Следующие несколько строк кода обращаются к API распознавания языка с помощью библиотеки `requests` для Python, чтобы определить язык текста в этих документах.
```python
headers   = {'Ocp-Apim-Subscription-Key': subscription_key}
response  = requests.post(key_phrase_api_url, headers=headers, json=documents)
key_phrases = response.json()
pprint(key_phrases)
```

Успешный ответ JSON:
```json
{'documents': [
    {'keyPhrases': ['wonderful experience', 'staff', 'rooms'], 'id': '1'},
    {'keyPhrases': ['food', 'terrible time', 'hotel', 'staff'], 'id': '2'},
    {'keyPhrases': ['Monte Rainier', 'caminos'], 'id': '3'},
    {'keyPhrases': ['carretera', 'tráfico', 'día'], 'id': '4'}],
    'errors': []
}
```

## <a name="identify-entities"></a>Определение сущностей

API сущностей определяет известные сущности в текстовом документе, используя [метод Entities](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634). Следующий пример определяет сущности в документах на английском языке.

Конечная точка службы для связывания сущностей доступна через следующий URL-адрес:

```python
entity_linking_api_url = text_analytics_base_url + "entities"
print(entity_linking_api_url)
```

    https://westcentralus.api.cognitive.microsoft.com/text/analytics/v2.1/entities

Ниже представлена коллекция документов.

```python
documents = {'documents' : [
  {'id': '1', 'text': 'Microsoft is an It company.'}
]}
```
Теперь эти документы можно передать в API анализа текста для получения ответа.

```python
headers   = {"Ocp-Apim-Subscription-Key": subscription_key}
response  = requests.post(entity_linking_api_url, headers=headers, json=documents)
entities = response.json()
```

Успешный ответ JSON:
```json
{  
   "documents":[  
      {  
         "id":"1",
         "entities":[  
            {  
               "name":"Microsoft",
               "matches":[  
                  {  
                     "wikipediaScore":0.20872054383103444,
                     "entityTypeScore":0.99996185302734375,
                     "text":"Microsoft",
                     "offset":0,
                     "length":9
                  }
               ],
               "wikipediaLanguage":"en",
               "wikipediaId":"Microsoft",
               "wikipediaUrl":"https://en.wikipedia.org/wiki/Microsoft",
               "bingId":"a093e9b9-90f5-a3d5-c4b8-5855e1b01f85",
               "type":"Organization"
            },
            {  
               "name":"Technology company",
               "matches":[  
                  {  
                     "wikipediaScore":0.82123868042800585,
                     "text":"It company",
                     "offset":16,
                     "length":10
                  }
               ],
               "wikipediaLanguage":"en",
               "wikipediaId":"Technology company",
               "wikipediaUrl":"https://en.wikipedia.org/wiki/Technology_company",
               "bingId":"bc30426e-22ae-7a35-f24b-454722a47d8f"
            }
         ]
      }
   ],
    "errors":[]
}
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Text Analytics with Power BI](../tutorials/tutorial-power-bi-key-phrases.md) (Анализ текста с использованием Power BI)

## <a name="see-also"></a>См. также 

 [Text Analytics overview](../overview.md) (Общие сведения об анализе текста)  
 [Часто задаваемые вопросы](../text-analytics-resource-faq.md)
