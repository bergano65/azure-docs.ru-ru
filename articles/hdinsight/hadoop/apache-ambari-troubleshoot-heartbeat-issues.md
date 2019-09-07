---
title: Проблемы пульса Apache Ambari в Azure HDInsight
description: Анализ различных причин проблем пульса Apache Ambari в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/02/2019
ms.openlocfilehash: 0b20bb5265dd5fa139a691fcb21aaa1795ff352c
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735969"
---
# <a name="apache-ambari-heartbeat-issues-in-azure-hdinsight"></a>Проблемы пульса Apache Ambari в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="scenario-high-cpu-utilization"></a>Сценарий. Высокая загрузка ЦП

### <a name="issue"></a>Проблемы

Агент Ambari использует высокую загрузку ЦП, что приводит к предупреждениям пользовательского интерфейса Ambari, который для некоторых узлов теряет пульс агента Ambari.

### <a name="cause"></a>Причина:

Из-за различных ошибок ambari-Agent в редких случаях ambari-Agent может иметь высокий уровень использования ЦП (близко к 100).

### <a name="resolution"></a>Разрешение

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

## <a name="scenario-ambari-agent-not-started"></a>Сценарий. Агент Ambari не запущен

### <a name="issue"></a>Проблемы

Агент Ambari не начал работу, что приводит к предупреждениям из пользовательского интерфейса Ambari, который для некоторых узлов теряет пульс агента Ambari.

### <a name="cause"></a>Причина:

Предупреждения вызваны тем, что агент Ambari не работает.

### <a name="resolution"></a>Разрешение

1. Подтвердите состояние ambari-Agent:

    ```bash
    service ambari-agent status
    ```

1. Проверьте, выполняются ли службы отказоустойчивого контроллера:

    ```bash
    ps -ef | grep failover
    ```

    Если службы контроллера отработки отказа не запущены, вероятно, это связано с тем, что служба hdinsight-Agent не может запустить контроллер отработки отказа. Проверьте журнал hdinsight-Agent из `/var/log/hdinsight-agent/hdinsight-agent.out` файла.

---

## <a name="next-steps"></a>Следующие шаги

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключайтесь с помощью [@AzureSupport](https://twitter.com/azuresupport) официальной учетной записи Microsoft Azure для улучшения качества работы клиентов, подключив сообщество Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Дополнительные сведения см. [в](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)этой службе. Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
