---
title: Руководство по поиску JSON в хранилище BLOB-объектов Azure — Поиск Azure
description: Из этого руководства вы узнаете, как искать частично структурированные данные больших двоичных объектов Azure с помощью службы "Поиск Azure".
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 1c8ce14dd3961eff33a54a14c2bd0b27650d8a50
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58201353"
---
# <a name="tutorial-search-semi-structured-data-in-azure-cloud-storage"></a>Руководство. Поиск частично структурированных данных в облачном хранилище Azure

Служба "Поиск Azure" может индексировать документы и массивы JSON в хранилище BLOB-объектов Azure с помощью [индексатора](search-indexer-overview.md), который умеет читать полуструктурированные данные. Частично структурированные данные содержат теги или метки, отделяющие содержимое в данных. Этим они отличаются от неструктурированных данных, которые должны быть полностью проиндексированы, и от формально структурированных данных, которые соответствуют модели данных, например схеме реляционной базы данных, и поддерживают индексирование по отдельным полям.

В этом учебнике используются [интерфейсы REST API службы "Поиск Azure"](https://docs.microsoft.com/rest/api/searchservice/) и клиент REST для выполнения следующих задач:

> [!div class="checklist"]
> * Настройка источника данных службы "Поиск Azure" для контейнера больших двоичных объектов.
> * Создание индекса службы "Поиск Azure", в котором будет находится содержимое для поиска.
> * Настройка и запуск индексатора для чтения контейнера и извлечения содержимого для поиска из хранилища BLOB-объектов Azure.
> * Поиск по индексу, который вы только что создали.

> [!NOTE]
> Это руководство основывается на поддержке массивов JSON. В данный момент эта функция находится на этапе предварительной версии в службе "Поиск Azure". Она недоступна на портале. Поэтому используется предварительная версия REST API, предоставляющая эту возможность, и клиентский инструмент REST для вызова API.

## <a name="prerequisites"></a>Предварительные требования

[Создайте службу "Поиск Azure"](search-create-service-portal.md) или [найдите имеющуюся службу](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) в рамках текущей подписки. Вы можете использовать бесплатную службу для выполнения инструкций, описанных в этом учебнике.

[Создайте учетную запись хранения Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) для хранения примера данных.

[Используйте Postman](https://www.getpostman.com/) или другой клиент REST для отправки запросов. Инструкции для настройки HTTP-запроса в Postman приведены в следующем разделе.

## <a name="set-up-postman"></a>Настройка Postman

Запустите Postman и настройте HTTP-запрос. Если вы не работали с этим инструментом, изучите статью [Работа с REST API службы "Поиск Azure" с помощью Postman или Fiddler](search-fiddler.md).

Метод запроса для каждого вызова в этом руководстве — POST. Заголовки ключей — Content-type и api-key. Значения ключей заголовков — application/json и admin key (ключ администратора — это заполнитель для первичного ключа поиска) соответственно. Тело — это расположение фактического содержимого вызова. В зависимости от используемого клиента возможны некоторые различия в способах создания запросов, однако эти являются основными.

  ![Частично структурированный поиск](media/search-semi-structured-data/postmanoverview.png)

Для вызовов REST, описанных в этом руководстве, требуется api-key поиска. api-key можно найти в разделе **Ключи** в службе поиска. Этот api-key необходимо указать в заголовке каждого вызова API (замените "ключ администратора" из предыдущего снимка экрана ним), который необходимо сделать в этом руководстве. Сохраните ключ, так как он необходим для каждого вызова.

  ![Частично структурированный поиск](media/search-semi-structured-data/keys.png)

## <a name="prepare-sample-data"></a>Подготовка примера данных

1. **Скачайте файл [clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip)** и распакуйте его в собственной папке. Источником данных, преобразованных в JSON для этого учебника, является [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results).

2. Войдите на [портал Azure](https://portal.azure.com), перейдите к учетной записи хранения Azure, откройте контейнер **данных** и щелкните **Загрузить**.

3. Щелкните **Дополнительно**, введите clinical-trials-json, а затем отправьте скачанные файлы JSON.

  ![Частично структурированный поиск](media/search-semi-structured-data/clinicalupload.png)

После завершения отправки файлы должны появиться в собственной вложенной папке внутри контейнера данных.

## <a name="connect-your-search-service-to-your-container"></a>Подключение службы поиска к контейнеру

Мы используем Postman для трех вызовов API к службе поиска для создания источника данных, индекса и индексатора. Источник данных содержит указатель к вашей учетной записи хранения и данным JSON. Служба поиска создает подключение при отправке данных.

Строка запроса должна содержать API предварительной версии (например, **api-version=2017-11-11-Preview**), а каждый вызов должен возвращать сообщение **201 — Создан ресурс**. В общедоступной версии API пока нет возможности обрабатывать JSON в качестве jsonArray. В настоящее время это можно сделать только в предварительной версии API.

Выполните следующие три вызова API из клиента REST.

## <a name="create-a-data-source"></a>Создание источника данных

Источник данных — это объект службы "Поиск Azure", в котором указываются данные для индексирования.

Конечная точка этого вызова — `https://[service name].search.windows.net/datasources?api-version=2016-09-01-Preview`. Замените `[service name]` именем службы поиска. Для этого вызова требуется указать имя учетной записи хранения и ключ учетной записи хранения. Ключ учетной записи хранения можно найти на портале Azure в разделе учетной записи хранения **Ключи доступа**. На следующем рисунке показано расположение:

  ![Частично структурированный поиск](media/search-semi-structured-data/storagekeys.png)

Обязательно замените `[storage account name]` и `[storage account key]` в тексте вызова перед его выполнением.

```json
{
    "name" : "clinical-trials-json",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
    "container" : { "name" : "data", "query" : "clinical-trials-json" }
}
```

Результат должен выглядеть следующим образом:

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#datasources/$entity",
    "@odata.etag": "\"0x8D505FBC3856C9E\"",
    "name": "clinical-trials-json",
    "description": null,
    "type": "azureblob",
    "subtype": null,
    "credentials": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=[mystorageaccounthere];AccountKey=[[myaccountkeyhere]]];"
    },
    "container": {
        "name": "data",
        "query": "clinical-trials-json"
    },
    "dataChangeDetectionPolicy": null,
    "dataDeletionDetectionPolicy": null
}
```

## <a name="create-an-index"></a>Создание индекса
    
Второй вызов API создает индекс службы "Поиск Azure". Индекс указывает все параметры и их атрибуты.

URL-адрес этого вызова — `https://[service name].search.windows.net/indexes?api-version=2016-09-01-Preview`. Замените `[service name]` именем службы поиска.

Сначала замените URL-адрес. Затем скопируйте и вставьте следующий код в тело и выполните запрос.

```json
{
  "name": "clinical-trials-json-index",  
  "fields": [
  {"name": "FileName", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
  {"name": "Description", "type": "Edm.String", "searchable": true, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
  {"name": "MinimumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
  {"name": "Title", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
  {"name": "URL", "type": "Edm.String", "searchable": false, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
  {"name": "MyURL", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
  {"name": "Gender", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "MaximumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
  {"name": "Summary", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
  {"name": "NCTID", "type": "Edm.String", "key": true, "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
  {"name": "Phase", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "Date", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
  {"name": "OverallStatus", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "OrgStudyId", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": false},
  {"name": "HealthyVolunteers", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "Keywords", "type": "Collection(Edm.String)", "searchable": true, "retrievable": true, "facetable": true, "filterable": false, "sortable": false},
  {"name": "metadata_storage_last_modified", "type":"Edm.DateTimeOffset", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
  {"name": "metadata_storage_size", "type":"Edm.String", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
  {"name": "metadata_content_type", "type":"Edm.String", "searchable": true, "retrievable": true, "filterable": true, "sortable": false}
  ],
  "suggesters": [
  {
    "name": "sg",
    "searchMode": "analyzingInfixMatching",
    "sourceFields": ["Title"]
  }
  ]
}
```

Результат должен выглядеть следующим образом:

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexes/$entity",
    "@odata.etag": "\"0x8D505FC00EDD5FA\"",
    "name": "clinical-trials-json-index",
    "fields": [
        {
            "name": "FileName",
            "type": "Edm.String",
            "searchable": false,
            "filterable": false,
            "retrievable": true,
            "sortable": true,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "retrievable": false,
            "sortable": false,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        ...
          "scoringProfiles": [],
    "defaultScoringProfile": null,
    "corsOptions": null,
    "suggesters": [],
    "analyzers": [],
    "tokenizers": [],
    "tokenFilters": [],
    "charFilters": []
}
```

## <a name="create-and-run-an-indexer"></a>Создание и запуск индексатора

Индексатор подключает источник данных, импортирует данные к целевому индексу поиска и при необходимости предоставляет расписание для автоматизации обновления данных.

URL-адрес этого вызова — `https://[service name].search.windows.net/indexers?api-version=2016-09-01-Preview`. Замените `[service name]` именем службы поиска.

Сначала замените URL-адрес. Затем скопируйте и вставьте следующий код в тело и отправьте запрос. Запрос обрабатывается немедленно. При возвращении ответа вы получите индекс с возможностью полнотекстового поиска.

```json
{
  "name" : "clinical-trials-json-indexer",
  "dataSourceName" : "clinical-trials-json",
  "targetIndexName" : "clinical-trials-json-index",
  "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
}
```

Результат должен выглядеть следующим образом:

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexers/$entity",
    "@odata.etag": "\"0x8D505FDE143D164\"",
    "name": "clinical-trials-json-indexer",
    "description": null,
    "dataSourceName": "clinical-trials-json",
    "targetIndexName": "clinical-trials-json-index",
    "schedule": null,
    "parameters": {
        "batchSize": null,
        "maxFailedItems": null,
        "maxFailedItemsPerBatch": null,
        "base64EncodeKeys": null,
        "configuration": {
            "parsingMode": "jsonArray"
        }
    },
    "fieldMappings": [],
    "enrichers": [],
    "disabled": null
}
```

## <a name="search-your-json-files"></a>Поиск JSON-файлов

Теперь вы можете выполнять запросы к индексу. Для этой задачи используйте [**проводник поиска**](search-explorer.md) на портале.

  ![Неструктурированный поиск](media/search-semi-structured-data/indexespane.png)

### <a name="user-defined-metadata-search"></a>Поиск определяемых пользователем метаданных

Как и прежде, данные можно запрашивать несколькими способами: полнотекстовый поиск, системные свойства или определяемые пользователем метаданные. Поиск свойств системы и определяемых пользователем метаданных можно выполнить только с параметром `$select`, если они были помечены как **извлекаемые** во время создания целевого индекса. Параметры индекса невозможно изменить после создания, но можно добавить дополнительные параметры.

Пример простого запроса — `$select=Gender,metadata_storage_size`, который ограничивает возвращаемые значения этими двумя параметрами.

  ![Частично структурированный поиск](media/search-semi-structured-data/lastquery.png)

Пример более сложного запроса — `$filter=MinimumAge ge 30 and MaximumAge lt 75`, который возвращает только результаты, в которых значение параметра MinimumAge не меньше 30, а MaximumAge — не больше 75.

  ![Частично структурированный поиск](media/search-semi-structured-data/metadatashort.png)

Поэкспериментируйте и попробуйте создать несколько дополнительных запросов самостоятельно, если хотите. Вы можете использовать логические операторы (and, or, not) и операторы сравнения (eq, ne, gt, lt, ge, le). При сравнении строк учитывается регистр.

Параметр `$filter` работает только с метаданными, которые отмечены как фильтруемые при создании индекса.

## <a name="clean-up-resources"></a>Очистка ресурсов

Самый быстрый способ очистки по завершении работы с руководством — удалить группу ресурсов, содержащую службу "Поиск Azure". Теперь можно удалить группу ресурсов вместе со всем ее содержимым без возможности восстановления. На портале имя группы ресурсов находится на странице "Обзор" службы "Поиск Azure".

## <a name="next-steps"></a>Дополнительная информация

Управляемые ИИ алгоритмы можно включить в конвейер индексатора. Дальнейшие действия см. в следующем руководстве.

> [!div class="nextstepaction"]
> [Индексирование документов в хранилище BLOB-объектов Azure с помощью службы поиска Azure](search-howto-indexing-azure-blob-storage.md)