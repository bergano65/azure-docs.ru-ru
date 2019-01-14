---
title: Краткое руководство. Преобразование текста в речь на .NET Core в службе "Речь"
titleSuffix: Azure Cognitive Services
description: В этом кратком руководстве вы узнаете, как преобразовать текст в речь с помощью REST API преобразования текста в речь. В это руководство включен образец текста, оформленный по стандарту разметки SSML (Speech Synthesis Markup Language). Этот стандарт позволяет выбрать голос и язык для речевого ответа.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.openlocfilehash: 6d463ca73fb533b8a44c2019a478f2df0162cb23
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53791451"
---
# <a name="quickstart-convert-text-to-speech-using-net-core"></a>Краткое руководство. Преобразование текста в речь с использованием .NET Core

В этом кратком руководстве вы узнаете, как преобразовать текст в речь с помощью .NET Core и REST API преобразования текста в речь. Пример текста в этом руководстве оформлен по стандарту разметки [SSML (Speech Synthesis Markup Language)](speech-synthesis-markup.md), который позволяет выбрать голос и язык для ответа.

Для этого краткого руководства требуется [учетная запись Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) с ресурсом службы "Речь". Если у вас нет учетной записи, можно использовать [бесплатную пробную версию](https://azure.microsoft.com/try/cognitive-services/), чтобы получить ключ подписки.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам понадобится:

* [Базовый пакет SDK для .NET](https://dotnet.microsoft.com/download)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) или любой другой редактор кода;
* ключ подписки Azure для службы "Речь".

## <a name="create-a-net-core-project"></a>Создание проекта .NET Core

Откройте командную строку или сеанс терминала и выполните следующие команды:

```console
dotnet new console -o tts-sample
cd tts-sample
```

С помощью первой команды создается консольное приложение .NET и каталог с именем `tts-sample`. Вторая команда позволяет изменить каталог для проекта.

## <a name="select-the-c-language-version"></a>Выбор версии языка C#

Для работы с этим кратким руководством потребуется C# версии 7.1 или более поздней. Есть несколько способов изменить версию C# для проекта. В этом руководстве мы покажем, как настроить файл `tts-sample.csproj`. Все доступные варианты, в том числе смена языка через Visual Studio, описаны в статье [Выбор версии языка C#](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version).

Откройте проект, а затем откройте `tts-sample.csproj`. Убедитесь, что для `LangVersion` установлено значение 7.1 или более высокое. Если для нужной версии языка не существует группы свойств, добавьте следующие строки:

```csharp
<PropertyGroup>
   <LangVersion>7.1</LangVersion>
</PropertyGroup>
```

Не забудьте сохранить изменения.

## <a name="add-required-namespaces-to-your-project"></a>Добавление обязательных пространств имен в проект

С помощью команды `dotnet new console`, которую вы выполнили ранее, был создан проект, включая `Program.cs`. Этот файл находится там, где будет размещаться код приложения. Откройте файл `Program.cs` и замените существующие инструкции using. Эти инструкции обеспечивают доступ ко всем типам, требуемым для создания и запуска примера приложения.

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.IO;
using System.Threading.Tasks;
```

## <a name="create-a-class-for-token-exchange"></a>Создание класса для обмена маркерами

REST API преобразования текста в речь требует предоставить маркер доступа для аутентификации. Чтобы получить маркер доступа, нужно выполнить обмен. Этот пример меняет ключ подписки службы "Речь" на маркер доступа через конечную точку `issueToken`.

В этом примере предполагается, что подписка службы "Речь" размещена в регионе "Западная часть США". Если вы используете другой регион, измените значение `FetchTokenUri`. См. [полный список регионов](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

```csharp
public class Authentication
{
    private string subscriptionKey;
    private string tokenFetchUri;

    public Authentication(string tokenFetchUri, string subscriptionKey)
    {
        if (string.IsNullOrWhiteSpace(tokenFetchUri))
        {
            throw new ArgumentNullException(nameof(tokenFetchUri));
        }
        if (string.IsNullOrWhiteSpace(subscriptionKey))
        {
            throw new ArgumentNullException(nameof(subscriptionKey));
        }
        this.tokenFetchUri = tokenFetchUri;
        this.subscriptionKey = subscriptionKey;
    }

    public async Task<string> FetchTokenAsync()
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", this.subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(this.tokenFetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null).ConfigureAwait(false);
            return await result.Content.ReadAsStringAsync().ConfigureAwait(false);
        }
    }
}
```

> [!NOTE]
> Дополнительные сведения о проверке подлинности см. в разделе [Проверка подлинности с помощью маркера доступа](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

## <a name="get-an-access-token-and-set-the-host-url"></a>Получение маркера доступа и настройка URL-адреса узла

Найдите строку `static void Main(string[] args)` и замените ее строкой `static async Task Main(string[] args)`.

Теперь скопируйте следующий код в метод main: Он выполняет сразу несколько действий, из которых самые важные — получение входных данных в текстовом виде и вызов функции `Authentication` для обмена ключа подписки на маркер доступа. Если что-то пойдет не так, на консоль выводится ошибка.

Не забудьте добавить ключ подписки перед запуском приложения.

```csharp
// Prompts the user to input text for TTS conversion
Console.Write("What would you like to convert to speech? ");
string text = Console.ReadLine();

// Gets an access token
string accessToken;
Console.WriteLine("Attempting token exchange. Please wait...\n");

// Add your subscription key here
// If your resource isn't in WEST US, change the endpoint
Authentication auth = new Authentication("https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken", "YOUR_SUBSCRIPTION_KEY");
try
{
    accessToken = await auth.FetchTokenAsync().ConfigureAwait(false);
    Console.WriteLine("Successfully obtained an access token. \n");
}
catch (Exception ex)
{
    Console.WriteLine("Failed to obtain an access token.");
    Console.WriteLine(ex.ToString());
    Console.WriteLine(ex.Message);
    return;
}
```

Затем настройте узел и маршрут для преобразования текста в речь:

```csharp
string host = "https://westus.tts.speech.microsoft.com/cognitiveservices/v1";
```

## <a name="build-the-ssml-request"></a>Создание запроса SSML

Текст отправляется в теле запроса `POST`. Синтаксис SSML позволяет указать голос и язык. В этом кратком руководстве мы будем использовать фрагмент SSML с языком `en-US` и голосом `ZiraRUS`. Давайте создадим SSML для такого запроса:

```csharp
string body = @"<speak version='1.0' xmlns='http://www.w3.org/2001/10/synthesis' xml:lang='en-US'>
              <voice name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>" +
              text + "</voice></speak>";
```

> [!NOTE]
> В этом примере используется голосовой шрифт `ZiraRUS`. См. [полный список предоставляемых корпорацией Майкрософт голосов и языков](language-support.md). Если вы хотите создать для своего бренда уникальный и узнаваемый голос, изучите статью [Создание настраиваемого голоса](how-to-customize-voice-font.md).

## <a name="instantiate-the-client-make-a-request-and-save-synthesized-audio-to-a-file"></a>Создание экземпляра клиента, выполнение запроса и сохранение созданного аудио в файл

В этом примере кода очень много действий. Давай кратко их перечислим.

* Создаются экземпляры клиента и запроса.
* Для метода HTTP задается тип `POST`.
* В запрос добавляются обязательные заголовки.
* Отправляется запрос и проверяется код состояния.
* Выполняется асинхронное чтение ответа и сохранение его в файл с именем sample.wav.

Скопируйте этот код в проект. Не забудьте заменить значение заголовка `User-Agent` правильным именем ресурса на портале Azure.

```csharp
using (var client = new HttpClient())
{
    using (var request = new HttpRequestMessage())
    {
        // Set the HTTP method
        request.Method = HttpMethod.Post;
        // Construct the URI
        request.RequestUri = new Uri(host);
        // Set the content type header
        request.Content = new StringContent(body, Encoding.UTF8, "application/ssml+xml");
        // Set additional header, such as Authorization and User-Agent
        request.Headers.Add("Authorization", "Bearer " + accessToken);
        request.Headers.Add("Connection", "Keep-Alive");
        // Update your resource name
        request.Headers.Add("User-Agent", "YOUR_RESOURCE_NAME");
        request.Headers.Add("X-Microsoft-OutputFormat", "riff-24khz-16bit-mono-pcm");
        // Create a request
        Console.WriteLine("Calling the TTS service. Please wait... \n");
        using (var response = await client.SendAsync(request).ConfigureAwait(false))
        {
            response.EnsureSuccessStatusCode();
            // Asynchronously read the response
            using (var dataStream = await response.Content.ReadAsStreamAsync().ConfigureAwait(false))
            {
                Console.WriteLine("Your speech file is being written to file...");
                using (var fileStream = new FileStream(@"sample.wav", FileMode.Create, FileAccess.Write, FileShare.Write))
                {
                    await dataStream.CopyToAsync(fileStream).ConfigureAwait(false);
                    fileStream.Close();
                }
                Console.WriteLine("\nYour file is ready. Press any key to exit.");
                Console.ReadLine();
            }
        }
    }
}
```

## <a name="run-the-sample-app"></a>Запуск примера приложения

Итак, все готово к запуску приложения преобразования текста в речь. В командной строке или сеансе терминала перейдите к каталогу проекта и выполните следующую команду:

```console
dotnet run
```

Если все пройдет успешно, готовый речевой файл будет сохранен в папке проекта. Воспроизведите его в любом проигрывателе мультимедиа.

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы закодировали свой ключ подписки в программе, обязательно удалите его после завершения работы с этим кратким руководством.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Справочник по API преобразования текста в речь](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api)

## <a name="see-also"></a>См. также

* [Создание настраиваемого голоса](how-to-customize-voice-font.md)
* [Запись примеров голоса для создания пользовательских голосовых моделей](record-custom-voice-samples.md)
