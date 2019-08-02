---
title: Не удается загрузить большие наборы данных с помощью JDBC/ODBC и платформы программного обеспечения Apache Thrift в Azure HDInsight.
description: Не удается загрузить большие наборы данных с помощью JDBC/ODBC и платформы программного обеспечения Apache Thrift в Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 4283de9d9046cc63ee10731c05b70850648ff981
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68668889"
---
# <a name="scenario-unable-to-download-large-data-sets-using-jdbcodbc-and-apache-thrift-software-framework-in-azure-hdinsight"></a>Сценарий: Не удается загрузить большие наборы данных с помощью JDBC/ODBC и платформы программного обеспечения Apache Thrift в Azure HDInsight.

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при использовании Apache Spark компонентов в кластерах Azure HDInsight.

## <a name="issue"></a>Проблемы

При попытке загрузить большие наборы данных с помощью JDBC/ODBC и платформы программного обеспечения Apache Thrift в Azure HDInsight вы получаете следующее сообщение об ошибке:

```
org.apache.spark.SparkException: Kryo serialization failed:
Buffer overflow. Available: 0, required: 36518. To avoid this, increase spark.kryoserializer.buffer.max value.
```

## <a name="cause"></a>Причина:

Это исключение вызвано тем, что процесс сериализации пытается использовать больше буферного пространства, чем разрешено. В Spark 2.0.0 класс `org.apache.spark.serializer.KryoSerializer` используется для сериализации объектов при доступе к данным через платформу Apache Thrift Software Framework. Для данных, которые будут отправляться по сети или кэшированы в сериализованной форме, используется другой класс.

## <a name="resolution"></a>Разрешение

Увеличьте значение `Kryoserializer` буфера. Добавьте ключ с именем `spark.kryoserializer.buffer.max` и задайте для `2048` него значение в файле config `Custom spark2-thrift-sparkconf`spark2 в разделе.

## <a name="next-steps"></a>Следующие шаги

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключайтесь с помощью [@AzureSupport](https://twitter.com/azuresupport) официальной учетной записи Microsoft Azure для улучшения качества работы клиентов, подключив сообщество Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Дополнительные сведения см. [в](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)этой службе. Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
