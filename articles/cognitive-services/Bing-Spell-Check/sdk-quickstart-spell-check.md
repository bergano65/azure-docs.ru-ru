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
ms.date: 12/16/2019
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 3304cc36c37a548eabd3aa6ddc32255f3d09dffb
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93083734"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-sdk-for-c"></a>Краткое руководство. Проверка орфографии с помощью пакета SDK для API проверки орфографии Bing для C#

> [!WARNING]
> API Поиска Bing будут перенесены из Cognitive Services в службы Поиска Bing. С **30 октября 2020 г.** подготовку всех новых экземпляров Поиска Bing необходимо будет выполнять в соответствии с процедурой, описанной [здесь](https://aka.ms/cogsvcs/bingmove).
> API-интерфейсы Поиска Bing, подготовленные с помощью Cognitive Services, будут поддерживаться в течение следующих трех лет или до завершения срока действия вашего Соглашения Enterprise (в зависимости от того, какой период окончится раньше).
> Инструкции по миграции см. в статье о [службах Поиска Bing](https://aka.ms/cogsvcs/bingmigration).

В этом кратком руководстве вы узнаете, как начать проверку орфографии с помощью пакета SDK для API проверки орфографии Bing для C#. Так как REST API проверки орфографии Bing совместим с большинством языков программирования, пакет SDK обеспечивает простой способ интеграции службы в ваши приложения. Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/SpellCheck).

## <a name="application-dependencies"></a>Зависимости приложения

* Любой выпуск [Visual Studio 2017 или более поздней версии](https://visualstudio.microsoft.com/downloads/).
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
    var firstspellCheckResult = result.Body.FlaggedTokens.FirstOrDefault();
    
    if (firstspellCheckResult != null)
    {
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
    ```

## <a name="run-the-application"></a>Выполнение приложения

Выполните сборку проекта и запустите его. Если вы используете Visual Studio, то для отладки файла нажмите клавишу **F5**.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Создание одностраничного веб-приложения](tutorials/spellcheck.md)

- [Что такое API проверки орфографии Bing?](overview.md)
- [Справочник по пакету SDK C# для API Проверки орфографии Bing](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingspellcheck?view=azure-dotnet)
