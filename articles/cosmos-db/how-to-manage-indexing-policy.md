---
title: Управление политиками индексирования в Azure Cosmos DB
description: Узнайте, как управлять политиками индексации, включать или исключать свойства из индексирования, определять индексацию с помощью различных пакетов SDK Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: thweiss
ms.openlocfilehash: 3b98975df194af4625087e1beb556efb2a347f43
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872066"
---
# <a name="manage-indexing-policies-in-azure-cosmos-db"></a>Управление политиками индексирования в Azure Cosmos DB

В Azure Cosmos DB данные индексируются с помощью приведенных ниже [политик индексирования](index-policy.md), определенных для каждого контейнера. Политика индексирования по умолчанию, задаваемая для только что созданных контейнеров, применяет диапазонные индексы для любых строк или чисел. Эту политику можно переопределить собственной политикой индексирования.

## <a name="indexing-policy-examples"></a>Примеры политик индексирования

Ниже приведены примеры политик индексирования в формате JSON, в котором они доступны на портале Azure. Значения параметров можно задать с помощью Azure CLI или любого пакета SDK.

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

Эта политика индексации эквивалентна приведенной ниже, которая вручную задает значения по умолчанию ```kind```, ```dataType```и ```precision```. Эти свойства больше не нужны для явной установки, и их можно полностью опустить в политике индексирования (как показано в примере выше).

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

Эта политика индексации эквивалентна приведенной ниже, которая вручную задает значения по умолчанию ```kind```, ```dataType```и ```precision```. Эти свойства больше не нужны для явной установки, и их можно полностью опустить в политике индексирования (как показано в примере выше).

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
            "path": "/\"_etag\"/?"
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

Кроме добавления и удаления путей отдельных свойств, вы также можете указать составной индекс. Если вы хотите выполнить запрос, который содержит предложение `ORDER BY` для нескольких свойств, эти свойства должны содержать [составной индекс](index-policy.md#composite-indexes) Кроме того, составные индексы будут иметь преимущество в производительности для запросов, имеющих фильтр и предложения ORDER BY для различных свойств.

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

Приведенный выше составной индекс по имени и возрасту необходим для #1 запросов и #2 запросов:

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

Этот составной индекс поможет #3 запросов и #4 запросов и оптимизации фильтров.

#3 запроса:

```sql
SELECT *
FROM c
WHERE c.name = "Tim"
ORDER BY c.name DESC, c.age ASC
```

#4 запроса:

```sql
SELECT *
FROM c
WHERE c.name = "Tim" AND c.age > 18
```

### <a name="composite-index-defined-for-name-asc-age-asc-and-name-asc-age-desc"></a>Составной индекс, определенный для (Name ASC, Age ASC) и (Name ASC, Age DESC):

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

### <a name="composite-index-defined-for-name-asc-age-asc"></a>Составной индекс, определенный для (Name ASC, Age ASC):

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

Эта политика отключит индексирование. Если `indexingMode` имеет значение `none`, то для контейнера нельзя задать TTL.

```json
    {
        "indexingMode": "none"
    }
```

## <a name="updating-indexing-policy"></a>Обновление политики индексации

В Azure Cosmos DB политику индексирования можно обновить одним из следующих способов.

- на портале Azure;
- с помощью Azure CLI;
- Использование PowerShell
- с помощью одного из пакетов SDK.

[Обновление политики индексирования](index-policy.md#modifying-the-indexing-policy) приводит к преобразованию индекса. Ход выполнения этого преобразования можно отслеживать с помощью пакетов SDK.

> [!NOTE]
> При обновлении политики индексации операции записи в Azure Cosmos DB будут непрерывными. Во время повторного индексирования запросы могут возвращать частичные результаты по мере обновления индекса.

## <a name="use-the-azure-portal"></a>Использование портала Azure

Контейнеры Azure Cosmos хранят используемую политику индексирования в виде документа JSON, который можно непосредственно редактировать на портале Azure.

1. Войдите на [портале Azure](https://portal.azure.com/).

1. Создайте новую учетную запись Azure Cosmos или выберите имеющуюся.

1. Откройте панель **обозревателя данных** и выберите контейнер, с которым собираетесь работать.

1. Щелкните **Scale & Settings** (Масштаб и параметры).

1. Измените документ JSON политики индексирования (ознакомьтесь с примерами [ниже](#indexing-policy-examples)).

1. После завершения нажмите кнопку **Save** (Сохранить).

![Управление индексированием с помощью портала Azure](./media/how-to-manage-indexing-policy/indexing-policy-portal.png)

## <a name="use-the-azure-cli"></a>Использование Azure CLI

Сведения о создании контейнера с пользовательской политикой индексации см. в разделе [Создание контейнера с настраиваемой политикой индексов с помощью интерфейса командной строки](manage-with-cli.md#create-a-container-with-a-custom-index-policy) .

## <a name="use-powershell"></a>Использование PowerShell

Сведения о создании контейнера с пользовательской политикой индексации см. в разделе [Создание контейнера с настраиваемой политикой индексов с помощью PowerShell](manage-with-powershell.md#create-container-custom-index) .

## <a name="use-the-net-sdk-v2"></a>Использование пакета SDK для .NET версии 2

Объект `DocumentCollection` из [пакета SDK .NET v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) предоставляет свойство `IndexingPolicy`, которое позволяет изменить `IndexingMode`, а также добавить или удалить `IncludedPaths` и `ExcludedPaths`.

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

## <a name="use-the-net-sdk-v3"></a>Использование пакета SDK для .NET v3

Объект `ContainerProperties` из [пакета SDK .NET v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) (см. [это краткое руководство](create-sql-api-dotnet.md) по его использованию) предоставляет свойство `IndexingPolicy`, которое позволяет изменить `IndexingMode` и добавить или удалить `IncludedPaths` и `ExcludedPaths`.

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

Чтобы отслеживать ход преобразования индекса, передайте объект `RequestOptions`, который задает для свойства `PopulateQuotaInfo` значение `true`, а затем извлеките его из заголовка ответа `x-ms-documentdb-collection-index-transformation-progress`.

```csharp
// retrieve the container's details
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync(new ContainerRequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = long.Parse(containerResponse.Headers["x-ms-documentdb-collection-index-transformation-progress"]);
```

При определении пользовательской политики индексирования при создании нового контейнера API-интерфейс V3's Fluent пакета SDK позволяет быстро и эффективно писать это определение:

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
ExcludedPath includedPath = new IncludedPath();
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

Получение сведений о контейнере

```javascript
const containerResponse = await client.database('database').container('container').read();
```

Установить режим индексирования "последовательный"

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

## <a name="use-the-python-sdk"></a>Использование пакета SDK для Python

При использовании [пакета SDK для Python](https://pypi.org/project/azure-cosmos/) (его использование описано в [этом кратком руководстве](create-sql-api-python.md)) управление конфигурацией контейнера осуществляется как управление словарем. Из этого словаря можно осуществлять доступ к политике индексации и всем ее атрибутам.

Получение сведений о контейнере

```python
containerPath = 'dbs/database/colls/collection'
container = client.ReadContainer(containerPath)
```

Установить режим индексирования "последовательный"

```python
container['indexingPolicy']['indexingMode'] = 'consistent'
```

Определение политики индексации с помощью включаемого пути и пространственного индекса

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

Определение политики индексации с исключенным путем

```python
container["indexingPolicy"] = {
    "indexingMode":"consistent",
    "includedPaths":[{"path":"/*","indexes":[]}],
    "excludedPaths":[{"path":"/name/*"}]
}
```

Добавление составного индекса

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

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения об индексировании см. по следующим ссылкам:

- [Общие сведения об индексировании](index-overview.md)
- [Политика индексирования в Azure Cosmos DB](index-policy.md)
