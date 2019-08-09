---
title: Проблемы с подключением к Apache Phoenix в Azure HDInsight
description: Проблемы с подключением к Apache Phoenix в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/07/2019
ms.openlocfilehash: 641d622377bad7a1239efd526b93c6f0f0c08d4a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68887044"
---
# <a name="scenario-apache-phoenix-connectivity-issues-in-azure-hdinsight"></a>Сценарий: Проблемы с подключением к Apache Phoenix в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблемы

Не удалось подключиться к Apache HBase с Apache Phoenix. Причины могут отличаться.

## <a name="cause-incorrect-ip"></a>Причина. неправильный IP-адрес;

Неправильный IP-адрес активного узла Zookeeper.

### <a name="resolution"></a>Разрешение

IP-адрес активного узла Zookeeper можно определить из пользовательского интерфейса Ambari, следуя ссылкам на **HBase-> Быстрые ссылки — > ZK***  **(Active) — > Zookeeper info**. При необходимости внесите исправления.

---

## <a name="cause-systemcatalog-table-offline"></a>Причина. Системой. Таблица каталога в автономном режиме

При выполнении таких команд, `!tables`как, появляется сообщение об ошибке следующего вида:

```
Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings.
```

При выполнении таких команд, `count 'SYSTEM.CATALOG'`как, появляется сообщение об ошибке следующего вида:

```
ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189)
```

### <a name="resolution"></a>Разрешение

Перезапустите службу HMaster на всех узлах Zookeeper из пользовательского интерфейса Ambari.

1. Перейдите по ссылке **HBase — > Active HBase Master** ссылку в разделе Сводка по HBase.

1. В разделе **компоненты** перезапустите службу HBase Master.

1. Повторите описанные выше шаги для оставшихся служб **резервного HBase Master** .

Для того, чтобы служба HBase Master стабилизировать и завершила восстановление, может потребоваться до 5 минут. После возврата `SYSTEM.CATALOG` таблицы в нормальное состояние проблемы с подключением к Apache Phoenix должно быть разрешено автоматически.

## <a name="next-steps"></a>Следующие шаги

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключение с [@AzureSupport](https://twitter.com/azuresupport) — официальная учетная запись Microsoft Azure для улучшения качества обслуживания клиентов. Подключение сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Для получения более подробных сведений см. статью [о создании запроса на поддержку Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
