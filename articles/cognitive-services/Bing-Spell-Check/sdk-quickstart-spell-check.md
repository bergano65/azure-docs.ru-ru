---
title: Краткое руководство. Проверка орфографии с помощью пакета SDK для API проверки орфографии Bing для C#
titleSuffix: Azure Cognitive Services
description: Приступите к работе с REST API проверки орфографии Bing для проверки орфографии и грамматики.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 09/13/2019
ms.author: aahi
ms.openlocfilehash: 74697d69fbeb9072f839f0b6d49c010c5a7a7a05
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996714"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-sdk-for-c"></a>Краткое руководство. Проверка орфографии с помощью пакета SDK для API проверки орфографии Bing для C#

В этом кратком руководстве вы узнаете, как начать проверку орфографии с помощью пакета SDK для API проверки орфографии Bing для C#. Так как REST API проверки орфографии Bing совместим с большинством языков программирования, пакет SDK обеспечивает простой способ интеграции службы в ваши приложения. Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/SpellCheck).

## <a name="application-dependencies"></a>Зависимости приложения

* Любой выпуск [Visual Studio 2017 или более поздней версии](https://visualstudio.microsoft.com/downloads/).
* [Пакет NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.SpellCheck) для API проверки орфографии Bing.

Чтобы добавить пакет SDK API "Проверка орфографии Bing" в проект, перейдите к параметру **Manage NuGet Packages** (Управление пакетами NuGet) в **обозревателе решений** в Visual Studio. Добавьте пакет `Microsoft.Azure.CognitiveServices.Language.SpellCheck`. При установке этого пакета также устанавливаются следующие зависимости:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Создание и инициализация приложения

1. Создайте консольное решение C# в Visual Studio. Затем добавьте следующий оператор `using`.
    
    ```csharp
    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck.Models;
    ```

2. Создайте новый класс. Затем создайте асинхронную функцию с именем `SpellCheckCorrection()`, которая принимает ключ подписки и отправляет запрос на проверку орфографии.

3. Создайте экземпляр клиента, создав новый объект `ApiKeyServiceClientCredentials`. 

    ```csharp
    public static class SpellCheckSample{
        public static async Task SpellCheckCorrection(string key){
            var client = new SpellCheckClient(new ApiKeyServiceClientCredentials(key));
        }
        //...
    }
    ```

## <a name="send-the-request-and-read-the-response"></a>Отправка запроса и считывание ответа

1. В рамках созданной выше функции выполните следующие шаги. Отправьте запрос на проверку орфографии в клиент. Добавьте текст для проверки в параметр `text` и установите режим `proof`.  
    
    ```csharp
    var result = await client.SpellCheckerWithHttpMessagesAsync(text: "Bill Gatas", mode: "proof");
    ```

2. Получите первый результат проверки орфографии, если он имеется. Выведите первое возвращенное слово (маркер) с ошибкой, тип маркера и количество предложений.

    ```csharp
    if (firstspellCheckResult != null){
        var firstspellCheckResult = result.Body.FlaggedTokens.FirstOrDefault();
    
        Console.WriteLine("SpellCheck Results#{0}", result.Body.FlaggedTokens.Count);
        Console.WriteLine("First SpellCheck Result token: {0} ", firstspellCheckResult.Token);
        Console.WriteLine("First SpellCheck Result Type: {0} ", firstspellCheckResult.Type);
        Console.WriteLine("First SpellCheck Result Suggestion Count: {0} ", firstspellCheckResult.Suggestions.Count);
    }
    ```

3. Получите первое предложенное исправление, если таковое имеется. Выведите оценку предложения и предложенное слово. 

    ```csharp
            var suggestions = firstspellCheckResult.Suggestions;

            if (suggestions?.Count > 0)
            {
                var firstSuggestion = suggestions.FirstOrDefault();
                Console.WriteLine("First SpellCheck Suggestion Score: {0} ", firstSuggestion.Score);
                Console.WriteLine("First SpellCheck Suggestion : {0} ", firstSuggestion.Suggestion);
            }
   }

## Next steps

> [!div class="nextstepaction"]
> [Create a single page web-app](tutorials/spellcheck.md)

- [What is the Bing Spell Check API?](overview.md)
- [Bing Spell Check C# SDK reference guide](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingspellcheck?view=azure-dotnet)