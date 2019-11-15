---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 10/02/2019
ms.author: aahi
ms.openlocfilehash: 847b2d0489dc04b4275465dbe957b72418bbf1a4
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73750175"
---
[Справочная документация](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-ruby/tree/master/data/azure_cognitiveservices_textanalytics) | [Пакет (RubyGems)](https://rubygems.org/gems/azure_cognitiveservices_textanalytics) | [Примеры](https://github.com/Azure-Samples/cognitive-services-quickstart-code)

<a name="HOLTop"></a>

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
* Текущая версия [Ruby](https://www.ruby-lang.org/)

## <a name="setting-up"></a>Настройка

### <a name="create-a-text-analytics-azure-resource"></a>Создание ресурса для Анализа текста в Azure 

[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

### <a name="create-a-new-ruby-application"></a>Создание нового приложения Ruby

В окне консоли (например, cmd, PowerShell или Bash) создайте новый каталог для приложения и перейдите в него. Затем создайте файл с именем `GemFile`, а также файл Ruby для своего кода.

```console
mkdir myapp && cd myapp
```

В `GemFile`добавьте следующие строки, чтобы добавить клиентскую библиотеку в качестве зависимости.

```ruby
source 'https://rubygems.org'
gem 'azure_cognitiveservices_textanalytics', '~>0.17.3'
```

Импортируйте следующие пакеты в файл Ruby.

[!code-ruby[Import statements](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=includeStatement)]

Создайте переменные для конечной точки и ключа ресурса Azure, именуемые `TEXT_ANALYTICS_ENDPOINT` и `TEXT_ANALYTICS_SUBSCRIPTION_KEY`. Если вы создали переменную среды после запуска приложения, для доступа к переменной следует закрыть и повторно открыть редактор, интегрированную среду разработки или оболочку, где эта переменная была запущена. 

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]


[!code-ruby[endpoint, key variables](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=vars)]

## <a name="object-model"></a>Объектная модель 

Клиент Анализа текста выполняет проверку подлинности в Azure, используя ключ. Этот клиент предоставляет несколько методов для анализа текста по отдельным строкам или пакетам строк. 

Текст отправляется в API в формате списка `documents` объектов `dictionary`, содержащих комбинации атрибутов `id`, `text` и `language` в зависимости от используемого метода. Атрибут `text` содержит текст для анализа в источнике `language`, а `id` может иметь любое значение. 

Объект в ответе — это список, содержащий аналитику по каждому документу. 

## <a name="code-examples"></a>Примеры кода

Эти фрагменты кода показывают, как выполнить следующие действия с клиентской библиотекой Анализа текста для Python:

* [аутентификация клиента](#authenticate-the-client);
* [Анализ тональности](#sentiment-analysis).
* [Пример. Как определить язык с помощью Анализа текста](#language-detection)
* [Распознавание сущностей](#entity-recognition)
* [Пример. Как извлечь ключевые фразы с помощью Анализа текста](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Аутентификация клиента

Создайте класс с именем `TextAnalyticsClient`. 

```ruby
class TextAnalyticsClient
  @textAnalyticsClient
  #...
end
```

В этом классе создайте функцию с именем `initialize` для проверки подлинности клиента. Используйте переменные среды `TEXT_ANALYTICS_SUBSCRIPTION_KEY` и `TEXT_ANALYTICS_ENDPOINT`. 

[!code-ruby[initialize function for authentication](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=initialize)]

За пределами класса используйте функцию клиента `new()`, чтобы создать ее экземпляр.

[!code-ruby[client creation](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=clientCreation)] 

<a name="SentimentAnalysis"></a>

## <a name="sentiment-analysis"></a>Анализ мнений

В клиентском объекте создайте функцию с именем `AnalyzeSentiment()`, которая получает список входных документов, которые будут созданы позже. Вызовите функцию клиента `sentiment()` и получите результат. Затем выполните итерацию результатов и выведите идентификатор каждого документа и оценку тональности. Оценка, близкая к 0, указывает на негативную тональность, а оценка, близкая к 1, — на позитивную.

[!code-ruby[client method for sentiment analysis](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=analyzeSentiment)] 

За пределами клиентской функции создайте новую функцию с именем `SentimentAnalysisExample()`, которая принимает ранее созданный объект `TextAnalyticsClient`. Создайте список объектов `MultiLanguageInput`, содержащих документы, которые необходимо проанализировать. Каждый объект будет содержать атрибуты `id`, `Language` и `text`. Атрибут `text` содержит текст для анализа, `language` — это язык документа, а атрибут `id` может иметь любое значение. Затем вызовите функцию клиента `AnalyzeSentiment()`.

[!code-ruby[sentiment analysis document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=sentimentCall)] 

Вызовите функцию `SentimentAnalysisExample()`.

```ruby
SentimentAnalysisExample(textAnalyticsClient)
```

### <a name="output"></a>Выходные данные

```console
===== SENTIMENT ANALYSIS =====
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

<a name="LanguageDetection"></a>

## <a name="language-detection"></a>Определение языка

В клиентском объекте создайте функцию с именем `DetectLanguage()`, которая получает список входных документов, которые будут созданы позже. Вызовите функцию клиента `detect_language()` и получите результат. Затем выполните итерацию результатов и выведите идентификатор каждого документа и обнаруженный язык.

[!code-ruby[client method for language detection](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=detectLanguage)] 

За пределами клиентской функции создайте новую функцию с именем `DetectLanguageExample()`, которая принимает ранее созданный объект `TextAnalyticsClient`. Создайте список объектов `LanguageInput`, содержащих документы, которые необходимо проанализировать. Каждый объект будет содержать атрибуты `id` и `text`. Атрибут `text` содержит текст для анализа, а параметр `id` может иметь любое значение. Затем вызовите функцию клиента `DetectLanguage()`.

[!code-ruby[language detection document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=detectLanguageCall)] 

Вызовите функцию `DetectLanguageExample()`.

```ruby
DetectLanguageExample(textAnalyticsClient)
```

### <a name="output"></a>Выходные данные

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

<a name="EntityRecognition"></a>

## <a name="entity-recognition"></a>Распознавание сущностей

В клиентском объекте создайте функцию с именем `RecognizeEntities()`, которая получает список входных документов, которые будут созданы позже. Вызовите функцию клиента `entities()` и получите результат. Затем выполните итерацию по результатам и выведите идентификатор каждого документа и распознаваемые сущности.

[!code-ruby[client method for entity recognition](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=recognizeEntities)]

За пределами клиентской функции создайте новую функцию с именем `RecognizeEntitiesExample()`, которая принимает ранее созданный объект `TextAnalyticsClient`. Создайте список объектов `MultiLanguageInput`, содержащих документы, которые необходимо проанализировать. Каждый объект будет содержать атрибуты `id`, `language` и `text`. Атрибут `text` содержит текст для анализа, `language` — это язык текста, а атрибут `id` может иметь любое значение. Затем вызовите функцию клиента `RecognizeEntities()`.

[!code-ruby[entity recognition documents and method call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=recognizeEntitiesCall)] 

Вызовите функцию `RecognizeEntitiesExample()`.

```ruby
RecognizeEntitiesExample(textAnalyticsClient)
```

### <a name="output"></a>Выходные данные

```console
===== ENTITY RECOGNITION =====
Document ID: 1
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 0, Length: 9,   Score: 1.0

        Name: Bill Gates,       Type: Person,   Sub-Type: N/A
        Offset: 25, Length: 10, Score: 0.999847412109375

        Name: Paul Allen,       Type: Person,   Sub-Type: N/A
        Offset: 40, Length: 10, Score: 0.9988409876823425

        Name: April 4,  Type: Other,    Sub-Type: N/A
        Offset: 54, Length: 7,  Score: 0.8

        Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
        Offset: 54, Length: 13, Score: 0.8

        Name: BASIC,    Type: Other,    Sub-Type: N/A
        Offset: 89, Length: 5,  Score: 0.8

        Name: Altair 8800,      Type: Other,    Sub-Type: N/A
        Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 21, Length: 9,  Score: 0.999755859375

        Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
        Offset: 60, Length: 7,  Score: 0.9911284446716309

        Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
        Offset: 71, Length: 13, Score: 0.8

        Name: Seattle,  Type: Location, Sub-Type: N/A
        Offset: 88, Length: 7,  Score: 0.9998779296875
```

<a name="KeyPhraseExtraction"></a>

## <a name="key-phrase-extraction"></a>Извлечение ключевой фразы

В клиентском объекте создайте функцию с именем `ExtractKeyPhrases()`, которая получает список входных документов, которые будут созданы позже. Вызовите функцию клиента `key_phrases()` и получите результат. Затем выполните итерацию результатов и выведите идентификатор каждого документа и извлеченные ключевые фразы.

[!code-ruby[key phrase extraction client method](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=extractKeyPhrases)] 

За пределами клиентской функции создайте новую функцию с именем `KeyPhraseExtractionExample()`, которая принимает ранее созданный объект `TextAnalyticsClient`. Создайте список объектов `MultiLanguageInput`, содержащих документы, которые необходимо проанализировать. Каждый объект будет содержать атрибуты `id`, `language` и `text`. Атрибут `text` содержит текст для анализа, `language` — это язык текста, а атрибут `id` может иметь любое значение. Затем вызовите функцию клиента `ExtractKeyPhrases()`.

[!code-ruby[key phrase document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=keyPhrasesCall)]


Вызовите функцию `KeyPhraseExtractionExample()`.

```ruby
KeyPhraseExtractionExample(textAnalyticsClient)
```

### <a name="output"></a>Выходные данные

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                rock
Document ID: 4
         Key phrases:
                fútbol
```
