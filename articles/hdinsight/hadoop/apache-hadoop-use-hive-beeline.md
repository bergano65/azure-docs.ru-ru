---
title: Использование Apache Beeline с Apache Hive в Azure HDInsight
description: Сведения о выполнении запросов Hive с помощью Hadoop в HDInsight с использованием клиента Beeline. Beeline — это служебная программа для работы с HiveServer2 через JDBC.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: 26a166e61086af8cf10f761b608fcf66eb8734fd
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406251"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Использование клиента Apache Beeline с Apache Hive

Узнайте, как использовать [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) для выполнения запросов Apache Hive в HDInsight.

Beeline — это клиент Hive, установленный на головных узлах кластера HDInsight. Он подключается к службе HiveServer2, размещенной на кластере HDInsight, с помощью JDBC. Beeline также позволяет удаленно подключаться к Hive в HDInsight через Интернет. В примерах ниже содержатся наиболее распространенные строки подключения, используемые для подключения к HDInsight из Beeline:

## <a name="types-of-connections"></a>Types of connections

### <a name="from-an-ssh-session"></a>From an SSH session

When connecting from an SSH session to a cluster headnode, you can then connect to the `headnodehost` address on port `10001`:

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

---

### <a name="over-an-azure-virtual-network"></a>Over an Azure Virtual Network

When connecting from a client to HDInsight over an Azure Virtual Network, you must provide the fully qualified domain name (FQDN) of a cluster head node. Так как подключение устанавливается напрямую к узлам кластера, для подключения используется порт `10001`:

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Replace `<headnode-FQDN>` with the fully qualified domain name of a cluster headnode. Чтобы найти полное доменное имя головного узла, используйте сведения из [этого раздела](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes).

---

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>To HDInsight Enterprise Security Package (ESP) cluster using Kerberos

When connecting from a client to an Enterprise Security Package (ESP) cluster joined to Azure Active Directory (AAD)-DS on a machine in same realm of the cluster, you must also specify the domain name `<AAD-Domain>` and the name of a domain user account with permissions to access the cluster `<username>`:

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Замените `<username>` на имя учетной записи домена с разрешениями на доступ к кластеру. Replace `<AAD-DOMAIN>` with the name of the Azure Active Directory (AAD) that the cluster is joined to. Use an uppercase string for the `<AAD-DOMAIN>` value, otherwise the credential won't be found. Check `/etc/krb5.conf` for the realm names if needed.

---

### <a name="over-public-or-private-endpoints"></a>Over public or private endpoints

When connecting to a cluster using the public or private endpoints, you must provide the cluster login account name (default `admin`) and password. Например, при использовании Beeline из системы клиента для подключения к адресу `<clustername>.azurehdinsight.net`. Это подключение устанавливается через порт `443` и шифруется с помощью SSL:

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n <username> -p password
```

or for private endpoint:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n <username> -p password
```

Замените `clustername` на имя вашего кластера HDInsight. Замените `<username>` учетной записью для входа в кластер. For ESP clusters, use the full UPN (e.g. user@domain.com). Замените `password` паролем этой учетной записи.

Private endpoints point to a basic load balancer, which can only be accessed from the VNETs peered in the same region. See [constraints on global VNet peering and load balancers](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) for more info. You can use the `curl` command with `-v` option to troubleshoot any connectivity problems with public or private endpoints before using beeline.

---

### <a id="sparksql"></a>Использование Beeline с Apache Spark

Apache Spark предоставляет собственную реализацию HiveServer2, которая иногда называется сервером Thrift Spark. Для разрешения запросов эта служба использует Spark SQL вместо Hive и может обеспечить более высокую производительность в зависимости от запроса.

#### <a name="through-public-or-private-endpoints"></a>Through public or private endpoints

The connection string used  is slightly different. Instead of containing `httpPath=/hive2` it's `httpPath/sparkhive2`:

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n <username> -p password
```

or for private endpoint:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n <username> -p password
```

Замените `clustername` на имя вашего кластера HDInsight. Замените `<username>` учетной записью для входа в кластер. For ESP clusters, use the full UPN (e.g. user@domain.com). Замените `password` паролем этой учетной записи.

Private endpoints point to a basic load balancer, which can only be accessed from the VNETs peered in the same region. See [constraints on global VNet peering and load balancers](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) for more info. You can use the `curl` command with `-v` option to troubleshoot any connectivity problems with public or private endpoints before using beeline.

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>From cluster head or inside Azure Virtual Network with Apache Spark

При подключении непосредственно из головного узла кластера или из ресурса в той же виртуальной сети Azure, что и кластер HDInsight, вместо порта `10001` для сервера Spark Thrift нужно использовать порт `10002`. The following example shows how to connect directly to the head node:

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a id="prereq"></a>Предварительные требования

* A Hadoop cluster on HDInsight. Ознакомьтесь со статьей [Краткое руководство. Использование Apache Hadoop и Apache Hive в Azure HDInsight с шаблоном Resource Manager](./apache-hadoop-linux-tutorial-get-started.md).

* Notice the [URI scheme](../hdinsight-hadoop-linux-information.md#URI-and-scheme) for your cluster's primary storage. For example,  `wasb://` for Azure Storage, `abfs://` for Azure Data Lake Storage Gen2, or `adl://` for Azure Data Lake Storage Gen1. If secure transfer is enabled for Azure Storage, the URI is `wasbs://`. For more information, see [secure transfer](../../storage/common/storage-require-secure-transfer.md).

* Option 1: An SSH client. Дополнительные сведения см. в руководстве по [подключению к HDInsight (Apache Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). Most of the steps in this document assume that you're using Beeline from an SSH session to the cluster.

* Option 2:  A local Beeline client.

## <a id="beeline"></a>Выполнение запроса Hive

This example is based on using the Beeline client from an SSH connection.

1. Open an SSH connection to the cluster with the code below. Замените `sshuser` именем пользователя SSH для кластера, а `CLUSTERNAME` — именем кластера. When prompted, enter the password for the SSH user account.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Connect to HiveServer2 with your Beeline client from your open SSH session by entering the following command:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. Команды Beeline начинаются со знака `!`. Например, `!help` выводит справку. Однако в некоторых командах `!` можно опустить. Например, `help` также работает.

    There's `!sql`, which is used to execute HiveQL statements. Однако эти инструкции настолько распространены, что `!sql`тоже можно опустить. Приведенные ниже две инструкции эквивалентны.

    ```hiveql
    !sql show tables;
    show tables;
    ```

    В новом кластере отображена только одна таблица, **hivesampletable**.

4. Используйте следующую команду, чтобы отобразить схему для таблицы hivesampletable.

    ```hiveql
    describe hivesampletable;
    ```

    Эта команда возвращает приведенные ниже сведения.

        +-----------------------+------------+----------+--+
        |       col_name        | data_type  | comment  |
        +-----------------------+------------+----------+--+
        | clientid              | string     |          |
        | querytime             | string     |          |
        | market                | string     |          |
        | deviceplatform        | string     |          |
        | devicemake            | string     |          |
        | devicemodel           | string     |          |
        | state                 | string     |          |
        | country               | string     |          |
        | querydwelltime        | double     |          |
        | sessionid             | bigint     |          |
        | sessionpagevieworder  | bigint     |          |
        +-----------------------+------------+----------+--+

    Они описывают столбцы в таблице.

5. Enter the following statements to create a table named **log4jLogs** by using sample data provided with the HDInsight cluster: (Revise as needed based on your [URI scheme](../hdinsight-hadoop-linux-information.md#URI-and-scheme).)

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs 
        WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' 
        GROUP BY t4;
    ```

    These statements do the following actions:

    * `DROP TABLE` - If the table exists, it's deleted.

    * `CREATE EXTERNAL TABLE`. Создает **внешнюю** таблицу в Hive. Внешние таблицы хранят только определения таблицы в Hive. Данные остаются в исходном расположении.

    * `ROW FORMAT` — настройка форматирования данных. В данном случае поля всех журналов разделены пробелом.

    * `STORED AS TEXTFILE LOCATION`. Указывает расположение для хранения данных и их формат.

    * `SELECT`. Подсчитывает количество строк, в которых столбец **t4** содержит значение **[ERROR]** . Этот запрос должен вернуть значение **3**, так как таблица содержит три строки с данным значением.

    * `INPUT__FILE__NAME LIKE '%.log'`. Hive пытается применить схему ко всем файлам в каталоге. In this case, the directory contains files that don't match the schema. Чтобы исключить лишние данные в результатах, эта инструкция указывает Hive возвращать данные только из файлов, заканчивающихся на .log.

   > [!NOTE]  
   > Внешние таблицы следует использовать, если исходные данные должны обновляться с использованием внешних источников. Например, процессом автоматизированной передачи данных или другой операцией MapReduce.
   >
   > Удаление внешней таблицы **не** приводит к удалению данных, будет удалено только определение таблицы.

    После выполнения этой команды вы должны увидеть текст, аналогичный приведенному ниже.

        INFO  : Tez session hasn't been created yet. Opening session
        INFO  :

        INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)

        INFO  : Map 1: -/-      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
        INFO  : Map 1: 1/1      Reducer 2: 1/1
        +----------+--------+--+
        |   sev    | count  |
        +----------+--------+--+
        | [ERROR]  | 3      |
        +----------+--------+--+
        1 row selected (47.351 seconds)

6. Чтобы выйти из Beeline, используйте инструкцию `!exit`.

## <a id="file"></a>Запуск файла HiveQL.

This is a continuation from the prior example. Чтобы создать файл, а затем запустить его с помощью Beeline, выполните следующие действия.

1. Создайте файл **query.hql**, используя следующую команду.

    ```bash
    nano query.hql
    ```

2. В качестве содержимого файла добавьте следующий текст: Этот запрос создает "внутреннюю" таблицу **errorLogs**.

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    These statements do the following actions:

   * **CREATE TABLE IF NOT EXISTS** - If the table doesn't already exist, it's created. Since the **EXTERNAL** keyword isn't used, this statement creates an internal table. Внутренние таблицы хранятся в хранилище данных Hive и полностью управляются Hive.
   * **STORED AS ORC** — сохраняет данные в формате Optimized Row Columnar (ORC). Это высокооптимизированный и эффективный формат для хранения данных Hive.
   * **INSERT OVERWRITE ... SELECT** - Selects rows from the **log4jLogs** table that contain **[ERROR]** , then inserts the data into the **errorLogs** table.

    > [!NOTE]  
    > В отличие от внешних таблиц, удаление внутренней таблицы приводит к удалению базовых данных.

3. To save the file, use **Ctrl**+**X**, then enter **Y**, and finally **Enter**.

4. Запустите файл с помощью Beeline, используя следующую команду:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > Параметр `-i` запускает Beeline и выполняет инструкции в файле `query.hql`. После выполнения запроса отобразится командная строка `jdbc:hive2://headnodehost:10001/>`. Можно также выполнить файл с помощью параметра `-f`, который завершает работу Beeline после завершения выполнения запроса.

5. Чтобы убедиться, что таблица **errorLogs** создана, выполните приведенную ниже инструкцию (она выводит все строки из таблицы **errorLogs**).

    ```hiveql
    SELECT * from errorLogs;
    ```

    В результате операции должны быть возвращены три строки со значением **[ERROR]** в столбце t4.

        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (0.813 seconds)

## <a id="summary"></a><a id="nextsteps"></a>Дальнейшие действия

* For more general information on Hive in HDInsight, see [Use Apache Hive with Apache Hadoop on HDInsight](hdinsight-use-hive.md)

* For more information on other ways you can work with Hadoop on HDInsight, see [Use MapReduce with Apache Hadoop on HDInsight](hdinsight-use-mapreduce.md)
