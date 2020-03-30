---
title: BindException - Адрес, уже используется в Azure HDInsight
description: BindException - Адрес, уже используется в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 80f984643d6d8be88b381881c6fc1cb1cb5f1815
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887348"
---
# <a name="scenario-bindexception---address-already-in-use-in-azure-hdinsight"></a>Сценарий: BindException - Адрес, уже используется в Azure HDInsight

В этой статье описаны шаги устранения неполадок и возможные решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблема

Операция перезапуска на сервере Apache HBase не завершена. Из `region-server.log` каталога в `/var/log/hbase` узлах рабочего, где завершается запуск сервера, можно увидеть сообщение об ошибке, аналогичное следующим образом:

```
Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
...

Caused by: java.net.BindException: Address already in use
...
```

## <a name="cause"></a>Причина

Перезапуск серверов Apache HBase в ходе большой рабочей нагрузки. Ниже приводится то, что происходит за кулисами, когда пользователь инициирует операцию перезагрузки на сервере региона HBase от UI Apache Ambari:

1. Агент Ambari отправляет запрос на завершение работы к региональному серверу.

1. Агент Ambari ждет 30 секунд, пока сервер региона выключится изящно

1. Если приложение продолжает подключаться к региональному серверу, его работа не будет завершена немедленно. Перед завершением работы должно истечь 30-секундное время ожидания.

1. Через 30 секунд агент Ambari отправляет команду force-kill (`kill -9`) на региональный сервер.

1. Из-за этого внезапного отключения, хотя процесс сервера региона уключается, порт, `AddressBindException`связанный с процессом, может не быть освобожден, что в конечном итоге приводит к .

## <a name="resolution"></a>Решение

Уменьшите нагрузку на серверы региона HBase перед началом перезапуска. Кроме того рекомендуется сначала очистить все таблицы. Подробные сведения об очистке таблиц см. в статье [HDInsight HBase: How to improve the Apache HBase cluster restart time by flushing tables](https://web.archive.org/web/20190112153155/https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/) (HDInsight HBase: как уменьшить время перезапуска кластера Apache HBase с помощью очистки таблиц).

Кроме того, попробуйте вручную перезапустить серверы регионов на рабочих узлах, используя следующие команды:

```bash
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"
```

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [поддержку сообщества Azure.](https://azure.microsoft.com/support/community/)

* Связаться [@AzureSupport](https://twitter.com/azuresupport) с - официальная учетная запись Microsoft Azure для улучшения обслуживания клиентов. Подключение сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, вы можете отправить запрос на поддержку с [портала Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **поддержку** из бара меню или откройте концентратор **поддержки Справка и.** Для получения более подробной информации просмотрите [Как создать запрос поддержки Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Доступ к управлению подпиской и поддержке выставления счетов включен в подписку Microsoft Azure, а техническая поддержка обеспечивается через один из [планов поддержки Azure.](https://azure.microsoft.com/support/plans/)
