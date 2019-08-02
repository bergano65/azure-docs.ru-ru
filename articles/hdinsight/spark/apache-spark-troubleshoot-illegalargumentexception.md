---
title: Иллегаларгументексцептион для действий Apache Spark в Azure HDInsight
description: Иллегаларгументексцептион для действия Apache Spark в Azure HDInsight для фабрики данных Azure
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 54e07a840aac1e754db68d9a14403750757f4bcf
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68620848"
---
# <a name="scenario-illegalargumentexception-for-apache-spark-activity-in-azure-hdinsight"></a>Сценарий: Иллегаларгументексцептион для действий Apache Spark в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при использовании Apache Spark компонентов в кластерах Azure HDInsight.

## <a name="issue"></a>Проблемы

При попытке выполнить действие Spark в конвейере фабрики данных Azure появляется следующее исключение:

```error
Exception in thread "main" java.lang.IllegalArgumentException:
Wrong FS: wasbs://additional@xxx.blob.core.windows.net/spark-examples_2.11-2.1.0.jar, expected: wasbs://wasbsrepro-2017-11-07t00-59-42-722z@xxx.blob.core.windows.net
```

## <a name="cause"></a>Причина:

Задание Spark завершится ошибкой, если JAR-файл приложения не размещается в хранилище по умолчанию (первичном) кластера Spark.

Это известная проблема с платформой с открытым кодом Spark, проданной в этой ошибке: [Задание Spark завершается ошибкой, если в FS. дефаултфс и JAR-файл хранятся разные URL](https://issues.apache.org/jira/browse/SPARK-22587).

Эта проблема решена в Spark 2.3.0.

## <a name="resolution"></a>Разрешение

Убедитесь, что JAR-файл приложения хранится в хранилище по умолчанию (первичном) для кластера HDInsight. В случае фабрики данных Azure убедитесь, что связанная служба ADF указывает на контейнер HDInsight по умолчанию, а не на дополнительный контейнер.

## <a name="next-steps"></a>Следующие шаги

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключайтесь с помощью [@AzureSupport](https://twitter.com/azuresupport) официальной учетной записи Microsoft Azure для улучшения качества работы клиентов, подключив сообщество Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Дополнительные сведения см. [в](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)этой службе. Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
