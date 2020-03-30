---
title: Индексы для сканирования данных во время импорта
titleSuffix: Azure Cognitive Search
description: Для извлечения данных для поиска и заполнения индекса когнитивного поиска для получения данных Azure и заполнения индекса когнитивного поиска для получения данных Azure, Azure Cosmos DB, Azure Cosmos DB или хранилища Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 2719bba0e88ba3125bd5ba163804e31885b286a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282995"
---
# <a name="indexers-in-azure-cognitive-search"></a>Indexers in Azure Cognitive Search (Индексаторы в службе "Когнитивный поиск Azure")

*Индексатор* в Azure Cognitive Search — это сканер, который извлекает данные и метаданные из внешнего источника данных Azure и заполняет индекс, основанный на отображении поля к полю между индексом и вашим источником данных. Этот подход иногда называют «моделью вытягивания», потому что служба втягивает данные без необходимости писать какой-либо код, который добавляет данные к индексу.

Индексы основаны на типах источников данных или платформах, а отдельные индексы для S'L Server — на Azure, Cosmos DB, Azure Table Storage и Blob Storage. Индексы хранения blob обладают дополнительными свойствами, характерными для типов содержимого blob.

Индексатор можно использовать как отдельное средство для приема данных. Кроме того, можно использовать комбинацию методов, предусматривающих использование индексатора для загрузки только некоторых полей в индексе.

Можно запускать индексы по требованию или по расписанию обновления периодических данных, который выполняется так часто, как каждые пять минут. Более частые обновления требуют модели push, которая одновременно обновляет данные как в Azure Cognitive Search, так и во внешнем источнике данных.

## <a name="approaches-for-creating-and-managing-indexers"></a>Способы создания индексаторов и управления ими

Далее представлены способы создания индексаторов и управления ими.

* [Мастер данных портала > импорта](search-import-data-portal.md)
* [API REST службы](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* [Пакет SDK для .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations)

Изначально новый индексатор объявляется в качестве предварительной версии функции. Предварительные версии функций добавляют в API (REST и .NET), а затем после выхода общедоступной версии интегрируются на портале. При оценке нового индексатора следует запланировать написание кода.

## <a name="permissions"></a>Разрешения

Все операции, связанные с индексаторами, включая запросы GET на статус или определения, требуют [admin api-key.](search-security-api-keys.md) 

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Поддерживаемые источники данных

Данные о сканировании, выполненном индексаторами, хранятся в Azure.

* [Хранение Azure Blob](search-howto-indexing-azure-blob-storage.md)
* [Хранение озер овеки Лазурных данных Gen2](search-howto-index-azure-data-lake-storage.md) (в предварительном просмотре)
* [Хранение таблиц ы Azure](search-howto-indexing-azure-tables.md)
* [Azure Космос DB](search-howto-index-cosmosdb.md)
* [База данных SQL Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [SQL Server на виртуальных машинах Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)
* [Управляемые экземпляры S'L в Azure](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

## <a name="basic-configuration-steps"></a>Основные этапы настройки
В индексаторах реализованы уникальные функции для работы с источниками данных. Поэтому тип индексатора будет определять особенности настройки источника данных или индексатора. Тем не менее всем индексаторам присущи сходные структура и требования. Ниже описаны действия, общие для всех индексаторов.

### <a name="step-1-create-a-data-source"></a>Шаг 1. Создание источника данных
Индексер получает подключение источника данных от объекта *источника данных.* Определение источника данных обеспечивает строку соединения и, возможно, учетные данные. Чтобы создать ресурс, вызовите REST API для [создания источника данных](https://docs.microsoft.com/rest/api/searchservice/create-data-source) или [класс DataSource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource).

Источники данных настраиваются и управляются независимо от индексаторов, которые их используют. Это означает, что источник данных может использоваться несколькими индексаторами для одновременной загрузки нескольких индексов.

### <a name="step-2-create-an-index"></a>Шаг 2. Создание индекса
Хотя индексатор автоматизирует некоторые задачи, связанные с приемом данных, это обычно не распространяется на создание индекса. Перед началом работы у вас должен быть стандартный индекс с полями, которые соответствуют полям во внешнем источнике данных. Поля должны совпадать по имени и типу данных. Для получения дополнительной информации о структурировании индекса [см. Создать индекс (Azure Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/Create-Index) или [класс индекса](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index). Для помощи в [Field mappings in Azure Cognitive Search indexers](search-indexer-field-mappings.md)полевых ассоциациях см.

> [!Tip]
> Хотя индексаторы не могут создавать индексы, вы можете это сделать с помощью мастера **импорта данных**. В большинстве случаев мастер может определить схему индекса на основе существующих метаданных в источнике, предоставляя предварительную схему, которую можно изменить в мастере во время его работы. После создания индекса в службе последующие изменения на портале ограничиваются добавлением новых полей. Используйте мастер для создания индекса, но не для изменения. Чтобы пройти практическое обучение, перейдите к [пошаговому руководству по порталу](search-get-started-portal.md).

### <a name="step-3-create-and-schedule-the-indexer"></a>Шаг 3. Создание и планирование индексатора
Определение индекса представляет собой конструкцию, которая объединяет все элементы, связанные с проглатыванием данных. Необходимые элементы включают источник данных и индекс. Дополнительные элементы включают график и полевые отображения. Полевое картирование не обязательно, если исходные поля и поля индекса четко соответствуют. Для получения дополнительной информации о структурировании индекса [см.](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer)

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>Запуск индексеров по требованию

Хотя обычно индексация запланировать, индексер также может вызываться по запросу с помощью [команды Run:](https://docs.microsoft.com/rest/api/searchservice/run-indexer)

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2019-05-06
    api-key: [Search service admin key]

> [!NOTE]
> При успешном возвращении API планируется вызов индексатора, но фактическая обработка происходит асинхронно. 

Вы можете контролировать состояние индекса на портале или через Get Indexer Status API. 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>Получить статус индекса

Вы можете получить историю состояния и выполнения индекса с помощью [команды Get Indexer Status:](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)


    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

Ответ содержит сведения об общем состоянии индексатора, последнем (или текущем) вызове индексатора, а также журнал последних вызовов индексатора.

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

История выполнения включает не более 50 последних завершенных выполнений, которые сортируются в обратном хронологическом порядке (то есть в ответе первым отображается последнее выполнение).

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы ознакомились с основной идеей, можно переходить к изучению требований и задач, связанных с каждым типом источника данных.

* [База данных Azure SQL (или SQL Server на виртуальных машинах Azure);](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Космос DB](search-howto-index-cosmosdb.md)
* [Хранение Azure Blob](search-howto-indexing-azure-blob-storage.md)
* [Хранение таблиц ы Azure](search-howto-indexing-azure-tables.md)
* [Индексирование CSV капли с помощью индекса Azure когнитивного поиска Blob](search-howto-index-csv-blobs.md)
* [Индексирование JSON капли с Azure когнитивного поиска Blob указатель](search-howto-index-json-blobs.md)
