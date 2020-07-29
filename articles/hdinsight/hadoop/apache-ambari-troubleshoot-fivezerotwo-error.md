---
title: Ошибка пользовательского интерфейса Apache Ambari 502 в Azure HDInsight
description: Ошибка пользовательского интерфейса Apache Ambari 502 при попытке доступа к кластеру Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: 2b17c2488e47148e8845433f9c7613e1127fbffa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75895755"
---
# <a name="scenario-apache-ambari-ui-502-error-in-azure-hdinsight"></a>Сценарий: Ошибка пользовательского интерфейса Apache Ambari 502 в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблемы

При попытке доступа к пользовательскому интерфейсу Apache Ambari для кластера HDInsight вы получаете примерно следующее сообщение: "502-веб-сервер получил недопустимый ответ при работе в качестве шлюза или прокси-сервера".

## <a name="cause"></a>Причина:

В общем случае код состояния HTTP 502 означает, что сервер Ambari работает неправильно на активном головного узла. Существует несколько возможных основных причин.

## <a name="resolution"></a>Решение

В большинстве случаев для устранения проблемы можно перезапустить активный головного узла. Или выберите больший размер виртуальной машины для головного узла.

### <a name="ambari-server-failed-to-start"></a>Не удалось запустить сервер Ambari

Вы можете проверить журналы ambari-Server, чтобы узнать, почему не удалось запустить сервер Ambari. Одна из распространенных причин — ошибка проверки согласованности базы данных. Это можно найти в следующем файле журнала: `/var/log/ambari-server/ambari-server-check-database.log` .

Если вы внесли изменения в узел кластера, отмените их. Для изменения любых конфигураций, связанных с Hadoop/Spark, всегда используйте пользовательский интерфейс Ambari.

### <a name="ambari-server-taking-100-cpu-utilization"></a>Сервер Ambari, который занимает 100% загрузки ЦП

В редких случаях мы видели, что ambari-серверный процесс постоянно закрылся до 100% использования ЦП. В качестве меры по устранению рисков вы можете подключаться по протоколу SSH к активному головного узла и завершить процесс Ambari Server и запустить его снова.

```bash
ps -ef | grep AmbariServer
top -p <ambari-server-pid>
kill -9 <ambari-server-pid>
service ambari-server start
```

### <a name="ambari-server-killed-by-oom-killer"></a>Сервер Ambari, уничтоженный с помощью нехватки памяти

В некоторых сценариях в головного узла не хватает памяти, а в Linux с нехваткой данных начинается выбор процессов для уничтожения. Эту ситуацию можно проверить, выполнив поиск по ИДЕНТИФИКАТОРу процесса Амбарисервер, который не должен быть найден. Затем найдите и найдите `/var/log/syslog` нечто вроде этого:

```
Jul 27 15:29:30 xxx-xxxxxx kernel: [874192.703153] java invoked oom-killer: gfp_mask=0x23201ca, order=0, oom_score_adj=0
```

Затем укажите, какие процессы занимают воспоминания, и попробуйте еще до того, как они попытаются стать причиной.

### <a name="other-issues-with-ambari-server"></a>Другие проблемы с сервером Ambari

Редко сервер Ambari не может обрабатывать входящий запрос. Дополнительные сведения см. в журналах Ambari-Server для любой ошибки. Один из таких случаев выглядит следующим образом:

```
Error Processing URI: /api/v1/clusters/xxxxxx/host_components - (java.lang.OutOfMemoryError) Java heap space
```

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы специалистов Azure на [сайте поддержки сообщества пользователей Azure](https://azure.microsoft.com/support/community/).

* Подключитесь к [@AzureSupport](https://twitter.com/azuresupport) — официальной учетной записи Microsoft Azure. Она помогает оптимизировать работу пользователей благодаря возможности доступа к ресурсам сообщества Azure (ответы на вопросы, поддержка и консультации специалистов).

* Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки на [портале Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **Поддержка** в строке меню или откройте центр **Справка и поддержка**. Дополнительные сведения см. в статье [Создание запроса на поддержку Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Доступ к управлению подписками и поддержкой выставления счетов уже включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется в рамках одного из [планов Службы поддержки Azure](https://azure.microsoft.com/support/plans/).
