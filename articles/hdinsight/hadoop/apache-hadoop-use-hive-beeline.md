---
title: Использование Apache Beeline с Apache Hive в Azure HDInsight
description: Сведения о выполнении запросов Hive с помощью Hadoop в HDInsight с использованием клиента Beeline. Beeline — это служебная программа для работы с HiveServer2 через JDBC.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 13c51f0db468c1591ca29de17f1744752589a1c8
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77663751"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Использование клиента Apache Beeline с Apache Hive

Узнайте, как использовать [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) для выполнения запросов Apache Hive в HDInsight.

Beeline — это клиент Hive, установленный на головных узлах кластера HDInsight. Чтобы установить Beeline локально, см. раздел [Install Beeline Client (установка клиента](#install-beeline-client)) ниже. Он подключается к службе HiveServer2, размещенной на кластере HDInsight, с помощью JDBC. Beeline также позволяет удаленно подключаться к Hive в HDInsight через Интернет. В следующих примерах приведены наиболее распространенные строки подключения, используемые для подключения к HDInsight из Beeline.

## <a name="types-of-connections"></a>Типы подключений

### <a name="from-an-ssh-session"></a>Из сеанса SSH

При подключении из сеанса SSH к кластеру головного узла можно подключиться к `headnodehost` адресу в `10001`порта:

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

---

### <a name="over-an-azure-virtual-network"></a>Через виртуальную сеть Azure

При подключении клиента к HDInsight через виртуальную сеть Azure необходимо указать полное доменное имя головного узла кластера. Так как подключение устанавливается напрямую к узлам кластера, для подключения используется порт `10001`:

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Замените `<headnode-FQDN>` на полное доменное имя кластера головного узла. Чтобы найти полное доменное имя головного узла, используйте сведения из [этого раздела](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes).

---

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>В кластер HDInsight Корпоративный пакет безопасности (ESP) по протоколу Kerberos

При подключении клиента к кластеру Корпоративный пакет безопасности (ESP), присоединенному к Azure Active Directory (AAD) — DS на компьютере в той же области кластера, необходимо также указать доменное имя `<AAD-Domain>` и имя учетной записи пользователя домена с разрешениями на доступ к `<username>`кластера:

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Замените `<username>` на имя учетной записи домена с разрешениями на доступ к кластеру. Замените `<AAD-DOMAIN>` именем Azure Active Directory (AAD), к которому присоединен кластер. Используйте строку в верхнем регистре для значения `<AAD-DOMAIN>`. в противном случае учетные данные не будут найдены. При необходимости проверьте `/etc/krb5.conf` для имен областей.

---

### <a name="over-public-or-private-endpoints"></a>Через общедоступные или частные конечные точки

При подключении к кластеру с помощью общедоступных или частных конечных точек необходимо указать имя учетной записи для входа в кластер (по умолчанию `admin`) и пароль. Например, при использовании Beeline из системы клиента для подключения к адресу `clustername.azurehdinsight.net`. Это подключение выполняется через порт `443`и шифруется с помощью SSL.

Замените `clustername` на имя вашего кластера HDInsight. Замените `admin` учетной записью для входа в кластер. Для кластеров ESP используйте полное имя участника-пользователя (например, user@domain.com). Замените `password` паролем этой учетной записи.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

или для частной конечной точки:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

Частные конечные точки указывают на базовую подсистему балансировки нагрузки, доступ к которой можно получить только с виртуальных сетей пиринга в том же регионе. Дополнительные сведения см. [в статье ограничения для пиринга глобальной виртуальной сети и подсистем балансировки нагрузки](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) . Можно использовать команду `curl` с параметром `-v` для устранения неполадок подключения к общедоступным или частным конечным точкам перед использованием Beeline.

---

### <a name="use-beeline-with-apache-spark"></a>Использование Beeline с Apache Spark

Apache Spark предоставляет собственную реализацию HiveServer2, которая иногда называется сервером Thrift Spark. Для разрешения запросов эта служба использует Spark SQL вместо Hive и может обеспечить более высокую производительность в зависимости от запроса.

#### <a name="through-public-or-private-endpoints"></a>Через общедоступные или частные конечные точки

Используемая строка подключения немного отличается. Вместо того, чтобы содержать `httpPath=/hive2` его `httpPath/sparkhive2`. Замените `clustername` на имя вашего кластера HDInsight. Замените `admin` учетной записью для входа в кластер. Для кластеров ESP используйте полное имя участника-пользователя (например, user@domain.com). Замените `password` паролем этой учетной записи.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

или для частной конечной точки:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

Частные конечные точки указывают на базовую подсистему балансировки нагрузки, доступ к которой можно получить только с виртуальных сетей пиринга в том же регионе. Дополнительные сведения см. [в статье ограничения для пиринга глобальной виртуальной сети и подсистем балансировки нагрузки](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) . Можно использовать команду `curl` с параметром `-v` для устранения неполадок подключения к общедоступным или частным конечным точкам перед использованием Beeline.

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>Из головного кластера или внутри виртуальной сети Azure с Apache Spark

При подключении непосредственно из головного узла кластера или из ресурса в той же виртуальной сети Azure, что и кластер HDInsight, вместо порта `10002` для сервера Spark Thrift нужно использовать порт `10001`. В следующем примере показано, как подключиться непосредственно к головному узлу:

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a name="prerequisites-for-examples"></a>Предварительные требования для примеров

* Кластер Hadoop в HDInsight. Ознакомьтесь со статьей [Краткое руководство. Использование Apache Hadoop и Apache Hive в Azure HDInsight с шаблоном Resource Manager](./apache-hadoop-linux-tutorial-get-started.md).

* Обратите внимание на [схему URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) для основного хранилища кластера. Например, `wasb://` для службы хранилища Azure, `abfs://` для Azure Data Lake Storage 2-го поколения или `adl://` для Azure Data Lake Storage 1-го поколения. Если для службы хранилища Azure включено безопасное перемещение, универсальный код ресурса (URI) `wasbs://`. Дополнительные сведения см. в разделе [безопасное перемещение](../../storage/common/storage-require-secure-transfer.md).

* Вариант 1. клиент SSH. Дополнительные сведения см. в руководстве по [подключению к HDInsight (Apache Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). В большинстве действий, описанных в этом документе, предполагается, что вы используете Beeline из сеанса SSH в кластере.

* Вариант 2. локальный клиент Beeline.

## <a name="run-a-hive-query"></a>Выполнение запроса Hive

Этот пример основан на использовании клиента Beeline из SSH-подключения.

1. Откройте SSH-подключение к кластеру, используя приведенный ниже код. Замените `sshuser` именем пользователя SSH для кластера, а `CLUSTERNAME` — именем кластера. При появлении запроса введите пароль для учетной записи пользователя SSH.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Подключитесь к HiveServer2 с помощью клиента Beeline из открытого сеанса SSH, введя следующую команду:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. Команды Beeline начинаются со знака `!`. Например, `!help` выводит справку. Однако в некоторых командах `!` можно опустить. Например, `help` также работает.

    `!sql`, который используется для выполнения инструкций HiveQL. Однако эти инструкции настолько распространены, что `!sql`тоже можно опустить. Приведенные ниже две инструкции эквивалентны.

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

5. Введите следующие инструкции, чтобы создать таблицу с именем **log4jLogs** с помощью демонстрационных данных, предоставляемых в кластере HDInsight: (внесите необходимые изменения в соответствии со [схемой URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme).)

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

    Эти инструкции выполняют следующие действия:

    |Выписка |Описание |
    |---|---|
    |DROP TABLE|Если таблица существует, она удаляется.|
    |СОЗДАТЬ ВНЕШНЮЮ ТАБЛИЦУ|Создает **внешнюю** таблицу в Hive. Внешние таблицы хранят только определения таблицы в Hive. Данные остаются в исходном расположении.|
    |ФОРМАТ СТРОКИ|Форматирование данных. В данном случае поля всех журналов разделены пробелом.|
    |ХРАНИТСЯ КАК РАСПОЛОЖЕНИЕ TEXTFILE|Место хранения данных и формат файла.|
    |SELECT|выбирает подсчет количества строк, в которых столбец **t4** содержит значение **[ERROR]** . Этот запрос должен вернуть значение **3**, так как таблица содержит три строки с данным значением.|
    |INPUT__FILE__NAME, например "%. log"|Hive пытается применить схему ко всем файлам в каталоге. В этом случае каталог содержит файлы, которые не соответствуют схеме. Чтобы исключить лишние данные в результатах, эта инструкция указывает Hive возвращать данные только из файлов, заканчивающихся на .log.|

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

6. Выход из Beeline:

    ```bash
    !exit
    ```

## <a name="run-a-hiveql-file"></a>Запуск файла HiveQL

Это продолжение из прежнего примера. Чтобы создать файл, а затем запустить его с помощью Beeline, выполните следующие действия.

1. Создайте файл **query.hql**, используя следующую команду.

    ```bash
    nano query.hql
    ```

1. В качестве содержимого файла добавьте следующий текст: Этот запрос создает "внутреннюю" таблицу **errorLogs**.

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Эти инструкции выполняют следующие действия:

    |Выписка |Описание |
    |---|---|
    |CREATE TABLE, ЕСЛИ НЕ СУЩЕСТВУЕТ|Если таблица еще не существует, она создается. Поскольку ключевое слово **External** не используется, эта инструкция создает внутреннюю таблицу. Внутренние таблицы хранятся в хранилище данных Hive и полностью управляются Hive.|
    |ХРАНИТСЯ В ВИДЕ ORC|Позволяет сохранить данные в формате ORC. Это высокооптимизированный и эффективный формат для хранения данных Hive.|
    |ВСТАВИТЬ ПЕРЕЗАПИСЬ... МЕТЬТЕ|выбирает строки из таблицы **log4jLogs**, которые содержат значение **[ERROR]** , а затем вставляет данные в таблицу **errorLogs**.|

    > [!NOTE]  
    > В отличие от внешних таблиц, удаление внутренней таблицы приводит к удалению базовых данных.

1. Чтобы сохранить файл, используйте **сочетание клавиш Ctrl**+**X**, введите **Y**и, наконец, **введите**.

1. Запустите файл с помощью Beeline, используя следующую команду:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > Параметр `-i` запускает Beeline и выполняет инструкции в файле `query.hql`. После выполнения запроса отобразится командная строка `jdbc:hive2://headnodehost:10001/>`. Можно также выполнить файл с помощью параметра `-f`, который завершает работу Beeline после завершения выполнения запроса.

1. Чтобы убедиться, что таблица **errorLogs** создана, выполните приведенную ниже инструкцию (она выводит все строки из таблицы **errorLogs**).

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

## <a name="install-beeline-client"></a>Установка клиента Beeline

Хотя Beeline входит в состав головных узлов кластера HDInsight, может потребоваться установить его на локальном компьютере.  Приведенные ниже действия по установке Beeline на локальном компьютере основаны на [подсистеме Windows для Linux](https://docs.microsoft.com/windows/wsl/install-win10).

1. Обновление списков пакетов. Введите следующую команду в оболочке bash:

    ```bash
    sudo apt-get update
    ```

1. Установите Java, если он не установлен. Проверить можно с помощью команды `which java`.

    1. Если пакет Java не установлен, введите следующую команду:

        ```bash
        sudo apt install openjdk-11-jre-headless
        ```

    1. Откройте файл bashrc (обычно находится в ~ файл/.bashrc): `nano ~/.bashrc`.

    1. Измените файл bashrc. В конце файла добавьте следующую строку:

        ```bash
        export JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64
        ```

        Затем нажмите клавиши **CTRL + X**, затем **Y**, а затем введите.

1. Скачайте архивы Hadoop и Beeline, введите следующие команды:

    ```bash
    wget https://archive.apache.org/dist/hadoop/core/hadoop-2.7.3/hadoop-2.7.3.tar.gz
    wget https://archive.apache.org/dist/hive/hive-1.2.1/apache-hive-1.2.1-bin.tar.gz
    ```

1. Распакуйте архивы и введите следующие команды:

    ```bash
    tar -xvzf hadoop-2.7.3.tar.gz
    tar -xvzf apache-hive-1.2.1-bin.tar.gz
    ```

1. Дополнительные уточнение файла bashrc. Необходимо будет задать путь, по которому будут распакованы архивы. Если используется [подсистема Windows для Linux](https://docs.microsoft.com/windows/wsl/install-win10)и вы следовали инструкциям в точности, путь будет `/mnt/c/Users/user/`, где `user` является именем пользователя.

    1. Откройте файл: `nano ~/.bashrc`

    1. Измените приведенные ниже команды, указав соответствующий путь, а затем введите их в конце файла bashrc:

        ```bash
        export HADOOP_HOME=/path_where_the_archives_were_unpacked/hadoop-2.7.3
        export HIVE_HOME=/path_where_the_archives_were_unpacked/apache-hive-1.2.1-bin
        PATH=$PATH:$HIVE_HOME/bin
        ```

    1. Затем нажмите клавиши **CTRL + X**, затем **Y**, а затем введите.

1. Закройте и снова откройте сеанс bash.

1. Проверьте подключение. Используйте формат подключения [через общедоступные или частные конечные точки](#over-public-or-private-endpoints)выше.

## <a name="next-steps"></a>Следующие шаги

* Дополнительные общие сведения о Hive в HDInsight см. в статье [использование Apache Hive с Apache Hadoop в hdinsight](hdinsight-use-hive.md) .

* Дополнительные сведения о других способах работы с Hadoop в HDInsight см. в статье [Использование MapReduce с Apache Hadoop в hdinsight](hdinsight-use-mapreduce.md) .
