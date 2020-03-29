---
title: Поиск по CSV капли
titleSuffix: Azure Cognitive Search
description: Извлекайте и импортируйте CSV из хранилища Azure Blob с помощью режима разграничения.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: bf600890bfed570e712a159005b8ef5267298cc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122327"
---
# <a name="how-to-index-csv-blobs-using-delimitedtext-parsing-mode-and-blob-indexers-in-azure-cognitive-search"></a>Как индексировать CSV капли с помощью режима разбора delimitedText и индексеров Blob в Azure Cognitive Search

По умолчанию [индексер Azure Cognitive Search разбирает](search-howto-indexing-azure-blob-storage.md) разграниченные текстовые капли в виде одного фрагмента текста. Однако в больших двоичных объектах, содержащих CSV-данные, часто возникает необходимость обрабатывать каждую строку объекта как отдельный документ. Например, учитывая следующий разделительный текст, вы можете проанализировать его в двух документах, каждый из которых содержит поля "id", "datePublished" и "tags": 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

В этой статье вы узнаете, как разобрать CSV капли с Azure когнитивного `delimitedText` поиска каплю указателя, установив режим разбора. 

> [!NOTE]
> Следуйте рекомендациям конфигурации индекса в [индексации «От одного к многим»](search-howto-index-one-to-many-blobs.md) и выдаю несколько поисковых документов из одной капли Azure.

## <a name="setting-up-csv-indexing"></a>Настройка индексирования CSV
Чтобы проиндексировать капли CSV, создайте или `delimitedText` обновите определение индекса с режимом разбора в [запросе Create Indexer:](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

`firstLineContainsHeaders` указывает, что первая (непустая) строка каждого BLOB-объекта содержит заголовки.
Если большие двоичные объекты не содержат строку заголовка, заголовки следует указать в конфигурации индексатора. 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

Можно настроить символ разделителя с помощью параметра конфигурации `delimitedTextDelimiter`. Пример:

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> Сейчас поддерживается только кодирование UTF-8. Если требуется поддержка других кодировок, проголосуйте за них на сайте [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> При использовании разграниченного режима анализа текста Azure Cognitive Search предполагает, что все капли в вашем источнике данных будут CSV. Если необходима поддержка как CSV-объектов, так и других больших двоичных объектов в одном источнике данных, проголосуйте на [нашем сайте UserVoice](https://feedback.azure.com/forums/263029-azure-search).
> 
> 

## <a name="request-examples"></a>Примеры запросов
Чтобы свести все описанное выше вместе, ознакомьтесь с приведенными далее примерами данных. 

Источник данных: 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

Индексатор:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-cognitive-search-better"></a>Помогите нам сделать когнитивный поиск Azure лучше
Если вам нужна какая-либо функция или у вас есть идеи, которые можно было бы реализовать, сообщите об этом на [сайте UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

