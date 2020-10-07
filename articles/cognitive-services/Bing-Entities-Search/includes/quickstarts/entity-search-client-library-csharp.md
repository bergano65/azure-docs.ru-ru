---
title: Краткое руководство по использованию клиентской библиотеки Поиска сущностей Bing (C#)
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.openlocfilehash: 27951a6da2c5e4a9d17e8b332d8ee9706701649d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2020
ms.locfileid: "88931899"
---
Используйте это краткое руководство, чтобы начать поиск сущностей с помощью клиентской библиотеки Поиска сущностей Bing для C#. Поскольку REST API Поиска сущностей Bing совместим с большинством языков программирования, клиентская библиотека обеспечивает простой способ интеграции службы в ваши приложения. Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingEntitySearch).


## <a name="prerequisites"></a>Предварительные требования

* Любой выпуск [Visual Studio 2017 или более поздней версии](https://www.visualstudio.com/downloads/).
* Платформа [Json.NET](https://www.newtonsoft.com/json), доступная в виде пакета NuGet.
* Если вы используете Linux или MacOS, это приложение можно запустить с помощью [Mono](https://www.mono-project.com/).
* [Пакет SDK Поиска новостей Bing для NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.EntitySearch/1.2.0). При установке этого пакета также устанавливаются следующие компоненты:
    * Microsoft.Rest.ClientRuntime
    * Microsoft.Rest.ClientRuntime.Azure
    * Newtonsoft.Json.

Чтобы добавить клиентскую библиотеку для API "Поиск сущностей Bing" в проект Visual Studio, используйте параметр **Управление пакетами NuGet** в **обозревателе решений** и добавьте пакет `Microsoft.Azure.CognitiveServices.Search.EntitySearch`.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>Создание и инициализация приложения

1. Создайте консольное приложение C# в Visual Studio. Затем добавьте следующие элементы в основной файл кода.

    ```csharp
    using System;
    using System.Linq;
    using System.Text;
    using Microsoft.Azure.CognitiveServices.Search.EntitySearch;
    using Microsoft.Azure.CognitiveServices.Search.EntitySearch.Models;
    using Newtonsoft.Json;
    ```

## <a name="create-a-client-and-send-a-search-request"></a>Создайте клиент и отправьте поисковый запрос.

1. Создайте клиент для поиска. Добавьте ключ подписки, создав новый `ApiKeyServiceClientCredentials`.

    ```csharp
    var client = new EntitySearchClient(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

1. Используйте клиентскую функцию `Entities.Search()` для поиска запроса.
    
    ```csharp
    var entityData = client.Entities.Search(query: "Satya Nadella");
    ```

## <a name="get-and-print-an-entity-description"></a>Получение и печать описания сущности

1. Если API вернул результаты поиска, получите основную сущность из `entityData`.

    ```csharp
    var mainEntity = entityData.Entities.Value.Where(thing => thing.EntityPresentationInfo.EntityScenario == EntityScenario.DominantEntity).FirstOrDefault();
    ```

2. Напечатайте описания основной сущности 

    ```csharp
    Console.WriteLine(mainEntity.Description);
    ```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Создание одностраничного веб-приложения](../../tutorial-bing-entities-search-single-page-app.md)

* [Основные сведения об API Bing для поиска сущностей](../../overview.md )
