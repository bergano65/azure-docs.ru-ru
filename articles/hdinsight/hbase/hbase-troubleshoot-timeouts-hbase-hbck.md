---
title: Время ожидания с помощью команды "HBase hbck" в Azure HDInsight
description: Возникла ошибка при выполнении команды "HBase hbck" при исправлении назначений регионов.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 800182498ab77993d0861c9b5383e0d71b302b6f
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091561"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>Сценарий. Время ожидания с помощью команды "HBase hbck" в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблемы

При исправлении назначений регионов возникают времена ожидания с помощью `hbase hbck` команды.

## <a name="cause"></a>Причина:

Потенциальной причиной проблем с временем ожидания при использовании команды `hbck` может быть то, что несколько регионов находятся в состоянии "идет переход" в течение долгого времени. Эти регионы можно увидеть как отключенные в пользовательском интерфейсе главного узла HBase. Так как большое число регионов пытается выполнить переход, HBase Master может исключаться из-за истечения времени ожидания и не сможет вернуть эти регионы в оперативный режим.

## <a name="resolution"></a>Разрешение

1. Войдите в кластер HDInsight HBase с помощью SSH.

1. Выполните `hbase zkcli` команду, чтобы подключиться с помощью оболочки Apache ZooKeeper.

1. Выполните `rmr /hbase/regions-in-transition` команду `rmr /hbase-unsecure/regions-in-transition` или.

1. Выйдите `hbase zkcli` из оболочки с `exit` помощью команды.

1. В пользовательском интерфейсе Apache Ambari перезапустите активную службу HBase Master.

1. Выполните команду `hbase hbck -fixAssignments`.

1. Отслеживайте HBase Master пользовательского интерфейса в области переходов, чтобы не зависнуть регион.

## <a name="next-steps"></a>Следующие шаги

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

- Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

- Подключение с [@AzureSupport](https://twitter.com/azuresupport) — официальная учетная запись Microsoft Azure для улучшения качества обслуживания клиентов. Подключение сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

- Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Для получения более подробных сведений см. статью [о создании запроса на поддержку Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
