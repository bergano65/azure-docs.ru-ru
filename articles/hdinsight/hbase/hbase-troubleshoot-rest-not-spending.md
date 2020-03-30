---
title: Apache HBase REST не отвечает на запросы в Azure HDInsight
description: Разрешить проблему с Apache HBase REST, не отвечая на запросы в Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: 49b547829a369ea6df35e2f1c2f7d60458e41040
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887178"
---
# <a name="scenario-apache-hbase-rest-not-responding-to-requests-in-azure-hdinsight"></a>Сценарий: Apache HBase REST не отвечает на запросы в Azure HDInsight

В этой статье описаны шаги устранения неполадок и возможные решения проблем при взаимодействии с кластерами Azure HDInsight.

## <a name="issue"></a>Проблема

Служба Apache HBase REST не отвечает на запросы в Azure HDInsight.

## <a name="cause"></a>Причина

Возможной причиной может быть apache HBase REST службы утечки розетки, что особенно часто, когда служба работает в течение длительного времени (например, месяцев). От клиента SDK вы можете увидеть сообщение об ошибке, похожее на:

```
System.Net.WebException : Unable to connect to the remote server --->
System.Net.Sockets.SocketException : A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond 10.0.0.19:8090
```

## <a name="resolution"></a>Решение

Перезапустите HBase REST с помощью нижей команды после SSHing к усту. Вы также можете использовать действия скрипта для перезапуска этой службы на всех рабочих узлах:

```bash
sudo service hdinsight-hbrest restart
```

Эта команда остановит сервер региона HBase на том же хосте. Вы можете либо вручную запустить HBase Region Server через Ambari, либо позволить Ambari автоматически перезапустить функциональность автоматического восстановления HBase Region Server автоматически.

Если проблема сохраняется, можно установить следующий сценарий смягчения в качестве задания CRON, который работает каждые 5 минут на каждом рабочем узлах. Этот скрипт смягчения пингов службы REST и перезапускает его в случае, если служба REST не отвечает.

```bash
#!/bin/bash
nc localhost 8090 < /dev/null
if [ $? -ne 0 ]
    then
    echo "RESTServer is not responding. Restarting"
    sudo /usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh restart rest
fi
```

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы от экспертов Azure через [поддержку сообщества Azure.](https://azure.microsoft.com/support/community/)

* Связаться [@AzureSupport](https://twitter.com/azuresupport) с официальным аккаунтом Microsoft Azure для улучшения обслуживания клиентов, подключив сообщество Azure к нужным ресурсам: ответам, поддержке и экспертам.

* Если вам нужна дополнительная помощь, вы можете отправить запрос на поддержку с [портала Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Выберите **поддержку** из бара меню или откройте концентратор **поддержки Справка и.** Для получения более подробной информации, пожалуйста, просмотрите [Как создать запрос поддержки Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Доступ к управлению подпиской и поддержке выставления счетов включен в подписку Microsoft Azure, а техническая поддержка обеспечивается через один из [планов поддержки Azure.](https://azure.microsoft.com/support/plans/)
