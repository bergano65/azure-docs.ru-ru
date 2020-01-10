---
title: Устранение неполадок соединителей фабрики данных Azure
description: Узнайте, как устранять неполадки соединителя в фабрике данных Azure.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 01/09/2020
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: 9f3a13a097d7cce87aead4ec2d76ce7cbbb1a206
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75778232"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Устранение неполадок соединителей фабрики данных Azure

В этой статье рассматриваются распространенные способы устранения неполадок соединителей в фабрике данных Azure.
  

## <a name="azure-blob-storage"></a>Хранилище BLOB-объектов Azure

### <a name="error-code--azurebloboperationfailed"></a>Код ошибки: Азуреблобоператионфаилед

- **Сообщение**: `Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **Причина**: ошибка при обращении к операции хранилища BLOB-объектов.

- **Рекомендация**: Проверьте сведения об ошибке в подробностях. См. справочный документ по BLOB-объектам: https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes. Обратитесь в службу хранилища, если вам нужна помощь.


### <a name="error-code--azureblobservicenotreturnexpecteddatalength"></a>Код ошибки: Азуреблобсервиценотретурнекспектеддаталенгс

- **Сообщение**: `Error occurred when trying to fetch the blob '%name;'. This could be a transient issue and you may rerun the job. If it fails again continuously, contact customer support.`


### <a name="error-code--azureblobnotsupportmultiplefilesintosingleblob"></a>Код ошибки: Азуреблобнотсуппортмултиплефилесинтосинглеблоб

- **Сообщение**: `Transferring multiple files into a single Blob is not supported. Currently only single file source is supported.`


### <a name="error-code--azurestorageoperationfailedconcurrentwrite"></a>Код ошибки: Азуресторажеоператионфаиледконкуррентврите

- **Сообщение**: `Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Сообщение об ошибке: слишком большой размер запроса

- **Симптомы**. данные копируются в Azure Cosmos DB с размером пакета записи по умолчанию и ошибкой *"**размер запроса слишком велик**"* .

- **Причина**: Cosmos DB ограничивает размер одного запроса до 2 МБ. Формула:, размер запроса = один размер документа * размер пакета записи. Если размер документа велик, то поведение по умолчанию приведет к слишком большому размеру запроса. Размер пакета записи можно настроить.

- **Решение**. в приемнике действия копирования Сократите значение параметра "запись размера пакета" (значение по умолчанию — 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Сообщение об ошибке: нарушение ограничения уникального индекса

- **Симптомы**. при копировании данных в Cosmos DB вы столкнулись со следующей ошибкой:

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **Причина**. возможны две причины:

    - При использовании инструкции **INSERT** как режима записи эта ошибка означает, что исходные данные содержат строки или объекты с ОДИНАКОВым идентификатором.

    - Если в качестве поведения записи используется **Upsert** , а для контейнера задан другой уникальный ключ, то эта ошибка означает, что исходные данные содержат строки или объекты с разными идентификаторами, но одно значение для определенного уникального ключа.

- **Решение**. 

    - Для cause1 задайте **Upsert** в качестве поведения записи.
    - Для причины 2 Убедитесь, что каждый документ имеет разное значение для определенного уникального ключа.

### <a name="error-message-request-rate-is-large"></a>Сообщение об ошибке: частота запросов велика

- **Симптомы**. при копировании данных в Cosmos DB вы столкнулись со следующей ошибкой:

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **Причина**: используемые единицы запроса больше, чем доступный ru, настроенный в Cosmos DB. Узнайте [, как](../cosmos-db/request-units.md#request-unit-considerations)Cosmos DB вычислит единицы между собой.

- **Решение**. ниже приведены два решения.

    1. **Увеличьте значение контейнера ru** в Cosmos DB, что улучшит производительность действия копирования, но требует дополнительных затрат на Cosmos DB. 

    2. Сократите **writeBatchSize** до меньшего значения (например, 1000) и задайте для **parallelCopies** меньшее значение, например 1, что приведет к снижению производительности при выполнении копирования, но не требует дополнительных затрат на Cosmos DB.

### <a name="column-missing-in-column-mapping"></a>Столбец отсутствует в сопоставлении столбцов

- **Симптомы**. при импорте схемы для Cosmos DB сопоставления столбцов отсутствуют некоторые столбцы. 

- **Причина**: ADF выводит схему из первых 10 Cosmos DB документов. Если некоторые столбцы или свойства не имеют значения в этих документах, они не будут обнаружены службой ADF, поэтому не отображаются.

- **Решение**. Вы можете настроить запрос следующим образом, чтобы обеспечить отображение столбца в результирующем наборе с пустым значением: (предположим, что в первых 10 документах отсутствует столбец "невозможное"). Кроме того, можно вручную добавить столбец для сопоставления.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Сообщение об ошибке: Гуидрепресентатион для модуля чтения — Кшарплегаци

- **Симптомы**. при копировании данных из поля Cosmos DB Монгоапи/MONGODB с UUID вы столкнулись со следующей ошибкой:

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **Причина**. Существует два способа представления UUID в BSON-Ууидстардард и ууидлегаци. По умолчанию Ууидлегаци используется для чтения данных. Если данные UUID в MongoDB имеют значение Ууидстандард, появится сообщение об ошибке.

- **Решение**. в строке подключения MongoDB добавьте параметр "**ууидрепресентатион = Standard**". Дополнительные сведения см. в разделе [строка подключения MongoDB](connector-mongodb.md#linked-service-properties).
            

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code--adlsgen2operationfailed"></a>Код ошибки: AdlsGen2OperationFailed

- **Сообщение**: `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Причина**: ADLS 2-го поколения выдает ошибку, указывающую на сбой операции.

- **Рекомендация**: проверьте подробное сообщение об ошибке, созданное ADLS 2-го поколения. Если это вызвано временным сбоем, повторите попытку. Если вам нужна дополнительная помощь, обратитесь в службу поддержки хранилища Azure и укажите идентификатор запроса в сообщении об ошибке.

- **Причина**: Если сообщение об ошибке содержит "запрещено", возможно, субъект-служба или управляемое удостоверение, которые вы используете, не имеют достаточных разрешений для доступа к ADLS 2-го поколения.

- **Рекомендация**: см. справочный документ: https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication.

- **Причина**: Если сообщение об ошибке содержит "InternalServerError", то ошибка возвращается ADLS 2-го поколения.

- **Рекомендация**. это может быть вызвано временной ошибкой, повторите попытку. Если проблема будет повторяться, обратитесь в службу поддержки хранилища Azure и укажите идентификатор запроса в сообщении об ошибке.


### <a name="error-code--adlsgen2invalidurl"></a>Код ошибки: AdlsGen2InvalidUrl

- **Сообщение**: `Invalid url '%url;' provided, expecting http[s]://<accountname>.dfs.core.windows.net.`


### <a name="error-code--adlsgen2invalidfolderpath"></a>Код ошибки: AdlsGen2InvalidFolderPath

- **Сообщение**: `The folder path is not specified. Cannot locate the file '%name;' under the ADLS Gen2 account directly. Please specify the folder path instead.`


### <a name="error-code--adlsgen2operationfailedconcurrentwrite"></a>Код ошибки: AdlsGen2OperationFailedConcurrentWrite

- **Сообщение**: `Error occurred when trying to upload a file. It's possible because you have multiple concurrent copy activities runs writing to the same file '%name;'. Check your ADF configuration.`


### <a name="error-code--adlsgen2timeouterror"></a>Код ошибки: AdlsGen2TimeoutError

- **Сообщение**: `Request to ADLS Gen2 account '%account;' met timeout error. It is mostly caused by the poor network between the Self-hosted IR machine and the ADLS Gen2 account. Check the network to resolve such error.`


## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Сообщение об ошибке: удаленный сервер вернул ошибку: (403) запрещено

- **Симптомы**. сбой действия копирования со следующей ошибкой: 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **Причина**: одна из возможных причин заключается в том, что используемый субъект-служба или управляемое удостоверение не имеет разрешения на доступ к определенной папке или файлу.

- **Решение**. Предоставьте соответствующие разрешения для всех папок и вложенных папок, которые необходимо скопировать. Обратитесь к [этому документу](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Сообщение об ошибке: не удалось получить маркер доступа с помощью субъекта-службы. Ошибка ADAL: service_unavailable

- **Симптомы**. сбой действия копирования со следующей ошибкой:

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **Причина**: когда сервер маркеров службы (STS), принадлежащий Azure Active Directory, недоступен, т. е. слишком занят для обработки запросов, он ВОЗВРАЩАЕТ ошибку HTTP 503. 

- **Решение**. повторно выполните действие копирования через несколько минут.
                  

## <a name="azure-sql-data-warehouseazure-sql-databasesql-server"></a>Хранилище данных SQL Azure, база данных SQL Azure или SQL Server

### <a name="error-code--sqlfailedtoconnect"></a>Код ошибки: Склфаиледтоконнект

- **Сообщение**: `Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`

- **Причина**: Если сообщение об ошибке содержит "SqlException", то база данных SQL выдает ошибку, указывающую на сбой некоторой конкретной операции.

- **Рекомендация**: выполните поиск по коду ошибки SQL в этом справочном документе для получения дополнительных сведений: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors. Если вам нужна дополнительная помощь, обратитесь в службу поддержки SQL Azure.

- **Причина**: Если сообщение об ошибке содержит "клиент с IP-адресом"... " не разрешен доступ к серверу ", и вы пытаетесь подключиться к базе данных SQL Azure, обычно это вызвано проблемой с брандмауэром базы данных SQL Azure.

- **Рекомендация**. в конфигурации брандмауэра Azure SQL Server включите параметр "разрешить доступ к службам Azure и ресурсам для доступа к этому серверу". Справочный документ: https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure.


### <a name="error-code--sqloperationfailed"></a>Код ошибки: Склоператионфаилед

- **Сообщение**: `A database operation failed. Please search error to get more details.`

- **Причина**: Если сообщение об ошибке содержит "SqlException", то база данных SQL выдает ошибку, указывающую на сбой некоторой конкретной операции.

- **Рекомендация**: Если ошибка SQL непонятна, попробуйте изменить базу данных на последний уровень совместимости "150". Он может вызвать ошибки SQL последней версии. Дополнительные сведения см. в документе: https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level?view=sql-server-ver15#backwardCompat.
        Для устранения неполадок, связанных с SQL, выполните поиск по коду ошибки SQL в этом справочном документе, чтобы получить дополнительные сведения: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors. Если вам нужна дополнительная помощь, обратитесь в службу поддержки SQL Azure.

- **Причина**: Если сообщение об ошибке содержит "пдвманажедтонативеинтеропексцептион", обычно это вызвано несоответствием размеров столбцов источника и приемника.

- **Рекомендация**: Проверьте размер столбцов источника и приемника. Если вам нужна дополнительная помощь, обратитесь в службу поддержки SQL Azure.

- **Причина**: Если сообщение об ошибке содержит "InvalidOperationException", обычно это вызвано недопустимыми входными данными.

- **Рекомендация**: чтобы узнать, какая строка сталкивается с проблемой, включите функцию отказоустойчивости в действии копирования, которая может переназначать проблемные строки в хранилище для дальнейшего изучения. Справочный документ: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance.


### <a name="error-code--sqlunauthorizedaccess"></a>Код ошибки: Склунаусоризедакцесс

- **Сообщение**: `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Причина**: учетные данные неверны, или учетная запись входа не может получить доступ к базе данных SQL.

- **Рекомендация**: Убедитесь, что учетная запись входа имеет достаточные разрешения для доступа к базе данных SQL.


### <a name="error-code--sqlopenconnectiontimeout"></a>Код ошибки: Склопенконнектионтимеаут

- **Сообщение**: `Open connection to database timeout after '%timeoutValue;' seconds.`

- **Причина**: может быть временным сбоем базы данных SQL.

- **Рекомендация**: повторите попытку, чтобы обновить строку подключения связанной службы с большим значением времени ожидания соединения.


### <a name="error-code--sqlautocreatetabletypemapfailed"></a>Код ошибки: Склаутокреатетаблетипемапфаилед

- **Сообщение**: `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **Причина**: таблица автоматического создания не может соответствовать требованиям к источнику.

- **Рекомендация**: обновите тип столбца в "сопоставлениях" или вручную создайте таблицу приемника на целевом сервере.


### <a name="error-code--sqldatatypenotsupported"></a>Код ошибки: Склдататипенотсуппортед

- **Сообщение**: `A database operation failed. Check the SQL errors.`

- **Причина**: Если неполадка возникает в ИСТОЧНИКе SQL, а ошибка связана с переполнением SqlDateTime, значение данных превышает диапазон типов логики (1/1/1753 12:00:00 AM-12/31/9999 11:59:59 РМ).

- **Рекомендация**: приведение типа к строке в исходном SQL-запросе или в сопоставлении столбцов действия копирования измените тип столбца на "String".

- **Причина**: Если неполадка возникает в ПРИЕМНИКе SQL, а ошибка связана с переполнением SqlDateTime, значение данных превышает допустимый диапазон в таблице приемника.

- **Рекомендация**: обновление соответствующего типа столбца до типа "datetime2" в таблице приемника.


### <a name="error-code--sqlinvaliddbstoredprocedure"></a>Код ошибки: Склинвалиддбсторедпроцедуре

- **Сообщение**: `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Причина**: указанная хранимая процедура недопустима. Это может быть вызвано тем, что хранимая процедура не возвращает никаких данных.

- **Рекомендация**: Проверьте хранимую процедуру средствами SQL. Убедитесь, что хранимая процедура может возвращать данные.


### <a name="error-code--sqlinvaliddbquerystring"></a>Код ошибки: Склинвалиддбкуеристринг

- **Сообщение**: `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Причина**: указан недопустимый SQL-запрос. Это может быть вызвано тем, что запрос не возвращает никаких данных.

- **Рекомендация**: Проверка SQL запроса средствами SQL. Убедитесь, что запрос может возвращать данные.


### <a name="error-code--sqlinvalidcolumnname"></a>Код ошибки: Склинвалидколумннаме

- **Сообщение**: `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Причина**: не удается найти столбец. Возможная неправильная конфигурация.

- **Рекомендация**: Проверьте столбец в запросе, "Structure" в наборе данных и "сопоставления" в действии.


### <a name="error-code--sqlcolumnnamemismatchbycasesensitive"></a>Код ошибки: Склколумннамемисматчбикасесенситиве

- **Сообщение**: `Column '%column;' in DataSet '%dataSetName;' cannot be found in physical SQL Database. Column matching is case-sensitive. Column '%columnInTable;' appears similar. Check the DataSet(s) configuration to proceed further.`


### <a name="error-code--sqlbatchwritetimeout"></a>Код ошибки: Склбатчвритетимеаут

- **Сообщение**: `Timeouts in SQL write operation.`

- **Причина**: может быть временным сбоем базы данных SQL.

- **Рекомендация**: повторите попытку. Если проблема воспроизводится, обратитесь в службу поддержки SQL Azure.


### <a name="error-code--sqlbatchwritetransactionfailed"></a>Код ошибки: Склбатчвритетрансактионфаилед

- **Сообщение**: `SQL transaction commits failed`

- **Причина**: Если сведения об исключении постоянно сообщают о превышении времени ожидания транзакций, задержка сети между средой выполнения интеграции и базой данных выше порогового значения по умолчанию — 30 секунд.

- **Рекомендация**: обновление строки подключения к связанной службе SQL с помощью значения "время ожидания подключения" равно 120 или выше и повторное выполнение действия.

- **Причина**. Если сведения об исключении не сообщают о нарушении соединения SqlConnection, это может привести к неустранимому сбою сети или к проблемам с базой данных SQL.

- **Рекомендация**: Повторите действие и проверьте метрики на стороне базы данных SQL.


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>Код ошибки: Склбулккопинвалидколумнленгс

- **Сообщение**: `SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **Причина**: не удалось выполнить групповое копирование SQL из-за получения недопустимой длины столбца от клиента bcp.

- **Рекомендация**: чтобы узнать, какая строка сталкивается с проблемой, включите функцию отказоустойчивости в действии копирования, которая может переназначать проблемные строки в хранилище для дальнейшего изучения. Справочный документ: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance.


### <a name="error-code--sqlconnectionisclosed"></a>Код ошибки: Склконнектионисклосед

- **Сообщение**: `The connection is closed by SQL Database.`

- **Причина**: соединение SQL закрывается базой данных SQL при высоком параллельном выполнении и разрыве соединения сервера.

- **Рекомендация**: удаленный сервер закрыл подключение SQL. Попробуйте еще раз. Если проблема воспроизводится, обратитесь в службу поддержки SQL Azure.


### <a name="error-code--sqlcreatetablefailedunsupportedtype"></a>Код ошибки: Склкреатетаблефаиледунсуппортедтипе

- **Сообщение**: `Type '%type;' in source side cannot be mapped to a type that supported by sink side(column name:'%name;') in autocreate table.`


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Сообщение об ошибке: не удалось выполнить преобразование из строки символов в uniqueidentifier

- **Симптомы**. при копировании данных из источника табличных данных (например, SQL Server) в хранилище данных SQL Azure с помощью промежуточного копирования и polybase вы столкнулись со следующей ошибкой:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **Причина**: Polybase хранилища данных SQL Azure не может преобразовать пустую строку в GUID.

- **Решение**. в приемнике действия копирования в разделе Параметры polybase задайте для параметра "**использовать тип по умолчанию**" значение false.

### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Сообщение об ошибке: ожидаемый тип данных: DECIMAL (x, x), ошибочное значение

- **Симптомы**. при копировании данных из источника табличных данных (например, SQL Server) в хранилище хранилища SQL с использованием промежуточного копирования и polybase вы столкнулись со следующей ошибкой:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **Причина**: Polybase хранилища данных SQL Azure не может вставить пустую строку (значение null) в десятичный столбец.

- **Решение**. в приемнике действия копирования в разделе Параметры polybase задайте для параметра "**использовать тип по умолчанию**" значение false.

### <a name="error-message-java-exception-messagehdfsbridgecreaterecordreader"></a>Сообщение об ошибке: сообщение исключения Java: Хдфсбридже:: Креатерекордреадер

- **Симптомы**. вы копируете данные в хранилище данных SQL Azure с помощью polybase и нажмем следующую ошибку:

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **Причина**. Возможная причина: схема (общая ширина столбца) слишком велика (больше 1 МБ). Проверьте схему целевой таблицы хранилища данных SQL, добавив размер всех столбцов:

    - Int-> 4 байта
    - Bigint — > 8 байт
    - Varchar (n), char (n), binary (n), varbinary (n) — > n байт
    - Nvarchar (n), nchar (n) — > n * 2 байта
    - Дата-> 6 байт
    - DateTime/(2), smalldatetime-> 16 байт
    - DateTimeOffset — > 20 байт
    - Десятичный-> 19 байт
    - Float-> 8 байт
    - Money-> 8 байт
    - Smallmoney-> 4 байта
    - Real-> 4 байта
    - Smallint — > 2 байта
    - Время — > 12 байт
    - Tinyint-> 1 байт

- **Решение**. Уменьшите ширину столбца, чтобы она была меньше 1 МБ.

- Или используйте метод "выполнить небольшую вставку", отключив polybase

### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Сообщение об ошибке: условие, указанное с помощью условных заголовков HTTP, не выполнено

- **Симптомы**. вы используете SQL Query для извлечения данных из хранилища данных SQL Azure и нажмем следующую ошибку:

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **Причина**: ошибка при обращении к хранилищу данных SQL Azure при выполнении запросов к внешней таблице в службе хранилища Azure.

- **Решение**. Выполните тот же запрос в SSMS и проверьте, отображается ли тот же результат. Если да, отправьте запрос в службу поддержки для Хранилища данных SQL Azure и укажите имя сервера и базы данных Хранилища данных SQL, чтобы устранить неполадки.
            

## <a name="delimited-text-format"></a>Текстовый формат с разделителями

### <a name="error-code--delimitedtextcolumnnamenotallownull"></a>Код ошибки: Делимитедтекстколумннаменоталловнулл

- **Сообщение**: `The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **Причина**: при задании "firstRowAsHeader" в действии первая строка будет использоваться в качестве имени столбца. Эта ошибка означает, что первая строка содержит пустое значение. Например: "Column a, Столбецb".

- **Рекомендация**: Проверьте первую строку и исправьте значение, если пустое значение.


### <a name="error-code--delimitedtextmorecolumnsthandefined"></a>Код ошибки: Делимитедтекстмореколумнссандефинед

- **Сообщение**: `Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %columnCount;.`

- **Причина**: количество столбцов в проблемной строке велико, чем число столбцов в первой строке. Это может быть вызвано проблемой с данными или неправильными параметрами символов-разделителей столбцов или кавычек.

- **Рекомендация**: получите сообщение о количестве строк в сообщении об ошибке, проверьте столбец строки и исправьте данные.

- **Причина**: Если ожидаемое число столбцов равно "1" в сообщении об ошибке, возможно, указано неправильное сжатие или параметры форматирования, что привело к неправильному анализу файлов в ADF.

- **Рекомендация**: Проверьте параметры формата, чтобы убедиться, что они соответствуют исходным файлам.

- **Причина**: Если источником является папка, то файлы в указанной папке могут иметь разные схемы.

- **Рекомендация**: Убедитесь, что файлы в указанной папке имеют идентичную схему.


### <a name="error-code--delimitedtextincorrectrowdelimiter"></a>Код ошибки: Делимитедтекстинкорректровделимитер

- **Сообщение**: `The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--delimitedtexttoolargecolumncount"></a>Код ошибки: Делимитедтексттуларжеколумнкаунт

- **Сообщение**: `Column count reaches limitation when deserializing csv file. Maximum size is '%size;'. Check the column delimiter and row delimiter provided. (Column delimiter: '%columnDelimiter;', Row delimiter: '%rowDelimiter;')`


### <a name="error-code--delimitedtextinvalidsettings"></a>Код ошибки: Делимитедтекстинвалидсеттингс

- **Сообщение**: `%settingIssues;`



## <a name="dynamics-365common-data-servicedynamics-crm"></a>Dynamics 365/Common Data Service/Dynamics CRM

### <a name="error-code--dynamicscreateserviceclienterror"></a>Код ошибки: Динамикскреатесервицеклиентеррор

- **Сообщение**: `This is a transient issue on dynamics server side. Try to rerun the pipeline.`

- **Причина**: это временная проблема на стороне Dynamics Server.

- **Рекомендация**: перезапустите конвейер. Если не удается устранить ошибку, попробуйте уменьшить параллелизм. Если все еще не удается, обратитесь в службу поддержки Dynamics.



## <a name="json-format"></a>Формат JSON

### <a name="error-code--jsoninvalidarraypathdefinition"></a>Код ошибки: Жсонинвалидаррайпасдефинитион

- **Сообщение**: `Error occurred when deserializing source JSON data. Check whether the JsonPath in JsonNodeReference and JsonPathDefintion is valid.`


### <a name="error-code--jsonemptyjobjectdata"></a>Код ошибки: Жсонемптижобжектдата

- **Сообщение**: `The specified row delimiter %rowDelimiter; is incorrect. Cannot detect a row after parse %size; MB data.`


### <a name="error-code--jsonnullvalueinpathdefinition"></a>Код ошибки: Жсоннуллвалуеинпасдефинитион

- **Сообщение**: `Null JSONPath detected in JsonPathDefinition.`


### <a name="error-code--jsonunsupportedhierarchicalcomplexvalue"></a>Код ошибки: Жсонунсуппортедхиерарчикалкомплексвалуе

- **Сообщение**: `The retrieved type of data %data; with value %value; is not supported yet. Please either remove the targeted column '%name;' or enable skip incompatible row to skip the issue rows.`


### <a name="error-code--jsonconflictpartitiondiscoveryschema"></a>Код ошибки: Жсонконфликтпартитиондисковерисчема

- **Сообщение**: `Conflicting partition column names detected.'%schema;', '%partitionDiscoverySchema;'`


### <a name="error-code--jsoninvaliddataformat"></a>Код ошибки: Жсонинвалиддатаформат

- **Сообщение**: `Error occurred when deserializing source JSON file '%fileName;'. Check if the data is in valid JSON object format.`


### <a name="error-code--jsoninvaliddatamixedarrayandobject"></a>Код ошибки: Жсонинвалиддатамикседаррайандобжект

- **Сообщение**: `Error occurred when deserializing source JSON file '%fileName;'. The JSON format doesn't allow mixed arrays and objects.`



## <a name="parquet-format"></a>Формат Parquet

### <a name="error-code--parquetjavainvocationexception"></a>Код ошибки: Паркуетжаваинвокатионексцептион

- **Сообщение**: `An error occurred when invoking java, message: %javaException;.`

- **Причина**: Если сообщение об ошибке содержит "Java. lang. OutOfMemory", "пространство кучи Java" и "даублекапаЦити", обычно это является проблемой управления памятью в старой версии среды выполнения интеграции.

- **Рекомендация**. Если используется локально размещенный Integration Runtime и версия более ранняя, чем 3.20.7159.1, рекомендуется обновить до последней версии.

- **Причина**: Если сообщение об ошибке содержит "Java. lang. OutOfMemory", то среда выполнения интеграции не имеет достаточно ресурсов для обработки файлов.

- **Рекомендация**: Ограничьте количество одновременных запусков в среде выполнения интеграции. Для Integration Runtime с локальным размещением увеличьте масштаб до мощной машины с объемом памяти, равным или превышающим 8 ГБ.

- **Причина**: Если сообщение об ошибке содержит "нуллпоинтерреференце", возможно, это временная ошибка.

- **Рекомендация**: повторите попытку. Если проблема не исчезнет, обратитесь в службу поддержки.


### <a name="error-code--parquetinvalidfile"></a>Код ошибки: Паркуетинвалидфиле

- **Сообщение**: `File is not a valid parquet file.`

- **Причина**: ошибка в файле Parquet.

- **Рекомендация**: Убедитесь, что входные данные являются допустимым файлом Parquet.


### <a name="error-code--parquetnotsupportedtype"></a>Код ошибки: Паркуетнотсуппортедтипе

- **Сообщение**: `Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **Причина**: формат Parquet не поддерживается в фабрике данных Azure.

- **Рекомендация**: дважды проверьте исходные данные. Обратитесь к документу: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs.


### <a name="error-code--parquetmisseddecimalprecisionscale"></a>Код ошибки: ПаркуетмисседдеЦималпреЦисионскале

- **Сообщение**: `Decimal Precision or Scale information is not found in schema for column: %column;.`

- **Причина**: Попробуйте проанализировать точность и масштаб числа, но такие сведения не предоставляются.

- **Рекомендация**: "источник" не возвращает правильную точность и масштаб. Проверьте точность и масштаб столбца проблемы.


### <a name="error-code--parquetinvaliddecimalprecisionscale"></a>Код ошибки: ПаркуетинвалиддеЦималпреЦисионскале

- **Сообщение**: `Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **Причина**: недопустимая схема.

- **Рекомендация**: проверьте точность и масштаб столбца проблемы.


### <a name="error-code--parquetcolumnnotfound"></a>Код ошибки: Паркуетколумннотфаунд

- **Сообщение**: `Column %column; does not exist in Parquet file.`

- **Причина**: исходная схема не соответствует схеме приемника.

- **Рекомендация**: Проверьте се'маппингс "в" действии ". Убедитесь, что исходный столбец может быть сопоставлен с нужным столбцом приемника.


### <a name="error-code--parquetinvaliddataformat"></a>Код ошибки: Паркуетинвалиддатаформат

- **Сообщение**: `Incorrect format of %srcValue; for converting to %dstType;.`

- **Причина**: данные не могут быть преобразованы в тип, указанный в сопоставлениях. источник

- **Рекомендация**: дважды проверьте исходные данные или укажите правильный тип данных для этого столбца в сопоставлении столбцов действия копирования. Обратитесь к документу: https://docs.microsoft.com/azure/data-factory/supported-file-formats-and-compression-codecs.


### <a name="error-code--parquetdatacountnotmatchcolumncount"></a>Код ошибки: Паркуетдатакаунтнотматчколумнкаунт

- **Сообщение**: `The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **Причина**: несоответствие количества столбцов в исходном столбце и приемника

- **Рекомендация**: число двойной проверки исходного столбца совпадает с числом столбцов приемника в "Mapping".


### <a name="error-code--parquetdatatypenotmatchcolumntype"></a>Код ошибки: Паркуетдататипенотматчколумнтипе

- **Сообщение**: тип данных% srcType; не соответствует заданному типу столбца% Дсттипе; в столбце "% columnIndex;".

- **Причина**: данные из источника не могут быть преобразованы в типизированные, определенные в приемнике

- **Рекомендация**: укажите правильный тип в сопоставлении. Sink.


### <a name="error-code--parquetbridgeinvaliddata"></a>Код ошибки: Паркуетбриджеинвалиддата

- **Сообщение**: `%message;`

- **Причина**: значение данных превышает ограничение

- **Рекомендация**: повторите попытку. Если проблему не удается устранить, свяжитесь с нами.


### <a name="error-code--parquetunsupportedinterpretation"></a>Код ошибки: Паркуетунсуппортединтерпретатион

- **Сообщение**: `The given interpretation '%interpretation;' of parquet format is not supported.`

- **Причина**: неподдерживаемый сценарий

- **Рекомендация**: "паркуетинтерпретфор" не должен быть "sparkSql".


### <a name="error-code--parquetunsupportfilelevelcompressionoption"></a>Код ошибки: Паркуетунсуппортфилелевелкомпрессионоптион

- **Сообщение**: `File level compression is not supported for Parquet.`

- **Причина**: неподдерживаемый сценарий

- **Рекомендация**: удалите "компрессионтипе" в полезных данных.



## <a name="general-copy-activity-error"></a>Общая ошибка действия копирования

### <a name="error-code--jrenotfound"></a>Код ошибки: Жренотфаунд

- **Сообщение**: `Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **Причина**: локальной среде выполнения интеграции не удается найти среду выполнения Java. Для чтения определенного источника требуется среда выполнения Java.

- **Рекомендация**: Проверьте среду выполнения интеграции, справочный документ: https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime


### <a name="error-code--wildcardpathsinknotsupported"></a>Код ошибки: Вилдкардпассинкнотсуппортед

- **Сообщение**: `Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **Причина**: в наборе данных приемника не поддерживается подстановочный знак.

- **Рекомендация**: Проверьте набор данных приемника и исправьте путь без подстановочного значения.


### <a name="error-code--mappinginvalidpropertywithemptyvalue"></a>Код ошибки: Маппингинвалидпропертивисемптивалуе

- **Сообщение**: `One or more '%sourceOrSink;' in copy activity mapping doesn't point to any data. Choose one of the three properties 'name', 'path' and 'ordinal' to reference columns/fields.`


### <a name="error-code--mappinginvalidpropertywithnamepathandordinal"></a>Код ошибки: Маппингинвалидпропертивиснамепасандординал

- **Сообщение**: `Mixed properties are used to reference '%sourceOrSink;' columns/fields in copy activity mapping. Please only choose one of the three properties 'name', 'path' and 'ordinal'. The problematic mapping setting is 'name': '%name;', 'path': '%path;','ordinal': '%ordinal;'.`


### <a name="error-code--mappingduplicatedordinal"></a>Код ошибки: Маппингдупликатедординал

- **Сообщение**: `Copy activity 'mappings' has duplicated ordinal value "%Ordinal;". Fix the setting in 'mappings'.`


### <a name="error-code--mappinginvalidordinalforsinkcolumn"></a>Код ошибки: Маппингинвалидординалфорсинкколумн

- **Сообщение**: `Invalid 'ordinal' property for sink column under 'mappings' property. Ordinal: %Ordinal;.`


## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительных сведений об устранении неполадок воспользуйтесь следующими ресурсами:

*  [Блог фабрики данных](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Запросы функций фабрики данных](https://feedback.azure.com/forums/270578-data-factory)
*  [Видеоролики по Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Форум MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Форум Stack Overflow для фабрики данных](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Сведения о фабрике данных в Twitter](https://twitter.com/hashtag/DataFactory)
            
