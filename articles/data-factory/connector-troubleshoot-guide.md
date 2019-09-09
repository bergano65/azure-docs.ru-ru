---
title: Устранение неполадок соединителей фабрики данных Azure | Документация Майкрософт
description: Узнайте, как устранять неполадки соединителя в фабрике данных Azure.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 08/26/2019
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: bea5191063cf673f6b1395d46a15536e80b0aa30
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70143509"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Устранение неполадок соединителей фабрики данных Azure

В этой статье рассматриваются распространенные способы устранения неполадок соединителей в фабрике данных Azure.

## <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Сообщение об ошибке: The remote server returned an error: (403) запрещено

- **Проблема.** Сбой действия копирования со следующей ошибкой: 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **Причина.** Одной из возможных причин является то, что используемый субъект-служба или управляемое удостоверение не имеет разрешения на доступ к определенной папке или файлу.

- **Решение**. Предоставьте соответствующие разрешения для всех папок и вложенных папок, которые необходимо скопировать. Обратитесь к [этому документу](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Сообщение об ошибке: Не удалось получить маркер доступа с помощью субъекта-службы. Ошибка ADAL: service_unavailable

- **Симптомы**. сбой действия копирования со следующей ошибкой:

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **Причина.** Если сервер маркеров службы (STS), принадлежащий Azure Active Directory, недоступен, т. е. слишком занят для обработки запросов, он возвращает ошибку HTTP 503. 

- **Решение**. Повторите действие копирования через несколько минут.

## <a name="azure-sql-data-warehouse"></a>Хранилище данных SQL Azure

### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Сообщение об ошибке: Не удалось выполнить преобразование из строки символов в uniqueidentifier

- **Проблема.** При копировании данных из источника табличных данных (например, SQL Server) в хранилище данных SQL Azure с помощью промежуточного копирования и Polybase вы столкнулись со следующей ошибкой:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **Причина.** Polybase хранилища данных SQL Azure не может преобразовать пустую строку в GUID.

- **Решение**. В поле приемник действия копирования в разделе Параметры polybase задайте для параметра "**использовать тип по умолчанию**" значение false.

### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Сообщение об ошибке: Ожидаемый тип данных: DECIMAL (x, x), ошибочное значение

- **Проблема.** При копировании данных из источника табличных данных (например, SQL Server) в хранилище SQL, используя промежуточное копирование и Polybase, вы столкнулись со следующей ошибкой:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **Причина.** Polybase хранилища данных SQL Azure не может вставить пустую строку (значение null) в десятичный столбец.

- **Решение**. В поле приемник действия копирования в разделе Параметры polybase задайте для параметра "**использовать тип по умолчанию**" значение false.

### <a name="error-message-java-exception-messagehdfsbridgecreaterecordreader"></a>Сообщение об ошибке: Сообщение об исключении Java: Хдфсбридже:: Креатерекордреадер

- **Проблема.** Вы копируете данные в хранилище данных SQL Azure с помощью Polybase и нажмем следующую ошибку:

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **Причина.** Возможная причина заключается в том, что схема (общая ширина столбца) слишком велика (больше 1 МБ). Проверьте схему целевой таблицы хранилища данных SQL, добавив размер всех столбцов:

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

- **Решение**. Уменьшить ширину столбца, чтобы она была меньше 1 МБ

- Или используйте метод "выполнить небольшую вставку", отключив polybase

### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Сообщение об ошибке: Условие, указанное с помощью условных заголовков HTTP, не выполнено

- **Проблема.** Используйте SQL Query для извлечения данных из хранилища данных SQL Azure и нажмите следующую ошибку:

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **Причина.** Ошибка при обращении к хранилищу данных SQL Azure при выполнении запросов к внешней таблице в службе хранилища Azure.

- **Решение**. Выполните тот же запрос в SSMS и проверьте, отображается ли тот же результат. Если да, отправьте запрос в службу поддержки в хранилище данных SQL Azure и укажите имя сервера и базы данных SQL DW, чтобы устранить неполадки.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Сообщение об ошибке: Слишком большой размер запроса

- **Проблема.** Данные копируются в Azure Cosmos DB с размером пакета записи по умолчанию и ошибкой *"**размер запроса слишком велик**"* .

- **Причина.** Cosmos DB ограничивает размер одного запроса до 2 МБ. Формула:, размер запроса = один размер документа * размер пакета записи. Если размер документа велик, то поведение по умолчанию приведет к слишком большому размеру запроса. Размер пакета записи можно настроить.

- **Решение**. В поле приемник действия копирования уменьшите значение параметра размер записи пакета (значение по умолчанию — 10000).

### <a name="error-message-unique-index-constraint-violation"></a>Сообщение об ошибке: Нарушение ограничения уникального индекса

- **Проблема.** При копировании данных в Cosmos DB вы столкнулись со следующей ошибкой:

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception &#61; Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **Причина.** Существуют две возможные причины.

    - При использовании инструкции **INSERT** как режима записи эта ошибка означает, что исходные данные содержат строки или объекты с ОДИНАКОВым идентификатором.

    - Если в качестве поведения записи используется **Upsert** , а для контейнера задан другой уникальный ключ, то эта ошибка означает, что исходные данные содержат строки или объекты с разными идентификаторами, но одно значение для определенного уникального ключа.

- **Решение**. 

    - Для cause1 задайте **Upsert** в качестве поведения записи.
    - Для причины 2 Убедитесь, что каждый документ имеет разное значение для определенного уникального ключа.

### <a name="error-message-request-rate-is-large"></a>Сообщение об ошибке: Высокая частота запросов

- **Проблема.** При копировании данных в Cosmos DB вы столкнулись со следующей ошибкой:

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **Причина.** Используемые единицы запроса больше, чем доступный RU, настроенный в Cosmos DB. Узнайте [, как](../cosmos-db/request-units.md#request-unit-considerations)Cosmos DB вычислит единицы между собой.

- **Решение**. Ниже приведены два решения.

    1. **Увеличьте значение контейнера ru** в Cosmos DB, что улучшит производительность действия копирования, но требует дополнительных затрат на Cosmos DB. 

    2. Сократите **writeBatchSize** до меньшего значения (например, 1000) и задайте для **parallelCopies** меньшее значение, например 1, что приведет к снижению производительности при выполнении копирования, но не требует дополнительных затрат на Cosmos DB.

### <a name="column-missing-in-column-mapping"></a>Столбец отсутствует в сопоставлении столбцов

- **Проблема.** При импорте схемы для Cosmos DB сопоставления столбцов некоторые столбцы отсутствуют. 

- **Причина.** ADF выводит схему из первых 10 Cosmos DB документов. Если некоторые столбцы или свойства не имеют значения в этих документах, они не будут обнаружены службой ADF, поэтому не отображаются.

- **Решение**. Вы можете настроить запрос следующим образом, чтобы обеспечить отображение столбца в результирующем наборе с пустым значением: (предположим, что в первых 10 документах отсутствует столбец "невозможное"). Кроме того, можно вручную добавить столбец для сопоставления.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Сообщение об ошибке: Гуидрепресентатион для читателя — Кшарплегаци

- **Проблема.** При копировании данных из Cosmos DB Монгоапи/MongoDB с полем UUID вы столкнулись со следующей ошибкой:

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **Причина.** Существует два способа представления UUID в BSON-Ууидстардард и Ууидлегаци. По умолчанию Ууидлегаци используется для чтения данных. Если данные UUID в MongoDB имеют значение Ууидстандард, появится сообщение об ошибке.

- **Решение**. В строке подключения MongoDB добавьте параметр "**ууидрепресентатион = Standard**". Дополнительные сведения см. в разделе [строка подключения MongoDB](connector-mongodb.md#linked-service-properties).

## <a name="sftp"></a>SFTP

### <a name="error-message-invalid-sftp-credential-provided-for-sshpublickey-authentication-type"></a>Сообщение об ошибке: Для типа проверки подлинности "SshPublicKey" указаны недопустимые учетные данные SFTP

- **Проблема.** Вы используете `SshPublicKey` проверку подлинности и нажмем следующую ошибку:

    ```
    Invalid Sftp credential provided for 'SshPublicKey' authentication type
    ```

- **Причина.** Возможны три причины:

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

## <a name="next-steps"></a>Следующие шаги

Для получения дополнительных сведений об устранении неполадок воспользуйтесь следующими ресурсами:

*  [Блог фабрики данных](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Запросы функций фабрики данных](https://feedback.azure.com/forums/270578-data-factory)
*  [Видеоролики по Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Форум MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Форум Stack Overflow для фабрики данных](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Сведения о фабрике данных в Twitter](https://twitter.com/hashtag/DataFactory)



