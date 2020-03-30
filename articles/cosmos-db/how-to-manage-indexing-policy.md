---
title: Управление политиками индексирования в Azure Cosmos DB
description: Узнайте, как управлять политиками индексирования, включать или исключать свойство из индексирования, как определить индексацию с помощью различных SDK Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: thweiss
ms.openlocfilehash: 58a1ee13afa76b152723cb71d4037f9c31cc8d4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252081"
---
# <a name="manage-indexing-policies-in-azure-cosmos-db"></a>Управление политиками индексирования в Azure Cosmos DB

В Azure Cosmos DB данные индексируются с помощью приведенных ниже [политик индексирования](index-policy.md), определенных для каждого контейнера. Политика индексирования по умолчанию, задаваемая для только что созданных контейнеров, применяет диапазонные индексы для любых строк или чисел. Эту политику можно переопределить собственной политикой индексирования.

## <a name="indexing-policy-examples"></a>Примеры политик индексирования

Вот несколько примеров политикиндексирования, показанных в [формате JSON,](index-policy.md#include-exclude-paths)которые разоблачаются на портале Azure. Значения параметров можно задать с помощью Azure CLI или любого пакета SDK.

### <a name="opt-out-policy-to-selectively-exclude-some-property-paths"></a>Политика отказа для выборочного исключения некоторых путей к свойствам

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*"
            }
        ],
        "excludedPaths": [
            {
                "path": "/path/to/single/excluded/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/excluded/properties/*"
            }
        ]
    }
```

Эта политика индексирования эквивалентна той, ниже ```kind``` ```dataType```которой ```precision``` вручную устанавливается, и к их значениям по умолчанию. Эти свойства больше не являются необходимыми для четкого набора, и вы можете полностью упустить их из политики индексирования (как показано в приведенном выше примере).

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number",
                        "precision": -1
                    },
                    {
                        "kind": "Range",
                        "dataType": "String",
                        "precision": -1
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/path/to/single/excluded/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/excluded/properties/*"
            }
        ]
    }
```

### <a name="opt-in-policy-to-selectively-include-some-property-paths"></a>Политика принятия для выборочного включения некоторых путей к свойствам

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*"
            }
        ],
        "excludedPaths": [
            {
                "path": "/*"
            }
        ]
    }
```

Эта политика индексирования эквивалентна той, ниже ```kind``` ```dataType```которой ```precision``` вручную устанавливается, и к их значениям по умолчанию. Эти свойства больше не являются необходимыми для четкого набора, и вы можете полностью упустить их из политики индексирования (как показано в приведенном выше примере).

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    }
                ]
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/*"
            }
        ]
    }
```

> [!NOTE] 
> Обычно рекомендуется использовать политику индексирования **отказа**, чтобы служба Azure Cosmos DB заранее индексировала любое новое свойство, которое может быть добавлено в модель.

### <a name="using-a-spatial-index-on-a-specific-property-path-only"></a>Использование пространственного индекса только для определенного пути к свойству

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/_etag/?"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/path/to/geojson/property/?",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ]
}
```

## <a name="composite-indexing-policy-examples"></a>Примеры политик составного индексирования

Кроме добавления и удаления путей отдельных свойств, вы также можете указать составной индекс. Если вы хотите выполнить запрос, который содержит предложение `ORDER BY` для нескольких свойств, эти свойства должны содержать [составной индекс](index-policy.md#composite-indexes) Кроме того, составные индексы будут иметь преимущество в производительности для запросов, которые имеют фильтр и имеют положение ORDER BY о различных свойствах.

### <a name="composite-index-defined-for-name-asc-age-desc"></a>Определенный составной индекс (name asc, age desc):

```json
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"descending"
                }
            ]
        ]
    }
```

Вышеуказанный составной индекс по имени и возрасту необходим для #1 запроса и #2 запросов:

Запрос 1:

```sql
    SELECT *
    FROM c
    ORDER BY c.name ASC, c.age DESC
```

Запрос 2:

```sql
    SELECT *
    FROM c
    ORDER BY c.name DESC, c.age ASC
```

Этот составной индекс принесет пользу #3 запроса и #4 и оптимизации фильтров:

Запрос #3:

```sql
SELECT *
FROM c
WHERE c.name = "Tim"
ORDER BY c.name DESC, c.age ASC
```

#4 запросов:

```sql
SELECT *
FROM c
WHERE c.name = "Tim" AND c.age > 18
```

### <a name="composite-index-defined-for-name-asc-age-asc-and-name-asc-age-desc"></a>Композитный индекс, определяемый для (имя ASC, возраст ASC) и (имя ASC, возраст DESC):

Вы можете определить несколько разных составных индексов в одной и той же политике индексирования.

```json
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"ascending"
                }
            ],
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"descending"
                }
            ]
        ]
    }
```

### <a name="composite-index-defined-for-name-asc-age-asc"></a>Композитный индекс, определяемый для (название ASC, возраст ASC):

Порядок указывать необязательно. Если он не указан, по умолчанию используется порядок по возрастанию.

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                },
                {  
                    "path":"/age",
                }
            ]
        ]
}
```

### <a name="excluding-all-property-paths-but-keeping-indexing-active"></a>Исключение всех путей к свойствам, но с активным индексированием

Эту политику можно использовать в ситуациях, когда [функция учета срока жизни (TTL)](time-to-live.md) включена, но дополнительный индекс не требуется (для использования Azure Cosmos DB в качестве хранилища исключительно пар "ключ-значение").

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [],
        "excludedPaths": [{
            "path": "/*"
        }]
    }
```

### <a name="no-indexing"></a>Без индексирования

Эта политика отключит индексацию. Если `indexingMode` `none`установлен, вы не можете установить TTL на контейнере.

```json
    {
        "indexingMode": "none"
    }
```

## <a name="updating-indexing-policy"></a>Политика обновления индексирования

В Azure Cosmos DB политика индексирования может быть обновлена с помощью любого из приведенных ниже методов:

- на портале Azure;
- с помощью Azure CLI;
- с помощью PowerShell
- с помощью одного из пакетов SDK.

[Обновление политики индексирования](index-policy.md#modifying-the-indexing-policy) приводит к преобразованию индекса. Ход выполнения этого преобразования можно отслеживать с помощью пакетов SDK.

> [!NOTE]
> При обновлении политики индексации записи в Azure Cosmos DB будут непрерывными. Во время повторной индексации запросы могут возвращать частичные результаты по мере обновления индекса.

## <a name="use-the-azure-portal"></a>Использование портала Azure

Контейнеры Azure Cosmos хранят используемую политику индексирования в виде документа JSON, который можно непосредственно редактировать на портале Azure.

1. Войдите на [портал Azure](https://portal.azure.com/).

1. Создайте новую учетную запись Azure Cosmos или выберите имеющуюся.

1. Откройте панель **обозревателя данных** и выберите контейнер, с которым собираетесь работать.

1. Щелкните **Scale & Settings** (Масштаб и параметры).

1. Измените документ JSON политики индексирования (ознакомьтесь с примерами [ниже](#indexing-policy-examples)).

1. После завершения нажмите кнопку **Save** (Сохранить).

![Управление индексированием с помощью портала Azure](./media/how-to-manage-indexing-policy/indexing-policy-portal.png)

## <a name="use-the-azure-cli"></a>Использование Azure CLI

Для создания контейнера с типоиковой политикой индексирования [см.](manage-with-cli.md#create-a-container-with-a-custom-index-policy)

## <a name="use-powershell"></a>Использование PowerShell

Для создания контейнера с типоичной политикой индексирования [см.](manage-with-powershell.md#create-container-custom-index)

## <a name="use-the-net-sdk-v2"></a>Использование пакета SDK для .NET версии 2

Объект `DocumentCollection` из [.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) `IndexingPolicy` предоставляет свойство, `IndexingMode` которое позволяет `IncludedPaths` `ExcludedPaths`изменять и добавлять или удалять и .

```csharp
// Retrieve the container's details
ResourceResponse<DocumentCollection> containerResponse = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"));
// Set the indexing mode to consistent
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
// Add an included path
containerResponse.Resource.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
// Add an excluded path
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/name/*" });
// Add a spatial index
containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(new SpatialSpec() { Path = "/locations/*", SpatialTypes = new Collection<SpatialType>() { SpatialType.Point } } );
// Add a composite index
containerResponse.Resource.IndexingPolicy.CompositeIndexes.Add(new Collection<CompositePath> {new CompositePath() { Path = "/name", Order = CompositePathSortOrder.Ascending }, new CompositePath() { Path = "/age", Order = CompositePathSortOrder.Descending }});
// Update container with changes
await client.ReplaceDocumentCollectionAsync(containerResponse.Resource);
```

Чтобы отслеживать ход выполнения преобразования индекса, передайте объект `RequestOptions`, который задает для свойства `PopulateQuotaInfo` значение `true`.

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> container = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"), new RequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = container.IndexTransformationProgress;
```

## <a name="use-the-net-sdk-v3"></a>Используйте .NET SDK V3

Объект `ContainerProperties` из [.NET SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) (см. [этот быстрый запуск](create-sql-api-dotnet.md) относительно его использования) предоставляет свойство, `IndexingPolicy` которое позволяет изменить `IndexingMode` и добавить или удалить `IncludedPaths` и `ExcludedPaths`.

```csharp
// Retrieve the container's details
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Set the indexing mode to consistent
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
// Add an included path
containerResponse.Resource.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
// Add an excluded path
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/name/*" });
// Add a spatial index
SpatialPath spatialPath = new SpatialPath
{
    Path = "/locations/*"
};
spatialPath.SpatialTypes.Add(SpatialType.Point);
containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(spatialPath);
// Add a composite index
containerResponse.Resource.IndexingPolicy.CompositeIndexes.Add(new Collection<CompositePath> { new CompositePath() { Path = "/name", Order = CompositePathSortOrder.Ascending }, new CompositePath() { Path = "/age", Order = CompositePathSortOrder.Descending } });
// Update container with changes
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

Чтобы отследить прогресс преобразования `RequestOptions` индекса, `PopulateQuotaInfo` передайте `true`объект, который устанавливает `x-ms-documentdb-collection-index-transformation-progress` свойство, затем извлеките значение из заголовка ответа.

```csharp
// retrieve the container's details
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync(new ContainerRequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = long.Parse(containerResponse.Headers["x-ms-documentdb-collection-index-transformation-progress"]);
```

При определении политики пользовательской индексации при создании нового контейнера, свободно aPI SDK V3 позволяет написать это определение кратким и эффективным способом:

```csharp
await client.GetDatabase("database").DefineContainer(name: "container", partitionKeyPath: "/myPartitionKey")
    .WithIndexingPolicy()
        .WithIncludedPaths()
            .Path("/*")
        .Attach()
        .WithExcludedPaths()
            .Path("/name/*")
        .Attach()
        .WithSpatialIndex()
            .Path("/locations/*", SpatialType.Point)
        .Attach()
        .WithCompositeIndex()
            .Path("/name", CompositePathSortOrder.Ascending)
            .Path("/age", CompositePathSortOrder.Descending)
        .Attach()
    .Attach()
    .CreateIfNotExistsAsync();
```

## <a name="use-the-java-sdk"></a>Использование пакета SDK для Java

Объект `DocumentCollection` из [пакета SDK для Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) (его использование описано в [этом кратком руководстве](create-sql-api-java.md)) предоставляет методы `getIndexingPolicy()` и `setIndexingPolicy()`. Объект `IndexingPolicy`, которым они управляют, позволяет изменить режим индексирования и добавить или удалить включенные и исключенные пути.

```java
// Retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), null);
containerResponse.subscribe(result -> {
DocumentCollection container = result.getResource();
IndexingPolicy indexingPolicy = container.getIndexingPolicy();

// Set the indexing mode to consistent
indexingPolicy.setIndexingMode(IndexingMode.Consistent);

// Add an included path

Collection<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.setPath("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Add an excluded path

Collection<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.setPath("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.setExcludedPaths(excludedPaths);

// Add a spatial index

Collection<SpatialSpec> spatialIndexes = new ArrayList<SpatialSpec>();
Collection<SpatialType> collectionOfSpatialTypes = new ArrayList<SpatialType>();

SpatialSpec spec = new SpatialSpec();
spec.setPath("/locations/*");
collectionOfSpatialTypes.add(SpatialType.Point);
spec.setSpatialTypes(collectionOfSpatialTypes);
spatialIndexes.add(spec);

indexingPolicy.setSpatialIndexes(spatialIndexes);

// Add a composite index

Collection<ArrayList<CompositePath>> compositeIndexes = new ArrayList<>();
ArrayList<CompositePath> compositePaths = new ArrayList<>();

CompositePath nameCompositePath = new CompositePath();
nameCompositePath.setPath("/name");
nameCompositePath.setOrder(CompositePathSortOrder.Ascending);

CompositePath ageCompositePath = new CompositePath();
ageCompositePath.setPath("/age");
ageCompositePath.setOrder(CompositePathSortOrder.Descending);

compositePaths.add(ageCompositePath);
compositePaths.add(nameCompositePath);

compositeIndexes.add(compositePaths);
indexingPolicy.setCompositeIndexes(compositeIndexes);

// Update the container with changes

 client.replaceCollection(container, null);
});
```

Чтобы отслеживать ход выполнения преобразования индекса в контейнере, передайте объект `RequestOptions`, который запрашивает сведения о квоте для заполнения, а затем извлеките значение из заголовка ответа `x-ms-documentdb-collection-index-transformation-progress`.

```java
// set the RequestOptions object
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPopulateQuotaInfo(true);
// retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), requestOptions);
containerResponse.subscribe(result -> {
    // retrieve the index transformation progress from the response headers
    String indexTransformationProgress = result.getResponseHeaders().get("x-ms-documentdb-collection-index-transformation-progress");
});
```

## <a name="use-the-nodejs-sdk"></a>Использование пакета SDK для Node.js

Интерфейс `ContainerDefinition` из [пакета SDK для Node.js](https://www.npmjs.com/package/@azure/cosmos) (его использование описано в [этом кратком руководстве](create-sql-api-nodejs.md)) предоставляет свойство `indexingPolicy`, которое позволяет изменить `indexingMode` и добавить или удалить `includedPaths` и `excludedPaths`.

Извлекать данные контейнера

```javascript
const containerResponse = await client.database('database').container('container').read();
```

Установите режим индексации на соответствие

```javascript
containerResponse.body.indexingPolicy.indexingMode = "consistent";
```

Добавление включенного пути, включая пространственный индекс

```javascript
containerResponse.body.indexingPolicy.includedPaths.push({
    includedPaths: [
      {
        path: "/age/*",
        indexes: [
          {
            kind: cosmos.DocumentBase.IndexKind.Range,
            dataType: cosmos.DocumentBase.DataType.String
          },
          {
            kind: cosmos.DocumentBase.IndexKind.Range,
            dataType: cosmos.DocumentBase.DataType.Number
          }
        ]
      },
      {
        path: "/locations/*",
        indexes: [
          {
            kind: cosmos.DocumentBase.IndexKind.Spatial,
            dataType: cosmos.DocumentBase.DataType.Point
          }
        ]
      }
    ]
  });
```

Добавление исключенного пути

```javascript
containerResponse.body.indexingPolicy.excludedPaths.push({ path: '/name/*' });
```

Обновление контейнера с изменениями

```javascript
const replaceResponse = await client.database('database').container('container').replace(containerResponse.body);
```

Чтобы отслеживать ход выполнения преобразования индекса в контейнере, передайте объект `RequestOptions`, который задает для свойства `populateQuotaInfo` значение `true`, а затем извлеките значение из заголовка ответа `x-ms-documentdb-collection-index-transformation-progress`.

```javascript
// retrieve the container's details
const containerResponse = await client.database('database').container('container').read({
    populateQuotaInfo: true
});
// retrieve the index transformation progress from the response headers
const indexTransformationProgress = replaceResponse.headers['x-ms-documentdb-collection-index-transformation-progress'];
```

## <a name="use-the-python-sdk-v3"></a>Используйте Python SDK V3

При использовании [Python SDK V3](https://pypi.org/project/azure-cosmos/) (см. [этот квистарт](create-sql-api-python.md) относительно его использования), конфигурация контейнера управляется как словарь. Из этого словаря можно осуществлять доступ к политике индексации и всем ее атрибутам.

Извлекать данные контейнера

```python
containerPath = 'dbs/database/colls/collection'
container = client.ReadContainer(containerPath)
```

Установите режим индексации на соответствие

```python
container['indexingPolicy']['indexingMode'] = 'consistent'
```

Определение политики индексирования с включенным путем и пространственным индексом

```python
container["indexingPolicy"] = {

    "indexingMode":"consistent",
    "spatialIndexes":[
                {"path":"/location/*","types":["Point"]}
             ],
    "includedPaths":[{"path":"/age/*","indexes":[]}],
    "excludedPaths":[{"path":"/*"}]
}
```

Определение политики индексирования с исключенным способом

```python
container["indexingPolicy"] = {
    "indexingMode":"consistent",
    "includedPaths":[{"path":"/*","indexes":[]}],
    "excludedPaths":[{"path":"/name/*"}]
}
```

Добавить составной индекс

```python
container['indexingPolicy']['compositeIndexes'] = [
                [
                    {
                        "path": "/name",
                        "order": "ascending"
                    },
                    {
                        "path": "/age",
                        "order": "descending"
                    }
                ]
                ]
```

Обновление контейнера с изменениями

```python
response = client.ReplaceContainer(containerPath, container)
```

## <a name="use-the-python-sdk-v4"></a>Используйте Python SDK V4

При использовании [Python SDK V4](https://pypi.org/project/azure-cosmos/)конфигурация контейнера управляется как словарь. Из этого словаря можно осуществлять доступ к политике индексации и всем ее атрибутам.

Извлекать данные контейнера

```python
database_client = cosmos_client.get_database_client('database')
container_client = database_client.get_container_client('container')
container = container_client.read()
```

Установите режим индексации на соответствие

```python
indexingPolicy = {
    'indexingMode': 'consistent'
}
```

Определение политики индексирования с включенным путем и пространственным индексом

```python
indexingPolicy = {
    "indexingMode":"consistent",
    "spatialIndexes":[
        {"path":"/location/*","types":["Point"]}
    ],
    "includedPaths":[{"path":"/age/*","indexes":[]}],
    "excludedPaths":[{"path":"/*"}]
}
```

Определение политики индексирования с исключенным способом

```python
indexingPolicy = {
    "indexingMode":"consistent",
    "includedPaths":[{"path":"/*","indexes":[]}],
    "excludedPaths":[{"path":"/name/*"}]
}
```

Добавить составной индекс

```python
indexingPolicy['compositeIndexes'] = [
    [
        {
            "path": "/name",
            "order": "ascending"
        },
        {
            "path": "/age",
            "order": "descending"
        }
    ]
]
```

Обновление контейнера с изменениями

```python
response = database_client.replace_container(container_client, container['partitionKey'], indexingPolicy)
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об индексировании см. по следующим ссылкам:

- [Общие сведения об индексировании](index-overview.md)
- [Политика индексирования](index-policy.md)
