---
title: Apache Hive Logs заполняет дисковое пространство - Azure HDInsight
description: В журналах Apache Hive заполняются дисковое пространство на головных узлах в Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.reviewer: jasonh
ms.date: 03/05/2020
ms.openlocfilehash: d843b942702d335065a5f3798572e34c71b4cd0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943968"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>Сценарий: журналы Apache Hive заполняют дисковое пространство на узлах головы в Azure HDInsight

В этой статье описаны шаги по устранению неполадок и возможные решения проблем, связанных с недостаточного дискового пространства на головных узлах в кластерах Azure HDInsight.

## <a name="issue"></a>Проблема

В кластере Apache Hive/LLAP нежелательные журналы занимают все пространство диска на головных узлах. Из-за чего, следующие вопросы могут быть видны.

1. Доступ SSH выходит из строя из-за отсутствия места на головном узлах.
2. Ambari дает *HTTP ERROR: 503 Услуга недоступна*.

В `ambari-agent` журналах будут отображаться следующие, когда возникает проблема.
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>Причина

В расширенных конфигурациях Hive-log4j параметр *log4j.appender.RFA.MaxBackupIndex* опущен. Это вызывает бесконечное поколение файлов журнала.

## <a name="resolution"></a>Решение

1. Перейдите к резюме компонента Hive на `Configs` портале Ambari и нажмите на вкладку.

2. Перейдите `Advanced hive-log4j` в раздел в расширенных настройках.

3. Установите `log4j.appender.RFA` параметр как RollingFileAppender. 

4. `log4j.appender.RFA.MaxFileSize` Установить `log4j.appender.RFA.MaxBackupIndex` и следующим образом.

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
5. `hive.root.logger` Установить `INFO,RFA` следующим образом. Параметр по умолчанию DEBUG, что делает журналы очень большими.

```
# Define some default values that can be overridden by system properties
hive.log.threshold=ALL
hive.root.logger=INFO,RFA
hive.log.dir=${java.io.tmpdir}/${user.name}
hive.log.file=hive.log
```

6. Сохраните конфигурацию и перезапустите необходимые компоненты.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [поддержку сообщества Azure.](https://azure.microsoft.com/support/community/)

* Связаться [@AzureSupport](https://twitter.com/azuresupport) с официальным аккаунтом Microsoft Azure для улучшения обслуживания клиентов, подключив сообщество Azure к нужным ресурсам: ответам, поддержке и экспертам.

* Если вам нужна дополнительная помощь, вы можете отправить запрос на поддержку с [портала Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **поддержку** из бара меню или откройте концентратор **поддержки Справка и.** Для получения более подробной информации, пожалуйста, просмотрите [Как создать запрос поддержки Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Доступ к управлению подпиской и поддержке выставления счетов включен в подписку Microsoft Azure, а техническая поддержка обеспечивается через один из [планов поддержки Azure.](https://azure.microsoft.com/support/plans/)
