---
title: Использование Apache Phoenix и SQLLine c HBase в Azure HDInsight
description: Из этой статьи вы узнаете, как использовать Apache Phoenix в HDInsight. Также здесь объясняется, как установить и настроить SQLLine на компьютере для подключения к кластеру HBase в HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/03/2018
ms.author: hrasheed
ms.openlocfilehash: 0bef586540635ee1bada3475f6316c84dea4308c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64722682"
---
# <a name="use-apache-phoenix-with-linux-based-apache-hbase-clusters-in-hdinsight"></a>Использование Apache Phoenix с кластерами Apache HBase под управлением Linux в HDInsight
Из этой статьи вы узнаете, как использовать [Apache Phoenix](https://phoenix.apache.org/) в Azure HDInsight, а также как использовать SQLLine. Дополнительные сведения о Phoenix см. в статье [Phoenix за 15 минут или меньше](https://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html). Сведения о грамматике Phoenix см. в статье [Грамматика Apache Phoenix](https://phoenix.apache.org/language/index.html).

> [!NOTE]  
> Сведения о версии Phoenix в HDInsight см. в статье [Каковы новые возможности версий кластера Apache Hadoop в HDInsight](../hdinsight-component-versioning.md).
>
>

## <a name="use-sqlline"></a>Использование SQLLine
[SQLLine](http://sqlline.sourceforge.net/) — это программа командной строки для выполнения SQL.

### <a name="prerequisites"></a>Технические условия
Для использования SQLLine требуются следующие компоненты:

* **Кластер Apache HBase в HDInsight**. Чтобы создать его, см. сведения в статье [Начало работы с примером Apache HBase в HDInsight](./apache-hbase-tutorial-get-started-linux.md).

При подключении к кластеру HBase необходимо подключиться к одной из виртуальных машин [Apache ZooKeeper](https://zookeeper.apache.org/). Каждый кластер HDInsight содержит три виртуальных машины Zookeeper.

**Получение имени узла Zookeeper**

1. Откройте [Apache Ambari](https://ambari.apache.org/), перейдя по адресу **https://\<имя кластера\>.azurehdinsight.net**.
2. Чтобы войти в систему, введите имя пользователя (кластера) HTTP и пароль.
3. В меню слева выберите **ZooKeeper**. В списке отобразятся три экземпляра **ZooKeeper Server**.
4. Выберите один из экземпляров **ZooKeeper Server**. На панели **Сводка** найдите **имя узла**. Оно аналогично *zk1-jdolehb.3lnng4rcvp5uzokyktxs4a5dhd.bx.internal.cloudapp.net*.

**Использование SQLLine**

1. Подключитесь к кластеру с помощью SSH. Дополнительные сведения см. в статье [Использование SSH с Hadoop на основе Linux в HDInsight из Linux, Unix или OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. В SSH используйте следующие команды для запуска SQLLine:

        cd /usr/hdp/current/phoenix-client/bin
        ./sqlline.py <ZOOKEEPER SERVER FQDN>:2181:/hbase-unsecure
3. Чтобы создать таблицу HBase и вставить данные, выполните следующие команды:

        CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));

        !tables

        UPSERT INTO Company VALUES(1, 'Microsoft');

        SELECT * FROM Company;

        !quit

Дополнительные сведения см. в [руководстве по SQLLine](http://sqlline.sourceforge.net/#manual) и статье о [грамматике Apache Phoenix](https://phoenix.apache.org/language/index.html).

## <a name="next-steps"></a>Дальнейшие действия
В этой статье вы изучили использование Apache Phoenix в HDInsight. Дополнительные сведения см. в статьях ниже:

* [Что такое HBase в HDInsight: база данных NoSQL, которая предоставляет возможности, схожие BigTable, для Hadoop][hdinsight-hbase-overview].
  Apache HBase — это разработанная Apache база данных NoSQL с открытым исходным кодом, созданная на основе Apache Hadoop. HBase обеспечивает произвольный доступ и строгую согласованность для больших объемов неструктурированных и слабоструктурированных данных.
* [Подготовка кластеров Apache HBase в виртуальной сети Azure][hdinsight-hbase-provision-vnet].
  Благодаря интеграции виртуальной сети кластеры Apache HBase могут быть развернуты в той же виртуальной сети, что и приложения. Это позволяет приложениям обмениваться данными с HBase напрямую.
* [Настройка репликации кластера HBase в виртуальных сетях Azure](apache-hbase-replication.md). Узнайте, как настроить репликацию Apache HBase между двумя центрами обработки данных Azure.


[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-hbase-provision-vnet]:apache-hbase-provision-vnet.md
[hdinsight-hbase-overview]:apache-hbase-overview.md


