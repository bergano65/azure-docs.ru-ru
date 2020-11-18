---
title: Примеры для Python
titleSuffix: Azure Cognitive Search
description: Ознакомьтесь с примерами кода Python для Azure Когнитивный поиск, которые используют пакет SDK для Azure .NET для Python или других компонентов.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 3de630552f7ad2cc941fe23369398c10ffce5870
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686852"
---
# <a name="python-code-samples-for-azure-cognitive-search"></a>Примеры кода Python для Azure Когнитивный поиск

Узнайте о примерах кода Python, демонстрирующих функции и функциональные возможности Azure Когнитивный поиск. Ниже приведены основные репозитории.

| Хранилище | Описание |
|------------|-------------|
| [Azure-SDK-for-Python/дерево/главный/SDK/Поиск/Azure-Search-документы/примеры/](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples) | Примеры, созданные командой пакета SDK для Azure, поставляемой с клиентской библиотекой Azure.Search.Docументс в пакете SDK. Вы также можете просмотреть [модульные тесты](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests) для клиентской библиотеки, чтобы увидеть, как вызываются различные API-интерфейсы. |
| [Примеры Azure-Samples/Azure-Search-Python](https://github.com/Azure-Samples/azure-search-python-samples) | Примеры кода, сопровождающие статьи с инструкциями, включая краткое руководство. [Создание индекса поиска в Python](search-get-started-python.md).|

> [!Tip]
> Воспользуйтесь [браузером примеров](/samples/browse/?languages=csharp&products=azure-cognitive-search) для поиска примеров кода Майкрософт в GitHub, отфильтрованных по продуктам, службам и языкам.

## <a name="python-sdk-samples"></a>Примеры пакета SDK для Python

Пакет Azure SDK для Python включает многочисленные примеры и [страницу «Начало работы](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples) », которая включает необходимые компоненты и установку пакета. На этой странице также содержатся ссылки на следующие примеры, указанные здесь для удобства.

| Примеры | Описание |
|---------|-------------|
| [Проверка подлинности](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_authentication.py) | Демонстрирует настройку клиента и проверку подлинности для службы. | 
| [Операции создания индекса — чтение и обновление — удаление](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) | Демонстрирует создание, обновление, получение, перечисление и удаление [индексов поиска](search-what-is-an-index.md). |
| [Операции создания индексатора — чтение и обновление — удаление](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexers_operations.py) | Демонстрирует создание, обновление, получение, перечисление, сброс и удаление [индексаторов](search-indexer-overview.md). |
| [Поиск источников данных индексатора](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexer_datasource_skillset.py) | Демонстрирует создание, обновление, получение, перечисление и удаление источников данных индексатора, необходимых для индексирования [поддерживаемых источников данных Azure](search-indexer-overview.md#supported-data-sources)на основе индексатора. |
| [Синонимы](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_synonym_map_operations.py) | Демонстрирует создание, обновление, получение, перечисление и удаление [сопоставлений синонимов](search-synonyms.md).  |
| [Загрузка документов](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_crud_operations.py) | Демонстрирует, как отправлять или объединять документы в индекс в операции [импорта данных](search-what-is-data-import.md) . |
| [Простой запрос](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_simple_query.py) | Демонстрирует, как настроить [базовый запрос](search-query-overview.md). |
| [Фильтрация запроса](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_filter_query.py) | Демонстрируется настройка [критерия фильтра](search-filters.md). |
| [Запрос аспекта](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_facet_query.py) | Демонстрирует работу с [аспектами](search-filters-facets.md). |

## <a name="documentation-samples"></a>Примеры документации

Следующие примеры содержат связанную статью в [документации по Azure когнитивный Поиск](https://docs.microsoft.com/azure/search/).

| Примеры | Описание | 
|---------|-------------|
| [QuickStart](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Quickstart) | Исходный код для [краткого руководства: создание индекса поиска в Python](search-get-started-python.md).  |
| [Руководство по AI — обогащение](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment)  | Исходный код для [учебника. Использование Python и AI для создания содержимого с возможностью поиска из больших двоичных объектов Azure](cognitive-search-tutorial-blob-python.md).  |
| [AzureML-Custom-навык](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill)  | Исходный код [. пример. Создание пользовательского навыка с помощью Python](cognitive-search-custom-skill-python.md).  |