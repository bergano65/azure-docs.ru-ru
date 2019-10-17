---
title: Настройка шифрования и проверки подлинности SSL для Apache Kafka в Azure HDInsight
description: Настройте SSL-шифрование для обмена данными между клиентами Kafka и брокерами Kafka, а также между брокерами Kafka. Настройте проверку подлинности клиентов по протоколу SSL.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: hrasheed
ms.openlocfilehash: 19a817124afb9afcee25b5f2bff73b8a17e16519
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72431277"
---
# <a name="set-up-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Настройка шифрования и проверки подлинности SSL (SSL) для Apache Kafka в Azure HDInsight

В этой статье показано, как настроить SSL-шифрование между клиентами Apache Kafka и брокерами Apache Kafka. Здесь также показано, как настроить проверку подлинности клиентов (иногда это называется двусторонним протоколом SSL).

> [!Important]
> Для приложений Kafka можно использовать два клиента: клиент Java и клиент консоли. Только клиент Java `ProducerConsumer.java` может использовать SSL как для создания, так и для использования. Клиент производителя консоли `console-producer.sh` не работает с SSL.

## <a name="apache-kafka-broker-setup"></a>Установка компонента Service Broker Apache Kafka

Программа установки брокера SSL Kafka будет использовать четыре виртуальных машины кластера HDInsight следующим образом:

* головного узла 0 — центр сертификации (ЦС)
* Рабочий узел 0, 1 и 2 — брокеры

> [!Note] 
> В этом руководстве будут использоваться самозаверяющие сертификаты, но наиболее безопасными являются сертификаты, выданные доверенными центрами сертификации.

Ниже приведена сводка процесса установки компонента Service Broker.

1. Следующие шаги повторяются на каждом из трех рабочих узлов:

    1. Создайте сертификат.
    1. Создайте запрос подписи сертификата.
    1. Отправьте запрос подписи сертификата в центр сертификации (ЦС).
    1. Войдите в центр сертификации и подпишите запрос.
    1. Наscp подписанный сертификат обратно на рабочий узел.
    1. Наscp открытый сертификат ЦС на рабочий узел.

1. Получив все сертификаты, поставьте сертификаты в хранилище сертификатов.
1. Перейдите по адресу Ambari и измените настройки.

Используйте следующие подробные инструкции для завершения установки компонента Service Broker.

> [!Important]
> В следующих фрагментах кода Внкс является сокращением для одного из трех рабочих узлов и должно быть заменено `wn0`, `wn1` или `wn2` соответственно. `WorkerNode0_Name` и `HeadNode0_Name` должны быть заменены именами соответствующих компьютеров, например `wn0-abcxyz` или `hn0-abcxyz`.

1. Выполните начальную настройку на головном узле 0, который для HDInsight будет заполнять роль центра сертификации (ЦС).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Выполните ту же начальную настройку для каждого брокера (рабочие узлы 0, 1 и 2).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. На каждом из рабочих узлов выполните следующие действия с помощью приведенного ниже фрагмента кода.
    1. Создайте хранилище ключей и заполните его новым частным сертификатом.
    1. Создайте запрос подписи сертификата.
    1. SCP — запрос подписи сертификата в ЦС (headnode0)

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. Перейдите на компьютер ЦС и подпишите все полученные запросы на подписывание сертификатов:

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. Отправьте подписанные сертификаты обратно рабочим узлам из центра сертификации (headnode0).

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. Отправьте открытый сертификат ЦС на каждый рабочий узел.

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. На каждом рабочем узле добавьте общедоступный сертификат CAs в trustStore и хранилище ключей. Затем добавьте в хранилище ключей собственный подписанный сертификат рабочего узла.

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-ssl-and-restart-brokers"></a>Обновите конфигурацию Kafka для использования SSL и перезапуска брокеров

Теперь вы настроили каждый брокер Kafka с хранилищем ключей и trustStore и импортировали правильные сертификаты. Далее измените связанные свойства конфигурации Kafka с помощью Ambari, а затем перезапустите брокеры Kafka.

Выполните следующие действия для завершения конфигурации:

1. Войдите на портал Azure и выберите свой кластер Azure HDInsight Apache Kafka.
1. Перейдите в пользовательский интерфейс Ambari, щелкнув **домашнюю страницу Ambari** в разделе **Панели мониторинга кластера**.
1. В разделе **Kafka Broker** (Брокер Kafka) задайте для свойства **listeners** значение `PLAINTEXT://localhost:9092,SSL://localhost:9093`.
1. В разделе **Advanced kafka-broker** (Расширенный брокер Kafka) задайте для свойства **security.inter.broker.protocol** значение `SSL`.

    ![Изменение свойств конфигурации SSL для Kafka в Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. В разделе **Custom kafka-broker** (Пользовательский брокер Kafka) задайте для свойства **ssl.client.auth** значение `required`. Этот шаг необходим только при настройке проверки подлинности и шифрования.

    ![Изменение свойств конфигурации SSL для Kafka в Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. Выполните приведенные ниже команды, которые будут добавлять свойства конфигурации в файл Kafka `server.properties` для объявления IP-адресов вместо полного доменного имени (FQDN).

    ```bash
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

1. Чтобы убедиться, что предыдущие изменения были внесены правильно, вы можете проверить, что следующие строки присутствуют в файле `server.properties` Kafka.

    ```bash
    advertised.listeners=PLAINTEXT://10.0.0.11:9092,SSL://10.0.0.11:9093
    ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks
    ssl.keystore.password=MyServerPassword123
    ssl.key.password=MyServerPassword123
    ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks
    ssl.truststore.password=MyServerPassword123
    ```

1. Перезапустите все брокеры Kafka.
1. Запустите клиент администрирования с параметрами Producer и Consumer, чтобы убедиться, что оба производителя и потребитель работают с портом 9093.

## <a name="client-setup-with-authentication"></a>Установка клиента (с проверкой подлинности)

> [!Note]
> Следующие шаги требуются только в том случае, если вы настраиваете проверку подлинности **и** шифрование SSL. Если вы настраиваете только шифрование, перейдите к [установке клиента без проверки подлинности](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication).

Чтобы завершить настройку клиента, выполните следующие действия.

1. Войдите на клиентский компьютер (HN1).
1. Создайте хранилище ключей Java и получите подписанный сертификат для брокера. Затем скопируйте сертификат на виртуальную машину, где запущен центр сертификации.
1. Переключитесь на компьютер CA (hn0), чтобы подписать сертификат клиента.
1. Войдите на клиентский компьютер (hn1) и перейдите к папке `~/ssl`. Скопируйте подписанный сертификат на клиентский компьютер.

```bash
cd ssl

# Create a java keystore and get a signed certificate for the broker. Then copy the certificate to the VM where the CA is running.

keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -dname "CN=mylaptop1" -alias my-local-pc1 -storetype pkcs12

keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -alias my-local-pc1 -storepass "MyClientPassword123" -keypass "MyClientPassword123"

# Copy the cert to the CA
scp client-cert-sign-request3 sshuser@HeadNode0_Name:~/tmp1/client-cert-sign-request

# Switch to the CA machine (hn0) to sign the client certificate.
cd ssl
openssl x509 -req -CA ca-cert -CAkey ca-key -in /tmp1/client-cert-sign-request -out /tmp1/client-cert-signed -days 365 -CAcreateserial -passin pass:MyServerPassword123

# Return to the client machine (hn1), navigate to ~/ssl folder and copy signed cert from the CA (hn0) to client machine
scp -i ~/kafka-security.pem sshuser@HeadNode0_Name:/tmp1/client-cert-signed

# Import CA cert to trust store
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import CA cert to key store
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import signed client (cert client-cert-signed1) to keystore
keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -alias my-local-pc1 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
```

Наконец, просмотрите файл `client-ssl-auth.properties` с помощью команды `cat client-ssl-auth.properties`. В нем должны присутствовать следующие строки.

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=MyClientPassword123
ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
ssl.keystore.password=MyClientPassword123
ssl.key.password=MyClientPassword123
```

## <a name="client-setup-without-authentication"></a>Установка клиента (без проверки подлинности)

Если проверка подлинности не требуется, выполните следующие действия для настройки только шифрования SSL:

1. Войдите на клиентский компьютер (hn1) и перейдите к папке `~/ssl`.
1. Скопируйте подписанный сертификат с компьютера ЦС (wn0) на клиентский компьютер.
1. Импортируйте сертификат ЦС в хранилище доверенных сертификатов.
1. Импортируйте сертификат ЦС в хранилище ключей.

Эти шаги показаны в следующем фрагменте кода.

```bash
cd ssl

# Copy signed cert to client machine
scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert .

# Import CA cert to truststore
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import CA cert to keystore
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file cert-signed -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
```

Наконец, просмотрите файл `client-ssl-auth.properties` с помощью команды `cat client-ssl-auth.properties`. В нем должны присутствовать следующие строки.

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=MyClientPassword123
```

## <a name="next-steps"></a>Дальнейшие действия

* [Что такое Apache Kafka в HDInsight?](apache-kafka-introduction.md)
