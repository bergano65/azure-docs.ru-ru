---
title: Ошибка UI 502 Apache Ambari в Azure HDInsight
description: Ошибка UI 502 Apache Ambari при попытке получить доступ к кластеру Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: 2b17c2488e47148e8845433f9c7613e1127fbffa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895755"
---
# <a name="scenario-apache-ambari-ui-502-error-in-azure-hdinsight"></a>Сценарий: Ошибка UI 502 Apache Ambari в Azure HDInsight

В этой статье описаны шаги устранения неполадок и возможные решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблема

При попытке получить доступ к uI Apache Ambari для кластера HDInsight вы получаете сообщение, похожее на: "502 - веб-сервер получил недействительный ответ, действуя в качестве шлюза или прокси-сервера".

## <a name="cause"></a>Причина

В целом, код состояния HTTP 502 означает, что сервер Ambari работает неправильно на активном головном уде. Есть несколько возможных коренных причин.

## <a name="resolution"></a>Решение

В большинстве случаев, чтобы смягчить проблему, можно перезапустить активный головной нод. Или выберите больший размер VM для вашего headnode.

### <a name="ambari-server-failed-to-start"></a>Сервер Ambari не стартовал

Вы можете проверить журналы ambari-server, чтобы выяснить, почему сервер Ambari не был заведован. Одной из распространенных причин является ошибка проверки согласованности базы данных. Вы можете узнать об этом `/var/log/ambari-server/ambari-server-check-database.log`в этом файле журнала: .

Если вы внесли какие-либо изменения в кластерный узлы, пожалуйста, отменить их. Всегда используйте ambari UI для изменения любых конфигураций, связанных с Hadoop/Spark.

### <a name="ambari-server-taking-100-cpu-utilization"></a>Сервер Ambari занимает 100% использование процессора

В редких случаях, мы видели амбари-сервер процесс имеет почти 100% использование процессора постоянно. В качестве смягчения, вы можете ssh к активному headnode, и убить процесс сервера Ambari и начать его снова.

```bash
ps -ef | grep AmbariServer
top -p <ambari-server-pid>
kill -9 <ambari-server-pid>
service ambari-server start
```

### <a name="ambari-server-killed-by-oom-killer"></a>Сервер Амбари убит ом-убийцей

В некоторых сценариях у вашего головного нода заканчивается память, и Linux oom-killer начинает подбирать процессы для убийства. Вы можете проверить эту ситуацию, иссмотреть идентификатор процесса AmbariServer, который не должен быть найден. Тогда посмотрите `/var/log/syslog`на ваш , и посмотрите на что-то вроде этого:

```
Jul 27 15:29:30 xxx-xxxxxx kernel: [874192.703153] java invoked oom-killer: gfp_mask=0x23201ca, order=0, oom_score_adj=0
```

Затем определите, какие процессы берут воспоминания, и попытайтесь продвинуть сяповую причину.

### <a name="other-issues-with-ambari-server"></a>Другие проблемы с сервером Ambari

Редко сервер Ambari не может обрабатывать входящий запрос, вы можете найти более подробную информацию, глядя на журналы ambari-сервера для любой ошибки. Одним из таких случаев является ошибка, как это:

```
Error Processing URI: /api/v1/clusters/xxxxxx/host_components - (java.lang.OutOfMemoryError) Java heap space
```

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [поддержку сообщества Azure.](https://azure.microsoft.com/support/community/)

* Связаться [@AzureSupport](https://twitter.com/azuresupport) с официальным аккаунтом Microsoft Azure для улучшения обслуживания клиентов, подключив сообщество Azure к нужным ресурсам: ответам, поддержке и экспертам.

* Если вам нужна дополнительная помощь, вы можете отправить запрос на поддержку с [портала Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **поддержку** из бара меню или откройте концентратор **поддержки Справка и.** Для получения более подробной информации, пожалуйста, просмотрите [Как создать запрос поддержки Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Доступ к управлению подпиской и поддержке выставления счетов включен в подписку Microsoft Azure, а техническая поддержка обеспечивается через один из [планов поддержки Azure.](https://azure.microsoft.com/support/plans/)
