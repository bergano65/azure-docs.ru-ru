---
title: Проблемы с подключением Apache Phoenix в Azure HDInsight
description: Проблемы с подключением между Apache HBase и Apache Phoenix в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/14/2019
ms.openlocfilehash: b886f51bcb2bb7308c49c76563dcb70148bbc583
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887297"
---
# <a name="scenario-apache-phoenix-connectivity-issues-in-azure-hdinsight"></a>Сценарий: Проблемы подключения Apache Phoenix в Azure HDInsight

В этой статье описаны шаги устранения неполадок и возможные решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблема

Невозможно подключиться к Apache HBase с Apache Phoenix. Причины могут варьироваться.

## <a name="cause-incorrect-ip"></a>Причина: Неправильный IP

Неправильное IP активного узла зоозащитника.

### <a name="resolution"></a>Решение

IP активного узла зоозащитника можно идентифицировать из uI Ambari, следуя ссылкам на **HBase** > **Быстрые ссылки** > **(Активный)** > **Зоохранител Информация**. Исправить IP по мере необходимости.

---

## <a name="cause-systemcatalog-table-offline"></a>Причина: SYSTEM. Таблица CATALOG в автономном режиме

При запуске команд, таких как, `!tables`вы получаете сообщение об ошибке, аналогичное:

```output
Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings.
```

При запуске команд, таких как, `count 'SYSTEM.CATALOG'`вы получаете сообщение об ошибке, аналогичное:

```output
ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189)
```

### <a name="resolution"></a>Решение

Из uI Apache Ambari выполните следующие шаги по перезапуску сервиса HMaster на всех узлах зоопарка:

1. Из **раздела Резюме** HBase, перейдите на **HBase** > **Активный HBase Мастер**.

1. Из раздела **Компоненты** перезапустите сервис HBase Master.

1. Повторите описанные выше шаги для остальных служб **главного узла HBase в режиме ожидания**.

Это может занять до пяти минут для hBase Master службы для стабилизации и завершения восстановления. После `SYSTEM.CATALOG` того, как таблица вернется в нормальное русло, проблема подключения к Apache Phoenix должна быть решена автоматически.

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [поддержку сообщества Azure.](https://azure.microsoft.com/support/community/)

* Связаться [@AzureSupport](https://twitter.com/azuresupport) с - официальная учетная запись Microsoft Azure для улучшения обслуживания клиентов. Подключение сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, вы можете отправить запрос на поддержку с [портала Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **поддержку** из бара меню или откройте концентратор **поддержки Справка и.** Для получения более подробной информации просмотрите [Как создать запрос поддержки Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Доступ к управлению подпиской и поддержке выставления счетов включен в подписку Microsoft Azure, а техническая поддержка обеспечивается через один из [планов поддержки Azure.](https://azure.microsoft.com/support/plans/)
