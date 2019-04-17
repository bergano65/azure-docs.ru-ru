---
title: Руководство по индексированию частично структурированных данных в больших двоичных объектах JSON в службе "Поиск Azure"
description: Сведения об индексировании и поиске частично структурированных больших двоичных объектов Azure JSON с помощью службы "Поиск Azure" и Postman.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 4df64595f83bd7280fa781f27f3030eda3729911
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/10/2019
ms.locfileid: "59471466"
---
# <a name="tutorial-index-and-search-semi-structured-data-json-blobs-in-azure-search"></a>Руководство по индексированию и поиску частично структурированных данных (больших двоичных объектов JSON) в службе "Поиск Azure"

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

В этом кратком руководстве используются приведенные ниже службы, инструменты и данные. 

[Создайте службу "Поиск Azure"](search-create-service-portal.md) или [найдите имеющуюся службу](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) в рамках текущей подписки. Вы можете использовать бесплатную службу для выполнения инструкций, описанных в этом учебнике. 

[Создайте учетную запись хранения Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) для хранения примера данных.

Используйте [классическое приложение Postman](https://www.getpostman.com/) для отправки запросов в службу "Поиск Azure".

Архив [clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip) содержит данные, используемые в этом учебнике. Скачайте этот файл и распакуйте его в собственной папке. Источником данных, преобразованных в JSON для этого учебника, является [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results).

## <a name="get-a-key-and-url"></a>Получение ключа и URL-адреса

Вызовам REST требуется URL-адрес службы и ключ доступа при каждом запросе. Служба поиска создана с обоими элементами, поэтому если вы добавили службу "Поиск Azure" в подписку, выполните следующие действия для получения необходимых сведений:

1. [Войдите на портал Azure](https://portal.azure.com/) и на странице **обзора** службы поиска получите URL-адрес. Пример конечной точки может выглядеть так: `https://mydemo.search.windows.net`.

1. В разделе **Параметры** > **Ключи** получите ключ администратора, чтобы обрести полные права на службу. Существуют два взаимозаменяемых ключа администратора, предназначенных для обеспечения непрерывности бизнес-процессов на случай, если вам потребуется сменить один из них. Вы можете использовать первичный или вторичный ключ для выполнения запросов на добавление, изменение и удаление объектов.

![Получение конечной точки HTTP и ключа доступа](media/search-fiddler/get-url-key.png "Получение конечной точки HTTP и ключа доступа")

Для выполнения любого запроса к службе требуется использование ключа API. Если есть действительный ключ, для каждого запроса устанавливаются отношения доверия между приложением, которое отправляет запрос, и службой, которая его обрабатывает.

## <a name="prepare-sample-data"></a>Подготовка примера данных

1. [Войдите на портал Azure](https://portal.azure.com), перейдите к учетной записи хранения Azure, выберите **Большие двоичные объекты**, а затем щелкните **Контейнер**.

1. [Создайте контейнер больших двоичных объектов](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) для хранения примера данных. Так как для подключения используются ключ и имя учетной записи хранения, убедитесь, что для общедоступного уровня контейнера установлено значение Container (anonymous read access for container) (Контейнер (анонимный доступ для чтения к контейнеру)).

   ![Настройка общедоступного уровня контейнера](media/search-semi-structured-data/container-public-access-level.png "Set public access level")

1. Откройте контейнер после создания и на панели команд выберите **Загрузить**.

   ![Кнопка "Загрузить" на панели команд](media/search-semi-structured-data/upload-command-bar.png "Upload on command bar")

1. Перейдите к папке, содержащей примеры файлов. Выберите их, а затем щелкните **Загрузить**.

   ![Загрузка файлов](media/search-semi-structured-data/clinicalupload.png "Upload files")

После завершения отправки файлы должны появиться в собственной вложенной папке внутри контейнера данных.

## <a name="set-up-postman"></a>Настройка Postman

Запустите Postman и настройте HTTP-запрос. Если вы не работали с этим инструментом, изучите статью [Работа с REST API службы "Поиск Azure" с помощью Postman или Fiddler](search-fiddler.md).

Для каждого вызова в этом учебнике используется метод запроса **POST**. Заголовки ключей — Content-type и api-key. Значения ключей заголовков — application/json и admin key (ключ администратора — это заполнитель для первичного ключа поиска) соответственно. Тело — это расположение фактического содержимого вызова. В зависимости от используемого клиента возможны некоторые различия в способах создания запросов, однако эти являются основными.

  ![Частично структурированный поиск](media/search-semi-structured-data/postmanoverview.png)

Мы используем Postman для трех вызовов API к службе поиска для создания источника данных, индекса и индексатора. Источник данных содержит указатель к вашей учетной записи хранения и данным JSON. Служба поиска создает подключение при отправке данных.

Строка запроса должна содержать API предварительной версии (например, **api-version=2017-11-11-Preview**), а каждый вызов должен возвращать сообщение **201 — Создан ресурс**. В общедоступной версии API пока нет возможности обрабатывать JSON в качестве jsonArray. В настоящее время это можно сделать только в предварительной версии API.

Выполните следующие три вызова API из клиента REST.

## <a name="create-a-data-source"></a>Создание источника данных

[API создания источника данных](https://docs.microsoft.com/rest/api/searchservice/create-data-source) создает объект службы "Поиск Azure", в котором указываются данные для индексирования.

Конечная точка этого вызова — `https://[service name].search.windows.net/datasources?api-version=2016-09-01-Preview`. Замените `[service name]` именем службы поиска. 

Для этого вызова в тексте запроса необходимо указать имя и ключ учетной записи хранения, а также имя контейнера большого двоичного объекта. Ключ учетной записи хранения можно найти на портале Azure в разделе учетной записи хранения **Ключи доступа**. На следующем рисунке показано расположение:

  ![Частично структурированный поиск](media/search-semi-structured-data/storagekeys.png)

Обязательно замените `[storage account name]`, `[storage account key]` и `[blob container name]` в тексте вызова перед его выполнением.

```json
{
    "name" : "clinical-trials-json",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
    "container" : { "name" : "[blob container name]"}
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
        "name": "[mycontainernamehere]",
        "query": null
    },
    "dataChangeDetectionPolicy": null,
    "dataDeletionDetectionPolicy": null
}
```

## <a name="create-an-index"></a>Создание индекса
    
Второй вызов — [API создания индекса](https://docs.microsoft.com/rest/api/searchservice/create-data-source), создающий индекс службы "Поиск Azure", в котором хранятся все доступные для поиска данные. Индекс указывает все параметры и их атрибуты.

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

Индексатор подключает источник данных, импортирует данные к целевому индексу поиска и при необходимости предоставляет расписание для автоматизации обновления данных. REST API — [создание индексатора](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

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

Вы можете начать поиск сразу после загрузки первого документа. Для этой задачи используйте [**проводник поиска**](search-explorer.md) на портале.

На портале Azure откройте страницу**обзора** службы поиска и в списке **Индексы** найдите созданный индекс.

Обязательно выберите только что созданный индекс. Вы можете использовать предварительную или общедоступную версию API. Использование лишь предварительной версии требовалось для индексирования массивов JSON.

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

Вы можете вложить управляемые ИИ алгоритмы Cognitive Services в конвейер индексатора. Дальнейшие действия см. в следующем руководстве.

> [!div class="nextstepaction"]
> [Индексирование с помощью ИИ](cognitive-search-tutorial-blob.md)