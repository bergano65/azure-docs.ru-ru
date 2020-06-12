---
title: Создание задания потоковой передачи T-SQL в SQL Azure для пограничных вычислений (предварительная версия)
description: Сведения о создании заданий Stream Analytics в SQL Azure для пограничных вычислений (предварительная версия)
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 323ec00667350917e6b16827f908ac1abeee77d6
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/31/2020
ms.locfileid: "84233306"
---
# <a name="create-stream-analytics-job-in-azure-sql-edge-preview"></a>Создание задания Stream Analytics в SQL Azure для пограничных вычислений (предварительная версия) 

Эта статья содержит сведения о создании задания потоковой передачи T-SQL в SQL Azure для пограничных вычислений (предварительная версия) Чтобы создать задание потоковой передачи в SQL Server, необходимо выполнить следующие действия.

1. Создайте входные и выходные объекты внешнего потока.
2. Определите запрос задания потоковой передачи в ходе создания задания потоковой передачи.

> [!NOTE]
> Чтобы включить функцию потоковой передачи T-SQL в SQL Azure для пограничных вычислений, включите TF 11515 в качестве параметра запуска или используйте команду [DBCC TRACEON]( https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql). Дополнительные сведения о том, как включить флаги трассировки с помощью файла mssql.conf, см. в разделе [Настройка с помощью файла mssql.conf](configure.md#configure-using-mssqlconf-file). Это требование будет удалено в будущих обновлениях SQL Azure для пограничных вычислений (предварительная версия).

## <a name="configure-an-external-stream-input-and-output-object"></a>Настройка входного и выходного объекта внешнего потока

Потоковая передача T-SQL использует функциональные возможности внешнего источника данных SQL Server для определения источников данных, связанных с входными и выходными данными внешнего потока задания потоковой передачи. Далее перечислены команды T-SQL, необходимые для создания входного или выходного объекта внешнего потока.

[CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql)

[CREATE EXTERNAL DATA SOURCE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql)

[CREATE EXTERNAL STREAM (Transact-SQL)](#example-create-an-external-stream-object-sql-database)

Кроме того, если в качестве потока вывода используется SQL Edge (или SQL Server, Azure SQL), то для определения учетных данных для доступа к базе данных SQL требуется выполнить команду T-SQL [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql).

### <a name="supported-input-and-output-stream-data-sources"></a>Поддерживаемые источники данных входного потока и потока вывода

В настоящее время SQL Azure для пограничных вычислений поддерживает только следующие источники данных в качестве входных потоков и потоков вывода.

| Тип источника данных | Входные данные | Выходные данные | Описание |
|------------------|-------|--------|------------------|
| Концентратор Azure IoT Edge | Да | Да | Источник данных для чтения и записи потоковых данных в концентратор Azure IoT Edge. Дополнительные сведения см. в разделе [Концентратор IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub).|
| База данных SQL | Нет | Да | Соединение с источником данных для записи потоковых данных в Базу данных SQL. База данных SQL может быть локальной Базой данных SQL, удаленной базой данных SQL Server или Базой данных SQL Azure.|
| хранилище BLOB-объектов Azure | Нет | Да | Источник данных для записи данных в большой двоичный объект в учетной записи хранения Azure. |
| Kafka | Да | Нет | Источник данных для чтения потоковых данных из раздела Kafka. Этот адаптер сейчас доступен только для версии Intel или AMD SQL Azure для пограничных вычислений и недоступен для SQL Azure для пограничных вычислений версии ARM64.|

### <a name="example-create-an-external-stream-inputoutput-object-for-azure-iot-edge-hub"></a>Пример Создание внешнего потока входных и выходных объектов для концентратора Azure IoT Edge

В приведенном ниже примере создается объект внешнего потока для концентратора Edge. Чтобы создать источник входных и выходных данных внешнего потока для концентратора Azure IoT Edge, сначала необходимо создать формат внешнего файла для SQL, чтобы понять, какие данные будут доступны для чтения и записи.

1. Создайте формат внешнего файла в формате JSON.

    ```sql
    Create External file format InputFileFormat
    WITH (  
       format_type = JSON,
    )
    go
    ```

2. Создайте внешний источник данных для концентратора IoT Edge. Приведенный ниже скрипт T-SQL создает соединение источника данных с концентратором Edge, работающем на том же узле docker, что и SQL для пограничных вычислений.

    ```sql
    CREATE EXTERNAL DATA SOURCE EdgeHubInput WITH (
    LOCATION = 'edgehub://'
    )
    go
    ```

3. Создайте объект внешнего потока для концентратора IoT Edge. Приведенный ниже скрипт T-SQL создает объект потока для концентратора Edge. В случае с объектом потока концентратора Edge параметр LOCATION представляет собой имя раздела или канала Edge, в котором выполняется операция считывания или записи.

    ```sql
    CREATE EXTERNAL STREAM MyTempSensors WITH (
    DATA_SOURCE = EdgeHubInput,
    FILE_FORMAT = InputFileFormat,
    LOCATION = N'TemperatureSensors',
    INPUT_OPTIONS = N'',
    OUTPUT_OPTIONS = N''
    )
    go
    ```

### <a name="example-create-an-external-stream-object-sql-database"></a>Пример Создание объекта внешнего потока Базы данных SQL

В приведенном ниже примере создается объект внешнего потока для локальной базы данных SQL Server. 

1. Создайте главный ключ в базе данных. Это необходимо для шифрования секрета учетных данных.

    ```sql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<<Strong_Password_For_Master_Key_Encryption>>';
    ```

2. Создайте учетные данные в области базы данных для доступа к источнику SQL Server. В следующем примере создаются учетные данные для внешнего источника данных с IDENTITY = username и SECRET = password.

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL SQLCredential
    WITH IDENTITY = '<SQL_Login>', SECRET = '<SQL_Login_PASSWORD>'
    go
    ```

3. Создайте внешний источник данных с помощью инструкции CREATE EXTERNAL DATA SOURCE. Следующий пример:

    * Создает внешний источник данных LocalSQLOutput.
    * Определяет внешний источник данных (LOCATION = <vendor>://<server>[:<port>]). В примере он указывает на локальный экземпляр SQL Server.
    * Наконец, в примере используются учетные данные, созданные ранее.

    ```sql
    CREATE EXTERNAL DATA SOURCE LocalSQLOutput WITH (
    LOCATION = 'sqlserver://tcp:.,1433'
    ,CREDENTIAL = SQLCredential
    )
    go
    ```

4. Создайте объект внешнего потока. В приведенном ниже примере создается объект внешнего потока, указывающий на таблицу *dbo.TemperatureMeasurements* в базе данных *MySQLDatabase*.

    ```sql
    CREATE EXTERNAL STREAM TemperatureMeasurements WITH (
    DATA_SOURCE = LocalSQLOutput,
    LOCATION = N'MySQLDatabase.dbo.TemperatureMeasurements',
    INPUT_OPTIONS = N'',
    OUTPUT_OPTIONS = N''
    )
    ```

## <a name="create-the-streaming-job-and-the-streaming-queries"></a>Создание задания потоковой передачи и потоковых запросов

Чтобы определить потоковые запросы и создать задание потоковой передачи, используйте системную хранимую процедуру **sys.sp_create_streaming_job**. Хранимая процедура **sp_create_streaming_job** принимает два параметра.

- job_name — имя задания потоковой передачи. Имена заданий потоковой передачи уникальны в пределах экземпляра.
- Оператор — оператор потокового запроса на [основе языка запросов Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?).

В приведенном ниже примере создается простое задание потоковой передачи с одним запросом потоковой передачи. Этот запрос считывает входные данные из концентратора Edge и выполняет запись в *dbo.TemperatureMeasurements* базы данных.

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob1',
@statement= N'Select * INTO TemperatureMeasurements from MyEdgeHubInput'
```

В приведенном ниже примере создается более сложное потоковое задание с несколькими различными запросами, включая запрос, который использует встроенную функцию AnomalyDetection_ChangePoint для выявления аномалий в данных о температуре.

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob2', @statement=
N' Select * INTO TemperatureMeasurements1 from MyEdgeHubInput1

Select * Into TemperatureMeasurements2 from MyEdgeHubInput2

Select * Into TemperatureMeasurements3 from MyEdgeHubInput3

SELECT
Timestamp as [Time],
[Temperature] As [Temperature],
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''Score'') as ChangePointScore,
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''IsAnomaly'') as IsChangePointAnomaly,
INTO TemperatureAnomalies FROM MyEdgeHubInput2;
'
go
```

## <a name="start-stop-drop-and-monitor-streaming-jobs"></a>Запуск, завершение, удаление и отслеживание заданий потоковой передачи

Чтобы запустить задание потоковой передачи в SQL Server, выполните хранимую процедуру **sys.sp_start_streaming_job**. Хранимая процедура требует запуска того же самого потокового задания, что и входные данные.

```sql
exec sys.sp_start_streaming_job @name=N'StreamingJob1'
go
```

Чтобы завершить задание потоковой передачи в SQL Server, выполните хранимую процедуру **sys.sp_stop_streaming_job**. Хранимая процедура требует остановки того же самого потокового задания, что и входные данные.

```sql
exec sys.sp_stop_streaming_job @name=N'StreamingJob1'
go
```

Чтобы прервать (удалить) задание потоковой передачи в SQL Server, выполните хранимую процедуру **sys.sp_drop_streaming_job**. Хранимая процедура требует прерывания того же самого потокового задания, что и входные данные.

```sql
exec sys.sp_drop_streaming_job @name=N'StreamingJob1'
go
```

Чтобы получить состояние текущего задания потоковой передачи в SQL Server, выполните хранимую процедуру **sys.sp_get_streaming_job**. Для хранимой процедуры требуется удалить то же потоковое задание, что и для ввода и вывода имени и текущего состояния потокового задания.

```sql
exec sys.sp_get_streaming_job @name=N'StreamingJob1'
        WITH RESULT SETS
(
       (
       name nvarchar(256),
       status nvarchar(256)
       )
)
```

Задание потоковой передачи может иметь одно из следующих состояний.

| Состояние | Описание |
|--------| ------------|
| Создание | Задание потоковой передачи было создано, но еще не запущено. |
| Запуск | Задание потоковой передачи запускается. |
| Бездействие | Задание потоковой передачи выполняется, но входные данные для обработки отсутствуют. |
| Обработка | Задание потоковой передачи выполняется, и входные данные обрабатываются. Это свидетельствует о работоспособности задания потоковой передачи. |
| Деградация | Задание потоковой передачи выполняется, но при обработке входных данных произошли устранимые ошибки сериализации или десериализации ввода-вывода. Входное задание продолжит выполняться, но входные данные, вызывающие ошибки, будут удаляться. |
| Остановлена | Выполнение задания потоковой передачи остановлено. |
| Ошибка | Сбой задания потоковой передачи. Обычно это указывает на неустранимую ошибку при обработке. |

## <a name="next-steps"></a>Next Steps

- [Представления каталога потоковой передачи (Transact-SQL)](streaming-catalog-views.md) 
- [Создание внешнего потока (Transact-SQL)](create-external-stream-transact-sql.md)
