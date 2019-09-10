---
title: Краткое руководство. Клиентская библиотека Анализа текста для Go | Документация Майкрософт
titleSuffix: Azure Cognitive Services
description: Получайте информацию и примеры кода, которые помогут вам приступить к работе с API Анализа текста в Azure Cognitive Services.
services: cognitive-services
author: laramume
manager: assafi
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: aahi
ms.openlocfilehash: 1ba2ec6b5c0c59be7b7264f7558fbb393adcc2d8
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142790"
---
# <a name="quickstart-text-analytics-client-library-for-go"></a>Краткое руководство. Клиентская библиотека Анализа текста для Go

Начало работы с клиентской библиотекой Анализа текста для Go. Выполните приведенные здесь действия, чтобы установить пакет и протестировать пример кода для выполнения базовых задач. 

Используйте клиентскую библиотеку Анализа текста для Go для выполнения следующих действий:

* Анализ мнений
* Определение языка
* Распознавание сущностей
* Извлечение ключевой фразы

[Справочная документация](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [Пакет (GitHub)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics) | [Примеры](https://github.com/Azure-Samples/cognitive-services-quickstart-code)

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
* Последняя версия [Go](https://golang.org/dl/).

## <a name="setting-up"></a>Настройка

### <a name="create-a-text-analytics-azure-resource"></a>Создание ресурса для Анализа текста в Azure 

Ресурсами Azure, на которые вы подписаны, будет представлено семейство служб Azure Cognitive Services. Создайте ресурс для Аналитики текста с помощью [портала Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) или [интерфейса командной строки Azure (CLI)](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) на локальном компьютере. Также можно:

* получить бесплатный [ключ пробной версии](https://azure.microsoft.com/try/cognitive-services/#decision) на 7 дней. (после регистрации он будет доступен на [веб-сайте Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/));
* Просмотреть этот ресурс на [портале Azure](https://portal.azure.com).

После получения ключа из своего ресурса или пробной подписки [задайте переменную среды](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) для ключа с именем `TEXT_ANALYTICS_SUBSCRIPTION_KEY`.

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

```golang
import (
    "context"
    "encoding/json"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics"
    "github.com/Azure/go-autorest/autorest"
    "log"
    "os"    
)
```

Добавьте в проект приведенные функции, так как большинство параметров и свойств в этом кратком руководстве ожидают указатели на строковые и логические значения.

```golang
// returns a pointer to the string value passed in.
func StringPointer(v string) *string {
    return &v
}

// returns a pointer to the bool value passed in.
func BoolPointer(v bool) *bool {
    return &v
}
```

В функции приложения `main` создайте переменные для конечной точки Azure вашего ресурса и ключа подписки. Получите эти значения из переменных среды TEXT_ANALYTICS_SUBSCRIPTION_KEY и TEXT_ANALYTICS_ENDPOINT. Если вы создали переменные среды после начала правки приложения, следует закрыть и повторно открыть редактор, интегрированную среду разработки или оболочку, которые использовались для доступа к этим переменным.

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```golang
var subscriptionKeyVar string = "TEXT_ANALYTICS_SUBSCRIPTION_KEY"
if "" == os.Getenv(subscriptionKeyVar) {
    log.Fatal("Please set/export the environment variable " + subscriptionKeyVar + ".")
}
var subscriptionKey string = os.Getenv(subscriptionKeyVar)
var endpointVar string = "TEXT_ANALYTICS_ENDPOINT"
if "" == os.Getenv(endpointVar) {
    log.Fatal("Please set/export the environment variable " + endpointVar + ".")
}
var endpoint string = os.Getenv(endpointVar)
```

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

В функции Main проекта создайте объект [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New). Передайте ключ в функцию [autorest.NewCognitiveServicesAuthorizer()](https://godoc.org/github.com/Azure/go-autorest/autorest#NewCognitiveServicesAuthorizer), откуда он будет передан в свойство клиента `authorizer`.

```golang
textAnalyticsClient := textanalytics.New(endpoint)
textAnalyticsClient.Authorizer = autorest.NewCognitiveServicesAuthorizer(subscriptionKey)
```

## <a name="sentiment-analysis"></a>Анализ мнений

Создайте функцию с именем `SentimentAnalysis()`, которая принимает созданный ранее клиент. Создайте список объектов [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput), содержащих документы для анализа. Каждый объект будет содержать атрибуты `id`, `Language` и `text`. Атрибут `text` содержит текст для анализа, `language` — это язык документа, а атрибут `id` может иметь любое значение. 

```golang
func SentimentAnalysis(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.MultiLanguageInput {
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("0"),
            Text:StringPointer("I had the best day of my life."),
        },
    }

    batchInput := textanalytics.MultiLanguageBatchInput{Documents:&inputDocuments}
}
```

В той же функции вызовите функцию клиента [Sentiment()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Sentiment) и получите результат. Затем выполните итерацию результатов и выведите идентификатор каждого документа и оценку тональности. Оценка, близкая к 0, указывает на негативную тональность, а оценка, близкая к 1, — на позитивную.

```golang
result, err := textAnalyticsclient.Sentiment(ctx, BoolPointer(false), &batchInput)
if err != nil { log.Fatal(err) }

batchResult := textanalytics.SentimentBatchResult{}
jsonString, _ := json.Marshal(result.Value)
json.Unmarshal(jsonString, &batchResult)

// Printing sentiment results
for _,document := range *batchResult.Documents {
    fmt.Printf("Document ID: %s\n", *document.ID)
    fmt.Printf("Sentiment Score: %f\n",*document.Score)
}

// Printing document errors
fmt.Println("Document Errors:")
for _,error := range *batchResult.Errors {
    fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
}
fmt.Println()
```

В функции Main проекта вызовите `SentimentAnalysis()`.

### <a name="output"></a>Выходные данные

```console
Document ID: 1 , Sentiment Score: 0.87
```

## <a name="language-detection"></a>Определение языка

Создайте функцию с именем `LanguageDetection()`, которая принимает созданный ранее клиент. Создайте список объектов [LanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#LanguageInput), содержащих документы для анализа. Каждый объект будет содержать атрибуты `id` и `text`. Атрибут `text` содержит текст для анализа, а параметр `id` может иметь любое значение. 

```golang
func LanguageDetection(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.LanguageInput {
        textanalytics.LanguageInput {
            ID:StringPointer("0"),
            Text:StringPointer("This is a document written in English."),
        },
    }

    batchInput := textanalytics.LanguageBatchInput{Documents:&inputDocuments}
}
```

В той же функции вызовите функцию клиента [DetectLanguage()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.DetectLanguage) и получите результат. Затем выполните итерацию результатов и выведите идентификатор каждого документа и обнаруженный язык.

```golang
result, err := textAnalyticsclient.DetectLanguage(ctx, BoolPointer(false), &batchInput)
if err != nil { log.Fatal(err) }

// Printing language detection results
for _,document := range *result.Documents {
    fmt.Printf("Document ID: %s " , *document.ID)
    fmt.Printf("Detected Languages with Score: ")
    for _,language := range *document.DetectedLanguages{
        fmt.Printf("%s %f,",*language.Name, *language.Score)
    }
    fmt.Println()
}

// Printing document errors
fmt.Println("Document Errors:")
for _,error := range *result.Errors {
    fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
}
fmt.Println()
```

В функции Main проекта вызовите `LanguageDetection()`.

### <a name="output"></a>Выходные данные

```console
Document ID: 0 Detected Languages with Score: English 1.000000
```

## <a name="entity-recognition"></a>Распознавание сущностей

Создайте функцию с именем `ExtractEntities()`, которая принимает созданный ранее клиент. Создайте список объектов [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput), содержащих документы для анализа. Каждый объект будет содержать атрибуты `id`, `language` и `text`. Атрибут `text` содержит текст для анализа, `language` — это язык документа, а атрибут `id` может иметь любое значение. 

```golang
func ExtractEntities(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.MultiLanguageInput {
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("0"),
            Text:StringPointer("Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."),
        },
    }

    batchInput := textanalytics.MultiLanguageBatchInput{Documents:&inputDocuments}
}
```

В той же функции вызовите функцию клиента [Entities()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Entities) и получите результат. Затем выполните итерацию результатов и выведите идентификатор каждого документа и оценку извлеченных сущностей.

```golang
    result, err := textAnalyticsclient.Entities(ctx, BoolPointer(false), &batchInput)
    if err != nil { log.Fatal(err) }

    // Printing extracted entities results
    for _,document := range *result.Documents {
        fmt.Printf("Document ID: %s\n" , *document.ID)
        fmt.Printf("\tExtracted Entities:\n")
        for _,entity := range *document.Entities{
            fmt.Printf("\t\tName: %s\tType: %s",*entity.Name, *entity.Type)
            if entity.SubType != nil{
                fmt.Printf("\tSub-Type: %s\n", *entity.SubType)
            }
            fmt.Println()
            for _,match := range *entity.Matches{
                fmt.Printf("\t\t\tOffset: %v\tLength: %v\tScore: %f\n", *match.Offset, *match.Length, *match.EntityTypeScore)
            }
        }
        fmt.Println()
    }

    // Printing document errors
    fmt.Println("Document Errors:")
    for _,error := range *result.Errors {
        fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
    }
    fmt.Println()
```

В функции Main проекта вызовите `ExtractEntities()`.

### <a name="output"></a>Выходные данные

```console
Document ID: 0
    Extracted Entities:
        Name: Microsoft Type: Organization
            Offset: 0   Length: 9   Score: 1.000000
        Name: Bill Gates    Type: Person
            Offset: 25  Length: 10  Score: 0.999847
        Name: Paul Allen    Type: Person
            Offset: 40  Length: 10  Score: 0.998841
        Name: April 4   Type: Other
            Offset: 54  Length: 7   Score: 0.800000
        Name: April 4, 1975 Type: DateTime  Sub-Type: Date

            Offset: 54  Length: 13  Score: 0.800000
        Name: BASIC Type: Other
            Offset: 89  Length: 5   Score: 0.800000
        Name: Altair 8800   Type: Other
            Offset: 116 Length: 11  Score: 0.800000
```

## <a name="key-phrase-extraction"></a>Извлечение ключевой фразы

Создайте функцию с именем `ExtractKeyPhrases()`, которая принимает созданный ранее клиент. Создайте список объектов [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput), содержащих документы для анализа. Каждый объект будет содержать атрибуты `id`, `language` и `text`. Атрибут `text` содержит текст для анализа, `language` — это язык документа, а атрибут `id` может иметь любое значение.

```golang
func ExtractKeyPhrases(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.MultiLanguageInput {
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("0"),
            Text:StringPointer("My cat might need to see a veterinarian."),
        },
    }

    batchInput := textanalytics.MultiLanguageBatchInput{Documents:&inputDocuments}
}
```

В той же функции вызовите функцию клиента [KeyPhrases()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.KeyPhrases) и получите результат. Затем выполните итерацию результатов и выведите идентификатор каждого документа и извлеченные ключевые фразы.

```golang
    result, err := textAnalyticsclient.KeyPhrases(ctx, BoolPointer(false), &batchInput)
    if err != nil { log.Fatal(err) }

    // Printing extracted key phrases results
    for _,document := range *result.Documents {
        fmt.Printf("Document ID: %s\n" , *document.ID)
        fmt.Printf("\tExtracted Key Phrases:\n")
        for _,keyPhrase := range *document.KeyPhrases{
            fmt.Printf("\t\t%s\n",keyPhrase)
        }
        fmt.Println()
    }

    // Printing document errors
    fmt.Println("Document Errors:")
    for _,error := range *result.Errors {
        fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
    }
    fmt.Println()
```

В функции Main проекта вызовите `ExtractKeyPhrases()`.

### <a name="output"></a>Выходные данные

```console
Document ID: 0
    Extracted Key Phrases:
        cat
        veterinarian
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите очистить и удалить подписку Cognitive Services, вы можете удалить ресурс или группу ресурсов. При этом удаляются все ресурсы, связанные с этой группой ресурсов.

* [Портал](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Интерфейс командной строки Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Дополнительная информация


> [!div class="nextstepaction"]
> [Text Analytics with Power BI](../tutorials/tutorial-power-bi-key-phrases.md) (Анализ текста с использованием Power BI)


* [Text Analytics overview](../overview.md) (Общие сведения об анализе текста)
* [Пример. Как определить тональность с помощью Анализа текста](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Распознавание сущностей](../how-tos/text-analytics-how-to-entity-linking.md)
* [Пример. Как определить язык с помощью Анализа текста](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Распознавание языка](../how-tos/text-analytics-how-to-language-detection.md)
* Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/cognitiveservices).
