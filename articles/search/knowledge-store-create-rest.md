---
title: Создание хранилища знаний с помощью REST в службе "Поиск Azure"
description: Создайте хранилище знаний службы "Поиск Azure" для сохранения дополнений из конвейера когнитивного поиска с помощью REST API и Postman.
author: lobrien
services: search
ms.service: search
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: laobri
ms.openlocfilehash: 26dc66474eecffd7f5a34bcfcaf93fd49f59606c
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936499"
---
# <a name="create-an-azure-search-knowledge-store-using-rest"></a>Создание хранилища знаний службы "Поиск Azure" с помощью REST

Хранилище знаний — это компонент Поиска Azure, который сохраняет выходные данные конвейера обогащения, выполняемого с помощью ИИ, для последующего анализа или обработки. Конвейер обогащения с помощью ИИ способен получать файлы изображений или неструктурированные текстовые файлы, отправлять их для индексирования в Поиск Azure, применять результаты обогащения ИИ, выполняемого службами Cognitive Services (например, анализ изображений и обработку естественного языка), а затем сохранять результаты в хранилище знаний в службе хранилища Azure. Затем можно использовать такие средства, как Power BI или Обозреватель службы хранилища, чтобы исследовать хранилище знаний.

В этой статье вы будете использовать интерфейс REST API для приема, индексирования и применения дополнений искусственного интеллекта к набору отзывов об отеле. Отзывы об отеле импортируются в хранилище BLOB-объектов Azure, и результаты сохраняются как хранилище знаний в хранилище таблиц Azure.

После создания хранилища знаний вы можете узнать, как получить к нему доступ с помощью [Обозревателя службы хранилища](knowledge-store-view-storage-explorer.md) или [Power BI](knowledge-store-connect-power-bi.md).

## <a name="1---create-services"></a>1\. Создание служб

+ [Создайте службу "Поиск Azure"](search-create-service-portal.md) или [найдите имеющуюся службу](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) в рамках текущей подписки. Вы можете использовать бесплатную службу для выполнения инструкций, описанных в этом учебнике.

+ [Создайте учетную запись хранения Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) для хранения примера данных и хранилища знаний. Ваша учетная запись хранения должна использовать то же расположение (например, "Запад США") для службы "Поиск Azure". *Тип учетной записи* хранения должен быть *StorageV2 (общего назначения версии 2)* (по умолчанию) или *Storage (общего назначения версии 1)* .

+ Рекомендация: Используйте [классическое приложение Postman](https://www.getpostman.com/) для отправки запросов в Поиск Azure. REST API можно использовать с любым инструментом, способным работать с HTTP-запросами и ответами. Postman — это хороший вариант для изучения интерфейсов REST API, который будет использован в этой статье. Кроме того, [исходный код](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json) для этой статьи включает в себя коллекцию запросов Postman. 

## <a name="2---store-the-data"></a>2\. Сохранение данных

Загрузите CSV-файл с отзывами об отеле в хранилище BLOB-объектов Azure, чтобы получить доступ к нему через индексатор службы "Поиск Azure" и провести через конвейер обогащения искусственного интеллекта.

### <a name="create-an-azure-blob-container-with-the-data"></a>Создание контейнера BLOB-объектов Azure с данными

1. [Скачайте данные отзывов об отеле, сохраненные в CSV-файле (HotelReviews_Free.csv)](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Эти данные получены с сайта Kaggle.com и содержат отзывы клиентов об отелях.
1. [Войдите на портал Azure](https://portal.azure.com) и перейдите в учетную запись хранения Azure.
1. [Создайте контейнер больших двоичных объектов](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). Для создания контейнера в левой панели навигации для своей учетной записи хранения щелкните **BLOB-объекты**, а затем — **+ Container** (+ Контейнер) на панели команд.
1. В качестве нового **имени** контейнера введите `hotel-reviews`.
1. Выберите любой **уровень общего доступа**. В нашем примере используется значение по умолчанию.
1. Нажмите кнопку **ОК**, чтобы создать контейнер BLOB-объектов Azure.
1. Откройте новый контейнер `hotels-review`, нажмите кнопку **Отправить** и выберите файл **HotelReviews-Free.csv**, скачанный на первом шаге.

    ![Отправка данных](media/knowledge-store-create-portal/upload-command-bar.png "Отправка отзывов отеля")

1. Нажмите кнопку **Отправить**, чтобы импортировать CSV-файл в хранилище BLOB-объектов Azure. Появится новый контейнер.

    ![Создание контейнера BLOB-объектов Azure](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Create the Azure Blob container")

## <a name="3---configure-postman"></a>3\. Настройка Postman

Скачайте [исходный код коллекции Postman](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json) и импортируйте его в Postman с помощью пунктов меню **Файл, Импорт...** . Перейдите на вкладку **Коллекции**, нажмите кнопку **...** и выберите **Правка**. 

![Приложение Postman с навигацией](media/knowledge-store-create-rest/postman-edit-menu.png "Перейдите в меню \"Правка\" в Postman")

В открывшемся диалоговом окне "Правка" перейдите на вкладку **Переменные**. 

Вкладка **Переменные** позволяет добавлять значения, которые Postman будет менять при каждом их обнаружении в двойных скобках. Например, Postman заменит символ `{{admin-key}}` на "текущее значение" `admin-key`. Postman сделает эту замену в URL-адресах, заголовках, тексте запроса и так далее. 

Вы найдете значение для `admin-key` на вкладке **Ключи** Службы поиска. Необходимо изменить `search-service-name` и `storage-account-name` на значения, выбранные на [шаге 1](#1---create-services). Задайте `storage-connection-string` из значения на вкладке **Ключи доступа** учетной записи хранения. Другие значения можно оставить без изменений.

![Вкладка переменных приложения Postman](media/knowledge-store-create-rest/postman-variables-window.png "Окно переменных Postman")


| Переменная    | Как получить |
|-------------|-----------------|
| `admin-key` | Служба поиска, вкладка **Ключи**              |
| `api-version` | Оставьте значение 2019-05-06-Preview |
| `datasource-name` | Оставьте значение hotel-reviews-ds | 
| `indexer-name` | Оставьте значение hotel-reviews-ixr | 
| `index-name` | Оставьте значение hotel-reviews-ix | 
| `search-service-name` | Служба поиска, основное имя. Введите URL-адрес `https://{{search-service-name}}.search.windows.net` | 
| `skillset-name` | Оставьте значение hotel-reviews-ss | 
| `storage-account-name` | Учетная запись хранения, основное имя | 
| `storage-connection-string` | Учетная запись хранения, вкладка**Ключи доступа**, **строка подключения** **key1** | 
| `storage-container-name` | Оставьте значение hotel-reviews | 

### <a name="review-the-request-collection-in-postman"></a>Обзор коллекции запросов в Postman

Для создания хранилища знаний необходимо выдать четыре HTTP-запроса: 

1. Запрос PUT для создания индекса. Этот индекс содержит данные, используемые и возвращаемые службой "Поиск Azure".
1. Запрос POST для создания источника данных. Этот источник данных связывает поведение службы "Поиск Azure" с учетной записью хранения данных и хранилища знаний. 
1. Запрос PUT для создания набора навыков. Набор навыков определяет дополнения к данным и структуру хранилища знаний.
1. Запрос PUT для создания индексатора. При выполнении индексатора считываются данные, применяется набор навыков и сохраняются результаты. Этот запрос необходимо выполнить последним.

[Исходный код](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json) содержит коллекцию Postman с этими четырьмя запросами. Чтобы выдать запросы, перейдите на вкладку запроса в Postman и добавьте заголовки запросов `api-key` и `Content-Type`. Присвойте параметру `api-key` значение `{{admin-key}}`. Присвойте параметру `Content-type` значение `application/json`. 

> [!div class="mx-imgBorder"]
> ![Снимок экрана, показывающий интерфейс Postman для заголовков](media/knowledge-store-create-rest/postman-headers-ui.png)

> [!Note]
> Во всех запросах необходимо задать заголовки `api-key` и `Content-type`. Если переменная распознается Postman, она будет отображена в виде оранжевого текста, как в случае с `{{admin-key}}` на снимке экрана. Если переменная написана с ошибкой, она будет отображена красным текстом.
>

## <a name="4---create-an-azure-search-index"></a>4\. Создание индекса службы "Поиск Azure"

Вам нужно создать индекс службы "Поиск Azure", чтобы представить данные, которые вас интересуют для поиска, фильтрации и улучшений. Вы создадите индекс, выполнив запрос PUT в `https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}`. Postman заменит символы, заключенные в двойные скобки, например `{{search-service-name}}`, `{{index-name}}`, и `{{api-version}}` значениями, указанными на [шаге 3](#3---configure-postman). Если вы используете другой инструмент для выполнения команд REST, вам придется заменить эти переменные самостоятельно.

Укажите структуру индекса службы "Поиск Azure" в тексте запроса. После установки заголовков `api-key` и `Content-type` в Postman перейдите на панель **Текст** запроса. Вы должны увидеть следующий код JSON. В противном случае выберите **Необработанный** и **JSON (application/json)** (JSON (приложение/JSON)) и вставьте в текст следующий код:

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

Вы увидите, что это определение индекса представляет собой комбинацию данных, которые необходимо предоставить пользователю (название отеля, содержание отзывов, дата и т. д.), метаданных поиска и данных расширения искусственного интеллекта (тональность, ключевые фразы и язык).

Нажмите кнопку **Отправить**, чтобы отправить запрос PUT. Вы должны получить сообщение о состоянии `201 - Created`. Если получено другое сообщение о состоянии, на панели **Текст** отобразится ответ JSON с сообщением об ошибке. 

## <a name="5---create-the-datasource"></a>5\. Создание источника данных

Теперь необходимо подключить службу "Поиск Azure" к данным отеля, сохраненным на [шаге 2](#2---store-the-data). Создание источника данных выполняется с помощью POST на `https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}`. Опять же, необходимо задать заголовки `api-key` и `Content-Type`, как указано выше. 

В окне Postman откройте запрос "Создание источника данных". Переключитесь на панель **Текст**, которая должна иметь следующий код:

```json
{
  "name" : "{{datasource-name}}",
  "description" : "Demo files to demonstrate knowledge store capabilities.",
  "type" : "azureblob",
  "credentials" : { "connectionString" : "{{storage-connection-string}}" },
  "container" : { "name" : "{{storage-container-name}}" }
}
```

Нажмите кнопку **Отправить**, чтобы отправить запрос POST. 

## <a name="6---create-the-skillset"></a>6\. Создание набора навыков 

Дальше нужно задать набор навыков, который указывает как применяемые улучшения, так и хранилище знаний, в котором будут храниться результаты. В окне Postman откройте вкладку Create the Skillset (Создание набора навыков). Этот запрос отправляет PUT на `https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}`.
Задайте заголовки `api-key` и `Content-type`, как было сделано ранее. 

Существует два больших объекта верхнего уровня: `"skills"` и `"knowledgeStore"`. Каждый объект в объекте `"skills"` является службой добавления. Каждая служба добавления имеет `"inputs"` и `"outputs"`. Обратите внимание, что для `LanguageDetectionSkill` задано выходное значение `targetName`,а именно `"Language"`. Значение этого узла используется большинством других навыков в качестве входных данных с источником `document/Language`. Эта возможность использования выходных данных одного узла в качестве входных данных для другого еще очевиднее в `ShaperSkill`, который указывает, как данные будут передаваться в таблицы хранилища знаний.

Объект `"knowledge_store"` подключается к учетной записи хранения через переменную `{{storage-connection-string}}` Postman. Здесь содержится набор сопоставлений между расширенным документом, таблицами и столбцами, которые будут доступны в хранилище знаний. 

Чтобы создать набор навыков, создайте запрос PUT, нажав кнопку **Отправить** в Postman.

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

## <a name="7---create-the-indexer"></a>7\. Создание индексатора

Последним шагом является создание индексатора, который фактически считывает данные и активирует набор навыков. В окне Postman перейдите к запросу Create Indexer (Создание индексатора) и проверьте текст. Как видите, определение индексатора относится к нескольким другим уже созданным ресурсам — источнику данных, индексу и набору навыков. 

Объект `"parameters/configuration"` управляет процессом приема данных индексатором. В этом случае входные данные находятся в одном документе со строкой заголовка и значениями, разделенными запятыми. Ключ документа — это уникальный идентификатор документа, который перед кодированием является URL-адресом исходного документа. Наконец, выходные значения набора навыков, такие как код языка, тональность и ключевые фразы, сопоставляются с соответствующими местами в документе. Обратите внимание, что хотя имеется одно значение для `Language`, `Sentiment` применяется к каждому элементу массива `pages`. `Keyphrases` сам по себе является массивом и также применяется к каждому элементу массива `pages`.

Установив заголовки `api-key` и `Content-type` и убедившись, что текст запроса похож на следующий исходный код, нажмите кнопку **Отправить** в Postman. Postman создаст запрос PUT на `https://{{search-service-name}}.search.windows.net/indexers/{{indexer-name}}?api-version={{api-version}}`. Служба "Поиск Azure" создаст и запустит индексатор. 

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

## <a name="8---run-the-indexer"></a>8\. Запуск индексатора 

На портале Azure перейдите к разделу **Обзор** Службы поиска и выберите вкладку **Индексаторы**. Щелкните **hotels-reviews-ixr**, созданный на предыдущем шаге. Если индексатор еще не запущен, нажмите кнопку **Запуск**. Задача индексирования может вызвать некоторые предупреждения, связанные с распознаванием языка, так как данные содержат некоторые отзывы, написанные на языках, которые еще не поддерживаются когнитивными навыками. 

## <a name="next-steps"></a>Дополнительная информация

Теперь, когда вы обогатили данные с помощью Cognitive Services и спроецировали результаты в хранилище знаний, вы можете изучить обогащенный набор данных с помощью Обозревателя службы хранилища или Power BI.

Сведения о том, как исследовать это хранилище знаний с помощью Обозревателя службы хранилища, см. в следующем пошаговом руководстве.

> [!div class="nextstepaction"]
> [Просмотр данных с помощью Обозревателя службы хранилища](knowledge-store-view-storage-explorer.md)

Сведения о том, как подключить это хранилище знаний к Power BI, см. в следующем пошаговом руководстве.

> [!div class="nextstepaction"]
> [Подключение с помощью Power BI](knowledge-store-connect-power-bi.md)

Если вы хотите повторить это упражнение или выполнить инструкции из другого пошагового руководства по обогащению с помощью ИИ, удалите только индексатор *hotel-reviews-idxr*. При удалении индексатора счетчик бесплатных ежедневных транзакций будет обнулен.
