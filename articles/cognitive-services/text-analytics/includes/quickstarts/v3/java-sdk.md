---
title: Краткое руководство. Клиентская библиотека Анализа текста версии 3 для Java | Документация Майкрософт
description: Начало работы с клиентской библиотекой Анализа текста версии 3 для Java.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/15/2020
ms.author: aahi
ms.reviewer: tasharm, assafi
ms.openlocfilehash: a7882f1337e990f828bea15cdc2b9ad72de44f18
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/20/2020
ms.locfileid: "76281197"
---
<a name="HOLTop"></a>

[Справочная документация](https://aka.ms/azsdk-java-textanalytics-ref-docs) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/textanalytics/azure-ai-textanalytics) | [Пакет (Maven)](https://oss.sonatype.org/#nexus-search;quick~com.azure) | [Примеры](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

> [!NOTE]
> * В этом кратком руководстве используется версия `3.0-preview` клиентской библиотеки Анализа текста, которая включает в себя общедоступную предварительную версию улучшенных функций [анализа тональности](../../../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) и [распознавания именованных сущностей (NER)](../../../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features).
> * В коде, приведенном в этой статье, для простоты используются синхронные методы и незащищенное хранилище учетных данных. Для рабочих сценариев рекомендуется использовать пакетные асинхронные методы для повышения производительности и масштабируемости. Например, следует вызывать `SentimentBatchAsync()` вместо `Sentiment()`.

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
* [Пакет разработчиков Java](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK) версии 8 или более поздней.

## <a name="setting-up"></a>Настройка

### <a name="create-a-text-analytics-azure-resource"></a>Создание ресурса для Анализа текста в Azure

[!INCLUDE [text-analytics-resource-creation](../resource-creation.md)]

### <a name="create-a-new-maven-project"></a>Создание нового проекта Maven

Добавьте в проект следующую зависимость текстовой аналитики. Эта версия зависимости использует версию `3.0-preview` API Анализа текста. 

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>1.0.0-beta.1</version>
    </dependency>
</dependencies>
```

Создайте файл Java в следующем каталоге: `\src\main\java`.

Откройте файл Java и добавьте следующие операторы `import`:

```java
import com.azure.ai.textanalytics.models.AnalyzeSentimentResult;
import com.azure.ai.textanalytics.models.DetectLanguageResult;
import com.azure.ai.textanalytics.models.DetectedLanguage;
import com.azure.ai.textanalytics.models.ExtractKeyPhraseResult;
import com.azure.ai.textanalytics.models.LinkedEntity;
import com.azure.ai.textanalytics.models.LinkedEntityMatch;
import com.azure.ai.textanalytics.models.NamedEntity;
import com.azure.ai.textanalytics.models.RecognizeEntitiesResult;
import com.azure.ai.textanalytics.models.RecognizeLinkedEntitiesResult;
import com.azure.ai.textanalytics.models.RecognizePiiEntitiesResult;
import com.azure.ai.textanalytics.models.TextSentiment;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;
import java.util.List;
```

В файле Java добавьте новый класс, а также ваш ключ и конечную точку ресурса Azure, как показано ниже.

[!INCLUDE [text-analytics-find-resource-information](../../find-azure-resource-info.md)]

```java
public class TextAnalyticsSample {
    private static String KEY = "<replace-with-your-text-analytics-key-here>";
    private static String ENDPOINT = "<replace-with-your-text-analytics-endpoint-here>";
}
```

Добавьте следующий метод main в класс. Вызванные методы будут определены здесь позже.

```java
public static void main(String[] args) {
    
    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);
    
    sentimentAnalysisExample(client);
    detectLanguageExample(client);
    recognizeEntitiesExample(client);
    recognizePIIEntitiesExample(client);
    recognizeLinkedEntitiesExample(client);
    extractKeyPhrasesExample(client);
}
```

## <a name="object-model"></a>Объектная модель

Клиент Анализа текста является объектом `TextAnalyticsClient`, который выполняет проверку подлинности в Azure с помощью вашего ключа и предоставляет функции для приема текста в виде отдельных строк или пакета. Текст в API можно отправлять как синхронно, так и асинхронно. Объект в ответе будет содержать аналитическую информацию по каждому отправленному вами документу. 

## <a name="code-examples"></a>Примеры кода

* [аутентификация клиента](#authenticate-the-client);
* [Анализ тональности](#sentiment-analysis) (общедоступная предварительная версия)
* [Пример. Как определить язык с помощью Анализа текста](#language-detection)
* [Распознавание именованных сущностей](#named-entity-recognition-public-preview) (общедоступная предварительная версия)
* [Распознавание именованных сущностей — персональные данные](#named-entity-recognition---personal-information-public-preview) (общедоступная предварительная версия)
* [Связывание сущностей](#entity-linking)
* [Пример. Как извлечь ключевые фразы с помощью Анализа текста](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Аутентификация клиента

Создайте метод для генерирования экземпляра объекта `TextAnalyticsClient` с использованием указанных ранее значений `KEY` и `ENDPOINT`.

```java
static TextAnalyticsClient authenticateClient(String subscriptionKey, String endpoint) {
    return new TextAnalyticsClientBuilder()
    .subscriptionKey(subscriptionKey)
    .endpoint(endpoint)
    .buildClient();
}
```

В методе `main()` программы вызовите метод проверки подлинности для создания экземпляра клиента.

## <a name="sentiment-analysis"></a>Анализ мнений

> [!NOTE]
> В приведенном ниже коде используется функция анализа тональности версии 3, которая находится в общедоступной предварительной версии.

Создайте функцию с именем `sentimentAnalysisExample()`, которая принимает созданный ранее клиент, и вызовите вложенную в нее функцию `analyzeSentiment()`. Возвращенный объект `AnalyzeSentimentResult` будет содержать `documentSentiment` и `sentenceSentiments`, если выполнение успешное, и значение `errorMessage` в противном случае. 

```java
static void sentimentAnalysisExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had the best day of my life. I wish you were there with me.";

    AnalyzeSentimentResult sentimentResult = client.analyzeSentiment(text);
    TextSentiment documentSentiment = sentimentResult.getDocumentSentiment();
    System.out.printf(
        "Recognized TextSentiment: %s, Positive Score: %.2f, Neutral Score: %.2f, Negative Score: %.2f.%n",
        documentSentiment.getTextSentimentClass(),
        documentSentiment.getPositiveScore(),
        documentSentiment.getNeutralScore(),
        documentSentiment.getNegativeScore());

    List<TextSentiment> sentiments = sentimentResult.getSentenceSentiments();
    for (TextSentiment textSentiment : sentiments) {
        System.out.printf(
            "Recognized Sentence TextSentiment: %s, Positive Score: %.2f, Neutral Score: %.2f, Negative Score: %.2f.%n",
            textSentiment.getTextSentimentClass(),
            textSentiment.getPositiveScore(),
            textSentiment.getNeutralScore(),
            textSentiment.getNegativeScore());
    }
}
```

### <a name="output"></a>Выходные данные

```console
Recognized TextSentiment: positive, Positive Score: 1.00, Neutral Score: 0.00, Negative Score: 0.00.
Recognized Sentence TextSentiment: positive, Positive Score: 1.00, Neutral Score: 0.00, Negative Score: 0.00.
Recognized Sentence TextSentiment: neutral, Positive Score: 0.21, Neutral Score: 0.77, Negative Score: 0.02.
```
## <a name="language-detection"></a>Определение языка

Создайте функцию с именем `detectLanguageExample()`, которая принимает созданный ранее клиент, и вызовите вложенную в нее функцию `detectLanguage()`. Возвращенный объект `DetectLanguageResult` будет содержать обнаруженный основной язык, список других обнаруженных языков, если выполнение успешное, и значение `errorMessage` в противном случае.

> [!Tip]
> В некоторых случаях неоднозначность языков на основе входных данных может быть трудно устранить. Используйте параметр `countryHint`, чтобы указать двухбуквенный код страны. По умолчанию API использует US в качестве значения countryHint. Чтобы отменить это поведение, вы можете сбросить этот параметр, установив для этого значения пустую строку `countryHint = ""`. Чтобы установить другое значение по умолчанию, задайте свойство `TextAnalyticsClientOptions.DefaultCountryHint` и передайте его во время инициализации клиента.

```java
static void detectLanguageExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Ce document est rédigé en Français.";

    DetectLanguageResult detectLanguageResult = client.detectLanguage(text, "US");
    DetectedLanguage detectedDocumentLanguage = detectLanguageResult.getPrimaryLanguage();
    System.out.printf("Language: %s, ISO 6391 Name: %s, Score: %s.%n",
        detectedDocumentLanguage.getName(),
        detectedDocumentLanguage.getIso6391Name(),
        detectedDocumentLanguage.getScore());
}
```

### <a name="output"></a>Выходные данные

```console
Language: French, ISO 6391 Name: fr, Score: 1.0.
```
## <a name="named-entity-recognition-public-preview"></a>Распознавание именованных сущностей (общедоступная предварительная версия)

> [!NOTE]
> В приведенном ниже коде используется функция распознавания именованных сущностей версии 3, которая находится в общедоступной предварительной версии.

Создайте функцию с именем `recognizeEntitiesExample()`, которая принимает созданный ранее клиент, и вызовите вложенную в нее функцию `recognizeEntities()`. Возвращенный объект `RecognizeEntitiesResult` будет содержать список `NamedEntity`, если выполнение успешное, и значение `errorMessage` в противном случае.

```java
static void recognizeEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analysed.
    String text = "I had a wonderful trip to Seattle last week.";
    
    RecognizeEntitiesResult recognizeEntitiesResult = client.recognizeEntities(text);

    for (NamedEntity entity : recognizeEntitiesResult.getNamedEntities()) {
        System.out.printf(
            "Recognized NamedEntity Text: %s, Type: %s, Subtype: %s, Offset: %s, Length: %s, Score: %.3f.%n",
            entity.getText(),
            entity.getType(),
            entity.getSubtype() == null || entity.getSubtype().isEmpty() ? "N/A" : entity.getSubtype(),
            entity.getOffset(),
            entity.getLength(),
            entity.getScore());
    }
}
```

### <a name="output"></a>Выходные данные

```console
Recognized NamedEntity Text: Seattle, Type: Location, Subtype: N/A, Offset: 26, Length: 7, Score: 0.806.
Recognized NamedEntity Text: last week, Type: DateTime, Subtype: DateRange, Offset: 34, Length: 9, Score: 0.800.
```

## <a name="named-entity-recognition---personal-information-public-preview"></a>Распознавание именованных сущностей — персональные данные (общедоступная предварительная версия)

> [!NOTE]
> Приведенный ниже код позволяет обнаружить личную информацию с помощью функции распознавания именованных сущностей версии 3, которая находится в общедоступной предварительной версии.

Создайте функцию с именем `recognizePIIEntitiesExample()`, которая принимает созданный ранее клиент, и вызовите вложенную в нее функцию `recognizePiiEntities()`. Возвращенный объект `RecognizePiiEntitiesResult` будет содержать список `NamedEntity`, если выполнение успешное, и значение `errorMessage` в противном случае. 

```java
static void recognizePIIEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analysed.
    String text = "Insurance policy for SSN on file 123-12-1234 is here by approved.";
    
    RecognizePiiEntitiesResult recognizePIIEntitiesResult = client.recognizePiiEntities(text);

    for (NamedEntity entity : recognizePIIEntitiesResult.getNamedEntities()) {
        System.out.printf(
            "Personally Identifiable Information Entities Text: %s, Type: %s, Subtype: %s, Offset: %s, Length: %s, Score: %s.%n",
            entity.getText(),
            entity.getType(),
            entity.getSubtype() == null || entity.getSubtype().isEmpty() ? "N/A" : entity.getSubtype(),
            entity.getOffset(),
            entity.getLength(),
            entity.getScore());
    }
}
```

### <a name="output"></a>Выходные данные

```console
Personally Identifiable Information Entities
Text: 123-12-1234, Type: U.S. Social Security Number (SSN), Subtype: N/A, Offset: 33, Length: 11, Score: 0.85.
```

## <a name="entity-linking"></a>Связывание сущностей

Создайте функцию с именем `recognizeLinkedEntitiesExample()`, которая принимает созданный ранее клиент, и вызовите вложенную в нее функцию `recognizeLinkedEntities()`. Возвращенный объект `RecognizeLinkedEntitiesResult` будет содержать список `LinkedEntity`, если выполнение успешное, и значение `errorMessage` в противном случае. Так как связанные сущности уникально идентифицируются, вхождения одной и той же сущности группируются в объекте `LinkedEntity` как список объектов `LinkedEntityMatch`.

```java
static void recognizeLinkedEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analysed.
    String text = "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
            "to develop and sell BASIC interpreters for the Altair 8800. " +
            "During his career at Microsoft, Gates held the positions of chairman, " +
            "chief executive officer, president and chief software architect, " +
            "while also being the largest individual shareholder until May 2014.";
    
    RecognizeLinkedEntitiesResult recognizeLinkedEntitiesResult = client.recognizeLinkedEntities(text);

    System.out.printf("Linked Entities:%n");
    for (LinkedEntity linkedEntity : recognizeLinkedEntitiesResult.getLinkedEntities()) {
        System.out.printf("Name: %s, ID: %s, URL: %s, Data Source: %s.%n",
            linkedEntity.getName(),
            linkedEntity.getId(),
            linkedEntity.getUrl(),
            linkedEntity.getDataSource());
        System.out.printf("tMatches:%n");
        for (LinkedEntityMatch linkedEntityMatch : linkedEntity.getLinkedEntityMatches()) {
            System.out.printf("Text: %s, Offset: %s, Length: %s, Score: %.2f.%n",
                linkedEntityMatch.getText(),
                linkedEntityMatch.getOffset(),
                linkedEntityMatch.getLength(),
                linkedEntityMatch.getScore());
        }
    }
}
```

### <a name="output"></a>Выходные данные

```console
Linked Entities:
Name: Altair 8800, ID: Altair 8800, URL: https://en.wikipedia.org/wiki/Altair_8800, Data Source: Wikipedia.
tMatches:
Text: Altair 8800, Offset: 11, Length: 116, Score: 0.65.
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
tMatches:
Text: Bill Gates, Offset: 10, Length: 25, Score: 0.24.
Text: Gates, Offset: 5, Length: 161, Score: 0.24.
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
tMatches:
Text: Paul Allen, Offset: 10, Length: 40, Score: 0.17.
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
tMatches:
Text: Microsoft, Offset: 9, Length: 0, Score: 0.20.
Text: Microsoft, Offset: 9, Length: 150, Score: 0.20.
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
tMatches:
Text: April 4, Offset: 7, Length: 54, Score: 0.14.
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
tMatches:
Text: BASIC, Offset: 5, Length: 89, Score: 0.05.
```
## <a name="key-phrase-extraction"></a>Извлечение ключевой фразы

Создайте функцию с именем `extractKeyPhrasesExample()`, которая принимает созданный ранее клиент, и вызовите вложенную в нее функцию `extractKeyPhrases()`. Возвращенный объект `ExtractKeyPhraseResult` будет содержать список ключевых фраз, если выполнение успешное, и значение `errorMessage` в противном случае.

```java
static void extractKeyPhrasesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "My cat might need to see a veterinarian.";
    
    ExtractKeyPhraseResult keyPhraseResult = client.extractKeyPhrases(text);

    for (String keyPhrase : keyPhraseResult.getKeyPhrases()) {
        System.out.printf("Recognized Phrases: %s.%n", keyPhrase);
    }
}
```

### <a name="output"></a>Выходные данные

```console
Recognized Phrases: cat.
Recognized Phrases: veterinarian.
```
