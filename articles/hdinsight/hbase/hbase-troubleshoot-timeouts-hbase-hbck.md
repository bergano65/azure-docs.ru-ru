---
title: Время ожидания с помощью команды "HBase hbck" в Azure HDInsight
description: Возникла ошибка при выполнении команды "HBase hbck" при исправлении назначений регионов.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/01/2019
ms.openlocfilehash: 4f3f1c22fa1dc05a66a8b6bf0179903a44cef9b6
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/02/2019
ms.locfileid: "68737931"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>Сценарий: Время ожидания с помощью команды "HBase hbck" в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблемы

При исправлении назначений регионов возникают времена ожидания с помощью `hbase hbck` команды.

## <a name="cause"></a>Причина:

Здесь возможной причиной могут быть несколько регионов, которые находятся в состоянии "идет переход" в течение долгого времени. Эти регионы можно рассматривать как автономные из интерфейса Apache HBase Master UI. Из-за большого числа регионов, которые пытаются выполнить переход, HBase Master может истечет время ожидания и не сможет вернуть эти регионы в состояние "в сети".

## <a name="resolution"></a>Разрешение

1. Войдите в кластер HDInsight HBase с помощью SSH.

1. Выполните `hbase zkcli` команду, чтобы подключиться к оболочке Zookeeper.

1. Выполните `rmr /hbase/regions-in-transition` команду `rmr /hbase-unsecure/regions-in-transition` или.

1. Выйдите `hbase zkcli` из оболочки с `exit` помощью команды.

1. Откройте пользовательский интерфейс Ambari и перезапустите службу активного главного узла HBase.

1. Отслеживайте HBase Master пользовательского интерфейса в области переходов, чтобы не зависнуть регион.

## <a name="next-steps"></a>Следующие шаги

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключайтесь с помощью [@AzureSupport](https://twitter.com/azuresupport) официальной учетной записи Microsoft Azure для улучшения качества работы клиентов, подключив сообщество Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Дополнительные сведения см. [в](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)этой службе. Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
