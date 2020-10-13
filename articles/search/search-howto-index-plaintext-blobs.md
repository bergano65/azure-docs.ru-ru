---
title: Поиск больших двоичных объектов в виде обычного текста
titleSuffix: Azure Cognitive Search
description: Настройте индексатор поиска, чтобы извлечь обычный текст из больших двоичных объектов Azure для полнотекстового поиска в Когнитивный поиск Azure.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 417bdacc3ce8b619d5ec9618e6060ac071882471
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91533931"
---
# <a name="how-to-index-plain-text-blobs-in-azure-cognitive-search"></a>Индексирование больших двоичных объектов с обычным текстом в Azure Когнитивный поиск

При использовании [индексатора больших двоичных объектов](search-howto-indexing-azure-blob-storage.md) для извлечения искомого текста для полнотекстового поиска можно вызвать различные режимы анализа для получения более эффективных результатов индексирования. По умолчанию индексатор анализирует текстовые BLOB-объекты с разделителями как один фрагмент текста. Однако если все большие двоичные объекты содержат обычный текст в той же кодировке, можно значительно повысить производительность индексирования с помощью **режима анализа текста**.

## <a name="set-up-plain-text-indexing"></a>Настройка индексирования в виде обычного текста

Для индексирования больших двоичных объектов с обычным текстом Создайте или обновите определение индексатора со `parsingMode` свойством Configuration в `text` в запросе на [Создание индексатора](/rest/api/searchservice/create-indexer) :

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }
```

По умолчанию предполагается кодировка `UTF-8`. Чтобы указать другую кодировку, используйте свойство конфигурации `encoding`: 

```http
    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }
```

## <a name="request-example"></a>Пример запроса

Режимы анализа указываются в определении индексатора.

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-plaintext-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }
```

## <a name="help-us-make-azure-cognitive-search-better"></a>Помогите нам сделать Azure Когнитивный поиск лучше

Если вам нужна какая-либо функция или у вас есть идеи, которые можно было бы реализовать, сообщите об этом на [сайте UserVoice](https://feedback.azure.com/forums/263029-azure-search/). Если вам нужна помощь с использованием существующего компонента, опубликуйте свой вопрос на [Stack overflow](https://stackoverflow.microsoft.com/questions/tagged/18870).

## <a name="next-steps"></a>Дальнейшие шаги

* [Indexers in Azure Cognitive Search](search-indexer-overview.md) (Индексаторы в службе "Когнитивный поиск Azure")
* [Настройка индексатора больших двоичных объектов](search-howto-indexing-azure-blob-storage.md)
* [Общие сведения об индексировании BLOB-объектов](search-blob-storage-integration.md)