---
title: Проблемы с пульсом Apache Ambari в Azure HDInsight
description: Анализ различных причин проблем пульса Apache Ambari в Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: 8d23b89ab155c47e09f82d22c065db47ab9ac73d
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2020
ms.locfileid: "92540794"
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

* Отключите ведение журнала Azure Monitor с помощью командлета PowerShell [Disable-аздинсигхтмониторинг](/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) .
* Удаление `mdsd.warn` файла журнала

---

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы специалистов Azure на [сайте поддержки сообщества пользователей Azure](https://azure.microsoft.com/support/community/).

* Подключитесь к [@AzureSupport](https://twitter.com/azuresupport) — официальной учетной записи Microsoft Azure. Она помогает оптимизировать работу пользователей благодаря возможности доступа к ресурсам сообщества Azure (ответы на вопросы, поддержка и консультации специалистов).

* Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки на [портале Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **Поддержка** в строке меню или откройте центр **Справка и поддержка** . Дополнительные сведения см. в статье [Создание запроса на поддержку Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Доступ к управлению подписками и поддержкой выставления счетов уже включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется в рамках одного из [планов Службы поддержки Azure](https://azure.microsoft.com/support/plans/).