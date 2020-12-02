---
title: Примеры для JavaScript
titleSuffix: Azure Cognitive Search
description: Найдите примеры кода JavaScript для Azure Когнитивный поиск, которые используют пакет SDK для Azure .NET для JavaScript.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 09a9ca2ad1b1f1e5578ecc5d35a85c81b32a5b1a
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96511752"
---
# <a name="javascript-code-samples-for-azure-cognitive-search"></a>Примеры кода JavaScript для Azure Когнитивный поиск

Узнайте о примерах кода JavaScript, демонстрирующих функции и функциональные возможности Azure Когнитивный поиск. Ниже приведены основные репозитории.

| Хранилище | Описание: |
|------------|-------------|
| [Azure-SDK-for-JS/дерево, главный/пакет SDK, Поиск и поиск — документы](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents) | Примеры, созданные командой пакета SDK для Azure, поставляемой с клиентской библиотекой Azure.Search.Docументс в пакете SDK. Вы также можете просмотреть [модульные тесты](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test) для клиентской библиотеки, чтобы увидеть, как вызываются различные API-интерфейсы. |
| [Примеры Azure-Samples/Azure-Search-JavaScript-Samples](https://github.com/Azure-Samples/azure-search-javascript-samples) | Примеры кода, сопровождающие статьи с инструкциями, включая краткое руководство. [Создание индекса поиска в JavaScript](search-get-started-javascript.md).|

> [!Tip]
> Воспользуйтесь [браузером примеров](/samples/browse/?languages=javascript&products=azure-cognitive-search) для поиска примеров кода Майкрософт в GitHub, отфильтрованных по продуктам, службам и языкам.

## <a name="javascript-sdk-samples"></a>Примеры SDK для JavaScript

Пакет Azure SDK для Java включает многочисленные примеры и страницу «Начало [работы](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/README.md#getting-started) », которая охватывает установку пакетов, установку клиентов и устранение неполадок. На странице также описываются следующие примеры категорий, которые перечислены здесь для удобства.

| Примеры | Описание |
|---------|-------------|
| [индексирует](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexes) | Демонстрирует создание, обновление, получение, перечисление и удаление [индексов поиска](search-what-is-an-index.md). В этот пример категории также включен пример статистики службы. |
| [Датасаурцеконнектионс (для индексаторов)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/dataSourceConnections) | Демонстрирует создание, обновление, получение, перечисление и удаление источников данных индексатора, необходимых для индексирования [поддерживаемых источников данных Azure](search-indexer-overview.md#supported-data-sources)на основе индексатора. |
| [индексаторов](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexers) |  Демонстрирует создание, обновление, получение, перечисление, сброс и удаление [индексаторов](search-indexer-overview.md).|
| [Набор навыков](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/skillSets) |   Демонстрирует создание, обновление, получение, перечисление и удаление [навыков](cognitive-search-working-with-skillsets.md) , присоединенных индексаторов, и выполнение обогащения на основе искусственного интеллекта во время индексирования. |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/synonymMaps) | Демонстрирует создание, обновление, получение, перечисление и удаление [сопоставлений синонимов](search-synonyms.md).  |
| [Запросы](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/javascript/src/readonlyQuery.js) | Демонстрирует выполнение запроса к открытому индексу только для чтения, размещенному корпорацией Майкрософт.  |

## <a name="typescript-samples"></a>Примеры TypeScript

Пакет SDK также предоставляет примеры TypeScript, указанные здесь для удобства.

| Примеры | Описание |
|---------|-------------|
| [индексирует](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexes) | Демонстрирует создание, обновление, получение, перечисление и удаление [индексов поиска](search-what-is-an-index.md). В этот пример категории также включен пример статистики службы. |
| [Датасаурцеконнектионс (для индексаторов)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/dataSourceConnections) | Демонстрирует создание, обновление, получение, перечисление и удаление источников данных индексатора, необходимых для индексирования [поддерживаемых источников данных Azure](search-indexer-overview.md#supported-data-sources)на основе индексатора. |
| [индексаторов](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexers) |  Демонстрирует создание, обновление, получение, перечисление, сброс и удаление [индексаторов](search-indexer-overview.md).|
| [Набор навыков](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/skillSets) |   Демонстрирует создание, обновление, получение, перечисление и удаление [навыков](cognitive-search-working-with-skillsets.md) , присоединенных индексаторов, и выполнение обогащения на основе искусственного интеллекта во время индексирования. |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/synonymMaps) | Демонстрирует создание, обновление, получение, перечисление и удаление [сопоставлений синонимов](search-synonyms.md).  |
| [Запросы](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/typescript/src/readonlyQuery.ts) | Демонстрирует выполнение запроса к открытому индексу только для чтения, размещенному корпорацией Майкрософт.  |

## <a name="documentation-samples"></a>Примеры документации

Следующие примеры содержат связанную статью в [документации по Azure когнитивный Поиск](./index.yml).

| Примеры | Описание | 
|---------|-------------|
| [QuickStart](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11) | Исходный код для [краткого руководства: создание индекса поиска в JavaScript](search-get-started-javascript.md).  |

## <a name="standalone-samples"></a>Автономные примеры

| Примеры | Описание |
|---------|-------------|
| [Azure-Search-реагирующий-шаблон](https://github.com/dereklegenzoff/azure-search-react-template) | Шаблон отклика для Когнитивный поиск Azure (github.com) |