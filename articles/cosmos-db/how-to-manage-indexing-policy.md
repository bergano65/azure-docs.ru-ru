---
title: Управление политиками индексирования в Azure Cosmos DB
description: Узнайте, как управлять политиками индексирования в Azure Cosmos DB.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: thweiss
ms.openlocfilehash: a6c1ec6d58939336fb8a982e3ab1b9be20d4e0a5
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172153"
---
# <a name="manage-indexing-policies-in-azure-cosmos-db"></a>Управление политиками индексирования в Azure Cosmos DB

В Azure Cosmos DB данные индексируются с помощью приведенных ниже [политик индексирования](index-policy.md), определенных для каждого контейнера. Политика индексирования по умолчанию, задаваемая для только что созданных контейнеров, применяет диапазонные индексы для любых строк или чисел и пространственные индексы — для любых объектов GeoJSON типа Point. Эта политика может быть переопределена:

- на портале Azure;
- с помощью Azure CLI;
- с помощью одного из пакетов SDK.

[Обновление политики индексирования](index-policy.md#modifying-the-indexing-policy) приводит к преобразованию индекса. Ход выполнения этого преобразования можно отслеживать с помощью пакетов SDK.

> [!NOTE]
> В процессе обновления пакета SDK и портала мы развиваем политику индексирования в соответствии с новым макетом индекса, который мы развернули для новых контейнеров. С помощью этого нового макета все примитивные типы данных индексируются как диапазон с полной точностью (-1). Таким образом, типы индекса и точность больше не предоставляются пользователю. В будущем пользователям необходимо будет просто добавить пути к разделу includedPaths и игнорировать indexKinds и точность. Это изменение не влияет на производительность, и можно продолжать обновление политики индексирования с помощью того же синтаксиса. Вы можете использовать все примеры в имеющейся документации, чтобы обновить политику индексирования.

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

Команда [az cosmosdb collection update](/cli/azure/cosmosdb/collection#az-cosmosdb-collection-update) в Azure CLI позволяет заменить определение JSON политики индексирования контейнера.

```azurecli-interactive
az cosmosdb collection update \
    --resource-group-name $resourceGroupName \
    --name $accountName \
    --db-name $databaseName \
    --collection-name $containerName \
    --indexing-policy "{\"indexingMode\": \"consistent\", \"includedPaths\": [{ \"path\": \"/*\", \"indexes\": [{ \"dataType\": \"String\", \"kind\": \"Range\" }] }], \"excludedPaths\": [{ \"path\": \"/headquarters/employees/?\" } ]}"
```

## <a name="use-the-net-sdk-v2"></a>Использование пакета SDK для .NET версии 2

Объект `DocumentCollection` из [пакета SDK для .NET версии 2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) (его использование описано в [этом кратком руководстве](create-sql-api-dotnet.md)) предоставляет свойство `IndexingPolicy`, которое позволяет изменить `IndexingMode` и добавить или удалить `IncludedPaths` и `ExcludedPaths`.

Получение сведений о контейнере

```csharp
ResourceResponse<DocumentCollection> containerResponse = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"));
```

Установить режим индексирования "последовательный"

```csharp
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
```

Добавление включаемого пути

```csharp
containerResponse.Resource.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/age/*" });
```

Добавление исключенного пути

```csharp
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/name/*" });
```

Добавление пространственного индекса

```csharp
containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(new SpatialSpec() { Path = "/locations/*", SpatialTypes = new Collection<SpatialType>() { SpatialType.Point } } );
```

Добавление составного индекса

```csharp
containerResponse.Resource.IndexingPolicy.CompositeIndexes.Add(new Collection<CompositePath> {new CompositePath() { Path = "/name", Order = CompositePathSortOrder.Ascending }, new CompositePath() { Path = "/age", Order = CompositePathSortOrder.Descending }});
```

Обновление контейнера с изменениями

```csharp
await client.ReplaceDocumentCollectionAsync(containerResponse.Resource);
```

Чтобы отслеживать ход выполнения преобразования индекса, передайте объект `RequestOptions`, который задает для свойства `PopulateQuotaInfo` значение `true`.

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> container = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"), new RequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = container.IndexTransformationProgress;
```

## <a name="use-the-java-sdk"></a>Использование пакета SDK для Java

Объект `DocumentCollection` из [пакета SDK для Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) (его использование описано в [этом кратком руководстве](create-sql-api-java.md)) предоставляет методы `getIndexingPolicy()` и `setIndexingPolicy()`. Объект `IndexingPolicy`, которым они управляют, позволяет изменить режим индексирования и добавить или удалить включенные и исключенные пути.

Получение сведений о контейнере

```java
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), null);
containerResponse.subscribe(result -> {
DocumentCollection container = result.getResource();
IndexingPolicy indexingPolicy = container.getIndexingPolicy();
```

Установить режим индексирования "последовательный"

```java
indexingPolicy.setIndexingMode(IndexingMode.Consistent);
```

Добавление включаемого пути

```java
Collection<IncludedPath> includedPaths = new ArrayList<>();
ExcludedPath includedPath = new IncludedPath();
includedPath.setPath("/age/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);
```

Добавление исключенного пути

```java
Collection<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.setPath("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.setExcludedPaths(excludedPaths);
```

Добавление пространственного индекса

```java
Collection<SpatialSpec> spatialIndexes = new ArrayList<SpatialSpec>();
Collection<SpatialType> collectionOfSpatialTypes = new ArrayList<SpatialType>();

SpatialSpec spec = new SpatialSpec();
spec.setPath("/locations/*");
collectionOfSpatialTypes.add(SpatialType.Point);          
spec.setSpatialTypes(collectionOfSpatialTypes);
spatialIndexes.add(spec);

indexingPolicy.setSpatialIndexes(spatialIndexes);

```

Добавление составного индекса

```java
Collection<ArrayList<CompositePath>> compositeIndexes = new ArrayList<>();
ArrayList<CompositePath> compositePaths = new ArrayList<>();

CompositePath nameCompositePath = new CompositePath();
nameCompositePath.setPath("/name/*");
nameCompositePath.setOrder(CompositePathSortOrder.Ascending);

CompositePath ageCompositePath = new CompositePath();
ageCompositePath.setPath("/age/*");
ageCompositePath.setOrder(CompositePathSortOrder.Descending);

compositePaths.add(ageCompositePath);
compositePaths.add(nameCompositePath);

compositeIndexes.add(compositePaths);
indexingPolicy.setCompositeIndexes(compositeIndexes);
```

Обновление контейнера с изменениями

```java
 client.replaceCollection(container, null);
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

## <a name="indexing-policy-examples"></a>Примеры политик индексирования

Ниже приведены примеры политик индексирования в формате JSON, в котором они доступны на портале Azure. Значения параметров можно задать с помощью Azure CLI или любого пакета SDK.

### <a name="opt-out-policy-to-selectively-exclude-some-property-paths"></a>Политика отказа для выборочного исключения некоторых путей к свойствам
```
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    },
                    {
                        "kind": "Spatial",
                        "dataType": "Point"
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
```
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    }
                ]
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
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

Примечание. Обычно рекомендуется использовать политику индексирования **отказа**, чтобы служба Azure Cosmos DB заранее индексировала любое новое свойство, которое может быть добавлено в модель.

### <a name="using-a-spatial-index-on-a-specific-property-path-only"></a>Использование пространственного индекса только для определенного пути к свойству
```
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*",
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
                "path": "/path/to/geojson/property/?",
                "indexes": [
                    {
                        "kind": "Spatial",
                        "dataType": "Point"
                    }
                ]
            }
        ],
        "excludedPaths": []
    }
```

### <a name="excluding-all-property-paths-but-keeping-indexing-active"></a>Исключение всех путей к свойствам, но с активным индексированием

Эту политику можно использовать в ситуациях, когда [функция учета срока жизни (TTL)](time-to-live.md) включена, но дополнительный индекс не требуется (для использования Azure Cosmos DB в качестве хранилища исключительно пар "ключ-значение").
```
    {
        "indexingMode": "consistent",
        "includedPaths": [],
        "excludedPaths": [{
            "path": "/*"
        }]
    }
```

### <a name="no-indexing"></a>Без индексирования
```
    {
        "indexingMode": "none"
    }
```

## <a name="composite-indexing-policy-examples"></a>Примеры политик составного индексирования

Кроме добавления и удаления путей отдельных свойств, вы также можете указать составной индекс. Если вы хотите выполнить запрос, который содержит предложение `ORDER BY` для нескольких свойств, эти свойства должны содержать [составной индекс](index-policy.md#composite-indexes)

### <a name="composite-index-defined-for-name-asc-age-desc"></a>Определенный составной индекс (name asc, age desc):
```
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[  

        ],
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

Этот составной индекс поддерживает следующих два запроса:

Запрос 1:
```sql
    SELECT *
    FROM c
    ORDER BY name asc, age desc    
```

Запрос 2:
```sql
    SELECT *
    FROM c
    ORDER BY name desc, age asc
```

### <a name="composite-index-defined-for-name-asc-age-asc-and-name-asc-age-desc"></a>Определенный составной индекс (name asc, age asc) и (name asc, age desc):

Вы можете определить несколько разных составных индексов в одной и той же политике индексирования. 
```
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[  

        ],
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

### <a name="composite-index-defined-for-name-asc-age-asc"></a>Определенный составной индекс (name asc, age asc):

Порядок указывать необязательно. Если он не указан, по умолчанию используется порядок по возрастанию.
```
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[  

        ],
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

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения об индексировании см. по следующим ссылкам:

- [Общие сведения об индексировании](index-overview.md)
- [Политика индексирования](index-policy.md)
