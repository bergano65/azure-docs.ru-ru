---
title: Отладка файловых операций WASB в Azure HDInsight
description: Описывает шаги по устранению неполадок и возможные способы решения проблем при взаимодействии с кластерами Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/18/2020
ms.openlocfilehash: f1707c7f8d6324678c8bf5a470bbded1e58c719e
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77470723"
---
# <a name="debug-wasb-file-operations-in-azure-hdinsight"></a>Отладка файловых операций WASB в Azure HDInsight

Иногда может потребоваться узнать, какие операции запустил драйвер WASB в службе хранилища Azure. Для клиентской стороны драйвер WASB создает журналы для каждой операции файловой системы на уровне **отладки** . Драйвер WASB использует log4j для управления уровнем ведения журнала, а значение по умолчанию — **информационный** уровень. Журналы аналитики на стороне сервера службы хранилища Azure см. в статье [ведение журнала аналитики службы хранилища Azure](../../storage/common/storage-analytics-logging.md).

Созданный журнал будет выглядеть примерно так:

```log
18/05/13 04:15:55 DEBUG NativeAzureFileSystem: Moving wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/_temporary/0/_temporary/attempt_20180513041552_0000_m_000000_0/part-00000 to wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/part-00000
```

## <a name="turn-on-wasb-debug-log-for-file-operations"></a>Включить журнал отладки WASB для файловых операций

1. В веб-браузере перейдите к `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs`, где `CLUSTERNAME` — имя кластера Spark.

1. Перейдите к **расширенному spark2-log4j-Properties**.

    1. Измените `log4j.appender.console.Threshold=INFO` на `log4j.appender.console.Threshold=DEBUG`.

    1. Добавьте `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG`.

1. Перейдите к **расширенному livy2-log4j-Properties**.

    Добавьте `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG`.

1. Сохраните изменения.

## <a name="additional-logging"></a>Дополнительное ведение журнала

Приведенные выше журналы должны обеспечивать общее понимание операций файловой системы. Если указанные выше журналы по-прежнему не предоставляют полезной информации или если вы хотите исследовать вызовы API хранилища больших двоичных объектов, добавьте `fs.azure.storage.client.logging=true` в `core-site`. Этот параметр включит журналы пакета SDK для Java для драйвера хранилища wasb и будет печатать каждый вызов на сервере хранилища BLOB-объектов. Удалите параметр после расследования, так как он может быстро заполнить диск и замедлить процесс.

Если серверная часть Azure Data Lake на основе, используйте следующий параметр log4j для компонента (например, Spark/Tez/HDFS):

```
log4j.logger.com.microsoft.azure.datalake.store=ALL,adlsFile
log4j.additivity.com.microsoft.azure.datalake.store=true
log4j.appender.adlsFile=org.apache.log4j.FileAppender
log4j.appender.adlsFile.File=/var/log/adl/adl.log
log4j.appender.adlsFile.layout=org.apache.log4j.PatternLayout
log4j.appender.adlsFile.layout.ConversionPattern=%p\t%d{ISO8601}\t%r\t%c\t[%t]\t%m%n
```

Найдите журналы в `/var/log/adl/adl.log` для журналов.

## <a name="next-steps"></a>Следующие шаги

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключайтесь с [@AzureSupport](https://twitter.com/azuresupport) — официальная учетная запись Microsoft Azure для улучшения качества взаимодействия с клиентами. Подключение сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Для получения более подробных сведений см. статью [о создании запроса на поддержку Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
