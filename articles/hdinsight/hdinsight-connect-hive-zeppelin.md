---
title: Выполнение запросов Apache Hive в Azure HDInsight с помощью Apache Zeppelin
description: Сведения о выполнении запросов Apache Hive с помощью Apache Zeppelin.
keywords: hdinsight hadoop, hive, interactive query, LLAP
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.topic: conceptual
ms.date: 11/05/2018
ms.author: hrasheed
ms.openlocfilehash: 417e9f8ae78889374983bf77900ee00fa7fc6338
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098766"
---
# <a name="use-apache-zeppelin-to-run-apache-hive-queries-in-azure-hdinsight"></a>Выполнение запросов Apache Hive в Azure HDInsight с помощью Apache Zeppelin 

Кластеры Interactive Query HDInsight включают записные книжки [Apache Zeppelin](https://zeppelin.apache.org/), которые можно использовать для выполнения интерактивных запросов Hive. Из этой статьи вы узнаете, как использовать Apache Zeppelin для выполнения запросов [Apache Hive](https://hive.apache.org/) в Azure HDInsight. 

## <a name="prerequisites"></a>Технические условия
Чтобы выполнить действия, указанные в этой статье, вам потребуется:

* **Кластер Interactive Query HDInsight**. Чтобы создать кластер HDInsight, обратитесь к разделу [Создание кластера](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).  Выберите тип кластера Interactive Query. 

## <a name="create-an-apache-zeppelin-note"></a>Создание заметки Apache Zeppelin

1. Перейдите на следующий URL-адрес:

        https://CLUSTERNAME.azurehdinsight.net/zeppelin
    Замените **CLUSTERNAME** именем кластера.

2. Введите имя пользователя и пароль Hadoop. На странице Zeppelin можно создать новую заметку или открыть существующие заметки. В разделе HiveSample находятся примеры запросов Hive.  

    ![Заметка Zeppelin для кластера Interactive Query HDInsight](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)
3. Щелкните **Создать новую заметку**.
4. Введите или выберите следующие значения:

    - Название заметки: введите название заметки.
    - Интерпретатор по умолчанию: выберите **JDBC**.

5. Щелкните **Создать заметку**.
6. Выполните следующий запрос Hive:

        %jdbc(hive)
        show tables

    ![Выполнение запроса заметки Zeppelin для кластера Interactive Query HDInsight](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    Инструкция **%jdbc(hive)** в первой строке означает, что записная книжка должна использовать интерпретатор JDBC Hive.

    Запрос должен вернуть одну таблицу Hive с именем *hivesampletable*.

    Ниже приведены два дополнительных запроса Hive, которые можно выполнить с таблицей hivesampletable. 

        %jdbc(hive)
        select * from hivesampletable limit 10

        %jdbc(hive)
        select ${group_name}, count(*) as total_count
        from hivesampletable
        group by ${group_name=market,market|deviceplatform|devicemake}
        limit ${total_count=10}

    По сравнению с обычным Hive, результаты запроса возвращаются намного быстрее.


## <a name="next-steps"></a>Дальнейшие действия
Из этой статьи вы узнали, как визуализировать данные HDInsight с помощью [Microsoft Power BI](https://powerbi.microsoft.com/).  Для получения дополнительных сведений ознакомьтесь со следующими статьями:

* [Визуализация данных Apache Hive с помощью Microsoft Power BI в Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Визуализация данных Apache Hive из кластера Interactive Query с помощью Power BI в Azure HDInsight](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)
* [Подключение Excel к Hadoop в Azure HDInsight с помощью Microsoft Hive ODBC Driver](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
* [Подключение Excel к Apache Hadoop с помощью Power Query](hadoop/apache-hadoop-connect-excel-power-query.md)
* [Подключение к Azure HDInsight и выполнение запросов Apache Hive с помощью Средств Data Lake для Visual Studio](hadoop/apache-hadoop-visual-studio-tools-get-started.md)
* [Использование средств Azure HDInsight для Visual Studio Code](hdinsight-for-vscode.md).
* [Отправка данных в HDInsight](./hdinsight-upload-data.md)
