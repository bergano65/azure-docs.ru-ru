---
title: Как приступить к работе с хранилищем знаний (предварительная версия) — Поиск Azure
description: Из этой статьи вы узнаете, как отправлять обогащенные документы, созданные в конвейерах индексирования ИИ службы "Поиск Azure", в хранилище знаний в учетной записи хранения Azure. С его помощью можно хранить обогащенные документы, просматривать и преобразовывать их, а также обеспечивать доступ к ним службе "Поиск Azure" и другим приложениям.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: heidist
ms.openlocfilehash: 2a904cfb049af413887798c8aab449561bc2b73f
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65030052"
---
# <a name="how-to-get-started-with-knowledge-store"></a>Как приступить к работе с хранилищем знаний

[Хранилище знаний](knowledge-store-concept-intro.md) — это новая функция (предварительная версия) в службе "Поиск Azure", которая позволяет хранить обогащенные с помощью ИИ данные, созданные в конвейере индексирования, для интеллектуального анализа знаний в других приложениях. Кроме того, с помощью сохраненных обогащений можно составить представление о конвейере индексирования в службе "Поиск Azure" и усовершенствовать его.

Хранилище знаний определяется набором навыков. В обычных сценариях полнотекстового поиска в службе "Поиск Azure" с помощью набора навыков предоставляются обогащенные с использованием ИИ данные, которые делают содержимое более доступным для поиска. В сценариях с использованием хранилища знаний набор навыков отвечает за создание и заполнение нескольких структур данных для интеллектуального анализа знаний.

В этом упражнении мы начнем с демонстрационных данных, служб и средств. Они помогут изучить базовый рабочий процесс для создания и использования вашего первого хранилища знаний с акцентом на определение набора навыков.

## <a name="prerequisites"></a>Предварительные требования

В этом кратком руководстве используются приведенные ниже службы, инструменты и данные. 

+ [Создайте службу "Поиск Azure"](search-create-service-portal.md) или [найдите имеющуюся службу](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) в рамках текущей подписки. Вы можете использовать бесплатную службу для выполнения инструкций, описанных в этом учебнике. 

+ [Создайте учетную запись хранения Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) для хранения примера данных. Хранилище знаний будет размещено в службе хранилища Azure.

+ [Создайте ресурс Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) на уровне S0 с оплатой по мере использования, чтобы получить широкий доступ ко всему набору навыков, используемых при обогащении данных с помощью ИИ.

+ Используйте [классическое приложение Postman](https://www.getpostman.com/) для отправки запросов в Поиск Azure.

+ Используйте [коллекцию Postman](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/caselaw) с подготовленными запросами для создания источника данных, индекса, набора навыков и индексатора. Некоторые определения объектов имеют слишком большую длину, поэтому мы не включили их в эту статью. Коллекция Postman позволяет просмотреть все определения индексов и набора навыков.

+ Используйте [демонстрационные данные Caselaw](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw) со страницы для массового скачивания общедоступных данных на сайте [Caselaw Access Project](https://case.law/bulk/download/). В этом упражнении используются первые 10 документов первого пакета для скачивания (Arkansas). Для этого упражнения мы выложили в GitHub 10 демонстрационных документов.

## <a name="get-a-key-and-url"></a>Получение ключа и URL-адреса

Вызовам REST требуется URL-адрес службы и ключ доступа при каждом запросе. Служба поиска создана с обоими элементами, поэтому если вы добавили службу "Поиск Azure" в подписку, выполните следующие действия для получения необходимых сведений:

1. [Войдите на портал Azure](https://portal.azure.com/) и на странице **обзора** службы поиска получите URL-адрес. Пример конечной точки может выглядеть так: `https://mydemo.search.windows.net`.

1. В разделе **Параметры** > **Ключи** получите ключ администратора, чтобы обрести полные права на службу. Существуют два взаимозаменяемых ключа администратора, предназначенных для обеспечения непрерывности бизнес-процессов на случай, если вам потребуется сменить один из них. Вы можете использовать первичный или вторичный ключ для выполнения запросов на добавление, изменение и удаление объектов.

    ![Получение конечной точки HTTP и ключа доступа](media/search-fiddler/get-url-key.png "Получение конечной точки HTTP и ключа доступа")

Для выполнения любого запроса к службе требуется использование ключа API.

## <a name="prepare-sample-data"></a>Подготовка примера данных

1. [Войдите на портал Azure](https://portal.azure.com), перейдите к учетной записи хранения Azure, выберите **Большие двоичные объекты**, а затем щелкните **Контейнер**.

1. [Создайте контейнер больших двоичных объектов](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) для хранения примера данных. Можно задать любое из допустимых значений уровня общего доступа.

1. Откройте контейнер после создания и на панели команд выберите **Загрузить**.

   ![Кнопка "Загрузить" на панели команд](media/search-semi-structured-data/upload-command-bar.png "Upload on command bar")

1. Перейдите к папке с примером файла **caselaw-sample.json**. Выберите файл и нажмите **Отправить**.


## <a name="set-up-postman"></a>Настройка Postman

Запустите Postman и настройте HTTP-запрос. Если вы не работали с этим инструментом, изучите статью [Работа с REST API службы "Поиск Azure" с помощью Postman или Fiddler](search-fiddler.md).

+ Для каждого вызова в этом пошаговом руководстве используется метод запроса **POST**.
+ Заголовки запросов (2) содержат следующие данные: "Content-type" со значением "application/json" и "api-key" с вашим значением "admin key" (ключ администратора — это заполнитель для первичного ключа поиска). 
+ Текст запроса — это фактическое содержимое вызова. 

  ![Частично структурированный поиск](media/search-semi-structured-data/postmanoverview.png)

Мы используем Postman для выполнения четырех вызовов API к службе поиска для создания источника данных, индекса, набора навыков и индексатора. Источник данных содержит указатель на вашу учетную запись хранения и данные JSON. Служба поиска создает подключение при импорте данных.

[Создание набора навыков](#create-skillset) — основной этап этого пошагового руководства. Набор навыков указывает действия обогащения и способ хранения данных в хранилище знаний.

В конечной точке URL-адреса нужно указать версию API. А каждый вызов должен возвращать результат **201 Created** (создан ресурс). Предварительная версия API для создания набора навыков с поддержкой хранилища знаний — `2019-05-06-Preview`.

Выполните следующие вызовы API из клиента REST.

## <a name="create-a-data-source"></a>Создание источника данных

[API создания источника данных](https://docs.microsoft.com/rest/api/searchservice/create-data-source) создает объект службы "Поиск Azure", в котором указываются данные для индексирования.

Конечная точка этого вызова — `https://[service name].search.windows.net/datasources?api-version=2019-05-06-Preview`. 

1. Замените `[service name]` именем службы поиска. 

2. Для этого вызова в тексте запроса необходимо указать строку подключения учетной записи хранения и имя контейнера больших двоичных объектов. Строку подключения можно найти на портале Azure в разделе учетной записи хранения **Ключи доступа**. 

   Перед выполнением вызова обязательно замените строку подключения и имя контейнера больших двоичных объектов в тексте запроса.

    ```json
    {
        "name": "caselaw-ds",
        "description": null,
        "type": "azureblob",
        "subtype": null,
        "credentials": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your storage key>;EndpointSuffix=core.windows.net"
        },
        "container": {
            "name": "<your blob container name>",
            "query": null
        },
        "dataChangeDetectionPolicy": null,
        "dataDeletionDetectionPolicy": null
    }
    ```

3. Отправьте запрос. Должен поступить ответ **201** с текстом, практически идентичным полезным данным запроса, которые вы указали.

    ```json
    {
        "name": "caselaw-ds",
        "description": null,
        "type": "azureblob",
        "subtype": null,
        "credentials": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your storage key>;EndpointSuffix=core.windows.net"
        },
        "container": {
            "name": "<your blob container name>",
            "query": null
        },
        "dataChangeDetectionPolicy": null,
        "dataDeletionDetectionPolicy": null
    }
    ```

## <a name="create-an-index"></a>Создание индекса
    
Второй вызов — [API создания индекса](https://docs.microsoft.com/rest/api/searchservice/create-data-source), создающий индекс службы "Поиск Azure", в котором хранятся все доступные для поиска данные. Индекс указывает все поля, параметры и атрибуты.

Индекс для интеллектуального анализа знаний использовать не обязательно, но без него не будет работать индексатор. 

URL-адрес этого вызова — `https://[service name].search.windows.net/indexes?api-version=2019-05-06-Preview`.

1. Замените `[service name]` именем службы поиска.

2. Скопируйте определение индекса из запроса Create Index в коллекции Postman в текст запроса. Определение индекса состоит из нескольких сотен строк, поэтому здесь оно не приводится. 

   Внешняя оболочка индекса содержит указанные ниже элементы. 

   ```json
   {
      "name": "caselaw",
      "defaultScoringProfile": null,
      "fields": [],
      "scoringProfiles": [],
      "corsOptions": null,
      "suggesters": [],
      "analyzers": [],
      "tokenizers": [],
      "tokenFilters": [],
      "charFilters": [],
      "encryptionKey": null
   }
   ```

3. Коллекция `fields` содержит основную часть определения индекса. Сюда входят простые поля, [сложные поля](search-howto-complex-data-types.md) с вложенными подструктурами и коллекции.

   Просмотрите определение поля для `casebody` в строках 302–384. Обратите внимание, что сложные поля могут содержать другие сложные поля, если требуются иерархические представления.

   ```json
   {
    "name": "casebody",
    "type": "Edm.ComplexType",
    "fields": [
        {
            "name": "status",
            "type": "Edm.String",
            "searchable": true,
            "filterable": true,
            "retrievable": true,
            "sortable": true,
            "facetable": true,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        {
            "name": "data",
            "type": "Edm.ComplexType",
            "fields": [
                {
                    "name": "head_matter",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": false,
                    "retrievable": true,
                    "sortable": false,
                    "facetable": false,
                    "key": false,
                    "indexAnalyzer": null,
                    "searchAnalyzer": null,
                    "analyzer": null,
                    "synonymMaps": []
                },
                {
                    "name": "opinions",
                    "type": "Collection(Edm.ComplexType)",
                    "fields": [
                        {
                            "name": "author",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": true,
                            "retrievable": true,
                            "sortable": false,
                            "facetable": true,
                            "key": false,
                            "indexAnalyzer": null,
                            "searchAnalyzer": null,
                            "analyzer": null,
                            "synonymMaps": []
                        },
                        {
                            "name": "text",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": false,
                            "retrievable": true,
                            "sortable": false,
                            "facetable": false,
                            "key": false,
                            "indexAnalyzer": null,
                            "searchAnalyzer": null,
                            "analyzer": null,
                            "synonymMaps": []
                        },
                        {
                            "name": "type",
                            "type": "Edm.String",
                            "searchable": true,
                            "filterable": true,
                            "retrievable": true,
                            "sortable": false,
                            "facetable": true,
                            "key": false,
                            "indexAnalyzer": null,
                            "searchAnalyzer": null,
                            "analyzer": null,
                            "synonymMaps": []
                        }
                    ]
                },
    . . .
   ```

4. Отправьте запрос. 

   Должен поступить ответ **201** примерно с такими данными (здесь показаны несколько первых полей):

    ```json
    {
        "name": "caselaw",
        "defaultScoringProfile": null,
        "fields": [
            {
                "name": "id",
                "type": "Edm.String",
                "searchable": true,
                "filterable": true,
                "retrievable": true,
                "sortable": true,
                "facetable": true,
                "key": true,
                "indexAnalyzer": null,
                "searchAnalyzer": null,
                "analyzer": null,
                "synonymMaps": []
            },
            {
                "name": "name",
                "type": "Edm.String",
                "searchable": true,
                "filterable": true,
                "retrievable": true,
                "sortable": true,
                "facetable": true,
                "key": false,
                "indexAnalyzer": null,
                "searchAnalyzer": null,
                "analyzer": null,
                "synonymMaps": []
            },
      . . .
    ```

<a name="create-skillset"></a>

## <a name="create-a-skillset-and-knowledge-store"></a>Создание набора навыков и хранилища знаний

[API создания набора навыков](https://docs.microsoft.com/rest/api/searchservice/create-skillset) позволяет создать объект службы "Поиск Azure", который указывает, какие когнитивные навыки вызвать, как связать их и, что самое важное для этого пошагового руководства, как указать хранилище знаний.

Конечная точка этого вызова — `https://[service name].search.windows.net/skillsets?api-version=2019-05-06-Preview`.

1. Замените `[service name]` именем службы поиска.

2. Скопируйте определение набора навыков из запроса Create Skillset в коллекции Postman в текст запроса. Определение набора навыков состоит из нескольких сотен строк, поэтому мы его здесь не приводим. Но это определение имеет ключевое значение при работе с этим пошаговым руководством.

   Внешняя оболочка набора навыков содержит указанные ниже элементы. Коллекция `skills` определяет обогащенные данные в памяти, но определение `knowledgeStore` указывает способ хранения выходных данных. Определение `cognitiveServices` обеспечивает подключение к подсистемам ИИ для обогащения данных.

   ```json
   {
    "name": "caselaw-ss",
    "description": null,
    "skills": [],
    "cognitiveServices": [],
    "knowledgeStore": []
   }
   ```

3. Сначала задайте `cognitiveServices`, ключ `knowledgeStore` и строку подключения. В примере эти строки будут находиться после определения набора навыков, ближе к концу текста запроса.

    ```json
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "<your cognitive services resource name>",
        "key": "<your cognitive services key>"
    },
    "knowledgeStore": {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your storage account name>;AccountKey=<your storage account key>;EndpointSuffix=core.windows.net",
    ```

3. Просмотрите коллекцию навыков, в частности навыки Shaper в строках 85 и 170 соответственно. Навык Shaper важен, так как он позволяет собрать структуры данных для интеллектуального анализа знаний. При выполнении набора навыков эти структуры доступны только в памяти. Но на следующем этапе вы узнаете, как эти выходные данные можно сохранить в хранилище знаний для дальнейшего изучения.

   Приведенный ниже фрагмент кода взят из строки 207. 

    ```json
    {
    "name": "Opinions",
    "source": null,
    "sourceContext": "/document/casebody/data/opinions/*",
    "inputs": [
        {
            "name": "Text",
            "source": "/document/casebody/data/opinions/*/text"
        },
        {
            "name": "Author",
            "source": "/document/casebody/data/opinions/*/author"
        },
        {
            "name": "Entities",
            "source": null,
            "sourceContext": "/document/casebody/data/opinions/*/text/pages/*/entities/*",
            "inputs": [
                {
                    "name": "Entity",
                    "source": "/document/casebody/data/opinions/*/text/pages/*/entities/*/value"
                },
                {
                    "name": "EntityType",
                    "source": "/document/casebody/data/opinions/*/text/pages/*/entities/*/category"
                }
             ]
          }
     ]
   }
   . . .
   ```

3. Просмотрите элемент `projections` в `knowledgeStore` начиная со строки 253. Проекции указывают состав хранилища знаний. Проекции указываются в парах "таблица-объект", но сейчас только поочередно. Как видно из первой проекции, значение `tables` указано, а `objects` — нет. Во второй проекции ситуация противоположная.

   В службе хранилища Azure в хранилище таблиц для каждой создаваемой вами таблицы будет создана соответствующая таблица. А для каждого объекта в хранилище BLOB-объектов создается контейнер.

   Объекты обычно содержат полное выражение обогащения. Таблицы обычно содержат частичные обогащения в сочетаниях, сформированных для определенных целей. В этом примере показана таблица Cases, но не представлены другие таблицы, например Entities, Judges и Opinions.

    ```json
    "projections": [
    {
        "tables": [
            {
              "tableName": "Opinions",
              "generatedKeyName": "OpinionId",
              "source": "/document/Case/OpinionsSnippets/*"
            },
          . . . 
        ],
        "objects": []
    },
    {
        "tables": [],
        "objects": [
            {
                "storageContainer": "enrichedcases",
                "key": "/document/CaseFull/Id",
                "source": "/document/CaseFull"
            }
          ]
        }
      ]
    }
    ```

5. Отправьте запрос. Должен поступить ответ **201** примерно с такими данными (здесь показана первая часть ответа):

    ```json
    {
    "name": "caselaw-ss",
    "description": null,
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "name": "SplitSkill#1",
            "description": null,
            "context": "/document/casebody/data/opinions/*/text",
            "defaultLanguageCode": "en",
            "textSplitMode": "pages",
            "maximumPageLength": 5000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/casebody/data/opinions/*/text
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        . . .
    ```

## <a name="create-and-run-an-indexer"></a>Создание и запуск индексатора

[API создания индексатора](https://docs.microsoft.com/rest/api/searchservice/create-indexer) позволяет создать и немедленно выполнить индексатор. На этом этапе все созданные определения вступают в действие. Индексатор запускается немедленно, так как он не существует в службе. Для существующего индексатора в рамках операции обновления выполняется вызов метода POST.

Конечная точка этого вызова — `https://[service name].search.windows.net/indexers?api-version=2019-05-06-Preview`.

1. Замените `[service name]` именем службы поиска. 

2. Для этого вызова в тексте запроса указывается имя индексатора. Для индексатора требуется источник данных и индекс. Набор навыков для индексатора использовать не обязательно, но это необходимо для обогащения данных с помощью ИИ.

    ```json
    {
        "name": "caselaw-idxr",
        "description": null,
        "dataSourceName": "caselaw-ds",
        "skillsetName": "caselaw-ss",
        "targetIndexName": "caselaw",
        "disabled": null,
        "schedule": null,
        "parameters": {
            "batchSize": 1,
            "maxFailedItems": null,
            "maxFailedItemsPerBatch": null,
            "base64EncodeKeys": null,
            "configuration": {
                "parsingMode": "jsonLines"
            }
        },
        "fieldMappings": [],
        "outputFieldMappings": [
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/people/*",
                "targetFieldName": "people",
                "mappingFunction": null
            },
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/organizations/*",
                "targetFieldName": "orginizations",
                "mappingFunction": null
            },
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/locations/*",
                "targetFieldName": "locations",
                "mappingFunction": null
            },
            {
                "sourceFieldName": "/document/Case/OpinionsSnippets/*/Entities/*",
                "targetFieldName": "entities",
                "mappingFunction": null
            },
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/keyPhrases/*",
                "targetFieldName": "keyPhrases",
                "mappingFunction": null
            }
        ]
    }
    ```

3. Отправьте запрос. Должен поступить ответ **201** с текстом, практически идентичным полезным данным запроса, которые вы указали (текст сокращен).

    ```json
    {
        "name": "caselaw-idxr",
        "description": null,
        "dataSourceName": "caselaw-ds",
        "skillsetName": "caselaw-ss",
        "targetIndexName": "caselaw",
        "disabled": null,
        "schedule": null,
        "parameters": {
            "batchSize": 1,
            "maxFailedItems": null,
            "maxFailedItemsPerBatch": null,
            "base64EncodeKeys": null,
            "configuration": {
                "parsingMode": "jsonLines"
            }
        },
        "fieldMappings": [],
        "outputFieldMappings": [
            {
                "sourceFieldName": "/document/casebody/data/opinions/*/text/pages/*/people/*",
                "targetFieldName": "people",
                "mappingFunction": null
            }
        ]
    }
    ```

## <a name="explore-knowledge-store"></a>Изучение хранилища знаний

Вы можете начать изучение сразу после импорта первого документа. Для этой задачи используйте [**Обозреватель службы хранилища**](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer) на портале.

Важно помнить, что хранилище знаний работает отдельно от службы "Поиск Azure". И индекс службы "Поиск Azure", и хранилище знаний содержат представление и содержимое данных, но на этом их сходство заканчивается. Индекс можно использовать для полнотекстового поиска, поиска с фильтрацией и всех сценариев, поддерживаемых в службе "Поиск Azure". Или же вы можете работать только с хранилищем знаний, подключая другие средства для анализа содержимого.

## <a name="takeaways"></a>Общие выводы

Вы создали свое первое хранилище знаний в службе хранилища Azure и просмотрели обогащения с помощью Обозревателя службы хранилища. Это основные принципы работы с хранимыми обогащениями. 

## <a name="next-steps"></a>Дополнительная информация

Навык Shaper позволяет выполнять трудоемкие задачи по созданию точных форм данных, из которых можно сформировать новые комбинации. Просмотрите страницу со справочными сведениями об этом навыке, чтобы составить представление о его использовании.

> [!div class="nextstepaction"]
> [Справочник по навыку Shaper](cognitive-search-skill-shaper.md)


<!---
## Keep This

How to convert unformatted JSON into an indented JSON document structure that allows you to quickly identify nested structures. Useful for creating an index that includes complex types.

1. Use Visual Studio Code.
2. Open data.jsonl
--->