---
title: Проблемы с серверами регионов в Azure HDInsight
description: Проблемы с серверами регионов в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/07/2019
ms.openlocfilehash: e75f2fdd0530b92e8c8405b74c2a364ff9e9e28e
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935436"
---
# <a name="issues-with-region-servers-in-azure-hdinsight"></a>Проблемы с серверами регионов в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="scenario-unassigned-regions"></a>Сценарий: Неназначенные регионы

### <a name="issue"></a>Проблемы

При выполнении `hbase hbck` команды отображается сообщение об ошибке следующего вида:

```
multiple regions being unassigned or holes in the chain of regions
```

В пользовательском интерфейсе Apache HBase Master можно увидеть, что количество регионов не сбалансировано на всех серверах регионов.

### <a name="cause"></a>Причина:

Отверстия могут быть результатом автономных регионов.

### <a name="resolution"></a>Разрешение

Исправьте назначения. Выполните следующие действия, чтобы вернуть неназначенные регионы в нормальное состояние.

1. Войдите в кластер HDInsight HBase с помощью SSH.

1. Выполните `hbase zkcli` команду, чтобы подключиться к оболочке Zookeeper.

1. Выполните `rmr /hbase/regions-in-transition` команду `rmr /hbase-unsecure/regions-in-transition` или.

1. Выйдите из оболочки Zookeeper `exit` с помощью команды.

1. Откройте пользовательский интерфейс Ambari и перезапустите службу активного главного узла HBase.

1. Выполните `hbase hbck` команду еще раз (без дополнительных параметров). Проверьте выходные данные и убедитесь, что все регионы назначены.

---

## <a name="scenario-dead-region-servers"></a>Сценарий: Неработающие серверы регионов

### <a name="issue"></a>Проблемы

Не удается запустить серверы регионов.

### <a name="cause"></a>Причина:

Множественное разделение каталогов WAL.

1. Получить список текущих Wal: `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out`.

1. `wals.out` Проверьте файл. Если слишком много разделенных каталогов (начиная с *-разделение), возможно, сервер регионов не работает из-за этих каталогов.

### <a name="resolution"></a>Разрешение

1. Отключите HBase на портале Ambari.

1. Выполните `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out` процедуру, чтобы получить актуальный список Wal.

1. Переместите *-разделение каталогов во временную папку `splitWAL`и удалите каталоги, разделенные на *.

1. Выполните `hbase zkcli` команду, чтобы подключиться к оболочке Zookeeper.

1. Выполните `rmr /hbase-unsecure/splitWAL`.

1. Перезапустите службу HBase.

## <a name="next-steps"></a>Следующие шаги

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключение с [@AzureSupport](https://twitter.com/azuresupport) — официальная учетная запись Microsoft Azure для улучшения качества обслуживания клиентов. Подключение сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Для получения более подробных сведений см. статью [о создании запроса на поддержку Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
