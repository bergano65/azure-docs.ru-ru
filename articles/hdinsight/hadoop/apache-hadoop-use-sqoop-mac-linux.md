---
title: Использование Apache Sqoop с Apache Hadoop в Azure HDInsight
description: Сведения об использовании Apache Sqoop для импорта и экспорта между Apache Hadoop в HDInsight и базой данных SQL Azure.
keywords: hadoop sqoop,sqoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: c839aeae77d7e75fb30d82c410c331d21f5868ae
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68406049"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-sql-database"></a>Использование Apache Sqoop для импорта и экспорта между Apache Hadoop в HDInsight и базой данных SQL

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Узнайте, как использовать Apache Sqoop для импорта и экспорта между кластером Apache Hadoop в Azure HDInsight и Базой данных SQL Azure или базой данных Microsoft SQL Server. В этом руководстве используется команда `sqoop` ​​непосредственно из головного узла кластера Hadoop. В этом документе вы подключитесь к головному узлу, используя SSH, и выполните команды. Эта статья является продолжением [использования Apache Sqoop с Hadoop в HDInsight](./hdinsight-use-sqoop.md).

## <a name="prerequisites"></a>предварительные требования

* Завершение [настройки тестовой среды](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) с [помощью Apache Sqoop с Hadoop в HDInsight](./hdinsight-use-sqoop.md).

* Клиент для запроса к базе данных SQL Azure. Рассмотрите возможность использования [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md) или [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md).

* Клиент SSH. Дополнительные сведения см. в руководстве по [подключению к HDInsight (Apache Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="sqoop-export"></a>Экспорт Sqoop

Из Hive в SQL Server.

1. Подключитесь к кластеру HDInsight с помощью SSH. Замените `CLUSTERNAME` именем кластера, а затем введите команду:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Замените `MYSQLSERVER` на имя SQL Server. Чтобы убедиться, что база данных SQL может видеть Sqoop, введите приведенную ниже команду в открытом SSH-подключении. При появлении запроса введите пароль для имени входа SQL Server. Эта команда возвращает список баз данных.

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433 --username sqluser -P
    ```

3. Замените `MYSQLSERVER` именем своего SQL Server и `MYDATABASE` именем базы данных SQL. Чтобы экспортировать данные из таблицы `hivesampletable` `mobiledata` Hive в таблицу базы данных SQL, введите приведенную ниже команду в открытом SSH-подключении. Введите пароль для имени входа SQL Server при появлении запроса

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

1. Замените `MYSQLSERVER` именем своего SQL Server и `MYDATABASE` именем базы данных SQL. Введите приведенную ниже команду в открытом SSH-подключении, `mobiledata` `wasb:///tutorials/usesqoop/importeddata` чтобы импортировать данные из таблицы в базе данных SQL в каталог в HDInsight. При появлении запроса введите пароль для имени входа SQL Server. Поля в данных разделены знаками табуляции, а строки завершаются символом новой строки.

    ```bash
    sqoop import --connect 'jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433;database=MYDATABASE' --username sqluser -P --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

2. После завершения импорта введите следующую команду в открытом SSH-подключении, чтобы вывести данные в новом каталоге:

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="limitations"></a>Ограничения

* Массовый экспорт: при использовании HDInsight на основе Linux соединитель Sqoop, применяемый для экспорта данных на сервер Microsoft SQL Server или в Базу данных SQL Azure, не поддерживает операции массовой вставки.

* Пакетная обработка: при использовании HDInsight на основе Linux, когда для выполнения вставок применяется параметр`-batch`, Sqoop выполняет несколько вставок вместо пакетной обработки операций вставки.

## <a name="important-considerations"></a>Важные сведения

* HDInsight и SQL Server должны находиться в одной виртуальной сети Azure.

    Пример см. в статье о [подключении HDInsight к локальной сети](./../connect-on-premises-network.md).

    Подробные сведения об использовании HDInsight c виртуальными сетями Azure см. в статье [Расширение возможностей HDInsight с помощью виртуальной сети Azure](../hdinsight-plan-virtual-network-deployment.md). Дополнительные сведения см. в статье [Виртуальная сеть Azure](../../virtual-network/virtual-networks-overview.md).

* Также SQL Server должен разрешать аутентификацию SQL. Дополнительные сведения см. в документе [Выбор режима проверки подлинности](https://msdn.microsoft.com/ms144284.aspx).

* Необходимо настроить SQL Server для удаленных соединений. Дополнительные сведения см. в документе [How to troubleshoot connecting to the SQL Server database engine](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) (Устранение неполадок при подключении к ядру СУБД SQL Server).

## <a name="next-steps"></a>Следующие шаги

Теперь вы узнали, как использовать Sqoop. Дополнительные сведения см. на следующих ресурсах:

* [Использование Apache Oozie с HDInsight](../hdinsight-use-oozie-linux-mac.md). Используйте действие Sqoop в рабочем процессе Oozie.
* [Анализ данных о задержке рейсов с помощью HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md). Используйте интерактивный запрос для анализа данных о задержке рейсов, а затем используйте Sqoop для экспорта данных в базу данных SQL Azure.
* [Отправка данных в HDInsight](../hdinsight-upload-data.md). Узнайте о других способах отправки данных в HDInsight или хранилище BLOB-объектов Azure.
