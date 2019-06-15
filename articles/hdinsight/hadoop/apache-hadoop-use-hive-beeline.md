---
title: Использование Apache Beeline с Apache Hive в Azure HDInsight
description: Сведения о выполнении запросов Hive с помощью Hadoop в HDInsight с использованием клиента Beeline. Beeline — это служебная программа для работы с HiveServer2 через JDBC.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: hrasheed
ms.openlocfilehash: dcfcd4b55f848e1725e286e6ef2a87a2c36e5a71
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64684933"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Использование клиента Apache Beeline с Apache Hive

Узнайте, как использовать [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) для выполнения запросов Apache Hive в HDInsight.

Beeline — это клиент Hive, установленный на головных узлах кластера HDInsight. Он подключается к службе HiveServer2, размещенной на кластере HDInsight, с помощью JDBC. Beeline также позволяет удаленно подключаться к Hive в HDInsight через Интернет. В примерах ниже содержатся наиболее распространенные строки подключения, используемые для подключения к HDInsight из Beeline:

## <a name="types-of-connections"></a>Типы подключений

### <a name="from-an-ssh-session"></a>Из сеанса SSH

При подключении из сеанса SSH к головному узлу кластера, затем можно подключиться к `headnodehost` адрес на порте `10001`:

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

---

### <a name="over-an-azure-virtual-network"></a>Через виртуальную сеть Azure

При подключении из клиента к HDInsight через виртуальную сеть Azure, необходимо указать полное доменное имя (FQDN) головного узла кластера. Так как подключение устанавливается напрямую к узлам кластера, для подключения используется порт `10001`:

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Замените `<headnode-FQDN>` с полное доменное имя головного узла кластера. Чтобы найти полное доменное имя головного узла, используйте сведения из [этого раздела](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes).

---

### <a name="to-hdinsight-enterprise-security-package-esp-cluster"></a>Кластер HDInsight Enterprise безопасности пакета (ESP)

При подключении из клиента к кластеру пакета безопасности предприятия (ESP) присоединен к Azure Active Directory (AAD), необходимо также указать имя домена `<AAD-Domain>` и имя учетной записи пользователя домена с разрешениями на доступ к кластеру `<username>`:

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Замените `<username>` на имя учетной записи домена с разрешениями на доступ к кластеру. Замените `<AAD-DOMAIN>` с именем из Azure Active Directory (AAD), присоединенного к кластеру. Используйте строку в верхнем регистре для `<AAD-DOMAIN>` значение, в противном случае учетные данные не будет найден. Проверьте `/etc/krb5.conf` для области имен при необходимости.

---

### <a name="over-public-internet"></a>Через общедоступный Интернет

При подключении через общедоступный Интернет необходимо указать имя учетной записи для входа в кластер (по умолчанию `admin`) и пароль. Например, при использовании Beeline из системы клиента для подключения к адресу `<clustername>.azurehdinsight.net`. Это подключение устанавливается через порт `443` и шифруется с помощью SSL:

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password
```

Замените `clustername` на имя вашего кластера HDInsight. Замените `admin` учетной записью для входа в кластер. Замените `password` паролем этой учетной записи.

---

### <a id="sparksql"></a>Использование Beeline с Apache Spark

Apache Spark предоставляет собственную реализацию HiveServer2, которая иногда называется сервером Thrift Spark. Для разрешения запросов эта служба использует Spark SQL вместо Hive и может обеспечить более высокую производительность в зависимости от запроса.

#### <a name="over-public-internet-with-apache-spark"></a>Через общедоступный Интернет с помощью Apache Spark

Строка подключения, используемая при подключении через Интернет, немного отличается. Вместо `httpPath=/hive2` это `httpPath/sparkhive2`:

```bash 
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p password
```

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>Из кластера головной или внутри виртуальной сети Azure с помощью Apache Spark

При подключении непосредственно из головного узла кластера или из ресурса в той же виртуальной сети Azure, что и кластер HDInsight, вместо порта `10001` для сервера Spark Thrift нужно использовать порт `10002`. В следующем примере показано, как подключиться непосредственно к головному узлу:

```bash
beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a id="prereq"></a>Предварительные требования

* Кластер Hadoop в HDInsight. Ознакомьтесь со статьей [Краткое руководство. Использование Apache Hadoop и Apache Hive в Azure HDInsight с шаблоном Resource Manager](./apache-hadoop-linux-tutorial-get-started.md).

* Обратите внимание, что [схема URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) для основного хранилища кластера. Например `wasb://` для службы хранилища Azure, `abfs://` для Gen2 хранилища Озера данных Azure, или `adl://` для Gen1 хранилища Озера данных Azure. Если безопасной передачи включено для службы хранилища Azure или Gen2 хранилища Озера данных, URL-адрес является `wasbs://` или `abfss://`, соответственно. Дополнительные сведения см. в разделе [безопасное перемещение](../../storage/common/storage-require-secure-transfer.md).


* Вариант 1. Клиент SSH. Дополнительные сведения см. в руководстве по [подключению к HDInsight (Apache Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). Большинство действий, описанных в этом руководстве, выполняются в клиенте Beeline из сеанса SSH в кластере.

* Вариант 2.  Локальный клиент Beeline.


## <a id="beeline"></a>Выполнение запроса Hive

Этот пример основан на использование клиента Beeline с помощью SSH-подключения.

1. Откройте подключение SSH к кластеру, приведенный ниже код. Замените `sshuser` именем пользователя SSH для кластера, а `CLUSTERNAME` — именем кластера. При запросе введите пароль для учетной записи пользователя SSH.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Подключение к HiveServer2 с помощью клиента Beeline из сеанса SSH откройте, введя следующую команду:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. Команды Beeline начинаются со знака `!`. Например, `!help` выводит справку. Однако в некоторых командах `!` можно опустить. Например, `help` также работает.

    Для выполнения инструкций HiveQL используется `!sql`. Однако эти инструкции настолько распространены, что `!sql`тоже можно опустить. Приведенные ниже две инструкции эквивалентны.

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

5. Введите следующие инструкции, чтобы создать таблицу **log4jLogs**, используя демонстрационные данные, предоставляемые с кластером HDInsight. (На основе исправить при необходимости вашей [схема URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme).)

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

    Эти операторы выполняют следующие действия:

    * `DROP TABLE`. Если таблица уже создана, она будет удалена.

    * `CREATE EXTERNAL TABLE`. Создает **внешнюю** таблицу в Hive. Внешние таблицы хранят только определения таблицы в Hive. Данные остаются в исходном расположении.

    * `ROW FORMAT` — настройка форматирования данных. В данном случае поля всех журналов разделены пробелом.

    * `STORED AS TEXTFILE LOCATION`. Указывает расположение для хранения данных и их формат.

    * `SELECT`. Подсчитывает количество строк, в которых столбец **t4** содержит значение **[ERROR]** . Этот запрос должен вернуть значение **3**, так как таблица содержит три строки с данным значением.

    * `INPUT__FILE__NAME LIKE '%.log'`. Hive пытается применить схему ко всем файлам в каталоге. В этом случае каталог содержит файлы, которые не соответствуют схеме. Чтобы исключить лишние данные в результатах, эта инструкция указывает Hive возвращать данные только из файлов, заканчивающихся на .log.

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

Это продолжение из предыдущего примера. Чтобы создать файл, а затем запустить его с помощью Beeline, выполните следующие действия.

1. Создайте файл **query.hql**, используя следующую команду.

    ```bash
    nano query.hql
    ```

2. В качестве содержимого файла добавьте следующий текст: Этот запрос создает "внутреннюю" таблицу **errorLogs**.

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Эти операторы выполняют следующие действия:

   * **CREATE TABLE IF NOT EXISTS** — создание таблицы, если ее не существует. Так как не используется ключевое слово **EXTERNAL**, эта инструкция создает внутреннюю таблицу. Внутренние таблицы хранятся в хранилище данных Hive и полностью управляются Hive.
   * **STORED AS ORC** : хранение данных в формате ORC (Optimized Row Columnar). Это высокооптимизированный и эффективный формат для хранения данных Hive.
   * **INSERT OVERWRITE ... SELECT**: выбирает строки из таблицы **log4jLogs**, которые содержат значение **[ERROR]** , а затем вставляет данные в таблицу **errorLogs**.

    > [!NOTE]  
    > В отличие от внешних таблиц, удаление внутренней таблицы приводит к удалению базовых данных.

3. Чтобы сохранить файл, нажмите клавиши **CTRL**+ **+X**, введите **Y** и нажмите клавишу **ВВОД**.

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
        3 rows selected (1.538 seconds)




## <a id="summary"></a><a id="nextsteps"></a>Дальнейшие действия

Дополнительные общие сведения об использовании Hadoop в HDInsight см. в следующем документе:

* [Использование Apache Hive с Apache Hadoop в HDInsight](hdinsight-use-hive.md)

Дополнительные сведения о способах работы с Hadoop в HDInsight см. в следующих документах:

* [Использование Apache Pig с Apache Hadoop в HDInsight](hdinsight-use-pig.md)
* [Использование MapReduce в Apache Hadoop в HDInsight](hdinsight-use-mapreduce.md)

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md

[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html


[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx
