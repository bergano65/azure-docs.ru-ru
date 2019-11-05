---
title: Apache Hive подключения к Apache Zookeeper — Azure HDInsight
description: Apache Hive просмотр недоступен из-за проблем Apache Zookeeper в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: a6ede13ea258048857e06e1c158a297219ff0df6
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494269"
---
# <a name="scenario-apache-hive-fails-to-establish-a-connection-to-apache-zookeeper-in-azure-hdinsight"></a>Сценарий: Apache Hive не удается установить подключение к Apache Zookeeper в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при использовании интерактивных компонентов запросов в кластерах Azure HDInsight.

## <a name="issue"></a>Проблема

Представление Hive недоступно, а журналы в `/var/log/hive` отображают ошибку, аналогичную следующей:

```
ERROR [Curator-Framework-0]: curator.ConnectionState (ConnectionState.java:checkTimeouts(200)) - Connection timed out for connection string (zk0-cluster.cloud.wbmi.com:2181,zk1-cluster.cloud.wbmi.com:2181,zk2-cluster.cloud.wbmi.com:2181) and timeout (15000) / elapsed (21852)
```

## <a name="cause"></a>Причина:

Возможно, Hive не сможет установить подключение к Zookeeper, что не позволит запустить представление Hive.

## <a name="resolution"></a>Разрешение

1. Убедитесь, что служба Zookeeper работоспособна.

1. Проверьте, имеет ли служба Zookeeper запись Znode будет удален для Hive Server2. Значение будет отсутствовать или быть неправильным.

    ```
    /usr/hdp/2.6.2.25-1/zookeeper/bin/zkCli.sh -server zk1-wbwdhs
    [zk: zk0-cluster(CONNECTED) 0] ls /hiveserver2-hive2
    ```

1. Чтобы повторно установить подключение, перезагрузите узлы Zookeeper и перезагрузите HiveServer2.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключайтесь с [@AzureSupport](https://twitter.com/azuresupport) — официальная учетная запись Microsoft Azure для улучшения качества обслуживания клиентов путем подключения сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Дополнительные сведения см. в статье [Как создать запрос на получение поддержки Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Доступ к поддержке в рамках управления подпиской и выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через тот или иной [план поддержки Azure](https://azure.microsoft.com/support/plans/).
