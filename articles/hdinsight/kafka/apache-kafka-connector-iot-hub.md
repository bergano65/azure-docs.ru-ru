---
title: Использование Apache Kafka в HDInsight с Центром Интернета вещей
description: Узнайте, как использовать Apache Kafka в HDInsight с Центром Интернета вещей. Проект Центра Интернета вещей Kafka Connect предоставляет соединитель источника и приемника для Kafka. Соединитель источника считывает данные из Центра Интернета вещей, а соединитель приемника записывает данные в Центр Интернета вещей.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/26/2019
ms.openlocfilehash: 48a72b5ba3819712b9e1d2536ae2dd3a06eaf3f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238816"
---
# <a name="use-apache-kafka-on-hdinsight-with-azure-iot-hub"></a>Использование Apache Kafka в HDInsight с Центром Интернета вещей

Узнайте, как использовать соединитель [Apache Kafka Connect Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) для перемещения данных между Apache Kafka в HDInsight и Центром Интернета вещей. В этом документе показано, как запускать соединитель Центра Интернета вещей из граничного узла кластера.

API Kafka Connect позволяет реализовать соединители, которые будут постоянно извлекать данные в Kafka или отправлять их из Kafka в другую систему. [Apache Kafka Connect Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) — это соединитель, который извлекает данные из Центра Интернета вещей в Apache Kafka. Он также может отправлять данные из Kafka в Центр Интернета вещей.

При извлечении из Центра Интернета вещей используется соединитель __источника__. При отправке в Центр Интернета вещей используется соединитель __приемника__. Соединитель Центра Интернета вещей может выступать как соединителем источника, так и соединителем приемника.

На следующей схеме показан поток данных между Центром Интернета вещей и Kafka в HDInsight при использовании соединителя.

![Изображение, на котором показан поток данных из Центра Интернета вещей в Kafka через соединитель](./media/apache-kafka-connector-iot-hub/iot-hub-kafka-connector-hdinsight.png)

Для получения дополнительной информации о [https://kafka.apache.org/documentation/#connect](https://kafka.apache.org/documentation/#connect)API подключения см.

## <a name="prerequisites"></a>Предварительные требования

* Кластер Apache Kafka на HDInsight. Дополнительные сведения см. в документе [Приступая к работе с Apache Kafka в HDInsight](apache-kafka-get-started.md).

* Граничный узел в кластере Kafka. Дополнительные сведения см. в статье [Использование пустых граничных узлов в кластерах Hadoop в HDInsight](../hdinsight-apps-use-edge-node.md).

* Клиент SSH. Дополнительные сведения см. в руководстве по [подключению к HDInsight (Apache Hadoop) с помощью SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Концентратор ИОт Azure. Для этой статьи рассмотрите возможность использования [онлайн-симулятора Connect Raspberry Pi для Azure IoT Hub.](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-web-simulator-get-started)

* [Инструмент сборки Scala.](https://www.scala-sbt.org/)

## <a name="build-the-connector"></a>Построить разъем

1. Загрузите источник для [https://github.com/Azure/toketi-kafka-connect-iothub/](https://github.com/Azure/toketi-kafka-connect-iothub/) разъема из локальной среды.

2. От командной подсказки `toketi-kafka-connect-iothub-master` перейдите в каталог. Затем используйте следующую команду для построения и упаковки проекта:

    ```cmd
    sbt assembly
    ```

    Сборка займет несколько минут. Команда создает файл, `kafka-connect-iothub-assembly_2.11-0.7.0.jar` указанный в каталоге `toketi-kafka-connect-iothub-master\target\scala-2.11` для проекта.

## <a name="install-the-connector"></a>Установка соединителя

1. Загрузите файл .jar на краевой узлы вашего Kafka в кластере HDInsight. Отоверьте приведенную ниже `CLUSTERNAME` команду, заменив фактическим названием кластера. Значения по умолчанию для учетной записи пользователя SSH и имя [кромки используются](../hdinsight-apps-use-edge-node.md#access-an-edge-node) ниже, изменяются по мере необходимости.

    ```cmd
    scp kafka-connect-iothub-assembly*.jar sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. Когда файл будет скопирован, подключитесь к граничному узлу с помощью SSH:

    ```bash
    ssh sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Чтобы установить соединитель в каталог `libs` Kafka, используйте следующую команду:

    ```bash
    sudo mv kafka-connect-iothub-assembly*.jar /usr/hdp/current/kafka-broker/libs/
    ```

Поддерживайте активность соединения SSH для остальных шагов.

## <a name="configure-apache-kafka"></a>Настройка Apache Kafka

От подключения SSH до кромки узла используйте следующие шаги для настройки Kafka для запуска разъема в автономном режиме:

1. Настройте переменную пароля. Замените PASSWORD паролем для входа в кластер, а затем введите команду:

    ```bash
    export password='PASSWORD'
    ```

1. Установите утилиту [jq.](https://stedolan.github.io/jq/) jq упрощает обработку документов JSON, возвращенных из запросов Ambari. Введите следующую команду:

    ```bash
    sudo apt -y install jq
    ```

1. Получите адреса брокеров Kafka. В вашем кластере может быть много брокеров, но вам нужно всего лишь ссылаться на один или два. Чтобы получить адрес двух узлов брокера, используйте следующую команду:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')

    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $KAFKABROKERS
    ```

    Копирование значений для последующего использования. Возвращаемое значение аналогично приведенному ниже тексту.

    `wn0-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092,wn1-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092`

1. Получите адреса узлов Apache Zookeeper. В кластере имеется несколько узлов Zookeeper, но необходимо ссылаться только на один или два. Используйте следующую команду для хранения `KAFKAZKHOSTS`адресов в переменной:

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

1. При запуске соединителя в автономном режиме файл `/usr/hdp/current/kafka-broker/config/connect-standalone.properties` используется для обмена данными с брокерами Kafka. Чтобы изменить файл `connect-standalone.properties`, используйте следующую команду:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-standalone.properties
    ```

1. Внести следующие вечения:

    |Текущее значение |Новое значение | Комментарий |
    |---|---|---|
    |`bootstrap.servers=localhost:9092`|Заменить `localhost:9092` значение с брокером хостов от предыдущего шага|Настраивает автономную конфигурацию для краевого узла, чтобы найти брокеров Kafka.|
    |`key.converter=org.apache.kafka.connect.json.JsonConverter`|`key.converter=org.apache.kafka.connect.storage.StringConverter`|Это изменение можно проверить с помощью отправителя консоли, входящего в состав Kafka. Для различных отправителей и потребителей вам могут понадобиться различные преобразователи. Для получения информации об использовании [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)других значений преобразователя см.|
    |`value.converter=org.apache.kafka.connect.json.JsonConverter`|`value.converter=org.apache.kafka.connect.storage.StringConverter`|То же, что и выше.|
    |Недоступно|`consumer.max.poll.records=10`|Добавьте к концу файла. Это изменение предназначено для предотвращения времени ожидания в соединителе приемника, ограничивая его 10 записями за раз. Для получения дополнительной [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)информации см.|

1. Чтобы сохранить файл, нажмите клавиши __CTRL+X__, затем — __Y__ и __ВВОД__.

1. Чтобы создать разделы для соединителя, используйте следующие команды:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotin --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotout --zookeeper $KAFKAZKHOSTS
    ```

    Чтобы проверить, что разделы `iotin` и `iotout` существуют, используйте следующую команду:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    Раздел `iotin` используется для получения сообщений из Центра Интернета вещей. Раздел `iotout` используется для отправки сообщений в Центр Интернета вещей.

## <a name="get-iot-hub-connection-information"></a>Получение сведений о подключении Центра Интернета вещей

Чтобы извлечь информацию о Центре Интернета вещей, используемую соединителем, выполните следующие действия:

1. Получите конечную точку, совместимую с центрами событий, и ее имя для Центра Интернета вещей. Чтобы получить эту информацию, используйте один из указанных ниже способов:

   * __На [портале Azure](https://portal.azure.com/)__ выполните указанные ниже действия.

     1. Перейдите к Центру Интернета вещей и выберите __Конечные точки__.
     2. В разделе __Встроенные конечные точки__ выберите __События__.
     3. В разделе __Свойства__ скопируйте значения следующих полей:

         * __Имя, совместимое с концентратором событий__
         * __Конечная точка, совместимая с концентраторами событий__
         * __Секции__

        > [!IMPORTANT]  
        > Значение конечной точки на портале может содержать лишний текст, который не требуется в этом примере. Извлеките текст, который соответствует этому шаблону: `sb://<randomnamespace>.servicebus.windows.net/`.

   * __В [интерфейсе командной строки Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__ введите следующую команду:

       ```azurecli
       az iot hub show --name myhubname --query "{EventHubCompatibleName:properties.eventHubEndpoints.events.path,EventHubCompatibleEndpoint:properties.eventHubEndpoints.events.endpoint,Partitions:properties.eventHubEndpoints.events.partitionCount}"
       ```

       Замените переменную `myhubname` именем Центра Интернета вещей. В ответ вы получите примерно такой текст:

       ```json
       "EventHubCompatibleEndpoint": "sb://ihsuprodbnres006dednamespace.servicebus.windows.net/",
       "EventHubCompatibleName": "iothub-ehub-myhub08-207673-d44b2a856e",
       "Partitions": 2
       ```

2. Получите __политику общего доступа__ и __ключ__. Для этого примера используйте __служебный__ ключ. Чтобы получить эту информацию, используйте один из указанных ниже способов:

    * __На [портале Azure](https://portal.azure.com/)__ выполните указанные ниже действия.

        1. Выберите __Политика общего доступа__, а затем выберите __службу__.
        2. Копирование __основного ключевого__ значения.
        3. Скопируйте значение поля __Строка подключения — первичный ключ__.

    * __В [интерфейсе командной строки Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__ введите следующую команду:

        1. Чтобы получить значение первичного ключа, используйте следующую команду:

            ```azurecli
            az iot hub policy show --hub-name myhubname --name service --query "primaryKey"
            ```

            Замените переменную `myhubname` именем Центра Интернета вещей. Ответ — это первичный ключ для политики `service` этого центра.

        2. Чтобы получить строку подключения для политики `service`, используйте следующую команду:

            ```azurecli
            az iot hub show-connection-string --name myhubname --policy-name service --query "connectionString"
            ```

            Замените переменную `myhubname` именем Центра Интернета вещей. Ответом является строка подключения для политики `service`.

## <a name="configure-the-source-connection"></a>Настройка подключения к источнику

Чтобы настроить источник для работы с Центром Интернета вещей, выполните следующие действия из SSH-подключения к граничному узлу:

1. Создайте копию файла `connect-iot-source.properties` в каталоге `/usr/hdp/current/kafka-broker/config/`. Скачайте файл из проекта toketi-kafka-connect-iothub, используя следующую команду:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-source.properties
    ```

1. Чтобы изменить файл `connect-iot-source.properties` и добавить информацию о Центре Интернета вещей, используйте следующую команду:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

1. В редакторе найдите и измените следующие записи:

    |Текущее значение |Изменить|
    |---|---|
    |`Kafka.Topic=PLACEHOLDER`|Замените `PLACEHOLDER` на `iotin`. Сообщения, полученные из Центра Интернета вещей, размещаются в разделе `iotin`.|
    |`IotHub.EventHubCompatibleName=PLACEHOLDER`|замените `PLACEHOLDER` именем, совместимым с Центрами событий.|
    |`IotHub.EventHubCompatibleEndpoint=PLACEHOLDER`|замените `PLACEHOLDER` конечной точкой, совместимой с Центрами событий.|
    |`IotHub.AccessKeyName=PLACEHOLDER`|Замените `PLACEHOLDER` на `service`.|
    |`IotHub.AccessKeyValue=PLACEHOLDER`|замените `PLACEHOLDER` первичным ключом политики `service`.|
    |`IotHub.Partitions=PLACEHOLDER`|замените `PLACEHOLDER` на количество секций из предыдущего шага.|
    |`IotHub.StartTime=PLACEHOLDER`|замените `PLACEHOLDER` датой в формате UTC. Это время и дата, когда соединитель начинает проверку на наличие сообщений. Формат даты — `yyyy-mm-ddThh:mm:ssZ`.|
    |`BatchSize=100`|Замените `100` на `5`. В таком случае соединитель считывает сообщения в Kafka, когда в Центре Интернета вещей появилось пять новых сообщений.|

    Для примера конфигурации см. [Kafka Connect Source Connectдля для концентратора Azure IoT.](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md)

1. Чтобы сохранить изменения, нажмите клавиши __CTRL+X__, затем — __Y__ и __ВВОД__.

Для получения дополнительной информации о настройке [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md)источника разъема см.

## <a name="configure-the-sink-connection"></a>Настройка подключения приемника

Чтобы настроить подключение приемника для работы с Центром Интернета вещей, выполните следующие действия из SSH-подключения к граничному узлу:

1. Создайте копию файла `connect-iothub-sink.properties` в каталоге `/usr/hdp/current/kafka-broker/config/`. Скачайте файл из проекта toketi-kafka-connect-iothub, используя следующую команду:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-sink.properties
    ```

1. Чтобы изменить файл `connect-iothub-sink.properties` и добавить информацию о Центре Интернета вещей, используйте следующую команду:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
    ```

1. В редакторе найдите и измените следующие записи:

    |Текущее значение |Изменить|
    |---|---|
    |`topics=PLACEHOLDER`|Замените `PLACEHOLDER` на `iotout`. Сообщение, записанные в раздел `iotout`, переадресовываются в Центр Интернета вещей.|
    |`IotHub.ConnectionString=PLACEHOLDER`|замените `PLACEHOLDER` строкой подключения политики `service`.|

    Для примера конфигурации см. [Kafka Connect Sink Connector для azure IoT Hub.](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)

1. Чтобы сохранить изменения, нажмите клавиши __CTRL+X__, затем — __Y__ и __ВВОД__.

Для получения дополнительной информации о настройке [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)раковины разъема см.

## <a name="start-the-source-connector"></a>Запуск соединителя источника

1. Чтобы запустить соединитель источника, используйте следующую команду из SSH-подключения к пограничному узлу:

    ```bash
    /usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

    После запуска соединителя отправьте сообщения в Центр Интернета вещей со своих устройств. Когда соединитель считывает сообщения из Центра Интернета вещей и сохраняет их в разделе Kafka, он регистрирует информацию на консоли:

    ```output
    [2017-08-29 20:15:46,112] INFO Polling for data - Obtained 5 SourceRecords from IotHub (com.microsoft.azure.iot.kafka.connect.IotHubSourceTask:39)
    [2017-08-29 20:15:54,106] INFO Finished WorkerSourceTask{id=AzureIotHubConnector-0} commitOffsets successfully in 4 ms (org.apache.kafka.connect.runtime.WorkerSourceTask:356)
    ```

    > [!NOTE]  
    > При запуске соединителя может появиться несколько предупреждений. Эти предупреждения не вызывают проблем с получением сообщений из Центра Интернета вещей.

1. Остановите разъем через несколько минут, используя **Ctrl и C** дважды. Это займет несколько минут для разъема, чтобы остановить.

## <a name="start-the-sink-connector"></a>Запуск соединителя приемника

Чтобы запустить соединитель приемника в автономном режиме, используйте следующую команду из SSH-подключения к граничному узлу:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
```

После запуска соединителя отобразится похожая информация:

```output
[2017-08-30 17:49:16,150] INFO Started tasks to send 1 messages to devices. (com.microsoft.azure.iot.kafka.connect.sink.
IotHubSinkTask:47)
[2017-08-30 17:49:16,150] INFO WorkerSinkTask{id=AzureIotHubSinkConnector-0} Committing offsets (org.apache.kafka.connect.runtime.WorkerSinkTask:262)
```

> [!NOTE]  
> При запуске соединителя может появиться несколько предупреждений. Эти предупреждения можно игнорировать.

## <a name="send-messages"></a>Отправка сообщений

Для отправки сообщений через соединитель, выполните следующие действия:

1. Откройте *вторую* сессию SSH в кластере Кафка:

    ```bash
    ssh sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Получите адрес брокеров Kafka для новой сессии ssh. Замените PASSWORD паролем для входа в кластер, а затем введите команду:

    ```bash
    export password='PASSWORD'

    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')

    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

1. Для отправки сообщений в раздел `iotout` используйте следующую команду:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic iotout
    ```

    Эта команда не возвращает вас к обычному запросу Bash. Вместо этого он отправляет ввод с клавиатуры в раздел `iotout`.

1. Чтобы отправить сообщение на устройство, вставьте документ JSON в сеанс SSH для `kafka-console-producer`.

    > [!IMPORTANT]  
    > Для записи `"deviceId"` необходимо задать идентификатор устройства. В следующем примере устройство называется `myDeviceId`:

    ```json
    {"messageId":"msg1","message":"Turn On","deviceId":"myDeviceId"}
    ```

    Схема для этого документа JSON описана более [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)подробно на .

    Если вы используете смоделированное устройство Raspberry Pi и оно работает, устройство регистрирует следующее сообщение:

    ```output
    Receive message: Turn On
    ```

    Повторно отправьте документ JSON, но измените значение записи `"message"`. Новое значение записывается на устройстве.

Для получения дополнительной информации об [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)использовании разъема раковины, см.

## <a name="next-steps"></a>Дальнейшие действия

Из этого документа вы узнали, как использовать API Apache Kafka Connect для запуска соединителя IoT Kafka в HDInsight. Другие материалы, посвященные работе с Kafka, доступны по следующим ссылкам:

* [Используйте Apache Spark с Apache Kafka на HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Использование Apache Storm с Apache Kafka в HDInsight](../hdinsight-apache-storm-with-kafka.md)
