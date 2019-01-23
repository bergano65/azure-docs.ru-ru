---
title: Настройка шифрования и проверки подлинности SSL для Apache Kafka в Azure HDInsight
description: Настройка шифрования SSL для обмена данными между клиентами Kafka и брокерами Kafka, а также между брокерами Kafka. Настройка проверки подлинности клиентов по протоколу SSL.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: hrasheed
ms.openlocfilehash: 665b439fb1ca0b907ea7385369f64d255e8e42e6
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54355296"
---
# <a name="setup-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Настройка шифрования и проверки подлинности SSL для Apache Kafka в Azure HDInsight

В этой статье описывается, как настроить шифрование SSL для данных, передаваемых от клиентов Apache Kafka к брокерам Apache Kafka, а также для данных, передаваемых между брокерами Apache Kafka. Здесь также приведены действия, необходимые для настройки проверки подлинности клиентов (иногда называется двусторонней проверкой подлинности SSL).

## <a name="server-setup"></a>Настройка сервера

Первый шаг — настройте хранилище ключей и хранилище доверия на брокерах Kafka и импортируйте сертификаты центра сертификации (ЦС) и брокера в эти хранилища.

> [!Note] 
> В этом руководстве будут использоваться самозаверяющие сертификаты, но наиболее безопасными являются сертификаты, выданные доверенными центрами сертификации.

1. Создайте папку с именем ssl и экспортируйте пароль для сервера в виде переменной среды. Для оставшейся части этого руководства замените значение `<server_password>` фактическим паролем администратора для сервера.

    ```bash
    $export SRVPASS=<server_password>
    $mkdir ssl
    $cd ssl
    ```

2. Далее создайте хранилище ключей Java (kafka.server.keystore.jks) и сертификат ЦС.

    ```bash
    $keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass $SRVPASS -keypass $SRVPASS -dname "CN=wn0-umakaf.xvbseke35rbuddm4fyvhm2vz2h.cx.internal.cloudapp.net" -storetype pkcs12
    ```

3. Затем создайте запрос на подпись сертификата, созданного на предыдущем шаге, этим центром сертификации.

    ```bash
    $keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass $SRVPASS -keypass $SRVPASS
    ```

4. Теперь отправьте запрос на подпись этого сертификата в ЦС. Так как используется самозаверяющий сертификат, мы подписываем сертификат в нашем ЦС с помощью команды `openssl`.

    ```bash
    $openssl x509 -req -CA ca-cert -CAkey ca-key -in cert-file -out cert-signed -days 365 -CAcreateserial -passin pass:$SRVPASS
    ```

5. Создайте хранилище доверия и импортируйте сертификат ЦС.

    ```bash
    $keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt
    ```

6. Импортируйте общедоступный сертификат ЦС в хранилище ключей.

    ```bash
    $keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt
    ```

7. Импортируйте подписанный сертификат в хранилище ключей.

    ```bash
    $keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt//output is "Certificate reply was added to keystore"
    ```

Импорт подписанного сертификата в хранилище ключей — последний шаг, необходимый для настройки хранилища доверия и хранилища ключей для брокера Kafka.

## <a name="update-configuration-to-use-ssl-and-restart-brokers"></a>Обновление конфигурации для использования SSL и перезапуск брокеров

Мы настроили для каждого брокера Kafka хранилище ключей и хранилище доверия, а также импортировали правильные сертификаты.  Далее измените связанные свойства конфигурации Kafka с помощью Ambari, а затем перезапустите брокеры Kafka.

1. Войдите на портал Azure и выберите свой кластер Azure HDInsight Apache Kafka.
1. Перейдите в пользовательский интерфейс Ambari, щелкнув **домашнюю страницу Ambari** в разделе **Панели мониторинга кластера**.
1. В разделе **Kafka Broker** (Брокер Kafka) задайте для свойства **listeners** значение `PLAINTEXT://localhost:9092,SSL://localhost:9093`.
1. В разделе **Advanced kafka-broker** (Расширенный брокер Kafka) задайте для свойства **security.inter.broker.protocol** значение `SSL`.

    ![Изменение свойств конфигурации SSL для Kafka в Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. В разделе **Custom kafka-broker** (Пользовательский брокер Kafka) задайте для свойства **ssl.client.auth** значение `required`. Это действие требуется только, если вы настраиваете проверку подлинности, а также шифрование.

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

1. Экспортируйте пароль клиента. Замените значение `<client_password>` фактическим паролем администратора на клиентском компьютере Kafka.

    ```bash
    $export CLIPASS=<client_password>
    $cd ssl
    ```

1. Создайте хранилище ключей Java и получите подписанный сертификат для брокера. Затем скопируйте сертификат на виртуальную машину, где запущен центр сертификации.

    ```bash
    $keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass $CLIPASS -keypass $CLIPASS -dname "CN=mylaptop1" -alias my-local-pc1 -storetype pkcs12

    $keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -alias my-local-pc1 -storepass $CLIPASS -keypass $CLIPASS

    $scp client-cert-sign-request3 sshuser@wn0-umakaf:~/tmp1/client-cert-sign-request
    ```

1. Переключитесь на компьютер ЦС (wn0) для подписания этого сертификата клиента.

    ```bash
    $cd ssl
    $openssl x509 -req -CA ca-cert -CAkey ca-key -in /tmp1/client-cert-sign-request -out /tmp1/client-cert-signed -days 365 -CAcreateserial -passin pass:<server_password>
    ```

1. Войдите на клиентский компьютер (hn1) и перейдите к папке `~/ssl`. Скопируйте подписанный сертификат на клиентский компьютер.

    ```bash
    #copy signed cert to client machine
    $scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/tmp1/client-cert-signed

    #copy signed cert to client machine
    $scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert

    #Import CA cert to trust store 
    $keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

    #Import CA cert to key store
    $keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

    #Import signed client (cert client-cert-signed1) to keystore
    $keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -alias my-local-pc1 -storepass $CLIPASS -keypass $CLIPASS -noprompt
    ```

1. Просмотрите файл `client-ssl-auth.properties` с помощью команды `$cat client-ssl-auth.properties`. В нем должны присутствовать следующие строки.

    ```bash
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=<client_password>
    ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
    ssl.keystore.password=<client_password>
    ssl.key.password=<client_password>
    ```

## <a name="client-setup-without-authentication"></a>Установка клиента (без проверки подлинности)

1. Экспортируйте пароль клиента. Замените значение `<client_password>` фактическим паролем администратора на клиентском компьютере Kafka.

    ```bash
    $export CLIPASS=<client_password>
    $cd ssl
    ```

1. Войдите на клиентский компьютер (hn1) и перейдите к папке `~/ssl`. Скопируйте подписанный сертификат на клиентский компьютер.

    ```bash
    #copy signed cert to client machine
    $scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert .

    #NOW IMPORT CA cert to trust store
    $keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

    #Import CA cert to key store
    $keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt
    ```

1. Просмотрите файл `client-ssl-auth.properties` с помощью команды `$cat client-ssl-auth.properties`. В нем должны присутствовать следующие строки.

    ```bash
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=<client_password>
    ```

## <a name="next-steps"></a>Дополнительная информация

* [Что такое Apache Kafka в HDInsight?](/../azure/hdinsight/kafka/apache-kafka-introduction)