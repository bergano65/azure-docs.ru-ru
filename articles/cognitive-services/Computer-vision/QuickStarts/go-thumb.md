---
title: Краткое руководство. Создание эскиза — REST, Go
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве вы узнаете, как создать эскиз изображения с помощью API компьютерного зрения в Go.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 49225fe535e8e4f05874444b285ced41a9085894
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2020
ms.locfileid: "91969200"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-with-go"></a>Краткое руководство. Создание эскиза с помощью REST API "Компьютерное зрение" и Go

В этом кратком руководстве описано, как создать эскиз изображения с помощью REST API Компьютерного зрения. Вы можете указать нужную высоту и ширину, которые могут отличаться в пропорции от исходного изображения. API компьютерного зрения использует интеллектуальную обрезку для идентификации интересующей области и создания координат обрезки для этой области.

## <a name="prerequisites"></a>Предварительные требования

* подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/cognitive-services/).
* [GO](https://golang.org/dl/)
* Получив подписку Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="создайте ресурс Компьютерного зрения"  target="_blank">create a Computer Vision resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> на портале Azure, чтобы получить ключ и конечную точку. После развертывания щелкните **Перейти к ресурсам**.
    * Для подключения приложения к API Компьютерного зрения потребуется ключ и конечная точка из созданного ресурса. Ключ и конечная точка будут вставлены в приведенный ниже код в кратком руководстве.
    * Используйте бесплатную ценовую категорию (`F0`), чтобы опробовать службу, а затем выполните обновление до платного уровня для рабочей среды.
* [Создайте переменные среды](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) для ключа и URL-адреса конечной точки с именами `COMPUTER_VISION_SUBSCRIPTION_KEY` и `COMPUTER_VISION_ENDPOINT` соответственно.


## <a name="create-and-run-the-sample"></a>Создание и выполнение примера кода

Чтобы создать и запустить пример, сделайте следующее.

1. Скопируйте приведенный ниже код в текстовый редактор.
1. При необходимости замените значение `imageUrl` URL-адресом другого изображения, из которого вы хотите создать эскиз.
1. Сохраните код как файл с расширением `.go`. Например, `get-thumbnail.go`.
1. Откройте окно командной строки.
1. В командной строке запустите команду `go build` для компиляции пакета из файла. Например, `go build get-thumbnail.go`.
1. Запустите скомпилированный пакет в командной строке. Например, `get-thumbnail`.

```go
package main

import (
    "bytes"
    "fmt"
    "io/ioutil"
    "io"
    "log"
    "net/http"
    "os"
    "strings"
    "time"
)

func main() {
    // Add your Computer Vision subscription key and endpoint to your environment variables.
    subscriptionKey := os.Getenv("COMPUTER_VISION_SUBSCRIPTION_KEY")
    endpoint := os.Getenv("COMPUTER_VISION_ENDPOINT")

    uriBase := endpoint + "vision/v3.1/generateThumbnail"
    const imageUrl = "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

    const params = "?width=100&height=100&smartCropping=true"
    uri := uriBase + params
    const imageUrlEnc = "{\"url\":\"" + imageUrl + "\"}"

    reader := strings.NewReader(imageUrlEnc)

    // Create the HTTP client
    client := &http.Client{
        Timeout: time.Second * 2,
    }

    // Create the POST request, passing the image URL in the request body
    req, err := http.NewRequest("POST", uri, reader)
    if err != nil {
        panic(err)
    }

    // Add headers
    req.Header.Add("Content-Type", "application/json")
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

    // Send the request and retrieve the response
    resp, err := client.Do(req)
    if err != nil {
        panic(err)
    }

    defer resp.Body.Close()

    // Read the response body.
    // Note, data is a byte array
    data, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        panic(err)
    }
    
    // Convert byte[] to io.Reader type
    readerThumb := bytes.NewReader(data)

    // Write the image binary to file
    file, err := os.Create("thumb_local.png")
    if err != nil { log.Fatal(err) }
    defer file.Close()
    _, err = io.Copy(file, readerThumb)
    if err != nil { log.Fatal(err) }

    fmt.Println("The thunbnail from local has been saved to file.")
    fmt.Println()
}
```

## <a name="examine-the-response"></a>Изучите ответ.

В случае успешного выполнения ответ будет содержать данные двоичного файла эскиза изображения. Если запрос завершается сбоем, ответ будет содержать код ошибки и сообщение с описанием проблемы.

## <a name="next-steps"></a>Дальнейшие действия

Ознакомьтесь с API Компьютерного зрения, чтобы анализировать изображения, обнаруживать знаменитостей и достопримечательности, создавать эскизы, извлекать печатный и рукописный текст. Для быстрых экспериментов с API компьютерного зрения можно использовать [открытую консоль тестирования API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Обзор API компьютерного зрения](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
