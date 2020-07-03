---
title: Проблемы с пульсом Apache Ambari в Azure HDInsight
description: Анализ различных причин проблем пульса Apache Ambari в Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: ab88f65d535be2aef5f0b26fa1171c03276466e8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77057079"
---
# <a name="apache-ambari-heartbeat-issues-in-azure-hdinsight"></a>Проблемы с пульсом Apache Ambari в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="scenario-high-cpu-utilization"></a>Сценарий: высокая загрузка ЦП

### <a name="issue"></a>Проблема

Агент Ambari использует высокую загрузку ЦП, что приводит к предупреждениям пользовательского интерфейса Ambari, который для некоторых узлов теряет пульс агента Ambari. Предупреждение о потере пульса обычно является временным.

### <a name="cause"></a>Причина

Из-за различных ошибок ambari-Agent в редких случаях ambari-Agent может иметь высокий уровень использования ЦП (близко к 100).

### <a name="resolution"></a>Решение

1. Идентификация идентификатора процесса (PID) ambari-Agent:

    ```bash
    ps -ef | grep ambari_agent
    ```

1. Чтобы просмотреть сведения об использовании ЦП, выполните следующую команду:

    ```bash
    top -p <ambari-agent-pid>
    ```

1. Перезапустите ambari-Agent, чтобы устранить проблемы.

    ```bash
    service ambari-agent restart
    ```

1. Если перезапуск не работает, завершите процесс ambari-Agent, а затем запустите его:

    ```bash
    kill -9 <ambari-agent-pid>
    service ambari-agent start
    ```

---

## <a name="scenario-ambari-agent-not-started"></a>Сценарий: агент Ambari не запущен

### <a name="issue"></a>Проблема

Агент Ambari не начал работу, что приводит к предупреждениям из пользовательского интерфейса Ambari, который для некоторых узлов теряет пульс агента Ambari.

### <a name="cause"></a>Причина

Предупреждения вызваны тем, что агент Ambari не работает.

### <a name="resolution"></a>Решение

1. Подтвердите состояние ambari-Agent:

    ```bash
    service ambari-agent status
    ```

1. Проверьте, выполняются ли службы отказоустойчивого контроллера:

    ```bash
    ps -ef | grep failover
    ```

    Если службы контроллера отработки отказа не запущены, скорее всего, это связано с тем, что служба hdinsight-Agent не может запустить контроллер отработки отказа. Проверьте журнал hdinsight-Agent из `/var/log/hdinsight-agent/hdinsight-agent.out` файла.

## <a name="scenario-heartbeat-lost-for-ambari"></a>Сценарий: потеря пульса для Ambari

### <a name="issue"></a>Проблема

Агент пульса Ambari был потерян.

### <a name="cause"></a>Причина

Журналы OMS вызывают высокую загрузку ЦП.

### <a name="resolution"></a>Решение

* Отключите ведение журнала Azure Monitor с помощью командлета PowerShell [Disable-аздинсигхтмониторинг](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) .
* Удаление файла `mdsd.warn` журнала

---

## <a name="next-steps"></a>Дальнейшие шаги

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключайтесь с помощью [@AzureSupport](https://twitter.com/azuresupport) официальной учетной записи Microsoft Azure для улучшения качества работы клиентов, подключив сообщество Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Дополнительные сведения см. [в](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)этой службе. Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
