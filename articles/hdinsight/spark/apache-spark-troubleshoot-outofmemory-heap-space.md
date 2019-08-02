---
title: Ошибка пространства кучи Java при попытке открыть сервер журнала Apache Spark в Azure HDInsight
description: Не удается запустить сервер Apache Livy с Java. lang. OutOfMemoryError в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/30/2019
ms.openlocfilehash: 5c2ae90bdca8512802c845a5ac58c3c4aeedd5b7
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667803"
---
# <a name="scenario-java-heap-space-error-when-trying-to-open-apache-spark-history-server-in-azure-hdinsight"></a>Сценарий: Ошибка пространства кучи Java при попытке открыть сервер журнала Apache Spark в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при использовании Apache Spark компонентов в кластерах Azure HDInsight.

## <a name="issue"></a>Проблемы

При открытии событий на сервере журнала Spark появляется следующее сообщение об ошибке:

```
scala.MatchError: java.lang.OutOfMemoryError: Java heap space (of class java.lang.OutOfMemoryError)
```

## <a name="cause"></a>Причина:

Эта проблема часто возникает из-за недостатка ресурсов при открытии больших файлов Spark-событий. Размер кучи Spark по умолчанию устанавливается равным 1 ГБ, но для больших файлов событий Spark может потребоваться больше.

Если вы хотите проверить размер загружаемых файлов, можно выполнить следующие команды:.

```bash
hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0274_1/
**576.5 M**  wasb:///hdp/spark2-events/application_1503957839788_0274_1

hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0264_1/
**2.1 G**  wasb:///hdp/spark2-events/application_1503957839788_0264_1
```

## <a name="resolution"></a>Разрешение

Можно увеличить объем памяти сервера журнала Spark, отредактировав `SPARK_DAEMON_MEMORY` свойство в конфигурации Spark и перезапуская все службы.

Это можно сделать в пользовательском интерфейсе браузера Ambari, выбрав раздел Spark2/config/Advanced Spark2-env.

![Расширенный раздел spark2-env](./media/apache-spark-ts-outofmemory-heap-space/image01.png)

Добавьте следующее свойство, чтобы изменить объем памяти сервера журнала Spark с 1 ГБ на 4G `SPARK_DAEMON_MEMORY=4g`:.

![Spark, свойство](./media/apache-spark-ts-outofmemory-heap-space/image02.png)

Не забудьте перезапустить все затронутые службы из Ambari.

## <a name="next-steps"></a>Следующие шаги

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключайтесь с помощью [@AzureSupport](https://twitter.com/azuresupport) официальной учетной записи Microsoft Azure для улучшения качества работы клиентов, подключив сообщество Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Дополнительные сведения см. [в](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)этой службе. Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
