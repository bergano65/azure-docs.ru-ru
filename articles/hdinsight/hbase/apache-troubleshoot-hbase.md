---
title: Устранение неполадок в HBase с помощью Azure HDInsight
description: Получите ответы на распространенные вопросы о работе с HBase и Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.custom: hdinsightactive, seodec18
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.openlocfilehash: 595acdc46aca76a86038acebdb9a7581c51e3688
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69573947"
---
# <a name="troubleshoot-apache-hbase-by-using-azure-hdinsight"></a>Устранение неполадок в Apache HBase с помощью Azure HDInsight

Ознакомьтесь с основными проблемами и их разрешением при работе с полезными данными Apache HBase в Apache Ambari.

## <a name="how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions"></a>Как запускать отчеты с помощью команды hbck с несколькими неназначенными регионами?

При выполнении команды `hbase hbck` часто можно увидеть сообщение об ошибке "multiple regions being unassigned or holes in the chain of regions" (несколько регионов не назначены, или присутствуют пропуски в цепочке регионов).

В пользовательском интерфейсе главного узла HBase можно увидеть количество регионов, которые не сбалансированы по всем региональным серверам. Затем можно выполнить команду `hbase hbck`, чтобы увидеть пропуски в цепочке регионов.

Пропуски могут возникнуть по причине отключенных регионов, поэтому сначала следует исправить назначения. 

Чтобы вернуть неназначенные регионы в нормальное состояние, выполните такие действия:

1. Войдите в кластер HDInsight HBase с помощью SSH.
2. Для подключения к оболочке Apache ZooKeeper выполните команду `hbase zkcli`.
3. Выполните команду `rmr /hbase/regions-in-transition` или `rmr /hbase-unsecure/regions-in-transition`.
4. Для выхода из оболочки `hbase zkcli` используйте команду `exit`.
5. Откройте пользовательский интерфейс Ambari Apache и перезапустите службу активного главного узла HBase.
6. Выполните команду `hbase hbck` еще раз (без каких-либо параметров). Проверьте выходные данные этой команды и убедитесь, что все регионы назначены.


## <a name="how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments"></a>Как устранить проблемы со временем ожидания команд hbck для назначения регионов?

### <a name="issue"></a>Проблемы

Потенциальной причиной проблем с временем ожидания при использовании команды `hbck` может быть то, что несколько регионов находятся в состоянии "идет переход" в течение долгого времени. Эти регионы можно увидеть как отключенные в пользовательском интерфейсе главного узла HBase. Так как большое количество областей пытается выполнить переход, главный узел HBase может превысить время ожидания и не сможет вернуть регионы обратно в оперативное состояние.

### <a name="resolution-steps"></a>Способы устранения

1. Войдите в кластер HDInsight HBase с помощью SSH.
2. Для подключения к оболочке Apache ZooKeeper выполните команду `hbase zkcli`.
3. Выполните команду `rmr /hbase/regions-in-transition` или `rmr /hbase-unsecure/regions-in-transition`.
4. Чтобы выйти из оболочки `hbase zkcli`, используйте команду `exit`.
5. Перезапустите службу активного главного узла HBase в пользовательском интерфейсе Ambari.
6. Выполните команду `hbase hbck -fixAssignments` еще раз.

## <a name="next-steps"></a>Следующие шаги

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключение с [@AzureSupport](https://twitter.com/azuresupport) — официальная учетная запись Microsoft Azure для улучшения качества обслуживания клиентов. Подключение сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Для получения более подробных сведений см. статью [о создании запроса на поддержку Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
