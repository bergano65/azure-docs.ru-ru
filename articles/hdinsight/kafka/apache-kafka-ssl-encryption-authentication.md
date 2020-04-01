---
title: Шифрование Apache Kafka TLS & аутентификацию - Azure HDInsight
description: Настройка шифрования TLS для связи между клиентами Kafka и брокерами Kafka, а также между брокерами Kafka. Настройка SSL аутентификации клиентов.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: hrasheed
ms.openlocfilehash: 027a66f4b83225f3c776e1bff1d706f6f4dba976
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80436995"
---
# <a name="set-up-tls-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Настройка шифрования TLS и аутентификации для Apache Kafka в Azure HDInsight

В этой статье показано, как настроить шифрование Transport Layer Security (TLS), ранее известное как шифрование Secure Sockets Layer (SSL), между клиентами Apache Kafka и брокерами Apache Kafka. Он также показывает, как настроить аутентификацию клиентов (иногда называют двусторонним TLS).

> [!Important]
> Есть два клиента, которые можно использовать для приложений Kafka: клиент Java и консольный клиент. Только клиент `ProducerConsumer.java` Java может использовать TLS как для производства, так и для потребления. Клиент `console-producer.sh` производителя консолей не работает с TLS.

> [!Note] 
> Производитель консолей HDInsight Kafka с версией 1.1 не поддерживает SSL.
## <a name="apache-kafka-broker-setup"></a>Настройка брокера Apache Kafka

Установка брокера Kafka TLS будет использовать четыре vMs-кластера HDInsight следующим образом:

* headnode 0 - Сертификат органа (CA)
* рабочий узла 0, 1 и 2 - брокеры

> [!Note] 
> В этом руководстве будут использоваться самозаверяющие сертификаты, но наиболее безопасными являются сертификаты, выданные доверенными центрами сертификации.

Резюме процесса настройки брокера заключается в следующем:

1. На каждом из трех рабочих узлов повторяются следующие шаги:

    1. Создайте сертификат.
    1. Создайте запрос на подписание сертификата.
    1. Отправить запрос на подписание сертификата в Сертификационный орган (CA).
    1. Вопийте в CA и подпишите запрос.
    1. SCP подписанный сертификат обратно в рабочий узла.
    1. SCP публичный сертификат CA к узлам работника.

1. После того, как у вас есть все сертификаты, положить сертификаты в магазин сертификата.
1. Перейти к Ambari и изменить конфигурации.

Используйте следующие подробные инструкции для завершения настройки брокера:

> [!Important]
> В следующих фрагментах кода wnX является аббревиатом для одного из трех `wn0` `wn1` рабочих `wn2` узлов и должен быть заменен или по мере необходимости. `WorkerNode0_Name`и `HeadNode0_Name` должны быть заменены названиями соответствующих машин.

1. Выполните начальную установку на головном узлах 0, которая для HDInsight будет заполнить роль Органа по сертификации (CA).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Выполните ту же начальную настройку на каждом из брокеров (рабочие узлы 0, 1 и 2).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. На каждом из узлов рабочего уровня выполните следующие действия, используя фрагмент кода ниже.
    1. Создайте магазин ключей и заселите его новым частным сертификатом.
    1. Создайте запрос на подписание сертификата.
    1. SCP запрос на подписание сертификата в CA (headnode0)

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. На машине CA выполняется следующая команда для создания файлов ca-cert и ca-key:

    ```bash
    openssl req -new -newkey rsa:4096 -days 365 -x509 -subj "/CN=Kafka-Security-CA" -keyout ca-key -out ca-cert -nodes
    ```

1. Измените в машину CA и подпишите все полученные запросы на подписание сертификата:

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. Отправить подписанные сертификаты обратно в рабочие узлы из CA (headnode0).

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. Отправить общедоступный сертификат CA каждому узлам работника.

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. На каждом узлах работника добавьте публичный сертификат CAs в магазин доверия и клавиатуру. Затем добавьте собственный подписанный сертификат рабочего узла в магазин ключей

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-tls-and-restart-brokers"></a>Обновление конфигурации Kafka для использования TLS и перезапуска брокеров

Теперь вы создали каждый брокер Kafka с keystore и truststore, и импортировали правильные сертификаты. Далее измените связанные свойства конфигурации Kafka с помощью Ambari, а затем перезапустите брокеры Kafka.

Выполните следующие действия для завершения конфигурации:

1. Войдите на портал Azure и выберите свой кластер Azure HDInsight Apache Kafka.
1. Перейдите в пользовательский интерфейс Ambari, щелкнув **домашнюю страницу Ambari** в разделе **Панели мониторинга кластера**.
1. В разделе **Kafka Broker** (Брокер Kafka) задайте для свойства **listeners** значение `PLAINTEXT://localhost:9092,SSL://localhost:9093`.
1. В разделе **Advanced kafka-broker** (Расширенный брокер Kafka) задайте для свойства **security.inter.broker.protocol** значение `SSL`.

    ![Изменение свойств конфигурации SSL для Kafka в Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. В разделе **Custom kafka-broker** (Пользовательский брокер Kafka) задайте для свойства **ssl.client.auth** значение `required`. Этот шаг необходим только при настройке аутентификации и шифрования.

    ![Изменение свойств конфигурации SSL для Kafka в Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. Добавьте новые свойства конфигурации в файл server.properties.

    ```bash
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092,SSL://$IP_ADDRESS:9093" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.key.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. Перейдите к uI конфигурации Ambari и убедитесь, что новые свойства отображаются под **Расширенный kafka-env** и свойство **шаблона kafka-env.**

    Для версии ИРЧП 3.6:

    ![Редактирование свойства шаблона kafka-env в Амбари](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env.png)

    Для версии ИРЧП 4.0:

     ![Редактирование шаблона kafka-env в Ambari four](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env-four.png)   

1. Перезапустите все брокеры Kafka.
1. Начните с клиента-админа с производителями и потребителями, чтобы убедиться, что как производители, так и потребители работают над портом 9093.

## <a name="client-setup-without-authentication"></a>Установка клиента (без проверки подлинности)

Если вам не нужна аутентификация, резюме шагов для настройки только шифрования TLS:

1. Войти в ЦС (активный головной узлы).
1. Копирование сертификата CA для клиентской машины с машины CA (wn0).
1. Войдите в клиентскую машину (hn1) и перейдите к папке. `~/ssl`
1. Импорт сертификата CA в доверительный магазин.
1. Импорт сертификата CA в ключевой магазин.

Эти шаги подробно описаны в следующих фрагментах кода.

1. Войти на узлы CA.

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    ```

1. Копирование ca-cert для клиентской машины

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

1. Войти на клиентскую машину (узло резервной головки).

    ```bash
    ssh sshuser@HeadNode1_Name
    cd ssl
    ```

1. Импорт сертификата CA в доверительный магазин.

    ```bash
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Импорт сертификата CA в ключевой магазин.
    
    ```bash
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. Создайте файл `client-ssl-auth.properties`. В нем должны присутствовать следующие строки.

    ```config
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ```

## <a name="client-setup-with-authentication"></a>Установка клиента (с проверкой подлинности)

> [!Note]
> Следующие шаги необходимы только при настройке шифрования TLS **и** аутентификации. Если вы только настраиваете шифрование, смотрите [настройку клиента без проверки подлинности.](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication)

Следующие четыре шага обобщают задачи, необходимые для завершения настройки клиента:

1. Войти на клиентскую машину (узло резервной головки).
1. Создайте хранилище ключей Java и получите подписанный сертификат для брокера. Затем скопируйте сертификат на виртуальную машину, где запущен центр сертификации.
1. Переключиться на машину CA (активный головной узлы) для подписания сертификата клиента.
1. Перейти к клиентской машине (резервный головной узлы) и перейти к папке. `~/ssl` Скопируйте подписанный сертификат на клиентский компьютер.

Подробная информация о каждом шаге приведена ниже.

1. Войти на клиентскую машину (узло резервной головки).

    ```bash
    ssh sshuser@HeadNode1_Name
    ```

1. Удалите существующий каталог ssl.

    ```bash
    rm -R ~/ssl
    mkdir ssl
    cd ssl
    ```

1. Создайте клавиатуру java и создайте запрос на подписание сертификата. 

    ```bash
    keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -dname "CN=HEADNODE1_FQDN" -storetype pkcs12
    
    keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -storepass "MyClientPassword123" -keypass "MyClientPassword123"
    ```

1. Копирование запроса на подписание сертификата в CA

    ```bash
    scp client-cert-sign-request sshuser@HeadNode0_Name:~/ssl/client-cert-sign-request
    ```

1. Переключитесь на машину CA (активный головной узлы) и подпишите сертификат клиента.

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    openssl x509 -req -CA ca-cert -CAkey ca-key -in ~/ssl/client-cert-sign-request -out ~/ssl/client-cert-signed -days 365 -CAcreateserial -passin pass:MyClientPassword123
    ```

1. Копирование подписанного сертификата клиента от CA (активный головной узла) до клиентской машины.

    ```bash
    scp client-cert-signed sshuser@HeadNode1_Name:~/ssl/client-signed-cert
    ```

1. Копирование ca-cert для клиентской машины

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

1. Создайте клиентский магазин с подписанным сертификатом и импортируйте ca cert в ключевой магазин и доверительный магазин:

    ```bash
    keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -storepass MyClientPassword123 -keypass MyClientPassword123 -noprompt
    
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass MyClientPassword123 -keypass MyClientPassword123 -noprompt
    
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass MyClientPassword123 -keypass MyClientPassword123 -noprompt
    ```

1. Создайте `client-ssl-auth.properties`файл . В нем должны присутствовать следующие строки.

    ```bash
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
    ssl.keystore.password=MyClientPassword123
    ssl.key.password=MyClientPassword123
    ```

## <a name="verification"></a>Проверка

> [!Note]
> Если HDInsight 4.0 и Kafka 2.1 установлены, вы можете использовать производителя консоли / потребителей для проверки вашей настройки. Если нет, запустите производителя Kafka на порту 9092 и отправить сообщения на эту тему, а затем использовать потребителя Кафки на порт 9093, который использует TLS.

### <a name="kafka-21-or-above"></a>Кафка 2.1 или выше

1. Создайте тему, если она еще не существует.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1.  Запустите производитель консолей `client-ssl-auth.properties` и предоставьте путь к файлу конфигурации для производителя.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9093 --topic topic1 --producer.config ~/ssl/client-ssl-auth.properties
    ```

1.  Откройте еще одно соединение ssh к клиентской `client-ssl-auth.properties` машине и запустите консоль потребителя и предоставьте путь в качестве файла конфигурации для потребителя.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

### <a name="kafka-11"></a>Кафка 1,1

1. Создайте тему, если она еще не существует.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE_0>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1.  Запустите производитель консолей и предоставьте путь к клиенту-ssl-auth.properties в качестве файла конфигурации для производителя.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9092 --topic topic1 
    ```

3.  Откройте еще одно соединение ssh к клиентской `client-ssl-auth.properties` машине и запустите консоль потребителя и предоставьте путь в качестве файла конфигурации для потребителя.

    ```bash
    $ /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

## <a name="next-steps"></a>Следующие шаги

* [Что такое Apache Kafka в HDInsight?](apache-kafka-introduction.md)
