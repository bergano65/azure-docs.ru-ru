---
title: Ошибка интерпретатора Apache Hive Zeppelin — Azure HDInsight
description: Интерпретатор JDBC Zeppelin Hive Apache указывает на неправильный URL-адрес в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 8e66db96f895d0d29d6c5018478e98c1f39e62ad
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288704"
---
# <a name="scenario-apache-hive-zeppelin-interpreter-gives-a-zookeeper-error-in-azure-hdinsight"></a>Сценарий: Apache Hive интерпретатор Zeppelin дает ошибку Zookeeper в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при использовании интерактивных компонентов запросов в кластерах Azure HDInsight.

## <a name="issue"></a>Проблема

В кластере Apache Hive LLAP интерпретатор Zeppelin выдает следующее сообщение об ошибке при попытке выполнить запрос:

```
java.sql.SQLException: org.apache.hive.jdbc.ZooKeeperHiveClientException: Unable to read HiveServer2 configs from ZooKeeper
```

## <a name="cause"></a>Причина

Интерпретатор JDBC Zeppelin Hive указывает на неверный URL-адрес.

## <a name="resolution"></a>Решение

1. Перейдите к сводке по компоненту Hive и скопируйте в буфер обмена URL-адрес Hive JDBC.

1. Перейдите на страницу `https://clustername.azurehdinsight.net/zeppelin/#/interpreter`.

1. Измените параметры JDBC: измените значение Hive. URL на URL-адрес JDBC для Hive, скопированный на шаге 1.

1. Сохраните, а затем повторите запрос

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]