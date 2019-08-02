---
title: Отправка запросов в Apache Hive с помощью драйвера JDBC — Azure HDInsight
description: Используйте драйвер JDBC из приложения Java для отправки запросов Apache Hive в Hadoop в службе HDInsight. Подключение можно осуществлять программными средствами и из клиента SQL SQuirrel.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: hrasheed
ms.openlocfilehash: 36233dc986752ded409389a0a8e267c92a40b5a5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562588"
---
# <a name="query-apache-hive-through-the-jdbc-driver-in-hdinsight"></a>Отправка запросов в Apache Hive с помощью драйвера JDBC в HDInsight

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Узнайте, как использовать драйвер JDBC из приложения Java для отправки запросов Apache Hive в Apache Hadoop в Azure HDInsight. Сведения в этом документе показывают, как подключаться программным способом и из клиента SQuirreL SQL.

Дополнительные сведения об интерфейсе JDBC Hive см. в статье [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

## <a name="prerequisites"></a>предварительные требования

* Кластер HDInsight Hadoop. Дополнительные сведения о создании кластера см. в статье [Приступая к работе с Hadoop в HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* [Пакет Java Developer Kit (JDK) версии 11](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html) или более поздней.
* [SQuirreL SQL](http://squirrel-sql.sourceforge.net/). SQuirreL представляет собой клиентское приложение JDBC.

## <a name="jdbc-connection-string"></a>Строка подключения JDBC

Подключения JDBC к кластеру HDInsight в Azure осуществляются через порт 443, а трафик защищен с помощью SSL. Открытый шлюз, за которым находятся кластеры, перенаправляет трафик к порту, который фактически прослушивается HiveServer2. В следующей строке подключения показан формат для HDInsight:

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2

Замените `CLUSTERNAME` на имя вашего кластера HDInsight.

## <a name="authentication"></a>Проверка подлинности

При установке подключения необходимо указать имя администратора кластера HDInsight и пароль для проверки подлинности в шлюзе кластера. При подключении клиентов JDBC, например SQuirreL SQL, необходимо ввести имя и пароль администратора в параметрах клиента.

При подключении из приложения Java необходимо использовать имя пользователя и пароль. Например, приведенный ниже код Java открывает новое подключение с помощью строки подключения, имени администратора и пароля:

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>Подключение с использованием клиента SQuirreL SQL

SQuirreL SQL — клиент JDBC, который можно использовать для удаленного выполнения запросов Hive с кластером HDInsight. В следующих шагах предполагается, что SQuirreL SQL уже установлен.

1. Создайте каталог, в котором будут храниться определенные файлы, копируемые из кластера.

2. В следующем сценарии замените `sshuser` именем учетной записи пользователя SSH для кластера.  Замените `CLUSTERNAME` именем кластера HDInsight.  В командной строке измените рабочий каталог на созданный на предыдущем шаге, а затем введите следующую команду, чтобы скопировать файлы из кластера HDInsight:

    ```cmd
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/{hadoop-auth.jar,hadoop-common.jar,lib/log4j-*.jar,lib/slf4j-*.jar,lib/curator-*.jar} .

    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/{commons-codec*.jar,commons-logging-*.jar,hive-*-*.jar,httpclient-*.jar,httpcore-*.jar,libfb*.jar,libthrift-*.jar} .
    ```

3. Запустите приложение SQuirreL SQL. В левой части окна выберите **Драйверы**.

    ![Вкладка "Драйверы" в левой части окна](./media/apache-hadoop-connect-hive-jdbc-driver/squirreldrivers.png)

4. Среди значков в верхней части диалогового окна **Drivers** (Драйверы) щелкните значок **+** для создания драйвера.

    ![Значки драйверов](./media/apache-hadoop-connect-hive-jdbc-driver/driversicons.png)

5. В диалоговом окне Add Driver (Добавление драйвера) укажите следующие сведения.

    * **Имя.** Hive
    * **Пример URL-адреса:** `jdbc:hive2://localhost:443/default;transportMode=http;ssl=true;httpPath=/hive2`
    * **Путь к дополнительным классам.** Используйте кнопку **Добавить** , чтобы добавить все скачанные ранее JAR-файлы.
    * **Имя класса:** org.apache.hive.jdbc.HiveDriver.

   ![диалоговое окно "Добавить драйвер"](./media/apache-hadoop-connect-hive-jdbc-driver/adddriver.png)

   Нажмите кнопку **ОК** , чтобы сохранить эти параметры.

6. В левой части окна SQuirreL SQL выберите **Псевдонимы**. Затем щелкните значок, чтобы создать псевдоним подключения. **+**

    ![добавление нового псевдонима](./media/apache-hadoop-connect-hive-jdbc-driver/aliases.png)

7. В диалоговом окне **Добавить псевдоним** укажите следующие значения.

    * **Имя.** Hive в HDInsight

    * **Драйвер**. С помощью раскрывающегося списка выберите драйвер **Hive** .

    * **URL-адрес:** `jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2`

        Замените **CLUSTERNAME** именем кластера HDInsight.

    * **Имя пользователя.** Имя пользователя для учетной записи входа кластера HDInsight. Значение по умолчанию — `admin`.

    * **Пароль**. Пароль для учетной записи входа кластера.

   ![диалоговое окно "Добавить псевдоним"](./media/apache-hadoop-connect-hive-jdbc-driver/addalias.png)

    > [!IMPORTANT] 
    > Нажмите кнопку **Проверить**, чтобы убедиться, что подключение работает. При появлении диалогового окна **Connect to: Hive on HDInsight** (Подключение: Hive в HDInsight) выберите **Подключиться**, чтобы выполнить проверку. Если проверка пройдет успешно, вы увидите диалоговое окно **Connection successful** (Подключение выполнено успешно). При возникновении ошибки см. раздел [Устранение неполадок](#troubleshooting).

    Чтобы сохранить псевдоним подключения, в нижней части диалогового окна **Add Alias** (Добавить псевдоним) нажмите кнопку **ОК**.

8. В раскрывающемся списке **Подключиться к** в верхней части окна SQuirreL SQL выберите **Hive on HDInsight** (Hive в HDInsight). При появлении запроса выберите **Подключиться**.

    ![диалоговое окно подключения](./media/apache-hadoop-connect-hive-jdbc-driver/connect.png)

9. После подключения введите следующий запрос в диалоговом окне SQL-запрос, а затем щелкните значок **запуска** (работающего человека). В области результатов должны появиться результаты запроса.

    ```hql
    select * from hivesampletable limit 10;
    ```

    ![диалоговое окно запроса SQL с результатами запроса](./media/apache-hadoop-connect-hive-jdbc-driver/sqlquery.png)

## <a name="connect-from-an-example-java-application"></a>Подключение из примера приложения Java

Пример использования клиента Java для запросов Hive доступен в [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc). Следуйте инструкциям в репозитории, чтобы построить и запустить образец.

## <a name="troubleshooting"></a>Устранение неполадок

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>Непредвиденная ошибка при попытке открыть подключение к SQL

**Проблема.** При подключении к кластеру HDInsight версии 3.3 или более поздней может появиться сообщение о возникновении непредвиденной ошибки. Трассировка стека для этой ошибки начинается со следующих строк:

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**Причина.** Эту ошибку вызывают прежние версии файла commons-codec.jar в составе SQuirreL.

**Решение**. Чтобы устранить эту ошибку, выполните приведенные далее действия.

1. Закройте SQuirreL, а затем перейдите в каталог, где установлен SQuirreL. В каталоге SquirreL в каталоге `lib` замените существующий файл commons-codec.ja файлом, скачанным из кластера HDInsight.

2. Перезапустите SQuirreL. При последующих подключениях к Hive в HDInsight ошибка больше не должна возникать.

## <a name="next-steps"></a>Следующие шаги

Теперь, когда вы узнали, как использовать JDBC для работы с Hive, воспользуйтесь следующими ссылками, чтобы изучить другие способы работы с Azure HDInsight.

* [Визуализация данных Apache Hive с помощью Microsoft Power BI в Azure HDInsight](apache-hadoop-connect-hive-power-bi.md)
* [Visualize Interactive Query Hive data with Microsoft Power BI using DirectQuery in Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) (Визуализация данных Hive из кластера Interactive Query с помощью Microsoft Power BI и DirectQuery в Azure HDInsight).
* [Выполнение запросов Apache Hive в Azure HDInsight с помощью Apache Zeppelin](../interactive-query/hdinsight-connect-hive-zeppelin.md)
* [Подключение Excel к Hadoop в Azure HDInsight с помощью Microsoft Hive ODBC Driver](apache-hadoop-connect-excel-hive-odbc-driver.md)
* [Подключение Excel к Apache Hadoop с помощью Power Query](apache-hadoop-connect-excel-power-query.md)
* [Подключение к Azure HDInsight и выполнение запросов Apache Hive с помощью Средств Data Lake для Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)
* [Использование средств Azure HDInsight для Visual Studio Code](../hdinsight-for-vscode.md).
* [Отправка данных в HDInsight](../hdinsight-upload-data.md)
* [Использование Hive и HiveQL с Hadoop в HDInsight для анализа примера файла Apache log4j](hdinsight-use-hive.md)
* [Использование Pig с Hadoop в HDInsight](hdinsight-use-pig.md)
* [Использование заданий MapReduce с HDInsight](hdinsight-use-mapreduce.md)
