---
title: Краткое руководство. Выполнение запросов Apache Hive в Azure HDInsight — Apache Zeppelin
description: Сведения о выполнении запросов Apache Hive с помощью Apache Zeppelin.
keywords: hdinsight hadoop, hive, interactive query, LLAP
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 05/06/2019
ms.author: hrasheed
ms.openlocfilehash: f4b8495646e83005dc48e8a729a0e5987b832721
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65801055"
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

## <a name="clean-up-resources"></a>Очистка ресурсов

После завершения работы с этим кратким руководством кластер можно удалить. В случае с HDInsight ваши данные хранятся в службе хранилища Azure, что позволяет безопасно удалить неиспользуемый кластер. Плата за кластеры HDInsight взимается, даже когда они не используются. Поскольку стоимость кластера во много раз превышает стоимость хранилища, экономически целесообразно удалять неиспользуемые кластеры.

Инструкции по удалению кластера см. в статье [Delete an HDInsight cluster using your browser, PowerShell, or the Azure CLI](../hdinsight-delete-cluster.md) (Удаление кластера HDInsight с помощью браузера, PowerShell или Azure CLI).

## <a name="next-steps"></a>Дополнительная информация

Из этого краткого руководства вы узнали, как использовать Apache Zeppelin для выполнения запросов Apache Hive в Azure HDInsight. Дополнительные сведения о запросах Hive см. следующей статье, в которой показано, как выполнять запросы с помощью Visual Studio.

> [!div class="nextstepaction"]
> [Подключение к Azure HDInsight и выполнение запросов Apache Hive с помощью средств Data Lake для Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)

## <a name="see-also"></a>См. также

* [Визуализация данных Apache Hive с помощью Microsoft Power BI в Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Визуализация данных Apache Hive из кластера Interactive Query с помощью Power BI в Azure HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md)
* [Подключение Excel к Hadoop в Azure HDInsight с помощью Microsoft Hive ODBC Driver](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
* [Подключение Excel к Apache Hadoop с помощью Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md)
* [Использование средств Azure HDInsight для Visual Studio Code](../hdinsight-for-vscode.md).
* [Отправка данных в HDInsight](../hdinsight-upload-data.md)
