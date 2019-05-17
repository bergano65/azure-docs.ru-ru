---
title: Индексирование больших двоичных объектов в формате CSV с помощью индексатора в службе "Поиск Azure"
description: Узнайте, как сканировать большие двоичные объекты в формате CSV в хранилище BLOB-объектов Azure для полнотекстового поиска с помощью индекса службы "Поиск Azure". Индексаторы автоматизируют прием данных из выбранных источников, таких как хранилище BLOB-объектов Azure.
ms.date: 05/02/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: e7d959e77d27fb04b18f402e4056d4dea1607039
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2019
ms.locfileid: "65522897"
---
# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>Индексирование BLOB-объектов в формате CSV с помощью индексатора BLOB-объектов службы поиска Azure

> [!Note]
> Режим анализа delimitedText находится в предварительной версии и не предназначена для использования в рабочей среде. [2019 г. версия REST API-05-06-Preview](search-api-preview.md) предоставляет эту функцию. В настоящее время не поддерживается пакет SDK для .NET.
>

По умолчанию [индексатор BLOB-объектов службы поиска Azure](search-howto-indexing-azure-blob-storage.md) анализирует текстовые BLOB-объекты (с разделителями) как один блок текста. Однако в больших двоичных объектах, содержащих CSV-данные, часто возникает необходимость обрабатывать каждую строку объекта как отдельный документ. Например, учитывая следующий разделительный текст, вы можете проанализировать его в двух документах, каждый из которых содержит поля "id", "datePublished" и "tags": 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

В этой статье вы узнаете, как выполнить синтаксический анализ больших двоичных объектов CSV с параметром indexerby BLOB-объектов службы поиска Azure `delimitedText` режим анализа. 

> [!NOTE]
> Следуйте рекомендациям по конфигурации индексатора в [один ко многим индексирования](search-howto-index-one-to-many-blobs.md) для вывода нескольких поиск документов из одного BLOB-объектов Azure.

## <a name="setting-up-csv-indexing"></a>Настройка индексирования CSV
Чтобы индексировать большие двоичные объекты в CSV-ФАЙЛ, создать или обновить определение индексатора с `delimitedText` режим анализа на [Создание индексатора](https://docs.microsoft.com/rest/api/searchservice/create-indexer) запроса:

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

`firstLineContainsHeaders` указывает, что первая (непустая) строка каждого BLOB-объекта содержит заголовки.
Если большие двоичные объекты не содержат строку заголовка, заголовки следует указать в конфигурации индексатора. 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

Можно настроить символ разделителя с помощью параметра конфигурации `delimitedTextDelimiter`. Например:

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> Сейчас поддерживается только кодирование UTF-8. Если требуется поддержка других кодировок, проголосуйте за них на сайте [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> Когда вы используете режим анализа текста с разделителями, служба поиска Azure предполагает, что все BLOB-объекты в источнике данных являются CSV-объектами. Если необходима поддержка как CSV-объектов, так и других больших двоичных объектов в одном источнике данных, проголосуйте на [нашем сайте UserVoice](https://feedback.azure.com/forums/263029-azure-search).
> 
> 

## <a name="request-examples"></a>Примеры запросов
Чтобы свести все описанное выше вместе, ознакомьтесь с приведенными далее примерами данных. 

Источник данных: 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

Индексатор:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-search-better"></a>Помогите нам усовершенствовать службу поиска Azure
Если вам нужна какая-либо функция или у вас есть идеи, которые можно было бы реализовать, сообщите об этом на [сайте UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

