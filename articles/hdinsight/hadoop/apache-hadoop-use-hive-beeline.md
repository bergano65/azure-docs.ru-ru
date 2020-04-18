---
title: Использование Apache Beeline с Apache Hive в Azure HDInsight
description: Сведения о выполнении запросов Hive с помощью Hadoop в HDInsight с использованием клиента Beeline. Beeline — это служебная программа для работы с HiveServer2 через JDBC.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/17/2020
ms.openlocfilehash: 10e53b6b7b79e7d4581a1843b70b3d02778e8df5
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617790"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Использование клиента Apache Beeline с Apache Hive

Узнайте, как использовать [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) для выполнения запросов Apache Hive в HDInsight.

Beeline — это клиент Hive, установленный на головных узлах кластера HDInsight. Для установки Билайн локально, [см. Установите Билайн клиента](#install-beeline-client), ниже. Он подключается к службе HiveServer2, размещенной на кластере HDInsight, с помощью JDBC. Beeline также позволяет удаленно подключаться к Hive в HDInsight через Интернет. Следующие примеры предоставляют наиболее распространенные строки соединения, используемые для подключения к HDInsight от Билайн.

## <a name="types-of-connections"></a>Типы соединений

### <a name="from-an-ssh-session"></a>Из сеанса SSH

При подключении от сеанса SSH к кластеру headnode, вы можете подключиться к адресу в `headnodehost` порту: `10001`

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

---

### <a name="over-an-azure-virtual-network"></a>Над виртуальной сетью Azure

При подключении от клиента к HDInsight через виртуальную сеть Azure необходимо предоставить полностью квалифицированное доменное имя (ФЗДН) головного узла кластера. Так как подключение устанавливается напрямую к узлам кластера, для подключения используется порт `10001`:

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Замените `<headnode-FQDN>` полностью квалифицированное доменное имя кластерного headnode. Чтобы найти полное доменное имя головного узла, используйте сведения из [этого раздела](../hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes).

---

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>Для кластера HDInsight Enterprise Security Package (ESP) с помощью Kerberos

При подключении от клиента к кластеру Enterprise Security Package (ESP), присоединенном к Azure Active Directory (AAD)-DS `<AAD-Domain>` на машине в той же области кластера, необходимо также указать доменное имя и имя учетной записи пользователя домена с разрешениями на доступ к кластеру: `<username>`

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Замените `<username>` на имя учетной записи домена с разрешениями на доступ к кластеру. Замените `<AAD-DOMAIN>` имя активного каталога Azure (AAD), к которому присоединяется кластер. Используйте строку верхнего регистра `<AAD-DOMAIN>` для значения, в противном случае учетные данные не будут найдены. Проверьте `/etc/krb5.conf` имена царства, если это необходимо.

Чтобы найти URL-адрес JDBC от Ambari:

1. Из веб-браузера, `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary`перейдите к , где `CLUSTERNAME` имя вашего кластера. Убедитесь, что HiveServer2 работает.

1. Используйте буфер обмена для копирования URL-адреса HiveServer2 JDBC.

---

### <a name="over-public-or-private-endpoints"></a>Над государственными или частными конечными точками

При подключении к кластеру с помощью общедоступных или частных `admin`конечных точек необходимо укажите имя учетной записи кластера (по умолчанию) и пароль. Например, при использовании Beeline из системы клиента для подключения к адресу `clustername.azurehdinsight.net`. Это соединение производится `443`через порт и шифруется с помощью TLS/SSL.

Замените `clustername` на имя вашего кластера HDInsight. Замените `admin` учетной записью для входа в кластер. Для кластеров ESP используйте полный UPN (например, user@domain.com). Замените `password` паролем этой учетной записи.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

или для частной точки:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

Частные конечные точки указывают на базовый балансоровык нагрузки, к которому можно получить доступ только из ВНТ, заглянувшего в один и тот же регион. Для получения дополнительной информации можно найти [ограничения на глобальных вранительных и нагрузочных балансерах VNet.](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) Вы можете `curl` использовать `-v` команду с опцией для устранения любых проблем с подключением с государственными или частными конечными точками перед использованием Beeline.

---

### <a name="use-beeline-with-apache-spark"></a>Использование Beeline с Apache Spark

Apache Spark предоставляет собственную реализацию HiveServer2, которая иногда называется сервером Thrift Spark. Эта услуга использует Spark S'L для решения запросов вместо Hive. И может обеспечить лучшую производительность в зависимости от вашего запроса.

#### <a name="through-public-or-private-endpoints"></a>Через государственные или частные конечные точки

Используемая строка соединения немного отличается. Вместо того, чтобы содержать `httpPath=/hive2` его использует `httpPath/sparkhive2`. Замените `clustername` на имя вашего кластера HDInsight. Замените `admin` учетной записью для входа в кластер. Для кластеров ESP используйте полный UPN (например, user@domain.com). Замените `password` паролем этой учетной записи.

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

или для частной точки:

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

Частные конечные точки указывают на базовый балансоровык нагрузки, к которому можно получить доступ только из ВНТ, заглянувшего в один и тот же регион. Для получения дополнительной информации можно найти [ограничения на глобальных вранительных и нагрузочных балансерах VNet.](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) Вы можете `curl` использовать `-v` команду с опцией для устранения любых проблем с подключением с государственными или частными конечными точками перед использованием Beeline.

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>От главы кластера или внутри виртуальной сети Azure с Apache Spark

При подключении непосредственно из головного узла кластера или из ресурса в той же виртуальной сети Azure, что и кластер HDInsight, вместо порта `10001` для сервера Spark Thrift нужно использовать порт `10002`. Ниже приводится следующий пример, как подключиться непосредственно к головному узлам:

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a name="prerequisites-for-examples"></a>Предварительные требования для примеров

* Кластер Хадуп на HDInsight. Ознакомьтесь со статьей [Краткое руководство. Использование Apache Hadoop и Apache Hive в Azure HDInsight с шаблоном Resource Manager](./apache-hadoop-linux-tutorial-get-started.md).

* Обратите внимание на схему URI для первичного хранилища кластера. Например, `wasb://` для хранилища `abfs://` Azure, для хранения azure `adl://` Data Lake Data Gen2 или для хранилища озер Azure Gen1. Если для хранилища Azure включена безопасная передача, URI — это `wasbs://`. Для получения дополнительной информации [см.](../../storage/common/storage-require-secure-transfer.md)

* Вариант 1: Клиент SSH. Дополнительные сведения см. в руководстве по [подключению к HDInsight (Apache Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). Большинство шагов в этом документе предполагают, что вы используете Билайн от сеанса SSH к кластеру.

* Вариант 2: Местный клиент Билайн.

## <a name="run-a-hive-query"></a>Выполнение запроса Hive

Этот пример основан на использовании клиента Beeline из соединения SSH.

1. Откройте соединение SSH к кластеру с приведенным ниже кодом. Замените `sshuser` именем пользователя SSH для кластера, а `CLUSTERNAME` — именем кластера. При запросе введите пароль для учетной записи пользователя SSH.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Подключитесь к HiveServer2 с вашим клиентом Beeline из открытой сессии SSH, введя следующую команду:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. Команды Beeline начинаются со знака `!`. Например, `!help` выводит справку. Однако в некоторых командах `!` можно опустить. Например, `help` также работает.

    Там `!sql`в , который используется для выполнения заявлений Hive'L. Однако эти инструкции настолько распространены, что `!sql`тоже можно опустить. Приведенные ниже две инструкции эквивалентны.

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

5. Введите следующие инструкции для создания таблицы под названием **log4jLogs,** используя выборочные данные, предоставленные кластером HDInsight: (Пересмотреть по мере необходимости на основе вашей схемы URI.)

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

    Эти заявления делают следующие действия:

    |. |Описание |
    |---|---|
    |DROP TABLE|Если таблица существует, она удаляется.|
    |СОЗДАНИЕ ВНЕШНЕЙ ТАБЛИЦЫ|Создает **внешний** стол в Hive. Внешние таблицы хранят только определения таблицы в Hive. Данные остаются в исходном расположении.|
    |ФОРМАТ СТРОКИ|Как отформатированы данные. В данном случае поля всех журналов разделены пробелом.|
    |СОХРАНЯЕТСЯ КАК МЕСТОПОЛОЖЕНИЕ ТЕКСТФАЙЛА|Где хранятся данные и в каком формате файла.|
    |SELECT|Выбрано количество всех строк, где столбец **t4** содержит значение **«ERROR».** Этот запрос должен вернуть значение **3**, так как таблица содержит три строки с данным значением.|
    |INPUT__FILE__NAME like '%. log'|Hive пытается применить схему ко всем файлам в каталоге. В этом случае каталог содержит файлы, которые не соответствуют схеме. Чтобы исключить лишние данные в результатах, эта инструкция указывает Hive возвращать данные только из файлов, заканчивающихся на .log.|

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

6. Выход Билайн:

    ```bash
    !exit
    ```

## <a name="run-a-hiveql-file"></a>Запуск файла HiveQL.

Этот пример является продолжением предыдущего примера. Чтобы создать файл, а затем запустить его с помощью Beeline, выполните следующие действия.

1. Создайте файл **query.hql**, используя следующую команду.

    ```bash
    nano query.hql
    ```

1. В качестве содержимого файла добавьте следующий текст: Этот запрос создает "внутреннюю" таблицу **errorLogs**.

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Эти заявления делают следующие действия:

    |. |Описание |
    |---|---|
    |СОЗДАНИЕ ТАБЛИЦЫ, ЕСЛИ НЕ СУЩЕСТВУЕТ|Если таблица еще не существует, она создана. Поскольку ключевое слово **EXTERNAL** не используется, это заявление создает внутреннюю таблицу. Внутренние таблицы хранятся в хранилище данных Hive и полностью управляются Hive.|
    |STORED AS ORC|Позволяет сохранить данные в формате ORC. Это высокооптимизированный и эффективный формат для хранения данных Hive.|
    |ВСТАВИТЬ ПЕРЕЗАПИСЬ ... Выберите|выбирает строки из таблицы **log4jLogs**, которые содержат значение **[ERROR]**, а затем вставляет данные в таблицу **errorLogs**.|

    > [!NOTE]  
    > В отличие от внешних таблиц, удаление внутренней таблицы приводит к удалению базовых данных.

1. Чтобы сохранить файл, используйте **Ctrl**+**X,** затем введите **Y,** и, наконец, **введите**.

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

## <a name="install-beeline-client"></a>Установка "Билайн клиента"

Хотя Билайн включен в головные узлы, вы можете установить его локально.  Шаги установки для локальной машины основаны на [подсистеме Windows для Linux.](https://docs.microsoft.com/windows/wsl/install-win10)

1. Обновление списков пакетов. Введите следующую команду в оболочке Bash:

    ```bash
    sudo apt-get update
    ```

1. Установите Java, если не установлен. Вы можете проверить `which java` с командой.

    1. Если пакет java не установлен, введите следующую команду:

        ```bash
        sudo apt install openjdk-11-jre-headless
        ```

    1. Откройте файл bashrc (часто встречается в `nano ~/.bashrc`q/.bashrc): .

    1. Вгоняй файл bashrc. В конце файла добавьте следующую строку:

        ```bash
        export JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64
        ```

        Затем нажмите **Ctrl'X**, затем **Y**, а затем введите.

1. Скачать архивы Hadoop и Beeline, введите следующие команды:

    ```bash
    wget https://archive.apache.org/dist/hadoop/core/hadoop-2.7.3/hadoop-2.7.3.tar.gz
    wget https://archive.apache.org/dist/hive/hive-1.2.1/apache-hive-1.2.1-bin.tar.gz
    ```

1. Распакуйте архивы, введите следующие команды:

    ```bash
    tar -xvzf hadoop-2.7.3.tar.gz
    tar -xvzf apache-hive-1.2.1-bin.tar.gz
    ```

1. Дальнейшее изменение файла bashrc. Вам нужно будет определить путь, по которому были распакованы архивы. Если вы используете [подсистему Windows для Linux,](https://docs.microsoft.com/windows/wsl/install-win10)и `/mnt/c/Users/user/`вы `user` следовали шагам точно, ваш путь будет , где ваше имя пользователя.

    1. Откройте файл:`nano ~/.bashrc`

    1. Измените команды ниже с соответствующим путем, а затем введите их в конце файла bashrc:

        ```bash
        export HADOOP_HOME=/path_where_the_archives_were_unpacked/hadoop-2.7.3
        export HIVE_HOME=/path_where_the_archives_were_unpacked/apache-hive-1.2.1-bin
        PATH=$PATH:$HIVE_HOME/bin
        ```

    1. Затем нажмите **Ctrl'X**, затем **Y**, а затем введите.

1. Закройте, а затем вновь открыть вас Баш сессии.

1. Проверьте соединение. Используйте формат соединения из [более государственных или частных конечных точек,](#over-public-or-private-endpoints)выше.

## <a name="next-steps"></a>Дальнейшие действия

* Для получения более подробной информации о Hive в HDInsight, [см.](hdinsight-use-hive.md)

* Для получения дополнительной информации о других способах работы с Hadoop на HDInsight, [см.](hdinsight-use-mapreduce.md)
