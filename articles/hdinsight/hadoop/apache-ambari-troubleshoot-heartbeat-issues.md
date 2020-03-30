---
title: Проблемы с пульсом Apache Ambari в Azure HDInsight
description: Обзор различных причин проблем с сердцебиением Apache Ambari в Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: ab88f65d535be2aef5f0b26fa1171c03276466e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057079"
---
# <a name="apache-ambari-heartbeat-issues-in-azure-hdinsight"></a>Проблемы с пульсом Apache Ambari в Azure HDInsight

В этой статье описаны шаги устранения неполадок и возможные решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="scenario-high-cpu-utilization"></a>Сценарий: Высокое использование процессора

### <a name="issue"></a>Проблема

Агент Ambari имеет высокую загрузку процессора, что приводит к предупреждениям от Ambari UI, что для некоторых узлов сердцебиение агента Ambari теряется. Сердцебиение потерял оповещение, как правило, переходный.

### <a name="cause"></a>Причина

Из-за различных ошибок ambari-агента, в редких случаях, ваш амбари-агент может иметь высокий (около 100) процент использования процессора.

### <a name="resolution"></a>Решение

1. Определить идентификатор процесса (pid) амбари-агента:

    ```bash
    ps -ef | grep ambari_agent
    ```

1. Чтобы просмотреть сведения об использовании ЦП, выполните следующую команду:

    ```bash
    top -p <ambari-agent-pid>
    ```

1. Перезапуск ambari-агента для смягчения проблемы:

    ```bash
    service ambari-agent restart
    ```

1. Если перезагрузка не работает, убейте процесс амбари-агента, а затем запустите его:

    ```bash
    kill -9 <ambari-agent-pid>
    service ambari-agent start
    ```

---

## <a name="scenario-ambari-agent-not-started"></a>Сценарий: Агент Амбари не начал

### <a name="issue"></a>Проблема

Агент Амбари не начал, что приводит к предупреждениям от Ambari UI, что для некоторых узлов агента Ambari сердцебиение теряется.

### <a name="cause"></a>Причина

Предупреждения вызваны тем, что агент Ambari не работает.

### <a name="resolution"></a>Решение

1. Подтвердите статус амбари-агента:

    ```bash
    service ambari-agent status
    ```

1. Подтвердите, что службы контроллера сбоя работают:

    ```bash
    ps -ef | grep failover
    ```

    Если службы контроллера сбоя не работают, это, скорее всего, из-за проблемы предотвратить hdinsight-агент от запуска контроллера failover. Проверьте журнал hdinsight-агента из `/var/log/hdinsight-agent/hdinsight-agent.out` файла.

## <a name="scenario-heartbeat-lost-for-ambari"></a>Сценарий: Сердцебиение потерял для Амбари

### <a name="issue"></a>Проблема

Агент сердцебиения Амбари был потерян.

### <a name="cause"></a>Причина

OmS журналы вызывают высокое использование процессора.

### <a name="resolution"></a>Решение

* Отключите журнал Azure Monitor с помощью смдлета [Disable-AzHDInsightMonitoring](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) PowerShell.
* Удалить `mdsd.warn` файл журнала

---

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [поддержку сообщества Azure.](https://azure.microsoft.com/support/community/)

* Связаться [@AzureSupport](https://twitter.com/azuresupport) с официальным аккаунтом Microsoft Azure для улучшения обслуживания клиентов, подключив сообщество Azure к нужным ресурсам: ответам, поддержке и экспертам.

* Если вам нужна дополнительная помощь, вы можете отправить запрос на поддержку с [портала Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **поддержку** из бара меню или откройте концентратор **поддержки Справка и.** Для получения более подробной информации, пожалуйста, просмотрите [Как создать запрос поддержки Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Доступ к управлению подпиской и поддержке выставления счетов включен в подписку Microsoft Azure, а техническая поддержка обеспечивается через один из [планов поддержки Azure.](https://azure.microsoft.com/support/plans/)
