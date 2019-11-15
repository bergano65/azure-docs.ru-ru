---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 10/28/2019
ms.author: aahi
ms.openlocfilehash: 9b148e413bc7dc6af7eff064e5ff3ec6385cfef4
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73750135"
---
[Справочная документация](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [Пакет (GitHub)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics) | [Примеры](https://github.com/Azure-Samples/cognitive-services-quickstart-code)

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
* Последняя версия [Go](https://golang.org/dl/).

## <a name="setting-up"></a>Настройка

### <a name="create-a-text-analytics-azure-resource"></a>Создание ресурса для Анализа текста в Azure 

[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

### <a name="create-a-new-go-project"></a>Создание проекта Go

В окне консоли (cmd, PowerShell, терминал или Bash) создайте новую рабочую область для проекта Go и перейдите в нее. Рабочая область будет содержать три папки: 

* каталог **src** содержит исходный код и пакеты. Все пакеты, установленные с помощью команды `go get`, будут находиться именно здесь;
* каталог **pkg** содержит скомпилированные объекты пакета Go. Все эти файлы имеют расширение `.a`;
* каталог **bin** содержит двоичные исполняемые файлы, создаваемые при запуске `go install`.

> [!TIP]
> Дополнительные сведения о структуре рабочей области Go вы найдете [здесь](https://golang.org/doc/code.html#Workspaces). В этом руководством содержатся сведения о настройке `$GOPATH` и `$GOROOT`.

Создайте рабочую область с именем `my-app` и необходимые подкаталоги для `src`, `pkg` и `bin`:

```console
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

### <a name="install-the-text-analytics-client-library-for-go"></a>Установка клиентской библиотеки Анализа текста для Go

Установите клиентскую библиотеку для Go: 

```console
$ go get -u <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

Или, если вы используете dep, выполните в репозитории такую команду:

```console
$ dep ensure -add <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

### <a name="create-your-go-application"></a>Создание приложения Go

Теперь создайте файл с именем `src/quickstart.go`:

```bash
$ cd src
$ touch quickstart.go
```

Откройте `quickstart.go` в любой среде разработки или текстовом редакторе. Затем добавьте пакет и импортируйте следующие библиотеки.

[!code-go[Import statements](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=imports)]

## <a name="object-model"></a>Объектная модель 

Клиент Анализа текста представляет собой объект [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New), который выполняет проверку подлинности в Azure с использованием ключа. Этот клиент предоставляет несколько методов для анализа текста по отдельным строкам или пакетам строк. 

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


Создайте в новой функции переменные для конечной точки ресурса Azure и ключа подписки. Эти значения можно получить из переменных среды `TEXT_ANALYTICS_SUBSCRIPTION_KEY` и `TEXT_ANALYTICS_ENDPOINT`. Если вы создали переменные среды после того как изменили приложение, следует закрыть и повторно открыть редактор, интегрированную среду разработки или оболочку, используемые для доступа к этим переменным.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

Создайте новый объект [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New). Передайте ключ в функцию [autorest.NewCognitiveServicesAuthorizer()](https://godoc.org/github.com/Azure/go-autorest/autorest#NewCognitiveServicesAuthorizer), откуда он будет передан в свойство клиента `authorizer`.

[!code-go[Client creation ](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=client)]

## <a name="sentiment-analysis"></a>Анализ мнений

Создайте функцию с именем `SentimentAnalysis()`, а затем создайте клиент с помощью ранее созданного метода `GetTextAnalyticsClient()`. Создайте список объектов [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput), содержащих документы для анализа. Каждый объект будет содержать атрибуты `id`, `Language` и `text`. Атрибут `text` содержит текст для анализа, `language` — это язык документа, а атрибут `id` может иметь любое значение. 

Вызовите функцию клиента [sentiment()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Sentiment) и получите результат. Затем выполните итерацию результатов и выведите идентификатор каждого документа и оценку тональности. Оценка, близкая к 0, указывает на негативную тональность, а оценка, близкая к 1, — на позитивную.

[!code-go[Sentiment analysis sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=sentimentAnalysis)]

Вызовите `SentimentAnalysis()` в проекте.

### <a name="output"></a>Выходные данные

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

## <a name="language-detection"></a>Определение языка

Создайте функцию с именем `LanguageDetection()`, а затем создайте клиент с помощью ранее созданного метода `GetTextAnalyticsClient()`. Создайте список объектов [LanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#LanguageInput), содержащих документы для анализа. Каждый объект будет содержать атрибуты `id` и `text`. Атрибут `text` содержит текст для анализа, а параметр `id` может иметь любое значение. 

Вызовите функцию клиента [DetectLanguage()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.DetectLanguage) и получите результат. Затем выполните итерацию результатов и выведите идентификатор каждого документа и обнаруженный язык.

[!code-go[Language detection sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=languageDetection)]

Вызовите `LanguageDetection()` в проекте.

### <a name="output"></a>Выходные данные

```console
Document ID: 0 , Language: English 
Document ID: 1 , Language: Spanish
Document ID: 2 , Language: Chinese_Simplified
```

## <a name="entity-recognition"></a>Распознавание сущностей

Создайте функцию с именем `ExtractEntities()`, а затем создайте клиент с помощью ранее созданного метода `GetTextAnalyticsClient()`. Создайте список объектов [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput), содержащих документы для анализа. Каждый объект будет содержать атрибуты `id`, `language` и `text`. Атрибут `text` содержит текст для анализа, `language` — это язык документа, а атрибут `id` может иметь любое значение. 

Вызовите функцию клиента [Entities()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Entities) и получите результат. Затем выполните итерацию результатов и выведите идентификатор каждого документа и оценку извлеченных сущностей.

[!code-go[entity recognition sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=entityRecognition)]

Вызовите `ExtractEntities()` в проекте.

### <a name="output"></a>Выходные данные

```console
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

## <a name="key-phrase-extraction"></a>Извлечение ключевой фразы

Создайте функцию с именем `ExtractKeyPhrases()`, а затем создайте клиент с помощью ранее созданного метода `GetTextAnalyticsClient()`. Создайте список объектов [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput), содержащих документы для анализа. Каждый объект будет содержать атрибуты `id`, `language` и `text`. Атрибут `text` содержит текст для анализа, `language` — это язык документа, а атрибут `id` может иметь любое значение.

Вызовите функцию клиента [KeyPhrases()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.KeyPhrases) и получите результат. Затем выполните итерацию результатов и выведите идентификатор каждого документа и извлеченные ключевые фразы.

[!code-go[key phrase extraction sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=keyPhrases)]

Вызовите `ExtractKeyPhrases()` в проекте.

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
                veterinarian
Document ID: 4
         Key phrases:
                fútbol
```
