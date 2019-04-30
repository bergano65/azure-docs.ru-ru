---
title: Использование Interactive Query в Azure HDInsight
description: Сведения об использовании Interactive Query (Hive LLAP) в HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.openlocfilehash: c7cee3dfd3b091d75f4dadcaa62513fddf0c0e68
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126602"
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
 
* Использование Apache Zeppelin

    См. в разделе [выполнения запросов Apache Hive в Azure HDInsight с помощью Apache Zeppelin](../hdinsight-connect-hive-zeppelin.md).

* Использование Visual Studio

    Ознакомьтесь с разделом [Выполнение интерактивных запросов Hive](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries).

* С помощью Visual Studio Code

    Ознакомьтесь со статьей [Использование средств Azure HDInsight для Visual Studio Code](../hdinsight-for-vscode.md).
* Запуск Apache Hive с помощью представления Hive Apache Ambari.
  
    Ознакомьтесь со статьей [Использование представления Hive Apache Ambari с Apache Hadoop в HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md).

* Запуск Apache Hive с помощью Beeline.
  
    Ознакомьтесь со статьей [Использование клиента Apache Beeline с Apache Hive](../hadoop/apache-hadoop-use-hive-beeline.md).
  
    Вы можете использовать Beeline на головном узле или пустом граничном узле. Рекомендуем использовать Beeline на пустом граничном узле. Дополнительные сведения о создании кластера HDInsight с пустым граничным узлом см. в статье [Использование пустых граничных узлов в кластерах Hadoop в HDInsight](../hdinsight-apps-use-edge-node.md).
* Запуск Apache Hive с помощью Hive ODBC.
  
    Ознакомьтесь со статьей [Подключение Excel к Apache Hadoop с помощью драйвера Microsoft Hive ODBC в Azure HDInsight](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

Чтобы найти строку подключения Java Database Connectivity (JDBC):

1. Войдите в Apache Ambari, используя следующий URL-адрес: https://\<имя_кластера\>.AzureHDInsight.net.
2. В меню слева выберите **Hive**.
3. Чтобы скопировать URL-адрес, щелкните значок буфера обмена:
   
   ![JDBC для кластеров HDInsight Hadoop типа Interactive Query на LLAP](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте, как [создавать кластеры Interactive Query в HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Узнайте, как [визуализировать большие данные с помощью Power BI в Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
* Узнайте, как [выполнения запросов Apache Hive в Azure HDInsight с помощью Apache Zeppelin](../hdinsight-connect-hive-zeppelin.md).
* Сведения о [запуске запросов Apache Hive с помощью средств Data Lake для Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries).
* Узнайте, как [использовать средствами HDInsight для Visual Studio Code](../hdinsight-for-vscode.md).
* Сведения об [использовании представления Apache Hive с Apache Hadoop в HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md)
* Сведения об [использовании Beeline для отправки запросов Apache Hive в HDInsight](../hadoop/apache-hadoop-use-hive-beeline.md).
* Сведения о [подключении Excel к Apache Hadoop с помощью драйвера Microsoft Hive ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

