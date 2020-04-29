---
title: Журналы Apache Hive, заполняющие место на диске — Azure HDInsight
description: Журналы Apache Hive заполняют место на диске головных узлов в Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.reviewer: jasonh
ms.date: 03/05/2020
ms.openlocfilehash: d843b942702d335065a5f3798572e34c71b4cd0e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78943968"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>Сценарий: журналы Apache Hive заполняют дисковое пространство головных узлов в Azure HDInsight.

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем, связанных с нехваткой дискового пространства на головных узлах в кластерах Azure HDInsight.

## <a name="issue"></a>Проблема

В кластере Apache Hive или LLAP нежелательные журналы занимают все дисковое пространство на головных узлах. Из-за этого могут возникнуть следующие проблемы.

1. Отказ в доступе SSH из-за отсутствия свободного места на головном узле.
2. Ambari выдает *ошибку HTTP: служба 503 недоступна*.

При `ambari-agent` возникновении проблемы в журналах будет отображаться следующее.
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>Причина

В расширенных конфигурациях Hive-log4j параметр *log4j. append. РФА. максбаккупиндекс* опущен. Это вызывает бесконечное создание файлов журнала.

## <a name="resolution"></a>Решение

1. Перейдите к сводке компонентов Hive на портале Ambari и щелкните `Configs` вкладку.

2. Перейдите к `Advanced hive-log4j` разделу в разделе Дополнительные параметры.

3. Задайте `log4j.appender.RFA` для параметра значение роллингфилеаппендер. 

4. Задайте `log4j.appender.RFA.MaxFileSize` и `log4j.appender.RFA.MaxBackupIndex` , как показано ниже.

```
log4jhive.log.maxfilesize=1024MB
log4jhive.log.maxbackupindex=10

log4j.appender.RFA=org.apache.log4j.RollingFileAppender
log4j.appender.RFA.File=${hive.log.dir}/${hive.log.file}
log4j.appender.RFA.MaxFileSize=${log4jhive.log.maxfilesize}
log4j.appender.RFA.MaxBackupIndex=${log4jhive.log.maxbackupindex}
log4j.appender.RFA.layout=org.apache.log4j.PatternLayout
log4j.appender.RFA.layout.ConversionPattern=%d{ISO8601} %-5p [%t] %c{2}: %m%n
```
5. Задайте `hive.root.logger` для `INFO,RFA` значение следующим образом. Значение по умолчанию — DEBUG, что делает журналы очень большими.

```
# Define some default values that can be overridden by system properties
hive.log.threshold=ALL
hive.root.logger=INFO,RFA
hive.log.dir=${java.io.tmpdir}/${user.name}
hive.log.file=hive.log
```

6. Сохраните настройки и перезапустите необходимые компоненты.

## <a name="next-steps"></a>Дальнейшие шаги

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключайтесь с помощью [@AzureSupport](https://twitter.com/azuresupport) официальной учетной записи Microsoft Azure для улучшения качества работы клиентов, подключив сообщество Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Дополнительные сведения см. [в](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)этой службе. Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
