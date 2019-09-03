---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 55ad3591a8c2e7d5de6d1efe255e0f3a4b3c11bd
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69907041"
---
[!INCLUDE [Prerequisites](prerequisites-csharp.md)]

[!INCLUDE [Setup and use environment variables](setup-env-variables.md)]

## <a name="create-a-net-core-project"></a>Создание проекта .NET Core

Откройте командную строку или сеанс терминала и выполните следующие команды:

```console
dotnet new console -o alternate-sample
cd alternate-sample
```

С помощью первой команды создается консольное приложение .NET и каталог с именем `alternate-sample`. Вторая команда позволяет изменить каталог для проекта.

Вам нужно будет установить модуль вручную. В каталоге проекта выполните следующую команду:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="add-required-namespaces-to-your-project"></a>Добавление обязательных пространств имен в проект

С помощью команды `dotnet new console`, которую вы выполнили ранее, был создан проект, включая `Program.cs`. Этот файл находится там, где будет размещаться код приложения. Откройте файл `Program.cs` и замените существующие инструкции using. Эти инструкции обеспечивают доступ ко всем типам, требуемым для создания и запуска примера приложения.

```csharp
using System;
using System.Net.Http;
using System.Text;
using Newtonsoft.Json;
```

## <a name="get-subscription-information-from-environment-variables"></a>Получение сведений о подписке из переменных среды

Добавьте следующие строки в класс `Program`. Эти строки считывают ключ подписки и конечную точку из переменных среды и выдают ошибку при возникновении каких-либо проблем.

```csharp
private const string key_var = "TRANSLATOR_TEXT_SUBSCRIPTION_KEY";
private static readonly string subscriptionKey = Environment.GetEnvironmentVariable(key_var);

private const string endpoint_var = "TRANSLATOR_TEXT_ENDPOINT";
private static readonly string endpoint = Environment.GetEnvironmentVariable(endpoint_var);

static Program()
{
    if (null == subscriptionKey)
    {
        throw new Exception("Please set/export the environment variable: " + key_var);
    }
    if (null == endpoint)
    {
        throw new Exception("Please set/export the environment variable: " + endpoint_var);
    }
}
// The code in the next section goes here.
```

## <a name="create-a-function-to-get-alternate-translations"></a>Создание функции для поиска вариантов перевода

В классе `Program` создайте функцию `AltTranslation`. Этот класс инкапсулирует код, используемый для вызова ресурса Dictionary, и выводит результат в консоль.

```csharp
static void AltTranslation()
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="construct-the-uri"></a>Создание универсального кода ресурса (URI)

Добавьте эти строки в функцию `AltTranslation`. Вы заметите, что кроме `api-version` были добавлены два дополнительных параметра. Эти параметры используются для настройки входных и выходных данных перевода. В этом примере мы используем английский и испанский языки: `en` и `es`.

```csharp
string route = "/dictionary/lookup?api-version=3.0";
static string params_ = "from=en&to=es";
static string uri = endpoint + path + params_;
```

Далее необходимо создать и сериализовать объект JSON, содержащий переводимый текст. Учтите, что в массив `body` можно передать несколько объектов.

```csharp
System.Object[] body = new System.Object[] { new { Text = @"Elephants" } };
var requestBody = JsonConvert.SerializeObject(body);
```

## <a name="instantiate-the-client-and-make-a-request"></a>Создание экземпляра клиента и выполнение запроса

С помощью этих строк создается экземпляр `HttpClient` и `HttpRequestMessage`:

```csharp
using (var client = new HttpClient())
using (var request = new HttpRequestMessage())
{
  // In the next few sections you'll add code to construct the request.
}
```

## <a name="construct-the-request-and-print-the-response"></a>Создание запроса и вывод ответа

При использовании `HttpRequestMessage` выполняются следующие операции:

* объявление метода HTTP;
* создание URI запроса;
* вставка текста запроса (сериализованный объект JSON);
* добавление требуемых заголовков;
* выполнение асинхронного запроса;
* Вывод ответа

Добавьте следующий код в `HttpRequestMessage`:

```csharp
// Set the method to POST
request.Method = HttpMethod.Post;

// Construct the full URI
request.RequestUri = new Uri(uri);

// Add the serialized JSON object to your request
request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");

// Add the authorization header
request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

// Send request, get response
var response = client.SendAsync(request).Result;
var jsonResponse = response.Content.ReadAsStringAsync().Result;

// Print the response
Console.WriteLine(PrettyPrint(jsonResponse));
Console.WriteLine("Press any key to continue.");
```

Добавьте `PrettyPrint`, чтобы добавить форматирование в ответ JSON:
```csharp
static string PrettyPrint(string s)
{
    return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
}
```

Если вы используете подписку на несколько служб Cognitive Services, необходимо также включить `Ocp-Apim-Subscription-Region` в параметрах запроса. [Дополнительные сведения об аутентификации с использованием подписки на несколько служб](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="put-it-all-together"></a>Сборка

Последний этап — вызов `AltTranslation()` в функции `Main`. Найдите `static void Main(string[] args)` и добавьте следующие строки:

```csharp
AltTranslation();
Console.WriteLine("Press any key to continue.");
Console.ReadKey();
```

## <a name="run-the-sample-app"></a>Запуск примера приложения

Теперь все готово к запуску примера приложения. В командной строке или сеансе терминала перейдите к каталогу проекта и выполните следующую команду:

```console
dotnet run
```

## <a name="sample-response"></a>Пример ответа

```json
[
    {
        "displaySource": "elephants",
        "normalizedSource": "elephants",
        "translations": [
            {
                "backTranslations": [
                    {
                        "displayText": "elephants",
                        "frequencyCount": 1207,
                        "normalizedText": "elephants",
                        "numExamples": 5
                    }
                ],
                "confidence": 1.0,
                "displayTarget": "elefantes",
                "normalizedTarget": "elefantes",
                "posTag": "NOUN",
                "prefixWord": ""
            }
        ]
    }
]
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите из исходного кода примера приложения все конфиденциальные сведения, например ключи подписки.

## <a name="next-steps"></a>Дополнительная информация

Просмотрите справочник по API, чтобы составить представление обо всех возможностях API "Перевод текстов".

> [!div class="nextstepaction"]
> [Справочник по API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
