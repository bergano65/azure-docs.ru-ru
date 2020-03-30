---
title: Apache Spark & Hive - Улей Склад Разъем - Azure HDInsight
description: Узнайте, как интегрировать Apache Spark и Apache Hive с разъемом склада Hive в Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/02/2020
ms.openlocfilehash: f386530ffb3a074a5c1db1d9f28535d28c8b1284
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252404"
---
# <a name="integrate-apache-spark-and-apache-hive-with-the-hive-warehouse-connector"></a>Integrate Apache Spark and Apache Hive with the Hive Warehouse Connector (Интеграция Apache Spark и Apache Hive с помощью соединителя хранилища Hive)

Apache Hive Warehouse Connector (HWC) — это библиотека, которая позволяет вам более легко работать с Apache Spark и Apache Hive, поддерживая такие задачи, как перемещение данных между таблицами Spark DataFrames и Hive, а также направление потоковых данных Spark в таблицы Hive. Hive Склад Коннектор работает как мост между Spark и Hive. Он поддерживает Scala, Java и Python для разработки.

Соединение склада Hive позволяет воспользоваться уникальными особенностями Hive и Spark для создания мощных приложений для больших данных. Apache Hive предлагает поддержку транзакций баз данных, которые являются атомными, последовательными, изолированными и долговечными (ACID). Для получения дополнительной информации о ACID и транзакциях в Hive [см.](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions) Hive также предлагает подробные средства контроля безопасности через Apache Ranger и аналитическую обработку с низкой задержкой, недоступную в Apache Spark.

Apache Spark имеет структурированный streaming API, который предоставляет возможности потоковой передачи, недоступные в Apache Hive. Начиная с HDInsight 4.0, Apache Spark 2.3.1 и Apache Hive 3.1.0 имеют отдельные метамагазины, что может затруднить совместимость. Соединение склада Hive упрощает совместное использование Spark и Hive. Библиотека HWC загружает данные с daemons LLAP на исполнителей Spark параллельно, что делает их более эффективными и масштабируемыми, чем использование стандартного соединения JDBC от Spark до Hive.

![архитектура разъема склада улья](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

Некоторые из операций, поддерживаемых Hive Склад Разъем являются:

* Описание таблицы
* Создание таблицы для данных, форматированных ORC
* Выбор данных Hive и получение данных DataFrame
* Написание DataFrame в Hive в партии
* Выполнение оператора обновления Hive
* Чтение данных таблицы с Hive, преобразование их в Spark и написание их в новую таблицу Hive
* Написание потока DataFrame или Spark в Hive с помощью HiveStreaming

## <a name="hive-warehouse-connector-setup"></a>Установка соединителя склада Улье

Выполните следующие действия, чтобы настроить соединение склада Hive между кластером Spark и Interactive Query в Azure HDInsight:

### <a name="create-clusters"></a>Создание кластеров

1. Создайте кластер HDInsight Spark **4.0** с учетной записью хранения данных и пользовательской виртуальной сетью Azure. Информацию о создании кластера в виртуальной сети Azure можно узнать в [виртуальной сети](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet)Azure, см.

1. Создайте кластер HDInsight Interactive Query (LLAP) **4.0** с той же учетной записью хранения и виртуальной сетью Azure, что и кластер Spark.

### <a name="modify-hosts-file"></a>Изменение файла хостов

Копируйте информацию о `/etc/hosts` узлах из файла на headnode0 кластера `/etc/hosts` интерактивных запросов и перепрыгивайте информацию в файл на headnode0 вашего кластера Spark. Этот шаг позволит кластеру Spark разрешать IP-адреса узлов в кластере интерактивных запросов. Просмотр содержимого обновленного `cat /etc/hosts`файла с помощью . Окончательный выход должен выглядеть примерно так, как показано на скриншоте ниже.

![файл узлов склада улья](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

### <a name="gather-preliminary-information"></a>Сбор предварительной информации

#### <a name="from-your-interactive-query-cluster"></a>Из кластера интерактивных запросов

1. Перейдите на страницу Apache Ambari `https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/configs` Hive, используя, где `LLAPCLUSTERNAME` находится название кластера интерактивных запросов.

1. Перейдите на **Advanced** > **General** > **hive.metastore.uris** и обратите внимание на значение. Значение может быть похоже `thrift://iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083,thrift://hn1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083`на: .

1. Перейдите на **Расширенный** > **Расширенный улей-сайт** > **hive.zookeeper.quorum** и обратите внимание на значение. Значение может быть похоже `zk0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181`на: .

#### <a name="from-your-apache-spark-cluster"></a>Из кластера Apache Spark

1. Перейдите на страницу Apache Ambari `https://SPARKCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/configs` Hive, используя, где `SPARKCLUSTERNAME` находится название вашего кластера Apache Spark.

1. Перейдите на **Расширенный** > **Расширенный улья-интерактивный-сайт** > **hive.llap.daemon.service.hosts** и обратите внимание на значение. Значение может быть похоже `@llap0`на: .

### <a name="configure-spark-cluster-settings"></a>Настройка параметров кластера Spark

С вашего веб-пользовательского разъема Spark Ambari перейдите на **Spark2** > **CONFIGS** > **Custom spark2-defaults.**

![Конфигурация Apache Ambari Spark2](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

Выберите **Добавить свойство ...,** как это необходимо, чтобы добавить / обновить следующее:

| Ключ | Значение |
|----|----|
|`spark.hadoop.hive.llap.daemon.service.hosts`|Значение, полученное ранее от **hive.llap.daemon.service.hosts**.|
|`spark.sql.hive.hiveserver2.jdbc.url`|`jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2`. Установите строку подключения JDBC, которая подключается к Hiveserver2 в кластере интерактивных запросов. REPLACE `LLAPCLUSTERNAME` с названием кластера интерактивных запросов. Замените `PWD` фактический пароль.|
|`spark.datasource.hive.warehouse.load.staging.dir`|`wasbs://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp`. Установите в подходящий HDFS-совместимый постановочный каталог. Если у вас есть два разных кластера, то каталог постановки должен быть папкой в постановочном каталоге учетной записи хранения кластера LLAP, чтобы HiveServer2 получил к нему доступ.  Заменить `STORAGE_ACCOUNT_NAME` имя учетной записи хранилища, используемой кластером, и `STORAGE_CONTAINER_NAME` названием контейнера для хранения.|
|`spark.datasource.hive.warehouse.metastoreUri`|Значение, полученное ранее от **hive.metastore.uris**.|
|`spark.security.credentials.hiveserver2.enabled`|`false`для режима развертывания клиента YARN.|
|`spark.hadoop.hive.zookeeper.quorum`|Значение, полученное ранее от **hive.zookeeper.quorum**.|

Сохраняйте изменения и перезапускают компоненты по мере необходимости.

## <a name="using-the-hive-warehouse-connector"></a>Использование разъема склада Hive

### <a name="connecting-and-running-queries"></a>Подключение и запуск запросов

Вы можете выбрать один из нескольких различных методов для подключения к кластеру интерактивных запросов и выполнения запросов с помощью разъема хранилища Hive. Поддерживаемые методы включают в себя следующие инструменты:

* [искра-оболочка](../spark/apache-spark-shell.md)
* PySpark
* искра-представить
* [Zeppelin](../spark/apache-spark-zeppelin-notebook.md)
* [Livy](../spark/apache-spark-livy-rest-interface.md)

Все примеры, приведенные в этой статье, будут выполнены с помощью искры-оболочки.

Чтобы начать сеанс оболочки искры, сделайте следующие шаги:

1. SSH в головной нод для вашего кластера Apache Spark. Для получения дополнительной информации о подключении к кластеру с SSH, [см. Connect to HDInsight (Apache Hadoop) с помощью SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Введите следующую команду, чтобы запустить оболочку искры:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

    Вы увидите приветственное сообщение и подсказку, `scala>` где вы можете ввести команды.

1. После запуска оболочки искры экземпляр «Разъем склада Улей» может быть запущен с помощью следующих команд:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>Подключение и запуск запросов в кластерах пакета корпоративной безопасности (ESP)

Пакет корпоративной безопасности (ESP) предоставляет возможности корпоративного уровня, такие как активная аутентификация на основе каталога, поддержка нескольких пользователей и элементанный контроль доступа для кластеров Apache Hadoop в Azure HDInsight. Для получения дополнительной информации о ESP, [см.](../domain-joined/apache-domain-joined-architecture.md)

1. SSH в головной нод для вашего кластера Apache Spark. Для получения дополнительной информации о подключении к кластеру с SSH, [см. Connect to HDInsight (Apache Hadoop) с помощью SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Введите `kinit` и войти в систему с пользователем домена.

1. Начните искру-оболочку с полным списком параметров конфигурации, как показано ниже. Все значения во всех заглавных буквах между угловыми скобками должны быть указаны на основе кластера. Если вам нужно узнать значения для ввода для любого из параметров ниже, см. [Hive Warehouse Connector setup](#hive-warehouse-connector-setup)

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    --conf spark.hadoop.hive.llap.daemon.service.hosts='<LLAP_APP_NAME>'
    --conf spark.sql.hive.hiveserver2.jdbc.url='jdbc:hive2://<ZOOKEEPER_QUORUM>;serviceDiscoveryMode=zookeeper;zookeeperNamespace=hiveserver2-interactive'
    --conf spark.datasource.hive.warehouse.load.staging.dir='<STAGING_DIR>'
    --conf spark.datasource.hive.warehouse.metastoreUri='<METASTORE_URI>'
    --conf spark.hadoop.hive.zookeeper.quorum='<ZOOKEEPER_QUORUM>'
   ```

### <a name="creating-spark-dataframes-from-hive-queries"></a>Создание Spark DataFrames из запросов Hive

Результаты всех запросов с использованием библиотеки HWC возвращаются в виде DataFrame. Следующие примеры демонстрируют, как создать базовый запрос.

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

Результатом запроса являются Spark DataFrames, которые могут быть использованы с библиотеками Spark, такими как MLIB и SparkS'L.

### <a name="writing-out-spark-dataframes-to-hive-tables"></a>Написание Spark DataFrames на таблицы Hive

Spark не поддерживает письмо к управляемым таблицам Hive ACID. Однако, используя HWC, вы можете выписать любой DataFrame на таблицу Hive. Эту функциональность можно увидеть в следующем примере:

1. Создайте таблицу, называемую `sampletable_colorado` и укажите ее столбцы, используя следующую команду:

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

1. Фильтр стол, `hivesampletable` где `state` столбец равен `Colorado`. Этот запрос таблицы Hive возвращается как Spark DataFrame. Затем DataFrame сохраняется в таблице `sampletable_colorado` Hive с помощью функции. `write`

    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

1. Просмотр результатов со следующей командой:

    ```scala
    hive.table("sampletable_colorado").show()
    ```

    ![улей склад разъем показать улей таблице](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>Структурированные записи потоковой передачи

Используя разъем складского склада Hive, вы можете использовать потоковую передачу Spark для записи данных в таблицы Hive.

Выполните ниже шаги, чтобы создать пример подключения склада Hive, который попадает в таблицу Hive из потока Spark на локальном порту 9999.

1. Следуйте шагам под [запросами подключения и выполнения.](#connecting-and-running-queries)

1. Начните поток искры со следующей команды:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9999).load()
    ```

1. Создавайте данные для созданного потока Spark, выполняя следующие действия:
    1. Откройте вторую сессию SSH в том же кластере Spark.
    1. В командной строке введите `nc -lk 9999`. Эта команда использует утилиту netcat для отправки данных из командной строки в указанный порт.

1. Вернитесь к первому сеансу SSH и создайте новую таблицу Hive для хранения потоковых данных. На искре-оболочке введите следующую команду:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. Затем напишите потоковые данные в вновь созданную таблицу, используя следующую команду:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > Параметры `metastoreUri` `database` и параметры в настоящее время должны быть установлены вручную из-за известной проблемы в Apache Spark. Более подробную информацию об этой проблеме можно оставить на данный вопрос, [см.](https://issues.apache.org/jira/browse/SPARK-25460)

1. Вернитесь ко второму сеансу SSH и введите следующие значения:

    ```bash
    foo
    HiveSpark
    bar
    ```

1. Вернитесь к первому сеансу SSH и обратите внимание на краткое действие. Используйте следующую команду для просмотра данных:

    ```scala
    hive.table("stream_table").show()
    ```

Используйте **Ctrl и C,** чтобы остановить netcat на второй сессии SSH. Используйте `:q` для выхода из искры-оболочки на первом сеансе SSH.

### <a name="securing-data-on-spark-esp-clusters"></a>Защита данных о кластерах Spark ESP

1. Создайте `demo` таблицу с некоторыми выборочными данными, введя следующие команды:

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. Просмотр содержимого таблицы со следующей командой. Перед применением политики `demo` в таблице отображается полный столбец.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![демо-таблица перед применением политики рейнджера](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. Примените политику маскировки столбца, которая отображает только последние четыре символа столбца.  
    1. Перейти к Ranger Admin `https://CLUSTERNAME.azurehdinsight.net/ranger/`UI на .
    1. Нажмите на услугу Hive для вашего кластера под **Hive.**
        ![менеджер по обслуживанию рейнджеров](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. Нажмите на вкладку **Маскировка,** а затем **добавьте новую политику**

        ![улей склад разъем рейнджер алей политики список](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)

    а. Укажите желаемое имя политики. Выберите базу данных: **По умолчанию,** таблица Hive: **демо,** колонка Hive: **имя**, Пользователь: **rsadmin2,** Типы доступа: **выберите**и **Частичная маска: показать последние 4** из меню Select **Masking Option.** Нажмите кнопку **Добавить**.
                ![создать политику](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. Просмотр содержимого таблицы еще раз. После применения политики рейнджера, мы можем увидеть только последние четыре символа колонки.

    ![демо-таблица после применения политики рейнджера](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>Дальнейшие действия

* [Использование Interactive Query в HDInsight](./apache-interactive-query-get-started.md)
* [Примеры взаимодействия с Hive Склад Разъем с использованием Цеппелин, Livy, искра представить, и pyspark](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
