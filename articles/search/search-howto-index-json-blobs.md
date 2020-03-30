---
title: Поиск по JSON капли
titleSuffix: Azure Cognitive Search
description: Crawl Azure JSON капли для текстового содержимого с помощью индекса Azure когнитивного поиска Blob. Индексаторы автоматизируют прием данных из выбранных источников, таких как хранилище BLOB-объектов Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 37fc78971124240077a59d4ad99aa06cc408dbae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533972"
---
# <a name="how-to-index-json-blobs-using-a-blob-indexer-in-azure-cognitive-search"></a>Как индексировать капли JSON с помощью индекса Blob в Azure Cognitive Search

В этой статье показано, как настроить [индекса](search-indexer-overview.md) Azure Cognitive Search blob для извлечения структурированного содержимого из документов JSON в хранилище Azure Blob и сделать его пригодным для поиска в Azure Cognitive Search. Этот рабочий процесс создает индекс когнитивного поиска Azure и загружает его существующим текстом, извлеченным из blobs JSON. 

Вы можете использовать [портал](#json-indexer-portal), [REST API](#json-indexer-rest) или [пакет SDK для .NET](#json-indexer-dotnet) для индексирования содержимого JSON. Общим для всех подходов является то, что документы JSON находятся в контейнере с каплями в учетной записи хранения Azure. Для получения рекомендаций по продвижению документов JSON [Data import in Azure Cognitive Search](search-what-is-data-import.md)с других платформ, не относясь от Azure, см.

Капли JSON в хранилище Azure Blob, как правило, являются либо `json`одним документом JSON (режим разбора) или коллекцией сущностj JSON. Для коллекций, капля может иметь **массив** хорошо сформированных элементов `jsonArray`JSON (режим разбора). Blobs также может состоять из нескольких отдельных сущностей JSON, `jsonLines`разделенных новой линией (режим разбора). Параметр **разбора** на запросе определяет выходные структуры.

> [!NOTE]
> Для получения дополнительной информации об индексации нескольких поисковых документов из одной капли [см.](search-howto-index-one-to-many-blobs.md)

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>Использование портала

Самый простой способ индексирования документов JSON — использование мастера на [портале Azure](https://portal.azure.com/). Путем синтаксического анализа метаданных в контейнере больших двоичных объектов Azure мастер [**импорта данных**](search-import-data-portal.md) может создать индекс по умолчанию, сопоставить поля источника с целевыми полями индекса и загрузить индекс в рамках одной операции. В зависимости от размера и сложности исходных данных можно создать рабочий индекс полнотекстового поиска за считаные минуты.

Мы рекомендуем использовать один и тот же регион или месторасположение как для Azure Cognitive Search, так и для хранения Azure для более низкой задержки и для избежания платы за пропускную способность.

### <a name="1---prepare-source-data"></a>1. Подготовка исходных данных

[Вопийте на порталaz](https://portal.azure.com/) и [создайте контейнер Blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) для хранения данных. Уровень общественного доступа может быть установлен на любой из его действительных значений.

Для извлечения данных в мастере **данных Импорта** вам понадобится имя учетной записи хранилища, имя контейнера и ключ доступа.

### <a name="2---start-import-data-wizard"></a>2. Запуск мастера импорта данных

На странице «Обзор» службы поиска можно [запустить мастера](search-import-data-portal.md) из панели команд.

   ![Команда данных импорта на портале](./media/search-import-data-portal/import-data-cmd2.png "Запуск мастера импорта данных")

### <a name="3---set-the-data-source"></a>3. Настройка источника данных

На странице **Источник данных** должен быть указан источник **Хранилище BLOB-объектов Azure** со следующими спецификациями.

+ Для параметра **Извлекаемые данные** выберите *Контент и метаданные*. При выборе этого варианта мастер может определить схему индекса и сопоставить поля для импорта.
   
+ **Режим разбора** должен быть установлен на линии *JSON,* *JSON* или *JSON.* 

  *JSON* определяет каждый большой двоичный объект как один поисковой документ, отображающийся как независимый элемент в результатах поиска. 

  *Массив JSON* предназначен для капли, которые содержат хорошо сформированные данные JSON - хорошо сформированный JSON соответствует массиву объектов, или имеет свойство, которое представляет собой массив объектов, и вы хотите, чтобы каждый элемент был сформулирован как автономный, независимый поисковый документ. Если большие двоичные объекты являются составными и вы не выберете *Массив JSON*, весь большой двоичный объект будет обработан как один документ.

  *Линии JSON* предназначены для капли, состоящие из нескольких сущностей JSON, разделенных новой линией, где вы хотите, чтобы каждая сущность была сформулирована как автономный независимый поисковый документ. Если капли сложны, и вы не выбираете режим разбора *линий JSON,* то вся капля попадает в единый документ.
   
+ Для параметра **Контейнер хранилища** укажите свою учетную запись хранения и контейнер или строку подключения, которая разрешается к контейнеру. Строки подключения можно получить на странице портала службы BLOB-объектов.

   ![Определение источника данных BLOB-объектов](media/search-howto-index-json/import-wizard-json-data-source.png)

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4 - Пропустить страницу "Обогащение содержимого" в мастере

Добавление когнитивных навыков (или обогащения) не является требованием импорта. Если у вас нет конкретной необходимости [добавлять обогащение ИИ](cognitive-search-concept-intro.md) в конвейер индексирования, вы должны пропустить этот шаг.

Чтобы пропустить шаг, нажмите синие кнопки в нижней части страницы для "Следующий" и "Пропустить".

### <a name="5---set-index-attributes"></a>5. Настройка атрибутов индекса

На странице **Индекс** вы увидите список полей с типом данных и ряд флажков для настройки атрибутов индекса. Мастер может создать список полей на основе метаданных и выборки исходных данных. 

Вы можете навалом выбрать атрибуты, нажав на флажок в верхней части столбца атрибута. Выберите **Retrievable** и **Searchable** для каждого поля, которое должно быть возвращено в клиентское приложение и при условии полной обработки текста. Вы заметите, что целые числа не полный текст или нечеткой поиска (цифры оцениваются дословно и часто полезны в фильтрах).

Для получения дополнительной информации просмотрите описание [атрибутов индекса](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) и [языковых анализаторов.](https://docs.microsoft.com/rest/api/searchservice/language-support) 

Просмотрите выбранные параметры. После запуска мастера создаются структуры физических данных и вы не сможете изменить эти поля без удаления и повторного создания всех объектов.

   ![Определение индексов больших двоичных объектов](media/search-howto-index-json/import-wizard-json-index.png)

### <a name="6---create-indexer"></a>6. Создание индексатора

Полностью настроенный мастер создает три разных объекта в службе поиска. Объект источника данных и объект индекса сохраняются как именованные ресурсы в службе когнитивного поиска Azure. На последнем шаге создает объект индексатора. Если присвоить индексатору имя, он будет существовать как отдельный ресурс, который можно запланировать и контролировать независимо от индекса и объекта источника данных, созданных в том же процессе мастера.

Если вы не знакомы с индексаторами, *индексатор* — это ресурс в Azure Cognitive Search, который сканирует внешний источник данных для поиска содержимого. Выход мастера **данных импорта** — это индексер, который сканирует ваш источник данных JSON, извлекает содержимое поиска и импортирует его в индекс в Azure Cognitive Search.

   ![Определение индексатора больших двоичных объектов](media/search-howto-index-json/import-wizard-json-indexer.png)

Нажмите кнопку **OK** для запуска мастера и создания всех объектов. Индексирование начинается немедленно.

Вы можете отслеживать импорт данных на страницах портала. Уведомления о ходе выполнения указывают состояние индексирования и количество передаваемых документов. 

По завершении индексирования можно использовать [Проводник поиска](search-explorer.md), чтобы отправить запрос индексу.

> [!NOTE]
> Если вы не видите ожидаемые данные, возможно, потребуется установить больше атрибутов на большем количестве полей. Удалите только что созданный индекс и индексер и повторите через мастер, изменив выбор атрибутов индекса в шаге 5. 

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>Использование REST API

Можно использовать REST API для индексирования капли JSON, следуя трехчастей рабочего процесса, общего для всех индексеров в Azure Cognitive Search: создать источник данных, создать индекс, создать индекс. Извлечение данных из хранилища капли происходит при отправке запроса Create Indexer. После завершения этого запроса у вас будет запрашиваемый индекс. 

В конце этого раздела можно просмотреть [пример кода REST,](#rest-example) который показывает, как создать все три объекта. В этом разделе также содержится подробная информация о [режимах разбора JSON,](#parsing-modes) [одиночных каплях,](#parsing-single-blobs) [массивах JSON](#parsing-arrays)и [вложенных массивах.](#nested-json-arrays)

Для индексирования JSON на основе кода используйте [Postman](search-get-started-postman.md) и REST API для создания следующих объектов:

+ [index](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [источник данных](https://docs.microsoft.com/rest/api/searchservice/create-data-source)
+ [Индексатор](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

Порядок операций требует, чтобы вы создавали и вызывать объекты в этом порядке. В отличие от рабочего процесса портала, кодовый подход требует доступного индекса для приема документов JSON, отправленных через запрос **Create Indexer.**

Капли JSON в хранилище Azure Blob, как правило, являются либо одним документом JSON, либо "арбойом" JSON. Индексатор blob в Azure Cognitive Search может разбирать конструкцию в зависимости от того, как вы устанавливаете параметр **разбора** в запросе.

| Документ JSON | parsingMode | Описание | Доступность |
|--------------|-------------|--------------|--------------|
| Один на большой двоичный объект | `json` | Анализирует большие двоичные объекты JSON как отдельный блок текста. Каждая капля JSON становится единым документом Azure Cognitive Search. | Как правило, доступны как в [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API, так и [в .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |
| Несколько на большой двоичный объект | `jsonArray` | Сравнивает массив JSON в blob, где каждый элемент массива становится отдельным документом Azure Cognitive Search.  | Как правило, доступны как в [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API, так и [в .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |
| Несколько на большой двоичный объект | `jsonLines` | Сравнивает каплю, содержащую несколько сущностей JSON ("массив"), разделенных новой строкой, где каждая сущность становится отдельным документом Azure Cognitive Search. | Как правило, доступны как в [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API, так и [в .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |

### <a name="1---assemble-inputs-for-the-request"></a>1 - Сборка входных данных для запроса

Для каждого запроса необходимо учесть имя службы и ключ администратора для Azure Cognitive Search (в заголовке POST), а также имя учетной записи и ключ для хранения капли. Вы можете использовать [Postman](search-get-started-postman.md) для отправки запросов HTTP в Azure Cognitive Search.

Скопируйте следующие четыре значения в блокнот, чтобы вы могли вставить их в запрос:

+ Имя службы когнитивного поиска Azure
+ Ключ админ-поиска Azure Cognitive
+ Имя учетной записи хранения Azure
+ Ключ учетной записи хранения Azure

Вы можете найти эти значения на портале:

1. На страницах портала для Azure Cognitive Search скопируйте URL поискового сервиса со страницы «Обзор».

2. В левом панели навигации щелкните **ключи,** а затем скопируйте либо основной или вторичный ключ (они эквивалентны).

3. Переключитесь на страницы портала для учетной записи хранилища. В левом навигационном стежке, под **настройками,** нажмите **клавиши доступа.** На этой странице приводится как имя учетной записи, так и ключ. Копируйте имя учетной записи хранилища и один из ключей к блокноту.

### <a name="2---create-a-data-source"></a>2 - Создание источника данных

Этот шаг предоставляет информацию об источнике связи данных, используемую индексатором. Источник данных является названным объектом в Azure Cognitive Search, который сохраняет информацию о подключении. Тип источника данных, определяет, `azureblob`какие поведения извлечения данных вызываются индексатором. 

Заменить допустимые значения для имени службы, ключа администратора, учетной записи хранилища и заполнителей ключей учетной записи.

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="3---create-a-target-search-index"></a>3 - Создание целевого индекса поиска 

Индексаторы связываются со схемой индекса. При использовании API-интерфейса (а не портала) необходимо предварительно подготовить индекс для указания в операции индексатора.

Индекс хранит поисковый контент в Azure Cognitive Search. Чтобы создать индекс, предоставьте схему, которая задает поля в документе, атрибуты и другие компоненты, которые определяют процедуру поиска. Если вы создаете индекс, который имеет те же имена полей и типы данных в качестве источника, индексатор будет сопоставлять поля источника и назначения и вам не придется явным образом сопоставлять эти поля.

В следующем примере показан запрос [Создать индекс](https://docs.microsoft.com/rest/api/searchservice/create-index). У индекса будет доступное для поиска поле `content` для хранения текста, извлеченного из больших двоичных объектов:   

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="4---configure-and-run-the-indexer"></a>4 - Настройка и запуск индекса

Как и в обители и источнике данных, и индексатор также является названным объектом, который создается и повторно используется в службе когнитивного поиска Azure. Полностью указанный запрос на создание индекса может выглядеть следующим образом:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

Конфигурация индекса находится в теле запроса. Для этого требуется источник данных и пустой целевой индекс, который уже существует в Azure Cognitive Search. 

Расписание и параметры не являются обязательными. Если вы опустете их, индексер работает немедленно, используя `json` в качестве режима разбора.

Данный индексер не включает полевые отображения. В определении индекса можно оставить **вне поля отображения,** если свойства документа JSON совпадают с полями индекса целевого поиска. 


### <a name="rest-example"></a>Пример REST

Этот раздел представляет собой повторение всех запросов, используемых для создания объектов. Для обсуждения компонентов см.

### <a name="data-source-request"></a>Запрос источника данных

Всем индексам требуется объект источника данных, который предоставляет информацию о подключении к существующим данным. 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }  


### <a name="index-request"></a>Запрос индекса

Всем индексам требуется целевой индекс, который получает данные. Тело запроса определяет схему индекса, состоящую из полей, приписываемых для поддержки желаемого поведения в индексе поиска. Этот индекс должен быть пустым при запуске индекса. 

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="indexer-request"></a>Запрос на индексатор

Этот запрос показывает полностью заданный индексер. Она включает в себя полевые картографы, которые были опущены в предыдущих примерах. Напомним, что "расписание", "параметры" и "полевые макинги" не являются обязательными при наличии доступного по умолчанию. Опуская "расписание" приводит к немедленному запуску индекса. Опуская "parsingMode" приводит к тому, что индекс использует "json" по умолчанию.

Создание индекса на Azure Cognitive Search запускает импорт данных. Он работает немедленно, а затем по расписанию, если вы предоставили один.

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } },
      "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
        ]
    }


<a name="json-indexer-dotnet"></a>

## <a name="use-net-sdk"></a>Использование пакета SDK для .NET

SDK .NET имеет полный паритет с REST API. Мы рекомендуем прочитать предыдущий раздел о REST API, чтобы ознакомиться с концепциями, рабочим процессом и требованиями. Используйте следующую справочную документацию по .NET API для реализации индексатора JSON в управляемом коде.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="parsing-modes"></a>

## <a name="parsing-modes"></a>Режимы разбора

JSON капли могут принимать несколько форм. Параметр **разбора** на индексе jSON определяет, как содержимое Blob JSON разбирается и структурировано в индексе когнитивного поиска Azure:

| parsingMode | Описание |
|-------------|-------------|
| `json`  | Индексировать каждую каплю в виде одного документа. Это значение по умолчанию. |
| `jsonArray` | Выберите этот режим, если ваши капли состоят из массивов JSON, и вам нужно, чтобы каждый элемент массива стал отдельным документом в Azure Cognitive Search. |
|`jsonLines` | Выберите этот режим, если ваши капли состоят из нескольких сущностей JSON, которые разделены новой строкой, и вам нужно, чтобы каждая сущность стала отдельным документом в Azure Cognitive Search. |

Документ можно представить себе как отдельный элемент в результатах поиска. Если вы хотите, чтобы каждый элемент в массиве отображал результаты поиска в качестве независимого элемента, используйте при необходимости или `jsonArray` `jsonLines` опцию.

В определении индексатора можно использовать [сопоставление полей](search-indexer-field-mappings.md) для выбора свойств исходного документа JSON, применяемых для заполнения целевого индекса поиска. Для `jsonArray` разразборки, если массив существует как свойство более низкого уровня, можно установить корень документа, указывающий, где массив находится внутри капли.

> [!IMPORTANT]
> При `json`использовании `jsonArray` `jsonLines` или анализе режима Azure Cognitive Search предполагает, что все капли в источнике данных содержат JSON. Если необходима поддержка как объектов JSON, так и других объектов в одном источнике данных, сообщите нам об этом на [нашем сайте UserVoice](https://feedback.azure.com/forums/263029-azure-search).


<a name="parsing-single-blobs"></a>

## <a name="parse-single-json-blobs"></a>Парс-сингл JSON капли

По умолчанию [индексер Azure Cognitive Search](search-howto-indexing-azure-blob-storage.md) разбирает капли JSON как один кусок текста. Часто требуется сохранить структуру документов JSON. Например, предположим, что в хранилище BLOB-объектов Azure имеется следующий документ JSON:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Индексатор blob разбирает документ JSON в единый документ Azure Cognitive Search. Индексатор загружает индекс, сопоставляя значения text, datePublished и tags из источника с идентичными по названию и типу целевыми полями индекса.

Как уже отмечалось, сопоставления полей необязательны. Если имеется индекс с полями "text", "datePublished и "tags", индексатор больших двоичных объектов может определить правильное сопоставление без наличия сопоставления полей в запросе.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>Массивы Parse JSON

Кроме того, можно использовать опцию массива JSON. Эта опция полезна, когда капли содержат *массив хорошо сформированных объектов JSON,* и вы хотите, чтобы каждый элемент стал отдельным документом Azure Cognitive Search. Например, учитывая следующий blob JSON, можно заполнить индекс когнитивного поиска Azure тремя отдельными документами, каждый из которых с полями "id" и "text".  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

Для массива JSON определение индексатора должно выглядеть, как в следующем примере. Обратите внимание, что параметр parsingMode указывает средство синтаксического анализа `jsonArray`. Определение правильного парзера и наличие правильного ввода данных являются единственными двумя конкретными требованиями для индексации капли JSON.

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

Снова отметим, что сопоставления полей необязательны. Если имеется индекс с полями с аналогичными именами id и text, индексатор больших двоичных объектов может определить правильное сопоставление без явного списка сопоставления полей.

<a name="nested-json-arrays"></a>

## <a name="parse-nested-arrays"></a>Парс вложенные массивы
Для массивов JSON, имеющих вложенные `documentRoot` элементы, можно указать многоуровневую структуру. Например, если большой двоичный объект выглядит следующим образом:

    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }

Используйте эту конфигурацию для индексации массива, содержащегося в свойстве `level2`:

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }

## <a name="parse-blobs-separated-by-newlines"></a>Парс капли разделены новыми линиями

Если в blob содержится несколько сущностей JSON, разделенных новой строкой, и вы хотите, чтобы каждый элемент стал отдельным документом Azure Cognitive Search, вы можете выбрать опцию JSON lines. Например, учитывая следующие капли (где есть три различных сущности JSON), можно заполнить свой индекс когнитивного поиска Azure тремя отдельными документами, каждый из которых с "id" и "текстовые" поля.

    { "id" : "1", "text" : "example 1" }
    { "id" : "2", "text" : "example 2" }
    { "id" : "3", "text" : "example 3" }

Для строк JSON определение индекса должно выглядеть так же, как и в следующем примере. Обратите внимание, что параметр parsingMode указывает средство синтаксического анализа `jsonLines`. 

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
    }

Опять же, обратите внимание, что полевая `jsonArray` картография может быть опущена, подобно режиму разбора.

## <a name="add-field-mappings"></a>Добавление карт полевых карт

Если исходные и целевые поля не полностью согласованы, можно определить область сопоставления полей в тексте запроса для их явного сопоставления.

В настоящее время Azure Cognitive Search не может индексировать произвольные документы JSON напрямую, поскольку он поддерживает только примитивные типы данных, строю и точки GeoJSON. Однако с помощью **сопоставления полей** можно выбирать части документа JSON и "поднимать" до полей верхнего уровня документа поиска. Чтобы узнать об основах карт [Field mappings in Azure Cognitive Search indexers](search-indexer-field-mappings.md)полевых карт, см.

Вернемся к нашему примеру документа JSON:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Допустим, что у вас есть индекс поиска со следующими полями: `text` типа `Edm.String`, `date` типа `Edm.DateTimeOffset` и `tags` типа `Collection(Edm.String)`. Обратите внимание на несоответствие между полем "datePublished" в источнике и полем `date` в индексе. Чтобы сопоставить JSON с необходимой формой, используйте следующие сопоставления полей:

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

Имена полей источника в сопоставлениях задаются с помощью нотации [указателя JSON](https://tools.ietf.org/html/rfc6901) . Укажите косую черту (корень документа JSON) и путь до нужного свойства (на произвольном уровне вложенности), разделяя элементы пути косой чертой.

Также можно ссылаться на отдельные элементы массива, используя отсчитываемый от нуля индекс. Например, чтобы выбрать первый элемент массива "tags" из приведенного выше примера, используйте следующее сопоставление полей:

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> Если имя поля источника в пути сопоставления полей ссылается на свойство, которое не существует в JSON, это сопоставление пропускается без ошибки. Это необходимо для поддержки документов с разными схемами (что часто встречается на практике). Поскольку проверка на ошибки не выполняется, будьте внимательны и не допускайте опечаток в спецификации сопоставления полей.
>
>

## <a name="see-also"></a>См. также

+ [Indexers in Azure Cognitive Search](search-indexer-overview.md) (Индексаторы в службе "Когнитивный поиск Azure")
+ [Индексирование хранилища Azure Blob с помощью когнитивного поиска Azure](search-howto-index-json-blobs.md)
+ [Индексирование CSV капли с Azure когнитивного поиска каплю индекса](search-howto-index-csv-blobs.md)
+ [Учебник: Поиск полуструктурированных данных из хранилища Azure Blob](search-semi-structured-data.md)
