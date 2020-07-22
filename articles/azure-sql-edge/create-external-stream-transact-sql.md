---
title: CREATE EXTERNAL STREAM (Transact-SQL) в SQL Azure для пограничных вычислений (предварительная версия)
description: Сведения об инструкции CREATE EXTERNAL STREAM в SQL Azure для пограничных вычислений (предварительная версия)
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 9e1f672a62ee7687fec9cea96ca03240c893ba95
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84233334"
---
# <a name="create-external-stream-transact-sql"></a>CREATE EXTERNAL STREAM (Transact-SQL)

Объект EXTERNAL STREAM имеет два назначения: для входных и выходных данных. Его можно использовать в качестве входных данных для отправки запросов потоковой передачи данных от служб приема событий, таких как события Azure или центры Интернета вещей, или в качестве выходных данных для расположения места и способа хранения результатов из запроса потоковой передачи.

Вы также можете указать и создать EXTERNAL STREAM в качестве выходных и входных данных для таких служб, как концентратор событий или хранилище BLOB-объектов. Это относится к сценариям цепочки, в которых запрос потоковой передачи сохраняет результаты во внешнем потоке в качестве выходных данных, а другой потоковый запрос считывает результаты из того же внешнего потока в качестве входных данных. 


## <a name="syntax"></a>Синтаксис

```sql
CREATE EXTERNAL STREAM {external_stream_name}  
(column definition [,column definitions ] * ) --Used for input - optional 
WITH  
(  
  DATA_SOURCE = <data_source_name>, 
  LOCATION = <location_name>, 
  EXTERNAL_FILE_FORMAT = <external_file_format_name>, --Used for input - optional 
   
  INPUT_OPTIONS =  
    ‘CONSUMER_GROUP=<consumer_group_name>; 
    ‘TIME_POLICY=<time_policy>; 
    LATE_EVENT_TOLERANCE=<late_event_tolerance_value>; 
    OUT_OF_ORDER_EVENT_TOLERANCE=<out_of_order_tolerance_value> 
     
    /* Edge options */ 
  , 
  OUTPUT_OPTIONS =  
    ‘REJECT_POLICY=<reject_policy>; 
    MINIMUM_ROWS=<row_value>; 
    MAXIMUM_TIME=<time_value_minutes>; 
    PARTITION_KEY_COLUMN=<partition_key_column_name>; 
    PROPERTY_COLUMNS=(); 
    SYSTEM_PROPERTY_COLUMNS=(); 
    PARTITION_KEY=<partition_key_name>; 
    ROW_KEY=<row_key_name>; 
    BATCH_SIZE=<batch_size_value>; 
    MAXIMUM_BATCH_COUNT=<batch_value>;  
    STAGING_AREA=<blob_data_source>’ 
     
    /* Edge options */ TAGS=<tag_column_value> 
); 
```


## <a name="arguments"></a>Аргументы


- [EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql/)
- [EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql/)
- **LOCATION**. Задает имя для фактических данных или расположения в источнике данных. В случае с центром Edge или объектом потока Kafka, LOCATION указывает имя центра Edge или раздел Kafka для чтения или записи.
- **INPUT_OPTIONS**. Указывает параметры в качестве пар "ключ — значение" для таких служб, как концентратор событий и центр Интернета вещей, которые являются входными данными для запросов потоковой передачи.
    - CONSUMER_GROUP. Концентратор событий и центры Интернета вещей ограничивают число читателей в одной группе потребителей (до пяти). Если оставить это поле пустым, будет использоваться группа потребителей $Default.
      - Применяется к концентраторам событий и центрам Интернета вещей.
    - TIME_POLICY. Описывает, следует ли удалять события или настраивать время продолжительности события, если поздние или неупорядоченные события передают свое значение допуска.
      - Применяется к концентраторам событий и центрам Интернета вещей.
    - LATE_EVENT_TOLERANCE. Максимально допустимая задержка времени. Задержка представляет разницу между меткой времени события и системными часами.
      - Применяется к концентраторам событий и центрам Интернета вещей.
    - OUT_OF_ORDER_EVENT_TOLERANCE. После того как события перешли из входных данных в запрос потоковой передачи, они могут поступать не по порядку. Эти события можно принять как есть или приостановить процесс на определенное время, чтобы изменить их порядок.
      - Применяется к концентраторам событий и центрам Интернета вещей.
- **OUTPUT_OPTIONS**. Указывает параметры в качестве пар "ключ — значение" для поддерживаемых служб, которые являются выходными данными для запросов потоковой передачи. 
  - REJECT_POLICY.  DROP | RETRY —указывает политики обработки ошибок данных при возникновении ошибок преобразования данных. 
    - Применяется ко всем поддерживаемым выходным данным. 
  - MINIMUM_ROWS.  
    Минимальное количество строк, необходимых для каждого пакета, записанного в выходные данные. Для Parquet каждый пакет создаст файл. 
    - Применяется ко всем поддерживаемым выходным данным. 
  - MAXIMUM_TIME.  
    Максимальное время ожидания на пакет. По истечении этого времени пакет будет записан в выходные данные, даже если не будет выполнено требование к минимальному числу строк. 
    - Применяется ко всем поддерживаемым выходным данным. 
  - PARTITION_KEY_COLUMN.  
    Столбец, который используется для ключа секции. 
    - Применяется к концентратору событий и разделу служебной шины. 
  - PROPERTY_COLUMNS.  
    Разделенный запятыми список имен выходных столбцов, которые, если их предоставить, будут вложены в сообщения в виде настраиваемых свойств.  
    - Применяется к концентратору событий и разделу или очереди служебной шины. 
  - SYSTEM_PROPERTY_COLUMNS.  
    Коллекция пар "имя — значение" для имен системных свойств и выходных столбцов, которая будет заполнена на основе сообщений служебной шины, в формате JSON. Например: { "MessageId": "column1", "PartitionKey": "column2"}. 
    - Применяется к разделу или очереди служебной шины. 
  - PARTITION_KEY.  
    Имя выходного столбца, содержащего ключ раздела. Ключ раздела — это уникальный идентификатор раздела в пределах конкретной таблицы, являющийся первой частью первичного ключа сущности. Это строковое значение размером до 1 КБ. 
    - Применяется к Хранилищу таблиц и Функции Azure. 
  - ROW_KEY.  
    Имя выходного столбца, содержащего ключ строки. Ключ строки — это уникальный идентификатор сущности внутри конкретного раздела. Он является второй частью первичного ключа сущности. Ключ строки — это строковое значение размером до 1 КБ. 
    - Применяется к Хранилищу таблиц и Функции Azure. 
  - BATCH_SIZE.  
    Представляет количество транзакций для хранилища таблиц, в которых максимальное число строк может доходить до 100. Для Функции Azure представляет размер пакета в байтах, отправляемого в функцию за один вызов. Значение по умолчанию — 256 КБ. 
    - Применяется к Хранилищу таблиц и Функции Azure. 
  - MAXIMUM_BATCH_COUNT.  
    Максимальное количество событий, отправленных в функцию, на вызов для Функции Azure. Значение по умолчанию — 100. Для Базы данных SQL представляет максимальное число записей, отправленных с каждой транзакцией массовой вставки. По умолчанию — 10 000. 
    - Применяется к Базе данных SQL и Функции Azure. 
  - STAGING_AREA. Объект EXTERNAL DATA SOURCE в хранилище BLOB-объектов. Промежуточная область для приема данных с высокой пропускной способностью в хранилище данных SQL. 
    - Применимо к хранилищу данных SQL


## <a name="examples"></a>Примеры

### <a name="example-1---edgehub"></a>Пример 1. EdgeHub

Тип: Вход или выход<br>
Параметры
- Вход или выход
  - Псевдоним 
  - Формат сериализации событий 
  - Кодирование 
- Только для входных данных: 
  - Тип сжатия событий 

Синтаксис:

```sql
CREATE EXTERNAL DATA SOURCE MyEdgeHub 
WITH  
(      
  LOCATION = 'edgehub://'       
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat  
WITH (  
   FORMAT_TYPE = 'JSON', 
); 
 
CREATE EXTERNAL STREAM Stream_A  
WITH    
(   
   DATA_SOURCE = MyEdgeHub, 
   EXTERNAL_FILE_FORMAT = myFileFormat, 
   LOCATION = ‘<mytopicname>’, 
   OUTPUT_OPTIONS =   
     ‘REJECT_TYPE: Drop’ 
);
```


### <a name="example-2---azure-sql-database-azure-sql-edge-sql-server"></a>Пример 2. База данных SQL Azure, SQL Azure для пограничных вычислений, SQL Server

Тип: Выходные данные<br>
Параметры
- Псевдоним выходных данных  
- База данных (необходимо для Базы данных SQL). 
- Сервер (необходимо для Базы данных SQL). 
- Имя пользователя (необходимо для Базы данных SQL). 
- Пароль (необходимо для Базы данных SQL). 
- Таблица 
- Объединяет все входные секции в одну запись или наследует схему секционирования предыдущего шага запроса или входных данных (требуется для Базы данных SQL). 
- Максимальное количество пакетов 

Синтаксис:

```sql
CREATE DATABASE SCOPED CREDENTIAL SQLCredName 
WITH IDENTITY = '<user>’, 
SECRET = '<password>'; 
 
-- Azure SQL Database 
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = ' <my_server_name>.database.windows.net’, 
  CREDENTIAL = SQLCredName 
); 
 
--SQL Server or Azure SQL Edge
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = ' <sqlserver://<ipaddress>,<port>’, 
  CREDENTIAL = SQLCredName 
); 
 
--SQL Database/Edge 
CREATE EXTERNAL STREAM Stream_A 
WITH   
(  
    DATA_SOURCE = MyTargetSQLTable, 
    LOCATION = ‘<DatabaseName>.<SchemaName>.<TableName>’ 
   --Note: If table is container in the database, <TableName> should be sufficient 
   --Note: Do not need external file format in this case 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    OUTPUT_OPTIONS =  
      ‘REJECT_TYPE: Drop 
); 
```

### <a name="example-3---kafka"></a>Пример 3. Kafka

Тип: Входные данные<br>
Параметры

- Сервер начальной загрузки Kafka 
- Имя раздела Kafka 
- Количество исходных секций 

Синтаксис:

```sql
CREATE EXTERNAL DATA SOURCE MyKafka_tweets 
WITH 
( 
  --The location maps to KafkaBootstrapServer 
  LOCATION = ' kafka://<kafkaserver>:<ipaddress>’, 
  CREDENTIAL = kafkaCredName 
 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’, 
    DELIMITER = ‘|’ 
); 
 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyKafka_tweets, 
    LOCATION = ‘<KafkaTopicName>’, 
   --JSON: Format, CSV: Delimiter and Encoding, AVRO: None 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      ‘PARTITIONS: 5’ 
); 
```

### <a name="example-4---blob-storage"></a>Пример 4. Хранилище BLOB-объектов

Тип: Вход или выход<br>
Параметры
- Вход или выход:
  - Псевдоним 
  - Учетная запись хранения 
  - Ключ учетной записи хранения 
  - Контейнер 
  - Шаблон пути 
  - Формат даты 
  - Формат времени 
  - Формат сериализации событий 
  - Кодирование 
- Только для входных данных: 
  - Секции (вход) 
  - Тип сжатия событий (вход) 
- Только для выходных данных: 
  - Минимальное число строк (выход) 
  - Максимальное время (выход) 
  - Режим проверки подлинности (выход) 

Синтаксис:

```sql
CREATE DATABASE SCOPED CREDENTIAL StorageAcctCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = 'AccountKey'; 
 
CREATE DATABASE SCOPED CREDENTIAL StorageAcctCredNameMSI 
WITH IDENTITY = 'Managed Identity’; 
 
CREATE EXTERNAL DATA SOURCE MyBlobStorage_tweets 
WITH 
(     
  LOCATION = 'sb://my-sb-namespace.servicebus.windows.net’, 
  CREDENTIAL = eventHubCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', --Event serialization format 
    DATE_FORMAT = 'YYYY/MM/DD HH', --Both Date and Time format 
    ENCODING = ‘UTF-8’ 
); 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyBlobStorage_tweets, 
    LOCATION = ‘<path_pattern>’, 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      ‘PARTITIONS: 1’, 
  
    OUTPUT_OPTIONS =  
      ‘REJECT_TYPE: Drop, 
      PARTITION_KEY_COLUMN: , 
      PROPERTY_COLUMNS: (), 
      MINUMUM_ROWS: 100000, 
      MAXIMUM_TIME: 60’ 
); 
```

### <a name="example-5---event-hub"></a>Пример 5. Концентратор событий

Тип: Вход или выход<br>
Параметры
- Вход или выход:
  - Псевдоним 
  - Пространство имен служебной шины 
  - имя концентратора событий; 
  - Имя политики концентратора событий 
  - Ключ политики концентратора событий 
  - Формат сериализации событий 
  - Кодирование 
- Только для входных данных: 
  - Группа потребителей концентратора событий 
  - Тип сжатия событий 
- Только для выходных данных: 
  - Столбец ключа раздела 
  - Столбцы свойств 

Синтаксис:

```sql
CREATE DATABASE SCOPED CREDENTIAL eventHubCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = '<policyName>'; 
 
CREATE EXTERNAL DATA SOURCE MyEventHub_tweets 
WITH 
(     
  LOCATION = 'sb://my-sb-namespace.servicebus.windows.net’, 
  CREDENTIAL = eventHubCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’, 
    DELIMITER = ‘|’ 
); 
 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyEventHub_tweets, 
    LOCATION = ‘<topicname>’, 
   --JSON: Format, CSV: Delimiter and Encoding, AVRO: None 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
 
    INPUT_OPTIONS =  
      ‘CONSUMER_GROUP: FirstConsumerGroup’, 
          
    OUTPUT_OPTIONS =  
      ‘REJECT_TYPE: Drop, 
      PARTITION_KEY_COLUMN: , 
      PROPERTY_COLUMNS: ()’ 
);
```

### <a name="example-6---iot-hub"></a>Пример 6. Центр Интернета вещей

Тип: Входные данные<br>
Параметры

- Псевдоним входных данных 
- Центр Интернета вещей 
- Конечная точка 
- Имя политики общего доступа 
- Ключ политики общего доступа 
- Группа потребителей 
- Формат сериализации событий 
- Кодирование 
- Тип сжатия событий 

Синтаксис:

```sql
CREATE DATABASE SCOPED CREDENTIAL IoTHubCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = '<policyName>'; 
 
CREATE EXTERNAL DATA SOURCE MyIoTHub_tweets 
WITH 
(     
  LOCATION = ' iot://iot_hub_name.azure-devices.net’, 
  CREDENTIAL = IoTHubCredName 
);  

CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', --Event serialization format 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’ 
); 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyIoTHub_tweets, 
    LOCATION = ‘<name>’, 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      ‘ENDPOINT: Messaging, 
      CONSUMER_GROUP: ‘FirstConsumerGroup’ 
); 
```

### <a name="example-7---azure-synapse-analytics-formerly-sql-data-warehouse"></a>Пример 7. Azure Synapse Analytics (прежнее название — хранилище данных SQL)

Тип: Выходные данные<br>
Параметры
- Псевдоним выходных данных 
- База данных 
- Сервер 
- Имя пользователя 
- Пароль 
- Таблица 
- Промежуточная область (для COPY) 

Синтаксис:

```sql
CREATE DATABASE SCOPED CREDENTIAL SQLCredName 
WITH IDENTITY = '<user>’, 
SECRET = '<password>'; 
 
CREATE EXTERNAL DATA SOURCE MyTargetSQLTable 
WITH 
(     
  LOCATION = ' <my_server_name>.database.windows.net’, 
  CREDENTIAL = SQLCredName 
); 
 
CREATE EXTERNAL STREAM MySQLTableOutput 
WITH ( 
   DATA_SOURCE = MyTargetSQLTable, 
   LOCATION = ‘<TableName>’ 
   --Note: Do not need external file format in this case 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop, 
     STAGING_AREA: staging_area_data_source’, 
); 
```


### <a name="example-8---table-storage"></a>Пример 8. Хранилище таблиц

Тип: Выходные данные<br>
Параметры
- Псевдоним выходных данных 
- Учетная запись хранения 
- Ключ учетной записи хранения 
- Имя таблицы 
- Ключ секции 
- Ключ строки. 
- Размер пакета 

Синтаксис:

```sql
CREATE DATABASE SCOPED CREDENTIAL TableStorageCredName 
WITH IDENTITY = ‘Storage account Key’, 
SECRET = '<storage_account_key>'; 
 
CREATE EXTERNAL DATA SOURCE MyTargetTableStorage 
WITH 
(     
  LOCATION = 'abfss://<storage_account>.dfs.core.windows.net’, 
  CREDENTIAL = TableStorageCredName 
); 
 
CREATE EXTERNAL STREAM MyTargetTableStorageOutput 
WITH ( 
   DATA_SOURCE = MyTargetTableStorage, 
   LOCATION = ‘<TableName>’, 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop, 
     PARTITION_KEY: <column_partition_key>, 
     ROW_KEY: <column_row_key>, 
     BATCH_SIZE: 100’ 
); 
```


### <a name="example-9---service-bus-topic-same-properties-as-queue"></a>Пример 9. Раздел служебной шины (имеет те же свойства, что и очередь)

Тип: Выходные данные<br>
Параметры
- Псевдоним выходных данных 
- Пространство имен служебной шины 
- Имя раздела 
- Имя политики раздела 
- Ключ политики раздела 
- Столбцы свойств 
- Столбцы системных свойств 
- Формат сериализации событий 
- Кодирование 

Синтаксис:

```sql
CREATE DATABASE SCOPED CREDENTIAL serviceBusCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = '<policyName>'; 
 
CREATE EXTERNAL DATA SOURCE MyServiceBus_tweets 
WITH 
(     
  LOCATION = 'sb://my-sb-namespace.servicebus.windows.net’, 
  CREDENTIAL = serviceBusCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', --Event serialization format 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’ 
); 
 
CREATE EXTERNAL STREAM MyServiceBusOutput 
WITH ( 
   DATA_SOURCE = MyServiceBus_tweets, 
   LOCATION = ‘<topic_name>’, 
   EXTERNAL_FILE_FORMAT = myFileFormat 
       OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop, 
     PARTITION_KEY_COLUMN: , 
     PROPERTY_COLUMNS: (), 
     SYSTEM_PROPERTY_COLUMNS: ()’ 
   --JSON: Format, CSV: Delimiter and Encoding, AVRO: None 
           
); 
```


### <a name="example-10---cosmos-db"></a>Пример 10. Cosmos DB

Тип: Выходные данные<br>
Параметры
- Псевдоним выходных данных 
- Идентификатор учетной записи 
- Ключ учетной записи 
- База данных 
- Имя контейнера 
- Идентификатор документа 


Синтаксис:

```sql
CREATE DATABASE SCOPED CREDENTIAL cosmosDBCredName 
WITH IDENTITY = ‘Storage Account Key’, 
SECRET = '<accountKey>'; 
 
CREATE EXTERNAL DATA SOURCE MyCosmosDB_tweets 
WITH 
(     
  LOCATION = ' cosmosdb://accountid.documents.azure.com:443/ database’, 
  CREDENTIAL = cosmosDBCredName 
); 
 
CREATE EXTERNAL STREAM MyCosmosDBOutput 
WITH ( 
   DATA_SOURCE = MyCosmosDB_tweets, 
   LOCATION = ‘<container/documentID>’ 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop', 
     --Note: Do not need external file format in this case 
          
);
```



### <a name="example-11---power-bi"></a>Пример 11. Power BI

Тип: Выходные данные<br>
Параметры
- Псевдоним выходных данных 
- Имя набора данных 
- Имя таблицы 


Синтаксис:

```sql
CREATE DATABASE SCOPED CREDENTIAL PBIDBCredName 
WITH IDENTITY = ‘Managed Identity’; 
 
CREATE EXTERNAL DATA SOURCE MyPbi_tweets 
WITH 
( 
  LOCATION = 'pbi://dataset/’, 
  CREDENTIAL = PBIDBCredName 
 
); 
 
CREATE EXTERNAL STREAM MyPbiOutput 
WITH ( 
   DATA_SOURCE = MyPbi_tweets, 
   LOCATION = 'tableName', 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop' 
        
);
```

### <a name="example-12---azure-function"></a>Пример 12. Функция Azure

Тип: Выходные данные<br>
Параметры
- Приложение-функция 
- Компонент 
- Клавиши 
- Максимальный размер пакета 
- Максимальное количество пакетов 

Синтаксис:

```sql
CREATE DATABASE SCOPED CREDENTIAL AzureFunctionCredName 
WITH IDENTITY = ‘Function Key’, 
SECRET = '<function_key>'; 
 
CREATE EXTERNAL DATA SOURCE MyTargetTableStorage 
WITH 
(     
  LOCATION = 'abfss://<storage_account>.dfs.core.windows.net’, 
  CREDENTIAL = TableStorageCredName 
); 
 
CREATE EXTERNAL STREAM MyTargetTableStorageOutput 
WITH ( 
   DATA_SOURCE = MyTargetTableStorage, 
   LOCATION = ‘<TableName>’, 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: 'Drop'      
     PARTITION_KEY: ‘<column_partition_key>, 
     ROW_KEY: <column_row_key>, 
     BATCH_SIZE: 100’ 
); 
```


## <a name="see-also"></a>См. также раздел

- [ALTER EXTERNAL STREAM (Transact-SQL)](alter-external-stream-transact-sql.md) 
- [DROP EXTERNAL STREAM (Transact-SQL)](drop-external-stream-transact-sql.md) 

