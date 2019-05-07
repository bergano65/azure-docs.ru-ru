---
title: Интеграция Apache Spark и Apache Hive с помощью соединителя хранилища Hive
description: Узнайте, как интегрировать Apache Spark и Apache Hive с помощью соединителя хранилища Hive в Azure HDInsight.
ms.service: hdinsight
author: nakhanha
ms.author: nakhanha
ms.reviewer: hrasheed
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: b2b3d1ac0a7c0e917f87be1dd131120f63a70f8e
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65142815"
---
# <a name="integrate-apache-spark-and-apache-hive-with-the-hive-warehouse-connector"></a>Интеграция Apache Spark и Apache Hive с помощью соединителя хранилища Hive

Apache Hive хранилище соединителя (HWC) — это библиотека, которая позволяет упростить работу с Apache Spark и Apache Hive, поддерживая задач, таких как перемещение данных между таблицы данных Spark и таблиц Hive, а также направление Spark, потоковая передача данных в таблицы Hive. Hive соединитель хранилища действует как мост между Spark и Hive. Он поддерживает Scala, Java и Python для разработки приложений.

Соединитель хранилища Hive позволяет воспользоваться преимуществами уникальными возможностями этого Hive и Spark для создания эффективных приложений больших данных. Apache Hive предлагает поддержку для транзакций базы данных, которые являются Atomic, Consistent, Isolated и устойчивые (ACID). Дополнительные сведения о свойствах ACID и транзакции в Hive, см. в разделе [Hive транзакции](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions). Hive также предлагает подробные сведения о безопасности элементов управления с помощью Apache Ranger и низкой задержки аналитическая обработка недоступно в Apache Spark.

Apache Spark, имеет API структурированной потоковой передачи, предоставляет возможности потоковой передачи не доступен в Apache Hive. Начиная с Hortonworks Data Platform (HDP) 3.0, Apache Spark и Apache Hive имеют отдельные хранилища метаданных, что могло усложнять взаимодействия. Соединитель Hive хранилища упрощает совместное использование Spark и Hive. Библиотека HWC загружает данные из управляющих программ LLAP в исполнителей Spark в параллельном режиме, что делает более эффективным и масштабируемым, чем через стандартное подключение JDBC из Spark для Hive.

![Архитектура](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

Ниже приведены некоторые из операций, поддерживаемых соединителем хранилища Hive.

* Описание таблицы
* Создание таблицы для данных в формате ORC
* Выбор данных Hive и извлечения кадра данных
* Запись блока данных в Hive в пакете
* Выполнение инструкции update Hive
* Чтение таблицы данных из Hive, преобразуя его в Spark и записываются в новой таблице Hive
* Запись потока данных или Spark в Hive с помощью HiveStreaming

## <a name="hive-warehouse-connector-setup"></a>Установка соединителя хранилища Hive

Выполните следующие действия, чтобы настроить соединитель хранилища Hive между кластер Spark и Interactive Query в Azure HDInsight.

1. Создание кластера HDInsight Spark 4.0 с помощью портала Azure с помощью учетной записи хранения и настраиваемой виртуальной сети Azure. Сведения о создании кластера в виртуальной сети Azure, см. в разделе [Добавление HDInsight в существующую виртуальную сеть](../../hdinsight/hdinsight-extend-hadoop-virtual-network.md#existingvnet).
1. Создание кластера HDInsight Interactive Query (LLAP) 4.0 с помощью портала Azure с одной учетной записи хранения и виртуальной сети Azure, что и кластер Spark.
1. Скопируйте содержимое `/etc/hosts` файл на headnode0 кластера Interactive Query с целью `/etc/hosts` файл на узле headnode0 кластера Spark. Этот шаг позволит кластером Spark разрешить IP-адреса узлов в кластер интерактивных запросов. Просмотреть содержимое файла, обновленные с `cat /etc/hosts`. Результат должен выглядеть примерно так, что показано на следующем снимке экрана.

    ![Просмотр файла hosts](./media/apache-hive-warehouse-connector/hive-warehouse-connector-hosts-file.png)

1. Настройка параметров кластера Spark, выполнив следующие действия: 
    1. Перейдите на портал Azure, можно выбрать кластеры HDInsight и затем щелкните свое имя кластера.
    1. Справа от оператора в разделе **панели мониторинга кластера**выберите **Ambari домашней**.
    1. В веб-Интерфейсу Ambari, выберите **SPARK2** > **CONFIGS** > **Custom spark2-defaults**.

        ![Конфигурация Spark2 Ambari](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

    1. Задайте `spark.hadoop.hive.llap.daemon.service.hosts` то же значение, так как свойство **LLAP имя_приложения** под **Advanced hive — интерактивные env**. Например `@llap0`.

    1. Задайте `spark.sql.hive.hiveserver2.jdbc.url` строку подключения JDBC, который подключает его к Hiveserver2 в кластере Interactive Query. Строка подключения для кластера будет выглядеть как ниже URI. `CLUSTERNAME` Имя кластера Spark и `user` и `password` присвоено правильные значения для кластера.

        ```
        jdbc:hive2://LLAPCLUSTERNAME.azurehdinsight.net:443/;user=admin;password=PWD;ssl=true;transportMode=http;httpPath=/hive2
        ```

        >[!Note] 
        > URL-адреса JDBC должен содержать учетные данные для подключения к Hiveserver2 включая имя пользователя и пароль.

    1. Задайте `spark.datasource.hive.warehouse.load.staging.dir` подходящий промежуточный каталог HDFS-совместимой. При наличии двух разных кластерах промежуточного каталога должны быть папки из промежуточного каталога учетной записи хранения кластера LLAP чтобы HiveServer2 имеет к нему доступ. Например `wasb://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp` где `STORAGE_ACCOUNT_NAME` — имя учетной записи хранения используемые в кластере, и `STORAGE_CONTAINER_NAME` имя контейнера хранилища.

    1. Задайте `spark.datasource.hive.warehouse.metastoreUri` со значением URI кластера Interactive Query хранилища метаданных. Чтобы найти metastoreUri кластер LLAP, найдите **hive.metastore.uris** свойство в пользовательском Интерфейсе Ambari для вашего LLAP кластера в разделе **Hive** > **ДОПОЛНИТЕЛЬНО**  >  **Общие**. Значение будет выглядеть примерно так следующий URI:

        ```
        thrift://hn0-hwclla.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:9083,
        thrift://hn1-hwclla.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:9083
        ```

    1. Задайте `spark.security.credentials.hiveserver2.enabled` для `false` YARN клиента развертывание в режиме.
    1. Задайте `spark.hadoop.hive.zookeeper.quorum` кворума Zookeeper LLAP кластера. Чтобы найти кворума Zookeeper для кластера LLAP, найдите **hive.zookeeper.quorum** свойство в пользовательском Интерфейсе Ambari для вашего LLAP кластера в разделе **Hive** > **ДОПОЛНИТЕЛЬНО**  >  **Advanced hive-site**. Значение будет выглядеть примерно так следующую строку:

        ```
        zk1-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181,
        zk4-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181,
        zk6-nkhvne.0iv2nyrmse1uvp2caa4e34jkmf.cx.internal.cloudapp.net:2181
        ```

Чтобы проверить конфигурацию соединителя хранилища Hive, выполните действия, описанные в разделе [подключения и выполнения запросов](#connecting-and-running-queries).

## <a name="using-the-hive-warehouse-connector"></a>С помощью соединителя хранилища Hive

### <a name="connecting-and-running-queries"></a>Подключение и выполнение запросов

Можно выбрать несколько различных методов для подключения к кластеру Interactive Query и выполнять запросы, с помощью соединителя хранилища Hive. Поддерживаемые методы включают в себя следующие средства:

* [оболочки Spark](../spark/apache-spark-shell.md)
* PySpark
* Spark-submit
* [Zeppelin](../spark/apache-spark-zeppelin-notebook.md)
* [Livy](../spark/apache-spark-livy-rest-interface.md)

Все примеры, приведенные в этой статье будет выполняться с помощью оболочки spark.

Чтобы запустить сеанс spark-shell, выполните следующие действия:

1. Скопируйте ключ SSH в головной узел кластера. Дополнительные сведения о подключении к кластеру по протоколу SSH см. в разделе [подключение к HDInsight (Apache Hadoop) с помощью SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).
1. Измените в правильный каталог, введя `cd /usr/hdp/current/hive_warehouse_connector` или укажите полный путь к все JAR-файлы, используемые в качестве параметров в команде оболочки spark.
1. Введите следующую команду, чтобы запустить оболочку spark:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/3.0.1.0-183/hive_warehouse_connector/hive-warehouse-connector-assembly-1.0.0.3.0.1.0-183.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. Вы увидите приветственное сообщение и `scala>` строки, где можно ввести команды.

1. После запуска оболочки spark, используя следующие команды можно будет запустить экземпляр соединителя хранилища Hive:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="connecting-and-running-queries-on-enterprise-security-package-esp-clusters"></a>Подключение и выполнение запросов в кластерах пакета безопасности предприятия (ESP)

Пакет безопасности предприятия (ESP) предоставляет возможности корпоративного уровня, такие как проверка подлинности на основе Active Directory, поддержка нескольких пользователей и управление доступом на основе ролей для кластеров Apache Hadoop в Azure HDInsight. Дополнительные сведения о ESP, см. в разделе [Общие сведения о безопасности Apache Hadoop с пакетом безопасности предприятия](../domain-joined/apache-domain-joined-introduction.md).

1. Выполните начальные действия 1 и 2 в разделе [подключения и выполнения запросов](#connecting-and-running-queries).
1. Тип `kinit` и войдите, используя пользователя домена.
1. Запуск оболочки spark с полный список параметров конфигурации, как показано ниже. Все значения из прописных букв угловых скобках указывается исходя из кластера. Если вам нужно узнать значения для входных данных для одного из перечисленных ниже параметров, см. в разделе на [Установка соединителя хранилища Hive](#hive-warehouse-connector-setup).:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/3.0.1.0-183/hive_warehouse_connector/hive-warehouse-connector-assembly-1.0.0.3.0.1.0-183.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    --conf spark.hadoop.hive.llap.daemon.service.hosts='<LLAP_APP_NAME>'
    --conf spark.sql.hive.hiveserver2.jdbc.url='jdbc:hive2://<ZOOKEEPER_QUORUM>;serviceDiscoveryMode=zookeeper;zookeeperNamespace=hiveserver2-interactive'
    --conf spark.datasource.hive.warehouse.load.staging.dir='<STAGING_DIR>'
    --conf spark.datasource.hive.warehouse.metastoreUri='<METASTORE_URI>'
    --conf spark.hadoop.hive.zookeeper.quorum='<ZOOKEEPER_QUORUM>'
   ```

### <a name="creating-spark-dataframes-from-hive-queries"></a>Создание таблицы данных Spark из запросов Hive

Результаты всех запросов, с помощью библиотеки HWC возвращаются в виде блока данных. Следующие примеры демонстрируют создание базового запроса.

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

Результаты запроса, таблицы данных Spark, который может использоваться с библиотеками Spark как MLIB и SparkSQL.

### <a name="writing-out-spark-dataframes-to-hive-tables"></a>Записи таблицы данных Spark в таблицы Hive

Spark не поддерживает запись управляемого ACID таблиц Hive. С помощью HWC, тем не менее, можно написать любой таблицы данных в таблице Hive. Вы увидите эту функцию в действии в следующем примере:

1. Создайте таблицу с именем `sampletable_colorado` и укажите ее столбцов, используя следующую команду:

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

2. Отфильтруйте таблицу `hivesampletable` где столбец `state` равно `Colorado`. Этот запрос таблицы Hive возвращаются в виде кадра данных Spark. То таблицы данных сохраняются в таблице Hive `sampletable_colorado` с помощью `write` функции.
    
    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format(HiveWarehouseSession.HIVE_WAREHOUSE_CONNECTOR).option("table","sampletable_colorado").save()
    ```

Вы увидите результирующая таблица на следующем снимке экрана.

![Показать результирующая таблица](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)

### <a name="structured-streaming-writes"></a>Структурированная потоковая передача записи

С помощью Hive в хранилище фабрики данных, можно использовать Spark streaming для записи данных в таблицы Hive.

Выполните следующие действия, чтобы создать пример Hive соединитель хранилища, в котором принимает данные из потоковая передача Spark через порт localhost 9999 в таблицу Hive.

1. Откройте окно терминала в кластере Spark.
1. Сначала потоковая передача spark с помощью следующей команды:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9988).load()
    ```

1. Создание данных для потока Spark, который вы создали, выполнив следующие действия:
    1. Открытие другого терминала в одном кластере Spark.
    1. В командной строке выполните следующую команду: `nc -lk 9999`. Эта команда использует программу netcat для отправки данных из командной строки для указанного порта.
    1. Введите слова, которые вы хотите потоковая передача Spark для приема, следуют символы возврата каретки.
        ![входные данные в потоковая передача spark](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark-stream-data-input.png)
1. Создайте таблицу Hive для потоковой передачи данных. В spark-shell введите следующие команды:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. Записи потоковых данных вновь созданную таблицу, используя следующую команду:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format(HiveWarehouseSession.STREAM_TO_STREAM).option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > `metastoreUri` И `database` параметры должны быть установлены связи с известной проблемой в Apache Spark в настоящее время вручную. Дополнительные сведения об этой проблеме см. в разделе [SPARK 25460](https://issues.apache.org/jira/browse/SPARK-25460).

1. Вы можете просмотреть данные, добавленные в таблицу с помощью следующей команды:

    ```scala
    hive.table("stream_table").show()
    ```

### <a name="securing-data-on-spark-esp-clusters"></a>Защита данных в кластерах Spark ESP

1. Создайте таблицу `demo` с демонстрационными данными, введя следующие команды:

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. Просмотр содержимого таблицы с помощью следующей команды. Перед применением политики `demo` таблицы показывает полный столбец.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![демонстрационную таблицу перед применением политик ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. Примените столбец маскирования политику, которая отображает только последние четыре символа столбца.  
    1. Перейдите к пользовательскому Интерфейсу администратора Ranger в `https://CLUSTERNAME.azurehdinsight.net/ranger/`.
    1. Выберите службу Hive для кластера в разделе **Hive**.
        ![демонстрационную таблицу перед применением политик ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. Щелкните **маскирования** tab и затем **добавить новую политику** ![список политик](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)
    1. Укажите имя требуемой политике. Выберите базу данных: **По умолчанию**, таблицу Hive: **Демонстрация**, столбец Hive: **имя**, пользователь: **rsadmin2**, типа доступа: **выберите**и **Частичного маски: Показать последние 4** из **Выбор параметра маскирования** меню. Щелкните **Добавить**.
                ![Список политик](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. Снова просмотрите содержимое таблицы. После применения политики ranger, мы видим только последние четыре символа столбца.

    ![Демонстрация таблицы после применения политики ranger](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>Дальнейшие действия

* [Использование Interactive Query в HDInsight](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-interactive-query-get-started)
* [Примеры взаимодействия с соединителем хранилища Hive с помощью Zeppelin, Livy spark-submit и pyspark](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
