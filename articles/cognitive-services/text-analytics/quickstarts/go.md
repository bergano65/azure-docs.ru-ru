---
title: Краткое руководство. Вызов API анализа текста с использованием Go
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
ms.openlocfilehash: 5c97648bd11a506d3c818584ed7d82d0a12d2e2c
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70387481"
---
# <a name="quickstart-using-go-to-call-the-text-analytics-cognitive-service"></a>Краткое руководство. Использование Go для вызова API анализа текста Cognitive Services 
<a name="HOLTop"></a>

В этой статье содержатся сведения о том, как [распознавать язык](#Detect), [анализировать тональность](#SentimentAnalysis), [извлекать ключевые фразы](#KeyPhraseExtraction) и [идентифицировать связанные сущности](#Entities), используя  [API анализа текста](//go.microsoft.com/fwlink/?LinkID=759711) и Go.

Техническую документацию по API-интерфейсам см. в разделе [API definitions](//go.microsoft.com/fwlink/?LinkID=759346) (Определения API).

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]


<a name="Detect"></a>

## <a name="detect-language"></a>Определение языка

API распознавания языка определяет язык текстового документа, используя [метод определения языка](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7).

1. Создайте переменные среды `TEXT_ANALYTICS_SUBSCRIPTION_KEY` и `TEXT_ANALYTICS_ENDPOINT` для конечной точки своего ресурса Azure и ключа подписки. Если вы создали переменные среды после начала правки приложения, следует закрыть и повторно открыть редактор, интегрированную среду разработки или оболочку, которые использовались для доступа к этим переменным.
1. Создайте проект Go в любом редакторе кода.
1. Добавьте указанный ниже код.
1. Сохраните файл с расширением .go.
1. Откройте из корневой папки командную строку на компьютере, на котором установлен язык Go.
1. Скомпилируйте файл, например: `go build detect.go`.
1. Запустите файл, например: `go run detect.go`.

```golang
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "log"
    "net/http"
    "os"
    "strings"
    "time"
)

func main() {
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

    const uriPath = "/text/analytics/v2.1/languages"

    var uri = endpoint + uriPath

    data := []map[string]string{
        {"id": "1", "text": "This is a document written in English."},
        {"id": "2", "text": "Este es un document escrito en Español."},
        {"id": "3", "text": "这是一个用中文写的文件"},
    }

    documents, err := json.Marshal(&data)
    if err != nil {
        fmt.Printf("Error marshaling data: %v\n", err)
        return
    }

    r := strings.NewReader("{\"documents\": " + string(documents) + "}")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    resp, err := client.Do(req)
    if err != nil {
        fmt.Printf("Error on request: %v\n", err)
        return
    }
    defer resp.Body.Close()

    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        fmt.Printf("Error reading response body: %v\n", err)
        return
    }

    var f interface{}
    json.Unmarshal(body, &f)

    jsonFormatted, err := json.MarshalIndent(f, "", "  ")
    if err != nil {
        fmt.Printf("Error producing JSON: %v\n", err)
        return
    }
    fmt.Println(string(jsonFormatted))
}
```

## <a name="detect-language-response"></a>Результат определения языка

Успешный ответ возвращается в формате JSON, как показано в примере ниже.

```json

{
   "documents": [
      {
         "id": "1",
         "detectedLanguages": [
            {
               "name": "English",
               "iso6391Name": "en",
               "score": 1.0
            }
         ]
      },
      {
         "id": "2",
         "detectedLanguages": [
            {
               "name": "Spanish",
               "iso6391Name": "es",
               "score": 1.0
            }
         ]
      },
      {
         "id": "3",
         "detectedLanguages": [
            {
               "name": "Chinese_Simplified",
               "iso6391Name": "zh_chs",
               "score": 1.0
            }
         ]
      }
   ],
   "errors": [

   ]
}
```

<a name="SentimentAnalysis"></a>

## <a name="analyze-sentiment"></a>Анализ тональности

API анализа тональности определяет тональность набора текстовых записей с помощью [метода определения тональности](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9). В следующем примере выполняется оценка двух документов — на английском и на испанском языках.

1. Создайте переменные среды `TEXT_ANALYTICS_SUBSCRIPTION_KEY` и `TEXT_ANALYTICS_ENDPOINT` для конечной точки своего ресурса Azure и ключа подписки. Если вы создали переменные среды после начала правки приложения, следует закрыть и повторно открыть редактор, интегрированную среду разработки или оболочку, которые использовались для доступа к этим переменным.
1. Создайте проект Go в любом редакторе кода.
1. Добавьте указанный ниже код.
1. Сохраните файл с расширением .go.
1. Откройте из корневой папки командную строку на компьютере, на котором установлен язык Go.
1. Скомпилируйте файл, например: `go build sentiment.go`.
1. Запустите файл, например: `go run sentiment.go`.

```golang
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "log"
    "net/http"
    "os"
    "strings"
    "time"
)

func main() {
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

    const uriPath = "/text/analytics/v2.1/sentiment"

    var uri = endpoint + uriPath

    data := []map[string]string{
        {"id": "1", "language": "en", "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
        {"id": "2", "language": "es", "text": "Este ha sido un dia terrible, llegué tarde al trabajo debido a un accidente automobilistico."},
    }

    documents, err := json.Marshal(&data)
    if err != nil {
        fmt.Printf("Error marshaling data: %v\n", err)
        return
    }

    r := strings.NewReader("{\"documents\": " + string(documents) + "}")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    resp, err := client.Do(req)
    if err != nil {
        fmt.Printf("Error on request: %v\n", err)
        return
    }
    defer resp.Body.Close()

    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        fmt.Printf("Error reading response body: %v\n", err)
        return
    }

    var f interface{}
    json.Unmarshal(body, &f)

    jsonFormatted, err := json.MarshalIndent(f, "", "  ")
    if err != nil {
        fmt.Printf("Error producing JSON: %v\n", err)
        return
    }
    fmt.Println(string(jsonFormatted))
}
```

## <a name="analyze-sentiment-response"></a>Результат анализа тональности

Результат определяется как положительный, если его значение близко к 1,0, и отрицательный — если значение близко к 0,0.
Успешный ответ возвращается в формате JSON, как показано в примере ниже.

```json
{
   "documents": [
      {
         "score": 0.99984133243560791,
         "id": "1"
      },
      {
         "score": 0.024017512798309326,
         "id": "2"
      },
   ],
   "errors": [   ]
}
```

<a name="KeyPhraseExtraction"></a>

## <a name="extract-key-phrases"></a>Извлечение ключевых фраз

API извлечения ключевых фраз извлекает ключевые фразы из текстового документа с помощью [метода ключевых фраз](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6). Следующий пример извлекает ключевые фразы в документах на английском и испанском языках.

1. Создайте переменные среды `TEXT_ANALYTICS_SUBSCRIPTION_KEY` и `TEXT_ANALYTICS_ENDPOINT` для конечной точки своего ресурса Azure и ключа подписки. Если вы создали переменные среды после начала правки приложения, следует закрыть и повторно открыть редактор, интегрированную среду разработки или оболочку, которые использовались для доступа к этим переменным.
1. Создайте проект Go в любом редакторе кода.
1. Добавьте указанный ниже код.
1. Сохраните файл с расширением .go.
1. Откройте командную строку на компьютере, на котором установлен язык Go.
1. Скомпилируйте файл, например: `go build key-phrases.go`.
1. Запустите файл, например: `go run key-phrases.go`.

```golang
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "log"
    "net/http"
    "os"
    "strings"
    "time"
)

func main() {
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
    
    const uriPath = "/text/analytics/v2.1/keyPhrases"

    var uri = endpoint + uriPath

    data := []map[string]string{
        {"id": "1", "language": "en", "text": "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."},
        {"id": "2", "language": "es", "text": "Si usted quiere comunicarse con Carlos, usted debe de llamarlo a su telefono movil. Carlos es muy responsable, pero necesita recibir una notificacion si hay algun problema."},
        {"id": "3", "language": "en", "text": "The Grand Hotel is a new hotel in the center of Seattle. It earned 5 stars in my review, and has the classiest decor I've ever seen."},
    }

    documents, err := json.Marshal(&data)
    if err != nil {
        fmt.Printf("Error marshaling data: %v\n", err)
        return
    }

    r := strings.NewReader("{\"documents\": " + string(documents) + "}")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    resp, err := client.Do(req)
    if err != nil {
        fmt.Printf("Error on request: %v\n", err)
        return
    }
    defer resp.Body.Close()

    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        fmt.Printf("Error reading response body: %v\n", err)
        return
    }

    var f interface{}
    json.Unmarshal(body, &f)

    jsonFormatted, err := json.MarshalIndent(f, "", "  ")
    if err != nil {
        fmt.Printf("Error producing JSON: %v\n", err)
        return
    }
    fmt.Println(string(jsonFormatted))
}
```

## <a name="extract-key-phrases-response"></a>Результат извлечения ключевых фраз

Успешный ответ возвращается в формате JSON, как показано в примере ниже.

```json
{
   "documents": [
      {
         "keyPhrases": [
            "HDR resolution",
            "new XBox",
            "clean look"
         ],
         "id": "1"
      },
      {
         "keyPhrases": [
            "Carlos",
            "notificacion",
            "algun problema",
            "telefono movil"
         ],
         "id": "2"
      },
      {
         "keyPhrases": [
            "new hotel",
            "Grand Hotel",
            "review",
            "center of Seattle",
            "classiest decor",
            "stars"
         ],
         "id": "3"
      }
   ],
   "errors": [  ]
}
```

<a name="Entities"></a>

## <a name="identify-entities"></a>Определение сущностей

API сущностей определяет известные сущности в текстовом документе, используя [метод Entities](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/5ac4251d5b4ccd1554da7634). [Сущности](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-entity-linking) извлекают слова из текста (например, "США"), а затем предоставляют вам тип и/или ссылки на Википедию для таких слов. Слово "США" имеет тип `location`, ссылка на Википедию для него следующая: `https://en.wikipedia.org/wiki/United_States`.  Следующий пример определяет сущности в документах на английском языке.

1. Создайте переменные среды `TEXT_ANALYTICS_SUBSCRIPTION_KEY` и `TEXT_ANALYTICS_ENDPOINT` для конечной точки своего ресурса Azure и ключа подписки. Если вы создали переменные среды после начала правки приложения, следует закрыть и повторно открыть редактор, интегрированную среду разработки или оболочку, которые использовались для доступа к этим переменным.
1. Создайте проект Go в любом редакторе кода.
1. Добавьте указанный ниже код.
1. Сохраните файл с расширением .go.
1. Откройте командную строку на компьютере, на котором установлен язык Go.
1. Скомпилируйте файл, например: `go build entities.go`.
1. Запустите файл, например: `go run entities.go`.

```golang
package main

import (
    "encoding/json"
    "fmt"
    "io/ioutil"
    "log"
    "net/http"
    "os"
    "strings"
    "time"
)

func main() {
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
    
    const uriPath = "/text/analytics/v2.1/entities"

    var uri = endpoint + uriPath

    data := []map[string]string{
        {"id": "1", "language": "en", "text": "Microsoft is an It company."},
    }

    documents, err := json.Marshal(&data)
    if err != nil {
        fmt.Printf("Error marshaling data: %v\n", err)
        return
    }

    r := strings.NewReader("{\"documents\": " + string(documents) + "}")

    client := &http.Client{
        Timeout: time.Second * 2,
    }

    req, err := http.NewRequest("POST", uri, r)
    if err != nil {
        fmt.Printf("Error creating request: %v\n", err)
        return
    }

    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    resp, err := client.Do(req)
    if err != nil {
        fmt.Printf("Error on request: %v\n", err)
        return
    }
    defer resp.Body.Close()

    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        fmt.Printf("Error reading response body: %v\n", err)
        return
    }

    var f interface{}
    json.Unmarshal(body, &f)

    jsonFormatted, err := json.MarshalIndent(f, "", "  ")
    if err != nil {
        fmt.Printf("Error producing JSON: %v\n", err)
        return
    }
    fmt.Println(string(jsonFormatted))
}
```

## <a name="entity-extraction-response"></a>Ответ функции извлечения сущностей

Успешный ответ возвращается в формате JSON, как показано в примере ниже.

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
