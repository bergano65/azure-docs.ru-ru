---
title: Копирование данных между хранением данных озера Gen1 и Azure S'L - Sqoop Документы Майкрософт
description: Сведения о копировании данных между базой данных SQL Azure и Azure Data Lake Storage 1-го поколения с помощью Sqoop.
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: twooley
ms.openlocfilehash: cf3893706afcb4c4cc5b90dd3d2431ecedc71d0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73839059"
---
# <a name="copy-data-between-data-lake-storage-gen1-and-azure-sql-database-using-sqoop"></a>Копирование данных между хранилищем данных озера Gen1 и Базой данных Azure S'L с помощью Sqoop

Узнайте, как с помощью Apache Sqoop импортировать и экспортировать данные между Базой данных SQL Azure и Azure Data Lake Storage 1-го поколения.

## <a name="what-is-sqoop"></a>Что такое Sqoop?

Приложения для работы с большими объемами данных являются естественным выбором для обработки неструктурированных и частично структурированных данных, таких как журналы и файлы. Однако может возникнуть необходимость обработки структурированных данных, хранящихся в реляционных базах данных.

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) — это средство, предназначенное для передачи данных между реляционными базами данных и репозиторием больших данных, например Azure Data Lake Storage 1-го поколения. Его можно использовать для импорта данных из системы управления реляционными базами данных (RDBMS), такой как База данных SQL Azure, в Azure Data Lake Storage 1-го поколения. Затем можно преобразовать и анализировать данные с помощью рабочих нагрузок больших данных, а затем экспортировать данные обратно в RDBMS. В этой статье вы используете базу данных Azure S'L в качестве реляционной базы данных для импорта/экспорта.

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы убедитесь, что у вас есть такие компоненты.

* **Подписка Azure**. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Учетная запись Azure Data Lake Storage 1-го поколения**. Для получения инструкций о том, как создать учетную запись, [см.](data-lake-store-get-started-portal.md)
* **Кластер Azure HDInsight** с доступом к учетной записи Data Lake Storage 1-го поколения. Дополнительные сведения см. в статье [Создание кластеров HDInsight, использующих Data Lake Store, с помощью портала Azure](data-lake-store-hdinsight-hadoop-use-portal.md). В этой статье предполагается, что у вас есть кластер HDInsight на платформе Linux с доступом к Data Lake Storage 1-го поколения.
* **база данных SQL Azure**. Инструкции по созданию базы данных см. в статье [Руководство по Базе данных SQL: создание базы данных SQL за несколько минут с помощью портала Azure](../sql-database/sql-database-get-started.md).

## <a name="create-sample-tables-in-the-azure-sql-database"></a>Создание выборочных таблиц в базе данных Azure S'L

1. Для начала создайте две таблицы выборки в базе данных Azure S'L. Для подключения к базе данных, а затем выполнения следующих запросов, используйте [студию управления серверами](../sql-database/sql-database-connect-query-ssms.md) или Visual Studio.

    **Создание Table1**

       CREATE TABLE [dbo].[Table1](
       [ID] [int] NOT NULL,
       [FName] [nvarchar](50) NOT NULL,
       [LName] [nvarchar](50) NOT NULL,
        CONSTRAINT [PK_Table_1] PRIMARY KEY CLUSTERED
           (
                  [ID] ASC
           )
       ) ON [PRIMARY]
       GO

    **Создание Table2**

       CREATE TABLE [dbo].[Table2](
       [ID] [int] NOT NULL,
       [FName] [nvarchar](50) NOT NULL,
       [LName] [nvarchar](50) NOT NULL,
        CONSTRAINT [PK_Table_2] PRIMARY KEY CLUSTERED
           (
                  [ID] ASC
           )
       ) ON [PRIMARY]
       GO

1. Выполнить следующую команду, чтобы добавить некоторые примеры данных в **таблицу1.** Оставьте таблицу **Table2** пустой. Позже вы будете импортировать данные из **таблицы 1** в Data Lake Storage Gen1. Затем вы будете экспортировать данные из Data Lake Storage Gen1 в **таблицу2.**

       INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson');

## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-storage-gen1"></a>Использование Sqoop из кластера Azure HDInsight с доступом в Data Lake Storage 1-го поколения

В кластере HDInsight уже имеются доступные пакеты Sqoop. Если вы настроили кластер HDInsight для использования Data Lake Storage Gen1 в качестве дополнительного хранилища, можно использовать Sqoop (без каких-либо изменений конфигурации) для импорта/экспорта данных между реляционной базой данных, такой как база данных Azure S'L, и учетной записью Data Lake Storage Gen1.

1. Для этой статьи мы предполагаем, что вы создали кластер Linux, поэтому вы должны использовать SSH для подключения к кластеру. См. раздел [Подключение к кластеру HDInsight на основе Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Проверьте, доступна ли учетная запись хранилища Data Lake Storage 1-го поколения из кластера. Выполните следующую команду из командной строки SSH:

       hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net/

   Эта команда предоставляет список файлов/папок в учетной записи Data Lake Storage Gen1.

### <a name="import-data-from-azure-sql-database-into-data-lake-storage-gen1"></a>Импорт данных из Базы данных SQL Azure в Data Lake Storage 1-го поколения

1. Перейдите в каталог, где доступны пакеты Sqoop. Как правило, `/usr/hdp/<version>/sqoop/bin`это место находится в.

1. Импортируйте данные из таблицы **Table1** в учетную запись Data Lake Storage 1-го поколения. Используйте следующий синтаксис:

       sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

   Заполнитель **sql-database-server-name** представляет имя сервера, на котором работает база данных Azure S'L. **sql-database-name** представляет реальное имя базы данных.

   Например,

       sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=twooley@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1

1. Убедитесь, что данные были переданы в учетную запись Data Lake Storage 1-го поколения. Выполните следующую команду:

       hdfs dfs -ls adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/

   Вы должны увидеть следующие выходные данные.

       -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
       -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
       -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
       -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
       -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003

   Каждый файл **part-m-** q соответствует строке в исходной таблице, **Таблица1**. Чтобы проверить это, просмотрите содержимое файлов part-m-*.

### <a name="export-data-from-data-lake-storage-gen1-into-azure-sql-database"></a>Экспорт данных из Data Lake Storage 1-го поколения в Базу данных SQL Azure

1. Экспортируйте данные из учетной записи Data Lake Storage 1-го поколения в пустую таблицу **Table2** в базе данных SQL Azure. Используйте приведенный ниже синтаксис.

       sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

   Например,

       sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=twooley@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

1. Убедитесь, что данные были отправлены в таблицу базы данных SQL. С помощью [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) или Visual Studio подключитесь к базе данных SQL Azure, а затем выполните следующий запрос.

       SELECT * FROM TABLE2

   Эта команда должна иметь следующий вывод.

        ID  FName    LName
       -------------------
       1    Neal     Kell
       2    Lila     Fulton
       3    Erna     Myers
       4    Annette  Simpson

## <a name="performance-considerations-while-using-sqoop"></a>Рекомендации по производительности при использовании Sqoop

Для получения информации о производительности настройки вашей работы Sqoop для копирования данных для хранения данных озера Gen1, [см.](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/)

## <a name="next-steps"></a>Дальнейшие действия

* [Копирование данных из больших двоичных объектов хранилища Azure в хранилище озера данных](data-lake-store-copy-data-azure-storage-blob.md)
* [Защита данных в Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Начало работы с Azure Data Lake Analytics с помощью портала Azure](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Создание кластеров HDInsight, использующих Data Lake Store, с помощью портала Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
