---
title: Устранение неполадок с соединителями Фабрики данных Azure
description: Узнайте, как устранять неполадки с соединителями Фабрики данных Azure.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 12/02/2020
ms.author: jingwang
ms.reviewer: craigg
ms.custom: has-adal-ref
ms.openlocfilehash: c90b7ce86e06669696a4b9f7e0b2f5287e9dd97e
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533202"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Устранение неполадок с соединителями Фабрики данных Azure

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

В этой статье рассматриваются распространенные методы устранения неполадок для соединителей Фабрики данных Azure.
  
## <a name="azure-blob-storage"></a>хранилище BLOB-объектов Azure

### <a name="error-code--azurebloboperationfailed"></a>Код ошибки:  AzureBlobOperationFailed

- **Сообщение**: `Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **Причина.** Проблема при выполнении операции с хранилищем BLOB-объектов.

- **Рекомендация**.  Изучите подробные сведения об ошибке. См. справочный документ по большим двоичным объектам: https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes. Если вам нужна помощь, обратитесь в команду разработки службы хранилища.


### <a name="error-code--azureblobservicenotreturnexpecteddatalength"></a>Код ошибки:  AzureBlobServiceNotReturnExpectedDataLength

- **Сообщение**: `Error occurred when trying to fetch the blob '%name;'. This could be a transient issue and you may rerun the job. If it fails again continuously, contact customer support.`


### <a name="error-code--azureblobnotsupportmultiplefilesintosingleblob"></a>Код ошибки:  AzureBlobNotSupportMultipleFilesIntoSingleBlob

- **Сообщение**: `Transferring multiple files into a single Blob is not supported. Currently only single file source is supported.`


### <a name="error-code--azurestorageoperationfailedconcurrentwrite"></a>Код ошибки:  AzureStorageOperationFailedConcurrentWrite

- **Сообщение**: `Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Сообщение об ошибке: Слишком большой размер запроса.

- **Проблема.** Копирование данных в Azure Cosmos DB с размером пакета для записи по умолчанию и получение сообщения об ошибке *"**Слишком большой размер запроса**"* .

- **Причина.** В Cosmos DB размер одного запроса не должен превышать 2 МБ. Формула выглядит так: размер запроса = размера одного документа * размер пакета для записи. Если размер документа велик, то поведение по умолчанию приведет к превышению размера запроса. Размер пакета для записи можно настроить.

- **Решение**. В приемнике действия копирования уменьшите размер пакета для записи (значение по умолчанию — 10 000).

### <a name="error-message-unique-index-constraint-violation"></a>Сообщение об ошибке: Нарушение уникального ограничения индекса

- **Проблема.** При копировании данных в Cosmos DB возникает следующая ошибка:

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **Причина.** Существуют две возможные причины.

    - Если вы используете инструкцию **Insert** в качестве поведения записи, эта ошибка означает, что в исходных данных есть строки или объекты с одинаковыми идентификаторами.

    - Если вы используете инструкцию **Upsert** в качестве поведения записи и задаете другой уникальный ключ для контейнера, эта ошибка означает, что в исходных данных есть строки или объекты с разными идентификаторами, но одинаковыми значениями уникального ключа.

- **Решение**. 

    - Для первой причины задайте инструкцию **Upsert** в качестве поведения записи.
    - Для второй причины убедитесь в том, что у всех документов разные значения уникального ключа.

### <a name="error-message-request-rate-is-large"></a>Сообщение об ошибке: Высокая частота запросов

- **Проблема.** При копировании данных в Cosmos DB возникает следующая ошибка:

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **Причина.** Используется больше единиц запросов, чем доступное количество ЕЗ, настроенное в Cosmos DB. Сведения о том, как количество ЕЗ рассчитывается в Cosmos DB, см. [здесь](../cosmos-db/request-units.md#request-unit-considerations).

- **Решение**. Есть два решения.

    1. **Увеличьте значение ЕЗ контейнера** в Cosmos DB. Это повысит производительность действия копирования, но потребует дополнительных затрат на Cosmos DB. 

    2. Задайте меньшее значение **writeBatchSize** (например, 1000) и **parallelCopies** (например, 1). Это приведет к снижению производительности при копировании, но не потребует дополнительных затрат на Cosmos DB.

### <a name="column-missing-in-column-mapping"></a>Столбец отсутствует в сопоставлении столбцов

- **Проблема.** При импорте схемы для сопоставления столбцов в Cosmos DB некоторые столбцы отсутствуют. 

- **Причина.** Фабрика данных Azure выводит схему из первых 10 документов Cosmos DB. Если некоторые столбцы или свойства не имеют значений в этих документах, они не будут обнаружены Фабрикой данных Azure и поэтому не будут отображаться.

- **Решение**. Вы можете настроить запрос указанным ниже образом, чтобы столбец обязательно присутствовал в результирующем наборе с пустым значением (предположим, что в первых 10 документах отсутствует столбец impossible). Кроме того, можно вручную добавить столбец для сопоставления.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Сообщение об ошибке: GuidRepresentation для модуля чтения имеет значение CSharpLegacy

- **Проблема.** При копировании данных из Cosmos DB MongoAPI или MongoDB с полем UUID возникает следующая ошибка:

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **Причина.** Существует два способа представления UUID в BSON: UuidStardard и UuidLegacy. По умолчанию UuidLegacy используется для чтения данных. Если данные UUID в MongoDB представлены как UuidStandard, произойдет ошибка.

- **Решение**. В строке подключения MongoDB добавьте параметр **uuidRepresentation=standard**. Дополнительные сведения см. в разделе о [строке подключения MongoDB](connector-mongodb.md#linked-service-properties).
            

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage 2-го поколения

### <a name="error-code--adlsgen2operationfailed"></a>Код ошибки:  AdlsGen2OperationFailed

- **Сообщение**: `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Причина.** ADLS 2-го поколения выдает ошибку, указывающую на сбой операции.

- **Рекомендация**.  Проверьте подробное сообщение об ошибке, выдаваемое ADLS 2-го поколения. Если она вызвана временным сбоем, повторите попытку. Если вам нужна дополнительная помощь, обратитесь в службу поддержки службы хранилища Azure и укажите идентификатор запроса в сообщении об ошибке.

- **Причина.** Когда в сообщении об ошибке есть слово Forbidden, возможно, у субъекта-службы или управляемого удостоверения, которые вы используете, недостаточно разрешений на доступ к ADLS 2-го поколения.

- **Рекомендация**.  См. справочный документ https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication.

- **Причина.** Если в сообщении об ошибке есть слово InternalServerError, ошибка возвращается ADLS 2-го поколения.

- **Рекомендация**.  Если ошибка вызвана временным сбоем, повторите попытку. Если проблема сохраняется, обратитесь в службу поддержки службы хранилища Azure и укажите идентификатор запроса в сообщении об ошибке.


### <a name="error-code--adlsgen2invalidurl"></a>Код ошибки:  AdlsGen2InvalidUrl

- **Сообщение**: `Invalid url '%url;' provided, expecting http[s]://<accountname>.dfs.core.windows.net.`


### <a name="error-code--adlsgen2invalidfolderpath"></a>Код ошибки:  AdlsGen2InvalidFolderPath

- **Сообщение**: `The folder path is not specified. Cannot locate the file '%name;' under the ADLS Gen2 account directly. Please specify the folder path instead.`


### <a name="error-code--adlsgen2operationfailedconcurrentwrite"></a>Код ошибки:  AdlsGen2OperationFailedConcurrentWrite

- **Сообщение**: `Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


### <a name="error-code-adlsgen2timeouterror"></a>Код ошибки: AdlsGen2TimeoutError

- **Сообщение**: `Request to ADLS Gen2 account '%account;' met timeout error. It is mostly caused by the poor network between the Self-hosted IR machine and the ADLS Gen2 account. Check the network to resolve such error.`


## <a name="azure-data-lake-storage-gen1"></a>Хранилище Azure Data Lake Storage 1-го поколения

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>Сообщение об ошибке: базовое соединение закрыто: не удалось установить доверительные отношения для защищенного канала SSL/TLS.

- **Симптомы**. сбой действия копирования со следующей ошибкой: 

    ```
    Message: Failure happened on 'Sink' side. ErrorCode=UserErrorFailedFileOperation,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Upload file failed at path STAGING/PLANT/INDIARENEWABLE/LiveData/2020/01/14\\20200114-0701-oem_gibtvl_mannur_data_10min.csv.,Source=Microsoft.DataTransfer.ClientLibrary,''Type=System.Net.WebException,Message=The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.,Source=System,''Type=System.Security.Authentication.AuthenticationException,Message=The remote certificate is invalid according to the validation procedure.,Source=System,'.
    ```

- **Причина**: сбой проверки сертификата во время подтверждения TLS.

- **Решение**. Используйте промежуточное копирование, чтобы пропустить проверку TLS для ADLS 1-го поколения. Необходимо воспроизвести эту ошибку и собрать трассировку NetMon, а затем обратиться в сетевую группу, чтобы проверить конфигурацию локальной сети.

    ![Устранение неполадок ADLS 1-го поколения](./media/connector-troubleshoot-guide/adls-troubleshoot.png)


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Сообщение об ошибке: Удаленный сервер вернул ошибку: (403) Запрещено

- **Проблема.** Сбой действия копирования со следующей ошибкой: 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **Причина.** Одна из возможных причин заключается в том, что у субъекта-службы или управляемого удостоверения, которые вы используете, нет разрешений на доступ к определенной папке или файлу.

- **Решение**. Предоставьте соответствующие разрешения для всех папок и вложенных папок, которые необходимо скопировать. См. [этот документ](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Сообщение об ошибке: Не удалось получить токен доступа с помощью субъекта-службы. Ошибка ADAL: service_unavailable

- **Проблема.** Сбой действия копирования со следующей ошибкой:

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **Причина.** Когда сервер маркеров службы (STS), принадлежащий Azure Active Directory, недоступен, то есть слишком занят для обработки запросов, он возвращает ошибку HTTP 503. 

- **Решение**. Повторите действие копирования через несколько минут.
                  

## <a name="azure-synapse-analyticsazure-sql-databasesql-server"></a>Azure синапсе Analytics/база данных SQL Azure/SQL Server

### <a name="error-code--sqlfailedtoconnect"></a>Код ошибки:  SqlFailedToConnect

- **Сообщение**: `Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`

- **Причина.** Если в сообщении об ошибке есть слово SqlException, база данных SQL выдает ошибку, указывающую на сбой определенной операции.

- **Рекомендация**: выполните поиск по коду ошибки SQL в этом справочном документе для получения дополнительных сведений: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors . Если вам нужна дополнительная помощь, обратитесь в службу поддержки Azure SQL.

- **Причина.** Если сообщение об ошибке содержит строку "Клиенту с IP-адресом "..." запрещен доступ к серверу" и вы пытаетесь подключиться к базе данных SQL Azure, обычно причиной является проблема с брандмауэром базы данных SQL Azure.

- **Рекомендация**: в логической конфигурации брандмауэра SQL Server включите параметр "разрешить службам Azure и ресурсам доступ к этому серверу". Справочный документ: https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure.


### <a name="error-code--sqloperationfailed"></a>Код ошибки:  SqlOperationFailed

- **Сообщение**: `A database operation failed. Please search error to get more details.`

- **Причина.** Если в сообщении об ошибке есть слово SqlException, база данных SQL выдает ошибку, указывающую на сбой определенной операции.

- **Рекомендация**.  Если ошибка SQL не ясна, попробуйте изменить уровень совместимости базы данных на последний (150). Могут возникнуть ошибки SQL последней версии. См. [подробный документ](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat).

    Чтобы устранить неполадки SQL, выполните поиск по коду ошибки SQL в этом справочном документе: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors. Если вам нужна дополнительная помощь, обратитесь в службу поддержки Azure SQL.

- **Причина.** Если в сообщении об ошибке есть слово PdwManagedToNativeInteropException, обычно это вызвано несоответствием размеров столбца-источника и столбца-приемника.

- **Рекомендация**.  Проверьте размер как столбца-источника, так и столбца-приемника. Если вам нужна дополнительная помощь, обратитесь в службу поддержки Azure SQL.

- **Причина.** Если в сообщении об ошибке есть слово InvalidOperationException, обычно это вызвано недопустимыми входными данными.

- **Рекомендация**.  Чтобы узнать, в какой строке возникает проблема, включите функцию отказоустойчивости для действия копирования, которая может перенаправлять проблемные строки в хранилище для дальнейшего изучения. Справочный документ: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance.



### <a name="error-code--sqlunauthorizedaccess"></a>Код ошибки:  SqlUnauthorizedAccess

- **Сообщение**: `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Причина.** Учетные данные неверны, или учетная запись входа не может получить доступ к базе данных SQL.

- **Рекомендация**.  Убедитесь в том, что у учетной записи входа достаточно разрешений для доступа к базе данных SQL.


### <a name="error-code--sqlopenconnectiontimeout"></a>Код ошибки:  SqlOpenConnectionTimeout

- **Сообщение**: `Open connection to database timeout after '%timeoutValue;' seconds.`

- **Причина.** Мог произойти временный сбой базы данных SQL.

- **Рекомендация**: повторите попытку обновления строки подключения связанной службы с большим значением времени ожидания соединения.


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>Код ошибки:  SqlAutoCreateTableTypeMapFailed

- **Сообщение**: `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **Причина.** Таблица автоматического создания не соответствует требованию к источнику.

- **Рекомендация**.  Измените тип столбца в сопоставлениях или вручную создайте таблицу-приемник на целевом сервере.


### <a name="error-code--sqldatatypenotsupported"></a>Код ошибки:  SqlDataTypeNotSupported

- **Сообщение**: `A database operation failed. Check the SQL errors.`

- **Причина.** Если эта ошибка возникает в источнике SQL и связана с переполнением SqlDateTime, значение данных превышает диапазон логического типа (01.01.1753 12:00:00–31.12.9999 23:59:59).

- **Рекомендация**.  Приведите тип к строке в исходном SQL-запросе либо в сопоставлении столбцов действия копирования измените тип столбца на String.

- **Причина.** Если эта ошибка возникает в приемнике SQL и связана с переполнением SqlDateTime, значение данных превышает допустимый диапазон в таблице-приемнике.

- **Рекомендация**.  Обновите соответствующий тип столбца на тип datetime2 в таблице-приемнике.


### <a name="error-code--sqlinvaliddbstoredprocedure"></a>Код ошибки:  SqlInvalidDbStoredProcedure

- **Сообщение**: `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Причина.** Указанная хранимая процедура недопустима. Возможно, она не вернула никаких данных.

- **Рекомендация**.  Проверьте хранимую процедуру средствами SQL. Убедитесь в том, что хранимая процедура может возвращать данные.


### <a name="error-code--sqlinvaliddbquerystring"></a>Код ошибки:  SqlInvalidDbQueryString

- **Сообщение**: `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Причина.** Указанный запрос SQL недопустим. Возможно, запрос не вернул никаких данных.

- **Рекомендация**.  Проверьте SQL-запрос средствами SQL. Убедитесь в том, что запрос может возвращать данные.


### <a name="error-code--sqlinvalidcolumnname"></a>Код ошибки:  SqlInvalidColumnName

- **Сообщение**: `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Причина.** Не удается найти столбец. Возможно, конфигурация неправильна.

- **Рекомендация**.  Проверьте столбец в запросе, структуру в наборе данных и сопоставления в действии.


### <a name="error-code--sqlcolumnnamemismatchbycasesensitive"></a>Код ошибки:  SqlColumnNameMismatchByCaseSensitive

- **Сообщение**: `Column '%column;' in DataSet '%dataSetName;' cannot be found in physical SQL Database. Column matching is case-sensitive. Column '%columnInTable;' appears similar. Check the DataSet(s) configuration to proceed further.`


### <a name="error-code--sqlbatchwritetimeout"></a>Код ошибки:  SqlBatchWriteTimeout

- **Сообщение**: `Timeouts in SQL write operation.`

- **Причина.** Мог произойти временный сбой базы данных SQL.

- **Рекомендация**: повторная попытка. Если проблема повторяется, обратитесь в службу поддержки Azure SQL.


### <a name="error-code--sqlbatchwritetransactionfailed"></a>Код ошибки:  SqlBatchWriteTransactionFailed

- **Сообщение**: `SQL transaction commits failed`

- **Причина.** Если в сведениях об исключении постоянно указывается превышение времени ожидания транзакций, задержка в сети между средой выполнения интеграции и базой данных больше порогового значения по умолчанию, равного 30 секундам.

- **Рекомендация**.  Измените строку подключения связанной службы SQL, указав значение времени ожидания подключения не менее 120, а затем повторно выполните действие.

- **Причина.** Если в сведениях об исключении периодически сообщается о нарушении соединения SQL, это может быть временным сбоем сети или проблемой на стороне базы данных SQL.

- **Рекомендация**: Повторите действие и проверьте метрики на стороне базы данных SQL.


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>Код ошибки:  SqlBulkCopyInvalidColumnLength

- **Сообщение**: `SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **Причина.** Не удалось выполнить массовое копирование SQL, так как от BCP-клиента получена недопустимая длина столбца.

- **Рекомендация**.  Чтобы узнать, в какой строке возникает проблема, включите функцию отказоустойчивости для действия копирования, которая может перенаправлять проблемные строки в хранилище для дальнейшего изучения. Справочный документ: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance.


### <a name="error-code--sqlconnectionisclosed"></a>Код ошибки:  SqlConnectionIsClosed

- **Сообщение**: `The connection is closed by SQL Database.`

- **Причина.** Соединение SQL закрывается базой данных SQL при большом количестве параллельных выполнений и разрыве соединения сервером.

- **Рекомендация**.  Удаленный сервер закрыл соединение SQL. Повторная попытка. Если проблема повторяется, обратитесь в службу поддержки Azure SQL.


### <a name="error-code--sqlcreatetablefailedunsupportedtype"></a>Код ошибки:  SqlCreateTableFailedUnsupportedType

- **Сообщение**: `Type '%type;' in source side cannot be mapped to a type that supported by sink side(column name:'%name;') in autocreate table.`


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Сообщение об ошибке: Ошибка при преобразовании строки символов в тип uniqueidentifier

- **Симптомы**. при копировании данных из источника табличных данных (например, SQL Server) в Azure синапсе Analytics с помощью промежуточного копирования и polybase вы столкнулись со следующей ошибкой:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into Azure Synapse Analytics.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **Причина**: Azure синапсе Analytics polybase не может преобразовать пустую строку в GUID.

- **Решение**. В приемнике действия копирования в разделе параметров PolyBase задайте для параметра, определяющего **использование типа по умолчанию**, значение false.

### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Сообщение об ошибке: Ожидаемый тип данных: DECIMAL(x,x), ошибочное значение

- **Симптомы**. при копировании данных из источника табличных данных (например, SQL Server) в Azure синапсе Analytics с помощью промежуточного копирования и polybase вы столкнулись со следующей ошибкой:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into Azure Synapse Analytics.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **Причина**: Azure синапсе Analytics polybase не может вставить пустую строку (значение null) в десятичный столбец.

- **Решение**. В приемнике действия копирования в разделе параметров PolyBase задайте для параметра, определяющего **использование типа по умолчанию**, значение false.

### <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>Сообщение об ошибке: сообщение исключения Java: Хдфсбридже:: Креатерекордреадер

- **Симптомы**. вы копируете данные в Azure синапсе Analytics с помощью polybase и нажмем следующую ошибку:

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **Причина.** Возможная причина заключается в том, что схема (общая ширина столбца) слишком велика (больше 1 МБ). Проверьте схему целевой таблицы Azure синапсе Analytics, добавив размер всех столбцов:

    - Int -> 4 байта
    - Bigint -> 8 байт
    - Varchar(n),char(n),binary(n), varbinary(n) -> n байт
    - Nvarchar(n), nchar(n) -> n*2 байта
    - Date -> 6 байт
    - Datetime/(2), smalldatetime -> 16 байт
    - Datetimeoffset -> 20 байт
    - Decimal -> 19 байт
    - Float -> 8 байт
    - Money -> 8 байт
    - Smallmoney -> 4 байта
    - Real -> 4 байта
    - Smallint -> 2 байта
    - Time -> 12 байт
    - Tinyint -> 1 байт

- **Решение**. Уменьшите ширину столбца, чтобы она не превышала 1 МБ.

- Или используйте подход с массовыми вставками, отключив PolyBase.

### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Сообщение об ошибке: Условие, указанное с помощью условных заголовков HTTP, не выполнено.

- **Симптомы**. вы используете SQL Query для извлечения данных из Azure синапсе Analytics и нажмем следующую ошибку:

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **Причина**: ошибка при обращении к Azure синапсе Analytics при выполнении запросов к внешней таблице в службе хранилища Azure.

- **Решение**. Выполните тот же запрос в SSMS и проверьте, отображается ли тот же результат. Если да, отправьте запрос в службу поддержки в Azure синапсе Analytics и укажите имя сервера и базы данных Azure синапсе Analytics для дальнейшего устранения неполадок.
            

## <a name="delimited-text-format"></a>Формат текста с разделителями

### <a name="error-code--delimitedtextcolumnnamenotallownull"></a>Код ошибки:  DelimitedTextColumnNameNotAllowNull

- **Сообщение**: `The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **Причина.** Если в действии задать firstRowAsHeader, в качестве имени столбца будет использоваться первая строка. Эта ошибка означает, что первая строка содержит пустое значение. Например: "Column a, Столбецb".

- **Рекомендация**.  Проверьте первую строку и исправьте пустое значение.


### <a name="error-code--delimitedtextmorecolumnsthandefined"></a>Код ошибки:  DelimitedTextMoreColumnsThanDefined

- **Сообщение**: `Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %columnCount;.`

- **Причина**: счетчик столбцов проблемной строки больше, чем число столбцов в первой строке. Это может быть вызвано проблемой с данными или неправильными настройками разделителей столбцов или символов кавычек.

- **Рекомендация**: получение количества строк в сообщении об ошибке, проверка столбца строки и исправление данных.

- **Причина.** Если ожидаемое число столбцов равно 1 в сообщении об ошибке, возможно, указаны неправильные параметры сжатия или форматирования, что привело к неправильному анализу файлов Фабрикой данных Azure.

- **Рекомендация**.  Проверьте параметры формата на соответствие исходным файлам.

- **Причина.** Если источником является папка, возможно, файлы в указанной папке имеют другую схему.

- **Рекомендация**.  Убедитесь в том, что файлы в указанной папке имеют идентичную схему.


### <a name="error-code--delimitedtextincorrectrowdelimiter"></a>Код ошибки:  DelimitedTextIncorrectRowDelimiter

- **Сообщение**: `The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--delimitedtexttoolargecolumncount"></a>Код ошибки:  DelimitedTextTooLargeColumnCount

- **Сообщение**: `Column count reaches limitation when deserializing csv file. Maximum size is '%size;'. Check the column delimiter and row delimiter provided. (Column delimiter: '%columnDelimiter;', Row delimiter: '%rowDelimiter;')`


### <a name="error-code--delimitedtextinvalidsettings"></a>Код ошибки:  DelimitedTextInvalidSettings

- **Сообщение**: `%settingIssues;`



## <a name="dynamics-365common-data-servicedynamics-crm"></a>Dynamics 365, Common Data Service, Dynamics CRM

### <a name="error-code--dynamicscreateserviceclienterror"></a>Код ошибки:  DynamicsCreateServiceClientError

- **Сообщение**: `This is a transient issue on dynamics server side. Try to rerun the pipeline.`

- **Причина.** Это временная проблема на стороне сервера Dynamics.

- **Рекомендация**.  Перезапустите конвейер. Если ошибка сохраняется, попробуйте уменьшить степень параллелизма. Если это не помогло, обратитесь в службу поддержки Dynamics.

## <a name="excel-format"></a>Формат Excel

### <a name="timeout-or-slow-performance-when-parsing-large-excel-file"></a>Время ожидания или снижение производительности при анализе большого файла Excel

- **Симптомы**:

    1. При создании набора данных Excel и импорте схемы из подключения или хранилища, предварительного просмотра данных, списка или обновления листов может возникнуть ошибка времени ожидания, если файл Excel имеет большой размер.
    2. При использовании действия копирования для копирования данных из большого файла Excel (>= 100 МБ) в другое хранилище данных может наблюдаться снижение производительности или проблем с производительностью.

- **Причина**. 

    1. Для таких операций, как импорт схемы, просмотр данных и вывод списка листов в наборе данных Excel, время ожидания равно 100s и статично. Для большого файла Excel эти операции могут не завершаться в течение времени ожидания.

    2. Действие копирования ADF считывает весь файл Excel в память, а затем находит указанный лист и ячейки для чтения данных. Это поведение вызвано тем, что в основном ADF-файле пакета SDK используется.

- **Решение**. 

    1. Для импорта схемы можно создать файл примера меньшего размера, который является подмножеством исходного файла, и выбрать «импортировать схему из образца файла» вместо «импортировать схему из подключения или хранилища».

    2. Для перечисления ворксит в раскрывающемся списке лист можно щелкнуть "Изменить" и ввести вместо этого имя или индекс листа.

    3. Чтобы скопировать большой файл Excel (>100 МБ) в другое хранилище, можно использовать источник Excel потока данных, который прочитает и улучшит потоковую передачу.

## <a name="json-format"></a>Формат JSON

### <a name="error-code--jsoninvalidarraypathdefinition"></a>Код ошибки:  JsonInvalidArrayPathDefinition

- **Сообщение**: `Error occurred when deserializing source JSON data. Check whether the JsonPath in JsonNodeReference and JsonPathDefintion is valid.`


### <a name="error-code--jsonemptyjobjectdata"></a>Код ошибки:  JsonEmptyJObjectData

- **Сообщение**: `The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--jsonnullvalueinpathdefinition"></a>Код ошибки:  JsonNullValueInPathDefinition

- **Сообщение**: `Null JSONPath detected in JsonPathDefinition.`


### <a name="error-code--jsonunsupportedhierarchicalcomplexvalue"></a>Код ошибки:  JsonUnsupportedHierarchicalComplexValue

- **Сообщение**: `The retrieved type of data %data; with value %value; is not supported yet. Please either remove the targeted column '%name;' or enable skip incompatible row to skip the issue rows.`


### <a name="error-code--jsonconflictpartitiondiscoveryschema"></a>Код ошибки:  JsonConflictPartitionDiscoverySchema

- **Сообщение**: `Conflicting partition column names detected.'%schema;', '%partitionDiscoverySchema;'`


### <a name="error-code--jsoninvaliddataformat"></a>Код ошибки:  JsonInvalidDataFormat

- **Сообщение**: `Error occurred when deserializing source JSON file '%fileName;'. Check if the data is in valid JSON object format.`


### <a name="error-code--jsoninvaliddatamixedarrayandobject"></a>Код ошибки:  JsonInvalidDataMixedArrayAndObject

- **Сообщение**: `Error occurred when deserializing source JSON file '%fileName;'. The JSON format doesn't allow mixed arrays and objects.`



## <a name="parquet-format"></a>Формат Parquet

### <a name="error-code--parquetjavainvocationexception"></a>Код ошибки:  ParquetJavaInvocationException

- **Сообщение**: `An error occurred when invoking java, message: %javaException;.`

- **Причина.** Если в сообщении об ошибке есть строки "java.lang.OutOfMemory", "пространство кучи Java" и "doubleCapacity", обычно это ошибка управления памятью в старой версии среды выполнения интеграции.

- **Рекомендация**. Если вы используете локально размещенную Integration Runtime и версия более ранняя, чем 3.20.7159.1, рекомендуется выполнить обновление до последней версии.

- **Причина.** Если в сообщении об ошибке есть строка "java.lang.OutOfMemory", среда выполнения интеграции имеет недостаточно ресурсов для обработки файлов.

- **Рекомендация**.  Ограничьте количество параллельных выполнений в среде выполнения интеграции. Для локальной среды выполнения интеграции увеличьте ресурсы компьютера. Объем его памяти должен быть не меньше 8 ГБ.

- **Причина.** Если в сообщении об ошибке есть слово NullPointerReference, возможно, это временная ошибка.

- **Рекомендация**: повторная попытка. Если проблема сохранится, обратитесь в службу поддержки.


### <a name="error-code--parquetinvalidfile"></a>Код ошибки:  ParquetInvalidFile

- **Сообщение**: `File is not a valid parquet file.`

- **Причина.** Ошибка в файле Parquet.

- **Рекомендация**.  Убедитесь в том, что входные данные являются допустимым файлом Parquet.


### <a name="error-code--parquetnotsupportedtype"></a>Код ошибки:  ParquetNotSupportedType

- **Сообщение**: `Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **Причина.** Формат Parquet не поддерживается в Фабрике данных Azure.

- **Рекомендация**.  Тщательно проверьте исходные данные. См. документ https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs.


### <a name="error-code--parquetmisseddecimalprecisionscale"></a>Код ошибки:  ParquetMissedDecimalPrecisionScale

- **Сообщение**: `Decimal Precision or Scale information is not found in schema for column: %column;.`

- **Причина.** Была предпринята попытка проанализировать точность числа и количество знаков после запятой, но эти сведения не были предоставлены.

- **Рекомендация**.  Источник не возвращает правильные сведения о точности и количестве знаков после запятой. Проверьте наличие проблем с точностью столбца и количеством знаков после запятой в нем.


### <a name="error-code--parquetinvaliddecimalprecisionscale"></a>Код ошибки:  ParquetInvalidDecimalPrecisionScale

- **Сообщение**: `Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **Причина.** Недопустимая схема.

- **Рекомендация**.  Проверьте наличие проблем с точностью столбца и количеством знаков после запятой в нем.


### <a name="error-code--parquetcolumnnotfound"></a>Код ошибки:  ParquetColumnNotFound

- **Сообщение**: `Column %column; does not exist in Parquet file.`

- **Причина.** Схема источника не соответствует схеме приемника.

- **Рекомендация**.  Проверьте сопоставления в действии. Убедитесь в том, что исходный столбец может быть сопоставлен с нужным столбцом приемника.


### <a name="error-code--parquetinvaliddataformat"></a>Код ошибки:  ParquetInvalidDataFormat

- **Сообщение**: `Incorrect format of %srcValue; for converting to %dstType;.`

- **Причина.** Невозможно преобразовать данные в тип, указанный в mappings.source.

- **Рекомендация**.  Тщательно проверьте исходные данные или укажите правильный тип данных для этого столбца в сопоставлении столбцов действия копирования. См. документ https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs.


### <a name="error-code--parquetdatacountnotmatchcolumncount"></a>Код ошибки:  ParquetDataCountNotMatchColumnCount

- **Сообщение**: `The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **Причина.** Несоответствие числа столбцов в источнике и приемнике

- **Рекомендация**.  Тщательно проверьте, совпадает ли в сопоставлении число столбцов в источнике с числом столбцов в приемнике.


### <a name="error-code--parquetdatatypenotmatchcolumntype"></a>Код ошибки:  ParquetDataTypeNotMatchColumnType

- **Сообщение**. Тип данных %srcType; не совпадает с указанным типом %dstType; столбца "%columnIndex;".

- **Причина.** Невозможно преобразовать данные из источника в тип, определенный в приемнике.

- **Рекомендация**: укажите правильный тип в сопоставлении. Sink.


### <a name="error-code--parquetbridgeinvaliddata"></a>Код ошибки:  ParquetBridgeInvalidData

- **Сообщение**: `%message;`

- **Причина.** Значение данных превысило ограничение.

- **Рекомендация**: повторная попытка. Если проблема не исчезла, обратитесь к нам.


### <a name="error-code--parquetunsupportedinterpretation"></a>Код ошибки:  ParquetUnsupportedInterpretation

- **Сообщение**: `The given interpretation '%interpretation;' of parquet format is not supported.`

- **Причина.** Неподдерживаемый сценарий

- **Рекомендация**.  Параметр ParquetInterpretFor не должен иметь значение sparkSql.


### <a name="error-code--parquetunsupportfilelevelcompressionoption"></a>Код ошибки:  ParquetUnsupportFileLevelCompressionOption

- **Сообщение**: `File level compression is not supported for Parquet.`

- **Причина.** Неподдерживаемый сценарий

- **Рекомендация**.  Удалите CompressionType в полезных данных.


## <a name="rest"></a>REST

### <a name="unexpected-network-response-from-rest-connector"></a>Непредвиденный сетевой ответ от соединителя RESTFUL

- **Симптомы**. Конечная точка иногда получает непредвиденный ответ (400/401/403/500) из соединителя RESTful.

- **Причина**: Соединитель источника RESTful использует URL-адрес и метод HTTP, заголовок или тело из связанной службы или набора данных, а также источник копирования в качестве параметров при формировании HTTP-запроса. Скорее всего, эта неполадка вызвана некоторыми ошибками в одном или нескольких указанных параметрах.

- **Решение**. 
    - Используйте параметр "прилистывание" в окне cmd, чтобы проверить, является ли значение параметра "причина" или "нет" (заголовки **Accept** и **User-Agent** всегда должны включаться):
        ```
        curl -i -X <HTTP method> -H <HTTP header1> -H <HTTP header2> -H "Accept: application/json" -H "User-Agent: azure-data-factory/2.0" -d '<HTTP body>' <URL>
        ```
      Если команда возвращает один и тот же непредвиденный ответ, исправьте параметры с параметром "листывание", пока не вернет ожидаемый ответ. 

      Кроме того, для более подробного использования команды можно использовать параметр "прилистывание — Справка".

    - Если только соединитель RESTFUL для ADF возвращает непредвиденный ответ, обратитесь в службу поддержки Майкрософт для дальнейшего устранения неполадок.
    
    - Обратите внимание, что "изогнутое" может не подойти для воспроизведения проблемы проверки SSL-сертификата. В некоторых сценариях команда "изогнутая" была выполнена успешно без возникновения каких-либо проблем с проверкой SSL-сертификата. Но если в браузере выполняется один и тот же URL-адрес, SSL-сертификат на самом деле не возвращается в первую очередь, чтобы клиент установил отношение доверия с сервером.

      Для описанного выше варианта рекомендуются такие средства, как **posts** и **Fiddler** .


## <a name="general-copy-activity-error"></a>Общая ошибка действия копирования

### <a name="error-code--jrenotfound"></a>Код ошибки:  JreNotFound

- **Сообщение**: `Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **Причина.** Локальной среде выполнения интеграции не удается найти среду выполнения Java. Среда выполнения Java требуется для чтения определенного источника.

- **Рекомендация**.  Проверьте среду выполнения интеграции. См. справочный документ https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime


### <a name="error-code--wildcardpathsinknotsupported"></a>Код ошибки:  WildcardPathSinkNotSupported

- **Сообщение**: `Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **Причина.** Набор данных приемника не поддерживает подстановочные знаки.

- **Рекомендация**.  Проверьте набор данных приемника и исправьте путь, убрав подстановочные знаки.


### <a name="error-code--mappinginvalidpropertywithemptyvalue"></a>Код ошибки:  MappingInvalidPropertyWithEmptyValue

- **Сообщение**: `One or more '%sourceOrSink;' in copy activity mapping doesn't point to any data. Choose one of the three properties 'name', 'path' and 'ordinal' to reference columns/fields.`


### <a name="error-code--mappinginvalidpropertywithnamepathandordinal"></a>Код ошибки:  MappingInvalidPropertyWithNamePathAndOrdinal

- **Сообщение**: `Mixed properties are used to reference '%sourceOrSink;' columns/fields in copy activity mapping. Please only choose one of the three properties 'name', 'path' and 'ordinal'. The problematic mapping setting is 'name': '%name;', 'path': '%path;','ordinal': '%ordinal;'.`


### <a name="error-code--mappingduplicatedordinal"></a>Код ошибки:  MappingDuplicatedOrdinal

- **Сообщение**: `Copy activity 'mappings' has duplicated ordinal value "%Ordinal;". Fix the setting in 'mappings'.`


### <a name="error-code--mappinginvalidordinalforsinkcolumn"></a>Код ошибки:  MappingInvalidOrdinalForSinkColumn

- **Сообщение**: `Invalid 'ordinal' property for sink column under 'mappings' property. Ordinal: %Ordinal;.`


## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительных сведений об устранении неполадок воспользуйтесь следующими ресурсами:

*  [Блог о Фабрике данных](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Запросы на добавление функции в Фабрику данных](https://feedback.azure.com/forums/270578-data-factory)
*  [Видео по Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Страница вопросов (раздел вопросов и ответов на сайте Майкрософт)](/answers/topics/azure-data-factory.html)
*  [Форум Stack Overflow по Фабрике данных](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Сведения о Фабрике данных в Twitter](https://twitter.com/hashtag/DataFactory)
