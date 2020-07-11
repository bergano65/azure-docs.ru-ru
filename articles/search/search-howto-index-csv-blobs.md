---
title: Поиск по CSV-BLOB-объектам
titleSuffix: Azure Cognitive Search
description: Извлеките и импортируйте CSV-файл из хранилища BLOB-объектов Azure с помощью режима синтаксического анализа delimitedText.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: a1d9e34687f4a8a5d973d90006e90692fde7a668
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86146862"
---
# <a name="how-to-index-csv-blobs-using-delimitedtext-parsing-mode-and-blob-indexers-in-azure-cognitive-search"></a>Как индексировать большие двоичные объекты CSV с помощью режима синтаксического анализа delimitedText и индексаторов BLOB-объектов в Azure Когнитивный поиск

По умолчанию [индексатор BLOB-объектов когнитивный Поиск Azure](search-howto-indexing-azure-blob-storage.md) анализирует текстовые большие двоичные объекты с разделителями как единый фрагмент текста. Однако в больших двоичных объектах, содержащих CSV-данные, часто возникает необходимость обрабатывать каждую строку объекта как отдельный документ. Например, учитывая следующий разделительный текст, вы можете проанализировать его в двух документах, каждый из которых содержит поля "id", "datePublished" и "tags": 

```text
id, datePublished, tags
1, 2016-01-12, "azure-search,azure,cloud"
2, 2016-07-07, "cloud,mobile"
```

В этой статье вы узнаете, как анализировать большие двоичные объекты CSV с помощью индексатора BLOB-объектов Azure Когнитивный поиск, установив `delimitedText` режим анализа. 

> [!NOTE]
> Следуйте рекомендациям по настройке индексатора в [индексировании "один ко многим](search-howto-index-one-to-many-blobs.md) ", чтобы вывести несколько документов поиска из одного большого двоичного объекта Azure.

## <a name="setting-up-csv-indexing"></a>Настройка индексирования CSV
Чтобы индексировать большие двоичные объекты CSV, создайте или обновите определение индексатора с помощью `delimitedText` режима анализа для запроса на [Создание индексатора](https://docs.microsoft.com/rest/api/searchservice/create-indexer) :

```http
    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }
```

`firstLineContainsHeaders` указывает, что первая (непустая) строка каждого BLOB-объекта содержит заголовки.
Если большие двоичные объекты не содержат строку заголовка, заголовки следует указать в конфигурации индексатора. 

```http
"parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 
```

Можно настроить символ разделителя с помощью параметра конфигурации `delimitedTextDelimiter`. Вот несколько примеров:

```http
"parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }
```

> [!NOTE]
> Сейчас поддерживается только кодирование UTF-8. Если требуется поддержка других кодировок, проголосуйте за них на сайте [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> При использовании режима синтаксического анализа текста с разделителями в Azure Когнитивный поиск предполагается, что все большие двоичные объекты в источнике данных будут иметь формат CSV. Если необходима поддержка как CSV-объектов, так и других больших двоичных объектов в одном источнике данных, проголосуйте на [нашем сайте UserVoice](https://feedback.azure.com/forums/263029-azure-search).
> 
> 

## <a name="request-examples"></a>Примеры запросов
Чтобы свести все описанное выше вместе, ознакомьтесь с приведенными далее примерами данных. 

Источник данных: 

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   
```

Индексатор:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }
```

## <a name="help-us-make-azure-cognitive-search-better"></a>Помогите нам сделать Azure Когнитивный поиск лучше
Если вам нужна какая-либо функция или у вас есть идеи, которые можно было бы реализовать, сообщите об этом на [сайте UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

