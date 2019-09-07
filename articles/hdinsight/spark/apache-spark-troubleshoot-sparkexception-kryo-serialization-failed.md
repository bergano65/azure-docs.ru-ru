---
title: Загрузка проблем с помощью JDBC/ODBC и Apache Thrift Framework — Azure HDInsight
description: Не удается загрузить большие наборы данных с помощью JDBC/ODBC и платформы программного обеспечения Apache Thrift в Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 97aaec9d42baa3e94be72a748e82345d159f4583
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70736239"
---
# <a name="scenario-unable-to-download-large-data-sets-using-jdbcodbc-and-apache-thrift-software-framework-in-azure-hdinsight"></a>Сценарий. Не удается загрузить большие наборы данных с помощью JDBC/ODBC и платформы программного обеспечения Apache Thrift в Azure HDInsight.

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
