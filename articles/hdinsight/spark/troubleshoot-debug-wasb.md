---
title: Операции файлов Debug WASB в Azure HDInsight
description: Описывает шаги устранения неполадок и возможные решения проблем при взаимодействии с кластерами Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/18/2020
ms.openlocfilehash: f1707c7f8d6324678c8bf5a470bbded1e58c719e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77470723"
---
# <a name="debug-wasb-file-operations-in-azure-hdinsight"></a>Операции файлов Debug WASB в Azure HDInsight

Иногда вы можете понять, какие операции драйвер WASB начал с Azure Storage. Для клиентской стороны драйвер WASB производит журналы для каждой работы файловой системы на уровне **DEBUG.** Драйвер WASB использует log4j для управления уровнем регистрации, а по умолчанию уровень **INFO.** В журналах аналитики серверов серверной системы Azure Storage смотрите [журналы аналитики Azure Storage.](../../storage/common/storage-analytics-logging.md)

Произведенный журнал будет выглядеть так же, как:

```log
18/05/13 04:15:55 DEBUG NativeAzureFileSystem: Moving wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/_temporary/0/_temporary/attempt_20180513041552_0000_m_000000_0/part-00000 to wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/part-00000
```

## <a name="turn-on-wasb-debug-log-for-file-operations"></a>Включите журнал отладки WASB для операций файлов

1. Из веб-браузера, `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs`перейдите к , где `CLUSTERNAME` это имя вашего кластера Spark.

1. Перейдите к **продвинутым искривичам2-log4j-свойствам.**

    1. Изменить `log4j.appender.console.Threshold=INFO` `log4j.appender.console.Threshold=DEBUG`до .

    1. Добавьте `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG`.

1. Перейдите к **Расширенный livy2-log4j-свойства**.

    Добавьте `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG`.

1. Сохраните изменения.

## <a name="additional-logging"></a>Дополнительное ведение журнала

Вышеуказанные журналы должны обеспечить понимание операций файловой системы на высоком уровне. Если приведенные выше журналы по-прежнему не предоставляют полезную информацию, или `fs.azure.storage.client.logging=true` если `core-site`вы хотите исследовать вызовы капли хранения api, добавьте в . Эта настройка позволит java sdk журналы для wasb драйвер аранжа хранения и будет печатать каждый вызов на сервер хранения капли. Удалите настройку после исследований, поскольку она может быстро заполнить диск и замедлить процесс.

Если бэкэнд основан на Azure Data Lake, используйте следующую настройку log4j для компонента (например, искра/тэз/hdfs):

```
log4j.logger.com.microsoft.azure.datalake.store=ALL,adlsFile
log4j.additivity.com.microsoft.azure.datalake.store=true
log4j.appender.adlsFile=org.apache.log4j.FileAppender
log4j.appender.adlsFile.File=/var/log/adl/adl.log
log4j.appender.adlsFile.layout=org.apache.log4j.PatternLayout
log4j.appender.adlsFile.layout.ConversionPattern=%p\t%d{ISO8601}\t%r\t%c\t[%t]\t%m%n
```

Ищите журналы `/var/log/adl/adl.log` для журналов.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [поддержку сообщества Azure.](https://azure.microsoft.com/support/community/)

* Связаться [@AzureSupport](https://twitter.com/azuresupport) с - официальная учетная запись Microsoft Azure для улучшения обслуживания клиентов. Подключение сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, вы можете отправить запрос на поддержку с [портала Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **поддержку** из бара меню или откройте концентратор **поддержки Справка и.** Для получения более подробной информации просмотрите [Как создать запрос поддержки Azure.](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) Доступ к управлению подпиской и поддержке выставления счетов включен в подписку Microsoft Azure, а техническая поддержка обеспечивается через один из [планов поддержки Azure.](https://azure.microsoft.com/support/plans/)
