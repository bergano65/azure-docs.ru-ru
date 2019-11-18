---
title: Поиск по CSV-BLOB-объектам
titleSuffix: Azure Cognitive Search
description: Извлеките и импортируйте CSV-файл из хранилища BLOB-объектов Azure с помощью режима разбора delimitedText, в настоящее время в общедоступной предварительной версии
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2166e100f03f21c218618d19dc37ee70c6ab29ef
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113030"
---
# <a name="how-to-index-csv-blobs-using-delimitedtext-parsing-mode-and-blob-indexers-in-azure-cognitive-search"></a>Как индексировать большие двоичные объекты CSV с помощью режима синтаксического анализа delimitedText и индексаторов BLOB-объектов в Azure Когнитивный поиск 

> [!IMPORTANT] 
> Режим анализа delimitedText в настоящее время находится в общедоступной предварительной версии. Для предварительной версии функции соглашение об уровне обслуживания не предусмотрено. Мы не рекомендуем использовать ее в рабочей среде. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Эта функция предоставляется в [версии REST API 2019-05-06-Preview](search-api-preview.md). В настоящее время нет поддержки портала или пакета SDK для .NET.

По умолчанию [индексатор BLOB-объектов когнитивный Поиск Azure](search-howto-indexing-azure-blob-storage.md) анализирует текстовые большие двоичные объекты с разделителями как единый фрагмент текста. Однако в больших двоичных объектах, содержащих CSV-данные, часто возникает необходимость обрабатывать каждую строку объекта как отдельный документ. Например, учитывая следующий разделительный текст, вы можете проанализировать его в двух документах, каждый из которых содержит поля "id", "datePublished" и "tags": 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

В этой статье вы узнаете, как анализировать большие двоичные объекты CSV с помощью индексатора BLOB-объектов Azure Когнитивный поиск, установив режим разбора `delimitedText`. 

> [!NOTE]
> Следуйте рекомендациям по настройке индексатора в [индексировании "один ко многим](search-howto-index-one-to-many-blobs.md) ", чтобы вывести несколько документов поиска из одного большого двоичного объекта Azure.

## <a name="setting-up-csv-indexing"></a>Настройка индексирования CSV
Чтобы индексировать большие двоичные объекты CSV, создайте или обновите определение индексатора с помощью `delimitedText` режима синтаксического анализа для запроса на [Создание индексатора](https://docs.microsoft.com/rest/api/searchservice/create-indexer) :

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

`firstLineContainsHeaders` указывает, что первая (непустая) строка каждого BLOB-объекта содержит заголовки.
Если большие двоичные объекты не содержат строку заголовка, заголовки следует указать в конфигурации индексатора. 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

Можно настроить символ разделителя с помощью параметра конфигурации `delimitedTextDelimiter`. Например,

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> Сейчас поддерживается только кодирование UTF-8. Если требуется поддержка других кодировок, проголосуйте за них на сайте [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> При использовании режима синтаксического анализа текста с разделителями в Azure Когнитивный поиск предполагается, что все большие двоичные объекты в источнике данных будут иметь формат CSV. Если необходима поддержка как CSV-объектов, так и других больших двоичных объектов в одном источнике данных, проголосуйте на [нашем сайте UserVoice](https://feedback.azure.com/forums/263029-azure-search).
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

## <a name="help-us-make-azure-cognitive-search-better"></a>Помогите нам сделать Azure Когнитивный поиск лучше
Если вам нужна какая-либо функция или у вас есть идеи, которые можно было бы реализовать, сообщите об этом на [сайте UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

