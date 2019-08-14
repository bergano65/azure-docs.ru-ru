---
title: Биндексцептион-Address уже используется в Azure HDInsight
description: Биндексцептион-Address уже используется в Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/08/2019
ms.openlocfilehash: 8851a4dfb7deafab7ad77ef80619dd49ca46ed71
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947855"
---
# <a name="scenario-bindexception---address-already-in-use-in-azure-hdinsight"></a>Сценарий: Биндексцептион-Address уже используется в Azure HDInsight

В этой статье описываются действия по устранению неполадок и возможные способы решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблемы

Не удается завершить операцию перезапуска на сервере региона Apache HBase. Из каталога `/var/log/hbase` в каталоге рабочих узлов, в которых происходит сбой запуска сервера Region, может появиться сообщение об ошибке, подобное приведенному ниже. `region-server.log`

```
Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
...

Caused by: java.net.BindException: Address already in use
...
```

## <a name="cause"></a>Причина:

Перезапуск серверов регионов HBase во время высокой активности рабочей нагрузки. Ниже показано, что происходит в фоновом режиме, когда пользователь инициирует операцию перезапуска на сервере HBase региона из пользовательского интерфейса Ambari:

1. Агент Ambari отправляет запрос на остановку регионального сервера.

1. Затем агент Ambari ожидает, пока сервер региона завершит работу в течение 30 секунд.

1. Если приложение продолжит подключаться к серверу региона, оно не будет немедленно завершено, поэтому 30-секундное время ожидания истечет раньше.

1. По истечении 30 секунд агент Ambari отправит запрос на принудительное отключение (kill -9) регионального сервера.

1. Из-за этого внезапного завершения работы, хотя процесс сервера региона завершается, порт, связанный с процессом, может не быть освобожден, что в конечном `AddressBindException`итоге приводит к.

## <a name="resolution"></a>Разрешение

Перед запуском перезагрузки сократите нагрузку на серверы регионов HBase.

Кроме того, попробуйте перезапустить серверы регионов на рабочих узлах вручную с помощью следующих команд:

```bash
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"
```

## <a name="next-steps"></a>Следующие шаги

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [службу поддержки сообщества Azure](https://azure.microsoft.com/support/community/).

* Подключение с [@AzureSupport](https://twitter.com/azuresupport) — официальная учетная запись Microsoft Azure для улучшения качества обслуживания клиентов. Подключение сообщества Azure к нужным ресурсам: ответы, поддержка и эксперты.

* Если вам нужна дополнительная помощь, можно отправить запрос в службу поддержки из [портал Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите пункт **Поддержка** в строке меню или откройте центр **справки и поддержки** . Для получения более подробных сведений см. статью [о создании запроса на поддержку Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Доступ к управлению подписками и поддержкой выставления счетов включен в вашу подписку Microsoft Azure, а техническая поддержка предоставляется через один из [планов поддержки Azure](https://azure.microsoft.com/support/plans/).
