---
title: Примеры для Java
titleSuffix: Azure Cognitive Search
description: Найдите демонстрационные примеры кода Java для Azure Когнитивный поиск, которые используют пакет SDK для Azure .NET для Java.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: c1d21d88ed49cb14aa9f964791154f001a40fbe2
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499583"
---
# <a name="java-code-samples-for-azure-cognitive-search"></a>Примеры кода Java для Azure Когнитивный поиск

Узнайте о примерах кода Java, демонстрирующих функции и функциональные возможности Azure Когнитивный поиск. Ниже приведены основные репозитории.

| Хранилище | Описание |
|------------|-------------|
| [Azure-SDK-for-Java/дерево/Master/SDK/Поиск/Azure-Search-Documents/src/Samples](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples) | Примеры, созданные командой пакета SDK для Azure, поставляемой с клиентской библиотекой Azure.Search.Docументс в пакете SDK. Вы также можете просмотреть [модульные тесты](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/test) для клиентской библиотеки, чтобы увидеть, как вызываются различные API-интерфейсы. |
| [Примеры Azure-Samples/Azure-Search-Java-Samples](https://github.com/Azure-Samples/azure-search-java-samples) | Примеры кода, сопровождающие статьи с инструкциями. **Примеры в этом репозитории еще не обновлены для использования пакета Azure SDK для Java**. В настоящее время эти примеры вызывают интерфейсы API RESTFUL в коде Java.|

> [!Tip]
> Воспользуйтесь [браузером примеров](/samples/browse/?languages=csharp&products=azure-cognitive-search) для поиска примеров кода Майкрософт в GitHub, отфильтрованных по продуктам, службам и языкам.

## <a name="java-sdk-samples"></a>Примеры пакета SDK для Java

Пакет Azure SDK для Java включает многочисленные примеры и [страницу «Начало работы](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples) », которая охватывает установку пакетов. На странице также приведен широкий спектр примеров. Ниже перечислены некоторые из наиболее распространенных операций.

| Примеры | Описание |
|---------|-------------|
| [Создание индекса поиска](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) | Демонстрирует создание [индексов поиска](search-what-is-an-index.md). |
| [Создание синонимов](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SynonymMapsCreateExample.java) | Демонстрирует создание [сопоставлений синонимов](search-synonyms.md).  |
| [Создание индексатора поиска](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) | Демонстрирует создание [индексаторов](search-indexer-overview.md). |
| [Создание источника данных индексатора поиска](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/DataSourceExample.java) | Демонстрирует создание источников данных индексатора, необходимых для индексирования [поддерживаемых источников данных Azure](search-indexer-overview.md#supported-data-sources)на основе индексатора. |
| [Создание набора навыков](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateSkillsetExample.java) |  Демонстрирует создание [навыков](cognitive-search-working-with-skillsets.md) , присоединенных индексаторов и выполняющих обогащение на основе искусственного интеллекта во время индексирования. |
| [Загрузка документов](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/IndexContentManagementExample.java) | Демонстрирует, как отправлять или объединять документы в индекс в операции [импорта данных](search-what-is-data-import.md) . |
| [Синтаксис запросов](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchAsyncWithFullyTypedDocumentsExample.java) | Демонстрирует, как настроить [базовый запрос](search-query-overview.md). |

## <a name="documentation-samples"></a>Примеры документации

Следующие примеры содержат связанную статью в [документации по Azure когнитивный Поиск](./index.yml).

| Примеры | Описание | 
|---------|-------------|
| [QuickStart](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/quickstart) | Исходный код для [краткого руководства: создание индекса поиска в Java](search-get-started-java.md). В этом примере вызываются интерфейсы API для остальных компонентов. |
| [Поиск — Java-индексатор — демонстрация](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/search-java-indexer-demo) | Демонстрирует Azure Cosmos DB индексатора в Java. В этом примере вызываются интерфейсы API для остальных компонентов. |