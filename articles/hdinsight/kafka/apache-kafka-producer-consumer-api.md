---
title: 'Руководство. Использование API производителя и потребителя Apache Kafka — Azure HDInsight '
description: Узнайте, как использовать API производителя и потребителя Apache Kafka для Kafka в HDInsight. В этом руководстве вы узнаете, как использовать эти API с Kafka в HDInsight из приложения Java.
services: hdinsight
author: dhgoelmsft
ms.author: dhgoel
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 11/06/2018
ms.openlocfilehash: dd4c077e23170a295a29a75df08cf8f29f8ba3e4
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53413359"
---
# <a name="tutorial-use-the-apache-kafka-producer-and-consumer-apis"></a>Руководство. Использование API производителя и потребителя Apache Kafka

Узнайте, как использовать API производителя и потребителя Apache Kafka для Kafka в HDInsight.

API производителя Kafka позволяет приложениям отправлять потоки данных в кластер Kafka. API потребителя Kafka позволяет приложениям считывать потоки данных из кластера.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Настройка среды разработки
> * Настройка окружения развертывания
> * Изучение кода
> * Создание и развертывание приложения.
> * Запуск приложения в кластере

Дополнительные сведения об интерфейсах API см. в документации [по API производителя](https://kafka.apache.org/documentation/#producerapi) и [API потребителя](https://kafka.apache.org/documentation/#consumerapi).

## <a name="set-up-your-development-environment"></a>Настройка среды разработки

В среде разработки необходимо установить следующие компоненты:

* [Java JDK 8](https://aka.ms/azure-jdks) или эквивалент, например OpenJDK.

* [Apache Maven](http://maven.apache.org/)

* Клиент SSH и команда `scp`. Дополнительные сведения см. в статье [Подключение к HDInsight (Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Текстовый редактор или Java IDE.

Во время установки Java и JDK на компьютере, где ведется разработка, могут быть установлены следующие переменные среды. Однако следует убедиться, что они существуют и что они содержат правильные значения для вашей системы.

* `JAVA_HOME`. Эта переменная должна указывать на каталог, в который установлен JDK.
* `PATH`. Эта переменная должна содержать следующие пути:

    * `JAVA_HOME` или эквивалентный путь.
    * `JAVA_HOME\bin` или эквивалентный путь.
    * Каталог, в который установлено ПО Maven.

## <a name="set-up-your-deployment-environment"></a>Настройка окружения развертывания

Для работы с этим руководством требуется Apache Kafka в HDInsight версии 3.6. Чтобы узнать, как создать кластер Kafka в HDInsight, ознакомьтесь с документом [начале работы с Apache Kafka в HDInsight](apache-kafka-get-started.md).

## <a name="understand-the-code"></a>Изучение кода

Пример приложения расположен в подкаталоге `Producer-Consumer` по адресу [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started). Приложение состоит в основном из четырех файлов:

* `pom.xml`: этот файл определяет зависимости проекта, версию Java и методы упаковки.
* `Producer.java`: этот файл отправляет случайные предложения в Kafka, используя API производителя.
* `Consumer.java`: этот файл использует API потребителя для чтения данных из Kafka и передачи их в STDOUT.
* `Run.java`: интерфейс командной строки используется для выполнения кода потребителя и производителя.

### <a name="pomxml"></a>Pom.xml

В файле `pom.xml` важны следующие элементы:

* Зависимости. Этот проект использует API производителя и потребителя Kafka, предоставленные в пакете `kafka-clients`. Приведенный ниже код XML определяет эту зависимость:

    ```xml
    <!-- Kafka client for producer/consumer operations -->
    <dependency>
      <groupId>org.apache.kafka</groupId>
      <artifactId>kafka-clients</artifactId>
      <version>${kafka.version}</version>
    </dependency>
    ```

    > [!NOTE]
    > Запись `${kafka.version}` объявлена в разделе `<properties>..</properties>` файла `pom.xml`. Она настроена для версии Kafka кластера HDInsight.

* Подключаемые модули. Подключаемые модули Maven предоставляют различные возможности. В этом проекте используются следующие подключаемые модули:

    * `maven-compiler-plugin`: с помощью этого модуля можно задать для проекта Java версии 8. Это версия Java, используемая в HDInsight 3.6.
    * `maven-shade-plugin`: используется для создания файла типа uber jar, содержащего это приложение, а также любые зависимости. Он также используется для установки точки входа приложения, с помощью которой вы сможете напрямую запускать JAR-файл, не указывая основной класс.

### <a name="producerjava"></a>Producer.java

Производитель взаимодействует с узлами брокера Kafka (рабочие узлы) и отправляет данные в разделы Kafka. Следующий фрагмент кода взят из файла [Producer.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Producer.java) в [репозитории GitHub](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started). Этот фрагмент демонстрирует, как задавать свойства производителя.

```java
Properties properties = new Properties();
// Set the brokers (bootstrap servers)
properties.setProperty("bootstrap.servers", brokers);
// Set how to serialize key/value pairs
properties.setProperty("key.serializer","org.apache.kafka.common.serialization.StringSerializer");
properties.setProperty("value.serializer","org.apache.kafka.common.serialization.StringSerializer");
KafkaProducer<String, String> producer = new KafkaProducer<>(properties);
```

### <a name="consumerjava"></a>Consumer.java

Потребитель взаимодействует с узлами брокера Kafka (рабочие узлы) и считывает записи в цикле. В следующем фрагменте кода из файла [Consumer.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Consumer.java) задаются свойства потребителя:

```java
KafkaConsumer<String, String> consumer;
// Configure the consumer
Properties properties = new Properties();
// Point it to the brokers
properties.setProperty("bootstrap.servers", brokers);
// Set the consumer group (all consumers must belong to a group).
properties.setProperty("group.id", groupId);
// Set how to serialize key/value pairs
properties.setProperty("key.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
properties.setProperty("value.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
// When a group is first created, it has no offset stored to start reading from. This tells it to start
// with the earliest record in the stream.
properties.setProperty("auto.offset.reset","earliest");

consumer = new KafkaConsumer<>(properties);
```

В этом коде потребитель настроен для чтения с самого начала раздела (`auto.offset.reset` имеет значение `earliest`).

### <a name="runjava"></a>Run.java

Файл [Run.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Run.java) предоставляет интерфейс командной строки, который выполняет код производителя или потребителя. Нужно указать сведения об узле брокера Kafka в качестве параметра. При необходимости можно указать значение идентификатора группы, которое используется процессом потребителя. При создании нескольких экземпляров потребителя с одним и тем же идентификатором группы между ними будет распределяться нагрузка по операциям чтения из раздела.

## <a name="build-and-deploy-the-example"></a>Создание и развертывание примера

1. Скачайте примеры по адресу [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started).

2. Откройте каталог `Producer-Consumer` и выполните следующую команду.

    ```
    mvn clean package
    ```

    Эта команда создает каталог с именем `target`, который содержит файл с именем `kafka-producer-consumer-1.0-SNAPSHOT.jar`.

3. С помощью следующих команд скопируйте файл `kafka-producer-consumer-1.0-SNAPSHOT.jar` в свой кластер HDInsight.

    ```bash
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```

    Замените **SSHUSER** именем пользователя SSH для кластера, а **CLUSTERNAME** — именем кластера. При появлении запроса введите пароль пользователя SSH.

## <a id="run"></a>Выполнение примера

1. Чтобы открыть SSH-подключение к кластеру, выполните следующую команду:

    ```bash
    ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Замените **SSHUSER** именем пользователя SSH для кластера, а **CLUSTERNAME** — именем кластера. При появлении запроса введите пароль для учетной записи пользователя SSH. Дополнительные сведения об использовании `scp` с HDInsight см. в статье [Подключение к HDInsight (Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Чтобы создать разделы Kafka, применяемые в этом примере, выполните следующие действия:

    1. Чтобы сохранить имя кластера в переменной и установить служебную программу синтаксического анализа JSON (`jq`), используйте следующие команды. При появлении запроса введите имя кластера Kafka:
    
        ```bash
        sudo apt -y install jq
        read -p 'Enter your Kafka cluster name:' CLUSTERNAME
        ```
    
    2. Чтобы получить узлы Apache Zookeeper и брокера Kafka, используйте приведенные ниже команды. При появлении запроса введите пароль для учетной записи администратора, чтобы войти на кластер.
    
        ```bash
        export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
        ```

    3. Чтобы создать раздел `test`, используйте следующую команду:

        ```bash
        java -jar kafka-producer-consumer.jar create test $KAFKABROKERS
        ```

3. Чтобы запустить производитель и сохранить данные в разделе, выполните следующую команду:

    ```bash
    java -jar kafka-producer-consumer.jar producer test $KAFKABROKERS
    ```

4. Когда процесс производителя завершится, выполните следующую команду для считывания данных из раздела:

    ```bash
    java -jar kafka-producer-consumer.jar consumer test $KAFKABROKERS
    ```

    Отобразятся считанные записи, а также их количество.

5. Нажмите клавиши __Ctrl+C__, чтобы закрыть потребитель.

### <a name="multiple-consumers"></a>Несколько потребителей

При считывании записей объект-получатель Kafka использует группу объектов-получателей. Использование одной группы с несколькими потребителями приведет к считыванию записей с балансировкой нагрузки из раздела. Каждый потребитель в группе получает часть записей.

Приложение-потребитель принимает параметр, который используется как идентификатор группы. Например, следующая команда предназначена для запуска нового потребителя с идентификатором группы `mygroup`.

```bash
java -jar kafka-producer-consumer.jar consumer test $KAFKABROKERS mygroup
```

Чтобы увидеть этот процесс в действии, выполните следующую команду:

```bash
tmux new-session 'java -jar kafka-producer-consumer.jar consumer test $KAFKABROKERS mygroup' \; split-w
indow -h 'java -jar kafka-producer-consumer.jar consumer test $KAFKABROKERS mygroup' \; attach
```

Эта команда использует `tmux`, чтобы разделить терминал на два столбца. Потребитель запускается в каждом столбце с тем же значением идентификатора группы. Когда потребители закончат чтение, обратите внимание, что каждый из них считал только часть записей. Дважды нажмите клавиши CTLR+C, чтобы выйти из `tmux`.

Потребление по клиентам, входящих в одну группу, обрабатывается по секциям раздела. В этом примере кода созданный ранее раздел `test` имеет восемь секций. Если запущено восемь потребителей, каждый из них считывает записи из одной секции раздела.

> [!IMPORTANT]
> Число экземпляров потребителя, входящих в группу потребителей, должно соответствовать числу секций. В этом примере одна группа объектов-получателей может содержать не больше восьми объектов-получателей, так как это число секций в разделе. Но у вас может быть несколько групп объектов-получателей, в каждую из которых входит не более восьми из них.

Записи хранятся в Kafka в том порядке, в котором они поступают в секцию. Чтобы обеспечить упорядоченную доставку записей *в секцию*, нужно создать группу потребителей. При этом число входящих в нее экземпляров потребителя должно соответствовать числу секций. Чтобы обеспечить упорядоченную доставку записей *в раздел*, нужно создать группу потребителей, которая содержит только один экземпляр потребителя.

## <a name="next-steps"></a>Дополнительная информация

Из этого документа вы узнали, как использовать API производителя и потребителя Apache Kafka для Kafka в HDInsight. Дополнительные сведения о работе с Kafka см. в следующих материалах.

* [Анализ журналов для Apache Kafka](apache-kafka-log-analytics-operations-management.md)
* [Репликация данных между кластерами Apache Kafka](apache-kafka-mirroring.md)
* [API потоков Kafka в HDInsight](apache-kafka-streams-api.md)
