---
title: Копирование данных между Azure Data Lake Storage 1-го поколения и базой данных SQL Azure с помощью Sqoop | Документация Майкрософт
description: Сведения о копировании данных между базой данных SQL Azure и Azure Data Lake Storage 1-го поколения с помощью Sqoop.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 3f914b2a-83cc-4950-b3f7-69c921851683
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 7d3283b03d15278d1f7fd42a72b154dab1a442b4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60878772"
---
# <a name="copy-data-between-azure-data-lake-storage-gen1-and-azure-sql-database-using-sqoop"></a>Копирование данных между Azure Data Lake Storage 1-го поколения и базой данных SQL Azure с помощью Sqoop
Узнайте, как с помощью Apache Sqoop импортировать и экспортировать данные между Базой данных SQL Azure и Azure Data Lake Storage 1-го поколения.

## <a name="what-is-sqoop"></a>Что такое Sqoop?
Приложения для работы с большими объемами данных являются естественным выбором для обработки неструктурированных и частично структурированных данных, таких как журналы и файлы. Однако может существовать необходимость обработки структурированных данных, хранимых в реляционных базах данных.

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) — это средство, предназначенное для передачи данных между реляционными базами данных и репозиторием больших данных, например Azure Data Lake Storage 1-го поколения. Его можно использовать для импорта данных из системы управления реляционными базами данных (RDBMS), такой как База данных SQL Azure, в Azure Data Lake Storage 1-го поколения. Затем данные можно преобразовать и проанализировать с помощью рабочих нагрузок больших данных, а после этого экспортировать их обратно в RDBMS. В этом учебнике в качестве реляционной базы данных для импорта и экспорта используется база данных SQL Azure.

## <a name="prerequisites"></a>Технические условия
Перед началом работы с этой статьей необходимо иметь следующее:

* **Подписка Azure**. См. страницу [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Учетная запись Azure Data Lake Storage 1-го поколения**. За инструкциями по созданию учетной записи обращайтесь к статье [Начало работы с Azure Data Lake Storage 1-го поколения](data-lake-store-get-started-portal.md).
* **Кластер Azure HDInsight** с доступом к учетной записи Data Lake Storage 1-го поколения. Дополнительные сведения см. в статье [Создание кластеров HDInsight, использующих Data Lake Store, с помощью портала Azure](data-lake-store-hdinsight-hadoop-use-portal.md). В этой статье предполагается, что у вас есть кластер HDInsight на платформе Linux с доступом к Data Lake Storage 1-го поколения.
* **База данных SQL Azure**. Инструкции по созданию базы данных см. в статье [Руководство по Базе данных SQL: создание базы данных SQL за несколько минут с помощью портала Azure](../sql-database/sql-database-get-started.md).

## <a name="do-you-learn-fast-with-videos"></a>Учитесь быстрее с помощью видео?
[Просмотрите это видео](https://mix.office.com/watch/1butcdjxmu114) о копировании данных между большими двоичными объектами службы хранилища Azure и Data Lake Storage 1-го поколения с помощью DistCp.

## <a name="create-sample-tables-in-the-azure-sql-database"></a>Создание образцов таблиц в Базе данных SQL Azure
1. Для начала создайте два образца таблиц в базе данных SQL Azure. Используйте [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) или Visual Studio для подключения к базе данных SQL Azure и выполните приведенные ниже запросы.

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
2. В таблицу **Table1**добавьте несколько примеров данных. Оставьте таблицу **Table2** пустой. Мы будем импортировать данные из таблицы **Table1** в Azure Data Lake Storage 1-го поколения. Затем мы будем экспортировать данные из Data Lake Storage 1-го поколения в таблицу **Table2**. Выполните следующий фрагмент кода.

        INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson');


## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-storage-gen1"></a>Использование Sqoop из кластера Azure HDInsight с доступом в Data Lake Storage 1-го поколения
В кластере HDInsight уже имеются доступные пакеты Sqoop. Если кластер HDInsight настроен для использования Data Lake Storage 1-го поколения в качестве дополнительного хранилища, можно использовать Sqoop (без изменения конфигурации) для импорта и экспорта данных между реляционной базой данных (в данном примере это База данных SQL Azure) и учетной записью Data Lake Storage 1-го поколения.

1. В этом учебнике предполагается, что вы создали кластер Linux, поэтому для подключения к нему следует использовать SSH. См. раздел [Подключение к кластеру HDInsight на основе Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).
2. Проверьте, доступна ли учетная запись хранилища Data Lake Storage 1-го поколения из кластера. Выполните следующую команду из командной строки SSH:

        hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net/

    Она должна вывести список файлов и папок в учетной записи хранилища Data Lake Storage 1-го поколения.

### <a name="import-data-from-azure-sql-database-into-data-lake-storage-gen1"></a>Импорт данных из Базы данных SQL Azure в Data Lake Storage 1-го поколения
1. Перейдите в каталог, где доступны пакеты Sqoop. Как правило, это будет каталог `/usr/hdp/<version>/sqoop/bin`.
2. Импортируйте данные из таблицы **Table1** в учетную запись Data Lake Storage 1-го поколения. Используйте следующий синтаксис:

        sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

    Обратите внимание, что заполнитель **sql-database-server-name** представляет имя сервера, на котором запущена база данных SQL Azure. **sql-database-name** представляет реальное имя базы данных.

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

    Каждый файл **part-m-** * соответствует строке в исходной таблице **Table1**. Чтобы проверить это, просмотрите содержимое файлов part-m-*.


### <a name="export-data-from-data-lake-storage-gen1-into-azure-sql-database"></a>Экспорт данных из Data Lake Storage 1-го поколения в Базу данных SQL Azure
1. Экспортируйте данные из учетной записи Data Lake Storage 1-го поколения в пустую таблицу **Table2** в базе данных SQL Azure. Используйте приведенный ниже синтаксис.

        sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

    Например,


        sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=twooley@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

1. Убедитесь, что данные были отправлены в таблицу базы данных SQL. С помощью [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) или Visual Studio подключитесь к базе данных SQL Azure, а затем выполните следующий запрос.

        SELECT * FROM TABLE2

    Вы должны увидеть следующие выходные данные.

         ID  FName   LName
        ------------------
        1    Neal    Kell
        2    Lila    Fulton
        3    Erna    Myers
        4    Annette    Simpson

## <a name="performance-considerations-while-using-sqoop"></a>Рекомендации по производительности при использовании Sqoop

Сведения о настройке производительности задания Sqoop для копирования данных в Data Lake Storage 1-го поколения см. в записи блога, посвященной [производительности Sqoop](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/).

## <a name="see-also"></a>См. также
* [Копирование данных из больших двоичных объектов хранилища Azure в хранилище озера данных](data-lake-store-copy-data-azure-storage-blob.md)
* [Защита данных в Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Начало работы с Azure Data Lake Analytics с помощью портала Azure](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Создание кластеров HDInsight, использующих Data Lake Store, с помощью портала Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
