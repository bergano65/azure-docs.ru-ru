---
title: Краткое руководство. Клиентская библиотека Автозаполнения Bing для Go | Документация Майкрософт
description: Начните работать с клиентской библиотекой Автозаполнения Bing для Go, чтобы получать предложения на основе частей строк запросов.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: quickstart
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: e826d69bbf0f94d02799e9b93b26db8d4cbbba54
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75451164"
---
# <a name="quickstart-bing-autosuggest-client-library-for-go"></a>Краткое руководство. Клиентская библиотека Автозаполнения Bing для Go

Эта статья поможет вам начать работу с клиентской библиотекой Автозаполнения Bing для Go. Здесь приведены действия по установке библиотеки и тестированию примеров кода для выполнения базовых задач. 

Используйте клиентскую библиотеку Автозаполнения Bing для Go, чтобы получать варианты поиска на основе частей строк запросов.

[Справочная документация](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/autosuggest) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics) | [Пример кода](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/BingAutoSuggest/BingAutoSuggestQuickstart.go)

## <a name="prerequisites"></a>предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
* Последняя версия [Go](https://golang.org/dl/).

## <a name="setting-up"></a>Настройка

### <a name="create-an-azure-resource"></a>Создание ресурса Azure 

Начните использовать клиентскую библиотеку Автозаполнения Bing, создав ресурс Azure. Выберите ниже ресурс подходящего типа.

[!INCLUDE [cognitive-services-bing-autosuggest-signup-requirements](../../../../includes/cognitive-services-bing-autosuggest-signup-requirements.md)]

### <a name="create-an-environment-variable"></a>Создание переменной среды

>[!NOTE]
> Конечные точки для ресурсов не из пробной версии, созданных после 1июля 2019 г., поддерживают пользовательский формат поддомена, показанный ниже. Дополнительные сведения и полный список региональных конечных точек см. в статье [Custom subdomain names for Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains) (Пользовательские имена поддоменов для Cognitive Services). 

Используя ключ и конечную точку из созданного ресурса, создайте две переменные среды для проверки подлинности:
<!-- replace the below variable names with the names expected in the code sample.-->
* `AUTOSUGGEST_SUBSCRIPTION_KEY` — ключ ресурса для проверки подлинности запросов.
* `AUTOSUGGEST_ENDPOINT` — конечная точка ресурса для отправки запросов API. Она должна выглядеть так: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Используйте инструкции для своей операционной системы.
<!-- replace the below endpoint and key examples -->
#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx BING_AUTOSUGGEST_SUBSCRIPTION_KEY <replace-with-your-autosuggest-api-key>
setx BING_AUTOSUGGEST_ENDPOINT <replace-with-your-autosuggest-api-endpoint>
```

Добавив переменную среды, перезапустите окно консоли.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

После добавления переменной среды запустите `source ~/.bashrc` из окна консоли, чтобы применить изменения.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Измените `.bash_profile` и добавьте переменную среды:

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

После добавления переменной среды запустите `source .bash_profile` из окна консоли, чтобы применить изменения.
***

### <a name="create-a-new-go-project"></a>Создание проекта Go

В окне консоли (cmd, PowerShell, терминал или Bash) создайте новую рабочую область для проекта Go и перейдите в нее. Рабочая область будет содержать три папки: 

* каталог **src** содержит исходный код и пакеты. Все пакеты, установленные с помощью команды `go get`, будут находиться именно здесь;
* каталог **pkg** содержит скомпилированные объекты пакета Go. Все эти файлы имеют расширение `.a`;
* каталог **bin** содержит двоичные исполняемые файлы, создаваемые при запуске `go install`.

> [!TIP]
> Дополнительные сведения о структуре рабочей области Go вы найдете [здесь](https://golang.org/doc/code.html#Workspaces). В этом руководством содержатся сведения о настройке `$GOPATH` и `$GOROOT`.

Создайте рабочую область с именем `my-app` и необходимые подкаталоги для `src`, `pkg` и `bin`:

```
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

### <a name="install-the-client-library-for-go"></a>Установка клиентской библиотеки для Go

Теперь установите клиентскую библиотеку для Go: 

```bash
$ go get -u <library-location-or-url>
```

Или, если вы используете dep, выполните в репозитории такую команду:

```bash
$ dep ensure -add <library-location-or-url>
```

### <a name="create-your-go-application"></a>Создание приложения Go

Далее создадим файл с именем `src/sample-app.go`:

```bash
$ cd src
$ touch sample-app.go
```

Откройте файл `sample-app.go`, добавьте пакет и импортируйте следующие библиотеки:

```Go
package main

import (
    "context"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/autosuggest"
    "github.com/Azure/go-autorest/autorest"
    "log"
    "os"
)
```

Создайте функцию с именем `main`. Затем создайте переменные среды для ключа и конечной точки Автозаполнения Bing.

```go
func main() {
    // Add your Bing Autosuggest subscription key to your environment variables.
    if "" == os.Getenv("BING_AUTOSUGGEST_SUBSCRIPTION_KEY") {
        log.Fatal("Please set/export the environment variable BING_AUTOSUGGEST_SUBSCRIPTION_KEY.")
    }
    // Add your Bing Autosuggest endpoint to your environment variables.
    var subscription_key string = os.Getenv("BING_AUTOSUGGEST_ENDPOINT")
    if "" == os.Getenv("BING_AUTOSUGGEST_ENDPOINT") {
        log.Fatal("Please set/export the environment variable BING_AUTOSUGGEST_ENDPOINT.")
    }
    var endpoint string = os.Getenv("BING_AUTOSUGGEST_ENDPOINT")
}
```

## <a name="code-examples"></a>Примеры кода

В этих примерах кода показано, как выполнять основные задачи с использованием клиентской библиотеки Автозаполнения Bing для Go:

* [аутентификация клиента](#authenticate-the-client);
* [отправка запроса API](#send-an-api-request).

## <a name="authenticate-the-client"></a>Аутентификация клиента

> [!NOTE] 
> В этом кратком руководстве предполагается, что вы уже [создали переменную среды](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) для ключа Автозаполнения Bing, который называется `BING_AUTOSUGGEST_SUBSCRIPTION_KEY`, и конечной точки `BING_AUTOSUGGEST_ENDPOINT`.

В функции `main()` создайте экземпляр клиента с использованием конечной точки и ключа. 

```go
// Get the context, which is required by the SDK methods.
ctx := context.Background()

client := autosuggest.New()
// Set the subscription key on the client.
client.Authorizer = autorest.NewCognitiveServicesAuthorizer(subscription_key)
client.Endpoint = endpoint
```

## <a name="send-an-api-request"></a>Отправка запроса API

В том же методе используйте метод [AutoSuggestMethodAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclientextensions.autosuggestmethodasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Search_AutoSuggest_AutoSuggestClientExtensions_AutoSuggestMethodAsync_Microsoft_Azure_CognitiveServices_Search_AutoSuggest_IAutoSuggestClient_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_Collections_Generic_IList_System_String__System_Threading_CancellationToken_) клиента для отправки запроса в Bing. Затем выполните итерацию предложений по ответу [Suggestions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.models.suggestions?view=azure-dotnet) и выведите первое предложение.

```Go
// This should return the query suggestion "xbox."
result, err := client.AutoSuggest (ctx, "xb", "", "", "", "", "", "", "", "", "", "", []autosuggest.ResponseFormat{"Json"})
if nil != err {
    log.Fatal(err)
}

groups := *result.SuggestionGroups
if len(groups) > 0 {
    group, _ := groups[0].AsSuggestionsSuggestionGroup()
    fmt.Printf ("First suggestion group: %s\n", (*group).Name)
    suggestions := *(*group).SearchSuggestions
    if len(suggestions) > 0 {
        fmt.Println("First suggestion:")
        fmt.Printf("Query: %s\n", *suggestions[0].Query)
        fmt.Printf("Display text: %s\n", *suggestions[0].DisplayText)
    } else {
        fmt.Println("No suggestions found in this group.")
    }
} else {
    fmt.Println("No suggestions found.")
}
```

## <a name="run-the-application"></a>Выполнение приложения

Запустите приложение Go, выполнив команду `go run [arguments]` из каталога приложения.

```Go
go run sample-app.go
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите очистить и удалить подписку Cognitive Services, вы можете удалить ресурс или группу ресурсов. При этом удаляются все ресурсы, связанные с ней.

* [Портал](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Руководство по API автозаполнения Bing](../tutorials/autosuggest.md)

## <a name="see-also"></a>См. также раздел

- [Что такое API автозаполнения Bing?](../get-suggested-search-terms.md)
- [Справочник по API автозаполнения Bing версии 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
