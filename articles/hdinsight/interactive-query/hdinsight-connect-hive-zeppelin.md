---
title: Краткое руководство. Apache Hive в Azure HDInsight с помощью Apache Zeppelin
description: Из этого краткого руководства вы узнаете, как использовать Apache Zeppelin для выполнения запросов Apache Hive.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: hdinsightactive
ms.date: 12/03/2019
ms.openlocfilehash: 915aca0e95fce05f74477b526de047c829c7f512
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74890405"
---
# <a name="quickstart-execute-apache-hive-queries-in-azure-hdinsight-with-apache-zeppelin"></a>Краткое руководство. Выполнение запросов Apache Hive в Azure HDInsight с помощью Apache Zeppelin

Из этого краткого руководства вы узнаете, как использовать Apache Zeppelin для выполнения запросов [Apache Hive](https://hive.apache.org/) в Azure HDInsight. Кластеры Interactive Query HDInsight включают записные книжки [Apache Zeppelin](https://zeppelin.apache.org/), которые можно использовать для выполнения интерактивных запросов Hive.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Кластер Interactive Query HDInsight. Чтобы создать кластер HDInsight, обратитесь к разделу [Создание кластера Hadoop](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).  Выберите тип кластера **Interactive Query**.

## <a name="create-an-apache-zeppelin-note"></a>Создание заметки Apache Zeppelin

1. В URL-адресе `https://CLUSTERNAME.azurehdinsight.net/zeppelin` замените `CLUSTERNAME` именем своего кластера. В веб-браузере перейдите по этому URL-адресу.

2. Введите имя пользователя и пароль для входа в кластер. На странице Zeppelin можно создать новую заметку или открыть существующие заметки. В разделе **HiveSample** находятся примеры запросов Hive.  

    ![Заметка Zeppelin для кластера Interactive Query HDInsight](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)

3. Выберите **Create new note** (Создать заметку).

4. В диалоговом окне **создания заметки** введите или выберите следующие значения:

    - Note Name (Имя заметки). Введите имя заметки.
    - Default interpreter (Интерпретатор по умолчанию). Выберите **jdbc** из раскрывающегося списка.

5. Выберите **Create Note** (Создать заметку).

6. Введите следующий запрос Hive в разделе кода и нажмите клавиши **SHIFT+ВВОД**:

    ```hive
    %jdbc(hive)
    show tables
    ```

    ![Выполнение запроса заметки Zeppelin для кластера Interactive Query HDInsight](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    Инструкция **%jdbc(hive)** в первой строке означает, что записная книжка должна использовать интерпретатор JDBC Hive.

    Запрос должен вернуть одну таблицу Hive с именем **hivesampletable**.

    Ниже приведены два дополнительных запроса Hive, которые можно выполнить с таблицей **hivesampletable**.

    ```hive
    %jdbc(hive)
    select * from hivesampletable limit 10

    %jdbc(hive)
    select ${group_name}, count(*) as total_count
    from hivesampletable
    group by ${group_name=market,market|deviceplatform|devicemake}
    limit ${total_count=10}
    ```

    По сравнению с обычным Hive, результаты запроса возвращаются намного быстрее.

### <a name="additional-examples"></a>Дополнительные примеры

1. Создайте таблицу. Выполните приведенный ниже код в записной книжке Zeppelin.

    ```hql
    %jdbc(hive)
    CREATE EXTERNAL TABLE log4jLogs (
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED
    FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE;
    ```

1. Загрузите данные в новую таблицу. Выполните приведенный ниже код в записной книжке Zeppelin.

    ```hql
    %jdbc(hive)
    LOAD DATA
    INPATH 'wasbs:///example/data/sample.log'
    INTO TABLE log4jLogs;
    ```

1. Вставьте одну запись. Выполните приведенный ниже код в записной книжке Zeppelin.

    ```hql
    %jdbc(hive)
    INSERT INTO TABLE log4jLogs2
    VALUES ('A', 'B', 'C', 'D', 'E', 'F', 'G');
    ```

Дополнительные сведения о синтаксисе см. в [руководстве по языку Hive](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).

## <a name="clean-up-resources"></a>Очистка ресурсов

После завершения работы с этим кратким руководством кластер можно удалить. В случае с HDInsight ваши данные хранятся в службе хранилища Azure, что позволяет безопасно удалить неиспользуемый кластер. Плата за кластеры HDInsight взимается, даже когда они не используются. Так как затраты на кластер во много раз превышают затраты на хранилище, экономически целесообразно удалять неиспользуемые кластеры.

Инструкции по удалению кластера см. в статье [Delete an HDInsight cluster using your browser, PowerShell, or the Azure CLI](../hdinsight-delete-cluster.md) (Удаление кластера HDInsight с помощью браузера, PowerShell или Azure CLI).

## <a name="next-steps"></a>Дополнительная информация

Из этого краткого руководства вы узнали, как использовать Apache Zeppelin для выполнения запросов Apache Hive в Azure HDInsight. Дополнительные сведения о запросах Hive см. следующей статье, в которой показано, как выполнять запросы с помощью Visual Studio.

> [!div class="nextstepaction"]
> [Подключение к Azure HDInsight и выполнение запросов Apache Hive с помощью средств Data Lake для Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
