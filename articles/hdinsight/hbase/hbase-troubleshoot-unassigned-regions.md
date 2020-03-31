---
title: Проблемы с серверами регионов в Azure HDInsight
description: Проблемы с серверами регионов в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 85aeafb2c4461b50d399e40d9abff2ac04b677c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272764"
---
# <a name="issues-with-region-servers-in-azure-hdinsight"></a>Проблемы с серверами регионов в Azure HDInsight

В этой статье описаны шаги устранения неполадок и возможные решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="scenario-unassigned-regions"></a>Сценарий: Неназначенные регионы

### <a name="issue"></a>Проблема

При `hbase hbck` запуске команды вы видите сообщение об ошибке, аналогичное:

```
multiple regions being unassigned or holes in the chain of regions
```

На uI Apache HBase Master можно увидеть количество несбалансированных регионов на всех серверах регионов. Затем можно выполнить команду `hbase hbck`, чтобы увидеть пропуски в цепочке регионов.

### <a name="cause"></a>Причина

Отверстия могут быть результатом автономных регионов.

### <a name="resolution"></a>Решение

Исправьте задания. Выполните следующие действия, чтобы вернуть неназначенные регионы в нормальное состояние.

1. Войти на кластер HDInsight HBase с помощью SSH.

1. Выполнить `hbase zkcli` команду для подключения к оболочке зоохранителя.

1. `rmr /hbase/regions-in-transition` Выполнить `rmr /hbase-unsecure/regions-in-transition` или командовать.

1. Выйдите оболочку `exit` зоозащитника с помощью команды.

1. Откройте пользовательский интерфейс Ambari Apache и перезапустите службу активного главного узла HBase.

1. Выполнить `hbase hbck` команду снова (без каких-либо дальнейших вариантов). Проверьте выход и убедитесь, что все регионы назначены.

---

## <a name="scenario-dead-region-servers"></a>Сценарий: Серверы мертвых регионов

### <a name="issue"></a>Проблема

Серверы региона не запускаться.

### <a name="cause"></a>Причина

Несколько разделив каталогов WAL.

1. Получить список текущих `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out`WALs: .

1. Осмотрите `wals.out` файл. Если слишком много разделенных каталогов (начиная с разделения), сервер региона, вероятно, выходит из строя из-за этих каталогов.

### <a name="resolution"></a>Решение

1. Остановите HBase с портала Ambari.

1. Выполните, `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out` чтобы получить свежий список WALs.

1. Переместите каталоги разделения в временную папку `splitWAL`и удалите каталоги разделения.

1. Выполняйте `hbase zkcli` команду для соединения с оболочкой зоозащитника.

1. Выполните процедуру `rmr /hbase-unsecure/splitWAL`.

1. Перезагрузка службы HBase.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [поддержку сообщества Azure.](https://azure.microsoft.com/support/community/)

* Связаться [@AzureSupport](https://twitter.com/azuresupport) с - официальная учетная запись Microsoft Azure для улучшения обслуживания клиентов. Подключение сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, вы можете отправить запрос на поддержку с [портала Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **поддержку** из бара меню или откройте концентратор **поддержки Справка и.** Для получения более подробной информации просмотрите [Как создать запрос поддержки Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Доступ к управлению подпиской и поддержке выставления счетов включен в подписку Microsoft Azure, а техническая поддержка обеспечивается через один из [планов поддержки Azure.](https://azure.microsoft.com/support/plans/)
