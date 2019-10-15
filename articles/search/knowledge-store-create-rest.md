---
title: Создание хранилища знаний с помощью REST (Поиск Azure)
description: Применение REST API и Postman для создания хранилища знаний службы "Поиск Azure" для сохранения дополнений из конвейера когнитивного поиска.
author: lobrien
services: search
ms.service: search
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: laobri
ms.openlocfilehash: b67f0cf60d279c7bc52b4114d29c37847f5c57f1
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244466"
---
# <a name="create-an-azure-search-knowledge-store-by-using-rest"></a>Создание хранилища знаний службы "Поиск Azure" с помощью REST

Хранилище знаний — это компонент службы "Поиск Azure", который сохраняет выходные данные конвейера обогащения, выполняемого с помощью ИИ, для последующего анализа или обработки. Конвейер обогащения с помощью ИИ способен получать файлы изображений или неструктурированные текстовые файлы, отправлять их для индексирования в службу "Поиск Azure", применять результаты дополнения с помощью ИИ, выполняемого в Azure Cognitive Services (например, анализ изображений и обработка естественного языка), а затем сохранять результаты в хранилище знаний в службе хранилища Azure. Вы можете использовать на портале Azure такие средства, как Power BI или Обозреватель службы хранилища, чтобы исследовать хранилище знаний.

В этой статье объясняется, как использовать интерфейс REST API для приема, индексирования и применения дополнений с помощью ИИ к набору отзывов об отеле. Отзывы об отелях импортируются в хранилище BLOB-объектов Azure. Результаты сохраняются как хранилище знаний в хранилище таблиц Azure.

После создания хранилища знаний вы можете узнать, как получить к нему доступ с помощью [Обозревателя службы хранилища](knowledge-store-view-storage-explorer.md) или [Power BI](knowledge-store-connect-power-bi.md).

## <a name="create-services"></a>Создание служб

Создайте следующие службы.

- Создайте экземпляр [службы "Поиск Azure"](search-create-service-portal.md) или [найдите существующую службу](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) в используемой подписке. Вы можете использовать бесплатную службу для выполнения инструкций, описанных в этом учебнике.

- Создайте [учетную запись хранения Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) для хранения тестовых данных и хранилища знаний. Ваша учетная запись хранения должна использовать то же расположение (например, "Запад США") для службы "Поиск Azure". Параметр **Тип учетной записи** хранения должен иметь значение **StorageV2 (версия 2, общего назначения)** (по умолчанию) или **Storage (версия 1, общего назначения)** .

- Рекомендация: Получите [классическое приложение Postman](https://www.getpostman.com/) для отправки запросов к службе "Поиск Azure". REST API можно использовать с любым инструментом, который может работать с HTTP-запросами и ответами. Postman будет хорошим вариантом для изучения интерфейсов REST API. В рамках этой статьи мы используем именно Postman. Кроме того, [исходный код](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json) для этой статьи содержит коллекцию запросов Postman. 

## <a name="store-the-data"></a>Сохранение данных

Загрузите CSV-файл с отзывами об отеле в хранилище BLOB-объектов Azure, чтобы получить доступ к нему через индексатор службы "Поиск Azure" и провести через конвейер обогащения искусственного интеллекта.

### <a name="create-a-blob-container-by-using-the-data"></a>Создание контейнера BLOB-объектов путем применения данных

1. Скачайте [данные отзывов об отелях](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D), сохраненные в CSV-файле (HotelReviews_Free.csv). Эти данные получены с сайта Kaggle.com и содержат отзывы клиентов об отелях.
1. Войдите на [портал Azure](https://portal.azure.com) и перейдите к учетной записи хранения Azure.
1. Создайте [контейнер BLOB-объектов](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). Чтобы создать контейнер, в левом меню для учетной записи хранения щелкните **BLOB-объекты**, а затем — **Container** (Контейнер).
1. Укажите для контейнера **имя** **hotel-reviews**.
1. Для параметра **Уровень общего доступа** выберите произвольное значение. В нашем примере используется значение по умолчанию.
1. Щелкните **ОК**, чтобы создать контейнер BLOB-объектов.
1. Откройте новый контейнер **hotels-review**, щелкните **Отправить** и выберите файл HotelReviews-Free.csv, который вы скачали на первом шаге.

    ![Отправка данных](media/knowledge-store-create-portal/upload-command-bar.png "Отправка отзывов отеля")

1. Щелкните **Отправить**, чтобы импортировать CSV-файл в хранилище BLOB-объектов Azure. Отобразится новый контейнер.

    ![Создание контейнера BLOB-объектов](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Create the blob container")

## <a name="configure-postman"></a>Настройка Postman

Установите и настройте Postman.

### <a name="download-and-install-postman"></a>Скачивание и установка Postman

1. Скачайте [исходный код коллекции Postman](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json).
1. Щелкните **File** > **Import** (Файл > Импорт), чтобы импортировать этот исходный код в Postman.
1. Выберите вкладку **Collections** (Коллекции) и нажмите кнопку **...** (многоточие).
1. Выберите **Изменить** 
   
   ![Приложение Postman с навигацией](media/knowledge-store-create-rest/postman-edit-menu.png "Переход в меню \"Edit\" (Правка) в Postman")
1. В диалоговом окне **Edit** (Правка) выберите вкладку **Variables** (Переменные). 

На вкладке **Переменные** вы можете добавить значения, которые Postman будет заменять во всех местах, где они указаны в двойных скобках. Например, Postman заменит символ `{{admin-key}}` текущим значением, которое вы указали для `admin-key`. Postman выполняет такие замены в URL-адресах, заголовках, текстах запросов и так далее. 

Чтобы получить значение `admin-key`, перейдите в службу "Поиск Azure" и откройте вкладку **Ключи**. Замените `search-service-name` и `storage-account-name` теми значениями, которые вы выбрали на шаге [Создание служб](#create-services). Задайте параметру `storage-connection-string` значение, полученное на вкладке **Ключи доступа** для учетной записи хранения. Для остальных параметров можно сохранить значения по умолчанию.

![Вкладка переменных приложения Postman](media/knowledge-store-create-rest/postman-variables-window.png "Окно переменных Postman")


| Переменная    | Как получить |
|-------------|-----------------|
| `admin-key` | На вкладке **Ключи** в службе "Поиск Azure".  |
| `api-version` | Сохраните значение **2019-05-06-Preview**. |
| `datasource-name` | Сохраните значение **hotel-reviews-ds**. | 
| `indexer-name` | Сохраните значение **hotel-reviews-ixr**. | 
| `index-name` | Сохраните значение **hotel-reviews-ix**. | 
| `search-service-name` | Основное имя службы "Поиск Azure". Введите URL-адрес `https://{{search-service-name}}.search.windows.net`. | 
| `skillset-name` | Сохраните значение **hotel-reviews-ss**. | 
| `storage-account-name` | Основное имя учетной записи хранения. | 
| `storage-connection-string` | На вкладке **Ключи доступа** для учетной записи хранения выберите **key1** > **Строка подключения**. | 
| `storage-container-name` | Сохраните значение **hotel-reviews**. | 

### <a name="review-the-request-collection-in-postman"></a>Обзор коллекции запросов в Postman

При создании хранилища знаний следует отправить четыре HTTP-запроса: 

- **Запрос PUT для создания индекса.** Этот индекс содержит данные, которые служба "Поиск Azure" использует и возвращает.
- **Запрос POST для создания источника данных.** Этот источник данных связывает поведение службы "Поиск Azure" с учетной записью хранения данных и хранилища знаний. 
- **Запрос PUT для создания набора навыков.** Набор навыков определяет дополнения, которые применяются к данным и структуре хранилища знаний.
- **Запрос PUT для создания индексатора.** При выполнении индексатора считываются данные, применяется набор навыков и сохраняются результаты. Этот запрос необходимо выполнить последним.

[Исходный код](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json) содержит коллекцию Postman с этими четырьмя запросами. Чтобы отправить запросы, выберите в Postman вкладку запроса. Добавьте здесь заголовки запроса `api-key` и `Content-Type`. Присвойте параметру `api-key` значение `{{admin-key}}`. Присвойте параметру `Content-type` значение `application/json`. 

![Снимок экрана с интерфейсом Postman для заголовков](media/knowledge-store-create-rest/postman-headers-ui.png)

> [!Note]
> Во всех запросах следует задать заголовки `api-key` и `Content-type`. Если Postman распознает переменную, она отображается оранжевым цветом, как `{{admin-key}}` на предыдущем снимке экрана. Если имя переменной написано с ошибкой, она отображается красным цветом.
>

## <a name="create-an-azure-search-index"></a>Создание индекса Поиска Azure

Создайте индекс службы "Поиск Azure", чтобы представить данные, которые вас интересуют для поиска, фильтрации и улучшений. Для создания индекса отправьте запрос PUT на адрес `https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}`. Postman заменит символы, заключенные в двойные скобки (например `{{search-service-name}}`, `{{index-name}}` и `{{api-version}}`), значениями, указанными на шаге [Настройка Postman](#configure-postman). Если вы используете другой инструмент для выполнения команд REST, вам придется заменить переменные самостоятельно.

Укажите структуру индекса службы "Поиск Azure" в тексте запроса. Настроив заголовки `api-key` и `Content-type` в Postman, перейдите на панель **Body** (Текст) для запроса. Вы увидите приведенный ниже код JSON. Если его здесь нет, выберите **Raw** (Необработанный) > **JSON (application/json)** и вставьте в текст запроса следующий код:

```JSON
{
    "name": "{{index-name}}",
    "fields": [
        { "name": "address", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "categories", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "city", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "country", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "latitude", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "longitude", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "name", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "postalCode", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "province", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_date", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_dateAdded", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_rating", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_text", "type": "Edm.String", "filterable": false,  "sortable": false, "facetable": false },
        { "name": "reviews_title", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_username", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "AzureSearch_DocumentKey", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false, "key": true },
        { "name": "metadata_storage_content_type", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_size", "type": "Edm.Int64", "searchable": false, "filterable": false, "sortable": false, "facetable": false},
        { "name": "metadata_storage_last_modified", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_name", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_path", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "Sentiment", "type": "Collection(Edm.Double)", "searchable": false, "filterable": true, "retrievable": true, "sortable": false, "facetable": true },
        { "name": "Language", "type": "Edm.String", "filterable": true, "sortable": false, "facetable": true },
        { "name": "Keyphrases", "type": "Collection(Edm.String)", "filterable": true, "sortable": false, "facetable": true }
    ]
}

```

Это определение индекса представляет собой комбинацию данных, которые необходимо предоставить пользователю (название отеля, содержание отзыва, дата), метаданных поиска и данных для дополнения с помощью ИИ (тональность, ключевые фразы и язык).

Щелкните **Send** (Отправить), чтобы отправить запрос PUT. Отобразится состояние `201 - Created`. Если вы увидите другое сообщение о состоянии, на панели **Body** (Текст) найдите ответ JSON с сообщением об ошибке. 

## <a name="create-the-datasource"></a>Создание источника данных

Теперь подключите службу "Поиск Azure" к данным об отелях, которые вы сохранили на шаге [Сохранение данных](#store-the-data). Чтобы создать источник данных, отправьте запрос POST на адрес `https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}`. Установите также заголовки `api-key` и `Content-Type`, как упоминалось ранее. 

В интерфейсе Postman перейдите к запросу **Create Datasource** (Создание источника данных) и откройте панель **Body** (Текст). Вы увидите приведенный ниже код.

```json
{
  "name" : "{{datasource-name}}",
  "description" : "Demo files to demonstrate knowledge store capabilities.",
  "type" : "azureblob",
  "credentials" : { "connectionString" : "{{storage-connection-string}}" },
  "container" : { "name" : "{{storage-container-name}}" }
}
```

Щелкните **Send** (Отправить), чтобы отправить запрос POST. 

## <a name="create-the-skillset"></a>Создание набора навыков 

Дальше нужно задать набор навыков, который указывает как применяемые улучшения, так и хранилище знаний, в котором будут храниться результаты. В Postman откройте вкладку **Create the Skillset** (Создание набора навыков). Этот запрос отправляет PUT на `https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}`. Задайте заголовки `api-key` и `Content-type`, как вы уже делали ранее. 

Существует два больших объекта верхнего уровня: `skills` и `knowledgeStore`. Каждый объект внутри объекта `skills` является службой дополнения. Каждая служба добавления имеет `inputs` и `outputs`. Для `LanguageDetectionSkill` определено выходное значение `targetName` с типом `Language`. Значение этого узла используется большинством других навыков в качестве входных данных. Источником является `document/Language`. Возможность использовать выходные данные одного узла в качестве входных данных для другого еще более важна для `ShaperSkill`, который определяет методы передачи данных в таблицы хранилища знаний.

Объект `knowledge_store` подключается к учетной записи хранения через переменную `{{storage-connection-string}}` Postman. `knowledge_store` содержит набор сопоставлений между расширенным документом с одной стороны, таблицами и столбцами в хранилище знаний с другой. 

Чтобы создать набор навыков, нажмите кнопку **Send** (Отправить) в Postman для отправки следующего запроса PUT.

```json
{
    "name": "{{skillset-name}}",
    "description": "Skillset to detect language, extract key phrases, and detect sentiment",
    "skills": [ 
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill", 
            "context": "/document/reviews_text", "textSplitMode": "pages", "maximumPageLength": 5000,
            "inputs": [ 
                { "name": "text", "source": "/document/reviews_text" },
                { "name": "languageCode", "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "textItems", "targetName": "pages" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode", "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "score", "targetName": "Sentiment" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "context": "/document",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text" }
            ],
            "outputs": [
                { "name": "languageCode", "targetName": "Language" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text",  "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode",  "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "keyPhrases" , "targetName": "Keyphrases" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "context": "/document",
            "inputs": [
                { "name": "name",  "source": "/document/name" },
                { "name": "reviews_date",  "source": "/document/reviews_date" },
                { "name": "reviews_rating",  "source": "/document/reviews_rating" },
                { "name": "reviews_text",  "source": "/document/reviews_text" },
                { "name": "reviews_title",  "source": "/document/reviews_title" },
                { "name": "AzureSearch_DocumentKey",  "source": "/document/AzureSearch_DocumentKey" },
                { 
                    "name": "pages",
                    "sourceContext": "/document/reviews_text/pages/*",
                    "inputs": [
                        { "name": "SentimentScore", "source": "/document/reviews_text/pages/*/Sentiment" },
                        { "name": "LanguageCode", "source": "/document/Language" },
                        { "name": "Page", "source": "/document/reviews_text/pages/*" },
                        { 
                            "name": "keyphrase", "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                            "inputs": [
                                { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/Keyphrases/*" }
                            ]
                        }
                    ]
                }
            ],
            "outputs": [
                { "name": "output" , "targetName": "tableprojection" }
            ]
        }
    ],
    "knowledgeStore": {
        "storageConnectionString": "{{storage-connection-string}}",
        "projections": [
            {
                "tables": [
                    { "tableName": "hotelReviewsDocument", "generatedKeyName": "Documentid", "source": "/document/tableprojection" },
                    { "tableName": "hotelReviewsPages", "generatedKeyName": "Pagesid", "source": "/document/tableprojection/pages/*" },
                    { "tableName": "hotelReviewsKeyPhrases", "generatedKeyName": "KeyPhrasesid", "source": "/document/tableprojection/pages/*/keyphrase/*" },
                    { "tableName": "hotelReviewsSentiment", "generatedKeyName": "Sentimentid", "source": "/document/tableprojection/pages/*/sentiment/*" }
                ],
                "objects": []
            },
            {
                "tables": [
                    { 
                        "tableName": "hotelReviewsInlineDocument", "generatedKeyName": "Documentid", "sourceContext": "/document",
                        "inputs": [
                            { "name": "name", "source": "/document/name"},
                            { "name": "reviews_date", "source": "/document/reviews_date"},
                            { "name": "reviews_rating", "source": "/document/reviews_rating"},
                            { "name": "reviews_text", "source": "/document/reviews_text"},
                            { "name": "reviews_title", "source": "/document/reviews_title"},
                            { "name": "AzureSearch_DocumentKey", "source": "/document/AzureSearch_DocumentKey" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviewsInlinePages", "generatedKeyName": "Pagesid", "sourceContext": "/document/reviews_text/pages/*",
                        "inputs": [
                            { "name": "SentimentScore", "source": "/document/reviews_text/pages/*/Sentiment"},
                            { "name": "LanguageCode", "source": "/document/Language"},
                            { "name": "Page", "source": "/document/reviews_text/pages/*" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviewsInlineKeyPhrases", "generatedKeyName": "kpidv2", "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                        "inputs": [
                            { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/Keyphrases/*" }
                        ]
                    }
                ],
                "objects": []
            }
        ]
    }
}
```

## <a name="create-the-indexer"></a>Создание индексатора

Последним шагом является создание индексатора. Индексатор считывает данные и активирует набор навыков. В окне Postman выберите запрос **Create Indexer** (Создание индексатора) и проверьте его текст. Определение этого индексатора содержит ссылки на другие ресурсы, которые вы уже создали — источник данных, индекс и набор навыков. 

Объект `parameters/configuration` управляет процессом приема данных индексатором. В этом примере входные данные находятся в одном документе со строкой заголовка и значениями, разделенными запятыми. Ключ документа выполняет роль уникального идентификатора этого документа. По сути, ключ документа содержит закодированный URL-адрес исходного документа. Наконец, выходные значения набора навыков, такие как код языка, тональность и ключевые фразы, сопоставляются с нужными местами в документе. `Language` имеет только одно значение, но `Sentiment` применяется к каждому элементу массива `pages`. `Keyphrases` является массивом и также применяется к каждому элементу массива `pages`.

Указав заголовки `api-key` и `Content-type` и убедившись, что текст запроса похож на приведенный ниже исходный код, нажмите кнопку **Send** (Отправить) в Postman. Postman отправит запрос PUT на адрес `https://{{search-service-name}}.search.windows.net/indexers/{{indexer-name}}?api-version={{api-version}}`. Служба "Поиск Azure" создаст и запустит индексатор. 

```json
{
    "name": "{{indexer-name}}",
    "dataSourceName": "{{datasource-name}}",
    "skillsetName": "{{skillset-name}}",
    "targetIndexName": "{{index-name}}",
    "parameters": {
        "configuration": {
            "dataToExtract": "contentAndMetadata",
            "parsingMode": "delimitedText",
            "firstLineContainsHeaders": true,
            "delimitedTextDelimiter": ","
        }
    },
    "fieldMappings": [
        {
            "sourceFieldName": "AzureSearch_DocumentKey",
            "targetFieldName": "AzureSearch_DocumentKey",
            "mappingFunction": { "name": "base64Encode" }
        }
    ],
    "outputFieldMappings": [
        { "sourceFieldName": "/document/reviews_text/pages/*/Keyphrases/*", "targetFieldName": "Keyphrases" },
        { "sourceFieldName": "/document/Language", "targetFieldName": "Language" },
        { "sourceFieldName": "/document/reviews_text/pages/*/Sentiment", "targetFieldName": "Sentiment" }
    ]
}
```

## <a name="run-the-indexer"></a>Запуск индексатора 

На портале Azure перейдите на страницу **обзора** службы "Поиск Azure". Выберите вкладку **Индексаторы** и элемент **hotels-reviews-ixr**. Если индексатор еще ни разу не выполнялся, щелкните **Запустить**. Задача индексатора может выдать несколько предупреждений, относящихся к распознаванию языка. В наборе данных есть отзывы на некоторых языках, которые пока не поддерживаются в когнитивных навыках. 

## <a name="next-steps"></a>Дополнительная информация

Итак, вы дополнили данные с помощью Cognitive Services и спроецировали результаты в хранилище знаний. Теперь можете изучить дополненный набор данных с помощью Обозревателя службы хранилища или Power BI.

Сведения о том, как исследовать это хранилище знаний с помощью Обозревателя службы хранилища, см. в следующем пошаговом руководстве.

> [!div class="nextstepaction"]
> [Просмотр данных с помощью Обозревателя службы хранилища](knowledge-store-view-storage-explorer.md)

Сведения о том, как подключить это хранилище знаний к Power BI, см. в следующем пошаговом руководстве.

> [!div class="nextstepaction"]
> [Подключение с помощью Power BI](knowledge-store-connect-power-bi.md)

Если вы хотите повторить это упражнение или выполнить инструкции из другого пошагового руководства по обогащению с помощью ИИ, удалите только индексатор **hotel-reviews-idxr**. При удалении индексатора счетчик бесплатных ежедневных транзакций будет обнулен.
