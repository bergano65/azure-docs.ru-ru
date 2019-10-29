---
title: Общие сведения об использовании хранилища знаний (предварительная версия)
titleSuffix: Azure Cognitive Search
description: Хранилище знаний — это специальное хранилище Azure, в котором можно хранить обогащенные документы, просматривать и преобразовывать их, а также обеспечивать доступ к ним службе "Когнитивный поиск Azure" и другим приложениям.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 82f8606f4b4201833667347d3ed16fdd73f70a36
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790357"
---
# <a name="introduction-to-knowledge-stores-in-azure-cognitive-search"></a>Общие сведения о хранилищах знаний в Когнитивном поиске Azure

> [!Note]
> Доступна предварительная версия хранилища знаний, не предназначенная для использования в рабочей среде. Эта функция предоставляется в [версии REST API 2019-05-06-Preview](search-api-preview.md). Поддержка пакета SDK для .NET пока не реализована.
>

Хранилище знаний — это компонент Когнитивного поиска Azure, который сохраняет выходные данные [конвейера обогащения, выполняемого с помощью ИИ](cognitive-search-concept-intro.md), для последующего анализа или обработки. *Обогащенный документ* — это выходные данные конвейера, созданные из содержимого, которое было извлечено, структурировано и проанализировано с использованием процессов ИИ. В стандартном конвейере на основе ИИ обогащенные документы являются временными и используются только во время индексирования, а затем удаляются. В хранилище знаний сохраняются обогащенные документы. 

Если вы уже использовали когнитивные навыки в Когнитивном поиске Azure, то знаете, что *наборы навыков* проводят серии операций обогащения документа. Результатом этого процесса может быть индекс поиска или проекции документа в хранилище знаний (нововведение в этой предварительной версии). Два вида выходных данных, индекс поиска и хранилище знаний, совместно используют одно и то же содержимое, но хранятся и используются совершенно разными способами.

Физически хранилищем знаний является [служба хранилища Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview) (хранилище таблиц Azure, хранилище BLOB-объектов Azure или оба). Любой инструмент или процесс, который может подключиться к службе хранилища Azure, может использовать содержимое хранилища знаний.

![Хранилище знаний на схеме конвейера](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Хранилище знаний на схеме конвейера")

Проекции — это механизм структурирования данных в хранилище знаний. Например, с помощью проекций вы можете выбрать способ сохранения выходных данных: в виде одного большого двоичного объекта или коллекции связанных таблиц. 

Чтобы использовать хранилище знаний, в набор навыков нужно добавить элемент `knowledgeStore`,определяющий пошаговые операции в конвейере индексирования. Во время выполнения Когнитивный поиск Azure создает пространство в учетной записи хранения Azure и проецирует обогащенные документы как большие двоичные объекты или в таблицы в зависимости от конфигурации.

## <a name="benefits-of-knowledge-store"></a>Преимущества хранилища знаний

Хранилище знаний позволяет получить структуру, контекст и фактическое содержимое из неструктурированных и частично структурированных данных, таких как большие двоичные объекты и проанализированные файлы изображений, и даже из структурированных данных, преобразованных в новые формы. В [пошаговом руководстве](knowledge-store-howto.md), подготовленном для этой предварительной версии, показано, как документ в формате JSON дробится на субструктуры, преобразуется в новые структуры и трансформируется другими способами. Затем он становится доступным для последующих процессов, таких как машинное обучение или обработка и анализ данных.

Хотя возможности конвейера обогащения на основе ИИ сами по себе впечатляют, главной особенностью хранилища знаний является способность изменять форму данных. Можно начать с базового набора навыков и совершенствовать его цикл за циклом, чтобы увеличить число уровней структур. Это позволит создавать структуры, пригодные для использования другими приложениями, помимо службы "Когнитивный поиск Azure".

Хранилище знаний обеспечивает следующие преимущества:

+ Обогащенные документы может использовать не только служба поиска, но и [средства аналитики и создания отчетов](#tools-and-apps). Power BI c Power Query являются оптимальным решением. Но и любое приложение, способное подключаться к службе хранилища Azure, может получать данные из созданного хранилища знаний.

+ Улучшение конвейера индексирования на основе ИИ в ходе отладки и определений набора навыков. Хранилище знаний предоставляет результат определения набора навыков в конвейере индексирования на основе ИИ. Эти результаты можно использовать для создания усовершенствованного набора навыков, так как вы можете отслеживать воздействие процесса обогащения. Просматривать содержимое хранилища знаний можно с помощью [Обозревателя службы хранилища](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) Azure.

+ Преобразование данных в новые формы. Теперь доступна функция преобразования данных, реализованная в коде набора навыков. [Навык формирователя](cognitive-search-skill-shaper.md) в службе "Когнитивный поиск Azure" был доработан для выполнения этой задачи. Преобразование формы позволяет определять проекцию, которая соответствует предполагаемому способу применения данных, сохраняя при этом взаимосвязи.

> [!Note]
> Обогащение на основе ИИ с использованием Cognitive Services работает следующим образом. Когнитивный поиск Azure интегрируется с функциями распознавания изображений и текста Cognitive Services. С помощью этих функций извлекаются сведения из файлов изображений, распознаются сущности, извлекаются ключевые фразы из текстовых файлов и т. д., после чего обогащаются исходные данные. Дополнительные сведения см. в статье [Что такое когнитивный поиск в службе "Поиск Azure"?](cognitive-search-concept-intro.md).

## <a name="creating-a-knowledge-store"></a>Создание хранилища знаний

Хранилище знаний является частью [набора навыков](cognitive-search-working-with-skillsets.md), который, в свою очередь, является частью [индексатора](search-indexer-overview.md). 

В этой предварительной версии можно создать хранилище знаний с помощью REST API и `api-version=2019-05-06-Preview` или мастера **импорта данных** на портале.

### <a name="json-representation-of-a-knowledge-store"></a>Представление JSON хранилища знаний

Приведенный ниже код JSON определяет `knowledgeStore` — элемент набора навыков, вызываемый индексатором (не показано). Если вы уже знакомы с обогащением на основе ИИ, то знаете, что набор навыков обуславливает создание, организацию и содержание каждого обогащенного документа. Набор навыков должен содержать по крайней мере один навык, который, скорее всего, является навыком формирователя, если вы модулируете структуры данных.

`knowledgeStore` состоит из соединения и проекций. 

+ Подключение к учетной записи хранения происходит в том же регионе, где находится служба "Поиск Azure". 

+ Проекции — это пары "таблицы — объекты". `Tables` определяют физическое выражение для обогащенных документов в хранилище таблиц Azure. `Objects` определяют физические объекты в хранилище BLOB-объектов Azure.

```json
{
  "name": "my-new-skillset",
  "description": "Example showing knowledgeStore placement in a skillset.",
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
               
            ]      
        },
        { 
            "tables": [ 
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

### <a name="sources-of-data-for-a-knowledge-store"></a>Источники данных для хранилища знаний

Если хранилище знаний выводится из конвейера обогащения с помощью ИИ, каковы эти входные данные? Исходные данные, которые необходимо извлечь, обогатить и в конечном итоге сохранить в хранилище знаний, могут исходить из любого источника данных Azure, поддерживаемого индексаторами поиска: 

* [Azure Cosmos DB](search-howto-index-cosmosdb.md)

* [Хранилище BLOB-объектов Azure](search-howto-indexing-azure-blob-storage.md)

* [Хранилище таблиц Azure](search-howto-indexing-azure-tables.md)

* [Azure SQL;](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

Создаваемые вами индексаторы и наборы навыков извлекают и обогащают или преобразуют это содержимое как часть рабочей нагрузки индексирования, а затем сохраняют результаты в хранилище знаний.

### <a name="rest-apis-used-in-creation-of-a-knowledge-store"></a>REST API, используемые при создании хранилища знаний

Только два API-интерфейса имеют расширения, необходимые для создания хранилища знаний (создание набора навыков и создание индексатора). Другие API-интерфейсы используются "как есть".

| Объект. | REST API | ОПИСАНИЕ |
|--------|----------|-------------|
| Источник данных | [Создание источника данных](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Ресурс, определяющий внешний источник данных с исходными данными, которые будут использоваться для создания обогащенных документов.  |
| Набор навыков | [Создание набора квалификационных навыков (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Ресурс, в котором описано использование [встроенных навыков](cognitive-search-predefined-skills.md) и [пользовательских когнитивных навыков](cognitive-search-custom-skill-interface.md) в конвейере обогащения данных во время индексирования. Набор навыков имеет определение `knowledgeStore` как дочерний элемент. |
| index | [Создание индекса](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Схема, которая выражает индекс поиска. Поля в этом индексе сопоставляются с полями в источнике данных или с полями, созданным на этапе обогащения (например, поле с названиями организаций, созданное навыком распознавания сущностей). |
| Индексатор | [Создание индексатора (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Ресурс, определяющий компоненты для процесса индексирования, в том числе источник данных, набор навыков, сопоставлений полей источника и промежуточных структур с полями целевого индекса, определение индекса. При запуске индексатора активируется процесс приема и обогащения данных. На выходе формируется индекс поиска, который основан на схеме индекса и заполнен исходными данными, обогащенными с помощью наборов навыков.  |

### <a name="physical-composition-of-a-knowledge-store"></a>Физический состав хранилища знаний

 *Проекция*, являющаяся элементом определения `knowledgeStore`, формулирует схему и структуру выходных данных так, чтобы они соответствовали предполагаемому использованию. При наличии приложений, использующих данные в различных форматах и формах, можно определить несколько проекций. 

Проекции могут храниться в виде объектов или таблиц:

+ Если проекция является объектом, она сопоставляется с хранилищем BLOB-объектов, в котором проекция хранится в контейнере вместе с объектами или иерархическими представлениями в формате JSON для таких сценариев, как конвейер обработки и анализа данных.

+ Если проекция является таблицей, она сопоставляется с Хранилищем таблиц. Табличный формат позволяет сохранить связи для таких сценариев, как анализ данных или экспорт в виде кадров данных для машинного обучения. Обогащенные проекции затем можно легко импортировать в другие хранилища данных. 

В хранилище знаний можно создать множество проекций, соответствующих требованиям различных пользователей в вашей организации. Разработчикам может требоваться доступ к полным представлениям обогащенных документов в формате JSON, а специалистам по анализу и обработке данных — детализированные или модульные структуры, сформированные набором навыков.

Например, если одна из целей обогащения данных состоит в создании набора данных для обучения модели, создав проекцию данных в виде объекта, можно использовать их в конвейерах обработки и анализа данных. Если же вам необходимо быстро создать панель мониторинга Power BI на основе обогащенных документов, для этого лучше подойдет проекция в табличном формате.

<a name="tools-and-apps"></a>

## <a name="connecting-with-tools-and-apps"></a>Подключение с помощью средств и приложений

После помещения обогащенных документов в хранилище любое средство или технология, способные подключаться к хранилищу BLOB-объектов или таблиц Azure, можно использовать для изучения, анализа или использования их содержимого. Вот список основных таких средств:

+ [Обозреватель службы хранилища](knowledge-store-view-storage-explorer.md) позволяет просматривать структуру и содержимое обогащенных документов. Его можно считать базовым средством для просмотра содержимого хранилища знаний.

+ [Power BI](knowledge-store-connect-power-bi.md) для средств создания отчетов и анализа, если у вас есть числовые данные.

+ [Фабрика данных Azure](https://docs.microsoft.com/azure/data-factory/) позволяет выполнять дальнейшую обработку данных.


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

<!-- ## Where do I start?

We recommend the Free service for learning purposes, but be aware that the number of free transactions is limited to 20 documents per day, per subscription.

When using multiple services, create all of your services in the same region for best performance and to minimize costs. You are not charged for bandwidth for inbound data or outbound data that goes to another service in the same region.

**Step 1: [Create an Azure Cognitive Search resource](search-create-service-portal.md)** 

**Step 2: [Create an Azure storage account](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)** 

**Step 3: [Create a Cognitive Services resource](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)** 

**Step 4: [Get started with the portal](cognitive-search-quickstart-blob.md) - or - [Get started with sample data using REST and Postman](knowledge-store-howto.md)** 

You can use REST `api-version=2019-05-06-Preview` to construct an AI-based pipeline that includes knowledge store. In the newest preview API, the Skillset object provides the `knowledgeStore` definition. -->

## <a name="next-steps"></a>Дополнительная информация

Хранилище знаний обеспечивает стойкость обогащенных документов, полезных при разработке набора навыков или создании структур и содержимого для использования любыми клиентскими приложениями, которые могут получить доступ к учетной записи службы хранилища Azure.

Проще всего создавать обогащенные документов с помощью мастера **импорта данных**, но вы также можете использовать Postman и REST API, что удобнее, если вы хотите получить представление о том, как создаются объекты и указываются ссылки на них.

> [!div class="nextstepaction"]
> [Создание хранилища знаний службы поиска Azure на портале Azure](knowledge-store-create-portal.md)
> [Создание хранилища знаний службы "Поиск Azure" с помощью REST](knowledge-store-create-rest.md)
