---
title: Миграция с CouchBase на Azure Cosmos DB API SQL
description: Пошаговое руководство по переходу с CouchBase на Azure Cosmos DB API SQL
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: mansha
author: manishmsfte
ms.openlocfilehash: 9713d963978e34ad874dc032676a6e1f14e4657c
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210948"
---
# <a name="migrate-from-couchbase-to-azure-cosmos-db-sql-api"></a>Миграция с CouchBase на Azure Cosmos DB API SQL

Azure Cosmos DB — это масштабируемая глобально распределенная, полностью управляемая база данных. Он обеспечивает гарантированный доступ к данным с низкой задержкой. Дополнительные сведения о Azure Cosmos DB см. в [обзорной](introduction.md) статье. В этой статье приведены инструкции по переносу приложений Java, подключенных к Couchbase, в учетную запись API SQL в Azure Cosmos DB.

## <a name="differences-in-nomenclature"></a>Различия в номенклатуре

Ниже приведены основные функции, которые работают по-разному в Azure Cosmos DB по сравнению с Couchbase.

|   Couchbase     |   Azure Cosmos DB   |
| ---------------|-------------------|
|Сервер Couchbase| Учетная запись       |
|Период           | Database      |
|Период           | Контейнер или коллекция |
|Документ JSON    | Элемент/документ |

## <a name="key-differences"></a>Основные отличия

* Azure Cosmos DB содержит поле ID в документе, тогда как Couchbase имеет идентификатор в составе контейнера. Поле "ID" уникально в пределах секции.

* Azure Cosmos DB масштабируется с помощью приема секционирования или сегментирования. Это означает, что данные разбиваются на несколько сегментов или секций. Эти секции и сегменты создаются на основе предоставляемого свойства ключа секции. Вы можете выбрать ключ секции, чтобы оптимизировать чтение, а также оптимизированные для чтения и записи операции. Дополнительные сведения см. в статье о [секционировании](./partition-data.md) .

* В Azure Cosmos DB не требуется, чтобы иерархия верхнего уровня обстоит коллекцию, так как имя коллекции уже существует. Эта функция значительно упрощает структуру JSON. Ниже приведен пример, демонстрирующий различия в модели данных между Couchbase и Azure Cosmos DB:

   **Couchbase**: Document ID = "99FF4444"

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

   **Azure Cosmos DB**: в документе содержится ссылка "ID", как показано ниже.

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
         
## <a name="java-sdk-support"></a>Поддержка пакета SDK для Java

Azure Cosmos DB содержит следующие пакеты SDK для поддержки различных платформ Java:

* Асинхронный пакет SDK
* Пакет SDK для пружинной загрузки

В следующих разделах описывается, когда следует использовать каждый из этих пакетов SDK. Рассмотрим пример, в котором у нас есть три типа рабочих нагрузок:

## <a name="couchbase-as-document-repository--spring-data-based-custom-queries"></a>Couchbase в репозитории документов & настраиваемых запросов на основе данных с пружинными данными

Если Переносимая Рабочая нагрузка основана на пакете SDK на основе пружинной загрузки, можно выполнить следующие действия.

1. Добавьте родительский элемент в файл POM. XML:

   ```java
   <parent>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-parent</artifactId>
      <version>2.1.5.RELEASE</version>
      <relativePath/>
   </parent>
   ```

1. Добавьте свойства в файл POM. XML:

   ```java
   <azure.version>2.1.6</azure.version>
   ```

1. Добавьте зависимости в файл POM. XML:

   ```java
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
       <version>2.1.6</version>
   </dependency>
   ```

1. Добавьте свойства приложения в раздел ресурсы и укажите следующие параметры. Обязательно замените параметры URL-адреса, ключа и имени базы данных:

   ```java
      azure.cosmosdb.uri=<your-cosmosDB-URL>
      azure.cosmosdb.key=<your-cosmosDB-key>
      azure.cosmosdb.database=<your-cosmosDB-dbName>
   ```

1. Определите имя коллекции в модели. Можно также указать дополнительные заметки. Например, ID, ключ секции для их явного обозначения:

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

Ниже приведены фрагменты кода для операций CRUD.

### <a name="insert-and-update-operations"></a>Операции вставки и обновления

Где *_repo* — объект репозитория, а *doc* — объект класса POJO. `.save` можно использовать для вставки или Upsert (если найден документ с указанным ИДЕНТИФИКАТОРом). В следующем фрагменте кода показано, как вставить или обновить объект doc:

```_repo.save(doc);```

### <a name="delete-operation"></a>Операция удаления

Рассмотрим следующий фрагмент кода, в котором объект doc будет иметь идентификатор и ключ секции, обязательные для нахождение и удаления объекта:

```_repo.delete(doc);```

### <a name="read-operation"></a>Операция чтения.

Можно прочитать документ с указанием ключа секции или без него. Если ключ секции не указан, он рассматривается как запрос между секциями. Рассмотрим приведенные ниже примеры кода, первая из которых будет выполнять операцию с использованием идентификатора и поля ключа секции. Во втором примере используется обычное поле & без указания поля ключа секции.

* ```_repo.findByIdAndName(objDoc.getId(),objDoc.getName());```
* ```_repo.findAllByStatus(objDoc.getStatus());```

Теперь вы можете использовать приложение с Azure Cosmos DB. Полный пример кода для примера, описанного в этом документе, доступен в репозитории GitHub [каучбасетокосмосдб-спрингкосмос](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/SpringCosmos) .

## <a name="couchbase-as-a-document-repository--using-n1ql-queries"></a>Couchbase в репозитории документов & с помощью запросов N1QL

Запросы N1QL — это способ определения запросов в Couchbase.

|Запрос N1QL | Запрос Azure CosmosDB|
|-------------------|-------------------|
|Выберите META (`TravelDocument`). идентификатор в виде идентификатора, `TravelDocument`. * из `TravelDocument`, где `_type` = "com. XX. XX. XX. xxx. xxx. XXXX" и Country = "Индия", а любой m в Висас соответствует m. Type = = "Multi-Entry" и m. Country в ["Индия", Бутан "] ORDER BY ` Validity` DESC LIMIT 25 OFFSET 0   | Выберите c. ID, c из c JOIN m в c. Country = "Индия" WHERE c. _type = "com. XX. XX. XX. xxx. xxx. XXXX" и c. Country = "Индия" и m. Type = "Multi-Entry" и m. Country в ("Индии", "Бутан") порядок по c. смещение DESC OFFSET 0 |

Вы можете заметить следующие изменения в запросах N1QL:

* Не нужно использовать ключевое слово META или ссылаться на документ первого уровня. Вместо этого можно создать собственную ссылку на контейнер. В этом примере мы рассматривали его как "c" (это может быть любой другой). Эта ссылка используется в качестве префикса для всех полей первого уровня. Пример fr, c.id, c. Country и т. д.

* Вместо "ANY" теперь можно выполнить соединение во вложенном документе и использовать его с выделенным псевдонимом, например "m". После создания псевдонима для вложенного документа необходимо использовать псевдоним. Например, m. Country.

* Последовательность смещения отличается в запросе Azure Cosmos DB. сначала необходимо указать OFFSET, а не LIMIT. Не рекомендуется использовать пакет SDK для пружинных данных, если вы используете максимальное количество пользовательских запросов, так как при передаче запроса в Azure Cosmos DB может потребоваться ненужная нагрузка на клиентской стороне. Вместо этого у нас есть прямой асинхронный пакет SDK для Java, который в данном случае можно использовать намного эффективнее.

### <a name="read-operation"></a>Операция чтения

Используйте пакет SDK для асинхронной разработки Java, выполнив следующие действия.

1. Настройте следующую зависимость на файл POM. XML:

   ```java
   <!-- https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb -->
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmos</artifactId>
       <version>3.0.0</version>
   </dependency>
   ```

1. Создайте объект соединения для Azure Cosmos DB с помощью метода `ConnectionBuilder`, как показано в следующем примере. Убедитесь, что вы поместили это объявление в Bean таким образом, что следующий код должен быть выполнен только один раз:

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

1. Чтобы выполнить запрос, необходимо выполнить следующий фрагмент кода:

   ```java
   Flux<FeedResponse<CosmosItemProperties>> objFlux= container.queryItems(query, fo);
   ```

Теперь с помощью описанного выше метода можно передать несколько запросов и выполнить без каких бы то ни было трудностей. Если у вас есть требование выполнить один большой запрос, который можно разделить на несколько запросов, попробуйте выполнить следующий фрагмент кода, а не предыдущий.

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

С помощью предыдущего кода можно параллельно выполнять запросы и повышать эффективность распределенных выполнений. Кроме того, можно выполнять операции вставки и обновления:

### <a name="insert-operation"></a>Операция вставки

Чтобы вставить документ, выполните следующий код:

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

### <a name="upsert-operation"></a>Операция Upsert

Для операции Upsert необходимо указать документ, который необходимо обновить. Чтобы получить полный документ, можно использовать фрагмент, упомянутый под заголовком операция чтения, а затем изменить необходимые поля. Следующий фрагмент кода операции Upsert документ:

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
Затем подпишитесь на Mono. Обратитесь к фрагменту подписки Mono в операции вставки.

### <a name="delete-operation"></a>Операция удаления

Следующий фрагмент кода выполняет операцию удаления:

```java     
CosmosItem objItem= container.getItem(doc.Id, doc.Tenant);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

Затем подпишитесь на моно, обратитесь к фрагменту подписки Mono в операции вставки. Полный пример кода доступен в репозитории GitHub [каучбасетокосмосдб-асинЦинспринг](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncInSpring) .

## <a name="couchbase-as-a-keyvalue-pair"></a>Couchbase в качестве пары "ключ-значение"

Это простой тип рабочей нагрузки, в котором можно выполнять поиск вместо запросов. Для пар "ключ-значение" выполните следующие действия.

1. Рекомендуется использовать "/ID" в качестве первичного ключа, что гарантирует возможность выполнения операции поиска непосредственно в определенной секции. Создайте коллекцию и укажите в качестве ключа секции "/ID".

1. Полностью выключите индексирование. Так как вы будете выполнять операции уточняющего запроса, нет никакой точки для накладных расходов на индексирование. Чтобы отключить индексирование, войдите в портал Azure, goto Azure Cosmos DB Account. Откройте **Обозреватель данных**, выберите свою **базу данных** и **контейнер**. Откройте вкладку **параметры & масштабирования** и выберите **политику индексирования**. Текущая политика индексации выглядит следующим образом:
    
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

   Замените указанную выше политику индексирования следующей:

   ```json
   {
       "indexingMode": "none"
   }
   ```

1. Используйте следующий фрагмент кода, чтобы создать объект соединения. Объект соединения (который будет помещен в @Bean или сделать его статическим):

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

Теперь можно выполнить операции CRUD следующим образом:

### <a name="read-operation"></a>Операция чтения

Чтобы прочитать элемент, используйте следующий фрагмент кода:

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

Затем подпишитесь на Mono как:

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

### <a name="upsert-operation"></a>Операция Upsert

Чтобы обновить значение элемента, обратитесь к фрагменту кода ниже:

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
Затем подпишитесь на моно, обратитесь к фрагменту подписки Mono в операции вставки.

### <a name="delete-operation"></a>Операция удаления

Используйте следующий фрагмент кода для выполнения операции удаления:

```java     
CosmosItem objItem= container.getItem(id, id);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

Затем подпишитесь на моно, обратитесь к фрагменту подписки Mono в операции вставки. Полный пример кода доступен в репозитории GitHub [каучбасетокосмосдб-асинккэйвалуе](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncKeyValue) .

## <a name="data-migration"></a>Перенос данных

Существует два способа миграции данных.

* **Используйте фабрику данных Azure.** Это наиболее предпочтительный способ переноса данных. Настройка источника в качестве Couchbase и приемника в качестве Azure Cosmos DB API SQL. подробные инструкции см. в статье о [соединителе фабрики данных Azure Cosmos DB](../data-factory/connector-azure-cosmos-db.md) .

* **Используйте Azure Cosmos DB средство импорта данных.** Этот параметр рекомендуется использовать для переноса с использованием виртуальных машин с меньшим объемом данных. Подробные инструкции см. в статье об [импортере данных](./import-data.md) .

## <a name="next-steps"></a>Следующие шаги

* Сведения о тестировании производительности см. в статье [Тестирование производительности и масштабирования с помощью Azure Cosmos DB](./performance-testing.md) статьи.
* Чтобы оптимизировать код, см. статью [Советы по повышению производительности Azure Cosmos DB](./performance-tips-async-java.md) .
* Изучите пакет SDK для Java Async v3, [ссылку на пакет SDK](https://github.com/Azure/azure-cosmosdb-java/tree/v3) для репозитория GitHub.
