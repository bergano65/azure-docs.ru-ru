---
title: Устранение неполадок с соединителями Фабрики данных Azure
description: Узнайте, как устранить проблемы разъема на Фабрике данных Azure.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 01/09/2020
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: 9f3a13a097d7cce87aead4ec2d76ce7cbbb1a206
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75778232"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Устранение неполадок с соединителями Фабрики данных Azure

В этой статье рассматриваются общие методы устранения неполадок для разъемов на фабрике данных Azure.
  

## <a name="azure-blob-storage"></a>хранилище BLOB-объектов Azure

### <a name="error-code--azurebloboperationfailed"></a>Код ошибки: AzureBlobOperationFailed

- **Сообщение**:`Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **Причина**: Blob хранения операции хит проблемы.

- **Рекомендация**: Проверьте ошибку в деталях. Ссылайтесь на документ https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codesпомощи капли: . При необходимости свяжитесь с группой хранения данных.


### <a name="error-code--azureblobservicenotreturnexpecteddatalength"></a>Код ошибки: AzureBlobServiceNotReturnExpectedDataДлина

- **Сообщение**:`Error occurred when trying to fetch the blob '%name;'. This could be a transient issue and you may rerun the job. If it fails again continuously, contact customer support.`


### <a name="error-code--azureblobnotsupportmultiplefilesintosingleblob"></a>Код ошибки: AzureBlobNotSupportMultipleFilesIntoSingleBlob

- **Сообщение**:`Transferring multiple files into a single Blob is not supported. Currently only single file source is supported.`


### <a name="error-code--azurestorageoperationfailedconcurrentwrite"></a>Код ошибки: AzureStorageОперацияFailedConcurrentWrite

- **Сообщение**:`Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Сообщение об ошибке: размер запроса слишком велик

- **Симптомы**: Вы копируете данные в Azure Cosmos DB с размером партии по умолчанию, и попадаете в *ошибку "**Размер запроса слишком велик**"*.

- **Причина**: Космос DB ограничивает размер одного запроса до 2 МБ. Формула, Размер запроса - Единый размер документа - Запись размера пакета. Если размер документа большой, поведение по умолчанию приведет к слишком большому размеру запроса. Вы можете настроить размер пакета записи.

- **Разрешение**: В поглотителе активности копирования уменьшите значение «Написать пакет» (значение по умолчанию 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Сообщение об ошибке: Уникальное нарушение ограничения индекса

- **Симптомы**: При копировании данных в Космос DB, вы попали в следующую ошибку:

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **Причина**: Есть две возможные причины:

    - Если вы используете **Insert** как поведение записи, эта ошибка означает, что у исходных данных есть строки/объекты с тем же идентификатором.

    - Если вы **используете Upsert** в качестве поведения записи и устанавливаете другой уникальный ключ к контейнеру, эта ошибка означает, что у исходных данных есть строки/объекты с различными идентификациями, но одинаковое значение для определенного уникального ключа.

- **Разрешение**: 

    - Для cause1 установите **Upsert** как поведение записи.
    - Для причины 2 убедитесь, что каждый документ имеет различное значение для определенного уникального ключа.

### <a name="error-message-request-rate-is-large"></a>Сообщение об ошибке: скорость запроса велика

- **Симптомы**: При копировании данных в Космос DB, вы попали в следующую ошибку:

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **Причина**: Используемые единицы запроса больше, чем доступный RU, настроенный в Cosmos DB. Узнайте, как Cosmos DB вычисляет RU [здесь](../cosmos-db/request-units.md#request-unit-considerations).

- **Разрешение**: Вот два решения:

    1. **Увеличьте значение контейнера RU** до большего значения в Cosmos DB, что улучшит производительность копирования, хотя и понесет больше затрат в Cosmos DB. 

    2. Уменьшите **writeBatchSize** к более малому значению (such as 1000) и установите **parallelCopies** к более малому значению such as 1, которое сделает производительность бега экземпляра более плохой чем в настоящее время но не понесет больше цены в Космос DB.

### <a name="column-missing-in-column-mapping"></a>Столбец отсутствует в столбце отображения

- **Симптомы**: При импорте схемы для Cosmos DB для отображения столбцов некоторые столбцы отсутствуют. 

- **Причина**: ADF выведет схему из первых 10 документов Cosmos DB. Если некоторые столбцы/свойства не имеют значения в этих документах, они не будут обнаружены ADF, таким образом, не будут отображаться.

- **Разрешение**: Вы можете настроить запрос ниже, чтобы заставить столбец отображаться в наборе результатов с пустым значением: (предположим: "невозможно" столбец отсутствует в первых 10 документах). Кроме того, можно вручную добавить столбец для отображения.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Сообщение об ошибке: GuidRepresentation для читателя CSharpLegacy

- **Симптомы**: При копировании данных из Cosmos DB MongoAPI/MongoDB с полем UUID, вы нажмете следующую ошибку:

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **Причина**: Есть два способа представлять UUID в BSON - UuidStardard и UuidLegacy. По умолчанию UuidLegacy используется для чтения данных. Вы попадете в ошибку, если ваши данные UUID в MongoDB является UuidStandard.

- **Разрешение**: В строке соединения MongoDB, добавьте вариант «**uuidRepresentation»standard**«. Для получения дополнительной информации [см.](connector-mongodb.md#linked-service-properties)
            

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage 2-го поколения

### <a name="error-code--adlsgen2operationfailed"></a>Код ошибки: AdlsGen2ОперацияFailed

- **Сообщение**:`ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Причина**: ADLS Gen2 бросает ошибку, указывающую на сбой операции.

- **Рекомендация**: Проверьте подробное сообщение об ошибке, брошенное ADLS Gen2. Если это вызвано переходным сбоем, пожалуйста, повторите. Если вам нужна дополнительная помощь, пожалуйста, свяжитесь со службой хранения данных Azure и предоставьте идентификатор запроса в сообщении об ошибке.

- **Причина**: Когда сообщение об ошибке содержит 'Запрещенное', основной сервис или управляемая идентификация, которую вы используете, могут не иметь достаточного разрешения для доступа к ADLS Gen2.

- **Рекомендация**: Ссылайтесь https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authenticationна справочный документ: .

- **Причина**: Когда сообщение об ошибке содержит 'InternalServerError', ошибка возвращается ADLS Gen2.

- **Рекомендация**: Это может быть вызвано переходным сбоем, пожалуйста, повторите. Если проблема сохраняется, пожалуйста, свяжитесь со службой хранения Azure и предоставьте идентификатор запроса в сообщении об ошибке.


### <a name="error-code--adlsgen2invalidurl"></a>Код ошибки: AdlsGen2InvalidUrl

- **Сообщение**:`Invalid url '%url;' provided, expecting http[s]://<accountname>.dfs.core.windows.net.`


### <a name="error-code--adlsgen2invalidfolderpath"></a>Код ошибки: AdlsGen2InvalidFolderPath

- **Сообщение**:`The folder path is not specified. Cannot locate the file '%name;' under the ADLS Gen2 account directly. Please specify the folder path instead.`


### <a name="error-code--adlsgen2operationfailedconcurrentwrite"></a>Код ошибки: AdlsGen2ОперацияConcurrentWrite

- **Сообщение**:`Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


### <a name="error-code--adlsgen2timeouterror"></a>Код ошибки: AdlsGen2TimeoutОшибка

- **Сообщение**:`Request to ADLS Gen2 account '%account;' met timeout error. It is mostly caused by the poor network between the Self-hosted IR machine and the ADLS Gen2 account. Check the network to resolve such error.`


## <a name="azure-data-lake-storage-gen1"></a>Хранилище Azure Data Lake Storage 1-го поколения

### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Сообщение об ошибке: Удаленный сервер вернул ошибку: (403) Запрещено

- **Симптомы**: Копирование деятельности неудачу со следующей ошибкой: 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **Причина**: Одной из возможных причин является то, что основной или управляемый идентификатор службы, который вы используете, не имеет разрешения на доступ к определенной папке/файлу.

- **Разрешение**: Предоставить соответствующие разрешения на все папки и субфолки, которые вам нужно скопировать. Ссылайтесь на [этот документ](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Сообщение об ошибке: Не удалось получить токен доступа с помощью основного сервиса. Ошибка ADAL: service_unavailable

- **Симптомы**: Копирование деятельности неудачу со следующей ошибкой:

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **Причина:** Когда сервер токенов службы (STS), принадлежащий Active Directory Azure, не доступен, т.е. слишком занят для обработки запросов, он возвращает ошибку HTTP 503. 

- **Разрешение**: Перезапустите действие копии через несколько минут.
                  

## <a name="azure-sql-data-warehouseazure-sql-databasesql-server"></a>Хранилище данных Лазурного средства/База данных Лазурного Зона/Сервер S'L

### <a name="error-code--sqlfailedtoconnect"></a>Код ошибки: SqlFailedToConnect

- **Сообщение**:`Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`

- **Причина**: Если сообщение об ошибке содержит "SqlException", база данных S'L бросает ошибку, указывающую на некоторые конкретные операции не удалось.

- **Рекомендация**: Пожалуйста, ищите по коду https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errorsошибки s'L в этом справочном документе для получения более подробной информации: . Если вам нужна дополнительная помощь, обратитесь в службу поддержки Azure S'L.

- **Причина**: Если сообщение об ошибке содержит "Клиент с IP-адресом '...' доступ к серверу не допускается, и вы пытаетесь подключиться к базе данных Azure S'L, обычно это вызвано проблемой брандмауэра базы данных Azure S'L.

- **Рекомендация**: В конфигурации брандмауэра сервера Azure S'L Server включить опцию «Разрешить службам и ресурсам Azure доступ к этому серверу». Справочный https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configureдокумент: .


### <a name="error-code--sqloperationfailed"></a>Код ошибки: SqlOperationFailed

- **Сообщение**:`A database operation failed. Please search error to get more details.`

- **Причина**: Если сообщение об ошибке содержит "SqlException", база данных S'L бросает ошибку, указывающую на некоторые конкретные операции не удалось.

- **Рекомендация**: Если ошибка S'L не ясна, пожалуйста, попробуйте изменить базу данных до последнего уровня совместимости '150'. Он может бросать последнюю версию ошибок S'L. Пожалуйста, обратитесь https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level?view=sql-server-ver15#backwardCompatк подробно документ: .
        Для устранения неполадок, пожалуйста, ищите по коду https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errorsошибки s'L в этом справочном документе для получения более подробной информации: . Если вам нужна дополнительная помощь, обратитесь в службу поддержки Azure S'L.

- **Причина**: Если сообщение об ошибке содержит "PdwManagedToNativeInopopException", обычно это вызвано несоответствием между размерами источника и столбца раковины.

- **Рекомендация**: Проверьте размер столбцов как источника, так и столбцов раковины. Если вам нужна дополнительная помощь, обратитесь в службу поддержки Azure S'L.

- **Причина**: Если сообщение об ошибке содержит "InvalidOperationException", обычно это вызвано недействительными входными данными.

- **Рекомендация**: Чтобы определить, какая строка сталкивается с проблемой, пожалуйста, включите функцию допуска к ошибке на активности копирования, которая может перенаправить проблемные строки (ы) на хранилище для дальнейшего исследования. Справочный https://docs.microsoft.com/azure/data-factory/copy-activity-fault-toleranceдокумент: .


### <a name="error-code--sqlunauthorizedaccess"></a>Код ошибки: SqlUnauthorizedAccess

- **Сообщение**:`Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Причина**: Учетные данные неверны, или учетная запись входа не может получить доступ к базе данных S'L.

- **Рекомендация**: Проверьте учетную запись входа имеет достаточное разрешение для доступа к базе данных S'L.


### <a name="error-code--sqlopenconnectiontimeout"></a>Код ошибки: SqlOpenConnectionTimeout

- **Сообщение**:`Open connection to database timeout after '%timeoutValue;' seconds.`

- **Причина**: Может быть переходный сбой базы данных S'L.

- **Рекомендация**: Пожалуйста, повторите для обновления строки подключения к службе с большим значением тайм-аута соединения.


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>Код ошибки: SqlAutoCreateTableTypeMapFailed

- **Сообщение**:`Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **Причина**: Таблица создания авто не может соответствовать требованиям источника.

- **Рекомендация**: Обновление ввода столбца в "отображениях" или вручную создайте таблицу раковины на целевом сервере.


### <a name="error-code--sqldatatypenotsupported"></a>Код ошибки: SqlDataTypeNotSupported

- **Сообщение**:`A database operation failed. Check the SQL errors.`

- **Причина**: Если проблема возникает на источнике S'L и ошибка связана с переполнением SlDate, значение данных находится над диапазоном логического типа (1/1/1753 12:00 AM - 12/31/9999 11:59:59 PM).

- **Рекомендация**: Отбросьте тип строки в исходном запросе S'L или в столбце активности копирования, изменяя тип столбца на "String".

- **Причина**: Если проблема возникает на раковине S'L и ошибка связана с переполнением SlDate, значение данных находится над допустимого диапазона в таблице раковины.

- **Рекомендация**: Обновление соответствующего типа столбца до типа 'datetime2' в таблице раковины.


### <a name="error-code--sqlinvaliddbstoredprocedure"></a>Код ошибки: SqlInvalidDbStoredProcedure

- **Сообщение**:`The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Причина**: Указанная Процедура хранения недействительна. Это может быть вызвано тем, что сохраненная процедура не возвращает никаких данных.

- **Рекомендация**: Проверка сохраненной процедуры с помощью s'L Tools. Убедитесь, что сохраненная процедура может вернуть данные.


### <a name="error-code--sqlinvaliddbquerystring"></a>Код ошибки: SqlInvalidD-КвириТринг

- **Сообщение**:`The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Причина**: Указанный запрос S'L недействителен. Это может быть вызвано тем, что запрос не возвращает данные

- **Рекомендация**: Проверка запроса с помощью S'L Tools. Убедитесь, что запрос может вернуть данные.


### <a name="error-code--sqlinvalidcolumnname"></a>Код ошибки: SqlInvalidColumnName

- **Сообщение**:`Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Причина**: Не удается найти столбец. Возможная конфигурация неправильно.

- **Рекомендация**: Проверка столбца в запросе, "структура" в наборе данных и "отображение" в деятельности.


### <a name="error-code--sqlcolumnnamemismatchbycasesensitive"></a>Код ошибки: SqlColumnNameMismatchByCaseЧувствье

- **Сообщение**:`Column '%column;' in DataSet '%dataSetName;' cannot be found in physical SQL Database. Column matching is case-sensitive. Column '%columnInTable;' appears similar. Check the DataSet(s) configuration to proceed further.`


### <a name="error-code--sqlbatchwritetimeout"></a>Код ошибки: SqlBatchWriteTimeout

- **Сообщение**:`Timeouts in SQL write operation.`

- **Причина**: Может быть переходный сбой базы данных S'L.

- **Рекомендация**: Пожалуйста, повторная попытка. Если проблема repro, обратитесь в службу поддержки Azure S'L.


### <a name="error-code--sqlbatchwritetransactionfailed"></a>Код ошибки: SqlBatchWriteТранзакциасснессис

- **Сообщение**:`SQL transaction commits failed`

- **Причина**: Если детали исключения постоянно говорят о тайм-ауте транзакции, задержка сети между временем выполнения интеграции и базой данных выше порога по умолчанию в 30 секунд.

- **Рекомендация**: Обновление Строки подключения к службе с значением "тайм-аут подключения" равна 120 или выше и повторное действие.

- **Причина**: Если детали исключения периодически говорят sqlconnection сломанной, это может быть просто переходный сбой сети или s'L базы данных боковой вопрос

- **Рекомендация**: Пожалуйста, повторите действия и просмотрите боковые метрики базы данных S'L.


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>Код ошибки: SqlbulkCopyНейднаяДлиннаяколонна

- **Сообщение**:`SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **Причина:** СЗЛ Массовая копия не удалось из-за получения недействительной длины столбца от клиента bcp.

- **Рекомендация**: Чтобы определить, какая строка сталкивается с проблемой, пожалуйста, включите функцию допуска к ошибке на активности копирования, которая может перенаправить проблемные строки (ы) на хранилище для дальнейшего исследования. Справочный https://docs.microsoft.com/azure/data-factory/copy-activity-fault-toleranceдокумент: .


### <a name="error-code--sqlconnectionisclosed"></a>Код ошибки: SqlConnectionЗакрыто

- **Сообщение**:`The connection is closed by SQL Database.`

- **Причина:** соединение S'L закрывается базой данных S'L при высоком одновременном запуске и завершении подключения сервера.

- **Рекомендация**: Удаленный сервер закрыл соединение S'L. Повторите попытку. Если проблема repro, обратитесь в службу поддержки Azure S'L.


### <a name="error-code--sqlcreatetablefailedunsupportedtype"></a>Код ошибки: SqlCreateTableFailedUnsupportedType

- **Сообщение**:`Type '%type;' in source side cannot be mapped to a type that supported by sink side(column name:'%name;') in autocreate table.`


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Сообщение об ошибке: Преобразование не удалось при преобразовании из строки символов в uniqueidentifier

- **Симптомы**: При копировании данных из табличного источника данных (например, сервера S'L) в хранилище данных Azure S'L с использованием постановочной копии и PolyBase вы попадаете в следующую ошибку:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **Причина**: Хранилище данных Azure S'L PolyBase не может преобразовать пустую строку в GUID.

- **Разрешение**: В поглотителе активности copy, под настройками Polybase, установите **«по умолчанию типа использования»** к ложному.

### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Сообщение об ошибке: Ожидаемый тип данных: DECIMAL (x,x), Значение оскорбления

- **Симптомы**: При копировании данных из табличного источника данных (например, сервера S'L) в S'L DW с помощью постановочной копии и PolyBase вы попадаете в следующую ошибку:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **Причина**: Полибаза данных Azure S'L Data Polybase не может вставить пустую строку (нулевое значение) в десятичную колонку.

- **Разрешение**: В поглотителе активности copy, под настройками Polybase, установите **«по умолчанию типа использования»** к ложному.

### <a name="error-message-java-exception-messagehdfsbridgecreaterecordreader"></a>Сообщение об ошибке: сообщение исключения Java:HdfsBridge::CreateRecordReader

- **Симптомы**: Вы копируете данные в хранилище данных Azure S'L с помощью PolyBase и попадаете в следующую ошибку:

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **Причина**: Возможная причина в том, что схема (общая ширина столбца) слишком велика (больше, чем 1 МБ). Проверьте схему целевой таблицы S'L DW, добавив размер всех столбцов:

    - Int -> 4 байта
    - Бигинт -> 8 байтов
    - Варчар (n),char(n),binary(n), varbinary(n) -> n байтов
    - Nvarchar (n), nchar(n) -> n'2 байта
    - Дата -> 6 байтов
    - Дата/(2), небольшое время -> 16 байтов
    - Datetimeoffset -> 20 байтов
    - Десятичная -> 19 байтов
    - Поплавок -> 8 байтов
    - Деньги - > 8 байтов
    - Smallmoney -> 4 байт
    - Реал -> 4 байта
    - Смолинт -> 2 байта
    - Время -> 12 байтов
    - Tinyint -> 1 байт

- **Разрешение**: Уменьшить ширину столбца, чтобы быть менее 1 МБ

- Или использовать подход навалом вставки, отключив Polybase

### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Сообщение об ошибке: Условие, указанное с помощью условного заголовка HTTP(ы) не выполняется

- **Симптомы**: Вы используете запрос S'L для вытягивания данных из хранилища данных Azure s'L и нажмите следующую ошибку:

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **Причина**: Хранилище данных Azure S'L попало в проблему запроса внешней таблицы в Azure Storage.

- **Разрешение**: Выполнить тот же запрос в SSMS и проверить, если вы видите тот же результат. Если да, отправьте запрос в службу поддержки для Хранилища данных SQL Azure и укажите имя сервера и базы данных Хранилища данных SQL, чтобы устранить неполадки.
            

## <a name="delimited-text-format"></a>Текстовый формат с разделителями

### <a name="error-code--delimitedtextcolumnnamenotallownull"></a>Код ошибки: DelimitedTextColumnNameNotAllowNull

- **Сообщение**:`The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **Причина**: При установке 'firstRowAsHeader' в действии, первая строка будет использоваться в качестве имени столбца. Эта ошибка означает, что первый ряд содержит пустое значение. Например: 'ColumnA,,ColumnB'.

- **Рекомендация**: Проверьте первый ряд и исправьте значение, если есть пустое значение.


### <a name="error-code--delimitedtextmorecolumnsthandefined"></a>Код ошибки: DelimitedTextMoreColumnsThanDefined

- **Сообщение**:`Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %columnCount;.`

- **Причина**: количество столбцов проблемного строки больше, чем количество столбцов первого ряда. Это может быть вызвано проблемой данных или неправильными настройками обезлимителивого столбца/цитаты.

- **Рекомендация**: Пожалуйста, получите количество строк в сообщении об ошибке, проверьте столбец строки и исправьте данные.

- **Причина**: Если ожидаемый отсчет столбца является "1" в сообщении об ошибке, возможно, что вы указали неправильные настройки сжатия или формата, что привело к неправильному разбору ADF вашего файла (ы).

- **Рекомендация**: Проверьте настройки формата, чтобы убедиться, что он соответствует исходной файлу (ы).

- **Причина**: Если ваш источник является папкой, возможно, что файлы в указанной папке имеют различную схему.

- **Рекомендация**: Убедитесь, что файлы в данной папке имеют идентичную схему.


### <a name="error-code--delimitedtextincorrectrowdelimiter"></a>Код ошибки: DelimitedTextIncorrectRowDelimiter

- **Сообщение**:`The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--delimitedtexttoolargecolumncount"></a>Код ошибки: DelimitedTextTooLargeColumnCount

- **Сообщение**:`Column count reaches limitation when deserializing csv file. Maximum size is '%size;'. Check the column delimiter and row delimiter provided. (Column delimiter: '%columnDelimiter;', Row delimiter: '%rowDelimiter;')`


### <a name="error-code--delimitedtextinvalidsettings"></a>Код ошибки: DelimitedTextInvalidSettings

- **Сообщение**:`%settingIssues;`



## <a name="dynamics-365common-data-servicedynamics-crm"></a>Динамика 365/Общая служба данных/Динамика CRM

### <a name="error-code--dynamicscreateserviceclienterror"></a>Код ошибки: DynamicsCreateServiceClientОшибк

- **Сообщение**:`This is a transient issue on dynamics server side. Try to rerun the pipeline.`

- **Причина**: Это переходная проблема на стороне сервера динамики.

- **Рекомендация**: Повтор конвейера. Если держать неудачу, попробуйте уменьшить параллелизм. Если все еще не удается, пожалуйста, свяжитесь с поддержкой динамики.



## <a name="json-format"></a>Формат JSON

### <a name="error-code--jsoninvalidarraypathdefinition"></a>Код ошибки: JsonInvalidArrayPathОпределение

- **Сообщение**:`Error occurred when deserializing source JSON data. Check whether the JsonPath in JsonNodeReference and JsonPathDefintion is valid.`


### <a name="error-code--jsonemptyjobjectdata"></a>Код ошибки: JsonEmptyJObjectData

- **Сообщение**:`The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--jsonnullvalueinpathdefinition"></a>Код ошибки: JsonNullValueInpathОпределение

- **Сообщение**:`Null JSONPath detected in JsonPathDefinition.`


### <a name="error-code--jsonunsupportedhierarchicalcomplexvalue"></a>Код ошибки: JsonUnsupportedHierarchicalComplexValue

- **Сообщение**:`The retrieved type of data %data; with value %value; is not supported yet. Please either remove the targeted column '%name;' or enable skip incompatible row to skip the issue rows.`


### <a name="error-code--jsonconflictpartitiondiscoveryschema"></a>Код ошибки: JsonConflictPartitionDiscoverySchema

- **Сообщение**:`Conflicting partition column names detected.'%schema;', '%partitionDiscoverySchema;'`


### <a name="error-code--jsoninvaliddataformat"></a>Код ошибки: JsonInvalidDataFormat

- **Сообщение**:`Error occurred when deserializing source JSON file '%fileName;'. Check if the data is in valid JSON object format.`


### <a name="error-code--jsoninvaliddatamixedarrayandobject"></a>Код ошибки: JsonInvalidDataMixedArrayandObject

- **Сообщение**:`Error occurred when deserializing source JSON file '%fileName;'. The JSON format doesn't allow mixed arrays and objects.`



## <a name="parquet-format"></a>формат PARQUET.

### <a name="error-code--parquetjavainvocationexception"></a>Код ошибки: паркетЯАКАцияОтмена

- **Сообщение**:`An error occurred when invoking java, message: %javaException;.`

- **Причина**: Когда сообщение об ошибке содержит 'java.lang.OutOfMemory', 'Java кучу пространство' и 'doubleCapacity', как правило, это проблема управления памятью в старой версии времени выполнения интеграции.

- **Рекомендация**: Если вы используете самостоятельно размещенную интеграцию Runtime и версия раньше, чем 3.20.7159.1, предложите обновить до последней версии.

- **Причина**: Когда сообщение об ошибке содержит 'java.lang.OutOfMemory', время выполнения интеграции не имеет достаточного ресурса для обработки файла(ы).

- **Рекомендация**: Ограничьте одновременные запуски на время выполнения интеграции. Для автономного запуска интеграции масштабируйте до мощной машины с памятью, равной или превышающему 8 ГБ.

- **Причина**: Когда сообщение об ошибке содержит 'NullPointerReference', возможно, это переходная ошибка.

- **Рекомендация**: Пожалуйста, повторная попытка. Если проблема сохраняется, обратитесь в службу поддержки.


### <a name="error-code--parquetinvalidfile"></a>Код ошибки: ПаркетНедействительноеФайл

- **Сообщение**:`File is not a valid parquet file.`

- **Причина**: Вопрос о файле паркета.

- **Рекомендация**: Проверьте вход является действительным паркетным файлом.


### <a name="error-code--parquetnotsupportedtype"></a>Код ошибки: ParquetNotSupportedType

- **Сообщение**:`Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **Причина**: Формат паркета не поддерживается на фабрике данных Azure.

- **Рекомендация**: Двойная проверка исходных данных. Обратитесь к https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecsдокументу: .


### <a name="error-code--parquetmisseddecimalprecisionscale"></a>Код ошибки: ParquetMissedDecimalPrecisionScale

- **Сообщение**:`Decimal Precision or Scale information is not found in schema for column: %column;.`

- **Причина**: Попробуйте разобрать точность и масштаб числа, но такая информация не предоставляется.

- **Рекомендация**: 'Источник' не возвращает правильную точность и масштаб. Проверьте точность и масштаб столбца проблемы.


### <a name="error-code--parquetinvaliddecimalprecisionscale"></a>Код ошибки: ПаркетНедействительноЕDecimalPrecisionScale

- **Сообщение**:`Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **Причина**: Схема недействительна.

- **Рекомендация**: Проверьте точность и масштаб столбца проблемы.


### <a name="error-code--parquetcolumnnotfound"></a>Код ошибки: ПаркетКолпомон

- **Сообщение**:`Column %column; does not exist in Parquet file.`

- **Причина**: Схема источника несоответствует схеме раковины.

- **Рекомендация**: Проверьте 'карты' в 'активности'. Убедитесь, что исходный столбец может быть отображены в правой колонке раковины.


### <a name="error-code--parquetinvaliddataformat"></a>Код ошибки: ПаркетНетадедДатаФормат

- **Сообщение**:`Incorrect format of %srcValue; for converting to %dstType;.`

- **Причина**: Данные не могут быть преобразованы в тип, указанный в mappings.source

- **Рекомендация**: Двойная проверка исходных данных или указать правильный тип данных для этого столбца в отображении столбца копирования. Обратитесь к https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecsдокументу: .


### <a name="error-code--parquetdatacountnotmatchcolumncount"></a>Код ошибки: ParquetDataCountNotMatchColumnCount

- **Сообщение**:`The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **Причина**: Количество столбцов источника и столбец раковины отсчет несоответствие

- **Рекомендация**: Двойной отсчет столбца источника проверки такой же, как количество столбцов раковины в 'отображении'.


### <a name="error-code--parquetdatatypenotmatchcolumntype"></a>Код ошибки: ParquetDataTypeNotMatchColumnType

- **Сообщение**: Тип данных %srcType; не совпадает с данной колонкой типа %dstType; в столбце '%columnIndex;'.

- **Причина**: Данные из источника не могут быть преобразованы в набранные, определенные в раковине

- **Рекомендация**: Пожалуйста, укажите правильный тип в mapping.sink.


### <a name="error-code--parquetbridgeinvaliddata"></a>Код ошибки: ParquetBridgeInvalidData

- **Сообщение**:`%message;`

- **Причина**: Значение данных над ограничением

- **Рекомендация**: Пожалуйста, повторная попытка. Если проблема сохраняется, пожалуйста, свяжитесь с нами.


### <a name="error-code--parquetunsupportedinterpretation"></a>Код ошибки: ПаркетНеподдерживаемоеИнтерпретация

- **Сообщение**:`The given interpretation '%interpretation;' of parquet format is not supported.`

- **Причина**: Не поддерживаемый сценарий

- **Рекомендация**: 'ParquetInterpretFor' не должна быть 'sparkSql'.


### <a name="error-code--parquetunsupportfilelevelcompressionoption"></a>Код ошибки: ParquetUnsupportFileLevelCompressionOption

- **Сообщение**:`File level compression is not supported for Parquet.`

- **Причина**: Не поддерживаемый сценарий

- **Рекомендация**: Удалите 'СжатиеType' в полезной нагрузке.



## <a name="general-copy-activity-error"></a>Общая ошибка копирования

### <a name="error-code--jrenotfound"></a>Код ошибки: JreNotFound

- **Сообщение**:`Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **Причина**: Самоходное время выполнения интеграции не может найти Java Runtime. Java Runtime требуется для чтения конкретного источника.

- **Рекомендация**: Проверьте среду выполнения интеграции, справочный документ:https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime


### <a name="error-code--wildcardpathsinknotsupported"></a>Код ошибки: WildcardPathSinkNotSupported

- **Сообщение**:`Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **Причина**: Набор данных Sink не поддерживает подстановочный знак.

- **Рекомендация**: Проверьте набор данных раковины и исправьте путь без значения подстановочного знака.


### <a name="error-code--mappinginvalidpropertywithemptyvalue"></a>Код ошибки: ОтображениеНетаваянедвижимостьСпустоезначение

- **Сообщение**:`One or more '%sourceOrSink;' in copy activity mapping doesn't point to any data. Choose one of the three properties 'name', 'path' and 'ordinal' to reference columns/fields.`


### <a name="error-code--mappinginvalidpropertywithnamepathandordinal"></a>Код ошибки: ОтображениеInvalidPropertyNamePathAndOrdinal

- **Сообщение**:`Mixed properties are used to reference '%sourceOrSink;' columns/fields in copy activity mapping. Please only choose one of the three properties 'name', 'path' and 'ordinal'. The problematic mapping setting is 'name': '%name;', 'path': '%path;','ordinal': '%ordinal;'.`


### <a name="error-code--mappingduplicatedordinal"></a>Код ошибки: КартированиеИмблемадодинал

- **Сообщение**:`Copy activity 'mappings' has duplicated ordinal value "%Ordinal;". Fix the setting in 'mappings'.`


### <a name="error-code--mappinginvalidordinalforsinkcolumn"></a>Код ошибки: ОтображениеInvalidOrdinalForSink

- **Сообщение**:`Invalid 'ordinal' property for sink column under 'mappings' property. Ordinal: %Ordinal;.`


## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительной помощи для устранения неполадок попробуйте следующие ресурсы:

*  [Блог Фабрики данных](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Запросы функций Фабрики данных](https://feedback.azure.com/forums/270578-data-factory)
*  [Видео по Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Форум MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Форум переполнения стеков для фабрики данных](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Информация в Твиттере о фабрике данных](https://twitter.com/hashtag/DataFactory)
            
