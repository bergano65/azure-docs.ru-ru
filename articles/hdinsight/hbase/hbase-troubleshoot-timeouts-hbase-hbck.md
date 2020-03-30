---
title: Истечение времени ожидания при выполнении команды "hbase hbck" в Azure HDInsight
description: Тайм-аут с командой 'hbase hbck' при фиксации назначений региона
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 5604b42e1611830f3aaea9ae180cdb8142ab0942
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887195"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>Сценарий: Тайм-ауты с командой 'hbase hbck' в Azure HDInsight

В этой статье описаны шаги устранения неполадок и возможные решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблема

Встреча тайм-аутов с `hbase hbck` командой при фиксации назначений региона.

## <a name="cause"></a>Причина

Потенциальной причиной проблем с временем ожидания при использовании команды `hbck` может быть то, что несколько регионов находятся в состоянии "идет переход" в течение долгого времени. Эти регионы можно увидеть как отключенные в пользовательском интерфейсе главного узла HBase. Поскольку большое число регионов пытаются перейти, HBase Master может тайм-аут и не в состоянии вернуть эти регионы в строй.

## <a name="resolution"></a>Решение

1. Войти на кластер HDInsight HBase с помощью SSH.

1. Выполнить `hbase zkcli` команду для подключения с оболочкой Apache зоопарка.

1. `rmr /hbase/regions-in-transition` Выполнить `rmr /hbase-unsecure/regions-in-transition` или командовать.

1. Выход `hbase zkcli` из оболочки с помощью `exit` команды.

1. С uI Apache Ambari перезапустите сервис Active HBase Master.

1. Выполните команду `hbase hbck -fixAssignments`.

1. Мониторинг HBase Master UI "регион в переходный период", что раздел, чтобы убедиться, что ни один регион не застревает.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

- Получите ответы от экспертов Azure через [поддержку сообщества Azure.](https://azure.microsoft.com/support/community/)

- Связаться [@AzureSupport](https://twitter.com/azuresupport) с - официальная учетная запись Microsoft Azure для улучшения обслуживания клиентов. Подключение сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

- Если вам нужна дополнительная помощь, вы можете отправить запрос на поддержку с [портала Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **поддержку** из бара меню или откройте концентратор **поддержки Справка и.** Для получения более подробной информации просмотрите [Как создать запрос поддержки Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Доступ к управлению подпиской и поддержке выставления счетов включен в подписку Microsoft Azure, а техническая поддержка обеспечивается через один из [планов поддержки Azure.](https://azure.microsoft.com/support/plans/)
