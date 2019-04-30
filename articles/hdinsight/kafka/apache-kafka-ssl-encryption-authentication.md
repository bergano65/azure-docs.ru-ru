---
title: Настройка шифрования и проверки подлинности SSL для Apache Kafka в Azure HDInsight
description: Настройка шифрования SSL для обмена данными между клиентами Kafka и брокерами Kafka, а также между брокерами Kafka. Настройка проверки подлинности клиентов по протоколу SSL.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
origin.date: 01/15/2019
ms.date: 04/15/2019
ms.author: v-yiso
ms.openlocfilehash: 9d8d5e57d0dd7d7022e65a061360c8450848fb4b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62114963"
---
# <a name="setup-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Настройка шифрования и проверки подлинности SSL для Apache Kafka в Azure HDInsight

В этой статье описывается, как установить шифрование SSL при передаче данных от клиентов к брокерам Apache Kafka. Здесь также приведены действия, необходимые для настройки проверки подлинности клиентов (иногда называется двусторонней проверкой подлинности SSL).

## <a name="server-setup"></a>Настройка сервера

Сначала необходимо создать хранилище ключей и хранилище доверенных сертификатов для каждого брокера Kafka. После их создания импортируйте Центр сертификации (ЦС) и сертификаты брокера в эти хранилища.

> [!Note] 
> В этом руководстве будут использоваться самозаверяющие сертификаты, но наиболее безопасными являются сертификаты, выданные доверенными центрами сертификации.

Выполните следующие действия для завершения установки сервера:

1. Создайте папку с именем ssl и экспортируйте пароль для сервера в виде переменной среды. Для оставшейся части этого руководства замените значение `<server_password>` фактическим паролем администратора для сервера.
1. Далее создайте хранилище ключей Java (kafka.server.keystore.jks) и сертификат ЦС.
1. Затем создайте запрос на подпись сертификата, созданного на предыдущем шаге, этим центром сертификации.
1. Теперь отправьте запрос на подпись этого сертификата в ЦС. Так как используется самозаверяющий сертификат, мы подписываем сертификат в нашем ЦС с помощью команды `openssl`.
1. Создайте хранилище доверия и импортируйте сертификат ЦС.
1. Импортируйте общедоступный сертификат ЦС в хранилище ключей.
1. Импортируйте подписанный сертификат в хранилище ключей.

Команды для выполнения этих действий показаны в следующем фрагменте кода.

```bash
export SRVPASS=<server_password>
mkdir ssl
cd ssl

# Create a java keystore (kafka.server.keystore.jks) and a CA certificate.

keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass $SRVPASS -keypass $SRVPASS -dname "CN=wn0-umakaf.xvbseke35rbuddm4fyvhm2vz2h.cx.internal.cloudapp.net" -storetype pkcs12

# Create a signing request to get the certificate created in the previous step signed by the CA.

keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass $SRVPASS -keypass $SRVPASS

# Send the signing request to the CA and get this certificate signed.

openssl x509 -req -CA ca-cert -CAkey ca-key -in cert-file -out cert-signed -days 365 -CAcreateserial -passin pass:$SRVPASS

# Create a trust store and import the certificate of the CA.

keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt

# Import the public CA certificate into the keystore.

keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt

# Import the signed certificate into the keystore.

keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt

# The output should say "Certificate reply was added to keystore"
```

Импорт подписанного сертификата в хранилище ключей — последний шаг, необходимый для настройки хранилища доверия и хранилища ключей для брокера Kafka.

## <a name="update-kafka-configuration-to-use-ssl-and-restart-brokers"></a>Обновите конфигурацию Kafka для использования SSL и перезапуска брокеров

Теперь вы настроили каждый брокер Kafka с хранилищем ключей и хранилищем доверенных сертификатов, а также импортировали правильные сертификаты.  Далее измените связанные свойства конфигурации Kafka с помощью Ambari, а затем перезапустите брокеры Kafka. 

Выполните следующие действия для завершения конфигурации:

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

Выполните следующие действия для установки клиента.

1. Войдите на клиентский компьютер (hn1).
1. Экспортируйте пароль клиента. Замените значение `<client_password>` фактическим паролем администратора на клиентском компьютере Kafka.
1. Создайте хранилище ключей Java и получите подписанный сертификат для брокера. Затем скопируйте сертификат на виртуальную машину, где запущен центр сертификации.
1. Переключитесь на компьютер ЦС (wn0) для подписания этого сертификата клиента.
1. Войдите на клиентский компьютер (hn1) и перейдите к папке `~/ssl`. Скопируйте подписанный сертификат на клиентский компьютер.

```bash
export CLIPASS=<client_password>
cd ssl

# Create a java keystore and get a signed certificate for the broker. Then copy the certificate to the VM where the CA is running.

keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass $CLIPASS -keypass $CLIPASS -dname "CN=mylaptop1" -alias my-local-pc1 -storetype pkcs12

keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -alias my-local-pc1 -storepass $CLIPASS -keypass $CLIPASS

# Copy the cert to the vm where the CA is
scp client-cert-sign-request3 sshuser@wn0-umakaf:~/tmp1/client-cert-sign-request

# Switch to the CA machine (wn0) to sign the client certificate.
cd ssl
openssl x509 -req -CA ca-cert -CAkey ca-key -in /tmp1/client-cert-sign-request -out /tmp1/client-cert-signed -days 365 -CAcreateserial -passin pass:<server_password>

# Return to the client machine (hn1), navigate to ~/ssl folder and copy signed cert to client machine
scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/tmp1/client-cert-signed

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

Если вам не требуется проверка подлинности, выполните приведенные ниже действия для настройки только SSL-шифрования.

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