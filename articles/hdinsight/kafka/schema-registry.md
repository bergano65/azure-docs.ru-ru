---
title: Apache Kafka с реестром схемы Fluent в Azure HDInsight
description: Разверните управляемый кластер Kafka HDInsight с граничным узлом в виртуальной сети, а затем установите реестр схемы Fluent на пограничном узле.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 7e17cdca508db81551d988c795bd1235fa729e82
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2020
ms.locfileid: "94636866"
---
# <a name="apache-kafka-with-confluent-schema-registry-in-azure-hdinsight"></a>Apache Kafka с реестром схемы Fluent в Azure HDInsight

Реестр схемы Kafka предоставляет сериализаторы, подключаемые к Apache Kafka клиентам, которые обрабатывают хранилище схемы сообщений и извлекают сообщения Kafka, отправляемые в формате Avro. Он использовался в качестве проекта OSS, но теперь находится под [лицензией на сообщество](https://www.confluent.io/blog/license-changes-confluent-platform/). Реестр схемы дополнительно выполняет следующие задачи:

* Хранение и извлечение схем для поставщиков и потребителей.
* Применение обратной/прямой совместимости/Full в разделах.
* Уменьшите размер полезных данных, отправляемых в Kafka.

В кластере Kafka под управлением HDInsight реестр схемы обычно разворачивается на пограничном узле, чтобы разрешить разделение вычислений от головных узлов.

Ниже приведена репрезентативная архитектура для развертывания реестра схемы в кластере HDInsight. Реестр схемы изначально предоставляет REST API для операций на нем.  Производители и потребители могут взаимодействовать с реестром схемы из виртуальной сети или с помощью [прокси-сервера KAFKA RESTful](rest-proxy.md).

![Реестр схемы HDInsight Kafka](./media/schema-registry/pic1.png)

## <a name="deploy-an-hdinsight-managed-kafka-with-confluent-schema-registry"></a>Развертывание Kafka под управлением HDInsight с помощью реестра схемы Fluent

В этом разделе мы развертываем кластер управляемого Kafka HDInsight с граничным узлом в виртуальной сети, а затем устанавливаем реестр схемы Fluent на пограничном узле.  

1. Нажмите кнопку **Развертывание в Azure** ниже, чтобы войти в Azure и открыть шаблон Resource Manager.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Farnabganguly%2FKafkaschemaregistry%2Fmaster%2Fazuredeploy.json" target="_blank">:::image type="icon" source="media/schema-registry/hdi-deploy-to-azure1.png":::</a>

1. В пользовательском шаблоне развертывания заполните поля, как описано ниже.

    |Свойство. |Описание |
    |---|---|
    |Подписка|В раскрывающемся списке выберите подписку Azure, которая используется для кластера.|
    |Группа ресурсов|В раскрывающемся списке выберите существующую группу ресурсов, а затем **Создать новую**.|
    |Регион|В раскрывающемся списке выберите регион, в котором создается кластер.|
    |Имя кластера,|Введите глобально уникальное имя Или оставьте "как есть", чтобы использовать имя по умолчанию.|
    |Имя пользователя для входа в кластер|Укажите имя пользователя, по умолчанию — **admin**.|
    |Пароль для входа в кластер|Укажите пароль.|
    |Имя пользователя SSH|Укажите имя пользователя, по умолчанию — **sshuser**.|
    |Пароль SSH|Укажите пароль.|

    Оставьте все остальные поля как есть. Чтобы продолжить выберите **Просмотр и создание**.

1. Просмотрите сведения о развертывании, а затем щелкните **создать** , чтобы инициализировать развертывание. Для завершения развертывания может потребоваться 45 минут.

## <a name="configure-the-confluent-schema-registry"></a>Настройка реестра схемы Fluent

В этом разделе мы настраиваем реестр схемы Kafka, который мы установили на пограничном узле. Реестр схемы ограниченного размещения находится в  `/etc/schema-registry/schema-registry.properties` , а механизмы запуска и завершения исполняемых файлов служб находятся в  `/usr/bin/` папке.

Регистр схемы должен быть уверен, что служба Zookeeper может взаимодействовать с кластером HDInsight Kafka. Чтобы получить сведения о кворуме Zookeeper, выполните указанные ниже действия.

1. Используйте [команду SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) для подключения к пограничному узлу кластера. Измените приведенную ниже команду, заменив CLUSTERNAME именем своего кластера, а затем введите команду:

    ```cmd
    ssh sshuser@schema-registry.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Настройте переменную пароля. Замените PASSWORD паролем для входа в кластер, а затем введите команду:

    ```bash
    export password='PASSWORD'
    ```

1. Извлеките имя кластера с правильным регистром. Выполните следующую команду:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
    ```

1. Извлеките узлы Kafka Zookeeper. Выполните следующую команду:

    ```bash
    export KAFKAZKHOSTS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2);

    echo $KAFKAZKHOSTS
    ```

    Запишите это значение, так как оно будет использоваться позже.

1. Извлеките узлы брокера Kafka. Выполните следующую команду:

    ```bash
    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);

    echo $KAFKABROKERS
    ```

1. Откройте файлы свойств реестра схемы в режиме редактирования. Выполните следующую команду:

    ```bash
    sudo nano /etc/schema-registry/schema-registry.properties
    ```

    1. Замените значение на `kafkastore.connection.url` строку Zookeeper, определенную ранее.
    1. Обновите значение для `debug` на `true` .

    Теперь файл свойств выглядит примерно так:  

    ```
    listeners=http://0.0.0.0:8081
    kafkastore.connection.url=zk1-ag4kaf.q2hwzr1xkxjuvobkaagmjjkhta.gx.internal.cloudapp.net:2181,zk2-ag4kaf.q2hwzr1xkxjuvobkaagmjjkhta.gx.internal.cloudapp.net:2181
    kafkastore.topic=_schemas
    debug=true
    ```

1. Чтобы сохранить файл, нажмите клавиши **CTRL+X** , затем — **Y** и **ВВОД**.

1. Запустите реестр схемы и укажите, что он будет использовать обновленный файл свойств реестра схемы. Выполните следующие команды:

    ```bash
    cd /bin
    sudo schema-registry-start /etc/schema-registry/schema-registry.properties
    ```

1. В реестре схемы, работающем в одном сеансе SSH, запустите другое окно SSH.  Зарегистрируйте новую версию схемы под темой "Kafka-Key" и обратите внимание на выходные данные:

    ```cmd
    curl -X POST -i -H "Content-Type: application/vnd.schemaregistry.v1+json" \
        --data '{"schema": "{\"type\": \"string\"}"}'
    ```

    ```output
       HTTP/1.1 200 OK
    Date: Sun, 22 Mar 2020 16:33:04 GMT
    Content-Type: application/vnd.schemaregistry.v1+json
    Content-Length: 9
    Server: Jetty(9.2.24.v20180105)
    ```

1. Зарегистрируйте новую версию схемы в теме "Kafka-value" и обратите внимание на выходные данные:

    ```cmd
    curl -X POST -i -H "Content-Type: application/vnd.schemaregistry.v1+json" \
        --data '{"schema": "{\"type\": \"string\"}"}' \
    ```

    ```output
    HTTP/1.1 200 OK
    Date: Sun, 22 Mar 2020 16:34:18 GMT
    Content-Type: application/vnd.schemaregistry.v1+json
    Content-Length: 9
    Server: Jetty(9.2.24.v20180105)
    ```

1. Перечислите все субъекты и проверьте выходные данные:

    ```cmd
    curl -X GET -i -H "Content-Type: application/vnd.schemaregistry.v1+json" \
        http://localhost:8081/subjects
    ```

    ```output
    HTTP/1.1 200 OK
    Date: Sun, 22 Mar 2020 16:34:39 GMT
    Content-Type: application/vnd.schemaregistry.v1+json
    Content-Length: 27
    Server: Jetty(9.2.24.v20180105)

    ["Kafka-value","Kafka-key"]
    ```

Вы можете попробовать некоторые другие [расширенные команды, перечисленные здесь](https://docs.confluent.io/1.0/schema-registry/docs/intro.html#quickstart).

## <a name="send-and-consume-avro-data-from-kafka-using-schema-registry"></a>Отправка и использование данных Avro из Kafka с помощью реестра схемы

В этом разделе мы будем читать данные из стандартных входных данных и записывать их в раздел Kafka в формате. Затем мы прочитаем данные из разделов Kafka, используя потребитель с модулем форматирования Avro, чтобы преобразовать эти данные в доступный для чтения формат.

1. Создайте раздел Kafka `agkafkaschemareg` .

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic agkafkaschemareg --zookeeper $KAFKAZKHOSTS
    ```

1. С помощью **производителя консоли Kafka Avro** Создайте схему, назначьте ей схему и приступите к отправке данных в раздел в формате Avro. Убедитесь, что раздел Kafka на предыдущем шаге создан, а также что **$KAFKABROKERS** имеет в нем значение.

    Схема, которую мы отправляем, является парой "ключ: значение"

    ```
    Value
    {
      "type": "record",
      "name": "example_schema",
      "namespace": "com.example",
      "fields": [
        {
          "name": "cust_id",
          "type": "int",
          "doc": "Id of the customer account"
        },
        {
          "name": "year",
          "type": "int",
          "doc": "year of expense"
        },
        {
          "name": "expenses",
          "type": {
            "type": "array",
            "items": "float"
          },
          "doc": "Expenses for the year"
        }
      ],
      "doc:": "A basic schema for storing messages"
    }
    ```

    Используйте приведенную ниже команду для запуска **производителя консоли Kafka Avro** :

    ```bash
    /usr/bin/kafka-avro-console-producer     --broker-list $KAFKABROKERS     --topic agkafkaschemareg     --property parse.key=true --property key.schema='{"type" : "int", "name" : "id"}'     --property value.schema='{ "type" : "record", "name" : "example_schema", "namespace" : "com.example", "fields" : [ { "name" : "cust_id", "type" : "int", "doc" : "Id of the customer account" }, { "name" : "year", "type" : "int", "doc" : "year of expense" }, { "name" : "expenses", "type" : {"type": "array", "items": "float"}, "doc" : "Expenses for the year" } ], "doc:" : "A basic schema for storing messages" }'
    ```

1. Когда производитель готов принять сообщения, начните отправку сообщений в стандартном формате схемы Avro. Используйте клавишу TAB для создания интервала между ключом и значением.

    ```
    1 TAB {"cust_id":1313131, "year":2012, "expenses":[1313.13, 2424.24]}
    2 TAB {"cust_id":3535353, "year":2011, "expenses":[761.35, 92.18, 14.41]}
    3 TAB {"cust_id":7979797, "year":2011, "expenses":[4489.00]}
    ```

1. Попробуйте ввести случайные данные, не связанные с схемой, в производитель консоли, чтобы увидеть, как производитель теперь разрешает любые данные, которые не соответствуют предварительно определенной схеме Avro.

    ```
    1       {"cust_id":1313131, "year":2012, "expenses":[1313.13, 2424.24]}
    2       {"cust_id":1313131,"cust_age":34 "year":2012, "expenses":[1313.13, 2424.24,34.212]}
    org.apache.kafka.common.errors.SerializationException: Error deserializing json {"cust_id":1313131,"cust_age":34 "year":2012, "expenses":[1313.13, 2424.24,34.212]} to Avro of schema {"type":"record","name":"example_schema","namespace":"com.example","fields":[{"name":"cust_id","type":"int","doc":"Id of the customer account"},{"name":"year","type":"int","doc":"year of expense"},{"name":"expenses","type":{"type":"array","items":"float"},"doc":"Expenses for the year"}],"doc:":"A basic schema for storing messages"}
    Caused by: org.codehaus.jackson.JsonParseException: Unexpected character ('"' (code 34)): was expecting comma to separate OBJECT entries
     at [Source: java.io.StringReader@3561c410; line: 1, column: 35]
            at org.codehaus.jackson.JsonParser._constructError(JsonParser.java:1433)
            at org.codehaus.jackson.impl.JsonParserMinimalBase._reportError(JsonParserMinimalBase.java:521)
            at org.codehaus.jackson.impl.JsonParserMinimalBase._reportUnexpectedChar(JsonParserMinimalBase.java:442)
            at org.codehaus.jackson.impl.ReaderBasedParser.nextToken(ReaderBasedParser.java:406)
            at org.apache.avro.io.JsonDecoder.getVaueAsTree(JsonDecoder.java:549)
            at org.apache.avro.io.JsonDecoder.doAction(JsonDecoder.java:474)
            at org.apache.avro.io.parsing.Parser.advance(Parser.java:88)
            at org.apache.avro.io.JsonDecoder.advance(JsonDecoder.java:139)
            at org.apache.avro.io.JsonDecoder.readInt(JsonDecoder.java:166)
            at org.apache.avro.io.ValidatingDecoder.readInt(ValidatingDecoder.java:83)
            at org.apache.avro.generic.GenericDatumReader.readInt(GenericDatumReader.java:511)
            at org.apache.avro.generic.GenericDatumReader.readWithoutConversion(GenericDatumReader.java:182)
            at org.apache.avro.generic.GenericDatumReader.read(GenericDatumReader.java:152)
            at org.apache.avro.generic.GenericDatumReader.readField(GenericDatumReader.java:240)
            at org.apache.avro.generic.GenericDatumReader.readRecord(GenericDatumReader.java:230)
            at org.apache.avro.generic.GenericDatumReader.readWithoutConversion(GenericDatumReader.java:174)
            at org.apache.avro.generic.GenericDatumReader.read(GenericDatumReader.java:152)
            at org.apache.avro.generic.GenericDatumReader.read(GenericDatumReader.java:144)
            at io.confluent.kafka.formatter.AvroMessageReader.jsonToAvro(AvroMessageReader.java:213)
            at io.confluent.kafka.formatter.AvroMessageReader.readMessage(AvroMessageReader.java:200)
            at kafka.tools.ConsoleProducer$.main(ConsoleProducer.scala:59)
            at kafka.tools.ConsoleProducer.main(ConsoleProducer.scala)
    
    ```

1. На другом экране запустите потребитель консоли Kafka Avro.

    ```bash
    sudo /usr/bin/kafka-avro-console-consumer --bootstrap-server $KAFKABROKERS --topic agkafkaschemareg --from-beginning
    ```

    Вы должны увидеть следующие выходные данные:

    ```output
    {"cust_id":1313131,"year":2012,"expenses":[1313.13,2424.24]}
    {"cust_id":7979797,"year":2011,"expenses":[4489.0]}
    {"cust_id":3535353,"year":2011,"expenses":[761.35,92.18,14.41]}
    ```

## <a name="next-steps"></a>Дальнейшие действия

* [Использование API-интерфейсов Apache Kafka производителя и потребителя](apache-kafka-producer-consumer-api.md)