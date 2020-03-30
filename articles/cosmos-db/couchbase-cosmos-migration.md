---
title: Миграция из CouchBase в Azure Cosmos DB S'L API
description: Пошаговое руководство для перехода от CouchBase к Azure Космос DB S'L API
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: mansha
author: manishmsfte
ms.openlocfilehash: 9713d963978e34ad874dc032676a6e1f14e4657c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77210948"
---
# <a name="migrate-from-couchbase-to-azure-cosmos-db-sql-api"></a>Миграция из CouchBase в Azure Cosmos DB S'L API

Azure Cosmos DB — это масштабируемая, полностью управляемая база данных, распорядительная по всему миру. Он обеспечивает гарантированный доступ к данным с низкой задержкой. Подробнее о DB Azure Cosmos можно узнать в статье [обзора.](introduction.md) В этой статье содержатся инструкции по переносу Java-приложений, подключенных к Couchbase, к учетной записи API в Azure Cosmos DB.

## <a name="differences-in-nomenclature"></a>Различия в номенклатуре

Ниже приведены ключевые функции, которые работают по-разному в Azure Cosmos DB по сравнению с Couchbase:

|   Couchbase     |   Azure Cosmos DB   |
| ---------------|-------------------|
|Сервер Couchbase| Учетная запись       |
|Ведро           | База данных      |
|Ведро           | Контейнер/сбор |
|Документ JSON    | Пункт / Документ |

## <a name="key-differences"></a>Основные отличия

* Azure Cosmos DB имеет поле "ID" в документе, в то время как Couchbase имеет идентификатор как часть ведра. Поле "ID" уникально по всему разделу.

* Azure Cosmos DB масштабируется с помощью метода раздела или осколков. Это означает, что он делит данные на несколько осколков/разделов. Эти разделы/осколки создаются на основе свойства ключа раздела, которое вы предоставляете. Вы можете выбрать ключ раздела для оптимизации чтения, а также писать операции или читать / писать оптимизированы тоже. Чтобы узнать больше, смотрите [раздельную](./partition-data.md) статью.

* В Azure Cosmos DB иерархия верхнего уровня не обязана обозначать коллекцию, поскольку имя коллекции уже существует. Эта функция значительно упрощает структуру JSON. Ниже приводится пример, который показывает различия в модели данных между Couchbase и Azure Cosmos DB:

   **Couchbase**: Документ ID - "99FF444"

    ```json
    {
      "TravelDocument":
      {
       "Country":"India",
      "Validity" : "2022-09-01",
        "Person":
        {
          "Name": "Manish",
          "Address": "AB Road, City-z"
        },
        "Visas":
        [
          {
          "Country":"India",
          "Type":"Multi-Entry",
          "Validity":"2022-09-01"
          },
          {
          "Country":"US",
          "Type":"Single-Entry",
          "Validity":"2022-08-01"
          }
        ]
      }
    }
   ```

   **Azure Cosmos DB**: Ссылка на "ID" в документе, как показано ниже

    ```json
    {
      "id" : "99FF4444",
    
      "Country":"India",
       "Validity" : "2022-09-01",
        "Person":
        {
          "Name": "Manish",
          "Address": "AB Road, City-z"
        },
        "Visas":
        [
          {
          "Country":"India",
          "Type":"Multi-Entry",
          "Validity":"2022-09-01"
          },
          {
          "Country":"US",
          "Type":"Single-Entry",
          "Validity":"2022-08-01"
          }
        ]
      }
    
    ```
         
## <a name="java-sdk-support"></a>Поддержка Java SDK

Azure Cosmos DB имеет следующие SDK для поддержки различных инфраструктур Java:

* Async SDK
* Весенняя загрузка SDK

В следующих разделах описывается, когда использовать каждый из этих SDK. Рассмотрим пример, где у нас есть три типа рабочих нагрузок:

## <a name="couchbase-as-document-repository--spring-data-based-custom-queries"></a>Couchbase как репозиторий документов & пружинные пользовательские запросы на основе данных

Если перемиграющая рабочая нагрузка основана на SDK на основе Spring Boot, вы можете использовать следующие действия:

1. Добавление родительского файла POM.xml:

   ```java
   <parent>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-parent</artifactId>
      <version>2.1.5.RELEASE</version>
      <relativePath/>
   </parent>
   ```

1. Добавьте свойства в файл POM.xml:

   ```java
   <azure.version>2.1.6</azure.version>
   ```

1. Добавление зависимостей в файл POM.xml:

   ```java
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
       <version>2.1.6</version>
   </dependency>
   ```

1. Добавление свойств приложения под ресурсы и укажите следующее. Убедитесь в том, чтобы заменить параметры URL, ключа и названия базы данных:

   ```java
      azure.cosmosdb.uri=<your-cosmosDB-URL>
      azure.cosmosdb.key=<your-cosmosDB-key>
      azure.cosmosdb.database=<your-cosmosDB-dbName>
   ```

1. Определите название коллекции в модели. Вы также можете указать дополнительные аннотации. Например, идентификатор, ключ раздела для обозначения:

   ```java
   @Document(collection = "mycollection")
       public class User {
           @id
           private String id;
           private String firstName;
           @PartitionKey
           private String lastName;
       }
   ```

Ниже приведены фрагменты кода для операций CRUD:

### <a name="insert-and-update-operations"></a>Вставка и обновление операций

Где *_repo* является объектом репозитория, а *док* - объектом класса POJO. Вы можете `.save` использовать для вставки или upsert (если документ с указанным ID найдено). На следующем фрагменте кода показано, как вставить или обновить объект документа:

```_repo.save(doc);```

### <a name="delete-operation"></a>Операция удаления

Рассмотрим следующий фрагмент кода, где объект doc будет иметь идентификатор и ключ раздела, обязательный для обнаружения и удаления объекта:

```_repo.delete(doc);```

### <a name="read-operation"></a>Операция чтения.

Вы можете прочитать документ с указанием или без указания ключа раздела. Если не указано ключ раздела, то он рассматривается как запрос перекрестной раздел. Рассмотрим следующие образцы кода, первый будет выполнять операцию с использованием поля ключа ID и раздела. Второй пример использует обычное поле & без указания поля ключа раздела.

* ```_repo.findByIdAndName(objDoc.getId(),objDoc.getName());```
* ```_repo.findAllByStatus(objDoc.getStatus());```

Вот и все, теперь вы можете использовать приложение с Azure Cosmos DB. Полный образец кода для примера, описанного в этом документе, доступен в репо [CouchbaseToCosmosDB-SpringCosmos](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/SpringCosmos) GitHub.

## <a name="couchbase-as-a-document-repository--using-n1ql-queries"></a>Couchbase как репозиторий документов & с использованием запросов N1'L

Запросы N1'L — это способ определения запросов в Couchbase.

|Запрос N1'L | Запрос Azure CosmosDB|
|-------------------|-------------------|
|SELECT META`TravelDocument`().id `TravelDocument`AS id, ." От `TravelDocument` ГДЕ `_type` "com.xx.xx.xx.xxxx" и страны " Индия" и любой м в визах SATISFIES m.type "Multi-Entry" и m.Country IN "Индия", Бутан" ` Validity`   | SELECT c.id,c FROM c JOIN m in c.country='India' WHERE c._type = " com.xx.xx.xx.xxx.xxx.xxxx" and c.country = 'India' and m.type = 'Multi-Entry' and m.Country IN ('India', 'Bhutan') ORDER BY c.Validity DESC OFFSET 0 LIMIT 25 |

Вы можете заметить следующие изменения в своих запросах N1'L:

* Вам не нужно использовать ключевое слово META или ссылаться на документ первого уровня. Вместо этого вы можете создать свою собственную ссылку на контейнер. В этом примере мы рассматривали его как "c" (это может быть что угодно). Эта ссылка используется в качестве префикса для всех полей первого уровня. Например, c.id, c.country и т.д.

* Вместо "ANY" теперь вы можете сделать соединение на субдокумент и передать его с выделенным псевдонимом, таких как "м". После того как вы создали псевдоним для субдокумента нужно использовать псевдоним. Например, м.Страна.

* Последовательность OFFSET отличается в запросе Azure Cosmos DB, сначала нужно указать OFFSET, затем LIMIT. Рекомендуется не использовать Spring Data SDK, если вы используете максимальные пользовательские определенные запросы, поскольку при передаче запроса в Azure Cosmos DB могут возникнуть ненужные накладные расходы. Вместо этого у нас есть прямой Async Java SDK, который может быть использован гораздо эффективно в этом случае.

### <a name="read-operation"></a>Прочитано операцию

Используйте Async Java SDK со следующими шагами:

1. Наверстойная настройка следующей зависимости на файл POM.xml:

   ```java
   <!-- https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb -->
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmos</artifactId>
       <version>3.0.0</version>
   </dependency>
   ```

1. Создайте объект соединения для Azure Cosmos `ConnectionBuilder` DB, используя метод, показанный в следующем примере. Убедитесь, что вы положили эту декларацию в пупу так, что следующий код должен быть выполнен только один раз:

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
    
   if(client==null)
    client= CosmosClient.builder()
        .endpoint(Host)//(Host, MasterKey, dbName, collName).Builder()
        .connectionPolicy(cp)
        .key(MasterKey)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();   
   
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

1. Для выполнения запроса необходимо выполнить следующий фрагмент кода:

   ```java
   Flux<FeedResponse<CosmosItemProperties>> objFlux= container.queryItems(query, fo);
   ```

Теперь, с помощью выше метода вы можете пройти несколько запросов и выполнить без каких-либо хлопот. В случае, если у вас есть требование выполнить один большой запрос, который может быть разделен на несколько запросов, то попробуйте следующий фрагмент кода вместо предыдущего:

```java
for(SqlQuerySpec query:queries)
{
    objFlux= container.queryItems(query, fo);
    objFlux .publishOn(Schedulers.elastic())
            .subscribe(feedResponse->
                {
                    if(feedResponse.results().size()>0)
                    {
                        _docs.addAll(feedResponse.results());
                    }
                
                },
                Throwable::printStackTrace,latch::countDown);
    lstFlux.add(objFlux);
}
                        
        Flux.merge(lstFlux);
        latch.await();
}
```

С предыдущим кодом можно параллельно запускать запросы и увеличивать распределенные выполнения для оптимизации. Далее можно также запустить операции вставки и обновления:

### <a name="insert-operation"></a>Операция вставки

Чтобы вставить документ, запустите следующий код:

```java 
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

Затем подпишитесь на Mono как:

```java
CountDownLatch latch=new CountDownLatch(1);
objMono .subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
            if(resourceResponse.statusCode()!=successStatus)
                {
                    throw new RuntimeException(resourceResponse.toString());
                }
            },
        Throwable::printStackTrace,latch::countDown);
latch.await();              
```

### <a name="upsert-operation"></a>Операция Апсерта

Операция Upsert требует указания документа, который необходимо обновить. Чтобы получить полный документ, можно использовать фрагмент, упомянутый в статье считывания, а затем изменить требуемое поле (ы). Следующий фрагмент кода upserts документ:

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
Затем подпишитесь на моно. Ссылайтесь на фрагмент моноподписки в операции вставки.

### <a name="delete-operation"></a>Операция удаления

После фрагмент ассока будет делать удаление операции:

```java     
CosmosItem objItem= container.getItem(doc.Id, doc.Tenant);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

Затем подписаться на моно, обратитесь моно подписки фрагмент в вставке операции. Полный образец кода доступен в репо [CouchbaseToCosmosDB-AsyncInSpring](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncInSpring) GitHub.

## <a name="couchbase-as-a-keyvalue-pair"></a>Couchbase как пара ключей/значений

Это простой тип рабочей нагрузки, в котором можно выполнять поиск вместо запросов. Используйте следующие шаги для пар ключей/значений:

1. Рассмотрите возможность проведения операции "/ID" в качестве основного ключа, который гарантирует, что вы можете выполнить операцию поиска непосредственно в определенном разделе. Создайте коллекцию и укажите "/ID" в качестве ключа раздела.

1. Полностью выключите индексирование. Поскольку вы будете выполнять операции поиска, нет смысла проводить индексацию накладных расходов. Чтобы отключить индексирование, впишитесь на портал Azure, goto Azure Cosmos DB Account. Откройте **Data Explorer,** выберите **базу данных** и **контейнер.** Откройте вкладку **«Настройки шкалы &** и выберите **политику индексирования.** В настоящее время политика индексации выглядит следующим образом:
    
   ```json
   {
       "indexingMode": "consistent",
       "includedPaths": 
       [
           {
            "path": "/*",
            "indexes": 
             [
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
       "excludedPaths": 
       [
         {
             "path": "/path/to/single/excluded/property/?"
         },
         {
             "path": "/path/to/root/of/multiple/excluded/properties/*"
         }
      ]
   }
   ````

   Замените вышеупомянутую политику индексации следующей политикой:

   ```json
   {
       "indexingMode": "none"
   }
   ```

1. Используйте следующий фрагмент кода для создания объекта соединения. Объект подключения (чтобы @Bean быть помещенным в или сделать его статическим):

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
   
   if(client==null)
    client= CosmosClient.builder()
        .endpoint(Host)//(Host, MasterKey, dbName, collName).Builder()
        .connectionPolicy(cp)
        .key(MasterKey)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();
    
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

Теперь вы можете выполнять операции CRUD следующим образом:

### <a name="read-operation"></a>Прочитано операцию

Чтобы прочитать элемент, используйте следующий фрагмент:

```java        
CosmosItemRequestOptions ro=new CosmosItemRequestOptions();
ro.partitionKey(new PartitionKey(documentId));
CountDownLatch latch=new CountDownLatch(1);
        
var objCosmosItem= container.getItem(documentId, documentId);
Mono<CosmosItemResponse> objMono = objCosmosItem.read(ro);
objMono .subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
            if(resourceResponse.item()!=null)
            {
                doc= resourceResponse.properties().toObject(UserModel.class);
            }
        },
        Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="insert-operation"></a>Операция вставки

Чтобы вставить элемент, можно выполнить следующий код:

```java 
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

Затем подпишитесь на моно, как:

```java
CountDownLatch latch=new CountDownLatch(1);
objMono.subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
            if(resourceResponse.statusCode()!=successStatus)
                {
                    throw new RuntimeException(resourceResponse.toString());
                }
            },
        Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="upsert-operation"></a>Операция Апсерта

Чтобы обновить значение элемента, перевеши фрагмент кода ниже:

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
Затем подписаться на моно, обратитесь моно подписки фрагмент в вставке операции.

### <a name="delete-operation"></a>Операция удаления

Используйте следующий фрагмент для выполнения операции удаления:

```java     
CosmosItem objItem= container.getItem(id, id);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

Затем подписаться на моно, обратитесь моно подписки фрагмент в вставке операции. Полный образец кода доступен в репо [CouchbaseToCosmosDB-AsyncKeyValue](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncKeyValue) GitHub.

## <a name="data-migration"></a>Перенос данных

Существует два способа переноса данных.

* **Используйте фабрику данных Azure:** Это наиболее рекомендуемый метод переноса данных. Нафоторуйте источник как Couchbase и погрузитесь в Azure Cosmos DB S'L API, см. статью [разъема](../data-factory/connector-azure-cosmos-db.md) data Factory Azure Cosmos DB для детальных шагов.

* **Используйте инструмент импорта данных Azure Cosmos DB:** Этот параметр рекомендуется мигрировать с использованием VMs с меньшим объемом данных. Подробные шаги можно узнать из статьи [импортера данных.](./import-data.md)

## <a name="next-steps"></a>Next Steps

* Чтобы сделать тестирование производительности, [см. Производительность и масштаб тестирования с Azure Космос DB](./performance-testing.md) статьи.
* Оптимизируйте код, смотрите советы по производительности для статьи [Azure Cosmos DB.](./performance-tips-async-java.md)
* Исследуйте Java Async V3 SDK, [SDK ссылка](https://github.com/Azure/azure-cosmosdb-java/tree/v3) GitHub репо.
