---
title: Проблемы с подключением к Apache Phoenix в Azure HDInsight
description: Проблемы с подключением между Apache HBase и Apache Phoenix в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/14/2019
ms.openlocfilehash: b886f51bcb2bb7308c49c76563dcb70148bbc583
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75887297"
---
# <a name="scenario-apache-phoenix-connectivity-issues-in-azure-hdinsight"></a>Сценарий: Apache Phoenix проблемы с подключением в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблема

Не удалось подключиться к Apache HBase с Apache Phoenix. Причины могут отличаться.

## <a name="cause-incorrect-ip"></a>Причина: неверный IP-адрес

Неправильный IP-адрес активного узла Zookeeper.

### <a name="resolution"></a>Разрешение

IP-адрес активного узла Zookeeper можно определить в пользовательском интерфейсе Ambari, выполнив ссылки на **HBase** > **быстрые ссылки** > **ZK (Active)**  > **Zookeeper info**. При необходимости исправьте IP-адрес.

---

## <a name="cause-systemcatalog-table-offline"></a>Причина: SYSTEM. Таблица каталога в автономном режиме

При выполнении таких команд, как `!tables`, появляется сообщение об ошибке следующего вида:

```output
Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings.
```

При выполнении таких команд, как `count 'SYSTEM.CATALOG'`, появляется сообщение об ошибке следующего вида:

```output
ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189)
```

### <a name="resolution"></a>Разрешение

В пользовательском интерфейсе Apache Ambari выполните следующие действия, чтобы перезапустить службу HMaster на всех узлах ZooKeeper:

1. В разделе **Сводка** по HBase перейдите к **hbase** > **Active HBase Master**.

1. В разделе **компоненты** перезапустите службу HBase Master.

1. Повторите описанные выше шаги для остальных служб **главного узла HBase в режиме ожидания**.

Для стабилизации и завершения восстановления службы HBase Master может потребоваться до пяти минут. После возврата таблицы `SYSTEM.CATALOG` в нормальное состояние проблемы с подключением к Apache Phoenix должно быть разрешено автоматически.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключайтесь с [@AzureSupport](https://twitter.com/azuresupport) — официальная учетная запись Microsoft Azure для улучшения качества взаимодействия с клиентами. Подключение сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Для получения более подробных сведений см. статью [о создании запроса на поддержку Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
