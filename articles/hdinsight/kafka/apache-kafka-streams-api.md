---
title: 'Руководство по Использование API Потоков Apache Kafka в Azure HDInsight '
description: 'Учебник: узнайте, как использовать API потоков Apache Kafka в HDInsight. Этот API позволяет выполнять потоковую обработку между разделами в Kafka.'
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/25/2019
ms.openlocfilehash: 0639ecaa0e4ae0581a6c88e1ea9a47de870a8355
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446393"
---
# <a name="tutorial-use-apache-kafka-streams-api-in-azure-hdinsight"></a>Руководство по использованию API потоков Apache Kafka в Azure HDInsight

Узнайте, как создать приложение, использующее API для Apache Kafka Streams, и запустить его с помощью Kafka в HDInsight.

В этом руководстве используется приложение для подсчета слов во время потоковой передачи. Оно считывает текстовые данные из раздела Kafka, извлекает отдельные слова, а затем сохраняет слово и количество слов в другом разделе Kafka.

Потоковая обработка Kafka часто выполняется с помощью Apache Spark или Apache Storm. В Kafka версии 1.1.0 (в HDInsight 3.5 и 3.6) появился API Потоков Kafka. Этот API позволяет преобразовать потоки данных между входными и выходными разделами. В некоторых случаях это может быть альтернативой созданию решения потоковой передачи Spark или Storm.

Дополнительные сведения о Потоках Kafka см. в [вводной документации ](https://kafka.apache.org/10/documentation/streams/) на сайте Apache.org.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Изучение кода
> * Создание и развертывание приложения.
> * Настройка разделов Kafka.
> * Выполнение кода

## <a name="prerequisites"></a>Предварительные требования

* Kafka в кластере HDInsight 3.6; Чтобы узнать, как создать кластер Kafka в HDInsight, ознакомьтесь с документом [начале работы с Apache Kafka в HDInsight](apache-kafka-get-started.md).

* Выполните шаги, приведенные в статье [Руководство. Использование API производителя и потребителя Apache Kafka](apache-kafka-producer-consumer-api.md). В шагах, описанных в этом документе, используется пример приложения и разделы, созданные в этом руководстве.

* Пакет [Java Developer Kit (JDK) версии 8](https://aka.ms/azure-jdks) или аналогичный пакет, например OpenJDK.

* Средство [Apache Maven](https://maven.apache.org/download.cgi), [установленное](https://maven.apache.org/install.html) согласно инструкций Apache.  Maven — система сборки проектов Java.

* Клиент SSH. Дополнительные сведения см. в руководстве по [подключению к HDInsight (Apache Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="understand-the-code"></a>Изучение кода

Пример приложения расположен в подкаталоге `Streaming` по адресу [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started). Приложение состоит из двух файлов:

* `pom.xml`: этот файл определяет зависимости проекта, версию Java и методы упаковки.
* `Stream.java`: этот файл реализует логику потоковой передачи.

### <a name="pomxml"></a>Pom.xml

В файле `pom.xml` важны следующие элементы:

* Зависимости. Этот проект использует API Потоков Kafka, предоставленный в пакете `kafka-clients`. Приведенный ниже код XML определяет эту зависимость:

    ```xml
    <!-- Kafka client for producer/consumer operations -->
    <dependency>
      <groupId>org.apache.kafka</groupId>
      <artifactId>kafka-clients</artifactId>
      <version>${kafka.version}</version>
    </dependency>
    ```

    Запись `${kafka.version}` объявлена в разделе `<properties>..</properties>` файла `pom.xml`. Она настроена для версии Kafka кластера HDInsight.

* Подключаемые модули. Подключаемые модули Maven предоставляют различные возможности. В этом проекте используются следующие подключаемые модули:

    * `maven-compiler-plugin`: с помощью этого модуля можно задать для проекта Java версии 8. Для HDInsight 3.6 требуется Java версии 8.
    * `maven-shade-plugin`: используется для создания файла типа uber jar, содержащего это приложение, а также любые зависимости. Он также используется для установки точки входа приложения, с помощью которой вы сможете напрямую запускать JAR-файл, не указывая основной класс.

### <a name="streamjava"></a>Stream.java

Файл [Stream.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Streaming/src/main/java/com/microsoft/example/Stream.java) использует API потоков для реализации приложения для подсчета слов. Оно считывает данные из раздела Kafka с именем `test` и записывает количество слов в раздел с именем `wordcounts`.

Следующий код определяет приложение для подсчета слов:

```java
package com.microsoft.example;

import org.apache.kafka.common.serialization.Serde;
import org.apache.kafka.common.serialization.Serdes;
import org.apache.kafka.streams.KafkaStreams;
import org.apache.kafka.streams.KeyValue;
import org.apache.kafka.streams.StreamsConfig;
import org.apache.kafka.streams.kstream.KStream;
import org.apache.kafka.streams.kstream.KStreamBuilder;

import java.util.Arrays;
import java.util.Properties;

public class Stream
{
    public static void main( String[] args ) {
        Properties streamsConfig = new Properties();
        // The name must be unique on the Kafka cluster
        streamsConfig.put(StreamsConfig.APPLICATION_ID_CONFIG, "wordcount-example");
        // Brokers
        streamsConfig.put(StreamsConfig.BOOTSTRAP_SERVERS_CONFIG, args[0]);
        // SerDes for key and values
        streamsConfig.put(StreamsConfig.KEY_SERDE_CLASS_CONFIG, Serdes.String().getClass().getName());
        streamsConfig.put(StreamsConfig.VALUE_SERDE_CLASS_CONFIG, Serdes.String().getClass().getName());

        // Serdes for the word and count
        Serde<String> stringSerde = Serdes.String();
        Serde<Long> longSerde = Serdes.Long();

        KStreamBuilder builder = new KStreamBuilder();
        KStream<String, String> sentences = builder.stream(stringSerde, stringSerde, "test");
        KStream<String, Long> wordCounts = sentences
                .flatMapValues(value -> Arrays.asList(value.toLowerCase().split("\\W+")))
                .map((key, word) -> new KeyValue<>(word, word))
                .countByKey("Counts")
                .toStream();
        wordCounts.to(stringSerde, longSerde, "wordcounts");

        KafkaStreams streams = new KafkaStreams(builder, streamsConfig);
        streams.start();

        Runtime.getRuntime().addShutdownHook(new Thread(streams::close));
    }
}
```

## <a name="build-and-deploy-the-example"></a>Создание и развертывание примера

Чтобы создать и развернуть проект для Kafka в кластере HDInsight, выполните следующие действия:

1. Укажите для текущего каталога расположение каталога `hdinsight-kafka-java-get-started-master\Streaming` и выполните следующую команду, чтобы создать пакет JAR:

    ```cmd
    mvn clean package
    ```

    Эта команда создает пакет в файле `target/kafka-streaming-1.0-SNAPSHOT.jar`.

2. Замените `sshuser` именем пользователя SSH для кластера, а `clustername` — именем кластера. Используя следующую команду, скопируйте файл `kafka-streaming-1.0-SNAPSHOT.jar` в свой кластер HDInsight. При появлении запроса введите пароль для учетной записи пользователя SSH.

    ```cmd
    scp ./target/kafka-streaming-1.0-SNAPSHOT.jar sshuser@clustername-ssh.azurehdinsight.net:kafka-streaming.jar
    ```

## <a name="create-apache-kafka-topics"></a>Создание разделов Apache Kafka

1. Замените `sshuser` именем пользователя SSH для кластера, а `CLUSTERNAME` — именем кластера. Откройте SSH-подключение к кластеру, выполнив следующую команду. При появлении запроса введите пароль для учетной записи пользователя SSH.

    ```bash
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Установите [jq](https://stedolan.github.io/jq/) — обработчик командной строки JSON. В открытом сеансе SSH-подключения введите следующую команду для установки `jq`:

    ```bash
    sudo apt -y install jq
    ```

3. Настройте переменные среды. Замените `PASSWORD` и `CLUSTERNAME` на пароль для входа в кластер и имя кластера соответственно, затем введите команду:

    ```bash
    export password='PASSWORD'
    export clusterNameA='CLUSTERNAME'
    ```

4. Извлеките имя кластера с правильным регистром. Фактический регистр имени кластера может отличаться от ожидаемого, в зависимости от способа создания кластера. Эта команда получит фактический регистр, сохранит его в переменной, а затем отобразит имя с правильным регистром и имя, которое вы указали ранее. Введите следующую команду:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "https://$clusterNameA.azurehdinsight.net/api/v1/clusters" \
  	| jq -r '.items[].Clusters.cluster_name')
    echo $clusterName, $clusterNameA
    ```

5. Чтобы получить узлы Apache Zookeeper и брокера Kafka, используйте приведенные ниже команды. При появлении запроса введите пароль для учетной записи администратора, чтобы войти на кластер. Запрос на ввод пароля появится дважды.

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin:$password -G \
    https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER \
  	| jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`;
    export KAFKABROKERS=`curl -sS -u admin:$password -G \
    https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER \
  	| jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`;
    ```

6. Чтобы создать разделы для операции потоковой передачи, используйте следующие команды:

    > [!NOTE]  
    > Вы можете получить сообщение-ошибку о том, что раздел `test` уже существует. Это нормально, так как он, возможно, был создан в руководстве по API производителя и потребителя.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic RekeyedIntermediateTopic --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcount-example-Counts-changelog --zookeeper $KAFKAZKHOSTS
    ```

    Разделы используются для следующих целей:

   * `test`: в этот раздел поступают записи. Здесь приложение потоковой передачи считывает их.
   * `wordcounts`: в этом разделе приложение потоковой передачи хранит свои выходные данные.
   * `RekeyedIntermediateTopic`: этот раздел используется для секционирования данных, так как счетчик обновляется оператором `countByKey`.
   * `wordcount-example-Counts-changelog`: этот раздел является хранилищем состояний, используемым операцией `countByKey`.

    Кроме того, Kafka в HDInsight можно настроить на автоматическое создание разделов. Дополнительные сведения см. в статье [How to configure Apache Kafka on HDInsight to automatically create topics](apache-kafka-auto-create-topics.md) (Настройка автоматического создания разделов в Apache Kafka в HDInsight).

## <a name="run-the-code"></a>Выполнение кода

1. Для запуска приложения потоковой передачи в качестве фонового процесса используйте следующую команду:

    ```bash
    java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS &
    ```

    Может появиться предупреждение об Apache log4j. На это можно не обращать внимания.

2. Чтобы отправить записи в раздел `test`, используйте следующую команду для запуска приложения-отправителя:

    ```bash
    java -jar kafka-producer-consumer.jar producer test $KAFKABROKERS
    ```

3. После завершения работы отправителя просмотрите сведения, хранящиеся в разделе `wordcounts`, с помощью следующей команды:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer --from-beginning
    ```

    В соответствии с параметрами `--property` объект-получатель консоли печатает ключ (машинное слово) и число (значение). Кроме того, этот параметр настраивает десериализатор, используемый при считывании этих значений из Kafka.

    Результат будет аналогичен приведенному ниже:
   
        dwarfs  13635
        ago     13664
        snow    13636
        dwarfs  13636
        ago     13665
        a       13803
        ago     13666
        a       13804
        ago     13667
        ago     13668
        jumped  13640
        jumped  13641
   
    Параметр `--from-beginning` настраивает запуск объекта-получателя в начале записей, хранящихся в разделе. Число увеличивается каждый раз, когда встречается слово, поэтому раздел содержит несколько записей для каждого слова с увеличивающимся числом.

4. Нажмите клавиши __Ctrl+C__, чтобы закрыть отправитель. Снова нажмите клавиши __Ctrl+C__, чтобы выйти из приложения и объекта-получателя.

5. Чтобы удалить разделы для операции потоковой передачи, используйте следующие команды:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic test --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic wordcounts --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic RekeyedIntermediateTopic --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic wordcount-example-Counts-changelog --zookeeper $KAFKAZKHOSTS
    ```

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы очистить ресурсы, созданные при работе с этим руководством, удалите группу ресурсов. При этом будет удален связанный кластер HDInsight и другие ресурсы, связанные с этой группой ресурсов.

Чтобы удалить группу ресурсов с помощью портала Azure, сделайте следующее:

1. На портале Azure разверните меню слева, чтобы открыть меню служб, а затем выберите __Группы ресурсов__, чтобы просмотреть список групп ресурсов.
2. Найдите группу ресурсов, которую нужно удалить, и щелкните правой кнопкой мыши кнопку __Дополнительно__ (…) справа от списка.
3. Выберите __Удалить группу ресурсов__ и подтвердите выбор.

## <a name="next-steps"></a>Дополнительная информация

Из этого документа вы узнали, как использовать API для Apache Kafka Streams с Kafka в HDInsight. Дополнительные сведения о работе с Kafka см. в следующих материалах.

> [!div class="nextstepaction"]
> [Анализ журналов для Apache Kafka в HDInsight](apache-kafka-log-analytics-operations-management.md)