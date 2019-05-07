---
title: Настройка шифрования SSL и проверки подлинности для Apache Kafka в Azure HDInsight
description: Настройка SSL-шифрования для обмена данными между клиентами Kafka и брокеров Kafka также между брокеров Kafka. Настройка проверки подлинности SSL клиентов.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: hrasheed
ms.openlocfilehash: e526908f5ba9feea53b1c1abebbbfc1bd9a51c54
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65147966"
---
# <a name="set-up-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Настроить шифрование Secure Sockets Layer (SSL) и проверку подлинности для Apache Kafka в Azure HDInsight

В этой статье показано, как настроить SSL-шифрование между клиентами Apache Kafka и брокеров Kafka, Apache. Он также показано, как для настройки проверки подлинности клиентов (иногда называют двустороннее SSL).

> [!Important]
> Существуют два клиента, которые можно использовать для Kafka приложений: клиента Java и консоли клиента. Только клиент Java `ProducerConsumer.java` можно использовать SSL для создания и использования. Производитель клиент консоли `console-producer.sh` не работает с SSL.

## <a name="apache-kafka-broker-setup"></a>Настройка брокера Apache Kafka

Настройка брокера Kafka SSL будет использовать четыре виртуальные машины кластера HDInsight следующим образом:

* головной узел 0 — центр сертификации (ЦС)
* брокеры рабочий узел 0, 1 и 2.

> [!Note] 
> В этом руководстве будут использоваться самозаверяющие сертификаты, но наиболее безопасными являются сертификаты, выданные доверенными центрами сертификации.

Сводка процесса установки broker выглядит следующим образом:

1. Следующие действия будут повторяться на каждой из трех рабочих узлов:

    1. Создайте сертификат.
    1. Создайте запрос подписи сертификата.
    1. Отправка сертификата подписи запроса для центра сертификации (ЦС).
    1. Войдите в ЦС и подписи запроса.
    1. SCP подписанный сертификат обратно к рабочему узлу.
    1. SCP открытый сертификат ЦС на рабочий узел.

1. Создав все сертификаты, поместите сертификаты в хранилище сертификатов.
1. Перейдите к Ambari и изменения конфигураций.

Используйте приведенные ниже подробные инструкции для завершения установки компонента broker:

> [!Important]
> В следующих фрагментах кода wnX представляет собой сокращение для одного из трех рабочих узлов и подстановки с `wn0`, `wn1` или `wn2` соответствующим образом. `WorkerNode0_Name` и `HeadNode0_Name` подстановки с именами соответствующих компьютеров, таких как `wn0-abcxyz` или `hn0-abcxyz`.

1. Выполните начальную установку на головном узле 0, что для HDInsight заполнит все роли центра сертификации (ЦС).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl

    # Export
    export SRVPASS=MyServerPassword123
    ```

1. Выполните же начальную установку на каждом из брокеров (рабочих узлов, 0, 1 и 2).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl

    # Export
    export MyServerPassword123=MyServerPassword123
    ```

1. На каждом из рабочих узлов выполните следующие действия, используя приведенный ниже фрагмент кода.
    1. Создайте хранилище ключей и заполняет ее новый закрытый сертификат.
    1. Создание запроса подписи сертификата.
    1. SCP запрос подписи сертификата в ЦС (headnode0)

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. Перейдите на компьютер ЦС и подписи всех полученных запросов подписи сертификата:

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. Экспорт сертификатов обратной отправки рабочих узлов из центра сертификации (headnode0).

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. Отправьте открытый сертификат ЦС для каждого рабочего узла.

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. На каждом рабочем узле Добавьте открытый сертификат ЦС в truststore и хранилища ключей. Затем добавьте подписанный сертификат узла собственной рабочей роли в хранилище ключей

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-ssl-and-restart-brokers"></a>Обновите конфигурацию Kafka для использования SSL и перезапуска брокеров

Вы настроить каждый брокер Kafka с помощью хранилища ключей и truststore и импортировать необходимые сертификаты. Далее измените связанные свойства конфигурации Kafka с помощью Ambari, а затем перезапустите брокеры Kafka.

Выполните следующие действия для завершения конфигурации:

1. Войдите на портал Azure и выберите свой кластер Azure HDInsight Apache Kafka.
1. Перейдите в пользовательский интерфейс Ambari, щелкнув **домашнюю страницу Ambari** в разделе **Панели мониторинга кластера**.
1. В разделе **Kafka Broker** (Брокер Kafka) задайте для свойства **listeners** значение `PLAINTEXT://localhost:9092,SSL://localhost:9093`.
1. В разделе **Advanced kafka-broker** (Расширенный брокер Kafka) задайте для свойства **security.inter.broker.protocol** значение `SSL`.

    ![Изменение свойств конфигурации SSL для Kafka в Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. В разделе **Custom kafka-broker** (Пользовательский брокер Kafka) задайте для свойства **ssl.client.auth** значение `required`. Это действие необходимо только требуется, если вы настраиваете проверку подлинности и шифрование.

    ![Изменение свойств конфигурации SSL для Kafka в Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. Добавьте свойства конфигурации в файл `server.properties` Kafka для объявления IP-адресов вместо полного доменного имени (FQDN).

    ```bash
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092,SSL://$IP_ADDRESS:9093" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.password=<server_password>" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.key.password=<server_password>" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.password=<server_password>" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. Чтобы убедиться, что предыдущие изменения были внесены правильно, вы можете проверить, что следующие строки присутствуют в файле `server.properties` Kafka.

    ```bash
    advertised.listeners=PLAINTEXT://10.0.0.11:9092,SSL://10.0.0.11:9093
    ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks
    ssl.keystore.password=<server_password>
    ssl.key.password=<server_password>
    ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks
    ssl.truststore.password=<server_password>
    ```

1. Перезапустите все брокеры Kafka.

## <a name="client-setup-with-authentication"></a>Установка клиента (с проверкой подлинности)

> [!Note]
> Следующие шаги требуются только в том случае, если вы настраиваете проверку подлинности **и** шифрование SSL. Если вы настраиваете только шифрование, перейдите к [установке клиента без проверки подлинности](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication).

Выполните следующие действия, чтобы завершить установку клиента.

1. Войдите на клиентский компьютер (hn1).
1. Экспортируйте пароль клиента. Замените значение `<client_password>` фактическим паролем администратора на клиентском компьютере Kafka.
1. Создайте хранилище ключей Java и получите подписанный сертификат для брокера. Затем скопируйте сертификат на виртуальную машину, где запущен центр сертификации.
1. Переключиться на компьютере ЦС (hn0) для подписания этого сертификата клиента.
1. Войдите на клиентский компьютер (hn1) и перейдите к папке `~/ssl`. Скопируйте подписанный сертификат на клиентский компьютер.

```bash
export CLIPASS=<client_password>
cd ssl

# Create a java keystore and get a signed certificate for the broker. Then copy the certificate to the VM where the CA is running.

keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass $CLIPASS -keypass $CLIPASS -dname "CN=mylaptop1" -alias my-local-pc1 -storetype pkcs12

keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -alias my-local-pc1 -storepass $CLIPASS -keypass $CLIPASS

# Copy the cert to the CA
scp client-cert-sign-request3 sshuser@HeadNode0_Name:~/tmp1/client-cert-sign-request

# Switch to the CA machine (hn0) to sign the client certificate.
cd ssl
openssl x509 -req -CA ca-cert -CAkey ca-key -in /tmp1/client-cert-sign-request -out /tmp1/client-cert-signed -days 365 -CAcreateserial -passin pass:<server_password>

# Return to the client machine (hn1), navigate to ~/ssl folder and copy signed cert from the CA (hn0) to client machine
scp -i ~/kafka-security.pem sshuser@HeadNode0_Name:/tmp1/client-cert-signed

# Import CA cert to trust store
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

# Import CA cert to key store
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

# Import signed client (cert client-cert-signed1) to keystore
keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -alias my-local-pc1 -storepass $CLIPASS -keypass $CLIPASS -noprompt
```

Наконец, просмотрите файл `client-ssl-auth.properties` с помощью команды `cat client-ssl-auth.properties`. В нем должны присутствовать следующие строки.

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=<client_password>
ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
ssl.keystore.password=<client_password>
ssl.key.password=<client_password>
```

## <a name="client-setup-without-authentication"></a>Установка клиента (без проверки подлинности)

Если вам не требуется проверка подлинности, приведены шаги для настройки только SSL-шифрования.

1. Войдите на клиентский компьютер (hn1) и перейдите к папке `~/ssl`.
1. Экспортируйте пароль клиента. Замените значение `<client_password>` фактическим паролем администратора на клиентском компьютере Kafka.
1. Скопируйте подписанный сертификат с компьютера ЦС (wn0) на клиентский компьютер.
1. Импортируйте сертификат ЦС в хранилище доверенных сертификатов.
1. Импортируйте сертификат ЦС в хранилище ключей.

Эти шаги показаны в следующем фрагменте кода.

```bash
export CLIPASS=<client_password>
cd ssl

# Copy signed cert to client machine
scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert .

# Import CA cert to truststore
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

# Import CA cert to keystore
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file cert-signed -storepass $CLIPASS -keypass $CLIPASS -noprompt
```

Наконец, просмотрите файл `client-ssl-auth.properties` с помощью команды `cat client-ssl-auth.properties`. В нем должны присутствовать следующие строки.

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=<client_password>
```

## <a name="next-steps"></a>Дальнейшие действия

* [Что такое Apache Kafka в HDInsight?](apache-kafka-introduction.md)
