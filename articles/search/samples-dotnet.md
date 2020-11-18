---
title: Примеры для .NET
titleSuffix: Azure Cognitive Search
description: Поиск демонстрационных примеров кода C# для Azure Когнитивный поиск, использующих клиентские библиотеки .NET.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: d068365cc8197a579c0b043d3fff2da3d54eb803
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94687209"
---
# <a name="net-c-code-samples-for-azure-cognitive-search"></a>Примеры кода .NET (C#) для Azure Когнитивный поиск

Узнайте о примерах кода C#, демонстрирующих функции и функциональные возможности Azure Когнитивный поиск. Ниже приведены основные репозитории.

| Хранилище | Описание |
|------------|-------------|
| [Azure-SDK-for-NET/SDK/Поиск/Azure.Search.Docументс/Samples/](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/samples) | Примеры, созданные командой пакета SDK для Azure, поставляемой с клиентской библиотекой Azure.Search.Docументс в пакете SDK. Вы также можете просмотреть [модульные тесты](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/tests) для клиентской библиотеки, чтобы увидеть, как вызываются различные API-интерфейсы. |
| [Azure-Samples/Azure-Search-DotNet-Samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) | Примеры, сопровождающие статьи документации, в том числе [Использование клиентской библиотеки .NET](search-howto-dotnet-sdk.md).|
| [Azure-Samples/Search-DotNet-Приступая к работе](https://github.com/Azure-Samples/search-dotnet-getting-started) | Примеры, сопровождающие краткие руководства и учебники в документации.|

> [!Tip]
> Воспользуйтесь [браузером примеров](/samples/browse/?languages=csharp&products=azure-cognitive-search) для поиска примеров кода Майкрософт в GitHub, отфильтрованных по продуктам, службам и языкам.

## <a name="net-sdk-samples"></a>Примеры пакета SDK для .NET

Пакет Azure SDK для .NET включает многочисленные примеры и [файл readme](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/README.md) с описанием каждого из них. Этот список приведен ниже для вашего удобства.

| Примеры | Описание |
|---------|-------------|
| ["Hello World", синхронно](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01a_HelloWorld.md) | Демонстрирует создание клиента, проверку подлинности и обработку ошибок с помощью синхронных методов.|
| ["Hello World" — асинхронно](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01b_HelloWorldAsync.md) | Демонстрирует создание клиента, проверку подлинности и обработку ошибок с помощью асинхронных методов.  |
| [Операции уровня службы](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample02_Service.md) | Демонстрирует создание индексов, индексаторов, источников данных, навыков и сопоставлений синонимов. В этом примере также показано, как получить статистику службы и как запросить индекс.  |
| [Операции с индексами](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample03_Index.md) | Демонстрирует выполнение действия с существующим индексом, в данном случае получение числа документов, хранящихся в индексе.  |
| [фиелдбуилдеригноре](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample04_FieldBuilderIgnore.md) | Демонстрирует методику работы с неподдерживаемыми типами данных.  |
| [Индексирование документов (модель push-уведомлений)](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample05_IndexingDocuments.md) | Индексирование модели push, куда вы отправляете полезные данные JSON в индекс службы.   |
| [Пример ключа шифрования](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample06_EncryptedIndex.md) | Демонстрирует использование управляемого клиентом ключа шифрования для добавления дополнительного уровня защиты от конфиденциального содержимого.  |

## <a name="documentation-samples"></a>Примеры документации

Следующие примеры содержат связанную статью в [документации по Azure когнитивный Поиск](https://docs.microsoft.com/azure/search/).

| Примеры | Описание |
|---------|-------------|
| [QuickStart](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart) | Исходный код для [краткого руководства: создание индекса поиска ](search-get-started-dotnet.md).  |
| [дотнесовто](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)  | Исходный код для [использования клиентской библиотеки .NET](search-howto-dotnet-sdk.md) |
| [дотнесовтосинонимс](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms)  | Списки синонимов используются для расширения запросов, предоставляя сопоставляемые термины, которые являются внешними по отношению к индексу. Этот пример включен в [Пример: Добавление синонимов в C#](search-synonyms-tutorial-sdk.md). |
| [дотнеттоиндексерс](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) | Исходный код программных фрагментов, связанных с индексатором, в различных статьях. В этом примере показано, как настроить индексатор с расписанием, сопоставлениями полей и параметрами.  |
| [дотнесовтоенкриптионусингкмк](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)  | Исходный код [настройки ключей, управляемых клиентом, для шифрования данных](search-security-manage-encryption-keys.md) |
| [Создание первого приложения на языке C #](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11) |  Исходный код для [учебника: создание первого приложения поиска](tutorial-csharp-create-first-app.md). Хотя большинство примеров являются консольными приложениями, в этом примере MVC используется веб-страница для переднего плана с примером индекса гостиниц, демонстрирующий базовый поиск, разбивку на страницы, автозаполнение и предлагаемые запросы, аспекты и фильтры. |
| [множественные источники данных](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources)  | Исходный код для [учебника: индекс из нескольких источников данных](tutorial-multiple-data-sources.md). |
|  [Оптимизация — индексирование данных](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing) | Исходный код для [учебника. Оптимизация индексирования с помощью API push-уведомлений](tutorial-optimize-indexing-push-api.md).  |
| [Руководство по AI — обогащение](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/tutorial-ai-enrichment)  | Исходный код для [руководства. созданное AI содержимое из больших двоичных объектов Azure с помощью пакета SDK для .NET](cognitive-search-tutorial-blob-dotnet.md).  |

## <a name="standalone-samples-and-solutions"></a>Автономные примеры и решения

| Примеры | Описание |
|---------|-------------|
| [Azure-Search-Power-Skills](https://github.com/Azure-Samples/azure-search-power-skills)  | Исходный код для использования настраиваемых навыков, которые можно включить в выигранные решения.  |
| [Акселератор решений для интеллектуального анализа знаний](https://docs.microsoft.com/samples/azure-samples/azure-search-knowledge-mining/azure-search-knowledge-mining/) | Включает шаблоны, файлы поддержки и аналитические отчеты для создания прототипа комплексного решения по интеллектуальному анализу знаний.  |
| [Репозиторий приложений для поиска ковид-19](https://github.com/liamca/covid19search) | Репозиторий исходного кода для [приложения поиска ковид-19](https://covid19search.azurewebsites.net/) на основе когнитивный Поиск |
| [JFK](https://github.com/Microsoft/AzureSearch_JFK_Files) | Дополнительные сведения о [решении ЖФК](https://www.microsoft.com/ai/ai-lab-jfk-files). |