---
title: Общие сведения об использовании хранилища знаний (предварительная версия) в Поиске Azure
description: Хранилище знаний — это специальное хранилище Azure, в котором можно хранить обогащенные документы, просматривать и преобразовывать их, а также обеспечивать доступ к ним службе "Поиск Azure" и другим приложениям.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.subservice: cognitive-search
ms.devlang: NA
ms.topic: overview
ms.date: 08/02/2019
ms.author: heidist
ms.openlocfilehash: 107478f7e2d3c6726d3b8fb9c503ef13271c6571
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840819"
---
# <a name="what-is-knowledge-store-in-azure-search"></a>Что собой представляет хранилище знаний в службе "Поиск Azure"

> [!Note]
> Доступна предварительная версия хранилища знаний, не предназначенная для использования в рабочей среде. Эта функция предоставляется в [версии REST API 2019-05-06-Preview](search-api-preview.md). Поддержка пакета SDK для .NET пока не реализована.
>

Хранилище знаний — это компонент службы "Поиск Azure", который предназначен для хранения обогащенных документов и метаданных, созданных с помощью конвейера индексирования на базе ИИ [(когнитивный поиск)](cognitive-search-concept-intro.md). Обогащенный документ — это выходные данные конвейера, созданные из содержимого, которое было извлечено, структурировано и проанализировано с использованием ресурсов в Cognitive Services. В стандартном конвейере на основе ИИ обогащенные документы являются временными и используются только во время индексирования, а затем удаляются. Благодаря хранилищу знаний документы сохраняются для последующей оценки, исследования и могут стать входными данными для последующей рабочей нагрузки обработки и анализа данных. 

Если вы ранее использовали когнитивный поиск, то знаете, что документ можно подвергнуть серии операций обогащения с помощью наборов навыков. Результатом этого процесса может быть индекс службы "Поиск Azure" или проекции документа в хранилище знаний (нововведение в этой предварительной версии). Два потока вывода, индекс поиска и хранилище знаний, отличаются друг от друга по физическим характеристикам. Они имеют одно и то же содержимое, но хранятся и используются по-разному.

Физически хранилище знаний создается в учетной записи хранения Azure как хранилище таблиц или хранилище BLOB-объектов Azure, в зависимости от того, как настроен конвейер. Любой инструмент или процесс, который может подключиться к службе хранилища Azure, может использовать содержимое хранилища знаний.

Проекции — это механизм структурирования данных в хранилище знаний. Например, с помощью проекций вы можете выбрать способ сохранения выходных данных: в виде одного большого двоичного объекта или коллекции связанных таблиц. Простой способ просмотра содержимого хранилища знаний — через встроенный [Обозреватель службы хранилища](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) для службы хранилища Azure.

![Схема конвейера с хранилищем знаний](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "Knowledge Store in pipeline diagram")

Чтобы использовать хранилище знаний, в набор навыков нужно добавить элемент `knowledgeStore`,определяющий пошаговые операции в конвейере индексирования. Во время выполнения служба "Поиск Azure" создает пространство в учетной записи хранения и заполняет его определениями и содержимым, создаваемыми конвейером.

## <a name="benefits-of-knowledge-store"></a>Преимущества хранилища знаний

Хранилище знаний позволяет получить структуру, контекст и фактическое содержимое из неструктурированных и частично структурированных данных, таких как большие двоичные объекты и проанализированные файлы изображений, и даже из структурированных данных, преобразованных в новые формы. В [пошаговом руководстве](knowledge-store-howto.md), подготовленном для этой предварительной версии, показано, как документ в формате JSON дробится на субструктуры, преобразуется в новые структуры и трансформируется другими способами. Затем он становится доступным для последующих процессов, таких как машинное обучение или обработка и анализ данных.

Хотя возможности конвейера индексирования на основе ИИ сами по себе впечатляют, главной особенностью хранилища знаний является способность изменять форму данных. Можно начать с базового набора навыков и совершенствовать его цикл за циклом, чтобы увеличить число уровней структур. Это позволит создавать новые структуры, пригодные для использования другими приложениями, помимо службы "Поиск Azure".

Хранилище знаний обеспечивает следующие преимущества:

+ Обогащенные документы может использовать не только служба поиска, но и [средства аналитики и создания отчетов](#tools-and-apps). Power BI c Power Query являются оптимальным решением. Но и любое приложение, способное подключаться к службе хранилища Azure, может получать данные из созданного хранилища знаний.

+ Улучшение конвейера индексирования на основе ИИ в ходе отладки и определений набора навыков. Хранилище знаний предоставляет результат определения набора навыков в конвейере индексирования на основе ИИ. Эти результаты можно использовать для создания усовершенствованного набора навыков, так как вы можете отслеживать воздействие процесса обогащения. Просматривать содержимое хранилища знаний можно с помощью [Обозревателя службы хранилища](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) Azure.

+ Преобразование данных в новые формы. Теперь доступна функция преобразования данных, реализованная в коде набора навыков. [Навык формирователя](cognitive-search-skill-shaper.md) в службе "Поиск Azure" был доработан для выполнения этой задачи. Преобразование формы позволяет определять проекцию, которая соответствует предполагаемому способу применения данных, сохраняя при этом взаимосвязи.

> [!Note]
> Индексирование на основе ИИ с использованием Cognitive Services работает следующим образом. Поиск Azure интегрируется с функциями распознавания изображений и текста Cognitive Services. С помощью этих функций извлекаются сведения из файлов изображений, распознаются сущности, извлекаются ключевые фразы из текстовых файлов и т. д., после чего обогащаются исходные данные. Дополнительные сведения см. в статье [Что такое когнитивный поиск в службе "Поиск Azure"?](cognitive-search-concept-intro.md)

## <a name="create-a-knowledge-store"></a>Создание хранилища знаний

Хранилище знаний — это часть определения набора навыков. В этой предварительной версии для его создания требуется REST API `api-version=2019-05-06-Preview` или **мастер импорта данных**.

Приведенный ниже код JSON определяет `knowledgeStore` — элемент набора навыков, вызываемый индексатором (не показано). Спецификация проекций в `knowledgeStore` определяет, что создается в службе хранилища Azure — таблицы или объекты.

Если вы уже знакомы с индексацией на основе ИИ, то знаете, что определение набора навыков обуславливает создание, организацию и содержание каждого обогащенного документа.

```json
{
  "name": "my-new-skillset",
  "description": 
  "Example showing knowledgeStore placement, supported in api-version=2019-05-06-Preview. You need at least one skill, most likely a Shaper skill if you are modulating data structures.",
  "skills":
  [
    {
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document/content/phrases/*",
    "inputs": [
        {
        "name": "text",
        "source": "/document/content/phrases/*"
        },
        {
        "name": "sentiment",
        "source": "/document/content/phrases/*/sentiment"
        }
    ],
    "outputs": [
        {
        "name": "output",
        "targetName": "analyzedText"
        }
    ]
    },
  ],
  "cognitiveServices": 
    {
    "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
    "description": "mycogsvcs resource in West US 2",
    "key": "<your key goes here>"
    },
  "knowledgeStore": { 
    "storageConnectionString": "<your connection string goes here>", 
    "projections": [ 
        { 
            "tables": [  
            { "tableName": "Reviews", "generatedKeyName": "ReviewId", "source": "/document/Review" , "sourceContext": null, "inputs": []}, 
            { "tableName": "Sentences", "generatedKeyName": "SentenceId", "source": "/document/Review/Sentences/*", "sourceContext": null, "inputs": []}, 
            { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/Review/Sentences/*/KeyPhrases", "sourceContext": null, "inputs": []}, 
            { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/Review/Sentences/*/Entities/*" ,"sourceContext": null, "inputs": []} 

            ], 
            "objects": [ 
                { 
                "storageContainer": "Reviews", 
                "format": "json", 
                "source": "/document/Review", 
                "key": "/document/Review/Id" 
                } 
            ]      
        }    
    ]     
    } 
}
```

## <a name="components-backing-a-knowledge-store"></a>Вспомогательные компоненты хранилища знаний

Для создания хранилища знаний потребуются следующие службы и артефакты.

### <a name="1---source-data"></a>1\. Исходные данные

Данные или документы, которые требуется обогатить, должны быть доступны в источнике данных Azure, поддерживаемом индексатором Поиска Azure: 

* [Azure SQL;](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

* [Azure Cosmos DB](search-howto-index-cosmosdb.md)

* [Хранилище BLOB-объектов Azure](search-howto-indexing-azure-blob-storage.md)

[Хранилище таблиц Azure](search-howto-indexing-azure-tables.md) можно использовать для исходящих данных в хранилище знаний, но нельзя использовать как ресурс для данных, поступающих в конвейер индексирования на основе ИИ.

### <a name="2---azure-search-service"></a>2\. Служба "Поиск Azure"

Вам также потребуется служба "Поиск Azure" и REST API для создания и настройки объектов, используемых для обогащения данных. `api-version=2019-05-06-Preview` — это REST API для создания хранилища знаний.

Поиск Azure предоставляет функцию индексатора, а индексаторы используются для выполнения всего процесса, в результате которого обогащенные документы помещаются в службу хранилища Azure. Индексаторы используют источник данных, индекс и набор навыков. Все они необходимы для наполнения хранилища знаний.

| Объект. | REST API | ОПИСАНИЕ |
|--------|----------|-------------|
| Источник данных | [Создание источника данных](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Ресурс, определяющий внешний источник данных с исходными данными, которые будут использоваться для создания обогащенных документов.  |
| Набор навыков | [Создание набора навыков (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Ресурс, в котором описано использование [встроенных навыков](cognitive-search-predefined-skills.md) и [пользовательских когнитивных навыков](cognitive-search-custom-skill-interface.md) в конвейере обогащения данных во время индексирования. |
| index | [Создание индекса](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Схема, описывающая индекс службы "Поиск Azure". Поля в этом индексе сопоставляются с полями в источнике данных или с полями, созданным на этапе обогащения (например, поле с названиями организаций, созданное навыком распознавания сущностей). |
| Индексатор | [Создание индексатора (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Ресурс, определяющий компоненты для процесса индексирования, в том числе источник данных, набор навыков, сопоставлений полей источника и промежуточных структур с полями целевого индекса, определение индекса. При запуске индексатора активируется процесс приема и обогащения данных. На выходе формируется индекс поиска, который основан на схеме индекса и заполнен исходными данными, обогащенными с помощью наборов навыков.  |

### <a name="3---cognitive-services"></a>3\. Cognitive Services

Определяемые набором навыков операции обогащения основаны на таких функциях Cognitive Services, как Компьютерное зрение и распознавание языка. Функции служб Cognitive Services используются во время индексирования с помощью набора навыков. Набор навыков — это сочетание навыков, связанных с отдельными функциями Компьютерного зрения и распознавания языка. Чтобы интегрировать Cognitive Services, необходимо [вложить ресурс Cognitive Services](cognitive-search-attach-cognitive-services.md) в набор навыков.

### <a name="4---storage-account"></a>4\. Учетная запись хранения

Служба "Поиск Azure" создает контейнер больших двоичных объектов или таблиц вашей учетной записи хранения службы хранилища Azure. Это зависит от настройки набора навыков. Данные должны находится в хранилище BLOB-объектов или таблиц. Если у вас его нет, необходимо создать учетную запись хранения в Azure. В службе хранилища Azure таблицы и объекты содержат обогащенные документы, созданные конвейером индексирования на основе ИИ.

Учетная запись хранения указывается в наборе навыков. В `api-version=2019-05-06-Preview` определение набора навыков включает определение хранилища знаний, поэтому вы можете предоставить сведения об учетной записи.

<a name="tools-and-apps"></a>

### <a name="5---access-and-consume"></a>5\. Доступ и использование

После помещения обогащенных документов в хранилище любое средство или технология, способные подключаться к хранилищу BLOB-объектов или таблиц Azure, можно использовать для изучения, анализа или использования их содержимого. Вот список основных таких средств:

+ [Обозреватель службы хранилища](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) позволяет просматривать структуру и содержимое обогащенных документов. Его можно считать базовым средством для просмотра содержимого хранилища знаний.

+ [Power BI с Power Query](https://support.office.com/article/connect-to-microsoft-azure-blob-storage-power-query-f8165faa-4589-47b1-86b6-7015b330d13e) позволяют создавать запросы на естественном языке или использовать средства для создания отчетов и анализа при работе с числовыми данными.

+ [Фабрика данных Azure](https://docs.microsoft.com/azure/data-factory/) позволяет выполнять дальнейшую обработку данных.

+ Индекс службы "Поиск Azure" позволяет выполнять полнотекстовый поиск по содержимому, проиндексированному с помощью функции [когнитивного поиска](cognitive-search-concept-intro.md).

## <a name="document-persistence"></a>Формат сохранения документов

В учетной записи хранения обогащенные документы представлены в виде таблиц в Хранилище таблиц Azure или в виде объектов в хранилище BLOB-объектов Azure. Учитывайте, что однажды сохраненные обогащенные документы в дальнейшем можно использовать как источник исходных данных для других баз данных и средств.

+ Хранилище таблиц полезно в том случае, если данные должны быть представлены в табличном формате, учитывающем схему. Если вы хотите изменить форму или перекомпоновать элементы по-другому, Хранилище таблиц обеспечит требуемый уровень детализации.

+ Хранилище BLOB-объектов создает одно комплексное представление для каждого документа в формате JSON. Чтобы получить полный спектр выражений можно использовать оба формата хранения в рамках одного набора навыков.

+ Поиск Azure сохраняет содержимое в индексе. Если ваш сценарий не связан с выполнением поиска (например, если требуется провести анализ данных в другом средстве), можно удалить индекс, созданный конвейером. Но можно сохранить индекс и использовать встроенные средства, например [обозреватель поиска](search-explorer.md), в качестве третьего промежуточного средства (помимо Обозревателя службы хранилища и приложения аналитики) для взаимодействия с содержимым.

Кроме содержимого документов в обогащенных документах также присутствуют метаданные версии набора навыков, с помощью которого выполнялось обогащение.  

## <a name="inside-a-knowledge-store"></a>Устройство хранилища знаний

Хранилище знаний состоит из кэша заметок и проекций. *Кэш* используется самой службой и предназначен для кэширования результатов обогащения и отслеживания изменений. *Проекция* определяет схему и структуру операций обогащения, которые соответствуют вашим задачам. В каждом хранилище может быть только один кэш и много проекций. 

Кэш всегда является контейнером BLOB-объектов, а проекции могут храниться как в виде как таблиц, так и в виде объектов:

+ Если проекция является объектом, она сопоставляется с хранилищем BLOB-объектов, в котором проекция хранится в контейнере вместе с объектами или иерархическими представлениями в формате JSON для таких сценариев, как конвейер обработки и анализа данных.

+ Если проекция является таблицей, она сопоставляется с Хранилищем таблиц. Табличный формат позволяет сохранить связи для таких сценариев, как анализ данных или экспорт в виде кадров данных для машинного обучения. Обогащенные проекции затем можно легко импортировать в другие хранилища данных. 

В хранилище знаний можно создать множество проекций, соответствующих требованиям различных пользователей в вашей организации. Разработчикам может требоваться доступ к полным представлениям обогащенных документов в формате JSON, а специалистам по анализу и обработке данных — детализированные или модульные структуры, сформированные набором навыков.

Например, если одна из целей обогащения данных состоит в создании набора данных для обучения модели, создав проекцию данных в виде объекта, можно использовать их в конвейерах обработки и анализа данных. Если же вам необходимо быстро создать панель мониторинга Power BI на основе обогащенных документов, для этого лучше подойдет проекция в табличном формате.

<!---
## Data lifecycle and billing

Each time you run the indexer, the cache in Azure storage is updated if the skillset definition or underlying source data has changed. As input documents are edited or deleted, changes are propagated through the annotation cache to the projections, ensuring that your projected data is a current representation of your inputs at the end of the indexer run. 

Generally speaking, pipeline processing can be an all-or-nothing operation, but Azure Search can process incremental changes, which saves you time and money.

If a document is new or updated, all skills are run. If only the skillset changes, reprocessing is scoped to just those skills and documents affected by your edit.

### Changes to a skillset
Suppose that you have a pipeline composed of multiple skills, operating over a large body of static data (for example, scanned documents), that takes 8 hours and costs $200 to create the knowledge store. Now suppose you need to tweak one of the skills in the skillset. Rather than starting over, Azure Search can determine which skill is affected, and reprocess only that skill. Cached data and projections that are unaffected by the change remain intact in the knowledge store.

### Changes in the data
Scenarios can vary considerably, but let's suppose instead of static data, you have volatile data that changes between indexer invocations. Given no changes to the skillset, you are charged for processing the delta of new and modified document. The timestamp information varies by data source, but for illustration, in a Blob container, Azure Search looks at the `lastmodified` date to determine which blobs need to be ingested.

> [!Note]
> While you can edit the data in the projections, any edits will be overwritten on the next pipeline invocation, assuming the document in source data is updated. 

### Deletions

Although Azure Search creates and updates structures and content in Azure storage, it does not delete them. Projections and cached documents continue to exist even when the skillset is deleted. As the owner of the storage account, you should delete a projection if it is no longer needed. 

### Tips for development

+ Start small with a representative sample of your data as you make significant changes to skillset composition. As your design finalizes, you can slowly add more data during later-stage development, and then roll in the entire data set when you are comfortable with the pipeline composition.

+ Retain control over indexer invocation. Indexers can run on a schedule, which is helpful for solutions that are rolled into production, but less helpful if you are actively developing your pipeline. During development, avoid schedules so that you don’t lose track of cache or projection state. Once your solution is in production and skillset composition is static, you can put the indexer on a schedule to pick up routine changes in the external source data. 

-->

## <a name="where-do-i-start"></a>С чего начать?

В учебных целях рекомендуется использовать бесплатную версию службы, но обратите внимание, что число бесплатных транзакций ограничено 20 документами для одной подписки.

При использовании нескольких служб создайте все службы в одном регионе, чтобы обеспечить их оптимальную производительность и минимизировать затраты. Плата не взимается за входящий или исходящий трафик данных, которые отправляются в другую службу, расположенную в том же регионе.

**Шаг 1. [Создание индекса службы "Поиск Azure"](search-create-service-portal.md)** 

**Шаг 2. [Создание учетной записи хранения Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)** 

**Шаг 3. [Создание ресурса Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)** 

**Шаг 4. [Начало работы с порталом](cognitive-search-quickstart-blob.md) или [начало работы с образцами данных с использованием REST и Postman](knowledge-store-howto.md)** 

С помощью `api-version=2019-05-06-Preview` REST можно создать конвейер на основе ИИ, в состав которого входит хранилище знаний. В последней предварительной версии API объект набора навыков предоставляет определение `knowledgeStore`.

## <a name="takeaways"></a>Общие выводы

Хранилище знаний обеспечивает ряд преимуществ, в том числе возможность использовать обогащенные документы в сценариях, связанных не только с поиском, а также управлять затратами и процессом обогащения данных. Чтобы все эти функции стали доступны, достаточно добавить учетную запись хранения в набор навыков и использовать обновленный язык выражений, как указано в статье о [начале работы с хранилищем знаний](knowledge-store-howto.md). 

## <a name="next-steps"></a>Дополнительная информация

Простейший способ создания обогащенных документов — использовать мастер **импорта данных**.

> [!div class="nextstepaction"]
> [Краткое руководство Создание конвейера когнитивного поиска с использованием навыков и примера данных](cognitive-search-quickstart-blob.md)
