---
title: Ошибка Иллегаларгументексцептион для Apache Spark — Azure HDInsight
description: Иллегаларгументексцептион для действия Apache Spark в Azure HDInsight для фабрики данных Azure
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 61190a932389b429040d6b643db2dd2732b2c41d
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287911"
---
# <a name="scenario-illegalargumentexception-for-apache-spark-activity-in-azure-hdinsight"></a>Сценарий: Иллегаларгументексцептион для действия Apache Spark в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы исправления проблем, возникающих при использовании компонентов Apache Spark в кластерах Azure HDInsight.

## <a name="issue"></a>Проблема

При попытке выполнить действие Spark в конвейере фабрики данных Azure появляется следующее исключение:

```error
Exception in thread "main" java.lang.IllegalArgumentException:
Wrong FS: wasbs://additional@xxx.blob.core.windows.net/spark-examples_2.11-2.1.0.jar, expected: wasbs://wasbsrepro-2017-11-07t00-59-42-722z@xxx.blob.core.windows.net
```

## <a name="cause"></a>Причина

Задание Spark завершится ошибкой, если JAR-файл приложения не размещается в хранилище по умолчанию (первичном) кластера Spark.

Это известная проблема с инфраструктурой открытого кода Spark, проданной в этой ошибке: [Задание Spark завершается ошибкой, если в FS. дефаултфс и JAR-файл хранятся разные URL](https://issues.apache.org/jira/browse/SPARK-22587).

Эта проблема решена в Spark 2.3.0.

## <a name="resolution"></a>Решение

Убедитесь, что JAR-файл приложения хранится в хранилище по умолчанию (первичном) для кластера HDInsight. В случае фабрики данных Azure убедитесь, что связанная служба ADF указывает на контейнер HDInsight по умолчанию, а не на дополнительный контейнер.

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]