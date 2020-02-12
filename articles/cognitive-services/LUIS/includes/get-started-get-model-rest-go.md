---
title: Получение модели с помощью вызова REST на Go
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: a153416a247ec3a38ec29e95b83fa919e765942b
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/02/2020
ms.locfileid: "76966821"
---
## <a name="prerequisites"></a>Предварительные требования

* Распознавание речи Azure — ключ ресурса из 32 символов и URL-адреса конечной точки для разработки. Создайте их с помощью [портала Azure](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) или [Azure CLI](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli).
* Импорт приложения [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) из репозитория GitHub cognitive-services-language-understanding.
* Идентификатор приложения LUIS для импортированного приложения TravelAgent. Идентификатор приложения отображается на панели мониторинга приложения.
* Идентификатор версии приложения, которое получает речевые фрагменты. Идентификатор по умолчанию — 0.1.
* язык программирования [GO](https://golang.org/);
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>Файл JSON с примерами высказываний.

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="change-model-programmatically"></a>Изменение модели программными средствами

На Go добавьте [API](https://aka.ms/luis-apim-v3-authoring) сущности машинного обучения в приложение.

1. Создайте файл с именем `predict.go`. Добавьте следующий код:

    ```go
    // dependencies
    package main
    import (
        "fmt"
        "net/http"
        "io/ioutil"
        "log"
        "strings"
    )

    // main function
    func main() {

        // NOTE: change to your app ID
        var appID = "YOUR-APP-ID"

        // NOTE: change to your authoring key
        var authoringKey = "YOUR-KEY"

        // NOTE: change to your authoring key's endpoint, for example, your-resource-name.api.cognitive.microsoft.com
        var endpoint = "YOUR-ENDPOINT"

        var version = "0.1"

        var exampleUtterances = `
        [
            {
              'text': 'go to Seattle today',
              'intentName': 'BookFlight',
              'entityLabels': [
                {
                  'entityName': 'Location::LocationTo',
                  'startCharIndex': 6,
                  'endCharIndex': 12
                }
              ]
            },
            {
                'text': 'a barking dog is annoying',
                'intentName': 'None',
                'entityLabels': []
            }
          ]
        `

        fmt.Println("add example utterances requested")
        addUtterance(authoringKey, appID, version, exampleUtterances, endpoint)

        fmt.Println("training selected")
        requestTraining(authoringKey, appID, version, endpoint)

        fmt.Println("training status selected")
        getTrainingStatus(authoringKey, appID, version, endpoint)
    }

    // get utterances from file and add to model
    func addUtterance(authoringKey string, appID string,  version string, labeledExampleUtterances string, endpoint string){

        var authoringUrl = fmt.Sprintf("https://%s/luis/authoring/v3.0-preview/apps/%s/versions/%s/examples", endpoint, appID, version)

        httpRequest("POST", authoringUrl, authoringKey, labeledExampleUtterances)
    }
    func requestTraining(authoringKey string, appID string,  version string, endpoint string){

        trainApp("POST", authoringKey, appID, version, endpoint)
    }
    func trainApp(httpVerb string, authoringKey string, appID string,  version string, endpoint string){

        var authoringUrl = fmt.Sprintf("https://%s/luis/authoring/v3.0-preview/apps/%s/versions/%s/train", endpoint, appID, version)

        httpRequest(httpVerb,authoringUrl, authoringKey, "")
    }
    func getTrainingStatus(authoringKey string, appID string, version string, endpoint string){

        trainApp("GET", authoringKey, appID, version, endpoint)
    }
    // generic HTTP request
    // includes setting header with authoring key
    func httpRequest(httpVerb string, url string, authoringKey string, body string){

        client := &http.Client{}

        request, err := http.NewRequest(httpVerb, url, strings.NewReader(body))
        request.Header.Add("Ocp-Apim-Subscription-Key", authoringKey)

        fmt.Println("body")
        fmt.Println(body)

        response, err := client.Do(request)
        if err != nil {
            log.Fatal(err)
        } else {
            defer response.Body.Close()
            contents, err := ioutil.ReadAll(response.Body)
            if err != nil {
                log.Fatal(err)
            }
            fmt.Println("   ", response.StatusCode)
            fmt.Println(string(contents))
        }
    }
    ```

1. Замените значения, начинающиеся с `YOUR-`, собственными значениями.

    |Сведения|Назначение|
    |--|--|
    |`YOUR-KEY`|Ключ для разработки (32 символа).|
    |`YOUR-ENDPOINT`| Конечная точка URL-адреса для разработки. Например, `replace-with-your-resource-name.api.cognitive.microsoft.com`. Имя ресурса задается при создании ресурса.|
    |`YOUR-APP-ID`| Идентификатор приложения LUIS. |

    Назначенные ключи и ресурсы отображаются на портале LUIS в разделе "Управление" на странице **ресурсов Azure**. Идентификатор приложения доступен в том же разделе "Управление" на странице **Параметры приложения**.

1. В командной строке в каталоге, в котором вы создали файл, введите следующую команду, чтобы скомпилировать файл Go:

    ```console
    go build model.go
    ```

1. Запустите приложение Go из командной строки, введя следующий текст:

    ```console
    go run model.go
    ```

## <a name="clean-up-resources"></a>Очистка ресурсов

По завершении работы с этим кратким руководством удалите файл из файловой системы.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Рекомендации для приложения](../luis-concept-best-practices.md)