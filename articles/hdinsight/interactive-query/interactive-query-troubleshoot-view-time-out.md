---
title: Время ожидания Apache Hive просмотра из результатов запроса — Azure HDInsight
description: Время ожидания Apache Hive просмотра при извлечении результатов запроса в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: c642760a5f6bfa7e59d42237e1583617e322ece3
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288829"
---
# <a name="scenario-apache-hive-view-times-out-when-fetching-a-query-result-in-azure-hdinsight"></a>Сценарий: время ожидания Apache Hive просмотра при извлечении результатов запроса в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при использовании интерактивных компонентов запросов в кластерах Azure HDInsight.

## <a name="issue"></a>Проблема

При выполнении определенных запросов из представления Apache Hive может возникнуть следующая ошибка:

```
result fetch timed out
java.util.concurrent.TimeoutException: deadline passed
```

## <a name="cause"></a>Причина

Значение времени ожидания по умолчанию для представления Hive не может быть подходящим для выполняемого запроса. Указанный период времени слишком короткий, чтобы в представлении Hive получить результат запроса.

## <a name="resolution"></a>Решение

Увеличьте время ожидания представления Hive в Apache Ambari, задав следующие свойства в `/etc/ambari-server/conf/ambari.properties` .

```
views.ambari.request.read.timeout.millis=300000
views.request.read.timeout.millis=300000
views.ambari.hive.<HIVE_VIEW_INSTANCE_NAME>.result.fetch.timeout=300000
```

Значение `HIVE_VIEW_INSTANCE_NAME` доступно в конце URL-адреса представления Hive.

## <a name="next-steps"></a>Дальнейшие действия

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]