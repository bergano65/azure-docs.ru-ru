---
title: Использование Apache Sqoop с Apache Hadoop в Azure HDInsight
description: Сведения об использовании Apache Sqoop для импорта и экспорта между Apache Hadoop в HDInsight и базой данных SQL Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/28/2019
ms.openlocfilehash: 21bc903349876a76576fb742840e9899f9d94bcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74769393"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-sql-database"></a>Использование Apache Sqoop для импорта и экспорта между Apache Hadoop в HDInsight и базой данных SQL

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Узнайте, как использовать Apache Sqoop для импорта и экспорта между кластером Apache Hadoop в Azure HDInsight и Базой данных SQL Azure или базой данных Microsoft SQL Server. В этом руководстве используется команда `sqoop` ​​непосредственно из головного узла кластера Hadoop. В этом документе вы подключитесь к головному узлу, используя SSH, и выполните команды. Эта статья является продолжением [использования Apache Sqoop с Hadoop в HDInsight](./hdinsight-use-sqoop.md).

## <a name="prerequisites"></a>Предварительные требования

* Завершение [настройки тестовой среды](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) от [Use Apache Sqoop с Hadoop в HDInsight](./hdinsight-use-sqoop.md).

* Клиент SSH. Дополнительные сведения см. в руководстве по [подключению к HDInsight (Apache Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Знакомство с Sqoop. Для получения дополнительной информации [см.](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html)

## <a name="set-up"></a>Настройка

1. Используйте [команду ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) для подключения к кластеру. Отоверьте приведенную ниже команду, заменив CLUSTERNAME на имя кластера, а затем введите команду:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Для удобства использования установите переменные. `PASSWORD`Заменить `MYSQLSERVER`, `MYDATABASE` и с соответствующими значениями, а затем ввести команды ниже:

    ```bash
    export password='PASSWORD'
    export sqlserver="MYSQLSERVER"
    export database="MYDATABASE"


    export serverConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password"
    export serverDbConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password;database=$database"
    ```

## <a name="sqoop-export"></a>Экспорт Sqoop

От Улейа до сервера S'L.

1. Чтобы убедиться, что Sqoop может видеть вашу базу данных S'L, введите команду ниже в открытом sSH-соединении. Эта команда возвращает список баз данных.

    ```bash
    sqoop list-databases --connect $serverConnect
    ```

1. Введите следующую команду, чтобы увидеть список таблиц для указанной базы данных:

    ```bash
    sqoop list-tables --connect $serverDbConnect
    ```

1. Для экспорта данных `hivesampletable` из `mobiledata` таблицы Hive в таблицу базы данных S'L введите приведенную ниже команду в открытом SSH-соединении:

    ```bash
    sqoop export --connect $serverDbConnect \
    -table mobiledata \
    --hcatalog-table hivesampletable
    ```

1. Чтобы убедиться, что данные были экспортированы, используйте следующие запросы из соединения SSH для просмотра экспортированных данных:

    ```bash
    sqoop eval --connect $serverDbConnect \
    --query "SELECT COUNT(*) from dbo.mobiledata WITH (NOLOCK)"


    sqoop eval --connect $serverDbConnect \
    --query "SELECT TOP(10) * from dbo.mobiledata WITH (NOLOCK)"
    ```

## <a name="sqoop-import"></a>Импорт Sqoop

От сервера S'L до хранилища Azure.

1. Введите приведенную ниже команду в открытом `mobiledata` SSH-соединении, `wasbs:///tutorials/usesqoop/importeddata` импортируйте данные из таблицы базы данных S'L в каталог HDInsight. Поля в данных разделены знаками табуляции, а строки завершаются символом новой строки.

    ```bash
    sqoop import --connect $serverDbConnect \
    --table mobiledata \
    --target-dir 'wasb:///tutorials/usesqoop/importeddata' \
    --fields-terminated-by '\t' \
    --lines-terminated-by '\n' -m 1
    ```

1. Кроме того, можно также указать таблицу Hive:

    ```bash
    sqoop import --connect $serverDbConnect \
    --table mobiledata \
    --target-dir 'wasb:///tutorials/usesqoop/importeddata2' \
    --fields-terminated-by '\t' \
    --lines-terminated-by '\n' \
    --create-hive-table \
    --hive-table mobiledata_imported2 \
    --hive-import -m 1
    ```

1. После завершения импорта введите следующую команду в открытом sSH-соединении, чтобы перечислить данные в новом каталоге:

    ```bash
    hadoop fs -tail /tutorials/usesqoop/importeddata/part-m-00000
    ```

1. Используйте ["Билайн",](./apache-hadoop-use-hive-beeline.md) чтобы убедиться, что таблица была создана в Hive.

    1. Подключение

        ```bash
        beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
        ```

    1. Выполните каждый запрос ниже одного за один раз и просмотрите вывод:

        ```hql
        show tables;
        describe mobiledata_imported2;
        SELECT COUNT(*) FROM mobiledata_imported2;
        SELECT * FROM mobiledata_imported2 LIMIT 10;
        ```

    1. Выход Билайн `!exit`с .

## <a name="limitations"></a>Ограничения

* Массовый экспорт - С помощью HDInsight на базе Linux разъем Sqoop, используемый для экспорта данных в Microsoft S'L Server или базу данных Azure S'L, не поддерживает объемные вставки.

* Пакетная обработка: при использовании HDInsight на основе Linux, когда для выполнения вставок применяется параметр`-batch`, Sqoop выполняет несколько вставок вместо пакетной обработки операций вставки.

## <a name="important-considerations"></a>Важные сведения

* HDInsight и SQL Server должны находиться в одной виртуальной сети Azure.

    Пример см. в статье о [подключении HDInsight к локальной сети](./../connect-on-premises-network.md).

    Подробные сведения об использовании HDInsight c виртуальными сетями Azure см. в статье [Расширение возможностей HDInsight с помощью виртуальной сети Azure](../hdinsight-plan-virtual-network-deployment.md). Дополнительные сведения см. в статье [Виртуальная сеть Azure](../../virtual-network/virtual-networks-overview.md).

* Также SQL Server должен разрешать аутентификацию SQL. Дополнительные сведения см. в документе [Выбор режима проверки подлинности](https://msdn.microsoft.com/ms144284.aspx).

* Необходимо настроить SQL Server для удаленных соединений. Дополнительные сведения см. в документе [How to troubleshoot connecting to the SQL Server database engine](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) (Устранение неполадок при подключении к ядру СУБД SQL Server).

## <a name="next-steps"></a>Дальнейшие действия

Теперь вы узнали, как использовать Sqoop. Дополнительные сведения см. на следующих ресурсах:

* [Используйте Apache Oozie с HDInsight:](../hdinsight-use-oozie-linux-mac.md)Используйте действие Sqoop в рабочем процессе Oozie.
* [Проанализируйте данные о задержке рейса с помощью HDInsight:](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)Используйте интерактивный запрос для анализа данных о задержке рейса, а затем используйте Sqoop для экспорта данных в базу данных Azure S'L.
* [Передача данных в HDInsight](../hdinsight-upload-data.md): узнайте о других способах отправки данных в HDInsight и хранилище больших двоичных объектов Azure.
