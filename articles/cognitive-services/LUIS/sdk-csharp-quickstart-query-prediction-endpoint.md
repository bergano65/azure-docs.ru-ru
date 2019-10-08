---
title: Краткое руководство. Запрос конечной точки прогнозирования с помощью пакета SDK для C# — LUIS
titleSuffix: Azure Cognitive Services
description: Использование пакета SDK для C# для отправки речевого фрагмента пользователя LUIS и получения прогноза.
author: diberry
manager: nitinme
ms.service: cognitive-services
services: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 4d7c02d8cf37f155af1fd9c0d5de7fa8ec948c4b
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703128"
---
# <a name="quickstart-query-v2-prediction-endpoint-with-c-net-sdk"></a>Краткое руководство. Запрос конечной точки прогнозирования версии 2 с помощью пакета SDK .NET для C#

Используйте пакет SDK для .NET, найденный на [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/), для отправки речевого фрагмента пользователя в Распознавание речи (LUIS) и получения прогноза намерения пользователя. 

В этом кратком руководстве отправляется речевой фрагмент пользователя, такой как `turn on the bedroom light`, общедоступному приложению LUIS, затем приходит прогноз и отображается намерение с максимальной оценкой `HomeAutomation.TurnOn` и сущностью `HomeAutomation.Room`, найденной в речевом фрагменте. 

## <a name="prerequisites"></a>Предварительные требования

* [выпуск Visual Studio Community 2017](https://visualstudio.microsoft.com/vs/community/);
* язык программирования C# (входит в состав выпуска Visual Studio Community 2017);
* идентификатор общедоступного приложения: df67dcdb-c37d-46af-88e1-8b97951ca1c2.

> [!Note]
> Полное решение доступно в репозитории GitHub [cognitive-services-language-understanding](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/sdk-quickstarts/c%23/UsePredictionRuntime).

Ищете дополнительную документацию?

 * [Справочная документация по пакету SDK](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet)


## <a name="get-cognitive-services-or-language-understanding-key"></a>Получение ключа Cognitive Services и Распознавания речи

Для использования общедоступного приложения для автоматизации дома требуется допустимый ключ для прогнозов конечной точки. Вы можете использовать ключ Cognitive Services (созданный ниже с помощью Azure CLI), который является допустимым для многих Cognitive Services, или ключ `Language Understanding`. 

Используйте следующую [команду Azure CLI для создания ключа Cognitive Service](https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create):

```azurecli-interactive
az cognitiveservices account create \
    -n my-cog-serv-resource \
    -g my-cog-serv-resource-group \
    --kind CognitiveServices \
    --sku S0 \
    -l WestEurope \ 
    --yes
```

## <a name="create-net-core-project"></a>Создание проекта .NET Core

Создайте проект консоли .NET Core в Visual Studio Community 2017.

1. Откройте Visual Studio Community 2017.
1. Создайте проект из раздела **Visual C#** , выберите **Console App (.NET Core)** (Консольное приложение (.NET Core)).
1. Введите имя проекта `QueryPrediction`, оставьте остальные значения по умолчанию и выберите **ОК**.
    При этом создается простой проект с основным файлом кода с именем **Program.cs**.

## <a name="add-sdk-with-nuget"></a>Добавление пакета SDK с помощью NuGet

1. В **обозревателе решений** выберите проект в представлении в виде дерева с именем **QueryPrediction**, а затем щелкните правой кнопкой мыши. В меню выберите **Управление пакетами NuGet...**
1. Выберите **Обзор**, а затем введите `Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime`. Когда отобразятся сведения о пакете, выберите **Установить** для установки пакета в проект. 
1. Добавьте следующие инструкции _using_ в начало файла **Program.cs**. Не удаляйте имеющуюся инструкцию _using_ для файла `System`. 

```csharp
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime.Models;
```

## <a name="create-a-new-method-for-the-prediction"></a>Создание метода для прогноза

Создайте метод `GetPrediction` для отправки запроса к конечной точке прогнозирующих запросов. Метод создаст и настроит все необходимые объекты, а затем возвратит файл `Task` с результатами прогнозирования [`LuisResult`](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.luisresult?view=azure-dotnet). 

```csharp
static async  Task<LuisResult> GetPrediction() {
}
```

## <a name="create-credentials-object"></a>Создание объекта учетных данных

Добавьте следующий код к методу `GetPrediction` для создания учетных данных клиента с помощью ключа Cognitive Service.

Замените значение `<REPLACE-WITH-YOUR-KEY>` регионом ключа Cognitive Service. Расположение ключа — на [портале Azure](https://portal.azure.com) на странице "Ключи" для этого ресурса.

```csharp
// Use Language Understanding or Cognitive Services key
// to create authentication credentials
var endpointPredictionkey = "<REPLACE-WITH-YOUR-KEY>";
var credentials = new ApiKeyServiceClientCredentials(endpointPredictionkey);
```

## <a name="create-language-understanding-client"></a>Создание клиента Распознавания речи

В методе `GetPrediction` после предыдущего кода добавьте следующий код для использования новых учетных данных, создавая объект клиента [`LUISRuntimeClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient.-ctor?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Runtime_LUISRuntimeClient__ctor_Microsoft_Rest_ServiceClientCredentials_System_Net_Http_DelegatingHandler___). 

Замените значение `<REPLACE-WITH-YOUR-KEY-REGION>` регионом ключа, таким как `westus`. Расположение региона ключа — на [портале Azure](https://portal.azure.com) на странице "Обзор" для этого ресурса.

```csharp
// Create Luis client and set endpoint
// region of endpoint must match key's region, for example `westus`
var luisClient = new LUISRuntimeClient(credentials, new System.Net.Http.DelegatingHandler[] { });
luisClient.Endpoint = "https://<REPLACE-WITH-YOUR-KEY-REGION>.api.cognitive.microsoft.com";
```

## <a name="set-query-parameters"></a>Установка параметров запроса

В методе `GetPrediction` после предыдущего кода добавьте следующий, чтобы задать параметры запроса.

```csharp
// public Language Understanding Home Automation app
var appId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2";

// query specific to home automation app
var query = "turn on the bedroom light";

// common settings for remaining parameters
Double? timezoneOffset = null;
var verbose = true;
var staging = false;
var spellCheck = false;
String bingSpellCheckKey = null;
var log = false;
```

## <a name="query-prediction-endpoint"></a>Запрос конечной точки прогнозирования

В методе `GetPrediction` после предыдущего кода добавьте следующий, чтобы задать параметры запроса:

```csharp
// Create prediction client
var prediction = new Prediction(luisClient);

// get prediction
return await prediction.ResolveAsync(appId, query, timezoneOffset, verbose, staging, spellCheck, bingSpellCheckKey, log, CancellationToken.None);
```

## <a name="display-prediction-results"></a>Отображение результатов прогнозирования

Измените метод **Main** для вызова нового метода `GetPrediction` и возврата результатов прогнозирования:

```csharp
static void Main(string[] args)
{

    var luisResult = GetPrediction().Result;

    // Display query
    Console.WriteLine("Query:'{0}'", luisResult.Query);

    // Display most common properties of query result
    Console.WriteLine("Top intent is '{0}' with score {1}", luisResult.TopScoringIntent.Intent,luisResult.TopScoringIntent.Score);

    // Display all entities detected in query utterance
    foreach (var entity in luisResult.Entities)
    {
        Console.WriteLine("{0}:'{1}' begins at position {2} and ends at position {3}", entity.Type, entity.Entity, entity.StartIndex, entity.EndIndex);
    }

    Console.Write("done");

}
```

## <a name="run-the-project"></a>Запуск проекта

Создайте проект в Studio и запустите его для просмотра выходных данных запроса:

```console
Query:'turn on the bedroom light'
Top intent is 'HomeAutomation.TurnOn' with score 0.809439957
HomeAutomation.Room:'bedroom' begins at position 12 and ends at position 18
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о [пакете SDK для .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) и [справочной документации по .NET](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet). 

> [!div class="nextstepaction"] 
> [Руководство. Создание приложения LUIS для определения намерений пользователя](luis-quickstart-intents-only.md) 