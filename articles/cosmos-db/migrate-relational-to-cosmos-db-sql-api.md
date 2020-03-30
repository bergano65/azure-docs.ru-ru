---
title: Преобразование от одного до нескольких реляционных данных в Azure Cosmos DB S'L API
description: Узнайте, как обрабатывать сложную миграцию данных для отношений между вами и несколькими в API
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: thvankra
ms.openlocfilehash: 467e9627a2623779bd808ca5aebdf76d8a5eda42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75896640"
---
# <a name="migrate-one-to-few-relational-data-into-azure-cosmos-db-sql-api-account"></a>Преобразование от одного до нескольких реляционных данных в учетную запись API Azure Cosmos DB S'L

Для того, чтобы перейти из соотношения базы данных в Azure Cosmos DB S'L API, для оптимизации может потребоваться внести изменения в модель данных.

Одной из распространенных преобразований является денормализация данных путем встраивания связанных подэлементов в один документ JSON. Ниже приведены некоторые варианты этого использования Azure Data Factory или Azure Databricks. Для получения общих рекомендаций по моделированию данных для Cosmos DB, пожалуйста, просмотрите [моделирование данных в Azure Cosmos DB](modeling-data.md).  

## <a name="example-scenario"></a>Пример сценария

Предположим, что у нас есть следующие две таблицы в нашей базе данных S'L, Заказы и OrderDetails.


![Order Details](./media/migrate-relational-to-cosmos-sql-api/orders.png)

Мы хотим объединить эти отношения «один к несколько» в один документ JSON во время миграции. Для этого мы можем создать запрос T-S'L с помощью "FOR JSON" в качестве ниже:

```sql
SELECT
  o.OrderID,
  o.OrderDate,
  o.FirstName,
  o.LastName,
  o.Address,
  o.City,
  o.State,
  o.PostalCode,
  o.Country,
  o.Phone,
  o.Total,
  (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
FROM Orders o;
```

Результаты этого запроса будут выглядеть следующим образом: 

![Order Details](./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png#lightbox)


В идеале необходимо использовать одну активность копирования Azure Data Factory (ADF) для запроса данных S'L в качестве источника и записи вывода непосредственно в DB Azure Cosmos в качестве соответствующих объектов JSON. В настоящее время невозможно выполнить необходимое преобразование JSON в одном действии копирования. Если мы попытаемся скопировать результаты вышеуказанного запроса в контейнер Azure Cosmos DB S'L API, мы увидим поле OrderDetails как свойство строки нашего документа, а не ожидаемого массива JSON.

Мы можем обойти это текущее ограничение одним из следующих способов:

* **Используйте фабрику данных Azure с двумя копиями:** 
  1. Передайте данные, полученные от JSON, из S'L в текстовый файл в промежуточном месте хранения кабы, и 
  2. Загрузите данные из текстового файла JSON в контейнер в Azure Cosmos DB.

* **Используйте Azure Databricks для чтения с S'L и записывайте в Azure Cosmos DB** - мы представим два варианта здесь.


Рассмотрим эти подходы более подробно:

## <a name="azure-data-factory"></a>Фабрика данных Azure

Хотя мы не можем вставлять OrderDetails в качестве JSON-array в документ назначения Cosmos DB, мы можем обойти эту проблему, используя два отдельных действия Copy.

### <a name="copy-activity-1-sqljsontoblobtext"></a>Копия деятельности #1: SqlJsonToBlobText

Для исходных данных мы используем запрос S'L, чтобы получить набор результатов в виде одного столбца с одним объектом JSON (представляющим Заказ) в строке, используя возможности S'L Server OPENJSON и ДЛЯ JSON PATH:

```sql
SELECT [value] FROM OPENJSON(
  (SELECT
    id = o.OrderID,
    o.OrderDate,
    o.FirstName,
    o.LastName,
    o.Address,
    o.City,
    o.State,
    o.PostalCode,
    o.Country,
    o.Phone,
    o.Total,
    (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
   FROM Orders o FOR JSON PATH)
)
```

![Копия ADF](./media/migrate-relational-to-cosmos-sql-api/adf1.png)


Для поглотителя действия копий SqlJsonToBlobText мы выбираем "Ограниченный текст" и направляем его на конкретную папку в@concatХранилище Azure Blob Storage с динамически сгенерированным уникальным именем файла (например, ' (конвейер(). RunId,'.json').
Так как наш текстовый файл на самом деле не "разграничен", и мы не хотим, чтобы он был разогнан на отдельные столбцы с использованием запятых и хотим сохранить двойные цитаты (), мы установили "Колонка делимитетер" на вкладку ("t") - или другой символ, не происходящий в данных - и "Цитата характер" на "Нет цитаты характер".

![Копия ADF](./media/migrate-relational-to-cosmos-sql-api/adf2.png)

### <a name="copy-activity-2-blobjsontocosmos"></a>Копирование деятельности #2: BlobJsonToCosmos

Затем мы изменяем наш конвейер ADF, добавляя второе действие Copy, которое выглядит в хранилище Azure Blob storage для текстового файла, созданного первым действием. Оно обрабатывает его как источник «JSON» для того чтобы вставить к раковине DB космоса как один документ в JSON-ряд найденный в архиве текста.

![Копия ADF](./media/migrate-relational-to-cosmos-sql-api/adf3.png)

Дополнительно мы также добавляем действие «Удалить» в конвейер, чтобы удалить все предыдущие файлы, оставшиеся в /Заказах/папке перед каждым запуском. Наш конвейер ADF теперь выглядит примерно так:

![Копия ADF](./media/migrate-relational-to-cosmos-sql-api/adf4.png)

После запуска конвейера выше мы видим файл, созданный в нашем промежуточном местоположении Azure Blob Storage, содержащий один JSON-объект в строке:

![Копия ADF](./media/migrate-relational-to-cosmos-sql-api/adf5.png)

Мы также видим документы заказов с правильно встроенными OrderDetails, вставленными в нашу коллекцию Cosmos DB:

![Копия ADF](./media/migrate-relational-to-cosmos-sql-api/adf6.png)


## <a name="azure-databricks"></a>Azure Databricks

Мы также можем использовать Spark в [Azure Databricks](https://azure.microsoft.com/services/databricks/) для копирования данных из нашего источника базы данных s'L в пункт назначения Azure Cosmos DB без создания посреднических текстовых /JSON файлов в Azure Blob Storage. 

> [!NOTE]
> Для ясности и простоты приведенные ниже фрагменты кода содержат пароли фиктивных баз данных, которые явно включат в себя, но всегда следует использовать секреты Azure Databricks.
>

Во-первых, мы создаем и прикрепляем необходимые [разъемы S-L](https://docs.databricks.com/data/data-sources/sql-databases-azure.html) и [библиотеки разъемов Azure Cosmos DB](https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html) к нашему кластеру Azure Databricks. Перезапустите кластер, чтобы убедиться, что библиотеки загружены.

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks1.png)

Далее мы представляем два образца для Scala и Python. 

### <a name="scala"></a>Scala
Здесь мы получаем результаты запроса с помощью запроса «ДЛЯ JSON» в DataFrame:

```scala
// Connect to Azure SQL https://docs.databricks.com/data/data-sources/sql-databases-azure.html
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._
val configSql = Config(Map(
  "url"          -> "xxxx.database.windows.net",
  "databaseName" -> "xxxx",
  "queryCustom"  -> "SELECT o.OrderID, o.OrderDate, o.FirstName, o.LastName, o.Address, o.City, o.State, o.PostalCode, o.Country, o.Phone, o.Total, (SELECT OrderDetailId, ProductId, UnitPrice, Quantity FROM OrderDetails od WHERE od.OrderId = o.OrderId FOR JSON AUTO) as OrderDetails FROM Orders o",
  "user"         -> "xxxx", 
  "password"     -> "xxxx" // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
))
// Create DataFrame from Azure SQL query
val orders = sqlContext.read.sqlDB(configSql)
display(orders)
```

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks2.png)

Далее мы подключаемся к нашей базе данных И РЭБ Cosmos DB:

```scala
// Connect to Cosmos DB https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
val configMap = Map(
  "Endpoint" -> "https://xxxx.documents.azure.com:443/",
  // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
  "Masterkey" -> "xxxx",
  "Database" -> "StoreDatabase",
  "Collection" -> "Orders")
val configCosmos = Config(configMap)
```

Наконец, мы определяем нашу схему и используем from_json для применения DataFrame до сохранения ее в коллекции CosmosDB.

```scala
// Convert DataFrame to proper nested schema
import org.apache.spark.sql.types._
val orderDetailsSchema = ArrayType(StructType(Array(
    StructField("OrderDetailId",IntegerType,true),
    StructField("ProductId",IntegerType,true),
    StructField("UnitPrice",DoubleType,true),
    StructField("Quantity",IntegerType,true)
  )))
val ordersWithSchema = orders.select(
  col("OrderId").cast("string").as("id"),
  col("OrderDate").cast("string"),
  col("FirstName").cast("string"),
  col("LastName").cast("string"),
  col("Address").cast("string"),
  col("City").cast("string"),
  col("State").cast("string"),
  col("PostalCode").cast("string"),
  col("Country").cast("string"),
  col("Phone").cast("string"),
  col("Total").cast("double"),
  from_json(col("OrderDetails"), orderDetailsSchema).as("OrderDetails")
)
display(ordersWithSchema)
// Save nested data to Cosmos DB
CosmosDBSpark.save(ordersWithSchema, configCosmos)
```

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks3.png)


### <a name="python"></a>Python

В качестве альтернативного подхода может потребоваться выполнение преобразований JSON в Spark (если исходная база данных не поддерживает "FOR JSON" или аналогичную операцию), или вы можете использовать параллельные операции для очень большого набора данных. Здесь мы представляем образец PySpark. Начните с настройки соединения исходной базы данных и целевой базы данных в первой ячейке:

```python
import uuid
import pyspark.sql.functions as F
from pyspark.sql.functions import col
from pyspark.sql.types import StringType,DateType,LongType,IntegerType,TimestampType
 
#JDBC connect details for SQL Server database
jdbcHostname = "jdbcHostname"
jdbcDatabase = "OrdersDB"
jdbcUsername = "jdbcUsername"
jdbcPassword = "jdbcPassword"
jdbcPort = "1433"
 
connectionProperties = {
  "user" : jdbcUsername,
  "password" : jdbcPassword,
  "driver" : "com.microsoft.sqlserver.jdbc.SQLServerDriver"
}
jdbcUrl = "jdbc:sqlserver://{0}:{1};database={2};user={3};password={4}".format(jdbcHostname, jdbcPort, jdbcDatabase, jdbcUsername, jdbcPassword)
 
#Connect details for Target Azure Cosmos DB SQL API account
writeConfig = {
    "Endpoint": "Endpoint",
    "Masterkey": "Masterkey",
    "Database": "OrdersDB",
    "Collection": "Orders",
    "Upsert": "true"
}
```

Затем мы запросим исходную базу данных (в данном случае S'L Server) как для записей о заказе, так и для обработки деталей заказа, поместив результаты в кадры данных Spark. Мы также создадим список, содержащий все идентиматы заказов, и пул потоков для параллельных операций:

```python
import json
import ast
import pyspark.sql.functions as F
import uuid
import numpy as np
import pandas as pd
from functools import reduce
from pyspark.sql import SQLContext
from pyspark.sql.types import *
from pyspark.sql import *
from pyspark.sql.functions import exp
from pyspark.sql.functions import col
from pyspark.sql.functions import lit
from pyspark.sql.functions import array
from pyspark.sql.types import *
from multiprocessing.pool import ThreadPool
 
#get all orders
orders = sqlContext.read.jdbc(url=jdbcUrl, table="orders", properties=connectionProperties)
 
#get all order details
orderdetails = sqlContext.read.jdbc(url=jdbcUrl, table="orderdetails", properties=connectionProperties)
 
#get all OrderId values to pass to map function 
orderids = orders.select('OrderId').collect()
 
#create thread pool big enough to process merge of details to orders in parallel
pool = ThreadPool(10)
```

Затем создайте функцию для записи заказов в целевую коллекцию API S'L. Эта функция будет фильтровать все сведения о заказа для идентификатора заказа, преобразовывать их в массив JSON и вставлять массив в документ JSON, который мы напишем в целевой коллекцию API для этого заказа:

```python
def writeOrder(orderid):
  #filter the order on current value passed from map function
  order = orders.filter(orders['OrderId'] == orderid[0])
  
  #set id to be a uuid
  order = order.withColumn("id", lit(str(uuid.uuid1())))
  
  #add details field to order dataframe
  order = order.withColumn("details", lit(''))
  
  #filter order details dataframe to get details we want to merge into the order document
  orderdetailsgroup = orderdetails.filter(orderdetails['OrderId'] == orderid[0])
  
  #convert dataframe to pandas
  orderpandas = order.toPandas()
  
  #convert the order dataframe to json and remove enclosing brackets
  orderjson = orderpandas.to_json(orient='records', force_ascii=False)
  orderjson = orderjson[1:-1] 
  
  #convert orderjson to a dictionaory so we can set the details element with order details later
  orderjsondata = json.loads(orderjson)
  
  
  #convert orderdetailsgroup dataframe to json, but only if details were returned from the earlier filter
  if (orderdetailsgroup.count() !=0):
    #convert orderdetailsgroup to pandas dataframe to work better with json
    orderdetailsgroup = orderdetailsgroup.toPandas()
    
    #convert orderdetailsgroup to json string
    jsonstring = orderdetailsgroup.to_json(orient='records', force_ascii=False)
    
    #convert jsonstring to dictionary to ensure correct encoding and no corrupt records
    jsonstring = json.loads(jsonstring)
    
    #set details json element in orderjsondata to jsonstring which contains orderdetailsgroup - this merges order details into the order 
    orderjsondata['details'] = jsonstring
 
  #convert dictionary to json
  orderjsondata = json.dumps(orderjsondata)
 
  #read the json into spark dataframe
  df = spark.read.json(sc.parallelize([orderjsondata]))
  
  #write the dataframe (this will be a single order record with merged many-to-one order details) to cosmos db using spark the connector
  #https://docs.microsoft.com/azure/cosmos-db/spark-connector
  df.write.format("com.microsoft.azure.cosmosdb.spark").mode("append").options(**writeConfig).save()
```

Наконец, мы назовем выше, используя функцию карты в пуле потоков, чтобы выполнить параллельно, передавая в списке идотов заказов, которые мы создали ранее:

```python
#map order details to orders in parallel using the above function
pool.map(writeOrder, orderids)
```
В любом подходе, в конце, мы должны получить должным образом сохранены встроенные OrderDetails в каждом документе заказа в коллекции Cosmos DB:

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks4.png)

## <a name="next-steps"></a>Дальнейшие действия
* Узнайте о [моделировании данных в Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/modeling-data)
* [Узнайте, как моделировать и перегородки данных на Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example)
