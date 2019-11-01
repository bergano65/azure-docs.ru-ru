---
title: Ошибка Иллегаларгументексцептион для Apache Spark — Azure HDInsight
description: Иллегаларгументексцептион для действия Apache Spark в Azure HDInsight для фабрики данных Azure
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: f922df5d5d7bbd6d90a2b7e208a346b773a3dc2f
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241822"
---
# <a name="scenario-illegalargumentexception-for-apache-spark-activity-in-azure-hdinsight"></a>Сценарий: Иллегаларгументексцептион для действия Apache Spark в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при использовании Apache Spark компонентов в кластерах Azure HDInsight.

## <a name="issue"></a>Проблема

При попытке выполнить действие Spark в конвейере фабрики данных Azure появляется следующее исключение:

```error
Exception in thread "main" java.lang.IllegalArgumentException:
Wrong FS: wasbs://additional@xxx.blob.core.windows.net/spark-examples_2.11-2.1.0.jar, expected: wasbs://wasbsrepro-2017-11-07t00-59-42-722z@xxx.blob.core.windows.net
```

## <a name="cause"></a>Причина:

Задание Spark завершится ошибкой, если JAR-файл приложения не размещается в хранилище по умолчанию (первичном) кластера Spark.

Это известная проблема с инфраструктурой открытого кода Spark, проданной в этой ошибке: [Задание Spark завершается ошибкой, если в FS. дефаултфс и JAR-файл хранятся разные URL](https://issues.apache.org/jira/browse/SPARK-22587).

Эта проблема решена в Spark 2.3.0.

## <a name="resolution"></a>Разрешение

Убедитесь, что JAR-файл приложения хранится в хранилище по умолчанию (первичном) для кластера HDInsight. В случае фабрики данных Azure убедитесь, что связанная служба ADF указывает на контейнер HDInsight по умолчанию, а не на дополнительный контейнер.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключайтесь с [@AzureSupport](https://twitter.com/azuresupport) — официальная учетная запись Microsoft Azure для улучшения качества обслуживания клиентов путем подключения сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Дополнительные сведения см. [в](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)этой службе. Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
