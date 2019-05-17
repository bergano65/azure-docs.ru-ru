---
title: Использование Interactive Query в Azure HDInsight
description: Сведения об использовании Interactive Query (Hive LLAP) в HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/07/2019
ms.openlocfilehash: db36bbf6e20f9fab2f9a99f59be7a088e8f208e1
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65800909"
---
# <a name="use-interactive-query-with-hdinsight"></a>Использование Interactive Query в HDInsight
Interactive Query (также называется Apache Hive LLAP или [Low Latency Analytical Processing](https://cwiki.apache.org/confluence/display/Hive/LLAP)) — это [тип кластера](../hdinsight-hadoop-provision-linux-clusters.md#cluster-types) Azure HDInsight. Interactive Query поддерживает кэширование в памяти, благодаря чему запросы Apache Hive становятся более быстрыми и интерактивными.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)] 

Кластер Interactive Query отличается от кластера Apache Hadoop. Он содержит только службу Hive. 

> [!NOTE]  
> Получить доступ к службе Hive в кластере Interactive Query можно только с помощью представления Apache Ambari Hive, Beeline и драйвера Microsoft Hive ODBC. Служба недоступна при использовании консоли Hive, Templeton, классического Azure CLI или Azure PowerShell. 

## <a name="create-an-interactive-query-cluster"></a>Создание кластера Interactive Query
Дополнительные сведения о создании кластера HDInsight см. в статье [Установка кластеров в HDInsight с использованием Hadoop, Spark, Kafka и других технологий](../hdinsight-hadoop-provision-linux-clusters.md). Выберите тип кластера Interactive Query.

## <a name="execute-apache-hive-queries-from-interactive-query"></a>Выполнение запросов Apache Hive из Interactive Query
Есть несколько способов выполнять запросы Hive:

* Использование Microsoft Power BI

    Ознакомьтесь со статьей о [визуализации данных интерактивного запроса Apache Hive с помощью Power BI в Azure HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md) и о [визуализации больших данных с помощью Power BI в Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).

* Использование Visual Studio

    Ознакомьтесь с разделом [Выполнение интерактивных запросов Hive](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries).

* С помощью Visual Studio Code

    См. в разделе [использование Visual Studio Code для Apache Hive, LLAP и pySpark](../hdinsight-for-vscode.md).
* Запуск Apache Hive с помощью представления Hive Apache Ambari.
  
    Ознакомьтесь со статьей [Использование представления Hive Apache Ambari с Apache Hadoop в HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md).

* Запуск Apache Hive с помощью Beeline.
  
    Ознакомьтесь со статьей [Использование клиента Apache Beeline с Apache Hive](../hadoop/apache-hadoop-use-hive-beeline.md).
  
    Вы можете использовать Beeline на головном узле или пустом граничном узле. Рекомендуем использовать Beeline на пустом граничном узле. Дополнительные сведения о создании кластера HDInsight с пустым граничным узлом см. в статье [Использование пустых граничных узлов в кластерах Hadoop в HDInsight](../hdinsight-apps-use-edge-node.md).
* Запуск Apache Hive с помощью Hive ODBC.
  
    Ознакомьтесь со статьей [Подключение Excel к Apache Hadoop с помощью драйвера Microsoft Hive ODBC в Azure HDInsight](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

Чтобы найти строку подключения Java Database Connectivity (JDBC):

1. Войдите Apache Ambari, используя следующий URL-адрес: `https://<cluster name>.AzureHDInsight.net`.
2. В меню слева выберите **Hive**.
3. Чтобы скопировать URL-адрес, щелкните значок буфера обмена:
   
   ![JDBC для кластеров HDInsight Hadoop типа Interactive Query на LLAP](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте, как [создавать кластеры Interactive Query в HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Узнайте, как [визуализировать большие данные с помощью Power BI в Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
* Узнайте, как [выполнения запросов Apache Hive в Azure HDInsight с помощью Apache Zeppelin](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* Сведения о [запуске запросов Apache Hive с помощью средств Data Lake для Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries).
* Узнайте, как [использовать средствами HDInsight для Visual Studio Code](../hdinsight-for-vscode.md).
* Сведения об [использовании представления Apache Hive с Apache Hadoop в HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md)
* Сведения об [использовании Beeline для отправки запросов Apache Hive в HDInsight](../hadoop/apache-hadoop-use-hive-beeline.md).
* Сведения о [подключении Excel к Apache Hadoop с помощью драйвера Microsoft Hive ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

