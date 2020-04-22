---
title: Импорт и экспорт данных между пулами Spark (предварительной версии) и пулами SQL
description: В этой статье содержатся сведения об использовании настраиваемого соединителя для перемещения данных между пулами SQL и пулами Spark (предварительной версии).
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: prgomata
ms.reviewer: euang
ms.openlocfilehash: f92c05476c9e85690fdeacade5463a43d0a4af42
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420428"
---
# <a name="introduction"></a>Введение

Соединитель Spark для аналитики SQL предназначен для эффективной передачи данных между пулом Spark (предварительной версии) и пулами SQL в Azure Synapse. Соединитель Spark для аналитики SQL работает только в пулах SQL и не работает с SQL по запросу.

## <a name="design"></a>Конструирование

Передача данных между пулами Spark и SQL может выполняться с помощью JDBC. Но при наличии двух распределенных систем, таких как пулы Spark и SQL, как правило, JDBC является узким местом при последовательной передаче данных.

Соединитель пулов Spark и аналитики SQL реализует источник данных для Apache Spark. Он использует Azure Data Lake Storage 2-го поколения и PolyBase в пулах SQL для эффективной передачи данных между кластером Spark и экземпляром аналитики SQL.

![Архитектура соединителя](./media/synapse-spark-sqlpool-import-export/arch1.png)

## <a name="authentication-in-azure-synapse-analytics"></a>Проверка подлинности в Azure Synapse Analytics

Проверка подлинности между системами упрощена в Azure Synapse Analytics. Служба токенов подключается к Azure Active Directory (AAD), чтобы получить маркеры безопасности, используемые при доступе к учетной записи хранения или к серверу хранилища данных. По этой причине нет необходимости создавать учетные данные или указывать их в API соединителя, если проверка подлинности AAD настроена для учетной записи хранения и сервера хранилища данных. В противном случае можно указать проверку подлинности SQL. Дополнительные сведения см. в разделе [Использование](#usage).

## <a name="constraints"></a>Ограничения

- Этот соединитель работает только в Scala.

## <a name="prerequisites"></a>Предварительные требования

- Наличие роли **db_exporter** в базе данных или пуле SQL, между которыми необходимо передавать данные.

Чтобы создать пользователей, подключитесь к базе данных и выполните действия, указанные в следующих примерах:

```Sql
CREATE USER Mary FROM LOGIN Mary;
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Чтобы назначить роль, выполните следующий код:

```Sql
EXEC sp_addrolemember 'db_exporter', 'Mary';
```

## <a name="usage"></a>Использование

Инструкции импорта не обязательно указывать. Они предварительно импортированы для интерфейса записной книжки.

### <a name="transferring-data-to-or-from-a-sql-pool-in-the-logical-server-dw-instance-attached-with-the-workspace"></a>Передача данных в пул SQL или из него на логическом сервере (экземпляре хранилища данных), подключенном к рабочей области

> [!NOTE]
> **Импорт в интерфейсе записной книжки не требуется.**

```Scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>API чтения

```Scala
val df = spark.read.sqlanalytics("[DBName].[Schema].[TableName]")
```

Указанный выше API будет работать как для внутренних (управляемых), так и для внешних таблиц в пуле SQL.

#### <a name="write-api"></a>API записи

```Scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

где TableType может быть Constants.INTERNAL или Constants.EXTERNAL:

```Scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.INTERNAL)
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.EXTERNAL)
```

Проверка подлинности для хранилища и SQL Server установлена.

### <a name="if-you-are-transferring-data-to-or-from-a-sql-pool-or-database-in-a-logical-server-outside-the-workspace"></a>Передача данных в пул SQL или базу данных или из них на логическом сервере вне рабочей области

> [!NOTE]
> Импорт в интерфейсе записной книжки не требуется.

```Scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>API чтения

```Scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>API записи

```Scala
df.write.
option(Constants.SERVER, "[samplews].[database.windows.net]").
sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

### <a name="using-sql-auth-instead-of-aad"></a>Использование проверки подлинности SQL вместо AAD

#### <a name="read-api"></a>API чтения

В настоящее время соединитель не поддерживает проверку подлинности на основе маркеров безопасности для пула SQL, который находится за пределами рабочей области. Необходимо использовать проверку подлинности SQL.

```Scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
option(Constants.USER, [SQLServer Login UserName]).
option(Constants.PASSWORD, [SQLServer Login Password]).
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>API записи

```Scala
df.write.
option(Constants.SERVER, "[samplews].[database.windows.net]").
option(Constants.USER, [SQLServer Login UserName]).
option(Constants.PASSWORD, [SQLServer Login Password]).
sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

### <a name="using-the-pyspark-connector"></a>Использование соединителя PySpark

> [!NOTE]
> В этом примере используется тот же интерфейс записной книжки.

Предположим, что у вас есть кадр данных pyspark_df, который необходимо записать в хранилище данных.

Создайте временную таблицу с помощью кадра данных в PySpark:

```Python
pyspark_df.createOrReplaceTempView("pysparkdftemptable")
```

Запустите ячейку Scala в записной книжке PySpark с помощью магических команд:

```Scala
%%spark
val scala_df = spark.sqlContext.sql ("select * from pysparkdftemptable")

pysparkdftemptable.write.sqlanalytics("sqlpool.dbo.PySparkTable", Constants.INTERNAL)
```
Аналогично в сценарии для чтения выполните считывание данных с помощью Scala и запишите их во временную таблицу. Используйте Spark SQL в PySpark, чтобы запросить перенос временной таблицы в кадр данных.

## <a name="next-steps"></a>Дальнейшие действия

- [Создание пула SQL]([Create a new Apache Spark pool for an Azure Synapse Analytics workspace](../../synapse-analytics/quickstart-create-apache-spark-pool.md))
- [Создание пула Apache Spark для рабочей области Azure Synapse Analytics](../../synapse-analytics/quickstart-create-apache-spark-pool.md) 