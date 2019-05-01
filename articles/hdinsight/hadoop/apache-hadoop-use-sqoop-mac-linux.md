---
title: Использование Apache Sqoop с Apache Hadoop в Azure HDInsight
description: Сведения об использовании Apache Sqoop для импорта и экспорта между Apache Hadoop в HDInsight и Базой данных SQL Azure.
keywords: hadoop sqoop,sqoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 6dcb6853daf34fede590011d165c0ba9001cbac6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64721631"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-sql-database"></a>Использование Apache Sqoop для импорта и экспорта между Apache Hadoop в HDInsight и базой данных SQL

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Узнайте, как использовать Apache Sqoop для импорта и экспорта между кластером Apache Hadoop в Azure HDInsight и Базой данных SQL Azure или базой данных Microsoft SQL Server. В этом руководстве используется команда `sqoop` ​​непосредственно из головного узла кластера Hadoop. В этом документе вы подключитесь к головному узлу, используя SSH, и выполните команды. Эта статья является продолжением [использование Apache Sqoop с Hadoop в HDInsight](./hdinsight-use-sqoop.md).

## <a name="prerequisites"></a>Технические условия

* Завершение [Настройка тестовой среды](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) из [использование Apache Sqoop с Hadoop в HDInsight](./hdinsight-use-sqoop.md).

* Клиент в базе данных Azure SQL. Рассмотрите возможность использования [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md) или [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md).

* Клиент SSH. Дополнительные сведения см. в руководстве по [подключению к HDInsight (Apache Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="sqoop-export"></a>Экспорт Sqoop

Из Hive в SQL Server.

1. Подключитесь к кластеру HDInsight с помощью SSH. Замените `CLUSTERNAME` с именем кластера, введите команду:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Замените `MYSQLSERVER` на имя вашего сервера SQL Server. Чтобы убедиться, что Sqoop может просматривать свою базу данных SQL, введите указанную ниже команду в открыть SSH-подключение. Введите пароль для имени входа SQL Server, при появлении запроса. Эта команда возвращает список баз данных.

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433 --username sqluser -P
    ```

3. Замените `MYSQLSERVER` с именем вашего сервера SQL и `MYDATABASE` с именем базы данных SQL. Чтобы экспортировать данные из Hive `hivesampletable` таблицу `mobiledata` таблица в базе данных SQL, введите указанную ниже команду в открыть SSH-подключение. Введите пароль для имени входа SQL Server, при появлении запроса

    ```bash
    sqoop export --connect 'jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433;database=MYDATABASE' --username sqluser -P -table 'mobiledata' --hcatalog-table hivesampletable
    ```

4. Чтобы убедиться, что данные экспортированы, используйте следующие запросы из клиента SQL для просмотра экспортированных данных:

    ```sql
    SELECT COUNT(*) FROM [dbo].[mobiledata] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[mobiledata] WITH (NOLOCK);
    ```

## <a name="sqoop-import"></a>Импорт Sqoop

Из SQL Server в службу хранилища Azure.

1. Замените `MYSQLSERVER` с именем вашего сервера SQL и `MYDATABASE` с именем базы данных SQL. Введите указанную ниже команду в Открытие SSH-подключения для импорта данных из `mobiledata` таблицы в базе данных SQL, `wasb:///tutorials/usesqoop/importeddata` каталог на HDInsight. Введите пароль для имени входа SQL Server, при появлении запроса. Поля в данных разделены знаками табуляции, а строки завершаются символом новой строки.

    ```bash
    sqoop import --connect 'jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433;database=MYDATABASE' --username sqluser -P --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

2. После завершения операции импорта, введите следующую команду в открыть SSH-подключение для вывода списка данных в новый каталог:

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="limitations"></a>Ограничения

* Массовый экспорт: при использовании HDInsight на основе Linux соединитель Sqoop, применяемый для экспорта данных на сервер Microsoft SQL Server или в базу данных SQL Azure, не поддерживает операции массовой вставки.

* Пакетная обработка: при использовании HDInsight на основе Linux, когда для выполнения вставок применяется параметр`-batch`, Sqoop выполняет несколько вставок вместо пакетной обработки операций вставки.

## <a name="important-considerations"></a>Важные сведения

* HDInsight и SQL Server должны находиться в одной виртуальной сети Azure.

    Пример см. в статье о [подключении HDInsight к локальной сети](./../connect-on-premises-network.md).

    Подробные сведения об использовании HDInsight c виртуальными сетями Azure см. в статье [Расширение возможностей HDInsight с помощью виртуальной сети Azure](../hdinsight-extend-hadoop-virtual-network.md). Дополнительные сведения см. в статье [Виртуальная сеть Azure](../../virtual-network/virtual-networks-overview.md).

* Также SQL Server должен разрешать аутентификацию SQL. Дополнительные сведения см. в документе [Выбор режима проверки подлинности](https://msdn.microsoft.com/ms144284.aspx).

* Необходимо настроить SQL Server для удаленных соединений. Дополнительные сведения см. в документе [How to troubleshoot connecting to the SQL Server database engine](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) (Устранение неполадок при подключении к ядру СУБД SQL Server).

## <a name="next-steps"></a>Дальнейшие действия

Теперь вы узнали, как использовать Sqoop. Дополнительные сведения см. на следующих ресурсах:

* [Использование Apache Oozie с HDInsight](../hdinsight-use-oozie-linux-mac.md). Используйте действие Sqoop в рабочем процессе Oozie.
* [Анализ данных о задержке рейсов с помощью HDInsight](../hdinsight-analyze-flight-delay-data-linux.md). Используйте Apache Hive для анализа данных о задержке рейсов, а затем используйте Sqoop для экспорта данных в базу данных SQL Azure.
* [Отправка данных в HDInsight](../hdinsight-upload-data.md). Узнайте о других способах отправки данных в HDInsight или хранилище BLOB-объектов Azure.
