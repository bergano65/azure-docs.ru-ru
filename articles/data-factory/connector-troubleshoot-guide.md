---
title: Устранение неполадок соединителей фабрики данных Azure
description: Узнайте, как устранять неполадки соединителя в фабрике данных Azure.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/26/2019
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: 218031830a7516dfd539e1c0b9b665807822f38d
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533151"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Устранение неполадок соединителей фабрики данных Azure

В этой статье рассматриваются распространенные способы устранения неполадок соединителей в фабрике данных Azure.

## <a name="azure-data-lake-storage"></a>Хранилище Azure Data Lake.

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

## <a name="sftp"></a>SFTP

### <a name="error-message-invalid-sftp-credential-provided-for-sshpublickey-authentication-type"></a>Сообщение об ошибке: для типа проверки подлинности "SshPublicKey" указаны недопустимые учетные данные SFTP

- **Симптомы**. вы используете проверку подлинности `SshPublicKey` и нажмем следующую ошибку:

    ```
    Invalid Sftp credential provided for 'SshPublicKey' authentication type
    ```

- **Причина**. возможны три причины:

    1. Если вы используете пользовательский интерфейс создания ADF для создания связанной службы SFTP, эта ошибка означает, что закрытый ключ, который вы решили использовать, имеет неправильный формат. Вы можете использовать формат PKCS # 8 закрытого ключа SSH, а Обратите внимание, что ADF поддерживает только традиционный формат ключей SSH. В частности, разница между форматом PKCS # 8 и традиционным форматом ключа — это содержимое ключа PKCS # 8, начинающееся с " *-----Begin encrypted закрытый ключ-----* ", а традиционный формат ключа начинается с " *-----Begin RSA закрытый ключ-----* ".
    2. Если вы используете Azure Key Vault для хранения содержимого закрытого ключа или используете программный способ для создания связанной службы SFTP, эта ошибка означает, что содержимое закрытого ключа неверно, возможно, это не в кодировке Base64.
    3. Недопустимое содержимое учетных данных или закрытого ключа.

- **Решение**. 

    - Чтобы вызвать #1, выполните следующие команды, чтобы преобразовать ключ в традиционный формат ключа, а затем используйте его в пользовательском интерфейсе создания ADF.

        ```
        # Decrypt the pkcs8 key and convert the format to traditional key format
        openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file

        chmod 600 traditional_format_key_file

        # Re-encrypte the key file using passphrase
        ssh-keygen -f traditional_format_key_file -p
        ```

    - Для #2 создания такой строки клиент может использовать следующие два способа:
    - Использование стороннего средства преобразования Base64. Вставьте содержимое закрытого ключа в такие средства, как [кодирование и декодирование Base64](https://www.base64encode.org/), закодировать его в строку формата Base64, затем вставить эту строку в хранилище ключей или использовать это значение для создания связанной службы SFTP программным способом.
    - Использование C# кода:

        ```c#
        byte[] keyContentBytes = File.ReadAllBytes(privateKeyPath);
        string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
        ```

    - Чтобы выполнить #3, проверьте правильность файла ключа или пароля с помощью других средств для проверки правильности доступа к серверу SFTP.
  

## <a name="azure-sql-data-warehouse--azure-sql-database--sql-server"></a>Хранилище данных SQL Azure \ SQL Server базы данных SQL Azure

### <a name="error-code--sqlfailedtoconnect"></a>Код ошибки: Склфаиледтоконнект

- **Сообщение**: `Cannot connect to SQL database: '%server;', Database: '%database;', User: '%user;'. Please check the linked service configuration is correct, and make sure the SQL database firewall allows the integration runtime to access.`

- **Причина**: Если сообщение об ошибке содержит "SqlException", то база данных SQL выдает ошибку, указывающую на сбой некоторой конкретной операции.

- **Рекомендация**: выполните поиск по коду ошибки SQL в этом справочном документе для получения дополнительных сведений: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors. Если вам нужна дополнительная помощь, обратитесь в службу поддержки SQL Azure.

- **Причина**: Если сообщение об ошибке содержит "клиент с IP-адресом"... " не разрешен доступ к серверу ", и вы пытаетесь подключиться к базе данных SQL Azure, обычно это вызвано проблемой с брандмауэром базы данных SQL Azure.

- **Рекомендация**. в конфигурации брандмауэра Azure SQL Server включите параметр "разрешить доступ к службам Azure и ресурсам для доступа к этому серверу". Справочный документ: https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure.


### <a name="error-code--sqloperationfailed"></a>Код ошибки: Склоператионфаилед

- **Сообщение**: `A database operation failed. Please search error to get more details.`

- **Причина**: Если сообщение об ошибке содержит "SqlException", то база данных SQL выдает ошибку, указывающую на сбой некоторой конкретной операции.

- **Рекомендация**: выполните поиск по коду ошибки SQL в этом справочном документе для получения дополнительных сведений: https://docs.microsoft.com/sql/relational-databases/errors-events/database-engine-events-and-errors. Если вам нужна дополнительная помощь, обратитесь в службу поддержки SQL Azure.

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

- **Сообщение**: `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(colunm name:'%colunmName;') in auto-create table.`

- **Причина**: таблица автоматического создания не может соответствовать требованиям к источнику.

- **Рекомендация**: обновите тип столбца в "сопоставлениях" или вручную создайте таблицу приемника на целевом сервере.


### <a name="error-code--sqldatatypenotsupported"></a>Код ошибки: Склдататипенотсуппортед

- **Сообщение**: `A database operation failed. Please check the SQL errors.`

- **Причина**: Если неполадка возникает в ИСТОЧНИКе SQL, а ошибка связана с переполнением SqlDateTime, значение данных превышает диапазон типов логики (1/1/1753 12:00:00 AM-12/31/9999 11:59:59 РМ).

- **Рекомендация**: приведите тип к строке в исходном SQL-запросе или в сопоставлении столбцов действия копирования измените тип столбца на "String".

- **Причина**: Если неполадка возникает в ПРИЕМНИКе SQL, а ошибка связана с переполнением SqlDateTime, значение данных превышает допустимый диапазон в таблице приемника.

- **Рекомендация**: обновите соответствующий тип столбца до типа "datetime2" в таблице приемника.


### <a name="error-code--sqlinvaliddbstoredprocedure"></a>Код ошибки: Склинвалиддбсторедпроцедуре

- **Сообщение**: `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Причина**: указанная хранимая процедура недопустима. Это может быть вызвано тем, что хранимая процедура не возвращает никаких данных.

- **Рекомендация**: Проверьте хранимую процедуру средствами SQL. Убедитесь, что хранимая процедура может возвращать данные.


### <a name="error-code--sqlinvaliddbquerystring"></a>Код ошибки: Склинвалиддбкуеристринг

- **Сообщение**: `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Причина**: указан недопустимый SQL-запрос. Это может быть вызвано тем, что запрос не возвращает никаких данных.

- **Рекомендация**: Проверьте запрос SQL средствами SQL. Убедитесь, что запрос может возвращать данные.


### <a name="error-code--sqlinvalidcolumnname"></a>Код ошибки: Склинвалидколумннаме

- **Сообщение**: `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Причина**: не удается найти столбец. Возможная неправильная конфигурация.

- **Рекомендация**: Проверьте столбец в запросе, "Structure" в наборе данных и "сопоставления" в действии.


### <a name="error-code--sqlbatchwritetimeout"></a>Код ошибки: Склбатчвритетимеаут

- **Сообщение**: `Timeout in SQL write opertaion.`

- **Причина**: может быть временным сбоем базы данных SQL.

- **Рекомендация**. Если проблема воспроизводится, обратитесь в службу поддержки SQL Azure.


### <a name="error-code--sqlbatchwriterollbackfailed"></a>Код ошибки: Склбатчвритероллбаккфаилед

- **Сообщение**: `Timeout in SQL write operation and rollback also fail.`

- **Причина**: может быть временным сбоем базы данных SQL.

- **Рекомендация**: повторите попытку, чтобы обновить строку подключения связанной службы с большим значением времени ожидания соединения.


### <a name="error-code--sqlbulkcopyinvalidcolumnlength"></a>Код ошибки: Склбулккопинвалидколумнленгс

- **Сообщение**: `SQL Bulk Copy failed due to received an invalid column length from the bcp client.`

- **Причина**: не удалось выполнить групповое копирование SQL из-за получения недопустимой длины столбца от клиента bcp.

- **Рекомендация**: чтобы узнать, какая строка сталкивается с проблемой, включите функцию отказоустойчивости в действии копирования, которая может переназначать проблемные строки в хранилище для дальнейшего изучения. Справочный документ: https://docs.microsoft.com/azure/data-factory/copy-activity-fault-tolerance.


### <a name="error-code--sqlconnectionisclosed"></a>Код ошибки: Склконнектионисклосед

- **Сообщение**: `The connection is closed by SQL database.`

- **Причина**: соединение SQL закрывается базой данных SQL при высоком параллельном выполнении и разрыве соединения с сервером.

- **Рекомендация**: удаленный сервер закрывает подключение SQL. Повторите попытку. Если проблема повторится, обратитесь в службу поддержки SQL Azure.

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
            

## <a name="azure-blob-storage"></a>Хранилище blob-объектов Azure

### <a name="error-code--azurebloboperationfailed"></a>Код ошибки: Азуреблобоператионфаилед

- **Сообщение**: `Blob operation Failed. ContainerName: %containerName;, path: %path;.`

- **Причина**: ошибка при обращении к операции хранилища BLOB-объектов.

- **Рекомендация**: просмотрите сведения об ошибке в подробностях. См. справочный документ по BLOB-объектам: https://docs.microsoft.com/rest/api/storageservices/blob-service-error-codes. Обратитесь в службу хранилища, если вам нужна помощь.



## <a name="azure-data-lake-gen2"></a>Azure Data Lake Gen2

### <a name="error-code--adlsgen2operationfailed"></a>Код ошибки: AdlsGen2OperationFailed

- **Сообщение**: `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Причина**: ADLS 2-го поколения выдает ошибку, указывающую на сбой операции.

- **Рекомендация**: проверьте подробное сообщение об ошибке, созданное ADLS 2-го поколения. Если это вызвано временным сбоем, повторите попытку. Если вам нужна дополнительная помощь, обратитесь в службу поддержки хранилища Azure и укажите идентификатор запроса в сообщении об ошибке.

- **Причина**: Если сообщение об ошибке содержит "запрещено", возможно, субъект-служба или управляемое удостоверение, которые вы используете, не имеют достаточных разрешений для доступа к ADLS 2-го поколения.

- **Рекомендация**: см. справочный документ: https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication.

- **Причина**: Если сообщение об ошибке содержит "InternalServerError", то ошибка возвращается ADLS 2-го поколения.

- **Рекомендация**. это может быть вызвано временной ошибкой, повторите попытку. Если проблема будет повторяться, обратитесь в службу поддержки хранилища Azure и укажите идентификатор запроса в сообщении об ошибке.


## <a name="next-steps"></a>Следующие шаги

Для получения дополнительных сведений об устранении неполадок воспользуйтесь следующими ресурсами:

*  [Блог, посвященный службе "Фабрика данных"](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Запросы функций фабрики данных](https://feedback.azure.com/forums/270578-data-factory)
*  [Видеоролики по Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Форум MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Форум Stack Overflow для фабрики данных](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Сведения о фабрике данных в Twitter](https://twitter.com/hashtag/DataFactory)
            
